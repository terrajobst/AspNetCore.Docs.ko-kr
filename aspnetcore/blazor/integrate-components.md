---
title: ASP.NET Core Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합
author: guardrex
description: Blazor apps의 구성 요소 및 DOM 요소에 대 한 데이터 바인딩 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: de1a37ffd9456c956e3d84fcc69431ecb794513c
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453213"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>ASP.NET Core Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합

By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합할 수 있습니다. 페이지나 보기가 렌더링 되 면 구성 요소를 동시에 미리 렌더링 된 수 있습니다.

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a>페이지 및 보기의 구성 요소를 사용 하도록 앱 준비

기존 Razor Pages 또는 MVC 앱은 Razor 구성 요소를 페이지 및 뷰에 통합할 수 있습니다.

1. 앱의 레이아웃 파일 ( *_Layout cshtml*)에서:

   * `<head>` 요소에 다음 `<base>` 태그를 추가 합니다.

     ```html
     <base href="~/" />
     ```

     앞의 예제에서 `href` 값 ( *앱 기본 경로*)은 앱이 루트 URL 경로 (`/`)에 있는 것으로 가정 합니다. 앱이 하위 응용 프로그램 인 경우 <xref:host-and-deploy/blazor/index#app-base-path> 문서의 *앱 기준 경로* 섹션에 있는 지침을 따르세요.

     *_Layout cshtml* 파일은 Razor Pages 앱의 *Pages/SHARED* 폴더 또는 MVC 앱의 *Views/shared* 폴더에 있습니다.

   * *Blazor* 스크립트에 대 한 `<script>` 태그를 닫는 `</body>` 태그 바로 앞에 추가 합니다.

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     프레임 워크는 *blazor* 스크립트를 앱에 추가 합니다. 앱에 스크립트를 수동으로 추가할 필요는 없습니다.

1. 다음 콘텐츠를 사용 하 여 프로젝트의 루트 폴더에 *_Imports razor* 파일을 추가 합니다 (마지막 네임 스페이스 `MyAppNamespace`를 앱의 네임 스페이스로 변경).

   ```razor
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

1. 모든 페이지 또는 보기에 구성 요소를 통합 합니다. 자세한 내용은 [페이지 또는 뷰에서 구성 요소 렌더링](#render-components-from-a-page-or-view) 섹션을 참조 하세요.

## <a name="use-routable-components-in-a-razor-pages-app"></a>Razor Pages 앱에서 라우팅 가능한 구성 요소 사용

*이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가 하는 방법에 대해 설명 합니다.*

Razor Pages 앱에서 라우팅할 수 있는 Razor 구성 요소를 지원 하려면 다음을 수행 합니다.

1. [페이지 및 뷰에서 구성 요소를 사용 하도록 앱 준비](#prepare-the-app-to-use-components-in-pages-and-views) 섹션의 지침을 따릅니다.

1. 다음 콘텐츠를 사용 하 여 프로젝트 루트에 *응용 프로그램 razor* 파일을 추가 합니다.

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

1. 라우팅할 수 있는 구성 요소를 앱에 추가 합니다. 다음은 그 예입니다.

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   네임 스페이스에 대 한 자세한 내용은 [구성 요소 네임 스페이스](#component-namespaces) 섹션을 참조 하세요.

## <a name="use-routable-components-in-an-mvc-app"></a>MVC 앱에서 라우팅 가능한 구성 요소 사용

*이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가 하는 방법에 대해 설명 합니다.*

MVC 앱에서 라우팅할 수 있는 Razor 구성 요소를 지원 하려면 다음을 수행 합니다.

1. [페이지 및 뷰에서 구성 요소를 사용 하도록 앱 준비](#prepare-the-app-to-use-components-in-pages-and-views) 섹션의 지침을 따릅니다.

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

1. *페이지* 폴더를 만들고 라우팅할 수 있는 구성 요소를 앱에 추가 합니다. 다음은 그 예입니다.

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   네임 스페이스에 대 한 자세한 내용은 [구성 요소 네임 스페이스](#component-namespaces) 섹션을 참조 하세요.

## <a name="component-namespaces"></a>구성 요소 네임 스페이스

사용자 지정 폴더를 사용 하 여 앱의 구성 요소를 저장 하는 경우 폴더를 나타내는 네임 스페이스를 페이지/보기 또는 _ViewImports로 추가 합니다. *cshtml* 파일. 다음 예제에서,

* `MyAppNamespace`를 앱의 네임 스페이스로 변경 합니다.
* 구성 요소 라는 폴더를 사용 하 여 구성 요소를 저장 *하지 않은 경우* 에는 구성 요소가 상주 하는 폴더로 `Components`를 변경 합니다.

```cshtml
@using MyAppNamespace.Components
```

*_ViewImports cshtml* 파일은 Razor Pages 앱의 *PAGES* 폴더 또는 MVC 앱의 *Views* 폴더에 있습니다.

자세한 내용은 <xref:blazor/components#import-components>을 참조하세요.

## <a name="render-components-from-a-page-or-view"></a>페이지 또는 뷰에서 구성 요소 렌더링

*이 섹션에서는 사용자 요청에서 구성 요소를 직접 라우팅할 수 없는 페이지 또는 보기에 구성 요소를 추가 하는 방법에 대해 설명 합니다.*

페이지 또는 뷰에서 구성 요소를 렌더링 하려면 `Component` 태그 도우미를 사용 합니다.

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

매개 변수 형식은 JSON serializable 이어야 합니다 .이는 일반적으로 형식에 기본 생성자와 설정 가능한 속성이 있어야 함을 의미 합니다. 예를 들어 `IncrementAmount` 형식이 JSON serializer에서 지 원하는 기본 형식인 `int`이기 때문에 `IncrementAmount` 값을 지정할 수 있습니다.

구성 요소가 있는지 여부를 구성 하 `RenderMode` 다음을 수행 합니다.

* 는 페이지에 미리 렌더링 된 됩니다.
* 는 페이지에서 정적 HTML로 렌더링 되거나 사용자 에이전트에서 Blazor 앱을 부트스트랩 하는 데 필요한 정보가 포함 되어 있습니다.

| `RenderMode`        | Description |
| ------------------- | ----------- |
| `ServerPrerendered` | 구성 요소를 정적 HTML로 렌더링 하 고 Blazor Server 앱에 대 한 마커를 포함 합니다. 사용자 에이전트가 시작 되 면이 마커는 Blazor 앱을 부트스트랩 하는 데 사용 됩니다. |
| `Server`            | Blazor Server 앱에 대 한 마커를 렌더링 합니다. 구성 요소의 출력은 포함 되지 않습니다. 사용자 에이전트가 시작 되 면이 마커는 Blazor 앱을 부트스트랩 하는 데 사용 됩니다. |
| `Static`            | 구성 요소를 정적 HTML로 렌더링 합니다. |

페이지 및 뷰에서 구성 요소를 사용할 수 있지만 반대의 경우는 그렇지 않습니다. 구성 요소는 부분 보기 및 섹션과 같은 보기 및 페이지 관련 시나리오를 사용할 수 없습니다. 구성 요소의 부분 뷰에서 논리를 사용 하려면 부분 뷰 논리를 구성 요소에 대 한 요소로 처리 합니다.

정적 HTML 페이지에서 서버 구성 요소를 렌더링 하는 것은 지원 되지 않습니다.

구성 요소를 렌더링 하는 방법, 구성 요소 상태 및 `Component` 태그 도우미에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
