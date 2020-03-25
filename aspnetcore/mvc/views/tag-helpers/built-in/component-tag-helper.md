---
title: ASP.NET Core의 구성 요소 태그 도우미
author: guardrex
ms.author: riande
description: ASP.NET Core 구성 요소 태그 도우미를 사용 하 여 페이지 및 뷰에서 Razor 구성 요소를 렌더링 하는 방법을 알아봅니다.
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 801ceb73de5bb4ef7500624e1fbddbf96d1ab89c
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80226383"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="4bb5d-103">ASP.NET Core의 구성 요소 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="4bb5d-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="4bb5d-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4bb5d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4bb5d-105">페이지 또는 뷰에서 구성 요소를 렌더링 하려면 [구성 요소 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

<span data-ttu-id="4bb5d-106">다음 구성 요소 태그 도우미는 페이지 또는 뷰에서 `Counter` 구성 요소를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-106">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="4bb5d-107">구성 요소 태그 도우미는 구성 요소에 매개 변수를 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-107">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="4bb5d-108">확인란 레이블의 색 및 크기를 설정 하는 다음과 같은 `ColorfulCheckbox` 구성 요소를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-108">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="4bb5d-109">구성 요소 태그 도우미는 `Size` (`int`) 및 `Color` (`string`) [구성 요소 매개 변수](xref:blazor/components#component-parameters) 를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-109">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="4bb5d-110">페이지 또는 뷰에서 렌더링 되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-110">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="4bb5d-111">앞의 예제에서 `param-Color` 같이 따옴표 붙은 문자열을 전달 하려면 [명시적 Razor 식이](xref:mvc/views/razor#explicit-razor-expressions)필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-111">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="4bb5d-112">`string` 형식 값에 대 한 Razor 구문 분석 동작은 특성이 `object` 형식 이므로 `param-*` 특성에 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-112">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="4bb5d-113">매개 변수 형식은 JSON serializable이어야 하며, 일반적으로 형식에 기본 생성자와 설정 가능한 속성이 있어야 함을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-113">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="4bb5d-114">예를 들어 `Size` 및 `Color` 형식이 JSON serializer에서 지원 되는 기본 형식 (`int` 및 `string`) 이기 때문에 `Size` 값을 지정 하 고 앞의 예제에서 `Color` 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-114">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="4bb5d-115"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-115"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="4bb5d-116">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="4bb5d-116">Is prerendered into the page.</span></span>
* <span data-ttu-id="4bb5d-117">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="4bb5d-117">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="4bb5d-118">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="4bb5d-118">Render Mode</span></span> | <span data-ttu-id="4bb5d-119">Description</span><span class="sxs-lookup"><span data-stu-id="4bb5d-119">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="4bb5d-120">구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-120">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4bb5d-121">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-121">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="4bb5d-122">Blazor 서버 앱의 표식을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-122">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4bb5d-123">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-123">Output from the component isn't included.</span></span> <span data-ttu-id="4bb5d-124">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-124">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="4bb5d-125">구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-125">Renders the component into static HTML.</span></span> |

<span data-ttu-id="4bb5d-126">페이지와 뷰는 구성 요소를 사용할 수 있지만 반대의 경우는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-126">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="4bb5d-127">구성 요소는 부분 보기 및 섹션과 같은 보기 및 페이지 관련 기능을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-127">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="4bb5d-128">구성 요소의 부분 뷰에서 논리를 사용 하려면 부분 뷰 논리를 구성 요소로 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-128">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="4bb5d-129">정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4bb5d-129">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4bb5d-130">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="4bb5d-130">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
