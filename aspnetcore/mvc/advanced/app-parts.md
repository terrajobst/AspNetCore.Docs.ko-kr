---
title: ASP.NET Core에서 응용 프로그램 파트
author: rick-anderson
description: ASP.NET Core의 응용 프로그램 파트를 사용하여 컨트롤러, 보기, Razor Pages 등을 공유
ms.author: riande
ms.date: 05/14/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 4b4c8c554a7045a180b56cf9998ab1a8496cde1b
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207355"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a>ASP.NET Core의 응용 프로그램 파트를 사용하여 컨트롤러, 보기, Razor Pages 등을 공유

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([다운로드 방법](xref:index#how-to-download-a-sample))

*응용 프로그램 파트*는 앱의 리소스에 대한 추상화입니다. ASP.NET Core는 응용 프로그램 파트를 사용하여 컨트롤러, 보기 구성 요소, 태그 도우미, Razor Pages, Razor 컴파일 원본 등을 찾을 수 있습니다. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart)는 응용 프로그램 파트입니다. `AssemblyPart`는 어셈블리 참조를 캡슐화하고 형식 및 컴파일 참조를 노출합니다.

*기능 공급자*는 응용 프로그램 파트를 사용하여 ASP.NET Core 앱의 기능을 채웁니다. 응용 프로그램 파트의 주요 사용 사례는 어셈블리로부터 ASP.NET Core 기능을 찾도록(또는 로드하지 않도록) 앱을 구성하는 것입니다. 예를 들어 여러 앱 간에 공통적인 기능은 공유하는 것이 좋습니다. 응용 프로그램 파트를 사용하면 여러 앱에서 컨트롤러, 보기, Razor Pages, Razor 컴파일 원본, 태그 도우미 등을 포함하고 있는 어셈블리(DLL)를 공유할 수 있습니다. 여러 프로젝트에서 코드를 복제하기 보다 어셈블리를 공유하는 것이 좋습니다.

ASP.NET Core 앱은 <xref:System.Web.WebPages.ApplicationPart>로부터 기능을 로드합니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 클래스는 어셈블리에서 지원하는 응용 프로그램 파트를 나타냅니다.

## <a name="load-aspnet-core-features"></a>ASP.NET Core 기능 로드

`ApplicationPart` 및 `AssemblyPart` 클래스를 사용하여 ASP.NET Core 기능(컨트롤러, 보기 구성 요소 등)을 검색 및 로드할 수 있습니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>는 사용할 수 있는 응용 프로그램 파트 및 기능 공급자를 추적합니다. `ApplicationPartManager`는 `Startup.ConfigureServices`에서 구성됩니다.

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

다음 코드는 `AssemblyPart`를 사용하여 `ApplicationPartManager`를 구성하는 대체 방법을 제공합니다.

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

이전 두 코드 예제는 어셈블리로부터 `SharedController`를 로드합니다. 이 `SharedController`는 응용 프로그램의 프로젝트에 포함되어 있지 않습니다. [WebAppParts 솔루션](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) 예제 다운로드를 참조하세요.

### <a name="include-views"></a>보기 포함

보기를 어셈블리에 포함하려면:

* 다음 태그를 공유 프로젝트 파일에 추가합니다.

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>에 <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider>를 추가합니다.

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a>리소스 로드 방지

응용 프로그램 파트를 사용하여 특정 어셈블리나 위치에서 리소스가 로드되는 것을 *방지*할 수 있습니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 컬렉션의 멤버를 추가 또는 제거하여 리소스를 숨기거나 사용 가능하게 만듭니다. `ApplicationParts` 컬렉션의 항목 순서는 중요하지 않습니다. 컨테이너에서 서비스를 구성하는 데 사용하기 전에 `ApplicationPartManager`를 구성하세요. 예를 들어 `AddControllersAsServices`를 호출하기 전에 `ApplicationPartManager`를 구성합니다. `ApplicationParts` 컬렉션에서 `Remove`를 호출하여 리소스를 제거합니다.

다음 코드는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>를 사용하여 앱에서 `MyDependentLibrary`를 제거합니다.

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

`ApplicationPartManager`는 다음에 대한 파트를 포함합니다.

* 앱의 어셈블리 및 종속 어셈블리.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>응용 프로그램 기능 공급자

응용 프로그램 기능 공급자는 응용 프로그램 파트를 검사하고 해당 파트에 대한 기능을 제공합니다. 다음 ASP.NET Core 기능에는 기본으로 제공되는 기능 공급자가 있습니다.

* [컨트롤러](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [태그 도우미](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [보기 구성 요소](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

기능 공급자는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>에서 상속되며, 여기서 `T`는 기능의 형식입니다. 기능 공급자는 이전에 나열된 모든 기능 형식에 대해 구현할 수 있습니다. `ApplicationPartManager.FeatureProviders`의 기능 공급자 순서는 런타임 동작에 영향을 줄 수 있습니다. 나중에 추가된 공급자는 이전에 추가된 공급자가 수행한 작업에 반응할 수 있습니다.

### <a name="generic-controller-feature"></a>제네릭 컨트롤러 기능

ASP.NET Core는 [제네릭 컨트롤러](/dotnet/csharp/programming-guide/generics/generic-classes)를 무시합니다. 제네릭 컨트롤러에는 형식 매개 변수(예: `MyController<T>`)가 존재합니다. 다음 예제는 지정된 형식 목록에 대한 제네릭 컨트롤러 인스턴스를 추가합니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

대상 형식은 `EntityTypes.Types`에 정의됩니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

기능 공급자는 `Startup`에서 추가됩니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

라우팅에 사용되는 제네릭 컨트롤러 이름은 *위젯*이 아닌 *GenericController`1[위젯]* 의 형식입니다. 다음 특성은 컨트롤러에서 사용하는 제네릭 형식에 대응하도록 이름을 수정합니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

`GenericController` 클래스는 다음과 같습니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

예를 들어 `https://localhost:5001/Sprocket`의 URL을 요청하면 다음 응답이 발생합니다.

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a>사용 가능한 기능 표시

앱에서 사용 가능한 기능은 [종속성 주입](../../fundamentals/dependency-injection.md)을 통해 `ApplicationPartManager`를 요청하여 열거할 수 있습니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[예제 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)는 이전 코드를 사용하여 앱 기능을 표시합니다.

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
