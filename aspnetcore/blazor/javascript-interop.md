---
title: ASP.NET Core Blazor JavaScript interop
author: guardrex
description: Blazor apps에서 JavaScript의 .NET 및 .NET 메서드에서 JavaScript 함수를 호출 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/javascript-interop
ms.openlocfilehash: b30bce6ef3ebf1cd2f4f3fe8d046e1db9b6929d5
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71924642"
---
# <a name="aspnet-core-blazor-javascript-interop"></a><span data-ttu-id="2ddc9-103">ASP.NET Core Blazor JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="2ddc9-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="2ddc9-104">[Javier Calvarro e](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)및 [luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2ddc9-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2ddc9-105">Blazor 앱은 JavaScript 코드에서 .NET 및 .NET 메서드의 JavaScript 함수를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="2ddc9-106">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2ddc9-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="2ddc9-107">.NET 메서드에서 JavaScript 함수 호출</span><span class="sxs-lookup"><span data-stu-id="2ddc9-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="2ddc9-108">.NET 코드에서 JavaScript 함수를 호출 해야 하는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="2ddc9-109">예를 들어 JavaScript 호출은 JavaScript 라이브러리의 브라우저 기능 또는 기능을 앱에 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="2ddc9-110">.Net에서 JavaScript를 호출 하려면 `IJSRuntime` 추상화를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="2ddc9-111">메서드 `InvokeAsync<T>` 는 원하는 수의 JSON 직렬화 가능 인수와 함께 호출 하려는 JavaScript 함수에 대 한 식별자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-111">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="2ddc9-112">함수 식별자는 전역 범위 (`window`)를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-112">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="2ddc9-113">을 호출 `window.someScope.someFunction`하려는 경우 `someScope.someFunction`식별자는입니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-113">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="2ddc9-114">호출 되기 전에 함수를 등록할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-114">There's no need to register the function before it's called.</span></span> <span data-ttu-id="2ddc9-115">반환 형식은 `T` JSON 직렬화도 가능 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-115">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="2ddc9-116">Blazor 서버 앱의 경우:</span><span class="sxs-lookup"><span data-stu-id="2ddc9-116">For Blazor Server apps:</span></span>

* <span data-ttu-id="2ddc9-117">Blazor 서버 앱에서 여러 사용자 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-117">Multiple user requests are processed by the Blazor Server app.</span></span> <span data-ttu-id="2ddc9-118">JavaScript 함수 `JSRuntime.Current` 를 호출 하기 위해 구성 요소에서를 호출 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-118">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="2ddc9-119">`IJSRuntime` 추상화를 주입 하 고 삽입 된 개체를 사용 하 여 JavaScript interop 호출을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-119">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>
* <span data-ttu-id="2ddc9-120">Blazor 앱은 렌더링 되지 않지만 브라우저와의 연결이 설정 되지 않았기 때문에 JavaScript를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-120">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="2ddc9-121">자세한 내용은 [Blazor 앱이 사전 렌더링 되는 경우 검색](#detect-when-a-blazor-app-is-prerendering) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="2ddc9-122">다음 예제는 실험적 JavaScript 기반 디코더 인 [Textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="2ddc9-123">이 예제에서는 C# 메서드에서 JavaScript 함수를 호출 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="2ddc9-124">JavaScript 함수는 C# 메서드에서 바이트 배열을 받아 배열을 디코딩하고 표시를 위해 구성 요소에 텍스트를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="2ddc9-125">*Wwwroot/index.html* (Blazor Weasembom) 또는 *Pages/_Host* (Blazor Server)의 `<head>` 요소 내에서-3을 @no__t 사용 하 여 전달 된 배열을 디코딩하는 함수를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a function that uses `TextDecoder` to decode a passed array:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

