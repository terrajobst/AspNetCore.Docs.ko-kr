---
title: Azure Active Blazor Directory B2C를 통해 ASP.NET 핵심 웹어셈블리 독립 실행형 앱 보안
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0734bad2d4281eb856783a362ef8c608a303c17a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977056"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Azure Active Blazor Directory B2C를 통해 ASP.NET 핵심 웹어셈블리 독립 실행형 앱 보안

[하비에르 칼바로 넬슨과](https://github.com/javiercn) [루크 라담](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

인증을 위해 Blazor [AAD(Azure Active Directory) B2C를](/azure/active-directory-b2c/overview) 사용하는 웹 Assembly 독립 실행형 앱을 만들려면 다음을 수행합니다.

1. 다음 항목의 지침을 따라 테넌트를 만들고 Azure Portal에서 웹 앱을 등록합니다.

   * [AAD B2C 테넌트](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; 만들기 다음 정보를 기록합니다.

     1\. AAD B2C 인스턴스(예: `https://contoso.b2clogin.com/`후행 슬래시포함)<br>
     2\. AAD B2C 테넌트 `contoso.onmicrosoft.com`도메인(예: )

   * [웹 응용 프로그램](/azure/active-directory-b2c/tutorial-register-applications) &ndash; 등록 앱 등록 중에 다음 을 선택합니다.

     1\. **웹 앱 / 웹 API를** **예로**설정합니다.<br>
     2\. 설정 **암시적 흐름을** 예로 **허용합니다.**<br>
     3\. 의 `https://localhost:5001/authentication/login-callback` **회신 URL을** 추가합니다.

     응용 프로그램 ID(클라이언트 ID)를 `11111111-1111-1111-1111-111111111111`기록합니다(예: ).

   * [사용자 흐름](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; 만들기 등록 및 로그인 사용자 흐름을 만듭니다.

     최소한 `LoginDisplay` 응용 프로그램 **클레임** > **표시 이름** 사용자 속성을 `context.User.Identity.Name` 선택하여 구성 요소(공유/LoginDisplay.razor)에 채워집니다.*Shared/LoginDisplay.razor*

     앱에 대해 생성된 등록 및 로그인 사용자 흐름 이름을 기록합니다(예: `B2C_1_signupsignin`).

1. 다음 명령의 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   프로젝트 폴더가 없는 경우 만드는 출력 위치를 지정하려면 경로가 있는 명령에 출력 옵션을 포함합니다(예: `-o BlazorSample`). 폴더 이름도 프로젝트 이름의 일부가 됩니다.

## <a name="authentication-package"></a>인증 패키지

개별 B2C 계정()을`IndividualB2C`사용하도록 앱을 만들면 앱은 Microsoft 인증 [라이브러리()에](/azure/active-directory/develop/msal-overview) `Microsoft.Authentication.WebAssembly.Msal`대한 패키지 참조를 자동으로 받습니다. 이 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 토큰을 얻는 데 도움이 되는 기본 요소 집합을 제공합니다.

앱에 인증을 추가하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가합니다.

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

앞의 패키지 참조에서 문서에 표시된 `{VERSION}` `Microsoft.AspNetCore.Blazor.Templates` 패키지 버전으로 <xref:blazor/get-started> 바꿉습니다.

패키지는 `Microsoft.Authentication.WebAssembly.Msal` 앱에 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 전이적으로 추가합니다.

## <a name="authentication-service-support"></a>인증 서비스 지원

사용자 인증에 대한 지원은 패키지에서 제공하는 `AddMsalAuthentication` 확장 방법으로 `Microsoft.Authentication.WebAssembly.Msal` 서비스 컨테이너에 등록됩니다. 이 메서드는 앱이 IP(ID 공급자)와 상호 작용하는 데 필요한 모든 서비스를 설정합니다.

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

메서드는 `AddMsalAuthentication` 응용 프로그램을 인증 하는 데 필요한 매개 변수를 구성 하는 콜백을 허용 합니다. 앱을 구성하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 얻을 수 있습니다.

## <a name="access-token-scopes"></a>토큰 범위에 액세스

WebAssembly 템플릿은 Blazor 보안 API에 대한 액세스 토큰을 요청하도록 앱을 자동으로 구성하지 않습니다. 로그인 흐름의 일부로 토큰을 프로비전하려면 `MsalProviderOptions`다음의 기본 액세스 토큰 범위에 범위를 추가합니다.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Azure 포털이 범위 URI를 제공하고 API에서 *401 무단* 응답을 받을 때 **앱이 처리되지 않은 예외를 throw하는** 경우 구성표 및 호스트를 포함하지 않는 범위 URI를 사용해 보십시오. 예를 들어 Azure 포털은 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> 스키마 및 호스트 없이 범위 URI를 제공합니다.
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

자세한 내용은 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>을 참조하세요.

## <a name="imports-file"></a>파일 가져오기

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>앱 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>리디렉션토로그인 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>로그인표시 구성 요소

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>인증 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 리소스

* [추가 액세스 토큰 요청](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [자습서: Azure Active Directory B2C 테넌트 만들기](/azure/active-directory-b2c/tutorial-create-tenant)
* [Microsoft ID 플랫폼 설명서](/azure/active-directory/develop/)
