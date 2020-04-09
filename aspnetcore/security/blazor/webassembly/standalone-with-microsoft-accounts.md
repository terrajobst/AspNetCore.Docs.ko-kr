---
title: Microsoft 계정으로 Blazor ASP.NET 핵심 웹어셈블리 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977082"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Microsoft 계정으로 Blazor ASP.NET 핵심 웹어셈블리 독립 실행형 앱 보호

[하비에르 칼바로 넬슨과](https://github.com/javiercn) [루크 라담](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

인증을 위해 Blazor [AAD(Azure Active Directory)를 사용하여 Microsoft 계정을](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) 사용하는 웹 Assembly 독립 실행형 앱을 만들려면 다음을 수행합니다.

1. [AAD 테넌트 및 웹 응용 프로그램 만들기](/azure/active-directory/develop/v2-overview)

   Azure 포털의 **Azure Active Directory** > 앱 등록 영역에 AAD**앱을 등록합니다.**

   1\. 앱의 **이름을** 제공합니다(예: ** Blazor 클라이언트 AAD).**<br>
   2\. **지원되는 계정 유형에서** **모든 조직 디렉터리에서 계정을**선택합니다.<br>
   3\. 리디렉션 **URI** 드롭다운을 **웹으로**설정하고 의 `https://localhost:5001/authentication/login-callback`리디렉션 URI를 제공합니다.<br>
   4\. 사용 **권한** > 부여 관리자 를 사용 권한 으로 설정 해제 하 고 offline_access 사용 권한 확인란을 사용 하지 않도록 설정**합니다.**<br>
   5\. **등록**을 선택합니다.

   **인증** > **플랫폼 구성** > **웹에서**:

   1\. `https://localhost:5001/authentication/login-callback` 리디렉션 **URI가** 있는지 확인합니다.<br>
   2\. **암시적 부여의**경우 Access **토큰** 및 ID 토큰에 대한 **확인란을 선택합니다.**<br>
   3\. 앱의 나머지 기본값은 이 경험에 사용할 수 있습니다.<br>
   4\. **저장** 단추를 선택합니다.

   응용 프로그램 ID(클라이언트 ID)를 `11111111-1111-1111-1111-111111111111`기록합니다(예: ).

1. 다음 명령의 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   프로젝트 폴더가 없는 경우 만드는 출력 위치를 지정하려면 경로가 있는 명령에 출력 옵션을 포함합니다(예: `-o BlazorSample`). 폴더 이름도 프로젝트 이름의 일부가 됩니다.

앱을 만든 후에는 다음을 수행할 수 있어야 합니다.

* Microsoft 계정을 사용하여 앱에 로그인합니다.
* 앱을 올바르게 구성한 경우 제공되는 독립 실행형 Blazor 앱과 동일한 접근 방식을 사용하여 Microsoft API에 대한 액세스 토큰을 요청합니다. 자세한 내용은 [빠른 시작: 웹 API를 노출하도록 응용 프로그램 구성을](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)참조하십시오.

## <a name="authentication-package"></a>인증 패키지

직장 또는 학교 계정()을`SingleOrg`사용하도록 앱을 만들면 앱은 Microsoft [인증 라이브러리()에](/azure/active-directory/develop/msal-overview) `Microsoft.Authentication.WebAssembly.Msal`대한 패키지 참조를 자동으로 받습니다. 이 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 토큰을 얻는 데 도움이 되는 기본 요소 집합을 제공합니다.

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
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

메서드는 `AddMsalAuthentication` 응용 프로그램을 인증 하는 데 필요한 매개 변수를 구성 하는 콜백을 허용 합니다. 앱을 구성하는 데 필요한 값은 앱을 등록할 때 Microsoft 계정 구성에서 얻을 수 있습니다.

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
* [빠른 시작: Microsoft ID 플랫폼에 응용 프로그램 등록](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [빠른 시작: 웹 API를 노출하도록 응용 프로그램 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
