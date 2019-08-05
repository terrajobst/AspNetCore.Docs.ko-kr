---
title: ASP.NET Core의 보기 구성 요소
author: rick-anderson
description: ASP.NET Core에서 보기 구성 요소가 사용되는 방법 및 이를 앱에 추가하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2019
uid: mvc/views/view-components
ms.openlocfilehash: e6990368519857a27b291d7d565c09072f23f1b0
ms.sourcegitcommit: 7001657c00358b082734ba4273693b9b3ed35d2a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68670089"
---
# <a name="view-components-in-aspnet-core"></a>ASP.NET Core의 보기 구성 요소

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="view-components"></a>뷰 구성 요소

뷰 구성 요소는 부분 보기와 유사하지만 훨씬 강력합니다. 뷰 구성 요소는 모델 바인딩을 사용하지 않으며 호출할 때 제공되는 데이터에만 의존합니다. 이 아티클은 컨트롤러와 뷰를 사용하여 작성되었지만, 뷰 구성 요소는 Razor Pages로도 작업합니다.

뷰 구성 요소:

* 전체 응답보다는 청크를 렌더링합니다.
* 컨트롤러 및 뷰 간에 확인할 수 있는 동일한 개념 분리 및 테스트 가능성 이점을 포함합니다.
* 매개 변수 및 비즈니스 논리를 포함할 수 있습니다.
* 일반적으로 레이아웃 페이지에서 호출됩니다.

뷰 구성 요소는 다음과 같이 부분 뷰에 대해 너무 복잡한 재사용 가능한 렌더링 논리를 포함하는 모든 곳에서 사용할 수 있습니다.

* 동적 탐색 메뉴
* 태그 클라우드(여기서는 데이터베이스를 쿼리)
* 로그인 패널
* 쇼핑 카트
* 최근에 게시된 문서
* 일반적인 블로그에서 추가 기사 콘텐츠
* 모든 페이지에 렌더링되고 사용자의 로그인 상태에 따라 로그아웃 또는 로그인하는 링크를 표시하는 로그인 패널

뷰 구성 요소는 클래스(일반적으로 [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)에서 파생됨)와 반환되는 결과(일반적으로 뷰)의 두 부분으로 구성됩니다. 컨트롤러와 마찬가지로, 뷰 구성 요소는 POCO일 수 있지만 대부분의 개발자는 `ViewComponent`에서 파생되어 사용 가능한 메서드와 속성을 활용하려고 합니다.

보기 구성 요소가 앱의 사양을 충족하는지를 고려할 때는 Razor 구성 요소를 대신 사용하는 것이 좋습니다. 또한 Razor 구성 요소는 태그와 C# 코드를 결합하여 다시 사용할 수 있는 UI 단위를 생성합니다. Razor 구성 요소는 클라이언트 쪽 UI 논리 및 컴퍼지션을 제공할 때 개발자 생산성을 위해 설계되었습니다. 자세한 내용은 <xref:blazor/components>을 참조하세요.

## <a name="creating-a-view-component"></a>뷰 구성 요소 만들기

이 섹션에서는 뷰 구성 요소를 만들기 위한 전반적인 요구 사항이 포함되어 있습니다. 이 문서의 뒷부분에서는 각 단계를 자세히 검토하고 뷰 구성 요소를 만듭니다.

### <a name="the-view-component-class"></a>뷰 구성 요소 클래스

다음 방법으로 뷰 구성 요소 클래스를 만들 수 있습니다.

* *ViewComponent*에서 파생
* `[ViewComponent]` 특성으로 클래스 데코레이팅 또는 `[ViewComponent]` 특성을 사용하여 클래스에서 파생
* 이름이 *ViewComponent* 접미사로 끝나는 클래스 만들기

컨트롤러와 마찬가지로, 뷰 구성 요소는 공용이고 비중첩 및 비추상 클래스여야 합니다. 뷰 구성 요소 이름은 "ViewComponent" 접미사가 제거된 클래스 이름입니다. 또한 `ViewComponentAttribute.Name` 속성을 사용하여 명시적으로 지정할 수도 있습니다.

뷰 구성 요소 클래스:

* 생성자 [종속성 주입](../../fundamentals/dependency-injection.md)을 완전히 지원합니다.

* 컨트롤러 수명 주기를 따르지 않습니다. 즉, 뷰 구성 요소에 [필터](../controllers/filters.md)를 사용할 수 없습니다.

### <a name="view-component-methods"></a>뷰 구성 요소 메서드

