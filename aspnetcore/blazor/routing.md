---
title: ASP.NET Core Blazor 라우팅
author: guardrex
description: 앱에서 요청을 라우팅하는 방법 및 NavLink 구성 요소에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/routing
ms.openlocfilehash: 1c61eedf7dbf0bbc8796eaa11360783b9d7aba6c
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963865"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="dd54f-103">ASP.NET Core Blazor 라우팅</span><span class="sxs-lookup"><span data-stu-id="dd54f-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="dd54f-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="dd54f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dd54f-105">Blazor apps에서 요청을 라우팅하는 방법 및 `NavLink` 구성 요소를 사용 하 여 탐색 링크를 만드는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="dd54f-106">ASP.NET Core 끝점 라우팅 통합</span><span class="sxs-lookup"><span data-stu-id="dd54f-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="dd54f-107">Blazor 서버는 [ASP.NET Core 끝점 라우팅](xref:fundamentals/routing)에 통합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="dd54f-108">ASP.NET Core 앱은 `MapBlazorHub` 에서 `Startup.Configure`대화형 구성 요소에 대 한 들어오는 연결을 허용 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="dd54f-109">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="dd54f-109">Route templates</span></span>

<span data-ttu-id="dd54f-110">구성 `Router` 요소는 지정 된 경로를 사용 하 여 각 구성 요소로 라우팅할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-110">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="dd54f-111">구성 요소가 다음과 같이 *응용 프로그램 razor* 파일에 나타납니다. `Router`</span><span class="sxs-lookup"><span data-stu-id="dd54f-111">The `Router` component appears in the *App.razor* file:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="dd54f-112">`@page` 지시문을 사용 하는 *razor* 파일이 컴파일되면 생성 된 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 클래스에 경로 템플릿을 지정 하는이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-112">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="dd54f-113">런타임에 구성 요소는 `RouteView` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-113">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="dd54f-114">원하는 매개 변수와 함께에서을 받습니다. `Router` `RouteData`</span><span class="sxs-lookup"><span data-stu-id="dd54f-114">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="dd54f-115">지정 된 매개 변수를 사용 하 여 지정 된 구성 요소를 해당 레이아웃 또는 선택적 기본 레이아웃으로 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-115">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="dd54f-116">레이아웃을 지정 하지 않는 `DefaultLayout` 구성 요소에 사용할 레이아웃 클래스가 포함 된 매개 변수를 선택적으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-116">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="dd54f-117">기본 Blazor 템플릿은 구성 요소를 `MainLayout` 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-117">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="dd54f-118">*Mainlayout. razor* 는 템플릿 프로젝트의 *공유* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-118">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="dd54f-119">레이아웃에 대 한 자세한 내용은을 <xref:blazor/layouts>참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="dd54f-119">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="dd54f-120">여러 경로 템플릿을 구성 요소에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-120">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="dd54f-121">다음 구성 요소는 및 `/BlazorRoute` `/DifferentBlazorRoute`에 대 한 요청에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-121">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="dd54f-122">Url이 올바르게 확인 될 수 `<base>` 있도록 앱은 `href` 특성 에지정된앱기본경로를사용하여wwwroot/index.html파일(Blazorweasembome)또는Pages/_Host파일(BlazorServer)에태그를포함해야합니다.`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="dd54f-122">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="dd54f-123">자세한 내용은 <xref:host-and-deploy/blazor/index#app-base-path>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd54f-123">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="dd54f-124">콘텐츠를 찾을 수 없는 경우 사용자 지정 콘텐츠 제공</span><span class="sxs-lookup"><span data-stu-id="dd54f-124">Provide custom content when content isn't found</span></span>

<span data-ttu-id="dd54f-125">요청 된 경로에 대 한 콘텐츠를 찾을 수 없는 경우 구성요소를사용하여앱에서사용자지정콘텐츠를지정할수있습니다.`Router`</span><span class="sxs-lookup"><span data-stu-id="dd54f-125">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="dd54f-126">*응용 프로그램 razor* 파일에서 `NotFound` `Router` 구성 요소의 템플릿 매개 변수에 사용자 지정 콘텐츠를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-126">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

