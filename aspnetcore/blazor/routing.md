---
title: ASP.NET Core Blazor 라우팅
author: guardrex
description: 앱에서 요청을 라우팅하는 방법과 NavLink 구성 요소를 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218897"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="ee00c-103">ASP.NET Core Blazor 라우팅</span><span class="sxs-lookup"><span data-stu-id="ee00c-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="ee00c-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="ee00c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ee00c-105">요청을 라우팅하는 방법과 Blazor 앱에서 `NavLink` 구성 요소를 사용하여 탐색 링크를 만드는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="ee00c-106">ASP.NET Core 엔드포인트 라우팅 통합</span><span class="sxs-lookup"><span data-stu-id="ee00c-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="ee00c-107">Blazor 서버는 [ASP.NET Core 엔드포인트 라우팅](xref:fundamentals/routing)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="ee00c-108">`Startup.Configure`의 `MapBlazorHub`를 사용하여 대화형 구성 요소에 대해 들어오는 연결을 허용하도록 ASP.NET Core 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="ee00c-109">가장 일반적인 구성은 Blazor 서버 앱의 서버 쪽 호스트 역할을 하는 Razor 페이지로 모든 요청을 라우팅하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="ee00c-110">규칙에 따라 ‘호스트’ 페이지의 이름은 일반적으로 *_Host.cshtml*입니다. </span><span class="sxs-lookup"><span data-stu-id="ee00c-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="ee00c-111">호스트 파일에 지정된 경로는 경로 일치 시 낮은 우선순위로 작동하기 때문에 ‘대체 경로’라고 합니다. </span><span class="sxs-lookup"><span data-stu-id="ee00c-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="ee00c-112">일치하는 다른 경로가 없는 경우 대체(fallback) 경로가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="ee00c-113">이렇게 하면 앱이 Blazor 서버 앱을 방해하지 않고 다른 컨트롤러와 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="ee00c-114">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="ee00c-114">Route templates</span></span>

<span data-ttu-id="ee00c-115">`Router` 구성 요소는 지정된 경로를 사용하여 각 구성 요소로 라우팅할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="ee00c-116">`Router` 구성 요소는 *App.razor* 파일에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-116">The `Router` component appears in the *App.razor* file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="ee00c-117">`@page` 지시문을 포함하는 *.razor* 파일을 컴파일하면, 생성된 클래스에 경로 템플릿을 지정하는 <xref:Microsoft.AspNetCore.Components.RouteAttribute>가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="ee00c-118">런타임에 `RouteView` 구성 요소는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="ee00c-119">원하는 매개 변수와 함께 `Router`에서 `RouteData`를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="ee00c-120">지정된 매개 변수를 사용하여 지정된 구성 요소를 해당 레이아웃(또는 선택적 기본 레이아웃)으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="ee00c-121">필요한 경우, 레이아웃을 지정하지 않는 구성 요소에 사용할 `DefaultLayout` 매개 변수를 레이아웃 클래스로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="ee00c-122">기본 Blazor 템플릿은 `MainLayout` 구성 요소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="ee00c-123">*MainLayout.razor*는 템플릿 프로젝트의 *Shared* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="ee00c-124">레이아웃에 대한 자세한 내용은 <xref:blazor/layouts>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee00c-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="ee00c-125">한 구성 요소에 여러 개의 경로 템플릿을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="ee00c-126">다음 구성 요소는 `/BlazorRoute` 및 `/DifferentBlazorRoute`에 대한 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="ee00c-127">URL이 올바르게 확인되려면 앱의 *wwwroot/index.html* 파일(Blazor WebAssembly) 또는 *Pages/_Host.cshtml* 파일(Blazor 서버)에 `<base>` 태그가 있고, 앱 기본 경로가 `href` 특성에 지정되어 있어야 합니다(`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="ee00c-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="ee00c-128">자세한 내용은 <xref:host-and-deploy/blazor/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee00c-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="ee00c-129">콘텐츠를 찾을 수 없는 경우 사용자 지정 콘텐츠 제공</span><span class="sxs-lookup"><span data-stu-id="ee00c-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="ee00c-130">`Router` 구성 요소를 사용하면 요청된 경로의 콘텐츠를 찾을 수 없는 경우 앱에서 사용자 지정 콘텐츠를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="ee00c-131">*App.razor* 파일에서 `Router` 구성 요소의 `NotFound` 템플릿 매개 변수에 사용자 지정 콘텐츠를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="ee00c-132">`<NotFound>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="ee00c-133">`NotFound` 콘텐츠에 기본 레이아웃을 적용하려면 <xref:blazor/layouts>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee00c-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="ee00c-134">여러 어셈블리에서 구성 요소로 라우팅</span><span class="sxs-lookup"><span data-stu-id="ee00c-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="ee00c-135">`AdditionalAssemblies` 매개 변수를 사용하여 라우팅 가능한 구성 요소를 검색할 때 고려할 추가 어셈블리를 `Router` 구성 요소에 대해 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="ee00c-136">지정한 어셈블리는 `AppAssembly`에서 지정한 어셈블리에 추가로 고려됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="ee00c-137">다음 예제에서 `Component1`은 참조된 클래스 라이브러리에서 정의된 라우팅 가능한 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="ee00c-138">다음 `AdditionalAssemblies` 예제에서는 `Component1`에 대해 라우팅 지원이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="ee00c-139">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="ee00c-139">Route parameters</span></span>

