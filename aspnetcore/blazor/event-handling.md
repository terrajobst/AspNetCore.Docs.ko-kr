---
title: ASP.NET Core Blazor 이벤트 처리
author: guardrex
description: 이벤트 인수 형식, 이벤트 콜백 및 기본 브라우저 이벤트 관리를 비롯 한 Blazor이벤트 처리 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 25844ef39aee849072d16f3d73eda0a1c20ee788
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453165"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Core Blazor 이벤트 처리

By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

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

## <a name="event-argument-types"></a>이벤트 인수 형식

일부 이벤트의 경우 이벤트 인수 형식이 허용 됩니다. 메서드 호출에서 이벤트 형식을 지정 하는 것은 이벤트 형식이 메서드에서 사용 되는 경우에만 필요 합니다.

지원 되는 `EventArgs` 다음 표에 나와 있습니다.

| 행사            | 클래스                | DOM 이벤트 및 참고 사항 |
| ---------------- | -------------------- | -------------------- |
| 클립보드        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| 옵니다             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>끌어온 항목 데이터를 포함 하는 `DataTransfer` 및 `DataTransferItem`. |
| Error            | `ErrorEventArgs`     | `onerror` |
| 행사            | `EventArgs`          | *일반*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*클립보드*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Input*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*미디어*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Focus            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>`relatedTarget`에 대 한 지원을 포함 하지 않습니다. |
| 입력            | `ChangeEventArgs`    | `onchange`, `oninput` |
| 키보드         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| 마우스            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| 마우스 포인터    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| 마우스 휠      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| 진행         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| 터치            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint` 터치를 구분 하는 장치에서 단일 접촉 지점을 나타냅니다. |

자세한 내용은 다음 리소스를 참조하세요.

* [ASP.NET Core 참조 원본의 EventArgs 클래스 (dotnet/aspnetcore release/3.1 분기)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)
* [MDN 웹 문서: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; 각 DOM 이벤트를 지 원하는 HTML 요소에 대 한 정보를 포함 합니다.

## <a name="lambda-expressions"></a>람다 식

[람다 식을](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) 사용할 수도 있습니다.

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

## <a name="eventcallback"></a>EventCallback

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

## <a name="prevent-default-actions"></a>기본 동작 방지

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

## <a name="stop-event-propagation"></a>이벤트 전파 중지

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
