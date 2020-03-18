---
title: ASP.NET Core Blazor의 JavaScript 함수에서 .NET 메서드 호출
author: guardrex
description: Blazor 앱의 JavaScript 함수에서 .NET 메서드를 호출하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: f4964341e261c65269eedafbbd6e676c1054f427
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647409"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="66adf-103">ASP.NET Core Blazor의 JavaScript 함수에서 .NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="66adf-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="66adf-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="66adf-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="66adf-105">Blazor 앱은 .NET 메서드에서 JavaScript 함수를 호출하고 JavaScript 함수에서 .NET 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="66adf-106">이러한 시나리오를 *JavaScript 상호 운용성*(*JS interop*)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="66adf-107">이 문서에서는 JavaScript에서 .NET 메서드를 호출하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="66adf-108">.NET에서 JavaScript 함수를 호출하는 방법에 대한 내용은 <xref:blazor/call-javascript-from-dotnet>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66adf-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="66adf-109">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="66adf-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="66adf-110">정적 .NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="66adf-110">Static .NET method call</span></span>

<span data-ttu-id="66adf-111">JavaScript에서 정적 .NET 메서드를 호출하려면 `DotNet.invokeMethod` 또는 `DotNet.invokeMethodAsync` 함수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="66adf-112">호출할 정적 메서드의 식별자, 함수를 포함하는 어셈블리의 이름 및 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="66adf-113">비동기 버전은 Blazor 서버 시나리오를 지원하기 위해 선호됩니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="66adf-114">.NET 메서드는 public, static이며 `[JSInvokable]` 특성이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="66adf-115">개방형 제네릭 메서드를 호출하는 것은 현재 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="66adf-116">샘플 앱에는 `int` 배열을 반환하기 위한 C# 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="66adf-117">`JSInvokable` 특성이 메서드에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="66adf-118">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="66adf-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="66adf-119">클라이언트에 제공된 JavaScript는 C# .Net 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="66adf-120">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="66adf-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="66adf-121">**.NET 정적 메서드 ReturnArrayAsync 트리거** 단추를 선택한 경우 브라우저의 웹 개발자 도구에서 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="66adf-122">콘솔 출력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="66adf-123">네 번째 배열 값은 `ReturnArrayAsync`에서 반환된 배열(`data.push(4);`)로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="66adf-124">기본적으로 메서드 식별자는 메서드 이름이지만 `JSInvokableAttribute` 생성자를 사용하여 다른 식별자를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="66adf-125">클라이언트 쪽 JavaScript 파일에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="66adf-126">인스턴스 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="66adf-126">Instance method call</span></span>

<span data-ttu-id="66adf-127">JavaScript에서 .NET 인스턴스 메서드를 호출할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="66adf-128">JavaScript에서 .NET 인스턴스 메서드를 호출하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="66adf-129">JavaScript에 대한 참조로 .NET 인스턴스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="66adf-130">`DotNetObjectReference.Create`에 대한 정적 호출을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="66adf-131">인스턴스를 `DotNetObjectReference` 인스턴스로 래핑하고 `DotNetObjectReference` 인스턴스에서 `Create`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="66adf-132">`DotNetObjectReference` 개체를 삭제합니다. 이 섹션의 뒷부분을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66adf-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="66adf-133">`invokeMethod` 또는 `invokeMethodAsync` 함수를 사용하여 인스턴스에서 .NET 인스턴스 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="66adf-134">JavaScript에서 다른 .NET 메서드를 호출할 때 .NET 인스턴스를 인수로 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="66adf-135">샘플 앱은 클라이언트 쪽 콘솔에 메시지를 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="66adf-136">샘플 앱에서 보여 주는 다음 예제에서는 브라우저의 개발자 도구에서 브라우저의 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="66adf-137">**.NET 인스턴스 메서드 HelloHelper.SayHello 트리거** 단추를 선택하면 `ExampleJsInterop.CallHelloHelperSayHello`가 호출되고 메서드에 이름 `Blazor`가 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="66adf-138">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="66adf-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="66adf-139">`CallHelloHelperSayHello`는 `HelloHelper`의 새 인스턴스를 사용하여 JavaScript 함수 `sayHello`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="66adf-140">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="66adf-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="66adf-141">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="66adf-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="66adf-142">이 이름은 `HelloHelper.Name` 속성을 설정하는 `HelloHelper`의 생성자에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="66adf-143">JavaScript 함수 `sayHello`가 실행되면 `HelloHelper.SayHello`는 JavaScript 함수를 통해 콘솔에 기록되는 `Hello, {Name}!` 메시지를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="66adf-144">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="66adf-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="66adf-145">브라우저의 웹 개발자 도구에 있는 콘솔 출력:</span><span class="sxs-lookup"><span data-stu-id="66adf-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="66adf-146">`DotNetObjectReference`를 만드는 구성 요소에서 메모리 누수를 방지하고 가비지 수집을 허용하려면 `DotNetObjectReference` 인스턴스를 만든 클래스에서 개체를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

```csharp
public class ExampleJsInterop : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private DotNetObjectReference<HelloHelper> _objRef;

    public ExampleJsInterop(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public ValueTask<string> CallHelloHelperSayHello(string name)
    {
        _objRef = DotNetObjectReference.Create(new HelloHelper(name));

        return _jsRuntime.InvokeAsync<string>(
            "exampleJsFunctions.sayHello",
            _objRef);
    }

    public void Dispose()
    {
        _objRef?.Dispose();
    }
}
```
  
<span data-ttu-id="66adf-147">`ExampleJsInterop` 클래스에 표시된 이전 패턴은 구성 요소에서 구현될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66adf-147">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>
  
```razor
@page "/JSInteropComponent"
@using BlazorSample.JsInteropClasses
@implements IDisposable
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    private DotNetObjectReference<HelloHelper> _objRef;

    public async Task TriggerNetInstanceMethod()
    {
        _objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

        await JSRuntime.InvokeAsync<string>(
            "exampleJsFunctions.sayHello",
            _objRef);
    }

    public void Dispose()
    {
        _objRef?.Dispose();
    }
}
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a><span data-ttu-id="66adf-148">추가 자료</span><span class="sxs-lookup"><span data-stu-id="66adf-148">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="66adf-149">InteropComponent.razor 예제(dotnet/AspNetCore GitHub 리포지토리, 3.1 릴리스 분기)</span><span class="sxs-lookup"><span data-stu-id="66adf-149">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="66adf-150">[Blazor 서버 앱에서 대용량 데이터 전송 수행](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="66adf-150">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
