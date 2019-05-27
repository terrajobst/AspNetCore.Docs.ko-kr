---
title: ASP.NET Core 2.0으로 인증 및 Id 마이그레이션
author: scottaddie
description: 이 문서에서는 ASP.NET Core 2.0으로 ASP.NET Core 1.x 인증 및 Id 마이그레이션에 대 한 가장 일반적인 단계를 간략하게 설명 합니다.
ms.author: scaddie
ms.date: 12/18/2018
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: 086deac51af186012315d5b6a1236c92c8980037
ms.sourcegitcommit: 5d384db2fa9373a93b5d15e985fb34430e49ad7a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66039236"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="2e2d9-103">ASP.NET Core 2.0으로 인증 및 Id 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="2e2d9-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="2e2d9-104">하 여 [Scott Addie](https://github.com/scottaddie) 고 [Hao 둘러싼](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="2e2d9-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="2e2d9-105">ASP.NET Core 2.0에는 새 모델을 인증 하 고 [Identity](xref:security/authentication/identity) 서비스를 사용 하 여 구성을 간소화 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) which simplifies configuration by using services.</span></span> <span data-ttu-id="2e2d9-106">아래에 설명 된 대로 새 모델을 사용 하도록 인증 또는 Id를 사용 하는 ASP.NET Core 1.x 응용 프로그램을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="2e2d9-107">인증 미들웨어 및 서비스</span><span class="sxs-lookup"><span data-stu-id="2e2d9-107">Authentication Middleware and services</span></span>

<span data-ttu-id="2e2d9-108">1.x 프로젝트에서 인증 미들웨어를 통해 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-108">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="2e2d9-109">지원 하려는 각 인증 체계에 대 한 미들웨어 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-109">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="2e2d9-110">Id를 사용 하 여 Facebook 인증을 구성 하는 1.x 다음과 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-110">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="2e2d9-111">2.0 프로젝트에서 인증 서비스를 통해 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-111">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="2e2d9-112">각 인증 체계에 등록 합니다 `ConfigureServices` 메서드의 *Startup.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-112">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="2e2d9-113">합니다 `UseIdentity` 메서드를 사용 하 여 바뀝니다 `UseAuthentication`합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-113">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="2e2d9-114">다음 예제에서는 2.0에서 Id를 사용 하 여 Facebook 인증 구성 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-114">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="2e2d9-115">`UseAuthentication` 메서드 자동 인증 및 원격 인증 요청의 처리를 담당 하는 단일 인증 미들웨어 구성 요소를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-115">The `UseAuthentication` method adds a single authentication middleware component which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="2e2d9-116">단일의 공통 미들웨어 구성 요소를 사용 하 여 각 미들웨어 구성 요소를 모두 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-116">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="2e2d9-117">다음은 각 주요 인증 체계에 대 한 마이그레이션 지침은 2.0입니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-117">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="2e2d9-118">쿠키 기반 인증</span><span class="sxs-lookup"><span data-stu-id="2e2d9-118">Cookie-based authentication</span></span>

<span data-ttu-id="2e2d9-119">아래 두 옵션 중 하나를 선택 하 고에서 필요한 사항을 변경한 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-119">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="2e2d9-120">Identity를 사용 하 여 쿠키를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-120">Use cookies with Identity</span></span>
    - <span data-ttu-id="2e2d9-121">바꿉니다 `UseIdentity` 사용 하 여 `UseAuthentication` 에 `Configure` 메서드:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-121">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="2e2d9-122">호출 된 `AddIdentity` 의 메서드는 `ConfigureServices` 쿠키 인증 서비스를 추가 하는 방법.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-122">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="2e2d9-123">필요에 따라 호출을 `ConfigureApplicationCookie` 또는 `ConfigureExternalCookie` 에서 메서드는 `ConfigureServices` Id 쿠키 설정을 조정 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-123">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="2e2d9-124">Id 없이 쿠키 사용</span><span class="sxs-lookup"><span data-stu-id="2e2d9-124">Use cookies without Identity</span></span>
    - <span data-ttu-id="2e2d9-125">대체는 `UseCookieAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-125">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="2e2d9-126">호출을 `AddAuthentication` 하 고 `AddCookie` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-126">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

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

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="2e2d9-127">JWT 전달자 인증</span><span class="sxs-lookup"><span data-stu-id="2e2d9-127">JWT Bearer Authentication</span></span>

<span data-ttu-id="2e2d9-128">다음과 같이 변경할 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-128">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="2e2d9-129">대체는 `UseJwtBearerAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-129">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="2e2d9-130">호출 된 `AddJwtBearer` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-130">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="2e2d9-131">이 코드 조각은 기본 체계를 전달 하 여 설정 해야 하므로 Id를 사용 하지 않습니다 `JwtBearerDefaults.AuthenticationScheme` 에 `AddAuthentication` 메서드.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-131">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="2e2d9-132">OIDC (OpenID Connect) 인증</span><span class="sxs-lookup"><span data-stu-id="2e2d9-132">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="2e2d9-133">다음과 같이 변경할 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-133">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="2e2d9-134">대체는 `UseOpenIdConnectAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-134">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="2e2d9-135">호출 된 `AddOpenIdConnect` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-135">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

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

- <span data-ttu-id="2e2d9-136">대체는 `PostLogoutRedirectUri` 의 속성을 `OpenIdConnectOptions` 작업과 `SignedOutRedirectUri`:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-136">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="2e2d9-137">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="2e2d9-137">Facebook authentication</span></span>

<span data-ttu-id="2e2d9-138">다음과 같이 변경할 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-138">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="2e2d9-139">대체는 `UseFacebookAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-139">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="2e2d9-140">호출 된 `AddFacebook` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-140">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="2e2d9-141">Google 인증</span><span class="sxs-lookup"><span data-stu-id="2e2d9-141">Google authentication</span></span>

<span data-ttu-id="2e2d9-142">다음과 같이 변경할 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-142">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="2e2d9-143">대체는 `UseGoogleAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-143">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="2e2d9-144">호출 된 `AddGoogle` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-144">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="2e2d9-145">Microsoft 계정 인증</span><span class="sxs-lookup"><span data-stu-id="2e2d9-145">Microsoft Account authentication</span></span>

<span data-ttu-id="2e2d9-146">다음과 같이 변경할 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-146">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="2e2d9-147">대체는 `UseMicrosoftAccountAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-147">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="2e2d9-148">호출 된 `AddMicrosoftAccount` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-148">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="2e2d9-149">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="2e2d9-149">Twitter authentication</span></span>

<span data-ttu-id="2e2d9-150">다음과 같이 변경할 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-150">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="2e2d9-151">대체는 `UseTwitterAuthentication` 메서드 호출을 `Configure` 메서드를 `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-151">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="2e2d9-152">호출 된 `AddTwitter` 의 메서드는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-152">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="2e2d9-153">기본 인증 체계를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-153">Setting default authentication schemes</span></span>

<span data-ttu-id="2e2d9-154">1.x의 경우에 `AutomaticAuthenticate` 및 `AutomaticChallenge` 의 속성을 [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) 기본 클래스에서 단일 인증 체계를 설정할 데 사용 된 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-154">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="2e2d9-155">이 적용할 좋은 방법이 없었습니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-155">There was no good way to enforce this.</span></span>

<span data-ttu-id="2e2d9-156">2.0에서는 이러한 두 속성이 제거 되었습니다. 개별 속성으로 `AuthenticationOptions` 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-156">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="2e2d9-157">구성할 수 있습니다 합니다 `AddAuthentication` 메서드 호출을 `ConfigureServices` 메서드의 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-157">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="2e2d9-158">기본 스키마로 이전 코드 조각에서 `CookieAuthenticationDefaults.AuthenticationScheme` ("쿠키").</span><span class="sxs-lookup"><span data-stu-id="2e2d9-158">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="2e2d9-159">또는의 오버 로드 된 버전을 사용 합니다 `AddAuthentication` 둘 이상의 속성을 설정 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-159">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="2e2d9-160">기본 스키마로 다음 오버 로드 된 메서드 예제의 `CookieAuthenticationDefaults.AuthenticationScheme`합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-160">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="2e2d9-161">인증 체계 또는 개별 내에서 지정할 수 있습니다 `[Authorize]` 특성 또는 권한 부여 정책.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-161">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="2e2d9-162">다음 조건 중 하나가 참인 경우 2.0 기본 체계를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-162">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="2e2d9-163">사용자가 자동으로 로그인</span><span class="sxs-lookup"><span data-stu-id="2e2d9-163">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="2e2d9-164">사용 된 `[Authorize]` 구성표를 지정 하지 않고 특성 또는 권한 부여 정책</span><span class="sxs-lookup"><span data-stu-id="2e2d9-164">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="2e2d9-165">이 규칙의 예외는 `AddIdentity` 메서드.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-165">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="2e2d9-166">이 메서드를 기본 인증 및 응용 프로그램 쿠키에 스키마를 요구 하는 집합에 대 한 쿠키를 추가 `IdentityConstants.ApplicationScheme`합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-166">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="2e2d9-167">기본 로그인 구성표 외부 쿠키에 설정 또한 `IdentityConstants.ExternalScheme`합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-167">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="2e2d9-168">HttpContext 인증 확장 프로그램 사용</span><span class="sxs-lookup"><span data-stu-id="2e2d9-168">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="2e2d9-169">`IAuthenticationManager` 인터페이스는 1.x 인증 시스템의 주 진입점입니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-169">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="2e2d9-170">새 집합을 사용 하 여 바뀌었습니다 `HttpContext` 의 확장 메서드는 `Microsoft.AspNetCore.Authentication` 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-170">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="2e2d9-171">예를 들어 1.x 프로젝트 참조는 `Authentication` 속성:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-171">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="2e2d9-172">2.0 프로젝트에서 가져오는 합니다 `Microsoft.AspNetCore.Authentication` 네임 스페이스 및 삭제는 `Authentication` 속성 참조:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-172">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="2e2d9-173">Windows 인증 (HTTP.sys / IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="2e2d9-173">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="2e2d9-174">Windows 인증의 두 가지 변형이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-174">There are two variations of Windows authentication:</span></span>
1. <span data-ttu-id="2e2d9-175">호스트에는 인증 된 사용자만 허용</span><span class="sxs-lookup"><span data-stu-id="2e2d9-175">The host only allows authenticated users</span></span>
2. <span data-ttu-id="2e2d9-176">호스트 허용 모두 익명 사용자를 인증 하 고</span><span class="sxs-lookup"><span data-stu-id="2e2d9-176">The host allows both anonymous and authenticated users</span></span>

<span data-ttu-id="2e2d9-177">위에서 설명한 첫 번째 변형 2.0 변경 내용의 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-177">The first variation described above is unaffected by the 2.0 changes.</span></span>

<span data-ttu-id="2e2d9-178">위에서 설명한 두 번째 변형 2.0 변경의 영향.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-178">The second variation described above is affected by the 2.0 changes.</span></span> <span data-ttu-id="2e2d9-179">예를 들어, 있습니다 수 허용 해서는 익명 사용자가 IIS에서 앱으로 또는 [HTTP.sys](xref:fundamentals/servers/httpsys) 컨트롤러 수준에서 권한 부여 하지만 사용자 계층입니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-179">As an example, you may be allowing anonymous users into your app at the IIS or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorizing users at the Controller level.</span></span> <span data-ttu-id="2e2d9-180">이 시나리오에서는 기본 스키마로 설정 합니다 `IISDefaults.AuthenticationScheme` 에 `Startup.ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-180">In this scenario, set the default scheme to `IISDefaults.AuthenticationScheme` in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

<span data-ttu-id="2e2d9-181">기본 스키마를 설정 하지 못했습니다에는 그에 따라 권한 부여 요청을에 작동에서 문제를 해결할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-181">Failure to set the default scheme accordingly prevents the authorize request to challenge from working.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="2e2d9-182">IdentityCookieOptions 인스턴스</span><span class="sxs-lookup"><span data-stu-id="2e2d9-182">IdentityCookieOptions instances</span></span>

<span data-ttu-id="2e2d9-183">2.0 변경의 부작용으로 나타납니다 명명 된 쿠키 옵션 인스턴스 대신 옵션을 사용 하도록 전환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-183">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="2e2d9-184">Identity 쿠키 구성표 이름을 사용자 지정 하는 기능 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-184">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="2e2d9-185">1.x 프로젝트를 사용 하 여 예를 들어 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection) 전달 하는 `IdentityCookieOptions` 에 매개 변수 *AccountController.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-185">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs*.</span></span> <span data-ttu-id="2e2d9-186">외부 쿠키 인증 체계는 제공 된 인스턴스에서 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-186">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="2e2d9-187">앞서 언급 한 생성자 주입 2.0 프로젝트에서 불필요 하 게 됩니다 및 `_externalCookieScheme` 필드를 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-187">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="2e2d9-188">`IdentityConstants.ExternalScheme` 상수를 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-188">The `IdentityConstants.ExternalScheme` constant can be used directly:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="2e2d9-189">POCO IdentityUser 탐색 속성 추가</span><span class="sxs-lookup"><span data-stu-id="2e2d9-189">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="2e2d9-190">자료의 Entity Framework (EF) Core 탐색 속성 `IdentityUser` POCO (Plain Old CLR Object) 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-190">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="2e2d9-191">이러한 속성을 사용 하는 1.x 프로젝트 경우 수동으로 추가 2.0 프로젝트:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-191">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

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

<span data-ttu-id="2e2d9-192">중복 된 외래 키를 EF Core 마이그레이션을 실행 하는 경우를 방지 하려면 다음을 추가 하 `IdentityDbContext` 클래스의 `OnModelCreating` 메서드 (후는 `base.OnModelCreating();` 호출):</span><span class="sxs-lookup"><span data-stu-id="2e2d9-192">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

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

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="2e2d9-193">GetExternalAuthenticationSchemes 대체</span><span class="sxs-lookup"><span data-stu-id="2e2d9-193">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="2e2d9-194">동기 메서드 `GetExternalAuthenticationSchemes` 비동기 버전을 위해 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-194">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="2e2d9-195">1.x 프로젝트 같은 코드를 가정해 *ManageController.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-195">1.x projects have the following code in *ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="2e2d9-196">이 메서드가 나타납니다 *Login.cshtml* 너무:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-196">This method appears in *Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?range=62,75-84)]

<span data-ttu-id="2e2d9-197">2.0 프로젝트에서 사용 된 `GetExternalAuthenticationSchemesAsync` 메서드:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-197">In 2.0 projects, use the `GetExternalAuthenticationSchemesAsync` method:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="2e2d9-198">*Login.cshtml*의 `AuthenticationScheme` 액세스 되는 속성을 `foreach` 루프 변경 `Name`:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-198">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?range=62,75-84)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="2e2d9-199">ManageLoginsViewModel 속성 변경</span><span class="sxs-lookup"><span data-stu-id="2e2d9-199">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="2e2d9-200">A `ManageLoginsViewModel` 개체가 사용 되는 `ManageLogins` 의 동작 *ManageController.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-200">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="2e2d9-201">1.x 프로젝트에서 개체의 `OtherLogins` 속성이 반환 형식이 `IList<AuthenticationDescription>`합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-201">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="2e2d9-202">이 반환 형식은 가져오는 `Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="2e2d9-202">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="2e2d9-203">2.0 프로젝트에서 반환 형식을 변경 `IList<AuthenticationScheme>`합니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-203">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="2e2d9-204">대체이 새 반환 형식이 필요 합니다 `Microsoft.AspNetCore.Http.Authentication` 사용 하 여 가져오기는 `Microsoft.AspNetCore.Authentication` 가져오기.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-204">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="2e2d9-205">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2e2d9-205">Additional resources</span></span>

<span data-ttu-id="2e2d9-206">추가 세부 정보 및 토론에 대 한 참조를 [Auth 2.0에 대 한 토론](https://github.com/aspnet/Security/issues/1338) GitHub에서 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="2e2d9-206">For additional details and discussion, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
