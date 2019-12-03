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
# <a name="work-with-samesite-cookies-in-aspnet-core"></a>ASP.NET Core에서 SameSite 쿠키 작업

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[SameSite](https://tools.ietf.org/html/draft-west-first-party-cookies-07) 은 csrf (교차 사이트 요청 위조) 공격에 대 한 보호를 제공 하도록 설계 된 [IETF](https://ietf.org/about/) 초안입니다. [SameSite 2019 초안](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00):

* 에서는 기본적으로 쿠키를 `SameSite=Lax`로 처리 합니다.
* 사이트 간 배달을 가능 하 게 하기 위해 `SameSite=None`를 명시적으로 어설션하는 쿠키는 `Secure`으로 표시 되어야 합니다.

`Lax` 대부분의 앱 쿠키에 대해 작동 합니다. Oidc ( [Openid connect Connect](https://openid.net/connect/) )와 같은 일부 형태의 인증 및 [ws-federation](https://auth0.com/docs/protocols/ws-fed) 은 게시 기반 리디렉션에 대해 기본적으로 사용 됩니다. 사후 기반 리디렉션은 SameSite 브라우저 보호를 트리거하고 이러한 구성 요소에 대해 SameSite을 사용할 수 없습니다. 대부분의 [OAuth](https://oauth.net/) 로그인은 요청 흐름의 차이로 인해 영향을 받지 않습니다.

`None` 매개 변수를 사용 하면 이전 2016 초안 표준 (예: iOS 12)을 구현한 클라이언트에서 호환성 문제가 발생 합니다. 이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.

쿠키를 내보내는 각 ASP.NET Core 구성 요소는 SameSite가 적절 한지 결정 해야 합니다.

## <a name="api-usage-with-samesite"></a>SameSite를 사용 하는 API 사용

SameSite. 기본값을 [추가](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) 하면 `Unspecified`에 추가 되 고, 쿠키에 추가 된 특성이 없으며, 클라이언트는 기본 동작을 사용 합니다. 다음 코드는 `SameSiteMode.Lax`쿠키 SameSite 값을 변경 하는 방법을 보여 줍니다.

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

쿠키를 내보내는 모든 ASP.NET Core 구성 요소는 해당 시나리오에 적합 한 설정을 사용 하 여 이전 기본값을 재정의 합니다. 재정의 된 이전 기본값은 변경 되지 않았습니다.

| 구성 요소 | 쿠키가 | 기본 |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [SessionOptions](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [CookieTempDataProviderOptions](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [AntiforgeryOptions](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [쿠키 인증](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [은 cookieauthenticationoptions.authenticationtype](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [TwitterOptions. StateCookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [Remoteauthenticationoptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None` |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [OpenIdConnectOptions. NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [HttpContext. 쿠키. 추가](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

ASP.NET Core 3.1 이상에서는 다음 SameSite 지원 합니다.

* `SameSiteMode.None`의 동작을 다시 정의 하 여 내보냅니다 `SameSite=None`
* `SameSiteMode.Unspecified` 새 값을 추가 하 여 SameSite 특성을 생략 합니다.
* 모든 쿠키 Api의 기본값은 `Unspecified`입니다. 쿠키를 사용 하는 일부 구성 요소는 해당 시나리오와 관련 된 값을 설정 합니다. 예는 위의 표를 참조 하세요.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 3.0 이상에서 SameSite 기본값은 일관 되지 않은 클라이언트 기본값과 충돌 하지 않도록 변경 되었습니다. 다음 Api는 이러한 쿠키에 대 한 SameSite 특성을 내보내지 않도록 기본값을 `SameSiteMode.Lax `에서 `-1`로 변경 했습니다.

* <xref:Microsoft.AspNetCore.Http.CookieOptions>는 Httpcontext.current와 함께 사용 [됩니다](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) .
* `CookieOptions`의 팩터리에서 사용 되는 <xref:Microsoft.AspNetCore.Http.CookieBuilder>
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a>기록 및 변경 내용

SameSite 지원은 [2016 초안 표준을](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)사용 하 여 2.0의 ASP.NET Core에서 처음 구현 되었습니다. 2016 표준은 옵트인 (opt in) 되었습니다. 기본적으로 여러 쿠키를 `Lax`로 설정 하 여 옵트인 (opt in)을 ASP.NET Core 합니다. 인증에 몇 가지 [문제가 발생](https://github.com/aspnet/Announcements/issues/318) 한 후에는 대부분의 SameSite 사용이 [사용 되지 않도록 설정](https://github.com/aspnet/Announcements/issues/348)되었습니다.

11 월 2019에 패치를 실행 하 여 2016 표준에서 2019 standard로 업데이트 했습니다. [SameSite 사양의 2019 초안](https://github.com/aspnet/Announcements/issues/390):

* 는 이전 버전과 호환 **되지 않습니다** . 2016 초안. 자세한 내용은이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.
* 기본적으로 `SameSite=Lax`로 처리 되는 쿠키를 지정 합니다.
* 교차 사이트 배달을 사용 하도록 설정 하기 위해 `SameSite=None`를 명시적으로 어설션하는 쿠키를 `Secure`으로 표시 해야 합니다. `None`은 옵트아웃 (opt out) 할 새 항목입니다.
* 는 ASP.NET Core 2.1, 2.2 및 3.0에 대해 발급 된 패치에 의해 지원 됩니다. ASP.NET Core 3.1에는 추가 SameSite 지원 됩니다.
* 는 기본적으로 [2 월 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)에 [Chrome](https://chromestatus.com/feature/5088147346030592) 에서 사용 하도록 예약 됩니다. 브라우저에서 2019의이 표준으로 이동 하기 시작 했습니다.

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a>2016 SameSite draft standard에서 2019 초안 표준으로 변경 되어 영향을 받는 Api

* [SameSiteMode](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [CookieOptions. SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [CookieBuilder. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a>이전 브라우저 지원

2016 SameSite 표준에서는 알 수 없는 값을 `SameSite=Strict` 값으로 처리 해야 합니다. 2016 SameSite 표준을 지 원하는 이전 브라우저에서 액세스 한 앱은 `None`값으로 SameSite 속성을 가져오면 중단 될 수 있습니다. 웹 앱은 이전 브라우저를 지원 하려는 경우 브라우저 검색을 구현 해야 합니다. 사용자 에이전트 값이 매우 휘발성 이며 자주 변경 되기 때문에 ASP.NET Core 브라우저 검색을 구현 하지 않습니다. <xref:Microsoft.AspNetCore.CookiePolicy> 확장 지점은 사용자 에이전트 특정 논리를 연결 하도록 허용 합니다.

`Startup.Configure`에서 *<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 또는 쿠키* 를 작성 하는 메서드를 호출 하기 전에 <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>를 호출 하는 코드를 추가 합니다.

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

`Startup.ConfigureServices`에서 다음과 유사한 코드를 추가 합니다.

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

위의 샘플에서 `MyUserAgentDetectionLib.DisallowsSameSiteNone` 사용자 에이전트가 SameSite `None`를 지원 하지 않는지 검색 하는 사용자 제공 라이브러리입니다.

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

다음 코드는 샘플 `DisallowsSameSiteNone` 메서드를 보여 줍니다.

> [!WARNING]
> 다음 코드는 데모용 으로만 사용할 수 있습니다.
> * 완료 되지 않은 것으로 간주 됩니다.
> * 유지 관리 되거나 지원 되지 않습니다.

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a>SameSite 문제에 대 한 앱 테스트

타사 로그인을 통해와 같은 원격 사이트와 상호 작용 하는 앱은 다음 작업을 수행 해야 합니다.

* 여러 브라우저에서 상호 작용을 테스트 합니다.
* 이 문서에서 설명 [하는 CookiePolicy 브라우저 검색 및 완화](#sob) 를 적용 합니다.

새 SameSite 동작을 옵트인 (opt in) 할 수 있는 클라이언트 버전을 사용 하 여 웹 앱을 테스트 합니다. Chrome, Firefox 및 Chromium Edge 모두에는 테스트에 사용할 수 있는 새로운 옵트인 기능 플래그가 있습니다. 앱이 SameSite 패치를 적용 한 후 이전 클라이언트 버전, 특히 Safari를 사용 하 여 테스트 합니다. 자세한 내용은이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.

### <a name="test-with-chrome"></a>Chrome으로 테스트

Chrome 78 +는 일시적인 완화를 제공 하기 때문에 잘못 된 결과를 제공 합니다. Chrome 78 + 임시 완화를 사용 하면 쿠키가 2 분 이내에 이전 될 수 있습니다. 적절 한 테스트 플래그가 설정 된 Chrome 76 또는 77은 보다 정확한 결과를 제공 합니다. 새 SameSite 동작을 테스트 하려면 `chrome://flags/#same-site-by-default-cookies` **설정**으로 전환 합니다. 이전 버전의 Chrome (75 및 아래)은 새 `None` 설정으로 인해 실패 하는 것으로 보고 됩니다. 이 문서의 [이전 브라우저 지원](#sob) 을 참조 하세요.

Google은 이전 chrome 버전을 사용할 수 없도록 설정 하지 않습니다. [Chromium 다운로드](https://www.chromium.org/getting-involved/download-chromium) 의 지침에 따라 이전 버전의 Chrome을 테스트 합니다. 이전 버전의 chrome을 검색 하 여 제공 된 링크에서 Chrome을 다운로드 **하지** 마세요.

* [Chromium 76 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [Chromium 74 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

### <a name="test-with-safari"></a>Safari를 사용 하 여 테스트

Safari 12는 이전 초안을 엄격 하 게 구현 했으며 새 `None` 값이 쿠키에 있는 경우 실패 합니다. 이 문서에서 [이전 브라우저를 지 원하는](#sob) 브라우저 검색 코드를 통해 `None`를 방지할 수 있습니다. MSAL, ADAL 또는 사용 중인 라이브러리를 사용 하 여 Safari 12, Safari 13 및 WebKit 기반 OS 스타일 로그인을 테스트 합니다. 문제는 기본 OS 버전에 따라 달라 집니다. OSX Mojave (10.14) 및 iOS 12는 새로운 SameSite 동작의 호환성 문제를 해결 하는 것으로 알려져 있습니다. OS를 OSX Catalina.properties (10.15) 또는 iOS 13로 업그레이드 하면 문제가 해결 됩니다. Safari에는 현재 새 사양 동작 테스트를 위한 옵트인 플래그가 없습니다.

### <a name="test-with-firefox"></a>Firefox로 테스트

새 표준에 대 한 Firefox 지원은 `network.cookie.sameSite.laxByDefault`기능 플래그를 사용 하 여 `about:config` 페이지에서 옵트인 하 여 버전 68 이상에서 테스트할 수 있습니다. 이전 버전의 Firefox와의 호환성 문제에 대 한 보고서가 없습니다.

### <a name="test-with-edge-browser"></a>Edge 브라우저를 사용 하 여 테스트

Edge는 이전 SameSite 표준을 지원 합니다. Edge 버전 44에는 새로운 표준과의 알려진 호환성 문제가 없습니다.

### <a name="test-with-edge-chromium"></a>Edge를 사용 하 여 테스트 (Chromium)

SameSite 플래그는 `edge://flags/#same-site-by-default-cookies` 페이지에 설정 되어 있습니다. Edge Chromium에서 호환성 문제가 검색 되지 않았습니다.

### <a name="test-with-electron"></a>전자로 테스트

전자의 버전에는 이전 버전의 Chromium가 포함 되어 있습니다. 예를 들어 팀에서 사용 하는 전자의 버전은 Chromium 66 이며,이는 이전 동작을 보여 주는 것입니다. 제품에서 사용 하는 전자 제품 버전으로 고유한 호환성 테스트를 수행 해야 합니다. 다음 섹션에서 [이전 브라우저 지원](#sob) 을 참조 하세요.

## <a name="additional-resources"></a>추가 자료

* [Chromium 블로그: 개발자: 새 SameSite를 사용할 준비가 되었습니다. 보안 쿠키 설정](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [SameSite 쿠키 설명](https://web.dev/samesite-cookies-explained/)
