---
title: ASP.NET Core Blazor 라우팅
author: guardrex
description: 앱에서 요청을 라우팅하는 방법 및 NavLink 구성 요소에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 32459f9f42220b01ce04e6444a9bb4a9592ee2da
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928279"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="51e70-103">ASP.NET Core Blazor 라우팅</span><span class="sxs-lookup"><span data-stu-id="51e70-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="51e70-104">작성자: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="51e70-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="51e70-105">Blazor apps에서 요청을 라우팅하는 방법 및 `NavLink` 구성 요소를 사용 하 여 탐색 링크를 만드는 방법에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="51e70-106">ASP.NET Core 끝점 라우팅 통합</span><span class="sxs-lookup"><span data-stu-id="51e70-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="51e70-107">Blazor 서버는 [ASP.NET Core 끝점 라우팅](xref:fundamentals/routing)에 통합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="51e70-108">ASP.NET Core 앱은 `Startup.Configure`에서 `MapBlazorHub`를 사용 하 여 대화형 구성 요소에 대 한 들어오는 연결을 허용 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="51e70-109">가장 일반적인 구성은 모든 요청을 Blazor Server 앱의 서버 쪽에 대 한 호스트 역할을 하는 Razor 페이지로 라우팅하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="51e70-110">규칙에 따라 *호스트* 페이지의 이름은 일반적으로 *_Host. cshtml*입니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="51e70-111">호스트 파일에 지정 된 경로는 경로 일치에서 낮은 우선 순위로 작동 하므로 *대체 경로* 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="51e70-112">다른 경로가 일치 하지 않는 경우 대체 (fallback) 경로를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="51e70-113">이렇게 하면 앱이 Blazor 서버 앱을 방해 하지 않고 다른 컨트롤러 및 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="51e70-114">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="51e70-114">Route templates</span></span>

<span data-ttu-id="51e70-115">`Router` 구성 요소는 지정 된 경로를 사용 하 여 각 구성 요소로 라우팅할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="51e70-116">`Router` 구성 요소가 *응용 프로그램 razor* 파일에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="51e70-117">`@page` 지시문이 있는 *razor* 파일이 컴파일되면 생성 된 클래스에 경로 템플릿을 지정 하는 <xref:Microsoft.AspNetCore.Components.RouteAttribute> 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="51e70-118">런타임에 `RouteView` 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="51e70-119">원하는 매개 변수와 함께 `Router`에서 `RouteData`을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="51e70-120">지정 된 매개 변수를 사용 하 여 지정 된 구성 요소를 해당 레이아웃 또는 선택적 기본 레이아웃으로 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="51e70-121">레이아웃을 지정 하지 않는 구성 요소에 사용할 레이아웃 클래스가 포함 된 `DefaultLayout` 매개 변수를 선택적으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="51e70-122">기본 Blazor 템플릿은 `MainLayout` 구성 요소를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="51e70-123">*Mainlayout. razor* 는 템플릿 프로젝트의 *공유* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="51e70-124">레이아웃에 대 한 자세한 내용은 <xref:blazor/layouts>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="51e70-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="51e70-125">여러 경로 템플릿을 구성 요소에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="51e70-126">다음 구성 요소는 `/BlazorRoute` 및 `/DifferentBlazorRoute`에 대 한 요청에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="51e70-127">Url이 올바르게 확인 될 수 있도록 앱은 *wwwroot/index.html* 파일 (Blazor Weasembome) 또는 *Pages/_Host. Cshtml* 파일 (Blazor Server)에 `href` 특성에 지정 된 앱 기본 경로 (`<base href="/">`)를 `<base>` 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="51e70-128">자세한 내용은 <xref:host-and-deploy/blazor/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="51e70-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="51e70-129">콘텐츠를 찾을 수 없는 경우 사용자 지정 콘텐츠 제공</span><span class="sxs-lookup"><span data-stu-id="51e70-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="51e70-130">요청 된 경로에 대 한 콘텐츠를 찾을 수 없는 경우 `Router` 구성 요소를 사용 하 여 앱에서 사용자 지정 콘텐츠를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="51e70-131">*응용 프로그램 razor* 파일에서 `Router` 구성 요소의 `NotFound` template 매개 변수에 사용자 지정 콘텐츠를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="51e70-132">`<NotFound>` 태그의 내용에는 다른 대화형 구성 요소와 같은 임의의 항목이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="51e70-133">`NotFound` 콘텐츠에 기본 레이아웃을 적용 하려면 <xref:blazor/layouts>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="51e70-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="51e70-134">여러 어셈블리에서 구성 요소로 라우팅</span><span class="sxs-lookup"><span data-stu-id="51e70-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="51e70-135">`AdditionalAssemblies` 매개 변수를 사용 하 여 라우팅할 수 있는 구성 요소를 검색할 때 고려할 `Router` 구성 요소에 대 한 추가 어셈블리를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="51e70-136">지정 된 어셈블리는 `AppAssembly`지정 된 어셈블리 외에도 고려 됩니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="51e70-137">다음 예제에서 `Component1`는 참조 된 클래스 라이브러리에 정의 된 라우팅 가능한 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="51e70-138">다음 `AdditionalAssemblies` 예제에서는 `Component1`에 대 한 라우팅을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="51e70-139">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="51e70-139">Route parameters</span></span>

