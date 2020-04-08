---
title: ASP.NET Core Blazor 템플릿 기반 구성 요소
author: guardrex
description: 템플릿 기반 구성 요소에서 하나 이상의 UI 템플릿을 매개 변수로 허용한 다음, 이 매개 변수를 구성 요소 렌더링 논리의 일부로 사용할 수 있는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b57e3fe186402723607e90b1628062f602c77632
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989496"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a><span data-ttu-id="9fac3-103">ASP.NET Core Blazor 템플릿 기반 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9fac3-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="9fac3-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9fac3-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9fac3-105">템플릿 기반 구성 요소는 하나 이상의 UI 템플릿을 매개 변수로 허용한 다음, 이 매개 변수를 구성 요소 렌더링 논리의 일부로 사용할 수 있는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="9fac3-106">템플릿 기반 구성 요소를 사용하면 일반 구성 요소보다 재사용하기 쉬운 상위 수준 구성 요소를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="9fac3-107">몇 가지 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-107">A couple of examples include:</span></span>

* <span data-ttu-id="9fac3-108">사용자가 테이블의 헤더, 행, 바닥글의 템플릿을 지정할 수 있게 해주는 테이블 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9fac3-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="9fac3-109">사용자가 목록으로 항목을 렌더링하기 위한 템플릿을 지정할 수 있게 해주는 목록 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9fac3-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="9fac3-110">템플릿 매개 변수</span><span class="sxs-lookup"><span data-stu-id="9fac3-110">Template parameters</span></span>

<span data-ttu-id="9fac3-111">템플릿 기반 구성 요소는 `RenderFragment` 또는 `RenderFragment<T>` 형식의 구성 요소 매개 변수를 하나 이상 지정하여 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="9fac3-112">렌더링 조각은 렌더링할 UI 세그먼트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="9fac3-113">`RenderFragment<T>`는 렌더링 조각을 호출할 때 지정할 수 있는 형식 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="9fac3-114">`TableTemplate` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="9fac3-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="9fac3-115">템플릿 기반 구성 요소를 사용하는 경우, 매개 변수 이름(다음 예제에서 `TableHeader` 및 `RowTemplate`)과 일치하는 자식 요소를 사용하여 템플릿 매개 변수를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

> [!NOTE]
> <span data-ttu-id="9fac3-116">제네릭 형식 제약 조건은 향후 릴리스에서 지원될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="9fac3-117">자세한 내용은 [Allow generic type constraints(dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433)(제네릭 형식 제약 조건 허용)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9fac3-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="9fac3-118">템플릿 컨텍스트 매개 변수</span><span class="sxs-lookup"><span data-stu-id="9fac3-118">Template context parameters</span></span>

<span data-ttu-id="9fac3-119">요소로 전달된 `RenderFragment<T>` 형식의 구성 요소 인수에는 `context`라는 암시적 매개 변수(예: 위의 코드 샘플에서 `@context.PetId`)가 있지만, 자식 요소의 `Context` 특성을 사용하여 매개 변수 이름을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="9fac3-120">다음 예제에서 `RowTemplate` 요소의 `Context` 특성은 `pet` 매개 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="9fac3-121">또는 구성 요소 요소에 `Context` 특성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="9fac3-122">지정한 `Context` 특성은 지정한 모든 템플릿 매개 변수에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="9fac3-123">이 기능은 래핑 자식 요소 없이 암시적 자식 콘텐츠의 콘텐츠 매개 변수 이름을 지정하려는 경우에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="9fac3-124">다음 예제에서 `Context` 특성은 `TableTemplate` 요소에 표시되고 모든 템플릿 매개 변수에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a><span data-ttu-id="9fac3-125">제네릭 형식 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9fac3-125">Generic-typed components</span></span>

<span data-ttu-id="9fac3-126">템플릿 기반 구성 요소는 제네릭 형식인 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-126">Templated components are often generically typed.</span></span> <span data-ttu-id="9fac3-127">예를 들어 제네릭 `ListViewTemplate` 구성 요소를 사용하여 `IEnumerable<T>` 값을 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="9fac3-128">제네릭 구성 요소를 정의하려면 [`@typeparam`](xref:mvc/views/razor#typeparam) 지시문을 사용하여 형식 매개 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="9fac3-129">제네릭 형식 구성 요소를 사용하는 경우, 가능하면 형식 매개 변수가 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="9fac3-130">불가능한 경우 형식 매개 변수 이름과 일치하는 특성을 사용하여 형식 매개 변수를 명시적으로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="9fac3-131">다음 예제에서 `TItem="Pet"`은 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9fac3-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
