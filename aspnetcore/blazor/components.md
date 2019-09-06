---
title: ASP.NET Core Razor 구성 요소 만들기 및 사용
author: guardrex
description: 데이터에 바인딩하고, 이벤트를 처리 하 고, 구성 요소 수명 주기를 관리 하는 방법을 비롯 하 여 Razor 구성 요소를 만들고 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/04/2019
uid: blazor/components
ms.openlocfilehash: ce9da14bbe19cbee960d215f6167a0e760bd607a
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310371"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="c3e38-103">ASP.NET Core Razor 구성 요소 만들기 및 사용</span><span class="sxs-lookup"><span data-stu-id="c3e38-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="c3e38-104">By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c3e38-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c3e38-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c3e38-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c3e38-106">Blazor apps는 *구성 요소*를 사용 하 여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="c3e38-107">구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI (사용자 인터페이스)의 자체 포함 청크입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="c3e38-108">구성 요소는 데이터를 삽입 하거나 UI 이벤트에 응답 하는 데 필요한 HTML 태그와 처리 논리를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="c3e38-109">구성 요소는 유연 하 고 간단 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="c3e38-110">프로젝트 간에 중첩, 재사용 및 공유 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="c3e38-111">구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="c3e38-111">Component classes</span></span>

<span data-ttu-id="c3e38-112">구성 요소는 및 HTML 태그의 C# 조합을 사용 하 여 [razor](xref:mvc/views/razor) 구성 요소 파일 (*razor*)에서 구현 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="c3e38-113">Blazor의 구성 요소는 공식적으로 *Razor 구성 요소*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="c3e38-114">구성 요소의 이름은 대문자로 시작 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="c3e38-115">예를 들어 *MyCoolComponent* 는 유효 하며 *MyCoolComponent* 은 유효 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="c3e38-116">MSBuild 속성을 `_RazorComponentInclude` 사용 하 여 파일이 Razor 구성 요소 파일로 식별 되는 *경우에* 는이 파일 확장명을 사용 하 여 구성 요소를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-116">Components can be authored using the *.cshtml* file extension as long as the files are identified as Razor component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="c3e38-117">예를 들어 *Pages* 폴더 아래의 모든 *Cshtml* 파일을 Razor 구성 요소 파일로 처리 하도록 지정 하는 앱이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-117">For example, an app that specifies that all *.cshtml* files under the *Pages* folder should be treated as Razor components files:</span></span>

```xml
<PropertyGroup>
  <_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
</PropertyGroup>
```

<span data-ttu-id="c3e38-118">구성 요소에 대 한 UI는 HTML을 사용 하 여 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-118">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="c3e38-119">동적 렌더링 논리(예: 루프, 조건, 식)는 [Razor](xref:mvc/views/razor)라는 포함된 C# 구문을 사용하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-119">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="c3e38-120">앱이 컴파일되면 HTML 태그 및 C# 렌더링 논리가 구성 요소 클래스로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-120">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="c3e38-121">생성 된 클래스의 이름은 파일 이름과 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-121">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="c3e38-122">구성 요소 클래스의 멤버는 `@code` 블록에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-122">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="c3e38-123">`@code` 블록에서 구성 요소 상태 (속성, 필드)는 이벤트 처리를 위한 메서드나 다른 구성 요소 논리를 정의 하는 데 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-123">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="c3e38-124">두 개 이상의 `@code` 블록이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-124">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="c3e38-125">ASP.NET Core 3.0 `@functions` 의 이전 미리 보기에서 블록은 Razor 구성 요소의 `@code` 블록과 동일한 용도에 사용 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-125">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="c3e38-126">`@functions`블록은 Razor 구성 요소에서 계속 작동 하지만 ASP.NET Core 3.0 Preview 6 `@code` 이상에서 블록을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-126">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="c3e38-127">구성 요소 멤버는로 C# `@`시작 하는 식을 사용 하 여 구성 요소의 렌더링 논리의 일부로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-127">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="c3e38-128">예를 들어 필드 C# 는 필드 이름을 접두사로 `@` 하 여 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-128">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="c3e38-129">다음 예에서는를 평가 하 고 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-129">The following example evaluates and renders:</span></span>

* <span data-ttu-id="c3e38-130">`_headingFontStyle`에 대 한 `font-style`CSS 속성 값입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-130">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="c3e38-131">`_headingText``<h1>` 요소의 내용에 대 한입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-131">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="c3e38-132">구성 요소가 처음 렌더링 되 면 구성 요소는 이벤트에 대 한 응답으로 렌더링 트리를 다시 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-132">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="c3e38-133">그런 다음 Blazor는 새 렌더링 트리를 이전 렌더링 트리와 비교 하 여 브라우저의 DOM (문서 개체 모델)에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-133">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="c3e38-134">구성 요소는 C# 일반 클래스 이며 프로젝트 내의 어느 위치에 나 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-134">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="c3e38-135">웹 페이지를 생성 하는 구성 요소는 일반적으로 *Pages* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-135">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="c3e38-136">페이지를 지정 하지 않는 구성 요소는 일반적으로 *공유* 폴더 또는 프로젝트에 추가 된 사용자 지정 폴더에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-136">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="c3e38-137">사용자 지정 폴더를 사용 하려면 부모 구성 요소 또는 앱의 *_Imports. razor* 파일에 사용자 지정 폴더의 네임 스페이스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-137">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="c3e38-138">예를 들어 다음 네임 스페이스는 응용 프로그램의 루트 네임 스페이스가 인 `WebApplication`경우 *구성 요소* 폴더의 구성 요소를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-138">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="c3e38-139">Razor Pages 및 MVC 앱에 구성 요소 통합</span><span class="sxs-lookup"><span data-stu-id="c3e38-139">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="c3e38-140">기존 Razor Pages 및 MVC 앱과 함께 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-140">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="c3e38-141">Razor 구성 요소를 사용 하기 위해 기존 페이지나 뷰를 다시 작성할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-141">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="c3e38-142">페이지 또는 뷰가 렌더링 될 때 구성 요소는 동시에 미리 렌더링 된 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-142">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="c3e38-143">페이지 또는 뷰에서 구성 요소를 렌더링 하려면 HTML 도우미 메서드를 `RenderComponentAsync<TComponent>` 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-143">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

<span data-ttu-id="c3e38-144">페이지 및 뷰에서 구성 요소를 사용할 수 있지만 반대의 경우는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-144">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="c3e38-145">구성 요소는 부분 보기 및 섹션과 같은 보기 및 페이지 관련 시나리오를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-145">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="c3e38-146">구성 요소의 부분 뷰에서 논리를 사용 하려면 부분 뷰 논리를 구성 요소에 대 한 요소로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-146">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="c3e38-147">구성 요소를 렌더링 하는 방법과 구성 요소 상태가 Blazor 서버 쪽 앱에서 관리 되는 방법에 대 한 <xref:blazor/hosting-models> 자세한 내용은 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c3e38-147">For more information on how components are rendered and component state is managed in Blazor server-side apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="use-components"></a><span data-ttu-id="c3e38-148">구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="c3e38-148">Use components</span></span>

<span data-ttu-id="c3e38-149">구성 요소에는 HTML 요소 구문을 사용 하 여 선언 함으로써 다른 구성 요소가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-149">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="c3e38-150">구성 요소 사용을 위한 태그는 태그 이름이 구성 요소 유형인 HTML 태그처럼 보입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-150">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="c3e38-151">특성 바인딩은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-151">Attribute binding is case sensitive.</span></span> <span data-ttu-id="c3e38-152">예 `@bind` 를 들어는 유효 하며 `@Bind` 는 유효 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-152">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="c3e38-153">*인덱스 razor* 의 다음 태그는 인스턴스를 `HeadingComponent` 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-153">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="c3e38-154">*Components/HeadingComponent*:</span><span class="sxs-lookup"><span data-stu-id="c3e38-154">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

<span data-ttu-id="c3e38-155">구성 요소에 구성 요소 이름과 일치 하지 않는 대문자 첫 글자가 포함 된 HTML 요소가 포함 된 경우 요소에 예기치 않은 이름이 있음을 나타내는 경고가 내보내집니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-155">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="c3e38-156">구성 요소 네임 스페이스에 대 한 문을추가하면구성요소를사용할수있게되므로경고가제거됩니다.`@using`</span><span class="sxs-lookup"><span data-stu-id="c3e38-156">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="c3e38-157">구성 요소 매개 변수</span><span class="sxs-lookup"><span data-stu-id="c3e38-157">Component parameters</span></span>

<span data-ttu-id="c3e38-158">구성 요소는 `[Parameter]` 특성을 사용 하 여 구성 요소 클래스의 공용 속성을 사용 하 여 정의 되는 *구성 요소 매개 변수*를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-158">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="c3e38-159">특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-159">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="c3e38-160">*Components/ChildComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="c3e38-160">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="c3e38-161">다음 예제에서는 `ParentComponent` 의 `Title` `ChildComponent`속성 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-161">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="c3e38-162">*Pages/ParentComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="c3e38-162">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="c3e38-163">자식 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="c3e38-163">Child content</span></span>

