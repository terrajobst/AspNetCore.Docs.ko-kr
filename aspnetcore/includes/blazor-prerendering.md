<span data-ttu-id="776ad-101">Blazor 서버 쪽 앱은 렌더링 되지 않지만 브라우저와의 연결이 설정 되지 않았기 때문에 JavaScript를 호출 하는 등의 특정 작업을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="776ad-101">While a Blazor server-side app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="776ad-102">미리 렌더링 된 때 구성 요소를 다르게 렌더링 해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="776ad-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="776ad-103">브라우저와의 연결이 설정 될 때까지 JavaScript interop 호출을 지연 시키려면 `OnAfterRenderAsync` 구성 요소 수명 주기 이벤트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="776ad-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="776ad-104">이 이벤트는 앱이 완전히 렌더링 되 고 클라이언트 연결이 설정 된 후에만 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="776ad-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" @ref:suppressField value="Value set during render" />

@code {
    private ElementReference myInput;

    protected override void OnAfterRender()
    {
        JSRuntime.InvokeAsync<object>(
            "setElementValue", myInput, "Value set after render");
    }
}
```

<span data-ttu-id="776ad-105">다음 구성 요소는 사전 렌더링과 호환 되는 방식으로 구성 요소의 초기화 논리의 일부로 JavaScript interop를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="776ad-105">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="776ad-106">구성 요소는 내부 `OnAfterRenderAsync`에서 렌더링 업데이트를 트리거할 수 있음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="776ad-106">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="776ad-107">개발자는이 시나리오에서 무한 루프를 만드는 것을 피해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="776ad-107">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="776ad-108">가 호출 되는 경우 `OnAfterRenderAsync` 는구성요소가렌더링될때까지JavaScript요소가없으므로이전수명주기메서드에서는사용되지않고에서만사용됩니다.`ElementRef` `JSRuntime.InvokeAsync`</span><span class="sxs-lookup"><span data-stu-id="776ad-108">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="776ad-109">`StateHasChanged`는 구성 요소를 JavaScript interop 호출에서 가져온 새 상태로 rerender 위해 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="776ad-109">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="776ad-110">가 인 `StateHasChanged` 경우`null`에만이 호출 되기 때문에 코드에서 무한 루프를 만들지 않습니다. `infoFromJs`</span><span class="sxs-lookup"><span data-stu-id="776ad-110">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IComponentContext ComponentContext
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

<p>
    Set value via JS interop call:
    <input id="val-set-by-interop" @ref="myElem" @ref:suppressField />
</p>

@code {
    private string infoFromJs;
    private ElementReference myElem;

    protected override async Task OnAfterRenderAsync()
    {
        // TEMPORARY: Currently we need this guard to avoid making the interop
        // call during prerendering. Soon this will be unnecessary because we
        // will change OnAfterRenderAsync so that it won't run during the
        // prerendering phase.
        if (!ComponentContext.IsConnected)
        {
            return;
        }

        if (infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementValue", myElem, "Hello from interop call");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="776ad-111">앱이 현재 콘텐츠를 렌더링 하는지 여부에 따라 다른 콘텐츠를 조건부로 렌더링 하려면 `IsConnected` `IComponentContext` 서비스에서 속성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="776ad-111">To conditionally render different content based on whether the app is currently prerendering content, use the `IsConnected` property on the `IComponentContext` service.</span></span> <span data-ttu-id="776ad-112">서버 쪽을 실행 하는 `IsConnected` 경우 클라이언트 `true` 에 대 한 활성 연결이 있는 경우에만이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="776ad-112">When running server-side, `IsConnected` only returns `true` if there's an active connection to the client.</span></span> <span data-ttu-id="776ad-113">클라이언트 쪽을 `true` 실행할 때 항상를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="776ad-113">It always returns `true` when running client-side.</span></span>

```cshtml
@page "/isconnected-example"
@using Microsoft.AspNetCore.Components.Services
@inject IComponentContext ComponentContext

<h1>IsConnected Example</h1>

<p>
    Current state:
    <strong id="connected-state">
        @(ComponentContext.IsConnected ? "connected" : "not connected")
    </strong>
</p>

<p>
    Clicks:
    <strong id="count">@count</strong>
    <button id="increment-count" @onclick="@(() => count++)">Click me</button>
</p>

@code {
    private int count;
}
```