보기 구성 요소는 `Task<IViewComponentResult>`를 반환하는 `InvokeAsync` 메서드 또는 `IViewComponentResult`를 반환하는 동기 `Invoke` 메서드에서 해당 논리를 정의합니다. 매개 변수는 모델 바인딩이 아닌 뷰 구성 요소 호출에서 직접 가져옵니다. 뷰 구성 요소는 요청을 직접 처리하지 않습니다. 일반적으로 뷰 구성 요소는 모델을 초기화하고 `View` 메서드를 호출하여 뷰에 전달합니다. 요약하자면, 뷰 구성 요소 메서드는 다음과 같습니다.

* `Task<IViewComponentResult>`를 반환하는 `InvokeAsync` 메서드 또는 `IViewComponentResult`를 반환하는 동기 `Invoke` 메서드를 정의합니다.
* 일반적으로 모델을 초기화하고 `ViewComponent` `View` 메서드를 호출하여 보기에 전달합니다.
* 매개 변수는 HTTP가 아닌 호출 메서드에서 가져옵니다. 모델 바인딩이 없습니다.
* HTTP 엔드포인트로 직접 연결할 수 없습니다. (일반적으로 보기의) 코드에서 호출됩니다. 보기 구성 요소는 요청을 처리하지 않습니다.
* 현재 HTTP 요청의 세부 정보가 아닌 서명에 오버로드됩니다.

### <a name="view-search-path"></a>뷰 검색 경로

런타임은 다음 경로에서 뷰를 검색합니다.

* /Views/{Controller Name}/Components/{View Component Name}/{View Name}
* /Views/Shared/Components/{View Component Name}/{View Name}
* /Pages/Shared/Components/{View Component Name}/{View Name}

검색 경로는 컨트롤러 + 뷰 및 Razor 페이지를 사용하는 프로젝트에 적용됩니다.

뷰 구성 요소에 대한 기본 뷰 이름은 *Default*이며 이것은 일반적으로 뷰 파일의 이름이 *Default.cshtml*로 지정됨을 의미합니다. 뷰 구성 요소 결과를 만들거나 `View` 메서드를 호출할 때 다른 뷰 이름을 지정할 수 있습니다.

뷰 파일 이름을 *Default.cshtml*로 지정하고 *Views/Shared/Components/{View Component Name}/{View Name}* 경로를 사용하는 것이 좋습니다. 이 샘플에 사용된 `PriorityList` 뷰 구성 요소는 뷰 구성 요소 보기에 *Views/Shared/Components/PriorityList/Default.cshtml*을 사용합니다.

## <a name="invoking-a-view-component"></a>뷰 구성 요소 호출

뷰 구성 요소를 사용하려면 뷰 내에서 다음을 호출합니다.

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

매개 변수가 `InvokeAsync` 메서드에 전달됩니다. 문서에서 개발된 `PriorityList` 뷰 구성 요소가 *Views/ToDo/Index.cshtml* 뷰 파일에서 호출됩니다. 다음에서 `InvokeAsync` 메서드는 두 매개 변수를 사용하여 호출됩니다.

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a>태그 도우미로 뷰 구성 요소 호출

ASP.NET Core 1.1 이상에서는 뷰 구성 요소를 [태그 도우미](xref:mvc/views/tag-helpers/intro)로 호출할 수 있습니다.

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

태그 도우미에 대한 파스칼식 클래스 및 메서드 매개 변수가 [kebab 소문자](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)로 번역됩니다. 뷰 구성 요소를 호출하는 태그 도우미는 `<vc></vc>` 요소를 사용합니다. 뷰 구성 요소는 다음과 같이 지정됩니다.

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

뷰 구성 요소를 태그 도우미로 사용하려면 `@addTagHelper` 지시문을 사용하여 뷰 구성 요소가 포함된 어셈블리를 등록합니다. 뷰 구성 요소가 `MyWebApp`이라는 어셈블리에 있는 경우 다음 지시문을 *_ViewImports.cshtml* 파일에 추가합니다.

```cshtml
@addTagHelper *, MyWebApp
```

뷰 구성 요소를 참조하는 모든 파일에 뷰 구성 요소를 태그 도우미로 등록할 수 있습니다. 태그 도우미를 등록하는 방법에 대한 자세한 내용은 [태그 도우미 범위 관리](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope)를 참조하세요.

이 자습서에 사용된 `InvokeAsync` 메서드:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

태그 도우미 태그에서:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

위의 샘플에서 `PriorityList` 뷰 구성 요소는 `priority-list`가 됩니다. 뷰 구성 요소에 대한 매개 변수는 kebab 소문자의 특성으로 전달됩니다.

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a>컨트롤러에서 뷰 구성 요소 직접 호출

일반적으로 뷰 구성 요소는 뷰에서 호출되지만 컨트롤러 메서드에서 직접 호출할 수 있습니다. 뷰 구성 요소는 컨트롤러와 같은 엔드포인트를 정의하지 않지만 `ViewComponentResult`의 내용을 반환하는 컨트롤러 동작을 쉽게 구현할 수 있습니다.

