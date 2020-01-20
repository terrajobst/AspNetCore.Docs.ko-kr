---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159904"
---
Blazor Server 앱은 렌더링 되지 않지만 브라우저와의 연결이 설정 되지 않았기 때문에 JavaScript를 호출 하는 등의 특정 작업을 수행할 수 없습니다. 미리 렌더링 된 때 구성 요소를 다르게 렌더링 해야 할 수 있습니다.

브라우저와의 연결이 설정 될 때까지 JavaScript interop 호출을 지연 시키려면 [OnAfterRenderAsync 구성 요소 수명 주기 이벤트](xref:blazor/lifecycle#after-component-render)를 사용할 수 있습니다. 이 이벤트는 앱이 완전히 렌더링 되 고 클라이언트 연결이 설정 된 후에만 호출 됩니다.

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

앞의 예제 코드에서는 *wwwroot/index.html* (Blazor Weasembmbe) 또는 *Pages/_Host. Cshtml* (Blazor Server)의 `<head>` 요소 내에 `setElementText` JavaScript 함수를 제공 합니다. 함수는 `IJSRuntime.InvokeVoidAsync`를 사용 하 여 호출 되 고 값을 반환 하지 않습니다.

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> 앞의 예제에서는 데모용 으로만 DOM (문서 개체 모델)을 직접 수정 합니다. Javascript를 사용 하 여 DOM을 직접 수정 하는 것은 JavaScript가 Blazor의 변경 내용 추적을 방해할 수 있기 때문에 권장 되지 않습니다.

다음 구성 요소는 사전 렌더링과 호환 되는 방식으로 구성 요소의 초기화 논리의 일부로 JavaScript interop를 사용 하는 방법을 보여 줍니다. 구성 요소는 `OnAfterRenderAsync`내부에서 렌더링 업데이트를 트리거할 수 있음을 보여 줍니다. 개발자는이 시나리오에서 무한 루프를 만드는 것을 피해 야 합니다.

`JSRuntime.InvokeAsync`를 호출 하는 경우 구성 요소가 렌더링 될 때까지 JavaScript 요소가 없으므로 이전 수명 주기 방법이 아닌 `OnAfterRenderAsync` 에서만 `ElementRef` 사용 됩니다.

[Statehaschanged](xref:blazor/lifecycle#state-changes) 는 JavaScript interop 호출에서 얻은 새 상태로 구성 요소를 rerender 위해 호출 됩니다. `infoFromJs` `null`경우에만 `StateHasChanged` 호출 되기 때문에 코드에서 무한 루프를 만들지 않습니다.

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

앞의 예제 코드에서는 *wwwroot/index.html* (Blazor Weasembmbe) 또는 *Pages/_Host. Cshtml* (Blazor Server)의 `<head>` 요소 내에 `setElementText` JavaScript 함수를 제공 합니다. 함수는 `IJSRuntime.InvokeAsync`를 사용 하 여 호출 되 고 값을 반환 합니다.

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> 앞의 예제에서는 데모용 으로만 DOM (문서 개체 모델)을 직접 수정 합니다. Javascript를 사용 하 여 DOM을 직접 수정 하는 것은 JavaScript가 Blazor의 변경 내용 추적을 방해할 수 있기 때문에 권장 되지 않습니다.
