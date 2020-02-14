---
title: ASP.NET Core Razor 구성 요소 만들기 및 사용
author: guardrex
description: 데이터에 바인딩하고, 이벤트를 처리 하 고, 구성 요소 수명 주기를 관리 하는 방법을 비롯 하 여 Razor 구성 요소를 만들고 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 0da0d83a4fde7b753a84bf05d3a9284776f2881f
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213352"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>ASP.NET Core Razor 구성 요소 만들기 및 사용

By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

Blazor apps는 *구성 요소*를 사용 하 여 빌드됩니다. 구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI (사용자 인터페이스)의 자체 포함 청크입니다. 구성 요소는 데이터를 삽입 하거나 UI 이벤트에 응답 하는 데 필요한 HTML 태그와 처리 논리를 포함 합니다. 구성 요소는 유연 하 고 간단 합니다. 프로젝트 간에 중첩, 재사용 및 공유 될 수 있습니다.

## <a name="component-classes"></a>구성 요소 클래스

구성 요소는 및 HTML 태그의 C# 조합을 사용 하 여 [razor](xref:mvc/views/razor) 구성 요소 파일 (*razor*)에서 구현 됩니다. Blazor의 구성 요소는 공식적으로 *Razor 구성 요소*라고 합니다.

구성 요소의 이름은 대문자로 시작 해야 합니다. 예를 들어 *MyCoolComponent* 는 유효 하며 *MyCoolComponent* 은 유효 하지 않습니다.

구성 요소에 대 한 UI는 HTML을 사용 하 여 정의 됩니다. 동적 렌더링 논리(예: 루프, 조건, 식)는 [Razor](xref:mvc/views/razor)라는 포함된 C# 구문을 사용하여 추가됩니다. 앱이 컴파일되면 HTML 태그 및 C# 렌더링 논리가 구성 요소 클래스로 변환 됩니다. 생성 된 클래스의 이름은 파일 이름과 일치 합니다.

구성 요소 클래스의 멤버는 `@code` 블록에서 정의됩니다. `@code` 블록에서 구성 요소 상태 (속성, 필드)는 이벤트 처리를 위한 메서드나 다른 구성 요소 논리를 정의 하는 데 지정 됩니다. 두 개 이상의 `@code` 블록이 허용됩니다.

구성 요소 멤버는 `@`로 시작 하는 식을 사용 하 여 C# 구성 요소의 렌더링 논리의 일부로 사용할 수 있습니다. 예를 들어 필드 C# 는 필드 이름에 `@`을 접두사로 하 여 렌더링 됩니다. 다음 예에서는를 평가 하 고 렌더링 합니다.

* `font-style`에 대 한 CSS 속성 값을 `_headingFontStyle` 합니다.
* `<h1>` 요소의 내용에 `_headingText` 합니다.

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

구성 요소가 처음 렌더링 되 면 구성 요소는 이벤트에 대 한 응답으로 렌더링 트리를 다시 생성 합니다. 그런 다음 Blazor는 새 렌더링 트리를 이전 렌더링 트리와 비교 하 여 브라우저의 DOM (문서 개체 모델)에 적용 합니다.

구성 요소는 C# 일반 클래스 이며 프로젝트 내의 어느 위치에 나 배치할 수 있습니다. 웹 페이지를 생성 하는 구성 요소는 일반적으로 *Pages* 폴더에 있습니다. 페이지를 지정 하지 않는 구성 요소는 일반적으로 *공유* 폴더 또는 프로젝트에 추가 된 사용자 지정 폴더에 배치 됩니다.

일반적으로 구성 요소의 네임 스페이스는 앱의 루트 네임 스페이스와 앱 내의 구성 요소 위치 (폴더)에서 파생 됩니다. 응용 프로그램의 루트 네임 스페이스가 `BlazorApp` 고 `Counter` 구성 요소가 *Pages* 폴더에 있는 경우:

* `Counter` 구성 요소의 네임 스페이스가 `BlazorApp.Pages`되었습니다.
* 구성 요소의 정규화 된 형식 이름이 `BlazorApp.Pages.Counter`입니다.

