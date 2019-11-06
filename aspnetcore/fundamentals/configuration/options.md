---
title: ASP.NET Core의 옵션 패턴
author: guardrex
description: 옵션 패턴을 사용하여 ASP.NET Core 앱에서 관련된 설정 그룹을 나타내는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/28/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: f9e94e8d1736b7ffaa2640aba03da6b239a34f0a
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034022"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="6ff61-103">ASP.NET Core의 옵션 패턴</span><span class="sxs-lookup"><span data-stu-id="6ff61-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="6ff61-104">작성자: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6ff61-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6ff61-105">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="6ff61-106">[구성 설정](xref:fundamentals/configuration/index)이 시나리오에 따라 별도 클래스로 격리된 경우 앱은 두 가지 중요한 소프트웨어 엔지니어링 원칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="6ff61-107">[ISP(Interface Segregation Principle, 인터페이스 분리 원칙) 또는 캡슐화](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; 구성 설정에 종속된 시나리오(클래스)는 사용하는 구성 설정에 의해서만 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="6ff61-108">[관심사의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; 앱의 다른 부분에 대한 설정은 다른 설정에 종속되거나 연결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="6ff61-109">옵션은 구성 데이터의 유효성을 검사하는 메커니즘도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="6ff61-110">자세한 내용은 [옵션 유효성 검사](#options-validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ff61-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="6ff61-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ff61-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="6ff61-112">패키지</span><span class="sxs-lookup"><span data-stu-id="6ff61-112">Package</span></span>

