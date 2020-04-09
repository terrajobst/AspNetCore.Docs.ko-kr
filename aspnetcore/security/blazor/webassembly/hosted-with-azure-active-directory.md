---
title: Azure active Blazor 디렉터리로 ASP.NET 코어 웹어셈블리 호스팅 앱 보안
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8fec9f585f42469665cf29069674a199e1626629
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977134"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Azure active Blazor 디렉터리로 ASP.NET 코어 웹어셈블리 호스팅 앱 보안

[하비에르 칼바로 넬슨과](https://github.com/javiercn) [루크 라담](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



이 문서에서는 인증을 위해 [AAD(Azure Active Directory)를](https://azure.microsoft.com/services/active-directory/) 사용하는 [ Blazor 웹 Assembly 호스팅 앱을](xref:blazor/hosting-models#blazor-webassembly) 만드는 방법에 대해 설명합니다.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>AAD B2C에 앱을 등록하고 솔루션을 만듭니다.

### <a name="create-a-tenant"></a>테넌트 만들기

[Quickstart: 테넌트를 설정하여](/azure/active-directory/develop/quickstart-create-new-tenant) AAD에서 테넌트를 만듭니다.

### <a name="register-a-server-api-app"></a>서버 API 앱 등록

[빠른 시작의 지침에 따라: Azure Id 플랫폼](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD 항목에 응용 프로그램을 등록하여 **Azure Active Directory** > 앱 등록 영역에서 *서버 API 앱에* 대한 AAD 앱을**등록합니다.**

1. **새 등록**을 선택합니다.
1. 앱의 **이름을** 제공합니다(예: ** Blazor 서버 AAD).**
1. **지원되는 계정 유형을**선택합니다. 이 경험에 대해 **이 조직 디렉터리에서 계정만(단일** 테넌트)을 선택할 수 있습니다.
1. 이 시나리오에서는 *Server API 앱에* **리디렉션 URI가** 필요하지 않으므로 드롭다운을 **웹으로** 설정하고 리디렉션 URI를 입력하지 않습니다.
1. 사용 **권한** > 부여 관리자 를 사용 권한 으로 설정 해제 하 고 offline_access 사용 권한 확인란을 사용 하지 않도록 설정**합니다.**
1. **등록**을 선택합니다.

**API 권한에서**앱에 로그인 또는 uer 프로필 액세스가 필요하지 않으므로 **Microsoft Graph** > **User.Read** 권한을 제거합니다.

**API 노출시**:

1. **범위 추가를**선택합니다.
1. **저장 및 계속**을 선택합니다.
1. 범위 **이름(예:** )을 `API.Access`제공합니다.
1. 관리자 **동의 표시** 이름(예: `Access API`)을 제공합니다.
1. 관리자 **동의** 설명(예: `Allows the app to access server app API endpoints.`)을 제공합니다.
1. **상태가** **사용 설정으로**설정되어 있는지 확인합니다.
1. **범위 추가를**선택합니다.

다음 정보를 기록합니다.

* *서버 API 앱* 응용 프로그램 ID(클라이언트 ID) `11111111-1111-1111-1111-111111111111`(예: )
* 앱 ID `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`URI(예: `api://11111111-1111-1111-1111-111111111111`, 또는 제공한 사용자 지정 값)
* 디렉터리 ID(예: `222222222-2222-2222-2222-222222222222`테넌트 ID)
* AAD 테넌트 도메인(예: `contoso.onmicrosoft.com`)
* 기본 범위(예: `API.Access`)

### <a name="register-a-client-app"></a>클라이언트 앱 등록

[빠른 시작의 지침에 따라: Azure ID 플랫폼](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD 항목에 응용 프로그램을 등록하여 Azure Active **Directory** > 앱 등록 영역에서 *클라이언트 앱에* 대한 AAD 앱을**등록합니다.**

1. **새 등록**을 선택합니다.
1. 앱의 **이름을** 제공합니다(예: ** Blazor 클라이언트 AAD).**
1. **지원되는 계정 유형을**선택합니다. 이 경험에 대해 **이 조직 디렉터리에서 계정만(단일** 테넌트)을 선택할 수 있습니다.
1. 리디렉션 **URI** 드롭다운을 **웹으로**설정하고 의 `https://localhost:5001/authentication/login-callback`리디렉션 URI를 제공합니다.
1. 사용 **권한** > 부여 관리자 를 사용 권한 으로 설정 해제 하 고 offline_access 사용 권한 확인란을 사용 하지 않도록 설정**합니다.**
1. **등록**을 선택합니다.

**인증** > **플랫폼 구성** > **웹에서**:

1. `https://localhost:5001/authentication/login-callback` 리디렉션 **URI가** 있는지 확인합니다.
1. **암시적 부여의**경우 Access **토큰** 및 ID 토큰에 대한 **확인란을 선택합니다.**
1. 앱의 나머지 기본값은 이 경험에 사용할 수 있습니다.
1. **저장** 단추를 선택합니다.

**API 권한:**

1. 앱에 Microsoft **그래프** > **User.Read** 권한이 있는지 확인합니다.
1. **권한 추가** 다음에 **내 API를**선택합니다.
1. **이름** 열(예: ** Blazor 서버 AAD)에서 서버** *API 앱을* 선택합니다.
1. **API** 목록을 엽니다.
1. API에 대한 액세스를 활성화합니다(예: `API.Access`).
1. **권한 추가**를 선택합니다.
1. **{테넌트 이름}** 단추에 대한 권한 부여 관리자 콘텐츠를 선택합니다. **예**를 선택하여 확인합니다.

클라이언트 *앱* 응용 프로그램 ID(예: `33333333-3333-3333-3333-333333333333`클라이언트 ID)를 기록합니다.

### <a name="create-the-app"></a>앱 만들기

다음 명령의 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행합니다.

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

프로젝트 폴더가 없는 경우 만드는 출력 위치를 지정하려면 경로가 있는 명령에 출력 옵션을 포함합니다(예: `-o BlazorSample`). 폴더 이름도 프로젝트 이름의 일부가 됩니다.

> [!NOTE]
> 앱 ID URI를 `app-id-uri` 옵션에 전달하지만 [Access 토큰 범위](#access-token-scopes) 섹션에 설명된 클라이언트 앱에서 구성 변경이 필요할 수 있습니다.

## <a name="server-app-configuration"></a>서버 앱 구성

*이 섹션에서는 솔루션의 **서버** 앱과 관련이 있습니다.*

### <a name="authentication-package"></a>인증 패키지

ASP.NET 코어 웹 API에 대한 호출을 인증하고 권한을 부여하기 위한 지원은 `Microsoft.AspNetCore.Authentication.AzureAD.UI`다음에서 제공합니다.

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>인증 서비스 지원

메서드는 `AddAuthentication` 앱 내에서 인증 서비스를 설정하고 JWT 베어러 처리기를 기본 인증 방법으로 구성합니다. 메서드는 `AddAzureADBearer` Azure Active Directory에서 내보낸 토큰의 유효성을 검사하는 데 필요한 JWT 베어러 처리기에서 특정 매개 변수를 설정합니다.

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`다음을 수행하십시오. `UseAuthorization`

* 앱은 들어오는 요청에 대해 토큰을 구문 분석하고 유효성을 검사하려고 시도합니다.
* 적절한 자격 증명 없이 보호된 리소스에 액세스하려는 모든 요청이 실패합니다.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

기본적으로 서버 앱 API는 `User.Identity.Name` `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 클레임 유형(예: `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`)의 값으로 채워집니다.

`name` 클레임 유형에서 값을 받도록 앱을 구성하려면 [토큰 유효성 검사매개 변수.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 을 구성합니다. `Startup.ConfigureServices`

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>앱 설정

*appsettings.json* 파일에는 액세스 토큰의 유효성을 검사하는 데 사용되는 JWT 베어러 처리기를 구성하는 옵션이 포함되어 있습니다.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a>일기 예보 컨트롤러

웨더 예측 컨트롤러(컨트롤러/WeatherForecastController.cs)는 컨트롤러에 적용된 `[Authorize]` 특성으로 보호된 API를*노출합니다.* 다음을 이해하는 **것이 중요합니다.**

* 이 `[Authorize]` API 컨트롤러의 특성은 이 API를 무단 액세스로부터 보호하는 유일한 특성입니다.
* WebAssembly 앱에 사용되는 특성은 `[Authorize]` 사용자가 앱이 올바르게 작동하도록 승인해야 한다는 힌트일 뿐입니다. Blazor

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>클라이언트 앱 구성

*이 섹션에서는 솔루션의 **클라이언트** 앱과 관련이 있습니다.*

### <a name="authentication-package"></a>인증 패키지

직장 또는 학교 계정()을`SingleOrg`사용하도록 앱을 만들면 앱은 Microsoft [인증 라이브러리()에](/azure/active-directory/develop/msal-overview) `Microsoft.Authentication.WebAssembly.Msal`대한 패키지 참조를 자동으로 받습니다. 이 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 토큰을 얻는 데 도움이 되는 기본 요소 집합을 제공합니다.

앱에 인증을 추가하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가합니다.

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

앞의 패키지 참조에서 문서에 표시된 `{VERSION}` `Microsoft.AspNetCore.Blazor.Templates` 패키지 버전으로 <xref:blazor/get-started> 바꿉습니다.

패키지는 `Microsoft.Authentication.WebAssembly.Msal` 앱에 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 전이적으로 추가합니다.

### <a name="authentication-service-support"></a>인증 서비스 지원

사용자 인증에 대한 지원은 패키지에서 제공하는 `AddMsalAuthentication` 확장 방법으로 `Microsoft.Authentication.WebAssembly.Msal` 서비스 컨테이너에 등록됩니다. 이 메서드는 앱이 IP(ID 공급자)와 상호 작용하는 데 필요한 모든 서비스를 설정합니다.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

메서드는 `AddMsalAuthentication` 응용 프로그램을 인증 하는 데 필요한 매개 변수를 구성 하는 콜백을 허용 합니다. 앱을 구성하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 얻을 수 있습니다.

### <a name="access-token-scopes"></a>토큰 범위에 액세스

기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위 목록을 나타냅니다.

* 로그인 요청에 기본적으로 포함됩니다.
* 인증 직후 액세스 토큰을 프로비전하는 데 사용됩니다.

모든 범위는 Azure Active Directory 규칙당 동일한 앱에 속해야 합니다. 필요에 따라 추가 API 앱에 대한 추가 범위를 추가할 수 있습니다.

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

### <a name="imports-file"></a>파일 가져오기

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>앱 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>리디렉션토로그인 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>로그인표시 구성 요소

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>인증 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>가져오기데이터 구성 요소

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>앱 실행

서버 프로젝트에서 앱을 실행합니다. Visual Studio를 사용하는 경우 **솔루션 탐색기에서** 서버 프로젝트를 선택하고 도구 모음에서 **실행** 단추를 선택하거나 **디버그** 메뉴에서 앱을 시작합니다.

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 리소스

* [추가 액세스 토큰 요청](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [Microsoft ID 플랫폼 설명서](/azure/active-directory/develop/)
