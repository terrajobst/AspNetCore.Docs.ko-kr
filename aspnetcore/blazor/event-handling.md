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
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="490ab-103">ASP.NET Core Blazor 이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="490ab-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="490ab-104">By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="490ab-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="490ab-105">Razor 구성 요소는 이벤트 처리 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-105">Razor components provide event handling features.</span></span> <span data-ttu-id="490ab-106">대리자 형식 값을 사용 하는 `on{EVENT}` (예: `onclick` 및 `onsubmit`) 이라는 HTML 요소 특성의 경우 Razor 구성 요소는 특성의 값을 이벤트 처리기로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-106">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="490ab-107">특성의 이름은 항상 [`@on{EVENT}`](xref:mvc/views/razor#onevent)서식 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-107">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="490ab-108">다음 코드는 UI에서 단추가 선택 될 때 `UpdateHeading` 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-108">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="490ab-109">다음 코드는 UI에서 확인란이 변경 될 때 `CheckChanged` 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-109">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="490ab-110">이벤트 처리기는 비동기 일 수도 있고 <xref:System.Threading.Tasks.Task>반환 될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-110">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="490ab-111">[Statehaschanged](xref:blazor/lifecycle#state-changes)를 수동으로 호출할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-111">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="490ab-112">예외가 발생 하면 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-112">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="490ab-113">다음 예제에서는 단추를 선택 하면 `UpdateHeading`를 비동기적으로 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-113">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="490ab-114">이벤트 인수 형식</span><span class="sxs-lookup"><span data-stu-id="490ab-114">Event argument types</span></span>

<span data-ttu-id="490ab-115">일부 이벤트의 경우 이벤트 인수 형식이 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-115">For some events, event argument types are permitted.</span></span> <span data-ttu-id="490ab-116">메서드 호출에서 이벤트 형식을 지정 하는 것은 이벤트 형식이 메서드에서 사용 되는 경우에만 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-116">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="490ab-117">지원 되는 `EventArgs` 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-117">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="490ab-118">행사</span><span class="sxs-lookup"><span data-stu-id="490ab-118">Event</span></span>            | <span data-ttu-id="490ab-119">클래스</span><span class="sxs-lookup"><span data-stu-id="490ab-119">Class</span></span>                | <span data-ttu-id="490ab-120">DOM 이벤트 및 참고 사항</span><span class="sxs-lookup"><span data-stu-id="490ab-120">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="490ab-121">클립보드</span><span class="sxs-lookup"><span data-stu-id="490ab-121">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="490ab-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="490ab-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="490ab-123">옵니다</span><span class="sxs-lookup"><span data-stu-id="490ab-123">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="490ab-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="490ab-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="490ab-125">끌어온 항목 데이터를 포함 하는 `DataTransfer` 및 `DataTransferItem`.</span><span class="sxs-lookup"><span data-stu-id="490ab-125">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="490ab-126">Error</span><span class="sxs-lookup"><span data-stu-id="490ab-126">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="490ab-127">행사</span><span class="sxs-lookup"><span data-stu-id="490ab-127">Event</span></span>            | `EventArgs`          | <span data-ttu-id="490ab-128">*일반*</span><span class="sxs-lookup"><span data-stu-id="490ab-128">*General*</span></span><br><span data-ttu-id="490ab-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="490ab-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="490ab-130">*클립보드*</span><span class="sxs-lookup"><span data-stu-id="490ab-130">*Clipboard*</span></span><br><span data-ttu-id="490ab-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="490ab-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="490ab-132">*Input*</span><span class="sxs-lookup"><span data-stu-id="490ab-132">*Input*</span></span><br><span data-ttu-id="490ab-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="490ab-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="490ab-134">*미디어*</span><span class="sxs-lookup"><span data-stu-id="490ab-134">*Media*</span></span><br><span data-ttu-id="490ab-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="490ab-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="490ab-136">Focus</span><span class="sxs-lookup"><span data-stu-id="490ab-136">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="490ab-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="490ab-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="490ab-138">`relatedTarget`에 대 한 지원을 포함 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="490ab-139">입력</span><span class="sxs-lookup"><span data-stu-id="490ab-139">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="490ab-140">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="490ab-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="490ab-141">키보드</span><span class="sxs-lookup"><span data-stu-id="490ab-141">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="490ab-142">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="490ab-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="490ab-143">마우스</span><span class="sxs-lookup"><span data-stu-id="490ab-143">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="490ab-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="490ab-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="490ab-145">마우스 포인터</span><span class="sxs-lookup"><span data-stu-id="490ab-145">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="490ab-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="490ab-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="490ab-147">마우스 휠</span><span class="sxs-lookup"><span data-stu-id="490ab-147">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="490ab-148">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="490ab-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="490ab-149">진행</span><span class="sxs-lookup"><span data-stu-id="490ab-149">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="490ab-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="490ab-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="490ab-151">터치</span><span class="sxs-lookup"><span data-stu-id="490ab-151">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="490ab-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="490ab-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="490ab-153">`TouchPoint` 터치를 구분 하는 장치에서 단일 접촉 지점을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-153">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="490ab-154">자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="490ab-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="490ab-155">[ASP.NET Core 참조 원본의 EventArgs 클래스 (dotnet/aspnetcore release/3.1 분기)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)</span><span class="sxs-lookup"><span data-stu-id="490ab-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="490ab-156">[MDN 웹 문서: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; 각 DOM 이벤트를 지 원하는 HTML 요소에 대 한 정보를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="490ab-157">람다 식</span><span class="sxs-lookup"><span data-stu-id="490ab-157">Lambda expressions</span></span>

<span data-ttu-id="490ab-158">[람다 식을](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="490ab-159">요소 집합을 반복 하는 경우와 같이 추가 값을 닫는 것이 편리한 경우가 종종 있습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="490ab-160">다음 예제에서는 UI에서 선택 된 경우 각각 이벤트 인수 (`MouseEventArgs`) 및 해당 단추 번호 (`buttonNumber`)를 전달 하 `UpdateHeading`를 호출 하는 세 개의 단추를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="490ab-161">람다 식에서 직접 루프 변수 (`i`)를 `for` 루프에서 사용 **하지** 마십시오.</span><span class="sxs-lookup"><span data-stu-id="490ab-161">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="490ab-162">그렇지 않으면 모든 람다 식에서 같은 변수를 사용 하 여 `i`값을 모든 람다에서 동일 하 게 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-162">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="490ab-163">항상 지역 변수 (이전 예제에서는`buttonNumber`)에서 해당 값을 캡처한 다음 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-163">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="490ab-164">EventCallback</span><span class="sxs-lookup"><span data-stu-id="490ab-164">EventCallback</span></span>

<span data-ttu-id="490ab-165">중첩 된 구성 요소를 사용 하는 일반적인 시나리오는 자식 구성 요소 이벤트가 발생할 때 부모 구성 요소의 메서드를 실행 하는 것입니다. 예를 들어, `onclick` 이벤트가 자식에서 발생할 때&mdash;합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-165">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="490ab-166">구성 요소 간에 이벤트를 노출 하려면 `EventCallback`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-166">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="490ab-167">부모 구성 요소는 자식 구성 요소의 `EventCallback`에 콜백 메서드를 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-167">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="490ab-168">샘플 앱 (component */ChildComponent. razor*)의 `ChildComponent`은 단추의 `onclick` 처리기가 샘플의 `ParentComponent`에서 `EventCallback` 대리자를 수신 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-168">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="490ab-169">`EventCallback`는 주변 장치의 `onclick` 이벤트에 적절 한 `MouseEventArgs`으로 입력 됩니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-169">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="490ab-170">`ParentComponent`은 자식의 `EventCallback<T>` (`OnClickCallback`)를 `ShowMessage` 메서드로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-170">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="490ab-171">*Pages/ParentComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="490ab-171">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="490ab-172">`ChildComponent`에서 단추가 선택 된 경우:</span><span class="sxs-lookup"><span data-stu-id="490ab-172">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="490ab-173">`ParentComponent`의 `ShowMessage` 메서드가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-173">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="490ab-174">`_messageText` 업데이트 되 고 `ParentComponent`에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-174">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="490ab-175">[Statehaschanged](xref:blazor/lifecycle#state-changes) 호출은 콜백의 메서드 (`ShowMessage`)에서 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-175">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="490ab-176">자식 이벤트가 자식 내에서 실행 되는 이벤트 처리기의 rerendering 구성 요소를 트리거하는 것 처럼 `StateHasChanged`는 자동으로 호출 되어 `ParentComponent`을 rerender 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-176">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="490ab-177">`EventCallback` 및 `EventCallback<T>`는 비동기 대리자를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-177">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="490ab-178">`EventCallback<T>`은 강력한 형식이 며 특정 인수 형식이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-178">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="490ab-179">`EventCallback` 약하게 형식화 되며 모든 인수 유형을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-179">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="490ab-180">`InvokeAsync`를 사용 하 여 `EventCallback` 또는 `EventCallback<T>`를 호출 하 고 <xref:System.Threading.Tasks.Task>를 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-180">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="490ab-181">이벤트 처리 및 바인딩 구성 요소 매개 변수에 `EventCallback` 및 `EventCallback<T>`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-181">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="490ab-182">`EventCallback`보다 강력한 형식의 `EventCallback<T>`를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-182">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="490ab-183">`EventCallback<T>` 구성 요소의 사용자에 게 더 나은 오류 피드백을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-183">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="490ab-184">다른 UI 이벤트 처리기와 마찬가지로 이벤트 매개 변수를 지정 하는 것은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-184">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="490ab-185">콜백에 전달 된 값이 없는 경우 `EventCallback`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-185">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="490ab-186">기본 동작 방지</span><span class="sxs-lookup"><span data-stu-id="490ab-186">Prevent default actions</span></span>

<span data-ttu-id="490ab-187">이벤트에 대 한 기본 동작을 방지 하려면 [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 지시어 특성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-187">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="490ab-188">입력 장치에서 키를 선택 하 고 요소 포커스가 텍스트 상자에 있는 경우 브라우저는 일반적으로 텍스트 상자에 키 문자를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-188">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="490ab-189">다음 예에서는 `@onkeypress:preventDefault` 지시어 특성을 지정 하 여 기본 동작을 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-189">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="490ab-190">카운터가 증가 하 고 **+** 키가 `<input>` 요소의 값으로 캡처되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-190">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="490ab-191">값 없이 `@on{EVENT}:preventDefault` 특성을 지정 하는 것은 `@on{EVENT}:preventDefault="true"`와 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-191">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="490ab-192">특성 값은 식일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-192">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="490ab-193">다음 예에서는 `true` 또는 `false`로 설정 된 `bool` 필드를 `_shouldPreventDefault` 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-193">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="490ab-194">기본 동작을 방지 하기 위해 이벤트 처리기가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-194">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="490ab-195">이벤트 처리기와 기본 작업 시나리오를 독립적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-195">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="490ab-196">이벤트 전파 중지</span><span class="sxs-lookup"><span data-stu-id="490ab-196">Stop event propagation</span></span>

<span data-ttu-id="490ab-197">[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 지시어 특성을 사용 하 여 이벤트 전파를 중지 합니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-197">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="490ab-198">다음 예제에서 확인란을 선택 하면 두 번째 자식 `<div>`의 click 이벤트가 부모 `<div>`에 전파 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="490ab-198">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
