---
title: ASP.NET Core Razor 구성 요소 만들기 및 사용
author: guardrex
description: 데이터에 바인딩하고, 이벤트를 처리하고, 구성 요소 수명 주기를 관리하는 방법을 비롯하여 Razor 구성 요소를 만들고 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: e444ebfef5143a6c33ed2d122933903ad3a4f4a7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648039"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="84c07-103">ASP.NET Core Razor 구성 요소 만들기 및 사용</span><span class="sxs-lookup"><span data-stu-id="84c07-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="84c07-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="84c07-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="84c07-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="84c07-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="84c07-106"> 앱은 *구성 요소*를 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-106"> apps are built using *components*.</span></span> <span data-ttu-id="84c07-107">구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI(사용자 인터페이스)의 자체 포함 청크입니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="84c07-108">구성 요소는 데이터를 주입하거나 UI 이벤트에 응답하는 데 필요한 HTML 태그와 처리 논리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="84c07-109">구성 요소는 유연하고 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="84c07-110">프로젝트 간에 중첩, 재사용 및 공유될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="84c07-111">구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="84c07-111">Component classes</span></span>

<span data-ttu-id="84c07-112">구성 요소는 C# 및 HTML 태그 조합을 사용하여 [Razor](xref:mvc/views/razor) 구성 요소 파일( *.razor*)에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="84c07-113">Blazor의 구성 요소는 공식적으로 *Razor 구성 요소*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="84c07-114">구성 요소의 이름은 대문자로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="84c07-115">예를 들어, *MyCoolComponent.razor*는 유효하고 *myCoolComponent*는 유효하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="84c07-116">구성 요소의 UI는 HTML을 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="84c07-117">동적 렌더링 논리(예: 루프, 조건, 식)는 [Razor](xref:mvc/views/razor)라는 포함된 C# 구문을 사용하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="84c07-118">앱이 컴파일되면 HTML 태그 및 C# 렌더링 논리는 구성 요소 클래스로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="84c07-119">생성된 클래스의 이름은 파일 이름과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="84c07-120">구성 요소 클래스의 멤버는 `@code` 블록에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="84c07-121">`@code` 블록에서 구성 요소 상태(속성, 필드)는 이벤트 처리 또는 다른 구성 요소 논리 정의를 위한 메서드로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="84c07-122">두 개 이상의 `@code` 블록이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="84c07-123">구성 요소 멤버는 `@`으로 시작되는 C# 식을 사용하는 구성 요소 렌더링 논리의 일부로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="84c07-124">예를 들어, C# 필드는 필드 이름에 앞에 `@`을 붙여 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="84c07-125">다음 예제는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="84c07-126">`_headingFontStyle`을 평가하고 `font-style`에 대한 CSS 속성 값으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="84c07-127">`_headingText`를 평가하고 `<h1>` 요소의 내용으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="84c07-128">구성 요소가 처음 렌더링되면 구성 요소는 이벤트에 대한 응답으로 렌더링 트리를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="84c07-129">그런 후 Blazor는 새로운 렌더링 트리를 이전 렌터링 트리와 비교하여 수정 사항을 브라우저 DOM(문서 개체 모델)에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-129">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="84c07-130">구성 요소는 일반 C# 클래스이며 프로젝트 내의 어느 위치에나 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="84c07-131">웹 페이지를 생성하는 구성 요소는 일반적으로 *Pages* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="84c07-132">페이지가 아닌 구성 요소는 *Shared* 폴더 또는 프로젝트에 추가된 사용자 지정 폴더에 자주 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="84c07-133">일반적으로 구성 요소의 네임스페이스는 앱의 루트 네임스페이스와 앱 내의 구성 요소 위치(폴더)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="84c07-134">앱의 루트 네임스페이스가 `BlazorApp`이고 `Counter` 구성 요소가 *Pages* 폴더에 있는 경우 다음이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="84c07-135">`Counter` 구성 요소의 네임스페이스는 `BlazorApp.Pages`입니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="84c07-136">구성 요소의 정규화된 형식 이름은 `BlazorApp.Pages.Counter`입니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="84c07-137">자세한 내용은 [구성 요소 가져오기](#import-components) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="84c07-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="84c07-138">사용자 지정 폴더를 사용하려면 사용자 지정 폴더의 네임스페이스를 부모 구성 요소나 앱의 *_Imports.razor* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="84c07-139">예를 들어, 다음 네임스페이스는 앱의 루트 네임스페이스가 `BlazorApp`일 때 *Components* 폴더의 구성 요소를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="84c07-140">정적 자산</span><span class="sxs-lookup"><span data-stu-id="84c07-140">Static assets</span></span>