이 예제에서는 뷰 구성 요소가 컨트롤러에서 직접 호출됩니다.

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a>연습: 간단한 뷰 구성 요소 만들기

시작 코드를 [다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), 빌드 및 테스트합니다. *ToDo* 항목의 목록을 표시하는 `ToDo` 컨트롤러가 포함된 간단한 프로젝트입니다.

![ToDo 목록](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a>ViewComponent 클래스 추가

*ViewComponents* 폴더를 만들고 다음 `PriorityListViewComponent` 클래스를 추가합니다.

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

코드에 대한 참고 사항:

* 뷰 구성 요소 클래스는 프로젝트의 **모든** 폴더에 포함될 수 있습니다.
* 클래스 이름 PriorityList**ViewComponent**는 **ViewComponent** 접미사로 끝나기 때문에 런타임은 뷰에서 클래스 구성 요소를 참조할 때 "PriorityList" 문자열을 사용합니다. 나중에 보다 자세히 설명하겠습니다.
* `[ViewComponent]` 특성은 뷰 구성 요소를 참조하는 데 사용된 이름을 변경할 수 있습니다. 예를 들어 클래스 `XYZ`의 이름을 지정하고 `ViewComponent` 특성을 적용할 수 있습니다.

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* 위의 `[ViewComponent]` 특성은 구성 요소와 연관된 뷰를 찾을 때 `PriorityList` 이름을 사용하고, 뷰에서 클래스 구성 요소를 참조할 때 "PriorityList" 문자열을 사용하도록 뷰 구성 요소 선택기에 지시합니다. 나중에 보다 자세히 설명하겠습니다.
* 이 구성 요소에서는 [종속성 주입](../../fundamentals/dependency-injection.md)을 사용하여 데이터 컨텍스트를 사용할 수 있도록 합니다.
* `InvokeAsync`는 뷰에서 호출할 수 있는 메서드를 노출하며 임의 수의 인수를 사용할 수 있습니다.
* `InvokeAsync` 메서드는 `isDone` 및 `maxPriority` 매개변수를 충족하는 `ToDo` 항목 집합을 반환합니다.

### <a name="create-the-view-component-razor-view"></a>뷰 구성 요소 Razor 뷰 만들기

* *Views/Shared/Components* 폴더를 만듭니다. 이 폴더의 이름은 *Components*로 **지정되어야** 합니다.

* *Views/Shared/Components/PriorityList* 폴더를 만듭니다. 이 폴더 이름은 뷰 구성 요소 클래스의 이름 또는 클래스 이름에서 접미사를 뺀 이름과 일치해야 합니다(규칙을 준수하고 클래스 이름에 *ViewComponent* 접미사를 사용한 경우). `ViewComponent` 특성을 사용한 경우 클래스 이름은 특성 지정과 일치해야 합니다.

* *Views/Shared/Components/PriorityList/Default.cshtml* Razor 뷰를 만듭니다.


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   Razor 뷰는 `TodoItem` 목록을 가져와 표시합니다. 뷰 구성 요소 `InvokeAsync` 메서드가 뷰의 이름을 전달하지 않은 경우(샘플에서처럼) 규칙에 따라 뷰 이름으로 *Default*가 사용됩니다. 자습서의 뒷부분에서 뷰 이름을 전달하는 방법을 보여 줍니다. 특정 컨트롤러에 대한 기본 스타일 지정을 재정의하려면 컨트롤러 관련 뷰 폴더에 뷰를 추가합니다(예: *Views/ToDo/Components/PriorityList/Default.cshtml)* .

    뷰 구성 요소가 컨트롤러에 관한 것이면 컨트롤러 관련 폴더에 추가할 수 있습니다(*Views/ToDo/Components/PriorityList/Default.cshtml*).

* 우선 순위 목록 구성 요소에 대한 호출을 포함하는 `div`를 *Views/ToDo/index.cshtml* 파일 맨 아래에 추가합니다.

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

`@await Component.InvokeAsync` 태그는 호출하는 뷰 구성 요소에 대한 구문을 보여 줍니다. 첫 번째 인수는 호출하려는 구성 요소의 이름입니다. 후속 매개 변수는 구성 요소에 전달됩니다. `InvokeAsync`는 임의 개수의 인수를 사용할 수 있습니다.

앱을 테스트합니다. 다음 이미지는 ToDo 목록 및 우선 순위 항목을 보여 줍니다.

![todo 목록 및 우선 순위 항목](view-components/_static/pi.png)

또한 컨트롤러에서 직접 뷰 구성 요소를 호출할 수도 있습니다.

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![IndexVC 작업에서 우선 순위 항목](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a>뷰 이름 지정

복잡한 뷰 구성 요소에는 조건에 따라 기본값이 아닌 뷰를 지정해야 할 수 있습니다. 다음 코드에서 `InvokeAsync` 메서드에서 "PVC" 뷰를 지정하는 방법을 보여 줍니다. `PriorityListViewComponent` 클래스에서 `InvokeAsync` 메서드를 업데이트합니다.

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

*Views/Shared/Components/PriorityList/Default.cshtml* 파일을 *Views/Shared/Components/PriorityList/PVC.cshtml*이라는 뷰에 복사합니다. PVC 뷰가 사용되었다는 것을 나타내는 제목을 추가합니다.

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

*Views/ToDo/Index.cshtml*을 업데이트합니다.

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

앱을 실행하고 PVC 뷰를 확인합니다.

![우선 순위 뷰 구성 요소](view-components/_static/pvc.png)

PVC 뷰가 렌더링되지 않는 경우 우선 순위가 4 이상인 뷰 구성 요소를 호출하고 있는지 확인합니다.

### <a name="examine-the-view-path"></a>뷰 경로 검사

* 우선 순위 뷰가 반환되지 않도록 우선 순위 매개 변수를 3 이하로 변경합니다.
* *Views/ToDo/Components/PriorityList/Default.cshtml*의 이름을 *1Default.cshtml*로 임시로 변경합니다.
* 앱을 테스트하면 다음과 같은 오류가 발생합니다.

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* *Views/ToDo/Components/PriorityList/1Default.cshtml*을 *Views/Shared/Components/PriorityList/Default.cshtml*에 복사합니다.
* *Shared* ToDo 뷰 구성 요소 보기에 일부 태그를 추가하여 뷰가 *Shared* 폴더에 있음을 나타냅니다.
* **Shared** 구성 요소 뷰를 테스트합니다.

![공유 구성 요소 뷰가 있는 ToDo 출력](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a>하드 코드된 문자열 방지

컴파일 시간 안전성을 원하는 경우 하드 코드된 뷰 구성 요소 이름을 클래스 이름으로 바꿀 수 있습니다. "ViewComponent" 접미사 없이 뷰 구성 요소를 만듭니다.

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

`using` 문을 Razor 뷰 파일에 추가하고 `nameof` 연산자를 사용합니다.

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a>동기 작업 수행

비동기 작업을 수행할 필요가 없는 경우 프레임워크에서 동기 `Invoke` 메서드 호출을 처리합니다. 다음 메서드는 동기 `Invoke` 뷰 구성 요소를 만듭니다.

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

뷰 구성 요소의 Razor 파일 목록은 `Invoke` 메서드(*Views/Home/Components/PriorityList/Default.cshtml*)에 전달된 문자열을 나열합니다.

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

뷰 구성 요소는 다음 방법 중 하나를 사용하여 Razor 파일(예: *Views/Home/Index.cshtml*)에서 호출됩니다.

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [태그 도우미](xref:mvc/views/tag-helpers/intro)

<xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> 접근 방법을 사용하려면 `Component.InvokeAsync`를 호출합니다.

::: moniker-end

::: moniker range="< aspnetcore-1.1"

뷰 구성 요소는 <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>를 사용하여 Razor 파일(예: *Views/Home/Index.cshtml*)에서 호출됩니다.

`Component.InvokeAsync`를 호출합니다.

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

태그 도우미를 사용하려면 `@addTagHelper` 지시문을 사용하여 뷰 구성 요소가 포함된 어셈블리를 등록합니다(뷰 구성 요소는 `MyWebApp`이라는 어셈블리에 있음).

```cshtml
@addTagHelper *, MyWebApp
```

Razor 태그 파일의 뷰 구성 요소 태그 도우미를 사용합니다.

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

`PriorityList.Invoke`의 메서드 시그니처는 동기이지만, Razor는 태그 파일에 `Component.InvokeAsync`를 사용하여 메서드를 찾고 호출합니다.

## <a name="all-view-component-parameters-are-required"></a>모든 뷰 구성 요소 매개 변수 필요

뷰 구성 요소의 각 매개 변수는 필수 특성입니다. 이 [GitHub 문제](https://github.com/aspnet/AspNetCore/issues/5011)를 참조하세요. 매개 변수가 하나라도 생략되면

* `InvokeAsync` 메서드 시그니처가 일치하지 않게 되므로 메서드가 실행되지 않습니다.
* ViewComponent가 마크업을 렌더링하지 않습니다.
* 오류가 throw되지 않습니다.

## <a name="additional-resources"></a>추가 자료

* [뷰에 종속성 주입](xref:mvc/views/dependency-injection)
