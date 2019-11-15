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
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a>ASP.NET Core의 애플리케이션 파트를 사용하여 컨트롤러, 보기, Razor Pages 등을 공유

::: moniker range=">= aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([다운로드 방법](xref:index#how-to-download-a-sample))

*애플리케이션 파트*는 앱의 리소스에 대한 추상화입니다. 애플리케이션 파트를 사용하면 ASP.NET Core 컨트롤러, 보기 구성 요소, 태그 도우미, Razor Pages, Razor 컴파일 소스 등을 검색할 수 있습니다. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart)는 애플리케이션 파트입니다. `AssemblyPart`는 어셈블리 참조를 캡슐화하고 형식 및 컴파일 참조를 공개합니다.

*기능 공급자*는 애플리케이션 파트를 사용하여 ASP.NET Core 앱의 기능을 구성합니다. 애플리케이션 파트에 대한 주요 사용 사례는 어셈블리에서 ASP.NET Core 기능을 검색(또는 로드를 방지)하도록 앱을 구성하는 것입니다. 예를 들어 여러 앱 간에 공통적인 기능을 공유하는 것이 좋습니다. 애플리케이션 파트를 사용하여 여러 앱에서 컨트롤러, 보기, Razor Pages, Razor 컴파일 소스, 태그 도우미 등을 포함하는 어셈블리(DLL)를 공유할 수 있습니다. 여러 프로젝트에서 코드를 복제하려면 어셈블리를 공유하는 것이 좋습니다.

ASP.NET Core 앱은 <xref:System.Web.WebPages.ApplicationPart>로부터 기능을 로드합니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 클래스는 어셈블리에서 지원하는 애플리케이션 파트를 나타냅니다.

## <a name="load-aspnet-core-features"></a>ASP.NET Core 기능 로드

`ApplicationPart` 및 `AssemblyPart` 클래스를 사용하여 ASP.NET Core 기능(컨트롤러, 보기 구성 요소 등)을 검색 및 로드합니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>는 사용할 수 있는 애플리케이션 파트 및 기능 공급자를 추적합니다. `ApplicationPartManager`는 `Startup.ConfigureServices`에 구성되어 있습니다.

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

다음 코드는 `AssemblyPart`를 사용하여 `ApplicationPartManager` 구성에 대한 대체 방법을 제공합니다.

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

위의 두 코드 샘플은 어셈블리로부터 `SharedController`를 로드합니다. `SharedController`는 애플리케이션의 프로젝트에 포함되지 않습니다. [WebAppParts 솔루션](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) 샘플 다운로드를 참조하세요.

### <a name="include-views"></a>보기 포함

어셈블리에 보기를 포함하려면:

* 다음 표시를 공유 프로젝트 파일에 추가합니다.

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider>를 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>에 추가합니다.

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a>리소스 로드 방지

애플리케이션 파트를 사용하여 리소스가 특정 어셈블리나 위치에서 로드되는 것을 *방지*할 수 있습니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 컬렉션의 멤버를 추가 또는 제거하여 리소스를 숨기거나 사용 가능하게 만듭니다. `ApplicationParts` 컬렉션의 항목 순서는 중요하지 않습니다. 컨테이너에서 서비스를 구성하는 데 사용하기 전에 `ApplicationPartManager`를 구성합니다. 예를 들어 `AddControllersAsServices`를 호출하기 전에 `ApplicationPartManager`를 구성합니다. `ApplicationParts` 컬렉션에서 `Remove`를 호출하여 리소스를 제거합니다.