<span data-ttu-id="6ff61-113">[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 패키지는 ASP.NET Core 앱에서 암시적으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-113">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="6ff61-114">옵션 인터페이스</span><span class="sxs-lookup"><span data-stu-id="6ff61-114">Options interfaces</span></span>

<span data-ttu-id="6ff61-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>는 옵션을 검색하고 `TOptions` 인스턴스에 대한 옵션 알림을 관리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="6ff61-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>은 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="6ff61-117">변경 알림</span><span class="sxs-lookup"><span data-stu-id="6ff61-117">Change notifications</span></span>
* [<span data-ttu-id="6ff61-118">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="6ff61-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="6ff61-119">다시 로드할 수 있는 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="6ff61-120">선택적 옵션 무효화(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="6ff61-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="6ff61-121">[사후 구성](#options-post-configuration) 시나리오에서는 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후 옵션을 설정하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="6ff61-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>는 새로운 옵션 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="6ff61-123">단일 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="6ff61-124">기본 구현에서는 등록된 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 및 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하며 먼저 구성을 모두 실행한 다음, 사후 구성을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="6ff61-125"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>와 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>를 구별하며 적절한 인터페이스만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="6ff61-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `TOptions` 인스턴스를 캐시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="6ff61-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 모니터의 옵션 인스턴스를 무효화하므로 값이 다시 계산됩니다(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="6ff61-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="6ff61-128"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>를 사용하여 값을 수동으로 도입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="6ff61-129">모든 명명된 인스턴스를 필요에 따라 다시 만들어야 하는 경우 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="6ff61-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 모든 요청에서 옵션을 다시 계산해야 하는 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="6ff61-131">자세한 내용은 [IOptionsSnapshot을 사용하여 구성 데이터 다시 로드](#reload-configuration-data-with-ioptionssnapshot)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ff61-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="6ff61-132"><xref:Microsoft.Extensions.Options.IOptions%601>를 사용하여 옵션을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="6ff61-133">그러나 <xref:Microsoft.Extensions.Options.IOptions%601>는 앞에서 설명한 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>의 시나리오를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="6ff61-134"><xref:Microsoft.Extensions.Options.IOptions%601> 인터페이스를 이미 사용하고 있으며 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 제공하는 시나리오가 필요하지 않은 기존 프레임워크 및 라이브러리에서는 <xref:Microsoft.Extensions.Options.IOptions%601>를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="6ff61-135">일반 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-135">General options configuration</span></span>

<span data-ttu-id="6ff61-136">일반 옵션 구성은 샘플 앱에 예제 &num;1로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-136">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="6ff61-137">옵션 클래스는 매개 변수가 없는 public 생성자를 사용하는 비추상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="6ff61-138">다음 클래스 `MyOptions`에는 `Option1` 및 `Option2`의 두 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="6ff61-139">기본 값 설정은 선택 사항이지만, 다음 예제의 클래스 생성자는 `Option1`의 기본값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="6ff61-140">`Option2`에는 직접 속성을 초기화하여 설정된 기본값이 있습니다(*Models/MyOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="6ff61-141">`MyOptions` 클래스는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="6ff61-142">다음 페이지 모델은 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 통해 [생성자 종속성 주입](xref:mvc/controllers/dependency-injection)을 사용하여 설정에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="6ff61-143">샘플의 *appsettings.json* 파일은 `option1` 및 `option2`에 대한 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="6ff61-144">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="6ff61-145">사용자 지정 <xref:System.Configuration.ConfigurationBuilder>를 사용하여 설정 파일에서 옵션 구성을 로드하는 경우 기본 경로가 올바르게 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="6ff61-146"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 통해 설정 파일에서 옵션 구성을 로드하는 경우에는 명시적으로 기본 경로를 설정하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="6ff61-147">대리자로 간단한 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="6ff61-148">대리자를 사용한 간단한 옵션 구성은 샘플 앱에 예제 &num;2로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-148">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="6ff61-149">대리자를 사용하여 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-149">Use a delegate to set options values.</span></span> <span data-ttu-id="6ff61-150">샘플 앱에서는 `MyOptionsWithDelegateConfig` 클래스(*Models/MyOptionsWithDelegateConfig.cs*)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="6ff61-151">다음 코드에서 두 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="6ff61-152">`MyOptionsWithDelegateConfig`를 통해 바인딩을 구성하는 데 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="6ff61-153">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="6ff61-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="6ff61-154">여러 구성 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="6ff61-155">구성 공급자는 NuGet 패키지에서 사용할 수 있으며 등록된 순서대로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="6ff61-156">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ff61-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="6ff61-157"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*>를 호출할 때마다 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="6ff61-158">위의 예제에서는 `Option1` 및 `Option2`의 값은 모두 *appsettings.json*에서 지정되지만, `Option1` 및 `Option2`의 값은 구성된 대리자에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="6ff61-159">둘 이상의 구성 서비스를 활성화한 경우 마지막 지정된 구성 소스가 *승리*하고 구성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="6ff61-160">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="6ff61-161">하위 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-161">Suboptions configuration</span></span>

<span data-ttu-id="6ff61-162">하위 옵션 구성은 샘플 앱에 예제 &num;3으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-162">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="6ff61-163">앱은 앱의 특정 시나리오 그룹(클래스)에 적합한 옵션 클래스를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="6ff61-164">구성 값을 필요로 하는 앱의 부분은 사용하는 구성 값에만 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="6ff61-165">옵션을 구성에 바인딩하는 경우 옵션 형식의 각 속성은 `property[:sub-property:]` 양식의 구성 키에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="6ff61-166">예를 들어 `MyOptions.Option1` 속성은 키 `Option1`에 바인딩되어 *appsettings.json*의 `option1` 속성에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="6ff61-167">다음 코드에서 세 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="6ff61-168">이 코드는 `MySubOptions`를 *appsettings.json* 파일의 `subsection` 섹션에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="6ff61-169">`GetSection` 메서드를 사용하려면 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 네임스페이스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-169">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="6ff61-170">샘플의 *appsettings.json* 파일은 `suboption1` 및 `suboption2`에 대한 키로 `subsection` 멤버를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-170">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="6ff61-171">`MySubOptions` 클래스는 `SubOption1` 및 `SubOption2` 속성을 정의하여 옵션 값을 유지합니다(*Models/MySubOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-171">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="6ff61-172">페이지 모델의 `OnGet` 메서드는 옵션 값이 포함된 문자열을 반환합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-172">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="6ff61-173">앱을 실행할 때 `OnGet` 메서드는 하위 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-173">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="6ff61-174">직접 보기 주입 또는 보기 모델에 의해 제공되는 옵션</span><span class="sxs-lookup"><span data-stu-id="6ff61-174">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="6ff61-175">직접 보기 주입 또는 보기 모델에 의해 제공되는 옵션은 샘플 앱에 예제 &num;4로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-175">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="6ff61-176">옵션은 뷰 모델 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 보기에 직접 주입하여 제공할 수 있습니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-176">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="6ff61-177">샘플 앱은 `@inject` 지시문을 사용하여 `IOptionsMonitor<MyOptions>`를 주입하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-177">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="6ff61-178">앱을 실행하면 옵션 값이 렌더링된 페이지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-178">When the app is run, the options values are shown in the rendered page:</span></span>

![옵션 값 Option1: value1_from_json 및 Option2: -1은 모델에서 로드되며 보기에 주입됩니다.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="6ff61-180">IOptionsSnapshot을 사용하여 구성 데이터 다시 로드</span><span class="sxs-lookup"><span data-stu-id="6ff61-180">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="6ff61-181"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하여 구성 데이터를 다시 로드하는 방법은 샘플 앱에 예제 &num;5로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-181">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="6ff61-182"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 최소한의 처리 오버헤드로 옵션 다시 로드를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-182"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="6ff61-183">옵션은 엑세스될 때 요청 당 한 번씩 계산되고 요청의 수명 동안 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-183">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="6ff61-184">다음 예제에는 *appsettings.json*이 변경된 후 새 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 만드는 방법이 설명되어 있습니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-184">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="6ff61-185">서버에 대한 여러 요청은 파일이 변경되고 구성이 다시 로드될 때까지 *appsettings.json* 파일에서 제공하는 상수 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-185">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="6ff61-186">다음 이미지는 *appsettings.json* 파일에서 로드된 초기 `option1` 및 `option2` 값을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-186">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="6ff61-187">*appsettings.json* 파일의 값을 `value1_from_json UPDATED` 및 `200`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-187">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="6ff61-188">*appsettings.json* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-188">Save the *appsettings.json* file.</span></span> <span data-ttu-id="6ff61-189">옵션 값이 변경되었음을 확인하려면 브라우저를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-189">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="6ff61-190">IConfigureNamedOptions로 명명된 옵션 지원</span><span class="sxs-lookup"><span data-stu-id="6ff61-190">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="6ff61-191"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>로 명명된 옵션 지원은 샘플 앱에 예제 &num;6으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-191">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="6ff61-192">앱은 *명명된 옵션* 지원을 통해 명명된 옵션 구성들을 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-192">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="6ff61-193">샘플 앱에서 명명된 옵션은 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 확장 메서드를 호출하는 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)를 사용하여 선언됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-193">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="6ff61-194">샘플 앱은 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*>을 사용하여 명명된 옵션에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-194">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="6ff61-195">샘플 앱을 실행하면 명명된 옵션이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-195">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="6ff61-196">`named_options_1` 값은 *appsettings.json* 파일에서 로드되는 구성에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-196">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="6ff61-197">`named_options_2` 값은 다음에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-197">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="6ff61-198">`Option1`에 대한 `ConfigureServices`의 `named_options_2` 대리자.</span><span class="sxs-lookup"><span data-stu-id="6ff61-198">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="6ff61-199">`MyOptions` 클래스에서 제공되는 `Option2`에 대한 기본값.</span><span class="sxs-lookup"><span data-stu-id="6ff61-199">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="6ff61-200">ConfigureAll 메서드를 사용하여 모든 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-200">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="6ff61-201"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 메서드를 사용하여 모든 옵션 인스턴스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-201">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="6ff61-202">다음 코드는 모든 구성 인스턴스에 대해 `Option1`을 공통 값으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-202">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="6ff61-203">다음 코드를 `Startup.ConfigureServices` 메서드에 직접 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-203">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="6ff61-204">코드를 추가한 후 샘플 앱을 실행하면 다음과 같은 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-204">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="6ff61-205">모든 옵션은 명명된 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-205">All options are named instances.</span></span> <span data-ttu-id="6ff61-206">기존 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 인스턴스는 `string.Empty`인 `Options.DefaultName` 인스턴스를 대상으로 지정한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-206">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="6ff61-207"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>는 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>도 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-207"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="6ff61-208"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>의 기본 구현에는 각 옵션을 적절하게 사용하기 위한 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-208">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="6ff61-209">`null` 명명된 옵션은 특정 명명된 인스턴스 대신 모든 명명된 인스턴스를 대상으로 지정하는 데 사용됩니다(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 및 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>에서 이 규칙을 사용함).</span><span class="sxs-lookup"><span data-stu-id="6ff61-209">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="6ff61-210">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="6ff61-210">OptionsBuilder API</span></span>

<span data-ttu-id="6ff61-211"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>는 `TOptions` 인스턴스를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-211"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="6ff61-212">`OptionsBuilder`는 `AddOptions<TOptions>(string optionsName)` 호출에 대한 단일 매개 변수이므로 모든 후속 호출에 나타나는 대신 명명된 옵션 생성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-212">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="6ff61-213">옵션 유효성 검사 및 서비스 종속성을 허용하는 `ConfigureOptions` 오버로드는 `OptionsBuilder`를 통해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-213">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="6ff61-214">DI 서비스를 사용하여 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-214">Use DI services to configure options</span></span>

<span data-ttu-id="6ff61-215">다음과 같은 두 가지 방법으로 옵션을 구성하는 동안 종속성 주입을 통해 다른 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-215">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="6ff61-216">구성 대리자를 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)의 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-216">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="6ff61-217">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)는 최대 5개의 서비스를 사용하여 옵션을 구성할 수 있는 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)의 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-217">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="6ff61-218"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 또는 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>를 구현하는 고유의 형식을 만들고 해당 형식을 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-218">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="6ff61-219">서비스를 만드는 것이 더 복잡하기 때문에 구성 대리자를 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-219">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="6ff61-220">고유의 형식을 만드는 것은 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)를 사용할 때 프레임워크가 수행하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-220">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="6ff61-221">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)을 호출하면 지정된 일반 서비스 유형을 허용하는 생성자가 있는 일시적인 일반 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-221">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="6ff61-222">옵션 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="6ff61-222">Options validation</span></span>

<span data-ttu-id="6ff61-223">옵션 유효성 검사를 사용하면 옵션이 구성될 때 옵션의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-223">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="6ff61-224">옵션이 유효하면 `true`를 반환하고 옵션이 유효하지 않으면 `false`를 반환하는 유효성 검사 메서드로 `Validate`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-224">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="6ff61-225">위의 예제에서는 명명된 옵션 인스턴스를 `optionalOptionsName`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-225">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="6ff61-226">기본 옵션 인스턴스는 `Options.DefaultName`입니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-226">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="6ff61-227">유효성 검사는 옵션 인스턴스가 만들어지면 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-227">Validation runs when the options instance is created.</span></span> <span data-ttu-id="6ff61-228">옵션 인스턴스는 처음 액세스되면 유효성 검사를 통과하도록 보장됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-228">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6ff61-229">옵션 유효성 검사는 옵션이 처음 구성되고 유효성이 검사된 후 옵션 수정에 대해 보호하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-229">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="6ff61-230">`Validate` 메서드는 `Func<TOptions, bool>`을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-230">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="6ff61-231">유효성 검사를 완전히 사용자 지정하려면 다음을 허용하는 `IValidateOptions<TOptions>`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-231">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="6ff61-232">여러 옵션 형식의 유효성 검사: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="6ff61-232">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="6ff61-233">다른 옵션 형식에 의존하는 유효성 검사: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="6ff61-233">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="6ff61-234">`IValidateOptions`는 다음의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-234">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="6ff61-235">특정 명명된 옵션 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="6ff61-235">A specific named options instance.</span></span>
* <span data-ttu-id="6ff61-236">`name`이 `null`인 경우 모든 옵션.</span><span class="sxs-lookup"><span data-stu-id="6ff61-236">All options when `name` is `null`.</span></span>

<span data-ttu-id="6ff61-237">인터페이스의 구현에서 `ValidateOptionsResult`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-237">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="6ff61-238">데이터 주석 기반 유효성 검사는 `OptionsBuilder<TOptions>`의 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> 메서드를 호출하여 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-238">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="6ff61-239">`Microsoft.Extensions.Options.DataAnnotations`는 ASP.NET Core 앱에서 암시적으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-239">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="6ff61-240">즉시 유효성 검사(시작 시 빠른 실패)는 향후 릴리스에서 고려 중입니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-240">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="6ff61-241">옵션 사후 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-241">Options post-configuration</span></span>

<span data-ttu-id="6ff61-242"><xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하여 사후 구성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-242">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="6ff61-243">사후 구성은 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-243">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="6ff61-244"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>를 사용하여 명명된 옵션을 사후 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-244"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="6ff61-245">모든 구성 인스턴스를 사후 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-245">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="6ff61-246">시작하는 동안 옵션 액세스</span><span class="sxs-lookup"><span data-stu-id="6ff61-246">Accessing options during startup</span></span>

<span data-ttu-id="6ff61-247">`Configure` 메서드가 실행되기 전에 서비스가 만들어지므로 `Startup.Configure`에서 <xref:Microsoft.Extensions.Options.IOptions%601> 및 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-247"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="6ff61-248">`Startup.ConfigureServices`에서는 <xref:Microsoft.Extensions.Options.IOptions%601> 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="6ff61-248">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6ff61-249">서비스 등록의 순서 지정으로 인해 일관성 없는 옵션 상태가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-249">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6ff61-250">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-250">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="6ff61-251">[구성 설정](xref:fundamentals/configuration/index)이 시나리오에 따라 별도 클래스로 격리된 경우 앱은 두 가지 중요한 소프트웨어 엔지니어링 원칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-251">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="6ff61-252">[ISP(Interface Segregation Principle, 인터페이스 분리 원칙) 또는 캡슐화](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; 구성 설정에 종속된 시나리오(클래스)는 사용하는 구성 설정에 의해서만 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-252">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="6ff61-253">[관심사의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; 앱의 다른 부분에 대한 설정은 다른 설정에 종속되거나 연결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-253">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="6ff61-254">옵션은 구성 데이터의 유효성을 검사하는 메커니즘도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-254">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="6ff61-255">자세한 내용은 [옵션 유효성 검사](#options-validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ff61-255">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="6ff61-256">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ff61-256">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6ff61-257">전제 조건</span><span class="sxs-lookup"><span data-stu-id="6ff61-257">Prerequisites</span></span>

<span data-ttu-id="6ff61-258">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-258">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="6ff61-259">옵션 인터페이스</span><span class="sxs-lookup"><span data-stu-id="6ff61-259">Options interfaces</span></span>

<span data-ttu-id="6ff61-260"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>는 옵션을 검색하고 `TOptions` 인스턴스에 대한 옵션 알림을 관리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-260"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="6ff61-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>은 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="6ff61-262">변경 알림</span><span class="sxs-lookup"><span data-stu-id="6ff61-262">Change notifications</span></span>
* [<span data-ttu-id="6ff61-263">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="6ff61-263">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="6ff61-264">다시 로드할 수 있는 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-264">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="6ff61-265">선택적 옵션 무효화(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="6ff61-265">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="6ff61-266">[사후 구성](#options-post-configuration) 시나리오에서는 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후 옵션을 설정하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-266">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="6ff61-267"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>는 새로운 옵션 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-267"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="6ff61-268">단일 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-268">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="6ff61-269">기본 구현에서는 등록된 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 및 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하며 먼저 구성을 모두 실행한 다음, 사후 구성을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-269">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="6ff61-270"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>와 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>를 구별하며 적절한 인터페이스만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-270">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="6ff61-271"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `TOptions` 인스턴스를 캐시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-271"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="6ff61-272"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 모니터의 옵션 인스턴스를 무효화하므로 값이 다시 계산됩니다(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="6ff61-272">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="6ff61-273"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>를 사용하여 값을 수동으로 도입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-273">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="6ff61-274">모든 명명된 인스턴스를 필요에 따라 다시 만들어야 하는 경우 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-274">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="6ff61-275"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 모든 요청에서 옵션을 다시 계산해야 하는 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-275"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="6ff61-276">자세한 내용은 [IOptionsSnapshot을 사용하여 구성 데이터 다시 로드](#reload-configuration-data-with-ioptionssnapshot)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ff61-276">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="6ff61-277"><xref:Microsoft.Extensions.Options.IOptions%601>를 사용하여 옵션을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-277"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="6ff61-278">그러나 <xref:Microsoft.Extensions.Options.IOptions%601>는 앞에서 설명한 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>의 시나리오를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-278">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="6ff61-279"><xref:Microsoft.Extensions.Options.IOptions%601> 인터페이스를 이미 사용하고 있으며 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 제공하는 시나리오가 필요하지 않은 기존 프레임워크 및 라이브러리에서는 <xref:Microsoft.Extensions.Options.IOptions%601>를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-279">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="6ff61-280">일반 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-280">General options configuration</span></span>

<span data-ttu-id="6ff61-281">일반 옵션 구성은 샘플 앱에 예제 &num;1로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-281">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="6ff61-282">옵션 클래스는 매개 변수가 없는 public 생성자를 사용하는 비추상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-282">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="6ff61-283">다음 클래스 `MyOptions`에는 `Option1` 및 `Option2`의 두 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-283">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="6ff61-284">기본 값 설정은 선택 사항이지만, 다음 예제의 클래스 생성자는 `Option1`의 기본값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-284">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="6ff61-285">`Option2`에는 직접 속성을 초기화하여 설정된 기본값이 있습니다(*Models/MyOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-285">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="6ff61-286">`MyOptions` 클래스는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-286">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="6ff61-287">다음 페이지 모델은 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 통해 [생성자 종속성 주입](xref:mvc/controllers/dependency-injection)을 사용하여 설정에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-287">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="6ff61-288">샘플의 *appsettings.json* 파일은 `option1` 및 `option2`에 대한 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-288">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="6ff61-289">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-289">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="6ff61-290">사용자 지정 <xref:System.Configuration.ConfigurationBuilder>를 사용하여 설정 파일에서 옵션 구성을 로드하는 경우 기본 경로가 올바르게 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-290">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="6ff61-291"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 통해 설정 파일에서 옵션 구성을 로드하는 경우에는 명시적으로 기본 경로를 설정하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-291">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="6ff61-292">대리자로 간단한 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-292">Configure simple options with a delegate</span></span>

<span data-ttu-id="6ff61-293">대리자를 사용한 간단한 옵션 구성은 샘플 앱에 예제 &num;2로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-293">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="6ff61-294">대리자를 사용하여 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-294">Use a delegate to set options values.</span></span> <span data-ttu-id="6ff61-295">샘플 앱에서는 `MyOptionsWithDelegateConfig` 클래스(*Models/MyOptionsWithDelegateConfig.cs*)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-295">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="6ff61-296">다음 코드에서 두 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-296">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="6ff61-297">`MyOptionsWithDelegateConfig`를 통해 바인딩을 구성하는 데 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-297">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="6ff61-298">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="6ff61-298">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="6ff61-299">여러 구성 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-299">You can add multiple configuration providers.</span></span> <span data-ttu-id="6ff61-300">구성 공급자는 NuGet 패키지에서 사용할 수 있으며 등록된 순서대로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-300">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="6ff61-301">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ff61-301">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="6ff61-302"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*>를 호출할 때마다 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-302">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="6ff61-303">위의 예제에서는 `Option1` 및 `Option2`의 값은 모두 *appsettings.json*에서 지정되지만, `Option1` 및 `Option2`의 값은 구성된 대리자에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-303">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="6ff61-304">둘 이상의 구성 서비스를 활성화한 경우 마지막 지정된 구성 소스가 *승리*하고 구성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-304">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="6ff61-305">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-305">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="6ff61-306">하위 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-306">Suboptions configuration</span></span>

<span data-ttu-id="6ff61-307">하위 옵션 구성은 샘플 앱에 예제 &num;3으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-307">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="6ff61-308">앱은 앱의 특정 시나리오 그룹(클래스)에 적합한 옵션 클래스를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-308">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="6ff61-309">구성 값을 필요로 하는 앱의 부분은 사용하는 구성 값에만 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-309">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="6ff61-310">옵션을 구성에 바인딩하는 경우 옵션 형식의 각 속성은 `property[:sub-property:]` 양식의 구성 키에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-310">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="6ff61-311">예를 들어 `MyOptions.Option1` 속성은 키 `Option1`에 바인딩되어 *appsettings.json*의 `option1` 속성에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-311">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="6ff61-312">다음 코드에서 세 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-312">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="6ff61-313">이 코드는 `MySubOptions`를 *appsettings.json* 파일의 `subsection` 섹션에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-313">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="6ff61-314">`GetSection` 메서드를 사용하려면 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 네임스페이스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-314">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="6ff61-315">샘플의 *appsettings.json* 파일은 `suboption1` 및 `suboption2`에 대한 키로 `subsection` 멤버를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-315">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="6ff61-316">`MySubOptions` 클래스는 `SubOption1` 및 `SubOption2` 속성을 정의하여 옵션 값을 유지합니다(*Models/MySubOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-316">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="6ff61-317">페이지 모델의 `OnGet` 메서드는 옵션 값이 포함된 문자열을 반환합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-317">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="6ff61-318">앱을 실행할 때 `OnGet` 메서드는 하위 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-318">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="6ff61-319">직접 보기 주입 또는 보기 모델에 의해 제공되는 옵션</span><span class="sxs-lookup"><span data-stu-id="6ff61-319">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="6ff61-320">직접 보기 주입 또는 보기 모델에 의해 제공되는 옵션은 샘플 앱에 예제 &num;4로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-320">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="6ff61-321">옵션은 뷰 모델 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 보기에 직접 주입하여 제공할 수 있습니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-321">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="6ff61-322">샘플 앱은 `@inject` 지시문을 사용하여 `IOptionsMonitor<MyOptions>`를 주입하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-322">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="6ff61-323">앱을 실행하면 옵션 값이 렌더링된 페이지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-323">When the app is run, the options values are shown in the rendered page:</span></span>

![옵션 값 Option1: value1_from_json 및 Option2: -1은 모델에서 로드되며 보기에 주입됩니다.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="6ff61-325">IOptionsSnapshot을 사용하여 구성 데이터 다시 로드</span><span class="sxs-lookup"><span data-stu-id="6ff61-325">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="6ff61-326"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하여 구성 데이터를 다시 로드하는 방법은 샘플 앱에 예제 &num;5로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-326">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="6ff61-327"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 최소한의 처리 오버헤드로 옵션 다시 로드를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-327"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="6ff61-328">옵션은 엑세스될 때 요청 당 한 번씩 계산되고 요청의 수명 동안 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-328">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="6ff61-329">다음 예제에는 *appsettings.json*이 변경된 후 새 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 만드는 방법이 설명되어 있습니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-329">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="6ff61-330">서버에 대한 여러 요청은 파일이 변경되고 구성이 다시 로드될 때까지 *appsettings.json* 파일에서 제공하는 상수 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-330">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="6ff61-331">다음 이미지는 *appsettings.json* 파일에서 로드된 초기 `option1` 및 `option2` 값을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-331">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="6ff61-332">*appsettings.json* 파일의 값을 `value1_from_json UPDATED` 및 `200`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-332">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="6ff61-333">*appsettings.json* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-333">Save the *appsettings.json* file.</span></span> <span data-ttu-id="6ff61-334">옵션 값이 변경되었음을 확인하려면 브라우저를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-334">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="6ff61-335">IConfigureNamedOptions로 명명된 옵션 지원</span><span class="sxs-lookup"><span data-stu-id="6ff61-335">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="6ff61-336"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>로 명명된 옵션 지원은 샘플 앱에 예제 &num;6으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-336">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="6ff61-337">앱은 *명명된 옵션* 지원을 통해 명명된 옵션 구성들을 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-337">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="6ff61-338">샘플 앱에서 명명된 옵션은 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 확장 메서드를 호출하는 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)를 사용하여 선언됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-338">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="6ff61-339">샘플 앱은 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*>을 사용하여 명명된 옵션에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-339">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="6ff61-340">샘플 앱을 실행하면 명명된 옵션이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-340">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="6ff61-341">`named_options_1` 값은 *appsettings.json* 파일에서 로드되는 구성에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-341">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="6ff61-342">`named_options_2` 값은 다음에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-342">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="6ff61-343">`Option1`에 대한 `ConfigureServices`의 `named_options_2` 대리자.</span><span class="sxs-lookup"><span data-stu-id="6ff61-343">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="6ff61-344">`MyOptions` 클래스에서 제공되는 `Option2`에 대한 기본값.</span><span class="sxs-lookup"><span data-stu-id="6ff61-344">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="6ff61-345">ConfigureAll 메서드를 사용하여 모든 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-345">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="6ff61-346"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 메서드를 사용하여 모든 옵션 인스턴스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-346">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="6ff61-347">다음 코드는 모든 구성 인스턴스에 대해 `Option1`을 공통 값으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-347">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="6ff61-348">다음 코드를 `Startup.ConfigureServices` 메서드에 직접 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-348">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="6ff61-349">코드를 추가한 후 샘플 앱을 실행하면 다음과 같은 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-349">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="6ff61-350">모든 옵션은 명명된 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-350">All options are named instances.</span></span> <span data-ttu-id="6ff61-351">기존 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 인스턴스는 `string.Empty`인 `Options.DefaultName` 인스턴스를 대상으로 지정한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-351">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="6ff61-352"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>는 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>도 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-352"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="6ff61-353"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>의 기본 구현에는 각 옵션을 적절하게 사용하기 위한 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-353">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="6ff61-354">`null` 명명된 옵션은 특정 명명된 인스턴스 대신 모든 명명된 인스턴스를 대상으로 지정하는 데 사용됩니다(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 및 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>에서 이 규칙을 사용함).</span><span class="sxs-lookup"><span data-stu-id="6ff61-354">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="6ff61-355">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="6ff61-355">OptionsBuilder API</span></span>

<span data-ttu-id="6ff61-356"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>는 `TOptions` 인스턴스를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-356"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="6ff61-357">`OptionsBuilder`는 `AddOptions<TOptions>(string optionsName)` 호출에 대한 단일 매개 변수이므로 모든 후속 호출에 나타나는 대신 명명된 옵션 생성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-357">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="6ff61-358">옵션 유효성 검사 및 서비스 종속성을 허용하는 `ConfigureOptions` 오버로드는 `OptionsBuilder`를 통해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-358">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="6ff61-359">DI 서비스를 사용하여 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-359">Use DI services to configure options</span></span>

<span data-ttu-id="6ff61-360">다음과 같은 두 가지 방법으로 옵션을 구성하는 동안 종속성 주입을 통해 다른 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-360">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="6ff61-361">구성 대리자를 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)의 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-361">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="6ff61-362">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)는 최대 5개의 서비스를 사용하여 옵션을 구성할 수 있는 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)의 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-362">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="6ff61-363"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 또는 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>를 구현하는 고유의 형식을 만들고 해당 형식을 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-363">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="6ff61-364">서비스를 만드는 것이 더 복잡하기 때문에 구성 대리자를 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-364">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="6ff61-365">고유의 형식을 만드는 것은 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)를 사용할 때 프레임워크가 수행하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-365">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="6ff61-366">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)을 호출하면 지정된 일반 서비스 유형을 허용하는 생성자가 있는 일시적인 일반 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-366">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="6ff61-367">옵션 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="6ff61-367">Options validation</span></span>

<span data-ttu-id="6ff61-368">옵션 유효성 검사를 사용하면 옵션이 구성될 때 옵션의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-368">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="6ff61-369">옵션이 유효하면 `true`를 반환하고 옵션이 유효하지 않으면 `false`를 반환하는 유효성 검사 메서드로 `Validate`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-369">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="6ff61-370">위의 예제에서는 명명된 옵션 인스턴스를 `optionalOptionsName`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-370">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="6ff61-371">기본 옵션 인스턴스는 `Options.DefaultName`입니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-371">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="6ff61-372">유효성 검사는 옵션 인스턴스가 만들어지면 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-372">Validation runs when the options instance is created.</span></span> <span data-ttu-id="6ff61-373">옵션 인스턴스는 처음 액세스되면 유효성 검사를 통과하도록 보장됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-373">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6ff61-374">옵션 유효성 검사는 옵션이 처음 구성되고 유효성이 검사된 후 옵션 수정에 대해 보호하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-374">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="6ff61-375">`Validate` 메서드는 `Func<TOptions, bool>`을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-375">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="6ff61-376">유효성 검사를 완전히 사용자 지정하려면 다음을 허용하는 `IValidateOptions<TOptions>`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-376">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="6ff61-377">여러 옵션 형식의 유효성 검사: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="6ff61-377">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="6ff61-378">다른 옵션 형식에 의존하는 유효성 검사: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="6ff61-378">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="6ff61-379">`IValidateOptions`는 다음의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-379">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="6ff61-380">특정 명명된 옵션 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="6ff61-380">A specific named options instance.</span></span>
* <span data-ttu-id="6ff61-381">`name`이 `null`인 경우 모든 옵션.</span><span class="sxs-lookup"><span data-stu-id="6ff61-381">All options when `name` is `null`.</span></span>

<span data-ttu-id="6ff61-382">인터페이스의 구현에서 `ValidateOptionsResult`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-382">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="6ff61-383">데이터 주석 기반 유효성 검사는 `OptionsBuilder<TOptions>`의 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> 메서드를 호출하여 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-383">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="6ff61-384">`Microsoft.Extensions.Options.DataAnnotations`는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-384">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="6ff61-385">즉시 유효성 검사(시작 시 빠른 실패)는 향후 릴리스에서 고려 중입니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-385">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="6ff61-386">옵션 사후 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-386">Options post-configuration</span></span>

<span data-ttu-id="6ff61-387"><xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하여 사후 구성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-387">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="6ff61-388">사후 구성은 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-388">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="6ff61-389"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>를 사용하여 명명된 옵션을 사후 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-389"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="6ff61-390">모든 구성 인스턴스를 사후 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-390">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="6ff61-391">시작하는 동안 옵션 액세스</span><span class="sxs-lookup"><span data-stu-id="6ff61-391">Accessing options during startup</span></span>

<span data-ttu-id="6ff61-392">`Configure` 메서드가 실행되기 전에 서비스가 만들어지므로 `Startup.Configure`에서 <xref:Microsoft.Extensions.Options.IOptions%601> 및 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-392"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="6ff61-393">`Startup.ConfigureServices`에서는 <xref:Microsoft.Extensions.Options.IOptions%601> 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="6ff61-393">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6ff61-394">서비스 등록의 순서 지정으로 인해 일관성 없는 옵션 상태가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-394">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="6ff61-395">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-395">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="6ff61-396">[구성 설정](xref:fundamentals/configuration/index)이 시나리오에 따라 별도 클래스로 격리된 경우 앱은 두 가지 중요한 소프트웨어 엔지니어링 원칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-396">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="6ff61-397">[ISP(Interface Segregation Principle, 인터페이스 분리 원칙) 또는 캡슐화](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; 구성 설정에 종속된 시나리오(클래스)는 사용하는 구성 설정에 의해서만 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-397">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="6ff61-398">[관심사의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; 앱의 다른 부분에 대한 설정은 다른 설정에 종속되거나 연결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-398">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="6ff61-399">옵션은 구성 데이터의 유효성을 검사하는 메커니즘도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-399">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="6ff61-400">자세한 내용은 [옵션 유효성 검사](#options-validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ff61-400">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="6ff61-401">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ff61-401">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6ff61-402">전제 조건</span><span class="sxs-lookup"><span data-stu-id="6ff61-402">Prerequisites</span></span>

<span data-ttu-id="6ff61-403">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-403">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="6ff61-404">옵션 인터페이스</span><span class="sxs-lookup"><span data-stu-id="6ff61-404">Options interfaces</span></span>

<span data-ttu-id="6ff61-405"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>는 옵션을 검색하고 `TOptions` 인스턴스에 대한 옵션 알림을 관리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-405"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="6ff61-406"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>은 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-406"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="6ff61-407">변경 알림</span><span class="sxs-lookup"><span data-stu-id="6ff61-407">Change notifications</span></span>
* [<span data-ttu-id="6ff61-408">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="6ff61-408">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="6ff61-409">다시 로드할 수 있는 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-409">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="6ff61-410">선택적 옵션 무효화(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="6ff61-410">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="6ff61-411">[사후 구성](#options-post-configuration) 시나리오에서는 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후 옵션을 설정하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-411">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="6ff61-412"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>는 새로운 옵션 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-412"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="6ff61-413">단일 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-413">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="6ff61-414">기본 구현에서는 등록된 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 및 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하며 먼저 구성을 모두 실행한 다음, 사후 구성을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-414">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="6ff61-415"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>와 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>를 구별하며 적절한 인터페이스만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-415">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="6ff61-416"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `TOptions` 인스턴스를 캐시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-416"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="6ff61-417"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 모니터의 옵션 인스턴스를 무효화하므로 값이 다시 계산됩니다(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="6ff61-417">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="6ff61-418"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>를 사용하여 값을 수동으로 도입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-418">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="6ff61-419">모든 명명된 인스턴스를 필요에 따라 다시 만들어야 하는 경우 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-419">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="6ff61-420"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 모든 요청에서 옵션을 다시 계산해야 하는 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-420"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="6ff61-421">자세한 내용은 [IOptionsSnapshot을 사용하여 구성 데이터 다시 로드](#reload-configuration-data-with-ioptionssnapshot)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ff61-421">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="6ff61-422"><xref:Microsoft.Extensions.Options.IOptions%601>를 사용하여 옵션을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-422"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="6ff61-423">그러나 <xref:Microsoft.Extensions.Options.IOptions%601>는 앞에서 설명한 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>의 시나리오를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-423">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="6ff61-424"><xref:Microsoft.Extensions.Options.IOptions%601> 인터페이스를 이미 사용하고 있으며 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 제공하는 시나리오가 필요하지 않은 기존 프레임워크 및 라이브러리에서는 <xref:Microsoft.Extensions.Options.IOptions%601>를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-424">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="6ff61-425">일반 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-425">General options configuration</span></span>

<span data-ttu-id="6ff61-426">일반 옵션 구성은 샘플 앱에 예제 &num;1로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-426">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="6ff61-427">옵션 클래스는 매개 변수가 없는 public 생성자를 사용하는 비추상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-427">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="6ff61-428">다음 클래스 `MyOptions`에는 `Option1` 및 `Option2`의 두 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-428">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="6ff61-429">기본 값 설정은 선택 사항이지만, 다음 예제의 클래스 생성자는 `Option1`의 기본값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-429">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="6ff61-430">`Option2`에는 직접 속성을 초기화하여 설정된 기본값이 있습니다(*Models/MyOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-430">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="6ff61-431">`MyOptions` 클래스는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-431">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="6ff61-432">다음 페이지 모델은 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 통해 [생성자 종속성 주입](xref:mvc/controllers/dependency-injection)을 사용하여 설정에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-432">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="6ff61-433">샘플의 *appsettings.json* 파일은 `option1` 및 `option2`에 대한 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-433">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="6ff61-434">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-434">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="6ff61-435">사용자 지정 <xref:System.Configuration.ConfigurationBuilder>를 사용하여 설정 파일에서 옵션 구성을 로드하는 경우 기본 경로가 올바르게 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-435">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="6ff61-436"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 통해 설정 파일에서 옵션 구성을 로드하는 경우에는 명시적으로 기본 경로를 설정하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-436">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="6ff61-437">대리자로 간단한 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-437">Configure simple options with a delegate</span></span>

<span data-ttu-id="6ff61-438">대리자를 사용한 간단한 옵션 구성은 샘플 앱에 예제 &num;2로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-438">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="6ff61-439">대리자를 사용하여 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-439">Use a delegate to set options values.</span></span> <span data-ttu-id="6ff61-440">샘플 앱에서는 `MyOptionsWithDelegateConfig` 클래스(*Models/MyOptionsWithDelegateConfig.cs*)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-440">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="6ff61-441">다음 코드에서 두 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-441">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="6ff61-442">`MyOptionsWithDelegateConfig`를 통해 바인딩을 구성하는 데 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-442">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="6ff61-443">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="6ff61-443">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="6ff61-444">여러 구성 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-444">You can add multiple configuration providers.</span></span> <span data-ttu-id="6ff61-445">구성 공급자는 NuGet 패키지에서 사용할 수 있으며 등록된 순서대로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-445">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="6ff61-446">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ff61-446">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="6ff61-447"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*>를 호출할 때마다 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-447">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="6ff61-448">위의 예제에서는 `Option1` 및 `Option2`의 값은 모두 *appsettings.json*에서 지정되지만, `Option1` 및 `Option2`의 값은 구성된 대리자에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-448">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="6ff61-449">둘 이상의 구성 서비스를 활성화한 경우 마지막 지정된 구성 소스가 *승리*하고 구성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-449">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="6ff61-450">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-450">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="6ff61-451">하위 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-451">Suboptions configuration</span></span>

<span data-ttu-id="6ff61-452">하위 옵션 구성은 샘플 앱에 예제 &num;3으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-452">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="6ff61-453">앱은 앱의 특정 시나리오 그룹(클래스)에 적합한 옵션 클래스를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-453">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="6ff61-454">구성 값을 필요로 하는 앱의 부분은 사용하는 구성 값에만 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-454">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="6ff61-455">옵션을 구성에 바인딩하는 경우 옵션 형식의 각 속성은 `property[:sub-property:]` 양식의 구성 키에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-455">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="6ff61-456">예를 들어 `MyOptions.Option1` 속성은 키 `Option1`에 바인딩되어 *appsettings.json*의 `option1` 속성에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-456">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="6ff61-457">다음 코드에서 세 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-457">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="6ff61-458">이 코드는 `MySubOptions`를 *appsettings.json* 파일의 `subsection` 섹션에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-458">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="6ff61-459">`GetSection` 메서드를 사용하려면 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 네임스페이스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-459">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="6ff61-460">샘플의 *appsettings.json* 파일은 `suboption1` 및 `suboption2`에 대한 키로 `subsection` 멤버를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-460">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="6ff61-461">`MySubOptions` 클래스는 `SubOption1` 및 `SubOption2` 속성을 정의하여 옵션 값을 유지합니다(*Models/MySubOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-461">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="6ff61-462">페이지 모델의 `OnGet` 메서드는 옵션 값이 포함된 문자열을 반환합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-462">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="6ff61-463">앱을 실행할 때 `OnGet` 메서드는 하위 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-463">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="6ff61-464">직접 보기 주입 또는 보기 모델에 의해 제공되는 옵션</span><span class="sxs-lookup"><span data-stu-id="6ff61-464">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="6ff61-465">직접 보기 주입 또는 보기 모델에 의해 제공되는 옵션은 샘플 앱에 예제 &num;4로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-465">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="6ff61-466">옵션은 뷰 모델 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 보기에 직접 주입하여 제공할 수 있습니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-466">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="6ff61-467">샘플 앱은 `@inject` 지시문을 사용하여 `IOptionsMonitor<MyOptions>`를 주입하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-467">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="6ff61-468">앱을 실행하면 옵션 값이 렌더링된 페이지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-468">When the app is run, the options values are shown in the rendered page:</span></span>

![옵션 값 Option1: value1_from_json 및 Option2: -1은 모델에서 로드되며 보기에 주입됩니다.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="6ff61-470">IOptionsSnapshot을 사용하여 구성 데이터 다시 로드</span><span class="sxs-lookup"><span data-stu-id="6ff61-470">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="6ff61-471"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하여 구성 데이터를 다시 로드하는 방법은 샘플 앱에 예제 &num;5로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-471">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="6ff61-472"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 최소한의 처리 오버헤드로 옵션 다시 로드를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-472"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="6ff61-473">옵션은 엑세스될 때 요청 당 한 번씩 계산되고 요청의 수명 동안 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-473">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="6ff61-474">다음 예제에는 *appsettings.json*이 변경된 후 새 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 만드는 방법이 설명되어 있습니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-474">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="6ff61-475">서버에 대한 여러 요청은 파일이 변경되고 구성이 다시 로드될 때까지 *appsettings.json* 파일에서 제공하는 상수 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-475">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="6ff61-476">다음 이미지는 *appsettings.json* 파일에서 로드된 초기 `option1` 및 `option2` 값을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-476">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="6ff61-477">*appsettings.json* 파일의 값을 `value1_from_json UPDATED` 및 `200`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-477">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="6ff61-478">*appsettings.json* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-478">Save the *appsettings.json* file.</span></span> <span data-ttu-id="6ff61-479">옵션 값이 변경되었음을 확인하려면 브라우저를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-479">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="6ff61-480">IConfigureNamedOptions로 명명된 옵션 지원</span><span class="sxs-lookup"><span data-stu-id="6ff61-480">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="6ff61-481"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>로 명명된 옵션 지원은 샘플 앱에 예제 &num;6으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-481">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="6ff61-482">앱은 *명명된 옵션* 지원을 통해 명명된 옵션 구성들을 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-482">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="6ff61-483">샘플 앱에서 명명된 옵션은 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 확장 메서드를 호출하는 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)를 사용하여 선언됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-483">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="6ff61-484">샘플 앱은 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*>을 사용하여 명명된 옵션에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="6ff61-484">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="6ff61-485">샘플 앱을 실행하면 명명된 옵션이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-485">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="6ff61-486">`named_options_1` 값은 *appsettings.json* 파일에서 로드되는 구성에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-486">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="6ff61-487">`named_options_2` 값은 다음에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-487">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="6ff61-488">`Option1`에 대한 `ConfigureServices`의 `named_options_2` 대리자.</span><span class="sxs-lookup"><span data-stu-id="6ff61-488">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="6ff61-489">`MyOptions` 클래스에서 제공되는 `Option2`에 대한 기본값.</span><span class="sxs-lookup"><span data-stu-id="6ff61-489">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="6ff61-490">ConfigureAll 메서드를 사용하여 모든 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-490">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="6ff61-491"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 메서드를 사용하여 모든 옵션 인스턴스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-491">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="6ff61-492">다음 코드는 모든 구성 인스턴스에 대해 `Option1`을 공통 값으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-492">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="6ff61-493">다음 코드를 `Startup.ConfigureServices` 메서드에 직접 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-493">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="6ff61-494">코드를 추가한 후 샘플 앱을 실행하면 다음과 같은 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-494">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="6ff61-495">모든 옵션은 명명된 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-495">All options are named instances.</span></span> <span data-ttu-id="6ff61-496">기존 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 인스턴스는 `string.Empty`인 `Options.DefaultName` 인스턴스를 대상으로 지정한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-496">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="6ff61-497"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>는 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>도 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-497"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="6ff61-498"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>의 기본 구현에는 각 옵션을 적절하게 사용하기 위한 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-498">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="6ff61-499">`null` 명명된 옵션은 특정 명명된 인스턴스 대신 모든 명명된 인스턴스를 대상으로 지정하는 데 사용됩니다(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 및 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>에서 이 규칙을 사용함).</span><span class="sxs-lookup"><span data-stu-id="6ff61-499">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="6ff61-500">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="6ff61-500">OptionsBuilder API</span></span>

<span data-ttu-id="6ff61-501"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>는 `TOptions` 인스턴스를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-501"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="6ff61-502">`OptionsBuilder`는 `AddOptions<TOptions>(string optionsName)` 호출에 대한 단일 매개 변수이므로 모든 후속 호출에 나타나는 대신 명명된 옵션 생성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-502">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="6ff61-503">옵션 유효성 검사 및 서비스 종속성을 허용하는 `ConfigureOptions` 오버로드는 `OptionsBuilder`를 통해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-503">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="6ff61-504">DI 서비스를 사용하여 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-504">Use DI services to configure options</span></span>

<span data-ttu-id="6ff61-505">다음과 같은 두 가지 방법으로 옵션을 구성하는 동안 종속성 주입을 통해 다른 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-505">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="6ff61-506">구성 대리자를 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)의 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-506">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="6ff61-507">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)는 최대 5개의 서비스를 사용하여 옵션을 구성할 수 있는 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)의 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-507">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="6ff61-508"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 또는 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>를 구현하는 고유의 형식을 만들고 해당 형식을 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-508">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="6ff61-509">서비스를 만드는 것이 더 복잡하기 때문에 구성 대리자를 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-509">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="6ff61-510">고유의 형식을 만드는 것은 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)를 사용할 때 프레임워크가 수행하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-510">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="6ff61-511">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)을 호출하면 지정된 일반 서비스 유형을 허용하는 생성자가 있는 일시적인 일반 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-511">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="6ff61-512">옵션 사후 구성</span><span class="sxs-lookup"><span data-stu-id="6ff61-512">Options post-configuration</span></span>

<span data-ttu-id="6ff61-513"><xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하여 사후 구성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-513">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="6ff61-514">사후 구성은 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-514">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="6ff61-515"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>를 사용하여 명명된 옵션을 사후 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-515"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="6ff61-516">모든 구성 인스턴스를 사후 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-516">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="6ff61-517">시작하는 동안 옵션 액세스</span><span class="sxs-lookup"><span data-stu-id="6ff61-517">Accessing options during startup</span></span>

<span data-ttu-id="6ff61-518">`Configure` 메서드가 실행되기 전에 서비스가 만들어지므로 `Startup.Configure`에서 <xref:Microsoft.Extensions.Options.IOptions%601> 및 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-518"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="6ff61-519">`Startup.ConfigureServices`에서는 <xref:Microsoft.Extensions.Options.IOptions%601> 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="6ff61-519">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6ff61-520">서비스 등록의 순서 지정으로 인해 일관성 없는 옵션 상태가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ff61-520">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6ff61-521">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6ff61-521">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
