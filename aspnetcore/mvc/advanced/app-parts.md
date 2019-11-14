---
title: ASP.NET Core에서 응용 프로그램 파트
author: rick-anderson
description: ASP.NET Core의 애플리케이션 파트를 사용하여 컨트롤러, 보기, Razor Pages 등을 공유
ms.author: riande
ms.date: 11/10/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 92c408adfad8af3dfd2572b625ae28ef24f64948
ms.sourcegitcommit: a7bbe3890befead19440075b05b9674351f98872
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905759"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a><span data-ttu-id="9379f-103">ASP.NET Core의 애플리케이션 파트를 사용하여 컨트롤러, 보기, Razor Pages 등을 공유</span><span class="sxs-lookup"><span data-stu-id="9379f-103">Share controllers, views, Razor Pages and more with Application Parts in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9379f-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9379f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9379f-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9379f-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9379f-106">*애플리케이션 파트*는 앱의 리소스에 대한 추상화입니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="9379f-107">애플리케이션 파트를 사용하면 ASP.NET Core 컨트롤러, 보기 구성 요소, 태그 도우미, Razor Pages, Razor 컴파일 소스 등을 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="9379f-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart)는 애플리케이션 파트입니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="9379f-109">`AssemblyPart`는 어셈블리 참조를 캡슐화하고 형식 및 컴파일 참조를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="9379f-110">*기능 공급자*는 애플리케이션 파트를 사용하여 ASP.NET Core 앱의 기능을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-110">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="9379f-111">애플리케이션 파트에 대한 주요 사용 사례는 어셈블리에서 ASP.NET Core 기능을 검색(또는 로드를 방지)하도록 앱을 구성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="9379f-112">예를 들어 여러 앱 간에 공통적인 기능을 공유하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="9379f-113">애플리케이션 파트를 사용하여 여러 앱에서 컨트롤러, 보기, Razor Pages, Razor 컴파일 소스, 태그 도우미 등을 포함하는 어셈블리(DLL)를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="9379f-114">여러 프로젝트에서 코드를 복제하려면 어셈블리를 공유하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="9379f-115">ASP.NET Core 앱은 <xref:System.Web.WebPages.ApplicationPart>로부터 기능을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="9379f-116"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 클래스는 어셈블리에서 지원하는 애플리케이션 파트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="9379f-117">ASP.NET Core 기능 로드</span><span class="sxs-lookup"><span data-stu-id="9379f-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="9379f-118">`ApplicationPart` 및 `AssemblyPart` 클래스를 사용하여 ASP.NET Core 기능(컨트롤러, 보기 구성 요소 등)을 검색 및 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-118">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="9379f-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>는 사용할 수 있는 애플리케이션 파트 및 기능 공급자를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="9379f-120">`ApplicationPartManager`는 `Startup.ConfigureServices`에 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="9379f-121">다음 코드는 `AssemblyPart`를 사용하여 `ApplicationPartManager` 구성에 대한 대체 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="9379f-122">위의 두 코드 샘플은 어셈블리로부터 `SharedController`를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="9379f-123">`SharedController`는 애플리케이션의 프로젝트에 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-123">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="9379f-124">[WebAppParts 솔루션](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) 샘플 다운로드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9379f-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="9379f-125">보기 포함</span><span class="sxs-lookup"><span data-stu-id="9379f-125">Include views</span></span>

<span data-ttu-id="9379f-126">어셈블리에 보기를 포함하려면:</span><span class="sxs-lookup"><span data-stu-id="9379f-126">To include views in the assembly:</span></span>

* <span data-ttu-id="9379f-127">다음 표시를 공유 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-127">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="9379f-128"><xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider>를 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-128">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="9379f-129">리소스 로드 방지</span><span class="sxs-lookup"><span data-stu-id="9379f-129">Prevent loading resources</span></span>