<span data-ttu-id="c3e38-164">구성 요소는 다른 구성 요소의 콘텐츠를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-164">Components can set the content of another component.</span></span> <span data-ttu-id="c3e38-165">할당 구성 요소는 받는 구성 요소를 지정 하는 태그 사이에 콘텐츠를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-165">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="c3e38-166">다음 예제에서에 `ChildComponent` 는 렌더링할 UI 세그먼트를 나타내는를 나타내는 `RenderFragment` `ChildContent` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-166">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="c3e38-167">의 `ChildContent` 값은 콘텐츠를 렌더링 해야 하는 구성 요소의 태그에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-167">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="c3e38-168">의 `ChildContent` 값은 부모 구성 요소에서 수신 되 고 부트스트랩 패널의 `panel-body`내에서 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-168">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="c3e38-169">*Components/ChildComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="c3e38-169">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="c3e38-170">콘텐츠를 `RenderFragment` 받는 속성의 이름은 규칙에 `ChildContent` 따라 지정 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-170">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="c3e38-171">다음 `ParentComponent` 은 `<ChildComponent>` 태그 내부에 내용을 배치 하 `ChildComponent` 여를 렌더링 하기 위한 콘텐츠를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-171">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="c3e38-172">*Pages/ParentComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="c3e38-172">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="c3e38-173">특성 스 플랫 및 임의 매개 변수</span><span class="sxs-lookup"><span data-stu-id="c3e38-173">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="c3e38-174">구성 요소는 구성 요소의 선언 된 매개 변수 외에 추가 특성도 캡처하고 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-174">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="c3e38-175">추가 특성을 사전에 캡처한 다음 Razor 지시문을 [@attributes](xref:mvc/views/razor#attributes) 사용 하 여 구성 요소를 렌더링할 때 요소로 splatted 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-175">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="c3e38-176">이 시나리오는 다양 한 사용자 지정을 지 원하는 태그 요소를 생성 하는 구성 요소를 정의 하는 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-176">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="c3e38-177">예를 들어 많은 매개 변수를 지 원하는에 대해 `<input>` 별도로 특성을 정의 하는 것이 지루한 일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-177">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="c3e38-178">다음 예제 `<input>` 에서 첫 번째 요소 (`id="useIndividualParams"`)는 개별 구성 요소 매개 변수를 사용 하는 반면 두`id="useAttributesDict"`번째 `<input>` 요소 ()는 특성 스 플랫를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-178">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="c3e38-179">매개 변수의 형식은 문자열 키를 사용 `IEnumerable<KeyValuePair<string, object>>` 하 여를 구현 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-179">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="c3e38-180">을 `IReadOnlyDictionary<string, object>` 사용 하는 것도이 시나리오의 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-180">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="c3e38-181">두 방법을 `<input>` 모두 사용 하 여 렌더링 된 요소는 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-181">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="c3e38-182">임의 특성을 허용 하려면 `[Parameter]` `CaptureUnmatchedValues` 속성이로 `true`설정 된 특성을 사용 하 여 구성 요소 매개 변수를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-182">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="c3e38-183">`CaptureUnmatchedValues` 의`[Parameter]` 속성을 사용 하면 매개 변수는 다른 매개 변수와 일치 하지 않는 모든 특성을 일치 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-183">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="c3e38-184">구성 요소는을 사용 하 `CaptureUnmatchedValues`여 단일 매개 변수만 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-184">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="c3e38-185">에 `CaptureUnmatchedValues` 사용 되는 속성 형식은 문자열 키를 `Dictionary<string, object>` 사용 하 여에서 할당할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-185">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="c3e38-186">`IEnumerable<KeyValuePair<string, object>>`또는 `IReadOnlyDictionary<string, object>` 이 시나리오의 옵션 이기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-186">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

## <a name="data-binding"></a><span data-ttu-id="c3e38-187">데이터 바인딩</span><span class="sxs-lookup"><span data-stu-id="c3e38-187">Data binding</span></span>

<span data-ttu-id="c3e38-188">구성 요소와 DOM 요소 모두에 대 한 데이터 바인딩은 [@bind](xref:mvc/views/razor#bind) 특성을 사용 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-188">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="c3e38-189">다음 예제에서는 `_italicsCheck` 필드를 확인란이 선택 된 상태에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-189">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="c3e38-190">확인란을 선택 하 고 선택 취소 하면 속성의 값이 각각 및 `true` `false`로 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-190">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="c3e38-191">확인란은 속성의 값을 변경 하는 것이 아니라 구성 요소가 렌더링 되는 경우에만 UI에서 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-191">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="c3e38-192">이벤트 처리기 코드를 실행 한 후 구성 요소를 자체적으로 렌더링 하므로 속성 업데이트는 일반적으로 UI에 즉시 반영 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-192">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="c3e38-193">`CurrentValue` 속성 `@bind` (`<input @bind="CurrentValue" />`)과 함께를 사용 하는 것은 기본적으로 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-193">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="c3e38-194">구성 요소가 렌더링 되 면 input 요소의 `value` 은 `CurrentValue` 속성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-194">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="c3e38-195">사용자가 텍스트 상자에를 `onchange` 입력 하면 이벤트가 발생 `CurrentValue` 하 고 속성이 변경 된 값으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-195">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="c3e38-196">실제로 코드 생성은 형식 변환이 수행 되는 몇 가지 경우 `@bind` 를 처리 하기 때문에 좀 더 복잡 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-196">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="c3e38-197">원칙적 `@bind` 으로는 식 `value` 의 현재 값을 특성과 연결 하 고 등록 된 처리기를 사용 하 여 변경 내용을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-197">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="c3e38-198">`onchange` 구문을 사용 [@bind-value](xref:mvc/views/razor#bind) `event` [@bind-value:event](xref:mvc/views/razor#bind)하 여 이벤트를 처리 하는 것 외에도 매개 변수 ()를 사용 하 여 특성을 지정 하면 다른 이벤트를 사용 하 여 속성 또는 필드를 바인딩할 수 있습니다. `@bind`</span><span class="sxs-lookup"><span data-stu-id="c3e38-198">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="c3e38-199">다음 예제에서는 `oninput` 이벤트에 `CurrentValue` 대 한 속성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-199">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="c3e38-200">요소가 `onchange`포커스를 잃을 때 발생 하는와 달리 `oninput` 는 텍스트 상자의 값이 변경 될 때 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-200">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="c3e38-201">**전역화**</span><span class="sxs-lookup"><span data-stu-id="c3e38-201">**Globalization**</span></span>

<span data-ttu-id="c3e38-202">`@bind`값은 현재 문화권의 규칙을 사용 하 여 표시 및 구문 분석을 위해 형식이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-202">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="c3e38-203">현재 문화권은 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 속성에서 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-203">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="c3e38-204">[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) 는 다음 필드 형식 (`<input type="{TYPE}" />`)에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-204">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="c3e38-205">이전 필드 형식:</span><span class="sxs-lookup"><span data-stu-id="c3e38-205">The preceding field types:</span></span>

* <span data-ttu-id="c3e38-206">적절 한 브라우저 기반 서식 지정 규칙을 사용 하 여 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-206">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="c3e38-207">자유 형식 텍스트를 포함할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-207">Can't contain free-form text.</span></span>
* <span data-ttu-id="c3e38-208">브라우저의 구현에 따라 사용자 상호 작용 특성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-208">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="c3e38-209">다음 필드 형식은 특정 형식 지정 요구 사항을 가지 며 현재 Blazor에서 지원 되지 않습니다. 모든 주요 브라우저에서 지원 되지 않기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-209">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="c3e38-210">`@bind`값을 구문 분석 하 고 <xref:System.Globalization.CultureInfo?displayProperty=fullName> 형식을 지정 하기 위해 매개변수를지원합니다.`@bind:culture`</span><span class="sxs-lookup"><span data-stu-id="c3e38-210">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="c3e38-211">`date` 및`number` 필드 형식을 사용할 때는 문화권을 지정 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-211">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="c3e38-212">`date`및 `number` 에는 필수 문화권을 제공 하는 기본 제공 Blazor 지원이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-212">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="c3e38-213">사용자의 문화권을 설정 하는 방법에 대 한 자세한 내용은 [지역화](#localization) 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="c3e38-213">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="c3e38-214">**서식 문자열**</span><span class="sxs-lookup"><span data-stu-id="c3e38-214">**Format strings**</span></span>

<span data-ttu-id="c3e38-215">데이터 바인딩은를 사용 <xref:System.DateTime> 하 여 [@bind:format](xref:mvc/views/razor#bind)형식 문자열과 함께 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-215">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="c3e38-216">통화 또는 숫자 형식과 같은 다른 형식 식은 현재 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-216">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="c3e38-217">위의 코드에서 요소의 필드 형식 `<input>` (`type`)은 기본적으로로 `text`설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-217">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="c3e38-218">`@bind:format`는 다음 .NET 형식의 바인딩에 대해 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-218">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="c3e38-219"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c3e38-219"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="c3e38-220"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c3e38-220"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="c3e38-221">특성 `@bind:format` `value` 은 요소의`<input>` 에 적용할 날짜 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-221">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="c3e38-222">형식은 `onchange` 이벤트가 발생할 때 값을 구문 분석 하는 데도 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-222">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="c3e38-223">Blazor에서 날짜 형식을 기본적 `date` 으로 지원 하기 때문에 필드 형식의 형식을 지정 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-223">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

<span data-ttu-id="c3e38-224">**구성 요소 매개 변수**</span><span class="sxs-lookup"><span data-stu-id="c3e38-224">**Component parameters**</span></span>

<span data-ttu-id="c3e38-225">바인딩은 구성 요소 매개 변수를 `@bind-{property}` 인식 합니다. 여기서는 구성 요소에서 속성 값을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-225">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="c3e38-226">다음 자식 구성 요소 (`ChildComponent`)에는 `Year` 구성 요소 매개 `YearChanged` 변수 및 콜백이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-226">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="c3e38-227">`EventCallback<T>`는 [Eventcallback](#eventcallback) 섹션에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-227">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="c3e38-228">다음 부모 구성 요소는 `ChildComponent` 부모의 `ParentYear` 매개 변수를 `Year` 사용 하 여 자식 구성 요소의 매개 변수에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-228">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

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

<span data-ttu-id="c3e38-229">을 로드 `ParentComponent` 하면 다음과 같은 태그가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-229">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="c3e38-230">`ParentYear` 의 단추 `ParentComponent` 를선택`ChildComponent` 하 여 속성 값이 변경 되 면의 속성이업데이트됩니다.`Year`</span><span class="sxs-lookup"><span data-stu-id="c3e38-230">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="c3e38-231">의 `Year` 새 값은 `ParentComponent` 가 수행 될 때 UI에서 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-231">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="c3e38-232">매개 `Year` 변수는 `Year` 매개 변수 형식과 일치 하는 `YearChanged` 동반 이벤트를 포함 하기 때문에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-232">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="c3e38-233">규칙에 따라 `<ChildComponent @bind-Year="ParentYear" />` 은 기본적으로 다음을 작성 하는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-233">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="c3e38-234">일반적으로 속성은 특성을 사용 하 여 `@bind-property:event` 해당 이벤트 처리기에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-234">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="c3e38-235">예를 들어 속성 `MyProp` 은 다음 두 가지 특성을 `MyEventHandler` 사용 하 여 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-235">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="c3e38-236">이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="c3e38-236">Event handling</span></span>

<span data-ttu-id="c3e38-237">Razor 구성 요소는 이벤트 처리 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-237">Razor components provide event handling features.</span></span> <span data-ttu-id="c3e38-238">대리자 형식 값을 사용 하 `on{event}` 여 ( `onclick` 예: 및 `onsubmit`) 이라는 HTML 요소 특성의 경우 Razor 구성 요소는 특성의 값을 이벤트 처리기로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-238">For an HTML element attribute named `on{event}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="c3e38-239">특성 이름에는 항상 [ @on{event}](xref:mvc/views/razor#onevent)형식이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-239">The attribute's name is always formatted [@on{event}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="c3e38-240">다음 코드는 UI에서 `UpdateHeading` 단추가 선택 될 때 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-240">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="c3e38-241">다음 코드는 UI에서 `CheckChanged` 확인란이 변경 될 때 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-241">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="c3e38-242">이벤트 처리기는 비동기 일 수도 있고를 <xref:System.Threading.Tasks.Task>반환할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-242">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="c3e38-243">를 수동으로 호출할 `StateHasChanged()`필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-243">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="c3e38-244">예외가 발생 하면 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-244">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="c3e38-245">다음 예제에서 단추를 `UpdateHeading` 선택 하면가 비동기적으로 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-245">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a><span data-ttu-id="c3e38-246">이벤트 인수 형식</span><span class="sxs-lookup"><span data-stu-id="c3e38-246">Event argument types</span></span>

<span data-ttu-id="c3e38-247">일부 이벤트의 경우 이벤트 인수 형식이 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-247">For some events, event argument types are permitted.</span></span> <span data-ttu-id="c3e38-248">이러한 이벤트 유형 중 하나에 대 한 액세스가 필요 하지 않은 경우에는 메서드 호출에 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-248">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="c3e38-249">지원 되는 [EventArgs](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web) 는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-249">Supported [EventArgs](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web) are shown in the following table.</span></span>

| <span data-ttu-id="c3e38-250">이벤트</span><span class="sxs-lookup"><span data-stu-id="c3e38-250">Event</span></span> | <span data-ttu-id="c3e38-251">클래스</span><span class="sxs-lookup"><span data-stu-id="c3e38-251">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="c3e38-252">클립보드</span><span class="sxs-lookup"><span data-stu-id="c3e38-252">Clipboard</span></span>        | `ClipboardEventArgs` |
| <span data-ttu-id="c3e38-253">옵니다</span><span class="sxs-lookup"><span data-stu-id="c3e38-253">Drag</span></span>             | <span data-ttu-id="c3e38-254">`DragEventArgs`끌어 온 항목 데이터를 `DataTransferItem` 저장 합니다. &ndash; `DataTransfer`</span><span class="sxs-lookup"><span data-stu-id="c3e38-254">`DragEventArgs` &ndash; `DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="c3e38-255">Error</span><span class="sxs-lookup"><span data-stu-id="c3e38-255">Error</span></span>            | `ErrorEventArgs` |
| <span data-ttu-id="c3e38-256">포커스</span><span class="sxs-lookup"><span data-stu-id="c3e38-256">Focus</span></span>            | <span data-ttu-id="c3e38-257">`FocusEventArgs`는에 대 한 `relatedTarget`지원을 포함 하지 않습니다. &ndash;</span><span class="sxs-lookup"><span data-stu-id="c3e38-257">`FocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="c3e38-258">`<input>` 변경</span><span class="sxs-lookup"><span data-stu-id="c3e38-258">`<input>` change</span></span> | `ChangeEventArgs` |
| <span data-ttu-id="c3e38-259">키보드</span><span class="sxs-lookup"><span data-stu-id="c3e38-259">Keyboard</span></span>         | `KeyboardEventArgs` |
| <span data-ttu-id="c3e38-260">마우스</span><span class="sxs-lookup"><span data-stu-id="c3e38-260">Mouse</span></span>            | `MouseEventArgs` |
| <span data-ttu-id="c3e38-261">마우스 포인터</span><span class="sxs-lookup"><span data-stu-id="c3e38-261">Mouse pointer</span></span>    | `PointerEventArgs` |
| <span data-ttu-id="c3e38-262">마우스 휠</span><span class="sxs-lookup"><span data-stu-id="c3e38-262">Mouse wheel</span></span>      | `WheelEventArgs` |
| <span data-ttu-id="c3e38-263">진행률</span><span class="sxs-lookup"><span data-stu-id="c3e38-263">Progress</span></span>         | `ProgressEventArgs` |
| <span data-ttu-id="c3e38-264">터치</span><span class="sxs-lookup"><span data-stu-id="c3e38-264">Touch</span></span>            | <span data-ttu-id="c3e38-265">`TouchEventArgs`&ndash; 터치를구분하는장치에서`TouchPoint` 단일 접촉 지점을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-265">`TouchEventArgs` &ndash; `TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="c3e38-266">위의 표에서 이벤트의 속성 및 이벤트 처리 동작에 대 한 자세한 내용은 [참조 소스의 EventArgs 클래스 (aspnet/AspNetCore release/3.0-preview9 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c3e38-266">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0-preview9 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="c3e38-267">람다 식</span><span class="sxs-lookup"><span data-stu-id="c3e38-267">Lambda expressions</span></span>

<span data-ttu-id="c3e38-268">람다 식을 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-268">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="c3e38-269">요소 집합을 반복 하는 경우와 같이 추가 값을 닫는 것이 편리한 경우가 종종 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-269">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="c3e38-270">다음 예제에서는 UI에서 선택 된 경우 각각 `UpdateHeading` 이벤트 인수 (`UIMouseEventArgs`)와 해당 단추 번호 (`buttonNumber`)를 전달 하는 세 개의 단추를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-270">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="c3e38-271">루프 변수 (`i`) `for` 를 람다 식에서 직접 루프에 사용 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c3e38-271">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="c3e38-272">그렇지 않으면 모든 람다 식에서 동일한 변수를 사용 하 `i`여 모든 람다 식에서 값이 동일 하 게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-272">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="c3e38-273">항상 지역 변수 (`buttonNumber` 이전 예제)에서 해당 값을 캡처한 다음 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-273">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="c3e38-274">EventCallback</span><span class="sxs-lookup"><span data-stu-id="c3e38-274">EventCallback</span></span>

<span data-ttu-id="c3e38-275">중첩 된 구성 요소가 있는 일반적인 시나리오는 자식 구성 요소 이벤트가 발생&mdash;하는 경우 (예: 자식에서 `onclick` 이벤트가 발생할 때) 부모 구성 요소의 메서드를 실행 하고자 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-275">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="c3e38-276">구성 요소 간에 이벤트를 노출 하려면를 `EventCallback`사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-276">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="c3e38-277">부모 구성 요소는 콜백 메서드를 자식 구성 요소 `EventCallback`에 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-277">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="c3e38-278">샘플 `ChildComponent` 앱의는 `onclick` 단추의 처리기가 샘플의 `ParentComponent`에서 `EventCallback` 대리자를 받도록 설정 되는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-278">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="c3e38-279">는 `EventCallback` 주변 장치의 `onclick` 이벤트 `UIMouseEventArgs`에 적합 한로 형식화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-279">The `EventCallback` is typed with `UIMouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="c3e38-280">는 `ParentComponent` 자식`EventCallback<T>` 를 해당`ShowMessage` 메서드로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-280">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="c3e38-281">에서 단추가 선택 된 경우 `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="c3e38-281">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="c3e38-282">`ParentComponent` 의`ShowMessage` 메서드가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-282">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="c3e38-283">`messageText`가 업데이트 되어에 `ParentComponent`표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-283">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="c3e38-284">호출 `StateHasChanged` 은 콜백의 메서드 (`ShowMessage`)에서 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-284">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="c3e38-285">`StateHasChanged`자식 이벤트가 자식 내에서 실행 `ParentComponent`되는 이벤트 처리기의 rerendering 구성 요소를 트리거하는 것 처럼 rerender가 자동으로 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-285">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="c3e38-286">`EventCallback`및 `EventCallback<T>` 는 비동기 대리자를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-286">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="c3e38-287">`EventCallback<T>`는 강력한 형식이 며 특정 인수 형식이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-287">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="c3e38-288">`EventCallback`는 약하게 형식화 되며 모든 인수 형식을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-288">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="c3e38-289">`EventCallback<T>` <xref:System.Threading.Tasks.Task>를 사용 `EventCallback` 하`InvokeAsync` 여 또는를 호출 하 고 다음을 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-289">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="c3e38-290">이벤트 `EventCallback` 처리 `EventCallback<T>` 및 바인딩 구성 요소 매개 변수에는 및를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-290">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="c3e38-291">`EventCallback<T>` 강력한`EventCallback`형식의를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-291">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="c3e38-292">`EventCallback<T>`구성 요소의 사용자에 게 더 나은 오류 피드백을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-292">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="c3e38-293">다른 UI 이벤트 처리기와 마찬가지로 이벤트 매개 변수를 지정 하는 것은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-293">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="c3e38-294">콜백에 `EventCallback` 전달 된 값이 없는 경우를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-294">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="c3e38-295">구성 요소에 대 한 참조 캡처</span><span class="sxs-lookup"><span data-stu-id="c3e38-295">Capture references to components</span></span>

<span data-ttu-id="c3e38-296">구성 요소 참조는 또는 `Show` `Reset`와 같은 해당 인스턴스에 대해 명령을 실행할 수 있도록 구성 요소 인스턴스를 참조 하는 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-296">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="c3e38-297">구성 요소 참조를 캡처하려면:</span><span class="sxs-lookup"><span data-stu-id="c3e38-297">To capture a component reference:</span></span>

* <span data-ttu-id="c3e38-298">자식 구성 요소에 [특성을추가합니다.@ref](xref:mvc/views/razor#ref)</span><span class="sxs-lookup"><span data-stu-id="c3e38-298">Add an [@ref](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="c3e38-299">자식 구성 요소와 동일한 유형으로 필드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-299">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="c3e38-300">구성 요소가 렌더링 되 면 필드는 `loginDialog` `MyLoginDialog` 자식 구성 요소 인스턴스로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-300">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="c3e38-301">그런 다음 구성 요소 인스턴스에서 .NET 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-301">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c3e38-302">변수 `loginDialog` 는 구성 요소가 렌더링 된 후에만 채워지고 출력에는 `MyLoginDialog` 요소가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-302">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="c3e38-303">이 시점까지 참조할 항목이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-303">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="c3e38-304">구성 요소에서 렌더링을 완료 한 후에 구성 요소 참조를 `OnAfterRenderAsync` 조작 `OnAfterRender` 하려면 또는 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-304">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="c3e38-305">구성 요소 참조를 캡처하는 것은 [요소 참조를 캡처하](xref:blazor/javascript-interop#capture-references-to-elements)는 데 유사한 구문을 사용 하지만 [JavaScript interop](xref:blazor/javascript-interop) 기능은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-305">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="c3e38-306">구성 요소 참조는 JavaScript 코드로&mdash;전달 되지 않으며 .net 코드 에서만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-306">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="c3e38-307">구성 요소 참조를 사용 하 여 자식 구성 요소의 상태를 변경할 수 **없습니다** .</span><span class="sxs-lookup"><span data-stu-id="c3e38-307">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="c3e38-308">대신, 일반 선언적 매개 변수를 사용 하 여 자식 구성 요소에 데이터를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-308">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="c3e38-309">일반적인 선언적 매개 변수를 사용 하면 자식 구성 요소가 자동으로 올바른 시간에 rerender 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-309">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="c3e38-310">외부에서 구성 요소 메서드를 호출 하 여 상태 업데이트</span><span class="sxs-lookup"><span data-stu-id="c3e38-310">Invoke component methods externally to update state</span></span>

<span data-ttu-id="c3e38-311">Blazor는를 `SynchronizationContext` 사용 하 여 단일 논리적 실행 스레드를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-311">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="c3e38-312">구성 요소의 수명 주기 메서드 및 Blazor에 의해 발생 하는 모든 이벤트 콜백이이 `SynchronizationContext`에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-312">A component's lifecycle methods and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="c3e38-313">외부 이벤트 (예: 타이머 또는 다른 알림)를 기반으로 구성 요소를 업데이트 해야 하는 경우 Blazor의 `InvokeAsync` `SynchronizationContext`로 디스패치할 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-313">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="c3e38-314">예를 들어 업데이트 된 상태의 수신 구성 요소를 알릴 수 있는 알림 *서비스* 를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-314">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

    public event Action<string, int, Task> Notify;
}
```

<span data-ttu-id="c3e38-315">을 사용 하 `NotifierService` 여 구성 요소를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-315">Usage of the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="c3e38-316">앞의 예제 `NotifierService` 에서는 Blazor의 외부에 `OnNotify` 있는 구성 요소의 `SynchronizationContext`메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-316">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="c3e38-317">`InvokeAsync`는 올바른 컨텍스트로 전환 하 고 렌더링을 큐에 대기 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-317">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="c3e38-318">키 \@를 사용 하 여 요소 및 구성 요소 유지 관리</span><span class="sxs-lookup"><span data-stu-id="c3e38-318">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="c3e38-319">요소 또는 구성 요소 목록을 렌더링할 때 이후에 요소나 구성 요소가 변경 되는 경우 Blazor의 diff 알고리즘은 유지할 수 있는 이전 요소 또는 구성 요소와 모델 개체가 이러한 요소에 매핑되는 방법을 결정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-319">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="c3e38-320">일반적으로이 프로세스는 자동 이며 무시 해도 되지만 프로세스를 제어 하는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-320">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="c3e38-321">다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c3e38-321">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="c3e38-322">`People` 컬렉션의 콘텐츠는 삽입, 삭제 또는 다시 정렬 된 항목으로 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-322">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="c3e38-323">구성 요소가 렌더링 때 구성 요소 `<DetailsEditor>` 는 다른 `Details` 매개 변수 값을 받도록 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-323">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="c3e38-324">이로 인해 예상 보다 더 복잡 한 rerendering 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-324">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="c3e38-325">경우에 따라 rerendering는 손실 요소 포커스와 같은 표시 되는 동작 차이를 일으킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-325">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="c3e38-326">매핑 프로세스는 `@key` 지시어 특성을 사용 하 여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-326">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="c3e38-327">`@key`diff 알고리즘에서 키의 값에 따라 요소 또는 구성 요소의 유지를 보장 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-327">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="@person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="c3e38-328">컬렉션이 변경 되 면 diff 알고리즘은 인스턴스 및 `person` 인스턴스 간 `<DetailsEditor>` 연결을 유지 합니다. `People`</span><span class="sxs-lookup"><span data-stu-id="c3e38-328">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="c3e38-329">이 목록에서 삭제 되 면 해당 `<DetailsEditor>` 인스턴스만 UI에서 제거 됩니다. `People` `Person`</span><span class="sxs-lookup"><span data-stu-id="c3e38-329">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="c3e38-330">다른 인스턴스는 변경 되지 않은 상태로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-330">Other instances are left unchanged.</span></span>
* <span data-ttu-id="c3e38-331">가 목록의 특정 위치에 삽입 되 면 해당 위치에 하나의 새 `<DetailsEditor>` 인스턴스가 삽입 됩니다. `Person`</span><span class="sxs-lookup"><span data-stu-id="c3e38-331">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="c3e38-332">다른 인스턴스는 변경 되지 않은 상태로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-332">Other instances are left unchanged.</span></span>
* <span data-ttu-id="c3e38-333">항목이 `Person` 다시 정렬 되 면 UI에서 해당 `<DetailsEditor>` 인스턴스가 유지 되 고 다시 정렬 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-333">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="c3e38-334">일부 시나리오에서는를 사용 `@key` 하 여 rerendering의 복잡성을 최소화 하 고 DOM의 상태 저장 부분 (예: 포커스 위치)에 대 한 잠재적인 문제를 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-334">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c3e38-335">키는 각 컨테이너 요소 또는 구성 요소에 대해 로컬입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-335">Keys are local to each container element or component.</span></span> <span data-ttu-id="c3e38-336">문서 전체에서 키를 전역적으로 비교 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-336">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="c3e38-337">키를 사용 \@하는 경우</span><span class="sxs-lookup"><span data-stu-id="c3e38-337">When to use \@key</span></span>

<span data-ttu-id="c3e38-338">일반적으로 목록이 렌더링 될 때마다 ( `@key` 예: `@foreach` 블록에서)를 사용 하 고을 정의 하는 데 적합 한 `@key`값을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-338">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="c3e38-339">를 사용 `@key` 하 여 개체가 변경 될 때 Blazor가 요소 또는 구성 요소 하위 트리를 유지 하지 못하게 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-339">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

<span data-ttu-id="c3e38-340">변경 `@currentPerson` 하는 경우 `@key` attribute 지시어는 Blazor에서 전체 `<div>` 및 해당 하위 항목을 삭제 하 고 새 요소 및 구성 요소를 사용 하 여 UI 내에서 하위 트리를 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-340">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="c3e38-341">이는 변경 시 `@currentPerson` UI 상태가 유지 되지 않도록 보장 해야 하는 경우에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-341">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="c3e38-342">키를 사용 \@하지 않는 경우</span><span class="sxs-lookup"><span data-stu-id="c3e38-342">When not to use \@key</span></span>

<span data-ttu-id="c3e38-343">로 diff 하 `@key`는 경우 성능이 저하 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-343">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="c3e38-344">성능 비용은 크지 않지만 요소 또는 구성 요소 유지 `@key` 규칙을 제어 하면 앱이 혜택을 받을 수 있는 경우에만 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-344">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="c3e38-345">를 사용 `@key` 하지 않더라도 Blazor는 자식 요소와 구성 요소 인스턴스를 최대한 많이 보존 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-345">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="c3e38-346">을 사용 `@key` 하는 경우의 유일한 장점은 매핑을 선택 하는 diff 알고리즘 대신 모델 인스턴스가 유지 된 구성 요소 인스턴스에 매핑되 *는 방식을* 제어 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-346">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="c3e38-347">키에 \@사용할 값</span><span class="sxs-lookup"><span data-stu-id="c3e38-347">What values to use for \@key</span></span>

<span data-ttu-id="c3e38-348">일반적으로에 대해 `@key`다음과 같은 종류의 값 중 하나를 제공 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-348">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="c3e38-349">모델 개체 인스턴스 (예: `Person` 이전 예제와 같은 인스턴스)</span><span class="sxs-lookup"><span data-stu-id="c3e38-349">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="c3e38-350">이렇게 하면 개체 참조 일치를 기반으로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-350">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="c3e38-351">고유 식별자 (예:, `int` `string`또는 `Guid`형식의 기본 키 값)입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-351">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="c3e38-352">에 `@key` 사용 되는 값이 충돌 하지 않는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-352">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="c3e38-353">동일한 부모 요소 내에서 충돌 방지 값이 검색 되는 경우 Blazor는 기존 요소나 구성 요소를 새 요소나 구성 요소에 명확 하 게 매핑할 수 없기 때문에 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-353">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="c3e38-354">개체 인스턴스 또는 기본 키 값과 같은 고유 값만 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-354">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="c3e38-355">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="c3e38-355">Lifecycle methods</span></span>

<span data-ttu-id="c3e38-356">`OnInitializedAsync`코드 `OnInitialized` 를 실행 하 여 구성 요소를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-356">`OnInitializedAsync` and `OnInitialized` execute code to initialize the component.</span></span> <span data-ttu-id="c3e38-357">비동기 작업을 수행 하려면 작업에서 `OnInitializedAsync` `await` 및 키워드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-357">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="c3e38-358">동기 작업의 경우 다음을 `OnInitialized`사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-358">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="c3e38-359">`OnParametersSetAsync`및 `OnParametersSet` 는 구성 요소가 부모 로부터 매개 변수를 수신 하 고 값이 속성에 할당 될 때 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-359">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="c3e38-360">이러한 메서드는 구성 요소 초기화 후와 구성 요소가 렌더링 될 때마다 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-360">These methods are executed after component initialization and each time the component is rendered:</span></span>

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

<span data-ttu-id="c3e38-361">`OnAfterRenderAsync`및 `OnAfterRender` 는 구성 요소 렌더링을 완료 한 후에 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-361">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="c3e38-362">요소 및 구성 요소 참조가이 시점에 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-362">Element and component references are populated at this point.</span></span> <span data-ttu-id="c3e38-363">렌더링 된 DOM 요소에 대해 작동 하는 타사 JavaScript 라이브러리 활성화와 같이 렌더링 된 콘텐츠를 사용 하 여 추가 초기화 단계를 수행 하려면이 단계를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-363">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="c3e38-364">렌더링 시 불완전 한 비동기 작업 처리</span><span class="sxs-lookup"><span data-stu-id="c3e38-364">Handle incomplete async actions at render</span></span>

<span data-ttu-id="c3e38-365">수명 주기 이벤트에서 수행 되는 비동기 작업은 구성 요소를 렌더링 하기 전에 완료 되지 않았을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-365">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="c3e38-366">수명 주기 메서드 `null` 를 실행 하는 동안 개체가 데이터로 채워지지 않거나 불완전 하 게 채워질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-366">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="c3e38-367">개체가 초기화 되었는지 확인 하는 렌더링 논리를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-367">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="c3e38-368">개체를 로드 하는 동안 자리 표시자 UI 요소 (예: 로드 메시지 `null`)를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-368">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="c3e38-369">Blazor 템플릿의 구성 요소에서`forecasts` 는비동기적receive예측데이터()로재정의됩니다.`OnInitializedAsync` `FetchData`</span><span class="sxs-lookup"><span data-stu-id="c3e38-369">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="c3e38-370">가 이면 `null`사용자에 게 로드 메시지가 표시 됩니다. `forecasts`</span><span class="sxs-lookup"><span data-stu-id="c3e38-370">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="c3e38-371">`Task` 에서`OnInitializedAsync` 반환 된이 완료 되 면 구성 요소는 업데이트 된 상태를 사용 하 여 다시 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-371">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="c3e38-372">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="c3e38-372">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="c3e38-373">매개 변수를 설정 하기 전에 코드를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-373">Execute code before parameters are set</span></span>

<span data-ttu-id="c3e38-374">`SetParameters`매개 변수를 설정 하기 전에 코드를 실행 하도록 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-374">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="c3e38-375">가 `base.SetParameters` 호출 되지 않으면 사용자 지정 코드는 필요한 방식으로 들어오는 매개 변수 값을 해석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-375">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="c3e38-376">예를 들어 클래스의 속성에는 들어오는 매개 변수를 할당할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-376">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="c3e38-377">UI 새로 고침 안 함</span><span class="sxs-lookup"><span data-stu-id="c3e38-377">Suppress refreshing of the UI</span></span>

<span data-ttu-id="c3e38-378">`ShouldRender`UI를 새로 고치지 않도록 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-378">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="c3e38-379">구현에서을 반환 `true`하면 UI가 새로 고쳐집니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-379">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="c3e38-380">가 재정의 `ShouldRender` 된 경우에도 구성 요소가 항상 처음에 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-380">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="c3e38-381">IDisposable을 사용한 구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="c3e38-381">Component disposal with IDisposable</span></span>

<span data-ttu-id="c3e38-382">구성 요소가을 구현 <xref:System.IDisposable>하는 경우 UI에서 구성 요소가 제거 되 면 [Dispose 메서드가](/dotnet/standard/garbage-collection/implementing-dispose) 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-382">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="c3e38-383">다음 구성 요소는 `@implements IDisposable` `Dispose` 및 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-383">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

## <a name="routing"></a><span data-ttu-id="c3e38-384">라우팅</span><span class="sxs-lookup"><span data-stu-id="c3e38-384">Routing</span></span>

<span data-ttu-id="c3e38-385">Blazor의 라우팅은 앱에서 액세스 가능한 각 구성 요소에 경로 템플릿을 제공 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-385">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="c3e38-386">`@page` 지시문을 사용 하는 Razor 파일이 컴파일되면 생성 된 클래스에 경로 템플릿을 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 지정 하는이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-386">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="c3e38-387">런타임에 라우터는를 `RouteAttribute` 사용 하 여 구성 요소 클래스를 검색 하 고 요청 된 URL과 일치 하는 경로 템플릿을 포함 하는 구성 요소를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-387">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="c3e38-388">여러 경로 템플릿을 구성 요소에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-388">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="c3e38-389">다음 구성 요소는 및 `/BlazorRoute` `/DifferentBlazorRoute`에 대 한 요청에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-389">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="c3e38-390">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="c3e38-390">Route parameters</span></span>

<span data-ttu-id="c3e38-391">구성 요소는 `@page` 지시문에 제공 된 경로 템플릿에서 경로 매개 변수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-391">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="c3e38-392">라우터는 경로 매개 변수를 사용 하 여 해당 하는 구성 요소 매개 변수를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-392">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="c3e38-393">*경로 매개 변수 구성 요소*:</span><span class="sxs-lookup"><span data-stu-id="c3e38-393">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="c3e38-394">선택적 매개 변수는 지원 되지 않으므로 `@page` 위의 예제에서 두 개의 지시문이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-394">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="c3e38-395">첫 번째는 매개 변수 없이 구성 요소에 대 한 탐색을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-395">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="c3e38-396">두 번째 `@page` 지시문은 `{text}` 경로 매개 변수를 사용 하 여 값을 `Text` 속성에 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-396">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="c3e38-397">"코드 숨김으로" 환경에 대 한 기본 클래스 상속</span><span class="sxs-lookup"><span data-stu-id="c3e38-397">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="c3e38-398">구성 요소 파일은 동일한 파일 C# 의 HTML 태그와 처리 코드를 혼합 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-398">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="c3e38-399">지시문 `@inherits` 을 사용 하 여 Blazor apps에 구성 요소 태그를 처리 코드에서 분리 하는 "코드를 사용 하는" 환경을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-399">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="c3e38-400">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) 은 구성 요소가 기본 클래스인 `BlazorRocksBase`를 상속 하 여 구성 요소의 속성과 메서드를 제공 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-400">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="c3e38-401">*Pages/BlazorRocks*:</span><span class="sxs-lookup"><span data-stu-id="c3e38-401">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="c3e38-402">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3e38-402">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="c3e38-403">기본 클래스는에서 `ComponentBase`파생 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-403">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="c3e38-404">구성 요소 가져오기</span><span class="sxs-lookup"><span data-stu-id="c3e38-404">Import components</span></span>

<span data-ttu-id="c3e38-405">Razor로 작성 된 구성 요소의 네임 스페이스는 다음을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-405">The namespace of a component authored with Razor is based on:</span></span>

* <span data-ttu-id="c3e38-406">프로젝트의 `RootNamespace`입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-406">The project's `RootNamespace`.</span></span>
* <span data-ttu-id="c3e38-407">프로젝트 루트에서 구성 요소로의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-407">The path from the project root to the component.</span></span> <span data-ttu-id="c3e38-408">예를 들어 `ComponentsSample/Pages/Index.razor` 는 네임 스페이스 `ComponentsSample.Pages`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-408">For example, `ComponentsSample/Pages/Index.razor` is in the namespace `ComponentsSample.Pages`.</span></span> <span data-ttu-id="c3e38-409">구성 요소 C# 는 이름 바인딩 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-409">Components follow C# name binding rules.</span></span> <span data-ttu-id="c3e38-410">*인덱스 razor*의 경우 동일한 폴더, *페이지*및 부모 폴더인 *ComponentsSample*의 모든 구성 요소가 범위에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-410">In the case of *Index.razor*, all components in the same folder, *Pages*, and the parent folder, *ComponentsSample*, are in scope.</span></span>

<span data-ttu-id="c3e38-411">다른 네임 스페이스에 정의 된 구성 요소는 Razor의 [ \@using](xref:mvc/views/razor#using) 지시문을 사용 하 여 범위로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-411">Components defined in a different namespace can be brought into scope using Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="c3e38-412">폴더 `NavMenu.razor` `Index.razor` `@using` 에 다른 구성 요소인가 있는 경우에서 다음 문을 사용 하 여 구성 요소를 사용할 수 있습니다. `ComponentsSample/Shared/`</span><span class="sxs-lookup"><span data-stu-id="c3e38-412">If another component, `NavMenu.razor`, exists in the folder `ComponentsSample/Shared/`, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="c3e38-413">구성 요소는 정규화 된 이름을 사용 하 여 참조할 수도 있으므로 [ \@using](xref:mvc/views/razor#using) 지시문이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-413">Components can also be referenced using their fully qualified names, which removes the need for the [\@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="c3e38-414">한정자 `global::` 는 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-414">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="c3e38-415">별칭이 `using` 지정 된 문 ( `@using Foo = Bar`예:)을 사용 하 여 가져올 구성 요소가 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-415">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="c3e38-416">부분적으로 정규화 된 이름은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-416">Partially qualified names aren't supported.</span></span> <span data-ttu-id="c3e38-417">예를 들어를 `@using ComponentsSample` 추가 하 `NavMenu.razor` 고 `<Shared.NavMenu></Shared.NavMenu>` 를 참조 하는 것은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-417">For example, adding `@using ComponentsSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="c3e38-418">조건부 HTML 요소 특성</span><span class="sxs-lookup"><span data-stu-id="c3e38-418">Conditional HTML element attributes</span></span>

<span data-ttu-id="c3e38-419">HTML 요소 특성은 .NET 값에 따라 조건부로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-419">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="c3e38-420">값이 `false` 또는 `null`이면 특성이 렌더링 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-420">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="c3e38-421">값이 `true`이면 특성이 최소화 된 상태로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-421">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="c3e38-422">다음 예제에서 `checked` 는가 `IsCompleted` 요소의 태그에서 렌더링 되는지 여부를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-422">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="c3e38-423">`IsCompleted` 가`true`이면 확인란은 다음과 같이 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-423">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="c3e38-424">`IsCompleted` 가`false`이면 확인란은 다음과 같이 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-424">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="c3e38-425">자세한 내용은 <xref:mvc/views/razor>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c3e38-425">For more information, see <xref:mvc/views/razor>.</span></span>

## <a name="raw-html"></a><span data-ttu-id="c3e38-426">원시 HTML</span><span class="sxs-lookup"><span data-stu-id="c3e38-426">Raw HTML</span></span>

<span data-ttu-id="c3e38-427">일반적으로 문자열은 DOM 텍스트 노드를 사용 하 여 렌더링 됩니다. 즉, 포함 될 수 있는 모든 태그는 무시 되 고 리터럴 텍스트로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-427">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="c3e38-428">원시 html을 렌더링 하려면 html 콘텐츠 `MarkupString` 를 값으로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-428">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="c3e38-429">값은 HTML 또는 SVG로 구문 분석 되 고 DOM에 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-429">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="c3e38-430">신뢰할 수 없는 소스에서 생성 된 원시 HTML을 렌더링 하면 **보안상 위험할** 수 있으므로 피해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-430">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="c3e38-431">다음 예제에서는 `MarkupString` 형식을 사용 하 여 정적 HTML 콘텐츠 블록을 구성 요소의 렌더링 된 출력에 추가 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-431">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="c3e38-432">템플릿 기반 구성 요소</span><span class="sxs-lookup"><span data-stu-id="c3e38-432">Templated components</span></span>

<span data-ttu-id="c3e38-433">템플릿 기반 구성 요소는 하나 이상의 UI 템플릿을 매개 변수로 허용 하는 구성 요소 이며,이 구성 요소는 구성 요소의 렌더링 논리의 일부로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-433">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="c3e38-434">템플릿 기반 구성 요소를 사용 하면 일반 구성 요소 보다 다시 사용할 수 있는 상위 수준 구성 요소를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-434">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="c3e38-435">몇 가지 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-435">A couple of examples include:</span></span>

* <span data-ttu-id="c3e38-436">사용자가 테이블의 머리글, 행 및 바닥글에 대 한 템플릿을 지정 하는 데 사용할 수 있는 테이블 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-436">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="c3e38-437">사용자가 목록에서 항목을 렌더링 하기 위한 템플릿을 지정할 수 있도록 하는 목록 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-437">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="c3e38-438">템플릿 매개 변수</span><span class="sxs-lookup"><span data-stu-id="c3e38-438">Template parameters</span></span>

<span data-ttu-id="c3e38-439">템플릿 기반 구성 요소는 또는 `RenderFragment` `RenderFragment<T>`형식의 구성 요소 매개 변수를 하나 이상 지정 하 여 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-439">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="c3e38-440">렌더링 조각은 렌더링할 UI 세그먼트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-440">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="c3e38-441">`RenderFragment<T>`렌더링 조각이 호출 될 때 지정할 수 있는 형식 매개 변수를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-441">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="c3e38-442">`TableTemplate`구성 요소</span><span class="sxs-lookup"><span data-stu-id="c3e38-442">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="c3e38-443">템플릿 기반 구성 요소를 사용 하는 경우 매개 변수의 이름과 일치 하는 자식 요소 (`TableHeader` 및 `RowTemplate` 다음 예제에서는)를 사용 하 여 템플릿 매개 변수를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-443">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
<TableTemplate Items="@pets">
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

### <a name="template-context-parameters"></a><span data-ttu-id="c3e38-444">템플릿 컨텍스트 매개 변수</span><span class="sxs-lookup"><span data-stu-id="c3e38-444">Template context parameters</span></span>

<span data-ttu-id="c3e38-445">요소로 전달 된 형식의 `RenderFragment<T>` 구성 요소 인수에는 라는 `context` 암시적 매개 변수가 있지만 (예: 앞의 코드 샘플 `@context.PetId`에서), 자식에 있는 특성을 `Context` 사용 하 여 매개 변수 이름을 변경할 수 있습니다. 요소인.</span><span class="sxs-lookup"><span data-stu-id="c3e38-445">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="c3e38-446">다음 예제에서 `Context` 요소의 특성은 `RowTemplate` 매개 변수를 `pet` 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-446">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
<TableTemplate Items="@pets">
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

<span data-ttu-id="c3e38-447">또는 구성 요소 요소에 특성 `Context` 을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-447">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="c3e38-448">지정 된 `Context` 특성은 모든 지정 된 템플릿 매개 변수에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-448">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="c3e38-449">이는 자식 요소를 래핑하는 대신 암시적 자식 콘텐츠에 대 한 콘텐츠 매개 변수 이름을 지정 하려는 경우에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-449">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="c3e38-450">다음 예제에서 특성은 `Context` `TableTemplate` 요소에 나타나고 모든 템플릿 매개 변수에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-450">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
<TableTemplate Items="@pets" Context="pet">
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

### <a name="generic-typed-components"></a><span data-ttu-id="c3e38-451">제네릭 형식의 구성 요소</span><span class="sxs-lookup"><span data-stu-id="c3e38-451">Generic-typed components</span></span>

<span data-ttu-id="c3e38-452">일반적으로 템플릿 구성 요소는 형식화 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-452">Templated components are often generically typed.</span></span> <span data-ttu-id="c3e38-453">예를 들어, 제네릭 `ListViewTemplate` 구성 요소를 사용 하 여 `IEnumerable<T>` 값을 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-453">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="c3e38-454">제네릭 구성 요소를 정의 하려면 `@typeparam` 지시문을 사용 하 여 형식 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-454">To define a generic component, use the `@typeparam` directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="c3e38-455">제네릭 형식의 구성 요소를 사용 하는 경우 가능 하면 형식 매개 변수가 유추 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-455">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="c3e38-456">그렇지 않은 경우 형식 매개 변수의 이름과 일치 하는 특성을 사용 하 여 형식 매개 변수를 명시적으로 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-456">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="c3e38-457">다음 예제 `TItem="Pet"` 에서는 유형을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-457">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="c3e38-458">연계 값 및 매개 변수</span><span class="sxs-lookup"><span data-stu-id="c3e38-458">Cascading values and parameters</span></span>

<span data-ttu-id="c3e38-459">일부 시나리오에서는 [구성 요소 매개 변수](#component-parameters)를 사용 하 여 상위 구성 요소에서 하위 구성 요소로 데이터를 이동 하는 것이 불편 합니다. 특히 구성 요소 계층이 여러 개 있는 경우에는 더욱 그렇습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-459">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="c3e38-460">연계 값 및 매개 변수는 상위 구성 요소에서 모든 하위 구성 요소에 값을 제공 하는 편리한 방법을 제공 하 여이 문제를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-460">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="c3e38-461">연계 값 및 매개 변수를 사용 하 여 구성 요소를 조정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-461">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="c3e38-462">테마 예</span><span class="sxs-lookup"><span data-stu-id="c3e38-462">Theme example</span></span>

<span data-ttu-id="c3e38-463">샘플 응용 프로그램의 다음 예제에서 클래스는 `ThemeInfo` 응용 프로그램의 지정 된 부분에 있는 모든 단추가 동일한 스타일을 공유 하도록 구성 요소 계층 구조의 아래쪽으로 이동 하는 테마 정보를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-463">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="c3e38-464">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3e38-464">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="c3e38-465">상위 구성 요소는 연계 값 구성 요소를 사용 하 여 연계 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-465">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="c3e38-466">구성 `CascadingValue` 요소 계층 구조의 하위 트리를 래핑하고 해당 하위 트리 내의 모든 구성 요소에 단일 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-466">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="c3e38-467">예를 들어 샘플 앱은`ThemeInfo` `@Body` 속성의 레이아웃 본문을 구성 하는 모든 구성 요소에 대 한 연계 매개 변수로 앱 레이아웃 중 하나에서 테마 정보 ()를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-467">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="c3e38-468">`ButtonClass`에는 레이아웃 구성 요소 `btn-success` 에 값이 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-468">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="c3e38-469">모든 하위 구성 요소는 연계 개체를 `ThemeInfo` 통해이 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-469">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="c3e38-470">`CascadingValuesParametersLayout`구성 요소</span><span class="sxs-lookup"><span data-stu-id="c3e38-470">`CascadingValuesParametersLayout` component:</span></span>

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
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

<span data-ttu-id="c3e38-471">연계 값을 사용 하기 위해 구성 요소는 `[CascadingParameter]` 특성을 사용 하 여 연계 매개 변수를 선언 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-471">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="c3e38-472">연계 값은 유형별 매개 변수에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-472">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="c3e38-473">샘플 앱에서 구성 요소는 `CascadingValuesParametersTheme` 연계 값을 `ThemeInfo` 연계 매개 변수에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-473">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="c3e38-474">매개 변수는 구성 요소에 의해 표시 되는 단추 중 하나에 대해 CSS 클래스를 설정 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-474">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="c3e38-475">`CascadingValuesParametersTheme`구성 요소</span><span class="sxs-lookup"><span data-stu-id="c3e38-475">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="c3e38-476">동일한 하위 트리 내에서 동일한 형식의 여러 값을 계단식으로 배열 하려면 각 `Name` `CascadingValue` 구성 요소 및 `CascadingParameter`해당에 대 한 고유 문자열을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-476">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="c3e38-477">다음 예제에서 두 `CascadingValue` 구성 요소는 이름으로의 `MyCascadingType` 서로 다른 인스턴스를 계단식으로 배열 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-477">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="c3e38-478">하위 구성 요소에서 종속 매개 변수는 상위 구성 요소의 해당 하는 종속 된 값에서 이름으로 해당 값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-478">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="c3e38-479">TabSet 예제</span><span class="sxs-lookup"><span data-stu-id="c3e38-479">TabSet example</span></span>

<span data-ttu-id="c3e38-480">연계 매개 변수를 사용 하면 구성 요소 계층 구조 전체에서 공동 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-480">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="c3e38-481">예를 들어 샘플 앱에서 다음 *Tabset* 예제를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="c3e38-481">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="c3e38-482">샘플 앱에는 탭 `ITab` 에서 구현 하는 인터페이스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-482">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="c3e38-483">구성 요소는 여러 `TabSet` `Tab` 구성 요소를 포함 하는 구성 요소를 사용 합니다. `CascadingValuesParametersTabSet`</span><span class="sxs-lookup"><span data-stu-id="c3e38-483">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="c3e38-484">자식 `Tab` 구성 요소는에 매개 변수로 `TabSet`명시적으로 전달 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-484">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="c3e38-485">대신 자식 `Tab` 구성 요소는 `TabSet`의 자식 콘텐츠에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-485">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="c3e38-486">그러나는 `TabSet` 헤더와 활성 탭을 렌더링할 수 `Tab` 있도록 각 구성 요소에 대해 알고 있어야 합니다. 추가 코드를 `TabSet` 요구 하지 않고이 조정을 사용 하려면 구성 요소가 하위 `Tab` 구성 요소에 의해 선택 되는 *연계 값으로 자신을 제공할 수 있습니다* .</span><span class="sxs-lookup"><span data-stu-id="c3e38-486">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="c3e38-487">`TabSet`구성 요소</span><span class="sxs-lookup"><span data-stu-id="c3e38-487">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="c3e38-488">하위 구성 요소는 포함 `TabSet` 된를 연계 매개 변수로 캡처하기 때문에 `Tab` 구성 요소는 자신이 활성화 `TabSet` 된 탭과 해당 구성 요소를 자동으로 추가 합니다. `Tab`</span><span class="sxs-lookup"><span data-stu-id="c3e38-488">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="c3e38-489">`Tab`구성 요소</span><span class="sxs-lookup"><span data-stu-id="c3e38-489">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="c3e38-490">Razor 템플릿</span><span class="sxs-lookup"><span data-stu-id="c3e38-490">Razor templates</span></span>

<span data-ttu-id="c3e38-491">렌더링 조각은 Razor 템플릿 구문을 사용 하 여 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-491">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="c3e38-492">Razor 템플릿은 UI 코드 조각을 정의 하 고 다음 형식을 가정 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-492">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="c3e38-493">다음 예제에서는 구성 요소에서 및 `RenderFragment` `RenderFragment<T>` 값을 지정 하 고 템플릿을 직접 렌더링 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-493">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="c3e38-494">렌더링 조각은 [템플릿 기반 구성 요소](#templated-components)에 인수로 전달 될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-494">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

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

<span data-ttu-id="c3e38-495">이전 코드의 렌더링 된 출력:</span><span class="sxs-lookup"><span data-stu-id="c3e38-495">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="c3e38-496">수동 RenderTreeBuilder 논리</span><span class="sxs-lookup"><span data-stu-id="c3e38-496">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="c3e38-497">`Microsoft.AspNetCore.Components.RenderTree`코드에서 C# 구성 요소를 수동으로 빌드하는 등 구성 요소 및 요소를 조작 하기 위한 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-497">`Microsoft.AspNetCore.Components.RenderTree` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="c3e38-498">구성 요소 `RenderTreeBuilder` 를 만드는 데를 사용 하는 것은 고급 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-498">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="c3e38-499">형식이 잘못 된 구성 요소 (예: 닫히지 않은 태그 태그)는 정의 되지 않은 동작을 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-499">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="c3e38-500">다른 구성 요소 `PetDetails` 에 수동으로 빌드할 수 있는 다음 구성 요소를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-500">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="c3e38-501">다음 예제에서 `CreateComponent` 메서드의 루프는 세 가지 `PetDetails` 구성 요소를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-501">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="c3e38-502">메서드를 `RenderTreeBuilder` 호출 하 여 구성 요소 (`OpenComponent` 및 `AddAttribute`)를 만드는 경우 시퀀스 번호는 소스 코드 줄 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-502">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="c3e38-503">Blazor 차이점 알고리즘은 고유 호출 호출이 아닌 개별 코드 줄에 해당 하는 시퀀스 번호를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-503">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="c3e38-504">메서드를 사용 `RenderTreeBuilder` 하 여 구성 요소를 만드는 경우 시퀀스 번호에 대 한 인수를 하드 코딩 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-504">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="c3e38-505">**계산 또는 카운터를 사용 하 여 시퀀스 번호를 생성 하면 성능이 저하 될 수 있습니다.**</span><span class="sxs-lookup"><span data-stu-id="c3e38-505">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="c3e38-506">자세한 내용은 [시퀀스 번호를 코드 줄 번호와 관련 되 고 실행 순서와는 관련이 없습니다](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) . 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c3e38-506">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="c3e38-507">`BuiltContent`구성 요소</span><span class="sxs-lookup"><span data-stu-id="c3e38-507">`BuiltContent` component:</span></span>

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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="c3e38-508">시퀀스 번호는 코드 줄 번호와 관련 되 고 실행 순서와는 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-508">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="c3e38-509">Blazor `.razor` 파일은 항상 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-509">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="c3e38-510">이는 컴파일 단계를 사용 하 `.razor` 여 런타임 시 앱 성능을 향상 시키는 정보를 주입할 수 있기 때문에의 경우에 매우 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-510">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="c3e38-511">이러한 향상 된 기능을 포함 하는 주요 예로 *시퀀스 번호가*있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-511">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="c3e38-512">시퀀스 번호는 코드의 고유 하 고 정렬 된 줄에서 제공 되는 출력을 런타임에 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-512">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="c3e38-513">런타임은이 정보를 사용 하 여 선형 시간에 효율적인 트리 차이을 생성 합니다 .이는 일반적으로 일반적인 트리 diff 알고리즘에 대해 가능한 것 보다 훨씬 빠릅니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-513">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="c3e38-514">다음과 같은 간단한 `.razor` 파일을 고려 하십시오.</span><span class="sxs-lookup"><span data-stu-id="c3e38-514">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="c3e38-515">위의 코드는 다음과 같은 항목으로 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-515">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="c3e38-516">코드가 처음 `someFlag` 실행 될 때가 `true`이면 작성기에서 다음을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-516">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="c3e38-517">Sequence</span><span class="sxs-lookup"><span data-stu-id="c3e38-517">Sequence</span></span> | <span data-ttu-id="c3e38-518">형식</span><span class="sxs-lookup"><span data-stu-id="c3e38-518">Type</span></span>      | <span data-ttu-id="c3e38-519">보기</span><span class="sxs-lookup"><span data-stu-id="c3e38-519">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c3e38-520">0</span><span class="sxs-lookup"><span data-stu-id="c3e38-520">0</span></span>        | <span data-ttu-id="c3e38-521">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="c3e38-521">Text node</span></span> | <span data-ttu-id="c3e38-522">첫째</span><span class="sxs-lookup"><span data-stu-id="c3e38-522">First</span></span>  |
| <span data-ttu-id="c3e38-523">1</span><span class="sxs-lookup"><span data-stu-id="c3e38-523">1</span></span>        | <span data-ttu-id="c3e38-524">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="c3e38-524">Text node</span></span> | <span data-ttu-id="c3e38-525">Second</span><span class="sxs-lookup"><span data-stu-id="c3e38-525">Second</span></span> |

<span data-ttu-id="c3e38-526">`someFlag` 가이되고태그가다시렌더링되는`false`것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-526">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="c3e38-527">이번에는 작성기가 다음을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-527">This time, the builder receives:</span></span>

| <span data-ttu-id="c3e38-528">Sequence</span><span class="sxs-lookup"><span data-stu-id="c3e38-528">Sequence</span></span> | <span data-ttu-id="c3e38-529">형식</span><span class="sxs-lookup"><span data-stu-id="c3e38-529">Type</span></span>       | <span data-ttu-id="c3e38-530">보기</span><span class="sxs-lookup"><span data-stu-id="c3e38-530">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="c3e38-531">1</span><span class="sxs-lookup"><span data-stu-id="c3e38-531">1</span></span>        | <span data-ttu-id="c3e38-532">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="c3e38-532">Text node</span></span>  | <span data-ttu-id="c3e38-533">Second</span><span class="sxs-lookup"><span data-stu-id="c3e38-533">Second</span></span> |

<span data-ttu-id="c3e38-534">런타임에서 diff를 수행 하는 경우 시퀀스 `0` 에 있는 항목이 제거 된 것을 확인 하 여 다음과 같은 trivial *편집 스크립트*를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-534">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="c3e38-535">첫 번째 텍스트 노드를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-535">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="c3e38-536">프로그래밍 방식으로 시퀀스 번호를 생성 하는 경우 문제가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-536">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="c3e38-537">대신 다음과 같은 렌더링 트리 작성기 논리를 작성 했다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-537">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="c3e38-538">이제 첫 번째 출력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-538">Now, the first output is:</span></span>

| <span data-ttu-id="c3e38-539">Sequence</span><span class="sxs-lookup"><span data-stu-id="c3e38-539">Sequence</span></span> | <span data-ttu-id="c3e38-540">형식</span><span class="sxs-lookup"><span data-stu-id="c3e38-540">Type</span></span>      | <span data-ttu-id="c3e38-541">보기</span><span class="sxs-lookup"><span data-stu-id="c3e38-541">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c3e38-542">0</span><span class="sxs-lookup"><span data-stu-id="c3e38-542">0</span></span>        | <span data-ttu-id="c3e38-543">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="c3e38-543">Text node</span></span> | <span data-ttu-id="c3e38-544">첫째</span><span class="sxs-lookup"><span data-stu-id="c3e38-544">First</span></span>  |
| <span data-ttu-id="c3e38-545">1</span><span class="sxs-lookup"><span data-stu-id="c3e38-545">1</span></span>        | <span data-ttu-id="c3e38-546">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="c3e38-546">Text node</span></span> | <span data-ttu-id="c3e38-547">Second</span><span class="sxs-lookup"><span data-stu-id="c3e38-547">Second</span></span> |

<span data-ttu-id="c3e38-548">이 결과는 이전 사례와 동일 하므로 부정적인 문제가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-548">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="c3e38-549">`someFlag`는 두 번째 렌더링 에있고출력은다음과같습니다.`false`</span><span class="sxs-lookup"><span data-stu-id="c3e38-549">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="c3e38-550">Sequence</span><span class="sxs-lookup"><span data-stu-id="c3e38-550">Sequence</span></span> | <span data-ttu-id="c3e38-551">형식</span><span class="sxs-lookup"><span data-stu-id="c3e38-551">Type</span></span>      | <span data-ttu-id="c3e38-552">보기</span><span class="sxs-lookup"><span data-stu-id="c3e38-552">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="c3e38-553">0</span><span class="sxs-lookup"><span data-stu-id="c3e38-553">0</span></span>        | <span data-ttu-id="c3e38-554">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="c3e38-554">Text node</span></span> | <span data-ttu-id="c3e38-555">Second</span><span class="sxs-lookup"><span data-stu-id="c3e38-555">Second</span></span> |

<span data-ttu-id="c3e38-556">이번에는 diff 알고리즘은 *두 가지* 변경이 발생 했음을 확인 하 고 알고리즘은 다음과 같은 편집 스크립트를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-556">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="c3e38-557">첫 번째 텍스트 노드의 값을로 `Second`변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-557">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="c3e38-558">두 번째 텍스트 노드를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-558">Remove the second text node.</span></span>

<span data-ttu-id="c3e38-559">시퀀스 번호를 생성 하면 원래 코드에서 `if/else` 분기와 루프가 있는 위치에 대 한 모든 유용한 정보가 손실 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-559">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="c3e38-560">이로 인해 이전 처럼 비교가 **두 번** 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-560">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="c3e38-561">이는 간단한 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-561">This is a trivial example.</span></span> <span data-ttu-id="c3e38-562">복잡 하 고 많이 중첩 된 구조와 특히 루프를 사용 하는 현실적인 경우에는 성능 비용이 더 심각 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-562">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="c3e38-563">삽입 또는 제거 된 루프 블록 또는 분기를 즉시 식별 하는 대신, diff 알고리즘은 렌더링 트리를 깊이 있게 구분 해야 하며, 일반적으로 이전 및 새 구조를 misinformed 하기 때문에 훨씬 더 긴 편집 스크립트를 작성 해야 합니다. 서로 관련 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-563">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="c3e38-564">지침 및 결론</span><span class="sxs-lookup"><span data-stu-id="c3e38-564">Guidance and conclusions</span></span>

* <span data-ttu-id="c3e38-565">시퀀스 번호가 동적으로 생성 되 면 앱 성능이 저하 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-565">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="c3e38-566">컴파일 시간에 캡처되는 경우를 제외 하 고 필요한 정보가 존재 하지 않기 때문에 프레임 워크는 런타임에 고유한 시퀀스 번호를 자동으로 만들 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-566">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="c3e38-567">수동으로 구현 `RenderTreeBuilder` 된 논리의 긴 블록을 작성 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c3e38-567">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="c3e38-568">파일 `.razor` 을 선호 하 고 컴파일러가 시퀀스 번호를 처리할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-568">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span>
* <span data-ttu-id="c3e38-569">시퀀스 번호가 하드 코딩 된 경우 diff 알고리즘에서는 값이 증가 하는 시퀀스 번호만 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-569">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="c3e38-570">초기 값과 간격은 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-570">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="c3e38-571">한 가지 합법적인 옵션은 코드 줄 번호를 시퀀스 번호로 사용 하거나 0부터 시작 하 여 또는 수백 (또는 선호 하는 간격) 만큼 증가 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-571">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="c3e38-572">Blazor는 시퀀스 번호를 사용 하지만 다른 트리 diff UI 프레임 워크는이를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-572">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="c3e38-573">시퀀스 번호가 사용 되는 경우 diff는 훨씬 더 빠르며, Blazor는 개발자가 파일을 작성 `.razor` 하는 개발자를 위해 자동으로 시퀀스 번호를 처리 하는 컴파일 단계를 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-573">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="localization"></a><span data-ttu-id="c3e38-574">지역화</span><span class="sxs-lookup"><span data-stu-id="c3e38-574">Localization</span></span>

<span data-ttu-id="c3e38-575">Blazor 서버 쪽 앱은 [지역화 미들웨어](xref:fundamentals/localization#localization-middleware)를 사용 하 여 지역화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-575">Blazor server-side apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="c3e38-576">미들웨어는 앱에서 리소스를 요청 하는 사용자에 게 적절 한 문화권을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-576">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="c3e38-577">문화권은 다음 방법 중 하나를 사용 하 여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-577">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="c3e38-578">쿠키</span><span class="sxs-lookup"><span data-stu-id="c3e38-578">Cookies</span></span>](#cookies)
* [<span data-ttu-id="c3e38-579">문화권을 선택 하는 UI 제공</span><span class="sxs-lookup"><span data-stu-id="c3e38-579">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="c3e38-580">자세한 내용과 예제를 보려면 <xref:fundamentals/localization>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c3e38-580">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="c3e38-581">쿠키</span><span class="sxs-lookup"><span data-stu-id="c3e38-581">Cookies</span></span>

<span data-ttu-id="c3e38-582">지역화 문화권 쿠키는 사용자의 문화권을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-582">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="c3e38-583">쿠키는 응용 프로그램의 호스트 `OnGet` 페이지 (*Pages/host-a*)의 메서드에 의해 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-583">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="c3e38-584">지역화 미들웨어는 후속 요청에서 쿠키를 읽어 사용자의 culture를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-584">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="c3e38-585">쿠키를 사용 하면 WebSocket 연결이 문화권을 올바르게 전파할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-585">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="c3e38-586">지역화 체계가 URL 경로 또는 쿼리 문자열을 기반으로 하는 경우 스키마는 Websocket을 사용 하지 못할 수 있으므로 문화권이 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-586">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="c3e38-587">따라서 지역화 문화권 쿠키를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-587">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="c3e38-588">문화권이 지역화 쿠키에 유지 되는 경우 모든 기술을 사용 하 여 문화권을 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-588">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="c3e38-589">서버 쪽 ASP.NET Core에 대해 설정 된 지역화 체계가 앱에 이미 있는 경우 앱의 기존 지역화 인프라를 계속 사용 하 고 앱의 체계 내에서 지역화 문화권 쿠키를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-589">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="c3e38-590">다음 예제에서는 지역화 미들웨어에서 읽을 수 있는 쿠키의 현재 문화권을 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-590">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="c3e38-591">Blazor 서버 쪽 앱에서 다음 내용을 사용 하 여 *Pages/Host. cshtml* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-591">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor server-side app:</span></span>

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

<span data-ttu-id="c3e38-592">지역화는 앱에서 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-592">Localization is handled in the app:</span></span>

1. <span data-ttu-id="c3e38-593">브라우저가 앱에 초기 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-593">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="c3e38-594">문화권이 지역화 미들웨어에 의해 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-594">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="c3e38-595">_Host `OnGet` 의 메서드 는 응답의 일부로 쿠키의 문화권을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-595">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="c3e38-596">브라우저는 WebSocket 연결을 열어 대화형 Blazor 서버 쪽 세션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-596">The browser opens a WebSocket connection to create an interactive Blazor server-side session.</span></span>
1. <span data-ttu-id="c3e38-597">지역화 미들웨어는 쿠키를 읽고 문화권을 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-597">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="c3e38-598">Blazor 서버 쪽 세션이 올바른 문화권으로 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-598">The Blazor server-side session begins with the correct culture.</span></span>

## <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="c3e38-599">문화권을 선택 하는 UI 제공</span><span class="sxs-lookup"><span data-stu-id="c3e38-599">Provide UI to choose the culture</span></span>

<span data-ttu-id="c3e38-600">사용자가 문화권을 선택할 수 있도록 UI를 제공 하려면 *리디렉션 기반 방법을* 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-600">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="c3e38-601">이 프로세스는 사용자가 보안 리소스&mdash;에 액세스 하려고 할 때 사용자가 로그인 페이지로 리디렉션되고 다시 원래 리소스로 리디렉션되는 경우와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-601">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="c3e38-602">앱은 컨트롤러에 대 한 리디렉션을 통해 사용자가 선택한 문화권을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-602">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="c3e38-603">컨트롤러는 사용자가 선택한 문화권을 쿠키로 설정 하 고 사용자를 다시 원래 URI로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-603">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="c3e38-604">서버에서 HTTP 끝점을 설정 하 여 사용자가 선택한 문화권을 쿠키에 설정 하 고 다시 원래 URI로 리디렉션을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-604">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="c3e38-605">`LocalRedirect` 작업 결과를 사용 하 여 열린 리디렉션 공격을 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-605">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="c3e38-606">자세한 내용은 <xref:security/preventing-open-redirects>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c3e38-606">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="c3e38-607">다음 구성 요소는 사용자가 문화권을 선택할 때 초기 리디렉션을 수행 하는 방법의 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-607">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

    private void OnSelected(UIChangeEventArgs e)
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

### <a name="use-net-localization-scenarios-in-blazor-apps"></a><span data-ttu-id="c3e38-608">Blazor apps에서 .NET 지역화 시나리오 사용</span><span class="sxs-lookup"><span data-stu-id="c3e38-608">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="c3e38-609">Blazor apps 내에서 다음과 같은 .NET 지역화 및 세계화 시나리오를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-609">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="c3e38-610">. NET의 리소스 시스템</span><span class="sxs-lookup"><span data-stu-id="c3e38-610">.NET's resources system</span></span>
* <span data-ttu-id="c3e38-611">문화권별 숫자 및 날짜 형식 지정</span><span class="sxs-lookup"><span data-stu-id="c3e38-611">Culture-specific number and date formatting</span></span>

<span data-ttu-id="c3e38-612">Blazor의 `@bind` 기능은 사용자의 현재 문화권에 따라 세계화를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-612">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="c3e38-613">자세한 내용은 [데이터 바인딩](#data-binding) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c3e38-613">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="c3e38-614">제한 된 ASP.NET Core의 지역화 시나리오 집합이 현재 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-614">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="c3e38-615">`IStringLocalizer<>`는 Blazor apps에서 *지원 됩니다* .</span><span class="sxs-lookup"><span data-stu-id="c3e38-615">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="c3e38-616">`IHtmlLocalizer<>`, `IViewLocalizer<>`및 데이터 주석 지역화는 MVC 시나리오 ASP.NET Core Blazor apps에서 **지원 되지 않습니다** .</span><span class="sxs-lookup"><span data-stu-id="c3e38-616">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="c3e38-617">자세한 내용은 <xref:fundamentals/localization>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c3e38-617">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="c3e38-618">SVG (스케일러블 벡터 그래픽) 이미지</span><span class="sxs-lookup"><span data-stu-id="c3e38-618">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="c3e38-619">Blazor는 HTML을 렌더링 하므로 svg (확장 가능한 벡터 그래픽) 이미지 (*svg*)를 비롯 한 브라우저 지원 이미지가 `<img>` 태그를 통해 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-619">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="c3e38-620">마찬가지로, 스타일 시트 파일 ( *.css*)의 css 규칙에서 SVG 이미지가 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-620">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="c3e38-621">그러나 인라인 SVG 태그는 일부 시나리오에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-621">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="c3e38-622">`<svg>` 태그를 구성 요소 파일 (*razor*)에 직접 저장 하는 경우 기본 이미지 렌더링이 지원 되지만 많은 고급 시나리오가 아직 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-622">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="c3e38-623">예를 들어 `<use>` 태그는 현재 적용 되지 않으며 `@bind` 일부 SVG 태그와 함께 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-623">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="c3e38-624">향후 릴리스에서 이러한 제한을 해결할 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="c3e38-624">We expect to address these limitations in a future release.</span></span>
