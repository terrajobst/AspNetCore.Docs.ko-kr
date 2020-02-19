---
title: ASP.NET Core Razor 구성 요소 만들기 및 사용
author: guardrex
description: 데이터에 바인딩하고, 이벤트를 처리 하 고, 구성 요소 수명 주기를 관리 하는 방법을 비롯 하 여 Razor 구성 요소를 만들고 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: f9b4eab29fafe8113528062f57d28dadd0f57577
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447102"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>ASP.NET Core Razor 구성 요소 만들기 및 사용

By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

Blazor 앱은 *구성 요소*를 사용 하 여 빌드됩니다. 구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI (사용자 인터페이스)의 자체 포함 청크입니다. 구성 요소는 데이터를 삽입 하거나 UI 이벤트에 응답 하는 데 필요한 HTML 태그와 처리 논리를 포함 합니다. 구성 요소는 유연 하 고 간단 합니다. 프로젝트 간에 중첩, 재사용 및 공유 될 수 있습니다.

## <a name="component-classes"></a>구성 요소 클래스

구성 요소는 및 HTML 태그의 C# 조합을 사용 하 여 [razor](xref:mvc/views/razor) 구성 요소 파일 (*razor*)에서 구현 됩니다. Blazor의 구성 요소는 *Razor 구성 요소*라고 합니다.

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

구성 요소가 처음 렌더링 되 면 구성 요소는 이벤트에 대 한 응답으로 렌더링 트리를 다시 생성 합니다. 그런 다음 Blazor 새 렌더링 트리를 이전 렌더링 트리와 비교 하 여 브라우저의 문서 개체 모델 (DOM)에 수정 사항을 적용 합니다.

구성 요소는 C# 일반 클래스 이며 프로젝트 내의 어느 위치에 나 배치할 수 있습니다. 웹 페이지를 생성 하는 구성 요소는 일반적으로 *Pages* 폴더에 있습니다. 페이지를 지정 하지 않는 구성 요소는 일반적으로 *공유* 폴더 또는 프로젝트에 추가 된 사용자 지정 폴더에 배치 됩니다.

일반적으로 구성 요소의 네임 스페이스는 앱의 루트 네임 스페이스와 앱 내의 구성 요소 위치 (폴더)에서 파생 됩니다. 응용 프로그램의 루트 네임 스페이스가 `BlazorApp` 고 `Counter` 구성 요소가 *Pages* 폴더에 있는 경우:

* `Counter` 구성 요소의 네임 스페이스가 `BlazorApp.Pages`되었습니다.
* 구성 요소의 정규화 된 형식 이름이 `BlazorApp.Pages.Counter`입니다.

