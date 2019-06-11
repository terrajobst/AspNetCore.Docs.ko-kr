---
title: ASP.NET Core Id 없이 쿠키 인증 사용
author: rick-anderson
description: ASP.NET Core Id 없이 쿠키 인증을 사용 하 여 설명
ms.author: riande
ms.date: 02/25/2019
uid: security/authentication/cookie
ms.openlocfilehash: f3e02b357a83cf5fc4b9fcdc79b2fbe80da98507
ms.sourcegitcommit: 9691b742134563b662948b0ed63f54ef7186801e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2019
ms.locfileid: "66824758"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="fe2d5-103">ASP.NET Core Id 없이 쿠키 인증 사용</span><span class="sxs-lookup"><span data-stu-id="fe2d5-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="fe2d5-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fe2d5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fe2d5-105">이전 인증 항목에서는 지금까지 살펴보았듯이 [ASP.NET Core Id](xref:security/authentication/identity) 만들기 및 로그인을 유지 관리를 완전 하 고 모든 기능을 갖춘 인증 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-105">As you've seen in the earlier authentication topics, [ASP.NET Core Identity](xref:security/authentication/identity) is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="fe2d5-106">그러나 다음 쿠키 기반 인증에 사용 하 여 사용자 고유의 사용자 지정 인증 논리를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-106">However, you may want to use your own custom authentication logic with cookie-based authentication at times.</span></span> <span data-ttu-id="fe2d5-107">ASP.NET Core Id 없이 독립 실행형 인증 공급자로 쿠키 기반 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-107">You can use cookie-based authentication as a standalone authentication provider without ASP.NET Core Identity.</span></span>

<span data-ttu-id="fe2d5-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fe2d5-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fe2d5-109">샘플 앱에서 데모를 위해 Maria Rodriguez 가상 사용자의 사용자 계정을 응용 프로그램에 하드 코딩 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="fe2d5-110">전자 메일 사용자 이름을 사용 하 여 "maria.rodriguez@contoso.com" 및 사용자를 로그인 할 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-110">Use the Email username "maria.rodriguez@contoso.com" and any password to sign in the user.</span></span> <span data-ttu-id="fe2d5-111">사용자가 인증을 `AuthenticateUser` 의 메서드를 *Pages/Account/Login.cshtml.cs* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="fe2d5-112">실제 예제에서는 데이터베이스에 대해 사용자를 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-112">In a real-world example, the user would be authenticated against a database.</span></span>

<span data-ttu-id="fe2d5-113">ASP.NET Core에서 마이그레이션 쿠키 기반 인증에 대 한 내용은 1.x에서 2.0으로 참조 [ASP.NET Core 2.0 항목 (쿠키 기반 인증)으로 마이그레이션 인증 및 Id](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication)합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-113">For information on migrating cookie-based authentication from ASP.NET Core 1.x to 2.0, see [Migrate Authentication and Identity to ASP.NET Core 2.0 topic (Cookie-based Authentication)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).</span></span>

<span data-ttu-id="fe2d5-114">ASP.NET Core Id를 사용 하려면 참조는 [Id 소개](xref:security/authentication/identity) 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-114">To use ASP.NET Core Identity, see the [Introduction to Identity](xref:security/authentication/identity) topic.</span></span>

## <a name="configuration"></a><span data-ttu-id="fe2d5-115">구성</span><span class="sxs-lookup"><span data-stu-id="fe2d5-115">Configuration</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="fe2d5-116">앱을 사용 하지 않는 경우는 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app), 프로젝트 파일의 패키지 참조를 만듭니다 합니다 [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 패키지 (버전 2.1.0 또는 나중에).</span><span class="sxs-lookup"><span data-stu-id="fe2d5-116">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package (version 2.1.0 or later).</span></span>

<span data-ttu-id="fe2d5-117">에 `ConfigureServices` 메서드를 인증 미들웨어의 서비스를 만드는 합니다 `AddAuthentication` 및 `AddCookie` 메서드:</span><span class="sxs-lookup"><span data-stu-id="fe2d5-117">In the `ConfigureServices` method, create the Authentication Middleware service with the `AddAuthentication` and `AddCookie` methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="fe2d5-118">`AuthenticationScheme` 전달할 `AddAuthentication` 앱에 대 한 기본 인증 체계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-118">`AuthenticationScheme` passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="fe2d5-119">`AuthenticationScheme` 쿠키 인증의 인스턴스가 여러 개 있고 하려는 경우에 유용 [특정 구성표로 권한 부여](xref:security/authorization/limitingidentitybyscheme)합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-119">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="fe2d5-120">설정 된 `AuthenticationScheme` 에 `CookieAuthenticationDefaults.AuthenticationScheme` 체계를 "쿠키"의 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-120">Setting the `AuthenticationScheme` to `CookieAuthenticationDefaults.AuthenticationScheme` provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="fe2d5-121">스키마를 구분 하는 임의의 문자열 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-121">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="fe2d5-122">앱의 인증 체계는 앱의 쿠키 인증 체계와 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-122">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="fe2d5-123">쿠키 인증 체계에 제공 되지 않는 경우 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>를 사용 하 여 [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("쿠키").</span><span class="sxs-lookup"><span data-stu-id="fe2d5-123">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies").</span></span>

<span data-ttu-id="fe2d5-124">인증 쿠키가 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 속성이 `true` 기본적으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-124">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="fe2d5-125">인증 쿠키는 사이트 방문자가 데이터 수집에 동의 하지 않은 경우에 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-125">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="fe2d5-126">자세한 내용은 <xref:security/gdpr#essential-cookies>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-126">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="fe2d5-127">에 `Configure` 메서드를 사용 하 여는 `UseAuthentication` 설정 하는 인증 미들웨어를 호출 하는 방법의 `HttpContext.User` 속성.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-127">In the `Configure` method, use the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="fe2d5-128">호출 된 `UseAuthentication` 메서드를 호출 하기 전에 `UseMvcWithDefaultRoute` 또는 `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="fe2d5-128">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="fe2d5-129">**AddCookie 옵션**</span><span class="sxs-lookup"><span data-stu-id="fe2d5-129">**AddCookie Options**</span></span>

<span data-ttu-id="fe2d5-130">합니다 [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) 클래스 인증 공급자 옵션을 구성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-130">The [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) class is used to configure the authentication provider options.</span></span>

