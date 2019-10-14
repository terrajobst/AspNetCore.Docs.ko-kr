---
title: ASP.NET Core에서 변경 토큰을 사용하여 변경 내용 검색
author: guardrex
description: 변경 토큰을 사용하여 변경 내용을 추적하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: bb30d7a4c7dc82200821c60a49c314b246562111
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007203"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="04b03-103">ASP.NET Core에서 변경 토큰을 사용하여 변경 내용 검색</span><span class="sxs-lookup"><span data-stu-id="04b03-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="04b03-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="04b03-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="04b03-105">*변경 토큰*은 상태 변경 내용을 추적하는 데 사용되는 범용의 하위 수준 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-105">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="04b03-106">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="04b03-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="04b03-107">IChangeToken 인터페이스</span><span class="sxs-lookup"><span data-stu-id="04b03-107">IChangeToken interface</span></span>

<span data-ttu-id="04b03-108"><xref:Microsoft.Extensions.Primitives.IChangeToken>은 변경이 발생했다는 알림을 전파합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="04b03-109">`IChangeToken`은 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 네임스페이스에 상주합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-109">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="04b03-110">[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 패키지는 ASP.NET Core 앱에 암시적으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-110">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="04b03-111">`IChangeToken`에는 두 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="04b03-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>는 토큰이 사전에 콜백을 발생시키는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="04b03-113">`ActiveChangedCallbacks`가 `false`로 설정된 경우 콜백은 호출되지 않고 앱은 변경에 대해 `HasChanged`를 폴링해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="04b03-114">또한 변경이 발생하지 않거나 기본 변경 리스너가 삭제되거나 사용하지 않도록 설정된 경우에도 토큰을 절대로 취소할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="04b03-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>는 변경이 발생했는지 나타내는 값을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="04b03-116">`IChangeToken` 인터페이스는 [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) 메서드를 포함하며 이는 토큰이 변경될 때 호출되는 콜백을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-116">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="04b03-117">`HasChanged`는 콜백이 호출되기 전에 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="04b03-118">ChangeToken 클래스</span><span class="sxs-lookup"><span data-stu-id="04b03-118">ChangeToken class</span></span>

