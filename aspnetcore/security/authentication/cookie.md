---
title: ASP.NET Core Id 없이 쿠키 인증 사용
author: rick-anderson
description: ASP.NET Core Id 없이 쿠키 인증을 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
uid: security/authentication/cookie
ms.openlocfilehash: 62a3d247dade6c83156a8378407d5e3891713fd1
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172112"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="974e8-103">ASP.NET Core Id 없이 쿠키 인증 사용</span><span class="sxs-lookup"><span data-stu-id="974e8-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="974e8-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="974e8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="974e8-105">ASP.NET Core Id는 로그인을 만들고 유지 관리 하기 위한 완전 한 기능을 갖춘 완전 한 인증 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="974e8-106">그러나 ASP.NET Core Id가 없는 쿠키 기반 인증 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="974e8-107">자세한 내용은 <xref:security/authentication/identity>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="974e8-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="974e8-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="974e8-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="974e8-109">샘플 앱의 데모용으로, 가상 사용자 (민 Rodriguez)의 사용자 계정이 앱에 하드 코딩 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="974e8-110">**전자 메일** 주소 `maria.rodriguez@contoso.com` 및 암호를 사용 하 여 사용자를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="974e8-111">사용자는 *Pages/Account/Login. cshtml* 파일의 `AuthenticateUser` 메서드에서 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="974e8-112">실제 예제에서는 사용자가 데이터베이스에 대해 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="974e8-113">Configuration</span><span class="sxs-lookup"><span data-stu-id="974e8-113">Configuration</span></span>

<span data-ttu-id="974e8-114">앱이 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 사용 하지 않는 경우 프로젝트 파일에서 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 패키지에 대 한 패키지 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="974e8-115">`Startup.ConfigureServices` 메서드에서 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 및 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 메서드를 사용 하 여 인증 미들웨어 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="974e8-116">`AddAuthentication`에 전달 된 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>는 앱에 대 한 기본 인증 체계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="974e8-117">`AuthenticationScheme`은 쿠키 인증 인스턴스가 여러 개 있고 [특정 스키마를 사용 하 여 권한을 부여](xref:security/authorization/limitingidentitybyscheme)하려는 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="974e8-118">`AuthenticationScheme`를 [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)로 설정하면 구성표에 "쿠키" 값이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="974e8-119">체계를 구별 하는 모든 문자열 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="974e8-120">앱의 인증 체계가 앱의 쿠키 인증 체계와 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="974e8-121"><xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>에 대 한 쿠키 인증 스키마를 제공 하지 않으면 `CookieAuthenticationDefaults.AuthenticationScheme` ("쿠키")를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="974e8-122">인증 쿠키의 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 속성은 기본적으로 `true`로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="974e8-123">인증 쿠키는 사이트 방문자가 데이터 수집에 동의한 하지 않은 경우에 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="974e8-124">자세한 내용은 <xref:security/gdpr#essential-cookies>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="974e8-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="974e8-125">`Startup.Configure`에서 `UseAuthentication` 및 `UseAuthorization`를 호출 하 여 `HttpContext.User` 속성을 설정 하 고 요청에 대 한 권한 부여 미들웨어를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="974e8-126">`UseEndpoints`를 호출 하기 전에 `UseAuthentication` 및 `UseAuthorization` 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="974e8-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 클래스는 인증 공급자 옵션을 구성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="974e8-128">`Startup.ConfigureServices` 메서드에서 인증에 대 한 서비스 구성에 `CookieAuthenticationOptions`을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="974e8-129">쿠키 정책 미들웨어</span><span class="sxs-lookup"><span data-stu-id="974e8-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="974e8-130">쿠키 [정책 미들웨어](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) 는 쿠키 정책 기능을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="974e8-131">미들웨어를 앱 처리 파이프라인에 추가 하는 작업은 파이프라인에 등록 된 다운스트림 구성 요소에만 영향을 주는&mdash;순서를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="974e8-132">쿠키 정책 미들웨어에 제공 된 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>를 사용 하 여 쿠키를 추가 하거나 삭제할 때 쿠키 처리 및 쿠키 처리 처리기에 대 한 전역 특성을 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="974e8-133">기본 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 값은 OAuth2 인증을 허용 하는 `SameSiteMode.Lax`입니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="974e8-134">`SameSiteMode.Strict`의 동일한 사이트 정책을 엄격 하 게 적용 하려면 `MinimumSameSitePolicy`를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="974e8-135">이 설정은 OAuth2 및 다른 크로스-원본 인증 스키마를 중단 하지만 크로스-원본 요청 처리에 의존 하지 않는 다른 유형의 앱에 대 한 쿠키 보안 수준을 강화 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="974e8-136">`MinimumSameSitePolicy`에 대 한 쿠키 정책 미들웨어 설정은 아래 행렬에 따라 `CookieAuthenticationOptions` 설정의 `Cookie.SameSite` 설정에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="974e8-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="974e8-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="974e8-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="974e8-138">Cookie.SameSite</span></span> | <span data-ttu-id="974e8-139">결과 쿠키. SameSite 설정</span><span class="sxs-lookup"><span data-stu-id="974e8-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="974e8-140">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="974e8-140">SameSiteMode.None</span></span>     | <span data-ttu-id="974e8-141">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="974e8-141">SameSiteMode.None</span></span><br><span data-ttu-id="974e8-142">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="974e8-143">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="974e8-144">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="974e8-144">SameSiteMode.None</span></span><br><span data-ttu-id="974e8-145">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="974e8-146">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="974e8-147">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="974e8-148">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="974e8-148">SameSiteMode.None</span></span><br><span data-ttu-id="974e8-149">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="974e8-150">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="974e8-151">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="974e8-152">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="974e8-153">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="974e8-154">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="974e8-155">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="974e8-155">SameSiteMode.None</span></span><br><span data-ttu-id="974e8-156">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="974e8-157">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="974e8-158">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="974e8-159">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="974e8-160">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="974e8-161">인증 쿠키 만들기</span><span class="sxs-lookup"><span data-stu-id="974e8-161">Create an authentication cookie</span></span>

