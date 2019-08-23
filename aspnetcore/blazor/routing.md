---
title: ASP.NET Core Blazor 라우팅
author: guardrex
description: 앱에서 요청을 라우팅하는 방법 및 NavLink 구성 요소에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/routing
ms.openlocfilehash: 197b1a91b3540d21639c3ee775b2c490da7b23fe
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030401"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="83e4d-103">ASP.NET Core Blazor 라우팅</span><span class="sxs-lookup"><span data-stu-id="83e4d-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="83e4d-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="83e4d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="83e4d-105">Blazor apps에서 요청을 라우팅하는 방법 및 `NavLink` 구성 요소를 사용 하 여 탐색 링크를 만드는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="83e4d-106">ASP.NET Core 끝점 라우팅 통합</span><span class="sxs-lookup"><span data-stu-id="83e4d-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="83e4d-107">Blazor 서버 쪽은 [ASP.NET Core 끝점 라우팅](xref:fundamentals/routing)에 통합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="83e4d-108">ASP.NET Core 앱은 `MapBlazorHub` 에서 `Startup.Configure`대화형 구성 요소에 대 한 들어오는 연결을 허용 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="83e4d-109">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="83e4d-109">Route templates</span></span>

<span data-ttu-id="83e4d-110">구성 `Router` 요소는 라우팅을 활성화 하 고 액세스 가능한 각 구성 요소에 경로 템플릿이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-110">The `Router` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="83e4d-111">구성 요소가 다음과 같이 *응용 프로그램 razor* 파일에 나타납니다. `Router`</span><span class="sxs-lookup"><span data-stu-id="83e4d-111">The `Router` component appears in the *App.razor* file:</span></span>

<span data-ttu-id="83e4d-112">Blazor 서버 쪽 앱에서:</span><span class="sxs-lookup"><span data-stu-id="83e4d-112">In a Blazor server-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly" />
```

<span data-ttu-id="83e4d-113">Blazor 클라이언트 쪽 앱에서:</span><span class="sxs-lookup"><span data-stu-id="83e4d-113">In a Blazor client-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="83e4d-114">`@page` 지시문을 사용 하는 *razor* 파일이 컴파일되면 생성 된 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 클래스에 경로 템플릿을 지정 하는이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-114">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="83e4d-115">런타임에 라우터는를 `RouteAttribute` 사용 하 여 구성 요소 클래스를 검색 하 고 요청 된 URL과 일치 하는 경로 템플릿을 사용 하 여 구성 요소를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-115">At runtime, the router looks for component classes with a `RouteAttribute` and renders the component with a route template that matches the requested URL.</span></span>

<span data-ttu-id="83e4d-116">여러 경로 템플릿을 구성 요소에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-116">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="83e4d-117">다음 구성 요소는 및 `/BlazorRoute` `/DifferentBlazorRoute`에 대 한 요청에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-117">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="83e4d-118">경로를 올바르게 생성 `<base>` 하려면 앱에서 `href` 특성에 지정 된 앱 기본 경로를 사용 하 여 *wwwroot/index.html* 파일 (Blazor client side) 또는 *Pages/_Host* 파일 (Blazor server side)에 태그를 포함 해야 합니다. `<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="83e4d-118">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="83e4d-119">자세한 내용은 <xref:host-and-deploy/blazor/client-side#app-base-path>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="83e4d-119">For more information, see <xref:host-and-deploy/blazor/client-side#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="83e4d-120">콘텐츠를 찾을 수 없는 경우 사용자 지정 콘텐츠 제공</span><span class="sxs-lookup"><span data-stu-id="83e4d-120">Provide custom content when content isn't found</span></span>

