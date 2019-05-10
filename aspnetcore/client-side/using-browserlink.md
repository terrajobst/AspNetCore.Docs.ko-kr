---
title: ASP.NET Core에서 브라우저 링크
author: ncarandini
description: 브라우저 링크는 하나 이상의 웹 브라우저를 사용 하 여 개발 환경에 연결 하는 Visual Studio 기능 하는 방법을 설명 합니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 09/22/2017
uid: client-side/using-browserlink
ms.openlocfilehash: 452ba5149563c186750466f471c7b950f0017614
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894710"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="a93d3-103">ASP.NET Core에서 브라우저 링크</span><span class="sxs-lookup"><span data-stu-id="a93d3-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="a93d3-104">하 여 [Nicolò Carandini](https://github.com/ncarandini)하십시오 [Mike Wasson](https://github.com/MikeWasson), 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="a93d3-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="a93d3-105">브라우저 링크에는 Visual studio 개발 환경 및 하나 이상의 웹 브라우저 간에 통신 채널을 만드는 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-105">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="a93d3-106">브라우저 간 테스트에 유용한 여러 브라우저에서 웹 응용 프로그램에 한 번만 새로 고치려면 브라우저 링크를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-106">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="a93d3-107">브라우저 링크 설치</span><span class="sxs-lookup"><span data-stu-id="a93d3-107">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="a93d3-108">ASP.NET Core 2.1 및 전환 하는 ASP.NET Core 2.0 프로젝트를 변환 하는 경우는 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)를 설치 합니다 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 패키지 BrowserLink 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-108">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for BrowserLink functionality.</span></span> <span data-ttu-id="a93d3-109">ASP.NET Core 2.1 프로젝트 템플릿을 사용 하 여는 `Microsoft.AspNetCore.App` 기본적으로 메타 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-109">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="a93d3-110">ASP.NET Core 2.0 **웹 응용 프로그램**, **빈**, 및 **Web API** 프로젝트 템플릿을 사용 합니다 [Microsoft.AspNetCore.All 메타 패키지](xref:fundamentals/metapackage) 에 대 한 패키지 참조를 포함 하는 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-110">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="a93d3-111">따라서를 사용 하 여 `Microsoft.AspNetCore.All` 메타 패키지는 브라우저 링크를 사용 하기 위해 사용할 수 있도록 하려면 추가 작업이 없으므로 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-111">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="a93d3-112">ASP.NET Core 1.x **웹 응용 프로그램** 프로젝트 서식 파일에 대 한 패키지 참조에는 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 패키지 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-112">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="a93d3-113">**빈** 하거나 **Web API** 서식 파일 프로젝트에 대 한 패키지 참조를 추가 해야 `Microsoft.VisualStudio.Web.BrowserLink`합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-113">The **Empty** or **Web API** template projects require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

<span data-ttu-id="a93d3-114">이므로 Visual Studio 기능에는 가장 쉬운 방법은 패키지를 추가 하는 **빈** 또는 **Web API** 템플릿 프로젝트를 여는 것은 **패키지 관리자 콘솔** (**뷰** > **기타 Windows** > **패키지 관리자 콘솔**) 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-114">Since this is a Visual Studio feature, the easiest way to add the package to an **Empty** or **Web API** template project is to open the **Package Manager Console** (**View** > **Other Windows** > **Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

<span data-ttu-id="a93d3-115">사용할 수 있습니다 **NuGet 패키지 관리자**합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-115">Alternatively, you can use **NuGet Package Manager**.</span></span> <span data-ttu-id="a93d3-116">프로젝트 이름을 마우스 오른쪽 단추로 클릭 **솔루션 탐색기** 선택한 **NuGet 패키지 관리**:</span><span class="sxs-lookup"><span data-stu-id="a93d3-116">Right-click the project name in **Solution Explorer** and choose **Manage NuGet Packages**:</span></span>

![열고 NuGet 패키지 관리자](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="a93d3-118">찾기 및 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-118">Find and install the package:</span></span>

![NuGet 패키지 관리자를 사용 하 여 패키지를 추가 합니다.](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="a93d3-120">구성</span><span class="sxs-lookup"><span data-stu-id="a93d3-120">Configuration</span></span>

<span data-ttu-id="a93d3-121">`Startup.Configure` 메서드에서:</span><span class="sxs-lookup"><span data-stu-id="a93d3-121">In the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="a93d3-122">내 코드는 일반적으로 `if` 다음과 같이 개발 환경에서 브라우저 링크 수만 있는 블록:</span><span class="sxs-lookup"><span data-stu-id="a93d3-122">Usually the code is inside an `if` block that only enables Browser Link in the Development environment, as shown here:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="a93d3-123">자세한 내용은 [여러 환경 사용](xref:fundamentals/environments)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a93d3-123">For more information, see [Use multiple environments](xref:fundamentals/environments).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="a93d3-124">브라우저 링크를 사용 하는 방법</span><span class="sxs-lookup"><span data-stu-id="a93d3-124">How to use Browser Link</span></span>

<span data-ttu-id="a93d3-125">Visual Studio 브라우저 링크 도구 모음 컨트롤 옆에 표시는 ASP.NET Core 프로젝트가 열려 있으면 합니다 **디버그 대상** toolbar 컨트롤:</span><span class="sxs-lookup"><span data-stu-id="a93d3-125">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![브라우저 링크 드롭 다운 메뉴](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="a93d3-127">브라우저 링크 도구 모음 컨트롤에서 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-127">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="a93d3-128">여러 브라우저에서 웹 응용 프로그램을 한 번 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-128">Refresh the web application in several browsers at once.</span></span>
* <span data-ttu-id="a93d3-129">엽니다는 **브라우저 링크 대시보드**합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-129">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="a93d3-130">또는 사용 안 함 **브라우저 링크**합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-130">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="a93d3-131">참고: 브라우저 링크는 Visual Studio 2017 (15.3)에서 기본적으로 비활성화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-131">Note: Browser Link is disabled by default in Visual Studio 2017 (15.3).</span></span>
* <span data-ttu-id="a93d3-132">또는 사용 안 함 [CSS 자동 동기화](#enable-or-disable-css-auto-sync)합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-132">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

> [!NOTE]
> <span data-ttu-id="a93d3-133">일부 Visual Studio 플러그 인, 가장 주목할 만한 *웹 확장 팩 2015* 하 고 *웹 확장 팩 2017*, 브라우저 링크에 대 한 확장 된 기능을 제공 하지만 추가 기능 중 일부는 ASP를 사용 하 여 작동 하지 않습니다. .NET Core 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-133">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="a93d3-134">한 번에 여러 브라우저에서 웹 앱을 새로 고침</span><span class="sxs-lookup"><span data-stu-id="a93d3-134">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="a93d3-135">프로젝트를 시작할 때 시작 하는 단일 웹 브라우저를 선택 하려면 드롭다운 메뉴에서를 사용 합니다 **디버그 대상** toolbar 컨트롤:</span><span class="sxs-lookup"><span data-stu-id="a93d3-135">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 드롭 다운 메뉴](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="a93d3-137">한 번에 여러 브라우저를 열고, 선택 **찾아보기...**  동일한 드롭다운 목록에서.</span><span class="sxs-lookup"><span data-stu-id="a93d3-137">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="a93d3-138">원하는 브라우저를 선택 하려면 CTRL 키를 누른 채 클릭 **찾아보기**:</span><span class="sxs-lookup"><span data-stu-id="a93d3-138">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![한 번에 다양 한 브라우저를 열으십시오](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="a93d3-140">인덱스 뷰를 사용 하 여 Visual Studio를 열고 보여 주는 스크린샷 및 열린 브라우저는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-140">Here's a screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![두 개의 브라우저 예제를 사용 하 여 동기화](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="a93d3-142">프로젝트에 연결 된 브라우저를 확인 하려면 브라우저 링크 도구 모음 컨트롤을 마우스로:</span><span class="sxs-lookup"><span data-stu-id="a93d3-142">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Hover 팁](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="a93d3-144">인덱스 뷰를 변경 하 고 모든 연결 된 브라우저는 브라우저 링크 새로 고침 단추를 클릭할 때 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-144">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="a93d3-146">브라우저 링크 Visual Studio 외부에서 시작 하 고 응용 프로그램 URL로 이동 하는 브라우저 에서도 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-146">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="a93d3-147">브라우저 링크 대시보드</span><span class="sxs-lookup"><span data-stu-id="a93d3-147">The Browser Link Dashboard</span></span>

<span data-ttu-id="a93d3-148">브라우저 링크 드롭다운 메뉴를 열고 브라우저를 사용 하 여 연결을 관리 목록에서 브라우저 링크 대시보드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-148">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![오픈-browserslink-대시보드](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="a93d3-150">선택 하 여 비 디버깅 세션을 시작할 수 없는 브라우저에 연결 하는 경우는 *브라우저에서 보기* 링크:</span><span class="sxs-lookup"><span data-stu-id="a93d3-150">If no browser is connected, you can start a non-debugging session by selecting the *View in Browser* link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="a93d3-152">그렇지 않으면 연결 된 브라우저는 각 브라우저에 표시 되는 페이지의 경로를 사용 하 여 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-152">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="a93d3-154">원하는 경우 해당 단일 브라우저를 새로 고치려면 나열 된 브라우저 이름을 클릭할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-154">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="a93d3-155">브라우저 링크를 사용할지 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-155">Enable or disable Browser Link</span></span>

<span data-ttu-id="a93d3-156">이 해제 한 후 브라우저 링크를 다시 설정 하면 해당 다시 연결 하려면 브라우저를 새로 고쳐야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-156">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="a93d3-157">CSS 자동 동기화를 사용할지 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-157">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="a93d3-158">CSS 자동 동기화를 사용 하는 경우 연결 된 브라우저는 자동으로 새로 고쳐집니다 CSS 파일을 변경 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="a93d3-158">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="a93d3-159">작동 방법</span><span class="sxs-lookup"><span data-stu-id="a93d3-159">How it works</span></span>

<span data-ttu-id="a93d3-160">브라우저 링크 SignalR을 사용 하 여 Visual Studio와 브라우저 간에 통신 채널을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-160">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="a93d3-161">브라우저 링크를 사용 하는 경우 Visual Studio는 여러 클라이언트 (브라우저)에 연결할 수 있는 SignalR 서버로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-161">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="a93d3-162">브라우저 링크는 또한 ASP.NET Core 요청 파이프라인에 미들웨어 구성 요소를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-162">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="a93d3-163">이 구성 요소를 특수 삽입 `<script>` 서버에서 모든 페이지 요청에 대 한 참조입니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-163">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="a93d3-164">선택 하 여 스크립트 파일에 대 한 참조를 확인할 수 있습니다 **소스 보기** 브라우저 및 스크롤의 끝에는 `<body>` 콘텐츠 태그:</span><span class="sxs-lookup"><span data-stu-id="a93d3-164">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="a93d3-165">소스 파일을 수정 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-165">Your source files aren't modified.</span></span> <span data-ttu-id="a93d3-166">미들웨어 구성 요소 스크립트 참조를 동적으로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-166">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="a93d3-167">브라우저 쪽 코드를 모든 JavaScript 이기 때문에 브라우저 플러그 인을 요구 하지 않고 SignalR을 지 원하는 모든 브라우저에서 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="a93d3-167">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
