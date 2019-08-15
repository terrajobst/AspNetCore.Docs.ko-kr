---
title: ASP.NET Core Razor 구성 요소 만들기 및 사용
author: guardrex
description: 데이터에 바인딩하고, 이벤트를 처리 하 고, 구성 요소 수명 주기를 관리 하는 방법을 비롯 하 여 Razor 구성 요소를 만들고 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/components
ms.openlocfilehash: 8cb2dc4c3cd22fe71fe15c22762948f9dcd3c08f
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030356"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>ASP.NET Core Razor 구성 요소 만들기 및 사용

By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

Blazor apps는 *구성 요소*를 사용 하 여 빌드됩니다. 구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI (사용자 인터페이스)의 자체 포함 청크입니다. 구성 요소는 데이터를 삽입 하거나 UI 이벤트에 응답 하는 데 필요한 HTML 태그와 처리 논리를 포함 합니다. 구성 요소는 유연 하 고 간단 합니다. 프로젝트 간에 중첩, 재사용 및 공유 될 수 있습니다.

## <a name="component-classes"></a>구성 요소 클래스

구성 요소는 및 HTML 태그의 C# 조합을 사용 하 여 [razor](xref:mvc/views/razor) 구성 요소 파일 (*razor*)에서 구현 됩니다. Blazor의 구성 요소는 공식적으로 *Razor 구성 요소*라고 합니다.

구성 요소의 이름은 대문자로 시작 해야 합니다. 예를 들어 *MyCoolComponent* 는 유효 하며 *MyCoolComponent* 은 유효 하지 않습니다.

MSBuild 속성을 `_RazorComponentInclude` 사용 하 여 파일이 Razor 구성 요소 파일로 식별 되는 *경우에* 는이 파일 확장명을 사용 하 여 구성 요소를 작성할 수 있습니다. 예를 들어 *Pages* 폴더 아래의 모든 *Cshtml* 파일을 Razor 구성 요소 파일로 처리 하도록 지정 하는 앱이 있습니다.

```xml
<PropertyGroup>
  <_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
</PropertyGroup>
```

구성 요소에 대 한 UI는 HTML을 사용 하 여 정의 됩니다. 동적 렌더링 논리(예: 루프, 조건, 식)는 [Razor](xref:mvc/views/razor)라는 포함된 C# 구문을 사용하여 추가됩니다. 앱이 컴파일되면 HTML 태그 및 C# 렌더링 논리가 구성 요소 클래스로 변환 됩니다. 생성 된 클래스의 이름은 파일 이름과 일치 합니다.

구성 요소 클래스의 멤버는 `@code` 블록에서 정의됩니다. `@code` 블록에서 구성 요소 상태 (속성, 필드)는 이벤트 처리를 위한 메서드나 다른 구성 요소 논리를 정의 하는 데 지정 됩니다. 두 개 이상의 `@code` 블록이 허용됩니다.

> [!NOTE]
> ASP.NET Core 3.0 `@functions` 의 이전 미리 보기에서 블록은 Razor 구성 요소의 `@code` 블록과 동일한 용도에 사용 되었습니다. `@functions`블록은 Razor 구성 요소에서 계속 작동 하지만 ASP.NET Core 3.0 Preview 6 `@code` 이상에서 블록을 사용 하는 것이 좋습니다.

구성 요소 멤버는로 C# `@`시작 하는 식을 사용 하 여 구성 요소의 렌더링 논리의 일부로 사용할 수 있습니다. 예를 들어 필드 C# 는 필드 이름을 접두사로 `@` 하 여 렌더링 됩니다. 다음 예에서는를 평가 하 고 렌더링 합니다.

* `_headingFontStyle`에 대 한 `font-style`CSS 속성 값입니다.
* `_headingText``<h1>` 요소의 내용에 대 한입니다.

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

구성 요소가 처음 렌더링 되 면 구성 요소는 이벤트에 대 한 응답으로 렌더링 트리를 다시 생성 합니다. 그런 다음 Blazor는 새 렌더링 트리를 이전 렌더링 트리와 비교 하 여 브라우저의 DOM (문서 개체 모델)에 적용 합니다.

구성 요소는 C# 일반 클래스 이며 프로젝트 내의 어느 위치에 나 배치할 수 있습니다. 웹 페이지를 생성 하는 구성 요소는 일반적으로 *Pages* 폴더에 있습니다. 페이지를 지정 하지 않는 구성 요소는 일반적으로 *공유* 폴더 또는 프로젝트에 추가 된 사용자 지정 폴더에 배치 됩니다. 사용자 지정 폴더를 사용 하려면 부모 구성 요소 또는 앱의 *_Imports. razor* 파일에 사용자 지정 폴더의 네임 스페이스를 추가 합니다. 예를 들어 다음 네임 스페이스는 응용 프로그램의 루트 네임 스페이스가 인 `WebApplication`경우 *구성 요소* 폴더의 구성 요소를 사용할 수 있도록 합니다.

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>Razor Pages 및 MVC 앱에 구성 요소 통합

기존 Razor Pages 및 MVC 앱과 함께 구성 요소를 사용 합니다. Razor 구성 요소를 사용 하기 위해 기존 페이지나 뷰를 다시 작성할 필요는 없습니다. 페이지 또는 뷰가 렌더링 될 때 구성 요소는 동시에 미리 렌더링 된 됩니다.

페이지 또는 뷰에서 구성 요소를 렌더링 하려면 HTML 도우미 메서드를 `RenderComponentAsync<TComponent>` 사용 합니다.

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

페이지 및 뷰에서 구성 요소를 사용할 수 있지만 반대의 경우는 그렇지 않습니다. 구성 요소는 부분 보기 및 섹션과 같은 보기 및 페이지 관련 시나리오를 사용할 수 없습니다. 구성 요소의 부분 뷰에서 논리를 사용 하려면 부분 뷰 논리를 구성 요소에 대 한 요소로 처리 합니다.

구성 요소를 렌더링 하는 방법과 구성 요소 상태가 Blazor 서버 쪽 앱에서 관리 되는 방법에 대 한 <xref:blazor/hosting-models> 자세한 내용은 문서를 참조 하세요.

## <a name="use-components"></a>구성 요소 사용

구성 요소에는 HTML 요소 구문을 사용 하 여 선언 함으로써 다른 구성 요소가 포함 될 수 있습니다. 구성 요소 사용을 위한 태그는 태그 이름이 구성 요소 유형인 HTML 태그처럼 보입니다.

특성 바인딩은 대/소문자를 구분 합니다. 예 `@bind` 를 들어는 유효 하며 `@Bind` 는 유효 하지 않습니다.

*인덱스 razor* 의 다음 태그는 인스턴스를 `HeadingComponent` 렌더링 합니다.

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

*Components/HeadingComponent*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

구성 요소에 구성 요소 이름과 일치 하지 않는 대문자 첫 글자가 포함 된 HTML 요소가 포함 된 경우 요소에 예기치 않은 이름이 있음을 나타내는 경고가 내보내집니다. 구성 요소 네임 스페이스에 대 한 문을추가하면구성요소를사용할수있게되므로경고가제거됩니다.`@using`

## <a name="component-parameters"></a>구성 요소 매개 변수

구성 요소는 `[Parameter]` 특성을 사용 하 여 구성 요소 클래스의 공용 속성을 사용 하 여 정의 되는 *구성 요소 매개 변수*를 포함할 수 있습니다. 특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.

*Components/ChildComponent. razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

