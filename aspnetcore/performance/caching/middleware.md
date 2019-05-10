---
title: ASP.NET Core의 응답 캐싱 미들웨어
author: guardrex
description: ASP.NET Core에서 응답 캐싱 미들웨어를 구성하고 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2019
uid: performance/caching/middleware
ms.openlocfilehash: ddbd547ec0fdd09da1f3e3ce30f8fa35a34870c2
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897370"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="e2e5d-103">ASP.NET Core의 응답 캐싱 미들웨어</span><span class="sxs-lookup"><span data-stu-id="e2e5d-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="e2e5d-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [John Luo](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="e2e5d-104">By [Luke Latham](https://github.com/guardrex) and [John Luo](https://github.com/JunTaoLuo)</span></span>

<span data-ttu-id="e2e5d-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e2e5d-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="e2e5d-106">이 문서에서는 ASP.NET Core 응용 프로그램에서 응답 캐싱 미들웨어를 구성하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-106">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="e2e5d-107">미들웨어는 응답을 캐싱할 수 있는 시점을 결정하고 응답을 저장하고 캐시에서 가져온 응답을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-107">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="e2e5d-108">HTTP 캐싱 및 `ResponseCache` 특성에 대한 소개는 [응답 캐싱](xref:performance/caching/response)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-108">For an introduction to HTTP caching and the `ResponseCache` attribute, see [Response Caching](xref:performance/caching/response).</span></span>

## <a name="package"></a><span data-ttu-id="e2e5d-109">Package</span><span class="sxs-lookup"><span data-stu-id="e2e5d-109">Package</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e2e5d-110">참조를 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) 에 대 한 패키지 참조를 추가 하거나 합니다 [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-110">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="e2e5d-111">참조를 [Microsoft.AspNetCore.All 메타 패키지](xref:fundamentals/metapackage) 에 대 한 패키지 참조를 추가 하거나 합니다 [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-111">Reference the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="e2e5d-112">패키지 참조를 추가 합니다 [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) 패키지 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-112">Add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="e2e5d-113">구성</span><span class="sxs-lookup"><span data-stu-id="e2e5d-113">Configuration</span></span>

