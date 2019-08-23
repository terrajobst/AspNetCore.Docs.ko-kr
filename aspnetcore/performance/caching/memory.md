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
# <a name="cache-in-memory-in-aspnet-core"></a>ASP.NET Core의 메모리 내 캐시

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo) 및 [Steve Smith](https://ardalis.com/)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>캐싱 기본 사항

캐싱은 콘텐츠를 생성하기 위해 필요한 작업을 줄임으로써 응용 프로그램의 성능 및 확장성을 크게 향상시킵니다. 캐싱은 자주 변경되지 않는 데이터에 가장 효과가 좋습니다. 캐싱은 원본에서 가져와서 반환하는 것보다 더 빠르게 반환할 수 있는 데이터의 복사본을 만듭니다. 캐시 된 데이터에 종속 **되지 않도록** 앱을 작성 하 고 테스트 해야 합니다.

ASP.NET Core는 몇 가지 다른 종류의 캐시를 지원합니다. 가장 간단한 캐시는 웹 서버의 메모리에 저장된 캐시를 나타내는 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)를 기반으로 합니다. 여러 서버의 서버 팜에서 실행 되는 앱은 메모리 내 캐시를 사용할 때 세션이 고정 되어 있는지 확인 해야 합니다. 고정 세션은 클라이언트의 이어지는 후속 요청이 모두 동일한 서버로 전달되도록 보장해줍니다. 예를 들어 Azure 웹 앱은 이어지는 모든 후속 요청을 동일한 서버로 라우트하기 위해서 [응용 프로그램 요청 라우팅](https://www.iis.net/learn/extensions/planning-for-arr)(ARR)을 사용합니다.

웹 팜에서 비-고정 세션을 사용할 경우에는 캐시 일관성 문제가 발생하지 않도록 [분산 캐시](distributed.md)가 필요합니다. 일부 앱의 경우 분산 캐시는 메모리 내 캐시 보다 더 높은 확장을 지원할 수 있습니다. 분산 캐시를 사용하면 캐시 메모리를 외부 프로세스에서 관리합니다.

::: moniker range="< aspnetcore-2.0"

`IMemoryCache` 캐시는 [캐시 우선 순위](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority)가 `CacheItemPriority.NeverRemove`로 설정되지 않은 캐시 항목을 메모리 부하에 따라 제거합니다. `CacheItemPriority`를 설정하면 메모리에 부하가 걸렸을 때 캐시가 항목을 제거하는 우선 순위를 조정할 수 있습니다.

::: moniker-end

메모리 내 캐시는 모든 개체를 저장할 수 있는 반면, 분산 캐시 인터페이스는 `byte[]`만 저장할 수 있습니다. 메모리 내 및 분산 캐시 저장소는 키-값 쌍으로 항목을 캐시 합니다.

## <a name="systemruntimecachingmemorycache"></a>System.Runtime.Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache>([NuGet 패키지](https://www.nuget.org/packages/System.Runtime.Caching/))는와 함께 사용할 수 있습니다.

* 2\.0 이상 .NET Standard 합니다.
* .NET Standard 2.0 이상을 대상으로 하는 [.net 구현](/dotnet/standard/net-standard#net-implementation-support) 예를 들어 ASP.NET Core 2.0 이상입니다.
* 4\.5 이상 .NET Framework 합니다.

[이 문서](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) 에 설명 된 대로, ASP.NET Core에 더 잘 통합 되기때문에이문서에설명되어있는것이좋습니다.`MemoryCache` / `System.Runtime.Caching` / `IMemoryCache` 예 `IMemoryCache` 를 들어은 ASP.NET Core [종속성 주입](xref:fundamentals/dependency-injection)을 기본적으로 사용 합니다.

ASP.NET `System.Runtime.Caching` 4.x에서 ASP.NET Core로 코드를 이식할 때 호환성 브리지로를 사용 / `MemoryCache` 합니다.

## <a name="cache-guidelines"></a>캐시 지침

* 코드에는 항상 데이터를 인출 하는 대체 (fallback) 옵션이 있으며 사용 가능한 캐시 된 값에 의존 **하지** 않아야 합니다.
* 캐시는 부족 한 리소스 인 메모리를 사용 합니다. 캐시 증가 제한:
  * 외부 입력을 캐시 키로 사용 **하지** 마십시오.
  * 캐시 증가를 제한 하려면 만료를 사용 합니다.
  * [SetSize, Size 및 SizeLimit를 사용 하 여 캐시 크기를 제한](#use-setsize-size-and-sizelimit-to-limit-cache-size)합니다. ASP.NET Core 런타임은 메모리 압력에 따라 캐시 크기를 제한 하지 않습니다. 캐시 크기를 제한 하는 것은 개발자에 게 있습니다.

## <a name="using-imemorycache"></a>IMemoryCache 사용하기

> [!WARNING]
> [종속성 주입](xref:fundamentals/dependency-injection) 에서 *공유* 메모리 캐시를 사용 하 고 `SetSize`, `Size`또는 `SizeLimit` 를 호출 하 여 캐시 크기를 제한 하면 앱에 오류가 발생할 수 있습니다. 캐시에 크기 제한이 설정 된 경우 모든 항목은 추가 될 때 크기를 지정 해야 합니다. 이로 인해 개발자가 공유 캐시를 사용 하는 항목을 완전히 제어할 수 없기 때문에 문제가 발생할 수 있습니다. 예를 들어 Entity Framework Core는 공유 캐시를 사용 하 고 크기를 지정 하지 않습니다. 앱이 캐시 크기 제한을 설정 하 고 EF Core를 사용 하는 경우 앱은 `InvalidOperationException`을 throw 합니다.
> , `SetSize` `Size`또는 를사용하여캐시를제한하는경우캐싱에대한캐시singleton을만듭니다.`SizeLimit` 자세한 내용 및 예제는 [SetSize, Size 및 SizeLimit를 사용 하 여 캐시 크기 제한](#use-setsize-size-and-sizelimit-to-limit-cache-size)을 참조 하세요.

메모리 내 캐시는 응용 프로그램에서 [종속성 주입](xref:fundamentals/dependency-injection)을 통해서 참조되는 *서비스*입니다. `ConfigureServices`에서 `AddMemoryCache`를 호출합니다.

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

그런 다음 생성자에서 `IMemoryCache`의 인스턴스를 요청합니다.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

`IMemoryCache`를 사용하려면 [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) NuGet 패키지가 필요합니다.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

`IMemoryCache`를 사용하려면 [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage)에서 사용할 수 있는 [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) NuGet 패키지를 설치해야 합니다.

::: moniker-end

::: moniker range="> aspnetcore-2.0"

`IMemoryCache`를 사용하려면 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)에서 사용할 수 있는 [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) NuGet 패키지를 설치해야 합니다.

::: moniker-end

다음 코드는 [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)를 이용해서 시간이 캐시되어 있는지 확인합니다. 만약 시간이 캐시되어 있지 않다면 새로운 항목이 생성되고 [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)을 통해서 캐시에 추가됩니다.

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

현재 시간과 캐시된 시간이 표시됩니다.

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

제한 시간 내에 요청이 있는 동안 캐시 된 값은캐시에남아있습니다.`DateTime` 다음 그림은 현재 시간과 캐시에서 조회한 그보다 오래된 시간을 보여줍니다.

![두 개의 서로 다른 시간을 표시하는 Index 뷰](memory/_static/time.png)

다음 코드는 [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) 및 [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) 를 이용해서 데이터를 캐시합니다.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

다음 코드는 [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 을 호출해서 캐시된 시간을 가져옵니다.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>및 [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 은의 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>기능을 확장 하는 [cacheextensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) 클래스의 일부인 확장 메서드입니다. 다른 캐시 메서드에 대 한 설명은 [IMemoryCache 메서드](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) 및 [cacheextensions 메서드](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) 를 참조 하세요.

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

다음 예제에서는:

* 절대 만료 시간을 설정합니다. 절대 만료 시간은 항목이 캐시될 수 있는 최대 시간으로, 슬라이딩 만료가 지속적으로 갱신될 경우 항목이 이전 상태로 지속되는 것을 방지합니다.
* 슬라이딩 만료 시간을 설정합니다. 상대 만료 시계는 캐시된 항목에 접근하는 요청에 따라 재설정됩니다.
* 캐시 우선 순위를 `CacheItemPriority.NeverRemove`로 설정합니다.
* 캐시에서 항목이 제거된 후에 호출되는 [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 를 설정합니다. 콜백은 캐시에서 항목을 제거하는 코드와 다른 스레드에서 실행됩니다. 콜백은 캐시에서 항목을 제거 하는 코드와 다른 스레드에서 실행 됩니다.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>SetSize, Size 및 SizeLimit를 사용 하 여 캐시 크기 제한

인스턴스 `MemoryCache` 는 필요에 따라 크기 제한을 지정 하 고 적용할 수 있습니다. 캐시에 항목 크기를 측정 하는 메커니즘이 없기 때문에 메모리 크기 제한에 정의 된 측정 단위가 없습니다. 캐시 메모리 크기 제한이 설정 된 경우 모든 항목의 크기를 지정 해야 합니다. ASP.NET Core 런타임은 메모리 압력에 따라 캐시 크기를 제한 하지 않습니다. 캐시 크기를 제한 하는 것은 개발자에 게 있습니다. 지정 된 크기는 개발자가 선택 하는 단위입니다.

예를 들어:

* 웹 앱이 주로 문자열을 캐싱하는 경우 각 캐시 엔트리 크기는 문자열 길이가 될 수 있습니다.
* 앱은 모든 항목의 크기를 1로 지정 하 고, 크기 제한은 항목 수를 지정 합니다.

다음 코드는 [종속성 주입](xref:fundamentals/dependency-injection)으로 액세스할 수 있는 고정 크기의 고정 크기 [memorycache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) 를 만듭니다.

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

[Sizelimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) 에는 단위가 없습니다. 캐시 된 항목은 캐시 메모리 크기가 설정 된 경우 가장 적합 한 것으로 간주 되는 단위의 크기를 지정 해야 합니다. 캐시 인스턴스의 모든 사용자는 동일한 단위 시스템을 사용 해야 합니다. 캐시 된 항목 크기의 합계가에 `SizeLimit`지정 된 값을 초과 하는 경우 항목이 캐시 되지 않습니다. 캐시 크기 제한을 설정 하지 않으면 항목에 설정 된 캐시 크기가 무시 됩니다.

다음 코드는 `MyMemoryCache` [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록 됩니다.

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache`는이 크기 제한 된 캐시를 인식 하는 구성 요소에 대 한 독립 메모리 캐시로 만들어지며 캐시 엔트리 크기를 적절 하 게 설정 하는 방법을 알고 있습니다.

다음 코드에서는를 `MyMemoryCache`사용 합니다.

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

캐시 엔트리의 크기는 [크기](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) 또는 [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) 확장 메서드를 통해 설정할 수 있습니다.

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a>캐시 종속성

다음 예제는 종속적인 항목을 만료할 경우 캐시 항목을 만료하는 방법을 보여줍니다. 캐시된 항목에 `CancellationChangeToken`이 추가됩니다. `CancellationTokenSource`의 `Cancel`이 호출되면 캐시된 두 항목 모두 제거됩니다.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

`CancellationTokenSource`를 사용하면 다수의 캐시 항목을 그룹으로 제거할 수 있습니다. 위의 코드의 `using` 패턴을 사용하면,`using` 블록 안에서 생성된 캐시 항목들이 트리거 및 만료 설정을 상속받습니다.

## <a name="additional-notes"></a>추가 참고 사항

* 캐시 항목을 다시 채우기 위해서 콜백을 사용할 때:

  * 다수의 요청이 캐시된 키 값으로 빈 값을 얻을 수도 있는데, 이는 콜백이 완료되지 않았기 때문입니다.
  * 이로 인해 다수의 스레드가 캐시된 항목을 다시 채울 수 있습니다.

* 한 캐시 항목을 사용해서 다른 캐시 항목을 만들 때, 하위 항목은 부모 항목의 만료 토큰 및 시간 기반의 만료 설정을 복사합니다. 하위 항목은 부모 항목을 수동으로 제거하거나 갱신하더라도 만료되지 않습니다.

* [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) 를 사용 하 여 캐시 엔트리를 캐시에서 제거한 후에 발생 하는 콜백을 설정 합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