<span data-ttu-id="974e8-162">사용자 정보를 포함 하는 쿠키를 만들려면 <xref:System.Security.Claims.ClaimsPrincipal>를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="974e8-163">사용자 정보는 serialize 되어 쿠키에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="974e8-164">필요한 <xref:System.Security.Claims.Claim>s를 사용 하 여 <xref:System.Security.Claims.ClaimsIdentity>를 만들고 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>를 호출 하 여 사용자를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="974e8-165">`SignInAsync`는 암호화 된 쿠키를 만들어 현재 응답에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="974e8-166">`AuthenticationScheme` 지정 하지 않으면 기본 체계가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="974e8-167">ASP.NET Core의 [데이터 보호](xref:security/data-protection/using-data-protection) 시스템이 암호화에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="974e8-168">여러 컴퓨터에서 호스트 되는 앱의 경우, 앱 간에 부하를 분산 하거나 웹 팜을 사용 하 여 [데이터 보호를 구성](xref:security/data-protection/configuration/overview) 하 여 동일한 키 링 및 앱 식별자를 사용 하도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="974e8-169">로그아웃</span><span class="sxs-lookup"><span data-stu-id="974e8-169">Sign out</span></span>

<span data-ttu-id="974e8-170">현재 사용자를 로그 아웃 하 고 쿠키를 삭제 하려면 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="974e8-171">`CookieAuthenticationDefaults.AuthenticationScheme` (또는 "쿠키")가 구성표 (예: "ContosoCookie")로 사용 되지 않는 경우 인증 공급자를 구성할 때 사용 되는 체계를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="974e8-172">그렇지 않으면 기본 체계가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="974e8-173">백 엔드 변경 내용에 대응</span><span class="sxs-lookup"><span data-stu-id="974e8-173">React to back-end changes</span></span>