```cshtml
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

<span data-ttu-id="dd54f-127">태그의 `<NotFound>` 내용에는 다른 대화형 구성 요소와 같은 임의의 항목이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-127">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="dd54f-128">`NotFound` 콘텐츠에 기본 레이아웃을 적용 하려면를 참조 <xref:blazor/layouts>하십시오.</span><span class="sxs-lookup"><span data-stu-id="dd54f-128">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="dd54f-129">여러 어셈블리에서 구성 요소로 라우팅</span><span class="sxs-lookup"><span data-stu-id="dd54f-129">Route to components from multiple assemblies</span></span>

<span data-ttu-id="dd54f-130">매개 변수를 사용 하 여 라우팅할 수 있는 `Router` 구성 요소를 검색할 때 고려할 구성 요소에 대 한 추가 어셈블리를 지정 합니다. `AdditionalAssemblies`</span><span class="sxs-lookup"><span data-stu-id="dd54f-130">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="dd54f-131">지정 된 어셈블리는 지정 된 어셈블리 외 `AppAssembly`에도 고려 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-131">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="dd54f-132">다음 예제 `Component1` 에서는 참조 된 클래스 라이브러리에 정의 된 라우팅할 수 있는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-132">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="dd54f-133">다음 `AdditionalAssemblies` 예에서는에 대 한 `Component1`라우팅을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-133">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

<span data-ttu-id="dd54f-134">< Router AppAssembly = "typeof (Program). 어셈블리 "AdditionalAssemblies =" new [] {typeof (Component1). Assembly} > ...</Router></span><span class="sxs-lookup"><span data-stu-id="dd54f-134"><Router AppAssembly="typeof(Program).Assembly" AdditionalAssemblies="new[] { typeof(Component1).Assembly }> ... </Router></span></span>

## <a name="route-parameters"></a><span data-ttu-id="dd54f-135">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="dd54f-135">Route parameters</span></span>

<span data-ttu-id="dd54f-136">라우터는 경로 매개 변수를 사용 하 여 해당 구성 요소 매개 변수를 동일한 이름 (대/소문자 구분 안 함)으로 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-136">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="dd54f-137">선택적 매개 변수는 ASP.NET Core 3.0 미리 보기에서 Blazor apps에 대해 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-137">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="dd54f-138">이전 `@page` 예제에서는 두 개의 지시문이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-138">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="dd54f-139">첫 번째는 매개 변수 없이 구성 요소에 대 한 탐색을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-139">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="dd54f-140">두 번째 `@page` 지시문은 `{text}` 경로 매개 변수를 사용 하 여 값을 `Text` 속성에 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-140">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="dd54f-141">경로 제약 조건</span><span class="sxs-lookup"><span data-stu-id="dd54f-141">Route constraints</span></span>

<span data-ttu-id="dd54f-142">경로 제약 조건은 경로 세그먼트에 대 한 형식 일치를 구성 요소에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-142">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="dd54f-143">다음 예제에서 `Users` 구성 요소에 대 한 경로는 다음과 같은 경우에만 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-143">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="dd54f-144">요청 URL에 경로 세그먼트가 있습니다. `Id`</span><span class="sxs-lookup"><span data-stu-id="dd54f-144">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="dd54f-145">세그먼트가 정수 (`int`)입니다. `Id`</span><span class="sxs-lookup"><span data-stu-id="dd54f-145">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="dd54f-146">다음 표에 표시 된 경로 제약 조건을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-146">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="dd54f-147">고정 문화권과 일치 하는 경로 제약 조건에 대 한 자세한 내용은 테이블 아래 경고를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="dd54f-147">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="dd54f-148">제약 조건</span><span class="sxs-lookup"><span data-stu-id="dd54f-148">Constraint</span></span> | <span data-ttu-id="dd54f-149">예제</span><span class="sxs-lookup"><span data-stu-id="dd54f-149">Example</span></span>           | <span data-ttu-id="dd54f-150">일치하는 예제</span><span class="sxs-lookup"><span data-stu-id="dd54f-150">Example Matches</span></span>                                                                  | <span data-ttu-id="dd54f-151">고정</span><span class="sxs-lookup"><span data-stu-id="dd54f-151">Invariant</span></span><br><span data-ttu-id="dd54f-152">Culture</span><span class="sxs-lookup"><span data-stu-id="dd54f-152">culture</span></span><br><span data-ttu-id="dd54f-153">일치</span><span class="sxs-lookup"><span data-stu-id="dd54f-153">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="dd54f-154">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="dd54f-154">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="dd54f-155">아니요</span><span class="sxs-lookup"><span data-stu-id="dd54f-155">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="dd54f-156">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="dd54f-156">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="dd54f-157">예</span><span class="sxs-lookup"><span data-stu-id="dd54f-157">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="dd54f-158">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="dd54f-158">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="dd54f-159">예</span><span class="sxs-lookup"><span data-stu-id="dd54f-159">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="dd54f-160">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="dd54f-160">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="dd54f-161">예</span><span class="sxs-lookup"><span data-stu-id="dd54f-161">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="dd54f-162">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="dd54f-162">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="dd54f-163">예</span><span class="sxs-lookup"><span data-stu-id="dd54f-163">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="dd54f-164">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="dd54f-164">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="dd54f-165">아니요</span><span class="sxs-lookup"><span data-stu-id="dd54f-165">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="dd54f-166">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="dd54f-166">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="dd54f-167">예</span><span class="sxs-lookup"><span data-stu-id="dd54f-167">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="dd54f-168">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="dd54f-168">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="dd54f-169">예</span><span class="sxs-lookup"><span data-stu-id="dd54f-169">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="dd54f-170">CLR 형식(예: `int` 또는 `DateTime`)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-170">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="dd54f-171">이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-171">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="dd54f-172">점이 포함 된 Url로 라우팅</span><span class="sxs-lookup"><span data-stu-id="dd54f-172">Routing with URLs that contain dots</span></span>

<span data-ttu-id="dd54f-173">Blazor Server 앱에서 *_Host* `/` 의 기본 경로는 (`@page "/"`)입니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-173">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="dd54f-174">Url이 파일을 요청 하는 것`.`으로 나타나기 때문에 점 ()이 포함 된 요청 url이 기본 경로와 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-174">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="dd54f-175">Blazor 앱은 존재 하지 않는 정적 파일에 대해 *404-찾을 수* 없음 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-175">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="dd54f-176">점이 포함 된 경로를 사용 하려면 다음 경로 템플릿을 사용 하 여 _Host를 구성 *합니다* .</span><span class="sxs-lookup"><span data-stu-id="dd54f-176">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="dd54f-177">템플릿은 `"/{**path}"` 다음을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-177">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="dd54f-178">큰따옴표 ()를 *사용* 하 여 여러`**`폴더 경계에서 경로를 캡처하는 두 개의 별표를 사용 하 여`/`슬래시 ()를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-178">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="dd54f-179">`path` 경로 매개 변수 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-179">A `path` route parameter name.</span></span>

<span data-ttu-id="dd54f-180">자세한 내용은 <xref:fundamentals/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd54f-180">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="dd54f-181">NavLink 구성 요소</span><span class="sxs-lookup"><span data-stu-id="dd54f-181">NavLink component</span></span>

<span data-ttu-id="dd54f-182">탐색 링크 `NavLink` 를 만들 때 HTML 하이퍼링크 요소 (`<a>`) 대신 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-182">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="dd54f-183">구성 `NavLink` 요소는 `<a>` 요소가 현재 URL `href` 과 일치 하는지 여부에 `active` 따라 CSS 클래스를 전환 하는 것을 제외 하 고 요소 처럼 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-183">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="dd54f-184">클래스 `active` 를 통해 사용자는 표시 된 탐색 링크 중에서 활성 페이지가 되는 페이지를 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-184">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="dd54f-185">다음 `NavMenu` 구성 요소는 `NavLink` 구성 요소를 사용하는 방법을 보여 주는 [부트스트랩](https://getbootstrap.com/docs/) 탐색 모음을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-185">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="dd54f-186">요소의`<NavLink>` `NavLinkMatch` 특성`Match` 에 할당할 수 있는 두 가지 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-186">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="dd54f-187">`NavLinkMatch.All`&ndash; 는`NavLink` 전체 현재 URL과 일치 하는 경우 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-187">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="dd54f-188">`NavLinkMatch.Prefix`(*기본값*) &ndash; 는`NavLink` 현재 URL의 접두사와 일치 하는 경우 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-188">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="dd54f-189">`NavLink` 위의 예제에서 홈 `href=""` 은 홈 `active` URL과 일치 하 고 앱의 `https://localhost:5001/`기본 기본 경로 URL (예:)에서 CSS 클래스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-189">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="dd54f-190">두 번째 `NavLink` 는 사용자 `active` 가 `MyComponent` 접두사 (예: 및 `https://localhost:5001/MyComponent/AnotherSegment`)를 사용 하 여 URL을 `https://localhost:5001/MyComponent` 방문할 때 클래스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-190">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="dd54f-191">추가 `NavLink` 구성 요소 특성이 렌더링 된 앵커 태그로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-191">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="dd54f-192">다음 예의 구성 요소는 `NavLink` `target` 특성을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-192">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="dd54f-193">렌더링 되는 HTML 태그는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-193">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="dd54f-194">URI 및 탐색 상태 도우미</span><span class="sxs-lookup"><span data-stu-id="dd54f-194">URI and navigation state helpers</span></span>

