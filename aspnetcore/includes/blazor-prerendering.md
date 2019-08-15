Blazor 서버 쪽 앱은 렌더링 되지 않지만 브라우저와의 연결이 설정 되지 않았기 때문에 JavaScript를 호출 하는 등의 특정 작업을 수행할 수 없습니다. 미리 렌더링 된 때 구성 요소를 다르게 렌더링 해야 할 수 있습니다.

브라우저와의 연결이 설정 될 때까지 JavaScript interop 호출을 지연 시키려면 `OnAfterRenderAsync` 구성 요소 수명 주기 이벤트를 사용할 수 있습니다. 이 이벤트는 앱이 완전히 렌더링 되 고 클라이언트 연결이 설정 된 후에만 호출 됩니다.

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

다음 구성 요소는 사전 렌더링과 호환 되는 방식으로 구성 요소의 초기화 논리의 일부로 JavaScript interop를 사용 하는 방법을 보여 줍니다. 구성 요소는 내부 `OnAfterRenderAsync`에서 렌더링 업데이트를 트리거할 수 있음을 보여 줍니다. 개발자는이 시나리오에서 무한 루프를 만드는 것을 피해 야 합니다.

가 호출 되는 경우 `OnAfterRenderAsync` 는구성요소가렌더링될때까지JavaScript요소가없으므로이전수명주기메서드에서는사용되지않고에서만사용됩니다.`ElementRef` `JSRuntime.InvokeAsync`

`StateHasChanged`는 구성 요소를 JavaScript interop 호출에서 가져온 새 상태로 rerender 위해 호출 됩니다. 가 인 `StateHasChanged` 경우`null`에만이 호출 되기 때문에 코드에서 무한 루프를 만들지 않습니다. `infoFromJs`

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

앱이 현재 콘텐츠를 렌더링 하는지 여부에 따라 다른 콘텐츠를 조건부로 렌더링 하려면 `IsConnected` `IComponentContext` 서비스에서 속성을 사용 합니다. 서버 쪽을 실행 하는 `IsConnected` 경우 클라이언트 `true` 에 대 한 활성 연결이 있는 경우에만이 반환 됩니다. 클라이언트 쪽을 `true` 실행할 때 항상를 반환 합니다.

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
