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
# <a name="distributed-caching-in-aspnet-core"></a>ASP.NET Core의 캐싱 분산

하 여 [Luke Latham](https://github.com/guardrex) 고 [Steve Smith](https://ardalis.com/)

분산된 캐시에 액세스 하는 응용 프로그램 서버 외부 서비스로 일반적으로 유지 관리 하는 여러 앱 서버에서 공유 캐시가입니다. 앱은 클라우드 서비스 또는 서버 팜을 호스팅하는 경우에 특히 분산된 캐시는 성능 및 ASP.NET Core 앱의 확장성을 개선할 수 있습니다.

분산된 캐시에는 개별 앱 서버에서 캐시 된 데이터가 저장 된 다른 캐싱 시나리오를 통해 몇 가지 장점이 있습니다.

캐시 된 데이터를 분산 하는 경우, 데이터:

* 됩니다 *일관 된* (일치)에서 여러 서버에 요청 합니다.
* 서버 다시 시작 되 고 앱 배포도 계속 유지 됩니다.
* 로컬 메모리를 사용 하지 않습니다.

분산된 캐시 구성은 특정 구현 합니다. 이 문서에서는 SQL Server를 구성 하 고 Redis cache를 배포 하는 방법을 설명 합니다. 제 3 자 구현도 같은 사용할 수 있습니다 [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([github NCache](https://github.com/Alachisoft/NCache)). 어떤 구현의 선택 하는 것에 관계 없이 앱을 사용 하 여 캐시 상호 작용을 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스입니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>전제 조건

::: moniker range=">= aspnetcore-2.2"

SQL Server를 사용 하 여 분산 캐시에 대 한 참조를 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) 에 대 한 패키지 참조를 추가 또는 합니다 [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) 패키지 합니다.

Redis를 사용 하 여 분산 캐시에 대 한 참조를 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) 에 대 한 패키지 참조를 추가 하 고는 [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 패키지 합니다. Redis 패키지에 포함 되어 있지는 `Microsoft.AspNetCore.App` 패키지를 프로젝트 파일의 Redis 패키지를 개별적으로 참조 해야 하므로 합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

SQL Server를 사용 하 여 분산 캐시에 대 한 참조를 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) 에 대 한 패키지 참조를 추가 또는 합니다 [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) 패키지 합니다.

Redis를 사용 하 여 분산 캐시에 대 한 참조를 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) 에 대 한 패키지 참조를 추가 하 고는 [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) 패키지 합니다. Redis 패키지에 포함 되어 있지는 `Microsoft.AspNetCore.App` 패키지를 프로젝트 파일의 Redis 패키지를 개별적으로 참조 해야 하므로 합니다.

::: moniker-end

## <a name="idistributedcache-interface"></a>IDistributedCache 인터페이스

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스 분산된 캐시 구현에서 항목을 조작 하려면 다음 메서드를 제공 합니다.

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>를 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; 문자열 키를 받아서으로 캐시 된 항목을 검색 합니다는 `byte[]` 경우 캐시에서 발견 합니다.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>를 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 항목을 추가 (으로 `byte[]` 배열) 문자열 키를 사용 하 여 캐시 합니다.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>하십시오 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; (있는 경우) 해당 상대 (sliding) 만료 시간 제한 다시 설정 하 고 키를 기반으로 캐시에서 항목을 새로 고칩니다.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>하십시오 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 해당 문자열 키를 기반으로 캐시 항목을 제거 합니다.

## <a name="establish-distributed-caching-services"></a>분산된 캐싱 서비스를 설정 합니다.

등록의 구현을 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 에서 `Startup.ConfigureServices`합니다. 이 항목에서 설명 하는 프레임 워크에서 제공한 구현은 다음과 같습니다.

* [분산된 메모리 내 캐시](#distributed-memory-cache)
* [분산 된 SQL Server 캐시](#distributed-sql-server-cache)
* [분산 된 Redis 캐시](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a>분산된 메모리 내 캐시

메모리 내 분산 캐시 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>)의 프레임 워크에서 제공한 구현인 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 메모리에 항목을 저장 하는 합니다. 분산 메모리 내 캐시는 실제 분산된 캐시 되지 않습니다. 캐시 된 항목은 앱이 실행 하는 서버에서 앱 인스턴스에 의해 저장 됩니다.

메모리 내 분산 캐시는 유용한 구현 합니다.

* 개발 및 테스트 시나리오입니다.
* 프로덕션 및 메모리 사용량에서 단일 서버를 사용할 경우 문제가 되지 않습니다. 데이터 저장소를 캐시 메모리 캐시 Distributed 요약을 구현 합니다. 허용을 구현 하기 위한 진정한 여러 노드 또는 내결함성 제공 해야 할 경우에 나중에 캐싱 솔루션 배포 합니다.

샘플 앱은 앱의 개발 환경에서 실행 될 때 메모리 내 분산 캐시 사용 `Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>분산 된 SQL 서버 캐시

분산 된 SQL Server 캐시 구현 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) SQL Server 데이터베이스를 백업 저장소로 사용 하 여 분산된 캐시를 허용 합니다. 테이블을 만들려면 SQL Server 캐시 된 항목의 SQL Server 인스턴스를 사용할 수는 `sql-cache` 도구입니다. 도구 이름 및 지정 된 스키마를 사용 하 여 테이블을 만듭니다.

SQL Server에서 실행 하 여 테이블을 만들기는 `sql-cache create` 명령입니다. SQL Server 인스턴스를 제공 (`Data Source`), 데이터베이스 (`Initial Catalog`), 스키마 (예를 들어 `dbo`)을 및 테이블 이름 (예를 들어 `TestCache`):

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

도구 성공 했음을 나타내는 메시지가 기록 됩니다.

```console
Table and index were created successfully.
```

생성 된 테이블을 `sql-cache` 도구는 다음 스키마를 갖습니다.

![Sql Server 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> 앱의 인스턴스를 사용 하 여 캐시 값을 조작 해야 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>이 아니라는 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>합니다.

샘플 앱 구현 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 비 개발 환경에서 `Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>)는 일반적으로 소스 제어 외부 저장 (에서 예를 들어 저장 합니다 [암호 관리자](xref:security/app-secrets) 또는 *appsettings.json* / *appsettings 합니다. {ENVIRONMENT}.json* 파일). 연결 문자열을 소스 제어 시스템에서 유지 해야 하는 자격 증명을 포함할 수 있습니다.

### <a name="distributed-redis-cache"></a>분산된 Redis Cache

[Redis](https://redis.io/)는 분산 캐시로 흔히 사용되는 오픈 소스 메모리 내 데이터 저장소입니다. Redis를 로컬로 사용할 수 있습니다 하 고 구성할 수 있습니다는 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) Azure에서 호스팅되는 ASP.NET Core 앱에 대 한 합니다.

::: moniker range=">= aspnetcore-2.2"

앱 구성 사용 하 여 캐시 구현 된 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 인스턴스 (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) 비 개발 환경에서 `Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

앱 구성 사용 하 여 캐시 구현 된 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> 인스턴스 (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

로컬 컴퓨터에 Redis를 설치 합니다.

* 설치 합니다 [Chocolatey Redis 패키지](https://chocolatey.org/packages/redis-64/)합니다.
* 실행 `redis-server` 명령 프롬프트에서.

## <a name="use-the-distributed-cache"></a>분산된 캐시 사용

사용 하 여 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스의 인스턴스를 요청 하십시오 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 앱에서 모든 생성자에서. 제공 하는 인스턴스 [종속성 주입 (DI)](xref:fundamentals/dependency-injection)합니다.

앱을 시작 하는 경우 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 삽입 되어 `Startup.Configure`입니다. 현재 사용 하 여 캐시 됩니다 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (자세한 내용은 참조 하세요. [웹 호스트: IApplicationLifetime 인터페이스](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

샘플 앱 삽입 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 에 `IndexModel` 인덱스 페이지를 사용 합니다.

인덱스 페이지가 로드 될 때마다 캐시에서 캐시 된 시간에 대 한 확인란이 `OnGetAsync`합니다. 캐시 된 시간 만료 되지 않았으면, 시간이 표시 됩니다. 20 초에 마지막으로 캐시 된 시간 (이 페이지를 로드 하는 마지막 시간) 액세스 된 이후 경과 된 경우 페이지에 표시 됩니다 *캐시 된 시간이 만료*합니다.

즉시 선택 하 여 현재 시간으로 캐시 된 시간을 업데이트 합니다 **캐시 된 시간 재설정** 단추입니다. 단추 트리거는 `OnPostResetCachedTime` 처리기 메서드.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인스턴스를 Singleton이나 Scoped 수명으로 사용할 필요는 없습니다(적어도 기본 구현일 경우).
>
> 만들 수도 있습니다는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> DI를 사용 하는 대신 해야 할 수 있습니다 하지만 코드에서 인스턴스를 만드는 코드를 만들 수 테스트 하기가 때마다 인스턴스를 위반 합니다 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)합니다.

## <a name="recommendations"></a>권장 사항

구현의 결정할 때 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 앱에 가장 적합 한 다음 사항을 고려 합니다.

* 기존 인프라
* 성능 요구 사항
* 비용
* 팀 환경

일반적으로 캐시 된 데이터의 빠른 검색을 위해 메모리 내 저장소 캐싱 솔루션에 의존 하지만 메모리가 제한 된 리소스 및 확장 비용이 많이 듭니다. 전용 저장소는 일반적으로 캐시에 데이터를 사용 합니다.

일반적으로 Redis cache는 더 높은 처리량 및 SQL Server 캐시 보다 더 낮은 대기 시간을 제공합니다. 그러나 벤치 마크는 일반적으로 캐싱 전략의 성능 특징을 결정 해야 합니다.

분산된 캐시 백업 저장소로 SQL Server를 사용 하면 캐시 및 앱의 일반 데이터 저장소에 대 한 동일한 데이터베이스의 사용 및 검색 둘 다의 성능 저하 될 수 있습니다. 백업 저장소는 분산된 캐시에 대 한 전용된 SQL Server 인스턴스를 사용 하는 것이 좋습니다.

## <a name="additional-resources"></a>추가 자료

* [Azure Redis Cache](/azure/azure-cache-for-redis/)
* [Azure SQL Database](/azure/sql-database/)
* [ASP.NET Core 웹 팜에서 NCache 공급자 IDistributedCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([github NCache](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