<span data-ttu-id="ee00c-140">라우터는 경로 매개 변수를 사용하여 해당 구성 요소 매개 변수를 동일한 이름(대/소문자 구분 안 함)으로 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="ee00c-141">선택적 매개 변수는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="ee00c-142">위의 예제에서는 두 개의 `@page` 지시문이 적용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="ee00c-143">첫 번째 지시문은 매개 변수 없이 구성 요소 탐색을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="ee00c-144">두 번째 `@page` 지시문은 `{text}` 경로 매개 변수를 사용하고 `Text` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="ee00c-145">경로 제약 조건</span><span class="sxs-lookup"><span data-stu-id="ee00c-145">Route constraints</span></span>

<span data-ttu-id="ee00c-146">경로 제약 조건은 경로 세그먼트의 형식 일치를 구성 요소에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="ee00c-147">다음 예제에서 `Users` 구성 요소의 경로는 다음과 같은 경우에만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="ee00c-148">요청 URL에 `Id` 경로 세그먼트가 있는 경우</span><span class="sxs-lookup"><span data-stu-id="ee00c-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="ee00c-149">`Id` 세그먼트가 정수(`int`)인 경우</span><span class="sxs-lookup"><span data-stu-id="ee00c-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="ee00c-150">다음 표에 나와 있는 경로 제약 조건을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="ee00c-151">고정 문화권과 일치하는 경로 제약 조건에 대한 자세한 내용은 표 아래에 있는 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee00c-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="ee00c-152">제약 조건</span><span class="sxs-lookup"><span data-stu-id="ee00c-152">Constraint</span></span> | <span data-ttu-id="ee00c-153">예제</span><span class="sxs-lookup"><span data-stu-id="ee00c-153">Example</span></span>           | <span data-ttu-id="ee00c-154">일치하는 예제</span><span class="sxs-lookup"><span data-stu-id="ee00c-154">Example Matches</span></span>                                                                  | <span data-ttu-id="ee00c-155">고정</span><span class="sxs-lookup"><span data-stu-id="ee00c-155">Invariant</span></span><br><span data-ttu-id="ee00c-156">culture</span><span class="sxs-lookup"><span data-stu-id="ee00c-156">culture</span></span><br><span data-ttu-id="ee00c-157">일치</span><span class="sxs-lookup"><span data-stu-id="ee00c-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="ee00c-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="ee00c-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="ee00c-159">아니요</span><span class="sxs-lookup"><span data-stu-id="ee00c-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="ee00c-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="ee00c-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="ee00c-161">예</span><span class="sxs-lookup"><span data-stu-id="ee00c-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="ee00c-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="ee00c-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="ee00c-163">예</span><span class="sxs-lookup"><span data-stu-id="ee00c-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="ee00c-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="ee00c-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="ee00c-165">예</span><span class="sxs-lookup"><span data-stu-id="ee00c-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="ee00c-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="ee00c-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="ee00c-167">예</span><span class="sxs-lookup"><span data-stu-id="ee00c-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="ee00c-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="ee00c-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="ee00c-169">아니요</span><span class="sxs-lookup"><span data-stu-id="ee00c-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="ee00c-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="ee00c-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="ee00c-171">예</span><span class="sxs-lookup"><span data-stu-id="ee00c-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="ee00c-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="ee00c-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="ee00c-173">예</span><span class="sxs-lookup"><span data-stu-id="ee00c-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="ee00c-174">CLR 형식(예: `int` 또는 `DateTime`)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="ee00c-175">이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="ee00c-176">점이 포함된 URL을 사용한 라우팅</span><span class="sxs-lookup"><span data-stu-id="ee00c-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="ee00c-177">Blazor 서버 앱에서 *_Host.cshtml*의 기본 경로는 `/`(`@page "/"`)입니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="ee00c-178">점(`.`)이 포함된 요청 URL은 URL이 파일을 요청하는 것 같으므로 기본 경로와 일치되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="ee00c-179">Blazor 앱은 존재하지 않는 정적 파일에 대해 ‘404 - 찾을 수 없음’ 응답을 반환합니다. </span><span class="sxs-lookup"><span data-stu-id="ee00c-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="ee00c-180">점이 포함된 경로를 사용하려면 다음 경로 템플릿을 통해 *_Host.cshtml*을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="ee00c-181">`"/{**path}"` 템플릿에는 다음이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="ee00c-182">슬래시(`/`)를 인코딩하지 않고 여러 폴더 경계에 걸쳐 있는 경로를 캡처하기 위한 이중 별표 *catch-all* 구문(`**`)</span><span class="sxs-lookup"><span data-stu-id="ee00c-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="ee00c-183">`path` 경로 매개 변수 이름</span><span class="sxs-lookup"><span data-stu-id="ee00c-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="ee00c-184">*Catch-all* 매개 변수 구문(`*`/`**`)은 Razor 구성 요소( *.razor*)에서 지원되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="ee00c-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="ee00c-185">자세한 내용은 <xref:fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee00c-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="ee00c-186">NavLink 구성 요소</span><span class="sxs-lookup"><span data-stu-id="ee00c-186">NavLink component</span></span>

