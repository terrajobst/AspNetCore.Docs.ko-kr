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
# <a name="component-tag-helper-in-aspnet-core"></a>ASP.NET Core의 구성 요소 태그 도우미

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

페이지 또는 뷰에서 구성 요소를 렌더링 하려면 [구성 요소 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)를 사용 합니다.

다음 구성 요소 태그 도우미는 페이지 또는 뷰에서 `Counter` 구성 요소를 렌더링 합니다.

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

구성 요소 태그 도우미는 구성 요소에 매개 변수를 전달할 수도 있습니다. 확인란 레이블의 색 및 크기를 설정 하는 다음과 같은 `ColorfulCheckbox` 구성 요소를 고려 합니다.

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

구성 요소 태그 도우미는 `Size` (`int`) 및 `Color` (`string`) [구성 요소 매개 변수](xref:blazor/components#component-parameters) 를 설정할 수 있습니다.

```cshtml
<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

페이지 또는 뷰에서 렌더링 되는 HTML은 다음과 같습니다.

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

앞의 예제에서 `param-Color` 같이 따옴표 붙은 문자열을 전달 하려면 [명시적 Razor 식이](xref:mvc/views/razor#explicit-razor-expressions)필요 합니다. `string` 형식 값에 대 한 Razor 구문 분석 동작은 특성이 `object` 형식 이므로 `param-*` 특성에 적용 되지 않습니다.

매개 변수 형식은 JSON serializable이어야 하며, 일반적으로 형식에 기본 생성자와 설정 가능한 속성이 있어야 함을 의미합니다. 예를 들어 `Size` 및 `Color` 형식이 JSON serializer에서 지원 되는 기본 형식 (`int` 및 `string`) 이기 때문에 `Size` 값을 지정 하 고 앞의 예제에서 `Color` 수 있습니다.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 구성 요소에 대해 다음을 구성합니다.

* 페이지에 미리 렌더링할지 여부
* 페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부

| 렌더링 모드 | Description |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor 서버 앱의 표식을 렌더링합니다. 구성 요소의 출력은 포함되지 않습니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 구성 요소를 정적 HTML에 렌더링합니다. |

페이지와 뷰는 구성 요소를 사용할 수 있지만 반대의 경우는 지원되지 않습니다. 구성 요소는 부분 보기 및 섹션과 같은 보기 및 페이지 관련 기능을 사용할 수 없습니다. 구성 요소의 부분 뷰에서 논리를 사용 하려면 부분 뷰 논리를 구성 요소로 구분 합니다.

정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
