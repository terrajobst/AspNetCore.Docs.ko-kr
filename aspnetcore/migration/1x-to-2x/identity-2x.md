---
title: ASP.NET Core 2.0으로 인증 및 Id 마이그레이션
author: scottaddie
description: 이 문서에서는 ASP.NET Core 2.0으로 ASP.NET Core 1.x 인증 및 Id 마이그레이션에 대 한 가장 일반적인 단계를 간략하게 설명 합니다.
ms.author: scaddie
ms.date: 06/21/2019
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: c83356e12fa5ae581b369265b9d857b08445ed51
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313739"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>ASP.NET Core 2.0으로 인증 및 Id 마이그레이션

하 여 [Scott Addie](https://github.com/scottaddie) 고 [Hao 둘러싼](https://github.com/HaoK)

ASP.NET Core 2.0에는 새 모델을 인증 하 고 [Identity](xref:security/authentication/identity) 서비스를 사용 하 여 구성을 간소화 하는 합니다. 아래에 설명 된 대로 새 모델을 사용 하도록 인증 또는 Id를 사용 하는 ASP.NET Core 1.x 응용 프로그램을 업데이트할 수 있습니다.

## <a name="update-namespaces"></a>네임 스페이스를 업데이트 합니다.

1\.x, 클래스 등 `IdentityRole` 하 고 `IdentityUser` 에서 찾을 수 없습니다는 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 네임 스페이스입니다.

2\.0에 <xref:Microsoft.AspNetCore.Identity> 네임 스페이스는 이러한 클래스의 새 홈 되었습니다. 영향을 받는 클래스에 포함 되어 기본 Identity 코드로 `ApplicationUser` 고 `Startup`입니다. 조정에 `using` 영향을 받는 참조를 확인 하는 문입니다.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>인증 미들웨어 및 서비스

1\.x 프로젝트에서 인증 미들웨어를 통해 구성 됩니다. 지원 하려는 각 인증 체계에 대 한 미들웨어 메서드를 호출 합니다.

Id를 사용 하 여 Facebook 인증을 구성 하는 1.x 다음과 *Startup.cs*:

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

2\.0 프로젝트에서 인증 서비스를 통해 구성 됩니다. 각 인증 체계에 등록 합니다 `ConfigureServices` 메서드의 *Startup.cs*합니다. 합니다 `UseIdentity` 메서드를 사용 하 여 바뀝니다 `UseAuthentication`합니다.

다음 예제에서는 2.0에서 Id를 사용 하 여 Facebook 인증 구성 *Startup.cs*:

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

`UseAuthentication` 메서드 자동 인증 및 원격 인증 요청의 처리를 담당 하는 단일 인증 미들웨어 구성 요소를 추가 합니다. 단일의 공통 미들웨어 구성 요소를 사용 하 여 각 미들웨어 구성 요소를 모두 대체합니다.

다음은 각 주요 인증 체계에 대 한 마이그레이션 지침은 2.0입니다.

### <a name="cookie-based-authentication"></a>쿠키 기반 인증

아래 두 옵션 중 하나를 선택 하 고에서 필요한 사항을 변경한 *Startup.cs*:

1. Identity를 사용 하 여 쿠키를 사용 합니다.
    - 바꿉니다 `UseIdentity` 사용 하 여 `UseAuthentication` 에 `Configure` 메서드:

        ```csharp
        app.UseAuthentication();
        ```

    - 호출 된 `AddIdentity` 의 메서드는 `ConfigureServices` 쿠키 인증 서비스를 추가 하는 방법.
    - 필요에 따라 호출을 `ConfigureApplicationCookie` 또는 `ConfigureExternalCookie` 에서 메서드는 `ConfigureServices` Id 쿠키 설정을 조정 하는 방법입니다.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Id 없이 쿠키 사용
    - 대체는 `UseCookieAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:

        ```csharp
        app.UseAuthentication();
        ```

    - 호출을 `AddAuthentication` 하 고 `AddCookie` 의 메서드는 `ConfigureServices` 메서드:

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

다음과 같이 변경할 *Startup.cs*:
- 대체는 `UseJwtBearerAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 호출 된 `AddJwtBearer` 의 메서드는 `ConfigureServices` 메서드:

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    이 코드 조각은 기본 체계를 전달 하 여 설정 해야 하므로 Id를 사용 하지 않습니다 `JwtBearerDefaults.AuthenticationScheme` 에 `AddAuthentication` 메서드.

### <a name="openid-connect-oidc-authentication"></a>OIDC (OpenID Connect) 인증

다음과 같이 변경할 *Startup.cs*:

- 대체는 `UseOpenIdConnectAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 호출 된 `AddOpenIdConnect` 의 메서드는 `ConfigureServices` 메서드:

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

- 대체는 `PostLogoutRedirectUri` 의 속성을 `OpenIdConnectOptions` 작업과 `SignedOutRedirectUri`:

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Facebook 인증

다음과 같이 변경할 *Startup.cs*:
- 대체는 `UseFacebookAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 호출 된 `AddFacebook` 의 메서드는 `ConfigureServices` 메서드:

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Google 인증

다음과 같이 변경할 *Startup.cs*:
- 대체는 `UseGoogleAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 호출 된 `AddGoogle` 의 메서드는 `ConfigureServices` 메서드:

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Microsoft 계정 인증

다음과 같이 변경할 *Startup.cs*:
- 대체는 `UseMicrosoftAccountAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 호출 된 `AddMicrosoftAccount` 의 메서드는 `ConfigureServices` 메서드:

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Twitter 인증

다음과 같이 변경할 *Startup.cs*:
- 대체는 `UseTwitterAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 호출 된 `AddTwitter` 의 메서드는 `ConfigureServices` 메서드:

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>기본 인증 체계를 설정합니다.

1\.x의 경우에 `AutomaticAuthenticate` 및 `AutomaticChallenge` 의 속성을 [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) 기본 클래스에서 단일 인증 체계를 설정할 데 사용 된 합니다. 이 적용할 좋은 방법이 없었습니다.

2\.0에서는 이러한 두 속성이 제거 되었습니다. 개별 속성으로 `AuthenticationOptions` 인스턴스. 구성할 수 있습니다 합니다 `AddAuthentication` 메서드 호출을 `ConfigureServices` 메서드의 *Startup.cs*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

기본 스키마로 이전 코드 조각에서 `CookieAuthenticationDefaults.AuthenticationScheme` ("쿠키").

또는의 오버 로드 된 버전을 사용 합니다 `AddAuthentication` 둘 이상의 속성을 설정 하는 방법입니다. 기본 스키마로 다음 오버 로드 된 메서드 예제의 `CookieAuthenticationDefaults.AuthenticationScheme`합니다. 인증 체계 또는 개별 내에서 지정할 수 있습니다 `[Authorize]` 특성 또는 권한 부여 정책.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

다음 조건 중 하나가 참인 경우 2.0 기본 체계를 정의 합니다.
- 사용자가 자동으로 로그인
- 사용 된 `[Authorize]` 구성표를 지정 하지 않고 특성 또는 권한 부여 정책

이 규칙의 예외는 `AddIdentity` 메서드. 이 메서드를 기본 인증 및 응용 프로그램 쿠키에 스키마를 요구 하는 집합에 대 한 쿠키를 추가 `IdentityConstants.ApplicationScheme`합니다. 기본 로그인 구성표 외부 쿠키에 설정 또한 `IdentityConstants.ExternalScheme`합니다.

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>HttpContext 인증 확장 프로그램 사용

`IAuthenticationManager` 인터페이스는 1.x 인증 시스템의 주 진입점입니다. 새 집합을 사용 하 여 바뀌었습니다 `HttpContext` 의 확장 메서드는 `Microsoft.AspNetCore.Authentication` 네임 스페이스입니다.

예를 들어 1.x 프로젝트 참조는 `Authentication` 속성:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

2\.0 프로젝트에서 가져오는 합니다 `Microsoft.AspNetCore.Authentication` 네임 스페이스 및 삭제는 `Authentication` 속성 참조:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Windows 인증 (HTTP.sys / IISIntegration)

Windows 인증의 두 가지 변형이 있습니다.

* 호스트는 인증 된 사용자만 허용합니다. 이 변형 2.0 변경 내용의 영향을 받지 않습니다.
* 호스트 허용 모두 익명 사용자를 인증 합니다. 이 변형 2.0 변경의 영향. 예를 들어 앱에서 익명 사용자를 허용 해야 합니다 [IIS](xref:host-and-deploy/iis/index) 또는 [HTTP.sys](xref:fundamentals/servers/httpsys) 계층 하지만 컨트롤러 수준에서 사용자 권한을 부여 합니다. 이 시나리오에서는 기본 스키마에 설정 된 `Startup.ConfigureServices` 메서드.

  에 대 한 [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), 기본 스키마로 `IISDefaults.AuthenticationScheme`:

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  에 대 한 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), 기본 스키마로 `HttpSysDefaults.AuthenticationScheme`:

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  기본 스키마를 설정 하지 못했습니다 다음 예외로 인해 작업에서 권한 부여 (챌린지) 요청을 방지 합니다.

  > `System.InvalidOperationException`: 없습니다 authenticationScheme 지정 하 고 찾을 수 없는 DefaultChallengeScheme 했습니다.

자세한 내용은 <xref:security/authentication/windowsauth>을 참조하세요.

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>IdentityCookieOptions 인스턴스

2\.0 변경의 부작용으로 나타납니다 명명 된 쿠키 옵션 인스턴스 대신 옵션을 사용 하도록 전환 됩니다. Identity 쿠키 구성표 이름을 사용자 지정 하는 기능 제거 됩니다.

1\.x 프로젝트를 사용 하 여 예를 들어 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection) 전달 하는 `IdentityCookieOptions` 에 매개 변수 *AccountController.cs* 하 고 *ManageController.cs*. 외부 쿠키 인증 체계는 제공 된 인스턴스에서 액세스 합니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

앞서 언급 한 생성자 주입 2.0 프로젝트에서 불필요 하 게 됩니다 및 `_externalCookieScheme` 필드를 삭제할 수 있습니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

사용 하는 1.x 프로젝트는 `_externalCookieScheme` 다음과 같이 필드:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

2\.0 프로젝트에서 앞의 코드를 다음으로 바꿉니다. `IdentityConstants.ExternalScheme` 상수를 직접 사용할 수 있습니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

새로 추가 된 해결 `SignOutAsync` 다음 네임 스페이스를 가져와서 호출 합니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>POCO IdentityUser 탐색 속성 추가

자료의 Entity Framework (EF) Core 탐색 속성 `IdentityUser` POCO (Plain Old CLR Object) 제거 되었습니다. 이러한 속성을 사용 하는 1.x 프로젝트 경우 수동으로 추가 2.0 프로젝트:

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

중복 된 외래 키를 EF Core 마이그레이션을 실행 하는 경우를 방지 하려면 다음을 추가 하 `IdentityDbContext` 클래스의 `OnModelCreating` 메서드 (후는 `base.OnModelCreating();` 호출):

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

## <a name="replace-getexternalauthenticationschemes"></a>GetExternalAuthenticationSchemes 대체

동기 메서드 `GetExternalAuthenticationSchemes` 비동기 버전을 위해 제거 되었습니다. 1.x 프로젝트 같은 코드를 가정해 *Controllers/ManageController.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

이 메서드가 나타납니다 *Views/Account/Login.cshtml* 너무:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

2\.0 프로젝트에서 사용 된 <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> 메서드. 변경을 *ManageController.cs* 다음 코드와 유사 합니다.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

*Login.cshtml*의 `AuthenticationScheme` 액세스 되는 속성을 `foreach` 루프 변경 `Name`:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>ManageLoginsViewModel 속성 변경

A `ManageLoginsViewModel` 개체가 사용 되는 `ManageLogins` 의 동작 *ManageController.cs*합니다. 1\.x 프로젝트에서 개체의 `OtherLogins` 속성이 반환 형식이 `IList<AuthenticationDescription>`합니다. 이 반환 형식은 가져오는 `Microsoft.AspNetCore.Http.Authentication`:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

2\.0 프로젝트에서 반환 형식을 변경 `IList<AuthenticationScheme>`합니다. 대체이 새 반환 형식이 필요 합니다 `Microsoft.AspNetCore.Http.Authentication` 사용 하 여 가져오기는 `Microsoft.AspNetCore.Authentication` 가져오기.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>추가 자료

자세한 내용은 참조는 [인증 2.0에 대 한 토론](https://github.com/aspnet/Security/issues/1338) GitHub에서 문제입니다.
