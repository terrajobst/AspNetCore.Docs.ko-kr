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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="4322c-103">ASP.NET Core Blazor 호스팅 모델 구성</span><span class="sxs-lookup"><span data-stu-id="4322c-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="4322c-104">작성자: [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4322c-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="4322c-105">이 문서에서는 호스팅 모델 구성에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-105">This article covers hosting model configuration.</span></span>

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a><span data-ttu-id="4322c-106">Blazor 서버</span><span class="sxs-lookup"><span data-stu-id="4322c-106">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="4322c-107">UI에 연결 상태 반영</span><span class="sxs-lookup"><span data-stu-id="4322c-107">Reflect the connection state in the UI</span></span>

<span data-ttu-id="4322c-108">클라이언트에서 연결이 끊어진 것을 감지하면, 클라이언트가 다시 연결하는 동안 기본 UI가 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-108">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="4322c-109">다시 연결하지 못한 경우 사용자에게 다시 시도 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-109">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="4322c-110">UI를 사용자 지정하려면 *_Host.cshtml* Razor 페이지의 `<body>`에서 `components-reconnect-modal`의 `id`를 사용하여 요소를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-110">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="4322c-111">다음 표에서는 `components-reconnect-modal` 요소에 적용된 CSS 클래스에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-111">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="4322c-112">CSS 클래스</span><span class="sxs-lookup"><span data-stu-id="4322c-112">CSS class</span></span>                       | <span data-ttu-id="4322c-113">표시&hellip;</span><span class="sxs-lookup"><span data-stu-id="4322c-113">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="4322c-114">연결이 끊어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-114">A lost connection.</span></span> <span data-ttu-id="4322c-115">클라이언트가 다시 연결하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-115">The client is attempting to reconnect.</span></span> <span data-ttu-id="4322c-116">모달을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-116">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="4322c-117">서버에 대해 활성 연결이 다시 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-117">An active connection is re-established to the server.</span></span> <span data-ttu-id="4322c-118">모달을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-118">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="4322c-119">네트워크 오류로 인해 다시 연결하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-119">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="4322c-120">다시 연결하려면 `window.Blazor.reconnect()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-120">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="4322c-121">다시 연결이 거부되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-121">Reconnection rejected.</span></span> <span data-ttu-id="4322c-122">서버에 접근했지만 연결이 거부되었으며, 서버의 사용자 상태가 손실되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-122">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="4322c-123">앱을 다시 로드하려면 `location.reload()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-123">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="4322c-124">이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-124">This connection state may result when:</span></span><ul><li><span data-ttu-id="4322c-125">서버 쪽 회로에서 크래시가 발생한 경우</span><span class="sxs-lookup"><span data-stu-id="4322c-125">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="4322c-126">서버에서 사용자 상태를 삭제하기에 충분한 기간에 클라이언트 연결이 끊긴 경우.</span><span class="sxs-lookup"><span data-stu-id="4322c-126">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="4322c-127">사용자가 조작 중인 구성 요소 인스턴스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-127">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="4322c-128">서버가 다시 시작되었거나, 앱의 작업자 프로세스가 재활용된 경우</span><span class="sxs-lookup"><span data-stu-id="4322c-128">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="4322c-129">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="4322c-129">Render mode</span></span>

<span data-ttu-id="4322c-130">Blazor 서버 앱은 기본적으로 클라이언트가 서버에 연결되기 전에 서버에서 UI를 미리 렌더링하도록 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-130">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="4322c-131">이 옵션은 *_Host.cshtml* Razor 페이지에서 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-131">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="4322c-132">`RenderMode`는 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-132">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="4322c-133">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="4322c-133">Is prerendered into the page.</span></span>
* <span data-ttu-id="4322c-134">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="4322c-134">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="4322c-135">설명</span><span class="sxs-lookup"><span data-stu-id="4322c-135">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="4322c-136">구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-136">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4322c-137">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-137">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="4322c-138">Blazor 서버 앱의 표식을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-138">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4322c-139">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-139">Output from the component isn't included.</span></span> <span data-ttu-id="4322c-140">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-140">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="4322c-141">구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-141">Renders the component into static HTML.</span></span> |

<span data-ttu-id="4322c-142">정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-142">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="4322c-143">Razor 페이지 및 뷰에서 상태 저장 대화형 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="4322c-143">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="4322c-144">Razor 페이지 또는 뷰에 상태 저장 대화형 구성 요소를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-144">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="4322c-145">페이지 또는 뷰를 렌더링하는 경우와 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-145">When the page or view renders:</span></span>

* <span data-ttu-id="4322c-146">구성 요소가 페이지 또는 뷰와 함께 미리 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-146">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="4322c-147">미리 렌더링하는 데 사용된 초기 구성 요소 상태가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-147">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="4322c-148">SignalR 연결이 완료되면 새 구성 요소 상태가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-148">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="4322c-149">다음 Razor 페이지는 `Counter` 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-149">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="4322c-150">Razor 페이지 및 뷰에서 비대화형 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="4322c-150">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="4322c-151">다음 Razor 페이지에서 `Counter` 구성 요소는 폼을 통해 지정된 초기 값을 사용하여 정적으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-151">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="4322c-152">`MyComponent`가 정적으로 렌더링되므로 구성 요소는 대화형이 될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-152">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="4322c-153">Blazor 서버 앱에 적합하게 SignalR 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="4322c-153">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="4322c-154">Blazor 서버 앱에서 사용하는 SignalR 클라이언트를 구성해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-154">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="4322c-155">예를 들어 연결 문제를 진단하기 위해 SignalR 클라이언트에서 로깅을 구성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-155">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="4322c-156">*Pages/_Host.cshtml* 파일에서 SignalR 클라이언트를 구성하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-156">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="4322c-157">`blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-157">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="4322c-158">`Blazor.start`를 호출하고 SignalR 작성기를 지정하는 구성 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="4322c-158">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
