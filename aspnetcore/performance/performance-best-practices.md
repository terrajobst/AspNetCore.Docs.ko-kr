---
title: ASP.NET Core 성능 모범 사례
author: mjrousos
description: ASP.NET Core 앱의 성능을 향상 하 고 일반적인 성능 문제를 방지 하기 위한 팁입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/26/2019
uid: performance/performance-best-practices
ms.openlocfilehash: a2952f5234cdef7f749a1af8dd4adcb887290629
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259778"
---
# <a name="aspnet-core-performance-best-practices"></a><span data-ttu-id="5ba3e-103">ASP.NET Core 성능 모범 사례</span><span class="sxs-lookup"><span data-stu-id="5ba3e-103">ASP.NET Core Performance Best Practices</span></span>

<span data-ttu-id="5ba3e-104">[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="5ba3e-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="5ba3e-105">이 문서에서는 ASP.NET Core의 성능 모범 사례에 대 한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-105">This article provides guidelines for performance best practices with ASP.NET Core.</span></span>

## <a name="cache-aggressively"></a><span data-ttu-id="5ba3e-106">적극적으로 캐시</span><span class="sxs-lookup"><span data-stu-id="5ba3e-106">Cache aggressively</span></span>

<span data-ttu-id="5ba3e-107">캐싱은이 문서의 여러 부분에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-107">Caching is discussed in several parts of this document.</span></span> <span data-ttu-id="5ba3e-108">자세한 내용은 <xref:performance/caching/response>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-108">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="understand-hot-code-paths"></a><span data-ttu-id="5ba3e-109">핫 코드 경로 이해</span><span class="sxs-lookup"><span data-stu-id="5ba3e-109">Understand hot code paths</span></span>

<span data-ttu-id="5ba3e-110">이 문서에서 *핫 코드 경로* 는 자주 호출 되는 코드 경로로 정의 되며 대부분의 실행 시간이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-110">In this document, a *hot code path* is defined as a code path that is frequently called and where much of the execution time occurs.</span></span> <span data-ttu-id="5ba3e-111">핫 코드 경로는 일반적으로 앱 확장 및 성능을 제한 하며이 문서의 여러 부분에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-111">Hot code paths typically limit app scale-out and performance and are discussed in several parts of this document.</span></span>

## <a name="avoid-blocking-calls"></a><span data-ttu-id="5ba3e-112">호출 차단 방지</span><span class="sxs-lookup"><span data-stu-id="5ba3e-112">Avoid blocking calls</span></span>

<span data-ttu-id="5ba3e-113">ASP.NET Core 앱은 여러 요청을 동시에 처리 하도록 설계 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-113">ASP.NET Core apps should be designed to process many requests simultaneously.</span></span> <span data-ttu-id="5ba3e-114">비동기 Api를 사용 하면 작은 스레드 풀에서 차단 호출을 기다리지 않고 수천 개의 동시 요청을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-114">Asynchronous APIs allow a small pool of threads to handle thousands of concurrent requests by not waiting on blocking calls.</span></span> <span data-ttu-id="5ba3e-115">장기 실행 동기 작업이 완료 되기를 기다리지 않고 스레드가 다른 요청에서 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-115">Rather than waiting on a long-running synchronous task to complete, the thread can work on another request.</span></span>

<span data-ttu-id="5ba3e-116">ASP.NET Core 앱의 일반적인 성능 문제는 비동기 일 수 있는 호출을 차단 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-116">A common performance problem in ASP.NET Core apps is blocking calls that could be asynchronous.</span></span> <span data-ttu-id="5ba3e-117">많은 동기 차단 호출이 [스레드 풀 고갈](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) 및 저하 된 응답 시간을 초래 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-117">Many synchronous blocking calls lead to [Thread Pool starvation](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) and degraded response times.</span></span>

<span data-ttu-id="5ba3e-118">안 **함**:</span><span class="sxs-lookup"><span data-stu-id="5ba3e-118">**Do not**:</span></span>

* <span data-ttu-id="5ba3e-119">[작업. 대기](/dotnet/api/system.threading.tasks.task.wait) 또는 [작업](/dotnet/api/system.threading.tasks.task-1.result)을 호출 하 여 비동기 실행을 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-119">Block asynchronous execution by calling [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) or [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).</span></span>
* <span data-ttu-id="5ba3e-120">공용 코드 경로에서 잠금을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-120">Acquire locks in common code paths.</span></span> <span data-ttu-id="5ba3e-121">ASP.NET Core 앱은 코드를 병렬로 실행 하도록 설계 될 때 가장 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-121">ASP.NET Core apps are most performant when architected to run code in parallel.</span></span>

<span data-ttu-id="5ba3e-122">**Do**:</span><span class="sxs-lookup"><span data-stu-id="5ba3e-122">**Do**:</span></span>

* <span data-ttu-id="5ba3e-123">[핫 코드 경로](#understand-hot-code-paths) 를 비동기식으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-123">Make [hot code paths](#understand-hot-code-paths) asynchronous.</span></span>
* <span data-ttu-id="5ba3e-124">데이터 액세스 및 장기 실행 작업 Api를 비동기식으로 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-124">Call data access and long-running operations APIs asynchronously.</span></span>
* <span data-ttu-id="5ba3e-125">컨트롤러/Razor 페이지 작업을 비동기식으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-125">Make controller/Razor Page actions asynchronous.</span></span> <span data-ttu-id="5ba3e-126">비동기 [/](/dotnet/csharp/programming-guide/concepts/async/) 대기 패턴을 활용 하기 위해 전체 호출 스택은 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-126">The entire call stack is asynchronous in order to benefit from [async/await](/dotnet/csharp/programming-guide/concepts/async/) patterns.</span></span>

<span data-ttu-id="5ba3e-127">[Perfview](https://github.com/Microsoft/perfview)와 같은 프로파일러를 사용 하 여 [스레드 풀](/windows/desktop/procthread/thread-pools)에 자주 추가 되는 스레드를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-127">A profiler, such as [PerfView](https://github.com/Microsoft/perfview), can be used to find threads frequently added to the [Thread Pool](/windows/desktop/procthread/thread-pools).</span></span> <span data-ttu-id="5ba3e-128">@No__t-0 이벤트는 스레드 풀에 추가 된 스레드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-128">The `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` event indicates a thread added to the thread pool.</span></span> <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a><span data-ttu-id="5ba3e-129">대량 개체 할당 최소화</span><span class="sxs-lookup"><span data-stu-id="5ba3e-129">Minimize large object allocations</span></span>

<span data-ttu-id="5ba3e-130">[.Net Core 가비지 수집기](/dotnet/standard/garbage-collection/) 는 ASP.NET Core 앱에서 메모리의 할당 및 해제를 자동으로 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-130">The [.NET Core garbage collector](/dotnet/standard/garbage-collection/) manages allocation and release of memory automatically in ASP.NET Core apps.</span></span> <span data-ttu-id="5ba3e-131">자동 가비지 수집은 일반적으로 개발자가 메모리 해제 방법 또는 시기에 대해 걱정할 필요가 없음을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-131">Automatic garbage collection generally means that developers don't need to worry about how or when memory is freed.</span></span> <span data-ttu-id="5ba3e-132">그러나 참조 되지 않은 개체를 정리 하는 경우 CPU 시간이 걸리므로 개발자는 [핫 코드 경로](#understand-hot-code-paths)에서 개체 할당을 최소화 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-132">However, cleaning up unreferenced objects takes CPU time, so developers should minimize allocating objects in [hot code paths](#understand-hot-code-paths).</span></span> <span data-ttu-id="5ba3e-133">가비지 수집은 큰 개체 (> 85 K 바이트)에서 특히 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-133">Garbage collection is especially expensive on large objects (> 85 K bytes).</span></span> <span data-ttu-id="5ba3e-134">Large 개체는 [large object 힙에](/dotnet/standard/garbage-collection/large-object-heap) 저장 되며 정리 하려면 전체 (2 세대) 가비지 수집이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-134">Large objects are stored on the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) and require a full (generation 2) garbage collection to clean up.</span></span> <span data-ttu-id="5ba3e-135">0 세대 및 1 세대 컬렉션과 달리 2 세대 수집에는 앱 실행을 임시로 일시 중단 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-135">Unlike generation 0 and generation 1 collections, a generation 2 collection requires a temporary suspension of app execution.</span></span> <span data-ttu-id="5ba3e-136">자주 큰 개체를 할당 및 할당 취소 하면 성능이 저하 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-136">Frequent allocation and de-allocation of large objects can cause inconsistent performance.</span></span>

<span data-ttu-id="5ba3e-137">관련</span><span class="sxs-lookup"><span data-stu-id="5ba3e-137">Recommendations:</span></span>

* <span data-ttu-id="5ba3e-138">자주 사용 되는 많은 개체를 캐싱하는 **것이 좋습니다** .</span><span class="sxs-lookup"><span data-stu-id="5ba3e-138">**Do** consider caching large objects that are frequently used.</span></span> <span data-ttu-id="5ba3e-139">큰 개체를 캐시 하면 비용이 많이 드는 할당이 방지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-139">Caching large objects prevents expensive allocations.</span></span>
* <span data-ttu-id="5ba3e-140">[@No__t-2](/dotnet/api/system.buffers.arraypool-1) 를 사용 하 여 대량 배열을 저장 하 여 풀 버퍼를 **수행** 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-140">**Do** pool buffers by using an [`ArrayPool<T>`](/dotnet/api/system.buffers.arraypool-1) to store large arrays.</span></span>
* <span data-ttu-id="5ba3e-141">[핫 코드 경로](#understand-hot-code-paths)에 단기 수명이 많은 개체를 할당 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="5ba3e-141">**Do not** allocate many, short-lived large objects on [hot code paths](#understand-hot-code-paths).</span></span>

<span data-ttu-id="5ba3e-142">[Perfview](https://github.com/Microsoft/perfview) 및 검사에서 GC (가비지 수집) 통계를 검토 하 여 위와 같은 메모리 문제를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-142">Memory issues, such as the preceding, can be diagnosed by reviewing garbage collection (GC) stats in [PerfView](https://github.com/Microsoft/perfview) and examining:</span></span>

* <span data-ttu-id="5ba3e-143">가비지 수집 일시 중지 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-143">Garbage collection pause time.</span></span>
* <span data-ttu-id="5ba3e-144">가비지 수집에 소요 되는 프로세서 시간의 비율입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-144">What percentage of the processor time is spent in garbage collection.</span></span>
* <span data-ttu-id="5ba3e-145">0 세대, 1, 2 세대의 가비지 컬렉션 수입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-145">How many garbage collections are generation 0, 1, and 2.</span></span>

<span data-ttu-id="5ba3e-146">자세한 내용은 [가비지 수집 및 성능](/dotnet/standard/garbage-collection/performance)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-146">For more information, see [Garbage Collection and Performance](/dotnet/standard/garbage-collection/performance).</span></span>

## <a name="optimize-data-access"></a><span data-ttu-id="5ba3e-147">데이터 액세스 최적화</span><span class="sxs-lookup"><span data-stu-id="5ba3e-147">Optimize Data Access</span></span>

<span data-ttu-id="5ba3e-148">데이터 저장소 및 다른 원격 서비스와의 상호 작용은 종종 ASP.NET Core 앱의 가장 느린 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-148">Interactions with a data store and other remote services are often the slowest parts of an ASP.NET Core app.</span></span> <span data-ttu-id="5ba3e-149">데이터를 효율적으로 읽고 쓰는 것이 좋은 성능을 위해 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-149">Reading and writing data efficiently is critical for good performance.</span></span>

<span data-ttu-id="5ba3e-150">관련</span><span class="sxs-lookup"><span data-stu-id="5ba3e-150">Recommendations:</span></span>

* <span data-ttu-id="5ba3e-151">모든 데이터 액세스 Api를 비동기적 **으로 호출 합니다** .</span><span class="sxs-lookup"><span data-stu-id="5ba3e-151">**Do** call all data access APIs asynchronously.</span></span>
* <span data-ttu-id="5ba3e-152">필요한 것 보다 더 많은 데이터를 검색 **하지** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-152">**Do not** retrieve more data than is necessary.</span></span> <span data-ttu-id="5ba3e-153">현재 HTTP 요청에 필요한 데이터만 반환 하는 쿼리를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-153">Write queries to return just the data that's necessary for the current HTTP request.</span></span>
* <span data-ttu-id="5ba3e-154">약간 오래 된 데이터를 사용할 수 있는 경우 데이터베이스 또는 원격 서비스에서 검색 되는 자주 액세스 하는 데이터를 캐시 하 **는 것이 좋습니다.**</span><span class="sxs-lookup"><span data-stu-id="5ba3e-154">**Do** consider caching frequently accessed data retrieved from a database or remote service if slightly out-of-date data is acceptable.</span></span> <span data-ttu-id="5ba3e-155">시나리오에 따라 [Memorycache](xref:performance/caching/memory) 또는 [microsoft.web.distributedcache](xref:performance/caching/distributed)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-155">Depending on the scenario, use a [MemoryCache](xref:performance/caching/memory) or a [DistributedCache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="5ba3e-156">자세한 내용은 <xref:performance/caching/response>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-156">For more information, see <xref:performance/caching/response>.</span></span>
* <span data-ttu-id="5ba3e-157">네트워크 왕복 **을 최소화 합니다** .</span><span class="sxs-lookup"><span data-stu-id="5ba3e-157">**Do** minimize network round trips.</span></span> <span data-ttu-id="5ba3e-158">목표는 여러 호출이 아닌 단일 호출에서 필요한 데이터를 검색 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-158">The goal is to retrieve the required data in a single call rather than several calls.</span></span>
* <span data-ttu-id="5ba3e-159">읽기 전용 용도로 데이터에 액세스할 때 Entity Framework Core에서 [추적 안 함 쿼리](/ef/core/querying/tracking#no-tracking-queries) **를 사용 합니다** .</span><span class="sxs-lookup"><span data-stu-id="5ba3e-159">**Do** use [no-tracking queries](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core when accessing data for read-only purposes.</span></span> <span data-ttu-id="5ba3e-160">EF Core 추적 되지 않는 쿼리 결과를 보다 효율적으로 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-160">EF Core can return the results of no-tracking queries more efficiently.</span></span>
* <span data-ttu-id="5ba3e-161">데이터베이스에서 필터링을 수행 하도록 LINQ 쿼리를 필터링 하 고 집계 합니다. 예를 들어 `.Where`, `.Select` 또는 `.Sum` 문을 사용 **해야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-161">**Do** filter and aggregate LINQ queries (with `.Where`, `.Select`, or `.Sum` statements, for example) so that the filtering is performed by the database.</span></span>
* <span data-ttu-id="5ba3e-162">EF Core에서 클라이언트의 일부 쿼리 연산자를 확인 하므로 비효율적인 쿼리 **실행이 발생할** 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-162">**Do** consider that EF Core resolves some query operators on the client, which may lead to inefficient query execution.</span></span> <span data-ttu-id="5ba3e-163">자세한 내용은 [클라이언트 평가 성능 문제](/ef/core/querying/client-eval#client-evaluation-performance-issues)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-163">For more information, see [Client evaluation performance issues](/ef/core/querying/client-eval#client-evaluation-performance-issues).</span></span>
* <span data-ttu-id="5ba3e-164">컬렉션에 대해 프로젝션 쿼리를 사용 **하지 마십시오. 이렇게** 하면 "N + 1" SQL 쿼리가 실행 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-164">**Do not** use projection queries on collections, which can result in executing "N + 1" SQL queries.</span></span> <span data-ttu-id="5ba3e-165">자세한 내용은 [상관 하위 쿼리 최적화](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-165">For more information, see [Optimization of correlated subqueries](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).</span></span>

<span data-ttu-id="5ba3e-166">대규모 앱의 성능을 향상 시킬 수 있는 방법에 대해서는 [EF High 성능](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-166">See [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) for approaches that may improve performance in high-scale apps:</span></span>

* [<span data-ttu-id="5ba3e-167">DbContext 풀링</span><span class="sxs-lookup"><span data-stu-id="5ba3e-167">DbContext pooling</span></span>](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [<span data-ttu-id="5ba3e-168">명시적으로 컴파일된 쿼리</span><span class="sxs-lookup"><span data-stu-id="5ba3e-168">Explicitly compiled queries</span></span>](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

<span data-ttu-id="5ba3e-169">코드 베이스를 커밋하기 전에 이전 고성능 방법의 영향을 측정 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-169">We recommend measuring the impact of the preceding high-performance approaches before committing the code base.</span></span> <span data-ttu-id="5ba3e-170">컴파일된 쿼리의 추가적인 복잡성은 성능 향상을 정당화 하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-170">The additional complexity of compiled queries may not justify the performance improvement.</span></span>

<span data-ttu-id="5ba3e-171">[Application Insights](/azure/application-insights/app-insights-overview) 또는 프로 파일링 도구를 사용 하 여 데이터에 액세스 하는 데 걸린 시간을 검토 하 여 쿼리 문제를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-171">Query issues can be detected by reviewing the time spent accessing data with [Application Insights](/azure/application-insights/app-insights-overview) or with profiling tools.</span></span> <span data-ttu-id="5ba3e-172">또한 대부분의 데이터베이스는 자주 실행 되는 쿼리와 관련 하 여 통계를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-172">Most databases also make statistics available concerning frequently executed queries.</span></span>

## <a name="pool-http-connections-with-httpclientfactory"></a><span data-ttu-id="5ba3e-173">HttpClientFactory를 사용 하 여 HTTP 연결 풀</span><span class="sxs-lookup"><span data-stu-id="5ba3e-173">Pool HTTP connections with HttpClientFactory</span></span>

<span data-ttu-id="5ba3e-174">[Httpclient](/dotnet/api/system.net.http.httpclient) 는 `IDisposable` 인터페이스를 구현 하지만 재사용을 위해 설계 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-174">Although [HttpClient](/dotnet/api/system.net.http.httpclient) implements the `IDisposable` interface, it's designed for reuse.</span></span> <span data-ttu-id="5ba3e-175">닫힘 `HttpClient` 인스턴스는 짧은 시간 동안 `TIME_WAIT` 상태에서 소켓을 열어 둡니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-175">Closed `HttpClient` instances leave sockets open in the `TIME_WAIT` state for a short period of time.</span></span> <span data-ttu-id="5ba3e-176">@No__t-0 개체를 만들고 삭제 하는 코드 경로가 자주 사용 되는 경우 앱에서 사용 가능한 소켓이 고갈 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-176">If a code path that creates and disposes of `HttpClient` objects is frequently used, the app may exhaust available sockets.</span></span> <span data-ttu-id="5ba3e-177">[Httpclientfactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) 는이 문제에 대 한 솔루션으로 ASP.NET Core 2.1에서 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-177">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) was introduced in ASP.NET Core 2.1 as a solution to this problem.</span></span> <span data-ttu-id="5ba3e-178">성능 및 안정성을 최적화 하기 위해 풀링 HTTP 연결을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-178">It handles pooling HTTP connections to optimize performance and reliability.</span></span>

<span data-ttu-id="5ba3e-179">관련</span><span class="sxs-lookup"><span data-stu-id="5ba3e-179">Recommendations:</span></span>

* <span data-ttu-id="5ba3e-180">@No__t 1 인스턴스를 직접 만들고 삭제 **하지 마십시오** .</span><span class="sxs-lookup"><span data-stu-id="5ba3e-180">**Do not** create and dispose of `HttpClient` instances directly.</span></span>
* <span data-ttu-id="5ba3e-181">[Httpclientfactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) **를 사용 하 여 @no__t** 2 인스턴스를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-181">**Do** use [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) to retrieve `HttpClient` instances.</span></span> <span data-ttu-id="5ba3e-182">자세한 내용은 [HttpClientFactory를 사용 하 여 복원 력 있는 HTTP 요청 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-182">For more information, see [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).</span></span>

## <a name="keep-common-code-paths-fast"></a><span data-ttu-id="5ba3e-183">자주 공용 코드 경로 유지</span><span class="sxs-lookup"><span data-stu-id="5ba3e-183">Keep common code paths fast</span></span>

<span data-ttu-id="5ba3e-184">코드를 최적화 하는 데 가장 중요 한 코드 경로를 최적화 하는 것이 가장 중요 한 모든 코드를 원합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-184">You want all of your code to be fast, frequently called code paths are the most critical to optimize:</span></span>

* <span data-ttu-id="5ba3e-185">앱의 요청 처리 파이프라인에 있는 미들웨어 구성 요소, 특히 미들웨어는 파이프라인 초기에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-185">Middleware components in the app's request processing pipeline, especially middleware run early in the pipeline.</span></span> <span data-ttu-id="5ba3e-186">이러한 구성 요소는 성능에 큰 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-186">These components have a large impact on performance.</span></span>
* <span data-ttu-id="5ba3e-187">요청 마다 또는 요청 마다 여러 번 실행 되는 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-187">Code that's executed for every request or multiple times per request.</span></span> <span data-ttu-id="5ba3e-188">예를 들어 사용자 지정 로깅, 권한 부여 처리기 또는 임시 서비스 초기화가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-188">For example, custom logging, authorization handlers, or initialization of transient services.</span></span>

<span data-ttu-id="5ba3e-189">관련</span><span class="sxs-lookup"><span data-stu-id="5ba3e-189">Recommendations:</span></span>

* <span data-ttu-id="5ba3e-190">장기 실행 작업에는 사용자 지정 미들웨어 구성 요소를 사용 **하지 마십시오** .</span><span class="sxs-lookup"><span data-stu-id="5ba3e-190">**Do not** use custom middleware components with long-running tasks.</span></span>
* <span data-ttu-id="5ba3e-191">[Visual Studio 진단 도구](/visualstudio/profiling/profiling-feature-tour) 또는 [perfview](https://github.com/Microsoft/perfview))와 같은 성능 프로 파일링 도구 **를 사용 하 여** [핫 코드 경로](#understand-hot-code-paths)를 식별 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-191">**Do** use performance profiling tools, such as [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) or [PerfView](https://github.com/Microsoft/perfview)), to identify [hot code paths](#understand-hot-code-paths).</span></span>

## <a name="complete-long-running-tasks-outside-of-http-requests"></a><span data-ttu-id="5ba3e-192">HTTP 요청 외부에서 장기 실행 작업 완료</span><span class="sxs-lookup"><span data-stu-id="5ba3e-192">Complete long-running Tasks outside of HTTP requests</span></span>

<span data-ttu-id="5ba3e-193">ASP.NET Core 앱에 대 한 대부분의 요청은 컨트롤러 또는 페이지 모델에서 필요한 서비스를 호출 하 고 HTTP 응답을 반환 하 여 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-193">Most requests to an ASP.NET Core app can be handled by a controller or page model calling necessary services and returning an HTTP response.</span></span> <span data-ttu-id="5ba3e-194">장기 실행 작업을 포함 하는 일부 요청의 경우 전체 요청-응답 프로세스를 비동기식으로 만드는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-194">For some requests that involve long-running tasks, it's better to make the entire request-response process asynchronous.</span></span>

<span data-ttu-id="5ba3e-195">관련</span><span class="sxs-lookup"><span data-stu-id="5ba3e-195">Recommendations:</span></span>

* <span data-ttu-id="5ba3e-196">일반 HTTP 요청 처리의 일부로 장기 실행 작업이 완료 될 때까지 기다리지 **마세요** .</span><span class="sxs-lookup"><span data-stu-id="5ba3e-196">**Do not** wait for long-running tasks to complete as part of ordinary HTTP request processing.</span></span>
* <span data-ttu-id="5ba3e-197">[백그라운드 서비스](xref:fundamentals/host/hosted-services) 를 사용 하 여 장기 실행 요청을 처리 하거나 [Azure 함수](/azure/azure-functions/)를 사용 하 여 out-of-process를 처리 하는 **것이 좋습니다** .</span><span class="sxs-lookup"><span data-stu-id="5ba3e-197">**Do** consider handling long-running requests with [background services](xref:fundamentals/host/hosted-services) or out of process with an [Azure Function](/azure/azure-functions/).</span></span> <span data-ttu-id="5ba3e-198">Out-of-process 작업을 완료 하는 작업은 특히 CPU를 많이 사용 하는 작업에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-198">Completing work out-of-process is especially beneficial for CPU-intensive tasks.</span></span>
* <span data-ttu-id="5ba3e-199">[SignalR](xref:signalr/introduction)와 같은 실시간 통신 옵션 **을 사용 하 여 비동기적** 으로 클라이언트와 통신 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-199">**Do** use real-time communication options, such as [SignalR](xref:signalr/introduction), to communicate with clients asynchronously.</span></span>

## <a name="minify-client-assets"></a><span data-ttu-id="5ba3e-200">클라이언트 자산 축소</span><span class="sxs-lookup"><span data-stu-id="5ba3e-200">Minify client assets</span></span>

<span data-ttu-id="5ba3e-201">복잡 한 프런트 엔드가 있는 ASP.NET Core 앱은 종종 많은 JavaScript, CSS 또는 이미지 파일을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-201">ASP.NET Core apps with complex front-ends frequently serve many JavaScript, CSS, or image files.</span></span> <span data-ttu-id="5ba3e-202">초기 로드 요청의 성능은 다음과 같은 방법으로 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-202">Performance of initial load requests can be improved by:</span></span>

* <span data-ttu-id="5ba3e-203">번들은 여러 파일을 하나로 결합 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-203">Bundling, which combines multiple files into one.</span></span>
* <span data-ttu-id="5ba3e-204">축소-공백과 주석을 제거 하 여 파일 크기를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-204">Minifying, which reduces the size of files by removing whitespace and comments.</span></span>

<span data-ttu-id="5ba3e-205">관련</span><span class="sxs-lookup"><span data-stu-id="5ba3e-205">Recommendations:</span></span>

* <span data-ttu-id="5ba3e-206">번들 및 축소 클라이언트 자산에 대 한 ASP.NET Core [기본 제공 지원을](xref:client-side/bundling-and-minification) **사용 합니다** .</span><span class="sxs-lookup"><span data-stu-id="5ba3e-206">**Do** use ASP.NET Core's [built-in support](xref:client-side/bundling-and-minification) for bundling and minifying client assets.</span></span>
* <span data-ttu-id="5ba3e-207">복합 클라이언트 자산 관리를 위해 다른 타사 도구 (예: [Webpack](https://webpack.js.org/)) **를 고려 합니다** .</span><span class="sxs-lookup"><span data-stu-id="5ba3e-207">**Do** consider other third-party tools, such as [Webpack](https://webpack.js.org/), for complex client asset management.</span></span>

## <a name="compress-responses"></a><span data-ttu-id="5ba3e-208">응답 압축</span><span class="sxs-lookup"><span data-stu-id="5ba3e-208">Compress responses</span></span>

 <span data-ttu-id="5ba3e-209">응답 크기를 줄이면 일반적으로 앱의 응답성이 크게 향상 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-209">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="5ba3e-210">페이로드 크기를 줄이는 한 가지 방법은 앱의 응답을 압축 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-210">One way to reduce payload sizes is to compress an app's responses.</span></span> <span data-ttu-id="5ba3e-211">자세한 내용은 [응답 압축](xref:performance/response-compression)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-211">For more information, see [Response compression](xref:performance/response-compression).</span></span>

## <a name="use-the-latest-aspnet-core-release"></a><span data-ttu-id="5ba3e-212">최신 ASP.NET Core 릴리스 사용</span><span class="sxs-lookup"><span data-stu-id="5ba3e-212">Use the latest ASP.NET Core release</span></span>

<span data-ttu-id="5ba3e-213">ASP.NET Core의 새 릴리스에는 성능 향상이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-213">Each new release of ASP.NET Core includes performance improvements.</span></span> <span data-ttu-id="5ba3e-214">.NET Core 및 ASP.NET Core의 최적화는 일반적으로 최신 버전이 이전 버전을 내지만 것을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-214">Optimizations in .NET Core and ASP.NET Core mean that newer versions generally outperform older versions.</span></span> <span data-ttu-id="5ba3e-215">예를 들어 .NET Core 2.1은 [`Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx)에서 컴파일된 정규식 및 benefitted에 대 한 지원을 추가 했습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-215">For example, .NET Core 2.1 added support for compiled regular expressions and benefitted from [`Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx).</span></span> <span data-ttu-id="5ba3e-216">ASP.NET Core 2.2에는 HTTP/2에 대 한 지원이 추가 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-216">ASP.NET Core 2.2 added support for HTTP/2.</span></span> <span data-ttu-id="5ba3e-217">[ASP.NET Core 3.0](xref:aspnetcore-3.0) 은 메모리 사용을 줄이고 처리량을 향상 시키는 많은 향상 된 기능을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-217">[ASP.NET Core 3.0 adds many improvements](xref:aspnetcore-3.0) that reduce memory usage and improve throughput.</span></span> <span data-ttu-id="5ba3e-218">성능이 우선 되는 경우 ASP.NET Core의 현재 버전으로 업그레이드 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-218">If performance is a priority, consider upgrading to the current version of ASP.NET Core.</span></span>

## <a name="minimize-exceptions"></a><span data-ttu-id="5ba3e-219">예외 최소화</span><span class="sxs-lookup"><span data-stu-id="5ba3e-219">Minimize exceptions</span></span>

<span data-ttu-id="5ba3e-220">예외는 드물게 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-220">Exceptions should be rare.</span></span> <span data-ttu-id="5ba3e-221">예외 throw 및 catch는 다른 코드 흐름 패턴을 기준으로 속도가 느립니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-221">Throwing and catching exceptions is slow relative to other code flow patterns.</span></span> <span data-ttu-id="5ba3e-222">따라서 일반적인 프로그램 흐름을 제어 하는 데는 예외를 사용 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-222">Because of this, exceptions shouldn't be used to control normal program flow.</span></span>

<span data-ttu-id="5ba3e-223">관련</span><span class="sxs-lookup"><span data-stu-id="5ba3e-223">Recommendations:</span></span>

* <span data-ttu-id="5ba3e-224">특히 [핫 코드 경로](#understand-hot-code-paths)에서 일반적인 프로그램 흐름의 수단으로 throw 또는 catch 예외를 사용 **하지 마십시오** .</span><span class="sxs-lookup"><span data-stu-id="5ba3e-224">**Do not** use throwing or catching exceptions as a means of normal program flow, especially in [hot code paths](#understand-hot-code-paths).</span></span>
* <span data-ttu-id="5ba3e-225">응용 프로그램에 논리 **를 포함 하** 여 예외를 발생 시키는 조건을 검색 하 고 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-225">**Do** include logic in the app to detect and handle conditions that would cause an exception.</span></span>
* <span data-ttu-id="5ba3e-226">비정상적인 또는 예기치 않은 조건에 대 한 예외 **를 throw 하거나** catch 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-226">**Do** throw or catch exceptions for unusual or unexpected conditions.</span></span>

<span data-ttu-id="5ba3e-227">Application Insights와 같은 앱 진단 도구는 응용 프로그램에서 성능에 영향을 줄 수 있는 일반적인 예외를 식별 하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-227">App diagnostic tools, such as Application Insights, can help to identify common exceptions in an app that may affect performance.</span></span>

## <a name="performance-and-reliability"></a><span data-ttu-id="5ba3e-228">성능 및 안정성</span><span class="sxs-lookup"><span data-stu-id="5ba3e-228">Performance and reliability</span></span>

<span data-ttu-id="5ba3e-229">다음 섹션에서는 성능 팁과 알려진 안정성 문제 및 해결 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-229">The following sections provide performance tips and known reliability problems and solutions.</span></span>

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a><span data-ttu-id="5ba3e-230">HttpRequest/Httpresponse.cache 본문에서 동기 읽기 또는 쓰기 방지</span><span class="sxs-lookup"><span data-stu-id="5ba3e-230">Avoid synchronous read or write on HttpRequest/HttpResponse body</span></span>

<span data-ttu-id="5ba3e-231">ASP.NET Core의 모든 IO는 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-231">All IO in ASP.NET Core is asynchronous.</span></span> <span data-ttu-id="5ba3e-232">서버는 동기 및 비동기 오버 로드를 모두 포함 하는 `Stream` 인터페이스를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-232">Servers implement the `Stream` interface, which has both synchronous and asynchronous overloads.</span></span> <span data-ttu-id="5ba3e-233">스레드 풀 스레드를 차단 하지 않도록 비동기를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-233">The asynchronous ones should be preferred to avoid blocking thread pool threads.</span></span> <span data-ttu-id="5ba3e-234">스레드를 차단 하면 스레드 풀이 고갈 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-234">Blocking threads can lead to thread pool starvation.</span></span>

<span data-ttu-id="5ba3e-235">**이 작업을 수행 하지 마십시오.** 다음 예제에서는 <xref:System.IO.StreamReader.ReadToEnd*>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-235">**Do not do this:** The following example uses the <xref:System.IO.StreamReader.ReadToEnd*>.</span></span> <span data-ttu-id="5ba3e-236">결과를 대기 하는 현재 스레드를 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-236">It blocks the current thread to wait for the result.</span></span> <span data-ttu-id="5ba3e-237">비동기 @ no__t-1을 통한 [sync의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-237">This is an example of [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

<span data-ttu-id="5ba3e-238">위의 코드에서 `Get`은 전체 HTTP 요청 본문을 메모리에 동기적으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-238">In the preceding code, `Get` synchronously reads the entire HTTP request body into memory.</span></span> <span data-ttu-id="5ba3e-239">클라이언트에 느린 업로드가 있으면 앱이 비동기를 통해 동기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-239">If the client is slowly uploading, the app is doing sync over async.</span></span> <span data-ttu-id="5ba3e-240">Kestrel은 동기 읽기를 지원 **하지** 않기 때문에 앱은 async를 통해 동기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-240">The app does sync over async because Kestrel does **NOT** support synchronous reads.</span></span>

<span data-ttu-id="5ba3e-241">**다음 작업을 수행 합니다.** 다음 예제에서는 <xref:System.IO.StreamReader.ReadToEndAsync*>을 사용 하며 읽는 동안 스레드를 차단 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-241">**Do this:** The following example uses <xref:System.IO.StreamReader.ReadToEndAsync*> and does not block the thread while reading.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

<span data-ttu-id="5ba3e-242">위의 코드는 전체 HTTP 요청 본문을 메모리에 비동기적으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-242">The preceding code asynchronously reads the entire HTTP request body into memory.</span></span>

> [!WARNING]
> <span data-ttu-id="5ba3e-243">요청이 클 경우 전체 HTTP 요청 본문을 메모리로 읽으면 OOM (메모리 부족) 조건이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-243">If the request is large, reading the entire HTTP request body into memory could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="5ba3e-244">OOM은 서비스 거부를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-244">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="5ba3e-245">자세한 내용은이 문서의 [메모리에 대 한 대량 요청 본문 또는 응답 본문 읽기 방지](#arlb) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-245">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="5ba3e-246">**다음 작업을 수행 합니다.** 다음 예제는 버퍼링 되지 않은 요청 본문을 사용 하 여 완전 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-246">**Do this:** The following example is fully asynchronous using a non buffered request body:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

<span data-ttu-id="5ba3e-247">위의 코드는 전체 HTTP 요청 본문을 메모리에 비동기적으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-247">The preceding code asynchronously reads the entire HTTP request body into memory.</span></span>

> [!WARNING]
> <span data-ttu-id="5ba3e-248">요청이 클 경우 전체 HTTP 요청 본문을 메모리로 읽으면 OOM (메모리 부족) 조건이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-248">If the request is large, reading the entire HTTP request body into memory could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="5ba3e-249">OOM은 서비스 거부를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-249">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="5ba3e-250">자세한 내용은이 문서의 [메모리에 대 한 대량 요청 본문 또는 응답 본문 읽기 방지](#arlb) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-250">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

## <a name="prefer-readformasync-over-requestform"></a><span data-ttu-id="5ba3e-251">요청을 통해 ReadFormAsync를 선호 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-251">Prefer ReadFormAsync over Request.Form</span></span>

<span data-ttu-id="5ba3e-252">`HttpContext.Request.ReadFormAsync` 대신 `HttpContext.Request.Form`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-252">Use `HttpContext.Request.ReadFormAsync` instead of `HttpContext.Request.Form`.</span></span>
<span data-ttu-id="5ba3e-253">`HttpContext.Request.Form`은 다음 조건 에서만 안전 하 게 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-253">`HttpContext.Request.Form` can be safely read only with the following conditions:</span></span>

* <span data-ttu-id="5ba3e-254">@No__t-0에 대 한 호출로 폼을 읽었습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-254">The form has been read by a call to `ReadFormAsync`, and</span></span>
* <span data-ttu-id="5ba3e-255">@No__t를 사용 하 여 캐시 된 형식 값을 읽고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-255">The cached form value is being read using `HttpContext.Request.Form`</span></span>

<span data-ttu-id="5ba3e-256">**이 작업을 수행 하지 마십시오.** 다음 예제에서는 `HttpContext.Request.Form`합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-256">**Do not do this:** The following example uses `HttpContext.Request.Form`.</span></span>  <span data-ttu-id="5ba3e-257">`HttpContext.Request.Form`은 async @ no__t-2를 통해 [sync를 사용 하 고 스레드 풀 고갈를 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-257">`HttpContext.Request.Form` uses [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) and can lead to thread pool starvation.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

<span data-ttu-id="5ba3e-258">**다음 작업을 수행 합니다.** 다음 예제에서는 `HttpContext.Request.ReadFormAsync`을 사용 하 여 폼 본문을 비동기식으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-258">**Do this:** The following example uses `HttpContext.Request.ReadFormAsync` to read the form body asynchronously.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a><span data-ttu-id="5ba3e-259">대량 요청 본문 또는 응답 본문을 메모리로 읽지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-259">Avoid reading large request bodies or response bodies into memory</span></span>

<span data-ttu-id="5ba3e-260">.NET에서 85 KB 보다 큰 모든 개체 할당은[LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)(large object heap)에서 끝납니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-260">In .NET, every object allocation greater than 85 KB ends up in the large object heap ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)).</span></span> <span data-ttu-id="5ba3e-261">큰 개체는 다음과 같은 두 가지 방법으로 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-261">Large objects are expensive in two ways:</span></span>

* <span data-ttu-id="5ba3e-262">새로 할당 된 큰 개체의 메모리를 지워야 하므로 할당 비용이 높습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-262">The allocation cost is high because the memory for a newly allocated large object has to be cleared.</span></span> <span data-ttu-id="5ba3e-263">CLR은 새로 할당 된 모든 개체의 메모리를 지우도록 보장 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-263">The CLR guarantees that memory for all newly allocated objects is cleared.</span></span>
* <span data-ttu-id="5ba3e-264">LOH는 나머지 힙을 사용 하 여 수집 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-264">LOH is collected with the rest of the heap.</span></span> <span data-ttu-id="5ba3e-265">LOH에는 전체 [가비지 수집](/dotnet/standard/garbage-collection/fundamentals) 또는 [Gen2 컬렉션이](/dotnet/standard/garbage-collection/fundamentals#generations)필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-265">LOH requires a full [garbage collection](/dotnet/standard/garbage-collection/fundamentals) or [Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations).</span></span>

<span data-ttu-id="5ba3e-266">이 [블로그 게시물](https://adamsitnik.com/Array-Pool/#the-problem) 에서는 문제 간략하게을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-266">This [blog post](https://adamsitnik.com/Array-Pool/#the-problem) describes the problem succinctly:</span></span>

> <span data-ttu-id="5ba3e-267">많은 개체가 할당 되 면 Gen 2 개체로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-267">When a large object is allocated, it’s marked as Gen 2 object.</span></span> <span data-ttu-id="5ba3e-268">작은 개체의 경우에는 Gen 0이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-268">Not Gen 0 as for small objects.</span></span> <span data-ttu-id="5ba3e-269">LOH에서 메모리가 부족 한 경우 GC는 LOH 뿐만 아니라 전체 관리 되는 힙을 정리 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-269">The consequences are that if you run out of memory in LOH, GC cleans up the whole managed heap, not only LOH.</span></span> <span data-ttu-id="5ba3e-270">따라서 LOH를 포함 하 여 Gen 0, Gen 1 및 Gen 2를 정리 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-270">So it cleans up Gen 0, Gen 1 and Gen 2 including LOH.</span></span> <span data-ttu-id="5ba3e-271">이를 전체 가비지 수집 이라고 하며 가장 시간이 많이 걸리는 가비지 수집입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-271">This is called full garbage collection and is the most time-consuming garbage collection.</span></span> <span data-ttu-id="5ba3e-272">많은 응용 프로그램의 경우 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-272">For many applications, it can be acceptable.</span></span> <span data-ttu-id="5ba3e-273">하지만, 평균 웹 요청을 처리 하는 데 큰 메모리 버퍼가 거의 필요 하지 않은 고성능 웹 서버에 대해서는 명확 하지 않습니다 (소켓에서 읽기, 압축 풀기, JSON & 추가).</span><span class="sxs-lookup"><span data-stu-id="5ba3e-273">But definitely not for high-performance web servers, where few big memory buffers are needed to handle an average web request (read from a socket, decompress, decode JSON & more).</span></span>

<span data-ttu-id="5ba3e-274">단일 `byte[]` 또는 `string`에 대량 요청 또는 응답 본문을 저장 하는 것을 Naively.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-274">Naively storing a large request or response body into a single `byte[]` or `string`:</span></span>

* <span data-ttu-id="5ba3e-275">LOH의 공간이 빠르게 부족 해질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-275">May result in quickly running out of space in the LOH.</span></span>
* <span data-ttu-id="5ba3e-276">전체 Gc를 실행 하 여 앱에 대 한 성능 문제를 일으킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-276">May cause performance issues for the app because of full GCs running.</span></span>

## <a name="working-with-a-synchronous-data-processing-api"></a><span data-ttu-id="5ba3e-277">동기 데이터 처리 API 작업</span><span class="sxs-lookup"><span data-stu-id="5ba3e-277">Working with a synchronous data processing API</span></span>

<span data-ttu-id="5ba3e-278">동기 읽기 및 쓰기를 지 원하는 serializer/직렬 변환기를 사용 하는 경우 (예: [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span><span class="sxs-lookup"><span data-stu-id="5ba3e-278">When using a serializer/de-serializer that only supports synchronous reads and writes (for example,  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span></span>

* <span data-ttu-id="5ba3e-279">데이터를 직렬 변환기/역직렬화로 전달 하기 전에 비동기적으로 메모리에 버퍼링 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-279">Buffer the data into memory asynchronously before passing it into the serializer/de-serializer.</span></span>

> [!WARNING]
> <span data-ttu-id="5ba3e-280">요청이 크면 OOM (메모리 부족) 조건이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-280">If the request is large, it could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="5ba3e-281">OOM은 서비스 거부를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-281">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="5ba3e-282">자세한 내용은이 문서의 [메모리에 대 한 대량 요청 본문 또는 응답 본문 읽기 방지](#arlb) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-282">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="5ba3e-283">ASP.NET Core 3.0는 기본적으로 JSON serialization에 대해 <xref:System.Text.Json>을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-283">ASP.NET Core 3.0 uses <xref:System.Text.Json> by default for JSON serialization.</span></span> <span data-ttu-id="5ba3e-284"><xref:System.Text.Json>:</span><span class="sxs-lookup"><span data-stu-id="5ba3e-284"><xref:System.Text.Json>:</span></span>

* <span data-ttu-id="5ba3e-285">JSON을 비동기적으로 읽고 씁니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-285">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="5ba3e-286">UTF-8 텍스트에 최적화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-286">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="5ba3e-287">일반적으로 `Newtonsoft.Json`보다 성능이 높습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-287">Typically higher performance than `Newtonsoft.Json`.</span></span>

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a><span data-ttu-id="5ba3e-288">IHttpContextAccessor를 필드에 저장 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-288">Do not store IHttpContextAccessor.HttpContext in a field</span></span>

<span data-ttu-id="5ba3e-289">[IHttpContextAccessor](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) 는 요청 스레드에서 액세스 될 때 활성 요청의 `HttpContext`을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-289">The [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) returns the `HttpContext` of the active request when accessed from the request thread.</span></span> <span data-ttu-id="5ba3e-290">@No__t-0은 필드 또는 변수에 저장 **되지** 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-290">The `IHttpContextAccessor.HttpContext` should **not** be stored in a field or variable.</span></span>

<span data-ttu-id="5ba3e-291">**이 작업을 수행 하지 마십시오.** 다음 예에서는 `HttpContext`을 필드에 저장 한 다음 나중에 사용 하려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-291">**Do not do this:** The following example stores the `HttpContext` in a field, and then attempts to use it later.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

<span data-ttu-id="5ba3e-292">위의 코드는 생성자에서 null 또는 잘못 된 @no__t를 캡처하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-292">The preceding code frequently captures a null or incorrect `HttpContext` in the constructor.</span></span>

<span data-ttu-id="5ba3e-293">**다음 작업을 수행 합니다.** 다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="5ba3e-293">**Do this:** The following example:</span></span>

* <span data-ttu-id="5ba3e-294">필드에 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-294">Stores the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> in a field.</span></span>
* <span data-ttu-id="5ba3e-295">는 `HttpContext` 필드를 올바른 시간에 사용 하 고 `null` 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-295">Uses the `HttpContext` field at the correct time and checks for `null`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a><span data-ttu-id="5ba3e-296">여러 스레드에서 HttpContext에 액세스 하지 않음</span><span class="sxs-lookup"><span data-stu-id="5ba3e-296">Do not access HttpContext from multiple threads</span></span>

<span data-ttu-id="5ba3e-297">`HttpContext`은 스레드로부터 안전 *하지 않습니다* .</span><span class="sxs-lookup"><span data-stu-id="5ba3e-297">`HttpContext` is *NOT* thread-safe.</span></span> <span data-ttu-id="5ba3e-298">여러 스레드에서 동시에 `HttpContext`에 액세스 하면 중단, 충돌, 데이터 손상 등의 정의 되지 않은 동작이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-298">Accessing `HttpContext` from multiple threads in parallel can result in undefined behavior such as hangs, crashes, and data corruption.</span></span>

<span data-ttu-id="5ba3e-299">**이 작업을 수행 하지 마십시오.** 다음 예제에서는 세 개의 병렬 요청을 만들고 나가는 HTTP 요청 전후에 들어오는 요청 경로를 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-299">**Do not do this:** The following example makes three parallel requests and logs the incoming request path before and after the outgoing HTTP request.</span></span> <span data-ttu-id="5ba3e-300">요청 경로는 여러 스레드에서 동시에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-300">The request path is accessed from multiple threads, potentially in parallel.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

<span data-ttu-id="5ba3e-301">**다음 작업을 수행 합니다.** 다음 예에서는 세 개의 병렬 요청을 만들기 전에 들어오는 요청에서 모든 데이터를 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-301">**Do this:** The following example copies all data from the incoming request before making the three parallel requests.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a><span data-ttu-id="5ba3e-302">요청이 완료 된 후에 HttpContext를 사용 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-302">Do not use the HttpContext after the request is complete</span></span>

<span data-ttu-id="5ba3e-303">`HttpContext`은 ASP.NET Core 파이프라인에 활성 HTTP 요청이 있는 동안에만 유효 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-303">`HttpContext` is only valid as long as there is an active HTTP request in the ASP.NET Core pipeline.</span></span> <span data-ttu-id="5ba3e-304">전체 ASP.NET Core 파이프라인은 모든 요청을 실행 하는 대리자의 비동기 체인입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-304">The entire ASP.NET Core pipeline is an asynchronous chain of delegates that executes every request.</span></span> <span data-ttu-id="5ba3e-305">이 체인에서 반환 된 `Task`이 완료 되 면 `HttpContext`이 재활용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-305">When the `Task` returned from this chain completes, the `HttpContext` is recycled.</span></span>

<span data-ttu-id="5ba3e-306">**이 작업을 수행 하지 마십시오.** 다음 예에서는 `async void`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-306">**Do not do this:** The following example uses `async void`:</span></span>

* <span data-ttu-id="5ba3e-307">ASP.NET Core 앱에서 **항상** 잘못 된 관행입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-307">Which is **ALWAYS** a bad practice in ASP.NET Core apps.</span></span>
* <span data-ttu-id="5ba3e-308">HTTP 요청이 완료 된 후 `HttpResponse`에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-308">Accesses the `HttpResponse` after the HTTP request is complete.</span></span>
* <span data-ttu-id="5ba3e-309">프로세스를 중단 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-309">Crashes the process.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

<span data-ttu-id="5ba3e-310">**다음 작업을 수행 합니다.** 다음 예에서는 작업이 완료 될 때까지 HTTP 요청이 완료 되지 않도록 프레임 워크에 대 한 `Task`을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-310">**Do this:** The following example returns a `Task` to the framework so the HTTP request doesn't complete until the action completes.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a><span data-ttu-id="5ba3e-311">백그라운드 스레드에서 HttpContext를 캡처하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-311">Do not capture the HttpContext in background threads</span></span>

<span data-ttu-id="5ba3e-312">**이 작업을 수행 하지 마십시오.** 다음 예에서는 `Controller` 속성에서 `HttpContext`을 캡처하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-312">**Do not do this:** The following example shows a closure is capturing the `HttpContext` from the `Controller` property.</span></span> <span data-ttu-id="5ba3e-313">작업 항목에서 다음을 수행할 수 있기 때문에 잘못 된 사례입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-313">This is a bad practice because the work item could:</span></span>

* <span data-ttu-id="5ba3e-314">요청 범위 외부에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-314">Run outside of the request scope.</span></span>
* <span data-ttu-id="5ba3e-315">잘못 된 @no__t를 읽으려고 했습니다.-0.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-315">Attempt to read the wrong `HttpContext`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

<span data-ttu-id="5ba3e-316">**다음 작업을 수행 합니다.** 다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="5ba3e-316">**Do this:** The following example:</span></span>

* <span data-ttu-id="5ba3e-317">요청 하는 동안 백그라운드 작업에 필요한 데이터를 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-317">Copies the data required in the background task during the request.</span></span>
* <span data-ttu-id="5ba3e-318">는 컨트롤러에서 아무것도 참조 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-318">Doesn't reference anything from the controller.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a><span data-ttu-id="5ba3e-319">백그라운드 스레드에서 컨트롤러에 삽입 된 서비스를 캡처하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-319">Do not capture services injected into the controllers on background threads</span></span>

<span data-ttu-id="5ba3e-320">**이 작업을 수행 하지 마십시오.** 다음 예에서는 `Controller` 액션 매개 변수에서 `DbContext`을 캡처하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-320">**Do not do this:** The following example shows a closure is capturing the `DbContext` from the `Controller` action parameter.</span></span> <span data-ttu-id="5ba3e-321">이는 잘못 된 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-321">This is a bad practice.</span></span>  <span data-ttu-id="5ba3e-322">작업 항목은 요청 범위 외부에서 실행 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-322">The work item could run outside of the request scope.</span></span> <span data-ttu-id="5ba3e-323">@No__t-0은 요청으로 범위가 지정 되어 `ObjectDisposedException`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-323">The `ContosoDbContext` is scoped to the request, resulting in an `ObjectDisposedException`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

<span data-ttu-id="5ba3e-324">**다음 작업을 수행 합니다.** 다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="5ba3e-324">**Do this:** The following example:</span></span>

* <span data-ttu-id="5ba3e-325">백그라운드 작업 항목에서 범위를 만들기 위해 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>을 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-325">Injects an <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> in order to create a scope in the background work item.</span></span> <span data-ttu-id="5ba3e-326">`IServiceScopeFactory`은 단일 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-326">`IServiceScopeFactory` is a singleton.</span></span>
* <span data-ttu-id="5ba3e-327">백그라운드 스레드에서 새 종속성 주입 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-327">Creates a new dependency injection scope in the background thread.</span></span>
* <span data-ttu-id="5ba3e-328">는 컨트롤러에서 아무것도 참조 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-328">Doesn't reference anything from the controller.</span></span>
* <span data-ttu-id="5ba3e-329">들어오는 요청에서 `ContosoDbContext`을 캡처하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-329">Doesn't capture the `ContosoDbContext` from the incoming request.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

<span data-ttu-id="5ba3e-330">강조 표시 된 코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-330">The following highlighted code:</span></span>

* <span data-ttu-id="5ba3e-331">백그라운드 작업의 수명 범위를 만들고 해당 작업에서 서비스를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-331">Creates a scope for the lifetime of the background operation and resolves services from it.</span></span>
* <span data-ttu-id="5ba3e-332">는 올바른 범위에서 `ContosoDbContext`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-332">Uses `ContosoDbContext` from the correct scope.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a><span data-ttu-id="5ba3e-333">응답 본문이 시작 된 후 상태 코드 또는 헤더를 수정 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-333">Do not modify the status code or headers after the response body has started</span></span>

<span data-ttu-id="5ba3e-334">ASP.NET Core는 HTTP 응답 본문을 버퍼링 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-334">ASP.NET Core does not buffer the HTTP response body.</span></span> <span data-ttu-id="5ba3e-335">처음 응답을 쓸 때:</span><span class="sxs-lookup"><span data-stu-id="5ba3e-335">The first time the response is written:</span></span>

* <span data-ttu-id="5ba3e-336">헤더는 해당 본문의 청크와 함께 클라이언트에 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-336">The headers are sent along with that chunk of the body to the client.</span></span>
* <span data-ttu-id="5ba3e-337">더 이상 응답 헤더를 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-337">It's no longer possible to change response headers.</span></span>

<span data-ttu-id="5ba3e-338">**이 작업을 수행 하지 마십시오.** 다음 코드는 응답이 이미 시작 된 후 응답 헤더를 추가 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-338">**Do not do this:** The following code tries to add response headers after the response has already started:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

<span data-ttu-id="5ba3e-339">위의 코드에서 `next()` @no__t 응답에 기록 된 경우 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-339">In the preceding code, `context.Response.Headers["test"] = "test value";` will throw an exception if `next()` has written to the response.</span></span>

<span data-ttu-id="5ba3e-340">**다음 작업을 수행 합니다.** 다음 예에서는 헤더를 수정 하기 전에 HTTP 응답이 시작 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-340">**Do this:** The following example checks if the HTTP response has started before modifying the headers.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

<span data-ttu-id="5ba3e-341">**다음 작업을 수행 합니다.** 다음 예제에서는 `HttpResponse.OnStarting`을 사용 하 여 응답 헤더가 클라이언트에 플러시될 때까지 헤더를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-341">**Do this:** The following example uses `HttpResponse.OnStarting` to set the headers before the response headers are flushed to the client.</span></span>

<span data-ttu-id="5ba3e-342">응답을 시작 하지 않은 경우 응답 헤더를 쓰기 직전에 호출 되는 콜백을 등록할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-342">Checking if the response has not started allows registering a callback that will be invoked just before response headers are written.</span></span> <span data-ttu-id="5ba3e-343">응답이 시작 되지 않은 경우를 확인 하는 중:</span><span class="sxs-lookup"><span data-stu-id="5ba3e-343">Checking if the response has not started:</span></span>

* <span data-ttu-id="5ba3e-344">헤더를 적시에 추가 하거나 재정의 하는 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-344">Provides the ability to append or override headers just in time.</span></span>
* <span data-ttu-id="5ba3e-345">파이프라인의 다음 미들웨어에 대 한 지식이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-345">Doesn't require knowledge of the next middleware in the pipeline.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a><span data-ttu-id="5ba3e-346">응답 본문에 대 한 쓰기를 이미 시작한 경우 next ()를 호출 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-346">Do not call next() if you have already started writing to the response body</span></span>

<span data-ttu-id="5ba3e-347">구성 요소는 응답을 처리 하 고 조작할 수 있는 경우에만 호출 될 것으로 예상 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5ba3e-347">Components only expect to be called if it's possible for them to handle and manipulate the response.</span></span>