<span data-ttu-id="974e8-174">쿠키가 만들어지면 쿠키는 id의 단일 소스입니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="974e8-175">백 엔드 시스템에서 사용자 계정을 사용할 수 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="974e8-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="974e8-176">앱의 쿠키 인증 시스템이 인증 쿠키에 따라 요청을 계속 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="974e8-177">사용자는 인증 쿠키가 유효한 경우에만 앱에 로그인 상태를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="974e8-178"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 이벤트를 사용 하 여 쿠키 id의 유효성 검사를 가로채 고 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="974e8-179">모든 요청에서 쿠키의 유효성을 검사 하면 해지 된 사용자가 앱에 액세스 하는 위험을 완화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="974e8-180">쿠키 유효성 검사에 대 한 한 가지 방법은 사용자 데이터베이스가 변경 되는 시기를 추적 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="974e8-181">사용자의 쿠키가 발급 된 이후에 데이터베이스가 변경 되지 않은 경우에는 해당 쿠키가 여전히 유효한 경우 사용자를 다시 인증할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="974e8-182">샘플 앱에서 데이터베이스는 `IUserRepository`에서 구현 되 고 `LastChanged` 값을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="974e8-183">데이터베이스에서 사용자를 업데이트 하는 경우 `LastChanged` 값은 현재 시간으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="974e8-184">`LastChanged` 값에 따라 데이터베이스가 변경 될 때 쿠키를 무효화 하려면 데이터베이스의 현재 `LastChanged` 값을 포함 하는 `LastChanged` 클레임을 사용 하 여 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="974e8-185">`ValidatePrincipal` 이벤트에 대 한 재정의를 구현 하려면 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>에서 파생 되는 클래스에 다음 시그니처를 사용 하 여 메서드를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="974e8-186">다음은 `CookieAuthenticationEvents`구현에 대 한 예입니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="974e8-187">`Startup.ConfigureServices` 메서드에서 쿠키 서비스를 등록 하는 동안 events 인스턴스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="974e8-188">`CustomCookieAuthenticationEvents` 클래스에 대해 범위가 지정 된 [서비스 등록](xref:fundamentals/dependency-injection#service-lifetimes) 을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="974e8-189">보안에 영향을 주지 않는 결정&mdash;사용자 이름이 업데이트 되는 상황을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="974e8-190">Destructively를 사용 하지 않는 사용자 계정을 업데이트 하려면 `context.ReplacePrincipal`를 호출 하 고 `context.ShouldRenew` 속성을 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="974e8-191">여기에 설명 된 방법은 모든 요청에서 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="974e8-192">모든 요청에서 모든 사용자에 대 한 인증 쿠키의 유효성을 검사 하면 앱에 대 한 성능 저하가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="974e8-193">영구 쿠키</span><span class="sxs-lookup"><span data-stu-id="974e8-193">Persistent cookies</span></span>

<span data-ttu-id="974e8-194">쿠키를 브라우저 세션 간에 유지 하려고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="974e8-195">이 지 속성은 로그인 또는 유사한 메커니즘에서 "사용자 이름" 확인란을 사용 하 여 명시적인 사용자 동의로만 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="974e8-196">다음 코드 조각에서는 브라우저 클로저를 통해 생존 하는 id 및 해당 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="974e8-197">이전에 구성 된 슬라이딩 만료 설정은 모두 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="974e8-198">브라우저가 닫히는 동안 쿠키가 만료 되 면 브라우저가 다시 시작 되 면 쿠키를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="974e8-199"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="974e8-200">절대 쿠키 만료</span><span class="sxs-lookup"><span data-stu-id="974e8-200">Absolute cookie expiration</span></span>

<span data-ttu-id="974e8-201"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>를 사용 하 여 절대 만료 시간을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="974e8-202">영구 쿠키를 만들려면 `IsPersistent`도 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="974e8-203">그렇지 않으면 쿠키는 세션 기반 수명으로 만들어지고이 쿠키는 보유 하 고 있는 인증 티켓 전후에 만료 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="974e8-204">`ExpiresUtc` 설정 된 경우 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>의 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> 옵션 값 (설정 된 경우)을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="974e8-205">다음 코드 조각에서는 20 분 동안 지속 되는 id 및 해당 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="974e8-206">이렇게 하면 이전에 구성 된 슬라이딩 만료 설정이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-206">This ignores any sliding expiration settings previously configured.</span></span>

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="974e8-207">ASP.NET Core Id는 로그인을 만들고 유지 관리 하기 위한 완전 한 기능을 갖춘 완전 한 인증 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-207">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="974e8-208">그러나 ASP.NET Core Id가 없는 쿠키 기반 인증 인증 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-208">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="974e8-209">자세한 내용은 <xref:security/authentication/identity>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="974e8-209">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="974e8-210">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="974e8-210">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="974e8-211">샘플 앱의 데모용으로, 가상 사용자 (민 Rodriguez)의 사용자 계정이 앱에 하드 코딩 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-211">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="974e8-212">**전자 메일** 주소 `maria.rodriguez@contoso.com` 및 암호를 사용 하 여 사용자를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-212">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="974e8-213">사용자는 *Pages/Account/Login. cshtml* 파일의 `AuthenticateUser` 메서드에서 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-213">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="974e8-214">실제 예제에서는 사용자가 데이터베이스에 대해 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-214">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="974e8-215">Configuration</span><span class="sxs-lookup"><span data-stu-id="974e8-215">Configuration</span></span>

