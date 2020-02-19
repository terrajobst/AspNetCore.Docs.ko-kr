---
title: ASP.NET Core Blazor 템플릿 구성 요소
author: guardrex
description: 템플릿 기반 구성 요소가 하나 이상의 UI 템플릿을 매개 변수로 허용 하는 방법에 대해 알아보고이를 구성 요소의 렌더링 논리의 일부로 사용할 수 있습니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b64d6a731e540b13c50b2c6108f75efd0ac9290c
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453153"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a>ASP.NET Core Blazor 템플릿 구성 요소

By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

템플릿 기반 구성 요소는 하나 이상의 UI 템플릿을 매개 변수로 허용 하는 구성 요소 이며,이 구성 요소는 구성 요소의 렌더링 논리의 일부로 사용할 수 있습니다. 템플릿 기반 구성 요소를 사용 하면 일반 구성 요소 보다 다시 사용할 수 있는 상위 수준 구성 요소를 작성할 수 있습니다. 몇 가지 예는 다음과 같습니다.

* 사용자가 테이블의 머리글, 행 및 바닥글에 대 한 템플릿을 지정 하는 데 사용할 수 있는 테이블 구성 요소입니다.
* 사용자가 목록에서 항목을 렌더링 하기 위한 템플릿을 지정할 수 있도록 하는 목록 구성 요소입니다.

## <a name="template-parameters"></a>템플릿 매개 변수

템플릿 기반 구성 요소는 `RenderFragment` 또는 `RenderFragment<T>`형식의 구성 요소 매개 변수를 하나 이상 지정 하 여 정의 됩니다. 렌더링 조각은 렌더링할 UI 세그먼트를 나타냅니다. `RenderFragment<T>`는 렌더링 조각이 호출 될 때 지정할 수 있는 형식 매개 변수를 사용 합니다.

`TableTemplate` 구성 요소:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

템플릿 기반 구성 요소를 사용 하는 경우 매개 변수의 이름과 일치 하는 자식 요소를 사용 하 여 템플릿 매개 변수를 지정할 수 있습니다 (`TableHeader` 및 다음 예제에서는 `RowTemplate`).

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

## <a name="template-context-parameters"></a>템플릿 컨텍스트 매개 변수

요소로 전달 된 `RenderFragment<T>` 형식의 구성 요소 인수에는 `context` 라는 암시적 매개 변수가 있습니다 (예: 앞의 코드 샘플에서 `@context.PetId`). 그러나 자식 요소의 `Context` 특성을 사용 하 여 매개 변수 이름을 변경할 수 있습니다. 다음 예제에서 `RowTemplate` 요소의 `Context` 특성은 `pet` 매개 변수를 지정 합니다.

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

또는 구성 요소 요소에 `Context` 특성을 지정할 수 있습니다. 지정 된 `Context` 특성은 모든 지정 된 템플릿 매개 변수에 적용 됩니다. 이는 자식 요소를 래핑하는 대신 암시적 자식 콘텐츠에 대 한 콘텐츠 매개 변수 이름을 지정 하려는 경우에 유용할 수 있습니다. 다음 예제에서는 `Context` 특성이 `TableTemplate` 요소에 나타나고 모든 템플릿 매개 변수에 적용 됩니다.

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

## <a name="generic-typed-components"></a>제네릭 형식의 구성 요소

일반적으로 템플릿 구성 요소는 형식화 되어 있습니다. 예를 들어 일반 `ListViewTemplate` 구성 요소를 사용 하 여 `IEnumerable<T>` 값을 렌더링할 수 있습니다. 제네릭 구성 요소를 정의 하려면 [`@typeparam`](xref:mvc/views/razor#typeparam) 지시어를 사용 하 여 형식 매개 변수를 지정 합니다.

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

제네릭 형식의 구성 요소를 사용 하는 경우 가능 하면 형식 매개 변수가 유추 됩니다.

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

그렇지 않은 경우 형식 매개 변수의 이름과 일치 하는 특성을 사용 하 여 형식 매개 변수를 명시적으로 지정 해야 합니다. 다음 예제에서 `TItem="Pet"`는 형식을 지정 합니다.

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
