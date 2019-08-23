---
title: ASP.NET Core의 메모리 내 캐시
author: rick-anderson
description: ASP.NET Core에서 데이터를 메모리에 캐시하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 8/11/2019
uid: performance/caching/memory
ms.openlocfilehash: 474f71225371ea89b023ee077d4ecc03e9751681
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030324"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="9e073-103">ASP.NET Core의 메모리 내 캐시</span><span class="sxs-lookup"><span data-stu-id="9e073-103">Cache in-memory in ASP.NET Core</span></span>

<span data-ttu-id="9e073-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9e073-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9e073-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9e073-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="9e073-106">캐싱 기본 사항</span><span class="sxs-lookup"><span data-stu-id="9e073-106">Caching basics</span></span>

<span data-ttu-id="9e073-107">캐싱은 콘텐츠를 생성하기 위해 필요한 작업을 줄임으로써 응용 프로그램의 성능 및 확장성을 크게 향상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="9e073-108">캐싱은 자주 변경되지 않는 데이터에 가장 효과가 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-108">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="9e073-109">캐싱은 원본에서 가져와서 반환하는 것보다 더 빠르게 반환할 수 있는 데이터의 복사본을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-109">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="9e073-110">캐시 된 데이터에 종속 **되지 않도록** 앱을 작성 하 고 테스트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-110">Apps should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="9e073-111">ASP.NET Core는 몇 가지 다른 종류의 캐시를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="9e073-112">가장 간단한 캐시는 웹 서버의 메모리에 저장된 캐시를 나타내는 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="9e073-113">여러 서버의 서버 팜에서 실행 되는 앱은 메모리 내 캐시를 사용할 때 세션이 고정 되어 있는지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-113">Apps that run on a server farm of multiple servers should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="9e073-114">고정 세션은 클라이언트의 이어지는 후속 요청이 모두 동일한 서버로 전달되도록 보장해줍니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-114">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="9e073-115">예를 들어 Azure 웹 앱은 이어지는 모든 후속 요청을 동일한 서버로 라우트하기 위해서 [응용 프로그램 요청 라우팅](https://www.iis.net/learn/extensions/planning-for-arr)(ARR)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-115">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="9e073-116">웹 팜에서 비-고정 세션을 사용할 경우에는 캐시 일관성 문제가 발생하지 않도록 [분산 캐시](distributed.md)가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-116">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="9e073-117">일부 앱의 경우 분산 캐시는 메모리 내 캐시 보다 더 높은 확장을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-117">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="9e073-118">분산 캐시를 사용하면 캐시 메모리를 외부 프로세스에서 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-118">Using a distributed cache offloads the cache memory to an external process.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="9e073-119">`IMemoryCache` 캐시는 [캐시 우선 순위](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority)가 `CacheItemPriority.NeverRemove`로 설정되지 않은 캐시 항목을 메모리 부하에 따라 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-119">The `IMemoryCache` cache will evict cache entries under memory pressure unless the [cache priority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) is set to `CacheItemPriority.NeverRemove`.</span></span> <span data-ttu-id="9e073-120">`CacheItemPriority`를 설정하면 메모리에 부하가 걸렸을 때 캐시가 항목을 제거하는 우선 순위를 조정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-120">You can set the `CacheItemPriority` to adjust the priority with which the cache evicts items under memory pressure.</span></span>

::: moniker-end

