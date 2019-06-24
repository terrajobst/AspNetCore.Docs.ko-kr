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
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="d8f84-103">ASP.NET Core 2.0으로 인증 및 Id 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="d8f84-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="d8f84-104">하 여 [Scott Addie](https://github.com/scottaddie) 고 [Hao 둘러싼](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="d8f84-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="d8f84-105">ASP.NET Core 2.0에는 새 모델을 인증 하 고 [Identity](xref:security/authentication/identity) 서비스를 사용 하 여 구성을 간소화 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="d8f84-106">아래에 설명 된 대로 새 모델을 사용 하도록 인증 또는 Id를 사용 하는 ASP.NET Core 1.x 응용 프로그램을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="d8f84-107">네임 스페이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-107">Update namespaces</span></span>

<span data-ttu-id="d8f84-108">1\.x, 클래스 등 `IdentityRole` 하 고 `IdentityUser` 에서 찾을 수 없습니다는 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="d8f84-109">2\.0에 <xref:Microsoft.AspNetCore.Identity> 네임 스페이스는 이러한 클래스의 새 홈 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="d8f84-110">영향을 받는 클래스에 포함 되어 기본 Identity 코드로 `ApplicationUser` 고 `Startup`입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="d8f84-111">조정에 `using` 영향을 받는 참조를 확인 하는 문입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="d8f84-112">인증 미들웨어 및 서비스</span><span class="sxs-lookup"><span data-stu-id="d8f84-112">Authentication Middleware and services</span></span>

<span data-ttu-id="d8f84-113">1\.x 프로젝트에서 인증 미들웨어를 통해 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="d8f84-114">지원 하려는 각 인증 체계에 대 한 미들웨어 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="d8f84-115">Id를 사용 하 여 Facebook 인증을 구성 하는 1.x 다음과 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8f84-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="d8f84-116">2\.0 프로젝트에서 인증 서비스를 통해 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="d8f84-117">각 인증 체계에 등록 합니다 `ConfigureServices` 메서드의 *Startup.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="d8f84-118">합니다 `UseIdentity` 메서드를 사용 하 여 바뀝니다 `UseAuthentication`합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="d8f84-119">다음 예제에서는 2.0에서 Id를 사용 하 여 Facebook 인증 구성 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8f84-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="d8f84-120">`UseAuthentication` 메서드 자동 인증 및 원격 인증 요청의 처리를 담당 하는 단일 인증 미들웨어 구성 요소를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="d8f84-121">단일의 공통 미들웨어 구성 요소를 사용 하 여 각 미들웨어 구성 요소를 모두 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="d8f84-122">다음은 각 주요 인증 체계에 대 한 마이그레이션 지침은 2.0입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="d8f84-123">쿠키 기반 인증</span><span class="sxs-lookup"><span data-stu-id="d8f84-123">Cookie-based authentication</span></span>

<span data-ttu-id="d8f84-124">아래 두 옵션 중 하나를 선택 하 고에서 필요한 사항을 변경한 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8f84-124">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="d8f84-125">Identity를 사용 하 여 쿠키를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="d8f84-126">바꿉니다 `UseIdentity` 사용 하 여 `UseAuthentication` 에 `Configure` 메서드:</span><span class="sxs-lookup"><span data-stu-id="d8f84-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="d8f84-127">호출 된 `AddIdentity` 의 메서드는 `ConfigureServices` 쿠키 인증 서비스를 추가 하는 방법.</span><span class="sxs-lookup"><span data-stu-id="d8f84-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="d8f84-128">필요에 따라 호출을 `ConfigureApplicationCookie` 또는 `ConfigureExternalCookie` 에서 메서드는 `ConfigureServices` Id 쿠키 설정을 조정 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="d8f84-129">Id 없이 쿠키 사용</span><span class="sxs-lookup"><span data-stu-id="d8f84-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="d8f84-130">대체는 `UseCookieAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="d8f84-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="d8f84-131">호출을 `AddAuthentication` 하 고 `AddCookie` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="d8f84-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

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

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="d8f84-132">JWT 전달자 인증</span><span class="sxs-lookup"><span data-stu-id="d8f84-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="d8f84-133">다음과 같이 변경할 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8f84-133">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="d8f84-134">대체는 `UseJwtBearerAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="d8f84-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="d8f84-135">호출 된 `AddJwtBearer` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="d8f84-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="d8f84-136">이 코드 조각은 기본 체계를 전달 하 여 설정 해야 하므로 Id를 사용 하지 않습니다 `JwtBearerDefaults.AuthenticationScheme` 에 `AddAuthentication` 메서드.</span><span class="sxs-lookup"><span data-stu-id="d8f84-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="d8f84-137">OIDC (OpenID Connect) 인증</span><span class="sxs-lookup"><span data-stu-id="d8f84-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="d8f84-138">다음과 같이 변경할 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8f84-138">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="d8f84-139">대체는 `UseOpenIdConnectAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="d8f84-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="d8f84-140">호출 된 `AddOpenIdConnect` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="d8f84-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

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

