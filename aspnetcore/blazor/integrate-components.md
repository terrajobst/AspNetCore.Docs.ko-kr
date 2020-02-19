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
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="fc8c3-103">ASP.NET Core Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합</span><span class="sxs-lookup"><span data-stu-id="fc8c3-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="fc8c3-104">By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="fc8c3-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="fc8c3-105">Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="fc8c3-106">페이지나 보기가 렌더링 되 면 구성 요소를 동시에 미리 렌더링 된 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="fc8c3-107">페이지 및 보기의 구성 요소를 사용 하도록 앱 준비</span><span class="sxs-lookup"><span data-stu-id="fc8c3-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="fc8c3-108">기존 Razor Pages 또는 MVC 앱은 Razor 구성 요소를 페이지 및 뷰에 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="fc8c3-109">앱의 레이아웃 파일 ( *_Layout cshtml*)에서:</span><span class="sxs-lookup"><span data-stu-id="fc8c3-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="fc8c3-110">`<head>` 요소에 다음 `<base>` 태그를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="fc8c3-111">앞의 예제에서 `href` 값 ( *앱 기본 경로*)은 앱이 루트 URL 경로 (`/`)에 있는 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="fc8c3-112">앱이 하위 응용 프로그램 인 경우 <xref:host-and-deploy/blazor/index#app-base-path> 문서의 *앱 기준 경로* 섹션에 있는 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="fc8c3-113">*_Layout cshtml* 파일은 Razor Pages 앱의 *Pages/SHARED* 폴더 또는 MVC 앱의 *Views/shared* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="fc8c3-114">*Blazor* 스크립트에 대 한 `<script>` 태그를 닫는 `</body>` 태그 바로 앞에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="fc8c3-115">프레임 워크는 *blazor* 스크립트를 앱에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="fc8c3-116">앱에 스크립트를 수동으로 추가할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="fc8c3-117">다음 콘텐츠를 사용 하 여 프로젝트의 루트 폴더에 *_Imports razor* 파일을 추가 합니다 (마지막 네임 스페이스 `MyAppNamespace`를 앱의 네임 스페이스로 변경).</span><span class="sxs-lookup"><span data-stu-id="fc8c3-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="fc8c3-118">`Startup.ConfigureServices`에서 Blazor 서버 서비스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="fc8c3-119">`Startup.Configure`에서 `app.UseEndpoints`에 Blazor Hub 끝점을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="fc8c3-120">모든 페이지 또는 보기에 구성 요소를 통합 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-120">Integrate components into any page or view.</span></span> <span data-ttu-id="fc8c3-121">자세한 내용은 [페이지 또는 뷰에서 구성 요소 렌더링](#render-components-from-a-page-or-view) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="fc8c3-122">Razor Pages 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="fc8c3-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="fc8c3-123">*이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가 하는 방법에 대해 설명 합니다.*</span><span class="sxs-lookup"><span data-stu-id="fc8c3-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="fc8c3-124">Razor Pages 앱에서 라우팅할 수 있는 Razor 구성 요소를 지원 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="fc8c3-125">[페이지 및 뷰에서 구성 요소를 사용 하도록 앱 준비](#prepare-the-app-to-use-components-in-pages-and-views) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="fc8c3-126">다음 콘텐츠를 사용 하 여 프로젝트 루트에 *응용 프로그램 razor* 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="fc8c3-127">다음 내용을 사용 하 여 *Pages* 폴더에 *_Host cshtml* 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="fc8c3-128">구성 요소는 해당 레이아웃에 대해 공유 *_Layout cshtml* 파일을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="fc8c3-129">_Host에 대 한 우선 순위가 낮은 경로를 `Startup.Configure`의 끝점 구성에 추가 *합니다* .</span><span class="sxs-lookup"><span data-stu-id="fc8c3-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="fc8c3-130">라우팅할 수 있는 구성 요소를 앱에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-130">Add routable components to the app.</span></span> <span data-ttu-id="fc8c3-131">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="fc8c3-132">네임 스페이스에 대 한 자세한 내용은 [구성 요소 네임 스페이스](#component-namespaces) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-132">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="fc8c3-133">MVC 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="fc8c3-133">Use routable components in an MVC app</span></span>

