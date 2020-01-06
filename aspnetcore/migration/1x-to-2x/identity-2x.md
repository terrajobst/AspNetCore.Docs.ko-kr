---
title: ASP.NET Core 2.0으로 인증 및 Id 마이그레이션
author: scottaddie
description: 이 문서에서는 ASP.NET Core 1.x 인증 및 Id를 ASP.NET Core 2.0으로 마이그레이션하기 위한 가장 일반적인 단계를 간략하게 설명 합니다.
ms.author: scaddie
ms.date: 06/21/2019
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: f3817fa1808c331f7e167618e3bb00d68ad08571
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355174"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>ASP.NET Core 2.0으로 인증 및 Id 마이그레이션

[Scott Addie](https://github.com/scottaddie) 및 [jia-hao Kung](https://github.com/HaoK)

ASP.NET Core 2.0에는 서비스를 사용 하 여 구성을 간소화 하는 인증 및 [id](xref:security/authentication/identity) 에 대 한 새 모델이 있습니다. 인증 또는 Id를 사용 하는 ASP.NET Core 1. x 응용 프로그램은 아래에 설명 된 대로 새 모델을 사용 하도록 업데이트할 수 있습니다.

## <a name="update-namespaces"></a>네임 스페이스 업데이트

1\.x에서 `IdentityRole` 및 `IdentityUser` 같은 클래스는 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 네임 스페이스에 있습니다.

2\.0에서 <xref:Microsoft.AspNetCore.Identity> 네임 스페이스는 이러한 클래스 중 일부에 대 한 새 홈이 됩니다. 기본 Id 코드를 사용 하는 경우 영향을 받는 클래스는 `ApplicationUser` 및 `Startup`를 포함 합니다. 영향을 받는 참조를 확인 하도록 `using` 문을 조정 합니다.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>인증 미들웨어 및 서비스

1\.x 프로젝트에서 인증은 미들웨어를 통해 구성 됩니다. 지원 하려는 각 인증 체계에 대해 미들웨어 메서드가 호출 됩니다.

다음 1.x 예에서는 *Startup.cs*에서 id를 사용 하 여 Facebook 인증을 구성 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

2\.0 프로젝트에서 인증은 서비스를 통해 구성 됩니다. 각 인증 체계가 *Startup.cs*의 `ConfigureServices` 메서드에 등록 되어 있습니다. `UseIdentity` 메서드는 `UseAuthentication`로 바뀝니다.

다음 2.0 예제에서는 *Startup.cs*에서 id를 사용 하 여 Facebook 인증을 구성 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

`UseAuthentication` 메서드는 자동 인증 및 원격 인증 요청 처리를 담당 하는 단일 인증 미들웨어 구성 요소를 추가 합니다. 모든 개별 미들웨어 구성 요소를 하나의 공통 미들웨어 구성 요소로 바꿉니다.

각 주요 인증 체계에 대 한 2.0 마이그레이션 지침은 다음과 같습니다.

### <a name="cookie-based-authentication"></a>쿠키 기반 인증

아래 두 옵션 중 하나를 선택 하 고 *Startup.cs*에서 필요한 변경을 수행 합니다.

1. Id로 쿠키 사용
    - `Configure` 메서드에서 `UseIdentity`를 `UseAuthentication`으로 바꿉니다.

        ```csharp
        app.UseAuthentication();
        ```

    - `ConfigureServices` 메서드에서 `AddIdentity` 메서드를 호출 하 여 쿠키 인증 서비스를 추가 합니다.
    - 필요에 따라 `ConfigureServices` 메서드에서 `ConfigureApplicationCookie` 또는 `ConfigureExternalCookie` 메서드를 호출 하 여 Id 쿠키 설정을 조정 합니다.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Id가 없는 쿠키 사용
    - `Configure` 메서드의 `UseCookieAuthentication` 메서드 호출을 `UseAuthentication`으로 바꿉니다.

        ```csharp
        app.UseAuthentication();
        ```

    - `ConfigureServices` 메서드에서 `AddAuthentication` 및 `AddCookie` 메서드를 호출 합니다.

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>JWT 전달자 인증

*Startup.cs*에서 다음과 같이 변경 합니다.
- `Configure` 메서드의 `UseJwtBearerAuthentication` 메서드 호출을 `UseAuthentication`으로 바꿉니다.

    ```csharp
    app.UseAuthentication();
    ```

- `ConfigureServices` 메서드에서 `AddJwtBearer` 메서드를 호출 합니다.

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    이 코드 조각에서는 Id를 사용 하지 않으므로 `AddAuthentication` 메서드에 `JwtBearerDefaults.AuthenticationScheme`를 전달 하 여 기본 체계를 설정 해야 합니다.

### <a name="openid-connect-oidc-authentication"></a>OIDC (Openid connect Connect) 인증

*Startup.cs*에서 다음과 같이 변경 합니다.

- `Configure` 메서드의 `UseOpenIdConnectAuthentication` 메서드 호출을 `UseAuthentication`으로 바꿉니다.

    ```csharp
    app.UseAuthentication();
    ```

- `ConfigureServices` 메서드에서 `AddOpenIdConnect` 메서드를 호출 합니다.

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- `OpenIdConnectOptions` 작업의 `PostLogoutRedirectUri` 속성을 `SignedOutRedirectUri`으로 바꿉니다.

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Facebook 인증

*Startup.cs*에서 다음과 같이 변경 합니다.
- `Configure` 메서드의 `UseFacebookAuthentication` 메서드 호출을 `UseAuthentication`으로 바꿉니다.

    ```csharp
    app.UseAuthentication();
    ```

- `ConfigureServices` 메서드에서 `AddFacebook` 메서드를 호출 합니다.

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Google 인증

*Startup.cs*에서 다음과 같이 변경 합니다.
- `Configure` 메서드의 `UseGoogleAuthentication` 메서드 호출을 `UseAuthentication`으로 바꿉니다.

    ```csharp
    app.UseAuthentication();
    ```

- `ConfigureServices` 메서드에서 `AddGoogle` 메서드를 호출 합니다.

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Microsoft 계정 인증

Microsoft 계정 인증에 대 한 자세한 내용은 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/14455)를 참조 하세요.