- <span data-ttu-id="d8f84-141">대체는 `PostLogoutRedirectUri` 의 속성을 `OpenIdConnectOptions` 작업과 `SignedOutRedirectUri`:</span><span class="sxs-lookup"><span data-stu-id="d8f84-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="d8f84-142">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="d8f84-142">Facebook authentication</span></span>

<span data-ttu-id="d8f84-143">다음과 같이 변경할 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8f84-143">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="d8f84-144">대체는 `UseFacebookAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="d8f84-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="d8f84-145">호출 된 `AddFacebook` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="d8f84-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="d8f84-146">Google 인증</span><span class="sxs-lookup"><span data-stu-id="d8f84-146">Google authentication</span></span>

<span data-ttu-id="d8f84-147">다음과 같이 변경할 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8f84-147">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="d8f84-148">대체는 `UseGoogleAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="d8f84-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="d8f84-149">호출 된 `AddGoogle` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="d8f84-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="d8f84-150">Microsoft 계정 인증</span><span class="sxs-lookup"><span data-stu-id="d8f84-150">Microsoft Account authentication</span></span>

<span data-ttu-id="d8f84-151">다음과 같이 변경할 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8f84-151">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="d8f84-152">대체는 `UseMicrosoftAccountAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="d8f84-152">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="d8f84-153">호출 된 `AddMicrosoftAccount` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="d8f84-153">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="d8f84-154">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="d8f84-154">Twitter authentication</span></span>

<span data-ttu-id="d8f84-155">다음과 같이 변경할 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8f84-155">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="d8f84-156">대체는 `UseTwitterAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="d8f84-156">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="d8f84-157">호출 된 `AddTwitter` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="d8f84-157">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="d8f84-158">기본 인증 체계를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-158">Setting default authentication schemes</span></span>

<span data-ttu-id="d8f84-159">1\.x의 경우에 `AutomaticAuthenticate` 및 `AutomaticChallenge` 의 속성을 [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) 기본 클래스에서 단일 인증 체계를 설정할 데 사용 된 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-159">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="d8f84-160">이 적용할 좋은 방법이 없었습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-160">There was no good way to enforce this.</span></span>

