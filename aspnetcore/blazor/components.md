---
title: ASP.NET Core Razor 구성 요소 만들기 및 사용
author: guardrex
description: 데이터에 바인딩하고, 이벤트를 처리하고, 구성 요소 수명 주기를 관리하는 방법을 비롯하여 Razor 구성 요소를 만들고 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 7afc9250cdfb4b791ef939ead0f41b503d83fad8
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511277"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>ASP.NET Core Razor 구성 요소 만들기 및 사용

작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

Blazor 앱은 *구성 요소*를 사용하여 빌드됩니다. 구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI(사용자 인터페이스)의 자체 포함 청크입니다. 구성 요소는 데이터를 주입하거나 UI 이벤트에 응답하는 데 필요한 HTML 태그와 처리 논리를 포함합니다. 구성 요소는 유연하고 간단합니다. 프로젝트 간에 중첩, 재사용 및 공유될 수 있습니다.

## <a name="component-classes"></a>구성 요소 클래스

구성 요소는 C# 및 HTML 태그 조합을 사용하여 [Razor](xref:mvc/views/razor) 구성 요소 파일( *.razor*)에서 구현됩니다. Blazor의 구성 요소는 공식적으로 *Razor 구성 요소*라고 합니다.

구성 요소의 이름은 대문자로 시작해야 합니다. 예를 들어, *MyCoolComponent.razor*는 유효하고 *myCoolComponent*는 유효하지 않습니다.

구성 요소의 UI는 HTML을 사용하여 정의됩니다. 동적 렌더링 논리(예: 루프, 조건, 식)는 [Razor](xref:mvc/views/razor)라는 포함된 C# 구문을 사용하여 추가됩니다. 앱이 컴파일되면 HTML 태그 및 C# 렌더링 논리는 구성 요소 클래스로 변환됩니다. 생성된 클래스의 이름은 파일 이름과 일치합니다.

구성 요소 클래스의 멤버는 `@code` 블록에서 정의됩니다. `@code` 블록에서 구성 요소 상태(속성, 필드)는 이벤트 처리 또는 다른 구성 요소 논리 정의를 위한 메서드로 지정됩니다. 두 개 이상의 `@code` 블록이 허용됩니다.

구성 요소 멤버는 `@`으로 시작되는 C# 식을 사용하는 구성 요소 렌더링 논리의 일부로 사용할 수 있습니다. 예를 들어, C# 필드는 필드 이름에 앞에 `@`을 붙여 렌더링됩니다. 다음 예제는 다음 작업을 수행합니다.

* `_headingFontStyle`을 평가하고 `font-style`에 대한 CSS 속성 값으로 렌더링합니다.
* `_headingText`를 평가하고 `<h1>` 요소의 내용으로 렌더링합니다.

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

구성 요소가 처음 렌더링되면 구성 요소는 이벤트에 대한 응답으로 렌더링 트리를 다시 생성합니다. 그런 후 Blazor는 새로운 렌더링 트리를 이전 렌터링 트리와 비교하여 수정 사항을 브라우저 DOM(문서 개체 모델)에 적용합니다.

구성 요소는 일반 C# 클래스이며 프로젝트 내의 어느 위치에나 배치할 수 있습니다. 웹 페이지를 생성하는 구성 요소는 일반적으로 *Pages* 폴더에 있습니다. 페이지가 아닌 구성 요소는 *Shared* 폴더 또는 프로젝트에 추가된 사용자 지정 폴더에 자주 배치됩니다.

일반적으로 구성 요소의 네임스페이스는 앱의 루트 네임스페이스와 앱 내의 구성 요소 위치(폴더)에서 파생됩니다. 앱의 루트 네임스페이스가 `BlazorApp`이고 `Counter` 구성 요소가 *Pages* 폴더에 있는 경우 다음이 적용됩니다.

* `Counter` 구성 요소의 네임스페이스는 `BlazorApp.Pages`입니다.
* 구성 요소의 정규화된 형식 이름은 `BlazorApp.Pages.Counter`입니다.