*Startup.cs*에서 다음과 같이 변경 합니다.
- `Configure` 메서드의 `UseMicrosoftAccountAuthentication` 메서드 호출을 `UseAuthentication`으로 바꿉니다.

    ```csharp
    app.UseAuthentication();
    ```

- `ConfigureServices` 메서드에서 `AddMicrosoftAccount` 메서드를 호출 합니다.

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Twitter 인증

*Startup.cs*에서 다음과 같이 변경 합니다.
- `Configure` 메서드의 `UseTwitterAuthentication` 메서드 호출을 `UseAuthentication`으로 바꿉니다.

    ```csharp
    app.UseAuthentication();
    ```

- `ConfigureServices` 메서드에서 `AddTwitter` 메서드를 호출 합니다.

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>기본 인증 체계 설정

1\.x에서 [Authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) 기본 클래스의 `AutomaticAuthenticate` 및 `AutomaticChallenge` 속성은 단일 인증 체계에 설정 하기 위한 것입니다. 이를 적용 하는 좋은 방법은 없습니다.

2\.0에서는 이러한 두 속성이 개별 `AuthenticationOptions` 인스턴스에서 속성으로 제거 되었습니다. *Startup.cs*의 `ConfigureServices` 메서드 내에서 `AddAuthentication` 메서드 호출을 통해 구성할 수 있습니다.

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

위의 코드 조각에서 기본 체계는 `CookieAuthenticationDefaults.AuthenticationScheme` ("쿠키")로 설정 됩니다.

또는 오버 로드 된 버전의 `AddAuthentication` 메서드를 사용 하 여 둘 이상의 속성을 설정 합니다. 다음의 오버 로드 된 메서드 예제에서 기본 체계는 `CookieAuthenticationDefaults.AuthenticationScheme`로 설정 됩니다. 개별 `[Authorize]` 특성 또는 권한 부여 정책 내에서 인증 체계를 지정할 수 있습니다.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

다음 조건 중 하나에 해당 하는 경우 2.0에서 기본 스키마를 정의 합니다.
- 사용자에 게 자동으로 로그인 하려고 합니다.
- 스키마를 지정 하지 않고 `[Authorize]` 특성 또는 권한 부여 정책을 사용 합니다.

이 규칙의 예외는 `AddIdentity` 메서드입니다. 이 메서드는 쿠키를 추가 하 고 기본 인증 및 챌린지 체계를 응용 프로그램 쿠키 `IdentityConstants.ApplicationScheme`로 설정 합니다. 또한 `IdentityConstants.ExternalScheme`외부 쿠키에 대 한 기본 로그인 체계를 설정 합니다.

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>HttpContext 인증 확장 프로그램 사용

`IAuthenticationManager` 인터페이스는 1.x 인증 시스템의 기본 진입점입니다. `Microsoft.AspNetCore.Authentication` 네임 스페이스의 새로운 `HttpContext` 확장 메서드 집합으로 대체 되었습니다.

예를 들어 1. x 프로젝트는 `Authentication` 속성을 참조 합니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

2\.0 프로젝트에서 `Microsoft.AspNetCore.Authentication` 네임 스페이스를 가져오고 `Authentication` 속성 참조를 삭제 합니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Windows 인증 (HTTP.SYS/IISIntegration)