<span data-ttu-id="d8f84-161">2\.0에서는 이러한 두 속성이 제거 되었습니다. 개별 속성으로 `AuthenticationOptions` 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="d8f84-161">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="d8f84-162">구성할 수 있습니다 합니다 `AddAuthentication` 메서드 호출을 `ConfigureServices` 메서드의 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8f84-162">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="d8f84-163">기본 스키마로 이전 코드 조각에서 `CookieAuthenticationDefaults.AuthenticationScheme` ("쿠키").</span><span class="sxs-lookup"><span data-stu-id="d8f84-163">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="d8f84-164">또는의 오버 로드 된 버전을 사용 합니다 `AddAuthentication` 둘 이상의 속성을 설정 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-164">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="d8f84-165">기본 스키마로 다음 오버 로드 된 메서드 예제의 `CookieAuthenticationDefaults.AuthenticationScheme`합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-165">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="d8f84-166">인증 체계 또는 개별 내에서 지정할 수 있습니다 `[Authorize]` 특성 또는 권한 부여 정책.</span><span class="sxs-lookup"><span data-stu-id="d8f84-166">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="d8f84-167">다음 조건 중 하나가 참인 경우 2.0 기본 체계를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-167">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="d8f84-168">사용자가 자동으로 로그인</span><span class="sxs-lookup"><span data-stu-id="d8f84-168">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="d8f84-169">사용 된 `[Authorize]` 구성표를 지정 하지 않고 특성 또는 권한 부여 정책</span><span class="sxs-lookup"><span data-stu-id="d8f84-169">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="d8f84-170">이 규칙의 예외는 `AddIdentity` 메서드.</span><span class="sxs-lookup"><span data-stu-id="d8f84-170">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="d8f84-171">이 메서드를 기본 인증 및 응용 프로그램 쿠키에 스키마를 요구 하는 집합에 대 한 쿠키를 추가 `IdentityConstants.ApplicationScheme`합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-171">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="d8f84-172">기본 로그인 구성표 외부 쿠키에 설정 또한 `IdentityConstants.ExternalScheme`합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-172">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="d8f84-173">HttpContext 인증 확장 프로그램 사용</span><span class="sxs-lookup"><span data-stu-id="d8f84-173">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="d8f84-174">`IAuthenticationManager` 인터페이스는 1.x 인증 시스템의 주 진입점입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-174">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="d8f84-175">새 집합을 사용 하 여 바뀌었습니다 `HttpContext` 의 확장 메서드는 `Microsoft.AspNetCore.Authentication` 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-175">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="d8f84-176">예를 들어 1.x 프로젝트 참조는 `Authentication` 속성:</span><span class="sxs-lookup"><span data-stu-id="d8f84-176">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="d8f84-177">2\.0 프로젝트에서 가져오는 합니다 `Microsoft.AspNetCore.Authentication` 네임 스페이스 및 삭제는 `Authentication` 속성 참조:</span><span class="sxs-lookup"><span data-stu-id="d8f84-177">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="d8f84-178">Windows 인증 (HTTP.sys / IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="d8f84-178">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="d8f84-179">Windows 인증의 두 가지 변형이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-179">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="d8f84-180">호스트는 인증 된 사용자만 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-180">The host only allows authenticated users.</span></span> <span data-ttu-id="d8f84-181">이 변형 2.0 변경 내용의 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-181">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="d8f84-182">호스트 허용 모두 익명 사용자를 인증 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-182">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="d8f84-183">이 변형 2.0 변경의 영향.</span><span class="sxs-lookup"><span data-stu-id="d8f84-183">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="d8f84-184">예를 들어 앱에서 익명 사용자를 허용 해야 합니다 [IIS](xref:host-and-deploy/iis/index) 또는 [HTTP.sys](xref:fundamentals/servers/httpsys) 계층 하지만 컨트롤러 수준에서 사용자 권한을 부여 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-184">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="d8f84-185">이 시나리오에서는 기본 스키마에 설정 된 `Startup.ConfigureServices` 메서드.</span><span class="sxs-lookup"><span data-stu-id="d8f84-185">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="d8f84-186">에 대 한 [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), 기본 스키마로 `IISDefaults.AuthenticationScheme`:</span><span class="sxs-lookup"><span data-stu-id="d8f84-186">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="d8f84-187">에 대 한 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), 기본 스키마로 `HttpSysDefaults.AuthenticationScheme`:</span><span class="sxs-lookup"><span data-stu-id="d8f84-187">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="d8f84-188">기본 스키마를 설정 하지 못했습니다 다음 예외로 인해 작업에서 권한 부여 (챌린지) 요청을 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-188">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="d8f84-189">`System.InvalidOperationException`: 없습니다 authenticationScheme 지정 하 고 찾을 수 없는 DefaultChallengeScheme 했습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-189">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="d8f84-190">자세한 내용은 <xref:security/authentication/windowsauth>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d8f84-190">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="d8f84-191">IdentityCookieOptions 인스턴스</span><span class="sxs-lookup"><span data-stu-id="d8f84-191">IdentityCookieOptions instances</span></span>

<span data-ttu-id="d8f84-192">2\.0 변경의 부작용으로 나타납니다 명명 된 쿠키 옵션 인스턴스 대신 옵션을 사용 하도록 전환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-192">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="d8f84-193">Identity 쿠키 구성표 이름을 사용자 지정 하는 기능 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-193">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="d8f84-194">1\.x 프로젝트를 사용 하 여 예를 들어 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection) 전달 하는 `IdentityCookieOptions` 에 매개 변수 *AccountController.cs* 하 고 *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8f84-194">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs*.</span></span> <span data-ttu-id="d8f84-195">외부 쿠키 인증 체계는 제공 된 인스턴스에서 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-195">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="d8f84-196">앞서 언급 한 생성자 주입 2.0 프로젝트에서 불필요 하 게 됩니다 및 `_externalCookieScheme` 필드를 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-196">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="d8f84-197">사용 하는 1.x 프로젝트는 `_externalCookieScheme` 다음과 같이 필드:</span><span class="sxs-lookup"><span data-stu-id="d8f84-197">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="d8f84-198">2\.0 프로젝트에서 앞의 코드를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-198">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="d8f84-199">`IdentityConstants.ExternalScheme` 상수를 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-199">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="d8f84-200">새로 추가 된 해결 `SignOutAsync` 다음 네임 스페이스를 가져와서 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-200">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="d8f84-201">POCO IdentityUser 탐색 속성 추가</span><span class="sxs-lookup"><span data-stu-id="d8f84-201">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="d8f84-202">자료의 Entity Framework (EF) Core 탐색 속성 `IdentityUser` POCO (Plain Old CLR Object) 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-202">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="d8f84-203">이러한 속성을 사용 하는 1.x 프로젝트 경우 수동으로 추가 2.0 프로젝트:</span><span class="sxs-lookup"><span data-stu-id="d8f84-203">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

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