<span data-ttu-id="83e4d-121">요청 된 경로에 대 한 콘텐츠를 찾을 수 없는 경우 구성요소를사용하여앱에서사용자지정콘텐츠를지정할수있습니다.`Router`</span><span class="sxs-lookup"><span data-stu-id="83e4d-121">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="83e4d-122">*응용 프로그램 razor* 파일에서 `<NotFoundContent>` `Router` 구성 요소의 요소에 사용자 지정 콘텐츠를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-122">In the *App.razor* file, set custom content in the `<NotFoundContent>` element of the `Router` component:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <NotFoundContent>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFoundContent>
</Router>
```

<span data-ttu-id="83e4d-123">의 `<NotFoundContent>` 콘텐츠에는 다른 대화형 구성 요소와 같은 임의의 항목이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-123">The content of `<NotFoundContent>` can include arbitrary items, such as other interactive components.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="83e4d-124">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="83e4d-124">Route parameters</span></span>

<span data-ttu-id="83e4d-125">라우터는 경로 매개 변수를 사용 하 여 해당 구성 요소 매개 변수를 동일한 이름 (대/소문자 구분 안 함)으로 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-125">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="83e4d-126">선택적 매개 변수는 ASP.NET Core 3.0 미리 보기에서 Blazor apps에 대해 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-126">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="83e4d-127">이전 `@page` 예제에서는 두 개의 지시문이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-127">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="83e4d-128">첫 번째는 매개 변수 없이 구성 요소에 대 한 탐색을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-128">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="83e4d-129">두 번째 `@page` 지시문은 `{text}` 경로 매개 변수를 사용 하 여 값을 `Text` 속성에 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-129">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="83e4d-130">경로 제약 조건</span><span class="sxs-lookup"><span data-stu-id="83e4d-130">Route constraints</span></span>

<span data-ttu-id="83e4d-131">경로 제약 조건은 경로 세그먼트에 대 한 형식 일치를 구성 요소에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-131">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="83e4d-132">다음 예제에서 `Users` 구성 요소에 대 한 경로는 다음과 같은 경우에만 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-132">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="83e4d-133">요청 URL에 경로 세그먼트가 있습니다. `Id`</span><span class="sxs-lookup"><span data-stu-id="83e4d-133">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="83e4d-134">세그먼트가 정수 (`int`)입니다. `Id`</span><span class="sxs-lookup"><span data-stu-id="83e4d-134">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="83e4d-135">다음 표에 표시 된 경로 제약 조건을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-135">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="83e4d-136">고정 문화권과 일치 하는 경로 제약 조건에 대 한 자세한 내용은 테이블 아래 경고를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="83e4d-136">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="83e4d-137">제약 조건</span><span class="sxs-lookup"><span data-stu-id="83e4d-137">Constraint</span></span> | <span data-ttu-id="83e4d-138">예제</span><span class="sxs-lookup"><span data-stu-id="83e4d-138">Example</span></span>           | <span data-ttu-id="83e4d-139">일치하는 예제</span><span class="sxs-lookup"><span data-stu-id="83e4d-139">Example Matches</span></span>                                                                  | <span data-ttu-id="83e4d-140">고정</span><span class="sxs-lookup"><span data-stu-id="83e4d-140">Invariant</span></span><br><span data-ttu-id="83e4d-141">Culture</span><span class="sxs-lookup"><span data-stu-id="83e4d-141">culture</span></span><br><span data-ttu-id="83e4d-142">일치</span><span class="sxs-lookup"><span data-stu-id="83e4d-142">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="83e4d-143">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="83e4d-143">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="83e4d-144">아니요</span><span class="sxs-lookup"><span data-stu-id="83e4d-144">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="83e4d-145">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="83e4d-145">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="83e4d-146">예</span><span class="sxs-lookup"><span data-stu-id="83e4d-146">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="83e4d-147">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="83e4d-147">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="83e4d-148">예</span><span class="sxs-lookup"><span data-stu-id="83e4d-148">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="83e4d-149">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="83e4d-149">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="83e4d-150">예</span><span class="sxs-lookup"><span data-stu-id="83e4d-150">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="83e4d-151">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="83e4d-151">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="83e4d-152">예</span><span class="sxs-lookup"><span data-stu-id="83e4d-152">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="83e4d-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="83e4d-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="83e4d-154">아니요</span><span class="sxs-lookup"><span data-stu-id="83e4d-154">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="83e4d-155">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="83e4d-155">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="83e4d-156">예</span><span class="sxs-lookup"><span data-stu-id="83e4d-156">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="83e4d-157">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="83e4d-157">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="83e4d-158">예</span><span class="sxs-lookup"><span data-stu-id="83e4d-158">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="83e4d-159">CLR 형식(예: `int` 또는 `DateTime`)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-159">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="83e4d-160">이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-160">These constraints assume that the URL is non-localizable.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="83e4d-161">NavLink 구성 요소</span><span class="sxs-lookup"><span data-stu-id="83e4d-161">NavLink component</span></span>

<span data-ttu-id="83e4d-162">탐색 링크 `NavLink` 를 만들 때 HTML 하이퍼링크 요소 (`<a>`) 대신 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-162">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="83e4d-163">구성 `NavLink` 요소는 `<a>` 요소가 현재 URL `href` 과 일치 하는지 여부에 `active` 따라 CSS 클래스를 전환 하는 것을 제외 하 고 요소 처럼 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-163">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="83e4d-164">클래스 `active` 를 통해 사용자는 표시 된 탐색 링크 중에서 활성 페이지가 되는 페이지를 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-164">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="83e4d-165">다음 `NavMenu` 구성 요소는 `NavLink` 구성 요소를 사용하는 방법을 보여 주는 [부트스트랩](https://getbootstrap.com/docs/) 탐색 모음을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-165">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="83e4d-166">요소의`<NavLink>` `NavLinkMatch` 특성`Match` 에 할당할 수 있는 두 가지 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-166">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="83e4d-167">`NavLinkMatch.All`&ndash; 는`NavLink` 전체 현재 URL과 일치 하는 경우 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-167">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="83e4d-168">`NavLinkMatch.Prefix`(*기본값*) &ndash; 는`NavLink` 현재 URL의 접두사와 일치 하는 경우 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-168">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="83e4d-169">`NavLink` 위의 예제에서 홈 `href=""` 은 홈 `active` URL과 일치 하 고 앱의 `https://localhost:5001/`기본 기본 경로 URL (예:)에서 CSS 클래스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-169">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="83e4d-170">두 번째 `NavLink` 는 사용자 `active` 가 `MyComponent` 접두사 (예: 및 `https://localhost:5001/MyComponent/AnotherSegment`)를 사용 하 여 URL을 `https://localhost:5001/MyComponent` 방문할 때 클래스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-170">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="83e4d-171">추가 `NavLink` 구성 요소 특성이 렌더링 된 앵커 태그로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-171">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="83e4d-172">다음 예의 구성 요소는 `NavLink` `target` 특성을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-172">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="83e4d-173">렌더링 되는 HTML 태그는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-173">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="83e4d-174">URI 및 탐색 상태 도우미</span><span class="sxs-lookup"><span data-stu-id="83e4d-174">URI and navigation state helpers</span></span>

