---
title: ASP.NET Core에서 원본 간 요청 (CORS)를 사용 하도록 설정
author: rick-anderson
description: 에 대해 알아봅니다 어떻게 CORS 허용 하거나 거부 하는 ASP.NET Core 앱에서 크로스-원본 요청에 대 한 표준으로 합니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: security/cors
ms.openlocfilehash: 655d9be894c677f8adf0fecc2b465d5ae7af2b61
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897470"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>ASP.NET Core에서 원본 간 요청 (CORS)를 사용 하도록 설정

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 문서에서는 ASP.NET Core 앱에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.

브라우저 보안 요청을 웹 페이지를 제공 하는 다른 도메인에서 웹 페이지를 방지 합니다. 이 제한은 라고 합니다 *동일 원본 정책*합니다. 동일 원본 정책에서 다른 사이트의 중요 한 데이터를 읽을 악성 사이트를 방지 합니다. 경우에 따라 다음 다른 사이트 앱 크로스-원본 요청을 허용 하는 것이 좋습니다. 자세한 내용은 참조는 [Mozilla CORS 문서](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)합니다.

[원본 간 리소스 공유](https://www.w3.org/TR/cors/) (CORS):

* W3C은 동일 원본 정책을 완화 하는 서버를 허용 하는 표준입니다.
* 됩니다 **되지** CORS 보안 기능으로 보안을 완화 합니다. API는 CORS 허용 하 여 안전 하 게 아닙니다. 자세한 내용은 [CORS 어떻게 작동](#how-cors)합니다.
* 명시적으로 다른 사용자를 거부 하는 동안 일부 원본 간 요청을 허용 하도록 서버를 허용 합니다.
* 안전 하 고 이전 기술 보다 더 유연한 같은입니다 [JSONP](/dotnet/framework/wcf/samples/jsonp)합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>동일한 원본

동일한 구성표, 호스트 및 포트에 있는 경우 두 개의 Url가 동일한 원본 ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

다음 두 URL은 동일한 원본입니다.

* `https://example.com/foo.html`
* `https://example.com/bar.html`

이러한 Url에 이전 두 Url 보다 다양 한 원본:

* `https://example.net` &ndash; 다른 도메인
* `https://www.example.com/foo.html` &ndash; 다른 하위 도메인
* `http://example.com/foo.html` &ndash; 다른 구성표
* `https://example.com:9000/foo.html` &ndash; 다른 포트

Internet Explorer는 원본을 비교할 때 포트를 고려하지 않습니다.

## <a name="cors-with-named-policy-and-middleware"></a>명명 된 정책 및 미들웨어를 사용 하 여 CORS

CORS 미들웨어는 크로스-원본 요청을 처리합니다. 다음 코드는 지정 된 origin 사용 하 여 전체 앱에 대 한 CORS를 사용 하면:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

위의 코드는:

* 정책 이름 설정 "\_myAllowSpecificOrigins"입니다. 정책 이름은 임의로 지정 됩니다.
* 호출 된 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> CORS를 사용 하도록 설정 하는 확장 메서드.
* 호출 <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> 사용 하 여는 [람다 식](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)합니다. 람다는 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 개체를 전달받습니다. [구성 옵션](#cors-policy-options)와 같은 `WithOrigins`,이 문서의 뒷부분에 설명 되어 있습니다.

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> CORS 서비스 앱의 서비스 컨테이너를 추가 하는 메서드를 호출 합니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

자세한 내용은 [CORS 정책 옵션](#cpo) 이 문서의.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 메서드는 다음 코드와 같이 메서드를 연결할 수 있습니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

다음 강조 표시 된 코드에는 CORS 미들웨어를 통해 모든 앱 끝점에 CORS 정책 적용 됩니다.

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

참조 [Razor 페이지, 컨트롤러 및 작업 메서드에서 CORS를 사용 하도록 설정](#ecors) 페이지/컨트롤러/작업 수준에서 CORS 정책을 적용 합니다.

참고:

* `UseCors` 먼저 호출 해야 `UseMvc`합니다.
* URL은 **되지** 후행 슬래시를 포함 (`/`). URL을 사용 하 여 종료 되 면 `/`, 비교 반환 `false` 헤더가 없으면 반환 됩니다.

참조 [테스트 CORS](#test) 앞의 코드를 테스트에 대 한 지침은 합니다.

<a name="ecors"></a>

## <a name="enable-cors-with-attributes"></a>특성을 사용 하 여 CORS를 사용 하도록 설정

합니다 [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성을 전역적으로 CORS를 적용 하는 대신 제공 합니다. `[EnableCors]` 특성 모든 끝점 보다는 선택한 끝점에 대 한 CORS를 사용 하도록 설정 합니다.

사용 하 여 `[EnableCors]` 기본 정책을 지정 하 고 `[EnableCors("{Policy String}")]` 정책을 지정 하려면.

`[EnableCors]` 특성을 적용할 수 있습니다.

* Razor 페이지 `PageModel`
* 컨트롤러
* 컨트롤러 동작 메서드

컨트롤러/페이지-모델/작업과 다른 정책을 적용할 수는 `[EnableCors]` 특성입니다. 경우는 `[EnableCors]` 컨트롤러/페이지-모델/작업 메서드에 특성이 적용 되 고 CORS는 미들웨어에서 사용 하도록 설정, 두 정책이 모두 적용 됩니다. 정책 결합에 대 한 것이 좋습니다. 사용 된 `[EnableCors]` 특성 또는 둘 다 동일한 앱에서 미들웨어입니다.

각 메서드에 다음 코드에 다른 정책을 적용 됩니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

다음 코드에서는 CORS 기본 정책 및 정책 이라는 `"AnotherPolicy"`:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>CORS 비활성시키기

합니다 [ &lbrack;하도록 설정 하려면 DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성 컨트롤러/페이지-모델/작업에 대 한 CORS를 해제 합니다.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS 정책 옵션

이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션을 설명 합니다.

* [허용되는 원본 설정하기](#set-the-allowed-origins)
* [허용되는 HTTP 메서드 설정하기](#set-the-allowed-http-methods)
* [허용되는 요청 헤더 설정하기](#set-the-allowed-request-headers)
* [노출되는 응답 헤더 설정하기](#set-the-exposed-response-headers)
* [교차 원본 요청의 자격 증명](#credentials-in-cross-origin-requests)
* [예비 요청 만료 시간 설정하기](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 라고 `Startup.ConfigureServices`합니다. 일부 옵션에 대 한 읽기 하면 도움이 될 합니다 [CORS 어떻게 작동](#how-cors) 먼저 섹션입니다.

## <a name="set-the-allowed-origins"></a>허용되는 원본 설정하기

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; 임의의 체계를 사용 하 여 모든 원본에서 CORS 요청할 수 있습니다 (`http` 또는 `https`). `AllowAnyOrigin` 안전 하지 않은 때문에 *웹 사이트* 앱에 크로스-원본 요청을 수행할 수 있습니다.

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> 지정 `AllowAnyOrigin` 고 `AllowCredentials` 는 안전 하지 않은 구성 및 교차 사이트 요청 위조 될 수 있습니다. CORS 서비스 앱 모두 메서드를 사용 하 여 구성 된 경우 잘못 된 CORS 응답을 반환 합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> 지정 `AllowAnyOrigin` 고 `AllowCredentials` 는 안전 하지 않은 구성 및 교차 사이트 요청 위조 될 수 있습니다. 보안 앱의 경우 클라이언트 자체 서버 리소스에 액세스 권한을 부여 해야 하는 경우는 정확한 원본 목록이 올바르면을 지정 합니다.

::: moniker-end

<!-- REVIEW required
I changed from
Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration. **This** setting affects preflight requests and the ...
to
**`AllowAnyOrigin`** affects preflight requests and the

to remove the ambiguous **This**.
-->

`AllowAnyOrigin` 영향을 줍니다 요청 실행 전 및 `Access-Control-Allow-Origin` 헤더입니다. 자세한 내용은 참조는 [요청을 실행 전](#preflight-requests) 섹션입니다.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; 집합의 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 원본 허용 되는 경우 평가할 때 구성 된 와일드 카드 도메인에 맞게 원본을 허용 하는 함수를 정책의 속성입니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-104&highlight=4)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a>허용되는 HTTP 메서드 설정하기

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* 모든 HTTP 메서드를 사용할 수 있습니다.
* 영향을 줍니다 요청 실행 전 및 `Access-Control-Allow-Methods` 헤더입니다. 자세한 내용은 참조는 [요청을 실행 전](#preflight-requests) 섹션입니다.

### <a name="set-the-allowed-request-headers"></a>허용되는 요청 헤더 설정하기

특정 헤더를 CORS 요청을 보낼 수 있도록 호출 *요청 헤더를 작성*, 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 허용 되는 헤더를 지정 합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

요청 헤더에 모든 author 수 있도록 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

이 설정은 실행 전 요청 및 `Access-Control-Request-Headers` 헤더입니다. 자세한 내용은 참조는 [요청을 실행 전](#preflight-requests) 섹션입니다.

::: moniker range=">= aspnetcore-2.2"

지정 된 특정 헤더에 CORS 미들웨어 정책 일치가 `WithHeaders` 은 헤더 전송 하는 경우에 가능 `Access-Control-Request-Headers` 에 명시 된 헤더와 정확히 일치 `WithHeaders`합니다.

예를 들어 다음과 같이 구성 된 앱을 고려해 야 합니다.

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS 미들웨어 때문에 다음 요청 헤더를 사용 하 여 실행 전 요청을 거절 `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage))에 나열 되지 않은 `WithHeaders`:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

앱 반환을 *200 확인* 응답 하지만 CORS 헤더를 다시 보내지 않습니다. 따라서 브라우저는 크로스-원본 요청을 시도 하지 않습니다.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS 미들웨어의 네 가지 헤더를 항상 허용 된 `Access-Control-Request-Headers` CorsPolicy.Headers에 구성 된 값에 관계 없이 전송 됩니다. 이 헤더 목록에는 다음이 포함 됩니다.

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

예를 들어 다음과 같이 구성 된 앱을 고려해 야 합니다.

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS 미들웨어가 성공적으로 응답 다음 요청 헤더를 사용 하 여 실행 전 요청에 있으므로 `Content-Language` 은 항상 허용 목록에 추가 합니다.

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a>노출되는 응답 헤더 설정하기

브라우저는 기본적으로 모든 앱에 대 한 응답 헤더를 노출 하지 합니다. 자세한 내용은 참조 하세요. [W3C 크로스-원본 자원 공유 (용어): 간단한 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)합니다.

기본적으로 사용 가능한 응답 헤더들은 다음과 같습니다.

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

이러한 헤더를 호출 하는 CORS 사양 *간단한 응답 헤더*합니다. 다른 헤더를 사용할 수 있도록 앱을 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>교차 원본 요청의 자격 증명

CORS 요청에서 자격 증명(Credentials)은 특별한 처리를 해야 합니다. 기본적으로 브라우저는 크로스-원본 요청을 사용 하 여 자격 증명을 보내지 않습니다. 자격 증명 쿠키 및 HTTP 인증 체계를 포함합니다. 크로스-원본 요청을 사용 하 여 자격 증명을 보내려면 클라이언트 설정 해야 합니다 `XMLHttpRequest.withCredentials` 에 `true`입니다.

사용 하 여 `XMLHttpRequest` 직접.

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

JQuery를 사용합니다.

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

사용 하 여 [API를 가져올](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

서버 자격 증명을 허용 해야 합니다. 크로스-원본 자격 증명을 허용 하려면 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

HTTP 응답에 포함 됩니다는 `Access-Control-Allow-Credentials` 서버 크로스-원본 요청에 대 한 자격 증명을 허용 하는지 브라우저에 지시 하는 헤더입니다.

경우 브라우저는 자격 증명을 보내지만 응답 유효한 없는 `Access-Control-Allow-Credentials` 헤더, 브라우저 앱에 대 한 응답을 제공 하지 않습니다 및 크로스-원본 요청에 실패 합니다.

크로스-원본 자격 증명을 허용 하는 것은 보안상 위험 합니다. 다른 도메인에서 웹 사이트는 사용자의 지식 없이 사용자를 대신 하 여 앱에는 로그인 한 사용자의 자격 증명을 보낼 수 있습니다. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS 사양도 해당 설정에 따라 원본이 `"*"` (모든 원본) 올바르지 않습니다 경우는 `Access-Control-Allow-Credentials` 헤더가 있습니다.

### <a name="preflight-requests"></a>실행 전 요청

브라우저는 일부 CORS 요청에 대 한 실제 요청 하기 전에 추가 요청을 보냅니다. 이 요청에서 호출 되는 *실행 전 요청*합니다. 다음 조건이 true 인 경우 브라우저가 실행 전 요청을 건너뛸 수 있습니다.:

* 요청 메서드가 GET, HEAD 또는 POST 됩니다.
* 앱 이외의 요청 헤더를 설정 하지 않는 `Accept`, `Accept-Language`를 `Content-Language`를 `Content-Type`, 또는 `Last-Event-ID`합니다.
* `Content-Type` 헤더 경우 설정에 다음 값 중 하나:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

호출 하 여 앱을 설정 하는 헤더에 적용 되는 클라이언트 요청에 대 한 요청 헤더에는 규칙 집합 `setRequestHeader` 에 `XMLHttpRequest` 개체입니다. 이러한 헤더를 호출 하는 CORS 사양 *요청 헤더를 작성*합니다. 헤더와 같은 브라우저 설정 수에 규칙 적용 되지 않습니다 `User-Agent`하십시오 `Host`, 또는 `Content-Length`합니다.

다음은 실행 전 요청의 예입니다.

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

HTTP OPTIONS 메서드를 사용 하는 사전 요청 합니다. 두 가지 특수 헤더를 포함합니다.

* `Access-Control-Request-Method`: 실제 요청에 사용 될 HTTP 메서드.
* `Access-Control-Request-Headers`: 실제 요청에서 앱을 설정 하는 요청 헤더의 목록입니다. 여기와 같은 브라우저 설정 하는 헤더가 포함 되지 않습니다 앞에서 설명한 대로 `User-Agent`입니다.

CORS 실행 전 요청에 포함 될 수 있습니다는 `Access-Control-Request-Headers` 헤더로 실제 요청과 함께 전송 되는 헤더를 서버에 알립니다.

특정 헤더를 허용 하려면 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

요청 헤더에 모든 author 수 있도록 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

브라우저 설정 하는 방법에 전적으로 일관 되지 `Access-Control-Request-Headers`합니다. 이외의 헤더 값으로 설정 하면 `"*"` (사용할지 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>)를 하나 이상 포함 해야 `Accept`, `Content-Type`, 및 `Origin`, 지원 하려는 모든 사용자 지정 헤더 및.

다음은 실행 전 요청 (서버 요청을 허용 하는지 가정할 경우)에 대 한 예제 응답입니다.

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

응답에 포함 되어는 `Access-Control-Allow-Methods` 허용 되는 메서드를 나열 하는 헤더 및 필요에 따라는 `Access-Control-Allow-Headers` 허용 되는 헤더를 나열 하는 헤더입니다. 실행 전 요청이 성공 하면 브라우저는 실제 요청을 보냅니다.

실행 전 요청이 거부 되 면 앱에서 반환 된 *200 확인* 응답 CORS 헤더를 다시 보내지 않습니다. 하지만 합니다. 따라서 브라우저는 크로스-원본 요청을 시도 하지 않습니다.

### <a name="set-the-preflight-expiration-time"></a>예비 요청 만료 시간 설정하기

`Access-Control-Max-Age` 헤더 실행 전 요청에 응답을 캐시할 수 기간을 지정 합니다. 이 헤더를 설정 하려면 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS의 동작 방식

이 섹션에서는 수행 되는 작업에 대해 설명 합니다.는 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) HTTP 메시지 수준에서 요청 합니다.

* CORS는 **되지** 보안 기능입니다. CORS는 서버를 동일 원본 정책을 완화할 수 있는 W3C 표준입니다.
  * 예를 들어, 악의적인 행위자가 사용할 수 [되지 않도록 사이트 간 스크립팅 (XSS)](xref:security/cross-site-scripting) 사이트에 대 한 정보를 도용 하는 사용 하도록 설정 하는 CORS 사이트로 교차 사이트 요청을 실행 하 고 있습니다.
* API는 CORS 허용 하 여 안전 하 게 아닙니다.
  * CORS를 적용 하는 클라이언트 (브라우저)에 달려 있습니다. 서버 요청을 실행 하 고 응답을 반환 합니다, 그리고 오류가 및 블록 응답을 반환 하는 클라이언트입니다. 예를 들어 서버 응답을 표시는 다음 도구 중 하나:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * 주소 표시줄에 URL을 입력 하 여 웹 브라우저.
* 크로스-원본을 실행 하는 브라우저를 허용 하도록 서버에 대 한 방법을 것 [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 또는 [인출 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 요청 금지 될 것입니다.
  * 브라우저 (CORS) 없이 크로스-원본 요청을 수행할 수 없습니다. CORS를 하기 전에 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 이 제한 사항을 회피할 하는 데 사용 되었습니다. JSONP XHR를 사용 하지 않는 사용 하 여는 `<script>` 태그를 응답을 수신 합니다. 스크립트는 로드 된 크로스-원본 될 수 있습니다.

합니다 [CORS 사양](https://www.w3.org/TR/cors/) 크로스-원본 요청을 사용 하도록 설정 하는 몇 가지 새로운 HTTP 헤더를 도입 합니다. 브라우저에서 CORS를 지 원하는 경우 이러한 머리글 크로스-원본 요청을 자동으로 설정 합니다. CORS를 사용 하도록 사용자 지정 JavaScript 코드 필요는 없습니다.

다음은 원본 간 요청의 예입니다. `Origin` 헤더는 요청 하는 사이트의 도메인을 제공 합니다.

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

서버 요청을 허용 하는 경우에 설정의 `Access-Control-Allow-Origin` 헤더를 응답 합니다. 이 헤더의 값이 일치 하거나 합니다 `Origin` 요청의 헤더 또는 와일드 카드 값 `"*"`, 모든 원본을 허용 되는 의미:

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

응답에 포함 되지 않은 경우는 `Access-Control-Allow-Origin` 헤더를 크로스-원본 요청에 실패 합니다. 특히 브라우저 요청을 허용 하지 않습니다. 서버 성공적인 응답을 반환 하는 경우에 브라우저 되어도 응답이 클라이언트 앱에 사용할 수 있습니다.

<a name="test"></a>

## <a name="test-cors"></a>CORS 테스트

CORS 테스트:

1. [API 프로젝트를 만들고](xref:tutorials/first-web-api)합니다. 또는 수 있습니다 [샘플을 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)합니다.
1. 이 문서의 방법 중 하나를 사용 하 여 CORS를 사용 하도록 설정 합니다. 예를 들어:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` 유사한 샘플 앱을 테스트용만 사용 해야 합니다 [샘플 코드 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)합니다.

1. (Razor 페이지나 MVC) 웹 앱 프로젝트를 만듭니다. 이 샘플에서는 Razor 페이지를 사용 합니다. API 프로젝트와 동일한 솔루션에서 웹 앱을 만들 수 있습니다.
1. 다음 강조 표시 된 코드를 추가 합니다 *Index.cshtml* 파일:

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. 위의 코드에서 `url: 'https://<web app>.azurewebsites.net/api/values/1',` 배포 된 앱에 대 한 URL로 합니다.
1. API 프로젝트를 배포 합니다. 예를 들어 [Azure에 배포](xref:host-and-deploy/azure-apps/index)합니다.
1. 바탕 화면에서 Razor 페이지 또는 MVC 앱을 실행 하 고 클릭 합니다 **테스트** 단추입니다. 오류 메시지를 검토 하려면 F12 도구를 사용 합니다.
1. localhost 원점 제거 `WithOrigins` 및 앱을 배포 합니다. 또는 다른 포트를 사용 하 여 클라이언트 앱을 실행 합니다. 예를 들어, Visual Studio에서 실행 합니다.
1. 클라이언트 앱을 사용 하 여 테스트 합니다. CORS 오류는 오류를 반환 하지만 오류 메시지를 JavaScript에 사용할 수 없습니다. F12 도구 콘솔 탭을 사용 하 여 오류를 참조 하세요. 브라우저에 따라 오류가 발생 (F12 도구 콘솔)에서 다음과 유사 합니다.

   * Microsoft Edge를 사용합니다.

     **SEC7120: [CORS] 원점 `https://localhost:44375` 찾지 `https://localhost:44375` 에서 크로스-원본 자원에 대 한 액세스 제어-허용-원본 응답 헤더 `https://webapi.azurewebsites.net/api/values/1`**

   * Chrome을 사용합니다.

     **XMLHttpRequest에 대 한 액세스 `https://webapi.azurewebsites.net/api/values/1` 원본의 `https://localhost:44375` CORS 정책에 의해 차단 되었습니다. 요청된 된 리소스에을 ' 액세스 제어-허용-원본 ' 헤더가 없습니다.**

## <a name="additional-resources"></a>추가 자료

* [크로스-원본 자원 공유 (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
