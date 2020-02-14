---
title: JavaScript interop Blazor ASP.NET Core
author: guardrex
description: Blazor apps에서 JavaScript의 .NET 및 .NET 메서드에서 JavaScript 함수를 호출 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/javascript-interop
ms.openlocfilehash: c4f2444b60fc2d3a8af893df379cf62636a7bdd5
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213365"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a>JavaScript interop Blazor ASP.NET Core

[Javier Calvarro e](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)및 [luke latham 문자](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 앱은 JavaScript 코드에서 .NET 및 .NET 메서드의 JavaScript 함수를 호출할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="invoke-javascript-functions-from-net-methods"></a>.NET 메서드에서 JavaScript 함수 호출

.NET 코드에서 JavaScript 함수를 호출 해야 하는 경우가 있습니다. 예를 들어 JavaScript 호출은 JavaScript 라이브러리의 브라우저 기능 또는 기능을 앱에 노출할 수 있습니다. 이 시나리오를 *JavaScript 상호 운용성* (*JS interop*) 이라고 합니다.

.NET에서 JavaScript를 호출 하려면 `IJSRuntime` 추상화를 사용 합니다. JS interop 호출을 실행 하려면 구성 요소에 `IJSRuntime` 추상화를 삽입 합니다. `InvokeAsync<T>` 메서드는 원하는 수의 JSON serialize 가능 인수와 함께 호출 하려는 JavaScript 함수에 대 한 식별자를 사용 합니다. 함수 식별자는 전역 범위 (`window`)를 기준으로 합니다. `window.someScope.someFunction`를 호출 하려는 경우에는 식별자가 `someScope.someFunction`됩니다. 호출 되기 전에 함수를 등록할 필요가 없습니다. 반환 형식 `T` JSON serializable 이어야 합니다. `T`는 반환 되는 JSON 형식에 가장 잘 매핑되는 .NET 형식과 일치 해야 합니다.

렌더링을 사용 하도록 설정 된 Blazor Server 앱의 경우 초기 렌더링을 수행 하는 동안 JavaScript를 호출할 수 없습니다. JavaScript interop 호출은 브라우저와의 연결이 설정 될 때까지 지연 되어야 합니다. 자세한 내용은 [Blazor 앱이 사전 렌더링 되는 경우 검색](#detect-when-a-blazor-app-is-prerendering) 섹션을 참조 하세요.

다음 예제는 실험적 JavaScript 기반 디코더 인 [Textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)를 기반으로 합니다. 이 예제에서는 C# 메서드에서 JavaScript 함수를 호출 하는 방법을 보여 줍니다. JavaScript 함수는 C# 메서드에서 바이트 배열을 받아 배열을 디코딩하고 표시를 위해 구성 요소에 텍스트를 반환 합니다.

*Wwwroot/index.html* (Blazor WebAssembly) 또는 *Pages/_Host. Cshtml* (Blazor Server)의 `<head>` 요소 내에서 `TextDecoder`를 사용 하 여 전달 된 배열을 디코딩하고 디코딩된 값을 반환 하는 JavaScript 함수를 제공 합니다.

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

앞의 예제에 표시 된 코드와 같은 JavaScript 코드는 스크립트 파일에 대 한 참조를 사용 하 여 JavaScript 파일 ( *.js*)에서 로드할 수도 있습니다.

```html
<script src="exampleJsInterop.js"></script>
```

다음 구성 요소:

* 구성 요소 단추 (**배열 변환**)를 선택한 경우 `JSRuntime`를 사용 하 여 `convertArray` JavaScript 함수를 호출 합니다.
* JavaScript 함수를 호출한 후에는 전달 된 배열이 문자열로 변환 됩니다. 문자열은 표시를 위해 구성 요소로 반환 됩니다.

[!code-razor[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a>IJSRuntime 사용

`IJSRuntime` 추상화를 사용 하려면 다음 방법 중 하나를 채택 합니다.

* Razor 구성 요소 (*razor*)에 `IJSRuntime` 추상화를 삽입 합니다.

  [!code-razor[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  *Wwwroot/index.html* 의 `<head>` 요소 (Blazor Weasembomom) 또는 *Pages/_Host Cshtml* (Blazor Server) 내에서 `handleTickerChanged` JavaScript 함수를 제공 합니다. 함수는 `IJSRuntime.InvokeVoidAsync`를 사용 하 여 호출 되 고 값을 반환 하지 않습니다.

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* `IJSRuntime` 추상화를 클래스 ( *.cs*)에 삽입 합니다.

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  *Wwwroot/index.html* 의 `<head>` 요소 (Blazor Weasembomom) 또는 *Pages/_Host Cshtml* (Blazor Server) 내에서 `handleTickerChanged` JavaScript 함수를 제공 합니다. 함수는 `JSRuntime.InvokeAsync`를 사용 하 여 호출 되 고 값을 반환 합니다.

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)를 사용 하 여 동적 콘텐츠를 생성 하려면 `[Inject]` 특성을 사용 합니다.

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

이 항목과 함께 제공 되는 클라이언트 쪽 샘플 앱에서는 사용자 입력을 받고 환영 메시지를 표시 하기 위해 DOM과 상호 작용 하는 두 가지 JavaScript 함수를 앱에서 사용할 수 있습니다.

* `showPrompt` &ndash;에서 사용자 입력을 수락 하는 프롬프트 (사용자 이름)를 생성 하 고 이름을 호출자에 게 반환 합니다.
* `displayWelcome` &ndash;는 `welcome``id`를 사용 하 여 호출자의 시작 메시지를 DOM 개체에 할당 합니다.

*wwwroot/exampleJsInterop*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

JavaScript 파일을 참조 하는 `<script>` 태그를 *wwwroot/index.html* 파일 (Blazor Weasembmboms) 또는 *Pages/_Host cshtml* 파일 (Blazor Server)에 저장 합니다.

*wwwroot/index.html* (Blazor):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Pages/_Host* (Blazor 서버):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

`<script>` 태그를 동적으로 업데이트할 수 없으므로 구성 요소 파일에 `<script>` 태그를 넣지 마세요.

.NET 메서드는 `IJSRuntime.InvokeAsync<T>`를 호출 하 여 *exampleJsInterop* 파일의 JavaScript 함수를 사용 하 여 상호 운용 합니다.

`IJSRuntime` 추상화는 Blazor 서버 시나리오를 허용 하는 비동기입니다. 앱이 Blazor Weasembomapp이 고 JavaScript 함수를 동기적으로 호출 하려는 경우에는 다운 캐스트를 `IJSInProcessRuntime` 하 고 대신 `Invoke<T>`를 호출 합니다. 대부분의 JS interop 라이브러리는 비동기 Api를 사용 하 여 모든 시나리오에서 라이브러리를 사용할 수 있도록 하는 것이 좋습니다.

샘플 앱에는 JS interop를 시연 하는 구성 요소가 포함 되어 있습니다. 구성 요소:

* JavaScript 프롬프트를 통해 사용자 입력을 받습니다.
* 처리를 위해 구성 요소에 텍스트를 반환 합니다.
* 는 DOM과 상호 작용 하 여 시작 메시지를 표시 하는 두 번째 JavaScript 함수를 호출 합니다.

*Pages/JSInterop*:

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        // showPrompt is implemented in wwwroot/exampleJsInterop.js
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");
        // displayWelcome is implemented in wwwroot/exampleJsInterop.js
        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. 구성 요소의 **트리거 Javascript 프롬프트** 단추를 선택 하 여 `TriggerJsPrompt`를 실행 하면 *wwwroot/exampleJsInterop* 파일에 제공 된 javascript `showPrompt` 함수를 호출 합니다.
1. `showPrompt` 함수는 사용자 입력 (사용자 이름)을 허용 하며,이는 HTML로 인코딩하고 구성 요소로 반환 됩니다. 이 구성 요소는 `name`지역 변수에 사용자 이름을 저장 합니다.
1. `name`에 저장 된 문자열은 환영 메시지에 통합 됩니다 .이 메시지는 환영 메시지를 머리글 태그에 렌더링 하는 `displayWelcome`JavaScript 함수에 전달 됩니다.

## <a name="call-a-void-javascript-function"></a>Void JavaScript 함수 호출

[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 또는 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) 를 반환 하는 JavaScript 함수는 `IJSRuntime.InvokeVoidAsync`를 사용 하 여 호출 됩니다.

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a>Blazor 앱이 사전 렌더링 되는 경우 검색
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>요소에 대 한 참조 캡처

일부 JS interop 시나리오에서는 HTML 요소에 대 한 참조가 필요 합니다. 예를 들어, UI 라이브러리에서 초기화를 위해 요소 참조를 요구 하거나, `focus` 또는 `play`와 같은 요소에서 명령 같은 Api를 호출 해야 할 수 있습니다.

다음 방법을 사용 하 여 구성 요소의 HTML 요소에 대 한 참조를 캡처합니다.

* HTML 요소에 `@ref` 특성을 추가 합니다.
* `@ref` 특성의 값과 이름이 일치 하는 `ElementReference` 형식의 필드를 정의 합니다.

다음 예에서는 `username` `<input>` 요소에 대 한 참조를 캡처하는 방법을 보여 줍니다.

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Blazor와 상호 작용 하지 않는 빈 요소의 내용은 요소 참조를 사용 하 여 변경할 수 있습니다. 이 시나리오는 타사 API가 요소에 콘텐츠를 제공 하는 경우에 유용 합니다. Blazor는 요소와 상호 작용 하지 않으므로 요소와 DOM의 Blazor표시 간에 충돌이 발생할 가능성이 없습니다.
>
> 다음 예제에서는 Blazor DOM과 상호 작용 하 여이 요소의 목록 항목 (`<li>`)을 채우기 때문에 순서가 지정 되지 않은 목록 (`ul`)의 *콘텐츠를 변경할 수 있습니다* .
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> JS interop에서 요소 `MyList`의 내용을 capture 하 고 Blazor 요소에 차이을 적용 하려고 하면 차이는 DOM과 일치 하지 않습니다.

.NET 코드와 관련 하 여 `ElementReference` 불투명 핸들입니다. `ElementReference`로 수행할 수 있는 *유일한* 작업은 JS interop를 통해 JavaScript 코드에 전달 하는 것입니다. 이렇게 하면 JavaScript 쪽 코드가 일반적인 DOM Api와 함께 사용할 수 있는 `HTMLElement` 인스턴스를 수신 합니다.

예를 들어 다음 코드는 요소에 포커스를 설정할 수 있도록 하는 .NET 확장 메서드를 정의 합니다.

*exampleJsInterop*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

값을 반환 하지 않는 JavaScript 함수를 호출 하려면 `IJSRuntime.InvokeVoidAsync`을 사용 합니다. 다음 코드는 캡처된 `ElementReference`로 이전 JavaScript 함수를 호출 하 여 사용자 이름 입력에 포커스를 설정 합니다.

[!code-razor[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

확장 메서드를 사용 하려면 `IJSRuntime` 인스턴스를 수신 하는 정적 확장 메서드를 만듭니다.

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

`Focus` 메서드는 개체에서 직접 호출 됩니다. 다음 예에서는 `Focus` 메서드를 `JsInteropClasses` 네임 스페이스에서 사용할 수 있다고 가정 합니다.

[!code-razor[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> `username` 변수는 구성 요소가 렌더링 된 후에만 채워집니다. JavaScript 코드에 채워지지 않은 `ElementReference` 전달 되 면 JavaScript 코드는 `null`값을 받습니다. 구성 요소에서 요소에 대 한 초기 포커스를 설정 하기 위해 렌더링을 완료 한 후 요소 참조를 조작 하려면 [OnAfterRenderAsync 또는 OnAfterRender 구성 요소 수명 주기 메서드](xref:blazor/lifecycle#after-component-render)를 사용 합니다.

제네릭 형식으로 작업 하 고 값을 반환 하는 경우 [\<t >](xref:System.Threading.Tasks.ValueTask`1)를 사용 합니다.

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`는 형식을 사용 하 여 개체에서 직접 호출 됩니다. 다음 예에서는 `GenericMethod` `JsInteropClasses` 네임 스페이스에서 사용할 수 있다고 가정 합니다.

[!code-razor[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>구성 요소 간 참조 요소

`ElementReference`는 구성 요소의 `OnAfterRender` 메서드에서 유효 하 게 보장 되며 요소 참조는 `struct`이므로 요소 참조는 구성 요소 간에 전달 될 수 없습니다.

부모 구성 요소가 다른 구성 요소에서 요소 참조를 사용할 수 있도록 하기 위해 부모 구성 요소는 다음을 수행할 수 있습니다.

* 자식 구성 요소가 콜백을 등록할 수 있도록 허용 합니다.
* 전달 된 요소 참조를 사용 하 여 `OnAfterRender` 이벤트 중에 등록 된 콜백을 호출 합니다. 간접적으로이 방법을 사용 하면 자식 구성 요소가 부모의 요소 참조와 상호 작용할 수 있습니다.

다음 Blazor Weasembomamboma 예제는 접근 방법을 보여 줍니다.

*Wwwroot/index.html*의 `<head>`에서 다음을 수행 합니다.

```html
<style>
    .red { color: red }
</style>
```

*Wwwroot/index.html*의 `<body>`에서 다음을 수행 합니다.

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Pages/Index. razor* (부모 구성 요소):

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*페이지/인덱스. razor .cs*:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

*Shared/SurveyPrompt* (자식 구성 요소):

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

*Shared/SurveyPrompt*:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="invoke-net-methods-from-javascript-functions"></a>JavaScript 함수에서 .NET 메서드 호출

### <a name="static-net-method-call"></a>정적 .NET 메서드 호출

JavaScript에서 정적 .NET 메서드를 호출 하려면 `DotNet.invokeMethod` 또는 `DotNet.invokeMethodAsync` 함수를 사용 합니다. 호출할 정적 메서드의 식별자, 함수를 포함 하는 어셈블리의 이름 및 인수를 전달 합니다. 비동기 버전은 Blazor 서버 시나리오를 지원 하기 위해 선호 됩니다. .NET 메서드는 public, static 및 `[JSInvokable]` 특성을 가져야 합니다. Open 제네릭 메서드를 호출 하는 것은 현재 지원 되지 않습니다.

샘플 앱에는 `int`C# s의 배열을 반환 하는 메서드가 포함 되어 있습니다. `JSInvokable` 특성이 메서드에 적용 됩니다.

*Pages/JsInterop*:

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

클라이언트에 제공 된 JavaScript는 .Net C# 메서드를 호출 합니다.

*wwwroot/exampleJsInterop*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

**.Net 정적 메서드 ReturnArrayAsync 트리거** 단추를 선택한 경우 브라우저의 웹 개발자 도구에서 콘솔 출력을 검사 합니다.

콘솔 출력은 다음과 같습니다.

```console
Array(4) [ 1, 2, 3, 4 ]
```

네 번째 배열 값은 `ReturnArrayAsync`에서 반환 된 배열 (`data.push(4);`)으로 푸시됩니다.

기본적으로 메서드 식별자는 메서드 이름 이지만 `JSInvokableAttribute` 생성자를 사용 하 여 다른 식별자를 지정할 수 있습니다.

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

클라이언트 쪽 JavaScript 파일에서 다음을 수행 합니다.

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

### <a name="instance-method-call"></a>인스턴스 메서드 호출

JavaScript에서 .NET 인스턴스 메서드를 호출할 수도 있습니다. JavaScript에서 .NET 인스턴스 메서드를 호출 하려면 다음을 수행 합니다.

* `DotNetObjectReference` 인스턴스에 래핑하여 .NET 인스턴스를 JavaScript에 전달 합니다. .NET 인스턴스는 JavaScript에 대 한 참조로 전달 됩니다.
* `invokeMethod` 또는 `invokeMethodAsync` 함수를 사용 하 여 인스턴스에서 .NET 인스턴스 메서드를 호출 합니다. JavaScript에서 다른 .NET 메서드를 호출할 때 .NET 인스턴스도 인수로 전달 될 수도 있습니다.

> [!NOTE]
> 샘플 앱은 클라이언트 쪽 콘솔에 메시지를 기록 합니다. 샘플 앱에서 보여 주는 다음 예제에서는 브라우저의 개발자 도구에서 브라우저의 콘솔 출력을 검사 합니다.

**HelloHelper .net 인스턴스 메서드 SayHello** 단추를 선택 하면 `ExampleJsInterop.CallHelloHelperSayHello`가 호출 되 고 메서드에 `Blazor`이름이 전달 됩니다.

*Pages/JsInterop*:

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello`는 `HelloHelper`의 새 인스턴스와 `sayHello` JavaScript 함수를 호출 합니다.

*JsInteropClasses/ExampleJsInterop*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

이 이름은 `HelloHelper.Name` 속성을 설정 하는 `HelloHelper`의 생성자에 전달 됩니다. JavaScript 함수 `sayHello` 실행 되 면 `HelloHelper.SayHello` JavaScript 함수를 통해 콘솔에 기록 되는 `Hello, {Name}!` 메시지를 반환 합니다.

*JsInteropClasses/HelloHelper*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

브라우저의 웹 개발자 도구에 있는 콘솔 출력:

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a>클래스 라이브러리의 interop 코드 공유

JS interop 코드를 클래스 라이브러리에 포함 하 여 NuGet 패키지의 코드를 공유할 수 있습니다.

클래스 라이브러리는 빌드된 어셈블리에 포함 된 JavaScript 리소스를 처리 합니다. JavaScript 파일은 *wwwroot* 폴더에 배치 됩니다. 도구는 라이브러리가 빌드될 때 리소스를 포함 하는 작업을 담당 합니다.

빌드된 NuGet 패키지는 NuGet 패키지를 참조 하는 것과 동일한 방식으로 앱의 프로젝트 파일에서 참조 됩니다. 패키지가 복원 된 후에는 앱 코드가 JavaScript를로 호출할 수 있습니다 C#.

자세한 내용은 <xref:blazor/class-libraries>을 참조하세요.

## <a name="harden-js-interop-calls"></a>JS interop 호출 강화

JS interop는 네트워킹 오류로 인해 실패할 수 있으며 신뢰할 수 없는 것으로 처리 되어야 합니다. 기본적으로 Blazor Server 앱은 1 분 후 서버에서 JS interop 호출을 시간 제한 합니다. 앱에서 10 초 등의 적극적 시간 제한을 허용할 수 있는 경우 다음 방법 중 하나를 사용 하 여 시간 제한을 설정 합니다.

* `Startup.ConfigureServices`에서 전역적으로 시간 제한을 지정 합니다.

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* 구성 요소 코드에서 호출 마다 단일 호출에서 제한 시간을 지정할 수 있습니다.

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

리소스 소모에 대 한 자세한 내용은 <xref:security/blazor/server>를 참조 하세요.

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a>Blazor Server 앱에서 대량 데이터 전송 수행

일부 시나리오에서는 JavaScript와 Blazor간에 많은 양의 데이터를 전송 해야 합니다. 일반적으로 다음과 같은 경우 대량 데이터 전송이 발생 합니다.

* 브라우저 파일 시스템 Api는 파일을 업로드 하거나 다운로드 하는 데 사용 됩니다.
* 타사 라이브러리와의 상호 운용성이 필요 합니다.

Blazor 서버에서 성능 문제를 일으킬 수 있는 단일 큰 메시지를 전달 하는 것을 방지 하기 위해 제한이 적용 됩니다.

JavaScript와 Blazor간에 데이터를 전송 하는 코드를 개발 하는 경우 다음 지침을 고려 하세요.

* 데이터를 더 작은 부분으로 분할 하 고 서버에서 모든 데이터를 받을 때까지 데이터 세그먼트를 순차적으로 보냅니다.
* JavaScript 및 C# 코드에는 많은 개체를 할당 하지 않습니다.
* 데이터를 보내거나 받을 때 긴 기간 동안 주 UI 스레드를 차단 하지 않습니다.
* 프로세스가 완료 되거나 취소 될 때 사용 되는 메모리를 확보 합니다.
* 보안을 위해 다음과 같은 추가 요구 사항을 적용 합니다.
  * 전달할 수 있는 최대 파일 또는 데이터 크기를 선언 합니다.
  * 클라이언트에서 서버로의 최소 업로드 율을 선언 합니다.
* 서버에서 데이터를 받은 후에는 다음과 같은 데이터를 사용할 수 있습니다.
  * 모든 세그먼트가 수집 될 때까지 메모리 버퍼에 임시로 저장 됩니다.
  * 즉시 사용 됩니다. 예를 들어 데이터를 데이터베이스에 즉시 저장 하거나 각 세그먼트를 수신 하면 디스크에 쓸 수 있습니다.

다음 파일 업 로더 클래스는 클라이언트에서 JS interop를 처리 합니다. 업 로더 클래스는 JS interop를 사용 하 여 다음을 수행 합니다.

* 클라이언트를 폴링하여 데이터 세그먼트를 보냅니다.
* 폴링 시간이 초과 되 면 트랜잭션을 중단 합니다.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

앞의 예제에서:

* `_maxBase64SegmentSize`은 `_maxBase64SegmentSize = _segmentSize * 4 / 3`에서 계산 되는 `8192`로 설정 됩니다.
* 낮은 수준 .NET Core 메모리 관리 Api를 사용 하 여 서버의 메모리 세그먼트를 `_uploadedSegments`에 저장 합니다.
* `ReceiveFile` 메서드는 JS interop를 통해 업로드를 처리 하는 데 사용 됩니다.
  * 파일 크기는 `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`를 사용 하 여 JS interop에서 바이트 단위로 결정 됩니다.
  * 수신할 세그먼트 수가 계산 되 고 `numberOfSegments`저장 됩니다.
  * 세그먼트는 `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`를 사용 하 여 JS interop를 통해 `for` 루프에서 요청 됩니다. 디코딩을 수행 하기 전에 모든 세그먼트가 8192 바이트 여야 합니다. 클라이언트는 효율적인 방식으로 데이터를 전송 하도록 강제 합니다.
  * 수신 된 각 세그먼트에 대해 <xref:System.Convert.TryFromBase64String*>를 사용 하 여 디코딩을 수행 하기 전에 검사를 수행 합니다.
  * 업로드가 완료 되 면 데이터가 있는 스트림이 새 <xref:System.IO.Stream> (`SegmentedStream`)로 반환 됩니다.

분할 된 스트림 클래스는 세그먼트 목록을 검색할 수가 없는 읽기 전용 <xref:System.IO.Stream>으로 노출 합니다.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

다음 코드는 데이터를 수신 하는 JavaScript 함수를 구현 합니다.

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```

## <a name="additional-resources"></a>추가 자료

* [InteropComponent 예 (dotnet/AspNetCore GitHub 리포지토리, 3.1 릴리스 분기)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
