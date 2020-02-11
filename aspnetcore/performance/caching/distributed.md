---
title: ASP.NET Core 분산 캐싱
author: guardrex
description: 특히 클라우드 또는 서버 팜 환경에서 ASP.NET Core 분산 캐시를 사용 하 여 앱 성능 및 확장성을 개선 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: performance/caching/distributed
ms.openlocfilehash: d39ac6c7496de7cf9dc8d40718bbaf611e744c19
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114746"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="05429-103">ASP.NET Core 분산 캐싱</span><span class="sxs-lookup"><span data-stu-id="05429-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="05429-104">By [Luke Latham](https://github.com/guardrex), [Mohsin Nasir](https://github.com/mohsinnasir)및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="05429-104">By [Luke Latham](https://github.com/guardrex), [Mohsin Nasir](https://github.com/mohsinnasir), and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="05429-105">분산 캐시는 여러 앱 서버에서 공유 하는 캐시로, 일반적으로 액세스 하는 앱 서버에 외부 서비스로 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="05429-106">분산 캐시는 특히 앱이 클라우드 서비스 또는 서버 팜에서 호스트 되는 경우 ASP.NET Core 앱의 성능과 확장성을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="05429-107">분산 캐시는 개별 앱 서버에 캐시 된 데이터가 저장 되는 다른 캐싱 시나리오에 비해 여러 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="05429-108">캐시 된 데이터를 배포 하는 경우 데이터는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="05429-109">는 여러 서버에 대 한 여러 요청 *에서 일관 되* 고 일관 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="05429-110">서버를 다시 시작 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="05429-111">로컬 메모리를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-111">Doesn't use local memory.</span></span>

<span data-ttu-id="05429-112">분산 캐시 구성은 구현 별로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="05429-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="05429-113">이 문서에서는 SQL Server 및 Redis 분산 캐시를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="05429-114">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))와 같은 타사 구현도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="05429-115">어떤 구현이 선택 되는지에 관계 없이 앱은 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스를 사용 하 여 캐시와 상호 작용 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="05429-116">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="05429-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="05429-117">필수 조건</span><span class="sxs-lookup"><span data-stu-id="05429-117">Prerequisites</span></span>

<span data-ttu-id="05429-118">SQL Server 분산 캐시를 사용 하려면 패키지 참조를 [Microsoft 확장명이](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) . i n t. i n t. i n t 패키지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="05429-119">Redis 분산 캐시를 사용 하려면 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 패키지에 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="05429-120">NCache 분산 캐시를 사용 하려면 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 패키지에 대 한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="05429-121">IDistributedCache 인터페이스</span><span class="sxs-lookup"><span data-stu-id="05429-121">IDistributedCache interface</span></span>

<span data-ttu-id="05429-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스는 분산 캐시 구현에서 항목을 조작 하는 다음 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="05429-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash;는 문자열 키를 허용 하 고 캐시 된 항목을 캐시에 있는 경우 `byte[]` 배열로 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="05429-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash;는 문자열 키를 사용 하 여 캐시에 항목 (`byte[]` 배열로)을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="05429-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>&ndash; <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>는 키를 기준으로 캐시의 항목을 새로 고쳐 슬라이딩 만료 시간 제한 (있는 경우)을 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="05429-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash;는 해당 문자열 키에 따라 캐시 항목을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="05429-127">분산 캐싱 서비스 설정</span><span class="sxs-lookup"><span data-stu-id="05429-127">Establish distributed caching services</span></span>

<span data-ttu-id="05429-128">`Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 구현을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="05429-129">이 항목에서 설명 하는 프레임 워크 제공 구현에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="05429-130">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="05429-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="05429-131">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="05429-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="05429-132">Distributed Redis cache</span><span class="sxs-lookup"><span data-stu-id="05429-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="05429-133">Distributed NCache cache</span><span class="sxs-lookup"><span data-stu-id="05429-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="05429-134">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="05429-134">Distributed Memory Cache</span></span>

<span data-ttu-id="05429-135"><xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>(Distributed Memory Cache)는 메모리에 항목을 저장 하는 프레임 워크에서 제공 하는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>의 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="05429-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="05429-136">분산 메모리 캐시는 실제 분산 된 캐시가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="05429-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="05429-137">캐시 된 항목은 앱이 실행 되 고 있는 서버의 앱 인스턴스에 의해 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="05429-138">분산 메모리 캐시는 다음과 같은 유용한 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="05429-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="05429-139">개발 및 테스트 시나리오에서</span><span class="sxs-lookup"><span data-stu-id="05429-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="05429-140">프로덕션 환경에서 단일 서버를 사용 하는 경우 메모리 소비가 문제가 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="05429-141">분산 메모리 캐시를 구현 하면 캐시 된 데이터 저장소를 추상화 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="05429-142">이를 통해 여러 노드 또는 내결함성이 필요한 경우 나중에 진정한 분산 캐싱 솔루션을 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="05429-143">샘플 앱은 `Startup.ConfigureServices`의 개발 환경에서 응용 프로그램을 실행할 때 분산 메모리 캐시를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="05429-144">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="05429-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="05429-145">분산 캐시<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>(SQL Server 캐시 구현)를 사용 하면 분산 캐시에서 SQL Server 데이터베이스를 백업 저장소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="05429-146">SQL Server 인스턴스에 SQL Server 캐시 된 항목 테이블을 만들려면 `sql-cache` 도구를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="05429-147">이 도구는 사용자가 지정한 이름 및 스키마를 사용 하 여 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="05429-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="05429-148">`sql-cache create` 명령을 실행 하 여 SQL Server에서 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="05429-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="05429-149">SQL Server 인스턴스 (`Data Source`), 데이터베이스 (`Initial Catalog`), 스키마 (예: `dbo`) 및 테이블 이름 (예: `TestCache`)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="05429-150">도구가 성공 했음을 나타내는 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="05429-151">`sql-cache` 도구에서 만든 테이블에는 다음 스키마가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="05429-153">앱은 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>가 아닌 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>의 인스턴스를 사용 하 여 캐시 값을 조작 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="05429-154">샘플 앱은 `Startup.ConfigureServices`의 개발 이외의 환경에서 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="05429-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>)는 일반적으로 소스 제어 외부에 저장 됩니다 (예: [암호 관리자](xref:security/app-secrets) 또는 *appsettings*/appsettings에 의해 저장 됨) *. 환경}. json* 파일).</span><span class="sxs-lookup"><span data-stu-id="05429-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="05429-156">연결 문자열에는 원본 제어 시스템에서 유지 되어야 하는 자격 증명이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="05429-157">분산 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="05429-157">Distributed Redis Cache</span></span>

<span data-ttu-id="05429-158">[Redis](https://redis.io/) 는 분산 캐시로 자주 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="05429-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="05429-159">Redis를 로컬로 사용할 수 있으며, Azure에서 호스트 되는 ASP.NET Core 앱에 대 한 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) 를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-159">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="05429-160">앱은 `Startup.ConfigureServices`의 개발 이외의 환경에서 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 인스턴스 (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>)를 사용 하 여 캐시 구현을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="05429-161">로컬 컴퓨터에 Redis를 설치 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-161">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="05429-162">[Chocolatey Redis 패키지](https://chocolatey.org/packages/redis-64/)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-162">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="05429-163">명령 프롬프트에서 `redis-server`를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-163">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="05429-164">Distributed NCache Cache</span><span class="sxs-lookup"><span data-stu-id="05429-164">Distributed NCache Cache</span></span>

<span data-ttu-id="05429-165">[NCache](https://github.com/Alachisoft/NCache) 는 .NET 및 .net Core에서 기본적으로 개발 된 오픈 소스 메모리 내 분산 캐시입니다.</span><span class="sxs-lookup"><span data-stu-id="05429-165">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="05429-166">NCache는 로컬에서 작동 하 고 Azure 또는 다른 호스팅 플랫폼에서 실행 되는 ASP.NET Core 앱에 대 한 분산 캐시 클러스터로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-166">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="05429-167">로컬 컴퓨터에 NCache를 설치 및 구성 하려면 [NCache 시작 가이드 For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="05429-167">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="05429-168">NCache를 구성 하려면:</span><span class="sxs-lookup"><span data-stu-id="05429-168">To configure NCache:</span></span>

1. <span data-ttu-id="05429-169">[NCache 오픈 소스 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-169">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="05429-170">[Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)에서 캐시 클러스터를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-170">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="05429-171">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-171">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="05429-172">분산 캐시 사용</span><span class="sxs-lookup"><span data-stu-id="05429-172">Use the distributed cache</span></span>

<span data-ttu-id="05429-173"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스를 사용 하려면 앱의 모든 생성자에서 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>의 인스턴스를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-173">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="05429-174">인스턴스는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-174">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="05429-175">샘플 앱이 시작 되 면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.Configure`에 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="05429-176">현재 시간은 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>를 사용 하 여 캐시 됩니다. 자세한 내용은 [제네릭 호스트: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="05429-176">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="05429-177">샘플 앱은 인덱스 페이지에서 사용할 `IndexModel` <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>을 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="05429-178">인덱스 페이지가 로드 될 때마다 캐시가 캐시 된 시간 `OnGetAsync`에 대해 검사 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="05429-179">캐시 된 시간이 만료 되지 않은 경우에는 시간이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="05429-180">캐시 된 시간에 마지막으로 액세스 한 후 20 초가 경과한 경우 (이 페이지가 마지막으로 로드 된 시간) 페이지에는 *캐시 된 시간 만료*가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="05429-181">캐시 된 시간 **다시 설정** 단추를 선택 하 여 캐시 된 시간을 현재 시간으로 즉시 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="05429-182">단추는 `OnPostResetCachedTime` 처리기 메서드를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="05429-183"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인스턴스에 대해 Singleton 또는 범위가 지정 된 수명을 사용할 필요는 없습니다 (최소한 기본 제공 구현의 경우).</span><span class="sxs-lookup"><span data-stu-id="05429-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="05429-184">DI를 사용 하는 대신 필요한 경우에도 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인스턴스를 만들 수 있지만, 코드에서 인스턴스를 만들면 코드를 테스트 하는 것이 어렵고 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 위반 하 게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="05429-185">권장 사항</span><span class="sxs-lookup"><span data-stu-id="05429-185">Recommendations</span></span>

<span data-ttu-id="05429-186">앱에 가장 적합 한 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 구현을 결정 하는 경우 다음 사항을 고려 하세요.</span><span class="sxs-lookup"><span data-stu-id="05429-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="05429-187">기존 인프라</span><span class="sxs-lookup"><span data-stu-id="05429-187">Existing infrastructure</span></span>
* <span data-ttu-id="05429-188">성능 요구 사항</span><span class="sxs-lookup"><span data-stu-id="05429-188">Performance requirements</span></span>
* <span data-ttu-id="05429-189">비용</span><span class="sxs-lookup"><span data-stu-id="05429-189">Cost</span></span>
* <span data-ttu-id="05429-190">팀 환경</span><span class="sxs-lookup"><span data-stu-id="05429-190">Team experience</span></span>

<span data-ttu-id="05429-191">캐싱 솔루션은 일반적으로 메모리 내 저장소를 사용 하 여 캐시 된 데이터를 신속 하 게 검색 하지만 메모리는 제한 된 리소스 이며 확장 하는 데 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="05429-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="05429-192">일반적으로 사용 되는 데이터를 캐시에만 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="05429-193">일반적으로 Redis cache는 SQL Server 캐시 보다 높은 처리량과 짧은 대기 시간을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="05429-194">그러나 일반적으로 벤치마킹은 캐싱 전략의 성능 특성을 결정 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="05429-195">SQL Server를 분산 캐시 백업 저장소로 사용 하는 경우 캐시에 대해 동일한 데이터베이스를 사용 하 고 앱의 일반 데이터 저장 및 검색을 사용 하면 두 성능에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="05429-196">분산 캐시 백업 저장소에 전용 SQL Server 인스턴스를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="05429-197">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="05429-197">Additional resources</span></span>

* [<span data-ttu-id="05429-198">Azure의 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="05429-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="05429-199">Azure의 SQL Database</span><span class="sxs-lookup"><span data-stu-id="05429-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="05429-200">[웹 팜의 ASP.NET Core IDistributedCache Provider For NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="05429-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="05429-201">분산 캐시는 여러 앱 서버에서 공유 하는 캐시로, 일반적으로 액세스 하는 앱 서버에 외부 서비스로 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-201">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="05429-202">분산 캐시는 특히 앱이 클라우드 서비스 또는 서버 팜에서 호스트 되는 경우 ASP.NET Core 앱의 성능과 확장성을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-202">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="05429-203">분산 캐시는 개별 앱 서버에 캐시 된 데이터가 저장 되는 다른 캐싱 시나리오에 비해 여러 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-203">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="05429-204">캐시 된 데이터를 배포 하는 경우 데이터는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-204">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="05429-205">는 여러 서버에 대 한 여러 요청 *에서 일관 되* 고 일관 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-205">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="05429-206">서버를 다시 시작 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-206">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="05429-207">로컬 메모리를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-207">Doesn't use local memory.</span></span>

<span data-ttu-id="05429-208">분산 캐시 구성은 구현 별로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="05429-208">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="05429-209">이 문서에서는 SQL Server 및 Redis 분산 캐시를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-209">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="05429-210">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))와 같은 타사 구현도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-210">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="05429-211">어떤 구현이 선택 되는지에 관계 없이 앱은 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스를 사용 하 여 캐시와 상호 작용 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-211">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="05429-212">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="05429-212">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="05429-213">필수 조건</span><span class="sxs-lookup"><span data-stu-id="05429-213">Prerequisites</span></span>

<span data-ttu-id="05429-214">SQL Server 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하거나 패키지 참조를 [패키지에 추가 합니다.](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="05429-214">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="05429-215">Redis 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 패키지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-215">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="05429-216">Redis 패키지는 `Microsoft.AspNetCore.App` 패키지에 포함 되어 있지 않으므로 프로젝트 파일에서 Redis 패키지를 개별적으로 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-216">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="05429-217">NCache 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-217">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="05429-218">NCache 패키지는 `Microsoft.AspNetCore.App` 패키지에 포함 되어 있지 않으므로 프로젝트 파일에서 NCache 패키지를 개별적으로 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-218">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="05429-219">IDistributedCache 인터페이스</span><span class="sxs-lookup"><span data-stu-id="05429-219">IDistributedCache interface</span></span>

<span data-ttu-id="05429-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스는 분산 캐시 구현에서 항목을 조작 하는 다음 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-220">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="05429-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash;는 문자열 키를 허용 하 고 캐시 된 항목을 캐시에 있는 경우 `byte[]` 배열로 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="05429-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash;는 문자열 키를 사용 하 여 캐시에 항목 (`byte[]` 배열로)을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="05429-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>&ndash; <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>는 키를 기준으로 캐시의 항목을 새로 고쳐 슬라이딩 만료 시간 제한 (있는 경우)을 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="05429-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash;는 해당 문자열 키에 따라 캐시 항목을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="05429-225">분산 캐싱 서비스 설정</span><span class="sxs-lookup"><span data-stu-id="05429-225">Establish distributed caching services</span></span>

<span data-ttu-id="05429-226">`Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 구현을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-226">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="05429-227">이 항목에서 설명 하는 프레임 워크 제공 구현에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-227">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="05429-228">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="05429-228">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="05429-229">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="05429-229">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="05429-230">Distributed Redis cache</span><span class="sxs-lookup"><span data-stu-id="05429-230">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="05429-231">Distributed NCache cache</span><span class="sxs-lookup"><span data-stu-id="05429-231">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="05429-232">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="05429-232">Distributed Memory Cache</span></span>

<span data-ttu-id="05429-233"><xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>(Distributed Memory Cache)는 메모리에 항목을 저장 하는 프레임 워크에서 제공 하는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>의 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="05429-233">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="05429-234">분산 메모리 캐시는 실제 분산 된 캐시가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="05429-234">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="05429-235">캐시 된 항목은 앱이 실행 되 고 있는 서버의 앱 인스턴스에 의해 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-235">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="05429-236">분산 메모리 캐시는 다음과 같은 유용한 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="05429-236">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="05429-237">개발 및 테스트 시나리오에서</span><span class="sxs-lookup"><span data-stu-id="05429-237">In development and testing scenarios.</span></span>
* <span data-ttu-id="05429-238">프로덕션 환경에서 단일 서버를 사용 하는 경우 메모리 소비가 문제가 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-238">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="05429-239">분산 메모리 캐시를 구현 하면 캐시 된 데이터 저장소를 추상화 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-239">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="05429-240">이를 통해 여러 노드 또는 내결함성이 필요한 경우 나중에 진정한 분산 캐싱 솔루션을 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-240">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="05429-241">샘플 앱은 `Startup.ConfigureServices`의 개발 환경에서 응용 프로그램을 실행할 때 분산 메모리 캐시를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-241">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="05429-242">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="05429-242">Distributed SQL Server Cache</span></span>

<span data-ttu-id="05429-243">분산 캐시<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>(SQL Server 캐시 구현)를 사용 하면 분산 캐시에서 SQL Server 데이터베이스를 백업 저장소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-243">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="05429-244">SQL Server 인스턴스에 SQL Server 캐시 된 항목 테이블을 만들려면 `sql-cache` 도구를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-244">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="05429-245">이 도구는 사용자가 지정한 이름 및 스키마를 사용 하 여 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="05429-245">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="05429-246">`sql-cache create` 명령을 실행 하 여 SQL Server에서 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="05429-246">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="05429-247">SQL Server 인스턴스 (`Data Source`), 데이터베이스 (`Initial Catalog`), 스키마 (예: `dbo`) 및 테이블 이름 (예: `TestCache`)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-247">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="05429-248">도구가 성공 했음을 나타내는 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-248">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="05429-249">`sql-cache` 도구에서 만든 테이블에는 다음 스키마가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-249">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="05429-251">앱은 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>가 아닌 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>의 인스턴스를 사용 하 여 캐시 값을 조작 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-251">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="05429-252">샘플 앱은 `Startup.ConfigureServices`의 개발 이외의 환경에서 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-252">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="05429-253"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>)는 일반적으로 소스 제어 외부에 저장 됩니다 (예: [암호 관리자](xref:security/app-secrets) 또는 *appsettings*/appsettings에 의해 저장 됨) *. 환경}. json* 파일).</span><span class="sxs-lookup"><span data-stu-id="05429-253">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="05429-254">연결 문자열에는 원본 제어 시스템에서 유지 되어야 하는 자격 증명이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-254">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="05429-255">분산 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="05429-255">Distributed Redis Cache</span></span>

<span data-ttu-id="05429-256">[Redis](https://redis.io/) 는 분산 캐시로 자주 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="05429-256">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="05429-257">Redis를 로컬로 사용할 수 있으며, Azure에서 호스트 되는 ASP.NET Core 앱에 대 한 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) 를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-257">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="05429-258">앱은 `Startup.ConfigureServices`의 개발 이외의 환경에서 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 인스턴스 (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>)를 사용 하 여 캐시 구현을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-258">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="05429-259">로컬 컴퓨터에 Redis를 설치 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-259">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="05429-260">[Chocolatey Redis 패키지](https://chocolatey.org/packages/redis-64/)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-260">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="05429-261">명령 프롬프트에서 `redis-server`를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-261">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="05429-262">Distributed NCache Cache</span><span class="sxs-lookup"><span data-stu-id="05429-262">Distributed NCache Cache</span></span>

<span data-ttu-id="05429-263">[NCache](https://github.com/Alachisoft/NCache) 는 .NET 및 .net Core에서 기본적으로 개발 된 오픈 소스 메모리 내 분산 캐시입니다.</span><span class="sxs-lookup"><span data-stu-id="05429-263">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="05429-264">NCache는 로컬에서 작동 하 고 Azure 또는 다른 호스팅 플랫폼에서 실행 되는 ASP.NET Core 앱에 대 한 분산 캐시 클러스터로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-264">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="05429-265">로컬 컴퓨터에 NCache를 설치 및 구성 하려면 [NCache 시작 가이드 For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="05429-265">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="05429-266">NCache를 구성 하려면:</span><span class="sxs-lookup"><span data-stu-id="05429-266">To configure NCache:</span></span>

1. <span data-ttu-id="05429-267">[NCache 오픈 소스 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-267">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="05429-268">[Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)에서 캐시 클러스터를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-268">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="05429-269">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-269">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="05429-270">분산 캐시 사용</span><span class="sxs-lookup"><span data-stu-id="05429-270">Use the distributed cache</span></span>

<span data-ttu-id="05429-271"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스를 사용 하려면 앱의 모든 생성자에서 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>의 인스턴스를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-271">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="05429-272">인스턴스는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-272">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="05429-273">샘플 앱이 시작 되 면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.Configure`에 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-273">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="05429-274">현재 시간은 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime>를 사용 하 여 캐시 됩니다. 자세한 내용은 [웹 호스트: IApplicationLifetime 인터페이스](xref:fundamentals/host/web-host#iapplicationlifetime-interface)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="05429-274">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="05429-275">샘플 앱은 인덱스 페이지에서 사용할 `IndexModel` <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>을 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-275">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="05429-276">인덱스 페이지가 로드 될 때마다 캐시가 캐시 된 시간 `OnGetAsync`에 대해 검사 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-276">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="05429-277">캐시 된 시간이 만료 되지 않은 경우에는 시간이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-277">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="05429-278">캐시 된 시간에 마지막으로 액세스 한 후 20 초가 경과한 경우 (이 페이지가 마지막으로 로드 된 시간) 페이지에는 *캐시 된 시간 만료*가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-278">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="05429-279">캐시 된 시간 **다시 설정** 단추를 선택 하 여 캐시 된 시간을 현재 시간으로 즉시 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-279">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="05429-280">단추는 `OnPostResetCachedTime` 처리기 메서드를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-280">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="05429-281"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인스턴스에 대해 Singleton 또는 범위가 지정 된 수명을 사용할 필요는 없습니다 (최소한 기본 제공 구현의 경우).</span><span class="sxs-lookup"><span data-stu-id="05429-281">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="05429-282">DI를 사용 하는 대신 필요한 경우에도 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인스턴스를 만들 수 있지만, 코드에서 인스턴스를 만들면 코드를 테스트 하는 것이 어렵고 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 위반 하 게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-282">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="05429-283">권장 사항</span><span class="sxs-lookup"><span data-stu-id="05429-283">Recommendations</span></span>

<span data-ttu-id="05429-284">앱에 가장 적합 한 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 구현을 결정 하는 경우 다음 사항을 고려 하세요.</span><span class="sxs-lookup"><span data-stu-id="05429-284">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="05429-285">기존 인프라</span><span class="sxs-lookup"><span data-stu-id="05429-285">Existing infrastructure</span></span>
* <span data-ttu-id="05429-286">성능 요구 사항</span><span class="sxs-lookup"><span data-stu-id="05429-286">Performance requirements</span></span>
* <span data-ttu-id="05429-287">비용</span><span class="sxs-lookup"><span data-stu-id="05429-287">Cost</span></span>
* <span data-ttu-id="05429-288">팀 환경</span><span class="sxs-lookup"><span data-stu-id="05429-288">Team experience</span></span>

<span data-ttu-id="05429-289">캐싱 솔루션은 일반적으로 메모리 내 저장소를 사용 하 여 캐시 된 데이터를 신속 하 게 검색 하지만 메모리는 제한 된 리소스 이며 확장 하는 데 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="05429-289">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="05429-290">일반적으로 사용 되는 데이터를 캐시에만 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-290">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="05429-291">일반적으로 Redis cache는 SQL Server 캐시 보다 높은 처리량과 짧은 대기 시간을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-291">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="05429-292">그러나 일반적으로 벤치마킹은 캐싱 전략의 성능 특성을 결정 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-292">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="05429-293">SQL Server를 분산 캐시 백업 저장소로 사용 하는 경우 캐시에 대해 동일한 데이터베이스를 사용 하 고 앱의 일반 데이터 저장 및 검색을 사용 하면 두 성능에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-293">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="05429-294">분산 캐시 백업 저장소에 전용 SQL Server 인스턴스를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-294">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="05429-295">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="05429-295">Additional resources</span></span>

* [<span data-ttu-id="05429-296">Azure의 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="05429-296">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="05429-297">Azure의 SQL Database</span><span class="sxs-lookup"><span data-stu-id="05429-297">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="05429-298">[웹 팜의 ASP.NET Core IDistributedCache Provider For NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="05429-298">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="05429-299">분산 캐시는 여러 앱 서버에서 공유 하는 캐시로, 일반적으로 액세스 하는 앱 서버에 외부 서비스로 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-299">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="05429-300">분산 캐시는 특히 앱이 클라우드 서비스 또는 서버 팜에서 호스트 되는 경우 ASP.NET Core 앱의 성능과 확장성을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-300">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="05429-301">분산 캐시는 개별 앱 서버에 캐시 된 데이터가 저장 되는 다른 캐싱 시나리오에 비해 여러 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-301">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="05429-302">캐시 된 데이터를 배포 하는 경우 데이터는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-302">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="05429-303">는 여러 서버에 대 한 여러 요청 *에서 일관 되* 고 일관 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-303">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="05429-304">서버를 다시 시작 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-304">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="05429-305">로컬 메모리를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-305">Doesn't use local memory.</span></span>

<span data-ttu-id="05429-306">분산 캐시 구성은 구현 별로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="05429-306">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="05429-307">이 문서에서는 SQL Server 및 Redis 분산 캐시를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-307">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="05429-308">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))와 같은 타사 구현도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-308">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="05429-309">어떤 구현이 선택 되는지에 관계 없이 앱은 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스를 사용 하 여 캐시와 상호 작용 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-309">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="05429-310">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="05429-310">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="05429-311">필수 조건</span><span class="sxs-lookup"><span data-stu-id="05429-311">Prerequisites</span></span>

<span data-ttu-id="05429-312">SQL Server 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하거나 패키지 참조를 [패키지에 추가 합니다.](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="05429-312">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="05429-313">Redis 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) 패키지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-313">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="05429-314">Redis 패키지는 `Microsoft.AspNetCore.App` 패키지에 포함 되어 있지 않으므로 프로젝트 파일에서 Redis 패키지를 개별적으로 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-314">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="05429-315">NCache 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-315">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="05429-316">NCache 패키지는 `Microsoft.AspNetCore.App` 패키지에 포함 되어 있지 않으므로 프로젝트 파일에서 NCache 패키지를 개별적으로 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-316">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="05429-317">IDistributedCache 인터페이스</span><span class="sxs-lookup"><span data-stu-id="05429-317">IDistributedCache interface</span></span>

<span data-ttu-id="05429-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스는 분산 캐시 구현에서 항목을 조작 하는 다음 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-318">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="05429-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash;는 문자열 키를 허용 하 고 캐시 된 항목을 캐시에 있는 경우 `byte[]` 배열로 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="05429-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash;는 문자열 키를 사용 하 여 캐시에 항목 (`byte[]` 배열로)을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="05429-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>&ndash; <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>는 키를 기준으로 캐시의 항목을 새로 고쳐 슬라이딩 만료 시간 제한 (있는 경우)을 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="05429-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash;는 해당 문자열 키에 따라 캐시 항목을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="05429-323">분산 캐싱 서비스 설정</span><span class="sxs-lookup"><span data-stu-id="05429-323">Establish distributed caching services</span></span>

<span data-ttu-id="05429-324">`Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 구현을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-324">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="05429-325">이 항목에서 설명 하는 프레임 워크 제공 구현에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-325">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="05429-326">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="05429-326">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="05429-327">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="05429-327">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="05429-328">Distributed Redis cache</span><span class="sxs-lookup"><span data-stu-id="05429-328">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="05429-329">Distributed NCache cache</span><span class="sxs-lookup"><span data-stu-id="05429-329">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="05429-330">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="05429-330">Distributed Memory Cache</span></span>

<span data-ttu-id="05429-331"><xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>(Distributed Memory Cache)는 메모리에 항목을 저장 하는 프레임 워크에서 제공 하는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>의 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="05429-331">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="05429-332">분산 메모리 캐시는 실제 분산 된 캐시가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="05429-332">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="05429-333">캐시 된 항목은 앱이 실행 되 고 있는 서버의 앱 인스턴스에 의해 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-333">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="05429-334">분산 메모리 캐시는 다음과 같은 유용한 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="05429-334">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="05429-335">개발 및 테스트 시나리오에서</span><span class="sxs-lookup"><span data-stu-id="05429-335">In development and testing scenarios.</span></span>
* <span data-ttu-id="05429-336">프로덕션 환경에서 단일 서버를 사용 하는 경우 메모리 소비가 문제가 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-336">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="05429-337">분산 메모리 캐시를 구현 하면 캐시 된 데이터 저장소를 추상화 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-337">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="05429-338">이를 통해 여러 노드 또는 내결함성이 필요한 경우 나중에 진정한 분산 캐싱 솔루션을 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-338">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="05429-339">샘플 앱은 `Startup.ConfigureServices`의 개발 환경에서 응용 프로그램을 실행할 때 분산 메모리 캐시를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-339">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="05429-340">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="05429-340">Distributed SQL Server Cache</span></span>

<span data-ttu-id="05429-341">분산 캐시<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>(SQL Server 캐시 구현)를 사용 하면 분산 캐시에서 SQL Server 데이터베이스를 백업 저장소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-341">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="05429-342">SQL Server 인스턴스에 SQL Server 캐시 된 항목 테이블을 만들려면 `sql-cache` 도구를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-342">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="05429-343">이 도구는 사용자가 지정한 이름 및 스키마를 사용 하 여 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="05429-343">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="05429-344">`sql-cache create` 명령을 실행 하 여 SQL Server에서 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="05429-344">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="05429-345">SQL Server 인스턴스 (`Data Source`), 데이터베이스 (`Initial Catalog`), 스키마 (예: `dbo`) 및 테이블 이름 (예: `TestCache`)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-345">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="05429-346">도구가 성공 했음을 나타내는 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-346">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="05429-347">`sql-cache` 도구에서 만든 테이블에는 다음 스키마가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-347">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="05429-349">앱은 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>가 아닌 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>의 인스턴스를 사용 하 여 캐시 값을 조작 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-349">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="05429-350">샘플 앱은 `Startup.ConfigureServices`의 개발 이외의 환경에서 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-350">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="05429-351"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>)는 일반적으로 소스 제어 외부에 저장 됩니다 (예: [암호 관리자](xref:security/app-secrets) 또는 *appsettings*/appsettings에 의해 저장 됨) *. 환경}. json* 파일).</span><span class="sxs-lookup"><span data-stu-id="05429-351">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="05429-352">연결 문자열에는 원본 제어 시스템에서 유지 되어야 하는 자격 증명이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-352">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="05429-353">분산 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="05429-353">Distributed Redis Cache</span></span>

<span data-ttu-id="05429-354">[Redis](https://redis.io/) 는 분산 캐시로 자주 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="05429-354">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="05429-355">Redis를 로컬로 사용할 수 있으며, Azure에서 호스트 되는 ASP.NET Core 앱에 대 한 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) 를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-355">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="05429-356">앱은 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> 인스턴스 (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>)를 사용 하 여 캐시 구현을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-356">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="05429-357">로컬 컴퓨터에 Redis를 설치 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-357">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="05429-358">[Chocolatey Redis 패키지](https://chocolatey.org/packages/redis-64/)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-358">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="05429-359">명령 프롬프트에서 `redis-server`를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-359">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="05429-360">Distributed NCache Cache</span><span class="sxs-lookup"><span data-stu-id="05429-360">Distributed NCache Cache</span></span>

<span data-ttu-id="05429-361">[NCache](https://github.com/Alachisoft/NCache) 는 .NET 및 .net Core에서 기본적으로 개발 된 오픈 소스 메모리 내 분산 캐시입니다.</span><span class="sxs-lookup"><span data-stu-id="05429-361">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="05429-362">NCache는 로컬에서 작동 하 고 Azure 또는 다른 호스팅 플랫폼에서 실행 되는 ASP.NET Core 앱에 대 한 분산 캐시 클러스터로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-362">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="05429-363">로컬 컴퓨터에 NCache를 설치 및 구성 하려면 [NCache 시작 가이드 For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="05429-363">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="05429-364">NCache를 구성 하려면:</span><span class="sxs-lookup"><span data-stu-id="05429-364">To configure NCache:</span></span>

1. <span data-ttu-id="05429-365">[NCache 오픈 소스 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-365">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="05429-366">[Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)에서 캐시 클러스터를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-366">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="05429-367">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-367">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="05429-368">분산 캐시 사용</span><span class="sxs-lookup"><span data-stu-id="05429-368">Use the distributed cache</span></span>

<span data-ttu-id="05429-369"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스를 사용 하려면 앱의 모든 생성자에서 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>의 인스턴스를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-369">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="05429-370">인스턴스는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-370">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="05429-371">샘플 앱이 시작 되 면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.Configure`에 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-371">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="05429-372">현재 시간은 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime>를 사용 하 여 캐시 됩니다. 자세한 내용은 [웹 호스트: IApplicationLifetime 인터페이스](xref:fundamentals/host/web-host#iapplicationlifetime-interface)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="05429-372">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="05429-373">샘플 앱은 인덱스 페이지에서 사용할 `IndexModel` <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>을 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-373">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="05429-374">인덱스 페이지가 로드 될 때마다 캐시가 캐시 된 시간 `OnGetAsync`에 대해 검사 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-374">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="05429-375">캐시 된 시간이 만료 되지 않은 경우에는 시간이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-375">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="05429-376">캐시 된 시간에 마지막으로 액세스 한 후 20 초가 경과한 경우 (이 페이지가 마지막으로 로드 된 시간) 페이지에는 *캐시 된 시간 만료*가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="05429-376">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="05429-377">캐시 된 시간 **다시 설정** 단추를 선택 하 여 캐시 된 시간을 현재 시간으로 즉시 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-377">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="05429-378">단추는 `OnPostResetCachedTime` 처리기 메서드를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-378">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="05429-379"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인스턴스에 대해 Singleton 또는 범위가 지정 된 수명을 사용할 필요는 없습니다 (최소한 기본 제공 구현의 경우).</span><span class="sxs-lookup"><span data-stu-id="05429-379">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="05429-380">DI를 사용 하는 대신 필요한 경우에도 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인스턴스를 만들 수 있지만, 코드에서 인스턴스를 만들면 코드를 테스트 하는 것이 어렵고 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 위반 하 게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-380">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="05429-381">권장 사항</span><span class="sxs-lookup"><span data-stu-id="05429-381">Recommendations</span></span>

<span data-ttu-id="05429-382">앱에 가장 적합 한 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 구현을 결정 하는 경우 다음 사항을 고려 하세요.</span><span class="sxs-lookup"><span data-stu-id="05429-382">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="05429-383">기존 인프라</span><span class="sxs-lookup"><span data-stu-id="05429-383">Existing infrastructure</span></span>
* <span data-ttu-id="05429-384">성능 요구 사항</span><span class="sxs-lookup"><span data-stu-id="05429-384">Performance requirements</span></span>
* <span data-ttu-id="05429-385">비용</span><span class="sxs-lookup"><span data-stu-id="05429-385">Cost</span></span>
* <span data-ttu-id="05429-386">팀 환경</span><span class="sxs-lookup"><span data-stu-id="05429-386">Team experience</span></span>

<span data-ttu-id="05429-387">캐싱 솔루션은 일반적으로 메모리 내 저장소를 사용 하 여 캐시 된 데이터를 신속 하 게 검색 하지만 메모리는 제한 된 리소스 이며 확장 하는 데 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="05429-387">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="05429-388">일반적으로 사용 되는 데이터를 캐시에만 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-388">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="05429-389">일반적으로 Redis cache는 SQL Server 캐시 보다 높은 처리량과 짧은 대기 시간을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-389">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="05429-390">그러나 일반적으로 벤치마킹은 캐싱 전략의 성능 특성을 결정 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="05429-390">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="05429-391">SQL Server를 분산 캐시 백업 저장소로 사용 하는 경우 캐시에 대해 동일한 데이터베이스를 사용 하 고 앱의 일반 데이터 저장 및 검색을 사용 하면 두 성능에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-391">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="05429-392">분산 캐시 백업 저장소에 전용 SQL Server 인스턴스를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="05429-392">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="05429-393">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="05429-393">Additional resources</span></span>

* [<span data-ttu-id="05429-394">Azure의 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="05429-394">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="05429-395">Azure의 SQL Database</span><span class="sxs-lookup"><span data-stu-id="05429-395">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="05429-396">[웹 팜의 ASP.NET Core IDistributedCache Provider For NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="05429-396">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