다음 예제에서는 `ParentComponent` 의 `Title` `ChildComponent`속성 값을 설정 합니다.

*Pages/ParentComponent. razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a>자식 콘텐츠

구성 요소는 다른 구성 요소의 콘텐츠를 설정할 수 있습니다. 할당 구성 요소는 받는 구성 요소를 지정 하는 태그 사이에 콘텐츠를 제공 합니다.

다음 예제에서에 `ChildComponent` 는 렌더링할 UI 세그먼트를 나타내는를 나타내는 `RenderFragment` `ChildContent` 속성이 있습니다. 의 `ChildContent` 값은 콘텐츠를 렌더링 해야 하는 구성 요소의 태그에 배치 됩니다. 의 `ChildContent` 값은 부모 구성 요소에서 수신 되 고 부트스트랩 패널의 `panel-body`내에서 렌더링 됩니다.

*Components/ChildComponent. razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> 콘텐츠를 `RenderFragment` 받는 속성의 이름은 규칙에 `ChildContent` 따라 지정 되어야 합니다.

다음 `ParentComponent` 은 `<ChildComponent>` 태그 내부에 내용을 배치 하 `ChildComponent` 여를 렌더링 하기 위한 콘텐츠를 제공할 수 있습니다.

*Pages/ParentComponent. razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>특성 스 플랫 및 임의 매개 변수