<span data-ttu-id="51e70-140">라우터는 경로 매개 변수를 사용 하 여 해당 구성 요소 매개 변수를 동일한 이름 (대/소문자 구분 안 함)으로 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="51e70-141">선택적 매개 변수는 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="51e70-142">이전 예제에서는 두 개의 `@page` 지시문이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="51e70-143">첫 번째는 매개 변수 없이 구성 요소에 대 한 탐색을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="51e70-144">두 번째 `@page` 지시어는 `{text}` route 매개 변수를 사용 하 여 `Text` 속성에 값을 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="51e70-145">경로 제약 조건</span><span class="sxs-lookup"><span data-stu-id="51e70-145">Route constraints</span></span>

<span data-ttu-id="51e70-146">경로 제약 조건은 경로 세그먼트에 대 한 형식 일치를 구성 요소에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="51e70-147">다음 예제에서 `Users` 구성 요소에 대 한 경로는 다음과 같은 경우에만 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="51e70-148">요청 URL에 `Id` 경로 세그먼트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="51e70-149">`Id` 세그먼트가 정수 (`int`)입니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="51e70-150">다음 표에 표시 된 경로 제약 조건을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="51e70-151">고정 문화권과 일치 하는 경로 제약 조건에 대 한 자세한 내용은 테이블 아래 경고를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="51e70-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="51e70-152">제약 조건</span><span class="sxs-lookup"><span data-stu-id="51e70-152">Constraint</span></span> | <span data-ttu-id="51e70-153">예</span><span class="sxs-lookup"><span data-stu-id="51e70-153">Example</span></span>           | <span data-ttu-id="51e70-154">일치하는 예제</span><span class="sxs-lookup"><span data-stu-id="51e70-154">Example Matches</span></span>                                                                  | <span data-ttu-id="51e70-155">고정</span><span class="sxs-lookup"><span data-stu-id="51e70-155">Invariant</span></span><br><span data-ttu-id="51e70-156">Culture</span><span class="sxs-lookup"><span data-stu-id="51e70-156">culture</span></span><br><span data-ttu-id="51e70-157">일치</span><span class="sxs-lookup"><span data-stu-id="51e70-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="51e70-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="51e70-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="51e70-159">아니요</span><span class="sxs-lookup"><span data-stu-id="51e70-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="51e70-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="51e70-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="51e70-161">예</span><span class="sxs-lookup"><span data-stu-id="51e70-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="51e70-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="51e70-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="51e70-163">예</span><span class="sxs-lookup"><span data-stu-id="51e70-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="51e70-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="51e70-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="51e70-165">예</span><span class="sxs-lookup"><span data-stu-id="51e70-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="51e70-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="51e70-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="51e70-167">예</span><span class="sxs-lookup"><span data-stu-id="51e70-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="51e70-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="51e70-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="51e70-169">아니요</span><span class="sxs-lookup"><span data-stu-id="51e70-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="51e70-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="51e70-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="51e70-171">예</span><span class="sxs-lookup"><span data-stu-id="51e70-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="51e70-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="51e70-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="51e70-173">예</span><span class="sxs-lookup"><span data-stu-id="51e70-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="51e70-174">CLR 형식(예: `int` 또는 `DateTime`)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="51e70-175">이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="51e70-176">점이 포함 된 Url로 라우팅</span><span class="sxs-lookup"><span data-stu-id="51e70-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="51e70-177">Blazor Server 앱에서 *_Host* 의 기본 경로는 `/` (`@page "/"`)입니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="51e70-178">URL이 파일을 요청 하는 것으로 나타나기 때문에 점 (`.`)을 포함 하는 요청 URL이 기본 경로와 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="51e70-179">Blazor 앱은 존재 하지 않는 정적 파일에 대해 *404-찾을 수* 없음 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="51e70-180">점이 포함 된 경로를 사용 하려면 다음 경로 템플릿을 사용 하 여 _Host를 구성 *합니다* .</span><span class="sxs-lookup"><span data-stu-id="51e70-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="51e70-181">`"/{**path}"` 템플릿에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="51e70-182">큰따옴표 (`/`)를 사용 하지 않고 여러 폴더 경계에서 경로를 캡처하기 위한 두 개의 별표를 사용 하는 *모든* 구문 (`**`)입니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="51e70-183">`path` 경로 매개 변수 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="51e70-184">*Catch-모든* 매개 변수 구문 (`*`/`**`)은 razor 구성 요소 (*razor*)에서 지원 **되지 않습니다** .</span><span class="sxs-lookup"><span data-stu-id="51e70-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="51e70-185">자세한 내용은 <xref:fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="51e70-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="51e70-186">NavLink 구성 요소</span><span class="sxs-lookup"><span data-stu-id="51e70-186">NavLink component</span></span>

