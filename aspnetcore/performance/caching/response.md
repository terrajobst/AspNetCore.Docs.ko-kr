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
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="db359-103">ASP.NET core에서 응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="db359-103">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="db359-104">작성자: [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="db359-104">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="db359-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db359-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="db359-106">응답 캐싱은 클라이언트나 프록시가 웹 서버에 요청하는 회수를 줄여줍니다.</span><span class="sxs-lookup"><span data-stu-id="db359-106">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="db359-107">또한 응답 캐싱은 웹 서버가 응답을 생성하기 위해 수행해야 하는 작업의 총량도 줄여줍니다.</span><span class="sxs-lookup"><span data-stu-id="db359-107">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="db359-108">응답 캐싱은 클라이언트, 프록시, 및 미들웨어가 응답을 캐싱해야 하는 방식을 지시하는 헤더에 의해 제어됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-108">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="db359-109">합니다 [ResponseCache 특성](#responsecache-attribute) 응답 헤더는 클라이언트가 응답을 캐시 하는 경우 적용 될 수 있습니다 캐싱 설정에 참여 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-109">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers, which clients may honor when caching responses.</span></span> <span data-ttu-id="db359-110">[응답 캐싱 미들웨어](xref:performance/caching/middleware) 서버의 응답을 캐시 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-110">[Response Caching Middleware](xref:performance/caching/middleware) can be used to cache responses on the server.</span></span> <span data-ttu-id="db359-111">미들웨어를 사용 하 여 수 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 속성 서버 쪽 캐싱 동작에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="db359-111">The middleware can use <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="db359-112">HTTP 기반 응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="db359-112">HTTP-based response caching</span></span>

<span data-ttu-id="db359-113">[HTTP 1.1 캐싱 사양](https://tools.ietf.org/html/rfc7234)은 인터넷 캐시가 동작해야 하는 방식을 기술합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-113">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="db359-114">캐싱에 사용되는 가장 기본적인 HTTP 헤더는 [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2)로 캐시 *지시문*을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-114">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="db359-115">지시문은 요청 방식으로 클라이언트에서 서버로 인해 및 응답으로 서버에서 클라이언트에 다시 확인 하는 대로 캐싱 동작을 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-115">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="db359-116">프록시 서버를 통해서 이동하는 요청 및 응답, 그리고 프록시 서버 역시 HTTP 1.1 캐싱 사양을 준수해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-116">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="db359-117">기본적인 `Cache-Control` 지시문은 다음 표와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="db359-117">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="db359-118">지시문</span><span class="sxs-lookup"><span data-stu-id="db359-118">Directive</span></span>                                                       | <span data-ttu-id="db359-119">작업</span><span class="sxs-lookup"><span data-stu-id="db359-119">Action</span></span> |
| --------------------------------------------------------------- | ------ |
| [<span data-ttu-id="db359-120">public</span><span class="sxs-lookup"><span data-stu-id="db359-120">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | <span data-ttu-id="db359-121">캐시에 응답을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db359-121">A cache may store the response.</span></span> |
| [<span data-ttu-id="db359-122">private</span><span class="sxs-lookup"><span data-stu-id="db359-122">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | <span data-ttu-id="db359-123">공유 캐시는 응답을 저장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db359-123">The response must not be stored by a shared cache.</span></span> <span data-ttu-id="db359-124">사설 캐시는 응답을 저장하고 재사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db359-124">A private cache may store and reuse the response.</span></span> |
| [<span data-ttu-id="db359-125">max-age</span><span class="sxs-lookup"><span data-stu-id="db359-125">max-age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | <span data-ttu-id="db359-126">클라이언트는 오래 된 시간 (초) 지정 된 숫자 보다 큰 경우 응답을 수락 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db359-126">The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="db359-127">예를 들면 다음과 같습니다. `max-age=60` (60 초), `max-age=2592000` (1 월)</span><span class="sxs-lookup"><span data-stu-id="db359-127">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month)</span></span> |
| [<span data-ttu-id="db359-128">no-cache</span><span class="sxs-lookup"><span data-stu-id="db359-128">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | <span data-ttu-id="db359-129">**요청에**: 캐시 요청을 충족 하도록 저장된 응답을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-129">**On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="db359-130">원본 서버는 클라이언트에 대 한 응답을 다시 생성 하 고 미들웨어 저장된 응답을 캐시를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-130">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="db359-131">**응답에서**: 원본 서버에서 유효성을 검사 하지 않고 후속 요청에 대 한 응답을 사용 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-131">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> |
| [<span data-ttu-id="db359-132">no-store</span><span class="sxs-lookup"><span data-stu-id="db359-132">no-store</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | <span data-ttu-id="db359-133">**요청에**: 캐시는 요청을 저장 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-133">**On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="db359-134">**응답에서**: 캐시에서 응답의 일부를 저장 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-134">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="db359-135">캐싱 역할을 수행하는 다른 캐시 헤더는 다음 표와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="db359-135">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="db359-136">헤더</span><span class="sxs-lookup"><span data-stu-id="db359-136">Header</span></span>                                                     | <span data-ttu-id="db359-137">기능</span><span class="sxs-lookup"><span data-stu-id="db359-137">Function</span></span> |
| ---------------------------------------------------------- | -------- |
| [<span data-ttu-id="db359-138">Age</span><span class="sxs-lookup"><span data-stu-id="db359-138">Age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.1)     | <span data-ttu-id="db359-139">응답이 생성되거나 원본 서버에서 정상적으로 검증된 이후로부터 지난 초 단위의 총 추정 시간.</span><span class="sxs-lookup"><span data-stu-id="db359-139">An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> |
| [<span data-ttu-id="db359-140">Expires</span><span class="sxs-lookup"><span data-stu-id="db359-140">Expires</span></span>](https://tools.ietf.org/html/rfc7234#section-5.3) | <span data-ttu-id="db359-141">부실 코드가 시간이 지나면 응답 것으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-141">The time after which the response is considered stale.</span></span> |
| [<span data-ttu-id="db359-142">Pragma</span><span class="sxs-lookup"><span data-stu-id="db359-142">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)  | <span data-ttu-id="db359-143">`no-cache` 동작 설정에 대한 HTTP/1.0 캐시와의 하위 호환성을 지원하기 위해 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-143">Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="db359-144">`Cache-Control` 헤더가 존재할 경우 `Pragma` 헤더는 무시됩니다. </span><span class="sxs-lookup"><span data-stu-id="db359-144">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> |
| [<span data-ttu-id="db359-145">Vary</span><span class="sxs-lookup"><span data-stu-id="db359-145">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | <span data-ttu-id="db359-146">모든 `Vary` 헤더 필드가 캐시된 응답의 원본 요청 및 새 요청 모두와 일치하지 않는 한 캐시된 응답이 전송되지 않도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-146">Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="db359-147">HTTP 기반 캐싱은 Cache-Control 지시문을 준수합니다</span><span class="sxs-lookup"><span data-stu-id="db359-147">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="db359-148">[Cache-Control 헤더에 대한 HTTP 1.1 캐싱 사양](https://tools.ietf.org/html/rfc7234#section-5.2)은 캐시에게 클라이언트가 전송하는 유효한 `Cache-Control` 헤더를 준수할 것을 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-148">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="db359-149">클라이언트는 `no-cache` 헤더 값을 설정한 요청을 전송함으로써 모든 요청에 대해 서버가 새로운 응답을 생성하도록 강제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db359-149">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="db359-150">HTTP 캐싱의 목적을 고려했을 때 항상 클라이언트의 `Cache-Control` 요청 헤더를 준수하는 것이 이치에 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="db359-150">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="db359-151">공식 사양에서 캐싱은 클라이언트, 프록시 및 서버 간의 네트워크에서 요청의 대기 시간 및 네트워크 오버헤드를 만족스럽게 줄이기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="db359-151">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="db359-152">원본 서버에서 부하를 제어하기 위한 방법이 필수적인 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="db359-152">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="db359-153">사용 하는 경우이 캐싱 동작을 통해 개발자 컨트롤이 없는 합니다 [응답 캐싱 미들웨어](xref:performance/caching/middleware) 미들웨어 캐싱 사양 공식 준수 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="db359-153">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="db359-154">[미들웨어의 향상 된 기능을 계획](https://github.com/aspnet/AspNetCore/issues/2612) 요청을 무시 하도록 미들웨어를 구성 하는 기회는 `Cache-Control` 헤더 캐시 된 응답을 제공 하고자 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="db359-154">[Planned enhancements to the middleware](https://github.com/aspnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="db359-155">계획 된 향상 된 기능에는 제어 서버 부하를 효과적으로 기회를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-155">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="db359-156">ASP.NET Core의 다른 캐싱 기술</span><span class="sxs-lookup"><span data-stu-id="db359-156">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="db359-157">메모리 내 캐싱</span><span class="sxs-lookup"><span data-stu-id="db359-157">In-memory caching</span></span>

<span data-ttu-id="db359-158">메모리 내 캐싱은 서버의 메모리를 이용해서 캐시된 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-158">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="db359-159">이 방식의 캐싱은 단일 서버나 *고정 세션*을 사용하는 복수의 서버에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-159">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="db359-160">고정 세션은 클라이언트의 요청이 처리를 위해 항상 동일한 서버로 라우트된다는 것을 뜻합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-160">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="db359-161">자세한 내용은 <xref:performance/caching/memory>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db359-161">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="db359-162">분산 캐시</span><span class="sxs-lookup"><span data-stu-id="db359-162">Distributed Cache</span></span>

<span data-ttu-id="db359-163">응용 프로그램이 클라우드나 서버 팜에서 호스팅될 때 메모리에 데이터를 저장하려면 분산 캐시를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-163">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="db359-164">이 캐시는 요청을 처리하는 서버들 간에 서로 공유됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-164">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="db359-165">클라이언트에 대한 캐시 데이터를 사용할 수 있는 경우 클라이언트는 그룹의 어떤 서버에서나 처리할 수 있는 요청을 제출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db359-165">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="db359-166">ASP.NET Core는 SQL Server 및 Redis 분산 캐시를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-166">ASP.NET Core offers SQL Server and Redis distributed caches.</span></span>

<span data-ttu-id="db359-167">자세한 내용은 <xref:performance/caching/distributed>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db359-167">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="db359-168">캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="db359-168">Cache Tag Helper</span></span>

<span data-ttu-id="db359-169">캐시 태그 도우미를 사용 하 여 MVC 뷰 또는 Razor 페이지 콘텐츠를 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-169">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="db359-170">캐시 태그 도우미는 메모리 내 캐시에 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-170">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="db359-171">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db359-171">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="db359-172">분산 캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="db359-172">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="db359-173">분산 캐시 태그 도우미를 사용 하 여 분산된 클라우드 또는 웹 팜 시나리오에는 Razor 페이지나 MVC 보기에서 해당 콘텐츠를 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-173">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="db359-174">분산 캐시 태그 도우미는 SQL Server 또는 Redis에 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-174">The Distributed Cache Tag Helper uses SQL Server or Redis to store data.</span></span>

<span data-ttu-id="db359-175">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db359-175">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="db359-176">ResponseCache 특성</span><span class="sxs-lookup"><span data-stu-id="db359-176">ResponseCache attribute</span></span>

<span data-ttu-id="db359-177"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 응답 캐싱에서 적절 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-177">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="db359-178">인증된 클라이언트의 정보를 포함하는 콘텐츠는 캐싱하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-178">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="db359-179">사용자 ID 또는 사용자 로그인 여부와 무관하게 변경되지 않는 콘텐츠만 캐싱해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-179">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="db359-180"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 저장된 응답 지정된 목록 쿼리 키의 값에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="db359-180"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="db359-181">단일 값으로 `*`를 지정하면 모든 쿼리 문자열 매개 변수별로 미들웨어의 응답이 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="db359-181">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="db359-182">[응답 캐싱 미들웨어](xref:performance/caching/middleware) 설정에 사용 하도록 설정 해야 합니다는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="db359-182">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="db359-183">그렇지 않으면 런타임 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-183">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="db359-184"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 속성에 대응하는 HTTP 헤더는 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db359-184">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="db359-185">속성이 응답 캐싱 미들웨어에서 처리 하는 HTTP 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="db359-185">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="db359-186">미들웨어가 캐시된 응답을 제공하려면 쿼리 문자열 및 쿼리 문자열 값이 이전 요청과 동일해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-186">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="db359-187">예를 들어, 다음 표는 요청 순서에 따른 결과를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="db359-187">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="db359-188">요청</span><span class="sxs-lookup"><span data-stu-id="db359-188">Request</span></span>                          | <span data-ttu-id="db359-189">결과</span><span class="sxs-lookup"><span data-stu-id="db359-189">Result</span></span>                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | <span data-ttu-id="db359-190">서버에서 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-190">Returned from the server.</span></span> |
| `http://example.com?key1=value1` | <span data-ttu-id="db359-191">미들웨어에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-191">Returned from middleware.</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="db359-192">서버에서 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-192">Returned from the server.</span></span> |

<span data-ttu-id="db359-193">첫 번째 요청은 서버에서 반환되고 미들웨어에 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-193">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="db359-194">두 번째 요청은 쿼리 문자열이 이전 요청과 일치하기 때문에 미들웨어에서 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-194">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="db359-195">세 번째 요청은 쿼리 문자열 값이 이전 요청과 일치하지 않기 때문에 미들웨어 캐시에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db359-195">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="db359-196">합니다 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 만들고 구성 하는 데 사용 됩니다 (통해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>)는 <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter>합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-196">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter>.</span></span> <span data-ttu-id="db359-197"><xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter> 적절 한 HTTP 헤더 및 응답의 기능 업데이트 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-197">The <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter> performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="db359-198">필터:</span><span class="sxs-lookup"><span data-stu-id="db359-198">The filter:</span></span>

* <span data-ttu-id="db359-199">기존에 존재하는 `Vary`, `Cache-Control` 및 `Pragma` 헤더를 모두 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-199">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="db359-200"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>에 설정된 속성을 기반으로 적절한 헤더를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-200">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="db359-201"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 속성이 설정된 경우 응답 캐싱 HTTP 기능을 갱신합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-201">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="db359-202">Vary</span><span class="sxs-lookup"><span data-stu-id="db359-202">Vary</span></span>

<span data-ttu-id="db359-203">이 헤더는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 속성이 설정된 경우에만 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-203">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="db359-204">속성을로 설정 된 `Vary` 속성의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="db359-204">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="db359-205">다음 예제는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 속성을 사용합니다:</span><span class="sxs-lookup"><span data-stu-id="db359-205">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="db359-206">샘플 앱을 사용 하 여 브라우저의 네트워크 도구를 사용 하 여 응답 헤더를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="db359-206">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="db359-207">다음 응답 헤더에 Cache1 페이지 응답과 함께 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-207">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="db359-208">NoStore 및 Location.None</span><span class="sxs-lookup"><span data-stu-id="db359-208">NoStore and Location.None</span></span>

<span data-ttu-id="db359-209"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 속성은 다른 대부분의 속성을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="db359-209"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="db359-210">이 속성이 `true`로 설정되면 `Cache-Control` 헤더가 `no-store`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-210">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="db359-211">만약 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>이 `None`으로 설정되면:</span><span class="sxs-lookup"><span data-stu-id="db359-211">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="db359-212">`Cache-Control`이 `no-store,no-cache`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-212">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="db359-213">`Pragma`이 `no-cache`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-213">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="db359-214">경우 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 됩니다 `false` 및 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 은 `None`, `Cache-Control`, 및 `Pragma` 으로 설정 됩니다 `no-cache`합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-214">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="db359-215"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 일반적으로 `true` 오류 페이지에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-215"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="db359-216">샘플 앱에서 Cache2 페이지를 응답을 저장할 수 없도록 클라이언트에 게 지시 하는 응답 헤더를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-216">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="db359-217">다음 헤더를 사용 하 여 Cache2 페이지를 반환 하는 샘플 앱:</span><span class="sxs-lookup"><span data-stu-id="db359-217">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="db359-218">Location 및 Duration</span><span class="sxs-lookup"><span data-stu-id="db359-218">Location and Duration</span></span>

<span data-ttu-id="db359-219">캐싱을 사용하기 위해서는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration>을 양수 값으로 설정하고 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>이 `Any` (기본값) 또는 `Client`이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-219">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="db359-220">이 경우 `Cache-Control` 헤더는 응답의 `max-age`가 뒤에 추가된 위치 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-220">In this case, the `Cache-Control` header is set to the location value followed by the `max-age` of the response.</span></span>

> [!NOTE]
> <span data-ttu-id="db359-221"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 옵션의 `Any`와 `Client`는 각각 `Cache-Control` 헤더의 값, `public`과 `private`로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-221"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="db359-222">앞에서 설명한 것처럼 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>을 `None`으로 설정하면 `Cache-Control` 및 `Pragma` 헤더가 모두 `no-cache`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-222">As noted previously, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="db359-223">다음 예제에서는 샘플 앱 및 설정 하 여 생성 된 헤더에서 Cache3 페이지 모델을 보여 줍니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 기본값을 그대로 두고 및 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 값:</span><span class="sxs-lookup"><span data-stu-id="db359-223">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="db359-224">샘플 앱은 다음 헤더를 사용 하 여 Cache3 페이지를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-224">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="db359-225">캐시 프로필</span><span class="sxs-lookup"><span data-stu-id="db359-225">Cache profiles</span></span>

<span data-ttu-id="db359-226">여러 컨트롤러 작업 특성에 대 한 응답 캐시 설정을 복제 하는 대신 캐시 프로필으로 구성할 수 있습니다 옵션의 MVC/Razor 페이지를 설정할 때 `Startup.ConfigureServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-226">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="db359-227">참조 된 캐시 프로필의 값이는 기본적으로 사용 됩니다는 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 특성에 지정 된 모든 속성에 의해 재정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db359-227">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="db359-228">캐시 프로필을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-228">Set up a cache profile.</span></span> <span data-ttu-id="db359-229">다음 예제에서는 샘플 앱의 두 번째 30 캐시 프로필을 보여 줍니다. `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="db359-229">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="db359-230">샘플 앱의 Cache4 페이지 모델 참조의 `Default30` 프로필을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-230">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="db359-231"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db359-231">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* <span data-ttu-id="db359-232">Razor 페이지 처리기 (클래스) &ndash; 처리기 메서드에 특성을 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="db359-232">Razor Page handlers (classes) &ndash; Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="db359-233">MVC 컨트롤러 (클래스)입니다.</span><span class="sxs-lookup"><span data-stu-id="db359-233">MVC controllers (classes).</span></span>
* <span data-ttu-id="db359-234">MVC 작업 (메서드) &ndash; 메서드 수준 특성이 클래스 수준 특성에 지정 된 설정을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="db359-234">MVC actions (methods) &ndash; Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="db359-235">결과 헤더 Cache4 페이지 응답에 적용 된 `Default30` 프로필 캐시:</span><span class="sxs-lookup"><span data-stu-id="db359-235">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="db359-236">추가 자료</span><span class="sxs-lookup"><span data-stu-id="db359-236">Additional resources</span></span>

* [<span data-ttu-id="db359-237">캐시에 응답 저장하기</span><span class="sxs-lookup"><span data-stu-id="db359-237">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="db359-238">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="db359-238">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
