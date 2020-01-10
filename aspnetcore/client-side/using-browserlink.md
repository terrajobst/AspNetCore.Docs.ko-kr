---
title: ASP.NET Core의 브라우저 링크
author: ncarandini
description: 브라우저 링크가 하나 이상의 웹 브라우저와 개발 환경을 연결 하는 Visual Studio 기능을 설명 합니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828272"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="63b0b-103">ASP.NET Core의 브라우저 링크</span><span class="sxs-lookup"><span data-stu-id="63b0b-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="63b0b-104">[Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="63b0b-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="63b0b-105">브라우저 링크는 Visual Studio 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="63b0b-106">개발 환경과 하나 이상의 웹 브라우저 간에 통신 채널을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="63b0b-107">브라우저 링크를 사용 하 여 여러 브라우저에서 웹 앱을 한 번에 새로 고칠 수 있으며,이는 브라우저 간 테스트에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="63b0b-108">브라우저 링크 설정</span><span class="sxs-lookup"><span data-stu-id="63b0b-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="63b0b-109">[VisualStudio 링크](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 패키지를 프로젝트에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="63b0b-110">ASP.NET Core Razor Pages 또는 MVC 프로젝트의 경우 <xref:mvc/views/view-compilation>에 설명 된 대로 Razor (*cshtml*) 파일의 런타임 컴파일을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="63b0b-111">Razor 구문 변경 내용은 런타임 컴파일을 사용 하도록 설정한 경우에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="63b0b-112">ASP.NET Core 2.0 프로젝트를 ASP.NET Core 2.1로 변환 하 고 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)로 전환 하는 경우 브라우저 링크 기능을 위한 [VisualStudio 링크](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="63b0b-113">ASP.NET Core 2.1 프로젝트 템플릿은 기본적으로 `Microsoft.AspNetCore.App` 메타 패키지를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="63b0b-114">ASP.NET Core 2.0 **웹 응용 프로그램**, **빈**및 **Web API** 프로젝트 템플릿은 메타 패키지 링크를 포함 하는 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)에 대 한 패키지 참조를 포함 하는 [AspNetCore](xref:fundamentals/metapackage)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="63b0b-115">따라서 `Microsoft.AspNetCore.All` 메타 패키지를 사용 하려면 브라우저 링크를 사용할 수 있도록 하는 추가 작업이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="63b0b-116">ASP.NET Core 1.x **웹 응용 프로그램** 프로젝트 템플릿에는 [VisualStudio 링크](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 패키지에 대 한 패키지 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="63b0b-117">다른 프로젝트 형식에서는 `Microsoft.VisualStudio.Web.BrowserLink`에 대 한 패키지 참조를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="63b0b-118">구성</span><span class="sxs-lookup"><span data-stu-id="63b0b-118">Configuration</span></span>

<span data-ttu-id="63b0b-119">`Startup.Configure` 메서드에서 `UseBrowserLink`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="63b0b-120">`UseBrowserLink` 호출은 일반적으로 개발 환경에서 브라우저 링크만 사용 하도록 설정 하는 `if` 블록 내에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="63b0b-121">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="63b0b-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="63b0b-122">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="63b0b-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="63b0b-123">브라우저 링크를 사용 하는 방법</span><span class="sxs-lookup"><span data-stu-id="63b0b-123">How to use Browser Link</span></span>

<span data-ttu-id="63b0b-124">ASP.NET Core 프로젝트를 열면 Visual Studio에서 **디버그 대상** 도구 모음 컨트롤 옆에 브라우저 링크 도구 모음 컨트롤을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![브라우저 링크 드롭다운 메뉴](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="63b0b-126">브라우저 링크 도구 모음 컨트롤에서 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="63b0b-127">한 번에 여러 브라우저에서 웹 앱을 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="63b0b-128">**브라우저 링크 대시보드**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="63b0b-129">**브라우저 링크**를 사용 하거나 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="63b0b-130">참고: 브라우저 링크는 Visual Studio에서 기본적으로 사용 하지 않도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="63b0b-131">[CSS 자동 동기화](#enable-or-disable-css-auto-sync)를 사용 하거나 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="63b0b-132">한 번에 여러 브라우저에서 웹 앱을 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="63b0b-133">프로젝트를 시작할 때 실행할 단일 웹 브라우저를 선택 하려면 **디버그 대상** 도구 모음 컨트롤의 드롭다운 메뉴를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 드롭다운 메뉴](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="63b0b-135">한 번에 여러 브라우저를 열려면 동일한 드롭다운에서 **찾아보기** ...를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="63b0b-136"><kbd>Ctrl</kbd> 키를 누른 채 원하는 브라우저를 선택 하 고 **찾아보기**를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![한 번에 많은 브라우저 열기](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="63b0b-138">다음 스크린샷에서는 인덱스 뷰가 열려 있는 Visual Studio와 두 개의 열린 브라우저가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![두 브라우저의 동기화 예제](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="63b0b-140">브라우저 링크 도구 모음 컨트롤을 마우스로 가리켜 프로젝트에 연결 된 브라우저를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![가리키기 팁](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="63b0b-142">브라우저 링크 새로 고침 단추를 클릭 하면 인덱스 뷰를 변경 하 고 연결 된 모든 브라우저를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="63b0b-144">브라우저 링크는 Visual Studio 외부에서 시작 하 여 앱 URL로 이동 하는 브라우저 에서도 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="63b0b-145">브라우저 링크 대시보드</span><span class="sxs-lookup"><span data-stu-id="63b0b-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="63b0b-146">브라우저 링크 드롭다운 메뉴에서 **브라우저 링크 대시보드** 창을 열고 오픈 브라우저와의 연결을 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![browserslink-대시보드](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="63b0b-148">브라우저가 연결 되지 않은 경우 **브라우저에서 보기** 링크를 선택 하 여 디버깅 하지 않는 세션을 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="63b0b-150">그렇지 않으면 연결 된 브라우저가 각 브라우저가 표시 하는 페이지에 대 한 경로와 함께 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="63b0b-152">개별 브라우저 이름을 클릭 하 여 해당 브라우저만 새로 고칠 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="63b0b-153">브라우저 링크 사용 또는 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="63b0b-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="63b0b-154">브라우저 링크를 사용 하지 않도록 설정한 후 다시 사용 하도록 설정 하는 경우 브라우저를 새로 고쳐 다시 연결 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="63b0b-155">CSS 자동 동기화 사용 또는 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="63b0b-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="63b0b-156">CSS 자동 동기화를 사용 하도록 설정 하면 CSS 파일을 변경할 때 연결 된 브라우저가 자동으로 새로 고쳐집니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="63b0b-157">작동 방법</span><span class="sxs-lookup"><span data-stu-id="63b0b-157">How it works</span></span>

<span data-ttu-id="63b0b-158">브라우저 링크 [SignalR](xref:signalr/introduction) 를 사용 하 여 Visual Studio와 브라우저 간에 통신 채널을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="63b0b-159">브라우저 링크를 사용 하는 경우 Visual Studio는 여러 클라이언트 (브라우저)가 연결할 수 있는 SignalR 서버 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="63b0b-160">또한 브라우저 링크는 ASP.NET Core 요청 파이프라인에 미들웨어 구성 요소를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="63b0b-161">이 구성 요소는 서버의 모든 페이지 요청에 대 한 특수 `<script>` 참조를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="63b0b-162">브라우저에서 **소스 보기** 를 선택 하 고 `<body>` 태그 내용 끝으로 스크롤하면 스크립트 참조를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="63b0b-163">소스 파일은 수정 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-163">Your source files aren't modified.</span></span> <span data-ttu-id="63b0b-164">미들웨어 구성 요소는 스크립트 참조를 동적으로 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="63b0b-165">브라우저 쪽 코드는 모두 JavaScript 이므로 브라우저 플러그 인을 요구 하지 않고 SignalR 지 원하는 모든 브라우저에서 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="63b0b-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
