---
title: ASP.NET Core의 응답 캐싱 미들웨어
author: guardrex
description: ASP.NET Core에서 응답 캐싱 미들웨어를 구성하고 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: performance/caching/middleware
ms.openlocfilehash: a8e656e1d59114e2e953323e98e0a2399efca98a
ms.sourcegitcommit: 09f4a5ded39cc8204576fe801d760bd8b611f3aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73611451"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="5b4c6-103">ASP.NET Core의 응답 캐싱 미들웨어</span><span class="sxs-lookup"><span data-stu-id="5b4c6-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="5b4c6-104">By [Luke Latham 문자](https://github.com/guardrex) 및 [John 루 오 어](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="5b4c6-104">By [Luke Latham](https://github.com/guardrex) and [John Luo](https://github.com/JunTaoLuo)</span></span>

<span data-ttu-id="5b4c6-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5b4c6-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5b4c6-106">이 문서에서는 ASP.NET Core 앱에서 응답 캐싱 미들웨어를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-106">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="5b4c6-107">미들웨어는 응답을 캐시할 시기를 결정 하 고, 응답을 저장 하 고, 캐시에서 응답을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-107">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="5b4c6-108">HTTP 캐싱 및 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성에 대 한 소개는 [응답 캐싱](xref:performance/caching/response)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-108">For an introduction to HTTP caching and the [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

## <a name="configuration"></a><span data-ttu-id="5b4c6-109">Configuration</span><span class="sxs-lookup"><span data-stu-id="5b4c6-109">Configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5b4c6-110">응답 캐싱 미들웨어는 공유 프레임 워크를 통해 ASP.NET Core 앱에 대해 암시적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-110">Response Caching Middleware is implicitly available for ASP.NET Core apps via the shared framework.</span></span>

<span data-ttu-id="5b4c6-111">`Startup.ConfigureServices`에서 응답 캐싱 미들웨어를 서비스 컬렉션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="5b4c6-112">`Startup.Configure`에서 요청 처리 파이프라인에 미들웨어를 추가 하는 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 확장 메서드를 사용 하 여 미들웨어를 사용 하도록 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

<span data-ttu-id="5b4c6-113">샘플 앱은 다음 요청에 대 한 캐싱을 제어 하는 헤더를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-113">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="5b4c6-114">[캐시 제어](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash;는 최대 10 초 동안 캐시할 수 있는 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-114">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="5b4c6-115">&ndash; [변경](https://tools.ietf.org/html/rfc7231#section-7.1.4) 하면 후속 요청의 [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) 헤더가 원래 요청에 대 한 헤더와 일치 하는 경우에만 캐시 된 응답을 제공 하도록 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-115">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

<span data-ttu-id="5b4c6-116">응답 캐싱 미들웨어는 200 (OK) 상태 코드를 발생 시키는 서버 응답만 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-116">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="5b4c6-117">[오류 페이지](xref:fundamentals/error-handling)를 비롯 한 다른 모든 응답은 미들웨어에서 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-117">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="5b4c6-118">인증 된 클라이언트에 대 한 콘텐츠를 포함 하는 응답은 미들웨어가 해당 응답을 저장 및 제공 하지 못하도록 캐시할 수 없음으로 표시 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-118">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="5b4c6-119">미들웨어가 응답을 캐시할 수 있는지 여부를 결정 하는 방법에 대 한 자세한 내용은 [캐싱에 대 한 조건을](#conditions-for-caching) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-119">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5b4c6-120">[AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 사용 하거나 [ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) 패키지에 대 한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-120">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="5b4c6-121">`Startup.ConfigureServices`에서 응답 캐싱 미들웨어를 서비스 컬렉션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-121">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="5b4c6-122">`Startup.Configure`에서 요청 처리 파이프라인에 미들웨어를 추가 하는 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 확장 메서드를 사용 하 여 미들웨어를 사용 하도록 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-122">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

<span data-ttu-id="5b4c6-123">샘플 앱은 다음 요청에 대 한 캐싱을 제어 하는 헤더를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-123">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="5b4c6-124">[캐시 제어](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash;는 최대 10 초 동안 캐시할 수 있는 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-124">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="5b4c6-125">&ndash; [변경](https://tools.ietf.org/html/rfc7231#section-7.1.4) 하면 후속 요청의 [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) 헤더가 원래 요청에 대 한 헤더와 일치 하는 경우에만 캐시 된 응답을 제공 하도록 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-125">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

<span data-ttu-id="5b4c6-126">응답 캐싱 미들웨어는 200 (OK) 상태 코드를 발생 시키는 서버 응답만 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-126">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="5b4c6-127">[오류 페이지](xref:fundamentals/error-handling)를 비롯 한 다른 모든 응답은 미들웨어에서 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-127">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="5b4c6-128">인증 된 클라이언트에 대 한 콘텐츠를 포함 하는 응답은 미들웨어가 해당 응답을 저장 및 제공 하지 못하도록 캐시할 수 없음으로 표시 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-128">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="5b4c6-129">미들웨어가 응답을 캐시할 수 있는지 여부를 결정 하는 방법에 대 한 자세한 내용은 [캐싱에 대 한 조건을](#conditions-for-caching) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-129">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

::: moniker-end

## <a name="options"></a><span data-ttu-id="5b4c6-130">옵션</span><span class="sxs-lookup"><span data-stu-id="5b4c6-130">Options</span></span>

<span data-ttu-id="5b4c6-131">응답 캐싱 옵션은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-131">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="5b4c6-132">옵션</span><span class="sxs-lookup"><span data-stu-id="5b4c6-132">Option</span></span> | <span data-ttu-id="5b4c6-133">설명</span><span class="sxs-lookup"><span data-stu-id="5b4c6-133">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="5b4c6-134">응답 본문에 대해 캐시할 수 있는 최대 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-134">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="5b4c6-135">기본값은 `64 * 1024 * 1024` (64 MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-135">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="5b4c6-136">응답 캐시 미들웨어의 크기 제한 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-136">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="5b4c6-137">기본값은 `100 * 1024 * 1024` (100 MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-137">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="5b4c6-138">대/소문자를 구분 하는 경로에서 응답이 캐시 되는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-138">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="5b4c6-139">기본값은 `false`여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-139">The default value is `false`.</span></span> |

<span data-ttu-id="5b4c6-140">다음 예제에서는 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-140">The following example configures the middleware to:</span></span>

* <span data-ttu-id="5b4c6-141">본문 크기가 1024 바이트 보다 작거나 같은 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-141">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="5b4c6-142">대/소문자 구분 경로를 기준으로 응답을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-142">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="5b4c6-143">예를 들어 `/page1` 및 `/Page1`는 별도로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-143">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="5b4c6-144">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="5b4c6-144">VaryByQueryKeys</span></span>

<span data-ttu-id="5b4c6-145">MVC/web API 컨트롤러나 Razor Pages 페이지 모델을 사용 하는 경우 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성은 응답 캐싱에 적합 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-145">When using MVC / web API controllers or Razor Pages page models, the [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="5b4c6-146">미들웨어가 엄격 하 게 필요한 `[ResponseCache]` 특성의 유일한 매개 변수는 실제 HTTP 헤더에 해당 하지 않는 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>입니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-146">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="5b4c6-147">자세한 내용은 <xref:performance/caching/response#responsecache-attribute>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-147">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="5b4c6-148">`[ResponseCache]` 특성을 사용 하지 않는 경우 `VaryByQueryKeys`에서 응답 캐싱이 달라질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-148">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="5b4c6-149">다음과 같이 [HttpContext 기능](xref:Microsoft.AspNetCore.Http.HttpContext.Features)에서 직접 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-149">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="5b4c6-150">`VaryByQueryKeys` `*`와 같은 단일 값을 사용 하면 모든 요청 쿼리 매개 변수에 의해 캐시가 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-150">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="5b4c6-151">응답 캐싱 미들웨어에서 사용 하는 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="5b4c6-151">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="5b4c6-152">다음 표에서는 응답 캐싱에 영향을 주는 HTTP 헤더에 대 한 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-152">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="5b4c6-153">Header</span><span class="sxs-lookup"><span data-stu-id="5b4c6-153">Header</span></span> | <span data-ttu-id="5b4c6-154">설명</span><span class="sxs-lookup"><span data-stu-id="5b4c6-154">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="5b4c6-155">헤더가 있으면 응답이 캐시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-155">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="5b4c6-156">미들웨어는 `public` cache 지시어로 표시 된 캐싱 응답만 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-156">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="5b4c6-157">다음 매개 변수를 사용 하 여 캐싱 제어:</span><span class="sxs-lookup"><span data-stu-id="5b4c6-157">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="5b4c6-158">최대 사용 기간</span><span class="sxs-lookup"><span data-stu-id="5b4c6-158">max-age</span></span></li><li><span data-ttu-id="5b4c6-159">최대-부실&#8224;</span><span class="sxs-lookup"><span data-stu-id="5b4c6-159">max-stale&#8224;</span></span></li><li><span data-ttu-id="5b4c6-160">최소-새로</span><span class="sxs-lookup"><span data-stu-id="5b4c6-160">min-fresh</span></span></li><li><span data-ttu-id="5b4c6-161">유효성을 다시 검사 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-161">must-revalidate</span></span></li><li><span data-ttu-id="5b4c6-162">캐시 없음</span><span class="sxs-lookup"><span data-stu-id="5b4c6-162">no-cache</span></span></li><li><span data-ttu-id="5b4c6-163">저장소 없음</span><span class="sxs-lookup"><span data-stu-id="5b4c6-163">no-store</span></span></li><li><span data-ttu-id="5b4c6-164">-인 경우에만 캐시</span><span class="sxs-lookup"><span data-stu-id="5b4c6-164">only-if-cached</span></span></li><li><span data-ttu-id="5b4c6-165">private</span><span class="sxs-lookup"><span data-stu-id="5b4c6-165">private</span></span></li><li><span data-ttu-id="5b4c6-166">public</span><span class="sxs-lookup"><span data-stu-id="5b4c6-166">public</span></span></li><li><span data-ttu-id="5b4c6-167">s-maxage</span><span class="sxs-lookup"><span data-stu-id="5b4c6-167">s-maxage</span></span></li><li><span data-ttu-id="5b4c6-168">프록시-유효성 다시 검사&#8225;</span><span class="sxs-lookup"><span data-stu-id="5b4c6-168">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="5b4c6-169">&#8224;`max-stale`에 대 한 제한이 지정 되지 않은 경우 미들웨어는 아무런 작업도 수행 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-169">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="5b4c6-170">&#8225;`proxy-revalidate` `must-revalidate`와 동일한 효과가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-170">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="5b4c6-171">자세한 내용은 [RFC 7231: 요청 캐시-제어 지시문](https://tools.ietf.org/html/rfc7234#section-5.2.1)을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-171">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="5b4c6-172">요청의 `Pragma: no-cache` 헤더는 `Cache-Control: no-cache`와 동일한 결과를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-172">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="5b4c6-173">이 헤더는 `Cache-Control` 헤더의 관련 지시문 (있는 경우)에 의해 재정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-173">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="5b4c6-174">HTTP/1.0과의 이전 버전과의 호환성을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-174">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="5b4c6-175">헤더가 있으면 응답이 캐시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-175">The response isn't cached if the header exists.</span></span> <span data-ttu-id="5b4c6-176">하나 이상의 쿠키를 설정 하는 요청 처리 파이프라인의 미들웨어는 응답 캐싱 미들웨어가 응답을 캐싱하는 것을 방지 합니다 (예: [쿠키 기반 TempData 공급자](xref:fundamentals/app-state#tempdata)).</span><span class="sxs-lookup"><span data-stu-id="5b4c6-176">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="5b4c6-177">`Vary` 헤더는 다른 헤더로 캐시 된 응답을 변경 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-177">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="5b4c6-178">예를 들어 `Accept-Encoding: gzip` 헤더를 사용 하 여 요청에 대 한 응답을 캐시 하 고 별도로 `Accept-Encoding: text/plain` 하는 `Vary: Accept-Encoding` 헤더를 포함 하 여 인코딩에 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-178">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="5b4c6-179">`*` 헤더 값이 포함 된 응답은 저장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-179">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="5b4c6-180">이 헤더에 의해 부실 하 게 간주 되는 응답은 다른 `Cache-Control` 헤더에 의해 재정의 되지 않는 한 저장 되거나 검색 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-180">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="5b4c6-181">값이 `*` 되지 않고 응답의 `ETag` 제공 된 값과 일치 하지 않는 경우 전체 응답이 캐시에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-181">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="5b4c6-182">그렇지 않으면 304 (수정 되지 않음) 응답이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-182">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="5b4c6-183">`If-None-Match` 헤더가 없으면 캐시 된 응답 날짜가 제공 된 값 보다 최신인 경우 전체 응답이 캐시에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-183">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="5b4c6-184">그렇지 않으면 *304 수정 되지 않은* 응답이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-184">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="5b4c6-185">캐시에서 서비스를 제공 하는 경우에는 `Date` 헤더가 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-185">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="5b4c6-186">캐시에서 서비스를 제공 하는 경우에는 `Content-Length` 헤더가 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-186">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="5b4c6-187">원래 응답에서 보낸 `Age` 헤더는 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-187">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="5b4c6-188">미들웨어는 캐시 된 응답을 제공할 때 새 값을 계산 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-188">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="5b4c6-189">캐싱 측면에서 요청 Cache-control 지시문</span><span class="sxs-lookup"><span data-stu-id="5b4c6-189">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="5b4c6-190">미들웨어는 [HTTP 1.1 캐싱 사양의](https://tools.ietf.org/html/rfc7234#section-5.2)규칙을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-190">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="5b4c6-191">규칙에는 클라이언트가 보낸 유효한 `Cache-Control` 헤더를 인식 하기 위해 캐시가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-191">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="5b4c6-192">사양에 따라 클라이언트는 `no-cache` 헤더 값을 사용 하 여 요청을 수행 하 고 서버에서 모든 요청에 대 한 새 응답을 생성 하도록 강제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-192">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="5b4c6-193">현재 미들웨어는 공식 캐싱 사양을 준수 하므로 미들웨어를 사용할 때이 캐싱 동작에 대 한 개발자 제어는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-193">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="5b4c6-194">캐싱 동작을 보다 세부적으로 제어 하려면 ASP.NET Core의 다른 캐싱 기능을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-194">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="5b4c6-195">다음 항목을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-195">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="5b4c6-196">문제 해결</span><span class="sxs-lookup"><span data-stu-id="5b4c6-196">Troubleshooting</span></span>

<span data-ttu-id="5b4c6-197">캐싱 동작이 예상과 다른 경우 응답이 캐시 가능 하 고 캐시에서 제공 될 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-197">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="5b4c6-198">요청의 들어오는 헤더와 응답의 나가는 헤더를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-198">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="5b4c6-199">디버깅에 도움이 되는 [로깅을](xref:fundamentals/logging/index) 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-199">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="5b4c6-200">캐싱 동작을 테스트 하 고 문제를 해결할 때 브라우저는 원치 않는 방식으로 캐싱에 영향을 주는 요청 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-200">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="5b4c6-201">예를 들어, 브라우저에서 페이지를 새로 고칠 때 `Cache-Control` 헤더를 `no-cache` 또는 `max-age=0`로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-201">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="5b4c6-202">다음 도구는 명시적으로 요청 헤더를 설정할 수 있으며 캐싱을 테스트 하는 데 기본 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-202">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="5b4c6-203">Fiddler</span><span class="sxs-lookup"><span data-stu-id="5b4c6-203">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="5b4c6-204">Postman</span><span class="sxs-lookup"><span data-stu-id="5b4c6-204">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="5b4c6-205">캐싱 조건</span><span class="sxs-lookup"><span data-stu-id="5b4c6-205">Conditions for caching</span></span>

* <span data-ttu-id="5b4c6-206">요청은 200 (OK) 상태 코드를 포함 하는 서버 응답을 생성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-206">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="5b4c6-207">요청 메서드는 GET 또는 HEAD 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-207">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="5b4c6-208">`Startup.Configure`에서 캐싱은 캐싱이 필요한 미들웨어 앞에 배치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-208">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="5b4c6-209">자세한 내용은 <xref:fundamentals/middleware/index>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-209">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="5b4c6-210">`Authorization` 헤더가 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-210">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="5b4c6-211">`Cache-Control` 헤더 매개 변수는 유효 해야 하 고 응답을 `public`로 표시 하 고 `private`표시 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-211">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="5b4c6-212">`Cache-Control` 헤더가 있을 때 `Pragma` 헤더를 재정의 하기 때문에 `Cache-Control` 헤더가 없으면 `Pragma: no-cache` 헤더가 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-212">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="5b4c6-213">`Set-Cookie` 헤더가 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-213">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="5b4c6-214">`Vary` 헤더 매개 변수는 유효 해야 하며 `*`와 같지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-214">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="5b4c6-215">`Content-Length` 헤더 값 (설정 된 경우)은 응답 본문의 크기와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-215">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="5b4c6-216"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-216">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="5b4c6-217">응답은 `Expires` 헤더에 지정 된 대로 유효 하지 않아야 하 고 `max-age` 및 `s-maxage` 캐시 지시문에 의해 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-217">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="5b4c6-218">응답 버퍼링이 성공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-218">Response buffering must be successful.</span></span> <span data-ttu-id="5b4c6-219">응답의 크기는 구성 된 또는 기본 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-219">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="5b4c6-220">응답의 본문 크기는 구성 된 또는 기본 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-220">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="5b4c6-221">[RFC 7234](https://tools.ietf.org/html/rfc7234) 사양에 따라 응답을 캐시할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-221">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="5b4c6-222">예를 들어 `no-store` 지시문은 요청 또는 응답 헤더 필드에 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-222">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="5b4c6-223">자세한 내용은 섹션 3: [RFC 7234](https://tools.ietf.org/html/rfc7234) 의 *캐시에 응답 저장* 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-223">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="5b4c6-224">CSRF (교차 사이트 요청 위조) 공격을 방지 하기 위해 CSRF (교차 사이트 요청 위조) 공격을 방지 하기 위한 위조 방지 시스템은 응답이 캐시 되지 않도록 `Cache-Control` 및 `Pragma` 헤더를 `no-cache` 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-224">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="5b4c6-225">HTML 양식 요소에 대 한 위조 방지 토큰을 사용 하지 않도록 설정 하는 방법에 대 한 자세한 내용은 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5b4c6-225">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b4c6-226">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5b4c6-226">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
