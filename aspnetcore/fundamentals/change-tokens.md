---
title: ASP.NET Core에서 변경 토큰을 사용하여 변경 내용 검색
author: guardrex
description: 변경 토큰을 사용하여 변경 내용을 추적하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 11/10/2017
uid: fundamentals/change-tokens
ms.openlocfilehash: f7dad52fc19b6dc5c8668ad852e69acd65a72162
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886878"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>ASP.NET Core에서 변경 토큰을 사용하여 변경 내용 검색

[Luke Latham](https://github.com/guardrex)으로

*변경 토큰*은 변경 내용을 추적하는 데 사용되는 범용의 하위 수준 구성 요소입니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>IChangeToken 인터페이스

[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)은 변경이 발생했다는 알림을 전파합니다. `IChangeToken`은 [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) 네임스페이스에 있습니다. [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 이상)를 사용하지 않는 앱의 경우 프로젝트 파일에서 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 패키지를 참조합니다.

`IChangeToken`에는 두 가지 속성이 있습니다.

* [ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks)는 토큰이 사전에 콜백을 발생시키는지 여부를 나타냅니다. `ActiveChangedCallbacks`가 `false`로 설정된 경우 콜백은 호출되지 않고 앱은 변경에 대해 `HasChanged`를 폴링해야 합니다. 또한 변경이 발생하지 않거나 기본 변경 리스너가 삭제되거나 사용하지 않도록 설정된 경우에도 토큰을 절대로 취소할 수 없습니다.
* [HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged)는 변경이 발생했는지 나타내는 값을 가져옵니다.

이 인터페이스에는 [RegisterChangeCallback(Action&lt;Object&gt;, Object)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback)라는 한 개의 메서드가 포함되며 이는 토큰이 변경될 때 호출되는 콜백을 등록합니다. `HasChanged`는 콜백이 호출되기 전에 설정되어야 합니다.

## <a name="changetoken-class"></a>ChangeToken 클래스

`ChangeToken`은 변경이 발생했다는 알림을 전파하는 데 사용되는 정적 클래스입니다. `ChangeToken`은 [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) 네임스페이스에 있습니다. [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 사용하지 않는 앱의 경우 프로젝트 파일에서 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 패키지를 참조합니다.

`ChangeToken` [OnChange(Func&lt;IChangeToken&gt;, Action)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_) 메서드는 토큰이 변경될 때마다 호출할 `Action`을 등록합니다.

* `Func<IChangeToken>`은 출력을 생성합니다.
* 토큰이 변경될 때 `Action`이 호출됩니다.

`ChangeToken`에는 토큰 소비자 `Action`에게 전달된 추가 `TState` 매개 변수를 사용하는 [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, Action&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_) 오버로드가 있습니다.

`OnChange`는 [IDisposable](/dotnet/api/system.idisposable)을 반환합니다. [Dispose](/dotnet/api/system.idisposable.dispose)를 호출하면 토큰이 더 이상 변경 내용을 수신 대기하지 않고 토큰의 리소스가 해제됩니다.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>ASP.NET Core에서 변경 토큰의 사용 예

변경 토큰은 ASP.NET Core에서 개체의 변경 내용을 모니터링하는 주요 영역에 사용됩니다.

* 파일에 대한 변경을 모니터링하기 위해 [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)의 [Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) 메서드는 지정된 파일 또는 조사할 폴더에 대해 `IChangeToken`을 만듭니다.
* `IChangeToken` 토큰을 캐시 항목에 추가하여 변경 시 캐시 제거를 트리거할 수 있습니다.
* `TOptions` 변경의 경우, [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1)의 기본 [OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1) 구현은 [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1) 인스턴스를 하나 이상 받아들이는 오버로드를 포함합니다. 각 인스턴스는 옵션 변경을 추적하기 위해 변경 알림 콜백을 등록하도록 `IChangeToken`을 반환합니다.

## <a name="monitoring-for-configuration-changes"></a>구성 변경 모니터링

기본적으로 ASP.NET Core 템플릿은 [JSON 구성 파일](xref:fundamentals/configuration/index#json-configuration-provider)(*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*)을 사용하여 앱 구성 설정을 로드합니다.

이러한 파일은 `reloadOnChange` 매개 변수를 받아들이는 [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder)에서 [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_) 확장 메서드를 사용하여 구성합니다(ASP.NET Core 1.1 이상). `reloadOnChange`는 파일 변경 시 구성을 다시 로드해야 하는지를 나타냅니다. [WebHost](/dotnet/api/microsoft.aspnetcore.webhost) 편의 메서드 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder)에서 이 설정을 참조하세요.

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);
```

파일 기반 구성은 [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource)로 표현됩니다. `FileConfigurationSource`는 [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)을 사용하여 파일을 모니터링합니다.

기본적으로 `IFileMonitor`는 [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider)에서 제공하며 [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher)를 사용하여 구성 파일 변경을 모니터링합니다.

샘플 앱을 통해 구성 변경 모니터링을 위한 두 가지 구현을 설명합니다. *appsettings.json* 파일이 변경되거나 파일의 환경 버전이 변경되는 경우 각 구현에서 사용자 지정 코드를 실행합니다. 샘플 앱은 메시지를 콘솔에 기록합니다.

구성 파일의 `FileSystemWatcher`는 단일 구성 파일 변경에 대해 토큰 콜백을 여러 개 트리거할 수 있습니다. 샘플의 구현은 구성 파일에서 파일 해시를 확인하여 이 문제를 방지합니다. 파일 해시를 확인하면 사용자 지정 코드를 실행하기 전에 적어도 하나의 구성 파일이 변경되었는지 확인할 수 있습니다. 샘플에서는 SHA1 파일 해시(*Utilities/Utilities.cs*)를 사용합니다.

   [!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet1)]

   재시도는 지수 백오프로 구현됩니다. 파일 중 하나에서 새 해시를 일시적으로 계산하지 못하게 하는 파일 잠금이 발생할 수 있으므로 재시도가 제공됩니다.

### <a name="simple-startup-change-token"></a>단순 시작 변경 토큰

변경 알림을 위한 토큰 소비자 `Action` 콜백을 구성 다시 로드 토큰(*Startup.cs*)에 등록합니다.

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet2)]

`config.GetReloadToken()`은 토큰을 제공합니다. 콜백은 `InvokeChanged` 메서드입니다.

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet3)]

콜백의 `state`가 `IHostingEnvironment`에서 전달하는 데 사용됩니다. 이것은 모니터링할 올바른 *appsettings* 구성 JSON 파일(*appsettings.&lt;Environment&gt;.json*)을 결정하는 데 유용합니다. 파일 해시는 구성 파일이 한 번만 변경된 경우 여러 토큰 콜백으로 인해 `WriteConsole` 문이 여러 번 실행되지 않도록 하는 데 사용됩니다.

이 시스템은 앱이 실행 중일 때 실행되며 사용자가 사용 중지할 수 없습니다.

### <a name="monitoring-configuration-changes-as-a-service"></a>서비스로 구성 변경 모니터링

이 샘플에서는 다음을 구현합니다.

* 기본 시작 토큰 모니터링
* 서비스로 모니터링
* 모니터링을 사용 및 사용 안 함으로 설정하는 메커니즘

이 샘플은 `IConfigurationMonitor` 인터페이스(*Extensions/ConfigurationMonitor.cs*)를 설정합니다.

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet1)]

구현된 클래스의 생성자인 `ConfigurationMonitor`는 변경 알림을 위한 콜백을 등록합니다.

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`은 토큰을 제공합니다. `InvokeChanged`는 콜백 메서드입니다. 이 인스턴스의 `state`는 모니터링 상태에 액세스하는 데 사용되는 `IConfigurationMonitor` 인스턴스에 대한 참조입니다. 두 개의 속성이 사용됩니다.

* `MonitoringEnabled`는 콜백이 사용자 지정 코드를 실행해야 하는지를 나타냅니다.
* `CurrentState`는 UI에서 사용하기 위해 현재 모니터링 상태를 설명합니다.

`InvokeChanged` 메서드는 다음을 제외하고 이전 방법과 유사합니다.

* `MonitoringEnabled`가 `true`가 아닌 경우 해당 코드를 실행하지 않습니다.
* `WriteConsole` 출력의 현재 `state`를 기록합니다.

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet3)]