<span data-ttu-id="9379f-130">애플리케이션 파트를 사용하여 리소스가 특정 어셈블리나 위치에서 로드되는 것을 *방지*할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-130">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="9379f-131"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 컬렉션의 멤버를 추가 또는 제거하여 리소스를 숨기거나 사용 가능하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-131">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="9379f-132">`ApplicationParts` 컬렉션의 항목 순서는 중요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-132">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="9379f-133">컨테이너에서 서비스를 구성하는 데 사용하기 전에 `ApplicationPartManager`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-133">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="9379f-134">예를 들어 `AddControllersAsServices`를 호출하기 전에 `ApplicationPartManager`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-134">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="9379f-135">`ApplicationParts` 컬렉션에서 `Remove`를 호출하여 리소스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-135">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="9379f-136">다음 코드는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>를 사용하여 앱에서 `MyDependentLibrary`를 제거합니다. [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="9379f-136">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="9379f-137">`ApplicationPartManager`에는 다음을 위한 파트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-137">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="9379f-138">앱의 어셈블리 및 종속 어셈블리.</span><span class="sxs-lookup"><span data-stu-id="9379f-138">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="9379f-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="9379f-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="9379f-140">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="9379f-140">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="9379f-141">애플리케이션 기능 공급자</span><span class="sxs-lookup"><span data-stu-id="9379f-141">Application feature providers</span></span>

<span data-ttu-id="9379f-142">애플리케이션 기능 공급자는 애플리케이션 파트를 검사하고 해당 파트에 대한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-142">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="9379f-143">다음 ASP.NET Core 기능에는 기본으로 제공되는 기능 공급자가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-143">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="9379f-144">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="9379f-144">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="9379f-145">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="9379f-145">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="9379f-146">보기 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9379f-146">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="9379f-147">기능 공급자는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>에서 상속됩니다. 여기서 `T`는 기능의 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-147">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="9379f-148">기능 공급자는 이전에 나열된 임의의 기능 형식에 대해 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-148">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="9379f-149">`ApplicationPartManager.FeatureProviders`에서 기능 공급자의 순서는 런타임 동작에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-149">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="9379f-150">나중에 추가된 공급자는 이전에 추가된 공급자가 수행한 작업에 반응할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-150">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="9379f-151">제네릭 컨트롤러 기능</span><span class="sxs-lookup"><span data-stu-id="9379f-151">Generic controller feature</span></span>

<span data-ttu-id="9379f-152">ASP.NET Core는 [제네릭 컨트롤러](/dotnet/csharp/programming-guide/generics/generic-classes)를 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-152">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="9379f-153">제네릭 컨트롤러에는 형식 매개 변수(예: `MyController<T>`)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-153">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="9379f-154">다음 샘플은 지정된 형식 목록에 대한 제네릭 컨트롤러 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-154">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="9379f-155">이 형식은 `EntityTypes.Types`에 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-155">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="9379f-156">기능 공급자는 `Startup`에서 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-156">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="9379f-157">라우팅에 사용되는 제네릭 컨트롤러 이름은 *위젯*이 아닌 *GenericController\`1[위젯]* 의 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-157">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="9379f-158">다음 특성은 컨트롤러에서 사용하는 제네릭 형식에 해당하도록 이름을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-158">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="9379f-159">`GenericController` 클래스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-159">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="9379f-160">예를 들어 `https://localhost:5001/Sprocket`의 URL을 요청하면 다음 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-160">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="9379f-161">사용 가능한 기능 표시</span><span class="sxs-lookup"><span data-stu-id="9379f-161">Display available features</span></span>

<span data-ttu-id="9379f-162">앱에 사용 가능한 기능은 [종속성 주입](../../fundamentals/dependency-injection.md)을 통해 `ApplicationPartManager`를 요청하여 열거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-162">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="9379f-163">[샘플 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)는 위의 코드를 사용하여 앱 기능을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-163">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9379f-164">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9379f-164">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9379f-165">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9379f-165">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9379f-166">*애플리케이션 파트*는 앱의 리소스에 대한 추상화입니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-166">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="9379f-167">애플리케이션 파트를 사용하면 ASP.NET Core 컨트롤러, 보기 구성 요소, 태그 도우미, Razor Pages, Razor 컴파일 소스 등을 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-167">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="9379f-168">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart)는 애플리케이션 파트입니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-168">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="9379f-169">`AssemblyPart`는 어셈블리 참조를 캡슐화하고 형식 및 컴파일 참조를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-169">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="9379f-170">*기능 공급자*는 애플리케이션 파트를 사용하여 ASP.NET Core 앱의 기능을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-170">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="9379f-171">애플리케이션 파트에 대한 주요 사용 사례는 어셈블리에서 ASP.NET Core 기능을 검색(또는 로드를 방지)하도록 앱을 구성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-171">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="9379f-172">예를 들어 여러 앱 간에 공통적인 기능을 공유하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-172">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="9379f-173">애플리케이션 파트를 사용하여 여러 앱에서 컨트롤러, 보기, Razor Pages, Razor 컴파일 소스, 태그 도우미 등을 포함하는 어셈블리(DLL)를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-173">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="9379f-174">여러 프로젝트에서 코드를 복제하려면 어셈블리를 공유하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-174">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="9379f-175">ASP.NET Core 앱은 <xref:System.Web.WebPages.ApplicationPart>로부터 기능을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-175">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="9379f-176"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 클래스는 어셈블리에서 지원하는 애플리케이션 파트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-176">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="9379f-177">ASP.NET Core 기능 로드</span><span class="sxs-lookup"><span data-stu-id="9379f-177">Load ASP.NET Core features</span></span>

<span data-ttu-id="9379f-178">`ApplicationPart` 및 `AssemblyPart` 클래스를 사용하여 ASP.NET Core 기능(컨트롤러, 보기 구성 요소 등)을 검색 및 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-178">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="9379f-179"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>는 사용할 수 있는 애플리케이션 파트 및 기능 공급자를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-179">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="9379f-180">`ApplicationPartManager`는 `Startup.ConfigureServices`에 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-180">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="9379f-181">다음 코드는 `AssemblyPart`를 사용하여 `ApplicationPartManager` 구성에 대한 대체 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-181">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="9379f-182">위의 두 코드 샘플은 어셈블리로부터 `SharedController`를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-182">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="9379f-183">`SharedController`는 애플리케이션의 프로젝트에 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-183">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="9379f-184">[WebAppParts 솔루션](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) 샘플 다운로드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9379f-184">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="9379f-185">보기 포함</span><span class="sxs-lookup"><span data-stu-id="9379f-185">Include views</span></span>

<span data-ttu-id="9379f-186">어셈블리에 보기를 포함하려면:</span><span class="sxs-lookup"><span data-stu-id="9379f-186">To include views in the assembly:</span></span>

* <span data-ttu-id="9379f-187">다음 표시를 공유 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-187">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="9379f-188"><xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider>를 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-188">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="9379f-189">리소스 로드 방지</span><span class="sxs-lookup"><span data-stu-id="9379f-189">Prevent loading resources</span></span>

<span data-ttu-id="9379f-190">애플리케이션 파트를 사용하여 리소스가 특정 어셈블리나 위치에서 로드되는 것을 *방지*할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-190">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="9379f-191"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 컬렉션의 멤버를 추가 또는 제거하여 리소스를 숨기거나 사용 가능하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-191">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="9379f-192">`ApplicationParts` 컬렉션의 항목 순서는 중요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-192">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="9379f-193">컨테이너에서 서비스를 구성하는 데 사용하기 전에 `ApplicationPartManager`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-193">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="9379f-194">예를 들어 `AddControllersAsServices`를 호출하기 전에 `ApplicationPartManager`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-194">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="9379f-195">`ApplicationParts` 컬렉션에서 `Remove`를 호출하여 리소스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-195">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="9379f-196">다음 코드는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>를 사용하여 앱에서 `MyDependentLibrary`를 제거합니다. [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="9379f-196">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="9379f-197">`ApplicationPartManager`에는 다음을 위한 파트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-197">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="9379f-198">앱의 어셈블리 및 종속 어셈블리.</span><span class="sxs-lookup"><span data-stu-id="9379f-198">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="9379f-199">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="9379f-199">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="9379f-200">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="9379f-200">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="9379f-201">애플리케이션 기능 공급자</span><span class="sxs-lookup"><span data-stu-id="9379f-201">Application feature providers</span></span>

<span data-ttu-id="9379f-202">애플리케이션 기능 공급자는 애플리케이션 파트를 검사하고 해당 파트에 대한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-202">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="9379f-203">다음 ASP.NET Core 기능에는 기본으로 제공되는 기능 공급자가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-203">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="9379f-204">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="9379f-204">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="9379f-205">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="9379f-205">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="9379f-206">보기 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9379f-206">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="9379f-207">기능 공급자는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>에서 상속됩니다. 여기서 `T`는 기능의 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-207">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="9379f-208">기능 공급자는 이전에 나열된 임의의 기능 형식에 대해 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-208">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="9379f-209">`ApplicationPartManager.FeatureProviders`에서 기능 공급자의 순서는 런타임 동작에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-209">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="9379f-210">나중에 추가된 공급자는 이전에 추가된 공급자가 수행한 작업에 반응할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-210">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="9379f-211">제네릭 컨트롤러 기능</span><span class="sxs-lookup"><span data-stu-id="9379f-211">Generic controller feature</span></span>

<span data-ttu-id="9379f-212">ASP.NET Core는 [제네릭 컨트롤러](/dotnet/csharp/programming-guide/generics/generic-classes)를 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-212">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="9379f-213">제네릭 컨트롤러에는 형식 매개 변수(예: `MyController<T>`)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-213">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="9379f-214">다음 샘플은 지정된 형식 목록에 대한 제네릭 컨트롤러 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-214">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="9379f-215">이 형식은 `EntityTypes.Types`에 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-215">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="9379f-216">기능 공급자는 `Startup`에서 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-216">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="9379f-217">라우팅에 사용되는 제네릭 컨트롤러 이름은 *위젯*이 아닌 *GenericController\`1[위젯]* 의 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-217">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="9379f-218">다음 특성은 컨트롤러에서 사용하는 제네릭 형식에 해당하도록 이름을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-218">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="9379f-219">`GenericController` 클래스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-219">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="9379f-220">예를 들어 `https://localhost:5001/Sprocket`의 URL을 요청하면 다음 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-220">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="9379f-221">사용 가능한 기능 표시</span><span class="sxs-lookup"><span data-stu-id="9379f-221">Display available features</span></span>

<span data-ttu-id="9379f-222">앱에 사용 가능한 기능은 [종속성 주입](../../fundamentals/dependency-injection.md)을 통해 `ApplicationPartManager`를 요청하여 열거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-222">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="9379f-223">[샘플 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)는 위의 코드를 사용하여 앱 기능을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9379f-223">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

::: moniker-end