다음 코드는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>를 사용하여 앱에서 `MyDependentLibrary`를 제거합니다. [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

`ApplicationPartManager`에는 다음을 위한 파트가 포함됩니다.

* 앱의 어셈블리 및 종속 어셈블리.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>애플리케이션 기능 공급자

애플리케이션 기능 공급자는 애플리케이션 파트를 검사하고 해당 파트에 대한 기능을 제공합니다. 다음 ASP.NET Core 기능에는 기본으로 제공되는 기능 공급자가 있습니다.

* [컨트롤러](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [태그 도우미](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [보기 구성 요소](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

기능 공급자는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>에서 상속되며, 여기서 `T`는 기능의 형식입니다. 기능 공급자는 이전에 나열된 모든 기능 형식에 대해 구현할 수 있습니다. `ApplicationPartManager.FeatureProviders`의 기능 공급자 순서는 런타임 동작에 영향을 줄 수 있습니다. 나중에 추가된 공급자는 이전에 추가된 공급자가 수행한 작업에 반응할 수 있습니다.

### <a name="generic-controller-feature"></a>제네릭 컨트롤러 기능

ASP.NET Core는 [제네릭 컨트롤러](/dotnet/csharp/programming-guide/generics/generic-classes)를 무시합니다. 제네릭 컨트롤러에는 형식 매개 변수(예: `MyController<T>`)가 있습니다. 다음 샘플은 지정된 형식 목록에 대한 제네릭 컨트롤러 인스턴스를 추가합니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

이 형식은 `EntityTypes.Types`에 정의됩니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

기능 공급자는 `Startup`에서 추가됩니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

라우팅에 사용되는 제네릭 컨트롤러 이름은 *위젯*이 아닌 *GenericController`1[위젯]* 의 형식입니다. 다음 특성은 컨트롤러에서 사용하는 제네릭 형식에 해당하도록 이름을 수정합니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

`GenericController` 클래스는 다음과 같습니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

예를 들어 `https://localhost:5001/Sprocket`의 URL을 요청하면 다음 응답이 발생합니다.

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a>사용 가능한 기능 표시

앱에 사용 가능한 기능은 [종속성 주입](../../fundamentals/dependency-injection.md)을 통해 `ApplicationPartManager`를 요청하여 열거할 수 있습니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[샘플 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)는 위의 코드를 사용하여 앱 기능을 표시합니다.

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

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([다운로드 방법](xref:index#how-to-download-a-sample))

*애플리케이션 파트*는 앱의 리소스에 대한 추상화입니다. 애플리케이션 파트를 사용하면 ASP.NET Core 컨트롤러, 보기 구성 요소, 태그 도우미, Razor Pages, Razor 컴파일 소스 등을 검색할 수 있습니다. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart)는 애플리케이션 파트입니다. `AssemblyPart`는 어셈블리 참조를 캡슐화하고 형식 및 컴파일 참조를 공개합니다.

*기능 공급자*는 애플리케이션 파트를 사용하여 ASP.NET Core 앱의 기능을 구성합니다. 애플리케이션 파트에 대한 주요 사용 사례는 어셈블리에서 ASP.NET Core 기능을 검색(또는 로드를 방지)하도록 앱을 구성하는 것입니다. 예를 들어 여러 앱 간에 공통적인 기능을 공유하는 것이 좋습니다. 애플리케이션 파트를 사용하여 여러 앱에서 컨트롤러, 보기, Razor Pages, Razor 컴파일 소스, 태그 도우미 등을 포함하는 어셈블리(DLL)를 공유할 수 있습니다. 여러 프로젝트에서 코드를 복제하려면 어셈블리를 공유하는 것이 좋습니다.

ASP.NET Core 앱은 <xref:System.Web.WebPages.ApplicationPart>로부터 기능을 로드합니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 클래스는 어셈블리에서 지원하는 애플리케이션 파트를 나타냅니다.

## <a name="load-aspnet-core-features"></a>ASP.NET Core 기능 로드

`ApplicationPart` 및 `AssemblyPart` 클래스를 사용하여 ASP.NET Core 기능(컨트롤러, 보기 구성 요소 등)을 검색 및 로드합니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>는 사용할 수 있는 애플리케이션 파트 및 기능 공급자를 추적합니다. `ApplicationPartManager`는 `Startup.ConfigureServices`에 구성되어 있습니다.

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

다음 코드는 `AssemblyPart`를 사용하여 `ApplicationPartManager` 구성에 대한 대체 방법을 제공합니다.

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

위의 두 코드 샘플은 어셈블리로부터 `SharedController`를 로드합니다. `SharedController`는 애플리케이션의 프로젝트에 포함되지 않습니다. [WebAppParts 솔루션](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) 샘플 다운로드를 참조하세요.

### <a name="include-views"></a>보기 포함

어셈블리에 보기를 포함하려면:

* 다음 표시를 공유 프로젝트 파일에 추가합니다.

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider>를 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>에 추가합니다.

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a>리소스 로드 방지

애플리케이션 파트를 사용하여 리소스가 특정 어셈블리나 위치에서 로드되는 것을 *방지*할 수 있습니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 컬렉션의 멤버를 추가 또는 제거하여 리소스를 숨기거나 사용 가능하게 만듭니다. `ApplicationParts` 컬렉션의 항목 순서는 중요하지 않습니다. 컨테이너에서 서비스를 구성하는 데 사용하기 전에 `ApplicationPartManager`를 구성합니다. 예를 들어 `AddControllersAsServices`를 호출하기 전에 `ApplicationPartManager`를 구성합니다. `ApplicationParts` 컬렉션에서 `Remove`를 호출하여 리소스를 제거합니다.

다음 코드는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>를 사용하여 앱에서 `MyDependentLibrary`를 제거합니다.[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

`ApplicationPartManager`에는 다음을 위한 파트가 포함됩니다.

* 앱의 어셈블리 및 종속 어셈블리.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>애플리케이션 기능 공급자

애플리케이션 기능 공급자는 애플리케이션 파트를 검사하고 해당 파트에 대한 기능을 제공합니다. 다음 ASP.NET Core 기능에는 기본으로 제공되는 기능 공급자가 있습니다.

* [컨트롤러](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [태그 도우미](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [보기 구성 요소](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

기능 공급자는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>에서 상속되며, 여기서 `T`는 기능의 형식입니다. 기능 공급자는 이전에 나열된 모든 기능 형식에 대해 구현할 수 있습니다. `ApplicationPartManager.FeatureProviders`의 기능 공급자 순서는 런타임 동작에 영향을 줄 수 있습니다. 나중에 추가된 공급자는 이전에 추가된 공급자가 수행한 작업에 반응할 수 있습니다.

### <a name="generic-controller-feature"></a>제네릭 컨트롤러 기능

ASP.NET Core는 [제네릭 컨트롤러](/dotnet/csharp/programming-guide/generics/generic-classes)를 무시합니다. 제네릭 컨트롤러에는 형식 매개 변수(예: `MyController<T>`)가 있습니다. 다음 샘플은 지정된 형식 목록에 대한 제네릭 컨트롤러 인스턴스를 추가합니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

이 형식은 `EntityTypes.Types`에 정의됩니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

기능 공급자는 `Startup`에서 추가됩니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

라우팅에 사용되는 제네릭 컨트롤러 이름은 *위젯*이 아닌 *GenericController`1[위젯]* 의 형식입니다. 다음 특성은 컨트롤러에서 사용하는 제네릭 형식에 해당하도록 이름을 수정합니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

`GenericController` 클래스는 다음과 같습니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

예를 들어 `https://localhost:5001/Sprocket`의 URL을 요청하면 다음 응답이 발생합니다.

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a>사용 가능한 기능 표시

앱에 사용 가능한 기능은 [종속성 주입](../../fundamentals/dependency-injection.md)을 통해 `ApplicationPartManager`를 요청하여 열거할 수 있습니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[샘플 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)는 위의 코드를 사용하여 앱 기능을 표시합니다.

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
