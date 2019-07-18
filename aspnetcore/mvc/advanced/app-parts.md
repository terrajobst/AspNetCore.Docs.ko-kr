---
title: ASP.NET Core에서 애플리케이션 파트
author: ardalis
description: 앱의 리소스에 대한 추상화인 애플리케이션 파트를 사용하여 어셈블리에서 기능을 검색하거나 로드하지 않도록 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 01/04/2017
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 9d0b4b5fadcc287172f23fa36c421f04ca2ade4a
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815476"
---
# <a name="application-parts-in-aspnet-core"></a>ASP.NET Core에서 애플리케이션 파트

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

*애플리케이션 파트*는 컨트롤러, 보기 구성 요소 또는 태그 도우미와 같은 MVC 기능이 검색할 수 있는 애플리케이션의 리소스에 대한 추상화입니다. 애플리케이션 파트의 한 가지 예로는 AssemblyPart가 있습니다. 여기서는 어셈블리 참조를 캡슐화하고 표시 유형 및 컴파일 참조를 노출합니다. *기능 공급자*는 애플리케이션 파트를 사용하여 ASP.NET Core MVC 앱의 기능을 구성합니다. 애플리케이션 파트에 대한 주요 사용 사례는 어셈블리에서 MVC 기능을 검색(또는 로드를 방지)하도록 앱을 구성하는 것입니다.

## <a name="introducing-application-parts"></a>애플리케이션 파트 소개

MVC 앱은 [애플리케이션 파트](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart)에서 해당 기능을 로드합니다. 특히 [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart) 클래스는 어셈블리에서 지원하는 애플리케이션 파트를 나타냅니다. 이러한 클래스를 사용하여 컨트롤러, 보기 구성 요소, 태그 도우미 및 Razor 컴파일 원본과 같은 MVC 기능을 검색하고 로드할 수 있습니다. [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager)는 MVC 앱에 사용할 수 있는 애플리케이션 파트 및 기능 공급자를 추적합니다. MVC를 구성하는 경우 `Startup`에서 `ApplicationPartManager`와 상호 작용할 수 있습니다.

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

기본적으로 MVC는 (다른 어셈블리에서도) 종속성 트리를 검색하고 컨트롤러를 찾습니다. 임의의 어셈블리를 로드하려면(예: 컴파일 타임 시 참조되지 않는 플러그 인에서) 애플리케이션 파트를 사용할 수 있습니다.

애플리케이션 파트를 사용하여 특정 어셈블리 또는 위치에서 컨트롤러를 찾지 않도록 *방지*할 수 있습니다. `ApplicationPartManager`의 `ApplicationParts` 컬렉션을 수정하여 앱에 사용할 수 있는 파트(또는 어셈블리)를 제어할 수 있습니다. `ApplicationParts` 컬렉션에 있는 항목의 순서는 중요하지 않습니다. 컨테이너에서 서비스를 구성하는 데 사용하기 전에 `ApplicationPartManager`를 완전히 구성해야 합니다. 예를 들어 `AddControllersAsServices`를 호출하기 전에 `ApplicationPartManager`를 완벽하게 구성해야 합니다. 이 작업에 실패하면 메서드 호출이 이후에 추가된 애플리케이션 파트의 컨트롤러가 애플리케이션에 잘못된 동작이 발생할 수 있는 영향을 받지 않는다는 것을 의미합니다(서비스로 등록되지 않음).

사용하지 않을 컨트롤러를 포함하는 어셈블리가 있는 경우 `ApplicationPartManager`에서 제거합니다.

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

`ApplicationPartManager`에는 프로젝트의 어셈블리 및 해당 종속 어셈블리 외에도 기본적으로 `Microsoft.AspNetCore.Mvc.TagHelpers` 및 `Microsoft.AspNetCore.Mvc.Razor`의 파트가 포함됩니다.

## <a name="application-feature-providers"></a>애플리케이션 기능 공급자

애플리케이션 기능 공급자는 애플리케이션 파트를 검사하고 해당 파트에 대한 기능을 제공합니다. 다음 MVC 기능에 대한 기본 제공 기능 공급자가 있습니다.

* [Controllers](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [메타데이터 참조](/dotnet/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [태그 도우미](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [보기 구성 요소](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

기능 공급자는 `IApplicationFeatureProvider<T>`에서 상속됩니다. 여기서 `T`는 기능의 형식입니다. 위에 나열된 MVC 기능 형식에 대한 고유한 공급자를 구현할 수 있습니다. 나중에 공급자가 이전 공급자 수행한 작업에 반응할 수 있으므로 `ApplicationPartManager.FeatureProviders` 컬렉션에서 기능 공급자의 순서는 중요할 수 있습니다.

### <a name="sample-generic-controller-feature"></a>예제: 제네릭 컨트롤러 기능

기본적으로 ASP.NET Core MVC는 제네릭 컨트롤러(예: `SomeController<T>`)를 무시합니다. 이 샘플에서는 기본 공급자 이후에 실행되고 지정된 목록 형식에 대한 제네릭 컨트롤러 인스턴스를 추가하는 컨트롤러 기능 공급자를 사용합니다(`EntityTypes.Types`에 정의됨).

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

엔터티 형식:

[!code-csharp[](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

기능 공급자는 `Startup`에 추가됩니다.

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm => 
        apm.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

기본적으로 라우팅에 사용되는 제네릭 컨트롤러 이름은 *위젯*이 아닌 *GenericController`1[위젯]* 의 형식입니다. 다음 특성을 사용하여 컨트롤러에서 사용하는 제네릭 형식에 해당하도록 이름을 수정합니다.

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

`GenericController` 클래스:

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

일치하는 경로를 요청하는 경우 결과:

![샘플 앱의 예제 출력은 '제네릭 Sproket 컨트롤러에서 인사드립니다.'로 읽습니다.](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a>예제: 사용 가능 기능 표시

[종속성 주입](../../fundamentals/dependency-injection.md)을 통해 `ApplicationPartManager`을 요청하고 적절한 기능의 인스턴스를 채우는 데 사용하여 앱에 제공되는 채워진 기능을 반복할 수 있습니다.

[!code-csharp[](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

예제 출력:

![샘플 앱의 예제 출력](app-parts/_static/available-features.png)