`ConfigurationMonitor` 인스턴스가 *Startup.cs*의 `ConfigureServices`에서 서비스로 등록됩니다.

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet1)]

인덱스 페이지에서는 구성 모니터링을 통해 사용자 컨트롤을 제공합니다. `IConfigurationMonitor`의 인스턴스는 `IndexModel`에 삽입됩니다.

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet1)]

단추는 모니터링을 사용 및 사용하지 않도록 설정합니다.

[!code-cshtml[](change-tokens/sample/Pages/Index.cshtml?range=35)]

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet2)]

`OnPostStartMonitoring`이 트리거되면 모니터링을 사용하도록 설정하고 현재 상태가 지워집니다. `OnPostStopMonitoring`이 트리거되면 모니터링을 사용하지 않도록 설정하고 모니터링이 발생하지 않음을 반영하도록 상태를 설정합니다.

## <a name="monitoring-cached-file-changes"></a>캐시된 파일 변경 모니터링

[IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)를 사용하여 파일 콘텐츠를 메모리 내에 캐시할 수 있습니다. 메모리 내 캐싱은 [메모리 내 캐시](xref:performance/caching/memory) 토픽에서 설명합니다. 아래에 설명된 구현과 같은 추가 단계를 수행하지 않으면 소스 데이터가 변경될 경우 캐시에서 *부실*(오래된) 데이터가 반환됩니다.

