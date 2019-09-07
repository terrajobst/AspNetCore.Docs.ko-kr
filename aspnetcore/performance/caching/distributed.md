---
title: ASP.NET Core 분산 캐싱
author: guardrex
description: 특히 클라우드 또는 서버 팜 환경에서 ASP.NET Core 분산 캐시를 사용 하 여 앱 성능 및 확장성을 개선 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: performance/caching/distributed
ms.openlocfilehash: 8417463038bcdc0f77852bec3c3bb8a618153009
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773846"
---
# <a name="distributed-caching-in-aspnet-core"></a>ASP.NET Core 분산 캐싱

By [Luke Latham 문자](https://github.com/guardrex) 및 [Steve Smith](https://ardalis.com/)

분산 캐시는 여러 앱 서버에서 공유 하는 캐시로, 일반적으로 액세스 하는 앱 서버에 외부 서비스로 유지 관리 됩니다. 분산 캐시는 특히 앱이 클라우드 서비스 또는 서버 팜에서 호스트 되는 경우 ASP.NET Core 앱의 성능과 확장성을 향상 시킬 수 있습니다.

분산 캐시는 개별 앱 서버에 캐시 된 데이터가 저장 되는 다른 캐싱 시나리오에 비해 여러 가지 이점을 제공 합니다.

캐시 된 데이터를 배포 하는 경우 데이터는 다음과 같습니다.

* 는 여러 서버에 대 한 여러 요청 *에서 일관 되* 고 일관 됩니다.
* 서버를 다시 시작 하 고 앱을 배포 합니다.
* 로컬 메모리를 사용 하지 않습니다.

분산 캐시 구성은 구현 별로 다릅니다. 이 문서에서는 SQL Server 및 Redis 분산 캐시를 구성 하는 방법을 설명 합니다. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))와 같은 타사 구현도 사용할 수 있습니다. 선택한 구현과 관계 없이 앱은 인터페이스를 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 사용 하 여 캐시와 상호 작용 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>필수 구성 요소

::: moniker range=">= aspnetcore-3.0"

SQL Server 분산 캐시를 사용 하려면 패키지 참조를 [Microsoft 확장명이](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) . i n t. i n t. i n t 패키지에 추가 합니다.

Redis 분산 캐시를 사용 하려면 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 패키지에 패키지 참조를 추가 합니다.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

SQL Server 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하거나 패키지 참조를 [패키지에 추가 합니다.](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)

Redis 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 패키지에 추가 합니다. Redis 패키지는 `Microsoft.AspNetCore.App` 패키지에 포함 되지 않으므로 프로젝트 파일에서 개별적으로 Redis 패키지를 참조 해야 합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

SQL Server 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하거나 패키지 참조를 [패키지에 추가 합니다.](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)

Redis 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) 패키지에 추가 합니다. Redis 패키지는 `Microsoft.AspNetCore.App` 패키지에 포함 되지 않으므로 프로젝트 파일에서 개별적으로 Redis 패키지를 참조 해야 합니다.

::: moniker-end

## <a name="idistributedcache-interface"></a>IDistributedCache 인터페이스

인터페이스 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 는 분산 캐시 구현에서 항목을 조작 하는 다음과 같은 메서드를 제공 합니다.

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>는 문자열 키를 `byte[]` 수락 하 고 캐시 된 항목을 캐시에 있는 경우 배열로 검색 합니다. &ndash; <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>는 문자열 키를 사용 하 `byte[]` 여 캐시에 항목 (배열)을 추가합니다.&ndash; <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>는 해당 키에 따라 캐시에서 항목을 새로고치고해당하는슬라이딩만료시간제한을다시설정합니다(있는경우).&ndash; <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>는 문자열 키에 따라 캐시 항목을 제거합니다.&ndash; <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>

