---
title: ASP.NET Core의 응답 캐싱 미들웨어
author: guardrex
description: ASP.NET Core에서 응답 캐싱 미들웨어를 구성하고 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: performance/caching/middleware
ms.openlocfilehash: 61fa42161560ce2b512a73f1d7e32d11cd9bcb2c
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114798"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>ASP.NET Core의 응답 캐싱 미들웨어

By [Luke Latham 문자](https://github.com/guardrex) 및 [John 루 오 어](https://github.com/JunTaoLuo)

::: moniker range=">= aspnetcore-3.0"

이 문서에서는 ASP.NET Core 응용 프로그램에서 응답 캐싱 미들웨어를 구성하는 방법을 알아봅니다. 미들웨어는 응답을 캐싱할 수 있는 시점을 결정하고 응답을 저장하고 캐시에서 가져온 응답을 제공합니다. HTTP 캐싱 및 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성에 대 한 소개는 [응답 캐싱](xref:performance/caching/response)을 참조 하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>구성

응답 캐싱 미들웨어는 공유 프레임 워크를 통해 ASP.NET Core 앱에 대해 암시적으로 사용할 수 있습니다.

`Startup.ConfigureServices`에서 응답 캐싱 미들웨어를 서비스 컬렉션에 추가 합니다.

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

`Startup.Configure`에서 요청 처리 파이프라인에 미들웨어를 추가 하는 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 확장 메서드를 사용 하 여 미들웨어를 사용 하도록 앱을 구성 합니다.

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

샘플 앱은 다음 요청에 대 한 캐싱을 제어 하는 헤더를 추가 합니다.

* [캐시 제어](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash;는 최대 10 초 동안 캐시할 수 있는 응답을 캐시 합니다.
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash;는 후속 요청의 [수락 인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4) 헤더가 원래 요청에 일치 하는 경우에만 캐시 된 응답을 제공 하도록 미들웨어를 구성 합니다.

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

응답 캐싱 미들웨어는 상태 코드가 200(정상)인 서버 응답만 캐시합니다. [오류 페이지](xref:fundamentals/error-handling)를 비롯 한 다른 모든 응답은 미들웨어에서 무시 됩니다.

> [!WARNING]
> 인증된 클라이언트에 대한 콘텐츠를 포함하고 있는 응답은 미들웨어가 해당 응답을 저장하거나 제공하는 것을 막을 수 있도록 반드시 캐시가 불가능한 것으로 표시해야 합니다. 미들웨어가 응답을 캐시할 수 있는지 여부를 결정 하는 방법에 대 한 자세한 내용은 [캐싱에 대 한 조건을](#conditions-for-caching) 참조 하세요.

## <a name="options"></a>옵션

응답 캐싱 옵션은 다음 표에 나와 있습니다.

| 옵션 | 설명 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | 캐시 가능한 응답 본문의 최대 바이트 크기입니다. 기본값은 `64 * 1024 * 1024` (64 MB)입니다. |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | 응답 캐싱 미들웨어의 바이트 크기 제한입니다. 기본값은 `100 * 1024 * 1024` (100 MB)입니다. |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | 경로의 대/소문자를 구분해서 응답을 캐시할지 결정합니다. 기본값은 `false`입니다. |

아래 예제는 미들웨어를 다음과 같이 구성합니다.

* 본문 크기가 1024 바이트 보다 작거나 같은 응답을 캐시 합니다.
* 대/소문자 구분 경로를 기준으로 응답을 저장 합니다. 예를 들어 `/page1` 및 `/Page1`는 별도로 저장 됩니다.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

MVC/web API 컨트롤러나 Razor Pages 페이지 모델을 사용 하는 경우 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성은 응답 캐싱에 적합 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다. 미들웨어가 엄격 하 게 필요한 `[ResponseCache]` 특성의 유일한 매개 변수는 실제 HTTP 헤더에 해당 하지 않는 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>입니다. 자세한 내용은 <xref:performance/caching/response#responsecache-attribute>을 참조하세요.

`[ResponseCache]` 특성을 사용 하지 않는 경우 `VaryByQueryKeys`에서 응답 캐싱이 달라질 수 있습니다. 다음과 같이 [HttpContext 기능](xref:Microsoft.AspNetCore.Http.HttpContext.Features)에서 직접 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>를 사용 합니다.

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

`VaryByQueryKeys` `*`와 같은 단일 값을 사용 하면 모든 요청 쿼리 매개 변수에 의해 캐시가 달라 집니다.

## <a name="http-headers-used-by-response-caching-middleware"></a>응답 캐싱 미들웨어에서 사용되는 HTTP 헤더

다음 표에서는 응답 캐싱에 영향을 주는 HTTP 헤더에 대 한 정보를 제공 합니다.

| 헤더 | 세부 정보 |
| ------ | ------- |
| `Authorization` | 이 헤더가 존재할 경우 응답이 캐시되지 않습니다. |
| `Cache-Control` | 미들웨어는 `public` cache 지시어로 표시 된 캐싱 응답만 고려 합니다. 다음 매개 변수로 캐싱을 제어하십시오.<ul><li>max-age</li><li>max-stale&#8224;</li><li>최소-새로</li><li>must-revalidate</li><li>no-cache</li><li>저장소 없음</li><li>-인 경우에만 캐시</li><li>프라이빗</li><li>public</li><li>s-maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;`max-stale`에 대 한 제한이 지정 되지 않은 경우 미들웨어는 아무런 작업도 수행 하지 않습니다.<br>&#8225;`proxy-revalidate` `must-revalidate`와 동일한 효과가 있습니다.<br><br>자세한 내용은 [RFC 7231: 요청 캐시-제어 지시문](https://tools.ietf.org/html/rfc7234#section-5.2.1)을 참조 하십시오. |
| `Pragma` | 요청의 `Pragma: no-cache` 헤더는 `Cache-Control: no-cache`와 동일한 결과를 생성 합니다. 이 헤더는 `Cache-Control` 헤더의 관련 지시문 (있는 경우)에 의해 재정의 됩니다. HTTP/1.0에 대한 하위 호환성을 감안하기 위한 헤더입니다. |
| `Set-Cookie` | 이 헤더가 존재할 경우 응답이 캐시되지 않습니다. 하나 이상의 쿠키를 설정 하는 요청 처리 파이프라인의 미들웨어는 응답 캐싱 미들웨어가 응답을 캐싱하는 것을 방지 합니다 (예: [쿠키 기반 TempData 공급자](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | `Vary` 헤더는 다른 헤더로 캐시 된 응답을 변경 하는 데 사용 됩니다. 예를 들어 `Accept-Encoding: gzip` 헤더를 사용 하 여 요청에 대 한 응답을 캐시 하 고 별도로 `Accept-Encoding: text/plain` 하는 `Vary: Accept-Encoding` 헤더를 포함 하 여 인코딩에 응답을 캐시 합니다. `*` 헤더 값이 포함 된 응답은 저장 되지 않습니다. |
| `Expires` | 이 헤더에 의해 부실 하 게 간주 되는 응답은 다른 `Cache-Control` 헤더에 의해 재정의 되지 않는 한 저장 되거나 검색 되지 않습니다. |
| `If-None-Match` | 값이 `*` 되지 않고 응답의 `ETag` 제공 된 값과 일치 하지 않는 경우 전체 응답이 캐시에서 제공 됩니다. 그렇지 않으면 304(수정되지 않음) 응답이 제공됩니다. |
| `If-Modified-Since` | `If-None-Match` 헤더가 없으면 캐시 된 응답 날짜가 제공 된 값 보다 최신인 경우 전체 응답이 캐시에서 제공 됩니다. 그렇지 않으면 *304 수정 되지 않은* 응답이 제공 됩니다. |
| `Date` | 캐시에서 서비스를 제공 하는 경우에는 `Date` 헤더가 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다. |
| `Content-Length` | 캐시에서 서비스를 제공 하는 경우에는 `Content-Length` 헤더가 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다. |
| `Age` | 원래 응답에서 보낸 `Age` 헤더는 무시 됩니다. 캐시된 응답을 제공할 때 미들웨어가 새로운 값을 계산합니다. |

## <a name="caching-respects-request-cache-control-directives"></a>캐싱에서 요청 Cache-Control 지시문 사용

미들웨어는 [HTTP 1.1 캐싱 사양의](https://tools.ietf.org/html/rfc7234#section-5.2)규칙을 고려 합니다. 규칙에는 클라이언트가 보낸 유효한 `Cache-Control` 헤더를 인식 하기 위해 캐시가 필요 합니다. 사양에 따라 클라이언트는 `no-cache` 헤더 값을 사용 하 여 요청을 수행 하 고 서버에서 모든 요청에 대 한 새 응답을 생성 하도록 강제할 수 있습니다. 현재 미들웨어를 사용할 때 이 캐싱 동작을 개발자가 제어할 수 있는 방법은 없으며, 그 이유는 미들웨어가 공식 캐싱 사양을 따르고 있기 때문입니다.

캐싱 동작을 보다 세세히 제어하려면 ASP.NET Core의 다른 캐싱 기능을 살펴보십시오. 다음 항목을 참조하세요.

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>문제 해결

캐싱이 예상과 다르게 동작한다면 응답이 캐싱 가능하고 캐시에서 제공될 수 있는지 확인하시기 바랍니다. 요청의 수신 헤더와 응답의 송신 헤더를 점검해보십시오. 디버깅에 도움이 되는 [로깅을](xref:fundamentals/logging/index) 사용 합니다.

캐싱 동작을 테스트하거나 문제를 해결할 때, 브라우저가 원하지 않는 방식으로 캐싱에 영향을 주는 요청 헤더를 설정할 수도 있습니다. 예를 들어, 브라우저에서 페이지를 새로 고칠 때 `Cache-Control` 헤더를 `no-cache` 또는 `max-age=0`로 설정할 수 있습니다. 다음 도구를 사용하면 명시적으로 요청 헤더를 설정할 수 있고 캐싱 테스트에도 적합합니다.

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>캐싱 조건

* 요청의 결과로 200(OK) 상태 코드가 설정된 서버 응답을 받아야 합니다.
* 요청 메서드가 GET 또는 HEAD여야 합니다.
* `Startup.Configure`에서 캐싱은 캐싱이 필요한 미들웨어 앞에 배치 해야 합니다. 자세한 내용은 <xref:fundamentals/middleware/index>을 참조하세요.
* `Authorization` 헤더가 없어야 합니다.
* `Cache-Control` 헤더 매개 변수는 유효 해야 하 고 응답을 `public`로 표시 하 고 `private`표시 하지 않아야 합니다.
* `Cache-Control` 헤더가 있을 때 `Pragma` 헤더를 재정의 하기 때문에 `Cache-Control` 헤더가 없으면 `Pragma: no-cache` 헤더가 없어야 합니다.
* `Set-Cookie` 헤더가 없어야 합니다.
* `Vary` 헤더 매개 변수는 유효 해야 하며 `*`와 같지 않아야 합니다.
* `Content-Length` 헤더 값 (설정 된 경우)은 응답 본문의 크기와 일치 해야 합니다.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> 사용 되지 않습니다.
* 응답은 `Expires` 헤더에 지정 된 대로 유효 하지 않아야 하 고 `max-age` 및 `s-maxage` 캐시 지시문에 의해 지정 됩니다.
* 응답 버퍼링이 성공 해야 합니다. 응답의 크기는 구성 된 또는 기본 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>보다 작아야 합니다. 응답의 본문 크기는 구성 된 또는 기본 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>보다 작아야 합니다.
* [RFC 7234](https://tools.ietf.org/html/rfc7234) 사양에 따라 응답을 캐시할 수 있어야 합니다. 예를 들어 `no-store` 지시문은 요청 또는 응답 헤더 필드에 없어야 합니다. 자세한 내용은 섹션 3: [RFC 7234](https://tools.ietf.org/html/rfc7234) 의 *캐시에 응답 저장* 을 참조 하세요.

> [!NOTE]
> CSRF (교차 사이트 요청 위조) 공격을 방지 하기 위해 CSRF (교차 사이트 요청 위조) 공격을 방지 하기 위한 위조 방지 시스템은 응답이 캐시 되지 않도록 `Cache-Control` 및 `Pragma` 헤더를 `no-cache` 설정 합니다. HTML 양식 요소에 대 한 위조 방지 토큰을 사용 하지 않도록 설정 하는 방법에 대 한 자세한 내용은 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>를 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 문서에서는 ASP.NET Core 응용 프로그램에서 응답 캐싱 미들웨어를 구성하는 방법을 알아봅니다. 미들웨어는 응답을 캐싱할 수 있는 시점을 결정하고 응답을 저장하고 캐시에서 가져온 응답을 제공합니다. HTTP 캐싱 및 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성에 대 한 소개는 [응답 캐싱](xref:performance/caching/response)을 참조 하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>구성

[AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 사용 하거나 [ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) 패키지에 대 한 패키지 참조를 추가 합니다.

`Startup.ConfigureServices`에서 응답 캐싱 미들웨어를 서비스 컬렉션에 추가 합니다.

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

`Startup.Configure`에서 요청 처리 파이프라인에 미들웨어를 추가 하는 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 확장 메서드를 사용 하 여 미들웨어를 사용 하도록 앱을 구성 합니다.

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

샘플 앱은 다음 요청에 대 한 캐싱을 제어 하는 헤더를 추가 합니다.

* [캐시 제어](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash;는 최대 10 초 동안 캐시할 수 있는 응답을 캐시 합니다.
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash;는 후속 요청의 [수락 인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4) 헤더가 원래 요청에 일치 하는 경우에만 캐시 된 응답을 제공 하도록 미들웨어를 구성 합니다.

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

응답 캐싱 미들웨어는 상태 코드가 200(정상)인 서버 응답만 캐시합니다. [오류 페이지](xref:fundamentals/error-handling)를 비롯 한 다른 모든 응답은 미들웨어에서 무시 됩니다.

> [!WARNING]
> 인증된 클라이언트에 대한 콘텐츠를 포함하고 있는 응답은 미들웨어가 해당 응답을 저장하거나 제공하는 것을 막을 수 있도록 반드시 캐시가 불가능한 것으로 표시해야 합니다. 미들웨어가 응답을 캐시할 수 있는지 여부를 결정 하는 방법에 대 한 자세한 내용은 [캐싱에 대 한 조건을](#conditions-for-caching) 참조 하세요.

## <a name="options"></a>옵션

응답 캐싱 옵션은 다음 표에 나와 있습니다.

| 옵션 | 설명 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | 캐시 가능한 응답 본문의 최대 바이트 크기입니다. 기본값은 `64 * 1024 * 1024` (64 MB)입니다. |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | 응답 캐싱 미들웨어의 바이트 크기 제한입니다. 기본값은 `100 * 1024 * 1024` (100 MB)입니다. |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | 경로의 대/소문자를 구분해서 응답을 캐시할지 결정합니다. 기본값은 `false`입니다. |

아래 예제는 미들웨어를 다음과 같이 구성합니다.

* 본문 크기가 1024 바이트 보다 작거나 같은 응답을 캐시 합니다.
* 대/소문자 구분 경로를 기준으로 응답을 저장 합니다. 예를 들어 `/page1` 및 `/Page1`는 별도로 저장 됩니다.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

MVC/web API 컨트롤러나 Razor Pages 페이지 모델을 사용 하는 경우 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성은 응답 캐싱에 적합 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다. 미들웨어가 엄격 하 게 필요한 `[ResponseCache]` 특성의 유일한 매개 변수는 실제 HTTP 헤더에 해당 하지 않는 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>입니다. 자세한 내용은 <xref:performance/caching/response#responsecache-attribute>을 참조하세요.

`[ResponseCache]` 특성을 사용 하지 않는 경우 `VaryByQueryKeys`에서 응답 캐싱이 달라질 수 있습니다. 다음과 같이 [HttpContext 기능](xref:Microsoft.AspNetCore.Http.HttpContext.Features)에서 직접 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>를 사용 합니다.

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

`VaryByQueryKeys` `*`와 같은 단일 값을 사용 하면 모든 요청 쿼리 매개 변수에 의해 캐시가 달라 집니다.

## <a name="http-headers-used-by-response-caching-middleware"></a>응답 캐싱 미들웨어에서 사용되는 HTTP 헤더

다음 표에서는 응답 캐싱에 영향을 주는 HTTP 헤더에 대 한 정보를 제공 합니다.

| 헤더 | 세부 정보 |
| ------ | ------- |
| `Authorization` | 이 헤더가 존재할 경우 응답이 캐시되지 않습니다. |
| `Cache-Control` | 미들웨어는 `public` cache 지시어로 표시 된 캐싱 응답만 고려 합니다. 다음 매개 변수로 캐싱을 제어하십시오.<ul><li>max-age</li><li>max-stale&#8224;</li><li>최소-새로</li><li>must-revalidate</li><li>no-cache</li><li>저장소 없음</li><li>-인 경우에만 캐시</li><li>프라이빗</li><li>public</li><li>s-maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;`max-stale`에 대 한 제한이 지정 되지 않은 경우 미들웨어는 아무런 작업도 수행 하지 않습니다.<br>&#8225;`proxy-revalidate` `must-revalidate`와 동일한 효과가 있습니다.<br><br>자세한 내용은 [RFC 7231: 요청 캐시-제어 지시문](https://tools.ietf.org/html/rfc7234#section-5.2.1)을 참조 하십시오. |
| `Pragma` | 요청의 `Pragma: no-cache` 헤더는 `Cache-Control: no-cache`와 동일한 결과를 생성 합니다. 이 헤더는 `Cache-Control` 헤더의 관련 지시문 (있는 경우)에 의해 재정의 됩니다. HTTP/1.0에 대한 하위 호환성을 감안하기 위한 헤더입니다. |
| `Set-Cookie` | 이 헤더가 존재할 경우 응답이 캐시되지 않습니다. 하나 이상의 쿠키를 설정 하는 요청 처리 파이프라인의 미들웨어는 응답 캐싱 미들웨어가 응답을 캐싱하는 것을 방지 합니다 (예: [쿠키 기반 TempData 공급자](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | `Vary` 헤더는 다른 헤더로 캐시 된 응답을 변경 하는 데 사용 됩니다. 예를 들어 `Accept-Encoding: gzip` 헤더를 사용 하 여 요청에 대 한 응답을 캐시 하 고 별도로 `Accept-Encoding: text/plain` 하는 `Vary: Accept-Encoding` 헤더를 포함 하 여 인코딩에 응답을 캐시 합니다. `*` 헤더 값이 포함 된 응답은 저장 되지 않습니다. |
| `Expires` | 이 헤더에 의해 부실 하 게 간주 되는 응답은 다른 `Cache-Control` 헤더에 의해 재정의 되지 않는 한 저장 되거나 검색 되지 않습니다. |
| `If-None-Match` | 값이 `*` 되지 않고 응답의 `ETag` 제공 된 값과 일치 하지 않는 경우 전체 응답이 캐시에서 제공 됩니다. 그렇지 않으면 304(수정되지 않음) 응답이 제공됩니다. |
| `If-Modified-Since` | `If-None-Match` 헤더가 없으면 캐시 된 응답 날짜가 제공 된 값 보다 최신인 경우 전체 응답이 캐시에서 제공 됩니다. 그렇지 않으면 *304 수정 되지 않은* 응답이 제공 됩니다. |
| `Date` | 캐시에서 서비스를 제공 하는 경우에는 `Date` 헤더가 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다. |
| `Content-Length` | 캐시에서 서비스를 제공 하는 경우에는 `Content-Length` 헤더가 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다. |
| `Age` | 원래 응답에서 보낸 `Age` 헤더는 무시 됩니다. 캐시된 응답을 제공할 때 미들웨어가 새로운 값을 계산합니다. |

## <a name="caching-respects-request-cache-control-directives"></a>캐싱에서 요청 Cache-Control 지시문 사용

미들웨어는 [HTTP 1.1 캐싱 사양의](https://tools.ietf.org/html/rfc7234#section-5.2)규칙을 고려 합니다. 규칙에는 클라이언트가 보낸 유효한 `Cache-Control` 헤더를 인식 하기 위해 캐시가 필요 합니다. 사양에 따라 클라이언트는 `no-cache` 헤더 값을 사용 하 여 요청을 수행 하 고 서버에서 모든 요청에 대 한 새 응답을 생성 하도록 강제할 수 있습니다. 현재 미들웨어를 사용할 때 이 캐싱 동작을 개발자가 제어할 수 있는 방법은 없으며, 그 이유는 미들웨어가 공식 캐싱 사양을 따르고 있기 때문입니다.

캐싱 동작을 보다 세세히 제어하려면 ASP.NET Core의 다른 캐싱 기능을 살펴보십시오. 다음 항목을 참조하세요.

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>문제 해결

캐싱이 예상과 다르게 동작한다면 응답이 캐싱 가능하고 캐시에서 제공될 수 있는지 확인하시기 바랍니다. 요청의 수신 헤더와 응답의 송신 헤더를 점검해보십시오. 디버깅에 도움이 되는 [로깅을](xref:fundamentals/logging/index) 사용 합니다.

캐싱 동작을 테스트하거나 문제를 해결할 때, 브라우저가 원하지 않는 방식으로 캐싱에 영향을 주는 요청 헤더를 설정할 수도 있습니다. 예를 들어, 브라우저에서 페이지를 새로 고칠 때 `Cache-Control` 헤더를 `no-cache` 또는 `max-age=0`로 설정할 수 있습니다. 다음 도구를 사용하면 명시적으로 요청 헤더를 설정할 수 있고 캐싱 테스트에도 적합합니다.

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>캐싱 조건

* 요청의 결과로 200(OK) 상태 코드가 설정된 서버 응답을 받아야 합니다.
* 요청 메서드가 GET 또는 HEAD여야 합니다.
* `Startup.Configure`에서 캐싱은 캐싱이 필요한 미들웨어 앞에 배치 해야 합니다. 자세한 내용은 <xref:fundamentals/middleware/index>을 참조하세요.
* `Authorization` 헤더가 없어야 합니다.
* `Cache-Control` 헤더 매개 변수는 유효 해야 하 고 응답을 `public`로 표시 하 고 `private`표시 하지 않아야 합니다.
* `Cache-Control` 헤더가 있을 때 `Pragma` 헤더를 재정의 하기 때문에 `Cache-Control` 헤더가 없으면 `Pragma: no-cache` 헤더가 없어야 합니다.
* `Set-Cookie` 헤더가 없어야 합니다.
* `Vary` 헤더 매개 변수는 유효 해야 하며 `*`와 같지 않아야 합니다.
* `Content-Length` 헤더 값 (설정 된 경우)은 응답 본문의 크기와 일치 해야 합니다.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> 사용 되지 않습니다.
* 응답은 `Expires` 헤더에 지정 된 대로 유효 하지 않아야 하 고 `max-age` 및 `s-maxage` 캐시 지시문에 의해 지정 됩니다.
* 응답 버퍼링이 성공 해야 합니다. 응답의 크기는 구성 된 또는 기본 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>보다 작아야 합니다. 응답의 본문 크기는 구성 된 또는 기본 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>보다 작아야 합니다.
* [RFC 7234](https://tools.ietf.org/html/rfc7234) 사양에 따라 응답을 캐시할 수 있어야 합니다. 예를 들어 `no-store` 지시문은 요청 또는 응답 헤더 필드에 없어야 합니다. 자세한 내용은 섹션 3: [RFC 7234](https://tools.ietf.org/html/rfc7234) 의 *캐시에 응답 저장* 을 참조 하세요.

> [!NOTE]
> CSRF (교차 사이트 요청 위조) 공격을 방지 하기 위해 CSRF (교차 사이트 요청 위조) 공격을 방지 하기 위한 위조 방지 시스템은 응답이 캐시 되지 않도록 `Cache-Control` 및 `Pragma` 헤더를 `no-cache` 설정 합니다. HTML 양식 요소에 대 한 위조 방지 토큰을 사용 하지 않도록 설정 하는 방법에 대 한 자세한 내용은 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>를 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
