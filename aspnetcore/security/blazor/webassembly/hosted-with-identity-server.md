---
title: ID 서버를 Blazor 통해 ASP.NET 코어 웹어셈블리 호스팅 앱 보안
author: guardrex
description: '[IdentityServer](https://identityserver.io/) 백 Blazor 엔드를 사용하는 Visual Studio 내에서 인증을 사용하여 새 호스팅 앱을 만들려면'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501277"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>ID 서버를 Blazor 통해 ASP.NET 코어 웹어셈블리 호스팅 앱 보안

[하비에르 칼바로 넬슨과](https://github.com/javiercn) [루크 라담](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

[IdServer를](https://identityserver.io/) Blazor 사용하여 사용자 및 API 호출을 인증하는 Visual Studio에서 새 호스팅 앱을 만들려면 다음을 수행합니다.

1. Visual Studio를 사용하여 ** Blazor ** 새 웹 Assembly 앱을 만듭니다. 자세한 내용은 <xref:blazor/get-started>을 참조하세요.
1. **새 Blazor 앱 만들기** 대화 상자에서 **인증** 섹션에서 **변경을** 선택합니다.
1. **개인 사용자 계정을** 선택한 다음 **확인을**선택합니다.
1. **고급** 섹션에서 **ASP.NET 코어 호스팅** 확인란을 선택합니다.
1. **만들기** 단추를 선택합니다.

명령 셸에서 앱을 만들려면 다음 명령을 실행합니다.

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

프로젝트 폴더가 없는 경우 만드는 출력 위치를 지정하려면 경로가 있는 명령에 출력 옵션을 포함합니다(예: `-o BlazorSample`). 폴더 이름도 프로젝트 이름의 일부가 됩니다.

## <a name="server-app-configuration"></a>서버 앱 구성

다음 섹션에서는 인증 지원이 포함된 경우 프로젝트에 대한 추가 방법을 설명합니다.

### <a name="startup-class"></a>시작 클래스

클래스에는 `Startup` 다음과 같은 추가 가 있습니다.

* `Startup.ConfigureServices`의 경우:

  * 기본 UI가 있는 ID:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IDServer 위에 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 몇 가지 기본 ASP.NET 핵심 규칙을 설정 하는 추가 도우미 메서드를 사용 하 여 IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * IdentityServer에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 생성한 JWT 토큰의 유효성을 검사하기 위해 앱을 구성하는 추가 도우미 메서드를 사용한 인증:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure`의 경우:

  * 요청 자격 증명의 유효성을 검사하고 요청 컨텍스트에서 사용자를 설정하는 인증 미들웨어:

    ```csharp
    app.UseAuthentication();
    ```

  * 개방형 ID 연결(OIDC) 끝점을 노출하는 IdentityServer 미들웨어:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>추가 Api 권한 부여

도우미 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 메서드는 ASP.NET 코어 시나리오에 대 한 [IdentityServer를](https://identityserver.io/) 구성 합니다. IdentityServer는 앱 보안 문제를 처리하기 위한 강력하고 확장 가능한 프레임워크입니다. IdentityServer는 가장 일반적인 시나리오에 대해 불필요한 복잡성을 노출합니다. 따라서 좋은 출발점으로 간주하는 일련의 규칙 및 구성 옵션이 제공됩니다. 인증이 변경되면 앱의 요구 사항에 맞게 인증을 사용자 지정할 수 있는 IdentityServer의 모든 기능을 계속 사용할 수 있습니다.

### <a name="addidentityserverjwt"></a>애드아이덴티티서버

도우미 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 메서드는 기본 인증 처리기로 앱에 대 한 정책 체계를 구성 합니다. 이 정책은 ID가 ID URL 공간의 `/Identity`모든 하위 경로로 라우팅된 모든 요청을 처리할 수 있도록 구성됩니다. 는 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 다른 모든 요청을 처리합니다. 또한 이 메서드는 다음과 같은 작업을 수행합니다.

* `{APPLICATION NAME}API` 의 기본 범위로 IDServer에 API `{APPLICATION NAME}API`리소스를 등록합니다.
* 앱에 대해 IdentityServer에서 발급한 토큰의 유효성을 검사하도록 JWT 베어러 토큰 미들웨어를 구성합니다.

### <a name="weatherforecastcontroller"></a>일기 예보컨트롤러

`WeatherForecastController` *(컨트롤러/WeatherForecastController.cs)에서* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성이 클래스에 적용됩니다. 이 특성은 리소스에 액세스하려면 기본 정책에 따라 사용자가 권한이 있어야 함을 나타냅니다. 기본 권한 부여 정책은 앞에서 언급한 정책 체계에 의해 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 설정된 기본 인증 체계를 사용하도록 구성됩니다. 도우미 메서드는 앱에 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 대한 요청에 대한 기본 처리기로 구성됩니다.

### <a name="applicationdbcontext"></a>응용 프로그램Db컨텍스트

`ApplicationDbContext` (Data/ApplicationDbContext.cs)에서 IdServer에 대한 스키마를 포함하도록 확장되는*Data/ApplicationDbContext.cs* <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 경우를 제외하고는 ID에서 동일이 사용됩니다. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>에서 파생됩니다.

데이터베이스 스키마를 완전히 제어하려면 사용 가능한 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 클래스 중 하나에서 상속하고 메서드를 호출하여 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` Identity `OnModelCreating` 스키마를 포함하도록 컨텍스트를 구성합니다.

### <a name="oidcconfigurationcontroller"></a>Oidc구성컨트롤러

`OidcConfigurationController` *(컨트롤러/OidcConfigurationController.cs)에서*클라이언트 끝점은 OIDC 매개 변수를 제공 하도록 프로비전 됩니다.

### <a name="app-settings-files"></a>앱 설정 파일

프로젝트 루트의 앱 설정 파일(appsettings.json)에서 `IdentityServer` 이 섹션에서는 구성된 클라이언트 목록을 설명합니다.*appsettings.json* 다음 예제에서는 단일 클라이언트가 있습니다. 클라이언트 이름은 앱 이름에 해당하며 OAuth `ClientId` 매개 변수에 규칙에 따라 매핑됩니다. 프로필은 구성 중인 앱 유형을 나타냅니다. 이 프로필은 내부적으로 서버의 구성 프로세스를 단순화하는 규칙을 구동하는 데 사용됩니다. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

개발 환경 앱 설정*파일(appsettings)에서. Development.json)* 프로젝트 루트에서 `IdentityServer` 이 섹션에서는 토큰에 서명하는 데 사용되는 키에 대해 설명합니다. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>클라이언트 앱 구성

### <a name="authentication-package"></a>인증 패키지

개별 사용자 계정()을`Individual`사용하도록 앱을 만들면 앱은 앱의 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 프로젝트 파일에서 패키지에 대한 패키지 참조를 자동으로 받습니다. 이 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 토큰을 얻는 데 도움이 되는 기본 요소 집합을 제공합니다.

앱에 인증을 추가하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가합니다.

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

앞의 패키지 참조에서 문서에 표시된 `{VERSION}` `Microsoft.AspNetCore.Blazor.Templates` 패키지 버전으로 <xref:blazor/get-started> 바꿉습니다.

### <a name="api-authorization-support"></a>API 권한 부여 지원

사용자 인증에 대한 지원은 패키지 내에 제공된 확장 방법에 의해 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 서비스 컨테이너에 연결됩니다. 이 메서드는 앱이 기존 권한 부여 시스템과 상호 작용하는 데 필요한 모든 서비스를 설정합니다.

```csharp
builder.Services.AddApiAuthorization();
```

기본적으로 `_configuration/{client-id}`에서 규칙에 따라 앱에 대한 구성을 로드합니다. 규칙에 따라 클라이언트 ID는 앱의 어셈블리 이름으로 설정됩니다. 이 URL은 옵션과 함께 오버로드를 호출하여 별도의 끝점을 가리키도록 변경할 수 있습니다.

### <a name="imports-file"></a>파일 가져오기

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>앱 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>리디렉션토로그인 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>로그인표시 구성 요소

구성*요소(공유/LoginDisplay.razor)는* `MainLayout` 구성 요소(공유/MainLayout.razor)에서 렌더링되고 다음 동작을 관리합니다.*Shared/MainLayout.razor* `LoginDisplay`

* 인증된 사용자의 경우:
  * 현재 사용자 이름을 표시합니다.
  * ASP.NET 코어 ID의 사용자 프로필 페이지에 대한 링크를 제공합니다.
  * 앱에서 로그아웃할 수 있는 버튼을 제공합니다.
* 익명 사용자의 경우:
  * 등록 옵션을 제공합니다.
  * 로그인 할 수있는 옵션을 제공합니다.

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

### <a name="fetchdata-component"></a>가져오기데이터 구성 요소

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>앱 실행

서버 프로젝트에서 앱을 실행합니다. Visual Studio를 사용하는 경우 **솔루션 탐색기에서** 서버 프로젝트를 선택하고 도구 모음에서 **실행** 단추를 선택하거나 **디버그** 메뉴에서 앱을 시작합니다.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 리소스

* [추가 액세스 토큰 요청](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