Windows 인증에는 두 가지 변형이 있습니다.

* 호스트는 인증 된 사용자만 허용 합니다. 이러한 변형은 2.0 변경의 영향을 받지 않습니다.
* 호스트는 익명 및 인증 된 사용자를 모두 허용 합니다. 이러한 변형은 2.0 변경의 영향을 받습니다. 예를 들어 응용 프로그램은 [IIS](xref:host-and-deploy/iis/index) 또는 [http.sys](xref:fundamentals/servers/httpsys) 계층에서 익명 사용자를 허용 하지만 컨트롤러 수준에서 사용자에 게 권한을 부여 해야 합니다. 이 시나리오에서는 `Startup.ConfigureServices` 메서드에서 기본 체계를 설정 합니다.

  [AspNetCore 통합](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)의 경우 기본 체계를 `IISDefaults.AuthenticationScheme`으로 설정 합니다.

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)의 경우 기본 체계를 `HttpSysDefaults.AuthenticationScheme`으로 설정 합니다.

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  기본 체계를 설정 하지 않으면 권한 부여 (챌린지) 요청이 다음 예외와 함께 작동 하지 않습니다.

  > `System.InvalidOperationException`: authenticationScheme가 지정 되지 않았으므로 DefaultChallengeScheme을 찾을 수 없습니다.

자세한 내용은 <xref:security/authentication/windowsauth>를 참조하세요.

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>IdentityCookieOptions 인스턴스

2\.0 변경의 부작용은 쿠키 옵션 인스턴스 대신 명명 된 옵션을 사용 하도록 전환 하는 것입니다. Id 쿠키 체계 이름을 사용자 지정 하는 기능이 제거 됩니다.

예를 들어, 1.x 프로젝트는 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection) 을 사용 하 여 `IdentityCookieOptions` 매개 변수를 *AccountController.cs* 및 *ManageController.cs*에 전달 합니다. 외부 쿠키 인증 체계는 제공 된 인스턴스에서 액세스할 수 있습니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

앞에서 언급 한 생성자 삽입은 2.0 프로젝트에서 필요 하지 않으며 `_externalCookieScheme` 필드를 삭제할 수 있습니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

1.x 프로젝트는 다음과 같이 `_externalCookieScheme` 필드를 사용 했습니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

2\.0 프로젝트에서 위의 코드를 다음 코드로 바꿉니다. `IdentityConstants.ExternalScheme` 상수는 직접 사용할 수 있습니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

다음 네임 스페이스를 가져와서 새로 추가 된 `SignOutAsync` 호출을 해결 합니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>IdentityUser POCO 탐색 속성 추가

기본 `IdentityUser` POCO (일반 이전 CLR 개체)의 Entity Framework (EF) 핵심 탐색 속성이 제거 되었습니다. 1\.x 프로젝트에서 이러한 속성을 사용 하는 경우 수동으로 2.0 프로젝트에 다시 추가 합니다.

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

EF Core 마이그레이션을 실행 하는 경우 중복 된 외래 키를 방지 하려면 `base.OnModelCreating();` 호출 후에 `IdentityDbContext` 클래스의 `OnModelCreating` 메서드에 다음을 추가 합니다.

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a>GetExternalAuthenticationSchemes 바꾸기

비동기 버전을 위해 동기 메서드 `GetExternalAuthenticationSchemes` 제거 되었습니다. 1.x 프로젝트에는 *controller/ManageController*에 다음 코드가 있습니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

이 메서드는 *보기/계정/로그인* 에 표시 됩니다.

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

2\.0 프로젝트에서 <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> 메서드를 사용 합니다. *ManageController.cs* 의 변경 내용은 다음 코드와 비슷합니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

*Login. cshtml*에서 `foreach` 루프에서 액세스 되는 `AuthenticationScheme` 속성은 `Name`변경 됩니다.

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>ManageLoginsViewModel 속성 변경

`ManageLoginsViewModel` 개체는 *ManageController.cs*의 `ManageLogins` 작업에 사용 됩니다. 1\.x 프로젝트에서 개체의 `OtherLogins` 속성 반환 형식은 `IList<AuthenticationDescription>`입니다. 이 반환 형식에는 `Microsoft.AspNetCore.Http.Authentication`가져오기가 필요 합니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

2\.0 프로젝트에서 반환 형식은 `IList<AuthenticationScheme>`로 변경 됩니다. 이 새 반환 형식을 사용 하려면 `Microsoft.AspNetCore.Http.Authentication` 가져오기를 `Microsoft.AspNetCore.Authentication` 가져오기로 바꾸어야 합니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>추가 자료

자세한 내용은 GitHub의 [Auth 2.0 문제에 대 한 설명을](https://github.com/aspnet/Security/issues/1338) 참조 하세요.