<span data-ttu-id="83e4d-175">를 `Microsoft.AspNetCore.Components.IUriHelper` 사용 하 여 코드에서 C# uri 및 탐색 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-175">Use `Microsoft.AspNetCore.Components.IUriHelper` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="83e4d-176">`IUriHelper`는 다음 표에 나와 있는 이벤트와 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-176">`IUriHelper` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="83e4d-177">멤버</span><span class="sxs-lookup"><span data-stu-id="83e4d-177">Member</span></span> | <span data-ttu-id="83e4d-178">설명</span><span class="sxs-lookup"><span data-stu-id="83e4d-178">Description</span></span> |
| ------ | ----------- |
| `GetAbsoluteUri` | <span data-ttu-id="83e4d-179">현재 절대 URI를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-179">Gets the current absolute URI.</span></span> |
| `GetBaseUri` | <span data-ttu-id="83e4d-180">절대 URI를 생성 하기 위해 상대 URI 경로 앞에 추가할 수 있는 기본 URI (후행 슬래시 포함)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-180">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="83e4d-181">일반적으로 `GetBaseUri` 는 *wwwroot/index.html* ( `href` Blazor) 또는 *Pages/_Host* (Blazor 서버측)의 문서 `<base>` 요소에 있는 특성에 해당 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-181">Typically, `GetBaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="83e4d-182">지정 된 URI로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-182">Navigates to the specified URI.</span></span> <span data-ttu-id="83e4d-183">`forceLoad` 가`true`다음과 같은 경우:</span><span class="sxs-lookup"><span data-stu-id="83e4d-183">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="83e4d-184">클라이언트 쪽 라우팅이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-184">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="83e4d-185">URI가 일반적으로 클라이언트 쪽 라우터에서 처리 되는지 여부와 상관 없이 브라우저는 서버에서 새 페이지를 강제로 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-185">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `OnLocationChanged` | <span data-ttu-id="83e4d-186">탐색 위치가 변경 될 때 발생 하는 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-186">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="83e4d-187">상대 URI를 절대 URI로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-187">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="83e4d-188">기본 uri가 지정 된 경우 (예: 이전에에서 `GetBaseUri`반환 된 uri)는 절대 uri를 기본 uri 접두사에 상대적인 uri로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-188">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="83e4d-189">다음 구성 요소는 단추를 선택 하면 `Counter` 앱의 구성 요소로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="83e4d-189">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```cshtml
@page "/navigate"
@using Microsoft.AspNetCore.Components
@inject IUriHelper UriHelper

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        UriHelper.NavigateTo("counter");
    }
}
```
