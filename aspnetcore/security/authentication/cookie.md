---
title: ASP.NET Core Id 없이 쿠키 인증 사용
author: rick-anderson
description: ASP.NET Core Id 없이 쿠키 인증을 사용 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/07/2019
uid: security/authentication/cookie
ms.openlocfilehash: bbba2e77f806e1ed30bb734763cdbaedc1471d62
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622739"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="c3c7e-103">ASP.NET Core Id 없이 쿠키 인증 사용</span><span class="sxs-lookup"><span data-stu-id="c3c7e-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="c3c7e-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c3c7e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c3c7e-105">ASP.NET Core Id는 만들기 및 로그인을 유지 관리에 대 한 완전 한 완전 한 인증 공급자.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="c3c7e-106">그러나 ASP.NET Core Id 없이 쿠키 기반 인증 인증 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-106">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="c3c7e-107">자세한 내용은 <xref:security/authentication/identity>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="c3c7e-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c3c7e-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c3c7e-109">샘플 앱에서 데모를 위해 Maria Rodriguez 가상 사용자의 사용자 계정을 응용 프로그램에 하드 코딩 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="c3c7e-110">사용 합니다 **전자 메일** 사용자 이름 `maria.rodriguez@contoso.com` 및 사용자를 로그인 할 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-110">Use the **Email** user name `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="c3c7e-111">사용자가 인증을 `AuthenticateUser` 의 메서드를 *Pages/Account/Login.cshtml.cs* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="c3c7e-112">실제 예제에서는 데이터베이스에 대해 사용자를 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="c3c7e-113">구성</span><span class="sxs-lookup"><span data-stu-id="c3c7e-113">Configuration</span></span>

<span data-ttu-id="c3c7e-114">앱을 사용 하지 않는 경우는 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app), 프로젝트 파일의 패키지 참조를 만듭니다 합니다 [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="c3c7e-115">에 `Startup.ConfigureServices` 메서드를 인증 미들웨어의 서비스를 만드는 합니다 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 및 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 메서드:</span><span class="sxs-lookup"><span data-stu-id="c3c7e-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="c3c7e-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 전달할 `AddAuthentication` 앱에 대 한 기본 인증 체계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="c3c7e-117">`AuthenticationScheme` 쿠키 인증의 인스턴스가 여러 개 있고 하려는 경우에 유용 [특정 구성표로 권한 부여](xref:security/authorization/limitingidentitybyscheme)합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="c3c7e-118">설정 합니다 `AuthenticationScheme` 하 [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) 체계를 "쿠키"의 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="c3c7e-119">스키마를 구분 하는 임의의 문자열 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="c3c7e-120">앱의 인증 체계는 앱의 쿠키 인증 체계와 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="c3c7e-121">쿠키 인증 체계에 제공 되지 않는 경우 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>를 사용 하 여 `CookieAuthenticationDefaults.AuthenticationScheme` ("쿠키").</span><span class="sxs-lookup"><span data-stu-id="c3c7e-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="c3c7e-122">인증 쿠키가 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 속성이 `true` 기본적으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="c3c7e-123">인증 쿠키는 사이트 방문자가 데이터 수집에 동의 하지 않은 경우에 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="c3c7e-124">자세한 내용은 <xref:security/gdpr#essential-cookies>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="c3c7e-125">에 `Startup.Configure` 메서드를 호출 합니다 `UseAuthentication` 설정 하는 인증 미들웨어를 호출 하는 방법을 `HttpContext.User` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-125">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="c3c7e-126">호출 된 `UseAuthentication` 메서드를 호출 하기 전에 `UseMvcWithDefaultRoute` 또는 `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="c3c7e-126">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="c3c7e-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 클래스 인증 공급자 옵션을 구성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="c3c7e-128">설정할 `CookieAuthenticationOptions` 에서 인증을 위해 서비스 구성에는 `Startup.ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="c3c7e-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="c3c7e-129">쿠키 정책 미들웨어</span><span class="sxs-lookup"><span data-stu-id="c3c7e-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="c3c7e-130">[쿠키 정책 미들웨어](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) 쿠키 정책 기능을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="c3c7e-131">앱 처리 파이프라인에 미들웨어를 추가 합니다. 순서가 중요 한&mdash;파이프라인에 등록 하는 다운스트림 구성 요소에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="c3c7e-132">사용 하 여 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> 쿠키를 추가 하거나 삭제할 때 쿠키 처리 처리기에 쿠키 처리 및 후크의 전역 특성을 제어 하는 쿠키 정책 미들웨어를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="c3c7e-133">기본값 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 값은 `SameSiteMode.Lax` OAuth2 인증을 허용 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="c3c7e-134">동일한 사이트 정책을 엄격 하 게 적용할 `SameSiteMode.Strict`설정의 `MinimumSameSitePolicy`합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="c3c7e-135">이 설정은 OAuth2 및 다른 크로스-원본 인증 체계를 중단 하지만 다른 유형의 크로스-원본 요청 처리에 의존 하지 않는 앱에 대 한 쿠키 보안 수준을 승격 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="c3c7e-136">에 대 한 쿠키 정책 미들웨어 설정을 `MinimumSameSitePolicy` 의 설정에 영향을 줄 수 있습니다 `Cookie.SameSite` 에서 `CookieAuthenticationOptions` 아래 매트릭스에 따라 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="c3c7e-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="c3c7e-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="c3c7e-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="c3c7e-138">Cookie.SameSite</span></span> | <span data-ttu-id="c3c7e-139">결과 Cookie.SameSite 설정</span><span class="sxs-lookup"><span data-stu-id="c3c7e-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="c3c7e-140">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="c3c7e-140">SameSiteMode.None</span></span>     | <span data-ttu-id="c3c7e-141">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="c3c7e-141">SameSiteMode.None</span></span><br><span data-ttu-id="c3c7e-142">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c3c7e-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="c3c7e-143">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c3c7e-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="c3c7e-144">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="c3c7e-144">SameSiteMode.None</span></span><br><span data-ttu-id="c3c7e-145">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c3c7e-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="c3c7e-146">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c3c7e-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="c3c7e-147">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c3c7e-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="c3c7e-148">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="c3c7e-148">SameSiteMode.None</span></span><br><span data-ttu-id="c3c7e-149">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c3c7e-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="c3c7e-150">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c3c7e-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="c3c7e-151">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c3c7e-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="c3c7e-152">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c3c7e-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="c3c7e-153">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c3c7e-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="c3c7e-154">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c3c7e-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="c3c7e-155">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="c3c7e-155">SameSiteMode.None</span></span><br><span data-ttu-id="c3c7e-156">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c3c7e-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="c3c7e-157">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c3c7e-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="c3c7e-158">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c3c7e-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="c3c7e-159">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c3c7e-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="c3c7e-160">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c3c7e-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="c3c7e-161">인증 쿠키를 만드는</span><span class="sxs-lookup"><span data-stu-id="c3c7e-161">Create an authentication cookie</span></span>

