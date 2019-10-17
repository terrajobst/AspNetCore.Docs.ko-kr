---
title: ASP.NET Core Blazor JavaScript interop
author: guardrex
description: Blazor apps에서 JavaScript의 .NET 및 .NET 메서드에서 JavaScript 함수를 호출 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/javascript-interop
ms.openlocfilehash: b4776a20c6da6c722d2c057d19863c570f530a21
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391059"
---
# <a name="aspnet-core-blazor-javascript-interop"></a>ASP.NET Core Blazor JavaScript interop

[Javier Calvarro e](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)및 [luke latham 문자](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 앱은 JavaScript 코드에서 .NET 및 .NET 메서드의 JavaScript 함수를 호출할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="invoke-javascript-functions-from-net-methods"></a>.NET 메서드에서 JavaScript 함수 호출

.NET 코드에서 JavaScript 함수를 호출 해야 하는 경우가 있습니다. 예를 들어 JavaScript 호출은 JavaScript 라이브러리의 브라우저 기능 또는 기능을 앱에 노출할 수 있습니다.

.NET에서 JavaScript를 호출 하려면 `IJSRuntime` 추상화를 사용 합니다. @No__t-0 메서드는 임의의 JSON 직렬화 가능 인수와 함께 호출할 JavaScript 함수의 식별자를 사용 합니다. 함수 식별자는 전역 범위 (`window`)를 기준으로 합니다. @No__t-0을 호출 하려는 경우 식별자는-1 @no__t 됩니다. 호출 되기 전에 함수를 등록할 필요가 없습니다. 반환 형식 `T`도 JSON serializable 이어야 합니다.

Blazor 서버 앱의 경우:

* Blazor 서버 앱에서 여러 사용자 요청을 처리 합니다. JavaScript 함수를 호출 하려면 구성 요소에서 `JSRuntime.Current`을 호출 하지 마세요.
* @No__t-0 추상화를 삽입 하 고 삽입 된 개체를 사용 하 여 JavaScript interop 호출을 실행 합니다.
* Blazor 앱은 렌더링 되지 않지만 브라우저와의 연결이 설정 되지 않았기 때문에 JavaScript를 호출할 수 없습니다. 자세한 내용은 [Blazor 앱이 사전 렌더링 되는 경우 검색](#detect-when-a-blazor-app-is-prerendering) 섹션을 참조 하세요.

다음 예제는 실험적 JavaScript 기반 디코더 인 [Textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder)를 기반으로 합니다. 이 예제에서는 C# 메서드에서 JavaScript 함수를 호출 하는 방법을 보여 줍니다. JavaScript 함수는 C# 메서드에서 바이트 배열을 받아 배열을 디코딩하고 표시를 위해 구성 요소에 텍스트를 반환 합니다.

*Wwwroot/index.html* (Blazor Weasembom) 또는 *Pages/_Host* (Blazor Server)의 `<head>` 요소 내에서-3을 @no__t 사용 하 여 전달 된 배열을 디코딩하는 함수를 제공 합니다.

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

앞의 예제에 표시 된 코드와 같은 JavaScript 코드는 스크립트 파일에 대 한 참조를 사용 하 여 JavaScript 파일 (*.js*)에서 로드할 수도 있습니다.

```html
<script src="exampleJsInterop.js"></script>
```

다음 구성 요소:

* 구성 요소 단추 (**배열 변환**)가 선택 된 경우 `JsRuntime`을 사용 하 여 `ConvertArray` JavaScript 함수를 호출 합니다.
* JavaScript 함수를 호출한 후에는 전달 된 배열이 문자열로 변환 됩니다. 문자열은 표시를 위해 구성 요소로 반환 됩니다.

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

@No__t-0 추상화를 사용 하려면 다음 방법 중 하나를 채택 합니다.

* Razor 구성 요소 (*razor*)에 `IJSRuntime` 추상화를 삽입 합니다.

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* @No__t-0 추상화를 클래스 (*.cs*)에 삽입 합니다.

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)를 사용 하 여 동적 콘텐츠를 생성 하려면 `[Inject]` 특성을 사용 합니다.

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

이 항목과 함께 제공 되는 클라이언트 쪽 샘플 앱에서는 사용자 입력을 받고 환영 메시지를 표시 하기 위해 DOM과 상호 작용 하는 두 가지 JavaScript 함수를 앱에서 사용할 수 있습니다.

* `showPrompt` &ndash;은 사용자 입력 (사용자 이름)을 수락 하 고 이름을 호출자에 게 반환 하 라는 프롬프트를 생성 합니다.
* `displayWelcome` &ndash;은 `welcome`의 `id` 인 DOM 개체에 대 한 시작 메시지를 호출자에 게 할당 합니다.

*wwwroot/exampleJsInterop*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

*Wwwroot/index.html* 파일 (Blazor Weasembmboms) 또는 *Pages/_Host* 파일 (Blazor Server)에서 JavaScript 파일을 참조 하는 `<script>` 태그를 저장 합니다.

*wwwroot/index.html* (Blazor):

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

*Pages/_Host* (Blazor 서버):

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

@No__t-1 태그는 동적으로 업데이트할 수 없으므로 구성 요소 파일에 `<script>` 태그를 넣지 마세요.

.NET 메서드는 `IJSRuntime.InvokeAsync<T>`을 호출 하 여 *exampleJsInterop* 파일의 JavaScript 함수를 사용 하 여 상호 운용 합니다.

@No__t-0 추상화는 Blazor 서버 시나리오를 허용 하는 비동기입니다. 앱이 Blazor Weasembomapp이 고 JavaScript 함수를 동기적으로 호출 하려는 경우 다운 캐스트는-0을 @no__t 하 고 대신 `Invoke<T>`을 호출 합니다. 대부분의 JavaScript interop 라이브러리는 비동기 Api를 사용 하 여 모든 시나리오에서 라이브러리를 사용할 수 있도록 하는 것이 좋습니다.

샘플 앱에는 JavaScript interop를 설명 하는 구성 요소가 포함 되어 있습니다. 구성 요소:

* JavaScript 프롬프트를 통해 사용자 입력을 받습니다.
* 처리를 위해 구성 요소에 텍스트를 반환 합니다.
* 는 DOM과 상호 작용 하 여 시작 메시지를 표시 하는 두 번째 JavaScript 함수를 호출 합니다.

*Pages/JSInterop*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. 구성 요소의 **트리거 Javascript 프롬프트** 단추를 선택 하 여 `TriggerJsPrompt`을 실행 하면 *wwwroot/exampleJsInterop* 파일에 제공 된 JavaScript `showPrompt` 함수가 호출 됩니다.
1. @No__t-0 함수는 HTML로 인코딩하고 구성 요소에 반환 되는 사용자 입력 (사용자 이름)을 허용 합니다. 이 구성 요소는 사용자의 이름을 지역 변수에 저장 합니다. `name`입니다.
1. @No__t-0에 저장 된 문자열은 환영 메시지에 통합 됩니다 .이 메시지는 JavaScript 함수에 전달 되 고 `displayWelcome`은 시작 메시지를 머리글 태그로 렌더링 합니다.

## <a name="call-a-void-javascript-function"></a>Void JavaScript 함수 호출

[Void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 또는 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) 를 반환 하는 JavaScript 함수는 `IJSRuntime.InvokeVoidAsync`를 사용 하 여 호출 됩니다.

## <a name="detect-when-a-blazor-app-is-prerendering"></a>Blazor 앱이 사전 렌더링 되는 경우 검색
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>요소에 대 한 참조 캡처

일부 [JavaScript interop](xref:blazor/javascript-interop) 시나리오에서는 HTML 요소에 대 한 참조가 필요 합니다. 예를 들어, UI 라이브러리에서 초기화를 위해 요소 참조를 요구 하거나, `focus` 또는 `play`과 같은 명령 Api를 요소에서 호출 해야 할 수 있습니다.

다음 방법을 사용 하 여 구성 요소의 HTML 요소에 대 한 참조를 캡처합니다.

* @No__t-0 특성을 HTML 요소에 추가 합니다.
* @No__t-1 특성의 값과 이름이 일치 하는 `ElementReference` 형식의 필드를 정의 합니다.

다음 예에서는 `username` @no__t 요소에 대 한 참조를 캡처하는 방법을 보여 줍니다.

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> 캡처한 요소 참조를 DOM을 채우는 방법으로 사용 **하지** 마십시오. 이렇게 하면 선언적 렌더링 모델을 방해할 수 있습니다.

.NET 코드와 관련 하 여 `ElementReference`은 불투명 핸들입니다. @No__t-1로 수행할 수 있는 *유일한* 작업은 javascript interop를 통해 javascript 코드에 전달 하는 것입니다. 이렇게 하면 JavaScript 쪽 코드가 일반적인 DOM Api에서 사용할 수 있는 `HTMLElement` 인스턴스를 수신 합니다.

예를 들어 다음 코드는 요소에 포커스를 설정할 수 있도록 하는 .NET 확장 메서드를 정의 합니다.

*exampleJsInterop*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

@No__t-0을 사용 하 고 `ElementReference`를 사용 하 여 `exampleJsFunctions.focusElement`을 호출 하 여 요소에 포커스를 둡니다.

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

확장 메서드를 사용 하 여 요소에 초점을 맞추기 위해 `IJSRuntime` 인스턴스를 수신 하는 정적 확장 메서드를 만듭니다.

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

메서드는 개체에서 직접 호출 됩니다. 다음 예에서는 `JsInteropClasses` 네임 스페이스에서 정적 `Focus` 메서드를 사용할 수 있다고 가정 합니다.

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> @No__t-0 변수는 구성 요소가 렌더링 된 후에만 채워집니다. 채워지지 않은 `ElementReference`이 JavaScript 코드에 전달 되 면 JavaScript 코드는 `null` 값을 받습니다. 구성 요소에서 렌더링을 완료 한 후 요소 참조를 조작 하려면 (요소에 초기 포커스를 설정 하려면) `OnAfterRenderAsync` 또는 `OnAfterRender` [구성 요소 수명 주기 방법을](xref:blazor/components#lifecycle-methods)사용 합니다.

## <a name="invoke-net-methods-from-javascript-functions"></a>JavaScript 함수에서 .NET 메서드 호출

### <a name="static-net-method-call"></a>정적 .NET 메서드 호출

JavaScript에서 정적 .NET 메서드를 호출 하려면 `DotNet.invokeMethod` 또는 `DotNet.invokeMethodAsync` 함수를 사용 합니다. 호출할 정적 메서드의 식별자, 함수를 포함 하는 어셈블리의 이름 및 인수를 전달 합니다. Blazor 서버 시나리오를 지원 하기 위해 비동기 버전이 선호 됩니다. JavaScript에서 .NET 메서드를 호출 하려면 .NET 메서드가 public, static 및 `[JSInvokable]` 특성을 가져야 합니다. 기본적으로 메서드 식별자는 메서드 이름 이지만 `JSInvokableAttribute` 생성자를 사용 하 여 다른 식별자를 지정할 수 있습니다. Open 제네릭 메서드를 호출 하는 것은 현재 지원 되지 않습니다.

샘플 앱에는 @no__t C# -1의 배열을 반환 하는 메서드가 포함 되어 있습니다. @No__t-0 특성이 메서드에 적용 됩니다.

*Pages/JsInterop*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

클라이언트에 제공 된 JavaScript는 .Net C# 메서드를 호출 합니다.

*wwwroot/exampleJsInterop*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

**.Net 정적 메서드 ReturnArrayAsync 트리거** 단추를 선택한 경우 브라우저의 웹 개발자 도구에서 콘솔 출력을 검사 합니다.

콘솔 출력은 다음과 같습니다.

```console
Array(4) [ 1, 2, 3, 4 ]
```

네 번째 배열 값은-1 @no__t에서 반환 된 배열 (`data.push(4);`)으로 푸시됩니다.

### <a name="instance-method-call"></a>인스턴스 메서드 호출

JavaScript에서 .NET 인스턴스 메서드를 호출할 수도 있습니다. JavaScript에서 .NET 인스턴스 메서드를 호출 하려면 다음을 수행 합니다.

* @No__t-0 인스턴스로 래핑하여 .NET 인스턴스를 JavaScript에 전달 합니다. .NET 인스턴스는 JavaScript에 대 한 참조로 전달 됩니다.
* @No__t-0 또는 `invokeMethodAsync` 함수를 사용 하 여 인스턴스에서 .NET 인스턴스 메서드를 호출 합니다. JavaScript에서 다른 .NET 메서드를 호출할 때 .NET 인스턴스도 인수로 전달 될 수도 있습니다.

> [!NOTE]
> 샘플 앱은 클라이언트 쪽 콘솔에 메시지를 기록 합니다. 샘플 앱에서 보여 주는 다음 예제에서는 브라우저의 개발자 도구에서 브라우저의 콘솔 출력을 검사 합니다.

**HelloHelper .net 인스턴스 메서드 SayHello** 단추를 선택 하면 `ExampleJsInterop.CallHelloHelperSayHello`이 호출 되 고 이름 `Blazor`가 메서드에 전달 됩니다.

*Pages/JsInterop*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello`은 `HelloHelper`의 새 인스턴스를 사용 하 여-1 @no__t JavaScript 함수를 호출 합니다.

*JsInteropClasses/ExampleJsInterop*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

이 이름은 `HelloHelper.Name` 속성을 설정 하는 `HelloHelper`의 생성자에 전달 됩니다. JavaScript 함수 `sayHello`이 실행 될 때 `HelloHelper.SayHello`은 JavaScript 함수에 의해 콘솔에 기록 되는 `Hello, {Name}!` 메시지를 반환 합니다.

*JsInteropClasses/HelloHelper*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

브라우저의 웹 개발자 도구에 있는 콘솔 출력:

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a>클래스 라이브러리의 interop 코드 공유

JavaScript interop 코드는 클래스 라이브러리에 포함 될 수 있으며,이를 통해 NuGet 패키지의 코드를 공유할 수 있습니다.

클래스 라이브러리는 빌드된 어셈블리에 포함 된 JavaScript 리소스를 처리 합니다. JavaScript 파일은 *wwwroot* 폴더에 배치 됩니다. 도구는 라이브러리가 빌드될 때 리소스를 포함 하는 작업을 담당 합니다.

빌드된 NuGet 패키지는 NuGet 패키지를 참조 하는 것과 동일한 방식으로 앱의 프로젝트 파일에서 참조 됩니다. 패키지가 복원 된 후에는 앱 코드가 JavaScript를로 호출할 수 있습니다 C#.

자세한 내용은 <xref:blazor/class-libraries>을 참조하십시오.

## <a name="harden-js-interop-calls"></a>JS interop 호출 강화

JS interop는 네트워킹 오류로 인해 실패할 수 있으며 신뢰할 수 없는 것으로 처리 되어야 합니다. 기본적으로 Blazor 서버 앱은 1 분 후 서버에서 JS interop 호출을 시간 제한 합니다. 앱에서 10 초 등의 적극적 시간 제한을 허용할 수 있는 경우 다음 방법 중 하나를 사용 하 여 시간 제한을 설정 합니다.

* @No__t-0에서 전역적으로 제한 시간을 지정 합니다.

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* 구성 요소 코드에서 호출 마다 단일 호출에서 제한 시간을 지정할 수 있습니다.

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

리소스 소모에 대 한 자세한 내용은 <xref:security/blazor/server>을 참조 하세요.
