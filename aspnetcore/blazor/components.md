---
title: ASP.NET Core Razor 구성 요소 만들기 및 사용
author: guardrex
description: 데이터에 바인딩하고, 이벤트를 처리 하 고, 구성 요소 수명 주기를 관리 하는 방법을 비롯 하 여 Razor 구성 요소를 만들고 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: f9b4eab29fafe8113528062f57d28dadd0f57577
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447102"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="5f26b-103">ASP.NET Core Razor 구성 요소 만들기 및 사용</span><span class="sxs-lookup"><span data-stu-id="5f26b-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="5f26b-104">By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5f26b-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="5f26b-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5f26b-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="5f26b-106"> 앱은 *구성 요소*를 사용 하 여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-106"> apps are built using *components*.</span></span> <span data-ttu-id="5f26b-107">구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI (사용자 인터페이스)의 자체 포함 청크입니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="5f26b-108">구성 요소는 데이터를 삽입 하거나 UI 이벤트에 응답 하는 데 필요한 HTML 태그와 처리 논리를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="5f26b-109">구성 요소는 유연 하 고 간단 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="5f26b-110">프로젝트 간에 중첩, 재사용 및 공유 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="5f26b-111">구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="5f26b-111">Component classes</span></span>

<span data-ttu-id="5f26b-112">구성 요소는 및 HTML 태그의 C# 조합을 사용 하 여 [razor](xref:mvc/views/razor) 구성 요소 파일 (*razor*)에서 구현 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="5f26b-113">Blazor의 구성 요소는 *Razor 구성 요소*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="5f26b-114">구성 요소의 이름은 대문자로 시작 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="5f26b-115">예를 들어 *MyCoolComponent* 는 유효 하며 *MyCoolComponent* 은 유효 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="5f26b-116">구성 요소에 대 한 UI는 HTML을 사용 하 여 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="5f26b-117">동적 렌더링 논리(예: 루프, 조건, 식)는 [Razor](xref:mvc/views/razor)라는 포함된 C# 구문을 사용하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="5f26b-118">앱이 컴파일되면 HTML 태그 및 C# 렌더링 논리가 구성 요소 클래스로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="5f26b-119">생성 된 클래스의 이름은 파일 이름과 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="5f26b-120">구성 요소 클래스의 멤버는 `@code` 블록에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="5f26b-121">`@code` 블록에서 구성 요소 상태 (속성, 필드)는 이벤트 처리를 위한 메서드나 다른 구성 요소 논리를 정의 하는 데 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="5f26b-122">두 개 이상의 `@code` 블록이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="5f26b-123">구성 요소 멤버는 `@`로 시작 하는 식을 사용 하 여 C# 구성 요소의 렌더링 논리의 일부로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="5f26b-124">예를 들어 필드 C# 는 필드 이름에 `@`을 접두사로 하 여 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="5f26b-125">다음 예에서는를 평가 하 고 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="5f26b-126">`font-style`에 대 한 CSS 속성 값을 `_headingFontStyle` 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="5f26b-127">`<h1>` 요소의 내용에 `_headingText` 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="5f26b-128">구성 요소가 처음 렌더링 되 면 구성 요소는 이벤트에 대 한 응답으로 렌더링 트리를 다시 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="5f26b-129">그런 다음 Blazor 새 렌더링 트리를 이전 렌더링 트리와 비교 하 여 브라우저의 문서 개체 모델 (DOM)에 수정 사항을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-129">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="5f26b-130">구성 요소는 C# 일반 클래스 이며 프로젝트 내의 어느 위치에 나 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="5f26b-131">웹 페이지를 생성 하는 구성 요소는 일반적으로 *Pages* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="5f26b-132">페이지를 지정 하지 않는 구성 요소는 일반적으로 *공유* 폴더 또는 프로젝트에 추가 된 사용자 지정 폴더에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="5f26b-133">일반적으로 구성 요소의 네임 스페이스는 앱의 루트 네임 스페이스와 앱 내의 구성 요소 위치 (폴더)에서 파생 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="5f26b-134">응용 프로그램의 루트 네임 스페이스가 `BlazorApp` 고 `Counter` 구성 요소가 *Pages* 폴더에 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="5f26b-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="5f26b-135">`Counter` 구성 요소의 네임 스페이스가 `BlazorApp.Pages`되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="5f26b-136">구성 요소의 정규화 된 형식 이름이 `BlazorApp.Pages.Counter`입니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="5f26b-137">자세한 내용은 [구성 요소 가져오기](#import-components) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5f26b-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="5f26b-138">사용자 지정 폴더를 사용 하려면 부모 구성 요소나 앱의 *_Imports razor* 파일에 사용자 지정 폴더의 네임 스페이스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="5f26b-139">예를 들어 다음 네임 스페이스는 응용 프로그램의 루트 네임 스페이스가 `BlazorApp`때 *구성 요소* 폴더의 구성 요소를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="tag-helpers-arent-used-in-components"></a><span data-ttu-id="5f26b-140">태그 도우미는 구성 요소에서 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-140">Tag Helpers aren't used in components</span></span>