<span data-ttu-id="dd54f-195">를 `Microsoft.AspNetCore.Components.NavigationManager` 사용 하 여 코드에서 C# uri 및 탐색 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-195">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="dd54f-196">`NavigationManager`는 다음 표에 나와 있는 이벤트와 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-196">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="dd54f-197">멤버</span><span class="sxs-lookup"><span data-stu-id="dd54f-197">Member</span></span> | <span data-ttu-id="dd54f-198">설명</span><span class="sxs-lookup"><span data-stu-id="dd54f-198">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="dd54f-199">현재 절대 URI를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-199">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="dd54f-200">절대 URI를 생성 하기 위해 상대 URI 경로 앞에 추가할 수 있는 기본 URI (후행 슬래시 포함)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-200">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="dd54f-201">일반적으로 `BaseUri` 는 *wwwroot/index.html* ( `href` Blazor weasembmbe) 또는 *Pages/_Host* (Blazor Server)의 문서 `<base>` 요소에 있는 특성에 해당 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-201">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| `NavigateTo` | <span data-ttu-id="dd54f-202">지정 된 URI로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-202">Navigates to the specified URI.</span></span> <span data-ttu-id="dd54f-203">`forceLoad` 가`true`다음과 같은 경우:</span><span class="sxs-lookup"><span data-stu-id="dd54f-203">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="dd54f-204">클라이언트 쪽 라우팅이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-204">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="dd54f-205">URI가 일반적으로 클라이언트 쪽 라우터에서 처리 되는지 여부와 상관 없이 브라우저는 서버에서 새 페이지를 강제로 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-205">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="dd54f-206">탐색 위치가 변경 될 때 발생 하는 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-206">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="dd54f-207">상대 URI를 절대 URI로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-207">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="dd54f-208">기본 uri가 지정 된 경우 (예: 이전에에서 `GetBaseUri`반환 된 uri)는 절대 uri를 기본 uri 접두사에 상대적인 uri로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-208">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="dd54f-209">다음 구성 요소는 단추를 선택 하면 `Counter` 앱의 구성 요소로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd54f-209">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```cshtml
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