<span data-ttu-id="2ddc9-126">앞의 예제에 표시 된 코드와 같은 JavaScript 코드는 스크립트 파일에 대 한 참조를 사용 하 여 JavaScript 파일 ( *.js*)에서 로드할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="2ddc9-127">다음 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="2ddc9-127">The following component:</span></span>

* <span data-ttu-id="2ddc9-128">구성 요소 `ConvertArray` 단추 (**배열 변환**)가 선택 된 경우를 사용 하 여 `JsRuntime` JavaScript 함수를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-128">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="2ddc9-129">JavaScript 함수를 호출한 후에는 전달 된 배열이 문자열로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="2ddc9-130">문자열은 표시를 위해 구성 요소로 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-130">The string is returned to the component for display.</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

<span data-ttu-id="2ddc9-131">`IJSRuntime` 추상화를 사용 하려면 다음 방법 중 하나를 채택 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="2ddc9-132">Razor 구성 `IJSRuntime` 요소 (*razor*)에 추상화를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* <span data-ttu-id="2ddc9-133">클래스 ( `IJSRuntime` *.cs*)에 추상화를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-133">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* <span data-ttu-id="2ddc9-134">[BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)를 사용 하 여 동적 콘텐츠를 생성 `[Inject]` 하려면 특성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-134">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="2ddc9-135">이 항목과 함께 제공 되는 클라이언트 쪽 샘플 앱에서는 사용자 입력을 받고 환영 메시지를 표시 하기 위해 DOM과 상호 작용 하는 두 가지 JavaScript 함수를 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-135">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="2ddc9-136">`showPrompt`&ndash; 사용자 입력 (사용자 이름)을 수락 하 고 이름을 호출자에 게 반환 하 라는 프롬프트를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-136">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="2ddc9-137">`displayWelcome`&ndash; 의`welcome`를사용 하 여 호출자의 시작 메시지를 DOM 개체에 할당 합니다. `id`</span><span class="sxs-lookup"><span data-stu-id="2ddc9-137">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="2ddc9-138">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="2ddc9-138">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="2ddc9-139">*Wwwroot/index.html* 파일 (Blazor Weasembmboms) 또는 *Pages/_Host* 파일 (Blazor Server)에서 JavaScript 파일을 참조 하는 `<script>` 태그를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-139">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="2ddc9-140">*wwwroot/index.html* (Blazor Weasembambmbsembse):</span><span class="sxs-lookup"><span data-stu-id="2ddc9-140">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="2ddc9-141">*Pages/_Host* (Blazor 서버):</span><span class="sxs-lookup"><span data-stu-id="2ddc9-141">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

<span data-ttu-id="2ddc9-142">태그를 동적 `<script>` 으로 업데이트할 수 `<script>` 없기 때문에 구성 요소 파일에 태그를 넣지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-142">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="2ddc9-143">.NET 메서드는를 호출 `IJSRuntime.InvokeAsync<T>`하 여 *exampleJsInterop* 파일의 JavaScript 함수를 사용 하 여 상호 운용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-143">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="2ddc9-144">Blazor `IJSRuntime` 서버 시나리오의 경우 추상화는 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-144">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="2ddc9-145">앱이 Blazor weasembomapp이 고 JavaScript 함수를 동기적으로 호출 하려는 경우로 다운 캐스트 및를 `IJSInProcessRuntime` 대신 호출 `Invoke<T>` 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-145">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="2ddc9-146">대부분의 JavaScript interop 라이브러리는 비동기 Api를 사용 하 여 모든 시나리오에서 라이브러리를 사용할 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-146">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="2ddc9-147">샘플 앱에는 JavaScript interop를 설명 하는 구성 요소가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-147">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="2ddc9-148">구성 요소:</span><span class="sxs-lookup"><span data-stu-id="2ddc9-148">The component:</span></span>

