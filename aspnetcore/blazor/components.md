---
title: ASP.NET Core Razor 구성 요소 만들기 및 사용
author: guardrex
description: 데이터에 바인딩하고, 이벤트를 처리 하 고, 구성 요소 수명 주기를 관리 하는 방법을 비롯 하 여 Razor 구성 요소를 만들고 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: blazor/components
ms.openlocfilehash: 438b3802087e2ac3df4cbe69a700b878c1cbbf63
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037428"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="95319-103">ASP.NET Core Razor 구성 요소 만들기 및 사용</span><span class="sxs-lookup"><span data-stu-id="95319-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="95319-104">By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="95319-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="95319-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="95319-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="95319-106">Blazor apps는 *구성 요소*를 사용 하 여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="95319-107">구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI (사용자 인터페이스)의 자체 포함 청크입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="95319-108">구성 요소는 데이터를 삽입 하거나 UI 이벤트에 응답 하는 데 필요한 HTML 태그와 처리 논리를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="95319-109">구성 요소는 유연 하 고 간단 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="95319-110">프로젝트 간에 중첩, 재사용 및 공유 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="95319-111">구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="95319-111">Component classes</span></span>

<span data-ttu-id="95319-112">구성 요소는 및 HTML 태그의 C# 조합을 사용 하 여 [razor](xref:mvc/views/razor) 구성 요소 파일 (*razor*)에서 구현 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="95319-113">Blazor의 구성 요소는 공식적으로 *Razor 구성 요소*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="95319-114">구성 요소의 이름은 대문자로 시작 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="95319-115">예를 들어 *MyCoolComponent* 는 유효 하며 *MyCoolComponent* 은 유효 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="95319-116">구성 요소에 대 한 UI는 HTML을 사용 하 여 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="95319-117">동적 렌더링 논리(예: 루프, 조건, 식)는 [Razor](xref:mvc/views/razor)라는 포함된 C# 구문을 사용하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="95319-118">앱이 컴파일되면 HTML 태그 및 C# 렌더링 논리가 구성 요소 클래스로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="95319-119">생성 된 클래스의 이름은 파일 이름과 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="95319-120">구성 요소 클래스의 멤버는 `@code` 블록에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="95319-121">@No__t-0 블록에서 구성 요소 상태 (속성, 필드)는 이벤트 처리를 위한 메서드나 다른 구성 요소 논리를 정의 하는 데 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="95319-122">두 개 이상의 `@code` 블록이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-122">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="95319-123">ASP.NET Core 3.0의 이전 미리 보기에서 `@functions` 블록은 Razor 구성 요소에서 `@code` 블록과 동일한 용도로 사용 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-123">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="95319-124">`@functions` 블록은 Razor 구성 요소에서 계속 작동 하지만 ASP.NET Core 3.0 Preview 6 이상에서 `@code` 블록을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-124">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="95319-125">구성 요소 멤버는 `@`로 시작 하는 식을 사용 하 C# 여 구성 요소의 렌더링 논리의 일부로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-125">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="95319-126">예를 들어 필드 C# 는 필드 이름 앞에 `@`을 접두사로 하 여 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-126">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="95319-127">다음 예에서는를 평가 하 고 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-127">The following example evaluates and renders:</span></span>

* <span data-ttu-id="95319-128">-0은 `font-style`에 대 한 CSS 속성 값을 @no__t 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-128">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="95319-129">`<h1>` 요소의 내용에 `_headingText`입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-129">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="95319-130">구성 요소가 처음 렌더링 되 면 구성 요소는 이벤트에 대 한 응답으로 렌더링 트리를 다시 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-130">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="95319-131">그런 다음 Blazor는 새 렌더링 트리를 이전 렌더링 트리와 비교 하 여 브라우저의 DOM (문서 개체 모델)에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-131">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="95319-132">구성 요소는 C# 일반 클래스 이며 프로젝트 내의 어느 위치에 나 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-132">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="95319-133">웹 페이지를 생성 하는 구성 요소는 일반적으로 *Pages* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-133">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="95319-134">페이지를 지정 하지 않는 구성 요소는 일반적으로 *공유* 폴더 또는 프로젝트에 추가 된 사용자 지정 폴더에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-134">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="95319-135">사용자 지정 폴더를 사용 하려면 부모 구성 요소 또는 앱의 *_Imports. razor* 파일에 사용자 지정 폴더의 네임 스페이스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-135">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="95319-136">예를 들어 다음 네임 스페이스는 응용 프로그램의 루트 네임 스페이스가-1 @no__t 때 *구성* 요소 폴더의 구성 요소를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-136">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="95319-137">Razor Pages 및 MVC 앱에 구성 요소 통합</span><span class="sxs-lookup"><span data-stu-id="95319-137">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="95319-138">기존 Razor Pages 및 MVC 앱과 함께 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-138">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="95319-139">Razor 구성 요소를 사용 하기 위해 기존 페이지나 뷰를 다시 작성할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-139">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="95319-140">페이지 또는 뷰가 렌더링 될 때 구성 요소는 동시에 미리 렌더링 된 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-140">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="95319-141">페이지 또는 뷰에서 구성 요소를 렌더링 하려면 `RenderComponentAsync<TComponent>` HTML 도우미 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-141">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

<span data-ttu-id="95319-142">페이지 및 뷰에서 구성 요소를 사용할 수 있지만 반대의 경우는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="95319-143">구성 요소는 부분 보기 및 섹션과 같은 보기 및 페이지 관련 시나리오를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-143">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="95319-144">구성 요소의 부분 뷰에서 논리를 사용 하려면 부분 뷰 논리를 구성 요소에 대 한 요소로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-144">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="95319-145">구성 요소를 렌더링 하는 방법과 구성 요소 상태가 Blazor Server 앱에서 관리 되는 방법에 대 한 자세한 내용은 <xref:blazor/hosting-models> 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="95319-145">For more information on how components are rendered and component state is managed in Blazor Server apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="use-components"></a><span data-ttu-id="95319-146">구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="95319-146">Use components</span></span>

<span data-ttu-id="95319-147">구성 요소에는 HTML 요소 구문을 사용 하 여 선언 함으로써 다른 구성 요소가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-147">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="95319-148">구성 요소 사용을 위한 태그는 태그 이름이 구성 요소 유형인 HTML 태그처럼 보입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-148">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="95319-149">특성 바인딩은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-149">Attribute binding is case sensitive.</span></span> <span data-ttu-id="95319-150">예를 들어 `@bind`은 유효 하 고 `@Bind`은 유효 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-150">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="95319-151">*인덱스 razor* 의 다음 태그는 @no__t 1 인스턴스를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-151">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="95319-152">*Components/HeadingComponent*:</span><span class="sxs-lookup"><span data-stu-id="95319-152">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

<span data-ttu-id="95319-153">구성 요소에 구성 요소 이름과 일치 하지 않는 대문자 첫 글자가 포함 된 HTML 요소가 포함 된 경우 요소에 예기치 않은 이름이 있음을 나타내는 경고가 내보내집니다.</span><span class="sxs-lookup"><span data-stu-id="95319-153">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="95319-154">구성 요소 네임 스페이스에 대 한 `@using` 문을 추가 하면 구성 요소를 사용할 수 있게 되므로 경고가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-154">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="95319-155">구성 요소 매개 변수</span><span class="sxs-lookup"><span data-stu-id="95319-155">Component parameters</span></span>

<span data-ttu-id="95319-156">구성 요소에는 구성 요소 매개 변수를 포함할 수 있습니다 .이 *매개 변수*는 구성 요소 클래스에서 `[Parameter]` 특성을 사용 하 여 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-156">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="95319-157">특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-157">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="95319-158">*Components/ChildComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="95319-158">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="95319-159">다음 예제에서 `ParentComponent`은 `ChildComponent`의 `Title` 속성 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-159">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="95319-160">*Pages/ParentComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="95319-160">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="95319-161">자식 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="95319-161">Child content</span></span>