Blazor<span data-ttu-id="84c07-141">는 프로젝트의 [웹 루트(wwwroot) 폴더](xref:fundamentals/index#web-root) 아래에 정적 자산을 배치하는 ASP.NET Core 앱의 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="84c07-142">기본 상대 경로(`/`)를 사용하여 정적 자산의 웹 루트를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="84c07-143">다음 예제에서 *logo.png*는 실제로 *{PROJECT ROOT}/wwwroot/images* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="84c07-144">Razor 구성 요소는 물결표-슬래시 표기법(`~/`)을 지원하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="84c07-144">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="84c07-145">앱의 기본 경로를 설정하는 방법에 대한 내용은 <xref:host-and-deploy/blazor/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="84c07-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="84c07-146">태그 도우미는 구성 요소에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="84c07-147">[태그 도우미](xref:mvc/views/tag-helpers/intro)는 Razor 구성 요소( *.razor* 파일)에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="84c07-148">Blazor에서 태그 도우미와 유사한 기능을 제공하려면 대신, 태그 도우미와 동일한 기능을 포함하는 구성 요소를 만들고 해당 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="84c07-149">구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="84c07-149">Use components</span></span>

<span data-ttu-id="84c07-150">구성 요소는 HTML 요소 구문을 사용하여 선언함으로써 다른 구성 요소를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="84c07-151">구성 요소 사용을 위한 태그는 태그 이름이 구성 요소 유형인 HTML 태그처럼 보입니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="84c07-152">특성 바인딩은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-152">Attribute binding is case sensitive.</span></span> <span data-ttu-id="84c07-153">예를 들어, `@bind`는 유효하고 `@Bind`는 유효하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-153">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="84c07-154">*Index.razor*의 다음 태그는 `HeadingComponent` 인스턴스를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-154">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="84c07-155">*Components/HeadingComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="84c07-155">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="84c07-156">구성 요소에 구성 요소 이름과 일치하지 않는 HTML 요소(첫 글자가 대문자임)가 포함되면 요소에 예기치 않은 이름이 있음을 나타내는 경고가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-156">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="84c07-157">구성 요소 네임스페이스에 대한 `@using` 지시문을 추가하면 해당 구성 요소를 사용할 수 있게 되므로 경고가 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-157">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="84c07-158">라우팅</span><span class="sxs-lookup"><span data-stu-id="84c07-158">Routing</span></span>

<span data-ttu-id="84c07-159">Blazor의 라우팅은 앱에서 액세스 가능한 각 구성 요소에 경로 템플릿을 제공하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-159">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="84c07-160">`@page` 지시어를 포함하는 Razor 파일이 컴파일되면 생성된 클래스에 경로 템플릿을 지정하는 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-160">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="84c07-161">런타임에 라우터는 `RouteAttribute`를 사용하여 구성 요소 클래스를 검색하고, 요청된 URL과 일치하는 경로 템플릿을 포함하는 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-161">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="84c07-162">자세한 내용은 <xref:blazor/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="84c07-162">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="84c07-163">매개 변수</span><span class="sxs-lookup"><span data-stu-id="84c07-163">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="84c07-164">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="84c07-164">Route parameters</span></span>

<span data-ttu-id="84c07-165">구성 요소는 `@page` 지시문에 제공된 경로 템플릿에서 경로 매개 변수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-165">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="84c07-166">라우터는 경로 매개 변수를 사용하여 해당하는 구성 요소 매개 변수를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-166">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="84c07-167">*Pages/RouteParameter.razor*:</span><span class="sxs-lookup"><span data-stu-id="84c07-167">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="84c07-168">선택적 매개 변수는 지원되지 않으므로 앞의 예제에서 두 개의 `@page` 지시문이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-168">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="84c07-169">첫 번째 지시문은 매개 변수 없이 구성 요소에 대한 탐색을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-169">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="84c07-170">두 번째 `@page` 지시문은 `{text}` 경로 매개 변수를 받고 `Text` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-170">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="84c07-171">여러 폴더 경계에서 경로를 캡처하는 *catch-all* 매개 변수 구문(`*`/`**`)은 Razor 구성 요소( *.razor*)에서 지원되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="84c07-171">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="84c07-172">구성 요소 매개 변수</span><span class="sxs-lookup"><span data-stu-id="84c07-172">Component parameters</span></span>

<span data-ttu-id="84c07-173">구성 요소에는 `[Parameter]` 특성을 사용하여 구성 요소 클래스의 퍼블릭 속성을 사용하여 정의되는 *구성 요소 매개 변수*가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-173">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="84c07-174">특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-174">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="84c07-175">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="84c07-175">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="84c07-176">샘플 앱의 다음 예제에서는 `ParentComponent`는 `ChildComponent`의 `Title` 속성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-176">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="84c07-177">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="84c07-177">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="84c07-178">자식 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="84c07-178">Child content</span></span>

<span data-ttu-id="84c07-179">구성 요소는 다른 구성 요소의 콘텐츠를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-179">Components can set the content of another component.</span></span> <span data-ttu-id="84c07-180">할당 구성 요소는 받는 구성 요소를 지정하는 태그 간 콘텐츠를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-180">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="84c07-181">다음 예제에서 `ChildComponent`에는 렌더링할 UI의 세그먼트를 나타내는 `RenderFragment`를 나타내는 `ChildContent` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-181">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="84c07-182">`ChildContent`의 값은 콘텐츠를 렌더링해야 하는 구성 요소의 태그에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-182">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="84c07-183">`ChildContent` 값은 부모 구성 요소에서 수신되고 부트스트랩 패널의 `panel-body` 내에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-183">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="84c07-184">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="84c07-184">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="84c07-185">`RenderFragment` 콘텐츠를 받는 속성은 규칙에 따라 이름 `ChildContent`가 지정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-185">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="84c07-186">샘플 앱의 `ParentComponent`는 콘텐츠를 `<ChildComponent>` 태그 안에 배치하여 `ChildComponent`를 렌더링하기 위한 콘텐츠를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-186">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="84c07-187">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="84c07-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="84c07-188">특성 스플래팅 및 임의 매개 변수</span><span class="sxs-lookup"><span data-stu-id="84c07-188">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="84c07-189">구성 요소는 구성 요소의 선언된 매개 변수 외에, 추가 특성도 캡처하고 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-189">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="84c07-190">추가 특성을 사전에 캡처한 다음, [`@attributes`](xref:mvc/views/razor#attributes) Razor 지시어를 사용하여 구성 요소를 렌더링할 때 요소에 *스플래팅*할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-190">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="84c07-191">이 시나리오는 다양한 사용자 지정을 지원하는 태그 요소를 생성하는 구성 요소를 정의할 때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-191">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="84c07-192">예를 들어, 많은 매개 변수를 지원하는 `<input>`에 대해 개별적으로 특성을 정의하는 것이 번거로울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-192">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="84c07-193">다음 예제에서 첫 번째 `<input>` 요소(`id="useIndividualParams"`)는 개별 구성 요소 매개 변수를 사용하지만 두 번째 `<input>` 요소(`id="useAttributesDict"`)는 특성 스플래팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-193">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="84c07-194">매개 변수의 형식은 문자열 키를 사용하여 `IEnumerable<KeyValuePair<string, object>>`를 구현해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-194">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="84c07-195">이 시나리오에서는 `IReadOnlyDictionary<string, object>`를 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-195">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="84c07-196">두 방법을 사용하여 렌더링되는 `<input>` 요소는 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-196">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="84c07-197">임의 특성을 허용하려면 `CaptureUnmatchedValues` 속성이 `true`로 설정한 상태로 `[Parameter]` 특성을 사용하여 구성 요소 매개 변수를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-197">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="84c07-198">`[Parameter]`의 `CaptureUnmatchedValues` 속성을 사용하면 해당 매개 변수는 다른 매개 변수와 일치하지 않는 모든 특성을 일치시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-198">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="84c07-199">구성 요소는 `CaptureUnmatchedValues`를 사용하여 단일 매개 변수만 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-199">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="84c07-200">`CaptureUnmatchedValues`에 사용되는 속성 형식은 문자열 키가 있는 `Dictionary<string, object>`에서 할당할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-200">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="84c07-201">이 시나리오에서는 `IEnumerable<KeyValuePair<string, object>>` 또는 `IReadOnlyDictionary<string, object>`도 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-201">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="84c07-202">요소 특성의 위치에 상대적인 `@attributes`의 위치는 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-202">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="84c07-203">요소에 `@attributes`가 스플래팅되면 오른쪽에서 왼쪽으로(마지막에 도달하면 처음으로) 특성이 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-203">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="84c07-204">`Child` 구성 요소를 사용하는 구성 요소의 다음 예를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="84c07-204">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="84c07-205">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="84c07-205">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="84c07-206">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="84c07-206">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="84c07-207">`Child` 구성 요소의 `extra` 특성은 `@attributes` 오른쪽으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-207">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="84c07-208">특성은 오른쪽에서 왼쪽으로(마지막에 도달하면 처음으로) 처리되기 때문에 `Parent` 구성 요소의 렌더링된 `<div>`는 추가 특성을 통해 전달될 때 `extra="5"`를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-208">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="84c07-209">다음 예에서는 `Child` 구성 요소의 `<div>`에서 `extra`와 `@attributes`의 순서가 반대로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-209">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="84c07-210">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="84c07-210">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="84c07-211">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="84c07-211">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="84c07-212">`Parent` 구성 요소의 렌더링된 `<div>`에는 추가 특성을 통해 전달될 경우 `extra="10"`을 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-212">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="84c07-213">구성 요소에 대한 참조 캡처</span><span class="sxs-lookup"><span data-stu-id="84c07-213">Capture references to components</span></span>

<span data-ttu-id="84c07-214">구성 요소 참조에서는 해당 인스턴스에 대해 명령(예: (`Show` 또는 `Reset`)을 실행할 수 있도록 구성 요소 인스턴스를 참조하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-214">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="84c07-215">구성 요소 참조를 캡처하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-215">To capture a component reference:</span></span>

* <span data-ttu-id="84c07-216">자식 구성 요소에 [`@ref`](xref:mvc/views/razor#ref) 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-216">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="84c07-217">자식 구성 요소와 동일한 유형으로 필드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-217">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

<span data-ttu-id="84c07-218">구성 요소가 렌더링되면 `_loginDialog` 필드가 `MyLoginDialog` 자식 구성 요소 인스턴스로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-218">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="84c07-219">그런 다음, 구성 요소 인스턴스에서 .NET 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-219">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="84c07-220">`_loginDialog` 변수는 구성 요소가 렌더링된 후에만 채워지고 출력에는 `MyLoginDialog` 요소가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-220">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="84c07-221">이 시점까지 참조할 항목이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-221">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="84c07-222">구성 요소에서 렌더링을 완료한 후에 구성 요소 참조를 조작하려면 [OnAfterRenderAsync 또는 OnAfterRender 메서드](xref:blazor/lifecycle#after-component-render)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-222">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="84c07-223">구성 요소 참조 캡처에는 [요소 참조 캡처](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)와 유사한 구문을 사용하지만 JavaScript interop 기능이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-223">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="84c07-224">구성 요소 참조는 JavaScript 코드에 전달되지 않으며, .NET 코드에서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-224">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="84c07-225">구성 요소 참조를 사용하여 자식 구성 요소의 상태를 변경하지 **않도록 합니다**.</span><span class="sxs-lookup"><span data-stu-id="84c07-225">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="84c07-226">대신, 일반 선언적 매개 변수를 사용하여 자식 구성 요소에 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-226">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="84c07-227">일반 선언적 매개 변수를 사용하면 자식 구성 요소가 올바른 시간에 자동으로 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-227">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="84c07-228">외부에서 구성 요소 메서드를 호출하여 상태 업데이트</span><span class="sxs-lookup"><span data-stu-id="84c07-228">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="84c07-229">는 `SynchronizationContext`를 사용하여 단일 논리적 실행 스레드를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-229"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="84c07-230">구성 요소의 [수명 주기 메서드](xref:blazor/lifecycle) 및 Blazor에서 발생하는 모든 이벤트 콜백은 이 `SynchronizationContext`에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-230">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="84c07-231">외부 이벤트(예: 타이머 또는 다른 알림)를 기준으로 구성 요소를 업데이트해야 하는 경우 Blazor의 `SynchronizationContext`에 디스패치되는 `InvokeAsync` 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-231">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="84c07-232">예를 들어, 업데이트된 상태를 수신 구성 요소에 알릴 수 있는 *알림 서비스*을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="84c07-232">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="84c07-233">`NotifierService`를 싱글톤으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-233">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="84c07-234">Blazor WebAssembly에서 `Program.Main`에 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-234">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="84c07-235">Blazor 서버에서 `Startup.ConfigureServices`에 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-235">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="84c07-236">`NotifierService`를 사용하여 구성 요소를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-236">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="84c07-237">위의 예제에서 `NotifierService`는 Blazor의 `SynchronizationContext` 외부에서 구성 요소의 `OnNotify` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-237">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="84c07-238">`InvokeAsync`는 올바른 컨텍스트로 전환하고 렌더링을 큐에 대기하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-238">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="84c07-239">\@ 키를 사용하여 요소 및 구성 요소 유지</span><span class="sxs-lookup"><span data-stu-id="84c07-239">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="84c07-240">요소 또는 구성 요소 목록을 렌더링하고, 이후에 요소 또는 구성 요소가 변경되는 경우 Blazor의 Diff 알고리즘은 유지할 수 있는 이전 요소 또는 구성 요소와 모델 개체가 이러한 요소에 매핑되는 방법을 결정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-240">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="84c07-241">일반적으로 이 프로세스는 자동이며 무시해도 되지만 프로세스를 제어하려는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-241">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="84c07-242">다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="84c07-242">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="84c07-243">`People` 컬렉션의 콘텐츠는 삽입, 삭제 또는 다시 정렬된 항목으로 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-243">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="84c07-244">구성 요소가 다시 렌더링되면 `<DetailsEditor>` 구성 요소가 다른 `Details` 매개 변수 값을 수신하도록 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-244">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="84c07-245">이로 인해 다시 렌더링이 예상보다 더 복잡해질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-245">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="84c07-246">경우에 따라 다시 렌더링을 수행하면 요소 포커스 손실과 같은 동작 차이가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-246">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="84c07-247">매핑 프로세스는 `@key` 지시어 특성을 사용하여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-247">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="84c07-248">`@key`를 사용하면 diff 알고리즘은 키의 값에 따라 요소 또는 구성 요소가 유지되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-248">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="84c07-249">`People` 컬렉션이 변경되면 diff 알고리즘은 `<DetailsEditor>` 인스턴스와 `person` 인스턴스 간 연결을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-249">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="84c07-250">`People` 목록에서 `Person`이 삭제된 경우 해당 `<DetailsEditor>` 인스턴스만 UI에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-250">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="84c07-251">다른 인스턴스는 변경되지 않은 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-251">Other instances are left unchanged.</span></span>
* <span data-ttu-id="84c07-252">`Person`이 목록의 특정 위치에 삽입되는 경우 해당 위치에 하나의 새 `<DetailsEditor>` 인스턴스가 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-252">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="84c07-253">다른 인스턴스는 변경되지 않은 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-253">Other instances are left unchanged.</span></span>
* <span data-ttu-id="84c07-254">`Person` 항목이 다시 정렬되면 해당 `<DetailsEditor>` 인스턴스가 유지되고 UI에서 다시 정렬됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-254">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="84c07-255">일부 시나리오에서는 `@key`를 사용하여 재랜더링의 복잡성을 최소화하고, DOM의 상태 저장 부분(예: 포커스 위치)이 변경될 수 있는 잠재적 문제를 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-255">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="84c07-256">키는 각 컨테이너 요소 또는 구성 요소에 대해 로컬입니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-256">Keys are local to each container element or component.</span></span> <span data-ttu-id="84c07-257">문서 전체에서 키가 전역적으로 비교되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-257">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="84c07-258">\@ 키를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="84c07-258">When to use \@key</span></span>

<span data-ttu-id="84c07-259">일반적으로 목록이 렌더링될 때마다(예: `@foreach` 블록에서) `@key`를 사용하는 것이 적절하며 `@key`을 정의하기 위한 적절한 값이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-259">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="84c07-260">`@key`를 사용하여 개체가 변경될 때 Blazor가 요소 또는 구성 요소 하위 트리를 유지하지 않도록 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-260">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="84c07-261">`@currentPerson`이 변경되면 `@key` 특성 지시문은 Blazor가 강제로 전체 `<div>` 및 해당 하위 항목을 삭제하고 새 요소와 구성 요소를 사용하여 UI 내에서 하위 트리를 다시 빌드하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-261">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="84c07-262">이것은 `@currentPerson`이 변경될 때 UI 상태가 유지되지 않도록 해야 하는 경우에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-262">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="84c07-263">\@ 키를 사용하지 않는 경우</span><span class="sxs-lookup"><span data-stu-id="84c07-263">When not to use \@key</span></span>

<span data-ttu-id="84c07-264">`@key`로 diff를 수행할 때 성능 비용이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-264">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="84c07-265">성능 비용은 크지 않지만 요소 또는 구성 요소 유지 규칙을 제어할 때 앱에 도움이 되는 경우에만 `@key`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-265">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="84c07-266">`@key`가 사용되지 않더라도 Blazor는 자식 요소와 구성 요소 인스턴스를 최대한 많이 보존합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-266">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="84c07-267">`@key`를 사용할 때의 유일한 장점은 매핑을 선택하는 diff 알고리즘 대신, 모델 인스턴스가 유지된 구성 요소 인스턴스에 매핑되는 *방법*을 제어할 수 있다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-267">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="84c07-268">\@ 키에 사용할 값</span><span class="sxs-lookup"><span data-stu-id="84c07-268">What values to use for \@key</span></span>

<span data-ttu-id="84c07-269">일반적으로 `@key`에 대해 다음과 같은 종류의 값 중 하나를 제공하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-269">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="84c07-270">모델 개체 인스턴스(예: 이전 예제와 같은 `Person` 인스턴스)</span><span class="sxs-lookup"><span data-stu-id="84c07-270">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="84c07-271">이렇게 하면 개체 참조 같음에 따라 보존이 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-271">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="84c07-272">고유 식별자(예: `int`, `string` 또는 `Guid` 형식의 기본 키 값)</span><span class="sxs-lookup"><span data-stu-id="84c07-272">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="84c07-273">`@key`에 사용되는 값이 충돌하지 않는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-273">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="84c07-274">동일한 부모 요소 내에서 충돌하는 값이 감지되면 이전 요소나 구성 요소를 새 요소나 구성 요소에 확정적으로 매핑할 수 없으므로 Blazor는 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-274">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="84c07-275">개체 인스턴스 또는 기본 키 값과 같은 고유 값만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-275">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="84c07-276">Partial 클래스 지원</span><span class="sxs-lookup"><span data-stu-id="84c07-276">Partial class support</span></span>

<span data-ttu-id="84c07-277">이제 Razor 구성 요소가 partial 클래스로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-277">Razor components are generated as partial classes.</span></span> <span data-ttu-id="84c07-278">Razor 구성 요소는 다음 방법 중 하나를 사용하여 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-278">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="84c07-279">C# 코드는 단일 파일에서 HTML 태그와 Razor 코드를 사용하여 [`@code`](xref:mvc/views/razor#code) 블록에 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-279">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="84c07-280"> 템플릿은 이 접근 방식을 사용하여 Razor 구성 요소를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-280"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="84c07-281">C# 코드는 partial 클래스로 정의된 코드 숨김 파일에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-281">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="84c07-282">다음 예제에서는 Blazor 템플릿에서 생성된 앱에서 `@code` 블록을 포함하는 기본 `Counter` 구성 요소를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-282">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="84c07-283">HTML 태그, Razor 코드 및 C# 코드는 다음과 같은 동일한 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-283">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="84c07-284">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="84c07-284">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="84c07-285">Partial 클래스에서 코드 숨김 파일을 사용하여 `Counter` 구성 요소를 만들 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-285">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="84c07-286">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="84c07-286">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="84c07-287">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="84c07-287">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

<span data-ttu-id="84c07-288">필요한 경우 partial 클래스 파일에 필요한 네임스페이스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-288">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="84c07-289">Razor 구성 요소에 사용되는 일반적인 네임스페이스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-289">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="84c07-290">기본 클래스 지정</span><span class="sxs-lookup"><span data-stu-id="84c07-290">Specify a base class</span></span>

<span data-ttu-id="84c07-291">[`@inherits`](xref:mvc/views/razor#inherits) 지시어를 사용하여 구성 요소에 대한 기본 클래스를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-291">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="84c07-292">다음 예제에서는 구성 요소가 기본 클래스 `BlazorRocksBase`를 상속하여 구성 요소의 속성과 메서드를 제공하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-292">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="84c07-293">기본 클래스는 `ComponentBase`에서 파생되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-293">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="84c07-294">*Pages/BlazorRocks.razor*:</span><span class="sxs-lookup"><span data-stu-id="84c07-294">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="84c07-295">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="84c07-295">*BlazorRocksBase.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a><span data-ttu-id="84c07-296">특성 지정</span><span class="sxs-lookup"><span data-stu-id="84c07-296">Specify an attribute</span></span>

<span data-ttu-id="84c07-297">[`@attribute`](xref:mvc/views/razor#attribute) 지시어를 사용하여 Razor 구성 요소에 특성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-297">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="84c07-298">다음 예제는 `[Authorize]` 특성을 구성 요소 클래스에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-298">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="84c07-299">구성 요소 가져오기</span><span class="sxs-lookup"><span data-stu-id="84c07-299">Import components</span></span>

<span data-ttu-id="84c07-300">Razor로 작성된 구성 요소의 네임스페이스는 다음을 기준으로 합니다(우선 순위에 따라).</span><span class="sxs-lookup"><span data-stu-id="84c07-300">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="84c07-301">Razor 파일( *.razor*) 태그(`@namespace BlazorSample.MyNamespace`)의 [`@namespace`](xref:mvc/views/razor#namespace) 지정</span><span class="sxs-lookup"><span data-stu-id="84c07-301">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="84c07-302">프로젝트 파일(`<RootNamespace>BlazorSample</RootNamespace>`)에서 프로젝트의 `RootNamespace`</span><span class="sxs-lookup"><span data-stu-id="84c07-302">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="84c07-303">프로젝트 파일의 파일 이름( *.csproj*)에서 가져온 프로젝트 이름, 프로젝트 루트에서 구성 요소로의 경로.</span><span class="sxs-lookup"><span data-stu-id="84c07-303">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="84c07-304">예를 들어, 이 프레임워크는 *{PROJECT ROOT}/Pages/Index.razor*(*BlazorSample.csproj*)를 네임스페이스 `BlazorSample.Pages`로 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-304">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="84c07-305">구성 요소는 C# 이름 바인딩 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-305">Components follow C# name binding rules.</span></span> <span data-ttu-id="84c07-306">이 예의 `Index` 구성 요소에서는 범위의 구성 요소가 모든 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-306">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="84c07-307">동일한 폴더의 *Pages*.</span><span class="sxs-lookup"><span data-stu-id="84c07-307">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="84c07-308">다른 네임스페이스를 명시적으로 지정하지 않는 프로젝트 루트의 구성 요소</span><span class="sxs-lookup"><span data-stu-id="84c07-308">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="84c07-309">다른 네임스페이스에 정의된 구성 요소는 Razor의 [`@using`](xref:mvc/views/razor#using) 지시문을 사용하여 범위로 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-309">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="84c07-310">다른 구성 요소에서 `NavMenu.razor`가 *BlazorSample/Shared/* 폴더에 있는 경우 다음 `@using` 문을 사용하여 `Index.razor`에서 구성 요소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-310">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="84c07-311">구성 요소는 정규화된 이름을 사용하여 참조할 수도 있습니다. 이 경우에는 [`@using`](xref:mvc/views/razor#using) 지시문이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-311">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="84c07-312">`global::` 한정자는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-312">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="84c07-313">별칭이 지정된 `using` 문(예: `@using Foo = Bar`)을 사용하여 구성 요소를 가져오는 것은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-313">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="84c07-314">부분적으로 정규화된 이름은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-314">Partially qualified names aren't supported.</span></span> <span data-ttu-id="84c07-315">예를 들어, `<Shared.NavMenu></Shared.NavMenu>`를 사용하여 `@using BlazorSample`을 추가하고 `NavMenu.razor`를 참조하는 것은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-315">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="84c07-316">조건부 HTML 요소 특성</span><span class="sxs-lookup"><span data-stu-id="84c07-316">Conditional HTML element attributes</span></span>

<span data-ttu-id="84c07-317">HTML 요소 특성은 .NET 값에 따라 조건부로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-317">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="84c07-318">값이 `false` 또는 `null`이면 특성이 렌더링되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-318">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="84c07-319">값이 `true`이면 특성이 최소화된 상태로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-319">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="84c07-320">다음 예제에서 `IsCompleted`는 `checked`가 요소의 태그에서 렌더링되는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-320">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="84c07-321">`IsCompleted`가 `true`이면 확인란이 다음과 같이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-321">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="84c07-322">`IsCompleted`가 `false`이면 확인란이 다음과 같이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-322">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="84c07-323">자세한 내용은 <xref:mvc/views/razor>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="84c07-323">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="84c07-324">[aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)와 같은 일부 HTML 특성은 .NET 형식이 `bool`일 경우 제대로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-324">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="84c07-325">이러한 경우 `bool` 대신 `string` 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-325">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="84c07-326">원시 HTML</span><span class="sxs-lookup"><span data-stu-id="84c07-326">Raw HTML</span></span>

<span data-ttu-id="84c07-327">일반적으로 문자열은 DOM 텍스트 노드를 사용하여 렌더링됩니다. 즉, 포함될 수 있는 모든 태그는 무시되고 리터럴 텍스트로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-327">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="84c07-328">원시 HTML을 렌더링하려면 HTML 콘텐츠를 `MarkupString` 값으로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-328">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="84c07-329">값은 HTML 또는 SVG로 구문 분석되고 DOM에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-329">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="84c07-330">신뢰할 수 없는 원본에서 생성된 원시 HTML을 렌더링할 경우 **보안 위험**이 있으므로 피해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-330">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="84c07-331">다음 예제에서는 `MarkupString` 형식을 사용하여 정적 HTML 콘텐츠 블록을 구성 요소의 렌더링된 출력에 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-331">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="84c07-332">연계 값 및 매개 변수</span><span class="sxs-lookup"><span data-stu-id="84c07-332">Cascading values and parameters</span></span>

<span data-ttu-id="84c07-333">일부 시나리오에서, 특히 여러 구성 요소 계층이 있는 경우 [구성 요소 매개 변수](#component-parameters)를 사용하여 상위 구성 요소에서 하위 구성 요소로 데이터를 이동하는 것이 불편할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-333">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="84c07-334">연계 값 및 매개 변수는 상위 구성 요소에서 모든 하위 구성 요소에 값을 제공하는 편리한 방법을 제공하여 이 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-334">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="84c07-335">연계 값 및 매개 변수를 사용하여 구성 요소를 조정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-335">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="84c07-336">테마 예</span><span class="sxs-lookup"><span data-stu-id="84c07-336">Theme example</span></span>

<span data-ttu-id="84c07-337">샘플 앱의 다음 예제에서 `ThemeInfo` 클래스는 앱의 지정된 부분 내에 있는 모든 단추가 동일한 스타일을 공유할 수 있도록 구성 요소 계층 구조의 아래로 테마 정보가 흐르도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-337">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="84c07-338">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="84c07-338">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="84c07-339">상위 구성 요소는 연계 값 구성 요소를 사용하여 연계 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-339">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="84c07-340">`CascadingValue` 구성 요소는 구성 요소 계층의 하위 트리를 래핑하고 해당 하위 트리 내의 모든 구성 요소에 단일 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-340">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="84c07-341">예를 들어, 샘플 앱은 테마 정보(`ThemeInfo`)를 앱 레이아웃 중 하나에 `@Body` 속성의 레이아웃 본문을 구성하는 모든 구성 요소에 대한 연계 매개 변수로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-341">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="84c07-342">레이아웃 구성 요소에서 `ButtonClass`에는 `btn-success` 값이 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-342">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="84c07-343">모든 하위 구성 요소는 `ThemeInfo` 연계 개체를 통해 이 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-343">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="84c07-344">`CascadingValuesParametersLayout` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="84c07-344">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="84c07-345">연계 값을 사용하기 위해 구성 요소는 `[CascadingParameter]` 특성을 사용하여 연계 매개 변수를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-345">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="84c07-346">연계 값은 형식별로 연계 매개 변수에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-346">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="84c07-347">샘플 앱에서 `CascadingValuesParametersTheme` 구성 요소는 `ThemeInfo` 연계 값을 연계 매개 변수에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-347">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="84c07-348">이 매개 변수는 구성 요소에 의해 표시되는 단추 중 하나에 대해 CSS 클래스를 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-348">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="84c07-349">`CascadingValuesParametersTheme` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="84c07-349">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="84c07-350">동일한 형식의 여러 값을 동일한 하위 트리 내에서 연계하려면 각 `CascadingValue` 구성 요소와 해당 `CascadingParameter`에 고유한 `Name` 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-350">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="84c07-351">다음 예제에서는 두 개의 `CascadingValue` 구성 요소가 이름별로 다른 `MyCascadingType` 인스턴스를 연계합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-351">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="84c07-352">하위 구성 요소에서 연계 매개 변수는 상위 구성 요소의 해당 연계 값에서 해당 값을 이름별로 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-352">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="84c07-353">TabSet 예제</span><span class="sxs-lookup"><span data-stu-id="84c07-353">TabSet example</span></span>

<span data-ttu-id="84c07-354">연계 매개 변수를 사용하면 여러 구성 요소가 구성 요소 계층 구조 전체에서 공동으로 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-354">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="84c07-355">예를 들어, 샘플 앱에서 다음 *TabSet* 예제를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-355">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="84c07-356">샘플 앱에는 탭이 구현하는 `ITab` 인터페이스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-356">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="84c07-357">`CascadingValuesParametersTabSet` 구성 요소는 여러 `Tab` 구성 요소를 포함하는 `TabSet` 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-357">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="84c07-358">자식 `Tab` 구성 요소는 `TabSet`에 대한 매개 변수로 명시적으로 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-358">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="84c07-359">대신, 자식 `Tab` 구성 요소는 `TabSet`의 자식 콘텐츠에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-359">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="84c07-360">그러나 `TabSet`은 헤더 및 활성 탭을 렌더링할 수 있도록 각 `Tab` 구성 요소에 대한 정보를 계속 알고 있어야 합니다. 추가 코드를 요구하지 않고 이러한 조정을 사용하도록 설정하기 위해 `TabSet` 구성 요소는  연계 값으로 제공된 다음, 하위 `Tab` 구성 요소에서 선택될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-360">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="84c07-361">`TabSet` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="84c07-361">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="84c07-362">하위 `Tab` 구성 요소는 포함하는 `TabSet`을 연계 매개 변수로 캡처하므로 `Tab` 구성 요소는 `TabSet`에 추가되고 활성 상태인 탭을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-362">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="84c07-363">`Tab` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="84c07-363">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="84c07-364">Razor 템플릿</span><span class="sxs-lookup"><span data-stu-id="84c07-364">Razor templates</span></span>

<span data-ttu-id="84c07-365">렌더링 조각은 Razor 템플릿 구문을 사용하여 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-365">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="84c07-366">Razor 템플릿은 UI 코드 조각을 정의하는 방법으로, 다음 형식을 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-366">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="84c07-367">다음 예제에서는 `RenderFragment` 및 `RenderFragment<T>` 값을 지정하고 구성 요소에서 직접 템플릿을 렌더링하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-367">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="84c07-368">렌더링 조각은 [템플릿 구성 요소](xref:blazor/templated-components)에 인수로 전달될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-368">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="84c07-369">이전 코드의 렌더링된 출력:</span><span class="sxs-lookup"><span data-stu-id="84c07-369">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="84c07-370">SVG(Scalable Vector Graphics) 이미지</span><span class="sxs-lookup"><span data-stu-id="84c07-370">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="84c07-371">Blazor는 HTML을 렌더링하므로 SVG(Scalable Vector Graphics) 이미지( *.svg*)를 포함하는 브라우저 지원 이미지는 `<img>` 태그를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-371">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="84c07-372">마찬가지로, 스타일시트 파일( *.css*)의 CSS 규칙에서는 SVG 이미지가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-372">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="84c07-373">그러나 인라인 SVG 태그는 일부 시나리오에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-373">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="84c07-374">`<svg>` 태그를 구성 요소 파일(*razor*)에 직접 배치하면 기본 이미지 렌더링이 지원되지만 많은 고급 시나리오는 아직 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-374">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="84c07-375">예를 들어, `<use>` 태그는 현재 적용되지 않으며 `@bind`를 일부 SVG 태그에서는 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-375">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="84c07-376">향후 릴리스에서 이러한 제한을 해결할 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-376">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84c07-377">추가 자료</span><span class="sxs-lookup"><span data-stu-id="84c07-377">Additional resources</span></span>

* <span data-ttu-id="84c07-378"><xref:security/blazor/server> &ndash; 리소스를 소모하지 않도록 하는 Blazor 서버 앱을 빌드하는 방법에 대한 지침을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="84c07-378"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
