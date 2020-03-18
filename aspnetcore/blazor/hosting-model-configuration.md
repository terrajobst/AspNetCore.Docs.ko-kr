---
title: ASP.NET Core Blazor 호스팅 모델 구성
author: guardrex
description: Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: bd44643877e45c5b48b0972bcc2f637fbc5d98f2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646791"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor 호스팅 모델 구성

작성자: [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

이 문서에서는 호스팅 모델 구성에 대해 설명합니다.

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a>Blazor 서버

### <a name="reflect-the-connection-state-in-the-ui"></a>UI에 연결 상태 반영

클라이언트에서 연결이 끊어진 것을 감지하면, 클라이언트가 다시 연결하는 동안 기본 UI가 사용자에게 표시됩니다. 다시 연결하지 못한 경우 사용자에게 다시 시도 옵션이 제공됩니다.

UI를 사용자 지정하려면 *_Host.cshtml* Razor 페이지의 `<body>`에서 `components-reconnect-modal`의 `id`를 사용하여 요소를 정의합니다.

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

다음 표에서는 `components-reconnect-modal` 요소에 적용된 CSS 클래스에 대해 설명합니다.

| CSS 클래스                       | 표시&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | 연결이 끊어졌습니다. 클라이언트가 다시 연결하는 중입니다. 모달을 표시합니다. |
| `components-reconnect-hide`     | 서버에 대해 활성 연결이 다시 설정되었습니다. 모달을 숨깁니다. |
| `components-reconnect-failed`   | 네트워크 오류로 인해 다시 연결하지 못했습니다. 다시 연결하려면 `window.Blazor.reconnect()`를 호출합니다. |
| `components-reconnect-rejected` | 다시 연결이 거부되었습니다. 서버에 접근했지만 연결이 거부되었으며, 서버의 사용자 상태가 손실되었습니다. 앱을 다시 로드하려면 `location.reload()`를 호출합니다. 이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.<ul><li>서버 쪽 회로에서 크래시가 발생한 경우</li><li>서버에서 사용자 상태를 삭제하기에 충분한 기간에 클라이언트 연결이 끊긴 경우. 사용자가 조작 중인 구성 요소 인스턴스가 삭제됩니다.</li><li>서버가 다시 시작되었거나, 앱의 작업자 프로세스가 재활용된 경우</li></ul> |

### <a name="render-mode"></a>렌더링 모드

Blazor 서버 앱은 기본적으로 클라이언트가 서버에 연결되기 전에 서버에서 UI를 미리 렌더링하도록 설정되어 있습니다. 이 옵션은 *_Host.cshtml* Razor 페이지에서 설정합니다.

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`는 구성 요소에 대해 다음을 구성합니다.

* 페이지에 미리 렌더링할지 여부
* 페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부

| `RenderMode`        | 설명 |
| ------------------- | ----------- |
| `ServerPrerendered` | 구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
| `Server`            | Blazor 서버 앱의 표식을 렌더링합니다. 구성 요소의 출력은 포함되지 않습니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
| `Static`            | 구성 요소를 정적 HTML에 렌더링합니다. |

정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Razor 페이지 및 뷰에서 상태 저장 대화형 구성 요소 렌더링

Razor 페이지 또는 뷰에 상태 저장 대화형 구성 요소를 추가할 수 있습니다.

페이지 또는 뷰를 렌더링하는 경우와 관련해서 다음 사항을 확인합니다.

* 구성 요소가 페이지 또는 뷰와 함께 미리 렌더링됩니다.
* 미리 렌더링하는 데 사용된 초기 구성 요소 상태가 손실됩니다.
* SignalR 연결이 완료되면 새 구성 요소 상태가 생성됩니다.

다음 Razor 페이지는 `Counter` 구성 요소를 렌더링합니다.

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Razor 페이지 및 뷰에서 비대화형 구성 요소 렌더링

다음 Razor 페이지에서 `Counter` 구성 요소는 폼을 통해 지정된 초기 값을 사용하여 정적으로 렌더링됩니다.

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

`MyComponent`가 정적으로 렌더링되므로 구성 요소는 대화형이 될 수 없습니다.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Blazor 서버 앱에 적합하게 SignalR 클라이언트 구성

Blazor 서버 앱에서 사용하는 SignalR 클라이언트를 구성해야 할 수도 있습니다. 예를 들어 연결 문제를 진단하기 위해 SignalR 클라이언트에서 로깅을 구성하는 것이 좋습니다.

*Pages/_Host.cshtml* 파일에서 SignalR 클라이언트를 구성하려면 다음을 수행합니다.

* `blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.
* `Blazor.start`를 호출하고 SignalR 작성기를 지정하는 구성 개체를 전달합니다.

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```