<span data-ttu-id="d8f84-204">중복 된 외래 키를 EF Core 마이그레이션을 실행 하는 경우를 방지 하려면 다음을 추가 하 `IdentityDbContext` 클래스의 `OnModelCreating` 메서드 (후는 `base.OnModelCreating();` 호출):</span><span class="sxs-lookup"><span data-stu-id="d8f84-204">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

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

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="d8f84-205">GetExternalAuthenticationSchemes 대체</span><span class="sxs-lookup"><span data-stu-id="d8f84-205">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="d8f84-206">동기 메서드 `GetExternalAuthenticationSchemes` 비동기 버전을 위해 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-206">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="d8f84-207">1.x 프로젝트 같은 코드를 가정해 *Controllers/ManageController.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8f84-207">1.x projects have the following code in *Controllers/ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="d8f84-208">이 메서드가 나타납니다 *Views/Account/Login.cshtml* 너무:</span><span class="sxs-lookup"><span data-stu-id="d8f84-208">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="d8f84-209">2\.0 프로젝트에서 사용 된 <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> 메서드.</span><span class="sxs-lookup"><span data-stu-id="d8f84-209">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="d8f84-210">변경을 *ManageController.cs* 다음 코드와 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-210">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="d8f84-211">*Login.cshtml*의 `AuthenticationScheme` 액세스 되는 속성을 `foreach` 루프 변경 `Name`:</span><span class="sxs-lookup"><span data-stu-id="d8f84-211">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="d8f84-212">ManageLoginsViewModel 속성 변경</span><span class="sxs-lookup"><span data-stu-id="d8f84-212">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="d8f84-213">A `ManageLoginsViewModel` 개체가 사용 되는 `ManageLogins` 의 동작 *ManageController.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-213">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="d8f84-214">1\.x 프로젝트에서 개체의 `OtherLogins` 속성이 반환 형식이 `IList<AuthenticationDescription>`합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-214">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="d8f84-215">이 반환 형식은 가져오는 `Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="d8f84-215">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="d8f84-216">2\.0 프로젝트에서 반환 형식을 변경 `IList<AuthenticationScheme>`합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-216">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="d8f84-217">대체이 새 반환 형식이 필요 합니다 `Microsoft.AspNetCore.Http.Authentication` 사용 하 여 가져오기는 `Microsoft.AspNetCore.Authentication` 가져오기.</span><span class="sxs-lookup"><span data-stu-id="d8f84-217">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="d8f84-218">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d8f84-218">Additional resources</span></span>

<span data-ttu-id="d8f84-219">자세한 내용은 참조는 [인증 2.0에 대 한 토론](https://github.com/aspnet/Security/issues/1338) GitHub에서 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f84-219">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
