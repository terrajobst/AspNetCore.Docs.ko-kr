---
title: ASP.NET Core에서 필터링
author: ardalis
description: 필터 작동 방법 및 ASP.NET Core에서 사용하는 방법을 자세히 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 5/08/2019
uid: mvc/controllers/filters
ms.openlocfilehash: cdf121b97396cb23103d49cd141b9ef19b8c0cc6
ms.sourcegitcommit: e1623d8279b27ff83d8ad67a1e7ef439259decdf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2019
ms.locfileid: "66223029"
---
# <a name="filters-in-aspnet-core"></a>ASP.NET Core에서 필터링

작성자: [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), [Steve Smith](https://ardalis.com/)

ASP.NET Core에서 ‘필터’를 사용하면 요청 처리 파이프라인의 특정 단계 전후에 코드를 실행할 수 있습니다.

기본 제공 필터는 다음과 같은 작업을 처리합니다.

* 권한 부여(사용자가 권한이 없는 리소스에 액세스 방지).
* 응답 캐싱(요청 파이프라인을 단락하면 캐시된 응답을 반환합니다).

사용자 지정 필터를 만들어 교차 편집 문제를 처리할 수 있습니다. 교차 편집 문제의 예로는 오류 처리, 캐싱, 구성, 권한 부여 및 로깅 등을 들 수 있습니다.  필터는 코드 중복을 방지합니다. 예를 들어 오류 처리 예외 필터는 오류 처리를 통합할 수 있습니다.

이 문서는 Razor 페이지, API 컨트롤러 및 보기를 포함한 컨트롤러에 적용됩니다.

[예제 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="how-filters-work"></a>필터 작동 방법

필터는 ‘필터 파이프라인’이라고도 하는 ‘ASP.NET Core 동작 호출 파이프라인’내에서 실행됩니다.  필터 파이프라인은 ASP.NET Core가 실행할 작업을 선택한 후에 실행됩니다.

![다른 미들웨어, 라우팅 미들웨어, 작업 선택 영역 및 ASP.NET Core 작업 호출 파이프라인을 통해 요청이 처리됩니다. 응답이 클라이언트에 전송되기 전에 작업 선택 영역, 라우팅 미들웨어 및 기타 다양한 미들웨어를 통해 요청 처리가 계속됩니다.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>필터 형식

각 필터 형식은 필터 파이프라인의 다른 단계에서 실행됩니다.

* [권한 부여 필터](#authorization-filters)는 먼저 실행되고 사용자가 요청에 대한 권한이 있는지 여부를 결정하는 데 사용됩니다. 권한 부여 필터는 요청이 인증되지 않는 경우 파이프라인을 단락(short-circuit) 처리합니다.

* [리소스 필터](#resource-filters):

  * 권한 부여 후 실행합니다.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>는 필터 파이프라인의 나머지 부분 앞에 코드를 실행할 수 있습니다. 예를 들어 `OnResourceExecuting`는 모델 바인딩 전에 코드를 실행할 수 있습니다.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>는 파이프라인의 나머지 부분이 완료된 후 코드를 실행할 수 있습니다.

* [작업 필터](#action-filters)는 개별 작업 메서드가 호출된 전후에 즉시 코드를 실행할 수 있습니다. 작업에 전달된 인수 및 작업에서 반환된 결과를 조작하는 데 사용할 수 있습니다. Razor Pages에서는 작업 필터가 지원되지 **않습니다**.

* [예외 필터](#exception-filters)는 응답 본문에 무언가 쓰여지기 전에 발생한 처리되지 않은 예외에 전역 정책을 적용하는 데 사용됩니다.

* [결과 필터](#result-filters)는 개별 작업이 실행된 전후에 즉시 코드를 실행할 수 있습니다. 작업 메서드가 성공적으로 실행되는 경우에만 실행됩니다. 보기 또는 포맷터 실행을 둘러싸야 하는 논리에 유용합니다.

다음 다이어그램에서는 필터 형식이 필터 파이프라인에서 상호 작용하는 방법을 보여줍니다.

![요청은 권한 부여 필터, 리소스 필터, 모델 바인딩, 작업 필터, 작업 실행 및 작업 결과 변환, 예외 필터, 결과 필터 및 결과 실행을 통해 처리됩니다. 주의할 점은 요청이 응답이 클라이언트에 전송되기 전에 결과 필터 및 리소스 필터에 따라서만 처리된다는 것입니다.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>구현

필터는 다른 인터페이스 정의를 통해 동기 및 비동기 구현을 모두 지원합니다.

동기 필터는 파이프라인 단계 전(`On-Stage-Executing`)과 후(`On-Stage-Executed`)에 코드를 실행할 수 있습니다. 예를 들어 `OnActionExecuting`는 작업 메서드가 호출되기 전에 호출됩니다. `OnActionExecuted`는 작업 메서드가 반환된 후 호출됩니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

비동기 필터는 `On-Stage-ExecutionAsync` 메서드를 정의합니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

위 코드에서 `SampleAsyncActionFilter`에는 작업 메서드를 실행하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>(`next`)가 포함됩니다.  각 `On-Stage-ExecutionAsync` 메서드는 필터의 파이프라인 단계를 실행하는 `FilterType-ExecutionDelegate`를 사용합니다.

### <a name="multiple-filter-stages"></a>여러 필터 단계

단일 클래스에서 여러 필터 단계에 대한 인터페이스를 구현할 수 있습니다. 예를 들어 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 클래스는 `IActionFilter`, `IResultFilter` 및 해당 비동기 값을 구현합니다.

필터 인터페이스의 동기 또는 비동기 버전 중 **하****나**를 구현합니다. 런타임은 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다. 그렇지 않으면 동기 인터페이스의 메서드를 호출합니다. 비동기 및 동기 인터페이스가 모두 하나의 클래스에서 구현되는 경우에는 비동기 메서드만 호출됩니다. <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>와 같은 추상 클래스를 사용하는 경우 각 필터 형식에 대한 동기 메서드 또는 비동기 메서드만 재정의합니다.

### <a name="built-in-filter-attributes"></a>기본 제공 필터 특성

ASP.NET Core에는 하위 클래스를 지정하고 사용자 지정할 수 있는 기본 제공 특성 기반 필터가 포함됩니다. 예를 들어 다음 결과 필터는 응답에 헤더를 추가합니다.

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

특성을 사용하면 앞의 예제와 같이 필터에서 인수를 허용할 수 있습니다. 컨트롤러나 작업 메서드에 `AddHeaderAttribute`를 적용하고 HTTP 헤더의 이름 및 값을 지정합니다.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

여러 필터 인터페이스에는 사용자 지정 구현에 대한 기본 클래스로 사용할 수 있는 해당 특성이 있습니다.

필터 특성:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>필터 범위 및 실행 순서

세 개의 *범위* 중 하나에서 파이프라인에 필터를 추가할 수 있습니다.

* 작업에서 특성 사용.
* 컨트롤러에서 특성 사용.
* 다음 코드와 같이 모든 컨트롤러와 작업에 전역으로 사용:

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

앞의 코드는 [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) 컬렉션을 사용하여 3개의 필터를 전역적으로 추가합니다.

### <a name="default-order-of-execution"></a>기본 실행 순서

파이프라인의 특정 단계에 여러 개의 필터가 있는 경우 범위는 기본 필터 실행 순서를 결정합니다.  전역 필터는 메서드 필터를 둘러싼 클래스 필터를 둘러쌉니다.

필터 중첩의 결과로 필터의 *after* 코드는 *before* 코드의 역순으로 실행됩니다. 필터 시퀀스:

* 전역 필터의 *before* 코드.
  * 컨트롤러 필터의 *before* 코드.
    * 작업 메서드 필터의 *before* 코드.
    * 작업 메서드 필터의 *after* 코드.
  * 컨트롤러 필터의 *after* 코드.
* 전역 필터의 *after* 코드.
  
다음 예제는 필터 메서드가 동기 작업 필터에 대해 호출되는 순서를 보여줍니다.

| Sequence | 필터 범위 | 필터 메서드 |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | 컨트롤러 | `OnActionExecuting` |
| 3 | 메서드 | `OnActionExecuting` |
| 4 | 메서드 | `OnActionExecuted` |
| 5 | 컨트롤러 | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

이 시퀀스는 다음을 보여 줍니다.

* 메서드 필터는 컨트롤러 필터 내에서 중첩됩니다.
* 컨트롤러 필터는 전역 필터 내에서 중첩됩니다.

### <a name="controller-and-razor-page-level-filters"></a>컨트롤러 및 Razor 페이지 수준 필터

<xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스에서 상속되는 모든 컨트롤러에는 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 및 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 메서드가 포함됩니다. 다음 메서드는

* 지정된 작업을 위해 실행되는 필터를 래핑합니다.
* `OnActionExecuting`는 작업 필터 이전에 호출됩니다.
* `OnActionExecuted`는 모든 작업 필터 이후에 호출됩니다.
* `OnActionExecutionAsync`는 작업 필터 이전에 호출됩니다. 작업 메서드 이후 `next`가 실행된 후의 필터 코드.

예를 들어 다운로드 예제에서 `MySampleActionFilter`은 시작할 때 전역적으로 적용됩니다.

`TestController`:

* `SampleActionFilterAttribute`(`[SampleActionFilter]`)(을)를 `FilterTest2` 작업에 적용합니다.
* `OnActionExecuting` 및 `OnActionExecuted`를 재정의합니다.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

`https://localhost:5001/Test/FilterTest2`로 이동하면 다음 코드가 실행됩니다.

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Razor Pages에 대해서는 [필터 메서드를 재정의하여 Razor 페이지 필터 구현](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)을 참조하세요.

### <a name="overriding-the-default-order"></a>기본 순서 재정의

<xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>를 구현하여 실행의 기본 시퀀스를 재정의할 수 있습니다. `IOrderedFilter`은 실행 순서를 결정하는 범위에 대해 우선 순위를 갖는 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 속성을 노출합니다. 낮은 `Order` 값을 가진 필터:

* 더 높은 `Order` 값을 가진 필터 이전에 *before* 코드를 실행합니다.
* 더 높은 `Order` 값을 가진 필터 이후에 *after* 코드를 실행합니다.

생성자 매개 변수로 `Order` 속성을 설정할 수 있습니다.

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

앞의 예와 동일한 3개의 작업 필터를 고려합니다. 컨트롤러와 전역 필터의 `Order` 속성이 각각 1과 2로 설정된 경우에는 실행 순서가 반대가 됩니다.

| Sequence | 필터 범위 | `Order` 속성 | 필터 메서드 |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | 메서드 | 0 | `OnActionExecuting` |
| 2 | 컨트롤러 | 1  | `OnActionExecuting` |
| 3 | Global | 2  | `OnActionExecuting` |
| 4 | Global | 2  | `OnActionExecuted` |
| 5 | 컨트롤러 | 1  | `OnActionExecuted` |
| 6 | 메서드 | 0  | `OnActionExecuted` |

`Order` 속성은 필터가 실행되는 순서를 결정할 때 범위를 재정의합니다. 필터가 순서에 따라 먼저 정렬된 다음, 범위는 연결을 끊는 데 사용됩니다. 모든 기본 제공 필터는 `IOrderedFilter`을 구현하고 기본 `Order` 값을 0으로 설정합니다. 기본 제공 필터의 경우 `Order`를 0이 아닌 값으로 설정하지 않는 한 범위가 순서를 결정합니다.

## <a name="cancellation-and-short-circuiting"></a>취소 및 단락(short-circuit)

제공된 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 매개 변수의 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 속성을 필터 메서드로 설정하여 언제든지 필터 파이프라인을 단락(short-circuit) 처리할 수 있습니다. 예를 들어 다음과 같은 리소스 필터는 파이프라인의 나머지 부분이 실행되지 않도록 방지합니다.

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

다음 코드에서 `ShortCircuitingResourceFilter` 및 `AddHeader` 필터는 `SomeResource` 작업 메서드를 대상으로 지정합니다. `ShortCircuitingResourceFilter`:

* 리소스 필터이고 `AddHeader`이 작업 필터이기 때문에 먼저 실행합니다.
* 나머지 파이프라인을 단락(Short-circuit) 처리합니다.

따라서 `AddHeader` 필터는 `SomeResource` 작업에 대해 절대 실행되지 않습니다. 이 동작은 작업 메서드 수준에서 두 필터를 적용한 경우에도 동일하며 먼저 실행되는 `ShortCircuitingResourceFilter`를 제공합니다. 해당 필터 형식 또는 `Order` 속성의 명시적 사용으로 인해 `ShortCircuitingResourceFilter`이 실행됩니다.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>종속성 주입

형식 또는 인스턴스별로 필터를 추가할 수 있습니다. 인스턴스가 추가되면 해당 인스턴스가 모든 요청에 사용됩니다. 형식이 추가되면 활성화됩니다. 형식이 활성화된 필터는 다음을 의미합니다.

* 각 요청에 대한 인스턴스가 됩니다.
* 생성자 종속성이 DI([종속성 주입](xref:fundamentals/dependency-injection))로 채워집니다.

특성으로 구현되고 컨트롤러 클래스 또는 작업 메서드에 직접 추가되는 필터에는 DI([종속성 주입](xref:fundamentals/dependency-injection))에서 제공하는 생성자 종속성이 없을 것입니다. DI는 다음과 같은 이유로 생성자 종속성을 제공할 수 없습니다.

* 특성이 적용될 때 해당 생성자 매개 변수가 제공되어야 하기 때문입니다. 
* 특성이 작동하는 방법의 제한 사항입니다.

다음 필터는 DI에서 제공하는 생성자 종속성을 지원 합니다.

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* 특성에서 구현된 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.

앞의 필터는 컨트롤러 또는 작업 메서드에 적용할 수 있습니다.

로거는 DI에서 사용할 수 있습니다. 그러나 로깅 용도로만 필터를 만들거나 사용하지는 마세요. [기본 제공 프레임워크 로깅](xref:fundamentals/logging/index)은 일반적으로 로깅에 필요한 것을 제공합니다. 필터에 추가된 로깅:

* 비즈니스 도메인 문제 또는 필터 고유의 동작에 중점을 두어야 합니다.
* 작업 또는 다른 프레임워크 이벤트를 로깅해서는 **안 됩니다**. 기본 제공 필터는 작업 및 프레임워크 이벤트를 로깅합니다.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

서비스 필터 구현 형식은 `ConfigureServices`에 등록됩니다. <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 DI에서 필터의 인스턴스를 검색합니다.

다음 코드는 `AddHeaderResultServiceFilter`을 보여 줍니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

다음 코드에서 `AddHeaderResultServiceFilter`은 DI 컨테이너에 추가됩니다.

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

다음 코드에서 `ServiceFilter` 특성은 DI에서 `AddHeaderResultServiceFilter` 필터의 인스턴스를 수신합니다.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):

* 필터 인스턴스가 원래 생성된 요청 범위 외부에서 재사용될 가능성이 *있음*을 암시하는 것입니다. ASP.NET Core 런타임은 다음을 보장하지 않습니다.

  * 필터의 단일 인스턴스 생성.
  * 필터는 나중에 DI 컨테이너에서 다시 요청되지 않습니다.

* 싱글톤이 아닌 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다. `IFilterFactory`은 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다. `CreateInstance`는 DI에서 지정된 형식을 로드합니다.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>와 비슷하지만 해당 형식은 DI 컨테이너에서 직접 확인되지 않습니다. <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>를 사용하여 형식을 인스턴스화합니다.

`TypeFilterAttribute` 형식이 DI 컨테이너에서 직접 확인되지 않기 때문입니다.

* `TypeFilterAttribute`을 사용하여 참조되는 형식은 DI 컨테이너를 사용하여 등록할 필요가 없습니다.  DI 컨테이너에서 충족하는 종속성을 갖고 있습니다.
* `TypeFilterAttribute`는 형식에 대한 생성자 인수를 필요에 따라 허용할 수 있습니다.

`TypeFilterAttribute`를 사용 중인 경우 `IsReusable`을 설정하면 필터 인스턴스가 원래 생성된 요청 범위 외부에서 재사용될 가능성이 *있음*을 암시하는 것입니다. ASP.NET Core 런타임은 단일 필터 인스턴스가 생성되도록 보장하지 않습니다. 싱글톤이 아닌 수명이 지정된 서비스에 의존하는 필터를 사용할 때는 `IsReusable`을(를) 사용하지 마세요.

다음 예제에서는 `TypeFilterAttribute`를 사용하여 인수를 형식에 전달하는 방법을 보여줍니다.

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>권한 부여 필터

권한 부여 필터:

* 필터 파이프라인에서 실행되는 첫 번째 필터입니다.
* 동작 메서드에 대한 액세스를 제어합니다.
* before 메서드는 있지만 after 메서드는 없습니다.

사용자 지정 권한 부여 필터는 사용자 지정 권한 부여 프레임워크를 필요로 합니다. 사용자 지정 필터를 작성하는 대신 권한 부여 정책을 구성하거나 사용자 지정 권한 부여 정책을 작성하는 것이 좋습니다. 기본 제공 권한 부여 필터:

* 권한 부여 시스템을 호출합니다.
* 요청 권한을 부여하지 않습니다.

권한 부여 필터 내에서 예외를 throw하지 **마세요**.

* 예외는 처리되지 않습니다.
* 예외 필터는 예외를 처리하지 않습니다.

권한 부여 필터에서 예외가 발생할 경우 과제 실행을 고려합니다.

[권한 부여](xref:security/authorization/introduction)에 대해 자세히 알아봅니다.

## <a name="resource-filters"></a>리소스 필터

리소스 필터:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 인터페이스 중 하나를 구현합니다.
* 실행은 필터 파이프라인 대부분을 래핑합니다.
* [권한 부여 필터](#authorization-filters)만 리소스 필터 전에 실행됩니다.

리소스 필터는 파이프라인의 대부분을 단락(short-circuit) 처리해야 하는 경우에 유용합니다. 예를 들어 캐싱 필터는 캐시 적중에서 파이프라인의 나머지 부분을 방지할 수 있습니다.

리소스 필터 예제:

* 이전에 표시된 [단락(short-circuiting) 리소스 필터](#short-circuiting-resource-filter).
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * 모델 바인딩이 양식 데이터에 액세스하는 것을 방지합니다.
  * 형식 데이터가 메모리로 읽히는 것을 방지하기 위해 대형 파일 업로드에 사용됩니다.

## <a name="action-filters"></a>작업 필터

> [!IMPORTANT]
> 작업 필터는 Razor Pages에 **적용되지 않습니다**. Razor Pages는 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>를 지원합니다. 자세한 내용은 [Razor 페이지에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.

작업 필터:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 인터페이스 중 하나를 구현합니다.
* 해당 실행은 작업 메서드의 실행을 둘러쌉니다.

다음 코드는 샘플 작업 필터를 보여 줍니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>는 다음과 같은 속성을 제공합니다.

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 작업 메서드에 대한 입력을 읽을 수 있도록 합니다.
* <xref:Microsoft.AspNetCore.Mvc.Controller> - 컨트롤러 인스턴스를 조작할 수 있도록 합니다.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` 설정은 작업 메서드 및 후속 작업 필터의 실행을 단락(short-circuit) 설정합니다.

작업 메서드에서 예외 throw.

* 후속 필터의 실행을 방지합니다.
* `Result` 설정과 달리 성공적인 결과 대신 실패로 처리됩니다.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>는 다음과 같은 속성과 함께 `Controller` 및 `Result`를 제공합니다.

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 작업 실행이 다른 필터에 의해 단락(short-circuit) 처리된 경우 true입니다.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> - 작업 또는 이전에 실행된 작업 필터에서 예외가 throw된 경우 null이 아닙니다. 이 속성을 null로 설정:

  * 예외를 효과적으로 처리합니다.
  * `Result`은 작업 메서드에서 반환되는 것처럼 실행됩니다.

`IAsyncActionFilter`의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>에 대한 호출:

* 후속 작업 필터 및 작업 메서드를 실행합니다.
* `ActionExecutedContext`을 반환합니다.

단락(short-circuit) 처리하려면 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName>을 결과 인스턴스에 할당하고 `next`(`ActionExecutionDelegate`)를 호출하지 않아야 합니다.

프레임워크는 하위 클래스를 지정할 수 있는 추상 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 제공합니다.

`OnActionExecuting` 작업 필터는 다음에 사용할 수 있습니다.

* 모델 상태의 유효성을 검사합니다.
* 상태가 유효하지 않은 경우 오류를 반환합니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

`OnActionExecuted` 메서드는 작업 메서드 이후에 실행됩니다.

* 그리고 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 속성을 통해 작업 결과를 확인하고 조작할 수 있습니다.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>는 작업 실행이 다른 필터에 의해 단락(short-circuit) 처리된 경우 true로 설정됩니다.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>은 작업 또는 후속 작업 필터에서 예외가 throw된 경우 null이 아닌 값으로 설정됩니다. `Exception`을 Null로 설정:

  * 예외를 효과적으로 처리합니다.
  * `ActionExecutedContext.Result`은 작업 메서드에서 정상적으로 반환되는 것처럼 실행됩니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>예외 필터

예외 필터:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>를 구현합니다. 
* 일반적인 오류 처리 정책을 구현하는 데 사용할 수 있습니다.

다음 샘플 예외 필터는 사용자 지정 오류 보기를 사용하여 앱을 개발 중인 경우에 발생하는 예외에 대한 세부 정보를 표시합니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=16-19)]

예외 필터:

* before 및 after 이벤트는 없습니다.
* <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>를 구현합니다.
* Razor Page 또는 컨트롤러 생성, [모델 바인딩](xref:mvc/models/model-binding), 작업 필터 또는 작업 메서드에서 발생하는 처리되지 않은 예외를 처리합니다.
* 리소스 필터, 결과 필터 또는 MVC 결과 실행에서 발생하는 예외를 catch하지 **않습니다**.

예외를 처리하려면 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 속성을 `true`로 설정하거나 응답을 작성합니다. 그러면 예외가 전파되지 않습니다. 예외 필터는 예외를 “성공”으로 변환할 수 없습니다. 작업 필터에서만 가능합니다.

예외 필터:

* 작업 내에서 발생하는 트래핑 예외에 좋습니다.
* 오류 처리 미들웨어만큼 유연하지 않습니다.

예외 처리의 경우 미들웨어를 선호합니다. 어떤 작업 메서드가 호출되는지에 따라 오류 처리 방식이 ‘다른’ 경우에만 예외 필터를 사용합니다. 예를 들어 앱에는 API 엔드포인트 및 보기/HTML 모두에 대한 작업 메서드가 있을 수 있습니다. API 엔드포인트는 JSON으로 오류 정보를 반환할 수 있습니다. 반면 보기 기반 작업은 HTML로 오류 페이지를 반환할 수 있습니다.

## <a name="result-filters"></a>결과 필터

결과 필터:

* 인터페이스 구현:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* 해당 실행은 작업 결과의 실행을 둘러쌉니다.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter 및 IAsyncResultFilter

다음 코드는 HTTP 헤더를 추가하는 결과 필터를 보여 줍니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

실행되는 결과의 종류는 작업에 따라 다릅니다. 보기를 반환하는 작업에는 실행되는 <xref:Microsoft.AspNetCore.Mvc.ViewResult>의 일부인 모든 Razor 프로세스가 포함됩니다. API 메서드는 실행 결과의 일부로 일부 serialization을 수행할 수 있습니다. [작업 결과](xref:mvc/controllers/actions)에 대한 자세한 정보

결과 필터는 작업 또는 작업 필터가 작업 결과 생성하는 성공적인 결과에 대해서만 실행됩니다. 예외 필터가 예외를 처리하는 경우 결과 필터는 실행되지 않습니다.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 메서드는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName>를 `true`로 설정하여 작업 결과 및 후속 결과 필터를 단락(short-circuit) 처리할 수 있습니다. 일반적으로 빈 응답을 생성하지 않도록 단락(short-circuit) 처리하는 경우 응답 개체에 작성합니다. `IResultFilter.OnResultExecuting`에서 예외 throw:

* 작업 결과 및 후속 필터의 실행을 방지합니다.
* 성공적인 결과 대신 실패로 처리됩니다.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 메서드가 실행되면:

* 응답이 클라이언트로 전송되었을 가능성이 있으며 변경할 수 없습니다.
* 예외가 throw된 경우 응답 본문이 전송되지 않습니다.

<!-- Review preceding "If an exception was thrown: Original 
When the OnResultExecuted method runs, the response has likely been sent to the client and cannot be changed further (unless an exception was thrown).

SHould that be , 
If an exception was thrown **IN THE RESULT FILTER**, the response body is not sent.

 -->

`ResultExecutedContext.Canceled`는 작업 결과 실행이 다른 필터에 의해 단락(short-circuit) 처리된 경우 `true`로 설정됩니다.

`ResultExecutedContext.Exception`은 작업 결과 또는 후속 결과 필터에서 예외가 throw된 경우 null이 아닌 값으로 설정됩니다. `Exception`을 효과적으로 null로 설정하면 예외를 처리하고 예외가 파이프라인의 뒷부분에 나오는 ASP.NET Core에 의해 다시 throw되지 않습니다. 결과 필터에서 예외를 처리하는 경우 데이터를 응답에 쓸 신뢰할 수 있는 방법이 없습니다. 작업 결과가 예외를 throw할 때 헤더가 클라이언트에 플러시된 경우 오류 코드를 전송하는 신뢰할 수 있는 메커니즘이 없습니다.

<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate>에서 `await next`에 대한 호출은 후속 결과 필터 및 작업 결과를 실행합니다. 단락(short-circuit) 처리하려면 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel)을 `true`로 설정하고 `ResultExecutionDelegate`를 호출하지 마세요.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

프레임워크는 하위 클래스를 지정할 수 있는 추상 `ResultFilterAttribute`를 제공합니다. 이전에 표시된 [AddHeaderAttribute](#add-header-attribute) 클래스는 결과 필터 특성의 예제입니다.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter 및 IAsyncAlwaysRunResultFilter

<xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 인터페이스는 모든 작업 결과에 대해 실행되는 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 구현을 선언합니다. 필터는 다음 경우가 아닌 한 모든 작업 결과에 적용됩니다.

* <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAuthorizationFilter>(은)는 응답을 적용하고 단락(short-circuit) 처리합니다.
* 예외 필터는 작업 결과를 생성하여 예외를 처리합니다.

`IExceptionFilter` 및 `IAuthorizationFilter` 이외의 필터는 `IAlwaysRunResultFilter` 및 `IAsyncAlwaysRunResultFilter`을 단락(short-circuit) 처리하지 않습니다.

예를 들어 다음 필터는 항상 작업 결과(콘텐츠 협상이 실패할 경우 *422 Unprocessable Entity* 상태 코드가 포함된 <xref:Microsoft.AspNetCore.Mvc.ObjectResult>)를 실행 및 설정합니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>를 구현합니다. 따라서 `IFilterFactory` 인스턴스를 필터 파이프라인에서 `IFilterMetadata` 인스턴스로 사용할 수 있습니다. 런타임이 필터를 호출하려고 준비하는 경우 `IFilterFactory`으로 캐스팅을 시도합니다. 해당 캐스팅에 성공하면 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 호출하여 호출되는 `IFilterMetadata` 인스턴스를 만듭니다. 앱이 시작될 때 정확한 필터 파이프라인을 명시적으로 설정할 필요가 없으므로 유연한 디자인을 제공합니다.

필터를 만드는 다른 방법으로 사용자 지정 특성 구현을 사용하여 `IFilterFactory`를 구현할 수 있습니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

[다운로드 샘플](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)을 실행하여 앞의 코드를 테스트할 수 있습니다.

* F12 개발자 도구를 호출합니다.
* `https://localhost:5001/Sample/HeaderWithFactory`로 이동합니다.

F12 개발자 도구는 샘플 코드에 의해 추가된 다음 응답 헤더를 표시합니다.

* **작성자:** `Joe Smith`
* **globaladdheader:** `Result filter added to MvcOptions.Filters`
* **internal:** `My header`

앞의 코드는 **internal:** `My header` 응답 헤더를 만듭니다.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>특성에서 구현된 IFilterFactory

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

`IFilterFactory`를 구현하는 필터는 다음과 같은 필터에 유용합니다.

* 매개 변수 전달을 필요로 하지 않는 필터.
* DI에 의해 채워져야 할 생성자 종속성이 있는 필터.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다. `IFilterFactory`은 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다. `CreateInstance`는 서비스 컨테이너(DI)에서 지정된 형식을 로드합니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

다음 코드는 `[SampleActionFilter]`을 적용하는 세 가지 방법을 보여 줍니다.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

앞의 코드에서 `[SampleActionFilter]`로 메서드를 장식하는 것은 `SampleActionFilter`을 적용하는 데 선호되는 방법입니다.

## <a name="using-middleware-in-the-filter-pipeline"></a>필터 파이프라인에서 미들웨어 사용

리소스 필터는 파이프라인의 뒷부분에 제공되는 모든 실행을 둘러싼다는 점에서 [미들웨어](xref:fundamentals/middleware/index)처럼 작동합니다. 하지만 필터는 ASP.NET Core 런타임의 일부라는 점에서 미들웨어와 다릅니다. 즉, ASP.NET Core 컨텍스트 및 구문에 액세스할 수 있습니다.

미들웨어를 필터로 사용하려면 필터 파이프라인에 삽입할 미들웨어를 지정하는 `Configure` 메서드를 포함한 형식을 만듭니다. 요청에서 현재 문화권을 설정하는 지역화 미들웨어를 사용하는 예제는 다음과 같습니다.

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

<xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>를 사용하여 미들웨어 실행:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

미들웨어 필터는 모델 바인딩 이전 및 나머지 파이프라인 이후에 리소스 필터와 동일한 필터 파이프라인 단계에서 실행됩니다.

## <a name="next-actions"></a>다음 작업

* [Razor Pages에 대한 필터 메서드](xref:razor-pages/filter) 참조
* 필터를 실험하려면 [GitHub 샘플을 다운로드하고, 테스트하고, 수정](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)합니다.
