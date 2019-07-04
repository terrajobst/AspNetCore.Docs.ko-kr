---
title: Facebook, Google 및 ASP.NET Core Id 없이 외부 공급자 인증
author: rick-anderson
description: Facebook, Google, Twitter, ASP.NET Core Id 없이 등 계정 사용자 인증을 사용 하 여 설명 합니다.
ms.author: riande
ms.date: 07/04/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 1e7124e8b07c0faf2d005ec3ef55c0414a697d64
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561563"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="7330b-103">ASP.NET Core Id 없이 사용 하 여 소셜 로그인 공급자 인증</span><span class="sxs-lookup"><span data-stu-id="7330b-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="7330b-104"><xref:security/authentication/social/index> 사용자가 OAuth 2.0을 사용 하 여 외부 인증 공급자의 자격 증명으로 로그인 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="7330b-105">해당 항목에서 설명 하는 방식은 인증 공급자를 ASP.NET Core Id를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="7330b-106">이 샘플에는 외부 인증 공급자를 사용 하는 방법을 보여 줍니다 **없이** ASP.NET Core Id입니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="7330b-107">ASP.NET Core Id의 기능의 일부를 요구 하지 않지만 여전히를 신뢰할 수 있는 외부 인증 공급자를 사용 하 여 통합이 필요 하는 앱에 대 한 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="7330b-108">이 샘플에서는 [Google 인증](xref:security/authentication/google-logins) 사용자를 인증 합니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="7330b-109">Google을 사용 하 여 인증 이동 Google에 로그인 프로세스를 관리 하는 복잡 한 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="7330b-110">다른 외부 인증 공급자와 통합 하려면 다음 항목을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="7330b-111">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="7330b-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="7330b-112">Microsoft 인증</span><span class="sxs-lookup"><span data-stu-id="7330b-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="7330b-113">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="7330b-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="7330b-114">기타 공급자</span><span class="sxs-lookup"><span data-stu-id="7330b-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="7330b-115">구성</span><span class="sxs-lookup"><span data-stu-id="7330b-115">Configuration</span></span>

<span data-ttu-id="7330b-116">에 `ConfigureServices` 메서드를 사용 하 여 앱의 인증 체계를 구성 합니다 `AddAuthentication`를 `AddCookie` 및 `AddGoogle` 메서드:</span><span class="sxs-lookup"><span data-stu-id="7330b-116">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie` and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="7330b-117">에 대 한 호출 [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) 앱의 설정 [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)합니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-117">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="7330b-118">합니다 `DefaultScheme` 다음에서 사용 하는 기본 체계 `HttpContext` 인증 확장 메서드:</span><span class="sxs-lookup"><span data-stu-id="7330b-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="7330b-119">앱 설정 `DefaultScheme` 하 [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("쿠키") 이러한 확장 메서드에 대 한 기본 체계로 쿠키를 사용 하도록 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="7330b-120">앱 설정 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 하 [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google")에 대 한 호출에 대 한 기본 체계로 Google을 사용 하도록 앱 구성 `ChallengeAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="7330b-121">`DefaultChallengeScheme`는 `DefaultScheme`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="7330b-122">참조 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> 재정의 하는 추가 속성에 대 한 `DefaultScheme` 설정 된 경우.</span><span class="sxs-lookup"><span data-stu-id="7330b-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="7330b-123">에 `Configure` 메서드를 호출 합니다 `UseAuthentication` 설정 하는 인증 미들웨어를 호출 하는 방법을 `HttpContext.User` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-123">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="7330b-124">호출 된 `UseAuthentication` 메서드를 호출 하기 전에 `UseMvcWithDefaultRoute` 또는 `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="7330b-124">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="7330b-125">인증 체계 및 쿠키 인증에 대 한 자세한 내용은를 참조 하세요. <xref:security/authentication/cookie>합니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-125">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="applying-authorization"></a><span data-ttu-id="7330b-126">권한 부여를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-126">Applying authorization</span></span>

<span data-ttu-id="7330b-127">앱의 인증 구성을 적용 하 여 테스트를 `AuthorizeAttribute` 특성을 컨트롤러, 작업 또는 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-127">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="7330b-128">다음 코드에 대 한 액세스를 제한 합니다 *개인 정보 보호* 인증 된 사용자에 게 페이지:</span><span class="sxs-lookup"><span data-stu-id="7330b-128">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="7330b-129">로그아웃</span><span class="sxs-lookup"><span data-stu-id="7330b-129">Sign out</span></span>

<span data-ttu-id="7330b-130">현재 사용자 로그 아웃을 해당 쿠키를 삭제 하려면 호출 [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0)합니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-130">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0).</span></span> <span data-ttu-id="7330b-131">다음 코드를 추가 하는 `Logout` 페이지 처리기를 *인덱스* 페이지:</span><span class="sxs-lookup"><span data-stu-id="7330b-131">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

<span data-ttu-id="7330b-132">호출 `SignOutAsync` 인증 체계를 지정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-132">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="7330b-133">앱의 `DefaultScheme` 의 `CookieAuthenticationDefaults.AuthenticationScheme` 를 대신 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7330b-133">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7330b-134">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7330b-134">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>
