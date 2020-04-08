---
title: ASP.NET Core의 옵션 패턴
author: rick-anderson
description: 옵션 패턴을 사용하여 ASP.NET Core 앱에서 관련된 설정 그룹을 나타내는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
uid: fundamentals/configuration/options
ms.openlocfilehash: 756d3d57122642ab10ab671c9accb75975c3799d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78649989"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="553d1-103">ASP.NET Core의 옵션 패턴</span><span class="sxs-lookup"><span data-stu-id="553d1-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="553d1-104">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-104">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="553d1-105">[구성 설정](xref:fundamentals/configuration/index)이 시나리오에 따라 별도 클래스로 격리된 경우 앱은 두 가지 중요한 소프트웨어 엔지니어링 원칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-105">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="553d1-106">[ISP(Interface Segregation Principle, 인터페이스 분리 원칙) 또는 캡슐화](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; 구성 설정에 종속된 시나리오(클래스)는 사용하는 구성 설정에 의해서만 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-106">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="553d1-107">[관심사의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; 앱의 다른 부분에 대한 설정은 다른 설정에 종속되거나 연결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-107">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="553d1-108">옵션은 구성 데이터의 유효성을 검사하는 메커니즘도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-108">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="553d1-109">자세한 내용은 [옵션 유효성 검사](#options-validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="553d1-109">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="553d1-110">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="553d1-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="553d1-111">패키지</span><span class="sxs-lookup"><span data-stu-id="553d1-111">Package</span></span>

<span data-ttu-id="553d1-112">[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 패키지는 ASP.NET Core 앱에서 암시적으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-112">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="553d1-113">옵션 인터페이스</span><span class="sxs-lookup"><span data-stu-id="553d1-113">Options interfaces</span></span>

<span data-ttu-id="553d1-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>는 옵션을 검색하고 `TOptions` 인스턴스에 대한 옵션 알림을 관리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="553d1-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>은 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="553d1-116">변경 알림</span><span class="sxs-lookup"><span data-stu-id="553d1-116">Change notifications</span></span>
* [<span data-ttu-id="553d1-117">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="553d1-117">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="553d1-118">다시 로드할 수 있는 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-118">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="553d1-119">선택적 옵션 무효화(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="553d1-119">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="553d1-120">[사후 구성](#options-post-configuration) 시나리오에서는 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후 옵션을 설정하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-120">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="553d1-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>는 새로운 옵션 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="553d1-122">단일 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-122">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="553d1-123">기본 구현에서는 등록된 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 및 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하며 먼저 구성을 모두 실행한 다음, 사후 구성을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-123">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="553d1-124"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>와 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>를 구별하며 적절한 인터페이스만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-124">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="553d1-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `TOptions` 인스턴스를 캐시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="553d1-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 모니터의 옵션 인스턴스를 무효화하므로 값이 다시 계산됩니다(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="553d1-126">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="553d1-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>를 사용하여 값을 수동으로 도입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-127">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="553d1-128">모든 명명된 인스턴스를 필요에 따라 다시 만들어야 하는 경우 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-128">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="553d1-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 모든 요청에서 옵션을 다시 계산해야 하는 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="553d1-130">자세한 내용은 [IOptionsSnapshot을 사용하여 구성 데이터 다시 로드](#reload-configuration-data-with-ioptionssnapshot)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="553d1-130">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="553d1-131"><xref:Microsoft.Extensions.Options.IOptions%601>를 사용하여 옵션을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-131"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="553d1-132">그러나 <xref:Microsoft.Extensions.Options.IOptions%601>는 앞에서 설명한 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>의 시나리오를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-132">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="553d1-133"><xref:Microsoft.Extensions.Options.IOptions%601> 인터페이스를 이미 사용하고 있으며 <xref:Microsoft.Extensions.Options.IOptions%601>에서 제공하는 시나리오가 필요하지 않은 기존 프레임워크 및 라이브러리에서는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-133">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="553d1-134">일반 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-134">General options configuration</span></span>

<span data-ttu-id="553d1-135">일반 옵션 구성은 샘플 앱에 예제 1로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-135">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="553d1-136">옵션 클래스는 매개 변수가 없는 public 생성자를 사용하는 비추상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-136">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="553d1-137">다음 클래스 `MyOptions`에는 `Option1` 및 `Option2`의 두 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-137">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="553d1-138">기본 값 설정은 선택 사항이지만, 다음 예제의 클래스 생성자는 `Option1`의 기본값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-138">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="553d1-139">`Option2`에는 직접 속성을 초기화하여 설정된 기본값이 있습니다(*Models/MyOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-139">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="553d1-140">`MyOptions` 클래스는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-140">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="553d1-141">다음 페이지 모델은 [를 통해 ](xref:mvc/controllers/dependency-injection)생성자 종속성 주입<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>을 사용하여 설정에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-141">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="553d1-142">샘플의 *appsettings.json* 파일은 `option1` 및 `option2`에 대한 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-142">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="553d1-143">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-143">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="553d1-144">사용자 지정 <xref:System.Configuration.ConfigurationBuilder>를 사용하여 설정 파일에서 옵션 구성을 로드하는 경우 기본 경로가 올바르게 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-144">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="553d1-145"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 통해 설정 파일에서 옵션 구성을 로드하는 경우에는 명시적으로 기본 경로를 설정하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-145">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="553d1-146">대리자로 간단한 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-146">Configure simple options with a delegate</span></span>

<span data-ttu-id="553d1-147">대리자를 사용한 간단한 옵션 구성은 샘플 앱에 예제 2로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-147">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="553d1-148">대리자를 사용하여 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-148">Use a delegate to set options values.</span></span> <span data-ttu-id="553d1-149">샘플 앱에서는 `MyOptionsWithDelegateConfig` 클래스(*Models/MyOptionsWithDelegateConfig.cs*)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-149">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="553d1-150">다음 코드에서 두 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-150">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="553d1-151">`MyOptionsWithDelegateConfig`를 통해 바인딩을 구성하는 데 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-151">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="553d1-152">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="553d1-152">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="553d1-153">여러 구성 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-153">You can add multiple configuration providers.</span></span> <span data-ttu-id="553d1-154">구성 공급자는 NuGet 패키지에서 사용할 수 있으며 등록된 순서대로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-154">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="553d1-155">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="553d1-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="553d1-156"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*>를 호출할 때마다 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-156">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="553d1-157">위의 예제에서는 `Option1` 및 `Option2`의 값은 모두 *appsettings.json*에서 지정되지만, `Option1` 및 `Option2`의 값은 구성된 대리자에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-157">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="553d1-158">둘 이상의 구성 서비스를 활성화한 경우 마지막 지정된 구성 소스가 *승리*하고 구성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-158">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="553d1-159">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-159">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="553d1-160">하위 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-160">Suboptions configuration</span></span>

<span data-ttu-id="553d1-161">하위 옵션 구성은 샘플 앱에 예제 3으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-161">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="553d1-162">앱은 앱의 특정 시나리오 그룹(클래스)에 적합한 옵션 클래스를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-162">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="553d1-163">구성 값을 필요로 하는 앱의 부분은 사용하는 구성 값에만 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-163">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="553d1-164">옵션을 구성에 바인딩하는 경우 옵션 형식의 각 속성은 `property[:sub-property:]` 양식의 구성 키에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-164">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="553d1-165">예를 들어 `MyOptions.Option1` 속성은 키 `Option1`에 바인딩되어 `option1`appsettings.json*의*  속성에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-165">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="553d1-166">다음 코드에서 세 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-166">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="553d1-167">이 코드는 `MySubOptions`를 `subsection`appsettings.json*파일의* 섹션에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-167">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="553d1-168">`GetSection` 메서드를 사용하려면 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 네임스페이스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-168">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="553d1-169">샘플의 *appsettings.json* 파일은 `subsection` 및 `suboption1`에 대한 키로 `suboption2` 멤버를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-169">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="553d1-170">`MySubOptions` 클래스는 `SubOption1` 및 `SubOption2` 속성을 정의하여 옵션 값을 유지합니다(*Models/MySubOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-170">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="553d1-171">페이지 모델의 `OnGet` 메서드는 옵션 값이 포함된 문자열을 반환합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-171">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="553d1-172">앱을 실행할 때 `OnGet` 메서드는 하위 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-172">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="553d1-173">옵션 삽입</span><span class="sxs-lookup"><span data-stu-id="553d1-173">Options injection</span></span>

<span data-ttu-id="553d1-174">옵션 삽입은 샘플 앱에 예제 4로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-174">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="553d1-175">다음에 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-175">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="553d1-176">[`@inject`](xref:mvc/views/razor#inject) Razor 지시문이 있는 Razor 페이지 또는 MVC 뷰입니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-176">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="553d1-177">페이지 또는 뷰 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-177">A page or view model.</span></span>

<span data-ttu-id="553d1-178">샘플 앱의 다음 예제는 페이지 모델(<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>Pages/Index.cshtml.cs *)에* 를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-178">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="553d1-179">샘플 앱은 `IOptionsMonitor<MyOptions>` 지시문을 사용하여 `@inject`를 주입하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-179">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="553d1-180">앱을 실행하면 옵션 값이 렌더링된 페이지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-180">When the app is run, the options values are shown in the rendered page:</span></span>

![옵션 값 Option1: value1_from_json 및 Option2: -1은 모델에서 로드되며 보기에 주입됩니다.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="553d1-182">IOptionsSnapshot을 사용하여 구성 데이터 다시 로드</span><span class="sxs-lookup"><span data-stu-id="553d1-182">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="553d1-183"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하여 구성 데이터를 다시 로드하는 방법은 샘플 앱에 예제 5로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-183">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="553d1-184"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하면, 옵션은 엑세스될 때 요청 당 한 번씩 계산되고 요청의 수명 동안 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-184">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="553d1-185">`IOptionsMonitor`와 `IOptionsSnapshot`의 차이점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-185">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="553d1-186">`IOptionsMonitor`는 언제든지 현재 옵션 값을 검색하는 [싱글톤 서비스](xref:fundamentals/dependency-injection#singleton)로, 싱글톤 종속성에서 특히 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-186">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="553d1-187">`IOptionsSnapshot`은 [범위가 지정된 서비스](xref:fundamentals/dependency-injection#scoped)이며 `IOptionsSnapshot<T>` 개체가 생성될 때 옵션의 스냅샷을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-187">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="553d1-188">옵션 스냅숏은 임시 및 범위가 지정된 종속성과 함께 사용하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-188">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="553d1-189">다음 예제에는 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>appsettings.json*이 변경된 후 새* 을 만드는 방법이 설명되어 있습니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-189">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="553d1-190">서버에 대한 여러 요청은 파일이 변경되고 구성이 다시 로드될 때까지 *appsettings.json* 파일에서 제공하는 상수 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-190">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="553d1-191">다음 이미지는 `option1`appsettings.json`option2` 파일에서 로드된 초기 *및* 값을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-191">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="553d1-192">*appsettings.json* 파일의 값을 `value1_from_json UPDATED` 및 `200`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-192">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="553d1-193">*appsettings.json* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-193">Save the *appsettings.json* file.</span></span> <span data-ttu-id="553d1-194">옵션 값이 변경되었음을 확인하려면 브라우저를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-194">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="553d1-195">IConfigureNamedOptions로 명명된 옵션 지원</span><span class="sxs-lookup"><span data-stu-id="553d1-195">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="553d1-196"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>로 명명된 옵션 지원은 샘플 앱에 예제 6으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-196">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="553d1-197">앱은 명명된 옵션 지원을 통해 명명된 옵션 구성들을 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-197">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="553d1-198">샘플 앱에서 명명된 옵션은 [ConfigureNamedOptions](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)TOptions>.Configure[ 확장 메서드를 호출하는 \<OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)를 사용하여 선언됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-198">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="553d1-199">명명된 옵션은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-199">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="553d1-200">샘플 앱은 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*>을 사용하여 명명된 옵션에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-200">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="553d1-201">샘플 앱을 실행하면 명명된 옵션이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-201">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="553d1-202">`named_options_1` 값은 *appsettings.json* 파일에서 로드되는 구성에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-202">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="553d1-203">`named_options_2` 값은 다음에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-203">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="553d1-204">`named_options_2`에 대한 `ConfigureServices`의 `Option1` 대리자.</span><span class="sxs-lookup"><span data-stu-id="553d1-204">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="553d1-205">`Option2` 클래스에서 제공되는 `MyOptions`에 대한 기본값.</span><span class="sxs-lookup"><span data-stu-id="553d1-205">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="553d1-206">ConfigureAll 메서드를 사용하여 모든 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-206">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="553d1-207"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 메서드를 사용하여 모든 옵션 인스턴스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-207">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="553d1-208">다음 코드는 모든 구성 인스턴스에 대해 `Option1`을 공통 값으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-208">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="553d1-209">다음 코드를 `Startup.ConfigureServices` 메서드에 직접 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-209">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="553d1-210">코드를 추가한 후 샘플 앱을 실행하면 다음과 같은 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-210">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="553d1-211">모든 옵션은 명명된 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-211">All options are named instances.</span></span> <span data-ttu-id="553d1-212">기존 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 인스턴스는 `Options.DefaultName`인 `string.Empty` 인스턴스를 대상으로 지정한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-212">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="553d1-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>는 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>도 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="553d1-214"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>의 기본 구현에는 각 옵션을 적절하게 사용하기 위한 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-214">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="553d1-215">`null` 명명된 옵션은 특정 명명된 인스턴스 대신 모든 명명된 인스턴스를 대상으로 지정하는 데 사용됩니다(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 및 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>에서 이 규칙을 사용함).</span><span class="sxs-lookup"><span data-stu-id="553d1-215">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="553d1-216">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="553d1-216">OptionsBuilder API</span></span>

<span data-ttu-id="553d1-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>는 `TOptions` 인스턴스를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="553d1-218">`OptionsBuilder`는 `AddOptions<TOptions>(string optionsName)` 호출에 대한 단일 매개 변수이므로 모든 후속 호출에 나타나는 대신 명명된 옵션 생성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-218">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="553d1-219">옵션 유효성 검사 및 서비스 종속성을 허용하는 `ConfigureOptions` 오버로드는 `OptionsBuilder`를 통해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-219">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="553d1-220">DI 서비스를 사용하여 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-220">Use DI services to configure options</span></span>

<span data-ttu-id="553d1-221">다음과 같은 두 가지 방법으로 옵션을 구성하는 동안 종속성 주입을 통해 다른 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-221">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="553d1-222">구성 대리자를 [OptionsBuilder](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)TOptions>[의 \<Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-222">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="553d1-223">`OptionsBuilder<TOptions>`는 최대 5개의 서비스를 사용하여 옵션을 구성할 수 있는 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)의 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-223">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="553d1-224"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 또는 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>를 구현하는 고유의 형식을 만들고 해당 형식을 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-224">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="553d1-225">서비스를 만드는 것이 더 복잡하기 때문에 구성 대리자를 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-225">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="553d1-226">고유의 형식을 만드는 것은 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)를 사용할 때 프레임워크가 수행하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-226">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="553d1-227">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)을 호출하면 지정된 일반 서비스 유형을 허용하는 생성자가 있는 일시적인 일반 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-227">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="553d1-228">옵션 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="553d1-228">Options validation</span></span>

<span data-ttu-id="553d1-229">옵션 유효성 검사를 사용하면 옵션이 구성될 때 옵션의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-229">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="553d1-230">옵션이 유효하면 `Validate`를 반환하고 옵션이 유효하지 않으면 `true`를 반환하는 유효성 검사 메서드로 `false`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-230">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="553d1-231">위의 예제에서는 명명된 옵션 인스턴스를 `optionalOptionsName`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-231">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="553d1-232">기본 옵션 인스턴스는 `Options.DefaultName`입니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-232">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="553d1-233">유효성 검사는 옵션 인스턴스가 만들어지면 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-233">Validation runs when the options instance is created.</span></span> <span data-ttu-id="553d1-234">옵션 인스턴스는 처음 액세스되면 유효성 검사를 통과하도록 보장됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-234">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="553d1-235">옵션 유효성 검사는 옵션 인스턴스가 생성된 후 옵션 수정에 대해 보호하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-235">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="553d1-236">예를 들어 옵션이 처음 액세스될 때 요청마다 한 번씩 `IOptionsSnapshot` 옵션이 생성되고 유효성이 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-236">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="553d1-237">`IOptionsSnapshot` 옵션은 *동일한 요청에 대한* 다음 액세스 시도에서 다시 검사되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-237">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="553d1-238">`Validate` 메서드는 `Func<TOptions, bool>`을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-238">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="553d1-239">유효성 검사를 완전히 사용자 지정하려면 다음을 허용하는 `IValidateOptions<TOptions>`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-239">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="553d1-240">여러 옵션 형식의 유효성 검사: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="553d1-240">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="553d1-241">다른 옵션 형식에 의존하는 유효성 검사: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="553d1-241">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="553d1-242">`IValidateOptions`는 다음의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-242">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="553d1-243">특정 명명된 옵션 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="553d1-243">A specific named options instance.</span></span>
* <span data-ttu-id="553d1-244">`name`이 `null`인 경우 모든 옵션.</span><span class="sxs-lookup"><span data-stu-id="553d1-244">All options when `name` is `null`.</span></span>

<span data-ttu-id="553d1-245">인터페이스의 구현에서 `ValidateOptionsResult`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-245">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="553d1-246">데이터 주석 기반 유효성 검사는 [의 ](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 메서드를 호출하여 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*>Microsoft.Extensions.Options.DataAnnotations`OptionsBuilder<TOptions>` 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-246">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="553d1-247">`Microsoft.Extensions.Options.DataAnnotations`는 ASP.NET Core 앱에서 암시적으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-247">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

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

<span data-ttu-id="553d1-248">즉시 유효성 검사(시작 시 빠른 실패)는 향후 릴리스에서 고려 중입니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-248">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="553d1-249">옵션 사후 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-249">Options post-configuration</span></span>

<span data-ttu-id="553d1-250"><xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하여 사후 구성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-250">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="553d1-251">사후 구성은 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-251">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="553d1-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>를 사용하여 명명된 옵션을 사후 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="553d1-253">모든 구성 인스턴스를 사후 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-253">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="553d1-254">시작하는 동안 옵션 액세스</span><span class="sxs-lookup"><span data-stu-id="553d1-254">Accessing options during startup</span></span>

<span data-ttu-id="553d1-255"><xref:Microsoft.Extensions.Options.IOptions%601> 메서드가 실행되기 전에 서비스가 만들어지므로 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `Startup.Configure` 및 `Configure`를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-255"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="553d1-256"><xref:Microsoft.Extensions.Options.IOptions%601>에서는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 또는 `Startup.ConfigureServices`를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="553d1-256">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="553d1-257">서비스 등록의 순서 지정으로 인해 일관성 없는 옵션 상태가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-257">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="553d1-258">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-258">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="553d1-259">[구성 설정](xref:fundamentals/configuration/index)이 시나리오에 따라 별도 클래스로 격리된 경우 앱은 두 가지 중요한 소프트웨어 엔지니어링 원칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-259">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="553d1-260">[ISP(Interface Segregation Principle, 인터페이스 분리 원칙) 또는 캡슐화](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; 구성 설정에 종속된 시나리오(클래스)는 사용하는 구성 설정에 의해서만 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-260">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="553d1-261">[관심사의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; 앱의 다른 부분에 대한 설정은 다른 설정에 종속되거나 연결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-261">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="553d1-262">옵션은 구성 데이터의 유효성을 검사하는 메커니즘도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-262">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="553d1-263">자세한 내용은 [옵션 유효성 검사](#options-validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="553d1-263">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="553d1-264">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="553d1-264">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="553d1-265">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="553d1-265">Prerequisites</span></span>

<span data-ttu-id="553d1-266">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-266">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="553d1-267">옵션 인터페이스</span><span class="sxs-lookup"><span data-stu-id="553d1-267">Options interfaces</span></span>

<span data-ttu-id="553d1-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>는 옵션을 검색하고 `TOptions` 인스턴스에 대한 옵션 알림을 관리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="553d1-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>은 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="553d1-270">변경 알림</span><span class="sxs-lookup"><span data-stu-id="553d1-270">Change notifications</span></span>
* [<span data-ttu-id="553d1-271">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="553d1-271">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="553d1-272">다시 로드할 수 있는 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-272">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="553d1-273">선택적 옵션 무효화(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="553d1-273">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="553d1-274">[사후 구성](#options-post-configuration) 시나리오에서는 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후 옵션을 설정하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-274">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="553d1-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>는 새로운 옵션 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="553d1-276">단일 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-276">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="553d1-277">기본 구현에서는 등록된 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 및 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하며 먼저 구성을 모두 실행한 다음, 사후 구성을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-277">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="553d1-278"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>와 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>를 구별하며 적절한 인터페이스만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-278">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="553d1-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `TOptions` 인스턴스를 캐시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="553d1-280"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 모니터의 옵션 인스턴스를 무효화하므로 값이 다시 계산됩니다(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="553d1-280">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="553d1-281"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>를 사용하여 값을 수동으로 도입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-281">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="553d1-282">모든 명명된 인스턴스를 필요에 따라 다시 만들어야 하는 경우 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-282">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="553d1-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 모든 요청에서 옵션을 다시 계산해야 하는 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="553d1-284">자세한 내용은 [IOptionsSnapshot을 사용하여 구성 데이터 다시 로드](#reload-configuration-data-with-ioptionssnapshot)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="553d1-284">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="553d1-285"><xref:Microsoft.Extensions.Options.IOptions%601>를 사용하여 옵션을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-285"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="553d1-286">그러나 <xref:Microsoft.Extensions.Options.IOptions%601>는 앞에서 설명한 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>의 시나리오를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-286">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="553d1-287"><xref:Microsoft.Extensions.Options.IOptions%601> 인터페이스를 이미 사용하고 있으며 <xref:Microsoft.Extensions.Options.IOptions%601>에서 제공하는 시나리오가 필요하지 않은 기존 프레임워크 및 라이브러리에서는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-287">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="553d1-288">일반 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-288">General options configuration</span></span>

<span data-ttu-id="553d1-289">일반 옵션 구성은 샘플 앱에 예제 1로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-289">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="553d1-290">옵션 클래스는 매개 변수가 없는 public 생성자를 사용하는 비추상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-290">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="553d1-291">다음 클래스 `MyOptions`에는 `Option1` 및 `Option2`의 두 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-291">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="553d1-292">기본 값 설정은 선택 사항이지만, 다음 예제의 클래스 생성자는 `Option1`의 기본값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-292">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="553d1-293">`Option2`에는 직접 속성을 초기화하여 설정된 기본값이 있습니다(*Models/MyOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-293">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="553d1-294">`MyOptions` 클래스는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-294">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="553d1-295">다음 페이지 모델은 [를 통해 ](xref:mvc/controllers/dependency-injection)생성자 종속성 주입<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>을 사용하여 설정에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-295">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="553d1-296">샘플의 *appsettings.json* 파일은 `option1` 및 `option2`에 대한 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-296">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="553d1-297">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-297">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="553d1-298">사용자 지정 <xref:System.Configuration.ConfigurationBuilder>를 사용하여 설정 파일에서 옵션 구성을 로드하는 경우 기본 경로가 올바르게 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-298">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="553d1-299"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 통해 설정 파일에서 옵션 구성을 로드하는 경우에는 명시적으로 기본 경로를 설정하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-299">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="553d1-300">대리자로 간단한 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-300">Configure simple options with a delegate</span></span>

<span data-ttu-id="553d1-301">대리자를 사용한 간단한 옵션 구성은 샘플 앱에 예제 2로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-301">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="553d1-302">대리자를 사용하여 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-302">Use a delegate to set options values.</span></span> <span data-ttu-id="553d1-303">샘플 앱에서는 `MyOptionsWithDelegateConfig` 클래스(*Models/MyOptionsWithDelegateConfig.cs*)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-303">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="553d1-304">다음 코드에서 두 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-304">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="553d1-305">`MyOptionsWithDelegateConfig`를 통해 바인딩을 구성하는 데 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-305">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="553d1-306">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="553d1-306">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="553d1-307">여러 구성 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-307">You can add multiple configuration providers.</span></span> <span data-ttu-id="553d1-308">구성 공급자는 NuGet 패키지에서 사용할 수 있으며 등록된 순서대로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-308">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="553d1-309">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="553d1-309">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="553d1-310"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*>를 호출할 때마다 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-310">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="553d1-311">위의 예제에서는 `Option1` 및 `Option2`의 값은 모두 *appsettings.json*에서 지정되지만, `Option1` 및 `Option2`의 값은 구성된 대리자에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-311">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="553d1-312">둘 이상의 구성 서비스를 활성화한 경우 마지막 지정된 구성 소스가 *승리*하고 구성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-312">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="553d1-313">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-313">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="553d1-314">하위 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-314">Suboptions configuration</span></span>

<span data-ttu-id="553d1-315">하위 옵션 구성은 샘플 앱에 예제 3으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-315">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="553d1-316">앱은 앱의 특정 시나리오 그룹(클래스)에 적합한 옵션 클래스를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-316">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="553d1-317">구성 값을 필요로 하는 앱의 부분은 사용하는 구성 값에만 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-317">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="553d1-318">옵션을 구성에 바인딩하는 경우 옵션 형식의 각 속성은 `property[:sub-property:]` 양식의 구성 키에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-318">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="553d1-319">예를 들어 `MyOptions.Option1` 속성은 키 `Option1`에 바인딩되어 `option1`appsettings.json*의*  속성에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-319">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="553d1-320">다음 코드에서 세 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-320">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="553d1-321">이 코드는 `MySubOptions`를 `subsection`appsettings.json*파일의* 섹션에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-321">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="553d1-322">`GetSection` 메서드를 사용하려면 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 네임스페이스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-322">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="553d1-323">샘플의 *appsettings.json* 파일은 `subsection` 및 `suboption1`에 대한 키로 `suboption2` 멤버를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-323">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="553d1-324">`MySubOptions` 클래스는 `SubOption1` 및 `SubOption2` 속성을 정의하여 옵션 값을 유지합니다(*Models/MySubOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-324">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="553d1-325">페이지 모델의 `OnGet` 메서드는 옵션 값이 포함된 문자열을 반환합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-325">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="553d1-326">앱을 실행할 때 `OnGet` 메서드는 하위 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-326">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="553d1-327">옵션 삽입</span><span class="sxs-lookup"><span data-stu-id="553d1-327">Options injection</span></span>

<span data-ttu-id="553d1-328">옵션 삽입은 샘플 앱에 예제 4로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-328">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="553d1-329">다음에 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-329">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="553d1-330">[`@inject`](xref:mvc/views/razor#inject) Razor 지시문이 있는 Razor 페이지 또는 MVC 뷰입니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-330">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="553d1-331">페이지 또는 뷰 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-331">A page or view model.</span></span>

<span data-ttu-id="553d1-332">샘플 앱의 다음 예제는 페이지 모델(<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>Pages/Index.cshtml.cs *)에* 를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-332">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="553d1-333">샘플 앱은 `IOptionsMonitor<MyOptions>` 지시문을 사용하여 `@inject`를 주입하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-333">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="553d1-334">앱을 실행하면 옵션 값이 렌더링된 페이지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-334">When the app is run, the options values are shown in the rendered page:</span></span>

![옵션 값 Option1: value1_from_json 및 Option2: -1은 모델에서 로드되며 보기에 주입됩니다.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="553d1-336">IOptionsSnapshot을 사용하여 구성 데이터 다시 로드</span><span class="sxs-lookup"><span data-stu-id="553d1-336">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="553d1-337"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하여 구성 데이터를 다시 로드하는 방법은 샘플 앱에 예제 5로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-337">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="553d1-338"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하면, 옵션은 엑세스될 때 요청 당 한 번씩 계산되고 요청의 수명 동안 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-338">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="553d1-339">`IOptionsMonitor`와 `IOptionsSnapshot`의 차이점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-339">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="553d1-340">`IOptionsMonitor`는 언제든지 현재 옵션 값을 검색하는 [싱글톤 서비스](xref:fundamentals/dependency-injection#singleton)로, 싱글톤 종속성에서 특히 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-340">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="553d1-341">`IOptionsSnapshot`은 [범위가 지정된 서비스](xref:fundamentals/dependency-injection#scoped)이며 `IOptionsSnapshot<T>` 개체가 생성될 때 옵션의 스냅샷을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-341">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="553d1-342">옵션 스냅숏은 임시 및 범위가 지정된 종속성과 함께 사용하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-342">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="553d1-343">다음 예제에는 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>appsettings.json*이 변경된 후 새* 을 만드는 방법이 설명되어 있습니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-343">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="553d1-344">서버에 대한 여러 요청은 파일이 변경되고 구성이 다시 로드될 때까지 *appsettings.json* 파일에서 제공하는 상수 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-344">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="553d1-345">다음 이미지는 `option1`appsettings.json`option2` 파일에서 로드된 초기 *및* 값을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-345">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="553d1-346">*appsettings.json* 파일의 값을 `value1_from_json UPDATED` 및 `200`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-346">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="553d1-347">*appsettings.json* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-347">Save the *appsettings.json* file.</span></span> <span data-ttu-id="553d1-348">옵션 값이 변경되었음을 확인하려면 브라우저를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-348">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="553d1-349">IConfigureNamedOptions로 명명된 옵션 지원</span><span class="sxs-lookup"><span data-stu-id="553d1-349">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="553d1-350"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>로 명명된 옵션 지원은 샘플 앱에 예제 6으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-350">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="553d1-351">앱은 명명된 옵션 지원을 통해 명명된 옵션 구성들을 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-351">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="553d1-352">샘플 앱에서 명명된 옵션은 [ConfigureNamedOptions](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)TOptions>.Configure[ 확장 메서드를 호출하는 \<OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)를 사용하여 선언됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-352">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="553d1-353">명명된 옵션은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-353">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="553d1-354">샘플 앱은 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*>을 사용하여 명명된 옵션에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-354">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="553d1-355">샘플 앱을 실행하면 명명된 옵션이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-355">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="553d1-356">`named_options_1` 값은 *appsettings.json* 파일에서 로드되는 구성에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-356">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="553d1-357">`named_options_2` 값은 다음에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-357">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="553d1-358">`named_options_2`에 대한 `ConfigureServices`의 `Option1` 대리자.</span><span class="sxs-lookup"><span data-stu-id="553d1-358">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="553d1-359">`Option2` 클래스에서 제공되는 `MyOptions`에 대한 기본값.</span><span class="sxs-lookup"><span data-stu-id="553d1-359">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="553d1-360">ConfigureAll 메서드를 사용하여 모든 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-360">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="553d1-361"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 메서드를 사용하여 모든 옵션 인스턴스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-361">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="553d1-362">다음 코드는 모든 구성 인스턴스에 대해 `Option1`을 공통 값으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-362">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="553d1-363">다음 코드를 `Startup.ConfigureServices` 메서드에 직접 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-363">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="553d1-364">코드를 추가한 후 샘플 앱을 실행하면 다음과 같은 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-364">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="553d1-365">모든 옵션은 명명된 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-365">All options are named instances.</span></span> <span data-ttu-id="553d1-366">기존 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 인스턴스는 `Options.DefaultName`인 `string.Empty` 인스턴스를 대상으로 지정한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-366">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="553d1-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>는 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>도 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="553d1-368"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>의 기본 구현에는 각 옵션을 적절하게 사용하기 위한 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-368">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="553d1-369">`null` 명명된 옵션은 특정 명명된 인스턴스 대신 모든 명명된 인스턴스를 대상으로 지정하는 데 사용됩니다(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 및 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>에서 이 규칙을 사용함).</span><span class="sxs-lookup"><span data-stu-id="553d1-369">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="553d1-370">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="553d1-370">OptionsBuilder API</span></span>

<span data-ttu-id="553d1-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>는 `TOptions` 인스턴스를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="553d1-372">`OptionsBuilder`는 `AddOptions<TOptions>(string optionsName)` 호출에 대한 단일 매개 변수이므로 모든 후속 호출에 나타나는 대신 명명된 옵션 생성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-372">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="553d1-373">옵션 유효성 검사 및 서비스 종속성을 허용하는 `ConfigureOptions` 오버로드는 `OptionsBuilder`를 통해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-373">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="553d1-374">DI 서비스를 사용하여 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-374">Use DI services to configure options</span></span>

<span data-ttu-id="553d1-375">다음과 같은 두 가지 방법으로 옵션을 구성하는 동안 종속성 주입을 통해 다른 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-375">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="553d1-376">구성 대리자를 [OptionsBuilder](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)TOptions>[의 \<Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-376">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="553d1-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)는 최대 5개의 서비스를 사용하여 옵션을 구성할 수 있는 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)의 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="553d1-378"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 또는 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>를 구현하는 고유의 형식을 만들고 해당 형식을 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-378">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="553d1-379">서비스를 만드는 것이 더 복잡하기 때문에 구성 대리자를 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-379">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="553d1-380">고유의 형식을 만드는 것은 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)를 사용할 때 프레임워크가 수행하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-380">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="553d1-381">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)을 호출하면 지정된 일반 서비스 유형을 허용하는 생성자가 있는 일시적인 일반 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-381">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="553d1-382">옵션 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="553d1-382">Options validation</span></span>

<span data-ttu-id="553d1-383">옵션 유효성 검사를 사용하면 옵션이 구성될 때 옵션의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-383">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="553d1-384">옵션이 유효하면 `Validate`를 반환하고 옵션이 유효하지 않으면 `true`를 반환하는 유효성 검사 메서드로 `false`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-384">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="553d1-385">위의 예제에서는 명명된 옵션 인스턴스를 `optionalOptionsName`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-385">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="553d1-386">기본 옵션 인스턴스는 `Options.DefaultName`입니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-386">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="553d1-387">유효성 검사는 옵션 인스턴스가 만들어지면 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-387">Validation runs when the options instance is created.</span></span> <span data-ttu-id="553d1-388">옵션 인스턴스는 처음 액세스되면 유효성 검사를 통과하도록 보장됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-388">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="553d1-389">옵션 유효성 검사는 옵션 인스턴스가 생성된 후 옵션 수정에 대해 보호하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-389">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="553d1-390">예를 들어 옵션이 처음 액세스될 때 요청마다 한 번씩 `IOptionsSnapshot` 옵션이 생성되고 유효성이 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-390">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="553d1-391">`IOptionsSnapshot` 옵션은 *동일한 요청에 대한* 다음 액세스 시도에서 다시 검사되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-391">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="553d1-392">`Validate` 메서드는 `Func<TOptions, bool>`을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-392">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="553d1-393">유효성 검사를 완전히 사용자 지정하려면 다음을 허용하는 `IValidateOptions<TOptions>`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-393">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="553d1-394">여러 옵션 형식의 유효성 검사: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="553d1-394">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="553d1-395">다른 옵션 형식에 의존하는 유효성 검사: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="553d1-395">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="553d1-396">`IValidateOptions`는 다음의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-396">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="553d1-397">특정 명명된 옵션 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="553d1-397">A specific named options instance.</span></span>
* <span data-ttu-id="553d1-398">`name`이 `null`인 경우 모든 옵션.</span><span class="sxs-lookup"><span data-stu-id="553d1-398">All options when `name` is `null`.</span></span>

<span data-ttu-id="553d1-399">인터페이스의 구현에서 `ValidateOptionsResult`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-399">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="553d1-400">데이터 주석 기반 유효성 검사는 [의 ](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 메서드를 호출하여 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*>Microsoft.Extensions.Options.DataAnnotations`OptionsBuilder<TOptions>` 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-400">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="553d1-401">`Microsoft.Extensions.Options.DataAnnotations`는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-401">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="553d1-402">즉시 유효성 검사(시작 시 빠른 실패)는 향후 릴리스에서 고려 중입니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-402">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="553d1-403">옵션 사후 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-403">Options post-configuration</span></span>

<span data-ttu-id="553d1-404"><xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하여 사후 구성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-404">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="553d1-405">사후 구성은 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-405">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="553d1-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>를 사용하여 명명된 옵션을 사후 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="553d1-407">모든 구성 인스턴스를 사후 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-407">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="553d1-408">시작하는 동안 옵션 액세스</span><span class="sxs-lookup"><span data-stu-id="553d1-408">Accessing options during startup</span></span>

<span data-ttu-id="553d1-409"><xref:Microsoft.Extensions.Options.IOptions%601> 메서드가 실행되기 전에 서비스가 만들어지므로 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `Startup.Configure` 및 `Configure`를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-409"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="553d1-410"><xref:Microsoft.Extensions.Options.IOptions%601>에서는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 또는 `Startup.ConfigureServices`를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="553d1-410">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="553d1-411">서비스 등록의 순서 지정으로 인해 일관성 없는 옵션 상태가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-411">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="553d1-412">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-412">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="553d1-413">[구성 설정](xref:fundamentals/configuration/index)이 시나리오에 따라 별도 클래스로 격리된 경우 앱은 두 가지 중요한 소프트웨어 엔지니어링 원칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-413">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="553d1-414">[ISP(Interface Segregation Principle, 인터페이스 분리 원칙) 또는 캡슐화](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; 구성 설정에 종속된 시나리오(클래스)는 사용하는 구성 설정에 의해서만 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-414">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="553d1-415">[관심사의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; 앱의 다른 부분에 대한 설정은 다른 설정에 종속되거나 연결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-415">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="553d1-416">옵션은 구성 데이터의 유효성을 검사하는 메커니즘도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-416">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="553d1-417">자세한 내용은 [옵션 유효성 검사](#options-validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="553d1-417">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="553d1-418">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="553d1-418">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="553d1-419">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="553d1-419">Prerequisites</span></span>

<span data-ttu-id="553d1-420">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-420">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="553d1-421">옵션 인터페이스</span><span class="sxs-lookup"><span data-stu-id="553d1-421">Options interfaces</span></span>

<span data-ttu-id="553d1-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>는 옵션을 검색하고 `TOptions` 인스턴스에 대한 옵션 알림을 관리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="553d1-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>은 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="553d1-424">변경 알림</span><span class="sxs-lookup"><span data-stu-id="553d1-424">Change notifications</span></span>
* [<span data-ttu-id="553d1-425">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="553d1-425">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="553d1-426">다시 로드할 수 있는 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-426">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="553d1-427">선택적 옵션 무효화(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="553d1-427">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="553d1-428">[사후 구성](#options-post-configuration) 시나리오에서는 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후 옵션을 설정하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-428">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="553d1-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>는 새로운 옵션 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="553d1-430">단일 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-430">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="553d1-431">기본 구현에서는 등록된 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 및 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하며 먼저 구성을 모두 실행한 다음, 사후 구성을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-431">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="553d1-432"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>와 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>를 구별하며 적절한 인터페이스만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-432">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="553d1-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `TOptions` 인스턴스를 캐시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="553d1-434"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 모니터의 옵션 인스턴스를 무효화하므로 값이 다시 계산됩니다(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="553d1-434">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="553d1-435"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>를 사용하여 값을 수동으로 도입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-435">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="553d1-436">모든 명명된 인스턴스를 필요에 따라 다시 만들어야 하는 경우 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-436">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="553d1-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 모든 요청에서 옵션을 다시 계산해야 하는 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="553d1-438">자세한 내용은 [IOptionsSnapshot을 사용하여 구성 데이터 다시 로드](#reload-configuration-data-with-ioptionssnapshot)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="553d1-438">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="553d1-439"><xref:Microsoft.Extensions.Options.IOptions%601>를 사용하여 옵션을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-439"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="553d1-440">그러나 <xref:Microsoft.Extensions.Options.IOptions%601>는 앞에서 설명한 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>의 시나리오를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-440">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="553d1-441"><xref:Microsoft.Extensions.Options.IOptions%601> 인터페이스를 이미 사용하고 있으며 <xref:Microsoft.Extensions.Options.IOptions%601>에서 제공하는 시나리오가 필요하지 않은 기존 프레임워크 및 라이브러리에서는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-441">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="553d1-442">일반 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-442">General options configuration</span></span>

<span data-ttu-id="553d1-443">일반 옵션 구성은 샘플 앱에 예제 1로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-443">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="553d1-444">옵션 클래스는 매개 변수가 없는 public 생성자를 사용하는 비추상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-444">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="553d1-445">다음 클래스 `MyOptions`에는 `Option1` 및 `Option2`의 두 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-445">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="553d1-446">기본 값 설정은 선택 사항이지만, 다음 예제의 클래스 생성자는 `Option1`의 기본값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-446">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="553d1-447">`Option2`에는 직접 속성을 초기화하여 설정된 기본값이 있습니다(*Models/MyOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-447">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="553d1-448">`MyOptions` 클래스는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-448">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="553d1-449">다음 페이지 모델은 [를 통해 ](xref:mvc/controllers/dependency-injection)생성자 종속성 주입<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>을 사용하여 설정에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-449">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="553d1-450">샘플의 *appsettings.json* 파일은 `option1` 및 `option2`에 대한 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-450">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="553d1-451">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-451">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="553d1-452">사용자 지정 <xref:System.Configuration.ConfigurationBuilder>를 사용하여 설정 파일에서 옵션 구성을 로드하는 경우 기본 경로가 올바르게 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-452">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="553d1-453"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 통해 설정 파일에서 옵션 구성을 로드하는 경우에는 명시적으로 기본 경로를 설정하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-453">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="553d1-454">대리자로 간단한 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-454">Configure simple options with a delegate</span></span>

<span data-ttu-id="553d1-455">대리자를 사용한 간단한 옵션 구성은 샘플 앱에 예제 2로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-455">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="553d1-456">대리자를 사용하여 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-456">Use a delegate to set options values.</span></span> <span data-ttu-id="553d1-457">샘플 앱에서는 `MyOptionsWithDelegateConfig` 클래스(*Models/MyOptionsWithDelegateConfig.cs*)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-457">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="553d1-458">다음 코드에서 두 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-458">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="553d1-459">`MyOptionsWithDelegateConfig`를 통해 바인딩을 구성하는 데 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-459">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="553d1-460">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="553d1-460">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="553d1-461">여러 구성 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-461">You can add multiple configuration providers.</span></span> <span data-ttu-id="553d1-462">구성 공급자는 NuGet 패키지에서 사용할 수 있으며 등록된 순서대로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-462">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="553d1-463">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="553d1-463">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="553d1-464"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*>를 호출할 때마다 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-464">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="553d1-465">위의 예제에서는 `Option1` 및 `Option2`의 값은 모두 *appsettings.json*에서 지정되지만, `Option1` 및 `Option2`의 값은 구성된 대리자에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-465">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="553d1-466">둘 이상의 구성 서비스를 활성화한 경우 마지막 지정된 구성 소스가 *승리*하고 구성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-466">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="553d1-467">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-467">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="553d1-468">하위 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-468">Suboptions configuration</span></span>

<span data-ttu-id="553d1-469">하위 옵션 구성은 샘플 앱에 예제 3으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-469">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="553d1-470">앱은 앱의 특정 시나리오 그룹(클래스)에 적합한 옵션 클래스를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-470">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="553d1-471">구성 값을 필요로 하는 앱의 부분은 사용하는 구성 값에만 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-471">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="553d1-472">옵션을 구성에 바인딩하는 경우 옵션 형식의 각 속성은 `property[:sub-property:]` 양식의 구성 키에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-472">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="553d1-473">예를 들어 `MyOptions.Option1` 속성은 키 `Option1`에 바인딩되어 `option1`appsettings.json*의*  속성에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-473">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="553d1-474">다음 코드에서 세 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-474">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="553d1-475">이 코드는 `MySubOptions`를 `subsection`appsettings.json*파일의* 섹션에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-475">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="553d1-476">`GetSection` 메서드를 사용하려면 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 네임스페이스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-476">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="553d1-477">샘플의 *appsettings.json* 파일은 `subsection` 및 `suboption1`에 대한 키로 `suboption2` 멤버를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-477">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="553d1-478">`MySubOptions` 클래스는 `SubOption1` 및 `SubOption2` 속성을 정의하여 옵션 값을 유지합니다(*Models/MySubOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-478">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="553d1-479">페이지 모델의 `OnGet` 메서드는 옵션 값이 포함된 문자열을 반환합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-479">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="553d1-480">앱을 실행할 때 `OnGet` 메서드는 하위 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-480">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="553d1-481">직접 보기 주입 또는 보기 모델에 의해 제공되는 옵션</span><span class="sxs-lookup"><span data-stu-id="553d1-481">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="553d1-482">직접 보기 주입 또는 보기 모델에 의해 제공되는 옵션은 샘플 앱에 예제 4로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-482">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="553d1-483">옵션은 뷰 모델 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 보기에 직접 주입하여 제공할 수 있습니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-483">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="553d1-484">샘플 앱은 `IOptionsMonitor<MyOptions>` 지시문을 사용하여 `@inject`를 주입하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-484">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="553d1-485">앱을 실행하면 옵션 값이 렌더링된 페이지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-485">When the app is run, the options values are shown in the rendered page:</span></span>

![옵션 값 Option1: value1_from_json 및 Option2: -1은 모델에서 로드되며 보기에 주입됩니다.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="553d1-487">IOptionsSnapshot을 사용하여 구성 데이터 다시 로드</span><span class="sxs-lookup"><span data-stu-id="553d1-487">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="553d1-488"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하여 구성 데이터를 다시 로드하는 방법은 샘플 앱에 예제 5로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-488">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="553d1-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 최소한의 처리 오버헤드로 옵션 다시 로드를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="553d1-490">옵션은 엑세스될 때 요청 당 한 번씩 계산되고 요청의 수명 동안 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-490">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="553d1-491">다음 예제에는 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>appsettings.json*이 변경된 후 새* 을 만드는 방법이 설명되어 있습니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-491">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="553d1-492">서버에 대한 여러 요청은 파일이 변경되고 구성이 다시 로드될 때까지 *appsettings.json* 파일에서 제공하는 상수 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-492">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="553d1-493">다음 이미지는 `option1`appsettings.json`option2` 파일에서 로드된 초기 *및* 값을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-493">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="553d1-494">*appsettings.json* 파일의 값을 `value1_from_json UPDATED` 및 `200`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-494">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="553d1-495">*appsettings.json* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-495">Save the *appsettings.json* file.</span></span> <span data-ttu-id="553d1-496">옵션 값이 변경되었음을 확인하려면 브라우저를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-496">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="553d1-497">IConfigureNamedOptions로 명명된 옵션 지원</span><span class="sxs-lookup"><span data-stu-id="553d1-497">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="553d1-498"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>로 명명된 옵션 지원은 샘플 앱에 예제 6으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-498">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="553d1-499">앱은 명명된 옵션 지원을 통해 명명된 옵션 구성들을 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-499">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="553d1-500">샘플 앱에서 명명된 옵션은 [ConfigureNamedOptions](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)TOptions>.Configure[ 확장 메서드를 호출하는 \<OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)를 사용하여 선언됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-500">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="553d1-501">명명된 옵션은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-501">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="553d1-502">샘플 앱은 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*>을 사용하여 명명된 옵션에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="553d1-502">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="553d1-503">샘플 앱을 실행하면 명명된 옵션이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-503">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="553d1-504">`named_options_1` 값은 *appsettings.json* 파일에서 로드되는 구성에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-504">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="553d1-505">`named_options_2` 값은 다음에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-505">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="553d1-506">`named_options_2`에 대한 `ConfigureServices`의 `Option1` 대리자.</span><span class="sxs-lookup"><span data-stu-id="553d1-506">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="553d1-507">`Option2` 클래스에서 제공되는 `MyOptions`에 대한 기본값.</span><span class="sxs-lookup"><span data-stu-id="553d1-507">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="553d1-508">ConfigureAll 메서드를 사용하여 모든 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-508">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="553d1-509"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 메서드를 사용하여 모든 옵션 인스턴스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-509">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="553d1-510">다음 코드는 모든 구성 인스턴스에 대해 `Option1`을 공통 값으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-510">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="553d1-511">다음 코드를 `Startup.ConfigureServices` 메서드에 직접 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-511">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="553d1-512">코드를 추가한 후 샘플 앱을 실행하면 다음과 같은 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-512">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="553d1-513">모든 옵션은 명명된 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-513">All options are named instances.</span></span> <span data-ttu-id="553d1-514">기존 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 인스턴스는 `Options.DefaultName`인 `string.Empty` 인스턴스를 대상으로 지정한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-514">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="553d1-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>는 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>도 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="553d1-516"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>의 기본 구현에는 각 옵션을 적절하게 사용하기 위한 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-516">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="553d1-517">`null` 명명된 옵션은 특정 명명된 인스턴스 대신 모든 명명된 인스턴스를 대상으로 지정하는 데 사용됩니다(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 및 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>에서 이 규칙을 사용함).</span><span class="sxs-lookup"><span data-stu-id="553d1-517">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="553d1-518">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="553d1-518">OptionsBuilder API</span></span>

<span data-ttu-id="553d1-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>는 `TOptions` 인스턴스를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="553d1-520">`OptionsBuilder`는 `AddOptions<TOptions>(string optionsName)` 호출에 대한 단일 매개 변수이므로 모든 후속 호출에 나타나는 대신 명명된 옵션 생성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-520">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="553d1-521">옵션 유효성 검사 및 서비스 종속성을 허용하는 `ConfigureOptions` 오버로드는 `OptionsBuilder`를 통해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-521">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="553d1-522">DI 서비스를 사용하여 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-522">Use DI services to configure options</span></span>

<span data-ttu-id="553d1-523">다음과 같은 두 가지 방법으로 옵션을 구성하는 동안 종속성 주입을 통해 다른 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-523">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="553d1-524">구성 대리자를 [OptionsBuilder](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)TOptions>[의 \<Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-524">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="553d1-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)는 최대 5개의 서비스를 사용하여 옵션을 구성할 수 있는 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)의 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="553d1-526"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 또는 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>를 구현하는 고유의 형식을 만들고 해당 형식을 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-526">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="553d1-527">서비스를 만드는 것이 더 복잡하기 때문에 구성 대리자를 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-527">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="553d1-528">고유의 형식을 만드는 것은 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)를 사용할 때 프레임워크가 수행하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-528">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="553d1-529">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)을 호출하면 지정된 일반 서비스 유형을 허용하는 생성자가 있는 일시적인 일반 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-529">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="553d1-530">옵션 사후 구성</span><span class="sxs-lookup"><span data-stu-id="553d1-530">Options post-configuration</span></span>

<span data-ttu-id="553d1-531"><xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하여 사후 구성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-531">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="553d1-532">사후 구성은 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-532">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="553d1-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>를 사용하여 명명된 옵션을 사후 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="553d1-534">모든 구성 인스턴스를 사후 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-534">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="553d1-535">시작하는 동안 옵션 액세스</span><span class="sxs-lookup"><span data-stu-id="553d1-535">Accessing options during startup</span></span>

<span data-ttu-id="553d1-536"><xref:Microsoft.Extensions.Options.IOptions%601> 메서드가 실행되기 전에 서비스가 만들어지므로 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `Startup.Configure` 및 `Configure`를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-536"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="553d1-537"><xref:Microsoft.Extensions.Options.IOptions%601>에서는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 또는 `Startup.ConfigureServices`를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="553d1-537">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="553d1-538">서비스 등록의 순서 지정으로 인해 일관성 없는 옵션 상태가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="553d1-538">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="553d1-539">추가 자료</span><span class="sxs-lookup"><span data-stu-id="553d1-539">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
