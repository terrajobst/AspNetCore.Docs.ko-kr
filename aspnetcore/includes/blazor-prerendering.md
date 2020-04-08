---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647529"
---
<span data-ttu-id="ab2ce-101">Blazor 서버 앱을 미리 렌더링 중인 경우, 브라우저와의 연결이 설정되어 있지 않으므로 JavaScript 호출과 같은 특정 작업을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="ab2ce-102">미리 렌더링된 경우 구성 요소를 다르게 렌더링해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="ab2ce-103">브라우저와의 연결이 설정된 후로 JavaScript interop 호출을 지연하려면 [OnAfterRenderAsync 구성 요소 수명 주기 이벤트](xref:blazor/lifecycle#after-component-render)를 사용하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/lifecycle#after-component-render).</span></span> <span data-ttu-id="ab2ce-104">이 이벤트는 앱이 완전히 렌더링되고 클라이언트 연결이 설정된 후에만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="ab2ce-105">앞에 나온 예제 코드에서, `setElementText`wwwroot/index.html`<head>`(*WebAssembly) 또는*Pages/_Host.cshtmlBlazor(*Server)의* 요소 안에 Blazor JavaScript 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="ab2ce-106">이 함수는 `IJSRuntime.InvokeVoidAsync`를 사용하여 호출되며 값을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-106">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="ab2ce-107">앞에 나온 예제에서 DOM(문서 개체 모델)을 직접 수정한 것은 단지 데모용일 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="ab2ce-108">JavaScript를 사용하여 DOM을 직접 수정할 경우 JavaScript가 Blazor의 변경 내용 추적을 방해할 수 있으므로 대부분의 시나리오에서 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="ab2ce-109">다음 구성 요소는 미리 렌더링과 호환되는 방식으로 구성 요소의 초기화 논리의 일부로서 JavaScript interop를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="ab2ce-110">이 구성 요소는 `OnAfterRenderAsync` 내부에서 렌더링 업데이트를 트리거하는 것이 가능함을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-110">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="ab2ce-111">개발자는 이 시나리오에서 무한 루프를 만들지 않도록 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="ab2ce-112">`JSRuntime.InvokeAsync`가 호출될 경우, 구성 요소가 렌더링되기 전까지는 JavaScript 요소가 없으므로 `ElementRef`는 `OnAfterRenderAsync`에서만 사용되고 그 전의 수명 주기 메서드에서는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-112">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="ab2ce-113">JavaScript interop 호출에서 얻은 새 상태로 구성 요소를 다시 렌더링하도록 [StateHasChanged](xref:blazor/lifecycle#state-changes)가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="ab2ce-114">`StateHasChanged`는 `infoFromJs`가 `null`인 경우에만 호출되기 때문에 이 코드는 무한 루프를 만들지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="ab2ce-115">앞에 나온 예제 코드에서, `setElementText`wwwroot/index.html`<head>`(*WebAssembly) 또는*Pages/_Host.cshtmlBlazor(*Server)의* 요소 안에 Blazor JavaScript 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="ab2ce-116">이 함수는 `IJSRuntime.InvokeAsync`를 사용하여 호출되며 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-116">The function is called with `IJSRuntime.InvokeAsync` and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="ab2ce-117">앞에 나온 예제에서 DOM(문서 개체 모델)을 직접 수정한 것은 단지 데모용일 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="ab2ce-118">JavaScript를 사용하여 DOM을 직접 수정할 경우 JavaScript가 Blazor의 변경 내용 추적을 방해할 수 있으므로 대부분의 시나리오에서 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab2ce-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