[상대(sliding) 만료](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) 기간을 갱신할 때 캐시된 소스 파일의 상태를 고려하지 않으면 부실 캐시 데이터가 발생합니다. 데이터에 대한 각 요청은 상대(sliding) 만료 기간을 갱신하지만 파일은 캐시에 다시 로드되지 않습니다. 파일의 캐시된 콘텐츠를 사용하는 모든 앱 기능은 부실 콘텐츠를 받을 수 있습니다.

파일 캐싱 시나리오에서 변경 토큰을 사용하면 캐시에 부실 파일 콘텐츠가 생기는 것을 방지할 수 있습니다. 샘플 앱에서 이러한 방법의 구현을 보여 줍니다.

이 샘플에서는 `GetFileContent`를 사용하여 다음을 수행합니다.

* 파일 콘텐츠를 반환합니다.
* 파일 잠금으로 인해 일시적으로 파일을 읽을 수 없는 경우를 처리하기 위해 지수 백오프를 사용하여 재시도 알고리즘을 구현합니다.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet2)]

캐시된 파일 조회를 처리하기 위해 `FileService`가 생성됩니다. 서비스의 `GetFileContent` 메서드 호출은 메모리 내 캐시에서 파일 콘텐츠를 가져와 호출자( *Services/FileService.cs*)에게 반환하려고 합니다.

캐시 키를 사용하여 캐시된 콘텐츠를 찾을 수 없으면 다음 작업이 수행됩니다.

1. `GetFileContent`를 사용하여 파일 콘텐츠를 가져옵니다.
1. [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch)를 통해 파일 공급자로부터 변경 토큰을 가져옵니다. 파일이 수정될 때 토큰의 콜백이 트리거됩니다.
1. [상대(sliding) 만료](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) 기간과 함께 파일 콘텐츠가 캐시됩니다. 변경 토큰은 [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken)과 연결되어 파일이 캐시되는 동안 변경되면 캐시 항목을 제거합니다.

[!code-csharp[](change-tokens/sample/Services/FileService.cs?name=snippet1)]

`FileService`가 메모리 캐싱 서비스(*Startup.cs*)와 함께 서비스 컨테이너에 등록됩니다.

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet4)]

페이지 모델은 서비스(*Pages/Index.cshtml.cs*)를 사용하여 파일의 콘텐츠를 로드합니다.

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken 클래스

단일 개체에 있는 하나 이상의 `IChangeToken` 인스턴스를 나타내는 경우 [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken) 클래스를 사용합니다.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        { 
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

표시된 모든 토큰의 `HasChanged`가 `true`인 경우 복합 토큰의 `HasChanged`는 `true`를 보고합니다. 표시된 모든 토큰의 `ActiveChangeCallbacks`가 `true`인 경우 복합 토큰의 `ActiveChangeCallbacks`는 `true`를 보고합니다. 여러 동시 변경 이벤트가 발생하면 복합 변경 콜백이 정확히 한 번 호출됩니다.

## <a name="additional-resources"></a>추가 자료

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
