---
title: ASP.NET Core Blazor 호스팅 모델 구성
author: guardrex
description: Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합 하는 방법을 비롯 하 여 Blazor 호스팅 모델 구성에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 91dd1aa32bb5165845eb4794377a50ccbd01adc3
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77214942"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor 호스팅 모델 구성

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

이 문서에서는 호스팅 모델 구성에 대해 설명 합니다.

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a>Blazor 서버

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a>Razor Pages 및 MVC 앱에 Razor 구성 요소 통합

#### <a name="use-components-in-pages-and-views"></a>페이지 및 뷰에서 구성 요소 사용

기존 Razor Pages 또는 MVC 앱은 Razor 구성 요소를 페이지 및 뷰에 통합할 수 있습니다.

1. 앱의 레이아웃 파일 ( *_Layout cshtml*)에서:

   * `<head>` 요소에 다음 `<base>` 태그를 추가 합니다.

     ```html
     <base href="~/" />
     ```

     앞의 예제에서 `href` 값 ( *앱 기본 경로*)은 앱이 루트 URL 경로 (`/`)에 있는 것으로 가정 합니다. 앱이 하위 응용 프로그램 인 경우 <xref:host-and-deploy/blazor/index#app-base-path> 문서의 *앱 기준 경로* 섹션에 있는 지침을 따르세요.

     *_Layout cshtml* 파일은 Razor Pages 앱의 *Pages/SHARED* 폴더 또는 MVC 앱의 *Views/shared* 폴더에 있습니다.

   * 닫는 `</body>` 태그 바로 앞에 *blazor* 스크립트에 대 한 `<script>` 태그를 추가 합니다.

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     프레임 워크는 *blazor* 스크립트를 앱에 추가 합니다. 앱에 스크립트를 수동으로 추가할 필요는 없습니다.

1. 다음 콘텐츠를 사용 하 여 프로젝트의 루트 폴더에 *_Imports razor* 파일을 추가 합니다 (마지막 네임 스페이스 `MyAppNamespace`를 앱의 네임 스페이스로 변경).

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. `Startup.ConfigureServices`에서 Blazor 서버 서비스를 등록 합니다.

   ```csharp
   services.AddServerSideBlazor();
   ```

1. `Startup.Configure`에서 `app.UseEndpoints`에 Blazor Hub 끝점을 추가 합니다.

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. 모든 페이지 또는 보기에 구성 요소를 통합 합니다. 자세한 내용은 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> 문서의 *Razor Pages 및 MVC 앱에 구성 요소 통합* 섹션을 참조 하세요.

#### <a name="use-routable-components-in-a-razor-pages-app"></a>Razor Pages 앱에서 라우팅 가능한 구성 요소 사용

Razor Pages 앱에서 라우팅할 수 있는 Razor 구성 요소를 지원 하려면 다음을 수행 합니다.

