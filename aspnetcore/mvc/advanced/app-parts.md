---
title: ASP.NET Core에서 애플리케이션 파트
author: ardalis
description: 앱의 리소스에 대한 추상화인 애플리케이션 파트를 사용하여 어셈블리에서 기능을 검색하거나 로드하지 않도록 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 01/04/2017
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 4900ccf5589500db076f8cecd9da198c6a7ceea4
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886460"
---
<!-- DO NOT MAKE CHANGES BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12376 Merges -->

# <a name="application-parts-in-aspnet-core"></a><span data-ttu-id="1acfe-103">ASP.NET Core에서 애플리케이션 파트</span><span class="sxs-lookup"><span data-stu-id="1acfe-103">Application Parts in ASP.NET Core</span></span>

<span data-ttu-id="1acfe-104">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1acfe-104">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1acfe-105">*애플리케이션 파트*는 컨트롤러, 보기 구성 요소 또는 태그 도우미와 같은 MVC 기능이 검색할 수 있는 애플리케이션의 리소스에 대한 추상화입니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-105">An *Application Part* is an abstraction over the resources of an application, from which MVC features like controllers, view components, or tag helpers may be discovered.</span></span> <span data-ttu-id="1acfe-106">애플리케이션 파트의 한 가지 예로는 AssemblyPart가 있습니다. 여기서는 어셈블리 참조를 캡슐화하고 표시 유형 및 컴파일 참조를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-106">One example of an application part is an AssemblyPart, which encapsulates an assembly reference and exposes types and compilation references.</span></span> <span data-ttu-id="1acfe-107">*기능 공급자*는 애플리케이션 파트를 사용하여 ASP.NET Core MVC 앱의 기능을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-107">*Feature providers* work with application parts to populate the features of an ASP.NET Core MVC app.</span></span> <span data-ttu-id="1acfe-108">애플리케이션 파트에 대한 주요 사용 사례는 어셈블리에서 MVC 기능을 검색(또는 로드를 방지)하도록 앱을 구성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-108">The main use case for application parts is to allow you to configure your app to discover (or avoid loading) MVC features from an assembly.</span></span>

## <a name="introducing-application-parts"></a><span data-ttu-id="1acfe-109">애플리케이션 파트 소개</span><span class="sxs-lookup"><span data-stu-id="1acfe-109">Introducing Application Parts</span></span>

<span data-ttu-id="1acfe-110">MVC 앱은 [애플리케이션 파트](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart)에서 해당 기능을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-110">MVC apps load their features from [application parts](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span></span> <span data-ttu-id="1acfe-111">특히 [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart) 클래스는 어셈블리에서 지원하는 애플리케이션 파트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-111">In particular, the [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart) class represents an application part that's backed by an assembly.</span></span> <span data-ttu-id="1acfe-112">이러한 클래스를 사용하여 컨트롤러, 보기 구성 요소, 태그 도우미 및 Razor 컴파일 원본과 같은 MVC 기능을 검색하고 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-112">You can use these classes to discover and load MVC features, such as controllers, view components, tag helpers, and razor compilation sources.</span></span> <span data-ttu-id="1acfe-113">[ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager)는 MVC 앱에 사용할 수 있는 애플리케이션 파트 및 기능 공급자를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-113">The [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) is responsible for tracking the application parts and feature providers available to the MVC app.</span></span> <span data-ttu-id="1acfe-114">MVC를 구성하는 경우 `Startup`에서 `ApplicationPartManager`와 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-114">You can interact with the `ApplicationPartManager` in `Startup` when you configure MVC:</span></span>

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services.AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services.AddMvc()
    .ConfigureApplicationPartManager(apm => apm.ApplicationParts.Add(part));