자세한 내용은 [구성 요소 가져오기](#import-components) 섹션을 참조 하세요.

사용자 지정 폴더를 사용 하려면 부모 구성 요소나 앱의 *_Imports razor* 파일에 사용자 지정 폴더의 네임 스페이스를 추가 합니다. 예를 들어 다음 네임 스페이스는 응용 프로그램의 루트 네임 스페이스가 `BlazorApp`때 *구성 요소* 폴더의 구성 요소를 사용할 수 있도록 합니다.

```razor
@using BlazorApp.Components
```

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

구성 요소에 구성 요소 이름과 일치 하지 않는 대문자 첫 글자가 포함 된 HTML 요소가 포함 된 경우 요소에 예기치 않은 이름이 있음을 나타내는 경고가 내보내집니다. 구성 요소 네임 스페이스에 대 한 `@using` 지시문을 추가 하면 해당 구성 요소를 사용할 수 있게 되므로 경고가 해결 됩니다.

## <a name="routing"></a>라우팅

Blazor 라우팅은 앱에서 액세스 가능한 각 구성 요소에 경로 템플릿을 제공 하 여 수행 됩니다.

`@page` 지시어를 사용 하 여 Razor 파일이 컴파일되면 생성 된 클래스에 경로 템플릿을 지정 하는 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 제공 됩니다. 런타임에 라우터는 `RouteAttribute`를 사용 하 여 구성 요소 클래스를 검색 하 고 요청 된 URL과 일치 하는 경로 템플릿을 포함 하는 구성 요소를 렌더링 합니다.

```razor
@page "/ParentComponent"

...
```

자세한 내용은 <xref:blazor/routing>을 참조하세요.

## <a name="parameters"></a>매개 변수

### <a name="route-parameters"></a>경로 매개 변수

구성 요소는 `@page` 지시문에 제공 된 경로 템플릿에서 경로 매개 변수를 받을 수 있습니다. 라우터는 경로 매개 변수를 사용 하 여 해당 하는 구성 요소 매개 변수를 채웁니다.

*Pages/RouteParameter*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

선택적 매개 변수는 지원 되지 않으므로 앞의 예제에서 두 개의 `@page` 지시문이 적용 됩니다. 첫 번째는 매개 변수 없이 구성 요소에 대 한 탐색을 허용 합니다. 두 번째 `@page` 지시어는 `{text}` route 매개 변수를 받고 `Text` 속성에 값을 할당 합니다.

*Catch-* 여러 폴더 경계에서 경로를 캡처하는 모든 매개 변수 구문 (`*`/`**`)은 razor 구성 요소 (*razor*)에서 지원 **되지 않습니다** .

### <a name="component-parameters"></a>구성 요소 매개 변수

구성 요소는 구성 요소 클래스의 공용 속성을 사용 하 여 정의 되는 *구성 요소 매개 변수*를 포함할 수 있습니다 .이 매개 변수는 `[Parameter]` 특성 특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.

*Components/ChildComponent. razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

샘플 앱의 다음 예제에서는 `ParentComponent` `ChildComponent`의 `Title` 속성 값을 설정 합니다.

*Pages/ParentComponent. razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a>자식 콘텐츠

구성 요소는 다른 구성 요소의 콘텐츠를 설정할 수 있습니다. 할당 구성 요소는 받는 구성 요소를 지정 하는 태그 사이에 콘텐츠를 제공 합니다.

다음 예제에서 `ChildComponent`에는 렌더링할 UI 세그먼트를 나타내는 `RenderFragment`를 나타내는 `ChildContent` 속성이 있습니다. `ChildContent`의 값은 콘텐츠를 렌더링 해야 하는 구성 요소의 태그에 배치 됩니다. `ChildContent` 값은 부모 구성 요소에서 수신 되 고 부트스트랩 패널의 `panel-body`내에서 렌더링 됩니다.

*Components/ChildComponent. razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> `RenderFragment` 콘텐츠를 받는 속성은 규칙에 따라 `ChildContent` 이름이 지정 되어야 합니다.

샘플 앱의 `ParentComponent`는 콘텐츠를 `<ChildComponent>` 태그 안에 배치 하 여 `ChildComponent`를 렌더링 하기 위한 콘텐츠를 제공할 수 있습니다.

*Pages/ParentComponent. razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

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

Blazor는 `SynchronizationContext`를 사용 하 여 단일 논리적 실행 스레드를 적용 합니다. 구성 요소의 [수명 주기 메서드](xref:blazor/lifecycle) 및 Blazor에 의해 발생 하는 모든 이벤트 콜백이이 `SynchronizationContext`실행 됩니다. 외부 이벤트 (예: 타이머 또는 다른 알림)를 기반으로 구성 요소를 업데이트 해야 하는 경우 Blazor의 `SynchronizationContext`에 디스패치할 `InvokeAsync` 메서드를 사용 합니다.

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

* Blazor Weasembmbambmbsembmba에서 서비스를 `Program.Main`에 등록 합니다.

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

요소 또는 구성 요소 목록을 렌더링할 때 이후에 요소 또는 구성 요소가 변경 되는 경우 Blazor의 diff 알고리즘은 유지할 수 있는 이전 요소 또는 구성 요소와 모델 개체가 이러한 요소에 매핑되는 방법을 결정 해야 합니다. 일반적으로이 프로세스는 자동 이며 무시 해도 되지만 프로세스를 제어 하는 경우가 있습니다.

다음과 같은 예제를 참조하세요.

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

`@key`를 사용 하 여 개체가 변경 될 때 요소가 요소 또는 구성 요소 하위 트리를 유지 Blazor 하지 않도록 할 수도 있습니다.

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

`@currentPerson` 변경 하는 경우 `@key` 특성 지시문은 Blazor 강제로 전체 `<div>` 및 해당 하위 항목을 삭제 하 고 새 요소와 구성 요소를 사용 하 여 UI 내에서 하위 트리를 다시 빌드합니다. 이는 `@currentPerson` 변경 될 때 UI 상태가 유지 되지 않도록 해야 하는 경우에 유용할 수 있습니다.

### <a name="when-not-to-use-key"></a>\@키를 사용 하지 않는 경우

`@key`으로 diff 때 성능 비용이 발생 합니다. 성능 비용은 크지 않지만 요소 또는 구성 요소 유지 규칙을 제어 하면 앱이 혜택을 받을 수 있는 경우에만 `@key`을 지정 합니다.

`@key` 사용 되지 않더라도 Blazor는 자식 요소와 구성 요소 인스턴스를 최대한 많이 보존 합니다. `@key`를 사용 하는 유일한 장점은 매핑을 선택 하는 diff 알고리즘 대신 모델 인스턴스가 유지 된 구성 요소 인스턴스에 매핑되 *는 방식을* 제어 하는 것입니다.

### <a name="what-values-to-use-for-key"></a>\@키에 사용할 값

일반적으로 `@key`에 대해 다음과 같은 종류의 값 중 하나를 제공 하는 것이 좋습니다.

* 모델 개체 인스턴스 (예: 이전 예제와 같은 `Person` 인스턴스) 이렇게 하면 개체 참조 일치를 기반으로 유지 됩니다.
* 고유 식별자 (예: `int`, `string`, `Guid`)의 기본 키 값입니다.

`@key`에 사용 되는 값이 충돌 하지 않는지 확인 합니다. 동일한 부모 요소 내에서 충돌 방지 값이 검색 되는 경우 기존 요소나 구성 요소를 새 요소나 구성 요소에 명확 하 게 매핑할 수 없기 때문에 Blazor 예외를 throw 합니다. 개체 인스턴스 또는 기본 키 값과 같은 고유 값만 사용 합니다.

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

다음 예제에서는 `RenderFragment` 및 `RenderFragment<T>` 값을 지정 하 고 구성 요소에서 직접 템플릿을 렌더링 하는 방법을 보여 줍니다. 렌더링 조각은 [템플릿 기반 구성 요소](xref:blazor/templated-components)에 인수로 전달 될 수도 있습니다.

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

## <a name="additional-resources"></a>추가 리소스

* <xref:security/blazor/server> &ndash;에는 리소스 고갈에 경합 해야 하는 Blazor Server 앱 빌드에 대 한 지침이 포함 되어 있습니다.
