---
title: ASP.NET Core에서 SameSite 쿠키 작업
author: rick-anderson
description: 를 사용 하 여 ASP.NET Core 쿠키를 SameSite 하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: security/samesite
ms.openlocfilehash: 988069a66cc4772583444303948bff2e47ff4310
ms.sourcegitcommit: 169ea5116de729c803685725d96450a270bc55b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74733988"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="412b8-103">ASP.NET Core에서 SameSite 쿠키 작업</span><span class="sxs-lookup"><span data-stu-id="412b8-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="412b8-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="412b8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="412b8-105">[SameSite](https://tools.ietf.org/html/draft-west-first-party-cookies-07) 은 csrf (교차 사이트 요청 위조) 공격에 대 한 보호를 제공 하도록 설계 된 [IETF](https://ietf.org/about/) 초안입니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-105">[SameSite](https://tools.ietf.org/html/draft-west-first-party-cookies-07) is an [IETF](https://ietf.org/about/) draft designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="412b8-106">[SameSite 2019 초안](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00):</span><span class="sxs-lookup"><span data-stu-id="412b8-106">The [SameSite 2019 draft](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00):</span></span>

* <span data-ttu-id="412b8-107">에서는 기본적으로 쿠키를 `SameSite=Lax`로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-107">Treats cookies as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="412b8-108">사이트 간 배달을 가능 하 게 하기 위해 `SameSite=None`를 명시적으로 어설션하는 쿠키는 `Secure`으로 표시 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-108">States cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span>

<span data-ttu-id="412b8-109">`Lax` 대부분의 앱 쿠키에 대해 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-109">`Lax` works for most app cookies.</span></span> <span data-ttu-id="412b8-110">Oidc ( [Openid connect Connect](https://openid.net/connect/) )와 같은 일부 형태의 인증 및 [ws-federation](https://auth0.com/docs/protocols/ws-fed) 은 게시 기반 리디렉션에 대해 기본적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-110">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="412b8-111">사후 기반 리디렉션은 SameSite 브라우저 보호를 트리거하고 이러한 구성 요소에 대해 SameSite을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-111">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="412b8-112">대부분의 [OAuth](https://oauth.net/) 로그인은 요청 흐름의 차이로 인해 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-112">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="412b8-113">`None` 매개 변수를 사용 하면 이전 2016 초안 표준 (예: iOS 12)을 구현한 클라이언트에서 호환성 문제가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-113">The `None` parameter causes compatibility problems with clients that implemented the prior 2016 draft standard (for example, iOS 12).</span></span> <span data-ttu-id="412b8-114">이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="412b8-114">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="412b8-115">쿠키를 내보내는 각 ASP.NET Core 구성 요소는 SameSite가 적절 한지 결정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-115">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="api-usage-with-samesite"></a><span data-ttu-id="412b8-116">SameSite를 사용 하는 API 사용</span><span class="sxs-lookup"><span data-stu-id="412b8-116">API usage with SameSite</span></span>

<span data-ttu-id="412b8-117">SameSite. 기본값을 [추가](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) 하면 `Unspecified`에 추가 되 고, 쿠키에 추가 된 특성이 없으며, 클라이언트는 기본 동작을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-117">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="412b8-118">다음 코드는 `SameSiteMode.Lax`쿠키 SameSite 값을 변경 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-118">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="412b8-119">쿠키를 내보내는 모든 ASP.NET Core 구성 요소는 해당 시나리오에 적합 한 설정을 사용 하 여 이전 기본값을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-119">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="412b8-120">재정의 된 이전 기본값은 변경 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-120">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="412b8-121">구성 요소</span><span class="sxs-lookup"><span data-stu-id="412b8-121">Component</span></span> | <span data-ttu-id="412b8-122">쿠키가</span><span class="sxs-lookup"><span data-stu-id="412b8-122">cookie</span></span> | <span data-ttu-id="412b8-123">기본</span><span class="sxs-lookup"><span data-stu-id="412b8-123">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="412b8-124">SessionOptions</span><span class="sxs-lookup"><span data-stu-id="412b8-124">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="412b8-125">CookieTempDataProviderOptions</span><span class="sxs-lookup"><span data-stu-id="412b8-125">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="412b8-126">AntiforgeryOptions</span><span class="sxs-lookup"><span data-stu-id="412b8-126">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="412b8-127">쿠키 인증</span><span class="sxs-lookup"><span data-stu-id="412b8-127">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="412b8-128">은 cookieauthenticationoptions.authenticationtype</span><span class="sxs-lookup"><span data-stu-id="412b8-128">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="412b8-129">TwitterOptions. StateCookie</span><span class="sxs-lookup"><span data-stu-id="412b8-129">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="412b8-130"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [Remoteauthenticationoptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="412b8-130"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="412b8-131">OpenIdConnectOptions. NonceCookie</span><span class="sxs-lookup"><span data-stu-id="412b8-131">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="412b8-132">HttpContext. 쿠키. 추가</span><span class="sxs-lookup"><span data-stu-id="412b8-132">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="412b8-133">ASP.NET Core 3.1 이상에서는 다음 SameSite 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-133">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="412b8-134">`SameSiteMode.None`의 동작을 다시 정의 하 여 내보냅니다 `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="412b8-134">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="412b8-135">`SameSiteMode.Unspecified` 새 값을 추가 하 여 SameSite 특성을 생략 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-135">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="412b8-136">모든 쿠키 Api의 기본값은 `Unspecified`입니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-136">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="412b8-137">쿠키를 사용 하는 일부 구성 요소는 해당 시나리오와 관련 된 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-137">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="412b8-138">예는 위의 표를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="412b8-138">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="412b8-139">ASP.NET Core 3.0 이상에서 SameSite 기본값은 일관 되지 않은 클라이언트 기본값과 충돌 하지 않도록 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-139">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="412b8-140">다음 Api는 이러한 쿠키에 대 한 SameSite 특성을 내보내지 않도록 기본값을 `SameSiteMode.Lax `에서 `-1`로 변경 했습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-140">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="412b8-141"><xref:Microsoft.AspNetCore.Http.CookieOptions>는 Httpcontext.current와 함께 사용 [됩니다](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) .</span><span class="sxs-lookup"><span data-stu-id="412b8-141"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="412b8-142">`CookieOptions`의 팩터리에서 사용 되는 <xref:Microsoft.AspNetCore.Http.CookieBuilder></span><span class="sxs-lookup"><span data-stu-id="412b8-142"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="412b8-143">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="412b8-143">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="412b8-144">기록 및 변경 내용</span><span class="sxs-lookup"><span data-stu-id="412b8-144">History and changes</span></span>

<span data-ttu-id="412b8-145">SameSite 지원은 [2016 초안 표준을](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)사용 하 여 2.0의 ASP.NET Core에서 처음 구현 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-145">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="412b8-146">2016 표준은 옵트인 (opt in) 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-146">The 2016 standard was opt-in.</span></span> <span data-ttu-id="412b8-147">기본적으로 여러 쿠키를 `Lax`로 설정 하 여 옵트인 (opt in)을 ASP.NET Core 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-147">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="412b8-148">인증에 몇 가지 [문제가 발생](https://github.com/aspnet/Announcements/issues/318) 한 후에는 대부분의 SameSite 사용이 [사용 되지 않도록 설정](https://github.com/aspnet/Announcements/issues/348)되었습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-148">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="412b8-149">11 월 2019에 패치를 실행 하 여 2016 표준에서 2019 standard로 업데이트 했습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-149">Patches were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="412b8-150">[SameSite 사양의 2019 초안](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="412b8-150">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="412b8-151">는 이전 버전과 호환 **되지 않습니다** . 2016 초안.</span><span class="sxs-lookup"><span data-stu-id="412b8-151">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="412b8-152">자세한 내용은이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="412b8-152">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="412b8-153">기본적으로 `SameSite=Lax`로 처리 되는 쿠키를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-153">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="412b8-154">교차 사이트 배달을 사용 하도록 설정 하기 위해 `SameSite=None`를 명시적으로 어설션하는 쿠키를 `Secure`으로 표시 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-154">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="412b8-155">`None`은 옵트아웃 (opt out) 할 새 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-155">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="412b8-156">는 ASP.NET Core 2.1, 2.2 및 3.0에 대해 발급 된 패치에 의해 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-156">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="412b8-157">ASP.NET Core 3.1에는 추가 SameSite 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-157">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="412b8-158">는 기본적으로 [2 월 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)에 [Chrome](https://chromestatus.com/feature/5088147346030592) 에서 사용 하도록 예약 됩니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-158">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="412b8-159">브라우저에서 2019의이 표준으로 이동 하기 시작 했습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-159">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="412b8-160">2016 SameSite draft standard에서 2019 초안 표준으로 변경 되어 영향을 받는 Api</span><span class="sxs-lookup"><span data-stu-id="412b8-160">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="412b8-161">SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="412b8-161">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="412b8-162">CookieOptions. SameSite</span><span class="sxs-lookup"><span data-stu-id="412b8-162">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="412b8-163">CookieBuilder. SameSite</span><span class="sxs-lookup"><span data-stu-id="412b8-163">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="412b8-164">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="412b8-164">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="412b8-165">이전 브라우저 지원</span><span class="sxs-lookup"><span data-stu-id="412b8-165">Supporting older browsers</span></span>

<span data-ttu-id="412b8-166">2016 SameSite 표준에서는 알 수 없는 값을 `SameSite=Strict` 값으로 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-166">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="412b8-167">2016 SameSite 표준을 지 원하는 이전 브라우저에서 액세스 한 앱은 `None`값으로 SameSite 속성을 가져오면 중단 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-167">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="412b8-168">웹 앱은 이전 브라우저를 지원 하려는 경우 브라우저 검색을 구현 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-168">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="412b8-169">사용자 에이전트 값이 매우 휘발성 이며 자주 변경 되기 때문에 ASP.NET Core 브라우저 검색을 구현 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-169">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="412b8-170"><xref:Microsoft.AspNetCore.CookiePolicy> 확장 지점은 사용자 에이전트 특정 논리를 연결 하도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-170">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="412b8-171">`Startup.Configure`에서 *<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 또는 쿠키* 를 작성 하는 메서드를 호출 하기 전에 <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>를 호출 하는 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-171">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="412b8-172">`Startup.ConfigureServices`에서 다음과 유사한 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-172">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="412b8-173">위의 샘플에서 `MyUserAgentDetectionLib.DisallowsSameSiteNone` 사용자 에이전트가 SameSite `None`를 지원 하지 않는지 검색 하는 사용자 제공 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-173">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="412b8-174">다음 코드는 샘플 `DisallowsSameSiteNone` 메서드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-174">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="412b8-175">다음 코드는 데모용 으로만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-175">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="412b8-176">완료 되지 않은 것으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-176">It should not be considered complete.</span></span>
> * <span data-ttu-id="412b8-177">유지 관리 되거나 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-177">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="412b8-178">SameSite 문제에 대 한 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="412b8-178">Test apps for SameSite problems</span></span>

<span data-ttu-id="412b8-179">타사 로그인을 통해와 같은 원격 사이트와 상호 작용 하는 앱은 다음 작업을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-179">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="412b8-180">여러 브라우저에서 상호 작용을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-180">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="412b8-181">이 문서에서 설명 [하는 CookiePolicy 브라우저 검색 및 완화](#sob) 를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-181">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="412b8-182">새 SameSite 동작을 옵트인 (opt in) 할 수 있는 클라이언트 버전을 사용 하 여 웹 앱을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-182">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="412b8-183">Chrome, Firefox 및 Chromium Edge 모두에는 테스트에 사용할 수 있는 새로운 옵트인 기능 플래그가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-183">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="412b8-184">앱이 SameSite 패치를 적용 한 후 이전 클라이언트 버전, 특히 Safari를 사용 하 여 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-184">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="412b8-185">자세한 내용은이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="412b8-185">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="412b8-186">Chrome으로 테스트</span><span class="sxs-lookup"><span data-stu-id="412b8-186">Test with Chrome</span></span>

<span data-ttu-id="412b8-187">Chrome 78 +는 일시적인 완화를 제공 하기 때문에 잘못 된 결과를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-187">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="412b8-188">Chrome 78 + 임시 완화를 사용 하면 쿠키가 2 분 이내에 이전 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-188">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="412b8-189">적절 한 테스트 플래그가 설정 된 Chrome 76 또는 77은 보다 정확한 결과를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-189">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="412b8-190">새 SameSite 동작을 테스트 하려면 `chrome://flags/#same-site-by-default-cookies` **설정**으로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-190">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="412b8-191">이전 버전의 Chrome (75 및 아래)은 새 `None` 설정으로 인해 실패 하는 것으로 보고 됩니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-191">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="412b8-192">이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="412b8-192">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="412b8-193">Google은 이전 chrome 버전을 사용할 수 없도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-193">Google does not make older chrome versions available.</span></span> <span data-ttu-id="412b8-194">[Chromium 다운로드](https://www.chromium.org/getting-involved/download-chromium) 의 지침에 따라 이전 버전의 Chrome을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-194">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="412b8-195">이전 버전의 chrome을 검색 하 여 제공 된 링크에서 Chrome을 다운로드 **하지** 마세요.</span><span class="sxs-lookup"><span data-stu-id="412b8-195">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="412b8-196">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="412b8-196">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="412b8-197">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="412b8-197">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

### <a name="test-with-safari"></a><span data-ttu-id="412b8-198">Safari를 사용 하 여 테스트</span><span class="sxs-lookup"><span data-stu-id="412b8-198">Test with Safari</span></span>

<span data-ttu-id="412b8-199">Safari 12는 이전 초안을 엄격 하 게 구현 했으며 새 `None` 값이 쿠키에 있는 경우 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-199">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="412b8-200">이 문서에서 [이전 브라우저를 지 원하는](#sob) 브라우저 검색 코드를 통해 `None`를 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-200">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="412b8-201">MSAL, ADAL 또는 사용 중인 라이브러리를 사용 하 여 Safari 12, Safari 13 및 WebKit 기반 OS 스타일 로그인을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-201">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="412b8-202">문제는 기본 OS 버전에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-202">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="412b8-203">OSX Mojave (10.14) 및 iOS 12는 새로운 SameSite 동작의 호환성 문제를 해결 하는 것으로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-203">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="412b8-204">OS를 OSX Catalina.properties (10.15) 또는 iOS 13로 업그레이드 하면 문제가 해결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-204">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="412b8-205">Safari에는 현재 새 사양 동작 테스트를 위한 옵트인 플래그가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-205">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="412b8-206">Firefox로 테스트</span><span class="sxs-lookup"><span data-stu-id="412b8-206">Test with Firefox</span></span>

<span data-ttu-id="412b8-207">새 표준에 대 한 Firefox 지원은 `network.cookie.sameSite.laxByDefault`기능 플래그를 사용 하 여 `about:config` 페이지에서 옵트인 하 여 버전 68 이상에서 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-207">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="412b8-208">이전 버전의 Firefox와의 호환성 문제에 대 한 보고서가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-208">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="412b8-209">Edge 브라우저를 사용 하 여 테스트</span><span class="sxs-lookup"><span data-stu-id="412b8-209">Test with Edge browser</span></span>

<span data-ttu-id="412b8-210">Edge는 이전 SameSite 표준을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-210">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="412b8-211">Edge 버전 44에는 새로운 표준과의 알려진 호환성 문제가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-211">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="412b8-212">Edge를 사용 하 여 테스트 (Chromium)</span><span class="sxs-lookup"><span data-stu-id="412b8-212">Test with Edge (Chromium)</span></span>

<span data-ttu-id="412b8-213">SameSite 플래그는 `edge://flags/#same-site-by-default-cookies` 페이지에 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-213">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="412b8-214">Edge Chromium에서 호환성 문제가 검색 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-214">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="412b8-215">전자로 테스트</span><span class="sxs-lookup"><span data-stu-id="412b8-215">Test with Electron</span></span>

<span data-ttu-id="412b8-216">전자의 버전에는 이전 버전의 Chromium가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-216">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="412b8-217">예를 들어 팀에서 사용 하는 전자의 버전은 Chromium 66 이며,이는 이전 동작을 보여 주는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-217">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="412b8-218">제품에서 사용 하는 전자 제품 버전으로 고유한 호환성 테스트를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="412b8-218">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="412b8-219">다음 섹션에서 [이전 브라우저 지원](#sob) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="412b8-219">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="412b8-220">추가 자료</span><span class="sxs-lookup"><span data-stu-id="412b8-220">Additional resources</span></span>

* [<span data-ttu-id="412b8-221">Chromium 블로그: 개발자: 새 SameSite를 사용할 준비가 되었습니다. 보안 쿠키 설정</span><span class="sxs-lookup"><span data-stu-id="412b8-221">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="412b8-222">SameSite 쿠키 설명</span><span class="sxs-lookup"><span data-stu-id="412b8-222">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
