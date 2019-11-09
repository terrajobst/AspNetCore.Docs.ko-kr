<span data-ttu-id="d8bfb-101">Blazor 서버 앱은 렌더링 되지 않지만 브라우저와의 연결이 설정 되지 않았기 때문에 JavaScript를 호출 하는 등의 특정 작업을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="d8bfb-102">미리 렌더링 된 때 구성 요소를 다르게 렌더링 해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="d8bfb-103">브라우저와의 연결이 설정 될 때까지 JavaScript interop 호출을 지연 시키려면 `OnAfterRenderAsync` 구성 요소 수명 주기 이벤트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="d8bfb-104">이 이벤트는 앱이 완전히 렌더링 되 고 클라이언트 연결이 설정 된 후에만 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

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

<span data-ttu-id="d8bfb-105">앞의 예제 코드에서는 *wwwroot/index.html* 의 `<head>` 요소 (Blazor Weasembomoms) 또는 *Pages/_Host. Cshtml* (Blazor Server) 내에 `setElementText` JavaScript 함수를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="d8bfb-106">함수는 `IJSRuntime.InvokeVoidAsync`를 사용 하 여 호출 되 고 값을 반환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-106">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="d8bfb-107">앞의 예제에서는 데모용 으로만 DOM (문서 개체 모델)을 직접 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="d8bfb-108">Javascript로 DOM을 직접 수정 하는 것은 JavaScript가 Blazor의 변경 내용 추적을 방해할 수 있기 때문에 대부분의 시나리오에서 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="d8bfb-109">다음 구성 요소는 사전 렌더링과 호환 되는 방식으로 구성 요소의 초기화 논리의 일부로 JavaScript interop를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="d8bfb-110">구성 요소는 `OnAfterRenderAsync` 내부에서 렌더링 업데이트를 트리거할 수 있음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-110">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="d8bfb-111">개발자는이 시나리오에서 무한 루프를 만드는 것을 피해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="d8bfb-112">`JSRuntime.InvokeAsync`를 호출 하는 경우 구성 요소가 렌더링 될 때까지 JavaScript 요소가 없으므로 이전 수명 주기 방법이 아닌 `OnAfterRenderAsync` 에서만 `ElementRef` 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-112">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="d8bfb-113">`StateHasChanged`는 JavaScript interop 호출에서 가져온 새 상태로 구성 요소를 rerender 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-113">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="d8bfb-114">`infoFromJs` `null`경우에만 `StateHasChanged` 호출 되기 때문에 코드에서 무한 루프를 만들지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

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

<span data-ttu-id="d8bfb-115">앞의 예제 코드에서는 *wwwroot/index.html* 의 `<head>` 요소 (Blazor Weasembomoms) 또는 *Pages/_Host. Cshtml* (Blazor Server) 내에 `setElementText` JavaScript 함수를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="d8bfb-116">함수는 `IJSRuntime.InvokeAsync`를 사용 하 여 호출 되 고 값을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-116">The function is called with `IJSRuntime.InvokeAsync` and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="d8bfb-117">앞의 예제에서는 데모용 으로만 DOM (문서 개체 모델)을 직접 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="d8bfb-118">Javascript로 DOM을 직접 수정 하는 것은 JavaScript가 Blazor의 변경 내용 추적을 방해할 수 있기 때문에 대부분의 시나리오에서 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d8bfb-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
