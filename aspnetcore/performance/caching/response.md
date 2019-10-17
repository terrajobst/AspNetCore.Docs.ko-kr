---
title: ASP.NET Core의 응답 캐싱
author: rick-anderson
description: 응답 캐싱을 사용하여 ASP.NET Core 앱의 성능을 향상하고 대역폭 요구 사항을 낮추는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/15/2019
uid: performance/caching/response
ms.openlocfilehash: 4ebac97689347245d25e0954b33729d78dd1b516
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378827"
---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="36b7c-103">ASP.NET Core의 응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="36b7c-103">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="36b7c-104">작성자, [John 루 오 어](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/)및 [luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="36b7c-104">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="36b7c-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36b7c-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="36b7c-106">응답 캐싱은 클라이언트 또는 프록시가 웹 서버에 대해 수행 하는 요청 수를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-106">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="36b7c-107">응답 캐싱은 웹 서버에서 응답을 생성 하기 위해 수행 하는 작업의 양을 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-107">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="36b7c-108">응답 캐싱은 클라이언트, 프록시 및 미들웨어에서 응답을 캐시 하는 방법을 지정 하는 헤더에 의해 제어 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-108">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="36b7c-109">[ResponseCache 특성](#responsecache-attribute) 은 응답을 캐시할 때 클라이언트가 처리할 수 있는 응답 캐싱 헤더 설정에 참여 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-109">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers, which clients may honor when caching responses.</span></span> <span data-ttu-id="36b7c-110">[응답 캐싱 미들웨어](xref:performance/caching/middleware) 는 서버에서 응답을 캐시 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-110">[Response Caching Middleware](xref:performance/caching/middleware) can be used to cache responses on the server.</span></span> <span data-ttu-id="36b7c-111">미들웨어는 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 속성을 사용 하 여 서버 쪽 캐싱 동작에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-111">The middleware can use <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="36b7c-112">HTTP 기반 응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="36b7c-112">HTTP-based response caching</span></span>

<span data-ttu-id="36b7c-113">[HTTP 1.1 캐싱 사양은](https://tools.ietf.org/html/rfc7234) 인터넷 캐시가 동작 하는 방식을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-113">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="36b7c-114">캐싱에 사용 되는 기본 HTTP 헤더는 캐시 *지시문*을 지정 하는 데 사용 되는 [cache-control](https://tools.ietf.org/html/rfc7234#section-5.2)입니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-114">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="36b7c-115">지시문은 요청이 클라이언트에서 서버로 이동 하 고 응답을 통해 서버에서 클라이언트로 다시 이동 하는 방식으로 캐싱 동작을 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-115">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="36b7c-116">요청 및 응답은 프록시 서버를 통해 이동 하 고, 프록시 서버는 HTTP 1.1 캐싱 사양을 준수 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-116">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="36b7c-117">다음 표에서는 일반적인 `Cache-Control` 지시문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-117">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="36b7c-118">지시문</span><span class="sxs-lookup"><span data-stu-id="36b7c-118">Directive</span></span>                                                       | <span data-ttu-id="36b7c-119">작업</span><span class="sxs-lookup"><span data-stu-id="36b7c-119">Action</span></span> |
| --------------------------------------------------------------- | ------ |
| [<span data-ttu-id="36b7c-120">public</span><span class="sxs-lookup"><span data-stu-id="36b7c-120">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | <span data-ttu-id="36b7c-121">캐시에서 응답을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-121">A cache may store the response.</span></span> |
| [<span data-ttu-id="36b7c-122">private</span><span class="sxs-lookup"><span data-stu-id="36b7c-122">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | <span data-ttu-id="36b7c-123">응답은 공유 캐시에 저장 되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-123">The response must not be stored by a shared cache.</span></span> <span data-ttu-id="36b7c-124">개인 캐시는 응답을 저장 하 고 다시 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-124">A private cache may store and reuse the response.</span></span> |
| [<span data-ttu-id="36b7c-125">최대 사용 기간</span><span class="sxs-lookup"><span data-stu-id="36b7c-125">max-age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | <span data-ttu-id="36b7c-126">클라이언트는 나이가 지정 된 시간 (초) 보다 큰 응답을 수락 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-126">The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="36b7c-127">예: `max-age=60` (60 초), `max-age=2592000` (1 개월)</span><span class="sxs-lookup"><span data-stu-id="36b7c-127">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month)</span></span> |
| [<span data-ttu-id="36b7c-128">캐시 없음</span><span class="sxs-lookup"><span data-stu-id="36b7c-128">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | <span data-ttu-id="36b7c-129">**요청 시**: 캐시가 저장 된 응답을 사용 하 여 요청을 충족 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-129">**On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="36b7c-130">원본 서버는 클라이언트에 대 한 응답을 다시 생성 하 고 미들웨어는 캐시에 저장 된 응답을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-130">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="36b7c-131">**응답 시**: 원본 서버에서 유효성 검사 없이 후속 요청에 대 한 응답을 사용 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-131">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> |
| [<span data-ttu-id="36b7c-132">저장소 없음</span><span class="sxs-lookup"><span data-stu-id="36b7c-132">no-store</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | <span data-ttu-id="36b7c-133">**요청 시**: 캐시에서 요청을 저장 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-133">**On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="36b7c-134">**응답 시**: 캐시가 응답의 일부를 저장 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-134">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="36b7c-135">캐시에서 역할을 수행 하는 다른 캐시 헤더는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-135">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="36b7c-136">Header</span><span class="sxs-lookup"><span data-stu-id="36b7c-136">Header</span></span>                                                     | <span data-ttu-id="36b7c-137">기능</span><span class="sxs-lookup"><span data-stu-id="36b7c-137">Function</span></span> |
| ---------------------------------------------------------- | -------- |
| [<span data-ttu-id="36b7c-138">발전할</span><span class="sxs-lookup"><span data-stu-id="36b7c-138">Age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.1)     | <span data-ttu-id="36b7c-139">응답을 생성 하거나 원본 서버에서 유효성을 검사 한 이후의 시간 (초)입니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-139">An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> |
| [<span data-ttu-id="36b7c-140">기간이</span><span class="sxs-lookup"><span data-stu-id="36b7c-140">Expires</span></span>](https://tools.ietf.org/html/rfc7234#section-5.3) | <span data-ttu-id="36b7c-141">응답이 오래 된 것으로 간주 되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-141">The time after which the response is considered stale.</span></span> |
| [<span data-ttu-id="36b7c-142">Pragma</span><span class="sxs-lookup"><span data-stu-id="36b7c-142">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)  | <span data-ttu-id="36b7c-143">@No__t-0 동작을 설정 하기 위한 HTTP/1.0 캐시와의 이전 버전과의 호환성을 위해 존재 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-143">Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="36b7c-144">@No__t-0 헤더가 있으면 `Pragma` 헤더가 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-144">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> |
| [<span data-ttu-id="36b7c-145">날</span><span class="sxs-lookup"><span data-stu-id="36b7c-145">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | <span data-ttu-id="36b7c-146">캐시 된 응답의 원래 요청과 새 요청 모두에서 `Vary` 헤더 필드가 모두 일치 하지 않는 경우 캐시 된 응답을 보내지 않도록 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-146">Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="36b7c-147">HTTP 기반 캐싱은 요청 캐시 제어 지시문을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-147">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="36b7c-148">[Cache-control 헤더에 대 한 HTTP 1.1 캐싱 지정](https://tools.ietf.org/html/rfc7234#section-5.2) 에는 클라이언트에서 보낸 유효한 @no__t 1 헤더를 인식 하기 위해 캐시가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-148">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="36b7c-149">클라이언트는 `no-cache` 헤더 값을 사용 하 여 요청을 수행 하 고 서버에서 모든 요청에 대 한 새 응답을 생성 하도록 강제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-149">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="36b7c-150">HTTP 캐싱 목표를 고려 하는 경우 항상 클라이언트 `Cache-Control` 요청 헤더를 설정 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-150">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="36b7c-151">공식 사양에서 캐싱은 클라이언트, 프록시 및 서버 네트워크에서 요청을 충족 하는 대기 시간 및 네트워크 오버 헤드를 줄이기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-151">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="36b7c-152">원본 서버에서 로드를 제어 하는 방법은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-152">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="36b7c-153">미들웨어가 공식 캐싱 사양을 준수 하기 때문에 [응답 캐싱 미들웨어](xref:performance/caching/middleware) 를 사용 하는 경우이 캐싱 동작에 대 한 개발자 제어는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-153">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="36b7c-154">[미들웨어에 대해 계획 된 향상 된 기능은](https://github.com/aspnet/AspNetCore/issues/2612) 캐시 된 응답을 제공 하도록 결정할 때 요청의 `Cache-Control` 헤더를 무시 하도록 미들웨어를 구성 하는 기회입니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-154">[Planned enhancements to the middleware](https://github.com/aspnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="36b7c-155">계획 된 향상 된 기능을 통해 서버 부하를 보다 효과적으로 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-155">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="36b7c-156">ASP.NET Core의 기타 캐싱 기술</span><span class="sxs-lookup"><span data-stu-id="36b7c-156">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="36b7c-157">메모리 내 캐싱</span><span class="sxs-lookup"><span data-stu-id="36b7c-157">In-memory caching</span></span>

<span data-ttu-id="36b7c-158">메모리 내 캐싱은 서버 메모리를 사용 하 여 캐시 된 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-158">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="36b7c-159">이 유형의 캐싱은 단일 서버 또는 *고정 세션*을 사용 하는 여러 서버에 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-159">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="36b7c-160">고정 세션은 클라이언트의 요청이 처리를 위해 항상 동일한 서버로 라우팅되는 것을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-160">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="36b7c-161">자세한 내용은 <xref:performance/caching/memory>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="36b7c-161">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="36b7c-162">분산 캐시</span><span class="sxs-lookup"><span data-stu-id="36b7c-162">Distributed Cache</span></span>

<span data-ttu-id="36b7c-163">앱이 클라우드 또는 서버 팜에서 호스팅될 때 분산 캐시를 사용 하 여 메모리에 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-163">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="36b7c-164">캐시는 요청을 처리 하는 서버에서 공유 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-164">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="36b7c-165">클라이언트에 대해 캐시 된 데이터를 사용할 수 있는 경우 클라이언트는 그룹의 모든 서버에서 처리 한 요청을 제출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-165">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="36b7c-166">ASP.NET Core는 SQL Server 및 Redis 분산 캐시를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-166">ASP.NET Core offers SQL Server and Redis distributed caches.</span></span>

<span data-ttu-id="36b7c-167">자세한 내용은 <xref:performance/caching/distributed>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="36b7c-167">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="36b7c-168">캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="36b7c-168">Cache Tag Helper</span></span>

<span data-ttu-id="36b7c-169">캐시 태그 도우미를 사용 하 여 MVC 뷰나 Razor 페이지에서 콘텐츠를 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-169">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="36b7c-170">캐시 태그 도우미는 메모리 내 캐싱을 사용 하 여 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-170">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="36b7c-171">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="36b7c-171">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="36b7c-172">분산 캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="36b7c-172">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="36b7c-173">분산 된 캐시 태그 도우미를 사용 하 여 분산 클라우드 또는 웹 팜 시나리오의 MVC 보기 또는 Razor 페이지에서 콘텐츠를 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-173">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="36b7c-174">분산 캐시 태그 도우미는 SQL Server 또는 Redis를 사용 하 여 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-174">The Distributed Cache Tag Helper uses SQL Server or Redis to store data.</span></span>

<span data-ttu-id="36b7c-175">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="36b7c-175">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="36b7c-176">ResponseCache 특성</span><span class="sxs-lookup"><span data-stu-id="36b7c-176">ResponseCache attribute</span></span>

<span data-ttu-id="36b7c-177">@No__t-0은 응답 캐싱에 적절 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-177">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="36b7c-178">인증 된 클라이언트에 대 한 정보가 포함 된 콘텐츠에 대해 캐싱을 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-178">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="36b7c-179">사용자의 id에 따라 변경 되지 않는 콘텐츠나 사용자의 로그인 여부에 따라 캐싱을 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-179">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="36b7c-180"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>은 지정 된 쿼리 키 목록 값에 따라 저장 된 응답을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-180"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="36b7c-181">단일 값 `*`이 제공 되 면 미들웨어는 모든 요청 쿼리 문자열 매개 변수에의 한 응답을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-181">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="36b7c-182">@No__t-1 속성을 설정 하려면 [응답 캐싱 미들웨어](xref:performance/caching/middleware) 를 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-182">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="36b7c-183">그렇지 않으면 런타임 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-183">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="36b7c-184">@No__t-0 속성에 해당 하는 HTTP 헤더가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-184">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="36b7c-185">속성은 응답 캐싱 미들웨어에 의해 처리 되는 HTTP 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-185">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="36b7c-186">미들웨어가 캐시 된 응답을 제공 하려면 쿼리 문자열 및 쿼리 문자열 값이 이전 요청과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-186">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="36b7c-187">예를 들어 다음 표에 표시 된 요청 및 결과의 순서를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-187">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="36b7c-188">요청</span><span class="sxs-lookup"><span data-stu-id="36b7c-188">Request</span></span>                          | <span data-ttu-id="36b7c-189">결과</span><span class="sxs-lookup"><span data-stu-id="36b7c-189">Result</span></span>                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | <span data-ttu-id="36b7c-190">서버에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-190">Returned from the server.</span></span> |
| `http://example.com?key1=value1` | <span data-ttu-id="36b7c-191">미들웨어에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-191">Returned from middleware.</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="36b7c-192">서버에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-192">Returned from the server.</span></span> |

<span data-ttu-id="36b7c-193">첫 번째 요청은 서버에서 반환 되 고 미들웨어에 캐시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-193">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="36b7c-194">쿼리 문자열이 이전 요청과 일치 하기 때문에 두 번째 요청은 미들웨어에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-194">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="36b7c-195">쿼리 문자열 값이 이전 요청과 일치 하지 않으므로 세 번째 요청이 미들웨어 캐시에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-195">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="36b7c-196">@No__t-0은 `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`를 통해 (<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>)을 구성 하 고 만드는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-196">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="36b7c-197">@No__t-0은 적절 한 HTTP 헤더와 응답의 기능을 업데이트 하는 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-197">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="36b7c-198">필터:</span><span class="sxs-lookup"><span data-stu-id="36b7c-198">The filter:</span></span>

* <span data-ttu-id="36b7c-199">@No__t-0, `Cache-Control` 및 `Pragma`에 대 한 기존 헤더를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-199">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="36b7c-200">@No__t-0에 설정 된 속성을 기반으로 적절 한 헤더를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-200">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="36b7c-201">@No__t-0이 설정 된 경우 응답 캐싱 HTTP 기능을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-201">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="36b7c-202">날</span><span class="sxs-lookup"><span data-stu-id="36b7c-202">Vary</span></span>

<span data-ttu-id="36b7c-203">이 헤더는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 속성이 설정 된 경우에만 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-203">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="36b7c-204">@No__t-0 속성 값으로 설정 된 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-204">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="36b7c-205">다음 샘플에서는 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 속성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-205">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="36b7c-206">샘플 앱을 사용 하 여 브라우저의 네트워크 도구를 사용 하 여 응답 헤더를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-206">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="36b7c-207">다음 응답 헤더는 Cache1 페이지 응답과 함께 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-207">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="36b7c-208">NoStore 및 위치입니다. 없음</span><span class="sxs-lookup"><span data-stu-id="36b7c-208">NoStore and Location.None</span></span>

<span data-ttu-id="36b7c-209"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>은 대부분의 다른 속성을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-209"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="36b7c-210">이 속성을 `true`으로 설정 하면 `Cache-Control` 헤더가 `no-store`로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-210">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="36b7c-211">@No__t-0이 `None`로 설정 된 경우:</span><span class="sxs-lookup"><span data-stu-id="36b7c-211">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="36b7c-212">`Cache-Control`이 `no-store,no-cache`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-212">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="36b7c-213">`Pragma`이 `no-cache`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-213">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="36b7c-214">@No__t-0이 `false`이 고 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>가 `None`, `Cache-Control` 및 `no-cache`으로 설정 된 경우</span><span class="sxs-lookup"><span data-stu-id="36b7c-214">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="36b7c-215"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>은 일반적으로 오류 페이지의 경우 `true`로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-215"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="36b7c-216">샘플 앱의 Cache2 페이지는 클라이언트에 응답을 저장 하지 않도록 지시 하는 응답 헤더를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-216">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="36b7c-217">샘플 앱은 다음 헤더를 포함 하는 Cache2 페이지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-217">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="36b7c-218">위치 및 기간</span><span class="sxs-lookup"><span data-stu-id="36b7c-218">Location and Duration</span></span>

<span data-ttu-id="36b7c-219">캐싱을 사용 하도록 설정 하려면 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration>을 양수 값으로 설정 하 고 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>은 `Any` (기본값) 또는 `Client` 중 하나 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-219">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="36b7c-220">이 경우 `Cache-Control` 헤더는 위치 값으로 설정 된 다음 응답의 @no__t 1이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-220">In this case, the `Cache-Control` header is set to the location value followed by the `max-age` of the response.</span></span>

> [!NOTE]
> <span data-ttu-id="36b7c-221">`Any` @no__t 및 `Client`의 옵션은 각각 `public` 및 `private`의 `Cache-Control` 헤더 값으로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-221"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="36b7c-222">앞에서 설명한 것 처럼 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>을 `None`로 설정 하면 `Cache-Control` 및 `Pragma` 헤더가 모두 `no-cache`로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-222">As noted previously, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="36b7c-223">다음 예제에서는 샘플 앱의 Cache3 페이지 모델과 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration>을 설정 하 고 기본 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 값을 그대로 두어 생성 된 헤더를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-223">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="36b7c-224">샘플 앱은 다음 헤더를 포함 하는 Cache3 페이지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-224">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="36b7c-225">캐시 프로필</span><span class="sxs-lookup"><span data-stu-id="36b7c-225">Cache profiles</span></span>

<span data-ttu-id="36b7c-226">여러 컨트롤러 작업 특성에 대 한 응답 캐시 설정을 복제 하는 대신 `Startup.ConfigureServices`에서 MVC/Razor Pages를 설정할 때 캐시 프로필을 옵션으로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-226">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36b7c-227">참조 된 캐시 프로필에 있는 값은 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>에서 기본값으로 사용 되며, 특성에 지정 된 속성에 의해 재정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-227">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="36b7c-228">캐시 프로필을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-228">Set up a cache profile.</span></span> <span data-ttu-id="36b7c-229">다음 예제에서는 샘플 앱의 `Startup.ConfigureServices`에 30 초 캐시 프로필을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-229">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="36b7c-230">샘플 앱의 Cache4 페이지 모델은 `Default30` 캐시 프로필을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-230">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="36b7c-231">@No__t-0은 다음에 적용 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-231">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* <span data-ttu-id="36b7c-232">Razor 페이지 처리기 (클래스) &ndash; 특성은 처리기 메서드에 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-232">Razor Page handlers (classes) &ndash; Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="36b7c-233">MVC 컨트롤러 (클래스)</span><span class="sxs-lookup"><span data-stu-id="36b7c-233">MVC controllers (classes).</span></span>
* <span data-ttu-id="36b7c-234">MVC 작업 (메서드) &ndash; 메서드 수준 특성은 클래스 수준 특성에 지정 된 설정을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-234">MVC actions (methods) &ndash; Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="36b7c-235">@No__t-0 캐시 프로필에의 한 Cache4 페이지 응답에 적용 되는 결과 헤더는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="36b7c-235">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="36b7c-236">추가 자료</span><span class="sxs-lookup"><span data-stu-id="36b7c-236">Additional resources</span></span>

* [<span data-ttu-id="36b7c-237">캐시에 응답 저장</span><span class="sxs-lookup"><span data-stu-id="36b7c-237">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="36b7c-238">Cache-control</span><span class="sxs-lookup"><span data-stu-id="36b7c-238">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
