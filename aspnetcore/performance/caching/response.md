---
title: ASP.NET core에서 응답 캐싱
author: rick-anderson
description: 낮은 대역폭 요구 사항에 대응하고 ASP.NET Core 응용 프로그램의 성능을 향상시키기 위해 응답 캐싱을 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/28/2019
uid: performance/caching/response
ms.openlocfilehash: 2e247dcff2cbaa3711a9206d7237a061ae351e1d
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892470"
---
# <a name="response-caching-in-aspnet-core"></a>ASP.NET core에서 응답 캐싱

작성자: [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), 및 [Luke Latham](https://github.com/guardrex)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

응답 캐싱은 클라이언트나 프록시가 웹 서버에 요청하는 회수를 줄여줍니다. 또한 응답 캐싱은 웹 서버가 응답을 생성하기 위해 수행해야 하는 작업의 총량도 줄여줍니다. 응답 캐싱은 클라이언트, 프록시, 및 미들웨어가 응답을 캐싱해야 하는 방식을 지시하는 헤더에 의해 제어됩니다.

합니다 [ResponseCache 특성](#responsecache-attribute) 응답 헤더는 클라이언트가 응답을 캐시 하는 경우 적용 될 수 있습니다 캐싱 설정에 참여 합니다. [응답 캐싱 미들웨어](xref:performance/caching/middleware) 서버의 응답을 캐시 하는 데 사용 됩니다. 미들웨어를 사용 하 여 수 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 속성 서버 쪽 캐싱 동작에 영향을 줍니다.

## <a name="http-based-response-caching"></a>HTTP 기반 응답 캐싱

[HTTP 1.1 캐싱 사양](https://tools.ietf.org/html/rfc7234)은 인터넷 캐시가 동작해야 하는 방식을 기술합니다. 캐싱에 사용되는 가장 기본적인 HTTP 헤더는 [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2)로 캐시 *지시문*을 지정하는 데 사용됩니다. 지시문은 요청 방식으로 클라이언트에서 서버로 인해 및 응답으로 서버에서 클라이언트에 다시 확인 하는 대로 캐싱 동작을 제어 합니다. 프록시 서버를 통해서 이동하는 요청 및 응답, 그리고 프록시 서버 역시 HTTP 1.1 캐싱 사양을 준수해야 합니다.

기본적인 `Cache-Control` 지시문은 다음 표와 같습니다.

| 지시문                                                       | 작업 |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | 캐시에 응답을 저장할 수 있습니다. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | 공유 캐시는 응답을 저장하지 않습니다. 사설 캐시는 응답을 저장하고 재사용할 수 있습니다. |
| [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | 클라이언트는 오래 된 시간 (초) 지정 된 숫자 보다 큰 경우 응답을 수락 하지 않습니다. 예를 들면 다음과 같습니다. `max-age=60` (60 초), `max-age=2592000` (1 월) |
| [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **요청에**: 캐시 요청을 충족 하도록 저장된 응답을 사용 해야 합니다. 원본 서버는 클라이언트에 대 한 응답을 다시 생성 하 고 미들웨어 저장된 응답을 캐시를 업데이트 합니다.<br><br>**응답에서**: 원본 서버에서 유효성을 검사 하지 않고 후속 요청에 대 한 응답을 사용 되어야 합니다. |
| [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **요청에**: 캐시는 요청을 저장 하지 않아야 합니다.<br><br>**응답에서**: 캐시에서 응답의 일부를 저장 하지 않아야 합니다. |

캐싱 역할을 수행하는 다른 캐시 헤더는 다음 표와 같습니다.

| 헤더                                                     | 기능 |
| ---------------------------------------------------------- | -------- |
| [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | 응답이 생성되거나 원본 서버에서 정상적으로 검증된 이후로부터 지난 초 단위의 총 추정 시간. |
| [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | 부실 코드가 시간이 지나면 응답 것으로 간주 됩니다. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | `no-cache` 동작 설정에 대한 HTTP/1.0 캐시와의 하위 호환성을 지원하기 위해 존재합니다. `Cache-Control` 헤더가 존재할 경우 `Pragma` 헤더는 무시됩니다.  |
| [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | 모든 `Vary` 헤더 필드가 캐시된 응답의 원본 요청 및 새 요청 모두와 일치하지 않는 한 캐시된 응답이 전송되지 않도록 지정합니다. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>HTTP 기반 캐싱은 Cache-Control 지시문을 준수합니다

[Cache-Control 헤더에 대한 HTTP 1.1 캐싱 사양](https://tools.ietf.org/html/rfc7234#section-5.2)은 캐시에게 클라이언트가 전송하는 유효한 `Cache-Control` 헤더를 준수할 것을 요구합니다. 클라이언트는 `no-cache` 헤더 값을 설정한 요청을 전송함으로써 모든 요청에 대해 서버가 새로운 응답을 생성하도록 강제할 수 있습니다.

HTTP 캐싱의 목적을 고려했을 때 항상 클라이언트의 `Cache-Control` 요청 헤더를 준수하는 것이 이치에 맞습니다. 공식 사양에서 캐싱은 클라이언트, 프록시 및 서버 간의 네트워크에서 요청의 대기 시간 및 네트워크 오버헤드를 만족스럽게 줄이기 위한 것입니다. 원본 서버에서 부하를 제어하기 위한 방법이 필수적인 것은 아닙니다.

사용 하는 경우이 캐싱 동작을 통해 개발자 컨트롤이 없는 합니다 [응답 캐싱 미들웨어](xref:performance/caching/middleware) 미들웨어 캐싱 사양 공식 준수 때문입니다. [미들웨어의 향상 된 기능을 계획](https://github.com/aspnet/AspNetCore/issues/2612) 요청을 무시 하도록 미들웨어를 구성 하는 기회는 `Cache-Control` 헤더 캐시 된 응답을 제공 하고자 하는 경우. 계획 된 향상 된 기능에는 제어 서버 부하를 효과적으로 기회를 제공 합니다.

## <a name="other-caching-technology-in-aspnet-core"></a>ASP.NET Core의 다른 캐싱 기술

### <a name="in-memory-caching"></a>메모리 내 캐싱

메모리 내 캐싱은 서버의 메모리를 이용해서 캐시된 데이터를 저장합니다. 이 방식의 캐싱은 단일 서버나 *고정 세션*을 사용하는 복수의 서버에 적합합니다. 고정 세션은 클라이언트의 요청이 처리를 위해 항상 동일한 서버로 라우트된다는 것을 뜻합니다.

자세한 내용은 <xref:performance/caching/memory>을 참조하세요.

### <a name="distributed-cache"></a>분산 캐시

응용 프로그램이 클라우드나 서버 팜에서 호스팅될 때 메모리에 데이터를 저장하려면 분산 캐시를 사용합니다. 이 캐시는 요청을 처리하는 서버들 간에 서로 공유됩니다. 클라이언트에 대한 캐시 데이터를 사용할 수 있는 경우 클라이언트는 그룹의 어떤 서버에서나 처리할 수 있는 요청을 제출할 수 있습니다. ASP.NET Core는 SQL Server 및 Redis 분산 캐시를 제공합니다.

자세한 내용은 <xref:performance/caching/distributed>을 참조하세요.

### <a name="cache-tag-helper"></a>캐시 태그 도우미

캐시 태그 도우미를 사용 하 여 MVC 뷰 또는 Razor 페이지 콘텐츠를 캐시 합니다. 캐시 태그 도우미는 메모리 내 캐시에 데이터를 저장합니다.

자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>을 참조하세요.

### <a name="distributed-cache-tag-helper"></a>분산 캐시 태그 도우미

분산 캐시 태그 도우미를 사용 하 여 분산된 클라우드 또는 웹 팜 시나리오에는 Razor 페이지나 MVC 보기에서 해당 콘텐츠를 캐시 합니다. 분산 캐시 태그 도우미는 SQL Server 또는 Redis에 데이터를 저장합니다.

자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>을 참조하세요.

## <a name="responsecache-attribute"></a>ResponseCache 특성

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 응답 캐싱에서 적절 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다.

> [!WARNING]
> 인증된 클라이언트의 정보를 포함하는 콘텐츠는 캐싱하면 안 됩니다. 사용자 ID 또는 사용자 로그인 여부와 무관하게 변경되지 않는 콘텐츠만 캐싱해야 합니다.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 저장된 응답 지정된 목록 쿼리 키의 값에 따라 달라 집니다. 단일 값으로 `*`를 지정하면 모든 쿼리 문자열 매개 변수별로 미들웨어의 응답이 달라집니다.

[응답 캐싱 미들웨어](xref:performance/caching/middleware) 설정에 사용 하도록 설정 해야 합니다는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 속성입니다. 그렇지 않으면 런타임 예외가 throw 됩니다. <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 속성에 대응하는 HTTP 헤더는 존재하지 않습니다. 속성이 응답 캐싱 미들웨어에서 처리 하는 HTTP 기능입니다. 미들웨어가 캐시된 응답을 제공하려면 쿼리 문자열 및 쿼리 문자열 값이 이전 요청과 동일해야 합니다. 예를 들어, 다음 표는 요청 순서에 따른 결과를 보여줍니다.

| 요청                          | 결과                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | 서버에서 반환 합니다. |
| `http://example.com?key1=value1` | 미들웨어에서 반환 됩니다. |
| `http://example.com?key1=value2` | 서버에서 반환 합니다. |

첫 번째 요청은 서버에서 반환되고 미들웨어에 캐시됩니다. 두 번째 요청은 쿼리 문자열이 이전 요청과 일치하기 때문에 미들웨어에서 반환됩니다. 세 번째 요청은 쿼리 문자열 값이 이전 요청과 일치하지 않기 때문에 미들웨어 캐시에 존재하지 않습니다.

합니다 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 만들고 구성 하는 데 사용 됩니다 (통해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>)는 <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter>합니다. <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter> 적절 한 HTTP 헤더 및 응답의 기능 업데이트 작업을 수행 합니다. 필터:

* 기존에 존재하는 `Vary`, `Cache-Control` 및 `Pragma` 헤더를 모두 제거합니다.
* <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>에 설정된 속성을 기반으로 적절한 헤더를 작성합니다.
* <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 속성이 설정된 경우 응답 캐싱 HTTP 기능을 갱신합니다.

### <a name="vary"></a>Vary

이 헤더는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 속성이 설정된 경우에만 작성됩니다. 속성을로 설정 된 `Vary` 속성의 값입니다. 다음 예제는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 속성을 사용합니다:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

샘플 앱을 사용 하 여 브라우저의 네트워크 도구를 사용 하 여 응답 헤더를 봅니다. 다음 응답 헤더에 Cache1 페이지 응답과 함께 전송 됩니다.

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>NoStore 및 Location.None

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 속성은 다른 대부분의 속성을 덮어씁니다. 이 속성이 `true`로 설정되면 `Cache-Control` 헤더가 `no-store`로 설정됩니다. 만약 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>이 `None`으로 설정되면:

* `Cache-Control`이 `no-store,no-cache`로 설정됩니다.
* `Pragma`이 `no-cache`로 설정됩니다.

경우 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 됩니다 `false` 및 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 은 `None`, `Cache-Control`, 및 `Pragma` 으로 설정 됩니다 `no-cache`합니다.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 일반적으로 `true` 오류 페이지에 대 한 합니다. 샘플 앱에서 Cache2 페이지를 응답을 저장할 수 없도록 클라이언트에 게 지시 하는 응답 헤더를 생성 합니다.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

다음 헤더를 사용 하 여 Cache2 페이지를 반환 하는 샘플 앱:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Location 및 Duration

캐싱을 사용하기 위해서는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration>을 양수 값으로 설정하고 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>이 `Any` (기본값) 또는 `Client`이어야 합니다. 이 경우 `Cache-Control` 헤더는 응답의 `max-age`가 뒤에 추가된 위치 값으로 설정됩니다.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 옵션의 `Any`와 `Client`는 각각 `Cache-Control` 헤더의 값, `public`과 `private`로 변환됩니다. 앞에서 설명한 것처럼 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>을 `None`으로 설정하면 `Cache-Control` 및 `Pragma` 헤더가 모두 `no-cache`로 설정됩니다.

다음 예제에서는 샘플 앱 및 설정 하 여 생성 된 헤더에서 Cache3 페이지 모델을 보여 줍니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 기본값을 그대로 두고 및 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 값:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

샘플 앱은 다음 헤더를 사용 하 여 Cache3 페이지를 반환합니다.

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>캐시 프로필

여러 컨트롤러 작업 특성에 대 한 응답 캐시 설정을 복제 하는 대신 캐시 프로필으로 구성할 수 있습니다 옵션의 MVC/Razor 페이지를 설정할 때 `Startup.ConfigureServices`합니다. 참조 된 캐시 프로필의 값이는 기본적으로 사용 됩니다는 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 특성에 지정 된 모든 속성에 의해 재정의 됩니다.

캐시 프로필을 설정 합니다. 다음 예제에서는 샘플 앱의 두 번째 30 캐시 프로필을 보여 줍니다. `Startup.ConfigureServices`:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

샘플 앱의 Cache4 페이지 모델 참조의 `Default30` 프로필을 캐시 합니다.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 에 적용할 수 있습니다.

* Razor 페이지 처리기 (클래스) &ndash; 처리기 메서드에 특성을 적용할 수 없습니다.
* MVC 컨트롤러 (클래스)입니다.
* MVC 작업 (메서드) &ndash; 메서드 수준 특성이 클래스 수준 특성에 지정 된 설정을 재정의 합니다.

결과 헤더 Cache4 페이지 응답에 적용 된 `Default30` 프로필 캐시:

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>추가 자료

* [캐시에 응답 저장하기](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