<span data-ttu-id="5f26b-141">[태그 도우미](xref:mvc/views/tag-helpers/intro) 는 razor 구성 요소 (*razor* 파일)에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-141">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="5f26b-142">Blazor에서 태그 도우미와 유사한 기능을 제공 하려면 태그 도우미와 동일한 기능을 포함 하는 구성 요소를 만들고 구성 요소를 대신 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-142">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="5f26b-143">구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="5f26b-143">Use components</span></span>

<span data-ttu-id="5f26b-144">구성 요소에는 HTML 요소 구문을 사용 하 여 선언 함으로써 다른 구성 요소가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-144">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="5f26b-145">구성 요소 사용을 위한 태그는 태그 이름이 구성 요소 유형인 HTML 태그처럼 보입니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-145">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="5f26b-146">특성 바인딩은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-146">Attribute binding is case sensitive.</span></span> <span data-ttu-id="5f26b-147">예를 들어 `@bind` 유효 하 고 `@Bind` 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-147">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="5f26b-148">다음은 *인덱스 razor* 의 다음 태그를 `HeadingComponent` 인스턴스를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-148">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="5f26b-149">*Components/HeadingComponent*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-149">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="5f26b-150">구성 요소에 구성 요소 이름과 일치 하지 않는 대문자 첫 글자가 포함 된 HTML 요소가 포함 된 경우 요소에 예기치 않은 이름이 있음을 나타내는 경고가 내보내집니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-150">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="5f26b-151">구성 요소 네임 스페이스에 대 한 `@using` 지시문을 추가 하면 해당 구성 요소를 사용할 수 있게 되므로 경고가 해결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-151">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="5f26b-152">라우팅</span><span class="sxs-lookup"><span data-stu-id="5f26b-152">Routing</span></span>

<span data-ttu-id="5f26b-153">Blazor 라우팅은 앱에서 액세스 가능한 각 구성 요소에 경로 템플릿을 제공 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-153">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="5f26b-154">`@page` 지시어를 사용 하 여 Razor 파일이 컴파일되면 생성 된 클래스에 경로 템플릿을 지정 하는 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-154">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="5f26b-155">런타임에 라우터는 `RouteAttribute`를 사용 하 여 구성 요소 클래스를 검색 하 고 요청 된 URL과 일치 하는 경로 템플릿을 포함 하는 구성 요소를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-155">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="5f26b-156">자세한 내용은 <xref:blazor/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5f26b-156">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="5f26b-157">매개 변수</span><span class="sxs-lookup"><span data-stu-id="5f26b-157">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="5f26b-158">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="5f26b-158">Route parameters</span></span>

<span data-ttu-id="5f26b-159">구성 요소는 `@page` 지시문에 제공 된 경로 템플릿에서 경로 매개 변수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-159">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="5f26b-160">라우터는 경로 매개 변수를 사용 하 여 해당 하는 구성 요소 매개 변수를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-160">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="5f26b-161">*Pages/RouteParameter*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-161">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="5f26b-162">선택적 매개 변수는 지원 되지 않으므로 앞의 예제에서 두 개의 `@page` 지시문이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-162">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="5f26b-163">첫 번째는 매개 변수 없이 구성 요소에 대 한 탐색을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-163">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="5f26b-164">두 번째 `@page` 지시어는 `{text}` route 매개 변수를 받고 `Text` 속성에 값을 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-164">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="5f26b-165">*Catch-* 여러 폴더 경계에서 경로를 캡처하는 모든 매개 변수 구문 (`*`/`**`)은 razor 구성 요소 (*razor*)에서 지원 **되지 않습니다** .</span><span class="sxs-lookup"><span data-stu-id="5f26b-165">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="5f26b-166">구성 요소 매개 변수</span><span class="sxs-lookup"><span data-stu-id="5f26b-166">Component parameters</span></span>

<span data-ttu-id="5f26b-167">구성 요소는 구성 요소 클래스의 공용 속성을 사용 하 여 정의 되는 *구성 요소 매개 변수*를 포함할 수 있습니다 .이 매개 변수는 `[Parameter]` 특성</span><span class="sxs-lookup"><span data-stu-id="5f26b-167">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="5f26b-168">특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-168">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="5f26b-169">*Components/ChildComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-169">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="5f26b-170">샘플 앱의 다음 예제에서는 `ParentComponent` `ChildComponent`의 `Title` 속성 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-170">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="5f26b-171">*Pages/ParentComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-171">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="5f26b-172">자식 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="5f26b-172">Child content</span></span>

