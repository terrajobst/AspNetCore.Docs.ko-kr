---
title: Id 서버를 사용 하 여 ASP.NET Core Blazor Weasembomommbommboman 호스팅된 앱 보호
author: guardrex
description: '[IdentityServer](https://identityserver.io/) 백 엔드를 사용 하는 Visual Studio 내에서 인증을 사용 하 여 새 Blazor 호스팅된 앱을 만들려면'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 98eb126ab3c483e0a6dc2274db8ffcfd9d5bc59a
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083610"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Id 서버를 사용 하 여 ASP.NET Core Blazor Weasembomommbommboman 호스팅된 앱 보호

[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Visual Studio에서 [IdentityServer](https://identityserver.io/) 를 사용 하 여 사용자 및 API 호출을 인증 하는 새 Blazor 호스팅된 앱을 만들려면 다음을 수행 합니다.

1. Visual Studio를 사용 하 여 새 **Blazor Weasembomapp** 을 만듭니다. 자세한 내용은 <xref:blazor/get-started>을 참조하세요.
1. **새 Blazor 앱 만들기** 대화 상자의 **인증** 섹션에서 **변경** 을 선택 합니다.
1. **개별 사용자 계정** , **확인을**차례로 선택 합니다.
1. **고급** 섹션에서 **호스팅된 ASP.NET Core** 확인란을 선택 합니다.
1. **만들기** 단추를 선택합니다.

명령 셸에서 앱을 만들려면 다음 명령을 실행 합니다.

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

출력 위치를 지정 하려면 프로젝트 폴더를 만드는 경우 (예: `-o BlazorSample`) 경로를 사용 하 여 명령에 출력 옵션을 포함 합니다 (예:). 또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.

## <a name="server-app-configuration"></a>서버 앱 구성

다음 섹션에서는 인증 지원을 포함 하는 경우 프로젝트에 대 한 추가 설명입니다.

### <a name="startup-class"></a>시작 클래스

`Startup` 클래스에는 다음과 같은 추가 기능이 있습니다.

* `Startup.ConfigureServices`의 경우:

  * 기본 UI를 사용 하는 id:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer IdentityServer 위에 몇 가지 기본 ASP.NET Core 규칙을 설정 하는 추가 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 도우미 메서드를 사용 합니다.

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * IdentityServer에서 생성 한 JWT 토큰의 유효성을 검사 하도록 앱을 구성 하는 추가 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 도우미 메서드를 사용 하 여 인증 합니다.

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure`의 경우:

  * 요청 자격 증명의 유효성을 검사 하 고 요청 컨텍스트에서 사용자를 설정 해야 하는 인증 미들웨어입니다.

    ```csharp
    app.UseAuthentication();
    ```

  * OIDC (Open ID Connect) 끝점을 노출 하는 IdentityServer 미들웨어입니다.

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 도우미 메서드는 ASP.NET Core 시나리오에 대해 [IdentityServer](https://identityserver.io/) 를 구성 합니다. IdentityServer는 앱 보안 문제를 처리 하기 위한 강력 하 고 확장 가능한 프레임 워크입니다. IdentityServer는 가장 일반적인 시나리오에 대 한 불필요 한 복잡성을 노출 합니다. 따라서 유용한 시작 지점을 고려 하는 규칙 집합과 구성 옵션이 제공 됩니다. 인증을 변경 해야 하는 경우 앱의 요구 사항에 맞게 인증을 사용자 지정 하기 위해 IdentityServer의 모든 기능을 계속 사용할 수 있습니다.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 도우미 메서드는 앱에 대 한 정책 스키마를 기본 인증 처리기로 구성 합니다. 이 정책은 id URL 공간 `/Identity`의 하위 경로에 라우팅되는 모든 요청을 Id가 처리할 수 있도록 구성 됩니다. <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>는 다른 모든 요청을 처리 합니다. 또한이 메서드는 다음과 같습니다.

* IdentityServer를 사용 하 여 `{APPLICATION NAME}API` API 리소스를 등록 하 고 기본 범위 `{APPLICATION NAME}API`를 사용 합니다.
* 앱에 대해 IdentityServer에서 발급 한 토큰의 유효성을 검사 하도록 JWT 전달자 토큰 미들웨어를 구성 합니다.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

`WeatherForecastController` (*controller/WeatherForecastController*)에서 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 클래스에 적용 됩니다. 특성은 리소스에 액세스 하기 위한 기본 정책에 따라 사용자에 게 권한이 부여 되어야 함을 나타냅니다. 기본 권한 부여 정책은 앞에서 언급 한 정책 체계에 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 하 여 설정 되는 기본 인증 체계를 사용 하도록 구성 됩니다. 도우미 메서드는 앱에 대 한 요청에 대 한 기본 처리기로 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>를 구성 합니다.

### <a name="applicationdbcontext"></a>ApplicationDbContext

`ApplicationDbContext` (*Data/ApplicationDbContext*)에서 IdentityServer에 대 한 스키마를 포함 하도록 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>를 확장 한다는 것을 제외 하 고 id에서 동일한 <xref:Microsoft.EntityFrameworkCore.DbContext> 사용 됩니다. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>에서 파생됩니다.

데이터베이스 스키마에 대 한 모든 권한을 얻으려면 사용 가능한 Id <xref:Microsoft.EntityFrameworkCore.DbContext> 클래스 중 하나에서 상속 하 고 `OnModelCreating` 메서드에서 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`를 호출 하 여 Id 스키마를 포함 하도록 컨텍스트를 구성 합니다.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

`OidcConfigurationController` (*controller/OidcConfigurationController*)에서 클라이언트 끝점은 oidc 매개 변수를 제공 하도록 프로 비전 됩니다.

### <a name="app-settings-files"></a>앱 설정 파일

프로젝트 루트의 앱 설정 파일 (*appsettings*)에서 `IdentityServer` 섹션은 구성 된 클라이언트 목록에 대해 설명 합니다. 다음 예제에는 단일 클라이언트가 있습니다. 클라이언트 이름은 앱 이름에 해당 하며 규칙에 따라 OAuth `ClientId` 매개 변수에 매핑됩니다. 프로필은 구성 중인 앱 유형을 나타냅니다. 프로필은 서버에 대 한 구성 프로세스를 간소화 하는 규칙을 구동 하기 위해 내부적으로 사용 됩니다. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

개발 환경 앱 설정 파일 (*appsettings. Development. json*) `IdentityServer` 섹션에서는 토큰에 서명 하는 데 사용 되는 키에 대해 설명 합니다. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>클라이언트 앱 구성

### <a name="authentication-package"></a>인증 패키지

개별 사용자 계정 (`Individual`)을 사용 하도록 앱을 만들면 앱은 앱의 프로젝트 파일에서 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지에 대 한 패키지 참조를 자동으로 받습니다. 패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.

앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

이전 패키지 참조의 `{VERSION}`를 <xref:blazor/get-started> 문서에 표시 된 `Microsoft.AspNetCore.Blazor.Templates` 패키지의 버전으로 바꿉니다.

### <a name="api-authorization-support"></a>API 권한 부여 지원

사용자 인증에 대 한 지원은 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지 내에서 제공 된 확장 메서드에 의해 서비스 컨테이너에 연결 됩니다. 이 메서드는 앱이 기존 인증 시스템과 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.

```csharp
builder.Services.AddApiAuthorization();
```

기본적으로 `_configuration/{client-id}`에서 규칙에 따라 앱에 대 한 구성을 로드 합니다. 규칙에 따라 클라이언트 ID는 앱의 어셈블리 이름으로 설정 됩니다. 옵션으로 오버 로드를 호출 하 여 별도의 끝점을 가리키도록이 URL을 변경할 수 있습니다.

### <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a>앱 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay 구성 요소

`LoginDisplay` 구성 요소 (*shared/LoginDisplay*)는 `MainLayout` 구성 요소 (*Shared/mainlayout. razor*)에서 렌더링 되며 다음 동작을 관리 합니다.

* 인증 된 사용자의 경우:
  * 현재 사용자 이름을 표시 합니다.
  * ASP.NET Core Id의 사용자 프로필 페이지에 대 한 링크를 제공 합니다.
  * 앱에서 로그 아웃할 수 있는 단추를 제공 합니다.
* 익명 사용자의 경우:
  * 등록할 수 있는 옵션을 제공 합니다.
  * 로그인 할 수 있는 옵션을 제공 합니다.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>인증 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData 구성 요소

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
