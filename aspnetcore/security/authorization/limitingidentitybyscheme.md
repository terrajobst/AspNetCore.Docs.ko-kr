---
title: ASP.NET Core에서 특정 체계를 사용 하 여 권한 부여
author: rick-anderson
description: 이 문서에서는 여러 인증 방법으로 작업할 때 id를 특정 체계로 제한 하는 방법을 설명 합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 38da80519b9d5d097c24d38b5a37503174629fc4
ms.sourcegitcommit: 4818385c3cfe0805e15138a2c1785b62deeaab90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896966"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="a59a9-103">ASP.NET Core에서 특정 체계를 사용 하 여 권한 부여</span><span class="sxs-lookup"><span data-stu-id="a59a9-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="a59a9-104">SPAs (단일 페이지 응용 프로그램)와 같은 일부 시나리오에서는 여러 인증 방법을 사용 하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="a59a9-105">예를 들어 앱은 쿠키 기반 인증을 사용 하 여 JavaScript 요청에 대해 로그인 및 JWT 전달자 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-105">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="a59a9-106">경우에 따라 앱에 인증 처리기의 인스턴스가 여러 개 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="a59a9-107">예를 들어, 하나는 기본 id를 포함 하 고 다른 하나는 MFA (multi-factor authentication)가 트리거될 때 생성 되는 두 개의 쿠키 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-107">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="a59a9-108">사용자가 추가 보안이 필요한 작업을 요청 하 여 MFA를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span>

<span data-ttu-id="a59a9-109">인증 체계는 인증 중에 인증 서비스가 구성 될 때 이름이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-109">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="a59a9-110">예를 들어 다음과 같은 가치를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-110">For example:</span></span>

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

<span data-ttu-id="a59a9-111">위의 코드에서 두 개의 인증 처리기가 추가 되었습니다. 하나는 쿠키이 고 하나는 전달자 용입니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-111">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="a59a9-112">기본 체계를 지정 하면 `HttpContext.User` 속성이 해당 id로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-112">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="a59a9-113">이 동작이 필요 하지 않은 경우에는 매개 변수가 없는 형식의 `AddAuthentication`를 호출 하 여 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-113">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="a59a9-114">권한 부여 특성이 있는 체계 선택</span><span class="sxs-lookup"><span data-stu-id="a59a9-114">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="a59a9-115">권한 부여 시점에서 앱은 사용할 처리기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-115">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="a59a9-116">쉼표로 구분 된 인증 스키마 목록을 `[Authorize]`에 전달 하 여 앱에 권한을 부여 하는 처리기를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-116">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="a59a9-117">`[Authorize]` 특성은 기본적으로 구성 되어 있는지 여부에 관계 없이 사용할 인증 체계 또는 체계를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-117">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="a59a9-118">예를 들어 다음과 같은 가치를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-118">For example:</span></span>

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

<span data-ttu-id="a59a9-119">앞의 예제에서는 쿠키와 전달자 처리기를 실행 하 고 현재 사용자에 대 한 id를 만들고 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-119">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="a59a9-120">단일 스키마만 지정 하면 해당 처리기가 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-120">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="a59a9-121">위의 코드에서는 "전달자" 체계가 있는 처리기만 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-121">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="a59a9-122">쿠키 기반 id는 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-122">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="a59a9-123">정책을 사용 하 여 체계 선택</span><span class="sxs-lookup"><span data-stu-id="a59a9-123">Selecting the scheme with policies</span></span>

<span data-ttu-id="a59a9-124">[정책](xref:security/authorization/policies)에서 원하는 스키마를 지정 하려는 경우 정책을 추가할 때 `AuthenticationSchemes` 컬렉션을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-124">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

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

<span data-ttu-id="a59a9-125">위의 예제에서 "Over18" 정책은 "전달자" 처리기에서 만든 id에 대해서만 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-125">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="a59a9-126">`[Authorize]` 특성의 `Policy` 속성을 설정 하 여 정책을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-126">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="a59a9-127">여러 인증 체계 사용</span><span class="sxs-lookup"><span data-stu-id="a59a9-127">Use multiple authentication schemes</span></span>

<span data-ttu-id="a59a9-128">일부 앱은 여러 유형의 인증을 지원 해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-128">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="a59a9-129">예를 들어 앱이 사용자 데이터베이스에서 Azure Active Directory 사용자를 인증할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-129">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="a59a9-130">또 다른 예로 Active Directory Federation Services 및 Azure Active Directory B2C에서 사용자를 인증 하는 앱이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-130">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="a59a9-131">이 경우 앱은 여러 발급자의 JWT 전달자 토큰을 수락 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-131">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="a59a9-132">수락 하려는 모든 인증 스키마를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-132">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="a59a9-133">예를 들어 `Startup.ConfigureServices`의 다음 코드는 발급자가 서로 다른 두 개의 JWT 전달자 인증 체계를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-133">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

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
> <span data-ttu-id="a59a9-134">`JwtBearerDefaults.AuthenticationScheme`기본 인증 체계를 사용 하 여 JWT 전달자 인증을 하나만 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-134">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="a59a9-135">추가 인증은 고유한 인증 체계를 사용 하 여 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-135">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="a59a9-136">다음 단계는 두 인증 체계를 모두 허용 하도록 기본 권한 부여 정책을 업데이트 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-136">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="a59a9-137">예를 들어 다음과 같은 가치를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-137">For example:</span></span>

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

<span data-ttu-id="a59a9-138">기본 권한 부여 정책이 재정의 되 면 컨트롤러에서 `[Authorize]` 특성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-138">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="a59a9-139">그러면 컨트롤러는 첫 번째 또는 두 번째 발급자가 발급 한 JWT를 사용 하 여 요청을 수락 합니다.</span><span class="sxs-lookup"><span data-stu-id="a59a9-139">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end