<span data-ttu-id="974e8-216">앱이 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 사용 하지 않는 경우 프로젝트 파일에서 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 패키지에 대 한 패키지 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-216">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="974e8-217">`Startup.ConfigureServices` 메서드에서 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 및 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 메서드를 사용 하 여 인증 미들웨어 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-217">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="974e8-218">`AddAuthentication`에 전달 된 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>는 앱에 대 한 기본 인증 체계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="974e8-219">`AuthenticationScheme`은 쿠키 인증 인스턴스가 여러 개 있고 [특정 스키마를 사용 하 여 권한을 부여](xref:security/authorization/limitingidentitybyscheme)하려는 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-219">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="974e8-220">`AuthenticationScheme`를 [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)로 설정하면 구성표에 "쿠키" 값이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-220">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="974e8-221">체계를 구별 하는 모든 문자열 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-221">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="974e8-222">앱의 인증 체계가 앱의 쿠키 인증 체계와 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-222">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="974e8-223"><xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>에 대 한 쿠키 인증 스키마를 제공 하지 않으면 `CookieAuthenticationDefaults.AuthenticationScheme` ("쿠키")를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-223">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="974e8-224">인증 쿠키의 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 속성은 기본적으로 `true`로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-224">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="974e8-225">인증 쿠키는 사이트 방문자가 데이터 수집에 동의한 하지 않은 경우에 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-225">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="974e8-226">자세한 내용은 <xref:security/gdpr#essential-cookies>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="974e8-226">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="974e8-227">`Startup.Configure` 메서드에서 `UseAuthentication` 메서드를 호출 하 여 `HttpContext.User` 속성을 설정 하는 인증 미들웨어를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-227">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="974e8-228">`UseMvcWithDefaultRoute` 또는 `UseMvc`를 호출 하기 전에 `UseAuthentication` 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-228">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="974e8-229"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 클래스는 인증 공급자 옵션을 구성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-229">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="974e8-230">`Startup.ConfigureServices` 메서드에서 인증에 대 한 서비스 구성에 `CookieAuthenticationOptions`을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-230">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="974e8-231">쿠키 정책 미들웨어</span><span class="sxs-lookup"><span data-stu-id="974e8-231">Cookie Policy Middleware</span></span>

