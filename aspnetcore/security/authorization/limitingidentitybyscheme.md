---
title: ASP.NET Core에서 특정 구성표로 권한 부여
author: rick-anderson
description: 이 문서에는 여러 인증 방법을 사용 하는 경우 id는 특정 체계를 제한 하는 방법을 설명 합니다.
ms.author: riande
ms.date: 10/22/2018
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 778bb61f472ab2e76f85da5999d3c79238188f19
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897340"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="c9579-103">ASP.NET Core에서 특정 구성표로 권한 부여</span><span class="sxs-lookup"><span data-stu-id="c9579-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="c9579-104">이 단일 페이지 응용 프로그램 (Spa) 등의 일부 시나리오에서 여러 인증 방법을 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="c9579-105">예를 들어, 앱 JavaScript 요청에 대 한 JWT 전달자 인증 및 쿠키 기반 인증 로그인을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-105">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="c9579-106">경우에 따라 앱에 인증 처리기를 여러 개 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="c9579-107">예를 들어 있는 기본 id를 포함 하는 두 명의 쿠키 처리기와 multi-factor authentication (MFA)가 트리거되면 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-107">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="c9579-108">MFA는 사용자 추가 보안이 필요한 작업을 요청 하기 때문에 트리거될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c9579-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c9579-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="c9579-110">인증 체계를 인증 하는 동안 인증 서비스를 구성할 때 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="c9579-111">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="c9579-111">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="c9579-112">위의 코드에서 두 명의 인증 처리기 추가 되었습니다: 쿠키 및 전달자에 대 한 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-112">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="c9579-113">기본 스키마를 지정 하면를 `HttpContext.User` 해당 id로 설정 되는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="c9579-114">해당 동작은 필요 하지 않지만, 하는 경우 매개 변수가 없는 형태로 호출 하 여 해제 `AddAuthentication`합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c9579-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c9579-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="c9579-116">인증 체계에는 인증 미들웨어가 인증 하는 동안 구성 된 경우 이름이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-116">Authentication schemes are named when authentication middlewares are configured during authentication.</span></span> <span data-ttu-id="c9579-117">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="c9579-117">For example:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    // Code omitted for brevity

    app.UseCookieAuthentication(new CookieAuthenticationOptions()
    {
        AuthenticationScheme = "Cookie",
        LoginPath = "/Account/Unauthorized/",
        AccessDeniedPath = "/Account/Forbidden/",
        AutomaticAuthenticate = false
    });
    
    app.UseJwtBearerAuthentication(new JwtBearerOptions()
    {
        AuthenticationScheme = "Bearer",
        AutomaticAuthenticate = false,
        Audience = "http://localhost:5001/",
        Authority = "http://localhost:5000/",
        RequireHttpsMetadata = false
    });
```

<span data-ttu-id="c9579-118">위의 코드에서 인증 미들웨어 두 개 추가 되었습니다: 쿠키 및 전달자에 대 한 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-118">In the preceding code, two authentication middlewares have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="c9579-119">기본 스키마를 지정 하면를 `HttpContext.User` 해당 id로 설정 되는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-119">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="c9579-120">해당 동작은 필요 하지 않지만, 하는 경우 사용 하지 않도록 설정 하 여 합니다 `AuthenticationOptions.AutomaticAuthenticate` 속성을 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-120">If that behavior isn't desired, disable it by setting the `AuthenticationOptions.AutomaticAuthenticate` property to `false`.</span></span>

---

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="c9579-121">Authorize 특성을 사용 하 여 스키마를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-121">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="c9579-122">권한 부여 시점에서 앱에 사용할 처리기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-122">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="c9579-123">처리기는 앱의 인증 체계를 쉼표로 구분 된 목록을 전달 하 여 권한이 부여 됩니다 선택 `[Authorize]`합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-123">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="c9579-124">`[Authorize]` 특성이 인증 체계 또는 기본값 구성 되어 있는지 여부에 관계 없이 사용할 체계를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-124">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="c9579-125">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="c9579-125">For example:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c9579-126">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c9579-126">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c9579-127">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c9579-127">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

---

<span data-ttu-id="c9579-128">앞의 예제에서 쿠키와 전달자 처리기 실행 되며를 만들고 현재 사용자에 대 한 id를 추가할 수 있는 기회를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-128">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="c9579-129">만 단일 구성표를 지정 하 여 해당 처리기를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-129">By specifying a single scheme only, the corresponding handler runs.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c9579-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c9579-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c9579-131">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c9579-131">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

---

<span data-ttu-id="c9579-132">위의 코드에서 "Bearer" 체계를 사용 하 여 처리기만 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-132">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="c9579-133">쿠키 기반 id는 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-133">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="c9579-134">정책 사용 하 여 스키마를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-134">Selecting the scheme with policies</span></span>

<span data-ttu-id="c9579-135">원하는 스키마를 지정 하려는 경우 [정책](xref:security/authorization/policies)를 설정할 수 있습니다는 `AuthenticationSchemes` 컬렉션 정책을 추가 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="c9579-135">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

<span data-ttu-id="c9579-136">앞의 예제에서 "Over18" 정책 "Bearer" 처리기에 의해 생성 된 id에 대해 에서만 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-136">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="c9579-137">정책을 설정 하 여 사용 합니다 `[Authorize]` 특성의 `Policy` 속성:</span><span class="sxs-lookup"><span data-stu-id="c9579-137">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="c9579-138">여러 인증 체계를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-138">Use multiple authentication schemes</span></span>

<span data-ttu-id="c9579-139">일부 앱은 여러 유형의 인증을 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-139">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="c9579-140">예를 들어 앱 사용자 데이터베이스 뿐만 아니라 Azure Active Directory에서 사용자를 인증할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-140">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="c9579-141">또 다른 예로 Active Directory Federation Services와 Azure Active Directory B2C에서 사용자를 인증 하는 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-141">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="c9579-142">이 경우 앱에서 여러 발급자 JWT 전달자 토큰을 수락 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-142">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="c9579-143">적용 하려는 모든 인증 체계를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-143">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="c9579-144">다음 코드 예를 들어 `Startup.ConfigureServices` 다른 발급자를 사용 하 여 두 JWT 전달자 인증 체계를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-144">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> <span data-ttu-id="c9579-145">기본 인증 체계를 사용 하 여 JWT 전달자 인증을 하나만 등록 `JwtBearerDefaults.AuthenticationScheme`합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-145">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="c9579-146">추가 인증 고유 인증 체계를 사용 하 여 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-146">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="c9579-147">다음 단계는 모두 인증 체계를 적용 하는 기본 권한 부여 정책을 업데이트 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-147">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="c9579-148">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="c9579-148">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

<span data-ttu-id="c9579-149">기본 권한 부여 정책이 재정의 되는지 때 사용할 수는 `[Authorize]` 컨트롤러의 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-149">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="c9579-150">컨트롤러는 다음 첫 번째 또는 두 번째 발급자가 발급 한 JWT를 사용 하 여 요청을 수락 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9579-150">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end
