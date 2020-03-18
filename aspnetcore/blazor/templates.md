---
title: ASP.NET Core Blazor 템플릿
author: guardrex
description: ASP.NET Core Blazor 앱 템플릿 및 Blazor 프로젝트 구조에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: acfa4b8a42cbd310c6fc6dc973573578e94ef999
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649449"
---
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="212a1-103">ASP.NET Core Blazor 템플릿</span><span class="sxs-lookup"><span data-stu-id="212a1-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="212a1-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="212a1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="212a1-105">Blazor 프레임워크는 각 Blazor 호스팅 모델용 앱을 개발하기 위한 템플릿을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="212a1-106"> WebAssembly(`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="212a1-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="212a1-107"> 서버(`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="212a1-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="212a1-108">Blazor의 호스팅 모델에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="212a1-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="212a1-109">템플릿에서 Blazor 앱을 만드는 방법에 대한 단계별 지침은 <xref:blazor/get-started>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="212a1-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="212a1-110"> 프로젝트 구조</span><span class="sxs-lookup"><span data-stu-id="212a1-110"> project structure</span></span>

<span data-ttu-id="212a1-111">다음 파일 및 폴더는 Blazor 템플릿에서 생성된 Blazor 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-111">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="212a1-112">*Program.cs* &ndash; 설정된 앱의 진입점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-112">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="212a1-113">ASP.NET Core [호스트](xref:fundamentals/host/generic-host)(Blazor 서버)</span><span class="sxs-lookup"><span data-stu-id="212a1-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="212a1-114">WebAssembly 호스트(Blazor WebAssembly) &ndash; 이 파일의 코드는 Blazor WebAssembly 템플릿(`blazorwasm`)에서 만든 앱에 고유합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-114">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="212a1-115">앱의 루트 구성 요소인 `App` 구성 요소는 `Add` 메서드에 대한 `app` DOM 요소로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-115">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="212a1-116">호스트 작성기(예: `builder.Services.AddSingleton<IMyDependency, MyDependency>();`)의 `ConfigureServices` 메서드로 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-116">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="212a1-117">호스트 작성기(`builder.Configuration`)를 통해 구성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-117">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="212a1-118">*Startup.cs*(Blazor 서버) &ndash; 앱의 시작 논리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-118">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="212a1-119">`Startup` 클래스는 다음 두 가지 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="212a1-120">`ConfigureServices` &ndash; 앱의 DI([종속성 주입)](xref:fundamentals/dependency-injection) 서비스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-120">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="212a1-121">Blazor 서버 앱에서 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>를 호출하여 서비스가 추가되고 예제 `FetchData` 구성 요소에서 사용할 수 있도록 서비스 컨테이너에 `WeatherForecastService`가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="212a1-122">`Configure` &ndash; 앱의 요청 처리 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-122">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="212a1-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>는 브라우저와의 실시간 연결을 위해 엔드포인트를 설정하도록 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="212a1-124">연결은 애플리케이션에 앱에 실시간 웹 기능을 추가하기 위한 프레임워크인 [SignalR](xref:signalr/introduction)를 사용하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="212a1-125">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)를 호출하여 앱의 루트 페이지(*Pages/_Host*)를 설정하고 탐색을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-125">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="212a1-126">*wwwroot/index.html*(Blazor WebAssembly) &ndash; HTML 페이지로 구현된 앱의 루트 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-126">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="212a1-127">앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="212a1-128">이 페이지는 루트 `App` 구성 요소가 렌더링되는 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="212a1-129">`App` 구성 요소(*App.razor*)는 `Startup.Configure`에서 `AddComponent` 메서드에 대한 `app` DOM 요소로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-129">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="212a1-130">`_framework/blazor.webassembly.js` JavaScript 파일이 로드되며, 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="212a1-131">.NET 런타임, 앱 및 앱의 종속성을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="212a1-132">런타임을 초기화하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-132">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="212a1-133">*Pages/_Host cshtml*(Blazor 서버) &ndash; Razor 페이지로 구현된 앱의 루트 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-133">*Pages/_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
  * <span data-ttu-id="212a1-134">앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-134">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="212a1-135">브라우저와 서버 간에 실시간 SignalR 연결을 설정하는 `_framework/blazor.server.js` JavaScript 파일이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-135">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
  * <span data-ttu-id="212a1-136">호스트 페이지는 루트 `App` 구성 요소(*App.razor*)가 렌더링되는 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-136">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>

* <span data-ttu-id="212a1-137">*App.razor* &ndash; <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하여 클라이언트 쪽 라우팅을 설정하는 앱의 루트 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-137">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="212a1-138">`Router` 구성 요소는 브라우저 탐색을 가로채서 요청된 주소와 일치하는 페이지를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-138">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="212a1-139">*Pages* 폴더 &ndash; Blazor 앱을 구성하는 라우팅 가능한 구성 요소/페이지( *.razor*)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-139">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app.</span></span> <span data-ttu-id="212a1-140">각 페이지에 대한 경로는 [`@page`](xref:mvc/views/razor#page) 지시어를 사용하여 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-140">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="212a1-141">이 템플릿은 다음 구성 요소를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-141">The template includes the following components:</span></span>
  * <span data-ttu-id="212a1-142">`Index`(*Index. razor*)&ndash; 홈페이지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-142">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>
  * <span data-ttu-id="212a1-143">`Counter`(*Counter.razor*)&ndash; 카운터 페이지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="212a1-144">`Error`(*Error.razor*, Blazor 서버 앱만 해당) &ndash; 앱에서 처리되지 않은 예외가 발생하면 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="212a1-145">`FetchData`(*FetchData.razor*)&ndash; 데이터 가져오기 페이지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>

* <span data-ttu-id="212a1-146">*Shared* 폴더 &ndash; 앱에서 사용되는 다른 UI 구성 요소( *.razor*)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-146">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="212a1-147">`MainLayout`(*MainLayout.razor*)&ndash; 앱의 레이아웃 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-147">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="212a1-148">`NavMenu`(*NavMenu.razor*)&ndash; 사이드바 탐색을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-148">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="212a1-149">다른 Razor 구성 요소에 대한 탐색 링크를 렌더링하는 [NavLink 구성 요소](xref:blazor/routing#navlink-component)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-149">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="212a1-150">`NavLink` 구성 요소는 해당 구성 요소가 로드될 때 선택된 상태를 자동으로 나타내므로 사용자가 현재 표시되는 구성 요소를 파악하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-150">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="212a1-151">*_Imports razor* &ndash; 네임스페이스에 대한 [`@using`](xref:mvc/views/razor#using) 지시문과 같은 앱의 구성 요소( *.razor*)에 포함할 일반적인 Razor 지시문이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-151">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="212a1-152">*Data* 폴더(Blazor 서버) &ndash; 앱의 `FetchData` 구성 요소에 예제 날씨 데이터를 제공하는 `WeatherForecast` 클래스 및 `WeatherForecastService` 구현이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-152">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="212a1-153">*wwwroot* &ndash; 앱의 퍼블릭 정적 자산을 포함하는 앱의 [웹 루트](xref:fundamentals/index#web-root) 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-153">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="212a1-154">*appsettings.json*(Blazor 서버) &ndash; 앱에 대한 구성 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="212a1-154">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