<span data-ttu-id="974e8-232">쿠키 [정책 미들웨어](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) 는 쿠키 정책 기능을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="974e8-233">미들웨어를 앱 처리 파이프라인에 추가 하는 작업은 파이프라인에 등록 된 다운스트림 구성 요소에만 영향을 주는&mdash;순서를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-233">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="974e8-234">쿠키 정책 미들웨어에 제공 된 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>를 사용 하 여 쿠키를 추가 하거나 삭제할 때 쿠키 처리 및 쿠키 처리 처리기에 대 한 전역 특성을 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="974e8-235">기본 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 값은 OAuth2 인증을 허용 하는 `SameSiteMode.Lax`입니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-235">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="974e8-236">`SameSiteMode.Strict`의 동일한 사이트 정책을 엄격 하 게 적용 하려면 `MinimumSameSitePolicy`를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-236">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="974e8-237">이 설정은 OAuth2 및 다른 크로스-원본 인증 스키마를 중단 하지만 크로스-원본 요청 처리에 의존 하지 않는 다른 유형의 앱에 대 한 쿠키 보안 수준을 강화 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-237">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="974e8-238">`MinimumSameSitePolicy`에 대 한 쿠키 정책 미들웨어 설정은 아래 행렬에 따라 `CookieAuthenticationOptions` 설정의 `Cookie.SameSite` 설정에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-238">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="974e8-239">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="974e8-239">MinimumSameSitePolicy</span></span> | <span data-ttu-id="974e8-240">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="974e8-240">Cookie.SameSite</span></span> | <span data-ttu-id="974e8-241">결과 쿠키. SameSite 설정</span><span class="sxs-lookup"><span data-stu-id="974e8-241">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="974e8-242">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="974e8-242">SameSiteMode.None</span></span>     | <span data-ttu-id="974e8-243">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="974e8-243">SameSiteMode.None</span></span><br><span data-ttu-id="974e8-244">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-244">SameSiteMode.Lax</span></span><br><span data-ttu-id="974e8-245">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-245">SameSiteMode.Strict</span></span> | <span data-ttu-id="974e8-246">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="974e8-246">SameSiteMode.None</span></span><br><span data-ttu-id="974e8-247">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="974e8-248">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-248">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="974e8-249">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-249">SameSiteMode.Lax</span></span>      | <span data-ttu-id="974e8-250">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="974e8-250">SameSiteMode.None</span></span><br><span data-ttu-id="974e8-251">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="974e8-252">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-252">SameSiteMode.Strict</span></span> | <span data-ttu-id="974e8-253">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="974e8-254">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="974e8-255">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-255">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="974e8-256">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-256">SameSiteMode.Strict</span></span>   | <span data-ttu-id="974e8-257">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="974e8-257">SameSiteMode.None</span></span><br><span data-ttu-id="974e8-258">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="974e8-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="974e8-259">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-259">SameSiteMode.Strict</span></span> | <span data-ttu-id="974e8-260">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-260">SameSiteMode.Strict</span></span><br><span data-ttu-id="974e8-261">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-261">SameSiteMode.Strict</span></span><br><span data-ttu-id="974e8-262">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="974e8-262">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="974e8-263">인증 쿠키 만들기</span><span class="sxs-lookup"><span data-stu-id="974e8-263">Create an authentication cookie</span></span>

<span data-ttu-id="974e8-264">사용자 정보를 포함 하는 쿠키를 만들려면 <xref:System.Security.Claims.ClaimsPrincipal>를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-264">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="974e8-265">사용자 정보는 serialize 되어 쿠키에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-265">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="974e8-266">필요한 <xref:System.Security.Claims.Claim>s를 사용 하 여 <xref:System.Security.Claims.ClaimsIdentity>를 만들고 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>를 호출 하 여 사용자를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-266">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="974e8-267">`SignInAsync`는 암호화 된 쿠키를 만들어 현재 응답에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-267">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="974e8-268">`AuthenticationScheme` 지정 하지 않으면 기본 체계가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-268">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="974e8-269">ASP.NET Core의 [데이터 보호](xref:security/data-protection/using-data-protection) 시스템이 암호화에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-269">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="974e8-270">여러 컴퓨터에서 호스트 되는 앱의 경우, 앱 간에 부하를 분산 하거나 웹 팜을 사용 하 여 [데이터 보호를 구성](xref:security/data-protection/configuration/overview) 하 여 동일한 키 링 및 앱 식별자를 사용 하도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-270">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="974e8-271">로그아웃</span><span class="sxs-lookup"><span data-stu-id="974e8-271">Sign out</span></span>

<span data-ttu-id="974e8-272">현재 사용자를 로그 아웃 하 고 쿠키를 삭제 하려면 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-272">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="974e8-273">`CookieAuthenticationDefaults.AuthenticationScheme` (또는 "쿠키")가 구성표 (예: "ContosoCookie")로 사용 되지 않는 경우 인증 공급자를 구성할 때 사용 되는 체계를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-273">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="974e8-274">그렇지 않으면 기본 체계가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-274">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="974e8-275">백 엔드 변경 내용에 대응</span><span class="sxs-lookup"><span data-stu-id="974e8-275">React to back-end changes</span></span>