<span data-ttu-id="95319-162">구성 요소는 다른 구성 요소의 콘텐츠를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-162">Components can set the content of another component.</span></span> <span data-ttu-id="95319-163">할당 구성 요소는 받는 구성 요소를 지정 하는 태그 사이에 콘텐츠를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-163">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="95319-164">다음 예제에서 `ChildComponent`에는 렌더링할 UI 세그먼트를 나타내는 `RenderFragment`를 나타내는 @no__t 1 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-164">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="95319-165">@No__t의 값은 콘텐츠를 렌더링 해야 하는 구성 요소의 태그에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-165">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="95319-166">@No__t-0의 값은 부모 구성 요소에서 수신 되 고 부트스트랩 패널의 `panel-body` 내에서 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-166">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="95319-167">*Components/ChildComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="95319-167">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="95319-168">@No__t-0 콘텐츠를 받는 속성의 이름은 규칙에 따라 `ChildContent`로 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-168">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="95319-169">다음 `ParentComponent`은 `<ChildComponent>` 태그 안에 내용을 배치 하 여 `ChildComponent`을 렌더링 하기 위한 콘텐츠를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-169">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="95319-170">*Pages/ParentComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="95319-170">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="95319-171">특성 스 플랫 및 임의 매개 변수</span><span class="sxs-lookup"><span data-stu-id="95319-171">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="95319-172">구성 요소는 구성 요소의 선언 된 매개 변수 외에 추가 특성도 캡처하고 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-172">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="95319-173">[@No__t-2](xref:mvc/views/razor#attributes) Razor 지시문을 사용 하 여 구성 요소를 렌더링할 때 사전에 추가 특성을 캡처한 다음 요소로 *splatted* 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-173">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="95319-174">이 시나리오는 다양 한 사용자 지정을 지 원하는 태그 요소를 생성 하는 구성 요소를 정의 하는 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-174">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="95319-175">예를 들어 많은 매개 변수를 지 원하는 `<input>`에 대해 개별적으로 특성을 정의 하는 것이 번거로울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-175">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="95319-176">다음 예제에서 첫 번째 `<input>` 요소 (`id="useIndividualParams"`)는 개별 구성 요소 매개 변수를 사용 하는 반면 두 번째 `<input>` 요소 (`id="useAttributesDict"`)는 특성 스 플랫를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-176">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```cshtml
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

<span data-ttu-id="95319-177">매개 변수의 형식은 문자열 키가 `IEnumerable<KeyValuePair<string, object>>`을 구현 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-177">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="95319-178">이 시나리오에서는 `IReadOnlyDictionary<string, object>`을 사용 하는 것도 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-178">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="95319-179">두 방법을 모두 사용 하 여 렌더링 된 `<input>` 요소는 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-179">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="95319-180">임의 특성을 허용 하려면 `CaptureUnmatchedValues` 속성이 `true`로 설정 된 `[Parameter]` 특성을 사용 하 여 구성 요소 매개 변수를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-180">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="95319-181">@No__t-1의 `CaptureUnmatchedValues` 속성은 매개 변수가 다른 매개 변수와 일치 하지 않는 모든 특성을 일치 시킬 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-181">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="95319-182">구성 요소는 `CaptureUnmatchedValues` 인 단일 매개 변수만 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-182">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="95319-183">@No__t-0과 함께 사용 되는 속성 형식은 문자열 키가 있는 `Dictionary<string, object>`에서 할당할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-183">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="95319-184">이 시나리오에서 `IEnumerable<KeyValuePair<string, object>>` 또는 `IReadOnlyDictionary<string, object>`도 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-184">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

## <a name="data-binding"></a><span data-ttu-id="95319-185">데이터 바인딩</span><span class="sxs-lookup"><span data-stu-id="95319-185">Data binding</span></span>

<span data-ttu-id="95319-186">구성 요소와 DOM 요소 모두에 대 한 데이터 바인딩은 [@bind](xref:mvc/views/razor#bind) 특성을 사용 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-186">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="95319-187">다음 예에서는 `_italicsCheck` 필드를 확인란의 선택 된 상태에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-187">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="95319-188">확인란을 선택 하 고 선택 취소 하면 속성의 값이 각각 `true` 및 `false`로 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-188">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="95319-189">확인란은 속성의 값을 변경 하는 것이 아니라 구성 요소가 렌더링 되는 경우에만 UI에서 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-189">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="95319-190">이벤트 처리기 코드를 실행 한 후 구성 요소를 자체적으로 렌더링 하므로 속성 업데이트는 일반적으로 UI에 즉시 반영 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-190">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="95319-191">@No__t-1 속성 (`<input @bind="CurrentValue" />`)과 함께 `@bind`을 사용 하는 것은 기본적으로 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-191">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="95319-192">구성 요소가 렌더링 되 면 입력 요소의 @no__t 0은 `CurrentValue` 속성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="95319-192">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="95319-193">사용자가 텍스트 상자에를 입력 하면 `onchange` 이벤트가 발생 하 고 `CurrentValue` 속성이 변경 된 값으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-193">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="95319-194">실제로는 `@bind`은 형식 변환이 수행 되는 몇 가지 경우를 처리 하기 때문에 코드 생성은 약간 더 복잡 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-194">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="95319-195">원칙적으로 `@bind`은 식의 현재 값을 `value` 특성과 연결 하 고 등록 된 처리기를 사용 하 여 변경 내용을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-195">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="95319-196">@No__t-1 구문을 사용 하 여 `onchange` 이벤트를 처리 하는 것 외에도 `event` 매개 변수 ([@no__t](xref:mvc/views/razor#bind))를 사용 하 여 [@bind-value](xref:mvc/views/razor#bind) 특성을 지정 하 여 다른 이벤트를 사용 하 여 속성 또는 필드를 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-196">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="95319-197">다음 예에서는 `oninput` 이벤트의 `CurrentValue` 속성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-197">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="95319-198">요소가 포커스를 잃을 때 발생 하는 `onchange`과 달리 텍스트 상자의 값이 변경 되 면 `oninput`이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-198">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="95319-199">**구문 분석할 값**</span><span class="sxs-lookup"><span data-stu-id="95319-199">**Unparsable values**</span></span>

<span data-ttu-id="95319-200">사용자가 데이터 바인딩된 요소에 구문 분석할 수 없는 값을 제공 하면 bind 이벤트가 트리거될 때 구문 분석할 수 없는 값이 자동으로 이전 값으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="95319-200">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="95319-201">다음과 같은 시나리오를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="95319-201">Consider the following scenario:</span></span>

* <span data-ttu-id="95319-202">@No__t-0 요소는 초기 값이 `123` 인 `int` 형식에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-202">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```cshtml
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="95319-203">사용자는 페이지에서 요소 값을 `123.45`으로 업데이트 하 고 요소 포커스를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-203">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="95319-204">위의 시나리오에서 요소 값은 `123`으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="95319-204">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="95319-205">값 `123.45`이 `123`의 원래 값을 사용 하 여 거부 되 면 사용자는 해당 값이 허용 되지 않았음을 인식 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-205">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="95319-206">기본적으로 바인딩은 요소의 `onchange` 이벤트 (`@bind="{PROPERTY OR FIELD}"`)에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-206">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="95319-207">@No__t-0을 사용 하 여 다른 이벤트를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-207">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="95319-208">@No__t-0 이벤트 (`@bind-value:event="oninput"`)의 경우 변경할지는 구문 분석할 값을 제공 하는 키 입력 후에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-208">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="95319-209">@No__t -1 바인딩된 형식의 `oninput` 이벤트를 대상으로 지정 하는 경우 사용자는 `.` 문자를 입력할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-209">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="95319-210">@No__t-0 문자는 즉시 제거 되므로 사용자는 전체 숫자만 허용 되는 즉각적인 피드백을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-210">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="95319-211">사용자가 구문 분석할 수 없는 @no__t 값을 지울 수 있어야 하는 경우와 같이 `oninput` 이벤트의 값을 되돌리는 것이 적합 하지 않은 시나리오가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-211">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="95319-212">대안은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-212">Alternatives include:</span></span>

* <span data-ttu-id="95319-213">@No__t-0 이벤트를 사용 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="95319-213">Don't use the `oninput` event.</span></span> <span data-ttu-id="95319-214">기본 `onchange` 이벤트 (`@bind="{PROPERTY OR FIELD}"`)를 사용 합니다 .이 이벤트는 요소가 포커스를 잃을 때까지 잘못 된 값을 되돌리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-214">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="95319-215">@No__t-0 또는 `string`과 같은 nullable 형식에 바인딩하고, 잘못 된 항목을 처리 하는 사용자 지정 논리를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-215">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="95319-216">@No__t-1 또는 `InputDate`와 같은 [폼 유효성 검사 구성 요소](xref:blazor/forms-validation)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-216">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="95319-217">양식 유효성 검사 구성 요소에는 잘못 된 입력을 관리 하기 위한 기본 제공 지원이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-217">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="95319-218">양식 유효성 검사 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="95319-218">Form validation components:</span></span>
  * <span data-ttu-id="95319-219">사용자가 연결 된 `EditContext`에 대해 잘못 된 입력 및 수신 유효성 검사 오류를 제공할 수 있도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-219">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="95319-220">사용자가 추가 webform 데이터를 입력 하는 것을 방해 하지 않고 UI에서 유효성 검사 오류를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-220">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="95319-221">**전역화**</span><span class="sxs-lookup"><span data-stu-id="95319-221">**Globalization**</span></span>

<span data-ttu-id="95319-222">`@bind` 값은 현재 문화권의 규칙을 사용 하 여 표시 및 구문 분석을 위해 형식이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-222">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="95319-223">@No__t-0 속성에서 현재 문화권에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-223">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="95319-224">[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) 는 다음 필드 형식 (`<input type="{TYPE}" />`)에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-224">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="95319-225">이전 필드 형식:</span><span class="sxs-lookup"><span data-stu-id="95319-225">The preceding field types:</span></span>

* <span data-ttu-id="95319-226">적절 한 브라우저 기반 서식 지정 규칙을 사용 하 여 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-226">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="95319-227">자유 형식 텍스트를 포함할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-227">Can't contain free-form text.</span></span>
* <span data-ttu-id="95319-228">브라우저의 구현에 따라 사용자 상호 작용 특성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-228">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="95319-229">다음 필드 형식은 특정 형식 지정 요구 사항을 가지 며 현재 Blazor에서 지원 되지 않습니다. 모든 주요 브라우저에서 지원 되지 않기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-229">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="95319-230">`@bind`은 `@bind:culture` 매개 변수를 지원 하 여 값을 구문 분석 하 고 서식을 지정 하는 데 <xref:System.Globalization.CultureInfo?displayProperty=fullName>를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-230">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="95319-231">@No__t-0 및 `number` 필드 형식을 사용할 때는 문화권을 지정 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-231">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="95319-232">`date` 및 `number`은 필수 문화권을 제공 하는 기본 제공 Blazor 지원입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-232">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="95319-233">사용자의 문화권을 설정 하는 방법에 대 한 자세한 내용은 [지역화](#localization) 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="95319-233">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="95319-234">**서식 문자열**</span><span class="sxs-lookup"><span data-stu-id="95319-234">**Format strings**</span></span>

<span data-ttu-id="95319-235">데이터 바인딩은 [@bind:format](xref:mvc/views/razor#bind)를 사용 하 여 <xref:System.DateTime> 형식 문자열에서 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-235">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="95319-236">통화 또는 숫자 형식과 같은 다른 형식 식은 현재 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-236">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="95319-237">위의 코드에서 `<input>` 요소의 필드 형식 (`type`)은 기본적으로 `text`로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-237">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="95319-238">`@bind:format`은 다음 .NET 형식의 바인딩에 대해 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-238">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="95319-239"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="95319-239"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="95319-240"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="95319-240"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="95319-241">@No__t-0 특성은 `<input>` 요소의 `value`에 적용할 날짜 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-241">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="95319-242">이 형식은 `onchange` 이벤트가 발생할 때 값을 구문 분석 하는 데도 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-242">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="95319-243">Blazor에서 날짜 형식을 기본적으로 지원 하기 때문에 `date` 필드 형식의 형식을 지정 하는 것은 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-243">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

<span data-ttu-id="95319-244">**구성 요소 매개 변수**</span><span class="sxs-lookup"><span data-stu-id="95319-244">**Component parameters**</span></span>

<span data-ttu-id="95319-245">바인딩은 구성 요소 매개 변수를 인식 합니다. 여기서 `@bind-{property}`은 구성 요소에서 속성 값을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-245">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="95319-246">다음 자식 구성 요소 (`ChildComponent`)에는 `Year` 구성 요소 매개 변수와 `YearChanged` 콜백이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-246">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="95319-247">`EventCallback<T>`은 [Eventcallback](#eventcallback) 섹션에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-247">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="95319-248">다음 부모 구성 요소는 `ChildComponent`을 사용 하 고 부모의 `ParentYear` 매개 변수를 자식 구성 요소의 `Year` 매개 변수에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-248">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="95319-249">@No__t-0을 로드 하면 다음과 같은 태그가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-249">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="95319-250">@No__t-1의 단추를 선택 하 여 `ParentYear` 속성 값이 변경 되 면-3 @no__t의 `Year` 속성이 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-250">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="95319-251">@No__t-0의 새 값은 `ParentComponent`이 주문형 인 경우 UI에서 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-251">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="95319-252">@No__t-0 매개 변수는 `Year` 매개 변수의 형식과 일치 하는 도우미 `YearChanged` 이벤트를 포함 하기 때문에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-252">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="95319-253">규칙에 따라 `<ChildComponent @bind-Year="ParentYear" />`은 기본적으로 다음을 작성 하는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-253">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="95319-254">일반적으로 `@bind-property:event` 특성을 사용 하 여 속성을 해당 이벤트 처리기에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-254">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="95319-255">예를 들어 다음 두 가지 특성을 사용 하 여 `MyProp` 속성을 `MyEventHandler`에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-255">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="95319-256">이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="95319-256">Event handling</span></span>

<span data-ttu-id="95319-257">Razor 구성 요소는 이벤트 처리 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-257">Razor components provide event handling features.</span></span> <span data-ttu-id="95319-258">대리자 형식의 값을 사용 하 여 @no__t-@no__t @no__t 0 이라는 HTML 요소 특성의 경우 Razor 구성 요소는 특성의 값을 이벤트 처리기로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-258">For an HTML element attribute named `on{event}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="95319-259">특성 이름은 항상 [-1 {event} @no__t](xref:mvc/views/razor#onevent)형식이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-259">The attribute's name is always formatted [@on{event}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="95319-260">다음 코드는 UI에서 단추가 선택 될 때 `UpdateHeading` 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-260">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="95319-261">다음 코드는 UI에서 확인란이 변경 될 때 `CheckChanged` 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-261">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="95319-262">이벤트 처리기는 비동기 일 수도 있고 <xref:System.Threading.Tasks.Task>을 반환할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-262">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="95319-263">@No__t를 수동으로 호출할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-263">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="95319-264">예외가 발생 하면 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-264">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="95319-265">다음 예제에서는 단추가 선택 될 때 `UpdateHeading`이 비동기적으로 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-265">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a><span data-ttu-id="95319-266">이벤트 인수 형식</span><span class="sxs-lookup"><span data-stu-id="95319-266">Event argument types</span></span>

<span data-ttu-id="95319-267">일부 이벤트의 경우 이벤트 인수 형식이 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-267">For some events, event argument types are permitted.</span></span> <span data-ttu-id="95319-268">이러한 이벤트 유형 중 하나에 대 한 액세스가 필요 하지 않은 경우에는 메서드 호출에 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-268">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="95319-269">지원 되는 `EventArgs`은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-269">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="95319-270">이벤트</span><span class="sxs-lookup"><span data-stu-id="95319-270">Event</span></span> | <span data-ttu-id="95319-271">클래스</span><span class="sxs-lookup"><span data-stu-id="95319-271">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="95319-272">클립보드</span><span class="sxs-lookup"><span data-stu-id="95319-272">Clipboard</span></span>        | `ClipboardEventArgs` |
| <span data-ttu-id="95319-273">옵니다</span><span class="sxs-lookup"><span data-stu-id="95319-273">Drag</span></span>             | <span data-ttu-id="95319-274">`DragEventArgs` &ndash; `DataTransfer` 및 `DataTransferItem`은 항목 데이터를 끌어 놓은 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-274">`DragEventArgs` &ndash; `DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="95319-275">오류</span><span class="sxs-lookup"><span data-stu-id="95319-275">Error</span></span>            | `ErrorEventArgs` |
| <span data-ttu-id="95319-276">포커스</span><span class="sxs-lookup"><span data-stu-id="95319-276">Focus</span></span>            | <span data-ttu-id="95319-277">`FocusEventArgs` &ndash;은 `relatedTarget`에 대 한 지원을 포함 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-277">`FocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="95319-278">`<input>` 변경</span><span class="sxs-lookup"><span data-stu-id="95319-278">`<input>` change</span></span> | `ChangeEventArgs` |
| <span data-ttu-id="95319-279">키보드</span><span class="sxs-lookup"><span data-stu-id="95319-279">Keyboard</span></span>         | `KeyboardEventArgs` |
| <span data-ttu-id="95319-280">마우스</span><span class="sxs-lookup"><span data-stu-id="95319-280">Mouse</span></span>            | `MouseEventArgs` |
| <span data-ttu-id="95319-281">마우스 포인터</span><span class="sxs-lookup"><span data-stu-id="95319-281">Mouse pointer</span></span>    | `PointerEventArgs` |
| <span data-ttu-id="95319-282">마우스 휠</span><span class="sxs-lookup"><span data-stu-id="95319-282">Mouse wheel</span></span>      | `WheelEventArgs` |
| <span data-ttu-id="95319-283">진행률</span><span class="sxs-lookup"><span data-stu-id="95319-283">Progress</span></span>         | `ProgressEventArgs` |
| <span data-ttu-id="95319-284">터치</span><span class="sxs-lookup"><span data-stu-id="95319-284">Touch</span></span>            | <span data-ttu-id="95319-285">`TouchEventArgs` &ndash; `TouchPoint`는 터치를 구분 하는 장치에서 단일 접촉 지점을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="95319-285">`TouchEventArgs` &ndash; `TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="95319-286">위의 표에서 이벤트의 속성 및 이벤트 처리 동작에 대 한 자세한 내용은 [참조 소스의 EventArgs 클래스 (aspnet/AspNetCore release/3.0 분기)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="95319-286">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="95319-287">람다 식</span><span class="sxs-lookup"><span data-stu-id="95319-287">Lambda expressions</span></span>

<span data-ttu-id="95319-288">람다 식을 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-288">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="95319-289">요소 집합을 반복 하는 경우와 같이 추가 값을 닫는 것이 편리한 경우가 종종 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-289">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="95319-290">다음 예제에서는 두 개의 단추를 만듭니다. 각 단추는 이벤트 인수를 전달 하는 `UpdateHeading` (`MouseEventArgs`)과 해당 단추 번호 (`buttonNumber`)를 UI에서 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-290">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="95319-291">람다 식에서 직접 `for` 루프의 루프 변수 (`i`)를 사용 **하지** 마십시오.</span><span class="sxs-lookup"><span data-stu-id="95319-291">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="95319-292">그렇지 않으면 모든 람다 식에서 같은 변수를 사용 하 여 `i`의 값을 모든 람다에서 동일 하 게 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-292">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="95319-293">항상 지역 변수 (이전 예제의 `buttonNumber`)에서 해당 값을 캡처한 다음 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-293">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="95319-294">EventCallback</span><span class="sxs-lookup"><span data-stu-id="95319-294">EventCallback</span></span>

<span data-ttu-id="95319-295">중첩 된 구성 요소를 사용 하는 일반적인 시나리오는 자식 구성 요소 이벤트가 발생 하는 경우 부모 구성 요소의 메서드를 실행 하려는 것입니다. 예를 들어 자식 구성 요소 이벤트가 발생 하는 경우에는 `onclick` 이벤트가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-295">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="95319-296">구성 요소 간에 이벤트를 노출 하려면 `EventCallback`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-296">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="95319-297">부모 구성 요소는 자식 구성 요소의 `EventCallback`에 콜백 메서드를 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-297">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="95319-298">샘플 앱의 `ChildComponent`은 샘플의 `ParentComponent`에서 `EventCallback` 대리자를 수신 하도록 단추의 @no__t 1 처리기를 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="95319-298">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="95319-299">@No__t-0은 `MouseEventArgs`로 입력 됩니다 .이는 주변 장치의 `onclick` 이벤트에 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-299">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="95319-300">@No__t-0은 자식의 `EventCallback<T>`을 `ShowMessage` 메서드로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-300">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="95319-301">@No__t에서 단추가 선택 된 경우-0:</span><span class="sxs-lookup"><span data-stu-id="95319-301">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="95319-302">@No__t-0의 `ShowMessage` 메서드가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-302">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="95319-303">`messageText`이 업데이트 되어 `ParentComponent`에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-303">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="95319-304">@No__t-0에 대 한 호출은 콜백 메서드에 필요 하지 않습니다 (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="95319-304">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="95319-305">자식 이벤트가 자식 내에서 실행 되는 이벤트 처리기의 rerendering 구성 요소를 트리거하는 것 처럼 `StateHasChanged`은 자동 @no__t으로 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-305">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="95319-306">`EventCallback` 및 `EventCallback<T>`은 비동기 대리자를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-306">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="95319-307">`EventCallback<T>`은 강력한 형식이 며 특정 인수 형식이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-307">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="95319-308">`EventCallback`은 약하게 형식화 되며 모든 인수 형식을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-308">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="95319-309">@No__t-2를 사용 하 여 `EventCallback` 또는 `EventCallback<T>`을 호출 하 고 <xref:System.Threading.Tasks.Task>을 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="95319-309">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="95319-310">이벤트 처리 및 바인딩 구성 요소 매개 변수를 사용 하려면 `EventCallback` 및 `EventCallback<T>`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-310">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="95319-311">@No__t-1 보다 강력한 형식의 `EventCallback<T>`을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-311">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="95319-312">`EventCallback<T>`은 구성 요소의 사용자에 게 더 나은 오류 피드백을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-312">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="95319-313">다른 UI 이벤트 처리기와 마찬가지로 이벤트 매개 변수를 지정 하는 것은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-313">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="95319-314">콜백에 전달 된 값이 없는 경우 `EventCallback`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-314">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="chained-bind"></a><span data-ttu-id="95319-315">연결 된 바인딩</span><span class="sxs-lookup"><span data-stu-id="95319-315">Chained bind</span></span>

<span data-ttu-id="95319-316">일반적인 시나리오는 데이터 바인딩된 매개 변수를 구성 요소 출력의 페이지 요소에 연결 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-316">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="95319-317">이 시나리오를 여러 수준의 바인딩이 동시에 발생 하기 때문에 연결 된 *바인딩* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-317">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="95319-318">페이지의 요소에 `@bind` 구문을 사용 하 여 연결 된 바인딩을 구현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-318">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="95319-319">이벤트 처리기 및 값은 별도로 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-319">The event handler and value must be specified separately.</span></span> <span data-ttu-id="95319-320">그러나 부모 구성 요소는 구성 요소의 매개 변수와 함께 `@bind` 구문을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-320">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="95319-321">다음 `PasswordField` 구성 요소 (*Passwordfield. razor*):</span><span class="sxs-lookup"><span data-stu-id="95319-321">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="95319-322">@No__t-0 요소의 값을 `Password` 속성으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-322">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="95319-323">[Eventcallback](#eventcallback)을 사용 하 여 `Password` 속성의 변경 내용을 부모 구성 요소에 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-323">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="95319-324">@No__t-0 구성 요소는 다른 구성 요소에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-324">The `PasswordField` component is used in another component:</span></span>

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="95319-325">위의 예에서 암호에 대 한 검사 또는 트랩 오류를 수행 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-325">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="95319-326">@No__t-0에 대 한 지원 필드를 만듭니다 (다음 예제 코드에서는-1 @no__t).</span><span class="sxs-lookup"><span data-stu-id="95319-326">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="95319-327">@No__t-0 setter에서 검사 또는 트랩 오류를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-327">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="95319-328">다음 예에서는 암호 값에 공백을 사용 하는 경우 사용자에 게 즉각적인 피드백을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-328">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="capture-references-to-components"></a><span data-ttu-id="95319-329">구성 요소에 대 한 참조 캡처</span><span class="sxs-lookup"><span data-stu-id="95319-329">Capture references to components</span></span>

<span data-ttu-id="95319-330">구성 요소 참조는 `Show` 또는 `Reset`과 같은 해당 인스턴스에 대해 명령을 실행할 수 있도록 구성 요소 인스턴스를 참조 하는 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-330">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="95319-331">구성 요소 참조를 캡처하려면:</span><span class="sxs-lookup"><span data-stu-id="95319-331">To capture a component reference:</span></span>

* <span data-ttu-id="95319-332">자식 구성 요소에 [@ref](xref:mvc/views/razor#ref) 특성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-332">Add an [@ref](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="95319-333">자식 구성 요소와 동일한 유형으로 필드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-333">Define a field with the same type as the child component.</span></span>

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="95319-334">구성 요소가 렌더링 되 면 `loginDialog` 필드가 `MyLoginDialog` 자식 구성 요소 인스턴스로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="95319-334">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="95319-335">그런 다음 구성 요소 인스턴스에서 .NET 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-335">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="95319-336">@No__t-0 변수는 구성 요소가 렌더링 된 후에만 채워지고 출력에는 `MyLoginDialog` 요소가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-336">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="95319-337">이 시점까지 참조할 항목이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-337">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="95319-338">구성 요소에서 렌더링을 완료 한 후에 구성 요소 참조를 조작 하려면 `OnAfterRenderAsync` 또는 `OnAfterRender` 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-338">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="95319-339">구성 요소 참조를 캡처하는 것은 [요소 참조를 캡처하](xref:blazor/javascript-interop#capture-references-to-elements)는 데 유사한 구문을 사용 하지만 [JavaScript interop](xref:blazor/javascript-interop) 기능은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="95319-339">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="95319-340">구성 요소 참조는 JavaScript 코드 @ no__t-0they're에 전달 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-340">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="95319-341">구성 요소 참조를 사용 하 여 자식 구성 요소의 상태를 변경할 수 **없습니다** .</span><span class="sxs-lookup"><span data-stu-id="95319-341">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="95319-342">대신, 일반 선언적 매개 변수를 사용 하 여 자식 구성 요소에 데이터를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-342">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="95319-343">일반적인 선언적 매개 변수를 사용 하면 자식 구성 요소가 자동으로 올바른 시간에 rerender 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-343">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="95319-344">외부에서 구성 요소 메서드를 호출 하 여 상태 업데이트</span><span class="sxs-lookup"><span data-stu-id="95319-344">Invoke component methods externally to update state</span></span>

<span data-ttu-id="95319-345">Blazor는 `SynchronizationContext`을 사용 하 여 단일 논리적 실행 스레드를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-345">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="95319-346">Blazor에 의해 발생 되는 구성 요소의 수명 주기 메서드 및 이벤트 콜백은이 `SynchronizationContext`에 대해 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-346">A component's lifecycle methods and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="95319-347">외부 이벤트 (예: 타이머 또는 다른 알림)를 기반으로 구성 요소를 업데이트 해야 하는 경우 Blazor의 `SynchronizationContext`로 디스패치할 `InvokeAsync` 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-347">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="95319-348">예를 들어 업데이트 된 상태의 수신 구성 요소를 알릴 수 있는 알림 *서비스* 를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-348">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="95319-349">@No__t-0을 사용 하 여 구성 요소를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-349">Usage of the `NotifierService` to update a component:</span></span>

```cshtml
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="95319-350">위의 예제에서 `NotifierService`은 Blazor의 `SynchronizationContext` 외부에서 구성 요소의 `OnNotify` 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-350">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="95319-351">`InvokeAsync`은 올바른 컨텍스트로 전환 하 고 렌더링을 큐에 대기 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-351">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="95319-352">@No__t-0key를 사용 하 여 요소 및 구성 요소 유지 관리</span><span class="sxs-lookup"><span data-stu-id="95319-352">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="95319-353">요소 또는 구성 요소 목록을 렌더링할 때 이후에 요소나 구성 요소가 변경 되는 경우 Blazor의 diff 알고리즘은 유지할 수 있는 이전 요소 또는 구성 요소와 모델 개체가 이러한 요소에 매핑되는 방법을 결정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-353">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="95319-354">일반적으로이 프로세스는 자동 이며 무시 해도 되지만 프로세스를 제어 하는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-354">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="95319-355">다음 예를 살펴 보십시오.</span><span class="sxs-lookup"><span data-stu-id="95319-355">Consider the following example:</span></span>

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

<span data-ttu-id="95319-356">@No__t-0 컬렉션의 콘텐츠는 삽입, 삭제 또는 다시 정렬 된 항목으로 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-356">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="95319-357">구성 요소가 렌더링 하면 `<DetailsEditor>` 구성 요소가 서로 다른 `Details` 매개 변수 값을 받도록 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-357">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="95319-358">이로 인해 예상 보다 더 복잡 한 rerendering 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-358">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="95319-359">경우에 따라 rerendering는 손실 요소 포커스와 같은 표시 되는 동작 차이를 일으킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-359">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="95319-360">매핑 프로세스는 `@key` 지시문 특성을 사용 하 여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-360">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="95319-361">`@key`은 키의 값에 따라 요소 또는 구성 요소를 보존 하도록 diff 알고리즘을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-361">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="95319-362">@No__t-0 컬렉션이 변경 되 면 diff 알고리즘은 @no__t 1 인스턴스와 `person` 인스턴스 간의 연결을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-362">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="95319-363">@No__t-0이 `People` 목록에서 삭제 되 면 해당 `<DetailsEditor>` 인스턴스만 UI에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-363">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="95319-364">다른 인스턴스는 변경 되지 않은 상태로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-364">Other instances are left unchanged.</span></span>
* <span data-ttu-id="95319-365">@No__t-0이 목록에서 특정 위치에 삽입 되는 경우 한 개의 새 `<DetailsEditor>` 인스턴스가 해당 위치에 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-365">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="95319-366">다른 인스턴스는 변경 되지 않은 상태로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-366">Other instances are left unchanged.</span></span>
* <span data-ttu-id="95319-367">@No__t-0 항목이 다시 정렬 되 면 해당 하는 @no__t 1 인스턴스가 유지 되 고 UI에서 다시 정렬 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-367">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="95319-368">일부 시나리오에서는 `@key`을 사용 하면 rerendering의 복잡성을 최소화 하 고 DOM의 상태 저장 부분 (예: 포커스 위치)에 대 한 잠재적인 문제를 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-368">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="95319-369">키는 각 컨테이너 요소 또는 구성 요소에 대해 로컬입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-369">Keys are local to each container element or component.</span></span> <span data-ttu-id="95319-370">문서 전체에서 키를 전역적으로 비교 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-370">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="95319-371">@No__t-0key를 사용 하는 경우</span><span class="sxs-lookup"><span data-stu-id="95319-371">When to use \@key</span></span>

<span data-ttu-id="95319-372">일반적으로 목록이 렌더링 될 때마다 (예: `@foreach` 블록) `@key`을 사용 하 고 `@key`를 정의 하는 데 적합 한 값이 있는지를 이해 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-372">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="95319-373">Blazor를 @no__t 사용 하 여 개체가 변경 될 때 요소가 요소 또는 구성 요소 하위 트리를 유지 하지 않도록 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-373">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="95319-374">@No__t 0으로 변경 되는 경우 `@key` 특성 지시문은 Blazor는 전체 `<div>` 및 해당 하위 항목을 삭제 하 고 새 요소와 구성 요소를 사용 하 여 UI 내에서 하위 트리를 다시 작성 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-374">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="95319-375">@No__t-0이 변경 될 때 UI 상태가 유지 되지 않도록 해야 하는 경우이 방법이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-375">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="95319-376">@No__t-0key를 사용 하지 않는 경우</span><span class="sxs-lookup"><span data-stu-id="95319-376">When not to use \@key</span></span>

<span data-ttu-id="95319-377">@No__t-0으로 diff 때 성능 비용이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-377">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="95319-378">성능 비용은 크지 않지만 요소를 제어 하거나 구성 요소 유지 규칙을 제어 하는 경우에만 `@key`을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-378">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="95319-379">@No__t-0이 사용 되지 않더라도 Blazor는 자식 요소와 구성 요소 인스턴스를 최대한 많이 보존 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-379">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="95319-380">@No__t-0을 사용 하는 유일한 장점은 매핑을 선택 하는 diff 알고리즘이 아닌 유지 된 구성 요소 인스턴스에 모델 인스턴스가 매핑되 *는 방식을* 제어 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-380">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="95319-381">@No__t-0key에 사용할 값</span><span class="sxs-lookup"><span data-stu-id="95319-381">What values to use for \@key</span></span>

<span data-ttu-id="95319-382">일반적으로 `@key`에 대해 다음과 같은 종류의 값 중 하나를 제공 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-382">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="95319-383">모델 개체 인스턴스 (예: 이전 예제와 같이 `Person` 인스턴스).</span><span class="sxs-lookup"><span data-stu-id="95319-383">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="95319-384">이렇게 하면 개체 참조 일치를 기반으로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-384">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="95319-385">고유 식별자 (예: `int` 형식의 기본 키 값, `string` 또는 `Guid`).</span><span class="sxs-lookup"><span data-stu-id="95319-385">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="95319-386">@No__t-0에 사용 되는 값이 충돌 하지 않는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-386">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="95319-387">동일한 부모 요소 내에서 충돌 방지 값이 검색 되는 경우 Blazor는 기존 요소나 구성 요소를 새 요소나 구성 요소에 명확 하 게 매핑할 수 없기 때문에 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-387">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="95319-388">개체 인스턴스 또는 기본 키 값과 같은 고유 값만 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-388">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="95319-389">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="95319-389">Lifecycle methods</span></span>

<span data-ttu-id="95319-390">`OnInitializedAsync` 및 `OnInitialized` 코드를 실행 하 여 구성 요소를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-390">`OnInitializedAsync` and `OnInitialized` execute code to initialize the component.</span></span> <span data-ttu-id="95319-391">비동기 작업을 수행 하려면 작업에서 `OnInitializedAsync` 및 `await` 키워드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-391">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="95319-392">동기 작업의 경우 `OnInitialized`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-392">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="95319-393">`OnParametersSetAsync` 및 `OnParametersSet`은 구성 요소가 부모 로부터 매개 변수를 수신 하 고 값이 속성에 할당 될 때 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-393">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="95319-394">이러한 메서드는 구성 요소 초기화 후와 구성 요소가 렌더링 될 때마다 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-394">These methods are executed after component initialization and each time the component is rendered:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="95319-395">`OnAfterRenderAsync` 및 `OnAfterRender`은 구성 요소가 렌더링을 완료 한 후에 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-395">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="95319-396">요소 및 구성 요소 참조가이 시점에 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="95319-396">Element and component references are populated at this point.</span></span> <span data-ttu-id="95319-397">렌더링 된 DOM 요소에 대해 작동 하는 타사 JavaScript 라이브러리 활성화와 같이 렌더링 된 콘텐츠를 사용 하 여 추가 초기화 단계를 수행 하려면이 단계를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-397">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="95319-398">*서버에서 사전 렌더링을 할 때 `OnAfterRender`이 호출 되지 않습니다.*</span><span class="sxs-lookup"><span data-stu-id="95319-398">`OnAfterRender` *is not called when prerendering on the server.*</span></span>

<span data-ttu-id="95319-399">@No__t-1 및 `OnAfterRender`에 대 한 `firstRender` 매개 변수는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-399">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender` is:</span></span>

* <span data-ttu-id="95319-400">구성 요소 인스턴스를 처음 호출할 때 `true`으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-400">Set to `true` the first time that the component instance is invoked.</span></span>
* <span data-ttu-id="95319-401">초기화 작업이 한 번만 수행 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-401">Ensures that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="95319-402">렌더링 시 불완전 한 비동기 작업 처리</span><span class="sxs-lookup"><span data-stu-id="95319-402">Handle incomplete async actions at render</span></span>

<span data-ttu-id="95319-403">수명 주기 이벤트에서 수행 되는 비동기 작업은 구성 요소를 렌더링 하기 전에 완료 되지 않았을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-403">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="95319-404">수명 주기 메서드를 실행 하는 동안 개체의 @no__t 0 이거나 데이터로 불완전 하 게 채워질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-404">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="95319-405">개체가 초기화 되었는지 확인 하는 렌더링 논리를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-405">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="95319-406">개체를 @no__t 하는 동안 자리 표시자 UI 요소를 렌더링 합니다 (예: 로드 메시지).</span><span class="sxs-lookup"><span data-stu-id="95319-406">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="95319-407">Blazor 템플릿의 `FetchData` 구성 요소에서 `OnInitializedAsync`은 비동기적 수신 예측 데이터 (`forecasts`)로 재정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-407">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="95319-408">@No__t-0 `null` 이면 사용자에 게 로드 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-408">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="95319-409">@No__t-1에서 반환 된 @no__t 0이 완료 되 면 구성 요소는 업데이트 된 상태를 사용 하 여 다시 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-409">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="95319-410">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="95319-410">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="95319-411">매개 변수를 설정 하기 전에 코드를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-411">Execute code before parameters are set</span></span>

<span data-ttu-id="95319-412">매개 변수를 설정 하기 전에 코드를 실행 하기 위해 `SetParameters`을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-412">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="95319-413">@No__t-0이 호출 되지 않은 경우 사용자 지정 코드는 필요한 방식으로 들어오는 매개 변수 값을 해석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-413">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="95319-414">예를 들어 클래스의 속성에는 들어오는 매개 변수를 할당할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-414">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="95319-415">UI 새로 고침 안 함</span><span class="sxs-lookup"><span data-stu-id="95319-415">Suppress refreshing of the UI</span></span>

<span data-ttu-id="95319-416">`ShouldRender`은 UI를 새로 고치지 않도록 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-416">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="95319-417">구현에서 `true`을 반환 하면 UI가 새로 고쳐집니다.</span><span class="sxs-lookup"><span data-stu-id="95319-417">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="95319-418">@No__t-0이 재정의 된 경우에도 구성 요소가 항상 처음에 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-418">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="95319-419">IDisposable을 사용한 구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="95319-419">Component disposal with IDisposable</span></span>

<span data-ttu-id="95319-420">구성 요소가 @no__t를 구현 하는 경우 구성 요소가 UI에서 제거 되 면 [Dispose 메서드가](/dotnet/standard/garbage-collection/implementing-dispose) 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-420">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="95319-421">다음 구성 요소는 `@implements IDisposable` 및 `Dispose` 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-421">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a><span data-ttu-id="95319-422">라우팅</span><span class="sxs-lookup"><span data-stu-id="95319-422">Routing</span></span>

<span data-ttu-id="95319-423">Blazor의 라우팅은 앱에서 액세스 가능한 각 구성 요소에 경로 템플릿을 제공 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-423">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="95319-424">@No__t-0 지시문이 있는 Razor 파일이 컴파일되면 생성 된 클래스에 경로 템플릿을 지정 하는 @no__t 1이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-424">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="95319-425">런타임에 라우터는 `RouteAttribute`으로 구성 요소 클래스를 검색 하 고 요청 된 URL과 일치 하는 경로 템플릿을 포함 하는 구성 요소를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-425">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="95319-426">여러 경로 템플릿을 구성 요소에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-426">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="95319-427">다음 구성 요소는 `/BlazorRoute` 및 `/DifferentBlazorRoute`에 대 한 요청에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-427">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="95319-428">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="95319-428">Route parameters</span></span>

<span data-ttu-id="95319-429">구성 요소는 `@page` 지시문에 제공 된 경로 템플릿에서 경로 매개 변수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-429">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="95319-430">라우터는 경로 매개 변수를 사용 하 여 해당 하는 구성 요소 매개 변수를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="95319-430">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="95319-431">*경로 매개 변수 구성 요소*:</span><span class="sxs-lookup"><span data-stu-id="95319-431">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="95319-432">선택적 매개 변수는 지원 되지 않으므로 위의 예제에서 두 개의 `@page` 지시문이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-432">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="95319-433">첫 번째는 매개 변수 없이 구성 요소에 대 한 탐색을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-433">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="95319-434">두 번째 `@page` 지시어는 `{text}` 경로 매개 변수를 사용 하 고 값을 `Text` 속성에 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-434">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="95319-435">"코드 숨김으로" 환경에 대 한 기본 클래스 상속</span><span class="sxs-lookup"><span data-stu-id="95319-435">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="95319-436">구성 요소 파일은 동일한 파일 C# 의 HTML 태그와 처리 코드를 혼합 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-436">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="95319-437">@No__t-0 지시어를 사용 하 여 구성 요소 태그를 처리 코드에서 분리 하는 "코드 숨김이" 환경으로 Blazor apps를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-437">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="95319-438">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) 은 구성 요소가 기본 @no__t 클래스를 상속 하는 방법을 보여 줍니다.-1은 구성 요소의 속성과 메서드를 제공 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-438">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="95319-439">*Pages/BlazorRocks*:</span><span class="sxs-lookup"><span data-stu-id="95319-439">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="95319-440">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="95319-440">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="95319-441">기본 클래스는 `ComponentBase`에서 파생 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-441">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="95319-442">구성 요소 가져오기</span><span class="sxs-lookup"><span data-stu-id="95319-442">Import components</span></span>

<span data-ttu-id="95319-443">Razor로 작성 된 구성 요소의 네임 스페이스는 (우선 순위)를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-443">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="95319-444">Razor 파일 (*razor*) 태그에 [@namespace](xref:mvc/views/razor#namespace) 지정 (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="95319-444">[@namespace](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="95319-445">프로젝트 파일 (`<RootNamespace>BlazorSample</RootNamespace>`)에서 프로젝트의 `RootNamespace`입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-445">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="95319-446">프로젝트 파일의 파일 이름 ( *.csproj*)에서 가져온 프로젝트 이름 및 프로젝트 루트에서 구성 요소로의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-446">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="95319-447">예를 들어 프레임 워크는 *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample*)를 네임 스페이스 `BlazorSample.Pages`로 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-447">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="95319-448">구성 요소 C# 는 이름 바인딩 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="95319-448">Components follow C# name binding rules.</span></span> <span data-ttu-id="95319-449">이 예제에서 `Index` 구성 요소의 경우 범위의 구성 요소는 모든 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-449">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="95319-450">같은 폴더 *에 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="95319-450">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="95319-451">프로젝트 루트에서 다른 네임 스페이스를 명시적으로 지정 하지 않은 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-451">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="95319-452">다른 네임 스페이스에 정의 된 구성 요소는 Razor의 [@using](xref:mvc/views/razor#using) 지시어를 사용 하 여 범위로 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="95319-452">Components defined in a different namespace are brought into scope using Razor's [@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="95319-453">*BlazorSample/Shared/* folder에 다른 구성 요소 (`NavMenu.razor`)가 있는 경우 다음 `@using` 문을 사용 하 여 `Index.razor`에서 구성 요소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-453">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="95319-454">[@No__t-1](xref:mvc/views/razor#using) 지시문이 필요 하지 않은 정규화 된 이름을 사용 하 여 구성 요소를 참조할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-454">Components can also be referenced using their fully qualified names, which doesn't require the [@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="95319-455">@No__t 0 한정자는 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-455">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="95319-456">별칭이 지정 된 `using` 문 (예: `@using Foo = Bar`)이 있는 구성 요소 가져오기는 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-456">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="95319-457">부분적으로 정규화 된 이름은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-457">Partially qualified names aren't supported.</span></span> <span data-ttu-id="95319-458">예를 들어 `@using BlazorSample`을 추가 하 고 `<Shared.NavMenu></Shared.NavMenu>`와 `NavMenu.razor`을 참조 하는 것은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-458">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="95319-459">조건부 HTML 요소 특성</span><span class="sxs-lookup"><span data-stu-id="95319-459">Conditional HTML element attributes</span></span>

<span data-ttu-id="95319-460">HTML 요소 특성은 .NET 값에 따라 조건부로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-460">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="95319-461">값이-0 또는 `null` @no__t 이면 특성이 렌더링 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-461">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="95319-462">값이-0 @no__t 이면 특성은 최소화 된 상태로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-462">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="95319-463">다음 예제에서 `IsCompleted`은 `checked`이 요소의 태그에서 렌더링 되는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-463">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="95319-464">@No__t-0 `true` 이면 확인란은 다음과 같이 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-464">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="95319-465">@No__t-0 `false` 이면 확인란은 다음과 같이 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-465">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="95319-466">자세한 내용은 <xref:mvc/views/razor>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="95319-466">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="95319-467">ASP.NET 형식이 `bool` 인 경우에는 [aria를 누르는](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)등의 일부 HTML 특성이 제대로 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-467">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="95319-468">이러한 경우에는-1 @no__t 대신 `string` 유형을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-468">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="95319-469">원시 HTML</span><span class="sxs-lookup"><span data-stu-id="95319-469">Raw HTML</span></span>

<span data-ttu-id="95319-470">일반적으로 문자열은 DOM 텍스트 노드를 사용 하 여 렌더링 됩니다. 즉, 포함 될 수 있는 모든 태그는 무시 되 고 리터럴 텍스트로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-470">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="95319-471">원시 HTML을 렌더링 하려면 `MarkupString` 값으로 HTML 콘텐츠를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-471">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="95319-472">값은 HTML 또는 SVG로 구문 분석 되 고 DOM에 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-472">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="95319-473">신뢰할 수 없는 소스에서 생성 된 원시 HTML을 렌더링 하면 **보안상 위험할** 수 있으므로 피해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-473">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="95319-474">다음 예제에서는 `MarkupString` 형식을 사용 하 여 정적 HTML 콘텐츠 블록을 구성 요소의 렌더링 된 출력에 추가 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="95319-474">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="95319-475">템플릿 기반 구성 요소</span><span class="sxs-lookup"><span data-stu-id="95319-475">Templated components</span></span>

<span data-ttu-id="95319-476">템플릿 기반 구성 요소는 하나 이상의 UI 템플릿을 매개 변수로 허용 하는 구성 요소 이며,이 구성 요소는 구성 요소의 렌더링 논리의 일부로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-476">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="95319-477">템플릿 기반 구성 요소를 사용 하면 일반 구성 요소 보다 다시 사용할 수 있는 상위 수준 구성 요소를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-477">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="95319-478">몇 가지 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-478">A couple of examples include:</span></span>

* <span data-ttu-id="95319-479">사용자가 테이블의 머리글, 행 및 바닥글에 대 한 템플릿을 지정 하는 데 사용할 수 있는 테이블 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-479">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="95319-480">사용자가 목록에서 항목을 렌더링 하기 위한 템플릿을 지정할 수 있도록 하는 목록 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-480">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="95319-481">템플릿 매개 변수</span><span class="sxs-lookup"><span data-stu-id="95319-481">Template parameters</span></span>

<span data-ttu-id="95319-482">템플릿 기반 구성 요소는 `RenderFragment` 또는 `RenderFragment<T>` 형식의 구성 요소 매개 변수를 하나 이상 지정 하 여 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-482">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="95319-483">렌더링 조각은 렌더링할 UI 세그먼트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="95319-483">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="95319-484">`RenderFragment<T>`은 렌더링 조각이 호출 될 때 지정할 수 있는 형식 매개 변수를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-484">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="95319-485">`TableTemplate` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="95319-485">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="95319-486">템플릿 기반 구성 요소를 사용 하는 경우 매개 변수 이름과 일치 하는 자식 요소를 사용 하 여 템플릿 매개 변수를 지정할 수 있습니다 (다음 예제에서는 `TableHeader` 및 `RowTemplate`).</span><span class="sxs-lookup"><span data-stu-id="95319-486">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a><span data-ttu-id="95319-487">템플릿 컨텍스트 매개 변수</span><span class="sxs-lookup"><span data-stu-id="95319-487">Template context parameters</span></span>

<span data-ttu-id="95319-488">요소로 전달 된 `RenderFragment<T>` 형식의 구성 요소 인수에는 `context` (예: 이전 코드 샘플, `@context.PetId`) 이라는 암시적 매개 변수가 있지만 자식 요소에서 `Context` 특성을 사용 하 여 매개 변수 이름을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-488">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="95319-489">다음 예제에서 `RowTemplate` 요소의 `Context` 특성은 `pet` 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-489">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="95319-490">또는 구성 요소 요소에 `Context` 특성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-490">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="95319-491">지정 된 `Context` 특성은 모든 지정 된 템플릿 매개 변수에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-491">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="95319-492">이는 자식 요소를 래핑하는 대신 암시적 자식 콘텐츠에 대 한 콘텐츠 매개 변수 이름을 지정 하려는 경우에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-492">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="95319-493">다음 예제에서는 `Context` 특성이 `TableTemplate` 요소에 나타나고 모든 템플릿 매개 변수에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-493">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a><span data-ttu-id="95319-494">제네릭 형식의 구성 요소</span><span class="sxs-lookup"><span data-stu-id="95319-494">Generic-typed components</span></span>

<span data-ttu-id="95319-495">일반적으로 템플릿 구성 요소는 형식화 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-495">Templated components are often generically typed.</span></span> <span data-ttu-id="95319-496">예를 들어, 일반 `ListViewTemplate` 구성 요소를 사용 하 여 1 @no__t 값을 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-496">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="95319-497">제네릭 구성 요소를 정의 하려면 [@typeparam](xref:mvc/views/razor#typeparam) 지시어를 사용 하 여 형식 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-497">To define a generic component, use the [@typeparam](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="95319-498">제네릭 형식의 구성 요소를 사용 하는 경우 가능 하면 형식 매개 변수가 유추 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-498">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="95319-499">그렇지 않은 경우 형식 매개 변수의 이름과 일치 하는 특성을 사용 하 여 형식 매개 변수를 명시적으로 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-499">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="95319-500">다음 예제에서 `TItem="Pet"`은 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-500">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="95319-501">연계 값 및 매개 변수</span><span class="sxs-lookup"><span data-stu-id="95319-501">Cascading values and parameters</span></span>

<span data-ttu-id="95319-502">일부 시나리오에서는 [구성 요소 매개 변수](#component-parameters)를 사용 하 여 상위 구성 요소에서 하위 구성 요소로 데이터를 이동 하는 것이 불편 합니다. 특히 구성 요소 계층이 여러 개 있는 경우에는 더욱 그렇습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-502">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="95319-503">연계 값 및 매개 변수는 상위 구성 요소에서 모든 하위 구성 요소에 값을 제공 하는 편리한 방법을 제공 하 여이 문제를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-503">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="95319-504">연계 값 및 매개 변수를 사용 하 여 구성 요소를 조정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-504">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="95319-505">테마 예</span><span class="sxs-lookup"><span data-stu-id="95319-505">Theme example</span></span>

<span data-ttu-id="95319-506">샘플 앱의 다음 예제에서 `ThemeInfo` 클래스는 응용 프로그램의 지정 된 부분에 있는 모든 단추가 동일한 스타일을 공유할 수 있도록 구성 요소 계층 구조의 아래로 이동 하는 테마 정보를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-506">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="95319-507">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="95319-507">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="95319-508">상위 구성 요소는 연계 값 구성 요소를 사용 하 여 연계 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-508">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="95319-509">@No__t-0 구성 요소는 구성 요소 계층의 하위 트리를 래핑하고 해당 하위 트리 내의 모든 구성 요소에 단일 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-509">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="95319-510">예를 들어 샘플 앱은 `@Body` 속성의 레이아웃 본문을 구성 하는 모든 구성 요소에 대 한 연계 매개 변수로 앱 레이아웃 중 하나에서 테마 정보 (`ThemeInfo`)를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-510">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="95319-511">`ButtonClass`은 레이아웃 구성 요소에서 `btn-success` 값이 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-511">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="95319-512">모든 하위 구성 요소는 `ThemeInfo` 연계 개체를 통해이 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-512">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="95319-513">`CascadingValuesParametersLayout` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="95319-513">`CascadingValuesParametersLayout` component:</span></span>

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="95319-514">연계 값을 사용 하기 위해 구성 요소는 `[CascadingParameter]` 특성을 사용 하 여 연계 매개 변수를 선언 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-514">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="95319-515">연계 값은 유형별 매개 변수에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-515">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="95319-516">샘플 앱에서 `CascadingValuesParametersTheme` 구성 요소는 `ThemeInfo` 연계 값을 연계 매개 변수에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-516">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="95319-517">매개 변수는 구성 요소에 의해 표시 되는 단추 중 하나에 대해 CSS 클래스를 설정 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-517">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="95319-518">`CascadingValuesParametersTheme` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="95319-518">`CascadingValuesParametersTheme` component:</span></span>

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

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
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="95319-519">동일한 하위 트리 내에서 동일한 형식의 여러 값을 계단식으로 배열 하려면 각 `CascadingValue` 구성 요소와 해당 `CascadingParameter`에 고유한 `Name` 문자열을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-519">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="95319-520">다음 예에서는 두 개의 `CascadingValue` 구성 요소가 @no__t 이름으로-1의 서로 다른 인스턴스를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-520">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```cshtml
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="95319-521">하위 구성 요소에서 종속 매개 변수는 상위 구성 요소의 해당 하는 종속 된 값에서 이름으로 해당 값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-521">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="95319-522">TabSet 예제</span><span class="sxs-lookup"><span data-stu-id="95319-522">TabSet example</span></span>

<span data-ttu-id="95319-523">연계 매개 변수를 사용 하면 구성 요소 계층 구조 전체에서 공동 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-523">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="95319-524">예를 들어 샘플 앱에서 다음 *Tabset* 예제를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="95319-524">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="95319-525">샘플 앱에는 탭에서 구현 하는 @no__t 0 인터페이스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-525">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="95319-526">@No__t-0 구성 요소는 `TabSet` @no__t 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-526">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="95319-527">자식 `Tab` 구성 요소는 `TabSet`에 매개 변수로 명시적으로 전달 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-527">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="95319-528">대신 자식 `Tab` 구성 요소는 `TabSet`의 자식 콘텐츠에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-528">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="95319-529">그러나 `TabSet`은 헤더와 활성 탭을 렌더링할 수 있도록 각 `Tab` 구성 요소에 대해 알고 있어야 합니다. 추가 코드를 요구 하지 않고이 조정을 사용 하도록 설정 하기 위해 `TabSet` 구성 요소는 *자체를 연계 값으로 제공* 하 여 하위 `Tab` 구성 요소에서 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-529">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="95319-530">`TabSet` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="95319-530">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="95319-531">하위 `Tab` 구성 요소는 포함 하는 `TabSet`을 연계 매개 변수로 캡처하기 때문에 @no__t 2 구성 요소는 `TabSet`에 자신을 추가 하 고 활성화 된 탭을 조정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-531">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="95319-532">`Tab` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="95319-532">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="95319-533">Razor 템플릿</span><span class="sxs-lookup"><span data-stu-id="95319-533">Razor templates</span></span>

<span data-ttu-id="95319-534">렌더링 조각은 Razor 템플릿 구문을 사용 하 여 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-534">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="95319-535">Razor 템플릿은 UI 코드 조각을 정의 하 고 다음 형식을 가정 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-535">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="95319-536">다음 예제에서는 `RenderFragment` 및 `RenderFragment<T>` 값을 지정 하 고 구성 요소에서 직접 템플릿을 렌더링 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="95319-536">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="95319-537">렌더링 조각은 [템플릿 기반 구성 요소](#templated-components)에 인수로 전달 될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-537">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="95319-538">이전 코드의 렌더링 된 출력:</span><span class="sxs-lookup"><span data-stu-id="95319-538">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="95319-539">수동 RenderTreeBuilder 논리</span><span class="sxs-lookup"><span data-stu-id="95319-539">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="95319-540">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`은 코드에서 C# 구성 요소를 수동으로 빌드하는 등 구성 요소 및 요소를 조작 하는 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-540">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="95319-541">구성 요소를 만드는 데 `RenderTreeBuilder`을 사용 하는 것은 고급 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-541">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="95319-542">형식이 잘못 된 구성 요소 (예: 닫히지 않은 태그 태그)는 정의 되지 않은 동작을 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-542">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="95319-543">다른 구성 요소에 수동으로 빌드할 수 있는 다음 `PetDetails` 구성 요소를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-543">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="95319-544">다음 예제에서 `CreateComponent` 메서드의 루프는 세 개의 `PetDetails` 개의 구성 요소를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-544">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="95319-545">@No__t-0 메서드를 호출 하 여 구성 요소를 만드는 경우 (`OpenComponent` 및 `AddAttribute`) 시퀀스 번호는 소스 코드 줄 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-545">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="95319-546">Blazor 차이점 알고리즘은 고유 호출 호출이 아닌 개별 코드 줄에 해당 하는 시퀀스 번호를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-546">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="95319-547">@No__t-0 메서드를 사용 하 여 구성 요소를 만드는 경우 시퀀스 번호에 대 한 인수를 하드 코딩 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-547">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="95319-548">**계산 또는 카운터를 사용 하 여 시퀀스 번호를 생성 하면 성능이 저하 될 수 있습니다.**</span><span class="sxs-lookup"><span data-stu-id="95319-548">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="95319-549">자세한 내용은 [시퀀스 번호를 코드 줄 번호와 관련 되 고 실행 순서와는 관련이 없습니다](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) . 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="95319-549">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="95319-550">`BuiltContent` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="95319-550">`BuiltContent` component:</span></span>

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> <span data-ttu-id="95319-551">! 내용의 @No__t-0의 형식은 렌더링 작업의 *결과* 를 처리할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-551">![WARNING] The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="95319-552">Blazor framework 구현의 내부 세부 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-552">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="95319-553">이러한 형식은 *불안정* 한 것으로 간주 되며 이후 릴리스에서 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-553">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="95319-554">시퀀스 번호는 코드 줄 번호와 관련 되 고 실행 순서와는 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-554">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="95319-555">Blazor `.razor` 파일은 항상 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-555">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="95319-556">이는 컴파일 단계를 사용 하 여 런타임 시 앱 성능을 향상 시키는 정보를 삽입할 수 있으므로 `.razor`의 경우에 매우 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-556">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="95319-557">이러한 향상 된 기능을 포함 하는 주요 예로 *시퀀스 번호가*있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-557">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="95319-558">시퀀스 번호는 코드의 고유 하 고 정렬 된 줄에서 제공 되는 출력을 런타임에 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-558">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="95319-559">런타임은이 정보를 사용 하 여 선형 시간에 효율적인 트리 차이을 생성 합니다 .이는 일반적으로 일반적인 트리 diff 알고리즘에 대해 가능한 것 보다 훨씬 빠릅니다.</span><span class="sxs-lookup"><span data-stu-id="95319-559">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="95319-560">다음과 같은 간단한 `.razor` 파일을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-560">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="95319-561">위의 코드는 다음과 같은 항목으로 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-561">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="95319-562">코드가 처음으로 실행 될 때 `someFlag` @no__t이-1 이면 작성기에서 다음을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-562">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="95319-563">Sequence</span><span class="sxs-lookup"><span data-stu-id="95319-563">Sequence</span></span> | <span data-ttu-id="95319-564">type</span><span class="sxs-lookup"><span data-stu-id="95319-564">Type</span></span>      | <span data-ttu-id="95319-565">data</span><span class="sxs-lookup"><span data-stu-id="95319-565">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="95319-566">0</span><span class="sxs-lookup"><span data-stu-id="95319-566">0</span></span>        | <span data-ttu-id="95319-567">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="95319-567">Text node</span></span> | <span data-ttu-id="95319-568">첫째</span><span class="sxs-lookup"><span data-stu-id="95319-568">First</span></span>  |
| <span data-ttu-id="95319-569">1</span><span class="sxs-lookup"><span data-stu-id="95319-569">1</span></span>        | <span data-ttu-id="95319-570">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="95319-570">Text node</span></span> | <span data-ttu-id="95319-571">Second</span><span class="sxs-lookup"><span data-stu-id="95319-571">Second</span></span> |

<span data-ttu-id="95319-572">@No__t-0이-1 @no__t 되 고 태그가 다시 렌더링 된다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-572">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="95319-573">이번에는 작성기가 다음을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-573">This time, the builder receives:</span></span>

| <span data-ttu-id="95319-574">Sequence</span><span class="sxs-lookup"><span data-stu-id="95319-574">Sequence</span></span> | <span data-ttu-id="95319-575">형식</span><span class="sxs-lookup"><span data-stu-id="95319-575">Type</span></span>       | <span data-ttu-id="95319-576">data</span><span class="sxs-lookup"><span data-stu-id="95319-576">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="95319-577">1</span><span class="sxs-lookup"><span data-stu-id="95319-577">1</span></span>        | <span data-ttu-id="95319-578">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="95319-578">Text node</span></span>  | <span data-ttu-id="95319-579">Second</span><span class="sxs-lookup"><span data-stu-id="95319-579">Second</span></span> |

<span data-ttu-id="95319-580">런타임에서 diff를 수행 하는 경우 시퀀스 `0`의 항목이 제거 되었으므로 다음과 같은 trivial *편집 스크립트*를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-580">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="95319-581">첫 번째 텍스트 노드를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-581">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="95319-582">프로그래밍 방식으로 시퀀스 번호를 생성 하는 경우 문제가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-582">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="95319-583">대신 다음과 같은 렌더링 트리 작성기 논리를 작성 했다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-583">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="95319-584">이제 첫 번째 출력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-584">Now, the first output is:</span></span>

| <span data-ttu-id="95319-585">Sequence</span><span class="sxs-lookup"><span data-stu-id="95319-585">Sequence</span></span> | <span data-ttu-id="95319-586">형식</span><span class="sxs-lookup"><span data-stu-id="95319-586">Type</span></span>      | <span data-ttu-id="95319-587">data</span><span class="sxs-lookup"><span data-stu-id="95319-587">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="95319-588">0</span><span class="sxs-lookup"><span data-stu-id="95319-588">0</span></span>        | <span data-ttu-id="95319-589">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="95319-589">Text node</span></span> | <span data-ttu-id="95319-590">첫째</span><span class="sxs-lookup"><span data-stu-id="95319-590">First</span></span>  |
| <span data-ttu-id="95319-591">1</span><span class="sxs-lookup"><span data-stu-id="95319-591">1</span></span>        | <span data-ttu-id="95319-592">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="95319-592">Text node</span></span> | <span data-ttu-id="95319-593">Second</span><span class="sxs-lookup"><span data-stu-id="95319-593">Second</span></span> |

<span data-ttu-id="95319-594">이 결과는 이전 사례와 동일 하므로 부정적인 문제가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-594">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="95319-595">`someFlag`은 두 번째 렌더링에서 `false` 이며 출력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-595">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="95319-596">Sequence</span><span class="sxs-lookup"><span data-stu-id="95319-596">Sequence</span></span> | <span data-ttu-id="95319-597">type</span><span class="sxs-lookup"><span data-stu-id="95319-597">Type</span></span>      | <span data-ttu-id="95319-598">data</span><span class="sxs-lookup"><span data-stu-id="95319-598">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="95319-599">0</span><span class="sxs-lookup"><span data-stu-id="95319-599">0</span></span>        | <span data-ttu-id="95319-600">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="95319-600">Text node</span></span> | <span data-ttu-id="95319-601">Second</span><span class="sxs-lookup"><span data-stu-id="95319-601">Second</span></span> |

<span data-ttu-id="95319-602">이번에는 diff 알고리즘은 *두 가지* 변경이 발생 했음을 확인 하 고 알고리즘은 다음과 같은 편집 스크립트를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-602">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="95319-603">첫 번째 텍스트 노드의 값을 `Second`으로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-603">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="95319-604">두 번째 텍스트 노드를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-604">Remove the second text node.</span></span>

<span data-ttu-id="95319-605">시퀀스 번호를 생성 하면 `if/else` 분기와 루프가 원래 코드에 표시 된 위치에 대 한 모든 유용한 정보가 손실 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-605">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="95319-606">이로 인해 이전 처럼 비교가 **두 번** 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-606">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="95319-607">이는 간단한 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-607">This is a trivial example.</span></span> <span data-ttu-id="95319-608">복잡 하 고 많이 중첩 된 구조와 특히 루프를 사용 하는 현실적인 경우에는 성능 비용이 더 심각 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-608">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="95319-609">삽입 또는 제거 된 루프 블록 또는 분기를 즉시 식별 하는 대신, diff 알고리즘은 렌더링 트리를 깊이 있게 구분 해야 하며, 일반적으로 이전 및 새 구조를 misinformed 하기 때문에 훨씬 더 긴 편집 스크립트를 작성 해야 합니다. 서로 관련 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-609">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="95319-610">지침 및 결론</span><span class="sxs-lookup"><span data-stu-id="95319-610">Guidance and conclusions</span></span>

* <span data-ttu-id="95319-611">시퀀스 번호가 동적으로 생성 되 면 앱 성능이 저하 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-611">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="95319-612">컴파일 시간에 캡처되는 경우를 제외 하 고 필요한 정보가 존재 하지 않기 때문에 프레임 워크는 런타임에 고유한 시퀀스 번호를 자동으로 만들 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-612">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="95319-613">수동으로 구현 된 `RenderTreeBuilder` 논리의 긴 블록을 작성 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="95319-613">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="95319-614">@No__t-0 파일을 선호 하 고 컴파일러가 시퀀스 번호를 처리할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-614">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="95319-615">수동 `RenderTreeBuilder` 논리를 방지할 수 없는 경우 긴 코드 블록을 `OpenRegion` @ no__t @ no__t-3 호출에서 래핑된 작은 조각으로 분할 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-615">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="95319-616">각 영역에는 고유한 시퀀스 번호 공간이 있으므로 각 지역 내에서 0 (또는 다른 임의의 임의의 숫자)으로 다시 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-616">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="95319-617">시퀀스 번호가 하드 코딩 된 경우 diff 알고리즘에서는 값이 증가 하는 시퀀스 번호만 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-617">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="95319-618">초기 값과 간격은 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-618">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="95319-619">한 가지 합법적인 옵션은 코드 줄 번호를 시퀀스 번호로 사용 하거나 0부터 시작 하 여 또는 수백 (또는 선호 하는 간격) 만큼 증가 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-619">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="95319-620">Blazor는 시퀀스 번호를 사용 하지만 다른 트리 diff UI 프레임 워크는이를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-620">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="95319-621">시퀀스 번호를 사용 하는 경우 diff는 훨씬 더 빠르며, Blazor는 `.razor` 파일을 작성 하는 개발자를 위해 자동으로 시퀀스 번호를 처리 하는 컴파일 단계를 활용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-621">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="localization"></a><span data-ttu-id="95319-622">지역화</span><span class="sxs-lookup"><span data-stu-id="95319-622">Localization</span></span>

<span data-ttu-id="95319-623">Blazor 서버 앱은 [지역화 미들웨어](xref:fundamentals/localization#localization-middleware)를 사용 하 여 지역화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-623">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="95319-624">미들웨어는 앱에서 리소스를 요청 하는 사용자에 게 적절 한 문화권을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-624">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="95319-625">문화권은 다음 방법 중 하나를 사용 하 여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-625">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="95319-626">쿠키</span><span class="sxs-lookup"><span data-stu-id="95319-626">Cookies</span></span>](#cookies)
* [<span data-ttu-id="95319-627">문화권을 선택 하는 UI 제공</span><span class="sxs-lookup"><span data-stu-id="95319-627">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="95319-628">자세한 내용과 예제를 보려면 <xref:fundamentals/localization>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="95319-628">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="95319-629">쿠키</span><span class="sxs-lookup"><span data-stu-id="95319-629">Cookies</span></span>

<span data-ttu-id="95319-630">지역화 문화권 쿠키는 사용자의 문화권을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-630">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="95319-631">쿠키는 앱의 호스트 페이지 (*Pages/host-a*)의 `OnGet` 메서드에 의해 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-631">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="95319-632">지역화 미들웨어는 후속 요청에서 쿠키를 읽어 사용자의 culture를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-632">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="95319-633">쿠키를 사용 하면 WebSocket 연결이 문화권을 올바르게 전파할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-633">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="95319-634">지역화 체계가 URL 경로 또는 쿼리 문자열을 기반으로 하는 경우 스키마는 Websocket을 사용 하지 못할 수 있으므로 문화권이 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-634">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="95319-635">따라서 지역화 문화권 쿠키를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-635">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="95319-636">문화권이 지역화 쿠키에 유지 되는 경우 모든 기술을 사용 하 여 문화권을 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-636">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="95319-637">서버 쪽 ASP.NET Core에 대해 설정 된 지역화 체계가 앱에 이미 있는 경우 앱의 기존 지역화 인프라를 계속 사용 하 고 앱의 체계 내에서 지역화 문화권 쿠키를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-637">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="95319-638">다음 예제에서는 지역화 미들웨어에서 읽을 수 있는 쿠키의 현재 문화권을 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="95319-638">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="95319-639">Blazor 서버 앱에 다음 내용이 포함 된 *Pages/Host. cshtml* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="95319-639">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="95319-640">지역화는 앱에서 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-640">Localization is handled in the app:</span></span>

1. <span data-ttu-id="95319-641">브라우저가 앱에 초기 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="95319-641">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="95319-642">문화권이 지역화 미들웨어에 의해 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-642">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="95319-643">*_Host* 의 `OnGet` 메서드는 응답의 일부로 쿠키의 문화권을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-643">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="95319-644">브라우저는 WebSocket 연결을 열어 대화형 Blazor 서버 세션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="95319-644">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="95319-645">지역화 미들웨어는 쿠키를 읽고 문화권을 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-645">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="95319-646">Blazor 서버 세션이 올바른 문화권으로 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-646">The Blazor Server session begins with the correct culture.</span></span>

## <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="95319-647">문화권을 선택 하는 UI 제공</span><span class="sxs-lookup"><span data-stu-id="95319-647">Provide UI to choose the culture</span></span>

<span data-ttu-id="95319-648">사용자가 문화권을 선택할 수 있도록 UI를 제공 하려면 *리디렉션 기반 방법을* 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-648">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="95319-649">이 프로세스는 사용자가 보안 리소스에 대 한 액세스를 시도할 때 웹 앱에서 수행 하는 작업과 비슷합니다. @ no__t-0 사용자가 로그인 페이지로 리디렉션되고 다시 원래 리소스로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-649">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="95319-650">앱은 컨트롤러에 대 한 리디렉션을 통해 사용자가 선택한 문화권을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-650">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="95319-651">컨트롤러는 사용자가 선택한 문화권을 쿠키로 설정 하 고 사용자를 다시 원래 URI로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-651">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="95319-652">서버에서 HTTP 끝점을 설정 하 여 사용자가 선택한 문화권을 쿠키에 설정 하 고 다시 원래 URI로 리디렉션을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-652">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="95319-653">@No__t-0 작업 결과를 사용 하 여 열린 리디렉션 공격을 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-653">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="95319-654">자세한 내용은 <xref:security/preventing-open-redirects>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="95319-654">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="95319-655">다음 구성 요소는 사용자가 문화권을 선택할 때 초기 리디렉션을 수행 하는 방법의 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="95319-655">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```cshtml
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a><span data-ttu-id="95319-656">Blazor apps에서 .NET 지역화 시나리오 사용</span><span class="sxs-lookup"><span data-stu-id="95319-656">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="95319-657">Blazor apps 내에서 다음과 같은 .NET 지역화 및 세계화 시나리오를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-657">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="95319-658">. NET의 리소스 시스템</span><span class="sxs-lookup"><span data-stu-id="95319-658">.NET's resources system</span></span>
* <span data-ttu-id="95319-659">문화권별 숫자 및 날짜 형식 지정</span><span class="sxs-lookup"><span data-stu-id="95319-659">Culture-specific number and date formatting</span></span>

<span data-ttu-id="95319-660">Blazor의 `@bind` 기능은 사용자의 현재 문화권에 따라 세계화를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-660">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="95319-661">자세한 내용은 [데이터 바인딩](#data-binding) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="95319-661">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="95319-662">제한 된 ASP.NET Core의 지역화 시나리오 집합이 현재 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-662">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="95319-663">`IStringLocalizer<>`은 Blazor apps에서 *지원 됩니다* .</span><span class="sxs-lookup"><span data-stu-id="95319-663">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="95319-664">`IHtmlLocalizer<>`, `IViewLocalizer<>` 및 데이터 주석 지역화는 MVC 시나리오 ASP.NET Core Blazor apps에서 **지원 되지 않습니다** .</span><span class="sxs-lookup"><span data-stu-id="95319-664">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="95319-665">자세한 내용은 <xref:fundamentals/localization>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="95319-665">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="95319-666">SVG (스케일러블 벡터 그래픽) 이미지</span><span class="sxs-lookup"><span data-stu-id="95319-666">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="95319-667">Blazor는 HTML을 렌더링 하므로 SVG (확장 가능한 벡터 그래픽)*이미지 (* )를 비롯 한 브라우저 지원 이미지는 `<img>` 태그를 통해 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-667">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="95319-668">마찬가지로, 스타일 시트 파일 ( *.css*)의 css 규칙에서 SVG 이미지가 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95319-668">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="95319-669">그러나 인라인 SVG 태그는 일부 시나리오에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-669">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="95319-670">구성 요소 파일 (*razor*)에 `<svg>` 태그를 직접 저장 하는 경우 기본 이미지 렌더링이 지원 되지만 많은 고급 시나리오는 아직 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-670">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="95319-671">예를 들어 `<use>` 태그는 현재 적용 되지 않으며 `@bind`은 일부 SVG 태그와 함께 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="95319-671">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="95319-672">향후 릴리스에서 이러한 제한을 해결할 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="95319-672">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95319-673">추가 자료</span><span class="sxs-lookup"><span data-stu-id="95319-673">Additional resources</span></span>

* <span data-ttu-id="95319-674"><xref:security/blazor/server> &ndash;은 리소스 고갈에 경합 해야 하는 Blazor Server 앱 빌드에 대 한 지침을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="95319-674"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
