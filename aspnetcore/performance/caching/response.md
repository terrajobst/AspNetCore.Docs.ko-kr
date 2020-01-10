---
title: ASP.NET Core의 응답 캐싱
author: rick-anderson
description: 응답 캐싱을 사용하여 ASP.NET Core 앱의 성능을 향상하고 대역폭 요구 사항을 낮추는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
uid: performance/caching/response
ms.openlocfilehash: 9246305e6979a6a7e006f567ee6bf9569029aef1
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828310"
---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="8ecbd-103">ASP.NET Core의 응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="8ecbd-103">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="8ecbd-104">작성자: [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8ecbd-104">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8ecbd-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8ecbd-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8ecbd-106">응답 캐싱은 클라이언트나 프록시가 웹 서버에 요청하는 회수를 줄여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-106">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="8ecbd-107">또한 응답 캐싱은 웹 서버가 응답을 생성하기 위해 수행해야 하는 작업의 총량도 줄여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-107">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="8ecbd-108">응답 캐싱은 클라이언트, 프록시, 및 미들웨어가 응답을 캐싱해야 하는 방식을 지시하는 헤더에 의해 제어됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-108">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="8ecbd-109">[ResponseCache 특성](#responsecache-attribute) 은 응답 캐싱 헤더 설정에 참여 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-109">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="8ecbd-110">클라이언트 및 중간 프록시는 [HTTP 1.1 캐싱 사양](https://tools.ietf.org/html/rfc7234)에 따라 응답을 캐시 하는 데 헤더를 적용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-110">Clients and intermediate proxies should honor the headers for caching responses under the the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="8ecbd-111">HTTP 1.1 캐싱 사양을 따르는 서버 쪽 캐싱의 경우 [응답 캐싱 미들웨어](xref:performance/caching/middleware)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-111">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="8ecbd-112">미들웨어는 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 속성을 사용 하 여 서버 쪽 캐싱 동작에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-112">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="8ecbd-113">HTTP 기반 응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="8ecbd-113">HTTP-based response caching</span></span>

<span data-ttu-id="8ecbd-114">[HTTP 1.1 캐싱 사양](https://tools.ietf.org/html/rfc7234)은 인터넷 캐시가 동작해야 하는 방식을 기술합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-114">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="8ecbd-115">캐싱에 사용되는 가장 기본적인 HTTP 헤더는 [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2)로 캐시 *지시문*을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-115">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="8ecbd-116">지시문은 요청이 클라이언트에서 서버로 이동 하 고 응답을 통해 서버에서 클라이언트로 다시 이동 하는 방식으로 캐싱 동작을 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-116">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="8ecbd-117">프록시 서버를 통해서 이동하는 요청 및 응답, 그리고 프록시 서버 역시 HTTP 1.1 캐싱 사양을 준수해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-117">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="8ecbd-118">기본적인 `Cache-Control` 지시문은 다음 표와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-118">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="8ecbd-119">Directive</span><span class="sxs-lookup"><span data-stu-id="8ecbd-119">Directive</span></span>                                                       | <span data-ttu-id="8ecbd-120">동작</span><span class="sxs-lookup"><span data-stu-id="8ecbd-120">Action</span></span> |
| --------------------------------------------------------------- | ------ |
| [<span data-ttu-id="8ecbd-121">public</span><span class="sxs-lookup"><span data-stu-id="8ecbd-121">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | <span data-ttu-id="8ecbd-122">캐시에 응답을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-122">A cache may store the response.</span></span> |
| [<span data-ttu-id="8ecbd-123">private</span><span class="sxs-lookup"><span data-stu-id="8ecbd-123">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | <span data-ttu-id="8ecbd-124">공유 캐시는 응답을 저장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-124">The response must not be stored by a shared cache.</span></span> <span data-ttu-id="8ecbd-125">사설 캐시는 응답을 저장하고 재사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-125">A private cache may store and reuse the response.</span></span> |
| [<span data-ttu-id="8ecbd-126">max-age</span><span class="sxs-lookup"><span data-stu-id="8ecbd-126">max-age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | <span data-ttu-id="8ecbd-127">클라이언트는 나이가 지정 된 시간 (초) 보다 큰 응답을 수락 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-127">The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="8ecbd-128">예: `max-age=60` (60초), `max-age=2592000` (1개월)</span><span class="sxs-lookup"><span data-stu-id="8ecbd-128">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month)</span></span> |
| [<span data-ttu-id="8ecbd-129">no-cache</span><span class="sxs-lookup"><span data-stu-id="8ecbd-129">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | <span data-ttu-id="8ecbd-130">**요청 시**: 캐시는 저장된 응답을 사용해서 요청에 대응하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-130">**On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="8ecbd-131">원본 서버는 클라이언트에 대 한 응답을 다시 생성 하 고 미들웨어는 캐시에 저장 된 응답을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-131">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="8ecbd-132">**응답 시**: 원본 서버에서 유효성을 검사받지 않은 응답을 후속 요청에 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-132">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> |
| [<span data-ttu-id="8ecbd-133">no-store</span><span class="sxs-lookup"><span data-stu-id="8ecbd-133">no-store</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | <span data-ttu-id="8ecbd-134">**요청 시**: 캐시가 요청을 저장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-134">**On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="8ecbd-135">**응답 시**: 캐시가 응답의 모든 부분에서 응답을 저장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-135">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="8ecbd-136">캐싱 역할을 수행하는 다른 캐시 헤더는 다음 표와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-136">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="8ecbd-137">Header</span><span class="sxs-lookup"><span data-stu-id="8ecbd-137">Header</span></span>                                                     | <span data-ttu-id="8ecbd-138">기능</span><span class="sxs-lookup"><span data-stu-id="8ecbd-138">Function</span></span> |
| ---------------------------------------------------------- | -------- |
| [<span data-ttu-id="8ecbd-139">Age</span><span class="sxs-lookup"><span data-stu-id="8ecbd-139">Age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.1)     | <span data-ttu-id="8ecbd-140">응답이 생성되거나 원본 서버에서 정상적으로 검증된 이후로부터 지난 초 단위의 총 추정 시간.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-140">An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> |
| [<span data-ttu-id="8ecbd-141">Expires</span><span class="sxs-lookup"><span data-stu-id="8ecbd-141">Expires</span></span>](https://tools.ietf.org/html/rfc7234#section-5.3) | <span data-ttu-id="8ecbd-142">응답이 오래 된 것으로 간주 되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-142">The time after which the response is considered stale.</span></span> |
| [<span data-ttu-id="8ecbd-143">Pragma</span><span class="sxs-lookup"><span data-stu-id="8ecbd-143">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)  | <span data-ttu-id="8ecbd-144">`no-cache` 동작 설정에 대한 HTTP/1.0 캐시와의 하위 호환성을 지원하기 위해 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-144">Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="8ecbd-145">`Cache-Control` 헤더가 존재할 경우 `Pragma` 헤더는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-145">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> |
| [<span data-ttu-id="8ecbd-146">Vary</span><span class="sxs-lookup"><span data-stu-id="8ecbd-146">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | <span data-ttu-id="8ecbd-147">모든 `Vary` 헤더 필드가 캐시된 응답의 원본 요청 및 새 요청 모두와 일치하지 않는 한 캐시된 응답이 전송되지 않도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-147">Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="8ecbd-148">HTTP 기반 캐싱은 Cache-Control 지시문을 준수합니다</span><span class="sxs-lookup"><span data-stu-id="8ecbd-148">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="8ecbd-149">[Cache-Control 헤더에 대한 HTTP 1.1 캐싱 사양](https://tools.ietf.org/html/rfc7234#section-5.2)은 캐시에게 클라이언트가 전송하는 유효한 `Cache-Control` 헤더를 준수할 것을 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-149">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="8ecbd-150">클라이언트는 `no-cache` 헤더 값을 설정한 요청을 전송함으로써 모든 요청에 대해 서버가 새로운 응답을 생성하도록 강제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-150">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="8ecbd-151">HTTP 캐싱의 목적을 고려했을 때 항상 클라이언트의 `Cache-Control` 요청 헤더를 준수하는 것이 이치에 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-151">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="8ecbd-152">공식 사양에서 캐싱은 클라이언트, 프록시 및 서버 간의 네트워크에서 요청의 대기 시간 및 네트워크 오버헤드를 만족스럽게 줄이기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-152">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="8ecbd-153">원본 서버에서 부하를 제어하기 위한 방법이 필수적인 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-153">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="8ecbd-154">미들웨어가 공식 캐싱 사양을 준수 하기 때문에 [응답 캐싱 미들웨어](xref:performance/caching/middleware) 를 사용 하는 경우이 캐싱 동작에 대 한 개발자 제어는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-154">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="8ecbd-155">[미들웨어에 대해 계획 된 향상 된 기능은](https://github.com/dotnet/AspNetCore/issues/2612) 캐시 된 응답을 제공 하도록 결정할 때 요청의 `Cache-Control` 헤더를 무시 하도록 미들웨어를 구성 하는 기회입니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-155">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="8ecbd-156">계획 된 향상 된 기능을 통해 서버 부하를 보다 효과적으로 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-156">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="8ecbd-157">ASP.NET Core의 다른 캐싱 기술</span><span class="sxs-lookup"><span data-stu-id="8ecbd-157">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="8ecbd-158">메모리 내 캐싱</span><span class="sxs-lookup"><span data-stu-id="8ecbd-158">In-memory caching</span></span>

<span data-ttu-id="8ecbd-159">메모리 내 캐싱은 서버의 메모리를 이용해서 캐시된 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-159">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="8ecbd-160">이 방식의 캐싱은 단일 서버나 *고정 세션*을 사용하는 복수의 서버에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-160">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="8ecbd-161">고정 세션은 클라이언트의 요청이 처리를 위해 항상 동일한 서버로 라우트된다는 것을 뜻합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-161">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="8ecbd-162">자세한 내용은 <xref:performance/caching/memory>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-162">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="8ecbd-163">분산 캐시</span><span class="sxs-lookup"><span data-stu-id="8ecbd-163">Distributed Cache</span></span>

<span data-ttu-id="8ecbd-164">응용 프로그램이 클라우드나 서버 팜에서 호스팅될 때 메모리에 데이터를 저장하려면 분산 캐시를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-164">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="8ecbd-165">이 캐시는 요청을 처리하는 서버들 간에 서로 공유됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-165">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="8ecbd-166">클라이언트에 대한 캐시 데이터를 사용할 수 있는 경우 클라이언트는 그룹의 어떤 서버에서나 처리할 수 있는 요청을 제출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-166">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="8ecbd-167">ASP.NET Core는 SQL Server 및 Redis 분산 캐시를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-167">ASP.NET Core offers SQL Server and Redis distributed caches.</span></span>

<span data-ttu-id="8ecbd-168">자세한 내용은 <xref:performance/caching/distributed>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-168">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="8ecbd-169">캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="8ecbd-169">Cache Tag Helper</span></span>

<span data-ttu-id="8ecbd-170">캐시 태그 도우미를 사용 하 여 MVC 뷰나 Razor 페이지에서 콘텐츠를 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-170">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="8ecbd-171">캐시 태그 도우미는 메모리 내 캐시에 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-171">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="8ecbd-172">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-172">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="8ecbd-173">분산 캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="8ecbd-173">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="8ecbd-174">분산 된 캐시 태그 도우미를 사용 하 여 분산 클라우드 또는 웹 팜 시나리오의 MVC 보기 또는 Razor 페이지에서 콘텐츠를 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-174">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="8ecbd-175">분산 캐시 태그 도우미는 SQL Server 또는 Redis에 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-175">The Distributed Cache Tag Helper uses SQL Server or Redis to store data.</span></span>

<span data-ttu-id="8ecbd-176">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-176">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="8ecbd-177">ResponseCache 특성</span><span class="sxs-lookup"><span data-stu-id="8ecbd-177">ResponseCache attribute</span></span>

<span data-ttu-id="8ecbd-178"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>는 응답 캐싱에 적절 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-178">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="8ecbd-179">인증된 클라이언트의 정보를 포함하는 콘텐츠는 캐싱하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-179">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="8ecbd-180">사용자 ID 또는 사용자 로그인 여부와 무관하게 변경되지 않는 콘텐츠만 캐싱해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-180">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="8ecbd-181"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>는 지정 된 쿼리 키 목록 값에 따라 저장 된 응답을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-181"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="8ecbd-182">단일 값으로 `*`를 지정하면 모든 쿼리 문자열 매개 변수별로 미들웨어의 응답이 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-182">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="8ecbd-183"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 속성을 설정 하려면 [응답 캐싱 미들웨어](xref:performance/caching/middleware) 를 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-183">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="8ecbd-184">그렇지 않으면 런타임 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-184">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="8ecbd-185"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 속성에 대응하는 HTTP 헤더는 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-185">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="8ecbd-186">속성은 응답 캐싱 미들웨어에 의해 처리 되는 HTTP 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-186">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="8ecbd-187">미들웨어가 캐시된 응답을 제공하려면 쿼리 문자열 및 쿼리 문자열 값이 이전 요청과 동일해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-187">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="8ecbd-188">예를 들어, 다음 표는 요청 순서에 따른 결과를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-188">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="8ecbd-189">요청</span><span class="sxs-lookup"><span data-stu-id="8ecbd-189">Request</span></span>                          | <span data-ttu-id="8ecbd-190">결과</span><span class="sxs-lookup"><span data-stu-id="8ecbd-190">Result</span></span>                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | <span data-ttu-id="8ecbd-191">서버에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-191">Returned from the server.</span></span> |
| `http://example.com?key1=value1` | <span data-ttu-id="8ecbd-192">미들웨어에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-192">Returned from middleware.</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="8ecbd-193">서버에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-193">Returned from the server.</span></span> |

<span data-ttu-id="8ecbd-194">첫 번째 요청은 서버에서 반환되고 미들웨어에 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-194">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="8ecbd-195">두 번째 요청은 쿼리 문자열이 이전 요청과 일치하기 때문에 미들웨어에서 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-195">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="8ecbd-196">세 번째 요청은 쿼리 문자열 값이 이전 요청과 일치하지 않기 때문에 미들웨어 캐시에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-196">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="8ecbd-197"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>은 `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`를 통해 구성 하 고 만드는 데 사용 됩니다 (<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>).</span><span class="sxs-lookup"><span data-stu-id="8ecbd-197">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="8ecbd-198">`ResponseCacheFilter`은 적절 한 HTTP 헤더와 응답의 기능을 업데이트 하는 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-198">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="8ecbd-199">필터:</span><span class="sxs-lookup"><span data-stu-id="8ecbd-199">The filter:</span></span>

* <span data-ttu-id="8ecbd-200">기존에 존재하는 `Vary`, `Cache-Control` 및 `Pragma` 헤더를 모두 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-200">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="8ecbd-201"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>에 설정된 속성을 기반으로 적절한 헤더를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-201">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="8ecbd-202"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 속성이 설정된 경우 응답 캐싱 HTTP 기능을 갱신합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-202">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="8ecbd-203">Vary</span><span class="sxs-lookup"><span data-stu-id="8ecbd-203">Vary</span></span>

<span data-ttu-id="8ecbd-204">이 헤더는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 속성이 설정된 경우에만 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-204">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="8ecbd-205">속성 `Vary` 속성 값으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-205">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="8ecbd-206">다음 예제는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 속성을 사용합니다:</span><span class="sxs-lookup"><span data-stu-id="8ecbd-206">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="8ecbd-207">샘플 앱을 사용 하 여 브라우저의 네트워크 도구를 사용 하 여 응답 헤더를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-207">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="8ecbd-208">다음 응답 헤더는 Cache1 페이지 응답과 함께 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-208">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="8ecbd-209">NoStore 및 위치입니다. 없음</span><span class="sxs-lookup"><span data-stu-id="8ecbd-209">NoStore and Location.None</span></span>

<span data-ttu-id="8ecbd-210"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 속성은 다른 대부분의 속성을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-210"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="8ecbd-211">이 속성이 `true`로 설정되면 `Cache-Control` 헤더가 `no-store`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-211">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="8ecbd-212">만약 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>이 `None`으로 설정되면:</span><span class="sxs-lookup"><span data-stu-id="8ecbd-212">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="8ecbd-213">`Cache-Control`이 `no-store,no-cache`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-213">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="8ecbd-214">`Pragma`이 `no-cache`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-214">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="8ecbd-215"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> `false` 하 고 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> `None`경우 `Cache-Control`및 `Pragma`가 `no-cache`로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-215">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="8ecbd-216"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>는 일반적으로 오류 페이지에 대해 `true`로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-216"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="8ecbd-217">샘플 앱의 Cache2 페이지는 클라이언트에 응답을 저장 하지 않도록 지시 하는 응답 헤더를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-217">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="8ecbd-218">샘플 앱은 다음 헤더를 포함 하는 Cache2 페이지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-218">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="8ecbd-219">Location 및 Duration</span><span class="sxs-lookup"><span data-stu-id="8ecbd-219">Location and Duration</span></span>

<span data-ttu-id="8ecbd-220">캐싱을 사용하기 위해서는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration>을 양수 값으로 설정하고 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>이 `Any` (기본값) 또는 `Client`이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-220">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="8ecbd-221">프레임 워크는 `Cache-Control` 헤더를 위치 값으로 설정 하 고 응답의 `max-age`를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-221">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="8ecbd-222"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 옵션의 `Any`와 `Client`는 각각 `Cache-Control` 헤더의 값, `public`과 `private`로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-222"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="8ecbd-223">[Nostore 및 Location. None](#nostore-and-locationnone) 섹션에서 설명한 대로 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>를 `None` 설정 하면 `Cache-Control`와 `Pragma` 헤더가 모두 `no-cache`로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-223">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="8ecbd-224">`Location.Any` (`public`로 설정`Cache-Control`)는 *클라이언트 또는 중간 프록시가* [응답 캐싱 미들웨어](xref:performance/caching/middleware)를 포함 하 여 값을 캐시할 수 있음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-224">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="8ecbd-225">`Location.Client` (`private`로 설정`Cache-Control`)는 *클라이언트만* 값을 캐시할 수 있음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-225">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="8ecbd-226">[응답 캐싱 미들웨어](xref:performance/caching/middleware)를 포함 하 여 값을 캐시 해야 하는 중간 캐시는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-226">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="8ecbd-227">캐시 컨트롤 헤더는 응답을 캐시 하는 방법 및 방법에 대 한 지침을 클라이언트 및 중간 프록시에 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-227">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="8ecbd-228">클라이언트 및 프록시가 [HTTP 1.1 캐싱 사양을](https://tools.ietf.org/html/rfc7234)인식 한다는 보장이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-228">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="8ecbd-229">[응답 캐싱 미들웨어](xref:performance/caching/middleware) 는 항상 사양에 의해 배치 된 캐싱 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-229">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="8ecbd-230">다음 예에서는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration>를 설정 하 고 기본 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 값을 그대로 두고 생성 된 헤더와 샘플 앱의 Cache3 페이지 모델을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-230">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="8ecbd-231">샘플 앱은 다음 헤더를 포함 하는 Cache3 페이지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-231">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="8ecbd-232">캐시 프로필</span><span class="sxs-lookup"><span data-stu-id="8ecbd-232">Cache profiles</span></span>

<span data-ttu-id="8ecbd-233">여러 컨트롤러 작업 특성에 대 한 응답 캐시 설정을 복제 하는 대신 `Startup.ConfigureServices`에서 MVC/Razor Pages를 설정할 때 캐시 프로필을 옵션으로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-233">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8ecbd-234">참조 된 캐시 프로필에 있는 값은 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>의 기본값으로 사용 되며, 특성에 지정 된 모든 속성에 의해 재정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-234">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="8ecbd-235">캐시 프로필을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-235">Set up a cache profile.</span></span> <span data-ttu-id="8ecbd-236">다음 예제에서는 샘플 앱의 `Startup.ConfigureServices`에 있는 30 초 캐시 프로필을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-236">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="8ecbd-237">샘플 앱의 Cache4 페이지 모델은 `Default30` 캐시 프로필을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-237">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="8ecbd-238"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>는 다음에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-238">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* <span data-ttu-id="8ecbd-239">Razor 페이지 처리기 (클래스) &ndash; 특성은 처리기 메서드에 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-239">Razor Page handlers (classes) &ndash; Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="8ecbd-240">MVC 컨트롤러 (클래스)</span><span class="sxs-lookup"><span data-stu-id="8ecbd-240">MVC controllers (classes).</span></span>
* <span data-ttu-id="8ecbd-241">MVC 작업 (메서드) &ndash; 메서드 수준 특성은 클래스 수준 특성에 지정 된 설정을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-241">MVC actions (methods) &ndash; Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="8ecbd-242">`Default30` 캐시 프로필에의 한 Cache4 페이지 응답에 적용 되는 결과 헤더는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8ecbd-242">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="8ecbd-243">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8ecbd-243">Additional resources</span></span>

* [<span data-ttu-id="8ecbd-244">캐시에 응답 저장하기</span><span class="sxs-lookup"><span data-stu-id="8ecbd-244">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="8ecbd-245">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="8ecbd-245">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