<span data-ttu-id="974e8-276">쿠키가 만들어지면 쿠키는 id의 단일 소스입니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-276">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="974e8-277">백 엔드 시스템에서 사용자 계정을 사용할 수 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="974e8-277">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="974e8-278">앱의 쿠키 인증 시스템이 인증 쿠키에 따라 요청을 계속 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-278">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="974e8-279">사용자는 인증 쿠키가 유효한 경우에만 앱에 로그인 상태를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-279">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="974e8-280"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 이벤트를 사용 하 여 쿠키 id의 유효성 검사를 가로채 고 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-280">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="974e8-281">모든 요청에서 쿠키의 유효성을 검사 하면 해지 된 사용자가 앱에 액세스 하는 위험을 완화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-281">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="974e8-282">쿠키 유효성 검사에 대 한 한 가지 방법은 사용자 데이터베이스가 변경 되는 시기를 추적 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-282">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="974e8-283">사용자의 쿠키가 발급 된 이후에 데이터베이스가 변경 되지 않은 경우에는 해당 쿠키가 여전히 유효한 경우 사용자를 다시 인증할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-283">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="974e8-284">샘플 앱에서 데이터베이스는 `IUserRepository`에서 구현 되 고 `LastChanged` 값을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-284">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="974e8-285">데이터베이스에서 사용자를 업데이트 하는 경우 `LastChanged` 값은 현재 시간으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-285">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="974e8-286">`LastChanged` 값에 따라 데이터베이스가 변경 될 때 쿠키를 무효화 하려면 데이터베이스의 현재 `LastChanged` 값을 포함 하는 `LastChanged` 클레임을 사용 하 여 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-286">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="974e8-287">`ValidatePrincipal` 이벤트에 대 한 재정의를 구현 하려면 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>에서 파생 되는 클래스에 다음 시그니처를 사용 하 여 메서드를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-287">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="974e8-288">다음은 `CookieAuthenticationEvents`구현에 대 한 예입니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-288">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="974e8-289">`Startup.ConfigureServices` 메서드에서 쿠키 서비스를 등록 하는 동안 events 인스턴스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-289">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="974e8-290">`CustomCookieAuthenticationEvents` 클래스에 대해 범위가 지정 된 [서비스 등록](xref:fundamentals/dependency-injection#service-lifetimes) 을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-290">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="974e8-291">보안에 영향을 주지 않는 결정&mdash;사용자 이름이 업데이트 되는 상황을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-291">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="974e8-292">Destructively를 사용 하지 않는 사용자 계정을 업데이트 하려면 `context.ReplacePrincipal`를 호출 하 고 `context.ShouldRenew` 속성을 `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-292">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="974e8-293">여기에 설명 된 방법은 모든 요청에서 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-293">The approach described here is triggered on every request.</span></span> <span data-ttu-id="974e8-294">모든 요청에서 모든 사용자에 대 한 인증 쿠키의 유효성을 검사 하면 앱에 대 한 성능 저하가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-294">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="974e8-295">영구 쿠키</span><span class="sxs-lookup"><span data-stu-id="974e8-295">Persistent cookies</span></span>

<span data-ttu-id="974e8-296">쿠키를 브라우저 세션 간에 유지 하려고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-296">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="974e8-297">이 지 속성은 로그인 또는 유사한 메커니즘에서 "사용자 이름" 확인란을 사용 하 여 명시적인 사용자 동의로만 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-297">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="974e8-298">다음 코드 조각에서는 브라우저 클로저를 통해 생존 하는 id 및 해당 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-298">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="974e8-299">이전에 구성 된 슬라이딩 만료 설정은 모두 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-299">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="974e8-300">브라우저가 닫히는 동안 쿠키가 만료 되 면 브라우저가 다시 시작 되 면 쿠키를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-300">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="974e8-301"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true`로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-301">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="974e8-302">절대 쿠키 만료</span><span class="sxs-lookup"><span data-stu-id="974e8-302">Absolute cookie expiration</span></span>

<span data-ttu-id="974e8-303"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>를 사용 하 여 절대 만료 시간을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-303">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="974e8-304">영구 쿠키를 만들려면 `IsPersistent`도 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-304">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="974e8-305">그렇지 않으면 쿠키는 세션 기반 수명으로 만들어지고이 쿠키는 보유 하 고 있는 인증 티켓 전후에 만료 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-305">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="974e8-306">`ExpiresUtc` 설정 된 경우 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>의 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> 옵션 값 (설정 된 경우)을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-306">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="974e8-307">다음 코드 조각에서는 20 분 동안 지속 되는 id 및 해당 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-307">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="974e8-308">이렇게 하면 이전에 구성 된 슬라이딩 만료 설정이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="974e8-308">This ignores any sliding expiration settings previously configured.</span></span>

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

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="974e8-309">추가 자료</span><span class="sxs-lookup"><span data-stu-id="974e8-309">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="974e8-310">정책 기반 역할 검사</span><span class="sxs-lookup"><span data-stu-id="974e8-310">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