## <a name="establish-distributed-caching-services"></a>분산 캐싱 서비스 설정

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 에서`Startup.ConfigureServices`의 구현을 등록 합니다. 이 항목에서 설명 하는 프레임 워크 제공 구현에는 다음이 포함 됩니다.

* [분산 메모리 캐시](#distributed-memory-cache)
* [분산 SQL Server 캐시](#distributed-sql-server-cache)
* [Distributed Redis cache](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a>분산 메모리 캐시

분산 메모리 캐시 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>)는 메모리에 항목을 저장 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 하는 프레임 워크에서 제공 하는 구현입니다. 분산 메모리 캐시는 실제 분산 된 캐시가 아닙니다. 캐시 된 항목은 앱이 실행 되 고 있는 서버의 앱 인스턴스에 의해 저장 됩니다.

분산 메모리 캐시는 다음과 같은 유용한 구현입니다.

* 개발 및 테스트 시나리오에서
* 프로덕션 환경에서 단일 서버를 사용 하는 경우 메모리 소비가 문제가 되지 않습니다. 분산 메모리 캐시를 구현 하면 캐시 된 데이터 저장소를 추상화 합니다. 이를 통해 여러 노드 또는 내결함성이 필요한 경우 나중에 진정한 분산 캐싱 솔루션을 구현할 수 있습니다.

샘플 앱은 앱이의 `Startup.ConfigureServices`개발 환경에서 실행 될 때 분산 메모리 캐시를 사용 합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

### <a name="distributed-sql-server-cache"></a>분산 SQL Server 캐시

분산 SQL Server 캐시 구현 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>)을 사용 하면 분산 캐시에서 SQL Server 데이터베이스를 백업 저장소로 사용할 수 있습니다. SQL Server 인스턴스에서 캐시 된 SQL Server 항목 테이블을 만들려면 `sql-cache` 도구를 사용할 수 있습니다. 이 도구는 사용자가 지정한 이름 및 스키마를 사용 하 여 테이블을 만듭니다.

`sql-cache create` 명령을 실행 하 여 SQL Server에서 테이블을 만듭니다. SQL Server 인스턴스 (`Data Source`), 데이터베이스 (`Initial Catalog`), `dbo`스키마 (예:) 및 테이블 이름 (예: `TestCache`)을 제공 합니다.

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

도구가 성공 했음을 나타내는 메시지가 기록 됩니다.

```console
Table and index were created successfully.
```

`sql-cache` 도구에서 만든 테이블에는 다음 스키마가 있습니다.

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> 앱은가 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>아닌 인스턴스를 사용 하 여 캐시 값을 조작 해야 합니다.

샘플 앱은 `Startup.ConfigureServices`다음과 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 같은 개발 이외의 환경에서을 구현 합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

> [!NOTE]
> ( <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> 및 필요에 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 따라 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>)는 일반적으로 소스 제어 외부에 저장 됩니다 (예: [암호 관리자](xref:security/app-secrets) 또는 *appsettings*/에서 저장 한 경우 *). 환경}. json* 파일). 연결 문자열에는 원본 제어 시스템에서 유지 되어야 하는 자격 증명이 포함 될 수 있습니다.

### <a name="distributed-redis-cache"></a>분산 Redis Cache

