---
title: JavaScript interop Blazor ASP.NET Core
author: guardrex
description: Blazor apps에서 JavaScript의 .NET 및 .NET 메서드에서 JavaScript 함수를 호출 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/javascript-interop
ms.openlocfilehash: 2350870f8548a9c8df324182883a105706c12c20
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355742"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a><span data-ttu-id="37be8-103">JavaScript interop Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37be8-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="37be8-104">[Javier Calvarro e](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)및 [luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="37be8-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="37be8-105">Blazor 앱은 JavaScript 코드에서 .NET 및 .NET 메서드의 JavaScript 함수를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="37be8-106">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37be8-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="37be8-107">.NET 메서드에서 JavaScript 함수 호출</span><span class="sxs-lookup"><span data-stu-id="37be8-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="37be8-108">.NET 코드에서 JavaScript 함수를 호출 해야 하는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="37be8-109">예를 들어 JavaScript 호출은 JavaScript 라이브러리의 브라우저 기능 또는 기능을 앱에 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span> <span data-ttu-id="37be8-110">이 시나리오를 *JavaScript 상호 운용성* (*JS interop*) 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-110">This scenario is called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="37be8-111">.NET에서 JavaScript를 호출 하려면 `IJSRuntime` 추상화를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-111">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="37be8-112">JS interop 호출을 실행 하려면 구성 요소에 `IJSRuntime` 추상화를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-112">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="37be8-113">`InvokeAsync<T>` 메서드는 원하는 수의 JSON serialize 가능 인수와 함께 호출 하려는 JavaScript 함수에 대 한 식별자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-113">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="37be8-114">함수 식별자는 전역 범위 (`window`)를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-114">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="37be8-115">`window.someScope.someFunction`를 호출 하려는 경우에는 식별자가 `someScope.someFunction`됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-115">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="37be8-116">호출 되기 전에 함수를 등록할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-116">There's no need to register the function before it's called.</span></span> <span data-ttu-id="37be8-117">반환 형식 `T` JSON serializable 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-117">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="37be8-118">`T`는 반환 되는 JSON 형식에 가장 잘 매핑되는 .NET 형식과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-118">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="37be8-119">렌더링을 사용 하도록 설정 된 Blazor Server 앱의 경우 초기 렌더링을 수행 하는 동안 JavaScript를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-119">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="37be8-120">JavaScript interop 호출은 브라우저와의 연결이 설정 될 때까지 지연 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-120">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="37be8-121">자세한 내용은 [Blazor 앱이 사전 렌더링 되는 경우 검색](#detect-when-a-blazor-app-is-prerendering) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="37be8-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="37be8-122">다음 예제는 실험적 JavaScript 기반 디코더 인 [Textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="37be8-123">이 예제에서는 C# 메서드에서 JavaScript 함수를 호출 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="37be8-124">JavaScript 함수는 C# 메서드에서 바이트 배열을 받아 배열을 디코딩하고 표시를 위해 구성 요소에 텍스트를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="37be8-125">*Wwwroot/index.html* (Blazor WebAssembly) 또는 *Pages/_Host. Cshtml* (Blazor Server)의 `<head>` 요소 내에서 `TextDecoder`를 사용 하 여 전달 된 배열을 디코딩하고 디코딩된 값을 반환 하는 JavaScript 함수를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="37be8-126">앞의 예제에 표시 된 코드와 같은 JavaScript 코드는 스크립트 파일에 대 한 참조를 사용 하 여 JavaScript 파일 ( *.js*)에서 로드할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="37be8-127">다음 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="37be8-127">The following component:</span></span>

* <span data-ttu-id="37be8-128">구성 요소 단추 (**배열 변환**)를 선택한 경우 `JSRuntime`를 사용 하 여 `convertArray` JavaScript 함수를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-128">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="37be8-129">JavaScript 함수를 호출한 후에는 전달 된 배열이 문자열로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="37be8-130">문자열은 표시를 위해 구성 요소로 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-130">The string is returned to the component for display.</span></span>

[!code-razor[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a><span data-ttu-id="37be8-131">IJSRuntime 사용</span><span class="sxs-lookup"><span data-stu-id="37be8-131">Use of IJSRuntime</span></span>

<span data-ttu-id="37be8-132">`IJSRuntime` 추상화를 사용 하려면 다음 방법 중 하나를 채택 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-132">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="37be8-133">Razor 구성 요소 (*razor*)에 `IJSRuntime` 추상화를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-133">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="37be8-134">*Wwwroot/index.html* 의 `<head>` 요소 (Blazor Weasembomom) 또는 *Pages/_Host Cshtml* (Blazor Server) 내에서 `handleTickerChanged` JavaScript 함수를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-134">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="37be8-135">함수는 `IJSRuntime.InvokeVoidAsync`를 사용 하 여 호출 되 고 값을 반환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-135">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="37be8-136">`IJSRuntime` 추상화를 클래스 ( *.cs*)에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-136">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="37be8-137">*Wwwroot/index.html* 의 `<head>` 요소 (Blazor Weasembomom) 또는 *Pages/_Host Cshtml* (Blazor Server) 내에서 `handleTickerChanged` JavaScript 함수를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-137">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="37be8-138">함수는 `JSRuntime.InvokeAsync`를 사용 하 여 호출 되 고 값을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-138">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="37be8-139">[BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)를 사용 하 여 동적 콘텐츠를 생성 하려면 `[Inject]` 특성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-139">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="37be8-140">이 항목과 함께 제공 되는 클라이언트 쪽 샘플 앱에서는 사용자 입력을 받고 환영 메시지를 표시 하기 위해 DOM과 상호 작용 하는 두 가지 JavaScript 함수를 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-140">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="37be8-141">`showPrompt` &ndash;에서 사용자 입력을 수락 하는 프롬프트 (사용자 이름)를 생성 하 고 이름을 호출자에 게 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-141">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="37be8-142">`displayWelcome` &ndash;는 `welcome``id`를 사용 하 여 호출자의 시작 메시지를 DOM 개체에 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-142">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="37be8-143">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="37be8-143">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="37be8-144">JavaScript 파일을 참조 하는 `<script>` 태그를 *wwwroot/index.html* 파일 (Blazor Weasembmboms) 또는 *Pages/_Host cshtml* 파일 (Blazor Server)에 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-144">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="37be8-145">*wwwroot/index.html* (Blazor):</span><span class="sxs-lookup"><span data-stu-id="37be8-145">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="37be8-146">*Pages/_Host* (Blazor 서버):</span><span class="sxs-lookup"><span data-stu-id="37be8-146">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=21)]

<span data-ttu-id="37be8-147">`<script>` 태그를 동적으로 업데이트할 수 없으므로 구성 요소 파일에 `<script>` 태그를 넣지 마세요.</span><span class="sxs-lookup"><span data-stu-id="37be8-147">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="37be8-148">.NET 메서드는 `IJSRuntime.InvokeAsync<T>`를 호출 하 여 *exampleJsInterop* 파일의 JavaScript 함수를 사용 하 여 상호 운용 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-148">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="37be8-149">`IJSRuntime` 추상화는 Blazor 서버 시나리오를 허용 하는 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-149">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="37be8-150">앱이 Blazor Weasembomapp이 고 JavaScript 함수를 동기적으로 호출 하려는 경우에는 다운 캐스트를 `IJSInProcessRuntime` 하 고 대신 `Invoke<T>`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-150">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="37be8-151">대부분의 JS interop 라이브러리는 비동기 Api를 사용 하 여 모든 시나리오에서 라이브러리를 사용할 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-151">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="37be8-152">샘플 앱에는 JS interop를 시연 하는 구성 요소가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="37be8-153">구성 요소:</span><span class="sxs-lookup"><span data-stu-id="37be8-153">The component:</span></span>

* <span data-ttu-id="37be8-154">JavaScript 프롬프트를 통해 사용자 입력을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="37be8-155">처리를 위해 구성 요소에 텍스트를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="37be8-156">는 DOM과 상호 작용 하 여 시작 메시지를 표시 하는 두 번째 JavaScript 함수를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="37be8-157">*Pages/JSInterop*:</span><span class="sxs-lookup"><span data-stu-id="37be8-157">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="37be8-158">구성 요소의 **트리거 Javascript 프롬프트** 단추를 선택 하 여 `TriggerJsPrompt`를 실행 하면 *wwwroot/exampleJsInterop* 파일에 제공 된 javascript `showPrompt` 함수를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-158">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="37be8-159">`showPrompt` 함수는 사용자 입력 (사용자 이름)을 허용 하며,이는 HTML로 인코딩하고 구성 요소로 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="37be8-160">이 구성 요소는 `name`지역 변수에 사용자 이름을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="37be8-161">`name`에 저장 된 문자열은 환영 메시지에 통합 됩니다 .이 메시지는 환영 메시지를 머리글 태그에 렌더링 하는 `displayWelcome`JavaScript 함수에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="37be8-162">Void JavaScript 함수 호출</span><span class="sxs-lookup"><span data-stu-id="37be8-162">Call a void JavaScript function</span></span>

<span data-ttu-id="37be8-163">[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 또는 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) 를 반환 하는 JavaScript 함수는 `IJSRuntime.InvokeVoidAsync`를 사용 하 여 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a><span data-ttu-id="37be8-164">Blazor 앱이 사전 렌더링 되는 경우 검색</span><span class="sxs-lookup"><span data-stu-id="37be8-164">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="37be8-165">요소에 대 한 참조 캡처</span><span class="sxs-lookup"><span data-stu-id="37be8-165">Capture references to elements</span></span>

<span data-ttu-id="37be8-166">일부 JS interop 시나리오에서는 HTML 요소에 대 한 참조가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-166">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="37be8-167">예를 들어, UI 라이브러리에서 초기화를 위해 요소 참조를 요구 하거나, `focus` 또는 `play`와 같은 요소에서 명령 같은 Api를 호출 해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="37be8-168">다음 방법을 사용 하 여 구성 요소의 HTML 요소에 대 한 참조를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="37be8-169">HTML 요소에 `@ref` 특성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="37be8-170">`@ref` 특성의 값과 이름이 일치 하는 `ElementReference` 형식의 필드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-170">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="37be8-171">다음 예에서는 `username` `<input>` 요소에 대 한 참조를 캡처하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="37be8-172">Blazor와 상호 작용 하지 않는 빈 요소의 내용은 요소 참조를 사용 하 여 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-172">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="37be8-173">이 시나리오는 타사 API가 요소에 콘텐츠를 제공 하는 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-173">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="37be8-174">Blazor는 요소와 상호 작용 하지 않으므로 요소와 DOM의 Blazor표시 간에 충돌이 발생할 가능성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-174">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="37be8-175">다음 예제에서는 Blazor DOM과 상호 작용 하 여이 요소의 목록 항목 (`<li>`)을 채우기 때문에 순서가 지정 되지 않은 목록 (`ul`)의 *콘텐츠를 변경할 수 있습니다* .</span><span class="sxs-lookup"><span data-stu-id="37be8-175">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="37be8-176">JS interop에서 요소 `MyList`의 내용을 capture 하 고 Blazor 요소에 차이을 적용 하려고 하면 차이는 DOM과 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-176">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="37be8-177">.NET 코드와 관련 하 여 `ElementReference` 불투명 핸들입니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-177">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="37be8-178">`ElementReference`로 수행할 수 있는 *유일한* 작업은 JS interop를 통해 JavaScript 코드에 전달 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-178">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="37be8-179">이렇게 하면 JavaScript 쪽 코드가 일반적인 DOM Api와 함께 사용할 수 있는 `HTMLElement` 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-179">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="37be8-180">예를 들어 다음 코드는 요소에 포커스를 설정할 수 있도록 하는 .NET 확장 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-180">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="37be8-181">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="37be8-181">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="37be8-182">값을 반환 하지 않는 JavaScript 함수를 호출 하려면 `IJSRuntime.InvokeVoidAsync`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-182">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="37be8-183">다음 코드는 캡처된 `ElementReference`로 이전 JavaScript 함수를 호출 하 여 사용자 이름 입력에 포커스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-183">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="37be8-184">확장 메서드를 사용 하려면 `IJSRuntime` 인스턴스를 수신 하는 정적 확장 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-184">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="37be8-185">`Focus` 메서드는 개체에서 직접 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-185">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="37be8-186">다음 예에서는 `Focus` 메서드를 `JsInteropClasses` 네임 스페이스에서 사용할 수 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-186">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="37be8-187">`username` 변수는 구성 요소가 렌더링 된 후에만 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-187">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="37be8-188">JavaScript 코드에 채워지지 않은 `ElementReference` 전달 되 면 JavaScript 코드는 `null`값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-188">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="37be8-189">구성 요소에서 요소에 대 한 초기 포커스를 설정 하기 위해 렌더링을 완료 한 후 요소 참조를 조작 하려면 [OnAfterRenderAsync 또는 OnAfterRender 구성 요소 수명 주기 메서드](xref:blazor/lifecycle#after-component-render)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-189">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="37be8-190">제네릭 형식으로 작업 하 고 값을 반환 하는 경우 [\<t >](xref:System.Threading.Tasks.ValueTask`1)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-190">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="37be8-191">`GenericMethod`는 형식을 사용 하 여 개체에서 직접 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-191">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="37be8-192">다음 예에서는 `GenericMethod` `JsInteropClasses` 네임 스페이스에서 사용할 수 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-192">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="37be8-193">JavaScript 함수에서 .NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="37be8-193">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="37be8-194">정적 .NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="37be8-194">Static .NET method call</span></span>

<span data-ttu-id="37be8-195">JavaScript에서 정적 .NET 메서드를 호출 하려면 `DotNet.invokeMethod` 또는 `DotNet.invokeMethodAsync` 함수를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-195">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="37be8-196">호출할 정적 메서드의 식별자, 함수를 포함 하는 어셈블리의 이름 및 인수를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-196">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="37be8-197">비동기 버전은 Blazor 서버 시나리오를 지원 하기 위해 선호 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-197">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="37be8-198">JavaScript에서 .NET 메서드를 호출 하려면 .NET 메서드가 public, static 및 `[JSInvokable]` 특성을 가져야 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-198">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="37be8-199">기본적으로 메서드 식별자는 메서드 이름 이지만 `JSInvokableAttribute` 생성자를 사용 하 여 다른 식별자를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-199">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="37be8-200">Open 제네릭 메서드를 호출 하는 것은 현재 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-200">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="37be8-201">샘플 앱에는 `int`C# s의 배열을 반환 하는 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-201">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="37be8-202">`JSInvokable` 특성이 메서드에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-202">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="37be8-203">*Pages/JsInterop*:</span><span class="sxs-lookup"><span data-stu-id="37be8-203">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="37be8-204">클라이언트에 제공 된 JavaScript는 .Net C# 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-204">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="37be8-205">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="37be8-205">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="37be8-206">**.Net 정적 메서드 ReturnArrayAsync 트리거** 단추를 선택한 경우 브라우저의 웹 개발자 도구에서 콘솔 출력을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-206">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="37be8-207">콘솔 출력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-207">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="37be8-208">네 번째 배열 값은 `ReturnArrayAsync`에서 반환 된 배열 (`data.push(4);`)으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-208">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="37be8-209">인스턴스 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="37be8-209">Instance method call</span></span>

<span data-ttu-id="37be8-210">JavaScript에서 .NET 인스턴스 메서드를 호출할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-210">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="37be8-211">JavaScript에서 .NET 인스턴스 메서드를 호출 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-211">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="37be8-212">`DotNetObjectReference` 인스턴스에 래핑하여 .NET 인스턴스를 JavaScript에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-212">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="37be8-213">.NET 인스턴스는 JavaScript에 대 한 참조로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-213">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="37be8-214">`invokeMethod` 또는 `invokeMethodAsync` 함수를 사용 하 여 인스턴스에서 .NET 인스턴스 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-214">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="37be8-215">JavaScript에서 다른 .NET 메서드를 호출할 때 .NET 인스턴스도 인수로 전달 될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-215">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="37be8-216">샘플 앱은 클라이언트 쪽 콘솔에 메시지를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-216">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="37be8-217">샘플 앱에서 보여 주는 다음 예제에서는 브라우저의 개발자 도구에서 브라우저의 콘솔 출력을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-217">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="37be8-218">**HelloHelper .net 인스턴스 메서드 SayHello** 단추를 선택 하면 `ExampleJsInterop.CallHelloHelperSayHello`가 호출 되 고 메서드에 `Blazor`이름이 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-218">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="37be8-219">*Pages/JsInterop*:</span><span class="sxs-lookup"><span data-stu-id="37be8-219">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="37be8-220">`CallHelloHelperSayHello`는 `HelloHelper`의 새 인스턴스와 `sayHello` JavaScript 함수를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-220">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="37be8-221">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="37be8-221">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="37be8-222">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="37be8-222">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="37be8-223">이 이름은 `HelloHelper.Name` 속성을 설정 하는 `HelloHelper`의 생성자에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-223">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="37be8-224">JavaScript 함수 `sayHello` 실행 되 면 `HelloHelper.SayHello` JavaScript 함수를 통해 콘솔에 기록 되는 `Hello, {Name}!` 메시지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-224">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="37be8-225">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="37be8-225">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="37be8-226">브라우저의 웹 개발자 도구에 있는 콘솔 출력:</span><span class="sxs-lookup"><span data-stu-id="37be8-226">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="37be8-227">클래스 라이브러리의 interop 코드 공유</span><span class="sxs-lookup"><span data-stu-id="37be8-227">Share interop code in a class library</span></span>

<span data-ttu-id="37be8-228">JS interop 코드를 클래스 라이브러리에 포함 하 여 NuGet 패키지의 코드를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-228">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="37be8-229">클래스 라이브러리는 빌드된 어셈블리에 포함 된 JavaScript 리소스를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-229">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="37be8-230">JavaScript 파일은 *wwwroot* 폴더에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-230">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="37be8-231">도구는 라이브러리가 빌드될 때 리소스를 포함 하는 작업을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-231">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="37be8-232">빌드된 NuGet 패키지는 NuGet 패키지를 참조 하는 것과 동일한 방식으로 앱의 프로젝트 파일에서 참조 됩니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-232">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="37be8-233">패키지가 복원 된 후에는 앱 코드가 JavaScript를로 호출할 수 있습니다 C#.</span><span class="sxs-lookup"><span data-stu-id="37be8-233">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="37be8-234">자세한 내용은 <xref:blazor/class-libraries>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="37be8-234">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="37be8-235">JS interop 호출 강화</span><span class="sxs-lookup"><span data-stu-id="37be8-235">Harden JS interop calls</span></span>

<span data-ttu-id="37be8-236">JS interop는 네트워킹 오류로 인해 실패할 수 있으며 신뢰할 수 없는 것으로 처리 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-236">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="37be8-237">기본적으로 Blazor Server 앱은 1 분 후 서버에서 JS interop 호출을 시간 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-237">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="37be8-238">앱에서 10 초 등의 적극적 시간 제한을 허용할 수 있는 경우 다음 방법 중 하나를 사용 하 여 시간 제한을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-238">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="37be8-239">`Startup.ConfigureServices`에서 전역적으로 시간 제한을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-239">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="37be8-240">구성 요소 코드에서 호출 마다 단일 호출에서 제한 시간을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37be8-240">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="37be8-241">리소스 소모에 대 한 자세한 내용은 <xref:security/blazor/server>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="37be8-241">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37be8-242">추가 자료</span><span class="sxs-lookup"><span data-stu-id="37be8-242">Additional resources</span></span>

* [<span data-ttu-id="37be8-243">InteropComponent 예 (aspnet/AspNetCore GitHub 리포지토리, 3.0 릴리스 분기)</span><span class="sxs-lookup"><span data-stu-id="37be8-243">InteropComponent.razor example (aspnet/AspNetCore GitHub repository, 3.0 release branch)</span></span>](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