<span data-ttu-id="9e073-121">메모리 내 캐시는 모든 개체를 저장할 수 있는 반면, 분산 캐시 인터페이스는 `byte[]`만 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-121">The in-memory cache can store any object; the distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="9e073-122">메모리 내 및 분산 캐시 저장소는 키-값 쌍으로 항목을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-122">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="9e073-123">System.Runtime.Caching/MemoryCache</span><span class="sxs-lookup"><span data-stu-id="9e073-123">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="9e073-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache>([NuGet 패키지](https://www.nuget.org/packages/System.Runtime.Caching/))는와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="9e073-125">2\.0 이상 .NET Standard 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-125">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="9e073-126">.NET Standard 2.0 이상을 대상으로 하는 [.net 구현](/dotnet/standard/net-standard#net-implementation-support)</span><span class="sxs-lookup"><span data-stu-id="9e073-126">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="9e073-127">예를 들어 ASP.NET Core 2.0 이상입니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-127">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="9e073-128">4\.5 이상 .NET Framework 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-128">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="9e073-129">[이 문서](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) 에 설명 된 대로, ASP.NET Core에 더 잘 통합 되기때문에이문서에설명되어있는것이좋습니다.`MemoryCache` / `System.Runtime.Caching` / `IMemoryCache`</span><span class="sxs-lookup"><span data-stu-id="9e073-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="9e073-130">예 `IMemoryCache` 를 들어은 ASP.NET Core [종속성 주입](xref:fundamentals/dependency-injection)을 기본적으로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-130">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="9e073-131">ASP.NET `System.Runtime.Caching` 4.x에서 ASP.NET Core로 코드를 이식할 때 호환성 브리지로를 사용 / `MemoryCache` 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-131">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="9e073-132">캐시 지침</span><span class="sxs-lookup"><span data-stu-id="9e073-132">Cache guidelines</span></span>

* <span data-ttu-id="9e073-133">코드에는 항상 데이터를 인출 하는 대체 (fallback) 옵션이 있으며 사용 가능한 캐시 된 값에 의존 **하지** 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-133">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="9e073-134">캐시는 부족 한 리소스 인 메모리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-134">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="9e073-135">캐시 증가 제한:</span><span class="sxs-lookup"><span data-stu-id="9e073-135">Limit cache growth:</span></span>
  * <span data-ttu-id="9e073-136">외부 입력을 캐시 키로 사용 **하지** 마십시오.</span><span class="sxs-lookup"><span data-stu-id="9e073-136">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="9e073-137">캐시 증가를 제한 하려면 만료를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-137">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="9e073-138">[SetSize, Size 및 SizeLimit를 사용 하 여 캐시 크기를 제한](#use-setsize-size-and-sizelimit-to-limit-cache-size)합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-138">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="9e073-139">ASP.NET Core 런타임은 메모리 압력에 따라 캐시 크기를 제한 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-139">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="9e073-140">캐시 크기를 제한 하는 것은 개발자에 게 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-140">It's up to the developer to limit cache size.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="9e073-141">IMemoryCache 사용하기</span><span class="sxs-lookup"><span data-stu-id="9e073-141">Using IMemoryCache</span></span>

> [!WARNING]
> <span data-ttu-id="9e073-142">[종속성 주입](xref:fundamentals/dependency-injection) 에서 *공유* 메모리 캐시를 사용 하 고 `SetSize`, `Size`또는 `SizeLimit` 를 호출 하 여 캐시 크기를 제한 하면 앱에 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-142">Using a *shared* memory cache from [Dependency Injection](xref:fundamentals/dependency-injection) and calling `SetSize`, `Size`, or `SizeLimit` to limit cache size can cause the app to fail.</span></span> <span data-ttu-id="9e073-143">캐시에 크기 제한이 설정 된 경우 모든 항목은 추가 될 때 크기를 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-143">When a size limit is set on a cache, all entries must specify a size when being added.</span></span> <span data-ttu-id="9e073-144">이로 인해 개발자가 공유 캐시를 사용 하는 항목을 완전히 제어할 수 없기 때문에 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-144">This can lead to issues since developers may not have full control on what uses the shared cache.</span></span> <span data-ttu-id="9e073-145">예를 들어 Entity Framework Core는 공유 캐시를 사용 하 고 크기를 지정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-145">For example, Entity Framework Core uses the shared cache and does not specify a size.</span></span> <span data-ttu-id="9e073-146">앱이 캐시 크기 제한을 설정 하 고 EF Core를 사용 하는 경우 앱은 `InvalidOperationException`을 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-146">If an app sets a cache size limit and uses EF Core, the app throws an `InvalidOperationException`.</span></span>
> <span data-ttu-id="9e073-147">, `SetSize` `Size`또는 를사용하여캐시를제한하는경우캐싱에대한캐시singleton을만듭니다.`SizeLimit`</span><span class="sxs-lookup"><span data-stu-id="9e073-147">When using `SetSize`, `Size`, or `SizeLimit` to limit cache, create a cache singleton for caching.</span></span> <span data-ttu-id="9e073-148">자세한 내용 및 예제는 [SetSize, Size 및 SizeLimit를 사용 하 여 캐시 크기 제한](#use-setsize-size-and-sizelimit-to-limit-cache-size)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9e073-148">For more information and an example, see [Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span>

<span data-ttu-id="9e073-149">메모리 내 캐시는 응용 프로그램에서 [종속성 주입](xref:fundamentals/dependency-injection)을 통해서 참조되는 *서비스*입니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-149">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9e073-150">`ConfigureServices`에서 `AddMemoryCache`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-150">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="9e073-151">그런 다음 생성자에서 `IMemoryCache`의 인스턴스를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-151">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="9e073-152">`IMemoryCache`를 사용하려면 [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) NuGet 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-152">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="9e073-153">`IMemoryCache`를 사용하려면 [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage)에서 사용할 수 있는 [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) NuGet 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-153">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="9e073-154">`IMemoryCache`를 사용하려면 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)에서 사용할 수 있는 [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) NuGet 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-154">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="9e073-155">다음 코드는 [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)를 이용해서 시간이 캐시되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-155">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="9e073-156">만약 시간이 캐시되어 있지 않다면 새로운 항목이 생성되고 [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)을 통해서 캐시에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-156">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="9e073-157">현재 시간과 캐시된 시간이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-157">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="9e073-158">제한 시간 내에 요청이 있는 동안 캐시 된 값은캐시에남아있습니다.`DateTime`</span><span class="sxs-lookup"><span data-stu-id="9e073-158">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span> <span data-ttu-id="9e073-159">다음 그림은 현재 시간과 캐시에서 조회한 그보다 오래된 시간을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-159">The following image shows the current time and an older time retrieved from the cache:</span></span>

![두 개의 서로 다른 시간을 표시하는 Index 뷰](memory/_static/time.png)

<span data-ttu-id="9e073-161">다음 코드는 [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) 및 [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) 를 이용해서 데이터를 캐시합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-161">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="9e073-162">다음 코드는 [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 을 호출해서 캐시된 시간을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-162">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="9e073-163"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>및 [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 은의 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>기능을 확장 하는 [cacheextensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) 클래스의 일부인 확장 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-163"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) are extension methods part of the [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) class that extends the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="9e073-164">다른 캐시 메서드에 대 한 설명은 [IMemoryCache 메서드](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) 및 [cacheextensions 메서드](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9e073-164">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of other cache methods.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="9e073-165">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="9e073-165">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="9e073-166">다음 예제에서는:</span><span class="sxs-lookup"><span data-stu-id="9e073-166">The following sample:</span></span>

* <span data-ttu-id="9e073-167">절대 만료 시간을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-167">Sets the absolute expiration time.</span></span> <span data-ttu-id="9e073-168">절대 만료 시간은 항목이 캐시될 수 있는 최대 시간으로, 슬라이딩 만료가 지속적으로 갱신될 경우 항목이 이전 상태로 지속되는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-168">This is the maximum time the entry can be cached and prevents the item from becoming too stale when the sliding expiration is continuously renewed.</span></span>
* <span data-ttu-id="9e073-169">슬라이딩 만료 시간을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-169">Sets a sliding expiration time.</span></span> <span data-ttu-id="9e073-170">상대 만료 시계는 캐시된 항목에 접근하는 요청에 따라 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-170">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="9e073-171">캐시 우선 순위를 `CacheItemPriority.NeverRemove`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-171">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
* <span data-ttu-id="9e073-172">캐시에서 항목이 제거된 후에 호출되는 [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 를 설정합니다. 콜백은 캐시에서 항목을 제거하는 코드와 다른 스레드에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-172">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="9e073-173">콜백은 캐시에서 항목을 제거 하는 코드와 다른 스레드에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-173">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="9e073-174">SetSize, Size 및 SizeLimit를 사용 하 여 캐시 크기 제한</span><span class="sxs-lookup"><span data-stu-id="9e073-174">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="9e073-175">인스턴스 `MemoryCache` 는 필요에 따라 크기 제한을 지정 하 고 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-175">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="9e073-176">캐시에 항목 크기를 측정 하는 메커니즘이 없기 때문에 메모리 크기 제한에 정의 된 측정 단위가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-176">The memory size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="9e073-177">캐시 메모리 크기 제한이 설정 된 경우 모든 항목의 크기를 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-177">If the cache memory size limit is set, all entries must specify size.</span></span> <span data-ttu-id="9e073-178">ASP.NET Core 런타임은 메모리 압력에 따라 캐시 크기를 제한 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-178">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="9e073-179">캐시 크기를 제한 하는 것은 개발자에 게 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-179">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="9e073-180">지정 된 크기는 개발자가 선택 하는 단위입니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-180">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="9e073-181">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="9e073-181">For example:</span></span>

* <span data-ttu-id="9e073-182">웹 앱이 주로 문자열을 캐싱하는 경우 각 캐시 엔트리 크기는 문자열 길이가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-182">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="9e073-183">앱은 모든 항목의 크기를 1로 지정 하 고, 크기 제한은 항목 수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-183">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="9e073-184">다음 코드는 [종속성 주입](xref:fundamentals/dependency-injection)으로 액세스할 수 있는 고정 크기의 고정 크기 [memorycache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-184">The following code creates a unitless fixed size [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="9e073-185">[Sizelimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) 에는 단위가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-185">[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) does not have units.</span></span> <span data-ttu-id="9e073-186">캐시 된 항목은 캐시 메모리 크기가 설정 된 경우 가장 적합 한 것으로 간주 되는 단위의 크기를 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-186">Cached entries must specify size in whatever units they deem most appropriate if the cache memory size has been set.</span></span> <span data-ttu-id="9e073-187">캐시 인스턴스의 모든 사용자는 동일한 단위 시스템을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-187">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="9e073-188">캐시 된 항목 크기의 합계가에 `SizeLimit`지정 된 값을 초과 하는 경우 항목이 캐시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-188">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="9e073-189">캐시 크기 제한을 설정 하지 않으면 항목에 설정 된 캐시 크기가 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-189">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="9e073-190">다음 코드는 `MyMemoryCache` [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-190">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="9e073-191">`MyMemoryCache`는이 크기 제한 된 캐시를 인식 하는 구성 요소에 대 한 독립 메모리 캐시로 만들어지며 캐시 엔트리 크기를 적절 하 게 설정 하는 방법을 알고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-191">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="9e073-192">다음 코드에서는를 `MyMemoryCache`사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-192">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="9e073-193">캐시 엔트리의 크기는 [크기](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) 또는 [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) 확장 메서드를 통해 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-193">The size of the cache entry can be set by [Size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) or the [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) extension method:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a><span data-ttu-id="9e073-194">캐시 종속성</span><span class="sxs-lookup"><span data-stu-id="9e073-194">Cache dependencies</span></span>

<span data-ttu-id="9e073-195">다음 예제는 종속적인 항목을 만료할 경우 캐시 항목을 만료하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-195">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="9e073-196">캐시된 항목에 `CancellationChangeToken`이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-196">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="9e073-197">`CancellationTokenSource`의 `Cancel`이 호출되면 캐시된 두 항목 모두 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-197">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="9e073-198">`CancellationTokenSource`를 사용하면 다수의 캐시 항목을 그룹으로 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-198">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="9e073-199">위의 코드의 `using` 패턴을 사용하면,`using` 블록 안에서 생성된 캐시 항목들이 트리거 및 만료 설정을 상속받습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-199">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="9e073-200">추가 참고 사항</span><span class="sxs-lookup"><span data-stu-id="9e073-200">Additional notes</span></span>

* <span data-ttu-id="9e073-201">캐시 항목을 다시 채우기 위해서 콜백을 사용할 때:</span><span class="sxs-lookup"><span data-stu-id="9e073-201">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="9e073-202">다수의 요청이 캐시된 키 값으로 빈 값을 얻을 수도 있는데, 이는 콜백이 완료되지 않았기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-202">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="9e073-203">이로 인해 다수의 스레드가 캐시된 항목을 다시 채울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-203">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="9e073-204">한 캐시 항목을 사용해서 다른 캐시 항목을 만들 때, 하위 항목은 부모 항목의 만료 토큰 및 시간 기반의 만료 설정을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-204">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="9e073-205">하위 항목은 부모 항목을 수동으로 제거하거나 갱신하더라도 만료되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-205">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="9e073-206">[PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) 를 사용 하 여 캐시 엔트리를 캐시에서 제거한 후에 발생 하는 콜백을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e073-206">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e073-207">추가 자료</span><span class="sxs-lookup"><span data-stu-id="9e073-207">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