[Redis](https://redis.io/)는 분산 캐시로 흔히 사용되는 오픈 소스 메모리 내 데이터 저장소입니다. Redis를 로컬로 사용할 수 있으며, Azure에서 호스트 되는 ASP.NET Core 앱에 대 한 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) 를 구성할 수 있습니다.

::: moniker range=">= aspnetcore-3.0"

앱은 `Startup.ConfigureServices`다음과 같은 개발 이외의 환경에서 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 인스턴스 (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>)를 사용 하 여 캐시 구현을 구성 합니다.

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

앱은 `Startup.ConfigureServices`다음과 같은 개발 이외의 환경에서 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 인스턴스 (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>)를 사용 하 여 캐시 구현을 구성 합니다.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

앱은 인스턴스 ( <xref:Microsoft.Extensions.Caching.Redis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>)를 사용 하 여 캐시 구현을 구성 합니다.

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

로컬 컴퓨터에 Redis를 설치 하려면 다음을 수행 합니다.

* [Chocolatey Redis 패키지](https://chocolatey.org/packages/redis-64/)를 설치 합니다.
* 명령 `redis-server` 프롬프트에서를 실행 합니다.

## <a name="use-the-distributed-cache"></a>분산 캐시 사용

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스를 사용 하려면 앱의 모든 생성자에서 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 의 인스턴스를 요청 합니다. 인스턴스는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)에 의해 제공 됩니다.

::: moniker range=">= aspnetcore-3.0"

샘플 앱 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 이 시작 되 면가에 `Startup.Configure`삽입 됩니다. 현재 시간은를 사용 하 여 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> 캐시 됩니다. 자세한 내용은 제네릭 [호스트를 참조 하십시오. IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

샘플 앱 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 이 시작 되 면가에 `Startup.Configure`삽입 됩니다. 현재 시간은를 사용 하 여 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 캐시 됩니다. 자세한 내용은 웹 [호스트를 참조 하십시오. IApplicationLifetime 인터페이스](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

샘플 앱은 인덱스 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 페이지에서 `IndexModel` 사용 하기 위해를에 삽입 합니다.

인덱스 페이지가 로드 될 때마다에서 `OnGetAsync`캐시 된 시간에 대 한 캐시를 확인 합니다. 캐시 된 시간이 만료 되지 않은 경우에는 시간이 표시 됩니다. 캐시 된 시간에 마지막으로 액세스 한 후 20 초가 경과한 경우 (이 페이지가 마지막으로 로드 된 시간) 페이지에는 *캐시 된 시간 만료*가 표시 됩니다.

캐시 된 시간 **다시 설정** 단추를 선택 하 여 캐시 된 시간을 현재 시간으로 즉시 업데이트 합니다. 단추는 처리기 메서드 `OnPostResetCachedTime` 를 트리거합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인스턴스를 Singleton이나 Scoped 수명으로 사용할 필요는 없습니다(적어도 기본 구현일 경우).
>
> DI를 사용 하는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 대신 인스턴스를 만들 수도 있지만, 코드에서 인스턴스를 만들면 코드를 테스트 하는 것이 어렵고 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 위반 하 게 될 수 있습니다.

## <a name="recommendations"></a>권장 사항

앱에 가장 적합 한 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 구현을 결정할 때 다음 사항을 고려 합니다.

* 기존 인프라
* 성능 요구 사항
* 비용
* 팀 환경

캐싱 솔루션은 일반적으로 메모리 내 저장소를 사용 하 여 캐시 된 데이터를 신속 하 게 검색 하지만 메모리는 제한 된 리소스 이며 확장 하는 데 비용이 많이 듭니다. 일반적으로 사용 되는 데이터를 캐시에만 저장 합니다.

일반적으로 Redis cache는 SQL Server 캐시 보다 높은 처리량과 짧은 대기 시간을 제공 합니다. 그러나 일반적으로 벤치마킹은 캐싱 전략의 성능 특성을 결정 하는 데 필요 합니다.

SQL Server를 분산 캐시 백업 저장소로 사용 하는 경우 캐시에 대해 동일한 데이터베이스를 사용 하 고 앱의 일반 데이터 저장 및 검색을 사용 하면 두 성능에 부정적인 영향을 줄 수 있습니다. 분산 캐시 백업 저장소에 전용 SQL Server 인스턴스를 사용 하는 것이 좋습니다.

## <a name="additional-resources"></a>추가 자료

* [Azure Redis Cache](/azure/azure-cache-for-redis/)
* [Azure SQL Database](/azure/sql-database/)
* [웹 팜의 NCache IDistributedCache Provider에 대 한 ASP.NET Core](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