| <span data-ttu-id="fe2d5-131">옵션</span><span class="sxs-lookup"><span data-stu-id="fe2d5-131">Option</span></span> | <span data-ttu-id="fe2d5-132">설명</span><span class="sxs-lookup"><span data-stu-id="fe2d5-132">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="fe2d5-133">AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="fe2d5-133">AccessDeniedPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-134">302 있음 (URL 리디렉션) 제공에 대 한 경로 제공 하 여 트리거되면 `HttpContext.ForbidAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-134">Provides the path to supply with a 302 Found (URL redirect) when triggered by `HttpContext.ForbidAsync`.</span></span> <span data-ttu-id="fe2d5-135">기본값은 `/Account/AccessDenied`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-135">The default value is `/Account/AccessDenied`.</span></span> |
| [<span data-ttu-id="fe2d5-136">ClaimsIssuer</span><span class="sxs-lookup"><span data-stu-id="fe2d5-136">ClaimsIssuer</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.claimsissuer?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-137">에 사용할 발급자 합니다 [발급자](/dotnet/api/system.security.claims.claim.issuer) 쿠키 인증 서비스에 의해 만들어진 모든 클레임의 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-137">The issuer to use for the [Issuer](/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication service.</span></span> |
| [<span data-ttu-id="fe2d5-138">Cookie.Domain</span><span class="sxs-lookup"><span data-stu-id="fe2d5-138">Cookie.Domain</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-139">쿠키를 제공 하는 도메인 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-139">The domain name where the cookie is served.</span></span> <span data-ttu-id="fe2d5-140">기본적으로 요청 호스트 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-140">By default, this is the host name of the request.</span></span> <span data-ttu-id="fe2d5-141">브라우저 요청에서 쿠키를 일치 하는 호스트 이름에만 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-141">The browser only sends the cookie in requests to a matching host name.</span></span> <span data-ttu-id="fe2d5-142">이 도메인의 모든 호스트에 사용할 수 있는 쿠키를 조정 하고자 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-142">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="fe2d5-143">예를 들어 쿠키 도메인 설정을 `.contoso.com` 사용할 수 있도록 `contoso.com`를 `www.contoso.com`, 및 `staging.www.contoso.com`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-143">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, and `staging.www.contoso.com`.</span></span> |
| [<span data-ttu-id="fe2d5-144">Cookie.HttpOnly</span><span class="sxs-lookup"><span data-stu-id="fe2d5-144">Cookie.HttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-145">쿠키를 서버에만 액세스할 수 있어야 하는 경우를 나타내는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-145">A flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="fe2d5-146">이 값을 변경 `false` 쿠키에 액세스 하는 클라이언트 쪽 스크립트를 허용 하 고 앱 있어야 앱 쿠키 도용을 열 수 있습니다를 [교차 사이트 스크립팅 (XSS)](xref:security/cross-site-scripting) 취약점으로 인 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-146">Changing this value to `false` permits client-side scripts to access the cookie and may open your app to cookie theft should your app have a [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerability.</span></span> <span data-ttu-id="fe2d5-147">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-147">The default value is `true`.</span></span> |
| [<span data-ttu-id="fe2d5-148">Cookie.Name</span><span class="sxs-lookup"><span data-stu-id="fe2d5-148">Cookie.Name</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-149">쿠키의 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-149">Sets the name of the cookie.</span></span> |
| [<span data-ttu-id="fe2d5-150">Cookie.Path</span><span class="sxs-lookup"><span data-stu-id="fe2d5-150">Cookie.Path</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-151">동일한 호스트 이름에서 실행 중인 앱을 격리 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-151">Used to isolate apps running on the same host name.</span></span> <span data-ttu-id="fe2d5-152">실행 되는 앱이 있는 경우 `/app1` 및 해당 앱에 대 한 쿠키를 제한 하려면 설정 합니다 `CookiePath` 속성을 `/app1`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-152">If you have an app running at `/app1` and want to restrict cookies to that app, set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="fe2d5-153">이 작업을 수행 하면 쿠키 에서만 사용 가능 대 한 요청에 `/app1` 및 그 아래에 있는 모든 앱.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-153">By doing so, the cookie is only available on requests to `/app1` and any app underneath it.</span></span> |
| [<span data-ttu-id="fe2d5-154">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="fe2d5-154">Cookie.SameSite</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-155">브라우저에서 동일한 사이트 요청에 연결 될 쿠키를 허용 하는지 여부를 나타냅니다 (`SameSiteMode.Strict`) 또는 동일한 사이트 요청과 안전한 HTTP 메서드를 사용 하 여 사이트 간 요청 (`SameSiteMode.Lax`).</span><span class="sxs-lookup"><span data-stu-id="fe2d5-155">Indicates whether the browser should allow the cookie to be attached to same-site requests only (`SameSiteMode.Strict`) or cross-site requests using safe HTTP methods and same-site requests (`SameSiteMode.Lax`).</span></span> <span data-ttu-id="fe2d5-156">로 설정 하면 `SameSiteMode.None`, 쿠키 헤더 값이 설정 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-156">When set to `SameSiteMode.None`, the cookie header value isn't set.</span></span> <span data-ttu-id="fe2d5-157">사실은 [쿠키 정책 미들웨어](#cookie-policy-middleware) 제공 하는 값을 덮어쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-157">Note that [Cookie Policy Middleware](#cookie-policy-middleware) might overwrite the value that you provide.</span></span> <span data-ttu-id="fe2d5-158">기본값은 OAuth 인증을 지원 하려면 `SameSiteMode.Lax`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-158">To support OAuth authentication, the default value is `SameSiteMode.Lax`.</span></span> <span data-ttu-id="fe2d5-159">자세한 내용은 [SameSite 쿠키 정책으로 인해 손상 되는 OAuth 인증](https://github.com/aspnet/Security/issues/1231)합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-159">For more information, see [OAuth authentication broken due to SameSite cookie policy](https://github.com/aspnet/Security/issues/1231).</span></span> |
| [<span data-ttu-id="fe2d5-160">Cookie.SecurePolicy</span><span class="sxs-lookup"><span data-stu-id="fe2d5-160">Cookie.SecurePolicy</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.securepolicy?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-161">만들어진 쿠키가 HTTPS로 제한 해야 하는 경우를 나타내는 플래그 (`CookieSecurePolicy.Always`), HTTP 또는 HTTPS (`CookieSecurePolicy.None`), 또는 요청과 동일한 프로토콜 (`CookieSecurePolicy.SameAsRequest`).</span><span class="sxs-lookup"><span data-stu-id="fe2d5-161">A flag indicating if the cookie created should be limited to HTTPS (`CookieSecurePolicy.Always`), HTTP or HTTPS (`CookieSecurePolicy.None`), or the same protocol as the request (`CookieSecurePolicy.SameAsRequest`).</span></span> <span data-ttu-id="fe2d5-162">기본값은 `CookieSecurePolicy.SameAsRequest`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-162">The default value is `CookieSecurePolicy.SameAsRequest`.</span></span> |
| [<span data-ttu-id="fe2d5-163">DataProtectionProvider</span><span class="sxs-lookup"><span data-stu-id="fe2d5-163">DataProtectionProvider</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-164">설정 된 `DataProtectionProvider` 기본값을 만드는 데 사용 되는 `TicketDataFormat`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-164">Sets the `DataProtectionProvider` that's used to create the default `TicketDataFormat`.</span></span> <span data-ttu-id="fe2d5-165">경우는 `TicketDataFormat` 속성을 설정 합니다 `DataProtectionProvider` 옵션 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-165">If the `TicketDataFormat` property is set, the `DataProtectionProvider` option isn't used.</span></span> <span data-ttu-id="fe2d5-166">지정 하지 않으면 앱의 기본 데이터 보호 공급자 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-166">If not provided, the app's default data protection provider is used.</span></span> |
| [<span data-ttu-id="fe2d5-167">이벤트</span><span class="sxs-lookup"><span data-stu-id="fe2d5-167">Events</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.events?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-168">처리기는 특정 처리 지점에서 앱 제어를 제공 하는 공급자에서 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-168">The handler calls methods on the provider that give the app control at certain processing points.</span></span> <span data-ttu-id="fe2d5-169">경우 `Events` 메서드 호출 될 때 아무 작업도 하지 않는 기본 인스턴스가 제공 되는 제공 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-169">If `Events` aren't provided, a default instance is supplied that does nothing when the methods are called.</span></span> |
| [<span data-ttu-id="fe2d5-170">EventsType</span><span class="sxs-lookup"><span data-stu-id="fe2d5-170">EventsType</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.eventstype?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-171">서비스 형식으로 가져오는 데 사용 된 `Events` 속성 대신 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-171">Used as the service type to get the `Events` instance instead of the property.</span></span> |
| [<span data-ttu-id="fe2d5-172">ExpireTimeSpan</span><span class="sxs-lookup"><span data-stu-id="fe2d5-172">ExpireTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-173">`TimeSpan` 쿠키 안에 저장 된 인증 티켓 만료 된 이후입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-173">The `TimeSpan` after which the authentication ticket stored inside the cookie expires.</span></span> <span data-ttu-id="fe2d5-174">`ExpireTimeSpan` 티켓에 대 한 만료 기간을 설정 하려면 현재 시간에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-174">`ExpireTimeSpan` is added to the current time to create the expiration time for the ticket.</span></span> <span data-ttu-id="fe2d5-175">`ExpiredTimeSpan` 항상 서버에서 확인 하는 암호화 된 AuthTicket 값에 들어갑니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-175">The `ExpiredTimeSpan` value always goes into the encrypted AuthTicket verified by the server.</span></span> <span data-ttu-id="fe2d5-176">로 할 수도 있습니다는 [Set-cookie](https://tools.ietf.org/html/rfc6265#section-4.1) 헤더가 없지만 경우에만 `IsPersistent` 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-176">It may also go into the [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) header, but only if `IsPersistent` is set.</span></span> <span data-ttu-id="fe2d5-177">설정 하려면 `IsPersistent` 를 `true`를 구성 합니다 [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) 전달할 `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-177">To set `IsPersistent` to `true`, configure the [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) passed to `SignInAsync`.</span></span> <span data-ttu-id="fe2d5-178">기본값은 `ExpireTimeSpan` 14 일입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-178">The default value of `ExpireTimeSpan` is 14 days.</span></span> |
| [<span data-ttu-id="fe2d5-179">LoginPath</span><span class="sxs-lookup"><span data-stu-id="fe2d5-179">LoginPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-180">302 있음 (URL 리디렉션) 제공에 대 한 경로 제공 하 여 트리거되면 `HttpContext.ChallengeAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-180">Provides the path to supply with a 302 Found (URL redirect) when triggered by `HttpContext.ChallengeAsync`.</span></span> <span data-ttu-id="fe2d5-181">401을 생성 하는 현재 URL에 추가 됩니다는 `LoginPath` 으로 명명 된 쿼리 문자열 매개 변수로 `ReturnUrlParameter`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-181">The current URL that generated the 401 is added to the `LoginPath` as a query string parameter named by the `ReturnUrlParameter`.</span></span> <span data-ttu-id="fe2d5-182">요청을 한 번 합니다 `LoginPath` 새 로그인 id에 부여 합니다 `ReturnUrlParameter` 값은 원래 권한이 없음된 상태 코드가 발생 시킨 URL로 다시 브라우저를 리디렉션하는 데 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-182">Once a request to the `LoginPath` grants a new sign-in identity, the `ReturnUrlParameter` value is used to redirect the browser back to the URL that caused the original unauthorized status code.</span></span> <span data-ttu-id="fe2d5-183">기본값은 `/Account/Login`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-183">The default value is `/Account/Login`.</span></span> |
| [<span data-ttu-id="fe2d5-184">LogoutPath</span><span class="sxs-lookup"><span data-stu-id="fe2d5-184">LogoutPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-185">경우는 `LogoutPath` 해당 경로에 요청을 리디렉션합니다 처리기에 제공 됩니다의 값을 기반으로 합니다 `ReturnUrlParameter`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-185">If the `LogoutPath` is provided to the handler, then a request to that path redirects based on the value of the `ReturnUrlParameter`.</span></span> <span data-ttu-id="fe2d5-186">기본값은 `/Account/Logout`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-186">The default value is `/Account/Logout`.</span></span> |
| [<span data-ttu-id="fe2d5-187">ReturnUrlParameter</span><span class="sxs-lookup"><span data-stu-id="fe2d5-187">ReturnUrlParameter</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.returnurlparameter?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-188">302 있음 (URL 리디렉션) 응답에 대 한 처리기가 추가 되는 쿼리 문자열 매개 변수의 이름을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-188">Determines the name of the query string parameter that's appended by the handler for a 302 Found (URL redirect) response.</span></span> <span data-ttu-id="fe2d5-189">`ReturnUrlParameter` 에 요청이 도착할 때 사용 되는 `LoginPath` 또는 `LogoutPath` 로그인 또는 로그 아웃 동작을 수행한 후 원래 URL로 브라우저를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-189">`ReturnUrlParameter` is used when a request arrives on the `LoginPath` or `LogoutPath` to return the browser to the original URL after the login or logout action is performed.</span></span> <span data-ttu-id="fe2d5-190">기본값은 `ReturnUrl`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-190">The default value is `ReturnUrl`.</span></span> |
| [<span data-ttu-id="fe2d5-191">SessionStore</span><span class="sxs-lookup"><span data-stu-id="fe2d5-191">SessionStore</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.sessionstore?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-192">요청 간에 id를 저장 하는 데 사용 하는 선택적 컨테이너입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-192">An optional container used to store identity across requests.</span></span> <span data-ttu-id="fe2d5-193">를 사용 하면 세션 식별자만 클라이언트로 보내집니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-193">When used, only a session identifier is sent to the client.</span></span> <span data-ttu-id="fe2d5-194">`SessionStore` 많은 id와 잠재적인 문제를 완화 하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-194">`SessionStore` can be used to mitigate potential problems with large identities.</span></span> |
| [<span data-ttu-id="fe2d5-195">SlidingExpiration</span><span class="sxs-lookup"><span data-stu-id="fe2d5-195">SlidingExpiration</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-196">업데이트 된 만료 시간을 사용 하 여 새 쿠키를 동적으로 발급 해야 하는 경우를 나타내는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-196">A flag indicating if a new cookie with an updated expiration time should be issued dynamically.</span></span> <span data-ttu-id="fe2d5-197">이 현재 쿠키 만료 기간 보다 50% 만료 된 모든 요청에서 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-197">This can happen on any request where the current cookie expiration period is more than 50% expired.</span></span> <span data-ttu-id="fe2d5-198">새 만료 날짜 앞으로 이동 하는 현재 날짜와 `ExpireTimespan`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-198">The new expiration date is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="fe2d5-199">[절대 쿠키 만료 시간](xref:security/authentication/cookie#absolute-cookie-expiration) 사용 하 여 설정할 수 있습니다 합니다 `AuthenticationProperties` 클래스를 호출할 때 `SignInAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-199">An [absolute cookie expiration time](xref:security/authentication/cookie#absolute-cookie-expiration) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="fe2d5-200">절대 만료 시간을 인증 쿠키의 유효 기간을 제한 하 여 앱의 보안을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-200">An absolute expiration time can improve the security of your app by limiting the amount of time that the authentication cookie is valid.</span></span> <span data-ttu-id="fe2d5-201">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-201">The default value is `true`.</span></span> |
| [<span data-ttu-id="fe2d5-202">TicketDataFormat</span><span class="sxs-lookup"><span data-stu-id="fe2d5-202">TicketDataFormat</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.ticketdataformat?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-203">`TicketDataFormat` 보호 및 id 및 쿠키 값에 저장 되는 기타 속성을 보호 해제 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-203">The `TicketDataFormat` is used to protect and unprotect the identity and other properties that are stored in the cookie value.</span></span> <span data-ttu-id="fe2d5-204">제공 되지 않은 경우는 `TicketDataFormat` 사용 하 여 만들어집니다 합니다 [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0)합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-204">If not provided, a `TicketDataFormat` is created using the [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0).</span></span> |
| [<span data-ttu-id="fe2d5-205">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="fe2d5-205">Validate</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.validate?view=aspnetcore-2.0) | <span data-ttu-id="fe2d5-206">옵션은 유효성을 검사 하는 메서드.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-206">Method that checks that the options are valid.</span></span> |

<span data-ttu-id="fe2d5-207">설정할 `CookieAuthenticationOptions` 에서 인증을 위해 서비스 구성에는 `ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="fe2d5-207">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="fe2d5-208">ASP.NET Core 1.x에서는 쿠키 [미들웨어](xref:fundamentals/middleware/index) 암호화 된 쿠키에 사용자 보안 주체를 serialize 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-208">ASP.NET Core 1.x uses cookie [middleware](xref:fundamentals/middleware/index) that serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="fe2d5-209">후속 요청에서 쿠키의 유효성을 검사 하 고 주 서버 이며 다시 할당할는 `HttpContext.User` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-209">On subsequent requests, the cookie is validated, and the principal is recreated and assigned to the `HttpContext.User` property.</span></span>

<span data-ttu-id="fe2d5-210">설치 합니다 [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 프로젝트에 NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-210">Install the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) NuGet package in your project.</span></span> <span data-ttu-id="fe2d5-211">이 패키지는 쿠키 미들웨어를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-211">This package contains the cookie middleware.</span></span>

<span data-ttu-id="fe2d5-212">사용 하 여는 `UseCookieAuthentication` 의 메서드를 `Configure` 에서 메서드 하 *Startup.cs* 하기 전에 파일 `UseMvc` 또는 `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="fe2d5-212">Use the `UseCookieAuthentication` method in the `Configure` method in your *Startup.cs* file before `UseMvc` or `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
{
    AccessDeniedPath = "/Account/Forbidden/",
    AuthenticationScheme = CookieAuthenticationDefaults.AuthenticationScheme,
    AutomaticAuthenticate = true,
    AutomaticChallenge = true,
    LoginPath = "/Account/Unauthorized/"
});
```

<span data-ttu-id="fe2d5-213">**CookieAuthenticationOptions 옵션**</span><span class="sxs-lookup"><span data-stu-id="fe2d5-213">**CookieAuthenticationOptions Options**</span></span>

<span data-ttu-id="fe2d5-214">합니다 [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) 클래스 인증 공급자 옵션을 구성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-214">The [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) class is used to configure the authentication provider options.</span></span>

| <span data-ttu-id="fe2d5-215">옵션</span><span class="sxs-lookup"><span data-stu-id="fe2d5-215">Option</span></span> | <span data-ttu-id="fe2d5-216">설명</span><span class="sxs-lookup"><span data-stu-id="fe2d5-216">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="fe2d5-217">AuthenticationScheme</span><span class="sxs-lookup"><span data-stu-id="fe2d5-217">AuthenticationScheme</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.authenticationscheme?view=aspnetcore-1.1) | <span data-ttu-id="fe2d5-218">인증 체계를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-218">Sets the authentication scheme.</span></span> <span data-ttu-id="fe2d5-219">`AuthenticationScheme` 인증의 인스턴스가 여러 개 있고 하려는 경우에 유용 [특정 구성표로 권한 부여](xref:security/authorization/limitingidentitybyscheme)합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-219">`AuthenticationScheme` is useful when there are multiple instances of authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="fe2d5-220">설정 된 `AuthenticationScheme` 에 `CookieAuthenticationDefaults.AuthenticationScheme` 체계를 "쿠키"의 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-220">Setting the `AuthenticationScheme` to `CookieAuthenticationDefaults.AuthenticationScheme` provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="fe2d5-221">스키마를 구분 하는 임의의 문자열 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-221">You can supply any string value that distinguishes the scheme.</span></span> |
| [<span data-ttu-id="fe2d5-222">AutomaticAuthenticate</span><span class="sxs-lookup"><span data-stu-id="fe2d5-222">AutomaticAuthenticate</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticauthenticate?view=aspnetcore-1.1) | <span data-ttu-id="fe2d5-223">쿠키 인증 요청 마다 실행 및 유효성 검사 하 고 만든 직렬화 된 모든 보안 주체를 다시 생성할 수 있는지를 나타내는 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-223">Sets a value to indicate that the cookie authentication should run on every request and attempt to validate and reconstruct any serialized principal it created.</span></span> |
| [<span data-ttu-id="fe2d5-224">AutomaticChallenge</span><span class="sxs-lookup"><span data-stu-id="fe2d5-224">AutomaticChallenge</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticchallenge?view=aspnetcore-1.1) | <span data-ttu-id="fe2d5-225">True 이면 인증 미들웨어 자동 과제를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-225">If true, the authentication middleware handles automatic challenges.</span></span> <span data-ttu-id="fe2d5-226">경우 false 이면 인증 미들웨어만 변경 하 여 명시적으로 지정 하는 경우 응답은 `AuthenticationScheme`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-226">If false, the authentication middleware only alters responses when explicitly indicated by the `AuthenticationScheme`.</span></span> |
| [<span data-ttu-id="fe2d5-227">ClaimsIssuer</span><span class="sxs-lookup"><span data-stu-id="fe2d5-227">ClaimsIssuer</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.claimsissuer?view=aspnetcore-1.1) | <span data-ttu-id="fe2d5-228">에 사용할 발급자 합니다 [발급자](/dotnet/api/system.security.claims.claim.issuer) 쿠키 인증 미들웨어에서 만든 모든 클레임의 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-228">The issuer to use for the [Issuer](/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication middleware.</span></span> |
| [<span data-ttu-id="fe2d5-229">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="fe2d5-229">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiedomain?view=aspnetcore-1.1) | <span data-ttu-id="fe2d5-230">쿠키를 제공 하는 도메인 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-230">The domain name where the cookie is served.</span></span> <span data-ttu-id="fe2d5-231">기본적으로 요청 호스트 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-231">By default, this is the host name of the request.</span></span> <span data-ttu-id="fe2d5-232">브라우저 쿠키를 일치 하는 호스트 이름 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-232">The browser only serves the cookie to a matching host name.</span></span> <span data-ttu-id="fe2d5-233">이 도메인의 모든 호스트에 사용할 수 있는 쿠키를 조정 하고자 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-233">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="fe2d5-234">예를 들어 쿠키 도메인 설정을 `.contoso.com` 사용할 수 있도록 `contoso.com`를 `www.contoso.com`, 및 `staging.www.contoso.com`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-234">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, and `staging.www.contoso.com`.</span></span> |
| [<span data-ttu-id="fe2d5-235">CookieHttpOnly</span><span class="sxs-lookup"><span data-stu-id="fe2d5-235">CookieHttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiehttponly?view=aspnetcore-1.1) | <span data-ttu-id="fe2d5-236">쿠키를 서버에만 액세스할 수 있어야 하는 경우를 나타내는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-236">A flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="fe2d5-237">이 값을 변경 `false` 쿠키에 액세스 하는 클라이언트 쪽 스크립트를 허용 하 고 앱 있어야 앱 쿠키 도용을 열 수 있습니다를 [교차 사이트 스크립팅 (XSS)](xref:security/cross-site-scripting) 취약점으로 인 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-237">Changing this value to `false` permits client-side scripts to access the cookie and may open your app to cookie theft should your app have a [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerability.</span></span> <span data-ttu-id="fe2d5-238">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-238">The default value is `true`.</span></span> |
| [<span data-ttu-id="fe2d5-239">CookiePath</span><span class="sxs-lookup"><span data-stu-id="fe2d5-239">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiepath?view=aspnetcore-1.1) | <span data-ttu-id="fe2d5-240">동일한 호스트 이름에서 실행 중인 앱을 격리 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-240">Used to isolate apps running on the same host name.</span></span> <span data-ttu-id="fe2d5-241">실행 되는 앱이 있는 경우 `/app1` 및 해당 앱에 대 한 쿠키를 제한 하려면 설정 합니다 `CookiePath` 속성을 `/app1`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-241">If you have an app running at `/app1` and want to restrict cookies to that app, set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="fe2d5-242">이 작업을 수행 하면 쿠키 에서만 사용 가능 대 한 요청에 `/app1` 및 그 아래에 있는 모든 앱.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-242">By doing so, the cookie is only available on requests to `/app1` and any app underneath it.</span></span> |
| [<span data-ttu-id="fe2d5-243">CookieSecure</span><span class="sxs-lookup"><span data-stu-id="fe2d5-243">CookieSecure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiesecure?view=aspnetcore-1.1) | <span data-ttu-id="fe2d5-244">만들어진 쿠키가 HTTPS로 제한 해야 하는 경우를 나타내는 플래그 (`CookieSecurePolicy.Always`), HTTP 또는 HTTPS (`CookieSecurePolicy.None`), 또는 요청과 동일한 프로토콜 (`CookieSecurePolicy.SameAsRequest`).</span><span class="sxs-lookup"><span data-stu-id="fe2d5-244">A flag indicating if the cookie created should be limited to HTTPS (`CookieSecurePolicy.Always`), HTTP or HTTPS (`CookieSecurePolicy.None`), or the same protocol as the request (`CookieSecurePolicy.SameAsRequest`).</span></span> <span data-ttu-id="fe2d5-245">기본값은 `CookieSecurePolicy.SameAsRequest`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-245">The default value is `CookieSecurePolicy.SameAsRequest`.</span></span> |
| [<span data-ttu-id="fe2d5-246">설명</span><span class="sxs-lookup"><span data-stu-id="fe2d5-246">Description</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.description?view=aspnetcore-1.1) | <span data-ttu-id="fe2d5-247">앱에 사용할 수 있는 인증 유형에 대 한 추가 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-247">Additional information about the authentication type which is made available to the app.</span></span> |
| [<span data-ttu-id="fe2d5-248">ExpireTimeSpan</span><span class="sxs-lookup"><span data-stu-id="fe2d5-248">ExpireTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.expiretimespan?view=aspnetcore-1.1) | <span data-ttu-id="fe2d5-249">`TimeSpan` 인증 티켓이 만료 된 이후입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-249">The `TimeSpan` after which the authentication ticket expires.</span></span> <span data-ttu-id="fe2d5-250">티켓에 대 한 만료 기간을 설정 하려면 현재 시간으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-250">It's added to the current time to create the expiration time for the ticket.</span></span> <span data-ttu-id="fe2d5-251">사용 하 `ExpireTimeSpan`를 설정 해야 합니다 `IsPersistent` 에 `true` 에 `AuthenticationProperties` 전달할 `SignInAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-251">To use `ExpireTimeSpan`, you must set `IsPersistent` to `true` in the `AuthenticationProperties` passed to `SignInAsync`.</span></span> <span data-ttu-id="fe2d5-252">기본값은 14 일입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-252">The default value is 14 days.</span></span> |
| [<span data-ttu-id="fe2d5-253">SlidingExpiration</span><span class="sxs-lookup"><span data-stu-id="fe2d5-253">SlidingExpiration</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-1.1) | <span data-ttu-id="fe2d5-254">개 중 절반 쿠키 만료 날짜를 다시 설정 하는지 여부를 나타내는 플래그를 `ExpireTimeSpan` 간격이 경과 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-254">A flag indicating whether the cookie expiration date resets when more than half of the `ExpireTimeSpan` interval has passed.</span></span> <span data-ttu-id="fe2d5-255">새 만료 시간 앞으로 이동 하는 현재 날짜와 `ExpireTimespan`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-255">The new expiration time is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="fe2d5-256">[절대 쿠키 만료 시간](xref:security/authentication/cookie#absolute-cookie-expiration) 사용 하 여 설정할 수 있습니다 합니다 `AuthenticationProperties` 클래스를 호출할 때 `SignInAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-256">An [absolute cookie expiration time](xref:security/authentication/cookie#absolute-cookie-expiration) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="fe2d5-257">절대 만료 시간을 인증 쿠키의 유효 기간을 제한 하 여 앱의 보안을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-257">An absolute expiration time can improve the security of your app by limiting the amount of time that the authentication cookie is valid.</span></span> <span data-ttu-id="fe2d5-258">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-258">The default value is `true`.</span></span> |

<span data-ttu-id="fe2d5-259">설정할 `CookieAuthenticationOptions` 에서 쿠키 인증 미들웨어에 대 한는 `Configure` 메서드:</span><span class="sxs-lookup"><span data-stu-id="fe2d5-259">Set `CookieAuthenticationOptions` for the Cookie Authentication Middleware in the `Configure` method:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    ...
});
```

::: moniker-end

## <a name="cookie-policy-middleware"></a><span data-ttu-id="fe2d5-260">쿠키 정책 미들웨어</span><span class="sxs-lookup"><span data-stu-id="fe2d5-260">Cookie Policy Middleware</span></span>

<span data-ttu-id="fe2d5-261">[쿠키 정책 미들웨어](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) 앱의 쿠키 정책 기능을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-261">[Cookie Policy Middleware](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) enables cookie policy capabilities in an app.</span></span> <span data-ttu-id="fe2d5-262">앱 처리 파이프라인에 미들웨어를 추가 합니다. 순서가 중요 합니다. 구성 요소를 파이프라인에 등록 후에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-262">Adding the middleware to the app processing pipeline is order sensitive; it only affects components registered after it in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

 <span data-ttu-id="fe2d5-263">합니다 [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) 쿠키 정책 미들웨어에 제공 된 쿠키를 추가 하거나 삭제할 때 쿠키 처리 처리기에 쿠키 처리 및 후크의 전역 특성을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-263">The [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) provided to the Cookie Policy Middleware allow you to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

| <span data-ttu-id="fe2d5-264">속성</span><span class="sxs-lookup"><span data-stu-id="fe2d5-264">Property</span></span> | <span data-ttu-id="fe2d5-265">설명</span><span class="sxs-lookup"><span data-stu-id="fe2d5-265">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="fe2d5-266">HttpOnly</span><span class="sxs-lookup"><span data-stu-id="fe2d5-266">HttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.httponly) | <span data-ttu-id="fe2d5-267">쿠키는 HttpOnly 수 있어야 하면 여부를 나타내는 플래그 쿠키를 서버에만 액세스할 수 있어야 하는 경우 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-267">Affects whether cookies must be HttpOnly, which is a flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="fe2d5-268">기본값은 `HttpOnlyPolicy.None`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-268">The default value is `HttpOnlyPolicy.None`.</span></span> |
| [<span data-ttu-id="fe2d5-269">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="fe2d5-269">MinimumSameSitePolicy</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.minimumsamesitepolicy) | <span data-ttu-id="fe2d5-270">쿠키의 동일한 사이트 특성을 (아래 참조)에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-270">Affects the cookie's same-site attribute (see below).</span></span> <span data-ttu-id="fe2d5-271">기본값은 `SameSiteMode.Lax`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-271">The default value is `SameSiteMode.Lax`.</span></span> <span data-ttu-id="fe2d5-272">이 옵션은 ASP.NET Core 2.0 이상에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-272">This option is available for ASP.NET Core 2.0+.</span></span> |
| [<span data-ttu-id="fe2d5-273">OnAppendCookie</span><span class="sxs-lookup"><span data-stu-id="fe2d5-273">OnAppendCookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.onappendcookie) | <span data-ttu-id="fe2d5-274">쿠키를 추가할 때 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-274">Called when a cookie is appended.</span></span> |
| [<span data-ttu-id="fe2d5-275">OnDeleteCookie</span><span class="sxs-lookup"><span data-stu-id="fe2d5-275">OnDeleteCookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.ondeletecookie) | <span data-ttu-id="fe2d5-276">쿠키 삭제 될 때 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-276">Called when a cookie is deleted.</span></span> |
| [<span data-ttu-id="fe2d5-277">보안</span><span class="sxs-lookup"><span data-stu-id="fe2d5-277">Secure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.secure) | <span data-ttu-id="fe2d5-278">쿠키는 보안이 적용 되도록 해야 하는지 여부를 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-278">Affects whether cookies must be Secure.</span></span> <span data-ttu-id="fe2d5-279">기본값은 `CookieSecurePolicy.None`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-279">The default value is `CookieSecurePolicy.None`.</span></span> |

<span data-ttu-id="fe2d5-280">**MinimumSameSitePolicy** (ASP.NET Core 2.0 +만 해당)</span><span class="sxs-lookup"><span data-stu-id="fe2d5-280">**MinimumSameSitePolicy** (ASP.NET Core 2.0+ only)</span></span>

<span data-ttu-id="fe2d5-281">기본값 `MinimumSameSitePolicy` 값은 `SameSiteMode.Lax` OAuth2 인증을 허용 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-281">The default `MinimumSameSitePolicy` value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="fe2d5-282">동일한 사이트 정책을 엄격 하 게 적용할 `SameSiteMode.Strict`설정의 `MinimumSameSitePolicy`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-282">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="fe2d5-283">이 설정은 OAuth2 및 다른 크로스-원본 인증 체계를 중단 하지만 다른 유형의 크로스-원본 요청 처리에 의존 하지 않는 앱에 대 한 쿠키 보안 수준을 승격 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-283">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="fe2d5-284">에 대 한 쿠키 정책 미들웨어 설정을 `MinimumSameSitePolicy` 의 설정에 영향을 줄 수 있습니다 `Cookie.SameSite` 에서 `CookieAuthenticationOptions` 아래 매트릭스에 따라 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-284">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect your setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="fe2d5-285">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="fe2d5-285">MinimumSameSitePolicy</span></span> | <span data-ttu-id="fe2d5-286">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="fe2d5-286">Cookie.SameSite</span></span> | <span data-ttu-id="fe2d5-287">결과 Cookie.SameSite 설정</span><span class="sxs-lookup"><span data-stu-id="fe2d5-287">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="fe2d5-288">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="fe2d5-288">SameSiteMode.None</span></span>     | <span data-ttu-id="fe2d5-289">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="fe2d5-289">SameSiteMode.None</span></span><br><span data-ttu-id="fe2d5-290">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="fe2d5-290">SameSiteMode.Lax</span></span><br><span data-ttu-id="fe2d5-291">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="fe2d5-291">SameSiteMode.Strict</span></span> | <span data-ttu-id="fe2d5-292">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="fe2d5-292">SameSiteMode.None</span></span><br><span data-ttu-id="fe2d5-293">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="fe2d5-293">SameSiteMode.Lax</span></span><br><span data-ttu-id="fe2d5-294">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="fe2d5-294">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="fe2d5-295">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="fe2d5-295">SameSiteMode.Lax</span></span>      | <span data-ttu-id="fe2d5-296">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="fe2d5-296">SameSiteMode.None</span></span><br><span data-ttu-id="fe2d5-297">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="fe2d5-297">SameSiteMode.Lax</span></span><br><span data-ttu-id="fe2d5-298">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="fe2d5-298">SameSiteMode.Strict</span></span> | <span data-ttu-id="fe2d5-299">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="fe2d5-299">SameSiteMode.Lax</span></span><br><span data-ttu-id="fe2d5-300">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="fe2d5-300">SameSiteMode.Lax</span></span><br><span data-ttu-id="fe2d5-301">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="fe2d5-301">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="fe2d5-302">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="fe2d5-302">SameSiteMode.Strict</span></span>   | <span data-ttu-id="fe2d5-303">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="fe2d5-303">SameSiteMode.None</span></span><br><span data-ttu-id="fe2d5-304">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="fe2d5-304">SameSiteMode.Lax</span></span><br><span data-ttu-id="fe2d5-305">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="fe2d5-305">SameSiteMode.Strict</span></span> | <span data-ttu-id="fe2d5-306">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="fe2d5-306">SameSiteMode.Strict</span></span><br><span data-ttu-id="fe2d5-307">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="fe2d5-307">SameSiteMode.Strict</span></span><br><span data-ttu-id="fe2d5-308">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="fe2d5-308">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="fe2d5-309">인증 쿠키를 만드는</span><span class="sxs-lookup"><span data-stu-id="fe2d5-309">Create an authentication cookie</span></span>

<span data-ttu-id="fe2d5-310">사용자 정보를 보관 하는 쿠키를 만들려면 생성 해야 합니다는 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-310">To create a cookie holding user information, you must construct a [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal).</span></span> <span data-ttu-id="fe2d5-311">사용자 정보를 직렬화 되며 쿠키에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-311">The user information is serialized and stored in the cookie.</span></span> 

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="fe2d5-312">만들기는 [ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity) 필요한를 사용 하 여 [클레임](/dotnet/api/system.security.claims.claim)s 및 호출 [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) 사용자를 로그인 하려면:</span><span class="sxs-lookup"><span data-stu-id="fe2d5-312">Create a [ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity) with any required [Claim](/dotnet/api/system.security.claims.claim)s and call [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="fe2d5-313">호출 [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) 사용자를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-313">Call [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) to sign in the user:</span></span>

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker-end

<span data-ttu-id="fe2d5-314">`SignInAsync` 암호화 된 쿠키를 만들고 현재 응답에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-314">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="fe2d5-315">지정 하지 않으면는 `AuthenticationScheme`, 기본 구성표가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-315">If you don't specify an `AuthenticationScheme`, the default scheme is used.</span></span>

<span data-ttu-id="fe2d5-316">내부적으로 사용 되는 암호화는 ASP.NET Core [데이터 보호](xref:security/data-protection/using-data-protection) 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-316">Under the covers, the encryption used is ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system.</span></span> <span data-ttu-id="fe2d5-317">여러 컴퓨터, 앱에 부하 분산 또는 웹 팜에서 사용 하 여 앱을 호스트 하는 경우 수행 해야 합니다 [데이터 보호를 구성](xref:security/data-protection/configuration/overview) 동일한 키 링 및 앱 식별자를 사용 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-317">If you're hosting app on multiple machines, load balancing across apps, or using a web farm, then you must [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="fe2d5-318">로그아웃</span><span class="sxs-lookup"><span data-stu-id="fe2d5-318">Sign out</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="fe2d5-319">현재 사용자 로그 아웃을 해당 쿠키를 삭제 하려면 호출 [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):</span><span class="sxs-lookup"><span data-stu-id="fe2d5-319">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="fe2d5-320">현재 사용자 로그 아웃을 해당 쿠키를 삭제 하려면 호출 [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):</span><span class="sxs-lookup"><span data-stu-id="fe2d5-320">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):</span></span>

```csharp
await HttpContext.Authentication.SignOutAsync(
    CookieAuthenticationDefaults.AuthenticationScheme);
```

::: moniker-end

<span data-ttu-id="fe2d5-321">사용 하지 않는 경우 `CookieAuthenticationDefaults.AuthenticationScheme` (또는 "쿠키") 체계로 (예: "ContosoCookie"), 인증 공급자를 구성 하는 경우를 사용 하는 체계를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-321">If you aren't using `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") as the scheme (for example, "ContosoCookie"), supply the scheme you used when configuring the authentication provider.</span></span> <span data-ttu-id="fe2d5-322">그렇지 않은 경우 기본 스키마가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-322">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="fe2d5-323">백 엔드 변경에 대응</span><span class="sxs-lookup"><span data-stu-id="fe2d5-323">React to back-end changes</span></span>

<span data-ttu-id="fe2d5-324">쿠키를 만든 후에 id의 단일 원본 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-324">Once a cookie is created, it becomes the single source of identity.</span></span> <span data-ttu-id="fe2d5-325">백 엔드 시스템에서 사용자를 해제 하는 경우에 쿠키 인증 시스템에이 인식 하지 및 사용자가 자신의 쿠키의 유효으로 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-325">Even if you disable a user in your back-end systems, the cookie authentication system has no knowledge of this, and a user stays logged in as long as their cookie is valid.</span></span>

<span data-ttu-id="fe2d5-326">[ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) ASP.NET Core에서 이벤트 2.x 또는 [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) 메서드 ASP.NET core 1.x를 가로채 고 쿠키 id의 유효성 검사 재정의를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-326">The [ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) event in ASP.NET Core 2.x or the [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) method in ASP.NET Core 1.x can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="fe2d5-327">이 방법은 앱에 액세스 하는 해지 된 사용자의 위험을 완화 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-327">This approach mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="fe2d5-328">쿠키 유효성 검사는 한 가지 방법을 기반으로 사용자 데이터베이스 변경 되었을 때의 추적 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-328">One approach to cookie validation is based on keeping track of when the user database has been changed.</span></span> <span data-ttu-id="fe2d5-329">데이터베이스 사용자의 쿠키가 발행 된 이후 변경 되지 않은 경우 해당 쿠키가 여전히 유효한 경우 사용자를 다시 인증할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-329">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="fe2d5-330">이 시나리오에서 구현 되는 데이터베이스를 구현 하려면 `IUserRepository` 예를 들어 저장 된 `LastChanged` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-330">To implement this scenario, the database, which is implemented in `IUserRepository` for this example, stores a `LastChanged` value.</span></span> <span data-ttu-id="fe2d5-331">모든 사용자 데이터베이스에서 업데이트 되 면는 `LastChanged` 값이 현재 시간으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-331">When any user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="fe2d5-332">데이터베이스 변경 내용을 기반으로 하는 경우 쿠키를 무효화 하기 위해 합니다 `LastChanged` 값을 사용 하 여 쿠키를 만들기는 `LastChanged` 현재 포함 된 클레임 `LastChanged` 데이터베이스에서 값:</span><span class="sxs-lookup"><span data-stu-id="fe2d5-332">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="fe2d5-333">에 대 한 재정의 구현 하는 `ValidatePrincipal` 이벤트에서 파생 된 클래스에서 다음 서명으로 메서드가 작성 [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):</span><span class="sxs-lookup"><span data-stu-id="fe2d5-333">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that you derive from [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="fe2d5-334">예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-334">An example looks like the following:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="fe2d5-335">쿠키 service 등록 하는 동안 이벤트 인스턴스를 등록 합니다 `ConfigureServices` 메서드.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-335">Register the events instance during cookie service registration in the `ConfigureServices` method.</span></span> <span data-ttu-id="fe2d5-336">에 대 한 범위가 지정 된 서비스 등록을 제공 하면 `CustomCookieAuthenticationEvents` 클래스:</span><span class="sxs-lookup"><span data-stu-id="fe2d5-336">Provide a scoped service registration for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="fe2d5-337">에 대 한 재정의 구현 하는 `ValidateAsync` 이벤트 메서드는 다음 서명 사용 하 여 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-337">To implement an override for the `ValidateAsync` event, write a method with the following signature:</span></span>

```csharp
ValidateAsync(CookieValidatePrincipalContext)
```

<span data-ttu-id="fe2d5-338">ASP.NET Core Id는이 검사의 일부로 구현 해당 [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync)합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-338">ASP.NET Core Identity implements this check as part of its [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync).</span></span> <span data-ttu-id="fe2d5-339">예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-339">An example looks like the following:</span></span>

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = 
            context.HttpContext.RequestServices
                .GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="fe2d5-340">쿠키 인증 구성 중 이벤트를 등록 합니다 `Configure` 메서드:</span><span class="sxs-lookup"><span data-stu-id="fe2d5-340">Register the event during cookie authentication configuration in the `Configure` method:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    Events = new CookieAuthenticationEvents
    {
        OnValidatePrincipal = LastChangedValidator.ValidateAsync
    }
});
```

::: moniker-end

<span data-ttu-id="fe2d5-341">사용자의 이름이 업데이트 되는 상황을 가정해 보겠습니다 &mdash; 결정 하는 어떤 방식으로 보안에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-341">Consider a situation in which the user's name is updated &mdash; a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="fe2d5-342">비파괴적인 사용자 보안 주체를 업데이트 하려는 경우 호출 `context.ReplacePrincipal` 설정 합니다 `context.ShouldRenew` 속성을 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-342">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="fe2d5-343">여기서 설명 하는 방식은 모든 요청에 대해 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-343">The approach described here is triggered on every request.</span></span> <span data-ttu-id="fe2d5-344">이 앱에 대 한 큰 성능 저하가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-344">This can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="fe2d5-345">영구 쿠키</span><span class="sxs-lookup"><span data-stu-id="fe2d5-345">Persistent cookies</span></span>

<span data-ttu-id="fe2d5-346">브라우저 세션 간에 유지 하기 위해 쿠키를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-346">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="fe2d5-347">이 지 속성에는 "암호 저장" 확인란 로그인 또는 유사한 메커니즘을 사용 하 여 명시적 사용자 동의 사용 하 여만 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-347">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on login or a similar mechanism.</span></span> 

<span data-ttu-id="fe2d5-348">다음 코드 조각은 id 및 브라우저 클로저를 통해 생존 하는 해당 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-348">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="fe2d5-349">이전에 구성 된 모든 상대 (sliding) 만료 설정이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-349">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="fe2d5-350">쿠키는 브라우저를 닫는 동안 만료 되 면 다시 시작 되 면 브라우저 쿠키를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-350">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="fe2d5-351">합니다 [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) 클래스에 있는 `Microsoft.AspNetCore.Authentication` 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-351">The [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) class resides in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="fe2d5-352">합니다 [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) 클래스에 있는 `Microsoft.AspNetCore.Http.Authentication` 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-352">The [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) class resides in the `Microsoft.AspNetCore.Http.Authentication` namespace.</span></span>

::: moniker-end

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="fe2d5-353">절대 쿠키 만료 기한</span><span class="sxs-lookup"><span data-stu-id="fe2d5-353">Absolute cookie expiration</span></span>

<span data-ttu-id="fe2d5-354">사용 하 여 절대 만료 시간을 설정할 수 있습니다 `ExpiresUtc`합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-354">You can set an absolute expiration time with `ExpiresUtc`.</span></span> <span data-ttu-id="fe2d5-355">영구 쿠키를 만들려면 설정 해야 `IsPersistent`;이 고, 그렇지 쿠키는 세션 기반 수명을 사용 하 여 생성 되 고 하기 전에 만료 될 있습니다 또는 보유 한 후 인증 티켓입니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-355">To create a persistent cookie, you must also set `IsPersistent`; otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="fe2d5-356">때 `ExpiresUtc` 에 설정 된 `SignInAsync`의 값을 재정의 합니다 `ExpireTimeSpan` 옵션의 `CookieAuthenticationOptions`이면 설정.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-356">When `ExpiresUtc` is set on `SignInAsync`, it overrides the value of the `ExpireTimeSpan` option of `CookieAuthenticationOptions`, if set.</span></span>

<span data-ttu-id="fe2d5-357">다음 코드 조각은 id 및 20 분 동안 지속 되는 해당 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-357">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="fe2d5-358">이 이전에 구성 된 모든 상대 (sliding) 만료 설정을 무시 합니다.</span><span class="sxs-lookup"><span data-stu-id="fe2d5-358">This ignores any sliding expiration settings previously configured.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="fe2d5-359">추가 자료</span><span class="sxs-lookup"><span data-stu-id="fe2d5-359">Additional resources</span></span>

* [<span data-ttu-id="fe2d5-360">2.0 인증 변경을 / 마이그레이션 알림</span><span class="sxs-lookup"><span data-stu-id="fe2d5-360">Auth 2.0 Changes / Migration Announcement</span></span>](https://github.com/aspnet/Announcements/issues/262)
* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="fe2d5-361">정책 기반 역할 확인</span><span class="sxs-lookup"><span data-stu-id="fe2d5-361">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
