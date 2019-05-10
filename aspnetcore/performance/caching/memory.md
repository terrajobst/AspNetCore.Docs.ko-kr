---
title: ASP.NET Core의 메모리 내 캐시
author: rick-anderson
description: ASP.NET Core에서 데이터를 메모리에 캐시하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/11/2019
uid: performance/caching/memory
ms.openlocfilehash: 1474dd397f2a8f83c7a1b0ead511b7f5751865bb
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894350"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="8fa72-103">ASP.NET Core의 메모리 내 캐시</span><span class="sxs-lookup"><span data-stu-id="8fa72-103">Cache in-memory in ASP.NET Core</span></span>

<span data-ttu-id="8fa72-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8fa72-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8fa72-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8fa72-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="8fa72-106">캐싱 기본 사항</span><span class="sxs-lookup"><span data-stu-id="8fa72-106">Caching basics</span></span>

<span data-ttu-id="8fa72-107">캐싱은 콘텐츠를 생성하기 위해 필요한 작업을 줄임으로써 응용 프로그램의 성능 및 확장성을 크게 향상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="8fa72-108">캐싱은 자주 변경되지 않는 데이터에 가장 효과가 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-108">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="8fa72-109">캐싱은 원본에서 가져와서 반환하는 것보다 더 빠르게 반환할 수 있는 데이터의 복사본을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-109">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="8fa72-110">앱 작성 되 고 테스트 해야 **되지** 캐시 된 데이터에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-110">Apps should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="8fa72-111">ASP.NET Core는 몇 가지 다른 종류의 캐시를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="8fa72-112">가장 간단한 캐시는 웹 서버의 메모리에 저장된 캐시를 나타내는 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="8fa72-113">여러 서버의 서버 팜에서 실행 되는 앱은 메모리 내 캐시를 사용 하는 경우 세션 고정 되는지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-113">Apps that run on a server farm of multiple servers should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="8fa72-114">고정 세션은 클라이언트의 이어지는 후속 요청이 모두 동일한 서버로 전달되도록 보장해줍니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-114">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="8fa72-115">예를 들어 Azure 웹 앱은 이어지는 모든 후속 요청을 동일한 서버로 라우트하기 위해서 [응용 프로그램 요청 라우팅](https://www.iis.net/learn/extensions/planning-for-arr)(ARR)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-115">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="8fa72-116">웹 팜에서 비-고정 세션을 사용할 경우에는 캐시 일관성 문제가 발생하지 않도록 [분산 캐시](distributed.md)가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-116">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="8fa72-117">일부 앱에서는 분산된 된 캐시는 메모리 내 캐시 보다 더 높은 규모를 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-117">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="8fa72-118">분산 캐시를 사용하면 캐시 메모리를 외부 프로세스에서 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-118">Using a distributed cache offloads the cache memory to an external process.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="8fa72-119">`IMemoryCache` 캐시는 [캐시 우선 순위](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority)가 `CacheItemPriority.NeverRemove`로 설정되지 않은 캐시 항목을 메모리 부하에 따라 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-119">The `IMemoryCache` cache will evict cache entries under memory pressure unless the [cache priority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) is set to `CacheItemPriority.NeverRemove`.</span></span> <span data-ttu-id="8fa72-120">`CacheItemPriority`를 설정하면 메모리에 부하가 걸렸을 때 캐시가 항목을 제거하는 우선 순위를 조정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-120">You can set the `CacheItemPriority` to adjust the priority with which the cache evicts items under memory pressure.</span></span>

::: moniker-end

