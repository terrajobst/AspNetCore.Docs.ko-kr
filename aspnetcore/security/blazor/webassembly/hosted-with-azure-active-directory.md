---
title: Azure Active Directory를 사용 하 여 ASP.NET Core Blazor Weasembomommbommboman 호스팅된 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: fc16a7212254e73efd4cea8155975f293e5d9ebb
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219287"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Azure Active Directory를 사용 하 여 ASP.NET Core Blazor Weasembomommbommboman 호스팅된 앱 보호

[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



이 문서에서는 인증을 위해 [AAD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) 를 사용 하는 [Blazor Weasembomhosted 앱](xref:blazor/hosting-models#blazor-webassembly) 을 만드는 방법을 설명 합니다.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>AAD B2C에 앱 등록 및 솔루션 만들기

### <a name="create-a-tenant"></a>테넌트 만들기

[빠른 시작: 테 넌 트를 설정](/azure/active-directory/develop/quickstart-create-new-tenant) 하 여 AAD에서 테 넌 트 만들기의 지침을 따릅니다.

### <a name="register-a-server-api-app"></a>서버 API 앱 등록

[빠른 시작: Microsoft id 플랫폼](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD에 응용 프로그램 등록 항목의 지침에 따라 Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 *서버 API 앱* 에 대 한 AAD 앱을 등록 합니다.

1. **새 등록**을 선택합니다.
1. 앱에 대 한 **이름** (예: **Blazor Server AAD**)을 제공 합니다.
1. **지원 되는 계정 유형을**선택 합니다. 이 환경 **에서는이 조직 디렉터리에만 계정** (단일 테 넌 트)을 선택할 수 있습니다.
1. *서버 API 앱* 은이 시나리오에서 **리디렉션 uri** 를 요구 하지 않으므로 드롭다운을 **웹** 으로 설정 하 고 리디렉션 uri를 입력 하지 않도록 합니다.
1. **권한을** 사용 하지 않도록 설정 > **관리자 동의 하도록 요구에 게 openid connect 및 offline_access 권한 부여 확인란을** 선택 합니다.
1. **등록**을 선택합니다.

**API 권한**에서 사용자 > **Microsoft Graph** 을 제거 **합니다.** 앱에 로그인 또는 Uer 프로필 액세스가 필요 하지 않으므로 읽기 권한이 필요 합니다.

**API 노출**:

1. **범위 추가**를 선택합니다.
1. **저장 및 계속**을 선택합니다.
1. **범위 이름을** 제공 합니다 (예: `API.Access`).
1. **관리자 동의 표시 이름** (예: `Access API`)을 제공 합니다.
1. **관리자 동의 설명** (예: `Allows the app to access server app API endpoints.`)을 제공 합니다.
1. **상태** 가 **사용**으로 설정 되어 있는지 확인 합니다.
1. **범위 추가**를 선택 합니다.

다음 정보를 기록 합니다.

* *서버 API 앱* 응용 프로그램 ID (클라이언트 ID) (예: `11111111-1111-1111-1111-111111111111`)
* 디렉터리 ID (테 넌 트 ID) (예: `222222222-2222-2222-2222-222222222222`)
* AAD 테 넌 트 도메인 (예: `contoso.onmicrosoft.com`)
* 기본 범위 (예: `API.Access`)

### <a name="register-a-client-app"></a>클라이언트 앱 등록

[빠른 시작: Microsoft id 플랫폼](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD에 응용 프로그램 등록 항목의 지침에 따라 Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 *클라이언트 앱* 에 대 한 AAD 앱을 등록 합니다.

1. **새 등록**을 선택합니다.
1. 앱에 대 한 **이름** (예: **Blazor Client AAD**)을 제공 합니다.
1. **지원 되는 계정 유형을**선택 합니다. 이 환경 **에서는이 조직 디렉터리에만 계정** (단일 테 넌 트)을 선택할 수 있습니다.
1. **리디렉션 uri** 드롭다운을 **웹**으로 설정 된 상태로 두고 `https://localhost:5001/authentication/login-callback`의 리디렉션 uri를 제공 합니다.
1. **권한을** 사용 하지 않도록 설정 > **관리자 동의 하도록 요구에 게 openid connect 및 offline_access 권한 부여 확인란을** 선택 합니다.
1. **등록**을 선택합니다.

**웹** > **인증** > **플랫폼 구성** 에서 다음을 수행 합니다.

1. `https://localhost:5001/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인 합니다.
1. **암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.
1. 앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.
1. **저장** 단추를 선택합니다.

**API 사용 권한**:

1. 앱이 > 사용자 **Microsoft Graph** 있는지 확인 **합니다. 읽기** 권한입니다.
1. **권한 추가를** 선택 하 고 **내 api**를 추가 합니다.
1. **이름** 열에서 *서버 API 앱* 을 선택 합니다 (예: **Blazor server AAD**).
1. **API** 목록을 엽니다.
1. API에 대 한 액세스를 사용 하도록 설정 합니다 (예: `API.Access`).
1. **권한 추가**를 선택합니다.
1. **{테 넌 트 이름}에 대 한 관리자 콘텐츠 부여** 단추를 선택 합니다. **예**를 선택하여 확인합니다.

*클라이언트 앱* 응용 프로그램 Id (클라이언트 id)를 기록 합니다 (예: `33333333-3333-3333-3333-333333333333`).

### <a name="create-the-app"></a>앱 만들기

다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP CLIENT ID}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

출력 위치를 지정 하려면 프로젝트 폴더를 만드는 경우 (예: `-o BlazorSample`) 경로를 사용 하 여 명령에 출력 옵션을 포함 합니다 (예:). 또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.

> [!NOTE]
> 기본 액세스 토큰 범위에 대 한 중요 한 구성 변경은 [인증 서비스 지원](#Authentication service support) 섹션을 참조 하세요. Blazor Weasembomtemplate에서 제공 하는 값은 *클라이언트 앱* 이 템플릿에서 만들어진 후 수동으로 변경 해야 합니다.

## <a name="server-app-configuration"></a>서버 앱 구성

*이 섹션은 솔루션의 **서버** 앱과 관련이 있습니다.*

### <a name="authentication-package"></a>인증 패키지

ASP.NET Core 웹 Api에 대 한 호출을 인증 하 고 권한을 부여 하는 데 대 한 지원은 `Microsoft.AspNetCore.Authentication.AzureAD.UI`제공 됩니다.

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>인증 서비스 지원

`AddAuthentication` 메서드는 앱 내에서 인증 서비스를 설정 하 고 JWT 전달자 처리기를 기본 인증 방법으로 구성 합니다. `AddAzureADBearer` 메서드는 Azure Active Directory에서 내보낸 토큰의 유효성을 검사 하는 데 필요한 JWT 전달자 처리기의 특정 매개 변수를 설정 합니다.

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication` 하 고 `UseAuthorization` 다음을 확인 합니다.

* 앱에서 들어오는 요청에 대 한 토큰을 구문 분석 하 고 유효성을 검사 하려고 합니다.
* 적절 한 자격 증명 없이 보호 된 리소스에 액세스 하려고 시도 하는 모든 요청은 실패 합니다.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a>앱 설정

*Appsettings* 파일에는 액세스 토큰의 유효성을 검사 하는 데 사용 되는 JWT 전달자 처리기를 구성 하는 옵션이 포함 되어 있습니다.

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

### <a name="weatherforecast-controller"></a>WeatherForecast 컨트롤러

WeatherForecast 컨트롤러 (controller */WeatherForecastController*)는 컨트롤러에 적용 된 `[Authorize]` 특성을 사용 하 여 보호 된 API를 노출 합니다. 다음을 이해 하는 것이 **중요** 합니다.

* 이 api 컨트롤러의 `[Authorize]` 특성은 무단 액세스 로부터이 API를 보호 하는 유일한 방법입니다.
* Blazor Weasembomapp에 사용 되는 `[Authorize]` 특성은 사용자가 앱이 올바르게 작동 하도록 권한이 부여 되어야 한다는 것을 앱에 대 한 힌트로만 사용 됩니다.

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

*이 섹션은 솔루션의 **클라이언트** 앱과 관련이 있습니다.*

### <a name="authentication-package"></a>인증 패키지

회사 또는 학교 계정 (`SingleOrg`)을 사용 하도록 앱을 만들 때 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`)에 대 한 패키지 참조를 자동으로 받습니다. 패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.

앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

이전 패키지 참조의 `{VERSION}`를 <xref:blazor/get-started> 문서에 표시 된 `Microsoft.AspNetCore.Blazor.Templates` 패키지의 버전으로 바꿉니다.

`Microsoft.Authentication.WebAssembly.Msal` 패키지는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 앱에 전이적으로 추가 합니다.

### <a name="authentication-service-support"></a>인증 서비스 지원

사용자 인증에 대 한 지원은 `Microsoft.Authentication.WebAssembly.Msal` 패키지에서 제공 하는 `AddMsalAuthentication` 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다. 이 메서드는 앱이 IP (Id 공급자)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.

*Program.cs*:

*클라이언트 앱* 이 생성 될 때 기본 액세스 토큰 범위는 `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`형식입니다. **범위 값의 `api://` 부분을 제거 합니다.** 이 문제는 이후 미리 보기 릴리스에서 해결 될 예정입니다.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> 기본 액세스 토큰 범위는 `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` 형식 이어야 합니다 (예: `11111111-1111-1111-1111-111111111111/API.Access`). Azure Portal에 표시 된 대로 범위 설정에 스키마 또는 스키마 및 호스트를 제공 하는 경우 *클라이언트 앱* 은 *서버 API 앱*에서 *401 권한 없음* 응답을 받을 때 처리 되지 않은 예외를 throw 합니다.

`AddMsalAuthentication` 메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다. 앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 가져올 수 있습니다.

기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위의 목록을 나타냅니다.

* 로그인 요청에 기본적으로 포함 됩니다.
* 인증 후 즉시 액세스 토큰을 프로 비전 하는 데 사용 됩니다.

모든 범위는 Azure Active Directory 규칙에 따라 동일한 앱에 속해야 합니다. 필요에 따라 추가 API 앱에 대 한 추가 범위를 추가할 수 있습니다.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{SCOPE}");
});
```

### <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>앱 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay 구성 요소

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>인증 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData 구성 요소

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>앱 실행

서버 프로젝트에서 앱을 실행 합니다. Visual Studio를 사용 하는 경우 **솔루션 탐색기** 에서 서버 프로젝트를 선택 하 고 도구 모음에서 **실행** 단추를 선택 하거나 **디버그** 메뉴에서 앱을 시작 합니다.

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 리소스

* <xref:security/authentication/azure-active-directory/index>