<span data-ttu-id="c3c7e-162">사용자 정보를 보관 하는 쿠키를 만들려면 생성 된 <xref:System.Security.Claims.ClaimsPrincipal>합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="c3c7e-163">사용자 정보를 직렬화 되며 쿠키에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="c3c7e-164">만들기는 <xref:System.Security.Claims.ClaimsIdentity> 필요한를 사용 하 여 <xref:System.Security.Claims.Claim>s 및 호출 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 사용자를 로그인 하려면:</span><span class="sxs-lookup"><span data-stu-id="c3c7e-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="c3c7e-165">`SignInAsync` 암호화 된 쿠키를 만들고 현재 응답에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="c3c7e-166">경우 `AuthenticationScheme` 지정 하지 않으면 기본 구성표가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="c3c7e-167">ASP.NET Core [데이터 보호](xref:security/data-protection/using-data-protection) 암호화에 대 한 시스템을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="c3c7e-168">여러 컴퓨터에서 호스트 되는 앱에 대 한 앱 간에 분산 또는 웹 팜에서 사용 하 여 로드할 [데이터 보호를 구성](xref:security/data-protection/configuration/overview) 동일한 키 링 및 앱 식별자를 사용 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="c3c7e-169">로그아웃</span><span class="sxs-lookup"><span data-stu-id="c3c7e-169">Sign out</span></span>

<span data-ttu-id="c3c7e-170">현재 사용자 로그 아웃을 해당 쿠키를 삭제 하려면 호출 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="c3c7e-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="c3c7e-171">경우 `CookieAuthenticationDefaults.AuthenticationScheme` (또는 "쿠키") 체계 (예: "ContosoCookie"), 인증 공급자를 구성할 때 사용 하는 체계를 제공 하는 대로 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="c3c7e-172">그렇지 않은 경우 기본 스키마가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="c3c7e-173">백 엔드 변경에 대응</span><span class="sxs-lookup"><span data-stu-id="c3c7e-173">React to back-end changes</span></span>