<span data-ttu-id="e2e5d-114">`Startup.ConfigureServices`에서 서비스 컬렉션에 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-114">In `Startup.ConfigureServices`, add the middleware to the service collection.</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="e2e5d-115">그리고 미들웨어를 요청 처리 파이프라인에 추가하는 `UseResponseCaching` 확장 메서드를 이용해서 응용 프로그램이 미들웨어를 사용하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-115">Configure the app to use the middleware with the `UseResponseCaching` extension method, which adds the middleware to the request processing pipeline.</span></span> <span data-ttu-id="e2e5d-116">예제 응용 프로그램은 최대 10초 동안 캐시가 응답을 캐싱할 수 있도록 [`Cache-Control`](https://tools.ietf.org/html/rfc7234#section-5.2) 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-116">The sample app adds a [`Cache-Control`](https://tools.ietf.org/html/rfc7234#section-5.2) header to the response that caches cacheable responses for up to 10 seconds.</span></span> <span data-ttu-id="e2e5d-117">또한 [`Vary`](https://tools.ietf.org/html/rfc7231#section-7.1.4) 헤더를 전송해서 후속 요청의 [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) 헤더가 원본 요청과 일치하는 경우에만 캐시된 응답을 제공하도록 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-117">The sample sends a [`Vary`](https://tools.ietf.org/html/rfc7231#section-7.1.4) header to configure the middleware to serve a cached response only if the [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span> <span data-ttu-id="e2e5d-118">다음 예제 코드에서 [CacheControlHeaderValue](/dotnet/api/microsoft.net.http.headers.cachecontrolheadervalue) 및 [HeaderNames](/dotnet/api/microsoft.net.http.headers.headernames)를 사용하려면 [Microsoft.Net.Http.Headers](/dotnet/api/microsoft.net.http.headers) 네임스페이스에 대한 `using` 문이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-118">In the code example that follows, [CacheControlHeaderValue](/dotnet/api/microsoft.net.http.headers.cachecontrolheadervalue) and [HeaderNames](/dotnet/api/microsoft.net.http.headers.headernames) require a `using` statement for the [Microsoft.Net.Http.Headers](/dotnet/api/microsoft.net.http.headers) namespace.</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17,22-29)]

<span data-ttu-id="e2e5d-119">응답 캐싱 미들웨어는 상태 코드가 200(정상)인 서버 응답만 캐시합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-119">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="e2e5d-120">[오류 페이지](xref:fundamentals/error-handling)를 비롯한 다른 모든 응답은 미들웨어에 의해 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-120">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="e2e5d-121">인증된 클라이언트에 대한 콘텐츠를 포함하고 있는 응답은 미들웨어가 해당 응답을 저장하거나 제공하는 것을 막을 수 있도록 반드시 캐시가 불가능한 것으로 표시해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-121">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="e2e5d-122">미들웨어가 캐싱할 수 있는 응답인지 여부를 판단하는 방법에 대한 자세한 내용은 [캐싱 조건](#conditions-for-caching)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-122">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="e2e5d-123">옵션</span><span class="sxs-lookup"><span data-stu-id="e2e5d-123">Options</span></span>

<span data-ttu-id="e2e5d-124">미들웨어는 응답 캐싱을 제어하기 위한 세 가지 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-124">The middleware offers three options for controlling response caching.</span></span>

| <span data-ttu-id="e2e5d-125">옵션</span><span class="sxs-lookup"><span data-stu-id="e2e5d-125">Option</span></span>                | <span data-ttu-id="e2e5d-126">설명</span><span class="sxs-lookup"><span data-stu-id="e2e5d-126">Description</span></span> |
| --------------------- | ----------- |
| <span data-ttu-id="e2e5d-127">UseCaseSensitivePaths</span><span class="sxs-lookup"><span data-stu-id="e2e5d-127">UseCaseSensitivePaths</span></span> | <span data-ttu-id="e2e5d-128">경로의 대/소문자를 구분해서 응답을 캐시할지 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-128">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="e2e5d-129">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-129">The default value is `false`.</span></span> |
| <span data-ttu-id="e2e5d-130">MaximumBodySize</span><span class="sxs-lookup"><span data-stu-id="e2e5d-130">MaximumBodySize</span></span>       | <span data-ttu-id="e2e5d-131">캐시 가능한 응답 본문의 최대 바이트 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-131">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="e2e5d-132">기본값은 `64 * 1024 * 1024`(64MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-132">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <span data-ttu-id="e2e5d-133">SizeLimit</span><span class="sxs-lookup"><span data-stu-id="e2e5d-133">SizeLimit</span></span>             | <span data-ttu-id="e2e5d-134">응답 캐싱 미들웨어의 바이트 크기 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-134">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="e2e5d-135">기본값은 `100 * 1024 * 1024`(100MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-135">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |

<span data-ttu-id="e2e5d-136">아래 예제는 미들웨어를 다음과 같이 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-136">The following example configures the middleware to:</span></span>

* <span data-ttu-id="e2e5d-137">1,024바이트 이하의 응답을 캐시합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-137">Cache responses smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="e2e5d-138">경로의 대/소문자를 구분해서 응답을 저장합니다(예를 들어 `/page1`과 `/Page1`을 별도로 저장합니다).</span><span class="sxs-lookup"><span data-stu-id="e2e5d-138">Store the responses by case-sensitive paths (for example, `/page1` and `/Page1` are stored separately).</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.UseCaseSensitivePaths = true;
    options.MaximumBodySize = 1024;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="e2e5d-139">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="e2e5d-139">VaryByQueryKeys</span></span>

<span data-ttu-id="e2e5d-140">MVC/Web API 컨트롤러 또는 Razor Pages 페이지 모델을 사용할 때 `ResponseCache` 특성은 응답 캐싱을 위한 적절한 헤더를 설정하기 위해 필요한 매개 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-140">When using MVC/Web API controllers or Razor Pages page models, the `ResponseCache` attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="e2e5d-141">미들웨어가 반드시 필요한 `ResponseCache` 특성의 유일한 매개 변수는 `VaryByQueryKeys`로, 이 매개 변수는 실제 HTTP 헤더와 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-141">The only parameter of the `ResponseCache` attribute that strictly requires the middleware is `VaryByQueryKeys`, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="e2e5d-142">자세한 내용은 [ResponseCache 특성](xref:performance/caching/response#responsecache-attribute)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-142">For more information, see [ResponseCache Attribute](xref:performance/caching/response#responsecache-attribute).</span></span>

<span data-ttu-id="e2e5d-143">`ResponseCache` 특성을 사용하지 않을 경우, `VaryByQueryKeys` 기능을 사용해서 응답 캐싱을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-143">When not using the `ResponseCache` attribute, response caching can be varied with the `VaryByQueryKeys` feature.</span></span> <span data-ttu-id="e2e5d-144">사용 된 `ResponseCachingFeature` 에서 직접 합니다 `IFeatureCollection` 의 `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="e2e5d-144">Use the `ResponseCachingFeature` directly from the `IFeatureCollection` of the `HttpContext`:</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();
if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="e2e5d-145">`*`에 `VaryByQueryKeys` 와 동일한 단일 값을 지정하면 모든 요청 쿼리 매개 변수에 따라 캐시가 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-145">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="e2e5d-146">응답 캐싱 미들웨어에서 사용되는 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="e2e5d-146">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="e2e5d-147">미들웨어에 의한 응답 캐싱은 HTTP 헤더를 이용해서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-147">Response caching by the middleware is configured using HTTP headers.</span></span>

| <span data-ttu-id="e2e5d-148">헤더</span><span class="sxs-lookup"><span data-stu-id="e2e5d-148">Header</span></span> | <span data-ttu-id="e2e5d-149">설명</span><span class="sxs-lookup"><span data-stu-id="e2e5d-149">Details</span></span> |
| ------ | ------- |
| <span data-ttu-id="e2e5d-150">Authorization</span><span class="sxs-lookup"><span data-stu-id="e2e5d-150">Authorization</span></span> | <span data-ttu-id="e2e5d-151">이 헤더가 존재할 경우 응답이 캐시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-151">The response isn't cached if the header exists.</span></span> |
| <span data-ttu-id="e2e5d-152">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="e2e5d-152">Cache-Control</span></span> | <span data-ttu-id="e2e5d-153">미들웨어는 `public` 캐시 지시문으로 표시된 캐싱 응답만 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-153">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="e2e5d-154">다음 매개 변수로 캐싱을 제어하십시오.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-154">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="e2e5d-155">max-age</span><span class="sxs-lookup"><span data-stu-id="e2e5d-155">max-age</span></span></li><li><span data-ttu-id="e2e5d-156">max-stale&#8224;</span><span class="sxs-lookup"><span data-stu-id="e2e5d-156">max-stale&#8224;</span></span></li><li><span data-ttu-id="e2e5d-157">최소 새로</span><span class="sxs-lookup"><span data-stu-id="e2e5d-157">min-fresh</span></span></li><li><span data-ttu-id="e2e5d-158">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="e2e5d-158">must-revalidate</span></span></li><li><span data-ttu-id="e2e5d-159">캐시 없음</span><span class="sxs-lookup"><span data-stu-id="e2e5d-159">no-cache</span></span></li><li><span data-ttu-id="e2e5d-160">no-store</span><span class="sxs-lookup"><span data-stu-id="e2e5d-160">no-store</span></span></li><li><span data-ttu-id="e2e5d-161">only-if-cached</span><span class="sxs-lookup"><span data-stu-id="e2e5d-161">only-if-cached</span></span></li><li><span data-ttu-id="e2e5d-162">private</span><span class="sxs-lookup"><span data-stu-id="e2e5d-162">private</span></span></li><li><span data-ttu-id="e2e5d-163">public</span><span class="sxs-lookup"><span data-stu-id="e2e5d-163">public</span></span></li><li><span data-ttu-id="e2e5d-164">기간</span><span class="sxs-lookup"><span data-stu-id="e2e5d-164">s-maxage</span></span></li><li><span data-ttu-id="e2e5d-165">proxy-revalidate&#8225;</span><span class="sxs-lookup"><span data-stu-id="e2e5d-165">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="e2e5d-166">&#8224; `max-stale`에 제한이 지정되지 않으면 미들웨어는 아무런 작업도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-166">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="e2e5d-167">&#8225; `proxy-revalidate`는 `must-revalidate`와 동일한 효과를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-167">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="e2e5d-168">자세한 내용은 참조 하세요. [RFC 7231: 캐시 제어 지시문을 요청](https://tools.ietf.org/html/rfc7234#section-5.2.1)합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-168">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| <span data-ttu-id="e2e5d-169">Pragma</span><span class="sxs-lookup"><span data-stu-id="e2e5d-169">Pragma</span></span> | <span data-ttu-id="e2e5d-170">요청에 지정된 `Pragma: no-cache` 헤더는 `Cache-Control: no-cache`와 동일한 효과를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-170">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="e2e5d-171">이 헤더는 `Cache-Control` 헤더가 존재할 경우, 지정된 관련 지시문에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-171">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="e2e5d-172">HTTP/1.0에 대한 하위 호환성을 감안하기 위한 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-172">Considered for backward compatibility with HTTP/1.0.</span></span> |
| <span data-ttu-id="e2e5d-173">Set-Cookie</span><span class="sxs-lookup"><span data-stu-id="e2e5d-173">Set-Cookie</span></span> | <span data-ttu-id="e2e5d-174">이 헤더가 존재할 경우 응답이 캐시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-174">The response isn't cached if the header exists.</span></span> <span data-ttu-id="e2e5d-175">하나 이상의 쿠키를 설정하는 요청 처리 파이프라인의 모든 미들웨어는 응답 캐싱 미들웨어가 응답을 캐싱하지 못하게 합니다(예를 들어 [쿠키 기반 TempData 공급자](xref:fundamentals/app-state#tempdata)).</span><span class="sxs-lookup"><span data-stu-id="e2e5d-175">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| <span data-ttu-id="e2e5d-176">Vary</span><span class="sxs-lookup"><span data-stu-id="e2e5d-176">Vary</span></span> | <span data-ttu-id="e2e5d-177">`Vary` 헤더는 다른 헤더를 이용해서 캐싱된 응답을 변경하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-177">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="e2e5d-178">예를 들어, `Vary: Accept-Encoding` 헤더가 지정된 요청과 `Accept-Encoding: gzip` 헤더가 지정된 요청에 대한 응답을 별도로 캐시하는 `Accept-Encoding: text/plain` 헤더를 지정해서 인코딩된 응답을 캐시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-178">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="e2e5d-179">헤더 값이 `*`인 응답은 절대로 저장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-179">A response with a header value of `*` is never stored.</span></span> |
| <span data-ttu-id="e2e5d-180">Expires</span><span class="sxs-lookup"><span data-stu-id="e2e5d-180">Expires</span></span> | <span data-ttu-id="e2e5d-181">이 헤더에 의해 낡은 것으로 간주되는 응답은 다른 `Cache-Control` 헤더에 의해서 재정의되지 않는 한 저장되거나 조회되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-181">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| <span data-ttu-id="e2e5d-182">-None-If-match</span><span class="sxs-lookup"><span data-stu-id="e2e5d-182">If-None-Match</span></span> | <span data-ttu-id="e2e5d-183">값이 `*`가 아니고 응답의 `ETag`가 제공된 모든 값과 일치하지 않으면 전체 응답이 캐시에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-183">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="e2e5d-184">그렇지 않으면 304(수정되지 않음) 응답이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-184">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| <span data-ttu-id="e2e5d-185">If-수정-이후</span><span class="sxs-lookup"><span data-stu-id="e2e5d-185">If-Modified-Since</span></span> | <span data-ttu-id="e2e5d-186">`If-None-Match` 헤더가 존재하지 않으면, 캐시된 응답 날짜가 제공된 값보다 새로운 경우 전체 응답이 캐시에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-186">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="e2e5d-187">그렇지 않으면 304(수정되지 않음) 응답이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-187">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| <span data-ttu-id="e2e5d-188">Date</span><span class="sxs-lookup"><span data-stu-id="e2e5d-188">Date</span></span> | <span data-ttu-id="e2e5d-189">캐시에서 응답을 제공할 때, 원본 응답이 `Date` 헤더를 제공하지 않으면 미들웨어에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-189">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| <span data-ttu-id="e2e5d-190">Content-Length</span><span class="sxs-lookup"><span data-stu-id="e2e5d-190">Content-Length</span></span> | <span data-ttu-id="e2e5d-191">캐시에서 응답을 제공할 때, 원본 응답이`Content-Length` 헤더를 제공하지 않으면 미들웨어에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-191">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| <span data-ttu-id="e2e5d-192">보존 기간</span><span class="sxs-lookup"><span data-stu-id="e2e5d-192">Age</span></span> | <span data-ttu-id="e2e5d-193">원본 응답이 전송한 `Age` 헤더는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-193">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="e2e5d-194">캐시된 응답을 제공할 때 미들웨어가 새로운 값을 계산합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-194">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="e2e5d-195">캐싱에서 요청 Cache-Control 지시문 사용</span><span class="sxs-lookup"><span data-stu-id="e2e5d-195">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="e2e5d-196">미들웨어는 [HTTP 1.1 캐싱 사양](https://tools.ietf.org/html/rfc7234#section-5.2)의 규칙을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-196">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="e2e5d-197">그리고 이 규칙은 캐시가 클라이언트에 의해서 전송된 유효한 `Cache-Control` 헤더를 준수할 것을 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-197">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="e2e5d-198">사양에 따라 클라이언트는`no-cache` 헤더 값을 설정한 요청을 전송함으로써 모든 요청에 대해 서버가 새로운 응답을 생성하도록 강제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-198">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="e2e5d-199">현재 미들웨어를 사용할 때 이 캐싱 동작을 개발자가 제어할 수 있는 방법은 없으며, 그 이유는 미들웨어가 공식 캐싱 사양을 따르고 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-199">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="e2e5d-200">캐싱 동작을 보다 세세히 제어하려면 ASP.NET Core의 다른 캐싱 기능을 살펴보십시오.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-200">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="e2e5d-201">다음 항목을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-201">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="e2e5d-202">문제 해결</span><span class="sxs-lookup"><span data-stu-id="e2e5d-202">Troubleshooting</span></span>

<span data-ttu-id="e2e5d-203">캐싱이 예상과 다르게 동작한다면 응답이 캐싱 가능하고 캐시에서 제공될 수 있는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-203">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="e2e5d-204">요청의 수신 헤더와 응답의 송신 헤더를 점검해보십시오.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-204">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="e2e5d-205">디버깅에 도움이 되도록 [로깅](xref:fundamentals/logging/index)을 활성화하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-205">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="e2e5d-206">캐싱 동작을 테스트하거나 문제를 해결할 때, 브라우저가 원하지 않는 방식으로 캐싱에 영향을 주는 요청 헤더를 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-206">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="e2e5d-207">예를 들어 페이지를 새로 고칠 때 브라우저가 `Cache-Control` 헤더를 `no-cache` 또는 `max-age=0`로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-207">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="e2e5d-208">다음 도구를 사용하면 명시적으로 요청 헤더를 설정할 수 있고 캐싱 테스트에도 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-208">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="e2e5d-209">Fiddler</span><span class="sxs-lookup"><span data-stu-id="e2e5d-209">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="e2e5d-210">Postman</span><span class="sxs-lookup"><span data-stu-id="e2e5d-210">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="e2e5d-211">캐싱 조건</span><span class="sxs-lookup"><span data-stu-id="e2e5d-211">Conditions for caching</span></span>

* <span data-ttu-id="e2e5d-212">요청의 결과로 200(OK) 상태 코드가 설정된 서버 응답을 받아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-212">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="e2e5d-213">요청 메서드가 GET 또는 HEAD여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-213">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="e2e5d-214">`Startup.Configure`, 응답 캐싱 미들웨어 압축이 필요한 미들웨어 전에 배치 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-214">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require compression.</span></span> <span data-ttu-id="e2e5d-215">자세한 내용은 <xref:fundamentals/middleware/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-215">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="e2e5d-216">`Authorization` 헤더가 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-216">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="e2e5d-217">`Cache-Control` 헤더의 매개 변수가 유효해야 하고 응답이 `public`으로 표시되어야 하며 `private`로 표시되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-217">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="e2e5d-218">`Pragma: no-cache`가 있으면 `Cache-Control` 헤더가 `Pragma` 헤더를 덮어쓰므로 `Cache-Control` 헤더가 없는 경우 no-cache header가 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-218">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="e2e5d-219">`Set-Cookie` 헤더가 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-219">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="e2e5d-220">`Vary` 헤더의 매개 변수가 유효해야 하고 `*`가 아니어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-220">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="e2e5d-221">`Content-Length` 헤더의 값이 (설정된 경우) 응답 본문의 크기와 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-221">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="e2e5d-222">[IHttpSendFileFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpsendfilefeature) 가 사용되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-222">The [IHttpSendFileFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpsendfilefeature) isn't used.</span></span>
* <span data-ttu-id="e2e5d-223">응답이 `Expires` 헤더와 `max-age` 및 `s-maxage` 캐시 지시문에 지정된 것보다 오래되면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-223">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="e2e5d-224">응답 버퍼링 성공 해야 하며 응답의 크기 구성 보다 작을 수 해야 또는 기본 `SizeLimit`입니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-224">Response buffering must be successful, and the size of the response must be smaller than the configured or default `SizeLimit`.</span></span>
* <span data-ttu-id="e2e5d-225">응답은 [RFC 7234](https://tools.ietf.org/html/rfc7234) 사양에 따라 캐시 가능해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-225">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="e2e5d-226">예를 들어 `no-store` 지시문이 요청 또는 응답 헤더 필드에 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-226">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="e2e5d-227">참조 *섹션 3: 캐시에서 응답을 저장할* 의 [RFC 7234](https://tools.ietf.org/html/rfc7234) 세부 정보에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-227">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="e2e5d-228">교차 사이트 요청 위조(CSRF, Cross-Site Request Forgery) 방지를 위한 보안 토큰을 생성하는 Antiforgery 시스템은 `Cache-Control` 및 `Pragma` 헤더를 `no-cache`로 설정하기 때문에 응답이 캐시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-228">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="e2e5d-229">HTML 폼 요소에 대한 Antiforgery 토큰을 비활성화시키는 방법에 대한 정보는 [ASP.NET Core Antiforgery 구성](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e2e5d-229">For information on how to disable antiforgery tokens for HTML form elements, see [ASP.NET Core antiforgery configuration](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2e5d-230">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e2e5d-230">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