<span data-ttu-id="51e70-187">탐색 링크를 만들 때 HTML 하이퍼링크 요소 (`<a>`) 대신 `NavLink` 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="51e70-188">`NavLink` 구성 요소는 `href` 현재 URL과 일치 하는지 여부에 따라 `active` CSS 클래스를 전환 하는 것을 제외 하 고 `<a>` 요소 처럼 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="51e70-189">사용자는 `active` 클래스를 사용 하 여 표시 된 탐색 링크 중에서 활성 페이지가 되는 페이지를 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="51e70-190">다음 `NavMenu` 구성 요소는 `NavLink` 구성 요소를 사용하는 방법을 보여 주는 [부트스트랩](https://getbootstrap.com/docs/) 탐색 모음을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="51e70-191">`<NavLink>` 요소의 `Match` 특성에 할당할 수 있는 두 가지 `NavLinkMatch` 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="51e70-192">`NavLinkMatch.All` &ndash; 전체 현재 URL과 일치 하는 `NavLink` 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="51e70-193">`NavLinkMatch.Prefix` (*기본값*) &ndash; `NavLink` 현재 URL의 접두사와 일치 하는 경우 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="51e70-194">앞의 예제에서 Home `NavLink` `href=""`는 홈 URL과 일치 하 고 앱의 기본 기본 경로 URL에서 `active` CSS 클래스 (예: `https://localhost:5001/`)만 받습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="51e70-195">두 번째 `NavLink`는 사용자가 `MyComponent` 접두사 (예: `https://localhost:5001/MyComponent` 및 `https://localhost:5001/MyComponent/AnotherSegment`)를 사용 하 여 URL을 방문할 때 `active` 클래스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="51e70-196">추가 `NavLink` 구성 요소 특성이 렌더링 된 앵커 태그로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="51e70-197">다음 예제에서 `NavLink` 구성 요소는 `target` 특성을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="51e70-198">렌더링 되는 HTML 태그는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="51e70-199">URI 및 탐색 상태 도우미</span><span class="sxs-lookup"><span data-stu-id="51e70-199">URI and navigation state helpers</span></span>

<span data-ttu-id="51e70-200">`Microsoft.AspNetCore.Components.NavigationManager`를 사용 하 여 코드에서 C# uri 및 탐색 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-200">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="51e70-201">`NavigationManager`는 다음 표에 나와 있는 이벤트와 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="51e70-202">Member</span><span class="sxs-lookup"><span data-stu-id="51e70-202">Member</span></span> | <span data-ttu-id="51e70-203">설명</span><span class="sxs-lookup"><span data-stu-id="51e70-203">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="51e70-204">현재 절대 URI를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-204">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="51e70-205">절대 URI를 생성 하기 위해 상대 URI 경로 앞에 추가할 수 있는 기본 URI (후행 슬래시 포함)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-205">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="51e70-206">일반적으로 `<base>` `href` *`BaseUri`는* *wwwroot/index.html* (Blazor weasembmbembembmbembembembmbembembembmbembembmbembembembembemb_Host mbe</span><span class="sxs-lookup"><span data-stu-id="51e70-206">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| `NavigateTo` | <span data-ttu-id="51e70-207">지정 된 URI로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-207">Navigates to the specified URI.</span></span> <span data-ttu-id="51e70-208">`forceLoad` `true`경우:</span><span class="sxs-lookup"><span data-stu-id="51e70-208">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="51e70-209">클라이언트 쪽 라우팅이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-209">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="51e70-210">URI가 일반적으로 클라이언트 쪽 라우터에서 처리 되는지 여부와 상관 없이 브라우저는 서버에서 새 페이지를 강제로 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-210">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="51e70-211">탐색 위치가 변경 될 때 발생 하는 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-211">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="51e70-212">상대 URI를 절대 URI로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-212">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="51e70-213">기본 URI가 지정 된 경우 (예: 이전에 `GetBaseUri`에서 반환 된 URI)는 절대 URI를 기본 URI 접두사에 상대적인 URI로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-213">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="51e70-214">단추를 선택 하면 다음 구성 요소가 앱의 `Counter` 구성 요소로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="51e70-214">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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
