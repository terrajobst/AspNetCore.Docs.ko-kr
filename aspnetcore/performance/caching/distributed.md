---
title: ASP.NET Core의 캐싱 분산
author: guardrex
description: 앱 성능 및 확장성, 클라우드 또는 서버 팜 환경에서 특히 개선 하기 위해 캐시를 분산 하는 ASP.NET Core를 사용 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: performance/caching/distributed
ms.openlocfilehash: 871e27c068de3a465cedb517418a35a87f992300
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085830"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="37209-103">ASP.NET Core의 캐싱 분산</span><span class="sxs-lookup"><span data-stu-id="37209-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="37209-104">하 여 [Luke Latham](https://github.com/guardrex) 고 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="37209-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="37209-105">분산된 캐시에 액세스 하는 응용 프로그램 서버 외부 서비스로 일반적으로 유지 관리 하는 여러 앱 서버에서 공유 캐시가입니다.</span><span class="sxs-lookup"><span data-stu-id="37209-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="37209-106">앱은 클라우드 서비스 또는 서버 팜을 호스팅하는 경우에 특히 분산된 캐시는 성능 및 ASP.NET Core 앱의 확장성을 개선할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37209-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="37209-107">분산된 캐시에는 개별 앱 서버에서 캐시 된 데이터가 저장 된 다른 캐싱 시나리오를 통해 몇 가지 장점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37209-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="37209-108">캐시 된 데이터를 분산 하는 경우, 데이터:</span><span class="sxs-lookup"><span data-stu-id="37209-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="37209-109">됩니다 *일관 된* (일치)에서 여러 서버에 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="37209-110">서버 다시 시작 되 고 앱 배포도 계속 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37209-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="37209-111">로컬 메모리를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37209-111">Doesn't use local memory.</span></span>

<span data-ttu-id="37209-112">분산된 캐시 구성은 특정 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="37209-113">이 문서에서는 SQL Server를 구성 하 고 Redis cache를 배포 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="37209-114">제 3 자 구현도 같은 사용할 수 있습니다 [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([github NCache](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="37209-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="37209-115">어떤 구현의 선택 하는 것에 관계 없이 앱을 사용 하 여 캐시 상호 작용을 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="37209-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="37209-116">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37209-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="37209-117">전제 조건</span><span class="sxs-lookup"><span data-stu-id="37209-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="37209-118">SQL Server를 사용 하 여 분산 캐시에 대 한 참조를 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) 에 대 한 패키지 참조를 추가 또는 합니다 [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-118">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="37209-119">Redis를 사용 하 여 분산 캐시에 대 한 참조를 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) 에 대 한 패키지 참조를 추가 하 고는 [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-119">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="37209-120">Redis 패키지에 포함 되어 있지는 `Microsoft.AspNetCore.App` 패키지를 프로젝트 파일의 Redis 패키지를 개별적으로 참조 해야 하므로 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-120">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="37209-121">SQL Server를 사용 하 여 분산 캐시에 대 한 참조를 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) 에 대 한 패키지 참조를 추가 또는 합니다 [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-121">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="37209-122">Redis를 사용 하 여 분산 캐시에 대 한 참조를 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) 에 대 한 패키지 참조를 추가 하 고는 [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-122">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="37209-123">Redis 패키지에 포함 되어 있지는 `Microsoft.AspNetCore.App` 패키지를 프로젝트 파일의 Redis 패키지를 개별적으로 참조 해야 하므로 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-123">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="37209-124">IDistributedCache 인터페이스</span><span class="sxs-lookup"><span data-stu-id="37209-124">IDistributedCache interface</span></span>

<span data-ttu-id="37209-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스 분산된 캐시 구현에서 항목을 조작 하려면 다음 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-125">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="37209-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>를 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; 문자열 키를 받아서으로 캐시 된 항목을 검색 합니다는 `byte[]` 경우 캐시에서 발견 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="37209-127"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>를 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 항목을 추가 (으로 `byte[]` 배열) 문자열 키를 사용 하 여 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-127"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="37209-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>하십시오 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; (있는 경우) 해당 상대 (sliding) 만료 시간 제한 다시 설정 하 고 키를 기반으로 캐시에서 항목을 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="37209-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="37209-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>하십시오 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 해당 문자열 키를 기반으로 캐시 항목을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="37209-130">분산된 캐싱 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-130">Establish distributed caching services</span></span>

<span data-ttu-id="37209-131">등록의 구현을 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 에서 `Startup.ConfigureServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-131">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="37209-132">이 항목에서 설명 하는 프레임 워크에서 제공한 구현은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="37209-132">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="37209-133">분산된 메모리 내 캐시</span><span class="sxs-lookup"><span data-stu-id="37209-133">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="37209-134">분산 된 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="37209-134">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="37209-135">분산 된 Redis 캐시</span><span class="sxs-lookup"><span data-stu-id="37209-135">Distributed Redis cache</span></span>](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="37209-136">분산된 메모리 내 캐시</span><span class="sxs-lookup"><span data-stu-id="37209-136">Distributed Memory Cache</span></span>

<span data-ttu-id="37209-137">메모리 내 분산 캐시 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>)의 프레임 워크에서 제공한 구현인 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 메모리에 항목을 저장 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-137">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="37209-138">분산 메모리 내 캐시는 실제 분산된 캐시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37209-138">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="37209-139">캐시 된 항목은 앱이 실행 하는 서버에서 앱 인스턴스에 의해 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37209-139">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="37209-140">메모리 내 분산 캐시는 유용한 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-140">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="37209-141">개발 및 테스트 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="37209-141">In development and testing scenarios.</span></span>
* <span data-ttu-id="37209-142">프로덕션 및 메모리 사용량에서 단일 서버를 사용할 경우 문제가 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37209-142">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="37209-143">데이터 저장소를 캐시 메모리 캐시 Distributed 요약을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-143">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="37209-144">허용을 구현 하기 위한 진정한 여러 노드 또는 내결함성 제공 해야 할 경우에 나중에 캐싱 솔루션 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-144">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="37209-145">샘플 앱은 앱의 개발 환경에서 실행 될 때 메모리 내 분산 캐시 사용 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="37209-145">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="37209-146">분산 된 SQL 서버 캐시</span><span class="sxs-lookup"><span data-stu-id="37209-146">Distributed SQL Server Cache</span></span>

<span data-ttu-id="37209-147">분산 된 SQL Server 캐시 구현 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) SQL Server 데이터베이스를 백업 저장소로 사용 하 여 분산된 캐시를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-147">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="37209-148">테이블을 만들려면 SQL Server 캐시 된 항목의 SQL Server 인스턴스를 사용할 수는 `sql-cache` 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="37209-148">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="37209-149">도구 이름 및 지정 된 스키마를 사용 하 여 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="37209-149">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="37209-150">SQL Server에서 실행 하 여 테이블을 만들기는 `sql-cache create` 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="37209-150">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="37209-151">SQL Server 인스턴스를 제공 (`Data Source`), 데이터베이스 (`Initial Catalog`), 스키마 (예를 들어 `dbo`)을 및 테이블 이름 (예를 들어 `TestCache`):</span><span class="sxs-lookup"><span data-stu-id="37209-151">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="37209-152">도구 성공 했음을 나타내는 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37209-152">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="37209-153">생성 된 테이블을 `sql-cache` 도구는 다음 스키마를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="37209-153">The table created by the `sql-cache` tool has the following schema:</span></span>

![Sql Server 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="37209-155">앱의 인스턴스를 사용 하 여 캐시 값을 조작 해야 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>이 아니라는 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-155">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="37209-156">샘플 앱 구현 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 비 개발 환경에서 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="37209-156">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="37209-157">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>)는 일반적으로 소스 제어 외부 저장 (에서 예를 들어 저장 합니다 [암호 관리자](xref:security/app-secrets) 또는 *appsettings.json* / *appsettings 합니다. {ENVIRONMENT}.json* 파일).</span><span class="sxs-lookup"><span data-stu-id="37209-157">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="37209-158">연결 문자열을 소스 제어 시스템에서 유지 해야 하는 자격 증명을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37209-158">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="37209-159">분산된 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="37209-159">Distributed Redis Cache</span></span>

<span data-ttu-id="37209-160">[Redis](https://redis.io/)는 분산 캐시로 흔히 사용되는 오픈 소스 메모리 내 데이터 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="37209-160">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="37209-161">Redis를 로컬로 사용할 수 있습니다 하 고 구성할 수 있습니다는 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) Azure에서 호스팅되는 ASP.NET Core 앱에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-161">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="37209-162">앱 구성 사용 하 여 캐시 구현 된 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 인스턴스 (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) 비 개발 환경에서 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="37209-162">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="37209-163">앱 구성 사용 하 여 캐시 구현 된 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> 인스턴스 (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span><span class="sxs-lookup"><span data-stu-id="37209-163">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

<span data-ttu-id="37209-164">로컬 컴퓨터에 Redis를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-164">To install Redis on your local machine:</span></span>

* <span data-ttu-id="37209-165">설치 합니다 [Chocolatey Redis 패키지](https://chocolatey.org/packages/redis-64/)합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-165">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
* <span data-ttu-id="37209-166">실행 `redis-server` 명령 프롬프트에서.</span><span class="sxs-lookup"><span data-stu-id="37209-166">Run `redis-server` from a command prompt.</span></span>

## <a name="use-the-distributed-cache"></a><span data-ttu-id="37209-167">분산된 캐시 사용</span><span class="sxs-lookup"><span data-stu-id="37209-167">Use the distributed cache</span></span>

<span data-ttu-id="37209-168">사용 하 여 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스의 인스턴스를 요청 하십시오 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 앱에서 모든 생성자에서.</span><span class="sxs-lookup"><span data-stu-id="37209-168">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="37209-169">제공 하는 인스턴스 [종속성 주입 (DI)](xref:fundamentals/dependency-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-169">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="37209-170">앱을 시작 하는 경우 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 삽입 되어 `Startup.Configure`입니다.</span><span class="sxs-lookup"><span data-stu-id="37209-170">When the app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="37209-171">현재 사용 하 여 캐시 됩니다 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (자세한 내용은 참조 하세요. [웹 호스트: IApplicationLifetime 인터페이스](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="37209-171">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="37209-172">샘플 앱 삽입 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 에 `IndexModel` 인덱스 페이지를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-172">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="37209-173">인덱스 페이지가 로드 될 때마다 캐시에서 캐시 된 시간에 대 한 확인란이 `OnGetAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-173">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="37209-174">캐시 된 시간 만료 되지 않았으면, 시간이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37209-174">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="37209-175">20 초에 마지막으로 캐시 된 시간 (이 페이지를 로드 하는 마지막 시간) 액세스 된 이후 경과 된 경우 페이지에 표시 됩니다 *캐시 된 시간이 만료*합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-175">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="37209-176">즉시 선택 하 여 현재 시간으로 캐시 된 시간을 업데이트 합니다 **캐시 된 시간 재설정** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="37209-176">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="37209-177">단추 트리거는 `OnPostResetCachedTime` 처리기 메서드.</span><span class="sxs-lookup"><span data-stu-id="37209-177">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="37209-178"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인스턴스를 Singleton이나 Scoped 수명으로 사용할 필요는 없습니다(적어도 기본 구현일 경우).</span><span class="sxs-lookup"><span data-stu-id="37209-178">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="37209-179">만들 수도 있습니다는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> DI를 사용 하는 대신 해야 할 수 있습니다 하지만 코드에서 인스턴스를 만드는 코드를 만들 수 테스트 하기가 때마다 인스턴스를 위반 합니다 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-179">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="37209-180">권장 사항</span><span class="sxs-lookup"><span data-stu-id="37209-180">Recommendations</span></span>

<span data-ttu-id="37209-181">구현의 결정할 때 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 앱에 가장 적합 한 다음 사항을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-181">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="37209-182">기존 인프라</span><span class="sxs-lookup"><span data-stu-id="37209-182">Existing infrastructure</span></span>
* <span data-ttu-id="37209-183">성능 요구 사항</span><span class="sxs-lookup"><span data-stu-id="37209-183">Performance requirements</span></span>
* <span data-ttu-id="37209-184">비용</span><span class="sxs-lookup"><span data-stu-id="37209-184">Cost</span></span>
* <span data-ttu-id="37209-185">팀 환경</span><span class="sxs-lookup"><span data-stu-id="37209-185">Team experience</span></span>

<span data-ttu-id="37209-186">일반적으로 캐시 된 데이터의 빠른 검색을 위해 메모리 내 저장소 캐싱 솔루션에 의존 하지만 메모리가 제한 된 리소스 및 확장 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="37209-186">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="37209-187">전용 저장소는 일반적으로 캐시에 데이터를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-187">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="37209-188">일반적으로 Redis cache는 더 높은 처리량 및 SQL Server 캐시 보다 더 낮은 대기 시간을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-188">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="37209-189">그러나 벤치 마크는 일반적으로 캐싱 전략의 성능 특징을 결정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="37209-189">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="37209-190">분산된 캐시 백업 저장소로 SQL Server를 사용 하면 캐시 및 앱의 일반 데이터 저장소에 대 한 동일한 데이터베이스의 사용 및 검색 둘 다의 성능 저하 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37209-190">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="37209-191">백업 저장소는 분산된 캐시에 대 한 전용된 SQL Server 인스턴스를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="37209-191">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37209-192">추가 자료</span><span class="sxs-lookup"><span data-stu-id="37209-192">Additional resources</span></span>

* [<span data-ttu-id="37209-193">Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="37209-193">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="37209-194">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="37209-194">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="37209-195">[ASP.NET Core 웹 팜에서 NCache 공급자 IDistributedCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([github NCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="37209-195">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