* <span data-ttu-id="2ddc9-149">JavaScript 프롬프트를 통해 사용자 입력을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-149">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="2ddc9-150">처리를 위해 구성 요소에 텍스트를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-150">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="2ddc9-151">는 DOM과 상호 작용 하 여 시작 메시지를 표시 하는 두 번째 JavaScript 함수를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-151">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="2ddc9-152">*Pages/JSInterop*:</span><span class="sxs-lookup"><span data-stu-id="2ddc9-152">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="2ddc9-153">구성 `TriggerJsPrompt` 요소의 **트리거 javascript 프롬프트** 단추를 선택 하 여를 실행 하면 `showPrompt` *wwwroot/exampleJsInterop* 파일에 제공 된 javascript 함수가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-153">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="2ddc9-154">함수 `showPrompt` 는 HTML로 인코딩하고 구성 요소에 반환 되는 사용자 입력 (사용자 이름)을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-154">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="2ddc9-155">이 `name`구성 요소는 사용자의 이름을 지역 변수에 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-155">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="2ddc9-156">에 `name` 저장 된 문자열은 JavaScript `displayWelcome`함수에 전달 되는 환영 메시지에 통합 됩니다 .이 함수는 환영 메시지를 머리글 태그로 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-156">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="2ddc9-157">Void JavaScript 함수 호출</span><span class="sxs-lookup"><span data-stu-id="2ddc9-157">Call a void JavaScript function</span></span>

<span data-ttu-id="2ddc9-158">[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 또는 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) 를 반환 하는 JavaScript 함수는 `IJSRuntime.InvokeVoidAsync`를 사용 하 여 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-158">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-blazor-app-is-prerendering"></a><span data-ttu-id="2ddc9-159">Blazor 앱이 사전 렌더링 되는 경우 검색</span><span class="sxs-lookup"><span data-stu-id="2ddc9-159">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="2ddc9-160">요소에 대 한 참조 캡처</span><span class="sxs-lookup"><span data-stu-id="2ddc9-160">Capture references to elements</span></span>