<span data-ttu-id="5f26b-173">구성 요소는 다른 구성 요소의 콘텐츠를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-173">Components can set the content of another component.</span></span> <span data-ttu-id="5f26b-174">할당 구성 요소는 받는 구성 요소를 지정 하는 태그 사이에 콘텐츠를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-174">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="5f26b-175">다음 예제에서 `ChildComponent`에는 렌더링할 UI 세그먼트를 나타내는 `RenderFragment`를 나타내는 `ChildContent` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-175">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="5f26b-176">`ChildContent`의 값은 콘텐츠를 렌더링 해야 하는 구성 요소의 태그에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-176">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="5f26b-177">`ChildContent` 값은 부모 구성 요소에서 수신 되 고 부트스트랩 패널의 `panel-body`내에서 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-177">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="5f26b-178">*Components/ChildComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-178">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="5f26b-179">`RenderFragment` 콘텐츠를 받는 속성은 규칙에 따라 `ChildContent` 이름이 지정 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-179">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="5f26b-180">샘플 앱의 `ParentComponent`는 콘텐츠를 `<ChildComponent>` 태그 안에 배치 하 여 `ChildComponent`를 렌더링 하기 위한 콘텐츠를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-180">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="5f26b-181">*Pages/ParentComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-181">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="5f26b-182">특성 스 플랫 및 임의 매개 변수</span><span class="sxs-lookup"><span data-stu-id="5f26b-182">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="5f26b-183">구성 요소는 구성 요소의 선언 된 매개 변수 외에 추가 특성도 캡처하고 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-183">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="5f26b-184">[`@attributes`](xref:mvc/views/razor#attributes) Razor 지시어를 사용 하 여 구성 요소를 렌더링할 때 사전에 추가 특성을 캡처한 다음 요소로 *splatted* 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-184">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="5f26b-185">이 시나리오는 다양 한 사용자 지정을 지 원하는 태그 요소를 생성 하는 구성 요소를 정의 하는 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-185">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="5f26b-186">예를 들어 많은 매개 변수를 지 원하는 `<input>`에 대해 개별적으로 특성을 정의 하는 것이 번거로울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-186">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="5f26b-187">다음 예제에서 첫 번째 `<input>` 요소 (`id="useIndividualParams"`)는 개별 구성 요소 매개 변수를 사용 하 고, 두 번째 `<input>` 요소 (`id="useAttributesDict"`)는 특성 스 플랫를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-187">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="5f26b-188">매개 변수의 형식은 문자열 키를 사용 하 여 `IEnumerable<KeyValuePair<string, object>>`를 구현 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-188">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="5f26b-189">이 시나리오에서는 `IReadOnlyDictionary<string, object>` 사용도 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-189">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="5f26b-190">두 방법을 모두 사용 하 여 렌더링 된 `<input>` 요소는 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-190">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="5f26b-191">임의 특성을 허용 하려면 `CaptureUnmatchedValues` 속성이 `true`로 설정 된 `[Parameter]` 특성을 사용 하 여 구성 요소 매개 변수를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-191">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="5f26b-192">`[Parameter]`의 `CaptureUnmatchedValues` 속성을 사용 하면 매개 변수는 다른 매개 변수와 일치 하지 않는 모든 특성을 일치 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-192">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="5f26b-193">구성 요소는 `CaptureUnmatchedValues`포함 된 단일 매개 변수만 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-193">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="5f26b-194">`CaptureUnmatchedValues`에 사용 되는 속성 형식은 문자열 키가 있는 `Dictionary<string, object>`에서 할당할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-194">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="5f26b-195">이 시나리오에서 `IEnumerable<KeyValuePair<string, object>>` 또는 `IReadOnlyDictionary<string, object>`도 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-195">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="5f26b-196">요소 특성의 위치를 기준으로 `@attributes`의 위치는 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-196">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="5f26b-197">요소에 대 한 `@attributes` splatted 때 오른쪽에서 왼쪽으로 (last to first) 특성을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-197">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="5f26b-198">`Child` 구성 요소를 사용 하는 구성 요소의 다음 예를 살펴보십시오.</span><span class="sxs-lookup"><span data-stu-id="5f26b-198">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="5f26b-199">*Parentcomponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-199">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="5f26b-200">*Childcomponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-200">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="5f26b-201">`Child` 구성 요소의 `extra` 특성은 `@attributes`오른쪽으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-201">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="5f26b-202">특성은 오른쪽에서 왼쪽으로 처리 되기 때문에 `Parent` 구성 요소의 렌더링 된 `<div>`는 추가 특성을 통해 전달 될 때 `extra="5"`를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-202">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="5f26b-203">다음 예에서는 `Child` 구성 요소의 `<div>`에서 `extra`와 `@attributes`의 순서를 반대로 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-203">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="5f26b-204">*Parentcomponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-204">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="5f26b-205">*Childcomponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-205">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="5f26b-206">`Parent` 구성 요소의 렌더링 된 `<div>`에는 추가 특성을 통해 전달 될 경우 `extra="10"` 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-206">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="5f26b-207">구성 요소에 대 한 참조 캡처</span><span class="sxs-lookup"><span data-stu-id="5f26b-207">Capture references to components</span></span>

<span data-ttu-id="5f26b-208">구성 요소 참조는 `Show` 또는 `Reset`와 같이 해당 인스턴스에 대 한 명령을 실행할 수 있도록 구성 요소 인스턴스를 참조 하는 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-208">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="5f26b-209">구성 요소 참조를 캡처하려면:</span><span class="sxs-lookup"><span data-stu-id="5f26b-209">To capture a component reference:</span></span>

* <span data-ttu-id="5f26b-210">자식 구성 요소에 [`@ref`](xref:mvc/views/razor#ref) 특성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-210">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="5f26b-211">자식 구성 요소와 동일한 유형으로 필드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-211">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="5f26b-212">구성 요소가 렌더링 되 면 `_loginDialog` 필드가 `MyLoginDialog` 자식 구성 요소 인스턴스로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-212">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="5f26b-213">그런 다음 구성 요소 인스턴스에서 .NET 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-213">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5f26b-214">`_loginDialog` 변수는 구성 요소가 렌더링 된 후에만 채워지고 출력에는 `MyLoginDialog` 요소가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-214">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="5f26b-215">이 시점까지 참조할 항목이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-215">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="5f26b-216">구성 요소에서 렌더링을 완료 한 후에 구성 요소 참조를 조작 하려면 [OnAfterRenderAsync 또는 OnAfterRender 메서드](xref:blazor/lifecycle#after-component-render)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-216">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="5f26b-217">구성 요소 참조를 캡처하는 것은 [요소 참조를 캡처하](xref:blazor/javascript-interop#capture-references-to-elements)는 데 유사한 구문을 사용 하지만 [JavaScript interop](xref:blazor/javascript-interop) 기능은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-217">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="5f26b-218">구성 요소 참조는 JavaScript 코드에 전달 되지&mdash;.NET 코드 에서만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-218">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="5f26b-219">구성 요소 참조를 사용 하 여 자식 구성 요소의 상태를 변경할 수 **없습니다** .</span><span class="sxs-lookup"><span data-stu-id="5f26b-219">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="5f26b-220">대신, 일반 선언적 매개 변수를 사용 하 여 자식 구성 요소에 데이터를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-220">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="5f26b-221">일반적인 선언적 매개 변수를 사용 하면 자식 구성 요소가 자동으로 올바른 시간에 rerender 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-221">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="5f26b-222">외부에서 구성 요소 메서드를 호출 하 여 상태 업데이트</span><span class="sxs-lookup"><span data-stu-id="5f26b-222">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="5f26b-223">는 `SynchronizationContext`를 사용 하 여 단일 논리적 실행 스레드를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-223"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="5f26b-224">구성 요소의 [수명 주기 메서드](xref:blazor/lifecycle) 및 Blazor에 의해 발생 하는 모든 이벤트 콜백이이 `SynchronizationContext`실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-224">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="5f26b-225">외부 이벤트 (예: 타이머 또는 다른 알림)를 기반으로 구성 요소를 업데이트 해야 하는 경우 Blazor의 `SynchronizationContext`에 디스패치할 `InvokeAsync` 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-225">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="5f26b-226">예를 들어 업데이트 된 상태의 수신 구성 요소를 알릴 수 있는 알림 *서비스* 를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-226">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="5f26b-227">`NotifierService`를 singletion으로 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-227">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="5f26b-228">Blazor Weasembmbambmbsembmba에서 서비스를 `Program.Main`에 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-228">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="5f26b-229">Blazor 서버에서 `Startup.ConfigureServices`에 서비스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-229">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="5f26b-230">`NotifierService`를 사용 하 여 구성 요소를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-230">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="5f26b-231">위의 예제에서 `NotifierService`는 Blazor의 `SynchronizationContext`외부에서 구성 요소의 `OnNotify` 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-231">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="5f26b-232">`InvokeAsync`은 올바른 컨텍스트로 전환 하 고 렌더링을 큐에 대기 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-232">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="5f26b-233">\@키를 사용 하 여 요소 및 구성 요소 유지 관리</span><span class="sxs-lookup"><span data-stu-id="5f26b-233">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="5f26b-234">요소 또는 구성 요소 목록을 렌더링할 때 이후에 요소 또는 구성 요소가 변경 되는 경우 Blazor의 diff 알고리즘은 유지할 수 있는 이전 요소 또는 구성 요소와 모델 개체가 이러한 요소에 매핑되는 방법을 결정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-234">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="5f26b-235">일반적으로이 프로세스는 자동 이며 무시 해도 되지만 프로세스를 제어 하는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-235">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="5f26b-236">다음과 같은 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5f26b-236">Consider the following example:</span></span>

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

<span data-ttu-id="5f26b-237">`People` 컬렉션의 콘텐츠는 삽입, 삭제 또는 다시 정렬 된 항목으로 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-237">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="5f26b-238">구성 요소가 렌더링 면 `<DetailsEditor>` 구성 요소가 다른 `Details` 매개 변수 값을 받도록 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-238">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="5f26b-239">이로 인해 예상 보다 더 복잡 한 rerendering 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-239">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="5f26b-240">경우에 따라 rerendering는 손실 요소 포커스와 같은 표시 되는 동작 차이를 일으킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-240">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="5f26b-241">매핑 프로세스는 `@key` 지시어 특성을 사용 하 여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-241">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="5f26b-242">`@key` diff 알고리즘은 키의 값에 따라 요소 또는 구성 요소를 보존 하도록 보장 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-242">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="5f26b-243">`People` 컬렉션이 변경 되 면 diff 알고리즘은 `<DetailsEditor>` 인스턴스와 `person` 인스턴스 간의 연결을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-243">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="5f26b-244">`People` 목록에서 `Person` 삭제 된 경우 해당 `<DetailsEditor>` 인스턴스만 UI에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-244">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="5f26b-245">다른 인스턴스는 변경 되지 않은 상태로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-245">Other instances are left unchanged.</span></span>
* <span data-ttu-id="5f26b-246">`Person` 목록에서 특정 위치에 삽입 되는 경우 해당 위치에 하나의 새 `<DetailsEditor>` 인스턴스가 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-246">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="5f26b-247">다른 인스턴스는 변경 되지 않은 상태로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-247">Other instances are left unchanged.</span></span>
* <span data-ttu-id="5f26b-248">`Person` 항목이 다시 정렬 되 면 해당 `<DetailsEditor>` 인스턴스가 유지 되 고 UI에서 다시 정렬 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-248">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="5f26b-249">일부 시나리오에서는 `@key`를 사용 하 여 rerendering의 복잡성을 최소화 하 고 DOM의 상태 저장 부분 (예: 포커스 위치)에 대 한 잠재적인 문제를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-249">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5f26b-250">키는 각 컨테이너 요소 또는 구성 요소에 대해 로컬입니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-250">Keys are local to each container element or component.</span></span> <span data-ttu-id="5f26b-251">문서 전체에서 키를 전역적으로 비교 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-251">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="5f26b-252">\@키를 사용 하는 경우</span><span class="sxs-lookup"><span data-stu-id="5f26b-252">When to use \@key</span></span>

<span data-ttu-id="5f26b-253">일반적으로 목록이 렌더링 될 때마다 (예: `@foreach` 블록에서) `@key`를 사용 하 고 `@key`을 정의 하는 데 적합 한 값을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-253">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="5f26b-254">`@key`를 사용 하 여 개체가 변경 될 때 요소가 요소 또는 구성 요소 하위 트리를 유지 Blazor 하지 않도록 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-254">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="5f26b-255">`@currentPerson` 변경 하는 경우 `@key` 특성 지시문은 Blazor 강제로 전체 `<div>` 및 해당 하위 항목을 삭제 하 고 새 요소와 구성 요소를 사용 하 여 UI 내에서 하위 트리를 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-255">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="5f26b-256">이는 `@currentPerson` 변경 될 때 UI 상태가 유지 되지 않도록 해야 하는 경우에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-256">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="5f26b-257">\@키를 사용 하지 않는 경우</span><span class="sxs-lookup"><span data-stu-id="5f26b-257">When not to use \@key</span></span>

<span data-ttu-id="5f26b-258">`@key`으로 diff 때 성능 비용이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-258">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="5f26b-259">성능 비용은 크지 않지만 요소 또는 구성 요소 유지 규칙을 제어 하면 앱이 혜택을 받을 수 있는 경우에만 `@key`을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-259">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="5f26b-260">`@key` 사용 되지 않더라도 Blazor는 자식 요소와 구성 요소 인스턴스를 최대한 많이 보존 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-260">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="5f26b-261">`@key`를 사용 하는 유일한 장점은 매핑을 선택 하는 diff 알고리즘 대신 모델 인스턴스가 유지 된 구성 요소 인스턴스에 매핑되 *는 방식을* 제어 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-261">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="5f26b-262">\@키에 사용할 값</span><span class="sxs-lookup"><span data-stu-id="5f26b-262">What values to use for \@key</span></span>

<span data-ttu-id="5f26b-263">일반적으로 `@key`에 대해 다음과 같은 종류의 값 중 하나를 제공 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-263">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="5f26b-264">모델 개체 인스턴스 (예: 이전 예제와 같은 `Person` 인스턴스)</span><span class="sxs-lookup"><span data-stu-id="5f26b-264">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="5f26b-265">이렇게 하면 개체 참조 일치를 기반으로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-265">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="5f26b-266">고유 식별자 (예: `int`, `string`, `Guid`)의 기본 키 값입니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-266">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="5f26b-267">`@key`에 사용 되는 값이 충돌 하지 않는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-267">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="5f26b-268">동일한 부모 요소 내에서 충돌 방지 값이 검색 되는 경우 기존 요소나 구성 요소를 새 요소나 구성 요소에 명확 하 게 매핑할 수 없기 때문에 Blazor 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-268">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="5f26b-269">개체 인스턴스 또는 기본 키 값과 같은 고유 값만 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-269">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="5f26b-270">Partial 클래스 지원</span><span class="sxs-lookup"><span data-stu-id="5f26b-270">Partial class support</span></span>

<span data-ttu-id="5f26b-271">Razor 구성 요소는 부분 클래스로 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-271">Razor components are generated as partial classes.</span></span> <span data-ttu-id="5f26b-272">Razor 구성 요소는 다음 방법 중 하나를 사용 하 여 작성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-272">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="5f26b-273">C#코드는 HTML 태그와 Razor 코드를 포함 하는 [`@code`](xref:mvc/views/razor#code) 블록에서 단일 파일로 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-273">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="5f26b-274"> 템플릿은이 접근 방식을 사용 하 여 Razor 구성 요소를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-274"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="5f26b-275">C#코드는 partial 클래스로 정의 된 코드 숨김이 파일에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-275">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="5f26b-276">다음 예제에서는 Blazor 템플릿에서 생성 된 앱에서 `@code` 블록을 사용 하는 기본 `Counter` 구성 요소를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-276">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="5f26b-277">HTML 태그, Razor 코드 및 C# 코드는 동일한 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-277">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="5f26b-278">*카운터. razor*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-278">*Counter.razor*:</span></span>

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

<span data-ttu-id="5f26b-279">Partial 클래스를 포함 하는 코드를 사용 하 여 `Counter` 구성 요소를 만들 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-279">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="5f26b-280">*카운터. razor*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-280">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="5f26b-281">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-281">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="5f26b-282">필요한 경우 partial 클래스 파일에 필요한 네임 스페이스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-282">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="5f26b-283">Razor 구성 요소에 사용 되는 일반적인 네임 스페이스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-283">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="5f26b-284">기본 클래스 지정</span><span class="sxs-lookup"><span data-stu-id="5f26b-284">Specify a base class</span></span>

<span data-ttu-id="5f26b-285">[`@inherits`](xref:mvc/views/razor#inherits) 지시어를 사용 하 여 구성 요소에 대 한 기본 클래스를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-285">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="5f26b-286">다음 예제에서는 구성 요소가 기본 클래스 `BlazorRocksBase`를 상속 하 여 구성 요소의 속성과 메서드를 제공 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-286">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="5f26b-287">기본 클래스는 `ComponentBase`에서 파생 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-287">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="5f26b-288">*Pages/BlazorRocks*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-288">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="5f26b-289">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-289">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="5f26b-290">특성 지정</span><span class="sxs-lookup"><span data-stu-id="5f26b-290">Specify an attribute</span></span>

<span data-ttu-id="5f26b-291">특성은 [`@attribute`](xref:mvc/views/razor#attribute) 지시어를 사용 하 여 Razor 구성 요소에 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-291">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="5f26b-292">다음 예제에서는 `[Authorize]` 특성을 component 클래스에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-292">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="5f26b-293">구성 요소 가져오기</span><span class="sxs-lookup"><span data-stu-id="5f26b-293">Import components</span></span>

<span data-ttu-id="5f26b-294">Razor로 작성 된 구성 요소의 네임 스페이스는 (우선 순위)를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-294">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="5f26b-295">Razor 파일 (*razor*) 태그에 지정 [`@namespace`](xref:mvc/views/razor#namespace) (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="5f26b-295">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="5f26b-296">프로젝트 파일 (`<RootNamespace>BlazorSample</RootNamespace>`)에서 프로젝트의 `RootNamespace`입니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-296">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="5f26b-297">프로젝트 파일의 파일 이름 ( *.csproj*)에서 가져온 프로젝트 이름 및 프로젝트 루트에서 구성 요소로의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-297">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="5f26b-298">예를 들어 프레임 워크는 네임 스페이스 `BlazorSample.Pages` *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample*)를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-298">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="5f26b-299">구성 요소 C# 는 이름 바인딩 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-299">Components follow C# name binding rules.</span></span> <span data-ttu-id="5f26b-300">이 예의 `Index` 구성 요소에 대 한 범위에 있는 구성 요소는 모든 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-300">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="5f26b-301">같은 폴더 *에 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="5f26b-301">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="5f26b-302">프로젝트 루트에서 다른 네임 스페이스를 명시적으로 지정 하지 않은 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-302">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="5f26b-303">다른 네임 스페이스에 정의 된 구성 요소는 Razor의 [`@using`](xref:mvc/views/razor#using) 지시어를 사용 하 여 범위로 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-303">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="5f26b-304">*BlazorSample/Shared/* folder에 다른 구성 요소인 `NavMenu.razor`있는 경우 다음 `@using` 문을 사용 하 여 `Index.razor`에서 구성 요소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-304">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="5f26b-305">[`@using`](xref:mvc/views/razor#using) 지시문이 필요 하지 않은 정규화 된 이름을 사용 하 여 구성 요소를 참조할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-305">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="5f26b-306">`global::` 한정자는 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-306">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="5f26b-307">별칭이 지정 된 `using` 문 (예: `@using Foo = Bar`)을 가져오는 구성 요소가 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-307">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="5f26b-308">부분적으로 정규화 된 이름은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-308">Partially qualified names aren't supported.</span></span> <span data-ttu-id="5f26b-309">예를 들어 `@using BlazorSample`를 추가 하 고 `<Shared.NavMenu></Shared.NavMenu>`를 사용 하 `NavMenu.razor`를 참조 하는 것은 지원 되지 않습니다</span><span class="sxs-lookup"><span data-stu-id="5f26b-309">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="5f26b-310">조건부 HTML 요소 특성</span><span class="sxs-lookup"><span data-stu-id="5f26b-310">Conditional HTML element attributes</span></span>

<span data-ttu-id="5f26b-311">HTML 요소 특성은 .NET 값에 따라 조건부로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-311">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="5f26b-312">값이 `false` 또는 `null`이면 특성이 렌더링 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-312">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="5f26b-313">값이 `true`이면 특성이 최소화 된 상태로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-313">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="5f26b-314">다음 예제에서 `IsCompleted`는 `checked` 요소의 태그에서 렌더링 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-314">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="5f26b-315">`IsCompleted` `true`이면 확인란은 다음과 같이 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-315">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="5f26b-316">`IsCompleted` `false`이면 확인란은 다음과 같이 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-316">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="5f26b-317">자세한 내용은 <xref:mvc/views/razor>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5f26b-317">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="5f26b-318">ASP.NET 형식이 `bool`경우에는 [aria를 누르는](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)등의 일부 HTML 특성이 제대로 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-318">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="5f26b-319">이러한 경우 `bool`대신 `string` 유형을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-319">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="5f26b-320">원시 HTML</span><span class="sxs-lookup"><span data-stu-id="5f26b-320">Raw HTML</span></span>

<span data-ttu-id="5f26b-321">일반적으로 문자열은 DOM 텍스트 노드를 사용 하 여 렌더링 됩니다. 즉, 포함 될 수 있는 모든 태그는 무시 되 고 리터럴 텍스트로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-321">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="5f26b-322">원시 HTML을 렌더링 하려면 HTML 콘텐츠를 `MarkupString` 값으로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-322">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="5f26b-323">값은 HTML 또는 SVG로 구문 분석 되 고 DOM에 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-323">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="5f26b-324">신뢰할 수 없는 소스에서 생성 된 원시 HTML을 렌더링 하면 **보안상 위험할** 수 있으므로 피해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-324">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="5f26b-325">다음 예제에서는 `MarkupString` 형식을 사용 하 여 정적 HTML 콘텐츠 블록을 구성 요소의 렌더링 된 출력에 추가 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-325">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="5f26b-326">연계 값 및 매개 변수</span><span class="sxs-lookup"><span data-stu-id="5f26b-326">Cascading values and parameters</span></span>

<span data-ttu-id="5f26b-327">일부 시나리오에서는 [구성 요소 매개 변수](#component-parameters)를 사용 하 여 상위 구성 요소에서 하위 구성 요소로 데이터를 이동 하는 것이 불편 합니다. 특히 구성 요소 계층이 여러 개 있는 경우에는 더욱 그렇습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-327">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="5f26b-328">연계 값 및 매개 변수는 상위 구성 요소에서 모든 하위 구성 요소에 값을 제공 하는 편리한 방법을 제공 하 여이 문제를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-328">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="5f26b-329">연계 값 및 매개 변수를 사용 하 여 구성 요소를 조정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-329">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="5f26b-330">테마 예</span><span class="sxs-lookup"><span data-stu-id="5f26b-330">Theme example</span></span>

<span data-ttu-id="5f26b-331">샘플 앱의 다음 예제에서 `ThemeInfo` 클래스는 응용 프로그램의 지정 된 부분에 있는 모든 단추가 동일한 스타일을 공유할 수 있도록 구성 요소 계층 구조의 아래로 이동 하는 테마 정보를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-331">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="5f26b-332">*UIThemeClasses/ThemeInfo*:</span><span class="sxs-lookup"><span data-stu-id="5f26b-332">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="5f26b-333">상위 구성 요소는 연계 값 구성 요소를 사용 하 여 연계 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-333">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="5f26b-334">`CascadingValue` 구성 요소는 구성 요소 계층의 하위 트리를 래핑하고 해당 하위 트리 내의 모든 구성 요소에 단일 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-334">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="5f26b-335">예를 들어 샘플 앱은 `@Body` 속성의 레이아웃 본문을 구성 하는 모든 구성 요소에 대 한 연계 매개 변수로 앱 레이아웃 중 하나에서 테마 정보 (`ThemeInfo`)를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-335">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="5f26b-336">`ButtonClass`에 레이아웃 구성 요소에서 `btn-success` 값이 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-336">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="5f26b-337">모든 하위 구성 요소는 `ThemeInfo` 연계 개체를 통해이 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-337">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="5f26b-338">`CascadingValuesParametersLayout` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="5f26b-338">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="5f26b-339">연계 값을 사용 하기 위해 구성 요소는 `[CascadingParameter]` 특성을 사용 하 여 연계 매개 변수를 선언 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-339">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="5f26b-340">연계 값은 유형별 매개 변수에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-340">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="5f26b-341">샘플 앱에서 `CascadingValuesParametersTheme` 구성 요소는 `ThemeInfo` 연계 값을 연계 매개 변수에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-341">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="5f26b-342">매개 변수는 구성 요소에 의해 표시 되는 단추 중 하나에 대해 CSS 클래스를 설정 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-342">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="5f26b-343">`CascadingValuesParametersTheme` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="5f26b-343">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="5f26b-344">동일한 하위 트리 내에서 동일한 형식의 여러 값을 계단식으로 배열 하려면 각 `CascadingValue` 구성 요소와 해당 `CascadingParameter`에 고유한 `Name` 문자열을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-344">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="5f26b-345">다음 예에서는 두 개의 `CascadingValue` 구성 요소가 이름별로 `MyCascadingType`의 다른 인스턴스를 계단식으로 배열 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-345">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="5f26b-346">하위 구성 요소에서 종속 매개 변수는 상위 구성 요소의 해당 하는 종속 된 값에서 이름으로 해당 값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-346">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="5f26b-347">TabSet 예제</span><span class="sxs-lookup"><span data-stu-id="5f26b-347">TabSet example</span></span>

<span data-ttu-id="5f26b-348">연계 매개 변수를 사용 하면 구성 요소 계층 구조 전체에서 공동 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-348">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="5f26b-349">예를 들어 샘플 앱에서 다음 *Tabset* 예제를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="5f26b-349">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="5f26b-350">샘플 앱에는 탭에서 구현 하는 `ITab` 인터페이스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-350">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="5f26b-351">`CascadingValuesParametersTabSet` 구성 요소는 여러 `Tab` 구성 요소를 포함 하는 `TabSet` 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-351">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="5f26b-352">자식 `Tab` 구성 요소는 `TabSet`에 대 한 매개 변수로 명시적으로 전달 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-352">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="5f26b-353">대신 자식 `Tab` 구성 요소는 `TabSet`의 자식 콘텐츠에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-353">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="5f26b-354">그러나 `TabSet`는 헤더와 활성 탭을 렌더링할 수 있도록 각 `Tab` 구성 요소에 대 한 정보를 계속 알고 있어야 합니다. 추가 코드를 요구 하지 않고이 조정을 사용 하기 위해 `TabSet` 구성 요소는 하위 `Tab` 구성 요소에 의해 선택 되는 *연계 값으로 자신을 제공할 수 있습니다* .</span><span class="sxs-lookup"><span data-stu-id="5f26b-354">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="5f26b-355">`TabSet` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="5f26b-355">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="5f26b-356">하위 `Tab` 구성 요소는 포함 하는 `TabSet`를 연계 매개 변수로 캡처하여 `Tab` 구성 요소가 `TabSet`에 추가 되 고 활성화 되는 탭을 조정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-356">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="5f26b-357">`Tab` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="5f26b-357">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="5f26b-358">Razor 템플릿</span><span class="sxs-lookup"><span data-stu-id="5f26b-358">Razor templates</span></span>

<span data-ttu-id="5f26b-359">렌더링 조각은 Razor 템플릿 구문을 사용 하 여 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-359">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="5f26b-360">Razor 템플릿은 UI 코드 조각을 정의 하 고 다음 형식을 가정 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-360">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="5f26b-361">다음 예제에서는 `RenderFragment` 및 `RenderFragment<T>` 값을 지정 하 고 구성 요소에서 직접 템플릿을 렌더링 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-361">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="5f26b-362">렌더링 조각은 [템플릿 기반 구성 요소](xref:blazor/templated-components)에 인수로 전달 될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-362">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

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

<span data-ttu-id="5f26b-363">이전 코드의 렌더링 된 출력:</span><span class="sxs-lookup"><span data-stu-id="5f26b-363">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="5f26b-364">SVG (스케일러블 벡터 그래픽) 이미지</span><span class="sxs-lookup"><span data-stu-id="5f26b-364">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="5f26b-365">Blazor는 HTML을 렌더링 하므로 SVG (확장 가능한 벡터 그래픽) 이미지 (*svg*)를 비롯 한 브라우저 지원 이미지는 `<img>` 태그를 통해 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-365">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="5f26b-366">마찬가지로, 스타일 시트 파일 ( *.css*)의 css 규칙에서 SVG 이미지가 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-366">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="5f26b-367">그러나 인라인 SVG 태그는 일부 시나리오에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-367">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="5f26b-368">구성 요소 파일 (*razor*)에 `<svg>` 태그를 직접 저장 하는 경우 기본 이미지 렌더링이 지원 되지만 많은 고급 시나리오는 아직 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-368">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="5f26b-369">예를 들어 `<use>` 태그는 현재 적용 되지 않으며 `@bind` 일부 SVG 태그와 함께 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-369">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="5f26b-370">향후 릴리스에서 이러한 제한을 해결할 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-370">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5f26b-371">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="5f26b-371">Additional resources</span></span>

* <span data-ttu-id="5f26b-372"><xref:security/blazor/server> &ndash;에는 리소스 고갈에 경합 해야 하는 Blazor Server 앱 빌드에 대 한 지침이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f26b-372"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