<span data-ttu-id="8fa72-121">메모리 내 캐시는 모든 개체를 저장할 수 있는 반면, 분산 캐시 인터페이스는 `byte[]`만 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-121">The in-memory cache can store any object; the distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="8fa72-122">키-값 쌍으로 메모리 내 및 분산 캐시 저장소 캐시 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-122">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="8fa72-123">System.Runtime.Caching/MemoryCache</span><span class="sxs-lookup"><span data-stu-id="8fa72-123">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="8fa72-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet 패키지](https://www.nuget.org/packages/System.Runtime.Caching/)) 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="8fa72-125">.NET 표준 2.0 이상입니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-125">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="8fa72-126">모든 [.NET 구현](/dotnet/standard/net-standard#net-implementation-support) .NET Standard 2.0 이상을 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-126">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="8fa72-127">예를 들어, ASP.NET Core 2.0 이상.</span><span class="sxs-lookup"><span data-stu-id="8fa72-127">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="8fa72-128">.NET framework 4.5 이상</span><span class="sxs-lookup"><span data-stu-id="8fa72-128">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="8fa72-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (이 문서에서 설명)는 것이 좋습니다 `System.Runtime.Caching` / `MemoryCache` ASP.NET Core를 더 잘 통합 되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="8fa72-130">예를 들어 `IMemoryCache` ASP.NET Core를 사용 하 여 고유 하 게 작동 [종속성 주입](xref:fundamentals/dependency-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-130">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="8fa72-131">사용 하 여 `System.Runtime.Caching` / `MemoryCache` ASP.NET에서 코드를 이식 하는 경우 호환성 다리 4.x ASP.NET Core에서.</span><span class="sxs-lookup"><span data-stu-id="8fa72-131">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="8fa72-132">캐시 지침</span><span class="sxs-lookup"><span data-stu-id="8fa72-132">Cache guidelines</span></span>

* <span data-ttu-id="8fa72-133">코드에서 데이터를 인출 하는 대체 (fallback) 옵션을 항상 있어야 하 고 **되지** 사용할 수 있는 캐시 된 값에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-133">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="8fa72-134">캐시는 메모리 부족 한 리소스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-134">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="8fa72-135">캐시 증가 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-135">Limit cache growth:</span></span>
  * <span data-ttu-id="8fa72-136">수행할 **되지** 캐시 키로 외부 입력을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-136">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="8fa72-137">캐시 증가 제한 하려면 만료를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-137">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="8fa72-138">[SetSize, 크기 및 SizeLimit 최대 캐시 크기를 사용 하 여](#use-setsize-size-and-sizelimit-to-limit-cache-size)입니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-138">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="8fa72-139">ASP.NET Core 런타임이 메모리 압력에 따라 캐시 크기를 제한 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-139">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="8fa72-140">캐시 크기를 제한 하는 개발자가.</span><span class="sxs-lookup"><span data-stu-id="8fa72-140">It's up to the developer to limit cache size.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="8fa72-141">IMemoryCache 사용하기</span><span class="sxs-lookup"><span data-stu-id="8fa72-141">Using IMemoryCache</span></span>

<span data-ttu-id="8fa72-142">메모리 내 캐시는 응용 프로그램에서 [종속성 주입](../../fundamentals/dependency-injection.md)을 통해서 참조되는 *서비스*입니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-142">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="8fa72-143">`ConfigureServices`에서 `AddMemoryCache`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-143">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="8fa72-144">그런 다음 생성자에서 `IMemoryCache`의 인스턴스를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-144">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="8fa72-145">`IMemoryCache` NuGet 패키지 [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-145">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="8fa72-146">`IMemoryCache` NuGet 패키지 [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)에서 제공 되는 [Microsoft.AspNetCore.All 메타 패키지](xref:fundamentals/metapackage)합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-146">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="8fa72-147">`IMemoryCache` NuGet 패키지 [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)에서 제공 되는 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-147">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="8fa72-148">다음 코드는 [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)를 이용해서 시간이 캐시되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-148">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="8fa72-149">만약 시간이 캐시되어 있지 않다면 새로운 항목이 생성되고 [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)을 통해서 캐시에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-149">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="8fa72-150">현재 시간과 캐시된 시간이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-150">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="8fa72-151">캐시 된 `DateTime` 값은 제한 시간 내에 요청이 있는 동안 캐시에 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-151">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span> <span data-ttu-id="8fa72-152">다음 그림은 현재 시간과 캐시에서 조회한 그보다 오래된 시간을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-152">The following image shows the current time and an older time retrieved from the cache:</span></span>

![두 개의 서로 다른 시간을 표시하는 Index 뷰](memory/_static/time.png)

<span data-ttu-id="8fa72-154">다음 코드는 [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) 및 [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) 를 이용해서 데이터를 캐시합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-154">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="8fa72-155">다음 코드는 [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 을 호출해서 캐시된 시간을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-155">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="8fa72-156"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, 및 [가져올](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 일부인 확장 메서드를 [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) 기능을 확장 하는 클래스 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-156"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) are extension methods part of the [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) class that extends the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="8fa72-157">참조 [IMemoryCache 메서드](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) 하 고 [CacheExtensions 메서드](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) 다른 캐시 방법에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-157">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of other cache methods.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="8fa72-158">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="8fa72-158">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="8fa72-159">다음 예제에서는:</span><span class="sxs-lookup"><span data-stu-id="8fa72-159">The following sample:</span></span>

* <span data-ttu-id="8fa72-160">절대 만료 시간을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-160">Sets the absolute expiration time.</span></span> <span data-ttu-id="8fa72-161">절대 만료 시간은 항목이 캐시될 수 있는 최대 시간으로, 슬라이딩 만료가 지속적으로 갱신될 경우 항목이 이전 상태로 지속되는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-161">This is the maximum time the entry can be cached and prevents the item from becoming too stale when the sliding expiration is continuously renewed.</span></span>
* <span data-ttu-id="8fa72-162">슬라이딩 만료 시간을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-162">Sets a sliding expiration time.</span></span> <span data-ttu-id="8fa72-163">상대 만료 시계는 캐시된 항목에 접근하는 요청에 따라 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-163">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="8fa72-164">캐시 우선 순위를 `CacheItemPriority.NeverRemove`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-164">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
* <span data-ttu-id="8fa72-165">캐시에서 항목이 제거된 후에 호출되는 [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 를 설정합니다. 콜백은 캐시에서 항목을 제거하는 코드와 다른 스레드에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-165">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="8fa72-166">콜백 캐시에서 항목을 제거 하는 코드에서 다른 스레드에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-166">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="8fa72-167">SetSize, 크기 및 SizeLimit를 사용 하 여 캐시 크기를 제한 하려면</span><span class="sxs-lookup"><span data-stu-id="8fa72-167">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="8fa72-168">`MemoryCache` 인스턴스 수 선택적으로 지정 하 고 크기 제한을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-168">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="8fa72-169">메모리 크기 제한 없는 정의 된 측정 단위를 캐시에 항목의 크기를 측정 하는 메커니즘이 없기 때문에 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-169">The memory size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="8fa72-170">캐시 메모리 크기 제한을 설정 된 경우 모든 항목 크기를 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-170">If the cache memory size limit is set, all entries must specify size.</span></span> <span data-ttu-id="8fa72-171">ASP.NET Core 런타임이 메모리 압력에 따라 캐시 크기를 제한 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-171">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="8fa72-172">캐시 크기를 제한 하는 개발자가.</span><span class="sxs-lookup"><span data-stu-id="8fa72-172">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="8fa72-173">지정 된 크기가 단위는 개발자가 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-173">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="8fa72-174">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="8fa72-174">For example:</span></span>

* <span data-ttu-id="8fa72-175">웹 앱 문자열 캐싱 주로 된 각 캐시 항목 크기 문자열 길이 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-175">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="8fa72-176">앱 1로 모든 항목의 크기를 지정할 수 및 크기 제한은 항목의 개수입니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-176">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="8fa72-177">다음 코드는 고정 크기 단위를 만듭니다 [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) 액세스할 [종속성 주입](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="8fa72-177">The following code creates a unitless fixed size [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="8fa72-178">[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) 단위가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-178">[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) does not have units.</span></span> <span data-ttu-id="8fa72-179">캐시 된 항목에 관계 없이 단위 하다 고 간주 캐시 메모리 크기를 설정한 경우에 가장 적합 한 크기를 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-179">Cached entries must specify size in whatever units they deem most appropriate if the cache memory size has been set.</span></span> <span data-ttu-id="8fa72-180">캐시 인스턴스의 모든 사용자는 동일한 단위 시스템을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-180">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="8fa72-181">항목이 캐시 항목 크기의 합계에 지정 된 값을 초과 하는 경우 캐시 되지 않을 `SizeLimit`합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-181">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="8fa72-182">캐시 크기 제한이 설정 된 경우 항목에 설정 된 캐시 크기 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-182">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="8fa72-183">다음 코드 레지스터 `MyMemoryCache` 사용 하 여 합니다 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너입니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-183">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="8fa72-184">`MyMemoryCache` 이 제한 된 크기 캐시 인식 하 고 캐시 항목 크기를 적절 하 게 설정 하는 방법을 알고 있는 구성 요소에 대 한 독립적인 메모리 캐시를 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-184">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="8fa72-185">다음 코드에서는 `MyMemoryCache`:</span><span class="sxs-lookup"><span data-stu-id="8fa72-185">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="8fa72-186">캐시 항목의 크기를 설정할 수 있습니다 [크기](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) 또는 [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) 확장 메서드:</span><span class="sxs-lookup"><span data-stu-id="8fa72-186">The size of the cache entry can be set by [Size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) or the [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) extension method:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a><span data-ttu-id="8fa72-187">캐시 종속성</span><span class="sxs-lookup"><span data-stu-id="8fa72-187">Cache dependencies</span></span>

<span data-ttu-id="8fa72-188">다음 예제는 종속적인 항목을 만료할 경우 캐시 항목을 만료하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-188">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="8fa72-189">캐시된 항목에 `CancellationChangeToken`이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-189">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="8fa72-190">`CancellationTokenSource`의 `Cancel`이 호출되면 캐시된 두 항목 모두 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-190">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="8fa72-191">`CancellationTokenSource`를 사용하면 다수의 캐시 항목을 그룹으로 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-191">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="8fa72-192">위의 코드의 `using` 패턴을 사용하면,`using` 블록 안에서 생성된 캐시 항목들이 트리거 및 만료 설정을 상속받습니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-192">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="8fa72-193">추가 참고 사항</span><span class="sxs-lookup"><span data-stu-id="8fa72-193">Additional notes</span></span>

* <span data-ttu-id="8fa72-194">캐시 항목을 다시 채우기 위해서 콜백을 사용할 때:</span><span class="sxs-lookup"><span data-stu-id="8fa72-194">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="8fa72-195">다수의 요청이 캐시된 키 값으로 빈 값을 얻을 수도 있는데, 이는 콜백이 완료되지 않았기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-195">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="8fa72-196">이로 인해 다수의 스레드가 캐시된 항목을 다시 채울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-196">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="8fa72-197">한 캐시 항목을 사용해서 다른 캐시 항목을 만들 때, 하위 항목은 부모 항목의 만료 토큰 및 시간 기반의 만료 설정을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-197">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="8fa72-198">하위 항목은 부모 항목을 수동으로 제거하거나 갱신하더라도 만료되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-198">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="8fa72-199">사용 하 여 [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) 캐시 엔트리를 캐시에서 제거 후에 시작 되는 콜백을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8fa72-199">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8fa72-200">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8fa72-200">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