<span data-ttu-id="fc8c3-134">*이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가 하는 방법에 대해 설명 합니다.*</span><span class="sxs-lookup"><span data-stu-id="fc8c3-134">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="fc8c3-135">MVC 앱에서 라우팅할 수 있는 Razor 구성 요소를 지원 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="fc8c3-136">[페이지 및 뷰에서 구성 요소를 사용 하도록 앱 준비](#prepare-the-app-to-use-components-in-pages-and-views) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-136">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="fc8c3-137">다음 콘텐츠를 사용 하 여 프로젝트의 루트에 *응용 프로그램 razor* 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="fc8c3-138">다음 내용을 사용 하 여 *Views/Home* 폴더에 _Host을 추가 *합니다* .</span><span class="sxs-lookup"><span data-stu-id="fc8c3-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="fc8c3-139">구성 요소는 해당 레이아웃에 대해 공유 *_Layout cshtml* 파일을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="fc8c3-140">홈 컨트롤러에 작업을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="fc8c3-141">`Startup.Configure`의 끝점 구성에 *_Host. cshtml* 뷰를 반환 하는 컨트롤러 작업에 우선 순위가 낮은 경로를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="fc8c3-142">*페이지* 폴더를 만들고 라우팅할 수 있는 구성 요소를 앱에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="fc8c3-143">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="fc8c3-144">네임 스페이스에 대 한 자세한 내용은 [구성 요소 네임 스페이스](#component-namespaces) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="fc8c3-145">구성 요소 네임 스페이스</span><span class="sxs-lookup"><span data-stu-id="fc8c3-145">Component namespaces</span></span>

<span data-ttu-id="fc8c3-146">사용자 지정 폴더를 사용 하 여 앱의 구성 요소를 저장 하는 경우 폴더를 나타내는 네임 스페이스를 페이지/보기 또는 _ViewImports로 추가 합니다. *cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-146">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="fc8c3-147">다음 예제에서,</span><span class="sxs-lookup"><span data-stu-id="fc8c3-147">In the following example:</span></span>

* <span data-ttu-id="fc8c3-148">`MyAppNamespace`를 앱의 네임 스페이스로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-148">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="fc8c3-149">구성 요소 라는 폴더를 사용 하 여 구성 요소를 저장 *하지 않은 경우* 에는 구성 요소가 상주 하는 폴더로 `Components`를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-149">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="fc8c3-150">*_ViewImports cshtml* 파일은 Razor Pages 앱의 *PAGES* 폴더 또는 MVC 앱의 *Views* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-150">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="fc8c3-151">자세한 내용은 <xref:blazor/components#import-components>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-151">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="fc8c3-152">페이지 또는 뷰에서 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="fc8c3-152">Render components from a page or view</span></span>

<span data-ttu-id="fc8c3-153">*이 섹션에서는 사용자 요청에서 구성 요소를 직접 라우팅할 수 없는 페이지 또는 보기에 구성 요소를 추가 하는 방법에 대해 설명 합니다.*</span><span class="sxs-lookup"><span data-stu-id="fc8c3-153">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="fc8c3-154">페이지 또는 뷰에서 구성 요소를 렌더링 하려면 `Component` 태그 도우미를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-154">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="fc8c3-155">매개 변수 형식은 JSON serializable 이어야 합니다 .이는 일반적으로 형식에 기본 생성자와 설정 가능한 속성이 있어야 함을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-155">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="fc8c3-156">예를 들어 `IncrementAmount` 형식이 JSON serializer에서 지 원하는 기본 형식인 `int`이기 때문에 `IncrementAmount` 값을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-156">For example, you can specify a value for `IncrementAmount` because the type of `IncrementAmount` is an `int`, which is a primitive type supported by the JSON serializer.</span></span>

<span data-ttu-id="fc8c3-157">구성 요소가 있는지 여부를 구성 하 `RenderMode` 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-157">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="fc8c3-158">는 페이지에 미리 렌더링 된 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-158">Is prerendered into the page.</span></span>
* <span data-ttu-id="fc8c3-159">는 페이지에서 정적 HTML로 렌더링 되거나 사용자 에이전트에서 Blazor 앱을 부트스트랩 하는 데 필요한 정보가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-159">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="fc8c3-160">Description</span><span class="sxs-lookup"><span data-stu-id="fc8c3-160">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="fc8c3-161">구성 요소를 정적 HTML로 렌더링 하 고 Blazor Server 앱에 대 한 마커를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-161">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="fc8c3-162">사용자 에이전트가 시작 되 면이 마커는 Blazor 앱을 부트스트랩 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-162">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="fc8c3-163">Blazor Server 앱에 대 한 마커를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-163">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="fc8c3-164">구성 요소의 출력은 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-164">Output from the component isn't included.</span></span> <span data-ttu-id="fc8c3-165">사용자 에이전트가 시작 되 면이 마커는 Blazor 앱을 부트스트랩 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="fc8c3-166">구성 요소를 정적 HTML로 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-166">Renders the component into static HTML.</span></span> |

<span data-ttu-id="fc8c3-167">페이지 및 뷰에서 구성 요소를 사용할 수 있지만 반대의 경우는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-167">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="fc8c3-168">구성 요소는 부분 보기 및 섹션과 같은 보기 및 페이지 관련 시나리오를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-168">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="fc8c3-169">구성 요소의 부분 뷰에서 논리를 사용 하려면 부분 뷰 논리를 구성 요소에 대 한 요소로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-169">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="fc8c3-170">정적 HTML 페이지에서 서버 구성 요소를 렌더링 하는 것은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-170">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="fc8c3-171">구성 요소를 렌더링 하는 방법, 구성 요소 상태 및 `Component` 태그 도우미에 대 한 자세한 내용은 다음 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fc8c3-171">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