<span data-ttu-id="ee00c-187">탐색 링크를 만드는 경우 HTML 하이퍼링크 요소(`<a>`) 대신 `NavLink` 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="ee00c-188">`NavLink` 구성 요소는 `href`가 현재 URL과 일치하는지 아닌지에 따라 `active` CSS 클래스를 전환한다는 점을 제외하고 `<a>` 요소처럼 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="ee00c-189">`active` 클래스는 사용자가 표시되는 탐색 링크 중에서 활성 페이지를 파악하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="ee00c-190">다음 `NavMenu` 구성 요소는 `NavLink` 구성 요소를 사용하는 방법을 보여 주는 [부트스트랩](https://getbootstrap.com/docs/) 탐색 모음을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="ee00c-191">`<NavLink>` 요소의 `Match` 특성에 할당할 수 있는 다음 두 가지 `NavLinkMatch` 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="ee00c-192">`NavLinkMatch.All` - 현재 URL 전체와 일치하는 경우 `NavLink`가 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="ee00c-193">`NavLinkMatch.Prefix`(‘기본값’) - 현재 URL의 접두사와 일치하는 경우 `NavLink`가 활성 상태입니다. </span><span class="sxs-lookup"><span data-stu-id="ee00c-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="ee00c-194">위의 예제에서 홈 `NavLink` `href=""`는 홈 URL과 일치하고, 앱의 기본 경로 URL(예: `https://localhost:5001/`)에 있는 `active` CSS 클래스만 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="ee00c-195">두 번째 `NavLink`는 사용자가 `MyComponent` 접두사를 포함하는 URL(예: `https://localhost:5001/MyComponent` 및 `https://localhost:5001/MyComponent/AnotherSegment`)을 방문할 때 `active` 클래스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="ee00c-196">추가 `NavLink` 구성 요소 특성이 렌더링된 앵커 태그로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="ee00c-197">다음 예제에서 `NavLink` 구성 요소는 `target` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="ee00c-198">다음 HTML 태그가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="ee00c-199">URI 및 탐색 상태 도우미</span><span class="sxs-lookup"><span data-stu-id="ee00c-199">URI and navigation state helpers</span></span>

<span data-ttu-id="ee00c-200"><xref:Microsoft.AspNetCore.Components.NavigationManager>를 사용하여 C# 코드로 URI 및 탐색 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="ee00c-201">`NavigationManager`는 다음 표에 나와 있는 이벤트와 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="ee00c-202">멤버</span><span class="sxs-lookup"><span data-stu-id="ee00c-202">Member</span></span> | <span data-ttu-id="ee00c-203">설명</span><span class="sxs-lookup"><span data-stu-id="ee00c-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="ee00c-204">URI</span><span class="sxs-lookup"><span data-stu-id="ee00c-204">Uri</span></span> | <span data-ttu-id="ee00c-205">현재 절대 URI를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="ee00c-206">BaseUri</span><span class="sxs-lookup"><span data-stu-id="ee00c-206">BaseUri</span></span> | <span data-ttu-id="ee00c-207">절대 URI를 생성하기 위해 상대 URI 경로 앞에 추가할 수 있는 기본 URI(후행 슬래시 포함)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="ee00c-208">일반적으로 `BaseUri`는 *wwwroot/index.html*(Blazor WebAssembly) 또는 *Pages/_Host.cshtml*(Blazor 서버)에 있는 문서 `<base>` 요소의 `href` 특성에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="ee00c-209">NavigateTo</span><span class="sxs-lookup"><span data-stu-id="ee00c-209">NavigateTo</span></span> | <span data-ttu-id="ee00c-210">지정한 URI로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-210">Navigates to the specified URI.</span></span> <span data-ttu-id="ee00c-211">`forceLoad`가 `true`인 경우</span><span class="sxs-lookup"><span data-stu-id="ee00c-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="ee00c-212">클라이언트 쪽 라우팅이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="ee00c-213">클라이언트 쪽 라우터에서 URI를 정상적으로 처리했는지와 상관없이 브라우저에서 서버의 새 페이지를 강제로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="ee00c-214">LocationChanged</span><span class="sxs-lookup"><span data-stu-id="ee00c-214">LocationChanged</span></span> | <span data-ttu-id="ee00c-215">탐색 위치가 변경된 경우에 발생하는 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="ee00c-216">ToAbsoluteUri</span><span class="sxs-lookup"><span data-stu-id="ee00c-216">ToAbsoluteUri</span></span> | <span data-ttu-id="ee00c-217">상대 URI를 절대 URI로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="ee00c-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="ee00c-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="ee00c-219">기본 URI(예: 이전에 `GetBaseUri`에서 반환된 URI)가 제공된 경우, 절대 URI를 기본 URI 접두사의 상대 URI로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="ee00c-220">단추를 선택하면 다음 구성 요소가 앱의 `Counter` 구성 요소로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="ee00c-221">다음 구성 요소는 위치 변경 이벤트를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-221">The following component handles a location changed event.</span></span> <span data-ttu-id="ee00c-222">`HandleLocationChanged` 메서드는 프레임워크에서 `Dispose`를 호출할 때 언후크됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="ee00c-223">메서드를 언후크하면 구성 요소의 가비지 수집이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-223">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implement IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="ee00c-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>는 이벤트에 대해 다음과 같은 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="ee00c-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; 새 위치의 URL</span><span class="sxs-lookup"><span data-stu-id="ee00c-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="ee00c-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; `true`인 경우 Blazor는 브라우저에서 탐색을 가로챕니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="ee00c-227">`false`인 경우 [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A)에서 탐색을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ee00c-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="ee00c-228">구성 요소 삭제에 대한 자세한 내용은 <xref:blazor/lifecycle#component-disposal-with-idisposable>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee00c-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
