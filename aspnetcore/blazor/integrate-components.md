---
title: Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합
author: guardrex
description: Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: de1a37ffd9456c956e3d84fcc69431ecb794513c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649083"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="1790f-103">Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합</span><span class="sxs-lookup"><span data-stu-id="1790f-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="1790f-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1790f-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="1790f-105">Razor Pages 및 MVC 앱에 Razor 구성 요소를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="1790f-106">페이지나 뷰를 렌더링할 때 구성 요소를 동시에 미리 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="1790f-107">페이지와 뷰에 구성 요소를 사용할 수 있도록 앱 준비</span><span class="sxs-lookup"><span data-stu-id="1790f-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="1790f-108">기존 Razor Pages 또는 MVC 앱은 페이지와 뷰에 Razor 구성 요소를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="1790f-109">앱의 레이아웃 파일( *_Layout.cshtml*)에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="1790f-110">`<head>` 요소에 다음 `<base>` 태그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="1790f-111">위의 예제에서 `href` 값(‘앱 기본 경로’)은 앱이 루트 URL 경로(`/`)에 있는 것으로 가정합니다. </span><span class="sxs-lookup"><span data-stu-id="1790f-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="1790f-112">앱이 하위 애플리케이션인 경우, <xref:host-and-deploy/blazor/index#app-base-path> 문서의 ‘앱 기본 경로’ 섹션에 설명된 지침을 따르세요. </span><span class="sxs-lookup"><span data-stu-id="1790f-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="1790f-113">*_Layout.cshtml* 파일은 Razor Pages 앱의 *Pages/Shared* 폴더 또는 MVC 앱의 *Views/Shared* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="1790f-114">닫는 `</body>` 태그 바로 앞에 *blazor.server.js* 스크립트의 `<script>` 태그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="1790f-115">프레임워크가 *blazor.server.js* 스크립트를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="1790f-116">앱에 스크립트를 수동으로 추가할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="1790f-117">다음 콘텐츠를 사용하여 프로젝트의 루트 폴더에 *_Imports.razor* 파일을 추가합니다(마지막 네임스페이스인 `MyAppNamespace`를 앱의 네임스페이스로 변경).</span><span class="sxs-lookup"><span data-stu-id="1790f-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="1790f-118">`Startup.ConfigureServices`에서 Blazor 서버 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="1790f-119">`Startup.Configure`에서 Blazor 허브 엔드포인트를 `app.UseEndpoints`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="1790f-120">페이지 또는 뷰에 구성 요소를 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-120">Integrate components into any page or view.</span></span> <span data-ttu-id="1790f-121">자세한 내용은 [페이지 또는 뷰에서 구성 요소 렌더링](#render-components-from-a-page-or-view) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1790f-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="1790f-122">Razor Pages 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="1790f-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="1790f-123">‘이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가하는 방법을 설명합니다.’ </span><span class="sxs-lookup"><span data-stu-id="1790f-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="1790f-124">Razor Pages 앱에서 라우팅 가능한 Razor 구성 요소를 지원하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="1790f-125">[페이지와 뷰에서 구성 요소를 사용할 수 있도록 앱 준비](#prepare-the-app-to-use-components-in-pages-and-views) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="1790f-126">다음 콘텐츠를 사용하여 프로젝트 루트에 *App.razor* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="1790f-127">다음 콘텐츠를 사용하여 *Pages* 폴더에 *_Host.cshtml* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="1790f-128">구성 요소는 해당 레이아웃에 공유 *_Layout.cshtml* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="1790f-129">`Startup.Configure`의 엔드포인트 구성에 *_Host.cshtml* 페이지의 우선순위가 낮은 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="1790f-130">라우팅 가능한 구성 요소를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-130">Add routable components to the app.</span></span> <span data-ttu-id="1790f-131">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="1790f-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="1790f-132">네임스페이스에 대한 자세한 내용은 [구성 요소 네임스페이스](#component-namespaces) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1790f-132">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="1790f-133">MVC 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="1790f-133">Use routable components in an MVC app</span></span>

<span data-ttu-id="1790f-134">‘이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가하는 방법을 설명합니다.’ </span><span class="sxs-lookup"><span data-stu-id="1790f-134">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="1790f-135">MVC 앱에서 라우팅 가능한 Razor 구성 요소를 지원하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="1790f-136">[페이지와 뷰에서 구성 요소를 사용할 수 있도록 앱 준비](#prepare-the-app-to-use-components-in-pages-and-views) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-136">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="1790f-137">다음 콘텐츠를 사용하여 프로젝트 루트에 *App.razor* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="1790f-138">다음 콘텐츠를 사용하여 *Views/Home* 폴더에 *_Host.cshtml* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="1790f-139">구성 요소는 해당 레이아웃에 공유 *_Layout.cshtml* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="1790f-140">홈 컨트롤러에 작업을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="1790f-141">`Startup.Configure`의 엔드포인트 구성에 *_Host.cshtml* 뷰를 반환하는 컨트롤러 작업의 우선순위가 낮은 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="1790f-142">*Pages* 폴더를 만들고 라우팅 가능한 구성 요소를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="1790f-143">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="1790f-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="1790f-144">네임스페이스에 대한 자세한 내용은 [구성 요소 네임스페이스](#component-namespaces) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1790f-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="1790f-145">구성 요소 네임스페이스</span><span class="sxs-lookup"><span data-stu-id="1790f-145">Component namespaces</span></span>

<span data-ttu-id="1790f-146">사용자 지정 폴더를 사용하여 앱의 구성 요소를 저장하는 경우, 폴더를 나타내는 네임스페이스를 페이지/뷰 또는 *_ViewImports.cshtml* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-146">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="1790f-147">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="1790f-147">In the following example:</span></span>

* <span data-ttu-id="1790f-148">`MyAppNamespace`를 앱의 네임스페이스로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-148">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="1790f-149">구성 요소를 저장하는 데 *Components* 폴더를 사용하지 않은 경우, `Components`를 구성 요소가 있는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-149">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="1790f-150">*_ViewImports.cshtml* 파일은 Razor Pages 앱의 *Pages* 폴더 또는 MVC 앱의 *Views* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-150">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="1790f-151">자세한 내용은 <xref:blazor/components#import-components>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1790f-151">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="1790f-152">페이지 또는 뷰에서 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="1790f-152">Render components from a page or view</span></span>

<span data-ttu-id="1790f-153">‘이 섹션에서는 사용자 요청에서 직접 구성 요소를 라우팅할 수 없는 페이지 또는 뷰에 구성 요소를 추가하는 방법을 설명합니다.’ </span><span class="sxs-lookup"><span data-stu-id="1790f-153">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="1790f-154">페이지 또는 뷰에서 구성 요소를 렌더링하려면 `Component` 태그 도우미를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-154">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="1790f-155">매개 변수 형식은 JSON serializable이어야 하며, 일반적으로 형식에 기본 생성자와 설정 가능한 속성이 있어야 함을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-155">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="1790f-156">예를 들어 `IncrementAmount`의 형식은 JSON 직렬 변환기에서 지원하는 기본 형식인 `int`이기 때문에 `IncrementAmount`의 값을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-156">For example, you can specify a value for `IncrementAmount` because the type of `IncrementAmount` is an `int`, which is a primitive type supported by the JSON serializer.</span></span>

<span data-ttu-id="1790f-157">`RenderMode`는 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-157">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="1790f-158">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="1790f-158">Is prerendered into the page.</span></span>
* <span data-ttu-id="1790f-159">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="1790f-159">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="1790f-160">설명</span><span class="sxs-lookup"><span data-stu-id="1790f-160">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="1790f-161">구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-161">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="1790f-162">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-162">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="1790f-163">Blazor 서버 앱의 표식을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-163">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="1790f-164">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-164">Output from the component isn't included.</span></span> <span data-ttu-id="1790f-165">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="1790f-166">구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-166">Renders the component into static HTML.</span></span> |

<span data-ttu-id="1790f-167">페이지와 뷰는 구성 요소를 사용할 수 있지만 반대의 경우는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-167">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="1790f-168">구성 요소는 부분 뷰, 섹션 등의 뷰 및 페이지 특정 시나리오를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-168">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="1790f-169">구성 요소에서 부분 뷰의 논리를 사용하려면 부분 뷰 논리를 구성 요소로 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-169">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="1790f-170">정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1790f-170">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="1790f-171">구성 요소를 렌더링하는 방법, 구성 요소 상태 및 `Component` 태그 도우미에 대한 자세한 내용은 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1790f-171">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