자세한 내용은 [구성 요소 가져오기](#import-components) 섹션을 참조 하세요.

사용자 지정 폴더를 사용 하려면 부모 구성 요소나 앱의 *_Imports razor* 파일에 사용자 지정 폴더의 네임 스페이스를 추가 합니다. 예를 들어 다음 네임 스페이스는 응용 프로그램의 루트 네임 스페이스가 `BlazorApp`때 *구성 요소* 폴더의 구성 요소를 사용할 수 있도록 합니다.

```razor
@using BlazorApp.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>Razor Pages 및 MVC 앱에 구성 요소 통합

Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합할 수 있습니다. 페이지나 보기가 렌더링 되 면 구성 요소를 동시에 미리 렌더링 된 수 있습니다.

Razor 구성 요소를 호스팅하도록 Razor Pages 또는 MVC 앱을 준비 하려면 <xref:blazor/hosting-model-configuration#integrate-razor-components-into-razor-pages-and-mvc-apps> 문서의 *Razor Pages 및 mvc 앱에 razor 구성 요소 통합* 섹션의 지침을 따르세요.

사용자 지정 폴더를 사용 하 여 앱의 구성 요소를 저장 하는 경우 폴더를 나타내는 네임 스페이스를 페이지/보기 또는 _ViewImports로 추가 합니다. *cshtml* 파일. 다음 예제에서는

* `MyAppNamespace`를 앱의 네임 스페이스로 변경 합니다.
* 구성 요소 라는 폴더를 사용 하 여 구성 요소를 저장 *하지 않은 경우* 에는 구성 요소가 상주 하는 폴더로 `Components`를 변경 합니다.

```csharp
@using MyAppNamespace.Components
```

*_ViewImports cshtml* 파일은 Razor Pages 앱의 *PAGES* 폴더 또는 MVC 앱의 *Views* 폴더에 있습니다.

페이지 또는 뷰에서 구성 요소를 렌더링 하려면 `Component` 태그 도우미를 사용 합니다.

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

매개 변수 형식은 JSON serializable 이어야 합니다 .이는 일반적으로 형식에 기본 생성자와 설정 가능한 속성이 있어야 함을 의미 합니다. 예를 들어 `IncrementAmount` 형식이 JSON serializer에서 지 원하는 기본 형식인 `int`이기 때문에 `IncrementAmount` 값을 지정할 수 있습니다.

구성 요소가 있는지 여부를 구성 하 `RenderMode` 다음을 수행 합니다.

* 는 페이지에 미리 렌더링 된 됩니다.
* 는 페이지에서 정적 HTML로 렌더링 되거나 사용자 에이전트에서 Blazor 앱을 부트스트랩 하는 데 필요한 정보가 포함 되어 있습니다.

| `RenderMode`        | 설명 |
| ------------------- | ----------- |
| `ServerPrerendered` | 구성 요소를 정적 HTML로 렌더링 하 고 Blazor 서버 앱에 대 한 마커를 포함 합니다. 사용자 에이전트가 시작 되 면이 표식은 Blazor 앱을 부트스트랩 하는 데 사용 됩니다. |
| `Server`            | Blazor 서버 앱에 대 한 마커를 렌더링 합니다. 구성 요소의 출력은 포함 되지 않습니다. 사용자 에이전트가 시작 되 면이 표식은 Blazor 앱을 부트스트랩 하는 데 사용 됩니다. |
| `Static`            | 구성 요소를 정적 HTML로 렌더링 합니다. |

페이지 및 뷰에서 구성 요소를 사용할 수 있지만 반대의 경우는 그렇지 않습니다. 구성 요소는 부분 보기 및 섹션과 같은 보기 및 페이지 관련 시나리오를 사용할 수 없습니다. 구성 요소의 부분 뷰에서 논리를 사용 하려면 부분 뷰 논리를 구성 요소에 대 한 요소로 처리 합니다.

정적 HTML 페이지에서 서버 구성 요소를 렌더링 하는 것은 지원 되지 않습니다.

구성 요소를 렌더링 하는 방법, 구성 요소 상태 및 `Component` 태그 도우미에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>

## <a name="tag-helpers-arent-used-in-components"></a>태그 도우미는 구성 요소에서 사용 되지 않습니다.

[태그 도우미](xref:mvc/views/tag-helpers/intro) 는 razor 구성 요소 (*razor* 파일)에서 지원 되지 않습니다. Blazor에서 태그 도우미와 유사한 기능을 제공 하려면 태그 도우미와 동일한 기능을 포함 하는 구성 요소를 만들고 구성 요소를 대신 사용 합니다.

## <a name="use-components"></a>구성 요소 사용

구성 요소에는 HTML 요소 구문을 사용 하 여 선언 함으로써 다른 구성 요소가 포함 될 수 있습니다. 구성 요소 사용을 위한 태그는 태그 이름이 구성 요소 유형인 HTML 태그처럼 보입니다.

특성 바인딩은 대/소문자를 구분 합니다. 예를 들어 `@bind` 유효 하 고 `@Bind` 잘못 되었습니다.

다음은 *인덱스 razor* 의 다음 태그를 `HeadingComponent` 인스턴스를 렌더링 합니다.

```razor
<HeadingComponent />
```

*Components/HeadingComponent*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

구성 요소에 구성 요소 이름과 일치 하지 않는 대문자 첫 글자가 포함 된 HTML 요소가 포함 된 경우 요소에 예기치 않은 이름이 있음을 나타내는 경고가 내보내집니다. 구성 요소 네임 스페이스에 대 한 `@using` 문을 추가 하면 구성 요소를 사용할 수 있게 되므로 경고가 제거 됩니다.

## <a name="component-parameters"></a>구성 요소 매개 변수

구성 요소는 구성 요소 클래스의 공용 속성을 사용 하 여 정의 되는 *구성 요소 매개 변수*를 포함할 수 있습니다 .이 매개 변수는 `[Parameter]` 특성 특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.

*Components/ChildComponent. razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

샘플 앱의 다음 예제에서는 `ParentComponent` `ChildComponent`의 `Title` 속성 값을 설정 합니다.

*Pages/ParentComponent. razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="child-content"></a>자식 콘텐츠

구성 요소는 다른 구성 요소의 콘텐츠를 설정할 수 있습니다. 할당 구성 요소는 받는 구성 요소를 지정 하는 태그 사이에 콘텐츠를 제공 합니다.

다음 예제에서 `ChildComponent`에는 렌더링할 UI 세그먼트를 나타내는 `RenderFragment`를 나타내는 `ChildContent` 속성이 있습니다. `ChildContent`의 값은 콘텐츠를 렌더링 해야 하는 구성 요소의 태그에 배치 됩니다. `ChildContent` 값은 부모 구성 요소에서 수신 되 고 부트스트랩 패널의 `panel-body`내에서 렌더링 됩니다.

*Components/ChildComponent. razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> `RenderFragment` 콘텐츠를 받는 속성은 규칙에 따라 `ChildContent` 이름이 지정 되어야 합니다.

샘플 앱의 `ParentComponent`는 콘텐츠를 `<ChildComponent>` 태그 안에 배치 하 여 `ChildComponent`를 렌더링 하기 위한 콘텐츠를 제공할 수 있습니다.

*Pages/ParentComponent. razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="attribute-splatting-and-arbitrary-parameters"></a>특성 스 플랫 및 임의 매개 변수

구성 요소는 구성 요소의 선언 된 매개 변수 외에 추가 특성도 캡처하고 렌더링할 수 있습니다. [`@attributes`](xref:mvc/views/razor#attributes) Razor 지시어를 사용 하 여 구성 요소를 렌더링할 때 사전에 추가 특성을 캡처한 다음 요소로 *splatted* 수 있습니다. 이 시나리오는 다양 한 사용자 지정을 지 원하는 태그 요소를 생성 하는 구성 요소를 정의 하는 경우에 유용 합니다. 예를 들어 많은 매개 변수를 지 원하는 `<input>`에 대해 개별적으로 특성을 정의 하는 것이 번거로울 수 있습니다.

다음 예제에서 첫 번째 `<input>` 요소 (`id="useIndividualParams"`)는 개별 구성 요소 매개 변수를 사용 하 고, 두 번째 `<input>` 요소 (`id="useAttributesDict"`)는 특성 스 플랫를 사용 합니다.

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

매개 변수의 형식은 문자열 키를 사용 하 여 `IEnumerable<KeyValuePair<string, object>>`를 구현 해야 합니다. 이 시나리오에서는 `IReadOnlyDictionary<string, object>` 사용도 선택할 수 있습니다.

두 방법을 모두 사용 하 여 렌더링 된 `<input>` 요소는 동일 합니다.

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

임의 특성을 허용 하려면 `CaptureUnmatchedValues` 속성이 `true`로 설정 된 `[Parameter]` 특성을 사용 하 여 구성 요소 매개 변수를 정의 합니다.

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

`[Parameter]`의 `CaptureUnmatchedValues` 속성을 사용 하면 매개 변수는 다른 매개 변수와 일치 하지 않는 모든 특성을 일치 시킬 수 있습니다. 구성 요소는 `CaptureUnmatchedValues`포함 된 단일 매개 변수만 정의할 수 있습니다. `CaptureUnmatchedValues`에 사용 되는 속성 형식은 문자열 키가 있는 `Dictionary<string, object>`에서 할당할 수 있어야 합니다. 이 시나리오에서 `IEnumerable<KeyValuePair<string, object>>` 또는 `IReadOnlyDictionary<string, object>`도 옵션입니다.

요소 특성의 위치를 기준으로 `@attributes`의 위치는 중요 합니다. 요소에 대 한 `@attributes` splatted 때 오른쪽에서 왼쪽으로 (last to first) 특성을 처리 합니다. `Child` 구성 요소를 사용 하는 구성 요소의 다음 예를 살펴보십시오.

*Parentcomponent. razor*:

```razor
<ChildComponent extra="10" />
```

*Childcomponent. razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Child` 구성 요소의 `extra` 특성은 `@attributes`오른쪽으로 설정 됩니다. 특성은 오른쪽에서 왼쪽으로 처리 되기 때문에 `Parent` 구성 요소의 렌더링 된 `<div>`는 추가 특성을 통해 전달 될 때 `extra="5"`를 포함 합니다.

```html
<div extra="5" />
```

다음 예에서는 `Child` 구성 요소의 `<div>`에서 `extra`와 `@attributes`의 순서를 반대로 합니다.

*Parentcomponent. razor*:

```razor
<ChildComponent extra="10" />
```

*Childcomponent. razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Parent` 구성 요소의 렌더링 된 `<div>`에는 추가 특성을 통해 전달 될 경우 `extra="10"` 포함 됩니다.

```html
<div extra="10" />
```

## <a name="data-binding"></a>데이터 바인딩

구성 요소와 DOM 요소 모두에 대 한 데이터 바인딩은 [`@bind`](xref:mvc/views/razor#bind) 특성을 사용 하 여 수행 됩니다. 다음 예제에서는 `CurrentValue` 속성을 텍스트 상자의 값에 바인딩합니다.

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

텍스트 상자가 포커스를 잃으면 속성의 값이 업데이트 됩니다.

텍스트 상자는 구성 요소가 렌더링 되는 경우에만 UI에서 업데이트 되며 속성의 값을 변경 하는 것에 대 한 응답으로는 업데이트 되지 않습니다. 이벤트 처리기 코드를 실행 한 후 구성 요소를 자체적으로 렌더링 하므로 속성 업데이트는 *일반적으로* 이벤트 처리기가 트리거되는 즉시 UI에 반영 됩니다.

`CurrentValue` 속성 (`<input @bind="CurrentValue" />`)과 함께 `@bind`를 사용 하는 것은 기본적으로 다음과 같습니다.

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

구성 요소가 렌더링 되 면 input 요소의 `value` `CurrentValue` 속성에서 제공 됩니다. 사용자가 텍스트 상자에를 입력 하 고 요소 포커스를 변경 하면 `onchange` 이벤트가 발생 하 고 `CurrentValue` 속성이 변경 된 값으로 설정 됩니다. 실제로는 `@bind`에서 형식 변환이 수행 되는 경우를 처리 하므로 코드 생성은 더 복잡 합니다. 원칙적으로 `@bind`은 식의 현재 값을 `value` 특성과 연결 하 고 등록 된 처리기를 사용 하 여 변경 내용을 처리 합니다.

`@bind` 구문을 사용 하 여 `onchange` 이벤트를 처리 하는 것 외에도 `event` 매개 변수 ([`@bind-value:event`](xref:mvc/views/razor#bind))를 사용 하 여 [`@bind-value`](xref:mvc/views/razor#bind) 특성을 지정 하 여 다른 이벤트를 사용 하 여 속성 또는 필드를 바인딩할 수 있습니다. 다음 예에서는 `oninput` 이벤트의 `CurrentValue` 속성을 바인딩합니다.

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

요소가 포커스를 잃을 때 발생 하는 `onchange`와는 달리 텍스트 상자의 값이 변경 될 때 발생 `oninput`.

이전 예제 바인딩의 `@bind-value`:

* 요소의 `value` 특성에 대 한 지정 된 식 (`CurrentValue`)입니다.
* `@bind-value:event`으로 지정 된 이벤트에 대 한 변경 이벤트 대리자입니다.

### <a name="unparsable-values"></a>구문 분석할 값

사용자가 데이터 바인딩된 요소에 구문 분석할 수 없는 값을 제공 하면 bind 이벤트가 트리거될 때 구문 분석할 수 없는 값이 자동으로 이전 값으로 되돌아갑니다.

다음과 같은 시나리오를 고려해 보세요.

* `<input>` 요소는 `123`초기 값을 사용 하 여 `int` 형식에 바인딩됩니다.

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* 사용자는 요소의 값을 업데이트 하 여 페이지에서 `123.45` 하 고 요소 포커스를 변경 합니다.

위의 시나리오에서 요소의 값은 `123`로 되돌아갑니다. `123`의 원래 값을 사용 하 여 `123.45` 값이 거부 되 면 사용자는 해당 값이 허용 되지 않는다는 것을 이해 합니다.

기본적으로 바인딩은 요소의 `onchange` 이벤트 (`@bind="{PROPERTY OR FIELD}"`)에 적용 됩니다. `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`를 사용 하 여 다른 이벤트를 설정 합니다. `oninput` 이벤트 (`@bind-value:event="oninput"`)의 경우 변경할지는 구문 분석할 값을 제공 하는 키 입력 후에 발생 합니다. `int`바인딩된 형식의 `oninput` 이벤트를 대상으로 지정 하는 경우 사용자는 `.` 문자를 입력할 수 없습니다. `.` 문자는 즉시 제거 되므로 사용자는 전체 숫자만 허용 되는 즉각적인 피드백을 받습니다. 사용자가 구문 분석할 수 없는 `<input>` 값을 지울 수 있어야 하는 경우와 같이 `oninput` 이벤트의 값을 되돌리는 것이 적합 하지 않은 시나리오가 있습니다. 대안은 다음과 같습니다.

* `oninput` 이벤트를 사용 하지 마세요. 요소가 포커스를 잃을 때까지 잘못 된 값이 되돌아가지 않는 기본 `onchange` 이벤트 (`@bind="{PROPERTY OR FIELD}"`)를 사용 합니다.
* `int?` 또는 `string`와 같은 nullable 형식에 바인딩하고 잘못 된 항목을 처리 하는 사용자 지정 논리를 제공 합니다.
* `InputNumber` 또는 `InputDate`와 같은 [폼 유효성 검사 구성 요소](xref:blazor/forms-validation)를 사용 합니다. 양식 유효성 검사 구성 요소에는 잘못 된 입력을 관리 하기 위한 기본 제공 지원이 있습니다. 양식 유효성 검사 구성 요소:
  * 사용자가 연결 된 `EditContext`에서 잘못 된 입력 및 수신 유효성 검사 오류를 제공할 수 있도록 허용 합니다.
  * 사용자가 추가 webform 데이터를 입력 하는 것을 방해 하지 않고 UI에서 유효성 검사 오류를 표시 합니다.

### <a name="globalization"></a>전역화

`@bind` 값은 현재 문화권의 규칙을 사용 하 여 표시 및 구문 분석을 위해 형식이 지정 됩니다.

현재 문화권은 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 속성에서 액세스할 수 있습니다.

[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) 는 다음 필드 형식 (`<input type="{TYPE}" />`)에 사용 됩니다.

* `date`
* `number`

이전 필드 형식:

* 적절 한 브라우저 기반 서식 지정 규칙을 사용 하 여 표시 됩니다.
* 자유 형식 텍스트를 포함할 수 없습니다.
* 브라우저의 구현에 따라 사용자 상호 작용 특성을 제공 합니다.

다음 필드 형식은 특정 형식 지정 요구 사항을 가지 며 현재 Blazor에서 지원 되지 않습니다. 모든 주요 브라우저에서 지원 되지 않기 때문입니다.

* `datetime-local`
* `month`
* `week`

`@bind`는 `@bind:culture` 매개 변수를 지원 하 여 값을 구문 분석 하 고 서식을 지정 하는 <xref:System.Globalization.CultureInfo?displayProperty=fullName>을 제공 합니다. `date` 및 `number` 필드 형식을 사용할 때는 문화권을 지정 하지 않는 것이 좋습니다. `date` 및 `number`에는 필수 문화권을 제공 하는 기본 제공 Blazor 지원이 있습니다.

사용자의 문화권을 설정 하는 방법에 대 한 자세한 내용은 [지역화](#localization) 섹션을 참조 하십시오.

### <a name="format-strings"></a>서식 문자열

데이터 바인딩은 [`@bind:format`](xref:mvc/views/razor#bind)를 사용 하 여 <xref:System.DateTime> 형식 문자열과 함께 작동 합니다. 통화 또는 숫자 형식과 같은 다른 형식 식은 현재 사용할 수 없습니다.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

위의 코드에서 `<input>` 요소의 필드 형식 (`type`)은 기본적으로 `text`입니다. 다음 .NET 형식의 바인딩에 대해 `@bind:format` 지원 됩니다.

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format` 특성은 `<input>` 요소의 `value`에 적용할 날짜 형식을 지정 합니다. 이 형식은 `onchange` 이벤트가 발생할 때 값을 구문 분석 하는 데도 사용 됩니다.

Blazor에서 날짜 형식을 기본적으로 지원 하기 때문에 `date` 필드 형식에 대 한 형식을 지정 하는 것은 권장 되지 않습니다. 권장 사항에도 불구 하 고 `date` 필드 형식과 함께 형식이 제공 되는 경우 바인딩이 제대로 작동 하려면 `yyyy-MM-dd` 날짜 형식만 사용 합니다.

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

### <a name="parent-to-child-binding-with-component-parameters"></a>구성 요소 매개 변수를 사용 하 여 부모-자식 바인딩

바인딩은 구성 요소 매개 변수를 인식 합니다. 여기서 `@bind-{property}`는 부모 구성 요소의 속성 값을 자식 구성 요소에 바인딩할 수 있습니다. 자식에서 부모로의 바인딩은 연결 된 bind 섹션을 [사용 하는 자식-부모 바인딩에](#child-to-parent-binding-with-chained-bind) 포함 됩니다.

다음 자식 구성 요소 (`ChildComponent`)에는 `Year` 구성 요소 매개 변수와 `YearChanged` 콜백이 있습니다.

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

`EventCallback<T>`는 [Eventcallback](#eventcallback) 섹션에 설명 되어 있습니다.

다음 부모 구성 요소는을 사용 합니다.

* 부모의 `ParentYear` 매개 변수를 `ChildComponent` 하 고 자식 구성 요소의 `Year` 매개 변수에 바인딩합니다.
* `onclick` 이벤트는 `ChangeTheYear` 메서드를 트리거하는 데 사용 됩니다. 자세한 내용은 [이벤트 처리](#event-handling) 섹션을 참조 하십시오.

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

`ParentComponent` 로드 하면 다음과 같은 태그가 생성 됩니다.

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

`ParentComponent`의 단추를 선택 하 여 `ParentYear` 속성 값이 변경 되 면 `ChildComponent`의 `Year` 속성이 업데이트 됩니다. `Year`의 새 값은 `ParentComponent`을 수행 하는 경우 UI에서 렌더링 됩니다.

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year` 매개 변수는 `Year` 매개 변수 형식과 일치 하는 도우미 `YearChanged` 이벤트를 포함 하기 때문에 바인딩할 수 있습니다.

규칙에 따라 `<ChildComponent @bind-Year="ParentYear" />`는 기본적으로 작성 하는 것과 같습니다.

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

일반적으로 속성은 `@bind-property:event` 특성을 사용 하 여 해당 이벤트 처리기에 바인딩할 수 있습니다. 예를 들어 `MyProp` 속성은 다음 두 가지 특성을 사용 하 여 `MyEventHandler`에 바인딩될 수 있습니다.

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

### <a name="child-to-parent-binding-with-chained-bind"></a>연결 된 바인딩을 사용 하는 자식-부모 바인딩

일반적인 시나리오는 데이터 바인딩된 매개 변수를 구성 요소 출력의 페이지 요소에 연결 하는 것입니다. 이 시나리오를 여러 수준의 바인딩이 동시에 발생 하기 때문에 연결 된 *바인딩* 이라고 합니다.

페이지의 요소에 `@bind` 구문을 사용 하 여 연결 된 바인딩을 구현할 수 없습니다. 이벤트 처리기 및 값은 별도로 지정 해야 합니다. 그러나 부모 구성 요소는 구성 요소의 매개 변수와 함께 `@bind` 구문을 사용할 수 있습니다.

다음 `PasswordField` 구성 요소 (*Passwordfield. razor*):

* `<input>` 요소의 값을 `Password` 속성으로 설정 합니다.
* [Eventcallback](#eventcallback)을 사용 하 여 `Password` 속성의 변경 내용을 부모 구성 요소에 노출 합니다.
* `ToggleShowPassword` 메서드를 트리거하는 데 사용 되는 `onclick` 이벤트를 사용 합니다. 자세한 내용은 [이벤트 처리](#event-handling) 섹션을 참조 하십시오.

```razor
<h1>Child Component</h2>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

`PasswordField` 구성 요소는 다른 구성 요소에서 사용 됩니다.

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

위의 예에서 암호에 대 한 검사 또는 트랩 오류를 수행 하려면 다음을 수행 합니다.

* `Password`에 대 한 지원 필드를 만듭니다 (다음 예제 코드에서`_password`).
* `Password` setter에서 확인 또는 트랩 오류를 수행 합니다.

다음 예에서는 암호 값에 공백을 사용 하는 경우 사용자에 게 즉각적인 피드백을 제공 합니다.

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

### <a name="radio-buttons"></a>라디오 단추

폼의 라디오 단추에 바인딩하는 방법에 대 한 자세한 내용은 <xref:blazor/forms-validation#work-with-radio-buttons>를 참조 하세요.

## <a name="event-handling"></a>이벤트 처리

Razor 구성 요소는 이벤트 처리 기능을 제공 합니다. 대리자 형식 값을 사용 하는 `on{EVENT}` (예: `onclick` 및 `onsubmit`) 이라는 HTML 요소 특성의 경우 Razor 구성 요소는 특성의 값을 이벤트 처리기로 처리 합니다. 특성의 이름은 항상 [`@on{EVENT}`](xref:mvc/views/razor#onevent)서식 지정 됩니다.

다음 코드는 UI에서 단추가 선택 될 때 `UpdateHeading` 메서드를 호출 합니다.

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

다음 코드는 UI에서 확인란이 변경 될 때 `CheckChanged` 메서드를 호출 합니다.

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

이벤트 처리기는 비동기 일 수도 있고 <xref:System.Threading.Tasks.Task>반환 될 수도 있습니다. [Statehaschanged](xref:blazor/lifecycle#state-changes)를 수동으로 호출할 필요가 없습니다. 예외가 발생 하면 기록 됩니다.

다음 예제에서는 단추를 선택 하면 `UpdateHeading`를 비동기적으로 호출 합니다.

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a>이벤트 인수 형식

일부 이벤트의 경우 이벤트 인수 형식이 허용 됩니다. 이러한 이벤트 유형 중 하나에 대 한 액세스가 필요 하지 않은 경우에는 메서드 호출에 필요 하지 않습니다.

지원 되는 `EventArgs` 다음 표에 나와 있습니다.

| 이벤트            | 클래스                | DOM 이벤트 및 참고 사항 |
| ---------------- | -------------------- | -------------------- |
| 클립보드        | `ClipboardEventArgs` | `oncut`에서 `oncopy`에서 `onpaste` |
| 옵니다             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>끌어온 항목 데이터를 포함 하는 `DataTransfer` 및 `DataTransferItem`. |
| Error            | `ErrorEventArgs`     | `onerror` |
| 이벤트            | `EventArgs`          | *일반*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*클립보드*<br>`onbeforecut`에서 `onbeforecopy`에서 `onbeforepaste`<br><br>*입력*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*미디어*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| 포커스            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>`relatedTarget`에 대 한 지원을 포함 하지 않습니다. |
| 입력            | `ChangeEventArgs`    | `onchange`, `oninput` |
| 키보드         | `KeyboardEventArgs`  | `onkeydown`에서 `onkeypress`에서 `onkeyup` |
| 마우스            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| 마우스 포인터    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| 마우스 휠      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| 진행률         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| 터치            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint` 터치를 구분 하는 장치에서 단일 접촉 지점을 나타냅니다. |

자세한 내용은 다음 리소스를 참조하십시오.

* [ASP.NET Core 참조 원본의 EventArgs 클래스 (dotnet/aspnetcore release/3.1 분기)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)
* [MDN 웹 문서: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; 각 DOM 이벤트를 지 원하는 HTML 요소에 대 한 정보를 포함 합니다.

### <a name="lambda-expressions"></a>람다 식

람다 식을 사용할 수도 있습니다.

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

요소 집합을 반복 하는 경우와 같이 추가 값을 닫는 것이 편리한 경우가 종종 있습니다. 다음 예제에서는 UI에서 선택 된 경우 각각 이벤트 인수 (`MouseEventArgs`) 및 해당 단추 번호 (`buttonNumber`)를 전달 하 `UpdateHeading`를 호출 하는 세 개의 단추를 만듭니다.

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> 람다 식에서 직접 루프 변수 (`i`)를 `for` 루프에서 사용 **하지** 마십시오. 그렇지 않으면 모든 람다 식에서 같은 변수를 사용 하 여 `i`값을 모든 람다에서 동일 하 게 합니다. 항상 지역 변수 (이전 예제에서는`buttonNumber`)에서 해당 값을 캡처한 다음 사용 합니다.

### <a name="eventcallback"></a>EventCallback

중첩 된 구성 요소를 사용 하는 일반적인 시나리오는 자식 구성 요소 이벤트가 발생할 때 부모 구성 요소의 메서드를 실행 하는 것입니다. 예를 들어, `onclick` 이벤트가 자식에서 발생할 때&mdash;합니다. 구성 요소 간에 이벤트를 노출 하려면 `EventCallback`을 사용 합니다. 부모 구성 요소는 자식 구성 요소의 `EventCallback`에 콜백 메서드를 할당할 수 있습니다.

샘플 앱 (component */ChildComponent. razor*)의 `ChildComponent`은 단추의 `onclick` 처리기가 샘플의 `ParentComponent`에서 `EventCallback` 대리자를 수신 하도록 설정 하는 방법을 보여 줍니다. `EventCallback`는 주변 장치의 `onclick` 이벤트에 적절 한 `MouseEventArgs`으로 입력 됩니다.

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent`은 자식의 `EventCallback<T>` (`OnClickCallback`)를 `ShowMessage` 메서드로 설정 합니다.

*Pages/ParentComponent. razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

`ChildComponent`에서 단추가 선택 된 경우:

* `ParentComponent`의 `ShowMessage` 메서드가 호출 됩니다. `_messageText` 업데이트 되 고 `ParentComponent`에 표시 됩니다.
* [Statehaschanged](xref:blazor/lifecycle#state-changes) 호출은 콜백의 메서드 (`ShowMessage`)에서 필요 하지 않습니다. 자식 이벤트가 자식 내에서 실행 되는 이벤트 처리기의 rerendering 구성 요소를 트리거하는 것 처럼 `StateHasChanged`는 자동으로 호출 되어 `ParentComponent`을 rerender 합니다.

`EventCallback` 및 `EventCallback<T>`는 비동기 대리자를 허용 합니다. `EventCallback<T>`은 강력한 형식이 며 특정 인수 형식이 필요 합니다. `EventCallback` 약하게 형식화 되며 모든 인수 유형을 허용 합니다.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

`InvokeAsync`를 사용 하 여 `EventCallback` 또는 `EventCallback<T>`를 호출 하 고 <xref:System.Threading.Tasks.Task>를 기다립니다.

```csharp
await callback.InvokeAsync(arg);
```

이벤트 처리 및 바인딩 구성 요소 매개 변수에 `EventCallback` 및 `EventCallback<T>`를 사용 합니다.

`EventCallback`보다 강력한 형식의 `EventCallback<T>`를 사용 하는 것이 좋습니다. `EventCallback<T>` 구성 요소의 사용자에 게 더 나은 오류 피드백을 제공 합니다. 다른 UI 이벤트 처리기와 마찬가지로 이벤트 매개 변수를 지정 하는 것은 선택 사항입니다. 콜백에 전달 된 값이 없는 경우 `EventCallback`를 사용 합니다.

### <a name="prevent-default-actions"></a>기본 동작 방지

이벤트에 대 한 기본 동작을 방지 하려면 [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 지시어 특성을 사용 합니다.

입력 장치에서 키를 선택 하 고 요소 포커스가 텍스트 상자에 있는 경우 브라우저는 일반적으로 텍스트 상자에 키 문자를 표시 합니다. 다음 예에서는 `@onkeypress:preventDefault` 지시어 특성을 지정 하 여 기본 동작을 방지 합니다. 카운터가 증가 하 고 **+** 키가 `<input>` 요소의 값으로 캡처되지 않습니다.

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

값 없이 `@on{EVENT}:preventDefault` 특성을 지정 하는 것은 `@on{EVENT}:preventDefault="true"`와 동일 합니다.

특성 값은 식일 수도 있습니다. 다음 예에서는 `true` 또는 `false`로 설정 된 `bool` 필드를 `_shouldPreventDefault` 합니다.

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

기본 동작을 방지 하기 위해 이벤트 처리기가 필요 하지 않습니다. 이벤트 처리기와 기본 작업 시나리오를 독립적으로 사용할 수 있습니다.

### <a name="stop-event-propagation"></a>이벤트 전파 중지

[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 지시어 특성을 사용 하 여 이벤트 전파를 중지 합니다.

다음 예제에서 확인란을 선택 하면 두 번째 자식 `<div>`의 click 이벤트가 부모 `<div>`에 전파 되지 않습니다.

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

## <a name="capture-references-to-components"></a>구성 요소에 대 한 참조 캡처

구성 요소 참조는 `Show` 또는 `Reset`와 같이 해당 인스턴스에 대 한 명령을 실행할 수 있도록 구성 요소 인스턴스를 참조 하는 방법을 제공 합니다. 구성 요소 참조를 캡처하려면:

* 자식 구성 요소에 [`@ref`](xref:mvc/views/razor#ref) 특성을 추가 합니다.
* 자식 구성 요소와 동일한 유형으로 필드를 정의 합니다.

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

구성 요소가 렌더링 되 면 `_loginDialog` 필드가 `MyLoginDialog` 자식 구성 요소 인스턴스로 채워집니다. 그런 다음 구성 요소 인스턴스에서 .NET 메서드를 호출할 수 있습니다.

> [!IMPORTANT]
> `_loginDialog` 변수는 구성 요소가 렌더링 된 후에만 채워지고 출력에는 `MyLoginDialog` 요소가 포함 됩니다. 이 시점까지 참조할 항목이 없습니다. 구성 요소에서 렌더링을 완료 한 후에 구성 요소 참조를 조작 하려면 [OnAfterRenderAsync 또는 OnAfterRender 메서드](xref:blazor/lifecycle#after-component-render)를 사용 합니다.

구성 요소 참조를 캡처하는 것은 [요소 참조를 캡처하](xref:blazor/javascript-interop#capture-references-to-elements)는 데 유사한 구문을 사용 하지만 [JavaScript interop](xref:blazor/javascript-interop) 기능은 아닙니다. 구성 요소 참조는 JavaScript 코드에 전달 되지&mdash;.NET 코드 에서만 사용 됩니다.

> [!NOTE]
> 구성 요소 참조를 사용 하 여 자식 구성 요소의 상태를 변경할 수 **없습니다** . 대신, 일반 선언적 매개 변수를 사용 하 여 자식 구성 요소에 데이터를 전달 합니다. 일반적인 선언적 매개 변수를 사용 하면 자식 구성 요소가 자동으로 올바른 시간에 rerender 됩니다.

## <a name="invoke-component-methods-externally-to-update-state"></a>외부에서 구성 요소 메서드를 호출 하 여 상태 업데이트

Blazor는 `SynchronizationContext`을 사용 하 여 단일 논리적 실행 스레드를 적용 합니다. 구성 요소의 [수명 주기 메서드](xref:blazor/lifecycle) 및 Blazor에 의해 발생 하는 모든 이벤트 콜백이이 `SynchronizationContext`실행 됩니다. 외부 이벤트 (예: 타이머 또는 다른 알림)를 기반으로 구성 요소를 업데이트 해야 하는 경우 Blazor의 `SynchronizationContext`에 디스패치할 `InvokeAsync` 메서드를 사용 합니다.

예를 들어 업데이트 된 상태의 수신 구성 요소를 알릴 수 있는 알림 *서비스* 를 살펴보겠습니다.

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

`NotifierService`를 singletion으로 등록 합니다.

* Blazor WebAssembly에서 서비스를 `Program.Main`에 등록 합니다.

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Blazor 서버에서 `Startup.ConfigureServices`에 서비스를 등록 합니다.

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

`NotifierService`를 사용 하 여 구성 요소를 업데이트 합니다.

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

위의 예제에서 `NotifierService`는 Blazor의 `SynchronizationContext`외부에서 구성 요소의 `OnNotify` 메서드를 호출 합니다. `InvokeAsync`은 올바른 컨텍스트로 전환 하 고 렌더링을 큐에 대기 하는 데 사용 됩니다.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>\@키를 사용 하 여 요소 및 구성 요소 유지 관리

요소 또는 구성 요소 목록을 렌더링할 때 이후에 요소나 구성 요소가 변경 되는 경우 Blazor의 diff 알고리즘은 유지할 수 있는 이전 요소 또는 구성 요소와 모델 개체가 이러한 요소에 매핑되는 방법을 결정 해야 합니다. 일반적으로이 프로세스는 자동 이며 무시 해도 되지만 프로세스를 제어 하는 경우가 있습니다.

다음 예를 살펴 보십시오.

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People` 컬렉션의 콘텐츠는 삽입, 삭제 또는 다시 정렬 된 항목으로 변경 될 수 있습니다. 구성 요소가 렌더링 면 `<DetailsEditor>` 구성 요소가 다른 `Details` 매개 변수 값을 받도록 변경 될 수 있습니다. 이로 인해 예상 보다 더 복잡 한 rerendering 발생할 수 있습니다. 경우에 따라 rerendering는 손실 요소 포커스와 같은 표시 되는 동작 차이를 일으킬 수 있습니다.

매핑 프로세스는 `@key` 지시어 특성을 사용 하 여 제어할 수 있습니다. `@key` diff 알고리즘은 키의 값에 따라 요소 또는 구성 요소를 보존 하도록 보장 합니다.

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People` 컬렉션이 변경 되 면 diff 알고리즘은 `<DetailsEditor>` 인스턴스와 `person` 인스턴스 간의 연결을 유지 합니다.

* `People` 목록에서 `Person` 삭제 된 경우 해당 `<DetailsEditor>` 인스턴스만 UI에서 제거 됩니다. 다른 인스턴스는 변경 되지 않은 상태로 유지 됩니다.
* `Person` 목록에서 특정 위치에 삽입 되는 경우 해당 위치에 하나의 새 `<DetailsEditor>` 인스턴스가 삽입 됩니다. 다른 인스턴스는 변경 되지 않은 상태로 유지 됩니다.
* `Person` 항목이 다시 정렬 되 면 해당 `<DetailsEditor>` 인스턴스가 유지 되 고 UI에서 다시 정렬 됩니다.

일부 시나리오에서는 `@key`를 사용 하 여 rerendering의 복잡성을 최소화 하 고 DOM의 상태 저장 부분 (예: 포커스 위치)에 대 한 잠재적인 문제를 방지 합니다.

> [!IMPORTANT]
> 키는 각 컨테이너 요소 또는 구성 요소에 대해 로컬입니다. 문서 전체에서 키를 전역적으로 비교 하지 않습니다.

### <a name="when-to-use-key"></a>\@키를 사용 하는 경우

일반적으로 목록이 렌더링 될 때마다 (예: `@foreach` 블록에서) `@key`를 사용 하 고 `@key`을 정의 하는 데 적합 한 값을 사용 하는 것이 좋습니다.

또한 `@key`를 사용 하 여 개체가 변경 될 때 Blazor가 요소 또는 구성 요소 하위 트리를 유지 하지 못하게 할 수 있습니다.

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

`@currentPerson` 변경 하는 경우 `@key` 특성 지시어는 강제로 전체 `<div>` 및 해당 하위 항목을 삭제 하 고 새 요소 및 구성 요소를 사용 하 여 UI 내에서 하위 트리를 다시 Blazor 합니다. 이는 `@currentPerson` 변경 될 때 UI 상태가 유지 되지 않도록 해야 하는 경우에 유용할 수 있습니다.

### <a name="when-not-to-use-key"></a>\@키를 사용 하지 않는 경우

`@key`으로 diff 때 성능 비용이 발생 합니다. 성능 비용은 크지 않지만 요소 또는 구성 요소 유지 규칙을 제어 하면 앱이 혜택을 받을 수 있는 경우에만 `@key`을 지정 합니다.

`@key` 사용 되지 않더라도 Blazor는 자식 요소와 구성 요소 인스턴스를 최대한 많이 보존 합니다. `@key`를 사용 하는 유일한 장점은 매핑을 선택 하는 diff 알고리즘 대신 모델 인스턴스가 유지 된 구성 요소 인스턴스에 매핑되 *는 방식을* 제어 하는 것입니다.

### <a name="what-values-to-use-for-key"></a>\@키에 사용할 값

일반적으로 `@key`에 대해 다음과 같은 종류의 값 중 하나를 제공 하는 것이 좋습니다.

* 모델 개체 인스턴스 (예: 이전 예제와 같은 `Person` 인스턴스) 이렇게 하면 개체 참조 일치를 기반으로 유지 됩니다.
* 고유 식별자 (예: `int`, `string`, `Guid`)의 기본 키 값입니다.

`@key`에 사용 되는 값이 충돌 하지 않는지 확인 합니다. 동일한 부모 요소 내에서 충돌 방지 값이 검색 되는 경우 Blazor는 기존 요소나 구성 요소를 새 요소나 구성 요소에 명확 하 게 매핑할 수 없기 때문에 예외를 throw 합니다. 개체 인스턴스 또는 기본 키 값과 같은 고유 값만 사용 합니다.

## <a name="routing"></a>라우팅

Blazor의 라우팅은 앱에서 액세스 가능한 각 구성 요소에 경로 템플릿을 제공 하 여 수행 됩니다.

`@page` 지시어를 사용 하 여 Razor 파일이 컴파일되면 생성 된 클래스에 경로 템플릿을 지정 하는 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 제공 됩니다. 런타임에 라우터는 `RouteAttribute`를 사용 하 여 구성 요소 클래스를 검색 하 고 요청 된 URL과 일치 하는 경로 템플릿을 포함 하는 구성 요소를 렌더링 합니다.

여러 경로 템플릿을 구성 요소에 적용할 수 있습니다. 다음 구성 요소는 `/BlazorRoute` 및 `/DifferentBlazorRoute`에 대 한 요청에 응답 합니다.

*Pages/BlazorRoute*:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

## <a name="route-parameters"></a>경로 매개 변수

구성 요소는 `@page` 지시문에 제공 된 경로 템플릿에서 경로 매개 변수를 받을 수 있습니다. 라우터는 경로 매개 변수를 사용 하 여 해당 하는 구성 요소 매개 변수를 채웁니다.

*Pages/RouteParameter*:

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

선택적 매개 변수는 지원 되지 않으므로 위의 예제에서 두 개의 `@page` 지시문이 적용 됩니다. 첫 번째는 매개 변수 없이 구성 요소에 대 한 탐색을 허용 합니다. 두 번째 `@page` 지시어는 `{text}` route 매개 변수를 사용 하 여 `Text` 속성에 값을 할당 합니다.

*Catch-* 여러 폴더 경계에서 경로를 캡처하는 모든 매개 변수 구문 (`*`/`**`)은 razor 구성 요소 (*razor*)에서 지원 **되지 않습니다** .

## <a name="partial-class-support"></a>Partial 클래스 지원

Razor 구성 요소는 부분 클래스로 생성 됩니다. Razor 구성 요소는 다음 방법 중 하나를 사용 하 여 작성 됩니다.

* C#코드는 HTML 태그와 Razor 코드를 포함 하는 [`@code`](xref:mvc/views/razor#code) 블록에서 단일 파일로 정의 됩니다. Blazor 템플릿은이 접근 방식을 사용 하 여 Razor 구성 요소를 정의 합니다.
* C#코드는 partial 클래스로 정의 된 코드 숨김이 파일에 배치 됩니다.

다음 예제에서는 Blazor 템플릿에서 생성 된 앱에서 `@code` 블록을 사용 하는 기본 `Counter` 구성 요소를 보여 줍니다. HTML 태그, Razor 코드 및 C# 코드는 동일한 파일에 있습니다.

*카운터. razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

Partial 클래스를 포함 하는 코드를 사용 하 여 `Counter` 구성 요소를 만들 수도 있습니다.

*카운터. razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

필요한 경우 partial 클래스 파일에 필요한 네임 스페이스를 추가 합니다. Razor 구성 요소에 사용 되는 일반적인 네임 스페이스는 다음과 같습니다.

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>기본 클래스 지정

[`@inherits`](xref:mvc/views/razor#inherits) 지시어를 사용 하 여 구성 요소에 대 한 기본 클래스를 지정할 수 있습니다. 다음 예제에서는 구성 요소가 기본 클래스 `BlazorRocksBase`를 상속 하 여 구성 요소의 속성과 메서드를 제공 하는 방법을 보여 줍니다. 기본 클래스는 `ComponentBase`에서 파생 되어야 합니다.

*Pages/BlazorRocks*:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs*:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a>특성 지정

특성은 [`@attribute`](xref:mvc/views/razor#attribute) 지시어를 사용 하 여 Razor 구성 요소에 지정할 수 있습니다. 다음 예제에서는 `[Authorize]` 특성을 component 클래스에 적용 합니다.

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>구성 요소 가져오기

Razor로 작성 된 구성 요소의 네임 스페이스는 (우선 순위)를 기반으로 합니다.

* Razor 파일 (*razor*) 태그에 지정 [`@namespace`](xref:mvc/views/razor#namespace) (`@namespace BlazorSample.MyNamespace`).
* 프로젝트 파일 (`<RootNamespace>BlazorSample</RootNamespace>`)에서 프로젝트의 `RootNamespace`입니다.
* 프로젝트 파일의 파일 이름 ( *.csproj*)에서 가져온 프로젝트 이름 및 프로젝트 루트에서 구성 요소로의 경로입니다. 예를 들어 프레임 워크는 네임 스페이스 `BlazorSample.Pages` *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample*)를 확인 합니다. 구성 요소 C# 는 이름 바인딩 규칙을 따릅니다. 이 예의 `Index` 구성 요소에 대 한 범위에 있는 구성 요소는 모든 구성 요소입니다.
  * 같은 폴더 *에 있습니다.*
  * 프로젝트 루트에서 다른 네임 스페이스를 명시적으로 지정 하지 않은 구성 요소입니다.

다른 네임 스페이스에 정의 된 구성 요소는 Razor의 [`@using`](xref:mvc/views/razor#using) 지시어를 사용 하 여 범위로 가져옵니다.

*BlazorSample/Shared/* folder에 다른 구성 요소인 `NavMenu.razor`있는 경우 다음 `@using` 문을 사용 하 여 `Index.razor`에서 구성 요소를 사용할 수 있습니다.

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

[`@using`](xref:mvc/views/razor#using) 지시문이 필요 하지 않은 정규화 된 이름을 사용 하 여 구성 요소를 참조할 수도 있습니다.

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> `global::` 한정자는 지원 되지 않습니다.
>
> 별칭이 지정 된 `using` 문 (예: `@using Foo = Bar`)을 가져오는 구성 요소가 지원 되지 않습니다.
>
> 부분적으로 정규화 된 이름은 지원 되지 않습니다. 예를 들어 `@using BlazorSample`를 추가 하 고 `<Shared.NavMenu></Shared.NavMenu>`를 사용 하 `NavMenu.razor`를 참조 하는 것은 지원 되지 않습니다

## <a name="conditional-html-element-attributes"></a>조건부 HTML 요소 특성

HTML 요소 특성은 .NET 값에 따라 조건부로 렌더링 됩니다. 값이 `false` 또는 `null`이면 특성이 렌더링 되지 않습니다. 값이 `true`이면 특성이 최소화 된 상태로 렌더링 됩니다.

다음 예제에서 `IsCompleted`는 `checked` 요소의 태그에서 렌더링 되는지 확인 합니다.

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

`IsCompleted` `true`이면 확인란은 다음과 같이 렌더링 됩니다.

```html
<input type="checkbox" checked />
```

`IsCompleted` `false`이면 확인란은 다음과 같이 렌더링 됩니다.

```html
<input type="checkbox" />
```

자세한 내용은 <xref:mvc/views/razor>을 참조하세요.

> [!WARNING]
> ASP.NET 형식이 `bool`경우에는 [aria를 누르는](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)등의 일부 HTML 특성이 제대로 작동 하지 않습니다. 이러한 경우 `bool`대신 `string` 유형을 사용 합니다.

## <a name="raw-html"></a>원시 HTML

일반적으로 문자열은 DOM 텍스트 노드를 사용 하 여 렌더링 됩니다. 즉, 포함 될 수 있는 모든 태그는 무시 되 고 리터럴 텍스트로 처리 됩니다. 원시 HTML을 렌더링 하려면 HTML 콘텐츠를 `MarkupString` 값으로 래핑합니다. 값은 HTML 또는 SVG로 구문 분석 되 고 DOM에 삽입 됩니다.

> [!WARNING]
> 신뢰할 수 없는 소스에서 생성 된 원시 HTML을 렌더링 하면 **보안상 위험할** 수 있으므로 피해 야 합니다.

다음 예제에서는 `MarkupString` 형식을 사용 하 여 정적 HTML 콘텐츠 블록을 구성 요소의 렌더링 된 출력에 추가 하는 방법을 보여 줍니다.

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>템플릿 기반 구성 요소

템플릿 기반 구성 요소는 하나 이상의 UI 템플릿을 매개 변수로 허용 하는 구성 요소 이며,이 구성 요소는 구성 요소의 렌더링 논리의 일부로 사용할 수 있습니다. 템플릿 기반 구성 요소를 사용 하면 일반 구성 요소 보다 다시 사용할 수 있는 상위 수준 구성 요소를 작성할 수 있습니다. 몇 가지 예는 다음과 같습니다.

* 사용자가 테이블의 머리글, 행 및 바닥글에 대 한 템플릿을 지정 하는 데 사용할 수 있는 테이블 구성 요소입니다.
* 사용자가 목록에서 항목을 렌더링 하기 위한 템플릿을 지정할 수 있도록 하는 목록 구성 요소입니다.

### <a name="template-parameters"></a>템플릿 매개 변수

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

### <a name="template-context-parameters"></a>템플릿 컨텍스트 매개 변수

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

### <a name="generic-typed-components"></a>제네릭 형식의 구성 요소

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

## <a name="cascading-values-and-parameters"></a>연계 값 및 매개 변수

일부 시나리오에서는 [구성 요소 매개 변수](#component-parameters)를 사용 하 여 상위 구성 요소에서 하위 구성 요소로 데이터를 이동 하는 것이 불편 합니다. 특히 구성 요소 계층이 여러 개 있는 경우에는 더욱 그렇습니다. 연계 값 및 매개 변수는 상위 구성 요소에서 모든 하위 구성 요소에 값을 제공 하는 편리한 방법을 제공 하 여이 문제를 해결 합니다. 연계 값 및 매개 변수를 사용 하 여 구성 요소를 조정할 수도 있습니다.

### <a name="theme-example"></a>테마 예

샘플 앱의 다음 예제에서 `ThemeInfo` 클래스는 응용 프로그램의 지정 된 부분에 있는 모든 단추가 동일한 스타일을 공유할 수 있도록 구성 요소 계층 구조의 아래로 이동 하는 테마 정보를 지정 합니다.

*UIThemeClasses/ThemeInfo*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

상위 구성 요소는 연계 값 구성 요소를 사용 하 여 연계 값을 제공할 수 있습니다. `CascadingValue` 구성 요소는 구성 요소 계층의 하위 트리를 래핑하고 해당 하위 트리 내의 모든 구성 요소에 단일 값을 제공 합니다.

예를 들어 샘플 앱은 `@Body` 속성의 레이아웃 본문을 구성 하는 모든 구성 요소에 대 한 연계 매개 변수로 앱 레이아웃 중 하나에서 테마 정보 (`ThemeInfo`)를 지정 합니다. `ButtonClass`에 레이아웃 구성 요소에서 `btn-success` 값이 할당 됩니다. 모든 하위 구성 요소는 `ThemeInfo` 연계 개체를 통해이 속성을 사용할 수 있습니다.

`CascadingValuesParametersLayout` 구성 요소:

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

연계 값을 사용 하기 위해 구성 요소는 `[CascadingParameter]` 특성을 사용 하 여 연계 매개 변수를 선언 합니다. 연계 값은 유형별 매개 변수에 바인딩됩니다.

샘플 앱에서 `CascadingValuesParametersTheme` 구성 요소는 `ThemeInfo` 연계 값을 연계 매개 변수에 바인딩합니다. 매개 변수는 구성 요소에 의해 표시 되는 단추 중 하나에 대해 CSS 클래스를 설정 하는 데 사용 됩니다.

`CascadingValuesParametersTheme` 구성 요소:

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

동일한 하위 트리 내에서 동일한 형식의 여러 값을 계단식으로 배열 하려면 각 `CascadingValue` 구성 요소와 해당 `CascadingParameter`에 고유한 `Name` 문자열을 제공 합니다. 다음 예에서는 두 개의 `CascadingValue` 구성 요소가 이름별로 `MyCascadingType`의 다른 인스턴스를 계단식으로 배열 합니다.

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

하위 구성 요소에서 종속 매개 변수는 상위 구성 요소의 해당 하는 종속 된 값에서 이름으로 해당 값을 받습니다.

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>TabSet 예제

연계 매개 변수를 사용 하면 구성 요소 계층 구조 전체에서 공동 작업할 수 있습니다. 예를 들어 샘플 앱에서 다음 *Tabset* 예제를 살펴보세요.

샘플 앱에는 탭에서 구현 하는 `ITab` 인터페이스가 있습니다.

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet` 구성 요소는 여러 `Tab` 구성 요소를 포함 하는 `TabSet` 구성 요소를 사용 합니다.

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

자식 `Tab` 구성 요소는 `TabSet`에 대 한 매개 변수로 명시적으로 전달 되지 않습니다. 대신 자식 `Tab` 구성 요소는 `TabSet`의 자식 콘텐츠에 속합니다. 그러나 `TabSet`는 헤더와 활성 탭을 렌더링할 수 있도록 각 `Tab` 구성 요소에 대 한 정보를 계속 알고 있어야 합니다. 추가 코드를 요구 하지 않고이 조정을 사용 하기 위해 `TabSet` 구성 요소는 하위 `Tab` 구성 요소에 의해 선택 되는 *연계 값으로 자신을 제공할 수 있습니다* .

`TabSet` 구성 요소:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

하위 `Tab` 구성 요소는 포함 하는 `TabSet`를 연계 매개 변수로 캡처하여 `Tab` 구성 요소가 `TabSet`에 추가 되 고 활성화 되는 탭을 조정 합니다.

`Tab` 구성 요소:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor 템플릿

렌더링 조각은 Razor 템플릿 구문을 사용 하 여 정의할 수 있습니다. Razor 템플릿은 UI 코드 조각을 정의 하 고 다음 형식을 가정 하는 방법입니다.

```razor
@<{HTML tag}>...</{HTML tag}>
```

다음 예제에서는 `RenderFragment` 및 `RenderFragment<T>` 값을 지정 하 고 구성 요소에서 직접 템플릿을 렌더링 하는 방법을 보여 줍니다. 렌더링 조각은 [템플릿 기반 구성 요소](#templated-components)에 인수로 전달 될 수도 있습니다.

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

이전 코드의 렌더링 된 출력:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="manual-rendertreebuilder-logic"></a>수동 RenderTreeBuilder 논리

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` 구성 요소를 코드에서 C# 수동으로 빌드하는 등 구성 요소 및 요소를 조작 하는 메서드를 제공 합니다.

> [!NOTE]
> 구성 요소를 만드는 데 `RenderTreeBuilder`를 사용 하는 것은 고급 시나리오입니다. 형식이 잘못 된 구성 요소 (예: 닫히지 않은 태그 태그)는 정의 되지 않은 동작을 발생 시킬 수 있습니다.

다른 구성 요소에 수동으로 빌드할 수 있는 다음 `PetDetails` 구성 요소를 고려 합니다.

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

다음 예제에서 `CreateComponent` 메서드의 루프는 세 개의 `PetDetails` 구성 요소를 생성 합니다. `RenderTreeBuilder` 메서드를 호출 하 여 구성 요소를 만드는 경우 (`OpenComponent` 및 `AddAttribute`) 시퀀스 번호는 소스 코드 줄 번호입니다. Blazor 차이점 알고리즘은 고유 호출 호출이 아닌 개별 코드 줄에 해당 하는 시퀀스 번호를 사용 합니다. `RenderTreeBuilder` 메서드를 사용 하 여 구성 요소를 만드는 경우 시퀀스 번호에 대 한 인수를 하드 코딩 합니다. **계산 또는 카운터를 사용 하 여 시퀀스 번호를 생성 하면 성능이 저하 될 수 있습니다.** 자세한 내용은 [시퀀스 번호를 코드 줄 번호와 관련 되 고 실행 순서와는 관련이 없습니다](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) . 섹션을 참조 하세요.

`BuiltContent` 구성 요소:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> `Microsoft.AspNetCore.Components.RenderTree` 형식을 사용 하면 렌더링 작업의 *결과* 를 처리할 수 있습니다. Blazor framework 구현의 내부 세부 정보입니다. 이러한 형식은 *불안정* 한 것으로 간주 되며 이후 릴리스에서 변경 될 수 있습니다.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>시퀀스 번호는 코드 줄 번호와 관련 되 고 실행 순서와는 관련이 없습니다.

Blazor `.razor` 파일은 항상 컴파일됩니다. 이는 컴파일 단계를 사용 하 여 런타임 시 앱 성능을 향상 시키는 정보를 주입할 수 있기 때문에 `.razor`에 매우 유용할 수 있습니다.

이러한 향상 된 기능을 포함 하는 주요 예로 *시퀀스 번호가*있습니다. 시퀀스 번호는 코드의 고유 하 고 정렬 된 줄에서 제공 되는 출력을 런타임에 표시 합니다. 런타임은이 정보를 사용 하 여 선형 시간에 효율적인 트리 차이을 생성 합니다 .이는 일반적으로 일반적인 트리 diff 알고리즘에 대해 가능한 것 보다 훨씬 빠릅니다.

다음 Razor 구성 요소 (*razor*) 파일을 살펴봅니다.

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

위의 코드는 다음과 같은 항목으로 컴파일됩니다.

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

코드가 처음 실행 될 때 `someFlag` `true`경우 작성기는 다음을 수신 합니다.

| Sequence | 형식      | data   |
| :------: | --------- | :----: |
| 0        | 텍스트 노드 | 첫째  |
| 1        | 텍스트 노드 | Second |

`someFlag` `false`되 고 태그가 다시 렌더링 된다고 가정 합니다. 이번에는 작성기가 다음을 받습니다.

| Sequence | 형식       | data   |
| :------: | ---------- | :----: |
| 1        | 텍스트 노드  | Second |

런타임에서 diff를 수행 하는 경우 시퀀스 `0`의 항목이 제거 된 것으로 확인 되므로 다음과 같은 trivial *편집 스크립트*를 생성 합니다.

* 첫 번째 텍스트 노드를 제거 합니다.

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a>프로그래밍 방식으로 시퀀스 번호를 생성 하는 경우 문제가 발생 합니다.

대신 다음과 같은 렌더링 트리 작성기 논리를 작성 했다고 가정해 보겠습니다.

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

이제 첫 번째 출력은 다음과 같습니다.

| Sequence | 형식      | data   |
| :------: | --------- | :----: |
| 0        | 텍스트 노드 | 첫째  |
| 1        | 텍스트 노드 | Second |

이 결과는 이전 사례와 동일 하므로 부정적인 문제가 없습니다. `someFlag`은 두 번째 렌더링에서 `false` 되며 출력은 다음과 같습니다.

| Sequence | 형식      | data   |
| :------: | --------- | ------ |
| 0        | 텍스트 노드 | Second |

이번에는 diff 알고리즘은 *두 가지* 변경이 발생 했음을 확인 하 고 알고리즘은 다음과 같은 편집 스크립트를 생성 합니다.

* 첫 번째 텍스트 노드의 값을 `Second`변경 합니다.
* 두 번째 텍스트 노드를 제거 합니다.

시퀀스 번호를 생성 하면 `if/else` 분기와 루프가 원래 코드에 표시 된 위치에 대 한 모든 유용한 정보가 손실 됩니다. 이로 인해 이전 처럼 비교가 **두 번** 수행 됩니다.

이는 간단한 예제입니다. 복잡 하 고 많이 중첩 된 구조와 특히 루프를 사용 하는 현실적인 경우에는 성능 비용이 더 심각 합니다. 삽입 또는 제거 된 루프 블록 또는 분기를 즉시 식별 하는 대신, diff 알고리즘은 렌더링 트리를 깊이 있게 구분 해야 하며, 일반적으로 이전 및 새 구조를 misinformed 하기 때문에 훨씬 더 긴 편집 스크립트를 작성 해야 합니다. 서로 관련 됩니다.

#### <a name="guidance-and-conclusions"></a>지침 및 결론

* 시퀀스 번호가 동적으로 생성 되 면 앱 성능이 저하 됩니다.
* 컴파일 시간에 캡처되는 경우를 제외 하 고 필요한 정보가 존재 하지 않기 때문에 프레임 워크는 런타임에 고유한 시퀀스 번호를 자동으로 만들 수 없습니다.
* 수동으로 구현 된 `RenderTreeBuilder` 논리의 긴 블록을 작성 하지 마세요. `.razor` 파일을 선호 하 고 컴파일러가 시퀀스 번호를 처리할 수 있도록 합니다. 수동 `RenderTreeBuilder` 논리를 방지할 수 없는 경우 긴 코드 블록을 `OpenRegion`/`CloseRegion` 호출에 래핑된 작은 조각으로 분할 합니다. 각 영역에는 고유한 시퀀스 번호 공간이 있으므로 각 지역 내에서 0 (또는 다른 임의의 임의의 숫자)으로 다시 시작할 수 있습니다.
* 시퀀스 번호가 하드 코딩 된 경우 diff 알고리즘에서는 값이 증가 하는 시퀀스 번호만 필요 합니다. 초기 값과 간격은 관련이 없습니다. 한 가지 합법적인 옵션은 코드 줄 번호를 시퀀스 번호로 사용 하거나 0부터 시작 하 여 또는 수백 (또는 선호 하는 간격) 만큼 증가 하는 것입니다. 
* Blazor는 시퀀스 번호를 사용 하는 반면 다른 트리 diff UI 프레임 워크는이를 사용 하지 않습니다. Diff는 시퀀스 번호를 사용 하는 경우 훨씬 더 빠르며, Blazor은 *razor* 파일을 작성 하는 개발자를 위해 자동으로 시퀀스 번호를 처리 하는 컴파일 단계를 활용 합니다.

## <a name="localization"></a>지역화

Blazor Server 앱은 [지역화 미들웨어](xref:fundamentals/localization#localization-middleware)를 사용 하 여 지역화 됩니다. 미들웨어는 앱에서 리소스를 요청 하는 사용자에 게 적절 한 문화권을 선택 합니다.

문화권은 다음 방법 중 하나를 사용 하 여 설정할 수 있습니다.

* [쿠키](#cookies)
* [문화권을 선택 하는 UI 제공](#provide-ui-to-choose-the-culture)

자세한 내용과 예제를 보려면 <xref:fundamentals/localization>을 참조하십시오.

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a>국제화를 위한 링커 구성 (Blazor Weasembmbambmbemboma)

기본적으로 Blazor WebAssembly 앱에 대한 Blazor의 링커 구성은 명시적으로 요청된 로캘을 제외하고 국제화 정보를 제거합니다. 링커 동작을 제어 하는 방법에 대 한 자세한 내용과 지침은 <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>를 참조 하세요.

### <a name="cookies"></a>쿠키

지역화 문화권 쿠키는 사용자의 문화권을 유지할 수 있습니다. 쿠키는 앱의 호스트 페이지 (*Pages/host-a*)의 `OnGet` 메서드에서 생성 됩니다. 지역화 미들웨어는 후속 요청에서 쿠키를 읽어 사용자의 culture를 설정 합니다. 

쿠키를 사용 하면 WebSocket 연결이 문화권을 올바르게 전파할 수 있습니다. 지역화 체계가 URL 경로 또는 쿼리 문자열을 기반으로 하는 경우 스키마는 Websocket을 사용 하지 못할 수 있으므로 문화권이 유지 되지 않습니다. 따라서 지역화 문화권 쿠키를 사용 하는 것이 좋습니다.

문화권이 지역화 쿠키에 유지 되는 경우 모든 기술을 사용 하 여 문화권을 할당할 수 있습니다. 서버 쪽 ASP.NET Core에 대해 설정 된 지역화 체계가 앱에 이미 있는 경우 앱의 기존 지역화 인프라를 계속 사용 하 고 앱의 체계 내에서 지역화 문화권 쿠키를 설정 합니다.

다음 예제에서는 지역화 미들웨어에서 읽을 수 있는 쿠키의 현재 문화권을 설정 하는 방법을 보여 줍니다. Blazor Server 앱에서 다음 내용을 사용 하 여 *Pages/Host. cshtml* 파일을 만듭니다.

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

지역화는 앱에서 처리 됩니다.

1. 브라우저가 앱에 초기 HTTP 요청을 보냅니다.
1. 문화권이 지역화 미들웨어에 의해 할당 됩니다.
1. *_Host* 의 `OnGet` 메서드는 응답의 일부로 쿠키의 문화권을 유지 합니다.
1. 브라우저는 WebSocket 연결을 열어 대화형 Blazor 서버 세션을 만듭니다.
1. 지역화 미들웨어는 쿠키를 읽고 문화권을 할당 합니다.
1. Blazor 서버 세션이 올바른 문화권으로 시작 합니다.

### <a name="provide-ui-to-choose-the-culture"></a>문화권을 선택 하는 UI 제공

사용자가 문화권을 선택할 수 있도록 UI를 제공 하려면 *리디렉션 기반 방법을* 사용 하는 것이 좋습니다. 이 프로세스는 사용자가 로그인 페이지로 리디렉션되고 다시 원래 리소스로 리디렉션되는&mdash;사용자가 보안 리소스에 액세스 하려고 할 때 웹 앱에서 발생 하는 작업과 비슷합니다. 

앱은 컨트롤러에 대 한 리디렉션을 통해 사용자가 선택한 문화권을 유지 합니다. 컨트롤러는 사용자가 선택한 문화권을 쿠키로 설정 하 고 사용자를 다시 원래 URI로 리디렉션합니다.

서버에서 HTTP 끝점을 설정 하 여 사용자가 선택한 문화권을 쿠키에 설정 하 고 다시 원래 URI로 리디렉션을 수행 합니다.

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> `LocalRedirect` 작업 결과를 사용 하 여 열린 리디렉션 공격을 방지 합니다. 자세한 내용은 <xref:security/preventing-open-redirects>을 참조하세요.

다음 구성 요소는 사용자가 문화권을 선택할 때 초기 리디렉션을 수행 하는 방법의 예를 보여 줍니다.

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-opno-locblazor-apps"></a>Blazor apps에서 .NET 지역화 시나리오 사용

Blazor apps 내에서 다음과 같은 .NET 지역화 및 세계화 시나리오를 사용할 수 있습니다.

* . NET의 리소스 시스템
* 문화권별 숫자 및 날짜 형식 지정

Blazor의 `@bind` 기능은 사용자의 현재 문화권에 따라 세계화를 수행 합니다. 자세한 내용은 [데이터 바인딩](#data-binding) 섹션을 참조 하세요.

제한 된 ASP.NET Core의 지역화 시나리오 집합이 현재 지원 됩니다.

* `IStringLocalizer<>`은 Blazor 앱에서 *지원 됩니다* .
* `IHtmlLocalizer<>`, `IViewLocalizer<>`및 데이터 주석 지역화는 MVC 시나리오 ASP.NET Core Blazor 앱에서 **지원 되지 않습니다** .

자세한 내용은 <xref:fundamentals/localization>을 참조하세요.

## <a name="scalable-vector-graphics-svg-images"></a>SVG (스케일러블 벡터 그래픽) 이미지

Blazor는 HTML을 렌더링 하므로 SVG (확장 가능한 벡터 그래픽) 이미지 (*svg*)를 비롯 한 브라우저 지원 이미지는 `<img>` 태그를 통해 지원 됩니다.

```html
<img alt="Example image" src="some-image.svg" />
```

마찬가지로, 스타일 시트 파일 ( *.css*)의 css 규칙에서 SVG 이미지가 지원 됩니다.

```css
.my-element {
    background-image: url("some-image.svg");
}
```

그러나 인라인 SVG 태그는 일부 시나리오에서 지원 되지 않습니다. 구성 요소 파일 (*razor*)에 `<svg>` 태그를 직접 저장 하는 경우 기본 이미지 렌더링이 지원 되지만 많은 고급 시나리오는 아직 지원 되지 않습니다. 예를 들어 `<use>` 태그는 현재 적용 되지 않으며 `@bind` 일부 SVG 태그와 함께 사용할 수 없습니다. 향후 릴리스에서 이러한 제한을 해결할 예정입니다.

## <a name="additional-resources"></a>추가 자료

* <xref:security/blazor/server> &ndash;에는 리소스 고갈에 경합 해야 하는 Blazor Server 앱 빌드에 대 한 지침이 포함 되어 있습니다.
