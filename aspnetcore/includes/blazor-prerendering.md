Blazor 서버 앱은 렌더링 되지 않지만 브라우저와의 연결이 설정 되지 않았기 때문에 JavaScript를 호출 하는 등의 특정 작업을 수행할 수 없습니다. 미리 렌더링 된 때 구성 요소를 다르게 렌더링 해야 할 수 있습니다.

브라우저와의 연결이 설정 될 때까지 JavaScript interop 호출을 지연 시키려면 `OnAfterRenderAsync` 구성 요소 수명 주기 이벤트를 사용할 수 있습니다. 이 이벤트는 앱이 완전히 렌더링 되 고 클라이언트 연결이 설정 된 후에만 호출 됩니다.

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" value="Value set during render" />

@code {
    private ElementReference myInput;

    protected override void OnAfterRender(bool firstRender)
    {
        if (firstRender)
        {
            JSRuntime.InvokeVoidAsync(
                "setElementValue", myInput, "Value set after render");
        }
    }
}
```

다음 구성 요소는 사전 렌더링과 호환 되는 방식으로 구성 요소의 초기화 논리의 일부로 JavaScript interop를 사용 하는 방법을 보여 줍니다. 구성 요소는 `OnAfterRenderAsync` 내부에서 렌더링 업데이트를 트리거할 수 있음을 보여 줍니다. 개발자는이 시나리오에서 무한 루프를 만드는 것을 피해 야 합니다.

@No__t_0를 호출 하는 경우 구성 요소가 렌더링 될 때까지 JavaScript 요소가 없으므로 이전 수명 주기 방법이 아닌 `OnAfterRenderAsync` 에서만 `ElementRef` 사용 됩니다.

`StateHasChanged`는 JavaScript interop 호출에서 가져온 새 상태로 구성 요소를 rerender 호출 됩니다. @No__t_1 `null` 경우에만 `StateHasChanged` 호출 되기 때문에 코드에서 무한 루프를 만들지 않습니다.

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

<p>
    Set value via JS interop call:
    <input id="val-set-by-interop" @ref="myElem" />
</p>

@code {
    private string infoFromJs;
    private ElementReference myElem;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementValue", myElem, "Hello from interop call");

            StateHasChanged();
        }
    }
}
```