<span data-ttu-id="2ddc9-161">일부 [JavaScript interop](xref:blazor/javascript-interop) 시나리오에서는 HTML 요소에 대 한 참조가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-161">Some [JavaScript interop](xref:blazor/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="2ddc9-162">예를 들어, UI 라이브러리에서 초기화를 위해 요소 참조를 요구 하거나 또는 `focus` `play`와 같은 요소에서 명령 같은 api를 호출 해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-162">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="2ddc9-163">다음 방법을 사용 하 여 구성 요소의 HTML 요소에 대 한 참조를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-163">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="2ddc9-164">HTML 요소에 특성을 추가 합니다. `@ref`</span><span class="sxs-lookup"><span data-stu-id="2ddc9-164">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="2ddc9-165">이름이 `ElementReference` 특성`@ref` 의 값과 일치 하는 형식의 필드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-165">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="2ddc9-166">다음 예제에서는 `username` `<input>` 요소에 대 한 참조를 캡처하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-166">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> <span data-ttu-id="2ddc9-167">캡처한 요소 참조를 DOM을 채우는 방법으로 사용 **하지** 마십시오.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-167">Do **not** use captured element references as a way of populating the DOM.</span></span> <span data-ttu-id="2ddc9-168">이렇게 하면 선언적 렌더링 모델을 방해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-168">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="2ddc9-169">.Net 코드와 관련 하 여 `ElementReference` 은 불투명 핸들입니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-169">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="2ddc9-170">에서 수행할 수 있는 *유일한* 작업 `ElementReference` 은 javascript interop를 통해 javascript 코드에 전달 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-170">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="2ddc9-171">이렇게 하면 JavaScript 쪽 코드는 일반 DOM api에서 사용할 수 `HTMLElement` 있는 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-171">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="2ddc9-172">예를 들어 다음 코드는 요소에 포커스를 설정할 수 있도록 하는 .NET 확장 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-172">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="2ddc9-173">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="2ddc9-173">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="2ddc9-174">및 `IJSRuntime.InvokeAsync<T>` 를 사용 `exampleJsFunctions.focusElement` 하 여 `ElementReference` 를 호출 하 고 요소에 포커스를 둡니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-174">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementReference` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="2ddc9-175">확장 메서드를 사용 하 여 요소에 포커스를 부여 하려면 `IJSRuntime` 인스턴스를 수신 하는 정적 확장 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-175">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="2ddc9-176">메서드는 개체에서 직접 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-176">The method is called directly on the object.</span></span> <span data-ttu-id="2ddc9-177">다음 예제에서는 `JsInteropClasses` 네임 스페이스에서 정적 `Focus` 메서드를 사용할 수 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-177">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> <span data-ttu-id="2ddc9-178">변수 `username` 는 구성 요소가 렌더링 된 후에만 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-178">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="2ddc9-179">Javascript 코드에 `ElementReference` 채워지지 않은이 전달 되 면 javascript 코드는 `null`값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-179">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="2ddc9-180">구성 요소가 렌더링을 완료 한 후 요소 참조를 조작 하려면 (요소에 초기 포커스를 설정 하기 위해) `OnAfterRenderAsync` 또는 `OnAfterRender` [구성 요소 수명 주기 메서드](xref:blazor/components#lifecycle-methods)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-180">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="2ddc9-181">JavaScript 함수에서 .NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="2ddc9-181">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="2ddc9-182">정적 .NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="2ddc9-182">Static .NET method call</span></span>

<span data-ttu-id="2ddc9-183">JavaScript에서 정적 .net 메서드를 호출 하려면 `DotNet.invokeMethod` 또는 `DotNet.invokeMethodAsync` 함수를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-183">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="2ddc9-184">호출할 정적 메서드의 식별자, 함수를 포함 하는 어셈블리의 이름 및 인수를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-184">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="2ddc9-185">Blazor 서버 시나리오를 지원 하기 위해 비동기 버전이 선호 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-185">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="2ddc9-186">JavaScript에서 .net 메서드를 호출 하려면 .net 메서드가 public, static 및 특성을 `[JSInvokable]` 가져야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-186">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="2ddc9-187">기본적으로 메서드 식별자는 메서드 이름 이지만 생성자를 `JSInvokableAttribute` 사용 하 여 다른 식별자를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-187">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="2ddc9-188">Open 제네릭 메서드를 호출 하는 것은 현재 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-188">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="2ddc9-189">샘플 앱에는의 C# `int`배열을 반환 하는 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-189">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="2ddc9-190">`JSInvokable` 특성은 메서드에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-190">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="2ddc9-191">*Pages/JsInterop*:</span><span class="sxs-lookup"><span data-stu-id="2ddc9-191">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="2ddc9-192">클라이언트에 제공 된 JavaScript는 .Net C# 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-192">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="2ddc9-193">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="2ddc9-193">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="2ddc9-194">**.Net 정적 메서드 ReturnArrayAsync 트리거** 단추를 선택한 경우 브라우저의 웹 개발자 도구에서 콘솔 출력을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-194">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="2ddc9-195">콘솔 출력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-195">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="2ddc9-196">네 번째 배열 값은에서`data.push(4);` `ReturnArrayAsync`반환 된 배열 ()로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-196">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="2ddc9-197">인스턴스 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="2ddc9-197">Instance method call</span></span>

<span data-ttu-id="2ddc9-198">JavaScript에서 .NET 인스턴스 메서드를 호출할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-198">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="2ddc9-199">JavaScript에서 .NET 인스턴스 메서드를 호출 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-199">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="2ddc9-200">`DotNetObjectReference` 인스턴스에 래핑하여 .net 인스턴스를 JavaScript에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-200">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="2ddc9-201">.NET 인스턴스는 JavaScript에 대 한 참조로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-201">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="2ddc9-202">`invokeMethod` 또는`invokeMethodAsync` 함수를 사용 하 여 인스턴스에서 .net 인스턴스 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-202">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="2ddc9-203">JavaScript에서 다른 .NET 메서드를 호출할 때 .NET 인스턴스도 인수로 전달 될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-203">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="2ddc9-204">샘플 앱은 클라이언트 쪽 콘솔에 메시지를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-204">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="2ddc9-205">샘플 앱에서 보여 주는 다음 예제에서는 브라우저의 개발자 도구에서 브라우저의 콘솔 출력을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-205">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="2ddc9-206">**HelloHelper .net 인스턴스 메서드 SayHello** 단추를 선택 `ExampleJsInterop.CallHelloHelperSayHello` 하면이 호출 되 고 메서드에 이름이 `Blazor`전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-206">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="2ddc9-207">*Pages/JsInterop*:</span><span class="sxs-lookup"><span data-stu-id="2ddc9-207">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="2ddc9-208">`CallHelloHelperSayHello``sayHello` 의`HelloHelper`새 인스턴스를 사용 하 여 JavaScript 함수를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-208">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="2ddc9-209">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ddc9-209">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="2ddc9-210">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="2ddc9-210">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="2ddc9-211">이름은 `HelloHelper.Name` 속성을 설정 하 `HelloHelper`는의 생성자에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-211">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="2ddc9-212">Javascript 함수가 `sayHello` 실행 되 면에서 `HelloHelper.SayHello` javascript 함수에 `Hello, {Name}!` 의해 콘솔에 기록 된 메시지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-212">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="2ddc9-213">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ddc9-213">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="2ddc9-214">브라우저의 웹 개발자 도구에 있는 콘솔 출력:</span><span class="sxs-lookup"><span data-stu-id="2ddc9-214">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="2ddc9-215">클래스 라이브러리의 interop 코드 공유</span><span class="sxs-lookup"><span data-stu-id="2ddc9-215">Share interop code in a class library</span></span>

<span data-ttu-id="2ddc9-216">JavaScript interop 코드는 클래스 라이브러리에 포함 될 수 있으며,이를 통해 NuGet 패키지의 코드를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-216">JavaScript interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="2ddc9-217">클래스 라이브러리는 빌드된 어셈블리에 포함 된 JavaScript 리소스를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-217">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="2ddc9-218">JavaScript 파일은 *wwwroot* 폴더에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-218">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="2ddc9-219">도구는 라이브러리가 빌드될 때 리소스를 포함 하는 작업을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-219">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="2ddc9-220">빌드된 NuGet 패키지는 NuGet 패키지를 참조 하는 것과 동일한 방식으로 앱의 프로젝트 파일에서 참조 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-220">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="2ddc9-221">패키지가 복원 된 후에는 앱 코드가 JavaScript를로 호출할 수 있습니다 C#.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-221">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="2ddc9-222">자세한 내용은 <xref:blazor/class-libraries>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-222">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="2ddc9-223">JS interop 호출 강화</span><span class="sxs-lookup"><span data-stu-id="2ddc9-223">Harden JS interop calls</span></span>

<span data-ttu-id="2ddc9-224">JS interop는 네트워킹 오류로 인해 실패할 수 있으며 신뢰할 수 없는 것으로 처리 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-224">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="2ddc9-225">기본적으로 Blazor 서버 앱은 1 분 후 서버에서 JS interop 호출을 시간 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-225">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="2ddc9-226">앱에서 10 초 등의 적극적 시간 제한을 허용할 수 있는 경우 다음 방법 중 하나를 사용 하 여 시간 제한을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-226">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="2ddc9-227">전역에서 `Startup.ConfigureServices`제한 시간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-227">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="2ddc9-228">구성 요소 코드에서 호출 마다 단일 호출에서 제한 시간을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-228">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="2ddc9-229">리소스 소모에 대 한 자세한 내용은을 <xref:security/blazor/server>참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="2ddc9-229">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>