구성 요소는 구성 요소의 선언 된 매개 변수 외에 추가 특성도 캡처하고 렌더링할 수 있습니다. 추가 특성을 사전에 캡처한 다음 Razor 지시문을 [@attributes](xref:mvc/views/razor#attributes) 사용 하 여 구성 요소를 렌더링할 때 요소로 splatted 수 있습니다. 이 시나리오는 다양 한 사용자 지정을 지 원하는 태그 요소를 생성 하는 구성 요소를 정의 하는 경우에 유용 합니다. 예를 들어 많은 매개 변수를 지 원하는에 대해 `<input>` 별도로 특성을 정의 하는 것이 지루한 일 수 있습니다.

다음 예제 `<input>` 에서 첫 번째 요소 (`id="useIndividualParams"`)는 개별 구성 요소 매개 변수를 사용 하는 반면 두`id="useAttributesDict"`번째 `<input>` 요소 ()는 특성 스 플랫를 사용 합니다.

```cshtml
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
            { "required", "true" },
            { "size", "50" }
        };
}
```

매개 변수의 형식은 문자열 키를 사용 `IEnumerable<KeyValuePair<string, object>>` 하 여를 구현 해야 합니다. 을 `IReadOnlyDictionary<string, object>` 사용 하는 것도이 시나리오의 옵션입니다.

두 방법을 `<input>` 모두 사용 하 여 렌더링 된 요소는 동일 합니다.

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="true"
       size="50">
```

임의 특성을 허용 하려면 `[Parameter]` `CaptureUnmatchedValues` 속성이로 `true`설정 된 특성을 사용 하 여 구성 요소 매개 변수를 정의 합니다.

```cshtml
@code {
    [Parameter(CaptureUnmatchedAttributes = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

`CaptureUnmatchedValues` 의`[Parameter]` 속성을 사용 하면 매개 변수는 다른 매개 변수와 일치 하지 않는 모든 특성을 일치 시킬 수 있습니다. 구성 요소는을 사용 하 `CaptureUnmatchedValues`여 단일 매개 변수만 정의할 수 있습니다. 에 `CaptureUnmatchedValues` 사용 되는 속성 형식은 문자열 키를 `Dictionary<string, object>` 사용 하 여에서 할당할 수 있어야 합니다. `IEnumerable<KeyValuePair<string, object>>`또는 `IReadOnlyDictionary<string, object>` 이 시나리오의 옵션 이기도 합니다.

## <a name="data-binding"></a>데이터 바인딩

구성 요소와 DOM 요소 모두에 대 한 데이터 바인딩은 [@bind](xref:mvc/views/razor#bind) 특성을 사용 하 여 수행 됩니다. 다음 예제에서는 `_italicsCheck` 필드를 확인란이 선택 된 상태에 바인딩합니다.

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

확인란을 선택 하 고 선택 취소 하면 속성의 값이 각각 및 `true` `false`로 업데이트 됩니다.

확인란은 속성의 값을 변경 하는 것이 아니라 구성 요소가 렌더링 되는 경우에만 UI에서 업데이트 됩니다. 이벤트 처리기 코드를 실행 한 후 구성 요소를 자체적으로 렌더링 하므로 속성 업데이트는 일반적으로 UI에 즉시 반영 됩니다.

`CurrentValue` 속성 `@bind` (`<input @bind="CurrentValue" />`)과 함께를 사용 하는 것은 기본적으로 다음과 같습니다.

```cshtml
<input value="@CurrentValue"
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

구성 요소가 렌더링 되 면 input 요소의 `value` 은 `CurrentValue` 속성에서 가져옵니다. 사용자가 텍스트 상자에를 `onchange` 입력 하면 이벤트가 발생 `CurrentValue` 하 고 속성이 변경 된 값으로 설정 됩니다. 실제로 코드 생성은 형식 변환이 수행 되는 몇 가지 경우 `@bind` 를 처리 하기 때문에 좀 더 복잡 합니다. 원칙적 `@bind` 으로는 식 `value` 의 현재 값을 특성과 연결 하 고 등록 된 처리기를 사용 하 여 변경 내용을 처리 합니다.

`onchange` 구문을 사용 [@bind-value](xref:mvc/views/razor#bind) `event` [@bind-value:event](xref:mvc/views/razor#bind)하 여 이벤트를 처리 하는 것 외에도 매개 변수 ()를 사용 하 여 특성을 지정 하면 다른 이벤트를 사용 하 여 속성 또는 필드를 바인딩할 수 있습니다. `@bind` 다음 예제에서는 `oninput` 이벤트에 `CurrentValue` 대 한 속성을 바인딩합니다.

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

요소가 `onchange`포커스를 잃을 때 발생 하는와 달리 `oninput` 는 텍스트 상자의 값이 변경 될 때 발생 합니다.

**전역화**

`@bind`값은 현재 문화권의 규칙을 사용 하 여 표시 및 구문 분석을 위해 형식이 지정 됩니다.

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

`@bind`값을 구문 분석 하 고 <xref:System.Globalization.CultureInfo?displayProperty=fullName> 형식을 지정 하기 위해 매개변수를지원합니다.`@bind:culture` `date` 및`number` 필드 형식을 사용할 때는 문화권을 지정 하지 않는 것이 좋습니다. `date`및 `number` 에는 필수 문화권을 제공 하는 기본 제공 Blazor 지원이 있습니다.

사용자의 문화권을 설정 하는 방법에 대 한 자세한 내용은 [지역화](#localization) 섹션을 참조 하십시오.

**서식 문자열**

데이터 바인딩은를 사용 <xref:System.DateTime> 하 여 [@bind:format](xref:mvc/views/razor#bind)형식 문자열과 함께 작동 합니다. 통화 또는 숫자 형식과 같은 다른 형식 식은 현재 사용할 수 없습니다.

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

위의 코드에서 요소의 필드 형식 `<input>` (`type`)은 기본적으로로 `text`설정 됩니다. `@bind:format`는 다음 .NET 형식의 바인딩에 대해 지원 됩니다.

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

특성 `@bind:format` `value` 은 요소의`<input>` 에 적용할 날짜 형식을 지정 합니다. 형식은 `onchange` 이벤트가 발생할 때 값을 구문 분석 하는 데도 사용 됩니다.

Blazor에서 날짜 형식을 기본적 `date` 으로 지원 하기 때문에 필드 형식의 형식을 지정 하지 않는 것이 좋습니다.

**구성 요소 매개 변수**

바인딩은 구성 요소 매개 변수를 `@bind-{property}` 인식 합니다. 여기서는 구성 요소에서 속성 값을 바인딩할 수 있습니다.

다음 자식 구성 요소 (`ChildComponent`)에는 `Year` 구성 요소 매개 `YearChanged` 변수 및 콜백이 있습니다.

```cshtml
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

다음 부모 구성 요소는 `ChildComponent` 부모의 `ParentYear` 매개 변수를 `Year` 사용 하 여 자식 구성 요소의 매개 변수에 바인딩합니다.

```cshtml
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

을 로드 `ParentComponent` 하면 다음과 같은 태그가 생성 됩니다.

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

`ParentYear` 의 단추 `ParentComponent` 를선택`ChildComponent` 하 여 속성 값이 변경 되 면의 속성이업데이트됩니다.`Year` 의 `Year` 새 값은 `ParentComponent` 가 수행 될 때 UI에서 렌더링 됩니다.

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

매개 `Year` 변수는 `Year` 매개 변수 형식과 일치 하는 `YearChanged` 동반 이벤트를 포함 하기 때문에 바인딩할 수 있습니다.

규칙에 따라 `<ChildComponent @bind-Year="ParentYear" />` 은 기본적으로 다음을 작성 하는 것과 같습니다.

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

일반적으로 속성은 특성을 사용 하 여 `@bind-property:event` 해당 이벤트 처리기에 바인딩할 수 있습니다. 예를 들어 속성 `MyProp` 은 다음 두 가지 특성을 `MyEventHandler` 사용 하 여 바인딩할 수 있습니다.

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a>이벤트 처리

Razor 구성 요소는 이벤트 처리 기능을 제공 합니다. 대리자 형식 값을 사용 하 `on{event}` 여 ( `onclick` 예: 및 `onsubmit`) 이라는 HTML 요소 특성의 경우 Razor 구성 요소는 특성의 값을 이벤트 처리기로 처리 합니다. 특성 이름에는 항상 [ @on{event}](xref:mvc/views/razor#onevent)형식이 지정 됩니다.

다음 코드는 UI에서 `UpdateHeading` 단추가 선택 될 때 메서드를 호출 합니다.

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

다음 코드는 UI에서 `CheckChanged` 확인란이 변경 될 때 메서드를 호출 합니다.

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

이벤트 처리기는 비동기 일 수도 있고를 <xref:System.Threading.Tasks.Task>반환할 수도 있습니다. 를 수동으로 호출할 `StateHasChanged()`필요가 없습니다. 예외가 발생 하면 기록 됩니다.

다음 예제에서 단추를 `UpdateHeading` 선택 하면가 비동기적으로 호출 됩니다.

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a>이벤트 인수 형식

일부 이벤트의 경우 이벤트 인수 형식이 허용 됩니다. 이러한 이벤트 유형 중 하나에 대 한 액세스가 필요 하지 않은 경우에는 메서드 호출에 필요 하지 않습니다.

지원 되는 [Uieventargs](https://github.com/aspnet/AspNetCore/blob/release/3.0-preview8/src/Components/Components/src/UIEventArgs.cs) 는 다음 표에 나와 있습니다.

| 이벤트 | 클래스 |
| ----- | ----- |
| 클립보드 | `UIClipboardEventArgs` |
| 옵니다  | `UIDragEventArgs`는 끌어서 놓기 작업을 수행 하는 동안 끌어 온 데이터를 저장 하는 데 사용 되며 `UIDataTransferItem`하나 이상의를 보유할 수 있습니다. &ndash; `DataTransfer` `UIDataTransferItem`하나의 끌기 데이터 항목을 나타냅니다. |
| Error | `UIErrorEventArgs` |
| 포커스 | `UIFocusEventArgs`는에 대 한 `relatedTarget`지원을 포함 하지 않습니다. &ndash; |
| `<input>` 변경 | `UIChangeEventArgs` |
| 키보드 | `UIKeyboardEventArgs` |
| 마우스 | `UIMouseEventArgs` |
| 마우스 포인터 | `UIPointerEventArgs` |
| 마우스 휠 | `UIWheelEventArgs` |
| 진행률 | `UIProgressEventArgs` |
| 터치 | `UITouchEventArgs`&ndash; 터치를구분하는장치에서`UITouchPoint` 단일 접촉 지점을 나타냅니다. |

위의 표에서 이벤트의 속성 및 이벤트 처리 동작에 대 한 자세한 내용은 [참조 소스에서 EventArgs 클래스](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview8/src/Components/Web/src)를 참조 하세요.

### <a name="lambda-expressions"></a>람다 식

람다 식을 사용할 수도 있습니다.

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

요소 집합을 반복 하는 경우와 같이 추가 값을 닫는 것이 편리한 경우가 종종 있습니다. 다음 예제에서는 UI에서 선택 된 경우 각각 `UpdateHeading` 이벤트 인수 (`UIMouseEventArgs`)와 해당 단추 번호 (`buttonNumber`)를 전달 하는 세 개의 단추를 만듭니다.

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> 루프 변수 (`i`) `for` 를 람다 식에서 직접 루프에 사용 하지 마세요. 그렇지 않으면 모든 람다 식에서 동일한 변수를 사용 하 `i`여 모든 람다 식에서 값이 동일 하 게 됩니다. 항상 지역 변수 (`buttonNumber` 이전 예제)에서 해당 값을 캡처한 다음 사용 합니다.

### <a name="eventcallback"></a>EventCallback

중첩 된 구성 요소가 있는 일반적인 시나리오는 자식 구성 요소 이벤트가 발생&mdash;하는 경우 (예: 자식에서 `onclick` 이벤트가 발생할 때) 부모 구성 요소의 메서드를 실행 하고자 하는 것입니다. 구성 요소 간에 이벤트를 노출 하려면를 `EventCallback`사용 합니다. 부모 구성 요소는 콜백 메서드를 자식 구성 요소 `EventCallback`에 할당할 수 있습니다.

샘플 `ChildComponent` 앱의는 `onclick` 단추의 처리기가 샘플의 `ParentComponent`에서 `EventCallback` 대리자를 받도록 설정 되는 방법을 보여 줍니다. 는 `EventCallback` 주변 장치의 `onclick` 이벤트 `UIMouseEventArgs`에 적합 한로 형식화 됩니다.

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

는 `ParentComponent` 자식`EventCallback<T>` 를 해당`ShowMessage` 메서드로 설정 합니다.

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

에서 단추가 선택 된 경우 `ChildComponent`:

* `ParentComponent` 의`ShowMessage` 메서드가 호출 됩니다. `messageText`가 업데이트 되어에 `ParentComponent`표시 됩니다.
* 호출 `StateHasChanged` 은 콜백의 메서드 (`ShowMessage`)에서 필요 하지 않습니다. `StateHasChanged`자식 이벤트가 자식 내에서 실행 `ParentComponent`되는 이벤트 처리기의 rerendering 구성 요소를 트리거하는 것 처럼 rerender가 자동으로 호출 됩니다.

`EventCallback`및 `EventCallback<T>` 는 비동기 대리자를 허용 합니다. `EventCallback<T>`는 강력한 형식이 며 특정 인수 형식이 필요 합니다. `EventCallback`는 약하게 형식화 되며 모든 인수 형식을 허용 합니다.

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

`EventCallback<T>` <xref:System.Threading.Tasks.Task>를 사용 `EventCallback` 하`InvokeAsync` 여 또는를 호출 하 고 다음을 기다립니다.

```csharp
await callback.InvokeAsync(arg);
```

이벤트 `EventCallback` 처리 `EventCallback<T>` 및 바인딩 구성 요소 매개 변수에는 및를 사용 합니다.

`EventCallback<T>` 강력한`EventCallback`형식의를 사용 하는 것이 좋습니다. `EventCallback<T>`구성 요소의 사용자에 게 더 나은 오류 피드백을 제공 합니다. 다른 UI 이벤트 처리기와 마찬가지로 이벤트 매개 변수를 지정 하는 것은 선택 사항입니다. 콜백에 `EventCallback` 전달 된 값이 없는 경우를 사용 합니다.

## <a name="capture-references-to-components"></a>구성 요소에 대 한 참조 캡처

구성 요소 참조는 또는 `Show` `Reset`와 같은 해당 인스턴스에 대해 명령을 실행할 수 있도록 구성 요소 인스턴스를 참조 하는 방법을 제공 합니다. 구성 요소 참조를 캡처하려면:

* 자식 구성 요소에 [특성을추가합니다.@ref](xref:mvc/views/razor#ref)
* 자식 구성 요소와 동일한 유형으로 필드를 정의 합니다.

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

구성 요소가 렌더링 되 면 필드는 `loginDialog` `MyLoginDialog` 자식 구성 요소 인스턴스로 채워집니다. 그런 다음 구성 요소 인스턴스에서 .NET 메서드를 호출할 수 있습니다.

> [!IMPORTANT]
> 변수 `loginDialog` 는 구성 요소가 렌더링 된 후에만 채워지고 출력에는 `MyLoginDialog` 요소가 포함 됩니다. 이 시점까지 참조할 항목이 없습니다. 구성 요소에서 렌더링을 완료 한 후에 구성 요소 참조를 `OnAfterRenderAsync` 조작 `OnAfterRender` 하려면 또는 메서드를 사용 합니다.

<!-- HOLD https://github.com/aspnet/AspNetCore.Docs/pull/13818
Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.

The Razor compiler automatically generates a backing field for element and component references when using [@ref](xref:mvc/views/razor#ref). In the following example, there's no need to create a `myLoginDialog` field for the `LoginDialog` component:

```cshtml
<LoginDialog @ref="myLoginDialog" ... />

@code {
    private void OnSomething()
    {
        myLoginDialog.Show();
    }
}
```

When the component is rendered, the generated `myLoginDialog` field is populated with the `LoginDialog` component instance. You can then invoke .NET methods on the component instance.

In some cases, a backing field is required. For example, declare a backing field when referencing generic components. To suppress backing field generation, specify the `@ref:suppressField` parameter.

> [!IMPORTANT]
> The generated `myLoginDialog` variable is only populated after the component is rendered and its output includes the `LoginDialog` element. Until that point, there's nothing to reference. To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.
-->

구성 요소 참조를 캡처하는 것은 [요소 참조를 캡처하](xref:blazor/javascript-interop#capture-references-to-elements)는 데 유사한 구문을 사용 하지만 [JavaScript interop](xref:blazor/javascript-interop) 기능은 아닙니다. 구성 요소 참조는 JavaScript 코드로&mdash;전달 되지 않으며 .net 코드 에서만 사용 됩니다.

> [!NOTE]
> 구성 요소 참조를 사용 하 여 자식 구성 요소의 상태를 변경할 수 **없습니다** . 대신, 일반 선언적 매개 변수를 사용 하 여 자식 구성 요소에 데이터를 전달 합니다. 일반적인 선언적 매개 변수를 사용 하면 자식 구성 요소가 자동으로 올바른 시간에 rerender 됩니다.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>키 \@를 사용 하 여 요소 및 구성 요소 유지 관리

요소 또는 구성 요소 목록을 렌더링할 때 이후에 요소나 구성 요소가 변경 되는 경우 Blazor의 diff 알고리즘은 유지할 수 있는 이전 요소 또는 구성 요소와 모델 개체가 이러한 요소에 매핑되는 방법을 결정 해야 합니다. 일반적으로이 프로세스는 자동 이며 무시 해도 되지만 프로세스를 제어 하는 경우가 있습니다.

다음 예제를 참조하세요.

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People` 컬렉션의 콘텐츠는 삽입, 삭제 또는 다시 정렬 된 항목으로 변경 될 수 있습니다. 구성 요소가 렌더링 때 구성 요소 `<DetailsEditor>` 는 다른 `Details` 매개 변수 값을 받도록 변경 될 수 있습니다. 이로 인해 예상 보다 더 복잡 한 rerendering 발생할 수 있습니다. 경우에 따라 rerendering는 손실 요소 포커스와 같은 표시 되는 동작 차이를 일으킬 수 있습니다.

매핑 프로세스는 `@key` 지시어 특성을 사용 하 여 제어할 수 있습니다. `@key`diff 알고리즘에서 키의 값에 따라 요소 또는 구성 요소의 유지를 보장 하도록 합니다.

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="@person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

컬렉션이 변경 되 면 diff 알고리즘은 인스턴스 및 `person` 인스턴스 간 `<DetailsEditor>` 연결을 유지 합니다. `People`

* 이 목록에서 삭제 되 면 해당 `<DetailsEditor>` 인스턴스만 UI에서 제거 됩니다. `People` `Person` 다른 인스턴스는 변경 되지 않은 상태로 유지 됩니다.
* 가 목록의 특정 위치에 삽입 되 면 해당 위치에 하나의 새 `<DetailsEditor>` 인스턴스가 삽입 됩니다. `Person` 다른 인스턴스는 변경 되지 않은 상태로 유지 됩니다.
* 항목이 `Person` 다시 정렬 되 면 UI에서 해당 `<DetailsEditor>` 인스턴스가 유지 되 고 다시 정렬 됩니다.

일부 시나리오에서는를 사용 `@key` 하 여 rerendering의 복잡성을 최소화 하 고 DOM의 상태 저장 부분 (예: 포커스 위치)에 대 한 잠재적인 문제를 방지할 수 있습니다.

> [!IMPORTANT]
> 키는 각 컨테이너 요소 또는 구성 요소에 대해 로컬입니다. 문서 전체에서 키를 전역적으로 비교 하지 않습니다.

### <a name="when-to-use-key"></a>키를 사용 \@하는 경우

일반적으로 목록이 렌더링 될 때마다 ( `@key` 예: `@foreach` 블록에서)를 사용 하 고을 정의 하는 데 적합 한 `@key`값을 사용 하는 것이 좋습니다.

를 사용 `@key` 하 여 개체가 변경 될 때 Blazor가 요소 또는 구성 요소 하위 트리를 유지 하지 못하게 할 수도 있습니다.

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

변경 `@currentPerson` 하는 경우 `@key` attribute 지시어는 Blazor에서 전체 `<div>` 및 해당 하위 항목을 삭제 하 고 새 요소 및 구성 요소를 사용 하 여 UI 내에서 하위 트리를 다시 빌드합니다. 이는 변경 시 `@currentPerson` UI 상태가 유지 되지 않도록 보장 해야 하는 경우에 유용할 수 있습니다.

### <a name="when-not-to-use-key"></a>키를 사용 \@하지 않는 경우

로 diff 하 `@key`는 경우 성능이 저하 됩니다. 성능 비용은 크지 않지만 요소 또는 구성 요소 유지 `@key` 규칙을 제어 하면 앱이 혜택을 받을 수 있는 경우에만 지정 합니다.

를 사용 `@key` 하지 않더라도 Blazor는 자식 요소와 구성 요소 인스턴스를 최대한 많이 보존 합니다. 을 사용 `@key` 하는 경우의 유일한 장점은 매핑을 선택 하는 diff 알고리즘 대신 모델 인스턴스가 유지 된 구성 요소 인스턴스에 매핑되 *는 방식을* 제어 하는 것입니다.

### <a name="what-values-to-use-for-key"></a>키에 \@사용할 값

일반적으로에 대해 `@key`다음과 같은 종류의 값 중 하나를 제공 하는 것이 좋습니다.

* 모델 개체 인스턴스 (예: `Person` 이전 예제와 같은 인스턴스) 이렇게 하면 개체 참조 일치를 기반으로 유지 됩니다.
* 고유 식별자 (예:, `int` `string`또는 `Guid`형식의 기본 키 값)입니다.

에 `@key` 사용 되는 값이 충돌 하지 않는지 확인 합니다. 동일한 부모 요소 내에서 충돌 방지 값이 검색 되는 경우 Blazor는 기존 요소나 구성 요소를 새 요소나 구성 요소에 명확 하 게 매핑할 수 없기 때문에 예외를 throw 합니다. 개체 인스턴스 또는 기본 키 값과 같은 고유 값만 사용 합니다.

## <a name="lifecycle-methods"></a>수명 주기 메서드

`OnInitializedAsync`코드 `OnInitialized` 를 실행 하 여 구성 요소를 초기화 합니다. 비동기 작업을 수행 하려면 작업에서 `OnInitializedAsync` `await` 및 키워드를 사용 합니다.

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

동기 작업의 경우 다음을 `OnInitialized`사용 합니다.

```csharp
protected override void OnInitialized()
{
    ...
}
```

`OnParametersSetAsync`및 `OnParametersSet` 는 구성 요소가 부모 로부터 매개 변수를 수신 하 고 값이 속성에 할당 될 때 호출 됩니다. 이러한 메서드는 구성 요소 초기화 후와 구성 요소가 렌더링 될 때마다 실행 됩니다.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

`OnAfterRenderAsync`및 `OnAfterRender` 는 구성 요소 렌더링을 완료 한 후에 호출 됩니다. 요소 및 구성 요소 참조가이 시점에 채워집니다. 렌더링 된 DOM 요소에 대해 작동 하는 타사 JavaScript 라이브러리 활성화와 같이 렌더링 된 콘텐츠를 사용 하 여 추가 초기화 단계를 수행 하려면이 단계를 사용 합니다.

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

### <a name="handle-incomplete-async-actions-at-render"></a>렌더링 시 불완전 한 비동기 작업 처리

수명 주기 이벤트에서 수행 되는 비동기 작업은 구성 요소를 렌더링 하기 전에 완료 되지 않았을 수 있습니다. 수명 주기 메서드 `null` 를 실행 하는 동안 개체가 데이터로 채워지지 않거나 불완전 하 게 채워질 수 있습니다. 개체가 초기화 되었는지 확인 하는 렌더링 논리를 제공 합니다. 개체를 로드 하는 동안 자리 표시자 UI 요소 (예: 로드 메시지 `null`)를 렌더링 합니다.

Blazor 템플릿의 구성 요소에서`forecasts` 는비동기적receive예측데이터()로재정의됩니다.`OnInitializedAsync` `FetchData` 가 이면 `null`사용자에 게 로드 메시지가 표시 됩니다. `forecasts` `Task` 에서`OnInitializedAsync` 반환 된이 완료 되 면 구성 요소는 업데이트 된 상태를 사용 하 여 다시 시작 됩니다.

*Pages/FetchData.razor*:

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a>매개 변수를 설정 하기 전에 코드를 실행 합니다.

`SetParameters`매개 변수를 설정 하기 전에 코드를 실행 하도록 재정의할 수 있습니다.

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

가 `base.SetParameters` 호출 되지 않으면 사용자 지정 코드는 필요한 방식으로 들어오는 매개 변수 값을 해석할 수 있습니다. 예를 들어 클래스의 속성에는 들어오는 매개 변수를 할당할 필요가 없습니다.

### <a name="suppress-refreshing-of-the-ui"></a>UI 새로 고침 안 함

`ShouldRender`UI를 새로 고치지 않도록 재정의할 수 있습니다. 구현에서을 반환 `true`하면 UI가 새로 고쳐집니다. 가 재정의 `ShouldRender` 된 경우에도 구성 요소가 항상 처음에 렌더링 됩니다.

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>IDisposable을 사용한 구성 요소 삭제

구성 요소가을 구현 <xref:System.IDisposable>하는 경우 UI에서 구성 요소가 제거 되 면 [Dispose 메서드가](/dotnet/standard/garbage-collection/implementing-dispose) 호출 됩니다. 다음 구성 요소는 `@implements IDisposable` `Dispose` 및 메서드를 사용 합니다.

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a>라우팅

Blazor의 라우팅은 앱에서 액세스 가능한 각 구성 요소에 경로 템플릿을 제공 하 여 수행 됩니다.

`@page` 지시문을 사용 하는 Razor 파일이 컴파일되면 생성 된 클래스에 경로 템플릿을 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 지정 하는이 제공 됩니다. 런타임에 라우터는를 `RouteAttribute` 사용 하 여 구성 요소 클래스를 검색 하 고 요청 된 URL과 일치 하는 경로 템플릿을 포함 하는 구성 요소를 렌더링 합니다.

여러 경로 템플릿을 구성 요소에 적용할 수 있습니다. 다음 구성 요소는 및 `/BlazorRoute` `/DifferentBlazorRoute`에 대 한 요청에 응답 합니다.

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>경로 매개 변수

구성 요소는 `@page` 지시문에 제공 된 경로 템플릿에서 경로 매개 변수를 받을 수 있습니다. 라우터는 경로 매개 변수를 사용 하 여 해당 하는 구성 요소 매개 변수를 채웁니다.

*경로 매개 변수 구성 요소*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

선택적 매개 변수는 지원 되지 않으므로 `@page` 위의 예제에서 두 개의 지시문이 적용 됩니다. 첫 번째는 매개 변수 없이 구성 요소에 대 한 탐색을 허용 합니다. 두 번째 `@page` 지시문은 `{text}` 경로 매개 변수를 사용 하 여 값을 `Text` 속성에 할당 합니다.

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>"코드 숨김으로" 환경에 대 한 기본 클래스 상속

구성 요소 파일은 동일한 파일 C# 의 HTML 태그와 처리 코드를 혼합 합니다. 지시문 `@inherits` 을 사용 하 여 Blazor apps에 구성 요소 태그를 처리 코드에서 분리 하는 "코드를 사용 하는" 환경을 제공할 수 있습니다.

[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) 은 구성 요소가 기본 클래스인 `BlazorRocksBase`를 상속 하 여 구성 요소의 속성과 메서드를 제공 하는 방법을 보여 줍니다.

*Pages/BlazorRocks*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

*BlazorRocksBase.cs*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

기본 클래스는에서 `ComponentBase`파생 되어야 합니다.

## <a name="import-components"></a>구성 요소 가져오기

Razor로 작성 된 구성 요소의 네임 스페이스는 다음을 기반으로 합니다.

* 프로젝트의 `RootNamespace`입니다.
* 프로젝트 루트에서 구성 요소로의 경로입니다. 예를 들어 `ComponentsSample/Pages/Index.razor` 는 네임 스페이스 `ComponentsSample.Pages`에 있습니다. 구성 요소 C# 는 이름 바인딩 규칙을 따릅니다. *인덱스 razor*의 경우 동일한 폴더, *페이지*및 부모 폴더인 *ComponentsSample*의 모든 구성 요소가 범위에 있습니다.

다른 네임 스페이스에 정의 된 구성 요소는 Razor의 [ \@using](xref:mvc/views/razor#using) 지시문을 사용 하 여 범위로 가져올 수 있습니다.

폴더 `NavMenu.razor` `Index.razor` `@using` 에 다른 구성 요소인가 있는 경우에서 다음 문을 사용 하 여 구성 요소를 사용할 수 있습니다. `ComponentsSample/Shared/`

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

구성 요소는 정규화 된 이름을 사용 하 여 참조할 수도 있으므로 [ \@using](xref:mvc/views/razor#using) 지시문이 필요 하지 않습니다.

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> 한정자 `global::` 는 지원 되지 않습니다.
>
> 별칭이 `using` 지정 된 문 ( `@using Foo = Bar`예:)을 사용 하 여 가져올 구성 요소가 지원 되지 않습니다.
>
> 부분적으로 정규화 된 이름은 지원 되지 않습니다. 예를 들어를 `@using ComponentsSample` 추가 하 `NavMenu.razor` 고 `<Shared.NavMenu></Shared.NavMenu>` 를 참조 하는 것은 지원 되지 않습니다.

## <a name="conditional-html-element-attributes"></a>조건부 HTML 요소 특성

HTML 요소 특성은 .NET 값에 따라 조건부로 렌더링 됩니다. 값이 `false` 또는 `null`이면 특성이 렌더링 되지 않습니다. 값이 `true`이면 특성이 최소화 된 상태로 렌더링 됩니다.

다음 예제에서 `checked` 는가 `IsCompleted` 요소의 태그에서 렌더링 되는지 여부를 확인 합니다.

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

`IsCompleted` 가`true`이면 확인란은 다음과 같이 렌더링 됩니다.

```html
<input type="checkbox" checked />
```

`IsCompleted` 가`false`이면 확인란은 다음과 같이 렌더링 됩니다.

```html
<input type="checkbox" />
```

자세한 내용은 <xref:mvc/views/razor>을 참조하세요.

## <a name="raw-html"></a>원시 HTML

일반적으로 문자열은 DOM 텍스트 노드를 사용 하 여 렌더링 됩니다. 즉, 포함 될 수 있는 모든 태그는 무시 되 고 리터럴 텍스트로 처리 됩니다. 원시 html을 렌더링 하려면 html 콘텐츠 `MarkupString` 를 값으로 래핑합니다. 값은 HTML 또는 SVG로 구문 분석 되 고 DOM에 삽입 됩니다.

> [!WARNING]
> 신뢰할 수 없는 소스에서 생성 된 원시 HTML을 렌더링 하면 **보안상 위험할** 수 있으므로 피해 야 합니다.

다음 예제에서는 `MarkupString` 형식을 사용 하 여 정적 HTML 콘텐츠 블록을 구성 요소의 렌더링 된 출력에 추가 하는 방법을 보여 줍니다.

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>템플릿 기반 구성 요소

템플릿 기반 구성 요소는 하나 이상의 UI 템플릿을 매개 변수로 허용 하는 구성 요소 이며,이 구성 요소는 구성 요소의 렌더링 논리의 일부로 사용할 수 있습니다. 템플릿 기반 구성 요소를 사용 하면 일반 구성 요소 보다 다시 사용할 수 있는 상위 수준 구성 요소를 작성할 수 있습니다. 몇 가지 예는 다음과 같습니다.

* 사용자가 테이블의 머리글, 행 및 바닥글에 대 한 템플릿을 지정 하는 데 사용할 수 있는 테이블 구성 요소입니다.
* 사용자가 목록에서 항목을 렌더링 하기 위한 템플릿을 지정할 수 있도록 하는 목록 구성 요소입니다.

### <a name="template-parameters"></a>템플릿 매개 변수

템플릿 기반 구성 요소는 또는 `RenderFragment` `RenderFragment<T>`형식의 구성 요소 매개 변수를 하나 이상 지정 하 여 정의 됩니다. 렌더링 조각은 렌더링할 UI 세그먼트를 나타냅니다. `RenderFragment<T>`렌더링 조각이 호출 될 때 지정할 수 있는 형식 매개 변수를 사용 합니다.

`TableTemplate`구성 요소

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

템플릿 기반 구성 요소를 사용 하는 경우 매개 변수의 이름과 일치 하는 자식 요소 (`TableHeader` 및 `RowTemplate` 다음 예제에서는)를 사용 하 여 템플릿 매개 변수를 지정할 수 있습니다.

```cshtml
<TableTemplate Items="@pets">
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

요소로 전달 된 형식의 `RenderFragment<T>` 구성 요소 인수에는 라는 `context` 암시적 매개 변수가 있지만 (예: 앞의 코드 샘플 `@context.PetId`에서), 자식에 있는 특성을 `Context` 사용 하 여 매개 변수 이름을 변경할 수 있습니다. 요소인. 다음 예제에서 `Context` 요소의 특성은 `RowTemplate` 매개 변수를 `pet` 지정 합니다.

```cshtml
<TableTemplate Items="@pets">
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

또는 구성 요소 요소에 특성 `Context` 을 지정할 수 있습니다. 지정 된 `Context` 특성은 모든 지정 된 템플릿 매개 변수에 적용 됩니다. 이는 자식 요소를 래핑하는 대신 암시적 자식 콘텐츠에 대 한 콘텐츠 매개 변수 이름을 지정 하려는 경우에 유용할 수 있습니다. 다음 예제에서 특성은 `Context` `TableTemplate` 요소에 나타나고 모든 템플릿 매개 변수에 적용 됩니다.

```cshtml
<TableTemplate Items="@pets" Context="pet">
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

일반적으로 템플릿 구성 요소는 형식화 되어 있습니다. 예를 들어, 제네릭 `ListViewTemplate` 구성 요소를 사용 하 여 `IEnumerable<T>` 값을 렌더링할 수 있습니다. 제네릭 구성 요소를 정의 하려면 `@typeparam` 지시문을 사용 하 여 형식 매개 변수를 지정 합니다.

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

제네릭 형식의 구성 요소를 사용 하는 경우 가능 하면 형식 매개 변수가 유추 됩니다.

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

그렇지 않은 경우 형식 매개 변수의 이름과 일치 하는 특성을 사용 하 여 형식 매개 변수를 명시적으로 지정 해야 합니다. 다음 예제 `TItem="Pet"` 에서는 유형을 지정 합니다.

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>연계 값 및 매개 변수

일부 시나리오에서는 [구성 요소 매개 변수](#component-parameters)를 사용 하 여 상위 구성 요소에서 하위 구성 요소로 데이터를 이동 하는 것이 불편 합니다. 특히 구성 요소 계층이 여러 개 있는 경우에는 더욱 그렇습니다. 연계 값 및 매개 변수는 상위 구성 요소에서 모든 하위 구성 요소에 값을 제공 하는 편리한 방법을 제공 하 여이 문제를 해결 합니다. 연계 값 및 매개 변수를 사용 하 여 구성 요소를 조정할 수도 있습니다.

### <a name="theme-example"></a>테마 예

샘플 응용 프로그램의 다음 예제에서 클래스는 `ThemeInfo` 응용 프로그램의 지정 된 부분에 있는 모든 단추가 동일한 스타일을 공유 하도록 구성 요소 계층 구조의 아래쪽으로 이동 하는 테마 정보를 지정 합니다.

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

상위 구성 요소는 연계 값 구성 요소를 사용 하 여 연계 값을 제공할 수 있습니다. 구성 `CascadingValue` 요소 계층 구조의 하위 트리를 래핑하고 해당 하위 트리 내의 모든 구성 요소에 단일 값을 제공 합니다.

예를 들어 샘플 앱은`ThemeInfo` `@Body` 속성의 레이아웃 본문을 구성 하는 모든 구성 요소에 대 한 연계 매개 변수로 앱 레이아웃 중 하나에서 테마 정보 ()를 지정 합니다. `ButtonClass`에는 레이아웃 구성 요소 `btn-success` 에 값이 할당 됩니다. 모든 하위 구성 요소는 연계 개체를 `ThemeInfo` 통해이 속성을 사용할 수 있습니다.

`CascadingValuesParametersLayout`구성 요소

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

연계 값을 사용 하기 위해 구성 요소는 특성을 사용 하 `[CascadingParameter]` 여 연계 매개 변수를 선언 하거나 문자열 이름 값을 기반으로 합니다.

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

문자열 이름 값을 사용 하는 바인딩은 동일한 형식의 여러 연계 값이 있고 동일한 하위 트리 내에서이 값을 구분 해야 하는 경우에 적합 합니다.

연계 값은 유형별 매개 변수에 바인딩됩니다.

샘플 앱에서 구성 요소는 `CascadingValuesParametersTheme` 연계 값을 `ThemeInfo` 연계 매개 변수에 바인딩합니다. 매개 변수는 구성 요소에 의해 표시 되는 단추 중 하나에 대해 CSS 클래스를 설정 하는 데 사용 됩니다.

`CascadingValuesParametersTheme`구성 요소

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

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
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

### <a name="tabset-example"></a>TabSet 예제

연계 매개 변수를 사용 하면 구성 요소 계층 구조 전체에서 공동 작업할 수 있습니다. 예를 들어 샘플 앱에서 다음 *Tabset* 예제를 살펴보세요.

샘플 앱에는 탭 `ITab` 에서 구현 하는 인터페이스가 있습니다.

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

구성 요소는 여러 `TabSet` `Tab` 구성 요소를 포함 하는 구성 요소를 사용 합니다. `CascadingValuesParametersTabSet`

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

자식 `Tab` 구성 요소는에 매개 변수로 `TabSet`명시적으로 전달 되지 않습니다. 대신 자식 `Tab` 구성 요소는 `TabSet`의 자식 콘텐츠에 속합니다. 그러나는 `TabSet` 헤더와 활성 탭을 렌더링할 수 `Tab` 있도록 각 구성 요소에 대해 알고 있어야 합니다. 추가 코드를 `TabSet` 요구 하지 않고이 조정을 사용 하려면 구성 요소가 하위 `Tab` 구성 요소에 의해 선택 되는 *연계 값으로 자신을 제공할 수 있습니다* .

`TabSet`구성 요소

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

하위 구성 요소는 포함 `TabSet` 된를 연계 매개 변수로 캡처하기 때문에 `Tab` 구성 요소는 자신이 활성화 `TabSet` 된 탭과 해당 구성 요소를 자동으로 추가 합니다. `Tab`

`Tab`구성 요소

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor 템플릿

렌더링 조각은 Razor 템플릿 구문을 사용 하 여 정의할 수 있습니다. Razor 템플릿은 UI 코드 조각을 정의 하 고 다음 형식을 가정 하는 방법입니다.

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

다음 예제에서는 구성 요소에서 및 `RenderFragment` `RenderFragment<T>` 값을 지정 하 고 템플릿을 직접 렌더링 하는 방법을 보여 줍니다. 렌더링 조각은 [템플릿 기반 구성 요소](#templated-components)에 인수로 전달 될 수도 있습니다.

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

이전 코드의 렌더링 된 출력:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a>수동 RenderTreeBuilder 논리

`Microsoft.AspNetCore.Components.RenderTree`코드에서 C# 구성 요소를 수동으로 빌드하는 등 구성 요소 및 요소를 조작 하기 위한 메서드를 제공 합니다.

> [!NOTE]
> 구성 요소 `RenderTreeBuilder` 를 만드는 데를 사용 하는 것은 고급 시나리오입니다. 형식이 잘못 된 구성 요소 (예: 닫히지 않은 태그 태그)는 정의 되지 않은 동작을 발생 시킬 수 있습니다.

다른 구성 요소 `PetDetails` 에 수동으로 빌드할 수 있는 다음 구성 요소를 고려 합니다.

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

다음 예제에서 `CreateComponent` 메서드의 루프는 세 가지 `PetDetails` 구성 요소를 생성 합니다. 메서드를 `RenderTreeBuilder` 호출 하 여 구성 요소 (`OpenComponent` 및 `AddAttribute`)를 만드는 경우 시퀀스 번호는 소스 코드 줄 번호입니다. Blazor 차이점 알고리즘은 고유 호출 호출이 아닌 개별 코드 줄에 해당 하는 시퀀스 번호를 사용 합니다. 메서드를 사용 `RenderTreeBuilder` 하 여 구성 요소를 만드는 경우 시퀀스 번호에 대 한 인수를 하드 코딩 합니다. **계산 또는 카운터를 사용 하 여 시퀀스 번호를 생성 하면 성능이 저하 될 수 있습니다.** 자세한 내용은 [시퀀스 번호를 코드 줄 번호와 관련 되 고 실행 순서와는 관련이 없습니다](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) . 섹션을 참조 하세요.

`BuiltContent`구성 요소

```cshtml
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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>시퀀스 번호는 코드 줄 번호와 관련 되 고 실행 순서와는 관련이 없습니다.

Blazor `.razor` 파일은 항상 컴파일됩니다. 이는 컴파일 단계를 사용 하 `.razor` 여 런타임 시 앱 성능을 향상 시키는 정보를 주입할 수 있기 때문에의 경우에 매우 유용할 수 있습니다.

이러한 향상 된 기능을 포함 하는 주요 예로 *시퀀스 번호가*있습니다. 시퀀스 번호는 코드의 고유 하 고 정렬 된 줄에서 제공 되는 출력을 런타임에 표시 합니다. 런타임은이 정보를 사용 하 여 선형 시간에 효율적인 트리 차이을 생성 합니다 .이는 일반적으로 일반적인 트리 diff 알고리즘에 대해 가능한 것 보다 훨씬 빠릅니다.

다음과 같은 간단한 `.razor` 파일을 고려 하십시오.

```cshtml
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

코드가 처음 `someFlag` 실행 될 때가 `true`이면 작성기에서 다음을 수신 합니다.

| Sequence | 형식      | 보기   |
| :------: | --------- | :----: |
| 0        | 텍스트 노드 | 첫째  |
| 1        | 텍스트 노드 | Second |

`someFlag` 가이되고태그가다시렌더링되는`false`것으로 가정 합니다. 이번에는 작성기가 다음을 받습니다.

| Sequence | 형식       | 보기   |
| :------: | ---------- | :----: |
| 1        | 텍스트 노드  | Second |

런타임에서 diff를 수행 하는 경우 시퀀스 `0` 에 있는 항목이 제거 된 것을 확인 하 여 다음과 같은 trivial *편집 스크립트*를 생성 합니다.

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

| Sequence | 형식      | 보기   |
| :------: | --------- | :----: |
| 0        | 텍스트 노드 | 첫째  |
| 1        | 텍스트 노드 | Second |

이 결과는 이전 사례와 동일 하므로 부정적인 문제가 없습니다. `someFlag`는 두 번째 렌더링 에있고출력은다음과같습니다.`false`

| Sequence | 형식      | 보기   |
| :------: | --------- | ------ |
| 0        | 텍스트 노드 | Second |

이번에는 diff 알고리즘은 *두 가지* 변경이 발생 했음을 확인 하 고 알고리즘은 다음과 같은 편집 스크립트를 생성 합니다.

* 첫 번째 텍스트 노드의 값을로 `Second`변경 합니다.
* 두 번째 텍스트 노드를 제거 합니다.

시퀀스 번호를 생성 하면 원래 코드에서 `if/else` 분기와 루프가 있는 위치에 대 한 모든 유용한 정보가 손실 됩니다. 이로 인해 이전 처럼 비교가 **두 번** 수행 됩니다.

이는 간단한 예제입니다. 복잡 하 고 많이 중첩 된 구조와 특히 루프를 사용 하는 현실적인 경우에는 성능 비용이 더 심각 합니다. 삽입 또는 제거 된 루프 블록 또는 분기를 즉시 식별 하는 대신, diff 알고리즘은 렌더링 트리를 깊이 있게 구분 해야 하며, 일반적으로 이전 및 새 구조를 misinformed 하기 때문에 훨씬 더 긴 편집 스크립트를 작성 해야 합니다. 서로 관련 됩니다.

#### <a name="guidance-and-conclusions"></a>지침 및 결론

* 시퀀스 번호가 동적으로 생성 되 면 앱 성능이 저하 됩니다.
* 컴파일 시간에 캡처되는 경우를 제외 하 고 필요한 정보가 존재 하지 않기 때문에 프레임 워크는 런타임에 고유한 시퀀스 번호를 자동으로 만들 수 없습니다.
* 수동으로 구현 `RenderTreeBuilder` 된 논리의 긴 블록을 작성 하지 마세요. 파일 `.razor` 을 선호 하 고 컴파일러가 시퀀스 번호를 처리할 수 있도록 합니다.
* 시퀀스 번호가 하드 코딩 된 경우 diff 알고리즘에서는 값이 증가 하는 시퀀스 번호만 필요 합니다. 초기 값과 간격은 관련이 없습니다. 한 가지 합법적인 옵션은 코드 줄 번호를 시퀀스 번호로 사용 하거나 0부터 시작 하 여 또는 수백 (또는 선호 하는 간격) 만큼 증가 하는 것입니다. 
* Blazor는 시퀀스 번호를 사용 하지만 다른 트리 diff UI 프레임 워크는이를 사용 하지 않습니다. 시퀀스 번호가 사용 되는 경우 diff는 훨씬 더 빠르며, Blazor는 개발자가 파일을 작성 `.razor` 하는 개발자를 위해 자동으로 시퀀스 번호를 처리 하는 컴파일 단계를 활용할 수 있습니다.

## <a name="localization"></a>지역화

Blazor 서버 쪽 앱은 [지역화 미들웨어](xref:fundamentals/localization#localization-middleware)를 사용 하 여 지역화 됩니다. 미들웨어는 앱에서 리소스를 요청 하는 사용자에 게 적절 한 문화권을 선택 합니다.

문화권은 다음 방법 중 하나를 사용 하 여 설정할 수 있습니다.

* [쿠키](#cookies)
* [문화권을 선택 하는 UI 제공](#provide-ui-to-choose-the-culture)

자세한 내용과 예제를 보려면 <xref:fundamentals/localization>을 참조하십시오.

### <a name="cookies"></a>쿠키

지역화 문화권 쿠키는 사용자의 문화권을 유지할 수 있습니다. 쿠키는 응용 프로그램의 호스트 `OnGet` 페이지 (*Pages/host-a*)의 메서드에 의해 만들어집니다. 지역화 미들웨어는 후속 요청에서 쿠키를 읽어 사용자의 culture를 설정 합니다. 

쿠키를 사용 하면 WebSocket 연결이 문화권을 올바르게 전파할 수 있습니다. 지역화 체계가 URL 경로 또는 쿼리 문자열을 기반으로 하는 경우 스키마는 Websocket을 사용 하지 못할 수 있으므로 문화권이 유지 되지 않습니다. 따라서 지역화 문화권 쿠키를 사용 하는 것이 좋습니다.

문화권이 지역화 쿠키에 유지 되는 경우 모든 기술을 사용 하 여 문화권을 할당할 수 있습니다. 서버 쪽 ASP.NET Core에 대해 설정 된 지역화 체계가 앱에 이미 있는 경우 앱의 기존 지역화 인프라를 계속 사용 하 고 앱의 체계 내에서 지역화 문화권 쿠키를 설정 합니다.

다음 예제에서는 지역화 미들웨어에서 읽을 수 있는 쿠키의 현재 문화권을 설정 하는 방법을 보여 줍니다. Blazor 서버 쪽 앱에서 다음 내용을 사용 하 여 *Pages/Host. cshtml* 파일을 만듭니다.

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
1. _Host `OnGet` 의 메서드 는 응답의 일부로 쿠키의 문화권을 유지 합니다.
1. 브라우저는 WebSocket 연결을 열어 대화형 Blazor 서버 쪽 세션을 만듭니다.
1. 지역화 미들웨어는 쿠키를 읽고 문화권을 할당 합니다.
1. Blazor 서버 쪽 세션이 올바른 문화권으로 시작 합니다.

## <a name="provide-ui-to-choose-the-culture"></a>문화권을 선택 하는 UI 제공

사용자가 문화권을 선택할 수 있도록 UI를 제공 하려면 *리디렉션 기반 방법을* 사용 하는 것이 좋습니다. 이 프로세스는 사용자가 보안 리소스&mdash;에 액세스 하려고 할 때 사용자가 로그인 페이지로 리디렉션되고 다시 원래 리소스로 리디렉션되는 경우와 비슷합니다. 

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

```cshtml
@inject IUriHelper UriHelper

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(UIChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(UriHelper.GetAbsoluteUri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        UriHelper.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a>Blazor apps에서 .NET 지역화 시나리오 사용

Blazor apps 내에서 다음과 같은 .NET 지역화 및 세계화 시나리오를 사용할 수 있습니다.

* . NET의 리소스 시스템
* 문화권별 숫자 및 날짜 형식 지정

Blazor의 `@bind` 기능은 사용자의 현재 문화권에 따라 세계화를 수행 합니다. 자세한 내용은 [데이터 바인딩](#data-binding) 섹션을 참조 하세요.

제한 된 ASP.NET Core의 지역화 시나리오 집합이 현재 지원 됩니다.

* `IStringLocalizer<>`는 Blazor apps에서 *지원 됩니다* .
* `IHtmlLocalizer<>`, `IViewLocalizer<>`및 데이터 주석 지역화는 MVC 시나리오 ASP.NET Core Blazor apps에서 **지원 되지 않습니다** .

자세한 내용은 <xref:fundamentals/localization>을 참조하세요.
