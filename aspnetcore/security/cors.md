---
title: 코어에서 CORS 간 요청(CORS) ASP.NET
author: rick-anderson
description: ASP.NET Core 앱에서 원본 간 요청을 허용하거나 거부하는 표준으로 CORS를 어떻게 사용할 수 있는지 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: 601e26e1990a86ad60aa50c8c93ffa490ff6b708
ms.sourcegitcommit: e72a58d6ebde8604badd254daae8077628f9d63e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81007186"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>코어에서 CORS 간 요청(CORS) ASP.NET

::: moniker range=">= aspnetcore-3.0"

[릭 앤더슨과](https://twitter.com/RickAndMSFT) [커크 라킨](https://twitter.com/serpent5)

이 문서에서는 ASP.NET 코어 앱에서 CORS를 사용하도록 설정하는 방법을 보여 주며, CORS를 활성화하는 방법을 보여 주시고 있습니다.

브라우저 보안은 웹 페이지가 웹 페이지를 제공한 도메인과 다른 도메인에 대한 요청을 하지 못하도록 합니다. 이 제한을 *동일한 원본 정책이라고*합니다. 동일한 원본 정책은 악의적인 사이트가 다른 사이트에서 중요한 데이터를 읽지 못하도록 합니다. 경우에 따라 다른 사이트에서 앱에 대한 원본 간 요청을 허용할 수 있습니다. 자세한 내용은 [모질라 CORS 문서를](https://developer.mozilla.org/docs/Web/HTTP/CORS)참조하십시오.

[원본 간 리소스](https://www.w3.org/TR/cors/) 공유(CORS):

* 서버가 동일한 원본 정책을 완화할 수 있는 W3C 표준입니다.
* 보안 기능이 **아니며** CORS는 보안을 완화합니다. CORS를 허용하면 API가 안전하지 않습니다. 자세한 내용은 [CORS 작동 방식을](#how-cors)참조하십시오.
* 서버가 다른 요청을 거부하면서 일부 원본 간 요청을 명시적으로 허용할 수 있습니다.
* [JSONP와](/dotnet/framework/wcf/samples/jsonp)같은 이전 기술보다 더 안전하고 유연합니다.

[샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [방법)](xref:index#how-to-download-a-sample)

## <a name="same-origin"></a>동일한 원산지

두 URL은 동일한 구성표, 호스트 및[포트(RFC 6454)가](https://tools.ietf.org/html/rfc6454)있는 경우 동일한 출처를 갖습니다.

이 두 URL의 출처는 동일합니다.

* `https://example.com/foo.html`
* `https://example.com/bar.html`

이러한 URL의 출처는 이전 두 URL과 다릅니다.

* `https://example.net`&ndash; 다른 도메인
* `https://www.example.com/foo.html`&ndash; 다른 하위 도메인
* `http://example.com/foo.html`&ndash; 다른 구성표
* `https://example.com:9000/foo.html`&ndash; 다른 포트

## <a name="enable-cors"></a>CORS를 사용하도록 설정

CORS를 활성화하는 방법에는 세 가지가 있습니다.

* [미들웨어에서 명명된 정책](#np) 또는 [기본 정책을](#dp)사용 하 여 .
* [끝점 라우팅](#ecors)을 사용 하 여 .
* [[인에이블코르] 특성을](#attr) 사용합니다.

명명된 정책과 함께 [[EnableCors] 특성을](#attr) 사용하면 CORS를 지원하는 끝점을 제한하는 데 가장 좋은 제어가 제공됩니다.

각 접근 방식은 다음 섹션에 자세히 설명되어 있습니다.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>명명된 정책 및 미들웨어가 있는 CORS

CORS 미들웨어는 교차 원산지 요청을 처리합니다. 다음 코드는 지정된 출처를 가진 모든 앱의 끝점에 CORS 정책을 적용합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

위의 코드:

* 정책 이름을 로 `_myAllowSpecificOrigins`설정합니다. 정책 이름은 임의입니다.
* 확장 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 메서드를 호출 하 `_myAllowSpecificOrigins` 고 CORS 정책을 지정 합니다. `UseCors`CORS 미들웨어를 추가합니다.
* <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [람다 식으로](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)호출합니다. 람다는 개체를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 취합니다. [과](#cors-policy-options)같은 `WithOrigins`구성 옵션은 이 문서의 후반부에서 설명합니다.
* 모든 `_myAllowSpecificOrigins` 컨트롤러 끝점에 대해 CORS 정책을 활성화합니다. 특정 끝점에 CORS 정책을 적용하려면 [끝점 라우팅을](#ecors) 참조하십시오.

끝점 라우팅의 경우 CORS 미들웨어는 에 대한 호출 `UseRouting` 과 `UseEndpoints`간에 실행하도록 ***구성되어야 합니다.***

위의 코드와 유사한 코드 테스트에 대한 지침은 [CORS 테스트를](#testc) 참조하십시오.

메서드 <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> 호출은 앱의 서비스 컨테이너에 CORS 서비스를 추가합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

자세한 내용은 이 문서의 [CORS 정책 옵션을](#cpo) 참조하십시오.

메서드는 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 다음 코드와 같이 연결할 수 있습니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

참고: 지정된 URL에는 후행 슬래시()가`/` **포함되어서는 안 됩니다.** URL이 `/`으로 종료되면 비교가 `false` 반환되고 헤더가 반환되지 않습니다.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>기본 정책 및 미들웨어가 있는 CORS

다음 강조 표시된 코드는 기본 CORS 정책을 사용할 수 있습니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

앞의 코드는 모든 컨트롤러 끝점에 기본 CORS 정책을 적용합니다.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>엔드포인트 라우팅을 사용하여 Cors 사용

현재 를 사용하여 `RequireCors` 엔드포인트별로 CORS를 사용하도록 설정하는 것은 자동 [프리플라이트 요청을](#apf)지원하지 ***않습니다.*** 자세한 내용은 [이 GitHub 문제](https://github.com/dotnet/aspnetcore/issues/20709) 및 [끝점 라우팅이 있는 테스트 CORS 및 [HttpOptions]를](#tcer)참조하십시오.

끝점 라우팅을 사용하면 다음과 같은 확장 메서드 집합을 사용하여 <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> 끝점별로 CORS를 사용할 수 있습니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,41,44)]

위의 코드에서

* `app.UseCors`CORS 미들웨어를 활성화할 수 있습니다. 기본 정책이 구성되지 않았기 때문에 `app.UseCors()` 단독으로는 CORS를 사용할 수 없습니다.
* `/echo` 및 컨트롤러 끝점은 지정된 정책을 사용하여 원본 간 요청을 허용합니다.
* `/echo2` 및 Razor 페이지 끝점은 기본 정책이 지정되지 않았기 때문에 원본 간 요청을 허용하지 ***않습니다.***

[[DisableCors]](#dc) 특성은 `RequireCors`에서 끝점 라우팅에 의해 활성화된 CORS를 사용하지 ***않도록 설정하지*** 않습니다.

선행과 유사한 코드 테스트에 대한 지침은 [끝점 라우팅이 포함된 테스트 CORS 및 [HttpOptions]를](#tcer) 참조하십시오.

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>특성을 사용하여 CORS 사용

[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성으로 CORS를 사용하도록 설정하고 CORS가 필요한 끝점에만 명명된 정책을 적용하면 최고의 컨트롤이 제공됩니다.

[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성은 CORS를 전역적으로 적용하는 대안을 제공합니다. 이 `[EnableCors]` 특성을 사용하면 모든 끝점이 아니라 선택한 끝점에 대해 CORS를 사용할 수 있습니다.

* `[EnableCors]`기본 정책을 지정합니다.
* `[EnableCors("{Policy String}")]`지정된 정책을 지정합니다.

특성을 `[EnableCors]` 다음에 적용할 수 있습니다.

* 면도기 페이지`PageModel`
* 컨트롤러
* 컨트롤러 작업 메서드

특성이 있는 컨트롤러, 페이지 모델 또는 작업 메서드에 다른 정책을 적용할 수 있습니다. `[EnableCors]` 특성이 `[EnableCors]` 컨트롤러, 페이지 모델 또는 작업 메서드에 적용되고 중간 웨어에서 CORS가 활성화되면 두 정책이 ***모두*** 적용됩니다. ***정책을 결합하지 않도록 하는 것이 좋습니다. *** `[EnableCors]` ***동일한 앱에서 모두 속성이나 미들웨어를*** 사용하지 않습니다.

다음 코드는 각 메서드에 다른 정책을 적용합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

다음 코드는 두 개의 CORS 정책을 만듭니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

CORS 요청을 제한하는 가장 미세한 제어를 위해:

* 명명된 정책과 함께 사용합니다. `[EnableCors("MyPolicy")]`
* 기본 정책을 정의하지 마십시오.
* [끝점 라우팅을](#ecors)사용하지 마십시오.

다음 섹션의 코드는 위의 목록을 충족합니다.

위의 코드와 유사한 코드 테스트에 대한 지침은 [CORS 테스트를](#testc) 참조하십시오.

<a name="dc"></a>

### <a name="disable-cors"></a>CORS 사용 안 함

[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성은 [끝점 라우팅에](#ecors)의해 활성화된 CORS를 사용하지 ***않도록 설정하지*** 않습니다.

다음 코드는 CORS 정책을 `"MyPolicy"`정의합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

다음 코드는 작업에 대한 `GetValues2` CORS를 사용하지 않도록 설정합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

위의 코드:

* [끝점 라우팅을](#ecors)사용하여 CORS를 활성화하지 않습니다.
* [기본 CORS 정책을](#dp)정의하지 않습니다.
* [[인에이블코르("MyPolicy")]를](#attr) `"MyPolicy"` 사용하여 컨트롤러에 대한 CORS 정책을 활성화합니다.
* 메서드에 대 한 `GetValues2` CORS를 사용 하지 않도록 설정 합니다.

앞의 코드 테스트에 대한 지침은 [CORS](#testc) 테스트를 참조하십시오.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS 정책 옵션

이 섹션에서는 CORS 정책에서 설정할 수 있는 다양한 옵션에 대해 설명합니다.

* [허용된 원원 설정](#set-the-allowed-origins)
* [허용되는 HTTP 메서드 설정](#set-the-allowed-http-methods)
* [허용된 요청 헤더 설정](#set-the-allowed-request-headers)
* [노출된 응답 헤더 설정](#set-the-exposed-response-headers)
* [원본 간 요청의 자격 증명](#credentials-in-cross-origin-requests)
* [비행 전 만료 시간 설정](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>에서 `Startup.ConfigureServices`호출됩니다. 일부 옵션의 경우 [먼저 CORS 작동 방식](#how-cors) 섹션을 읽는 것이 유용할 수 있습니다.

## <a name="set-the-allowed-origins"></a>허용된 원원 설정

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; 모든 구성표(또는)를`http` `https`사용하여 모든 원본의 CORS 요청을 허용합니다. `AllowAnyOrigin`모든 웹 *사이트가* 앱에 대한 교차 원산지 요청을 할 수 있기 때문에 안전하지 않습니다.

> [!NOTE]
> `AllowAnyOrigin` 지정하고 `AllowCredentials` 안전하지 않은 구성이며 사이트 간 요청이 위조될 수 있습니다. CORS 서비스는 앱이 두 메서드로 구성될 때 잘못된 CORS 응답을 반환합니다.

`AllowAnyOrigin`비행 전 요청 `Access-Control-Allow-Origin` 및 헤더에 영향을 줍니다. 자세한 내용은 [프리플라이트 요청](#preflight-requests) 섹션을 참조하십시오.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; 오리진이 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 허용되는지 평가할 때 원본이 구성된 와일드카드 도메인과 일치하도록 허용하는 함수로 정책의 속성을 설정합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>허용되는 HTTP 메서드 설정

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* HTTP 메서드를 허용합니다.
* 비행 전 요청 `Access-Control-Allow-Methods` 및 헤더에 영향을 줍니다. 자세한 내용은 [프리플라이트 요청](#preflight-requests) 섹션을 참조하십시오.

### <a name="set-the-allowed-request-headers"></a>허용된 요청 헤더 설정

[작성자 요청 헤더라고](https://xhr.spec.whatwg.org/#request)하는 CORS 요청에서 특정 헤더를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 보낼 수 있도록 하려면 허용된 헤더를 호출하고 지정합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

모든 작성자 요청 헤더를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>허용하려면 다음을 [호출합니다.](https://www.w3.org/TR/cors/#author-request-headers)

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`는 사전 플라이트 요청 및 [액세스 제어-요청-헤더](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) 헤더에 영향을 줍니다. 자세한 내용은 [프리플라이트 요청](#preflight-requests) 섹션을 참조하십시오.

CORS 미들웨어 정책이 지정된 특정 헤더와 일치하는 `WithHeaders` 경우 에 전송된 헤더가 에 `Access-Control-Request-Headers` `WithHeaders`명시된 헤더와 정확히 일치하는 경우에만 가능합니다.

예를 들어 다음과 같이 구성된 앱을 고려해 보십시오.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

CORS 미들웨어는 다음 요청 헤더를 사용 `Content-Language` 하 고 사전 플라이트 요청을 거부 `WithHeaders`하기 때문에[(headerNames.ContentLanguage)](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)다음에 나열 되지 않습니다.

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

앱이 *200 OK* 응답을 반환하지만 CORS 헤더를 다시 보내지 않습니다. 따라서 브라우저는 원본 간 요청을 시도하지 않습니다.

### <a name="set-the-exposed-response-headers"></a>노출된 응답 헤더 설정

기본적으로 브라우저는 모든 응답 헤더를 앱에 노출하지 않습니다. 자세한 내용은 [W3C 교차 원본 리소스 공유(용어): 간단한 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)를 참조하십시오.

기본적으로 사용할 수 있는 응답 헤더는 다음과 같습니다.

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 사양은 이러한 *헤더간단한 응답 헤더를 호출합니다.* 앱에서 다른 헤더를 사용할 수 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>있도록 하려면 다음을 호출합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>원본 간 요청의 자격 증명

자격 증명에는 CORS 요청에서 특별한 처리가 필요합니다. 기본적으로 브라우저는 원본 간 요청이 있는 자격 증명을 보내지 않습니다. 자격 증명에는 쿠키 및 HTTP 인증 스키마가 포함됩니다. 원본 간 요청이 있는 자격 증명을 보내려면 `true`클라이언트를 로 설정해야 `XMLHttpRequest.withCredentials` 합니다.

직접 `XMLHttpRequest` 사용:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

jQuery 사용:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

가져오기 [API](https://developer.mozilla.org/docs/Web/API/Fetch_API)사용 :

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

서버는 자격 증명을 허용해야 합니다. 원본 간 자격 증명을 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>허용하려면 다음을 호출하십시오.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

HTTP 응답에는 `Access-Control-Allow-Credentials` 서버가 원본 간 요청에 대한 자격 증명을 허용한다는 것을 브라우저에 알리는 헤더가 포함됩니다.

브라우저가 자격 증명을 전송하지만 응답에 유효한 `Access-Control-Allow-Credentials` 헤더가 포함되지 않으면 브라우저는 앱에 대한 응답을 노출하지 않으며 원본 간 요청이 실패합니다.

원본 간 자격 증명을 허용하는 것은 보안 위험입니다. 다른 도메인의 웹 사이트는 사용자가 모르는 새앱 한 사용자의 자격 증명을 사용자 없이 사용자를 대신하여 앱으로 보낼 수 있습니다. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

또한 CORS 사양에 `"*"` `Access-Control-Allow-Credentials` 따르면 헤더가 있는 경우 (모든 원본)에 대한 설정이 유효하지 않습니다.

<a name="pref"></a>

## <a name="preflight-requests"></a>비행 전 요청

일부 CORS 요청의 경우 브라우저는 실제 요청을 하기 전에 추가 [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 요청을 보냅니다. 이 요청을 [프리플라이트 요청이라고](https://developer.mozilla.org/docs/Glossary/Preflight_request)합니다. 브라우저는 다음 ***조건이 모두*** true인 경우 프리플라이트 요청을 건너뛸 수 있습니다.

* 요청 방법은 GET, HEAD 또는 POST입니다.
* 앱에서 요청 `Accept`헤더를 " `Accept-Language` `Content-Language` `Content-Type`또는 `Last-Event-ID`.
* 헤더(설정된 `Content-Type` 경우)에는 다음 값 중 하나가 있습니다.
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

클라이언트 요청에 대해 설정된 요청 헤더에 대한 규칙은 앱이 `setRequestHeader` `XMLHttpRequest` 개체를 호출하여 설정하는 헤더에 적용됩니다. CORS 사양은 이러한 헤더 [작성자 요청 헤더를 호출합니다.](https://www.w3.org/TR/cors/#author-request-headers) 이 규칙은 브라우저가 설정할 수 있는 헤더에 `User-Agent`적용되지 `Host`않습니다.(예: " 또는 `Content-Length`)

다음은 이 문서의 [테스트 CORS](#testc) 섹션의 **[테스트 넣기]** 단추에서 만든 프리플라이트 요청과 유사한 예제 응답입니다.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

프리플라이트 요청은 [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 방법을 사용합니다. 여기에는 다음 헤더가 포함될 수 있습니다.

* [액세스 제어- 요청 - 메서드](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): 실제 요청에 사용되는 HTTP 메서드입니다.
* [액세스 제어- 요청-헤더](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): 앱이 실제 요청에 대해 설정하는 요청 헤더 목록입니다. 앞서 설명했듯이 브라우저에서 설정하는 헤더는 `User-Agent`없습니다.
* [액세스 제어 허용 방법](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

프리플라이트 요청이 거부되면 앱은 `200 OK` 응답을 반환하지만 CORS 헤더를 설정하지 않습니다. 따라서 브라우저는 원본 간 요청을 시도하지 않습니다. 거부된 프리플라이트 요청의 예는 이 문서의 [테스트 CORS](#testc) 섹션을 참조하십시오.

F12 도구를 사용하여 콘솔 앱은 브라우저에 따라 다음 중 하나와 유사한 오류를 표시합니다.

* 파이어 폭스: 교차 원산지 요청 차단: 동일한 원본 정책에서 `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`원격 리소스를 읽을 수 없습니다. (이유: CORS 요청이 성공하지 못했습니다.) [자세히 알아보기](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* 크롬 기반: CORShttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5정책에 의해 'origin'에서https://cors3.azurewebsites.net가져오기 에 대한 액세스: 사전 비행 요청에 대한 응답은 액세스 제어 검사를 통과하지 못합니다: 요청된 리소스에 '액세스 제어-허용-원본' 헤더가 없습니다. 사용자 요구에 불명확한 응답이 제공되면 요청 모드를 'no-cors'로 설정하여 CORS가 사용되지 않도록 설정된 리소스를 가져옵니다.

특정 헤더를 허용하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>다음을 호출하십시오.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

모든 작성자 요청 헤더를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>허용하려면 다음을 [호출합니다.](https://www.w3.org/TR/cors/#author-request-headers)

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

브라우저는 설정 `Access-Control-Request-Headers`하는 방법에 일치 하지 않습니다. 다음 중 하나일 경우

* 헤더는 다른 것으로 설정됩니다.`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>호출됩니다: 지원하려는 `Accept` `Content-Type`사용자 `Origin`지정 헤더를 더한 추가 를 포함합니다.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>자동 프리플라이트 요청 코드

CORS 정책이 적용되는 경우 다음 중 하나가 적용됩니다.

* 에서 호출하여 `app.UseCors` `Startup.Configure`전역적으로.
* 특성을 `[EnableCors]` 사용 하 여.

ASP.NET 코어는 프리플라이트 옵션 요청에 응답합니다.

현재 사용 하 여 엔드 포인트 `RequireCors` 단위로 CORS를 사용 하 여 자동 사전 비행 요청을 지원 ***하지*** 않습니다.

이 문서의 [테스트 CORS](#testc) 섹션에서는 이 동작을 보여 줍니다.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[HttpOptions] 프리플라이트 요청에 대한 속성

적절한 정책으로 CORS를 사용하도록 설정하면 ASP.NET Core는 일반적으로 CORS 프리플라이트 요청에 자동으로 응답합니다. 일부 시나리오에서는 그렇지 않을 수 있습니다. 예를 들어 [끝점 라우팅과 함께 CORS를](#ecors)사용합니다.

다음 코드는 [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) 특성을 사용하여 OPTIONS 요청에 대한 끝점을 만듭니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

앞의 코드 테스트에 대한 지침은 [끝점 라우팅이 포함된 테스트 CORS 및 [HttpOptions]를](#tcer) 참조하십시오.

### <a name="set-the-preflight-expiration-time"></a>비행 전 만료 시간 설정

헤더는 `Access-Control-Max-Age` 프리플라이트 요청에 대한 응답을 캐시할 수 있는 기간(기간)을 지정합니다. 이 헤더를 설정하려면 다음을 호출하십시오. <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS 작동 방식

이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 요청에서 발생하는 작업을 설명합니다.

* CORS는 보안 기능이 **아닙니다.** CORS는 서버가 동일한 원본 정책을 완화할 수 있는 W3C 표준입니다.
  * 예를 들어 악의적인 행위자(XSS)는 사이트에 대해 [XSS(교차 사이트 스크립팅)를](xref:security/cross-site-scripting) 사용하고 CORS 지원 사이트에 대한 교차 사이트 요청을 실행하여 정보를 도용할 수 있습니다.
* CORS를 허용하면 API가 안전하지 않습니다.
  * CORS를 적용하는 것은 클라이언트(브라우저)의 결정입니다. 서버는 요청을 실행 하 고 응답을 반환, 오류를 반환 하 고 응답을 차단 하는 클라이언트입니다. 예를 들어 다음 도구 중 에는 서버 응답이 표시됩니다.
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET Http클라이언트](/dotnet/csharp/tutorials/console-webapiclient)
    * 주소 표시줄에 URL을 입력하여 웹 브라우저입니다.
* 서버가 브라우저가 금지될 수 있는 교차 원본 [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 요청을 실행할 수 있도록 허용하는 방법입니다.
  * CORS가 없는 브라우저는 원본 간 요청을 수행할 수 없습니다. CORS 이전에는 [JSONP를](https://www.w3schools.com/js/js_json_jsonp.asp) 사용하여 이러한 제한을 우회했습니다. JSONP는 XHR을 `<script>` 사용하지 않고 태그를 사용하여 응답을 수신합니다. 스크립트는 교차 원본으로 로드할 수 있습니다.

[CORS 사양은](https://www.w3.org/TR/cors/) 원본 간 요청을 가능하게 하는 몇 가지 새로운 HTTP 헤더를 도입했습니다. 브라우저가 CORS를 지원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정합니다. 사용자 지정 자바스크립트 코드는 CORS를 사용하도록 설정할 필요가 없습니다.

배포된 [샘플의](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [PUT 테스트 단추](https://cors3.azurewebsites.net/test)

다음은 [값](https://cors3.azurewebsites.net/) 테스트 단추에서 에 `https://cors1.azurewebsites.net/api/values`대한 교차 원본 요청의 예입니다. 헤더: `Origin`

* 요청을 하는 사이트의 도메인을 제공합니다.
* 호스트와 달라야 합니다.

**일반 헤더**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**응답 헤더**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**헤더 요청**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

요청에서 `OPTIONS` 서버는 응답에서 **응답 헤더 헤더를 설정합니다.** `Access-Control-Allow-Origin: {allowed origin}` 예를 들어 배포된 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [[EnableCors]](https://cors1.azurewebsites.net/test?number=2) 단추 `OPTIONS` 요청은 다음 헤더를 포함합니다.

**일반 헤더**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**응답 헤더**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**헤더 요청**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

앞의 **응답 헤더에서**서버는 응답에서 [액세스 제어-허용-원본](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 헤더를 설정합니다. 이 `https://cors1.azurewebsites.net` 헤더의 값은 `Origin` 요청의 헤더와 일치합니다.

이 호출된 경우 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> 는 와일드카드 값이 반환됩니다. `Access-Control-Allow-Origin: *` `AllowAnyOrigin`모든 원산지를 허용합니다.

응답에 헤더가 `Access-Control-Allow-Origin` 포함되지 않으면 원본 간 요청이 실패합니다. 특히 브라우저는 요청을 허용하지 않습니다. 서버가 성공적인 응답을 반환하더라도 브라우저는 클라이언트 앱에서 응답을 사용할 수 있도록 하지 않습니다.

<a name="options"></a>

### <a name="display-options-requests"></a>옵션 요청 표시

기본적으로 Chrome 및 Edge 브라우저는 F12 도구의 네트워크 탭에 옵션 요청을 표시하지 않습니다. 이러한 브라우저에서 옵션 요청을 표시하려면 다음을 수행하십시오.

* `chrome://flags/#out-of-blink-cors` 또는 `edge://flags/#out-of-blink-cors`
* 플래그를 비활성화합니다.
* 다시 시작할.

파이어 폭스는 기본적으로 옵션 요청을 보여줍니다.

## <a name="cors-in-iis"></a>IIS의 코르스

IIS에 배포할 때 서버가 익명 액세스를 허용하도록 구성되지 않은 경우 Windows 인증 전에 CORS를 실행해야 합니다. 이 시나리오를 지원하려면 [IIS CORS 모듈을](https://www.iis.net/downloads/microsoft/iis-cors-module) 앱에 대해 설치하고 구성해야 합니다.

<a name="testc"></a>

## <a name="test-cors"></a>CORS 테스트

[샘플 다운로드에는](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) CORS를 테스트하는 코드가 있습니다. [다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요. 샘플은 Razor 페이지가 추가된 API 프로젝트입니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`다운로드 샘플 [코드와](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)유사한 샘플 앱을 테스트하는 데만 사용해야 합니다.

다음은 `ValuesController` 테스트를 위한 끝점을 제공합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo는](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet 패키지에서 제공되며 경로 정보를 표시합니다.

다음 방법 중 하나를 사용하여 위의 샘플 코드를 테스트합니다.

* 에서 [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)배포된 샘플 앱을 사용합니다. 샘플을 다운로드할 필요가 없습니다.
* 의 기본 `dotnet run` URL을 사용하여 `https://localhost:5001`샘플을 실행합니다.
* 의 URL에 대 한 44398로 설정 된 `https://localhost:44398`포트와 Visual Studio에서 샘플을 실행 합니다.

F12 도구가 있는 브라우저 사용:

* **값** 단추를 선택하고 **네트워크** 탭에서 헤더를 검토합니다.
* PUT **테스트** 버튼을 선택합니다. [옵션 요청을](#options) 표시하는 방법에 대한 지침에 대한 옵션 표시 요청을 참조하십시오. **PUT 테스트는** 옵션 프리플라이트 요청과 PUT 요청의 두 가지 요청을 만듭니다.
* **`GetValues2 [DisableCors]`** 실패한 CORS 요청을 트리거하려면 단추를 선택합니다. 문서에서 설명한 대로 응답은 200개의 성공을 반환하지만 CORS 요청은 이루어지지 않습니다. **콘솔** 탭을 선택하여 CORS 오류를 확인합니다. 브라우저에 따라 다음과 유사한 오류가 표시됩니다.

     `'https://cors1.azurewebsites.net/api/values/GetValues2'` 원본에서 `'https://cors3.azurewebsites.net'` 가져오기에 대한 액세스가 CORS 정책에 의해 차단되었습니다: 요청된 리소스에 '액세스 제어-허용-원본' 헤더가 없습니다. 사용자 요구에 불명확한 응답이 제공되면 요청 모드를 'no-cors'로 설정하여 CORS가 사용되지 않도록 설정된 리소스를 가져옵니다.
     
CORS 지원 끝점은 [컬,](https://curl.haxx.se/) [피들러](https://www.telerik.com/fiddler)또는 [우체부와](https://www.getpostman.com/)같은 도구를 사용하여 테스트할 수 있습니다. 도구를 사용하는 경우 `Origin` 헤더가 지정한 요청의 출처는 요청을 받는 호스트와 달라야 합니다. 요청이 헤더 값에 따라 원본 간 이아닌 경우: *cross-origin* `Origin`

* CORS 미들웨어가 요청을 처리할 필요가 없습니다.
* CORS 헤더는 응답에서 반환되지 않습니다.

다음 명령은 `curl` 정보와 함께 OPTIONS 요청을 발행하는 데 사용됩니다.

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>끝점 라우팅 및 [HttpOptions]를 사용 하 고 CORS 테스트

현재 를 사용하여 `RequireCors` 엔드포인트별로 CORS를 사용하도록 설정하는 것은 자동 [프리플라이트 요청을](#apf)지원하지 ***않습니다.*** [CORS를 활성화하기 위해 끝점 라우팅을](#ecors)사용하는 다음 코드를 고려하십시오.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

다음은 `TodoItems1Controller` 테스트를 위한 끝점을 제공합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

배포된 [샘플의](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [테스트 페이지에서](https://cors1.azurewebsites.net/test?number=1) 이전 코드를 테스트합니다.

끝점이 프리플라이트 요청에 `[EnableCors]` 응답하고 응답하기 때문에 삭제 **[EnableCors]** 및 GET **[EnableCors] 버튼이** 성공합니다. 다른 끝점이 실패합니다. [JavaScript가](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) 다음을 전송하기 때문에 **GET** 버튼이 실패합니다.

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

다음은 `TodoItems2Controller` 유사한 끝점을 제공하지만 OPTIONS 요청에 응답하는 명시적 코드를 포함합니다.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

배포된 샘플의 [테스트 페이지에서](https://cors1.azurewebsites.net/test?number=2) 이전 코드를 테스트합니다. **컨트롤러** 드롭다운 목록에서 **프리플라이트** 를 선택한 다음 **컨트롤러 를 설정합니다.** 끝점에 대한 모든 `TodoItems2Controller` CORS 호출이 성공합니다.

## <a name="additional-resources"></a>추가 리소스

* [CORS(크로스-원본 자원 공유)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [IIS CORS 모듈 시작](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 문서에서는 ASP.NET 코어 앱에서 CORS를 사용하도록 설정하는 방법을 보여 주며, CORS를 활성화하는 방법을 보여 주시고 있습니다.

브라우저 보안은 웹 페이지가 웹 페이지를 제공한 도메인과 다른 도메인에 대한 요청을 하지 못하도록 합니다. 이 제한을 *동일한 원본 정책이라고*합니다. 동일한 원본 정책은 악의적인 사이트가 다른 사이트에서 중요한 데이터를 읽지 못하도록 합니다. 경우에 따라 다른 사이트에서 앱에 대한 원본 간 요청을 허용할 수 있습니다. 자세한 내용은 [모질라 CORS 문서를](https://developer.mozilla.org/docs/Web/HTTP/CORS)참조하십시오.

[원본 간 리소스](https://www.w3.org/TR/cors/) 공유(CORS):

* 서버가 동일한 원본 정책을 완화할 수 있는 W3C 표준입니다.
* 보안 기능이 **아니며** CORS는 보안을 완화합니다. CORS를 허용하면 API가 안전하지 않습니다. 자세한 내용은 [CORS 작동 방식을](#how-cors)참조하십시오.
* 서버가 다른 요청을 거부하면서 일부 원본 간 요청을 명시적으로 허용할 수 있습니다.
* [JSONP와](/dotnet/framework/wcf/samples/jsonp)같은 이전 기술보다 더 안전하고 유연합니다.

[샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) [방법)](xref:index#how-to-download-a-sample)

## <a name="same-origin"></a>동일한 원산지

두 URL은 동일한 구성표, 호스트 및[포트(RFC 6454)가](https://tools.ietf.org/html/rfc6454)있는 경우 동일한 출처를 갖습니다.

이 두 URL의 출처는 동일합니다.

* `https://example.com/foo.html`
* `https://example.com/bar.html`

이러한 URL의 출처는 이전 두 URL과 다릅니다.

* `https://example.net`&ndash; 다른 도메인
* `https://www.example.com/foo.html`&ndash; 다른 하위 도메인
* `http://example.com/foo.html`&ndash; 다른 구성표
* `https://example.com:9000/foo.html`&ndash; 다른 포트

인터넷 익스플로러는 출처를 비교할 때 포트를 고려하지 않습니다.

## <a name="cors-with-named-policy-and-middleware"></a>명명된 정책 및 미들웨어가 있는 CORS

CORS 미들웨어는 교차 원산지 요청을 처리합니다. 다음 코드는 지정된 원본을 가진 전체 앱에 대한 CORS를 활성화합니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

위의 코드:

* 정책 이름을 "myAllowSpecificOrigins"로\_설정합니다. 정책 이름은 임의입니다.
* CORS를 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 활성화하는 확장 메서드를 호출합니다.
* <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [람다 식으로](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)호출합니다. 람다는 개체를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 취합니다. [과](#cors-policy-options)같은 `WithOrigins`구성 옵션은 이 문서의 후반부에서 설명합니다.

메서드 <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> 호출은 앱의 서비스 컨테이너에 CORS 서비스를 추가합니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

자세한 내용은 이 문서의 [CORS 정책 옵션을](#cpo) 참조하십시오.

메서드는 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 다음 코드와 같이 메서드를 연결할 수 있습니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

참고: URL에 후행 슬래시()가`/` **포함되어서는 안 됩니다.** URL이 `/`으로 종료되면 비교가 `false` 반환되고 헤더가 반환되지 않습니다.

다음 코드는 CORS 미들웨어를 통해 모든 앱 끝점에 CORS 정책을 적용합니다.
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
참고: `UseCors` 먼저 `UseMvc`호출해야 합니다.

[Razor 페이지, 컨트롤러 및 작업 메서드에서 CORS 활성화를](#ecors) 참조하여 페이지/컨트롤러/작업 수준에서 CORS 정책을 적용합니다.

위의 코드와 유사한 코드 테스트에 대한 지침은 [CORS 테스트를](#test) 참조하십시오.

## <a name="enable-cors-with-attributes"></a>특성을 사용하여 CORS 사용

[ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성은 CORS를 전역적으로 적용하는 대안을 제공합니다. 이 `[EnableCors]` 특성을 사용하면 모든 끝점이 아니라 선택한 끝점에 대한 CORS를 사용할 수 있습니다.

기본 `[EnableCors]` 정책을 지정하고 `[EnableCors("{Policy String}")]` 정책을 지정하는 데 사용합니다.

특성을 `[EnableCors]` 다음에 적용할 수 있습니다.

* 면도기 페이지`PageModel`
* 컨트롤러
* 컨트롤러 작업 메서드

특성을 사용하여 컨트롤러/페이지 모델/작업에 다른 `[EnableCors]` 정책을 적용할 수 있습니다. 특성이 `[EnableCors]` 컨트롤러/페이지 모델/작업 메서드에 적용되고 중간 웨어에서 CORS가 활성화되면 두 정책이 ***모두*** 적용됩니다. 정책을 ***결합하지 않는*** 것이 좋습니다. 속성 `[EnableCors]` 또는 미들웨어를 사용 하 여 ***둘 다 아닙니다.** 을 `[EnableCors]`사용하는 경우 기본 정책을 **정의하지 않습니다.**

다음 코드는 각 메서드에 다른 정책을 적용합니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

다음 코드는 CORS 기본 정책과 다음 `"AnotherPolicy"`이라는 정책을 만듭니다.

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>CORS 사용 안 함

[ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성은 컨트롤러/페이지 모델/작업에 대한 CORS를 비활성화합니다.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS 정책 옵션

이 섹션에서는 CORS 정책에서 설정할 수 있는 다양한 옵션에 대해 설명합니다.

* [허용된 원원 설정](#set-the-allowed-origins)
* [허용되는 HTTP 메서드 설정](#set-the-allowed-http-methods)
* [허용된 요청 헤더 설정](#set-the-allowed-request-headers)
* [노출된 응답 헤더 설정](#set-the-exposed-response-headers)
* [원본 간 요청의 자격 증명](#credentials-in-cross-origin-requests)
* [비행 전 만료 시간 설정](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>에서 `Startup.ConfigureServices`호출됩니다. 일부 옵션의 경우 [먼저 CORS 작동 방식](#how-cors) 섹션을 읽는 것이 유용할 수 있습니다.

## <a name="set-the-allowed-origins"></a>허용된 원원 설정

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; 모든 구성표(또는)를`http` `https`사용하여 모든 원본의 CORS 요청을 허용합니다. `AllowAnyOrigin`모든 웹 *사이트가* 앱에 대한 교차 원산지 요청을 할 수 있기 때문에 안전하지 않습니다.

> [!NOTE]
> `AllowAnyOrigin` 지정하고 `AllowCredentials` 안전하지 않은 구성이며 사이트 간 요청이 위조될 수 있습니다. 보안 앱의 경우 클라이언트가 서버 리소스에 액세스하도록 권한을 부여해야 하는 경우 정확한 원본 목록을 지정합니다.

`AllowAnyOrigin`비행 전 요청 `Access-Control-Allow-Origin` 및 헤더에 영향을 줍니다. 자세한 내용은 [프리플라이트 요청](#preflight-requests) 섹션을 참조하십시오.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; 오리진이 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 허용되는지 평가할 때 원본이 구성된 와일드카드 도메인과 일치하도록 허용하는 함수로 정책의 속성을 설정합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>허용되는 HTTP 메서드 설정

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* HTTP 메서드를 허용합니다.
* 비행 전 요청 `Access-Control-Allow-Methods` 및 헤더에 영향을 줍니다. 자세한 내용은 [프리플라이트 요청](#preflight-requests) 섹션을 참조하십시오.

### <a name="set-the-allowed-request-headers"></a>허용된 요청 헤더 설정

*작성자 요청 헤더라고*하는 CORS 요청에서 특정 헤더를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 보낼 수 있도록 하려면 허용된 헤더를 호출하고 지정합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

모든 작성자 요청 헤더를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>허용하려면 다음을 호출합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

이 설정은 프리플라이트 `Access-Control-Request-Headers` 요청 및 헤더에 영향을 줍니다. 자세한 내용은 [프리플라이트 요청](#preflight-requests) 섹션을 참조하십시오.

CORS 미들웨어는 항상 CorsPolicy.Headers에서 구성된 값에 `Access-Control-Request-Headers` 관계없이 4개의 헤더를 보낼 수 있습니다. 이 헤더 목록에는 다음이 포함됩니다.

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

예를 들어 다음과 같이 구성된 앱을 고려해 보십시오.

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS 미들웨어는 항상 화이트리스트가 있기 때문에 `Content-Language` 다음 요청 헤더를 사용하여 프리플라이트 요청에 성공적으로 응답합니다.

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>노출된 응답 헤더 설정

기본적으로 브라우저는 모든 응답 헤더를 앱에 노출하지 않습니다. 자세한 내용은 [W3C 교차 원본 리소스 공유(용어): 간단한 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)를 참조하십시오.

기본적으로 사용할 수 있는 응답 헤더는 다음과 같습니다.

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 사양은 이러한 *헤더간단한 응답 헤더를 호출합니다.* 앱에서 다른 헤더를 사용할 수 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>있도록 하려면 다음을 호출합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>원본 간 요청의 자격 증명

자격 증명에는 CORS 요청에서 특별한 처리가 필요합니다. 기본적으로 브라우저는 원본 간 요청이 있는 자격 증명을 보내지 않습니다. 자격 증명에는 쿠키 및 HTTP 인증 스키마가 포함됩니다. 원본 간 요청이 있는 자격 증명을 보내려면 `true`클라이언트를 로 설정해야 `XMLHttpRequest.withCredentials` 합니다.

직접 `XMLHttpRequest` 사용:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

jQuery 사용:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

가져오기 [API](https://developer.mozilla.org/docs/Web/API/Fetch_API)사용 :

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

서버는 자격 증명을 허용해야 합니다. 원본 간 자격 증명을 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>허용하려면 다음을 호출하십시오.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

HTTP 응답에는 `Access-Control-Allow-Credentials` 서버가 원본 간 요청에 대한 자격 증명을 허용한다는 것을 브라우저에 알리는 헤더가 포함됩니다.

브라우저가 자격 증명을 전송하지만 응답에 유효한 `Access-Control-Allow-Credentials` 헤더가 포함되지 않으면 브라우저는 앱에 대한 응답을 노출하지 않으며 원본 간 요청이 실패합니다.

원본 간 자격 증명을 허용하는 것은 보안 위험입니다. 다른 도메인의 웹 사이트는 사용자가 모르는 새앱 한 사용자의 자격 증명을 사용자 없이 사용자를 대신하여 앱으로 보낼 수 있습니다. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

또한 CORS 사양에 `"*"` `Access-Control-Allow-Credentials` 따르면 헤더가 있는 경우 (모든 원본)에 대한 설정이 유효하지 않습니다.

### <a name="preflight-requests"></a>비행 전 요청

일부 CORS 요청의 경우 브라우저는 실제 요청을 하기 전에 추가 요청을 보냅니다. 이 요청을 *프리플라이트 요청이라고*합니다. 브라우저는 다음과 같은 조건이 true인 경우 프리플라이트 요청을 건너뛸 수 있습니다.

* 요청 방법은 GET, HEAD 또는 POST입니다.
* 앱에서 요청 `Accept`헤더를 " `Accept-Language` `Content-Language` `Content-Type`또는 `Last-Event-ID`.
* 헤더(설정된 `Content-Type` 경우)에는 다음 값 중 하나가 있습니다.
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

클라이언트 요청에 대해 설정된 요청 헤더에 대한 규칙은 앱이 `setRequestHeader` `XMLHttpRequest` 개체를 호출하여 설정하는 헤더에 적용됩니다. CORS 사양은 이러한 헤더 *작성자 요청 헤더를 호출합니다.* 이 규칙은 브라우저가 설정할 수 있는 헤더에 `User-Agent`적용되지 `Host`않습니다.(예: " 또는 `Content-Length`)

다음은 프리플라이트 요청의 예입니다.

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

비행 전 요청은 HTTP OPTIONS 방법을 사용합니다. 여기에는 두 개의 특수 헤더가 포함되어 있습니다.

* `Access-Control-Request-Method`: 실제 요청에 사용할 HTTP 메서드입니다.
* `Access-Control-Request-Headers`: 앱이 실제 요청에 대해 설정하는 요청 헤더 목록입니다. 앞서 설명했듯이 브라우저에서 설정하는 헤더는 `User-Agent`없습니다.

<!-- I think this needs to be removed, was put here accidently -->

적절한 정책으로 CORS를 사용하도록 설정하면 ASP.NET Core는 일반적으로 CORS 프리플라이트 요청에 자동으로 응답합니다. [비행 전 요청에 대한 [HttpOptions] 특성을](#pro)참조하십시오.

CORS 프리플라이트 요청에는 `Access-Control-Request-Headers` 실제 요청과 함께 전송되는 헤더를 서버에 나타내는 헤더가 포함될 수 있습니다.

특정 헤더를 허용하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>다음을 호출하십시오.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

모든 작성자 요청 헤더를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>허용하려면 다음을 호출합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

브라우저는 설정 `Access-Control-Request-Headers`하는 방법에 완전히 일치 하지 않습니다. 헤더를 (또는 `"*"` 사용) <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>이외의 것으로 설정하는 경우 최소한 `Accept` `Content-Type`을 `Origin`포함해야 합니다.

다음은 사전 비행 요청에 대한 예제 응답입니다(서버가 요청을 허용한다고 가정).

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

응답에는 허용된 메서드를 `Access-Control-Allow-Methods` 나열하는 `Access-Control-Allow-Headers` 헤더와 허용된 헤더를 나열하는 헤더가 포함됩니다. 프리플라이트 요청이 성공하면 브라우저는 실제 요청을 보냅니다.

프리플라이트 요청이 거부되면 앱은 *200 확인* 응답을 반환하지만 CORS 헤더를 다시 보내지 않습니다. 따라서 브라우저는 원본 간 요청을 시도하지 않습니다.

### <a name="set-the-preflight-expiration-time"></a>비행 전 만료 시간 설정

헤더는 `Access-Control-Max-Age` 프리플라이트 요청에 대한 응답을 캐시할 수 있는 기간(기간)을 지정합니다. 이 헤더를 설정하려면 다음을 호출하십시오. <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS 작동 방식

이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 요청에서 발생하는 작업을 설명합니다.

* CORS는 보안 기능이 **아닙니다.** CORS는 서버가 동일한 원본 정책을 완화할 수 있는 W3C 표준입니다.
  * 예를 들어 악의적인 행위자(XSS)는 사이트에 대해 [XSS 간 간 스크립팅 방지를](xref:security/cross-site-scripting) 사용하고 CORS 지원 사이트에 대한 교차 사이트 요청을 실행하여 정보를 도용할 수 있습니다.
* CORS를 허용하면 API가 안전하지 않습니다.
  * CORS를 적용하는 것은 클라이언트(브라우저)의 결정입니다. 서버는 요청을 실행 하 고 응답을 반환, 오류를 반환 하 고 응답을 차단 하는 클라이언트입니다. 예를 들어 다음 도구 중 에는 서버 응답이 표시됩니다.
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET Http클라이언트](/dotnet/csharp/tutorials/console-webapiclient)
    * 주소 표시줄에 URL을 입력하여 웹 브라우저입니다.
* 서버가 브라우저가 금지될 수 있는 교차 원본 [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 요청을 실행할 수 있도록 허용하는 방법입니다.
  * CORS가 없는 브라우저는 원본 간 요청을 수행할 수 없습니다. CORS 이전에는 [JSONP를](https://www.w3schools.com/js/js_json_jsonp.asp) 사용하여 이러한 제한을 우회했습니다. JSONP는 XHR을 `<script>` 사용하지 않고 태그를 사용하여 응답을 수신합니다. 스크립트는 교차 원본으로 로드할 수 있습니다.

[CORS 사양은](https://www.w3.org/TR/cors/) 원본 간 요청을 가능하게 하는 몇 가지 새로운 HTTP 헤더를 도입했습니다. 브라우저가 CORS를 지원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정합니다. 사용자 지정 자바스크립트 코드는 CORS를 사용하도록 설정할 필요가 없습니다.

다음은 원본 간 요청의 예입니다. 헤더는 `Origin` 요청을 하는 사이트의 도메인을 제공합니다. `Origin` 헤더는 필수이며 호스트와 달라야 합니다.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

서버가 요청을 허용하면 응답에서 `Access-Control-Allow-Origin` 헤더를 설정합니다. 이 헤더의 값은 `Origin` 요청의 헤더와 일치하거나 와일드카드 `"*"`값으로 모든 원본이 허용됨을 의미합니다.

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

응답에 헤더가 `Access-Control-Allow-Origin` 포함되지 않으면 원본 간 요청이 실패합니다. 특히 브라우저는 요청을 허용하지 않습니다. 서버가 성공적인 응답을 반환하더라도 브라우저는 클라이언트 앱에서 응답을 사용할 수 있도록 하지 않습니다.

<a name="test"></a>

## <a name="test-cors"></a>CORS 테스트

CORS를 테스트하려면 다음을 수행하십시오.

1. [API 프로젝트를 만듭니다.](xref:tutorials/first-web-api) 또는 [샘플을 다운로드할](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)수 있습니다.
1. 이 문서의 방법 중 하나를 사용하여 CORS를 사용하도록 설정합니다. 예를 들어:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`다운로드 샘플 [코드와](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)유사한 샘플 앱을 테스트하는 데만 사용해야 합니다.

1. 웹 앱 프로젝트(Razor 페이지 또는 MVC)를 만듭니다. 이 샘플에서는 면도기 페이지를 사용합니다. API 프로젝트와 동일한 솔루션에서 웹 앱을 만들 수 있습니다.
1. *Index.cshtml* 파일에 다음 강조 표시된 코드를 추가합니다.

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. 앞의 코드에서 배포된 앱의 URL로 바꿉니다. `url: 'https://<web app>.azurewebsites.net/api/values/1',`
1. API 프로젝트를 배포합니다. 예를 들어 [Azure 에 배포합니다.](xref:host-and-deploy/azure-apps/index)
1. 바탕 화면에서 Razor 페이지 또는 MVC 앱을 실행하고 **테스트** 버튼을 클릭합니다. F12 도구를 사용하여 오류 메시지를 검토합니다.
1. 로컬 호스트 원본을 `WithOrigins` 제거하고 앱을 배포합니다. 또는 다른 포트를 통해 클라이언트 앱을 실행합니다. 예를 들어 Visual Studio에서 실행합니다.
1. 클라이언트 앱으로 테스트합니다. CORS 오류는 오류를 반환하지만 오류 메시지는 JavaScript에서 사용할 수 없습니다. F12 도구의 콘솔 탭을 사용하여 오류를 확인합니다. 브라우저에 따라 다음과 유사한 오류가 발생합니다(F12 도구 콘솔에서).

   * 마이크로 소프트 에지 사용 :

     **SEC7120: [CORS] `https://localhost:44375` 원본에서 `https://localhost:44375` 원본간 리소스에 대한 액세스 제어-허용-원본 원본 응답 헤더에서 원본을 찾지 못했습니다.`https://webapi.azurewebsites.net/api/values/1`**

   * 크롬 사용:

     **원본에서 `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` XMLHttpRequest에 대한 액세스가 CORS 정책에 의해 차단되었습니다: 요청된 리소스에 '액세스 제어-허용-원본' 헤더가 없습니다.**
     
CORS 지원 엔드포인트는 [Fiddler](https://www.telerik.com/fiddler) 또는 [Postman](https://www.getpostman.com/)과 같은 도구를 사용하여 테스트할 수 있습니다. 도구를 사용하는 경우 `Origin` 헤더가 지정한 요청의 출처는 요청을 받는 호스트와 달라야 합니다. 요청이 헤더 값에 따라 원본 간 이아닌 경우: *cross-origin* `Origin`

* CORS 미들웨어가 요청을 처리할 필요가 없습니다.
* CORS 헤더는 응답에서 반환되지 않습니다.

## <a name="cors-in-iis"></a>IIS의 코르스

IIS에 배포할 때 서버가 익명 액세스를 허용하도록 구성되지 않은 경우 Windows 인증 전에 CORS를 실행해야 합니다. 이 시나리오를 지원하려면 [IIS CORS 모듈을](https://www.iis.net/downloads/microsoft/iis-cors-module) 앱에 대해 설치하고 구성해야 합니다.

## <a name="additional-resources"></a>추가 리소스

* [CORS(크로스-원본 자원 공유)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [IIS CORS 모듈 시작](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
