---
title: Azure Active Directory B2C를 사용 하 여 ASP.NET Core Blazor Weasembomoma 독립 실행형 앱을 보호 합니다.
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: bb03ef1e6d216cfc06e2b91919c64f92f2ef634e
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219274"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용 하 여 ASP.NET Core Blazor Weasembomoma 독립 실행형 앱을 보호 합니다.

[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

인증을 위해 [AAD (Azure Active Directory) B2C](/azure/active-directory-b2c/overview) 를 사용 하는 Blazor Weasembom독립형 앱을 만들려면 다음을 수행 합니다.

1. 다음 항목의 지침에 따라 테 넌 트를 만들고 Azure Portal에서 웹 앱을 등록 합니다.

   * [AAD B2C 테 넌 트를 만들어](/azure/active-directory-b2c/tutorial-create-tenant) 다음 정보를 기록 &ndash; 합니다.

     1\. AAD B2C 인스턴스 (예: 후행 슬래시를 포함 하는 `https://contoso.b2clogin.com/`)<br>
     2\. AAD B2C 테 넌 트 도메인 (예: `contoso.onmicrosoft.com`)

   * 앱을 등록 하는 동안 다음 항목을 선택 하 &ndash; [웹 응용 프로그램을 등록](/azure/active-directory-b2c/tutorial-register-applications) 합니다.

     1\. **웹 앱/웹 API** 를 **예**로 설정 합니다.<br>
     2\. **암시적 흐름 허용** 을 **예**로 설정 합니다.<br>
     3\. `https://localhost:5001/authentication/login-callback`**회신 URL** 을 추가 합니다.

     응용 프로그램 ID (클라이언트 ID)를 기록 합니다 (예: `11111111-1111-1111-1111-111111111111`).

   * [사용자](/azure/active-directory-b2c/tutorial-create-user-flows) 흐름 &ndash; 만들어 등록 및 로그인 사용자 흐름을 만듭니다.

     최소한 **응용 프로그램 클레임** > **표시 이름** 사용자 특성을 선택 하 여 `LoginDisplay` 구성 요소 (*Shared/LoginDisplay*)에 `context.User.Identity.Name`를 채웁니다.

     앱에 대해 만든 등록 및 로그인 사용자 흐름 이름을 기록 합니다 (예: `B2C_1_signupsignin`).

1. 다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   출력 위치를 지정 하려면 프로젝트 폴더를 만드는 경우 (예: `-o BlazorSample`) 경로를 사용 하 여 명령에 출력 옵션을 포함 합니다 (예:). 또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.

## <a name="authentication-package"></a>인증 패키지

`IndividualB2C`(개별 B2C 계정)를 사용 하도록 앱을 만들면 앱은`Microsoft.Authentication.WebAssembly.Msal`( [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) )에 대 한 패키지 참조를 자동으로 받습니다. 패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.

앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

이전 패키지 참조의 `{VERSION}`를 <xref:blazor/get-started> 문서에 표시 된 `Microsoft.AspNetCore.Blazor.Templates` 패키지의 버전으로 바꿉니다.

`Microsoft.Authentication.WebAssembly.Msal` 패키지는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 앱에 전이적으로 추가 합니다.

## <a name="authentication-service-support"></a>인증 서비스 지원

사용자 인증에 대 한 지원은 `Microsoft.Authentication.WebAssembly.Msal` 패키지에서 제공 하는 `AddMsalAuthentication` 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다. 이 메서드는 앱이 IP (Id 공급자)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

`AddMsalAuthentication` 메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다. 앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 가져올 수 있습니다.

Blazor Weasembmbomtemplate은 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다. 토큰을 로그인 흐름의 일부로 프로 비전 하려면 `MsalProviderOptions`의 기본 액세스 토큰 범위에 범위를 추가 합니다.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{API ID URI}/{SCOPE}");
});
```

## <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>앱 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 구성 요소

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>인증 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 리소스

* <xref:security/authentication/azure-ad-b2c>
* [자습서: Azure Active Directory B2C 테넌트 만들기](/azure/active-directory-b2c/tutorial-create-tenant)