자세한 내용은 [구성 요소 가져오기](#import-components) 섹션을 참조하세요.

사용자 지정 폴더를 사용하려면 사용자 지정 폴더의 네임스페이스를 부모 구성 요소나 앱의 *_Imports.razor* 파일에 추가합니다. 예를 들어, 다음 네임스페이스는 앱의 루트 네임스페이스가 `BlazorApp`일 때 *Components* 폴더의 구성 요소를 사용할 수 있도록 합니다.

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a>정적 자산

Blazor는 프로젝트의 [웹 루트(wwwroot) 폴더](xref:fundamentals/index#web-root) 아래에 정적 자산을 배치하는 ASP.NET Core 앱의 규칙을 따릅니다.

기본 상대 경로(`/`)를 사용하여 정적 자산의 웹 루트를 참조합니다. 다음 예제에서 *logo.png*는 실제로 *{PROJECT ROOT}/wwwroot/images* 폴더에 있습니다.

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor 구성 요소는 물결표-슬래시 표기법(`~/`)을 지원하지 **않습니다**.

앱의 기본 경로를 설정하는 방법에 대한 내용은 <xref:host-and-deploy/blazor/index#app-base-path>를 참조하세요.

## <a name="tag-helpers-arent-supported-in-components"></a>태그 도우미는 구성 요소에서 지원되지 않습니다.

[태그 도우미](xref:mvc/views/tag-helpers/intro)는 Razor 구성 요소( *.razor* 파일)에서 지원되지 않습니다. Blazor에서 태그 도우미와 유사한 기능을 제공하려면 대신, 태그 도우미와 동일한 기능을 포함하는 구성 요소를 만들고 해당 구성 요소를 사용합니다.

## <a name="use-components"></a>구성 요소 사용

구성 요소는 HTML 요소 구문을 사용하여 선언함으로써 다른 구성 요소를 포함할 수 있습니다. 구성 요소 사용을 위한 태그는 태그 이름이 구성 요소 유형인 HTML 태그처럼 보입니다.

*Index.razor*의 다음 태그는 `HeadingComponent` 인스턴스를 렌더링합니다.

```razor
<HeadingComponent />
```

*Components/HeadingComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

구성 요소에 구성 요소 이름과 일치하지 않는 HTML 요소(첫 글자가 대문자임)가 포함되면 요소에 예기치 않은 이름이 있음을 나타내는 경고가 발생합니다. 구성 요소 네임스페이스에 대한 `@using` 지시문을 추가하면 해당 구성 요소를 사용할 수 있게 되므로 경고가 해결됩니다.

## <a name="routing"></a>라우팅

Blazor의 라우팅은 앱에서 액세스 가능한 각 구성 요소에 경로 템플릿을 제공하여 수행됩니다.

`@page` 지시어를 포함하는 Razor 파일이 컴파일되면 생성된 클래스에 경로 템플릿을 지정하는 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>가 제공됩니다. 런타임에 라우터는 `RouteAttribute`를 사용하여 구성 요소 클래스를 검색하고, 요청된 URL과 일치하는 경로 템플릿을 포함하는 구성 요소를 렌더링합니다.

```razor
@page "/ParentComponent"

...
```

자세한 내용은 <xref:blazor/routing>를 참조하세요.

## <a name="parameters"></a>매개 변수

### <a name="route-parameters"></a>경로 매개 변수

구성 요소는 `@page` 지시문에 제공된 경로 템플릿에서 경로 매개 변수를 받을 수 있습니다. 라우터는 경로 매개 변수를 사용하여 해당하는 구성 요소 매개 변수를 채웁니다.

*Pages/RouteParameter.razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

선택적 매개 변수는 지원되지 않으므로 앞의 예제에서 두 개의 `@page` 지시문이 적용됩니다. 첫 번째 지시문은 매개 변수 없이 구성 요소에 대한 탐색을 허용합니다. 두 번째 `@page` 지시문은 `{text}` 경로 매개 변수를 받고 `Text` 속성에 값을 할당합니다.

여러 폴더 경계에서 경로를 캡처하는 *catch-all* 매개 변수 구문(`*`/`**`)은 Razor 구성 요소( *.razor*)에서 지원되지 **않습니다**.

### <a name="component-parameters"></a>구성 요소 매개 변수

구성 요소에는 `[Parameter]` 특성을 사용하여 구성 요소 클래스의 퍼블릭 속성을 사용하여 정의되는 *구성 요소 매개 변수*가 있을 수 있습니다. 특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.

*Components/ChildComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

샘플 앱의 다음 예제에서는 `ParentComponent`는 `ChildComponent`의 `Title` 속성 값을 설정합니다.

*Pages/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a>자식 콘텐츠

구성 요소는 다른 구성 요소의 콘텐츠를 설정할 수 있습니다. 할당 구성 요소는 받는 구성 요소를 지정하는 태그 간 콘텐츠를 제공합니다.

다음 예제에서 `ChildComponent`에는 렌더링할 UI의 세그먼트를 나타내는 `RenderFragment`를 나타내는 `ChildContent` 속성이 있습니다. `ChildContent`의 값은 콘텐츠를 렌더링해야 하는 구성 요소의 태그에 배치됩니다. `ChildContent` 값은 부모 구성 요소에서 수신되고 부트스트랩 패널의 `panel-body` 내에서 렌더링됩니다.

*Components/ChildComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> `RenderFragment` 콘텐츠를 받는 속성은 규칙에 따라 이름 `ChildContent`가 지정되어야 합니다.

샘플 앱의 `ParentComponent`는 콘텐츠를 `<ChildComponent>` 태그 안에 배치하여 `ChildComponent`를 렌더링하기 위한 콘텐츠를 제공할 수 있습니다.

*Pages/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>특성 스플래팅 및 임의 매개 변수

구성 요소는 구성 요소의 선언된 매개 변수 외에, 추가 특성도 캡처하고 렌더링할 수 있습니다. 추가 특성을 사전에 캡처한 다음, [`@attributes`](xref:mvc/views/razor#attributes) Razor 지시어를 사용하여 구성 요소를 렌더링할 때 요소에 *스플래팅*할 수 있습니다. 이 시나리오는 다양한 사용자 지정을 지원하는 태그 요소를 생성하는 구성 요소를 정의할 때 유용합니다. 예를 들어, 많은 매개 변수를 지원하는 `<input>`에 대해 개별적으로 특성을 정의하는 것이 번거로울 수 있습니다.

다음 예제에서 첫 번째 `<input>` 요소(`id="useIndividualParams"`)는 개별 구성 요소 매개 변수를 사용하지만 두 번째 `<input>` 요소(`id="useAttributesDict"`)는 특성 스플래팅을 사용합니다.

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

매개 변수의 형식은 문자열 키를 사용하여 `IEnumerable<KeyValuePair<string, object>>`를 구현해야 합니다. 이 시나리오에서는 `IReadOnlyDictionary<string, object>`를 사용할 수도 있습니다.

두 방법을 사용하여 렌더링되는 `<input>` 요소는 동일합니다.

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

임의 특성을 허용하려면 `CaptureUnmatchedValues` 속성이 `true`로 설정한 상태로 `[Parameter]` 특성을 사용하여 구성 요소 매개 변수를 정의합니다.

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

`[Parameter]`의 `CaptureUnmatchedValues` 속성을 사용하면 해당 매개 변수는 다른 매개 변수와 일치하지 않는 모든 특성을 일치시킬 수 있습니다. 구성 요소는 `CaptureUnmatchedValues`를 사용하여 단일 매개 변수만 정의할 수 있습니다. `CaptureUnmatchedValues`에 사용되는 속성 형식은 문자열 키가 있는 `Dictionary<string, object>`에서 할당할 수 있어야 합니다. 이 시나리오에서는 `IEnumerable<KeyValuePair<string, object>>` 또는 `IReadOnlyDictionary<string, object>`도 옵션입니다.

요소 특성의 위치에 상대적인 `@attributes`의 위치는 중요합니다. 요소에 `@attributes`가 스플래팅되면 오른쪽에서 왼쪽으로(마지막에 도달하면 처음으로) 특성이 처리됩니다. `Child` 구성 요소를 사용하는 구성 요소의 다음 예를 살펴보세요.

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Child` 구성 요소의 `extra` 특성은 `@attributes` 오른쪽으로 설정됩니다. 특성은 오른쪽에서 왼쪽으로(마지막에 도달하면 처음으로) 처리되기 때문에 `Parent` 구성 요소의 렌더링된 `<div>`는 추가 특성을 통해 전달될 때 `extra="5"`를 포함합니다.

```html
<div extra="5" />
```

다음 예에서는 `Child` 구성 요소의 `<div>`에서 `extra`와 `@attributes`의 순서가 반대로 바뀝니다.

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Parent` 구성 요소의 렌더링된 `<div>`에는 추가 특성을 통해 전달될 경우 `extra="10"`을 포함됩니다.

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>구성 요소에 대한 참조 캡처

구성 요소 참조에서는 해당 인스턴스에 대해 명령(예: (`Show` 또는 `Reset`)을 실행할 수 있도록 구성 요소 인스턴스를 참조하는 방법을 제공합니다. 구성 요소 참조를 캡처하려면 다음을 수행합니다.

* 자식 구성 요소에 [`@ref`](xref:mvc/views/razor#ref) 특성을 추가합니다.
* 자식 구성 요소와 동일한 유형으로 필드를 정의합니다.

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

구성 요소가 렌더링되면 `_loginDialog` 필드가 `MyLoginDialog` 자식 구성 요소 인스턴스로 채워집니다. 그런 다음, 구성 요소 인스턴스에서 .NET 메서드를 호출할 수 있습니다.

> [!IMPORTANT]
> `_loginDialog` 변수는 구성 요소가 렌더링된 후에만 채워지고 출력에는 `MyLoginDialog` 요소가 포함됩니다. 이 시점까지 참조할 항목이 없습니다. 구성 요소에서 렌더링을 완료한 후에 구성 요소 참조를 조작하려면 [OnAfterRenderAsync 또는 OnAfterRender 메서드](xref:blazor/lifecycle#after-component-render)를 사용합니다.

구성 요소 참조 캡처에는 [요소 참조 캡처](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)와 유사한 구문을 사용하지만 JavaScript interop 기능이 아닙니다. 구성 요소 참조는 JavaScript 코드에 전달되지 않으며, .NET 코드에서만 사용됩니다.

> [!NOTE]
> 구성 요소 참조를 사용하여 자식 구성 요소의 상태를 변경하지 **않도록 합니다**. 대신, 일반 선언적 매개 변수를 사용하여 자식 구성 요소에 데이터를 전달합니다. 일반 선언적 매개 변수를 사용하면 자식 구성 요소가 올바른 시간에 자동으로 다시 렌더링됩니다.

## <a name="invoke-component-methods-externally-to-update-state"></a>외부에서 구성 요소 메서드를 호출하여 상태 업데이트

Blazor는 `SynchronizationContext`를 사용하여 단일 논리적 실행 스레드를 적용합니다. 구성 요소의 [수명 주기 메서드](xref:blazor/lifecycle) 및 Blazor에서 발생하는 모든 이벤트 콜백은 이 `SynchronizationContext`에서 실행됩니다. 외부 이벤트(예: 타이머 또는 다른 알림)를 기준으로 구성 요소를 업데이트해야 하는 경우 Blazor의 `SynchronizationContext`에 디스패치되는 `InvokeAsync` 메서드를 사용합니다.

예를 들어, 업데이트된 상태를 수신 구성 요소에 알릴 수 있는 *알림 서비스*을 고려해 보세요.

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

`NotifierService`를 싱글톤으로 등록합니다.

* Blazor WebAssembly에서 `Program.Main`에 서비스를 등록합니다.

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Blazor 서버에서 `Startup.ConfigureServices`에 서비스를 등록합니다.

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

`NotifierService`를 사용하여 구성 요소를 업데이트합니다.

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

위의 예제에서 `NotifierService`는 Blazor의 `SynchronizationContext` 외부에서 구성 요소의 `OnNotify` 메서드를 호출합니다. `InvokeAsync`는 올바른 컨텍스트로 전환하고 렌더링을 큐에 대기하는 데 사용됩니다.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>\@ 키를 사용하여 요소 및 구성 요소 유지

요소 또는 구성 요소 목록을 렌더링하고, 이후에 요소 또는 구성 요소가 변경되는 경우 Blazor의 Diff 알고리즘은 유지할 수 있는 이전 요소 또는 구성 요소와 모델 개체가 이러한 요소에 매핑되는 방법을 결정해야 합니다. 일반적으로 이 프로세스는 자동이며 무시해도 되지만 프로세스를 제어하려는 경우가 있습니다.

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

`People` 컬렉션의 콘텐츠는 삽입, 삭제 또는 다시 정렬된 항목으로 변경될 수 있습니다. 구성 요소가 다시 렌더링되면 `<DetailsEditor>` 구성 요소가 다른 `Details` 매개 변수 값을 수신하도록 변경될 수 있습니다. 이로 인해 다시 렌더링이 예상보다 더 복잡해질 수 있습니다. 경우에 따라 다시 렌더링을 수행하면 요소 포커스 손실과 같은 동작 차이가 표시될 수 있습니다.

매핑 프로세스는 `@key` 지시어 특성을 사용하여 제어할 수 있습니다. `@key`를 사용하면 diff 알고리즘은 키의 값에 따라 요소 또는 구성 요소가 유지되도록 합니다.

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People` 컬렉션이 변경되면 diff 알고리즘은 `<DetailsEditor>` 인스턴스와 `person` 인스턴스 간 연결을 유지합니다.

* `People` 목록에서 `Person`이 삭제된 경우 해당 `<DetailsEditor>` 인스턴스만 UI에서 제거됩니다. 다른 인스턴스는 변경되지 않은 상태로 유지됩니다.
* `Person`이 목록의 특정 위치에 삽입되는 경우 해당 위치에 하나의 새 `<DetailsEditor>` 인스턴스가 삽입됩니다. 다른 인스턴스는 변경되지 않은 상태로 유지됩니다.
* `Person` 항목이 다시 정렬되면 해당 `<DetailsEditor>` 인스턴스가 유지되고 UI에서 다시 정렬됩니다.

일부 시나리오에서는 `@key`를 사용하여 재랜더링의 복잡성을 최소화하고, DOM의 상태 저장 부분(예: 포커스 위치)이 변경될 수 있는 잠재적 문제를 방지할 수 있습니다.

> [!IMPORTANT]
> 키는 각 컨테이너 요소 또는 구성 요소에 대해 로컬입니다. 문서 전체에서 키가 전역적으로 비교되지 않습니다.

### <a name="when-to-use-key"></a>\@ 키를 사용하는 경우

일반적으로 목록이 렌더링될 때마다(예: `@foreach` 블록에서) `@key`를 사용하는 것이 적절하며 `@key`을 정의하기 위한 적절한 값이 있습니다.

`@key`를 사용하여 개체가 변경될 때 Blazor가 요소 또는 구성 요소 하위 트리를 유지하지 않도록 할 수도 있습니다.

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

`@currentPerson`이 변경되면 `@key` 특성 지시문은 Blazor가 강제로 전체 `<div>` 및 해당 하위 항목을 삭제하고 새 요소와 구성 요소를 사용하여 UI 내에서 하위 트리를 다시 빌드하도록 합니다. 이것은 `@currentPerson`이 변경될 때 UI 상태가 유지되지 않도록 해야 하는 경우에 유용할 수 있습니다.

### <a name="when-not-to-use-key"></a>\@ 키를 사용하지 않는 경우

`@key`로 diff를 수행할 때 성능 비용이 발생합니다. 성능 비용은 크지 않지만 요소 또는 구성 요소 유지 규칙을 제어할 때 앱에 도움이 되는 경우에만 `@key`를 지정합니다.

`@key`가 사용되지 않더라도 Blazor는 자식 요소와 구성 요소 인스턴스를 최대한 많이 보존합니다. `@key`를 사용할 때의 유일한 장점은 매핑을 선택하는 diff 알고리즘 대신, 모델 인스턴스가 유지된 구성 요소 인스턴스에 매핑되는 *방법*을 제어할 수 있다는 것입니다.

### <a name="what-values-to-use-for-key"></a>\@ 키에 사용할 값

일반적으로 `@key`에 대해 다음과 같은 종류의 값 중 하나를 제공하는 것이 좋습니다.

* 모델 개체 인스턴스(예: 이전 예제와 같은 `Person` 인스턴스) 이렇게 하면 개체 참조 같음에 따라 보존이 이루어집니다.
* 고유 식별자(예: `int`, `string` 또는 `Guid` 형식의 기본 키 값)

`@key`에 사용되는 값이 충돌하지 않는지 확인합니다. 동일한 부모 요소 내에서 충돌하는 값이 감지되면 이전 요소나 구성 요소를 새 요소나 구성 요소에 확정적으로 매핑할 수 없으므로 Blazor는 예외를 throw합니다. 개체 인스턴스 또는 기본 키 값과 같은 고유 값만 사용합니다.

## <a name="partial-class-support"></a>Partial 클래스 지원

이제 Razor 구성 요소가 partial 클래스로 생성됩니다. Razor 구성 요소는 다음 방법 중 하나를 사용하여 작성됩니다.

* C# 코드는 단일 파일에서 HTML 태그와 Razor 코드를 사용하여 [`@code`](xref:mvc/views/razor#code) 블록에 정의됩니다. Blazor 템플릿은 이 접근 방식을 사용하여 Razor 구성 요소를 정의합니다.
* C# 코드는 partial 클래스로 정의된 코드 숨김 파일에 배치됩니다.

다음 예제에서는 Blazor 템플릿에서 생성된 앱에서 `@code` 블록을 포함하는 기본 `Counter` 구성 요소를 보여 줍니다. HTML 태그, Razor 코드 및 C# 코드는 다음과 같은 동일한 파일에 있습니다.

*Counter.razor*:

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

Partial 클래스에서 코드 숨김 파일을 사용하여 `Counter` 구성 요소를 만들 수도 있습니다.

*Counter.razor*:

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

필요한 경우 partial 클래스 파일에 필요한 네임스페이스를 추가합니다. Razor 구성 요소에 사용되는 일반적인 네임스페이스는 다음과 같습니다.

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>기본 클래스 지정

[`@inherits`](xref:mvc/views/razor#inherits) 지시어를 사용하여 구성 요소에 대한 기본 클래스를 지정할 수 있습니다. 다음 예제에서는 구성 요소가 기본 클래스 `BlazorRocksBase`를 상속하여 구성 요소의 속성과 메서드를 제공하는 방법을 보여 줍니다. 기본 클래스는 `ComponentBase`에서 파생되어야 합니다.

*Pages/BlazorRocks.razor*:

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

[`@attribute`](xref:mvc/views/razor#attribute) 지시어를 사용하여 Razor 구성 요소에 특성을 지정할 수 있습니다. 다음 예제는 `[Authorize]` 특성을 구성 요소 클래스에 적용합니다.

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>구성 요소 가져오기

Razor로 작성된 구성 요소의 네임스페이스는 다음을 기준으로 합니다(우선 순위에 따라).

* Razor 파일( *.razor*) 태그(`@namespace BlazorSample.MyNamespace`)의 [`@namespace`](xref:mvc/views/razor#namespace) 지정
* 프로젝트 파일(`<RootNamespace>BlazorSample</RootNamespace>`)에서 프로젝트의 `RootNamespace`
* 프로젝트 파일의 파일 이름( *.csproj*)에서 가져온 프로젝트 이름, 프로젝트 루트에서 구성 요소로의 경로. 예를 들어, 이 프레임워크는 *{PROJECT ROOT}/Pages/Index.razor*(*BlazorSample.csproj*)를 네임스페이스 `BlazorSample.Pages`로 확인합니다. 구성 요소는 C# 이름 바인딩 규칙을 따릅니다. 이 예의 `Index` 구성 요소에서는 범위의 구성 요소가 모든 구성 요소입니다.
  * 동일한 폴더의 *Pages*.
  * 다른 네임스페이스를 명시적으로 지정하지 않는 프로젝트 루트의 구성 요소

다른 네임스페이스에 정의된 구성 요소는 Razor의 [`@using`](xref:mvc/views/razor#using) 지시문을 사용하여 범위로 가져옵니다.

다른 구성 요소에서 `NavMenu.razor`가 *BlazorSample/Shared/* 폴더에 있는 경우 다음 `@using` 문을 사용하여 `Index.razor`에서 구성 요소를 사용할 수 있습니다.

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

구성 요소는 정규화된 이름을 사용하여 참조할 수도 있습니다. 이 경우에는 [`@using`](xref:mvc/views/razor#using) 지시문이 필요하지 않습니다.

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> `global::` 한정자는 지원되지 않습니다.
>
> 별칭이 지정된 `using` 문(예: `@using Foo = Bar`)을 사용하여 구성 요소를 가져오는 것은 지원되지 않습니다.
>
> 부분적으로 정규화된 이름은 지원되지 않습니다. 예를 들어, `<Shared.NavMenu></Shared.NavMenu>`를 사용하여 `@using BlazorSample`을 추가하고 `NavMenu.razor`를 참조하는 것은 지원되지 않습니다.

## <a name="conditional-html-element-attributes"></a>조건부 HTML 요소 특성

HTML 요소 특성은 .NET 값에 따라 조건부로 렌더링됩니다. 값이 `false` 또는 `null`이면 특성이 렌더링되지 않습니다. 값이 `true`이면 특성이 최소화된 상태로 렌더링됩니다.

다음 예제에서 `IsCompleted`는 `checked`가 요소의 태그에서 렌더링되는지를 확인합니다.

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

`IsCompleted`가 `true`이면 확인란이 다음과 같이 렌더링됩니다.

```html
<input type="checkbox" checked />
```

`IsCompleted`가 `false`이면 확인란이 다음과 같이 렌더링됩니다.

```html
<input type="checkbox" />
```

자세한 내용은 <xref:mvc/views/razor>를 참조하세요.

> [!WARNING]
> [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)와 같은 일부 HTML 특성은 .NET 형식이 `bool`일 경우 제대로 작동하지 않습니다. 이러한 경우 `bool` 대신 `string` 형식을 사용합니다.

## <a name="raw-html"></a>원시 HTML

일반적으로 문자열은 DOM 텍스트 노드를 사용하여 렌더링됩니다. 즉, 포함될 수 있는 모든 태그는 무시되고 리터럴 텍스트로 처리됩니다. 원시 HTML을 렌더링하려면 HTML 콘텐츠를 `MarkupString` 값으로 래핑합니다. 값은 HTML 또는 SVG로 구문 분석되고 DOM에 삽입됩니다.

> [!WARNING]
> 신뢰할 수 없는 원본에서 생성된 원시 HTML을 렌더링할 경우 **보안 위험**이 있으므로 피해야 합니다.

다음 예제에서는 `MarkupString` 형식을 사용하여 정적 HTML 콘텐츠 블록을 구성 요소의 렌더링된 출력에 추가하는 방법을 보여 줍니다.

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>연계 값 및 매개 변수

일부 시나리오에서, 특히 여러 구성 요소 계층이 있는 경우 [구성 요소 매개 변수](#component-parameters)를 사용하여 상위 구성 요소에서 하위 구성 요소로 데이터를 이동하는 것이 불편할 수 있습니다. 연계 값 및 매개 변수는 상위 구성 요소에서 모든 하위 구성 요소에 값을 제공하는 편리한 방법을 제공하여 이 문제를 해결합니다. 연계 값 및 매개 변수를 사용하여 구성 요소를 조정할 수도 있습니다.

### <a name="theme-example"></a>테마 예

샘플 앱의 다음 예제에서 `ThemeInfo` 클래스는 앱의 지정된 부분 내에 있는 모든 단추가 동일한 스타일을 공유할 수 있도록 구성 요소 계층 구조의 아래로 테마 정보가 흐르도록 지정합니다.

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

상위 구성 요소는 연계 값 구성 요소를 사용하여 연계 값을 제공할 수 있습니다. `CascadingValue` 구성 요소는 구성 요소 계층의 하위 트리를 래핑하고 해당 하위 트리 내의 모든 구성 요소에 단일 값을 제공합니다.

예를 들어, 샘플 앱은 테마 정보(`ThemeInfo`)를 앱 레이아웃 중 하나에 `@Body` 속성의 레이아웃 본문을 구성하는 모든 구성 요소에 대한 연계 매개 변수로 지정합니다. 레이아웃 구성 요소에서 `ButtonClass`에는 `btn-success` 값이 할당됩니다. 모든 하위 구성 요소는 `ThemeInfo` 연계 개체를 통해 이 속성을 사용할 수 있습니다.

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

연계 값을 사용하기 위해 구성 요소는 `[CascadingParameter]` 특성을 사용하여 연계 매개 변수를 선언합니다. 연계 값은 형식별로 연계 매개 변수에 바인딩됩니다.

샘플 앱에서 `CascadingValuesParametersTheme` 구성 요소는 `ThemeInfo` 연계 값을 연계 매개 변수에 바인딩합니다. 이 매개 변수는 구성 요소에 의해 표시되는 단추 중 하나에 대해 CSS 클래스를 설정하는 데 사용됩니다.

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

동일한 형식의 여러 값을 동일한 하위 트리 내에서 연계하려면 각 `CascadingValue` 구성 요소와 해당 `CascadingParameter`에 고유한 `Name` 문자열을 제공합니다. 다음 예제에서는 두 개의 `CascadingValue` 구성 요소가 이름별로 다른 `MyCascadingType` 인스턴스를 연계합니다.

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

하위 구성 요소에서 연계 매개 변수는 상위 구성 요소의 해당 연계 값에서 해당 값을 이름별로 수신합니다.

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

연계 매개 변수를 사용하면 여러 구성 요소가 구성 요소 계층 구조 전체에서 공동으로 작용할 수 있습니다. 예를 들어, 샘플 앱에서 다음 *TabSet* 예제를 고려합니다.

샘플 앱에는 탭이 구현하는 `ITab` 인터페이스가 있습니다.

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet` 구성 요소는 여러 `Tab` 구성 요소를 포함하는 `TabSet` 구성 요소를 사용합니다.

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

자식 `Tab` 구성 요소는 `TabSet`에 대한 매개 변수로 명시적으로 전달되지 않습니다. 대신, 자식 `Tab` 구성 요소는 `TabSet`의 자식 콘텐츠에 속합니다. 그러나 `TabSet`은 헤더 및 활성 탭을 렌더링할 수 있도록 각 `Tab` 구성 요소에 대한 정보를 계속 알고 있어야 합니다. 추가 코드를 요구하지 않고 이러한 조정을 사용하도록 설정하기 위해 `TabSet` 구성 요소는  연계 값으로 제공된 다음, 하위 `Tab` 구성 요소에서 선택될 수 있습니다.

`TabSet` 구성 요소:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

하위 `Tab` 구성 요소는 포함하는 `TabSet`을 연계 매개 변수로 캡처하므로 `Tab` 구성 요소는 `TabSet`에 추가되고 활성 상태인 탭을 조정합니다.

`Tab` 구성 요소:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor 템플릿

렌더링 조각은 Razor 템플릿 구문을 사용하여 정의할 수 있습니다. Razor 템플릿은 UI 코드 조각을 정의하는 방법으로, 다음 형식을 가정합니다.

```razor
@<{HTML tag}>...</{HTML tag}>
```

다음 예제에서는 `RenderFragment` 및 `RenderFragment<T>` 값을 지정하고 구성 요소에서 직접 템플릿을 렌더링하는 방법을 보여 줍니다. 렌더링 조각은 [템플릿 구성 요소](xref:blazor/templated-components)에 인수로 전달될 수도 있습니다.

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

이전 코드의 렌더링된 출력:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>SVG(Scalable Vector Graphics) 이미지

Blazor는 HTML을 렌더링하므로 SVG(Scalable Vector Graphics) 이미지( *.svg*)를 포함하는 브라우저 지원 이미지는 `<img>` 태그를 통해 지원됩니다.

```html
<img alt="Example image" src="some-image.svg" />
```

마찬가지로, 스타일시트 파일( *.css*)의 CSS 규칙에서는 SVG 이미지가 지원됩니다.

```css
.my-element {
    background-image: url("some-image.svg");
}
```

그러나 인라인 SVG 태그는 일부 시나리오에서 지원되지 않습니다. `<svg>` 태그를 구성 요소 파일(*razor*)에 직접 배치하면 기본 이미지 렌더링이 지원되지만 많은 고급 시나리오는 아직 지원되지 않습니다. 예를 들어, `<use>` 태그는 현재 적용되지 않으며 `@bind`를 일부 SVG 태그에서는 사용할 수 없습니다. 향후 릴리스에서 이러한 제한을 해결할 예정입니다.

## <a name="additional-resources"></a>추가 자료

* <xref:security/blazor/server> &ndash; 리소스를 소모하지 않도록 하는 Blazor 서버 앱을 빌드하는 방법에 대한 지침을 포함합니다.
