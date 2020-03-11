---
title: ASP.NET Core에서 CORS (원본 간 요청) 사용
author: rick-anderson
description: ASP.NET Core 앱에서 원본 간 요청을 허용 하거나 거부 하기 위한 표준으로 CORS를 학습 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: e0e0e1abf1ecaa12038b3ee1bdaa384d979be254
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654447"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>ASP.NET Core에서 CORS (원본 간 요청) 사용

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 문서에서는 ASP.NET Core 앱에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.

브라우저 보안은 웹 페이지에서 웹 페이지를 제공 하는 도메인과 다른 도메인에 대 한 요청을 수행 하지 못하도록 합니다. 이러한 제한을 *동일한 원본 정책*이라고 합니다. 동일한 원본 정책은 악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 방지 합니다. 경우에 따라 다른 사이트에서 앱에 대 한 원본 간 요청을 허용 하는 것이 좋습니다. 자세한 내용은 [MOZILLA CORS 문서](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)를 참조 하세요.

CORS ( [원본 간 리소스 공유](https://www.w3.org/TR/cors/) ):

* 는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.
* 는 CORS 완화 security의 보안 기능이 **아닙니다** . API는 CORS를 허용 하 여 안전 하지 않습니다. 자세한 내용은 [CORS 작동 방식](#how-cors)을 참조 하세요.
* 다른 서버를 거부 하는 동안 서버에서 일부 교차 원본 요청을 명시적으로 허용할 수 있습니다.
* [JSONP](/dotnet/framework/wcf/samples/jsonp)와 같은 이전 기술 보다 안전 하 고 유연 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>동일한 원점

동일한 스키마, 호스트 및 포트 ([RFC 6454](https://tools.ietf.org/html/rfc6454))가 있는 경우 두 url의 원본이 동일 합니다.

다음 두 URL은 동일한 원본입니다.

* `https://example.com/foo.html`
* `https://example.com/bar.html`

이러한 Url의 원본은 이전 두 Url과 다릅니다.

* `https://example.net` &ndash; 다른 도메인
* `https://www.example.com/foo.html` &ndash; 다른 하위 도메인
* `http://example.com/foo.html` &ndash; 다른 체계
* 다른 포트 `https://example.com:9000/foo.html` &ndash;

Internet Explorer는 원본을 비교할 때 포트를 고려하지 않습니다.

## <a name="cors-with-named-policy-and-middleware"></a>명명 된 정책 및 미들웨어를 사용 하는 CORS

CORS 미들웨어는 크로스-원본 요청을 처리 합니다. 다음 코드는 지정 된 원본으로 전체 앱에 대해 CORS를 사용 하도록 설정 합니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

위의 코드는:

* 정책 이름을 "\_myAllowSpecificOrigins"로 설정 합니다. 정책 이름은 임의로입니다.
* CORS를 사용 하도록 설정 하는 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 확장 메서드를 호출 합니다.
* [람다 식](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)으로 <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>를 호출 합니다. 람다는 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 개체를 사용 합니다. `WithOrigins`등의 [구성 옵션](#cors-policy-options)에 대해서는이 문서의 뒷부분에서 설명 합니다.

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> 메서드 호출은 CORS 서비스를 앱의 서비스 컨테이너에 추가 합니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

자세한 내용은이 문서의 [CORS 정책 옵션](#cpo) (영문)을 참조 하세요.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 메서드는 다음 코드와 같이 메서드를 연결할 수 있습니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

참고: URL은 후행 슬래시 (`/`)를 포함 **하지** 않아야 합니다. URL이 `/`로 종료 되는 경우 비교 시 `false` 반환 되 고 헤더가 반환 되지 않습니다.

::: moniker range=">= aspnetcore-3.0"

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a>모든 끝점에 CORS 정책 적용

다음 코드에서는 CORS 미들웨어를 통해 모든 앱 끝점에 CORS 정책을 적용 합니다.
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code ommitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code ommited.
}
```

> [!WARNING]
> 끝점 라우팅을 사용 하 여 `UseRouting`에 대 한 호출과 `UseEndpoints`사이에서 실행 되도록 CORS 미들웨어를 구성 해야 합니다. 잘못 된 구성으로 인해 미들웨어가 제대로 작동 하지 않을 수 있습니다.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
다음 코드에서는 CORS 미들웨어를 통해 모든 앱 끝점에 CORS 정책을 적용 합니다.
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
참고: `UseCors` `UseMvc`전에 호출 해야 합니다.

::: moniker-end

페이지/컨트롤러/동작 수준에서 cors 정책을 적용 하려면 [Razor Pages, 컨트롤러 및 작업 메서드에서 Cors 사용](#ecors) 을 참조 하세요.

위의 코드를 테스트 하는 방법에 대 한 지침은 [CORS 테스트](#test) 를 참조 하세요.

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a>끝점 라우팅을 사용 하 여 Cors 사용

끝점 라우팅을 사용 하면 `RequireCors` 확장 메서드 집합을 사용 하 여 끝점 별로 CORS를 사용 하도록 설정할 수 있습니다.

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

마찬가지로 모든 컨트롤러에 대해 CORS를 사용 하도록 설정할 수도 있습니다.

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a>특성과 함께 CORS 사용

[&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성은 CORS를 전역적으로 적용 하는 대안을 제공 합니다. `[EnableCors]` 특성은 모든 끝점이 아니라 선택한 끝점에 대해 CORS를 사용 하도록 설정 합니다.

`[EnableCors]`를 사용 하 여 기본 정책을 지정 하 고 정책을 지정 하 `[EnableCors("{Policy String}")]` 합니다.

`[EnableCors]` 특성을 적용할 수 있습니다.

* Razor 페이지 `PageModel`
* 컨트롤러
* 컨트롤러 동작 방법

`[EnableCors]` 특성을 사용 하 여 컨트롤러/페이지-모델/작업에 다른 정책을 적용할 수 있습니다. `[EnableCors]` 특성이 컨트롤러/페이지 모델/동작 메서드에 적용 되 고 CORS가 미들웨어에서 사용 되는 경우 두 정책이 모두 적용 됩니다. 정책을 결합 하지 않는 것이 좋습니다. 동일한 앱에서 `[EnableCors]` 특성 또는 미들웨어를 사용 합니다.

다음 코드는 각 메서드에 다른 정책을 적용 합니다.

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

다음 코드에서는 CORS 기본 정책과 `"AnotherPolicy"`이라는 정책을 만듭니다.

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>CORS 비활성시키기

[&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성은 컨트롤러/페이지-모델/작업에 대해 CORS를 사용 하지 않도록 설정 합니다.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS 정책 옵션

이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션에 대해 설명 합니다.

* [허용 되는 원본 설정](#set-the-allowed-origins)
* [허용 되는 HTTP 메서드 설정](#set-the-allowed-http-methods)
* [허용 되는 요청 헤더 설정](#set-the-allowed-request-headers)
* [노출 된 응답 헤더 설정](#set-the-exposed-response-headers)
* [원본 간 요청에 대 한 자격 증명](#credentials-in-cross-origin-requests)
* [실행 전 만료 시간 설정](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>은 `Startup.ConfigureServices`에서 호출 됩니다. 일부 옵션의 경우 [CORS 작동 방법](#how-cors) 섹션을 먼저 읽는 것이 도움이 될 수 있습니다.

## <a name="set-the-allowed-origins"></a>허용되는 원본 설정하기

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash;는 모든 원본의 CORS 요청 (`http` 또는 `https`)을 허용 합니다. *모든 웹 사이트* 에서 앱에 대 한 원본 간 요청을 수행할 수 있기 때문에 `AllowAnyOrigin`은 안전 하지 않습니다.

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> `AllowAnyOrigin` 및 `AllowCredentials` 지정은 안전 하지 않은 구성으로, 사이트 간 요청 위조를 발생 시킬 수 있습니다. 앱이 두 가지 방법으로 구성 된 경우 CORS 서비스는 잘못 된 CORS 응답을 반환 합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> `AllowAnyOrigin` 및 `AllowCredentials` 지정은 안전 하지 않은 구성으로, 사이트 간 요청 위조를 발생 시킬 수 있습니다. 보안 응용 프로그램의 경우 클라이언트에서 서버 리소스에 액세스할 수 있는 권한을 부여 해야 하는 경우 정확한 원본 목록을 지정 합니다.

::: moniker-end

`AllowAnyOrigin`는 실행 전 요청 및 `Access-Control-Allow-Origin` 헤더에 영향을 줍니다. 자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash;는 원본이 허용 되는지 평가할 때 구성 된 와일드 카드 도메인과 일치 하는 원본을 허용 하는 함수로 정책의 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 속성을 설정 합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a>허용되는 HTTP 메서드 설정하기

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* 모든 HTTP 메서드를 허용 합니다.
* 실행 전 요청 및 `Access-Control-Allow-Methods` 헤더에 영향을 줍니다. 자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.

### <a name="set-the-allowed-request-headers"></a>허용되는 요청 헤더 설정하기

*작성자 요청 헤더*라는 CORS 요청에서 특정 헤더를 전송 하도록 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>를 호출 하 고 허용 되는 헤더를 지정 합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

모든 작성자 요청 헤더를 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>를 호출 합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

이 설정은 실행 전 요청 및 `Access-Control-Request-Headers` 헤더에 영향을 줍니다. 자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.

::: moniker range=">= aspnetcore-2.2"

`WithHeaders`로 지정 된 특정 헤더와 일치 하는 CORS 미들웨어 정책은 `Access-Control-Request-Headers` 전송 된 헤더가 `WithHeaders`에 명시 된 헤더와 정확히 일치 하는 경우에만 가능 합니다.

예를 들어 다음과 같이 구성 된 앱을 살펴보겠습니다.

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS 미들웨어는 `Content-Language` ([Headernames](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage))가 `WithHeaders`에 나열 되지 않으므로 다음 요청 헤더를 사용 하 여 실행 전 요청을 거부 합니다.

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

앱은 *200 OK* 응답을 반환 하지만 CORS 헤더는 다시 전송 하지 않습니다. 따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS 미들웨어는 항상 `Access-Control-Request-Headers`에서 4 개의 헤더를 사용 하 여 CorsPolicy 헤더에 구성 된 값에 관계 없이 보낼 수 있습니다. 이 헤더 목록에는 다음이 포함 됩니다.

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

예를 들어 다음과 같이 구성 된 앱을 살펴보겠습니다.

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

`Content-Language`는 항상 허용 목록 이므로 CORS 미들웨어는 다음 요청 헤더를 사용 하 여 실행 전 요청에 응답 합니다.

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a>노출되는 응답 헤더 설정하기

기본적으로 브라우저는 모든 응답 헤더를 앱에 노출 하지 않습니다. 자세한 내용은 [W3C 원본 간 리소스 공유 (용어): 단순 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)를 참조 하세요.

기본적으로 사용 가능한 응답 헤더들은 다음과 같습니다.

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 사양은 이러한 헤더 *단순 응답 헤더*를 호출 합니다. 앱에서 다른 헤더를 사용할 수 있도록 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>를 호출 합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>교차 원본 요청의 자격 증명

CORS 요청에서 자격 증명(Credentials)은 특별한 처리를 해야 합니다. 기본적으로 브라우저는 원본 간 요청과 함께 자격 증명을 보내지 않습니다. 자격 증명에는 쿠키 및 HTTP 인증 스키마가 포함 됩니다. 원본 간 요청을 사용 하 여 자격 증명을 보내려면 클라이언트는 `XMLHttpRequest.withCredentials`를 `true`으로 설정 해야 합니다.

`XMLHttpRequest` 직접 사용:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

JQuery 사용:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

[FETCH API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)사용:

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

서버에서 자격 증명을 허용 해야 합니다. 원본 간 자격 증명을 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>를 호출 합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

HTTP 응답에는 서버에서 원본 간 요청에 대 한 자격 증명을 허용 하도록 브라우저에 지시 하는 `Access-Control-Allow-Credentials` 헤더가 포함 되어 있습니다.

브라우저에서 자격 증명을 전송 하지만 응답에 유효한 `Access-Control-Allow-Credentials` 헤더가 포함 되어 있지 않으면 브라우저에서 앱에 응답을 노출 하지 않으며 크로스-원본 요청이 실패 합니다.

크로스-원본 자격 증명을 허용 하는 것은 보안상 위험 합니다. 다른 도메인의 웹 사이트는 사용자의 지식 없이 사용자를 대신 하 여 사용자의 앱에 로그인 한 사용자의 자격 증명을 보낼 수 있습니다. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS 사양은 `Access-Control-Allow-Credentials` 헤더가 있는 경우 원본 설정 `"*"` (모든 원본)가 잘못 되었음을 나타냅니다.

### <a name="preflight-requests"></a>실행 전 요청

일부 CORS 요청의 경우 브라우저는 실제 요청을 만들기 전에 추가 요청을 보냅니다. 이 요청을 실행 *전 요청*이라고 합니다. 다음 조건에 해당 하는 경우 브라우저에서 실행 전 요청을 건너뛸 수 있습니다.

* 요청 메서드는 GET, HEAD 또는 POST입니다.
* 앱은 `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`또는 `Last-Event-ID`이외의 요청 헤더를 설정 하지 않습니다.
* `Content-Type` 헤더 (설정 된 경우)에는 다음 값 중 하나가 있습니다.
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

클라이언트 요청에 대해 설정 된 요청 헤더에 대 한 규칙은 앱이 `XMLHttpRequest` 개체에서 `setRequestHeader`를 호출 하 여 설정 하는 헤더에 적용 됩니다. CORS 사양은 이러한 헤더 *작성자 요청 헤더*를 호출 합니다. 이 규칙은 브라우저에서 설정할 수 있는 헤더 (예: `User-Agent`, `Host`, `Content-Length`)에는 적용 되지 않습니다.

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

사전 비행 요청은 HTTP OPTIONS 메서드를 사용 합니다. 여기에는 두 가지 특수 헤더가 포함 됩니다.

* `Access-Control-Request-Method`: 실제 요청에 사용 되는 HTTP 메서드입니다.
* `Access-Control-Request-Headers`: 앱이 실제 요청에 대해 설정 하는 요청 헤더 목록입니다. 앞에서 설명한 대로 브라우저에서 설정 하는 헤더 (예: `User-Agent`)는 여기에 포함 되지 않습니다.

CORS 실행 전 요청에는 실제 요청과 함께 전송 되는 헤더를 서버에 나타내는 `Access-Control-Request-Headers` 헤더가 포함 될 수 있습니다.

특정 헤더를 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>를 호출 합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

모든 작성자 요청 헤더를 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>를 호출 합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

브라우저는 `Access-Control-Request-Headers`설정 하는 방법에 완전히 일치 하지 않습니다. 헤더를 `"*"` 이외의 값으로 설정 하거나 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>를 사용 하는 경우 `Accept`, `Content-Type`, `Origin`, 지원 하려는 모든 사용자 지정 헤더를 포함 해야 합니다.

다음은 실행 전 요청에 대 한 응답 예제입니다 (서버에서 요청을 허용 한다고 가정).

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

응답에는 허용 된 메서드를 나열 하는 `Access-Control-Allow-Methods` 헤더와 허용 되는 헤더를 나열 하는 `Access-Control-Allow-Headers` 헤더가 포함 됩니다. 실행 전 요청이 성공 하면 브라우저에서 실제 요청을 보냅니다.

실행 전 요청이 거부 되 면 앱은 *200 OK* 응답을 반환 하지만 CORS 헤더는 다시 전송 하지 않습니다. 따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.

### <a name="set-the-preflight-expiration-time"></a>예비 요청 만료 시간 설정하기

`Access-Control-Max-Age` 헤더는 실행 전 요청에 대 한 응답을 캐시할 수 있는 기간을 지정 합니다. 이 헤더를 설정 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>를 호출 합니다.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS의 동작 방식

이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 요청에 발생 하는 작업에 대해 설명 합니다.

* CORS는 보안 기능이 **아닙니다** . CORS는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.
  * 예를 들어 악의적인 행위자는 사이트에 대 한 [XSS (교차 사이트 스크립팅) 방지](xref:security/cross-site-scripting) 를 사용 하 여 정보를 도용 하기 위해 CORS 지원 사이트에 대 한 교차 사이트 요청을 실행할 수 있습니다.
* API는 CORS를 허용 하 여 안전 하지 않습니다.
  * CORS를 적용 하는 것은 클라이언트 (브라우저)에 있습니다. 서버는 요청을 실행 하 고 응답을 반환 합니다. 클라이언트는 오류를 반환 하 고 응답을 차단 합니다. 예를 들어 다음 도구 중 하나는 서버 응답을 표시 합니다.
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * 주소 표시줄에 URL을 입력 하는 웹 브라우저입니다.
* 서버에서 브라우저가 원본 간 [Xhr](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 요청을 실행 하도록 허용 하는 방법입니다. 그렇지 않으면이를 사용할 수 없습니다.
  * 브라우저 (CORS 불포함)는 크로스-원본 요청을 수행할 수 없습니다. CORS 이전에는 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 을 사용 하 여이 제한을 회피 했습니다. JSONP는 XHR을 사용 하지 않으며 `<script>` 태그를 사용 하 여 응답을 수신 합니다. 스크립트를 크로스-원본으로 로드할 수 있습니다.

[CORS 사양](https://www.w3.org/TR/cors/) 에는 크로스-원본 요청을 가능 하 게 하는 몇 가지 새로운 HTTP 헤더가 도입 되었습니다. 브라우저에서 CORS를 지 원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정 합니다. CORS를 사용 하도록 설정 하는 데 사용자 지정 JavaScript 코드가 필요 하지 않습니다.

다음은 원본 간 요청의 예입니다. `Origin` 헤더는 요청을 수행 하는 사이트의 도메인을 제공 합니다. `Origin` 헤더는 필수 이며 호스트와 달라 야 합니다.

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

서버에서 요청을 허용 하는 경우 응답에 `Access-Control-Allow-Origin` 헤더를 설정 합니다. 이 헤더의 값은 요청의 `Origin` 헤더와 일치 하거나 `"*"`와일드 카드 값입니다. 즉, 모든 원본을 사용할 수 있습니다.

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

응답에 `Access-Control-Allow-Origin` 헤더가 포함 되어 있지 않으면 크로스-원본 요청이 실패 합니다. 특히 브라우저에서 요청을 허용 하지 않습니다. 서버에서 성공적인 응답을 반환 하는 경우에도 브라우저에서 응답을 클라이언트 앱에 사용할 수 있도록 설정 하지 않습니다.

<a name="test"></a>

## <a name="test-cors"></a>CORS 테스트

CORS를 테스트 하려면:

1. [API 프로젝트를 만듭니다](xref:tutorials/first-web-api). 또는 [샘플을 다운로드할](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)수 있습니다.
1. 이 문서의 방법 중 하나를 사용 하 여 CORS를 사용 하도록 설정 합니다. 다음은 그 예입니다.

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`은 [다운로드 샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)와 유사한 샘플 앱을 테스트 하는 데만 사용 해야 합니다.

1. 웹 앱 프로젝트 (Razor Pages 또는 MVC)를 만듭니다. 이 샘플에서는 Razor Pages을 사용 합니다. API 프로젝트와 동일한 솔루션에서 웹 앱을 만들 수 있습니다.
1. 다음 강조 표시 된 코드를 *Index. cshtml* 파일에 추가 합니다.

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. 위의 코드에서 `url: 'https://<web app>.azurewebsites.net/api/values/1',`을 배포 된 앱의 URL로 바꿉니다.
1. API 프로젝트를 배포 합니다. 예를 들어 [Azure에 배포할 수](xref:host-and-deploy/azure-apps/index)있습니다.
1. 바탕 화면에서 Razor Pages 또는 MVC 앱을 실행 하 고 **테스트** 단추를 클릭 합니다. F12 도구를 사용 하 여 오류 메시지를 검토 합니다.
1. `WithOrigins`에서 localhost 원본을 제거 하 고 앱을 배포 합니다. 또는 다른 포트를 사용 하 여 클라이언트 앱을 실행 합니다. 예를 들어 Visual Studio에서를 실행 합니다.
1. 클라이언트 앱을 사용 하 여 테스트 합니다. CORS 오류는 오류를 반환 하지만 JavaScript에는 오류 메시지를 사용할 수 없습니다. F12 도구에서 콘솔 탭을 사용 하 여 오류를 확인 합니다. 브라우저에 따라 다음과 비슷한 오류가 표시 됩니다 (F12 도구 콘솔).

   * Microsoft Edge 사용:

     **SEC7120: [CORS] 원본 `https://localhost:44375`에서 원본 간 리소스에 대 한 액세스 제어-원본 응답 헤더의 `https://localhost:44375`를 찾을 수 없습니다 `https://webapi.azurewebsites.net/api/values/1`**

   * Chrome 사용:

     **원본 `https://localhost:44375`에서 `https://webapi.azurewebsites.net/api/values/1`의 XMLHttpRequest 액세스가 CORS 정책에 의해 차단 되었습니다. 요청 된 리소스에 ' 액세스 제어-원본 ' 헤더가 없습니다.**
     
CORS 사용 끝점은 [Fiddler](https://www.telerik.com/fiddler) 또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 하 여 테스트할 수 있습니다. 도구를 사용 하는 경우 `Origin` 헤더에 지정 된 요청의 원점은 요청을 받는 호스트와 달라 야 합니다. 요청이 `Origin` 헤더의 값을 기반으로 하는 *교차 원점이* 아닌 경우:

* CORS 미들웨어가 요청을 처리할 필요가 없습니다.
* CORS 헤더는 응답에서 반환 되지 않습니다.

## <a name="cors-in-iis"></a>IIS의 CORS

IIS에 배포할 때 서버에서 익명 액세스를 허용 하도록 구성 되지 않은 경우 CORS를 Windows 인증 전에 실행 해야 합니다. 이 시나리오를 지원 하려면 앱에 대해 [IIS CORS 모듈](https://www.iis.net/downloads/microsoft/iis-cors-module) 을 설치 하 고 구성 해야 합니다.

## <a name="additional-resources"></a>추가 리소스

* [CORS (원본 간 리소스 공유)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [IIS CORS 모듈 시작](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)