<span data-ttu-id="c3c7e-174">쿠키 만들어지면 쿠키는 id의 단일 원본.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="c3c7e-175">백 엔드 시스템에서 사용자 계정을 사용 하지 않도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="c3c7e-176">앱의 쿠키 인증 시스템 계속 인증 쿠키를 기반으로 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="c3c7e-177">사용자 인증 쿠키의 유효으로 앱에 서명 된 상태로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="c3c7e-178"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 이벤트를 가로채 고 쿠키 id의 유효성 검사 재정의를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="c3c7e-179">요청 마다 쿠키 유효성을 검사 하는 앱에 액세스 하는 해지 된 사용자의 위험을 완화 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="c3c7e-180">쿠키 유효성 검사는 한 가지 방법을 기반으로 사용자 데이터베이스 변경 시의 추적 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="c3c7e-181">데이터베이스 사용자의 쿠키가 발행 된 이후 변경 되지 않은 경우 해당 쿠키가 여전히 유효한 경우 사용자를 다시 인증할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="c3c7e-182">샘플 앱을 데이터베이스에서 구현 됩니다 `IUserRepository` 가져와 `LastChanged` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="c3c7e-183">사용자 데이터베이스에서 업데이트 되 면는 `LastChanged` 값이 현재 시간으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="c3c7e-184">데이터베이스 변경 내용을 기반으로 하는 경우 쿠키를 무효화 하기 위해 합니다 `LastChanged` 값을 사용 하 여 쿠키를 만들기는 `LastChanged` 현재 포함 된 클레임 `LastChanged` 데이터베이스에서 값:</span><span class="sxs-lookup"><span data-stu-id="c3c7e-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="c3c7e-185">에 대 한 재정의 구현 하는 `ValidatePrincipal` 이벤트에서 파생 된 클래스에서 다음 서명으로 메서드가 작성 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="c3c7e-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="c3c7e-186">다음은 예제 구현의 `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="c3c7e-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="c3c7e-187">쿠키 service 등록 하는 동안 이벤트 인스턴스를 등록 합니다 `Startup.ConfigureServices` 메서드.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c3c7e-188">제공 된 [서비스 등록 범위](xref:fundamentals/dependency-injection#service-lifetimes) 에 대 한 프로그램 `CustomCookieAuthenticationEvents` 클래스:</span><span class="sxs-lookup"><span data-stu-id="c3c7e-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="c3c7e-189">사용자의 이름이 업데이트 되는 상황을 가정해 보겠습니다&mdash;결정 하는 어떤 방식으로 보안에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="c3c7e-190">비파괴적인 사용자 보안 주체를 업데이트 하려는 경우 호출 `context.ReplacePrincipal` 설정 합니다 `context.ShouldRenew` 속성을 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="c3c7e-191">여기서 설명 하는 방식은 모든 요청에 대해 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="c3c7e-192">모든 요청에 있는 모든 사용자에 대 한 인증 쿠키 유효성을 검사 하는 앱에 대 한 큰 성능 저하가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="c3c7e-193">영구 쿠키</span><span class="sxs-lookup"><span data-stu-id="c3c7e-193">Persistent cookies</span></span>

<span data-ttu-id="c3c7e-194">브라우저 세션 간에 유지 하기 위해 쿠키를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="c3c7e-195">이 지 속성에는 "암호 저장" 확인란 로그인 또는 유사한 메커니즘을 사용 하 여 명시적 사용자 동의 사용 하 여만 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="c3c7e-196">다음 코드 조각은 id 및 브라우저 클로저를 통해 생존 하는 해당 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="c3c7e-197">이전에 구성 된 모든 상대 (sliding) 만료 설정이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="c3c7e-198">쿠키는 브라우저를 닫는 동안 만료 되 면 다시 시작 되 면 브라우저 쿠키를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="c3c7e-199">설정할 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> 하 `true` 에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="c3c7e-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="c3c7e-200">절대 쿠키 만료 기한</span><span class="sxs-lookup"><span data-stu-id="c3c7e-200">Absolute cookie expiration</span></span>

<span data-ttu-id="c3c7e-201">절대 만료 시간을 사용 하 여 설정할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="c3c7e-202">영구 쿠키를 만들려면 `IsPersistent` 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="c3c7e-203">이 고, 그렇지 쿠키는 세션 기반 수명을 사용 하 여 생성 되 고 앞 이나 뒤 보유 하는 인증 티켓이 만료 될 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="c3c7e-204">때 `ExpiresUtc` 설정의 값을 재정의 합니다 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> 옵션을 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>경우 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="c3c7e-205">다음 코드 조각은 id 및 20 분 동안 지속 되는 해당 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="c3c7e-206">이 이전에 구성 된 모든 상대 (sliding) 만료 설정을 무시 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3c7e-206">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

## <a name="additional-resources"></a><span data-ttu-id="c3c7e-207">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c3c7e-207">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="c3c7e-208">정책 기반 역할 확인</span><span class="sxs-lookup"><span data-stu-id="c3c7e-208">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