```

<span data-ttu-id="1acfe-115">기본적으로 MVC는 (다른 어셈블리에서도) 종속성 트리를 검색하고 컨트롤러를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-115">By default MVC will search the dependency tree and find controllers (even in other assemblies).</span></span> <span data-ttu-id="1acfe-116">임의의 어셈블리를 로드하려면(예: 컴파일 타임 시 참조되지 않는 플러그 인에서) 애플리케이션 파트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-116">To load an arbitrary assembly (for instance, from a plugin that isn't referenced at compile time), you can use an application part.</span></span>

<span data-ttu-id="1acfe-117">애플리케이션 파트를 사용하여 특정 어셈블리 또는 위치에서 컨트롤러를 찾지 않도록 *방지*할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-117">You can use application parts to *avoid* looking for controllers in a particular assembly or location.</span></span> <span data-ttu-id="1acfe-118">`ApplicationPartManager`의 `ApplicationParts` 컬렉션을 수정하여 앱에 사용할 수 있는 파트(또는 어셈블리)를 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-118">You can control which parts (or assemblies) are available to the app by modifying the `ApplicationParts` collection of the `ApplicationPartManager`.</span></span> <span data-ttu-id="1acfe-119">`ApplicationParts` 컬렉션에 있는 항목의 순서는 중요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-119">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="1acfe-120">컨테이너에서 서비스를 구성하는 데 사용하기 전에 `ApplicationPartManager`를 완전히 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-120">It's important to fully configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="1acfe-121">예를 들어 `AddControllersAsServices`를 호출하기 전에 `ApplicationPartManager`를 완벽하게 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-121">For example, you should fully configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="1acfe-122">이 작업에 실패하면 메서드 호출이 이후에 추가된 애플리케이션 파트의 컨트롤러가 애플리케이션에 잘못된 동작이 발생할 수 있는 영향을 받지 않는다는 것을 의미합니다(서비스로 등록되지 않음).</span><span class="sxs-lookup"><span data-stu-id="1acfe-122">Failing to do so, will mean that controllers in application parts added after that method call won't be affected (won't get registered as services) which might result in incorrect behavior of your application.</span></span>

<span data-ttu-id="1acfe-123">사용하지 않을 컨트롤러를 포함하는 어셈블리가 있는 경우 `ApplicationPartManager`에서 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-123">If you have an assembly that contains controllers you don't want to be used, remove it from the `ApplicationPartManager`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm =>
    {
        var dependentLibrary = apm.ApplicationParts
            .FirstOrDefault(part => part.Name == "DependentLibrary");

        if (dependentLibrary != null)
        {
           apm.ApplicationParts.Remove(dependentLibrary);
        }
    })
```

<span data-ttu-id="1acfe-124">`ApplicationPartManager`에는 프로젝트의 어셈블리 및 해당 종속 어셈블리 외에도 기본적으로 `Microsoft.AspNetCore.Mvc.TagHelpers` 및 `Microsoft.AspNetCore.Mvc.Razor`의 파트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-124">In addition to your project's assembly and its dependent assemblies, the `ApplicationPartManager` will include parts for `Microsoft.AspNetCore.Mvc.TagHelpers` and `Microsoft.AspNetCore.Mvc.Razor` by default.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="1acfe-125">애플리케이션 기능 공급자</span><span class="sxs-lookup"><span data-stu-id="1acfe-125">Application Feature Providers</span></span>

<span data-ttu-id="1acfe-126">애플리케이션 기능 공급자는 애플리케이션 파트를 검사하고 해당 파트에 대한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-126">Application Feature Providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="1acfe-127">다음 MVC 기능에 대한 기본 제공 기능 공급자가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-127">There are built-in feature providers for the following MVC features:</span></span>