1. [페이지 및 뷰에서 구성 요소 사용](#use-components-in-pages-and-views) 섹션의 지침을 따릅니다.

1. 다음 콘텐츠를 사용 하 여 프로젝트의 루트에 *응용 프로그램 razor* 파일을 추가 합니다.

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. 다음 내용을 사용 하 여 *Pages* 폴더에 *_Host cshtml* 파일을 추가 합니다.

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   구성 요소는 해당 레이아웃에 대해 공유 *_Layout cshtml* 파일을 사용 합니다.

1. _Host에 대 한 우선 순위가 낮은 경로를 `Startup.Configure`의 끝점 구성에 추가 *합니다* .

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. 라우팅할 수 있는 구성 요소를 앱에 추가 합니다. 예를 들어:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   사용자 지정 폴더를 사용 하 여 앱의 구성 요소를 저장 하는 경우 폴더를 나타내는 네임 스페이스를 *Pages/_ViewImports cshtml* 파일에 추가 합니다. 자세한 내용은 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>을 참조하세요.

#### <a name="use-routable-components-in-an-mvc-app"></a>MVC 앱에서 라우팅 가능한 구성 요소 사용

MVC 앱에서 라우팅할 수 있는 Razor 구성 요소를 지원 하려면 다음을 수행 합니다.

1. [페이지 및 뷰에서 구성 요소 사용](#use-components-in-pages-and-views) 섹션의 지침을 따릅니다.

1. 다음 콘텐츠를 사용 하 여 프로젝트의 루트에 *응용 프로그램 razor* 파일을 추가 합니다.

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. 다음 내용을 사용 하 여 *Views/Home* 폴더에 _Host을 추가 *합니다* .

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   구성 요소는 해당 레이아웃에 대해 공유 *_Layout cshtml* 파일을 사용 합니다.

1. 홈 컨트롤러에 작업을 추가 합니다.

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. `Startup.Configure`의 끝점 구성에 *_Host. cshtml* 뷰를 반환 하는 컨트롤러 작업에 우선 순위가 낮은 경로를 추가 합니다.

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. *페이지* 폴더를 만들고 라우팅할 수 있는 구성 요소를 앱에 추가 합니다. 예를 들어:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   사용자 지정 폴더를 사용 하 여 앱의 구성 요소를 저장 하는 경우 폴더를 나타내는 네임 스페이스를 *Views/_ViewImports cshtml* 파일에 추가 합니다. 자세한 내용은 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>을 참조하세요.

### <a name="reflect-the-connection-state-in-the-ui"></a>UI의 연결 상태를 반영 합니다.

클라이언트에서 연결이 끊어진 것을 감지 하면 클라이언트에서 다시 연결을 시도 하는 동안 기본 UI가 표시 됩니다. 다시 연결이 실패 하는 경우 사용자에 게 다시 시도 하는 옵션이 제공 됩니다.

UI를 사용자 지정 하려면 _Host의 `<body>`에 `components-reconnect-modal` `id`를 사용 하 여 요소를 정의 *합니다. cshtml* Razor 페이지:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

다음 표에서는 `components-reconnect-modal` 요소에 적용 된 CSS 클래스에 대해 설명 합니다.

| CSS 클래스                       | &hellip;를 나타냅니다. |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | 연결이 끊어졌습니다. 클라이언트에서 다시 연결 하려고 합니다. 모달을 표시 합니다. |
| `components-reconnect-hide`     | 서버에 대 한 활성 연결이 다시 설정 됩니다. 모달을 숨깁니다. |
| `components-reconnect-failed`   | 네트워크 오류로 인해 다시 연결 하지 못했습니다. 다시 연결을 시도 하려면 `window.Blazor.reconnect()`를 호출 합니다. |
| `components-reconnect-rejected` | 다시 연결이 거부 되었습니다. 서버에 도달 했지만 연결이 거부 되었으며 서버에서 사용자의 상태가 손실 됩니다. 앱을 다시 로드 하려면 `location.reload()`를 호출 합니다. 이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.<ul><li>서버 쪽 회로의 작동이 중단 됩니다.</li><li>서버에서 사용자의 상태를 삭제 하기에 충분 한 길이의 클라이언트 연결이 끊겼습니다. 사용자가 상호 작용 하는 구성 요소의 인스턴스가 삭제 됩니다.</li><li>서버가 다시 시작 되거나 앱의 작업자 프로세스가 재활용 됩니다.</li></ul> |

### <a name="render-mode"></a>렌더링 모드

서버에 대 한 클라이언트 연결이 설정 되기 전에 서버에서 UI를 미리 렌더링 하도록 Blazor 서버 앱이 기본적으로 설정 되어 있습니다. 이는 _Host에 설정 되어 *있습니다. cshtml* Razor 페이지:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

구성 요소가 있는지 여부를 구성 하 `RenderMode` 다음을 수행 합니다.

* 는 페이지에 미리 렌더링 된 됩니다.
* 는 페이지에서 정적 HTML로 렌더링 되거나 사용자 에이전트에서 Blazor 앱을 부트스트랩 하는 데 필요한 정보가 포함 되어 있습니다.

| `RenderMode`        | 설명 |
| ------------------- | ----------- |
| `ServerPrerendered` | 구성 요소를 정적 HTML로 렌더링 하 고 Blazor Server 앱에 대 한 마커를 포함 합니다. 사용자 에이전트가 시작 되 면이 마커는 Blazor 앱을 부트스트랩 하는 데 사용 됩니다. |
| `Server`            | Blazor Server 앱에 대 한 마커를 렌더링 합니다. 구성 요소의 출력은 포함 되지 않습니다. 사용자 에이전트가 시작 되 면이 마커는 Blazor 앱을 부트스트랩 하는 데 사용 됩니다. |
| `Static`            | 구성 요소를 정적 HTML로 렌더링 합니다. |

정적 HTML 페이지에서 서버 구성 요소를 렌더링 하는 것은 지원 되지 않습니다.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Razor 페이지 및 뷰에서 상태 저장 대화형 구성 요소 렌더링

상태 저장 대화형 구성 요소는 Razor 페이지 또는 보기에 추가할 수 있습니다.

페이지 또는 뷰가 렌더링 되는 경우:

* 구성 요소가 페이지 또는 뷰와 미리 렌더링 된 됩니다.
* 렌더링에 사용 되는 초기 구성 요소 상태가 손실 됩니다.
* 새 구성 요소 상태는 SignalR 연결이 설정 될 때 생성 됩니다.

다음 Razor 페이지는 `Counter` 구성 요소를 렌더링 합니다.

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Razor 페이지 및 뷰에서 비 대화형 구성 요소 렌더링

다음 Razor 페이지에서 `Counter` 구성 요소는 폼을 사용 하 여 지정 된 초기 값을 사용 하 여 정적으로 렌더링 됩니다.

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

`MyComponent` 정적으로 렌더링 되므로 구성 요소는 대화형이 될 수 없습니다.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Blazor Server 앱에 대 한 SignalR 클라이언트 구성

경우에 따라 Blazor Server 앱에서 사용 하는 SignalR 클라이언트를 구성 해야 합니다. 예를 들어 SignalR 클라이언트에 대 한 로깅을 구성 하 여 연결 문제를 진단할 수 있습니다.

*Pages/_Host* 파일에서 SignalR 클라이언트를 구성 하려면 다음을 수행 합니다.

* `blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가 합니다.
* `Blazor.start`를 호출 하 고 SignalR 빌더를 지정 하는 구성 개체를 전달 합니다.

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