<span data-ttu-id="04b03-119"><xref:Microsoft.Extensions.Primitives.ChangeToken>은 변경이 발생했다는 알림을 전파하는 데 사용되는 정적 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="04b03-120">`ChangeToken`은 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 네임스페이스에 상주합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-120">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="04b03-121">[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 패키지는 ASP.NET Core 앱에 암시적으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-121">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="04b03-122">[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 메서드는 토큰이 변경될 때마다 호출할 `Action`을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-122">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="04b03-123">`Func<IChangeToken>`은 출력을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="04b03-124">토큰이 변경될 때 `Action`이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="04b03-125">[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 오버로드는 토큰 소비자 `Action`에게 전달된 추가 `TState` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-125">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="04b03-126">`OnChange`는 <xref:System.IDisposable>을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-126">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="04b03-127"><xref:System.IDisposable.Dispose*>를 호출하면 토큰이 더 이상 변경 내용을 수신 대기하지 않고 토큰의 리소스가 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-127">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="04b03-128">ASP.NET Core에서 변경 토큰의 사용 예</span><span class="sxs-lookup"><span data-stu-id="04b03-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="04b03-129">변경 토큰은 ASP.NET Core에서 개체의 변경 내용을 모니터링하는 주요 영역에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-129">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="04b03-130">파일에 대한 변경을 모니터링하기 위해 <xref:Microsoft.Extensions.FileProviders.IFileProvider>의 <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 메서드는 지정된 파일 또는 조사할 폴더에 대해 `IChangeToken`을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-130">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="04b03-131">`IChangeToken` 토큰을 캐시 항목에 추가하여 변경 시 캐시 제거를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="04b03-132">`TOptions` 변경의 경우, <xref:Microsoft.Extensions.Options.IOptionsMonitor`1>의 기본 <xref:Microsoft.Extensions.Options.OptionsMonitor`1> 구현은 둘 이상의 <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> 인스턴스를 받아들이는 오버로드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-132">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="04b03-133">각 인스턴스는 옵션 변경을 추적하기 위해 변경 알림 콜백을 등록하도록 `IChangeToken`을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="04b03-134">구성 변경 모니터링</span><span class="sxs-lookup"><span data-stu-id="04b03-134">Monitor for configuration changes</span></span>

<span data-ttu-id="04b03-135">기본적으로 ASP.NET Core 템플릿은 [JSON 구성 파일](xref:fundamentals/configuration/index#json-configuration-provider)(*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*)을 사용하여 앱 구성 설정을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="04b03-136">이 파일은 `reloadOnChange` 매개 변수를 받아들이는 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>에 대해 [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 확장 메서드를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="04b03-137">`reloadOnChange`는 파일 변경 시 구성을 다시 로드해야 하는지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="04b03-138">이 설정은 <xref:Microsoft.Extensions.Hosting.Host> 편의 메서드 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-138">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="04b03-139">파일 기반 구성은 <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>로 표현됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-139">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="04b03-140">`FileConfigurationSource`는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>를 사용하여 파일을 모니터링합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-140">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="04b03-141">기본적으로 `IFileMonitor`는 <xref:System.IO.FileSystemWatcher>를 사용하여 구성 파일 변경을 모니터링하는 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-141">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="04b03-142">샘플 앱을 통해 구성 변경 모니터링을 위한 두 가지 구현을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="04b03-143">*appsettings* 파일 중 하나라도 변경되어 두 파일 모니터링 구현 모두 사용자 지정 코드를 실행하면 &mdash;샘플 앱은 콘솔에 메시지를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-143">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="04b03-144">구성 파일의 `FileSystemWatcher`는 단일 구성 파일 변경에 대해 토큰 콜백을 여러 개 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-144">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="04b03-145">여러 토큰 콜백이 활성화될 때 사용자 지정 코드가 한 번만 실행 되도록 하기 위해 샘플의 구현은 파일 해시를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-145">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="04b03-146">샘플은 SHA1 파일 해시를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-146">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="04b03-147">재시도는 지수 백오프로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-147">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="04b03-148">파일에서 새 해시를 일시적으로 컴퓨팅하지 못하게 하는 파일 잠금이 발생할 수 있으므로 재시도가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-148">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="04b03-149">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="04b03-149">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="04b03-150">단순 시작 변경 토큰</span><span class="sxs-lookup"><span data-stu-id="04b03-150">Simple startup change token</span></span>

<span data-ttu-id="04b03-151">변경 알림을 위한 토큰 소비자 `Action` 콜백을 구성 로드 토큰에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-151">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="04b03-152">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="04b03-152">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="04b03-153">`config.GetReloadToken()`은 토큰을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="04b03-154">콜백은 `InvokeChanged` 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="04b03-155">콜백의 `state`는 모니터링할 정확한 *appsettings*의 구성 파일을 지정하는 데 유용한 `IWebHostEnvironment`를 전달하는 데 사용됩니다(예를 들어 개발 환경에서는 *appsettings.Development.json*).</span><span class="sxs-lookup"><span data-stu-id="04b03-155">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="04b03-156">파일 해시는 구성 파일이 한 번만 변경된 경우 여러 토큰 콜백으로 인해 `WriteConsole` 문이 여러 번 실행되지 않도록 하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-156">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="04b03-157">이 시스템은 앱이 실행 중일 때 실행되며 사용자가 사용 중지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-157">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="04b03-158">서비스로 구성 변경 모니터링</span><span class="sxs-lookup"><span data-stu-id="04b03-158">Monitor configuration changes as a service</span></span>

<span data-ttu-id="04b03-159">이 샘플에서는 다음을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-159">The sample implements:</span></span>

* <span data-ttu-id="04b03-160">기본 시작 토큰 모니터링</span><span class="sxs-lookup"><span data-stu-id="04b03-160">Basic startup token monitoring.</span></span>
* <span data-ttu-id="04b03-161">서비스로 모니터링</span><span class="sxs-lookup"><span data-stu-id="04b03-161">Monitoring as a service.</span></span>
* <span data-ttu-id="04b03-162">모니터링을 사용 및 사용 안 함으로 설정하는 메커니즘</span><span class="sxs-lookup"><span data-stu-id="04b03-162">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="04b03-163">이 샘플은 `IConfigurationMonitor` 인터페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-163">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="04b03-164">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="04b03-164">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="04b03-165">구현된 클래스의 생성자인 `ConfigurationMonitor`는 변경 알림을 위한 콜백을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="04b03-166">`config.GetReloadToken()`은 토큰을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="04b03-167">`InvokeChanged`는 콜백 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="04b03-168">이 인스턴스의 `state`는 모니터링 상태에 액세스하는 데 사용되는 `IConfigurationMonitor` 인스턴스에 대한 참조입니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="04b03-169">두 개의 속성이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-169">Two properties are used:</span></span>

* <span data-ttu-id="04b03-170">`MonitoringEnabled` &ndash; 콜백이 사용자 지정 코드를 실행해야 하는지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-170">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="04b03-171">`CurrentState` &ndash; UI에서 사용하기 위해 현재 모니터링 상태를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-171">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="04b03-172">`InvokeChanged` 메서드는 다음을 제외하고 이전 방법과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="04b03-173">`MonitoringEnabled`가 `true`가 아닌 경우 해당 코드를 실행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="04b03-174">`WriteConsole` 출력의 현재 `state`를 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-174">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="04b03-175">인스턴스 `ConfigurationMonitor`는 `Startup.ConfigureServices`에서 서비스로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-175">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="04b03-176">인덱스 페이지에서는 구성 모니터링을 통해 사용자 컨트롤을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="04b03-177">`IConfigurationMonitor`의 인스턴스는 `IndexModel`에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="04b03-178">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="04b03-178">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="04b03-179">구성 모니터(`_monitor`)는 모니터링을 사용 설정 또는 해제하고 UI 피드백에 대한 현재 상태를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-179">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="04b03-180">`OnPostStartMonitoring`이 트리거되면 모니터링을 사용하도록 설정하고 현재 상태가 지워집니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-180">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="04b03-181">`OnPostStopMonitoring`이 트리거되면 모니터링을 사용하지 않도록 설정하고 모니터링이 발생하지 않음을 반영하도록 상태를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-181">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="04b03-182">UI의 단추로 모니터링을 사용 설정 및 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-182">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="04b03-183">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="04b03-183">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="04b03-184">캐시된 파일 변경 모니터링</span><span class="sxs-lookup"><span data-stu-id="04b03-184">Monitor cached file changes</span></span>

<span data-ttu-id="04b03-185">파일 콘텐츠는 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>를 사용하여 메모리 내에 캐시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-185">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="04b03-186">메모리 내 캐싱은 [메모리 내 캐시](xref:performance/caching/memory) 토픽에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-186">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="04b03-187">아래에 설명된 구현과 같은 추가 단계를 수행하지 않으면 소스 데이터가 변경될 경우 캐시에서 *부실*(오래된) 데이터가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-187">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="04b03-188">예를 들어 [상대(sliding) 만료](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) 기간을 갱신할 때 캐시된 소스 파일의 상태를 고려하지 않으면 부실 캐시 데이터가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-188">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="04b03-189">데이터에 대한 각 요청은 상대(sliding) 만료 기간을 갱신하지만 파일은 캐시에 다시 로드되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-189">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="04b03-190">파일의 캐시된 콘텐츠를 사용하는 모든 앱 기능은 부실 콘텐츠를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-190">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="04b03-191">파일 캐싱 시나리오에서 변경 토큰을 사용하면 캐시에 부실 파일 콘텐츠가 생기는 것을 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-191">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="04b03-192">샘플 앱에서 이러한 방법의 구현을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-192">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="04b03-193">이 샘플에서는 `GetFileContent`를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-193">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="04b03-194">파일 콘텐츠를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-194">Return file content.</span></span>
* <span data-ttu-id="04b03-195">파일 잠금으로 인해 일시적으로 파일을 읽을 수 없는 경우를 처리하기 위해 지수 백오프를 사용하여 재시도 알고리즘을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-195">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="04b03-196">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="04b03-196">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="04b03-197">캐시된 파일 조회를 처리하기 위해 `FileService`가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-197">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="04b03-198">서비스의 `GetFileContent` 메서드 호출은 메모리 내 캐시에서 파일 콘텐츠를 가져와 호출자( *Services/FileService.cs*)에게 반환하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-198">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="04b03-199">캐시 키를 사용하여 캐시된 콘텐츠를 찾을 수 없으면 다음 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-199">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="04b03-200">`GetFileContent`를 사용하여 파일 콘텐츠를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-200">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="04b03-201">[IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)를 통해 파일 공급자로부터 변경 토큰을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-201">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="04b03-202">파일이 수정될 때 토큰의 콜백이 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-202">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="04b03-203">[상대(sliding) 만료](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) 기간과 함께 파일 콘텐츠가 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-203">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="04b03-204">변경 토큰은 [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*)과 연결되어 파일이 캐시되는 동안 변경되면 캐시 항목을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-204">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="04b03-205">다음 예제에서 파일은 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root)에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-205">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="04b03-206">`IWebHostEnvironment.ContentRootFileProvider`는 앱의 `IWebHostEnvironment.ContentRootPath`를 가리키는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>를 가져오는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-206">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="04b03-207">`filePath`는 [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-207">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="04b03-208">`FileService`가 메모리 캐싱 서비스와 함께 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-208">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="04b03-209">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="04b03-209">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="04b03-210">페이지 모델은 서비스를 사용하여 파일의 콘텐츠를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-210">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="04b03-211">인덱스 페이지의 `OnGet` 메서드(*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="04b03-211">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="04b03-212">CompositeChangeToken 클래스</span><span class="sxs-lookup"><span data-stu-id="04b03-212">CompositeChangeToken class</span></span>

<span data-ttu-id="04b03-213">단일 개체에 있는 하나 이상의 `IChangeToken` 인스턴스를 나타내는 경우 <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-213">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="04b03-214">표시된 모든 토큰의 `HasChanged`가 `true`인 경우 복합 토큰의 `HasChanged`는 `true`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-214">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="04b03-215">표시된 모든 토큰의 `ActiveChangeCallbacks`가 `true`인 경우 복합 토큰의 `ActiveChangeCallbacks`는 `true`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-215">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="04b03-216">여러 동시 변경 이벤트가 발생하면 복합 변경 콜백이 한 번 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-216">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="04b03-217">*변경 토큰*은 상태 변경 내용을 추적하는 데 사용되는 범용의 하위 수준 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-217">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="04b03-218">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="04b03-218">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="04b03-219">IChangeToken 인터페이스</span><span class="sxs-lookup"><span data-stu-id="04b03-219">IChangeToken interface</span></span>

<span data-ttu-id="04b03-220"><xref:Microsoft.Extensions.Primitives.IChangeToken>은 변경이 발생했다는 알림을 전파합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-220"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="04b03-221">`IChangeToken`은 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 네임스페이스에 상주합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-221">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="04b03-222">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 사용하지 않는 앱의 경우 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 패키지에 대한 패키지 참조를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-222">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="04b03-223">`IChangeToken`에는 두 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-223">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="04b03-224"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>는 토큰이 사전에 콜백을 발생시키는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-224"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="04b03-225">`ActiveChangedCallbacks`가 `false`로 설정된 경우 콜백은 호출되지 않고 앱은 변경에 대해 `HasChanged`를 폴링해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-225">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="04b03-226">또한 변경이 발생하지 않거나 기본 변경 리스너가 삭제되거나 사용하지 않도록 설정된 경우에도 토큰을 절대로 취소할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-226">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="04b03-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>는 변경이 발생했는지 나타내는 값을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="04b03-228">`IChangeToken` 인터페이스는 [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) 메서드를 포함하며 이는 토큰이 변경될 때 호출되는 콜백을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-228">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="04b03-229">`HasChanged`는 콜백이 호출되기 전에 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-229">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="04b03-230">ChangeToken 클래스</span><span class="sxs-lookup"><span data-stu-id="04b03-230">ChangeToken class</span></span>

<span data-ttu-id="04b03-231"><xref:Microsoft.Extensions.Primitives.ChangeToken>은 변경이 발생했다는 알림을 전파하는 데 사용되는 정적 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-231"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="04b03-232">`ChangeToken`은 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 네임스페이스에 상주합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-232">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="04b03-233">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 사용하지 않는 앱의 경우 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 패키지에 대한 패키지 참조를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-233">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="04b03-234">[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 메서드는 토큰이 변경될 때마다 호출할 `Action`을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-234">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="04b03-235">`Func<IChangeToken>`은 출력을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-235">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="04b03-236">토큰이 변경될 때 `Action`이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-236">`Action` is called when the token changes.</span></span>

<span data-ttu-id="04b03-237">[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 오버로드는 토큰 소비자 `Action`에게 전달된 추가 `TState` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-237">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="04b03-238">`OnChange`는 <xref:System.IDisposable>을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-238">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="04b03-239"><xref:System.IDisposable.Dispose*>를 호출하면 토큰이 더 이상 변경 내용을 수신 대기하지 않고 토큰의 리소스가 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-239">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="04b03-240">ASP.NET Core에서 변경 토큰의 사용 예</span><span class="sxs-lookup"><span data-stu-id="04b03-240">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="04b03-241">변경 토큰은 ASP.NET Core에서 개체의 변경 내용을 모니터링하는 주요 영역에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-241">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="04b03-242">파일에 대한 변경을 모니터링하기 위해 <xref:Microsoft.Extensions.FileProviders.IFileProvider>의 <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 메서드는 지정된 파일 또는 조사할 폴더에 대해 `IChangeToken`을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-242">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="04b03-243">`IChangeToken` 토큰을 캐시 항목에 추가하여 변경 시 캐시 제거를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-243">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="04b03-244">`TOptions` 변경의 경우, <xref:Microsoft.Extensions.Options.IOptionsMonitor`1>의 기본 <xref:Microsoft.Extensions.Options.OptionsMonitor`1> 구현은 둘 이상의 <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> 인스턴스를 받아들이는 오버로드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-244">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="04b03-245">각 인스턴스는 옵션 변경을 추적하기 위해 변경 알림 콜백을 등록하도록 `IChangeToken`을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-245">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="04b03-246">구성 변경 모니터링</span><span class="sxs-lookup"><span data-stu-id="04b03-246">Monitor for configuration changes</span></span>

<span data-ttu-id="04b03-247">기본적으로 ASP.NET Core 템플릿은 [JSON 구성 파일](xref:fundamentals/configuration/index#json-configuration-provider)(*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*)을 사용하여 앱 구성 설정을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-247">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="04b03-248">이 파일은 `reloadOnChange` 매개 변수를 받아들이는 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>에 대해 [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 확장 메서드를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-248">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="04b03-249">`reloadOnChange`는 파일 변경 시 구성을 다시 로드해야 하는지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-249">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="04b03-250">이 설정은 <xref:Microsoft.AspNetCore.WebHost> 편의 메서드 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-250">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="04b03-251">파일 기반 구성은 <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>로 표현됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-251">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="04b03-252">`FileConfigurationSource`는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>를 사용하여 파일을 모니터링합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-252">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="04b03-253">기본적으로 `IFileMonitor`는 <xref:System.IO.FileSystemWatcher>를 사용하여 구성 파일 변경을 모니터링하는 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-253">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="04b03-254">샘플 앱을 통해 구성 변경 모니터링을 위한 두 가지 구현을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-254">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="04b03-255">*appsettings* 파일 중 하나라도 변경되어 두 파일 모니터링 구현 모두 사용자 지정 코드를 실행하면 &mdash;샘플 앱은 콘솔에 메시지를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-255">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="04b03-256">구성 파일의 `FileSystemWatcher`는 단일 구성 파일 변경에 대해 토큰 콜백을 여러 개 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-256">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="04b03-257">여러 토큰 콜백이 활성화될 때 사용자 지정 코드가 한 번만 실행 되도록 하기 위해 샘플의 구현은 파일 해시를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-257">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="04b03-258">샘플은 SHA1 파일 해시를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-258">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="04b03-259">재시도는 지수 백오프로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-259">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="04b03-260">파일에서 새 해시를 일시적으로 컴퓨팅하지 못하게 하는 파일 잠금이 발생할 수 있으므로 재시도가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-260">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="04b03-261">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="04b03-261">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="04b03-262">단순 시작 변경 토큰</span><span class="sxs-lookup"><span data-stu-id="04b03-262">Simple startup change token</span></span>

<span data-ttu-id="04b03-263">변경 알림을 위한 토큰 소비자 `Action` 콜백을 구성 로드 토큰에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-263">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="04b03-264">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="04b03-264">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="04b03-265">`config.GetReloadToken()`은 토큰을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-265">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="04b03-266">콜백은 `InvokeChanged` 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-266">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="04b03-267">콜백의 `state`는 모니터링할 정확한 *appsettings*의 구성 파일을 지정하는 데 유용한 `IHostingEnvironment`를 전달하는 데 사용됩니다(예를 들어 개발 환경에서는 *appsettings.Development.json*).</span><span class="sxs-lookup"><span data-stu-id="04b03-267">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="04b03-268">파일 해시는 구성 파일이 한 번만 변경된 경우 여러 토큰 콜백으로 인해 `WriteConsole` 문이 여러 번 실행되지 않도록 하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-268">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="04b03-269">이 시스템은 앱이 실행 중일 때 실행되며 사용자가 사용 중지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-269">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="04b03-270">서비스로 구성 변경 모니터링</span><span class="sxs-lookup"><span data-stu-id="04b03-270">Monitor configuration changes as a service</span></span>

<span data-ttu-id="04b03-271">이 샘플에서는 다음을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-271">The sample implements:</span></span>

* <span data-ttu-id="04b03-272">기본 시작 토큰 모니터링</span><span class="sxs-lookup"><span data-stu-id="04b03-272">Basic startup token monitoring.</span></span>
* <span data-ttu-id="04b03-273">서비스로 모니터링</span><span class="sxs-lookup"><span data-stu-id="04b03-273">Monitoring as a service.</span></span>
* <span data-ttu-id="04b03-274">모니터링을 사용 및 사용 안 함으로 설정하는 메커니즘</span><span class="sxs-lookup"><span data-stu-id="04b03-274">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="04b03-275">이 샘플은 `IConfigurationMonitor` 인터페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-275">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="04b03-276">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="04b03-276">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="04b03-277">구현된 클래스의 생성자인 `ConfigurationMonitor`는 변경 알림을 위한 콜백을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-277">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="04b03-278">`config.GetReloadToken()`은 토큰을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-278">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="04b03-279">`InvokeChanged`는 콜백 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-279">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="04b03-280">이 인스턴스의 `state`는 모니터링 상태에 액세스하는 데 사용되는 `IConfigurationMonitor` 인스턴스에 대한 참조입니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-280">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="04b03-281">두 개의 속성이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-281">Two properties are used:</span></span>

* <span data-ttu-id="04b03-282">`MonitoringEnabled` &ndash; 콜백이 사용자 지정 코드를 실행해야 하는지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-282">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="04b03-283">`CurrentState` &ndash; UI에서 사용하기 위해 현재 모니터링 상태를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-283">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="04b03-284">`InvokeChanged` 메서드는 다음을 제외하고 이전 방법과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-284">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="04b03-285">`MonitoringEnabled`가 `true`가 아닌 경우 해당 코드를 실행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-285">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="04b03-286">`WriteConsole` 출력의 현재 `state`를 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-286">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="04b03-287">인스턴스 `ConfigurationMonitor`는 `Startup.ConfigureServices`에서 서비스로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-287">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="04b03-288">인덱스 페이지에서는 구성 모니터링을 통해 사용자 컨트롤을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-288">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="04b03-289">`IConfigurationMonitor`의 인스턴스는 `IndexModel`에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-289">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="04b03-290">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="04b03-290">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="04b03-291">구성 모니터(`_monitor`)는 모니터링을 사용 설정 또는 해제하고 UI 피드백에 대한 현재 상태를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-291">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="04b03-292">`OnPostStartMonitoring`이 트리거되면 모니터링을 사용하도록 설정하고 현재 상태가 지워집니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-292">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="04b03-293">`OnPostStopMonitoring`이 트리거되면 모니터링을 사용하지 않도록 설정하고 모니터링이 발생하지 않음을 반영하도록 상태를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-293">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="04b03-294">UI의 단추로 모니터링을 사용 설정 및 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-294">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="04b03-295">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="04b03-295">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="04b03-296">캐시된 파일 변경 모니터링</span><span class="sxs-lookup"><span data-stu-id="04b03-296">Monitor cached file changes</span></span>

<span data-ttu-id="04b03-297">파일 콘텐츠는 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>를 사용하여 메모리 내에 캐시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-297">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="04b03-298">메모리 내 캐싱은 [메모리 내 캐시](xref:performance/caching/memory) 토픽에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-298">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="04b03-299">아래에 설명된 구현과 같은 추가 단계를 수행하지 않으면 소스 데이터가 변경될 경우 캐시에서 *부실*(오래된) 데이터가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-299">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="04b03-300">예를 들어 [상대(sliding) 만료](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) 기간을 갱신할 때 캐시된 소스 파일의 상태를 고려하지 않으면 부실 캐시 데이터가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-300">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="04b03-301">데이터에 대한 각 요청은 상대(sliding) 만료 기간을 갱신하지만 파일은 캐시에 다시 로드되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-301">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="04b03-302">파일의 캐시된 콘텐츠를 사용하는 모든 앱 기능은 부실 콘텐츠를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-302">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="04b03-303">파일 캐싱 시나리오에서 변경 토큰을 사용하면 캐시에 부실 파일 콘텐츠가 생기는 것을 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-303">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="04b03-304">샘플 앱에서 이러한 방법의 구현을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-304">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="04b03-305">이 샘플에서는 `GetFileContent`를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-305">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="04b03-306">파일 콘텐츠를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-306">Return file content.</span></span>
* <span data-ttu-id="04b03-307">파일 잠금으로 인해 일시적으로 파일을 읽을 수 없는 경우를 처리하기 위해 지수 백오프를 사용하여 재시도 알고리즘을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-307">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="04b03-308">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="04b03-308">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="04b03-309">캐시된 파일 조회를 처리하기 위해 `FileService`가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-309">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="04b03-310">서비스의 `GetFileContent` 메서드 호출은 메모리 내 캐시에서 파일 콘텐츠를 가져와 호출자( *Services/FileService.cs*)에게 반환하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-310">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="04b03-311">캐시 키를 사용하여 캐시된 콘텐츠를 찾을 수 없으면 다음 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-311">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="04b03-312">`GetFileContent`를 사용하여 파일 콘텐츠를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-312">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="04b03-313">[IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)를 통해 파일 공급자로부터 변경 토큰을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-313">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="04b03-314">파일이 수정될 때 토큰의 콜백이 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-314">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="04b03-315">[상대(sliding) 만료](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) 기간과 함께 파일 콘텐츠가 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-315">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="04b03-316">변경 토큰은 [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*)과 연결되어 파일이 캐시되는 동안 변경되면 캐시 항목을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-316">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="04b03-317">다음 예제에서 파일은 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root)에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-317">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="04b03-318">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider)는 앱의 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>를 가리키는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>를 가져오는 데 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-318">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="04b03-319">`filePath`는 [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath)를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-319">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="04b03-320">`FileService`가 메모리 캐싱 서비스와 함께 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-320">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="04b03-321">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="04b03-321">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="04b03-322">페이지 모델은 서비스를 사용하여 파일의 콘텐츠를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-322">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="04b03-323">인덱스 페이지의 `OnGet` 메서드(*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="04b03-323">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="04b03-324">CompositeChangeToken 클래스</span><span class="sxs-lookup"><span data-stu-id="04b03-324">CompositeChangeToken class</span></span>

<span data-ttu-id="04b03-325">단일 개체에 있는 하나 이상의 `IChangeToken` 인스턴스를 나타내는 경우 <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-325">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="04b03-326">표시된 모든 토큰의 `HasChanged`가 `true`인 경우 복합 토큰의 `HasChanged`는 `true`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-326">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="04b03-327">표시된 모든 토큰의 `ActiveChangeCallbacks`가 `true`인 경우 복합 토큰의 `ActiveChangeCallbacks`는 `true`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-327">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="04b03-328">여러 동시 변경 이벤트가 발생하면 복합 변경 콜백이 한 번 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="04b03-328">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="04b03-329">추가 자료</span><span class="sxs-lookup"><span data-stu-id="04b03-329">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