* [<span data-ttu-id="1acfe-128">Controllers</span><span class="sxs-lookup"><span data-stu-id="1acfe-128">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="1acfe-129">메타데이터 참조</span><span class="sxs-lookup"><span data-stu-id="1acfe-129">Metadata Reference</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [<span data-ttu-id="1acfe-130">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="1acfe-130">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="1acfe-131">보기 구성 요소</span><span class="sxs-lookup"><span data-stu-id="1acfe-131">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="1acfe-132">기능 공급자는 `IApplicationFeatureProvider<T>`에서 상속됩니다. 여기서 `T`는 기능의 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-132">Feature providers inherit from `IApplicationFeatureProvider<T>`, where `T` is the type of the feature.</span></span> <span data-ttu-id="1acfe-133">위에 나열된 MVC 기능 형식에 대한 고유한 공급자를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-133">You can implement your own feature providers for any of MVC's feature types listed above.</span></span> <span data-ttu-id="1acfe-134">나중에 공급자가 이전 공급자 수행한 작업에 반응할 수 있으므로 `ApplicationPartManager.FeatureProviders` 컬렉션에서 기능 공급자의 순서는 중요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-134">The order of feature providers in the `ApplicationPartManager.FeatureProviders` collection can be important, since later providers can react to actions taken by previous providers.</span></span>

### <a name="sample-generic-controller-feature"></a><span data-ttu-id="1acfe-135">예제: 제네릭 컨트롤러 기능</span><span class="sxs-lookup"><span data-stu-id="1acfe-135">Sample: Generic controller feature</span></span>

<span data-ttu-id="1acfe-136">기본적으로 ASP.NET Core MVC는 제네릭 컨트롤러(예: `SomeController<T>`)를 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-136">By default, ASP.NET Core MVC ignores generic controllers (for example, `SomeController<T>`).</span></span> <span data-ttu-id="1acfe-137">이 샘플에서는 기본 공급자 이후에 실행되고 지정된 목록 형식에 대한 제네릭 컨트롤러 인스턴스를 추가하는 컨트롤러 기능 공급자를 사용합니다(`EntityTypes.Types`에 정의됨).</span><span class="sxs-lookup"><span data-stu-id="1acfe-137">This sample uses a controller feature provider that runs after the default provider and adds generic controller instances for a specified list of types (defined in `EntityTypes.Types`):</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

<span data-ttu-id="1acfe-138">엔터티 형식:</span><span class="sxs-lookup"><span data-stu-id="1acfe-138">The entity types:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

<span data-ttu-id="1acfe-139">기능 공급자는 `Startup`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-139">The feature provider is added in `Startup`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm => 
        apm.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

<span data-ttu-id="1acfe-140">기본적으로 라우팅에 사용되는 제네릭 컨트롤러 이름은 *위젯*이 아닌 *GenericController\`1[위젯]* 의 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-140">By default, the generic controller names used for routing would be of the form *GenericController\`1[Widget]* instead of *Widget*.</span></span> <span data-ttu-id="1acfe-141">다음 특성을 사용하여 컨트롤러에서 사용하는 제네릭 형식에 해당하도록 이름을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-141">The following attribute is used to modify the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="1acfe-142">`GenericController` 클래스:</span><span class="sxs-lookup"><span data-stu-id="1acfe-142">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

<span data-ttu-id="1acfe-143">일치하는 경로를 요청하는 경우 결과:</span><span class="sxs-lookup"><span data-stu-id="1acfe-143">The result, when a matching route is requested:</span></span>

![샘플 앱의 예제 출력은 '제네릭 Sproket 컨트롤러에서 인사드립니다.'로 읽습니다.](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a><span data-ttu-id="1acfe-145">예제: 사용 가능 기능 표시</span><span class="sxs-lookup"><span data-stu-id="1acfe-145">Sample: Display available features</span></span>

<span data-ttu-id="1acfe-146">[종속성 주입](../../fundamentals/dependency-injection.md)을 통해 `ApplicationPartManager`을 요청하고 적절한 기능의 인스턴스를 채우는 데 사용하여 앱에 제공되는 채워진 기능을 반복할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1acfe-146">You can iterate through the populated features available to your app by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md) and using it to populate instances of the appropriate features:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="1acfe-147">예제 출력:</span><span class="sxs-lookup"><span data-stu-id="1acfe-147">Example output:</span></span>

![샘플 앱의 예제 출력](app-parts/_static/available-features.png)
