---
title: ASP.NET Core Razor 구성 요소 만들기 및 사용
author: guardrex
description: 데이터에 바인딩하고, 이벤트를 처리 하 고, 구성 요소 수명 주기를 관리 하는 방법을 비롯 하 여 Razor 구성 요소를 만들고 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/28/2019
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: e73667925c04dd1b2360138343c4a2dcef0ee310
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160017"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="5957a-103">ASP.NET Core Razor 구성 요소 만들기 및 사용</span><span class="sxs-lookup"><span data-stu-id="5957a-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="5957a-104">By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5957a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="5957a-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5957a-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="5957a-106"> 앱은 *구성 요소*를 사용 하 여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-106"> apps are built using *components*.</span></span> <span data-ttu-id="5957a-107">구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI (사용자 인터페이스)의 자체 포함 청크입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="5957a-108">구성 요소는 데이터를 삽입 하거나 UI 이벤트에 응답 하는 데 필요한 HTML 태그와 처리 논리를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="5957a-109">구성 요소는 유연 하 고 간단 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="5957a-110">프로젝트 간에 중첩, 재사용 및 공유 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="5957a-111">구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="5957a-111">Component classes</span></span>

<span data-ttu-id="5957a-112">구성 요소는 및 HTML 태그의 C# 조합을 사용 하 여 [razor](xref:mvc/views/razor) 구성 요소 파일 (*razor*)에서 구현 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="5957a-113">Blazor의 구성 요소는 *Razor 구성 요소*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="5957a-114">구성 요소의 이름은 대문자로 시작 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="5957a-115">예를 들어 *MyCoolComponent* 는 유효 하며 *MyCoolComponent* 은 유효 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="5957a-116">구성 요소에 대 한 UI는 HTML을 사용 하 여 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="5957a-117">동적 렌더링 논리(예: 루프, 조건, 식)는 [Razor](xref:mvc/views/razor)라는 포함된 C# 구문을 사용하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="5957a-118">앱이 컴파일되면 HTML 태그 및 C# 렌더링 논리가 구성 요소 클래스로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="5957a-119">생성 된 클래스의 이름은 파일 이름과 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="5957a-120">구성 요소 클래스의 멤버는 `@code` 블록에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="5957a-121">`@code` 블록에서 구성 요소 상태 (속성, 필드)는 이벤트 처리를 위한 메서드나 다른 구성 요소 논리를 정의 하는 데 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="5957a-122">두 개 이상의 `@code` 블록이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="5957a-123">구성 요소 멤버는 `@`로 시작 하는 식을 사용 하 여 C# 구성 요소의 렌더링 논리의 일부로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="5957a-124">예를 들어 필드 C# 는 필드 이름에 `@`을 접두사로 하 여 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="5957a-125">다음 예에서는를 평가 하 고 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="5957a-126">`font-style`에 대 한 CSS 속성 값을 `_headingFontStyle` 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="5957a-127">`<h1>` 요소의 내용에 `_headingText` 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="5957a-128">구성 요소가 처음 렌더링 되 면 구성 요소는 이벤트에 대 한 응답으로 렌더링 트리를 다시 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="5957a-129">그런 다음 Blazor 새 렌더링 트리를 이전 렌더링 트리와 비교 하 여 브라우저의 문서 개체 모델 (DOM)에 수정 사항을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-129">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="5957a-130">구성 요소는 C# 일반 클래스 이며 프로젝트 내의 어느 위치에 나 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="5957a-131">웹 페이지를 생성 하는 구성 요소는 일반적으로 *Pages* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="5957a-132">페이지를 지정 하지 않는 구성 요소는 일반적으로 *공유* 폴더 또는 프로젝트에 추가 된 사용자 지정 폴더에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="5957a-133">일반적으로 구성 요소의 네임 스페이스는 앱의 루트 네임 스페이스와 앱 내의 구성 요소 위치 (폴더)에서 파생 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="5957a-134">응용 프로그램의 루트 네임 스페이스가 `BlazorApp` 고 `Counter` 구성 요소가 *Pages* 폴더에 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="5957a-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="5957a-135">`Counter` 구성 요소의 네임 스페이스가 `BlazorApp.Pages`되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="5957a-136">구성 요소의 정규화 된 형식 이름이 `BlazorApp.Pages.Counter`입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="5957a-137">자세한 내용은 [구성 요소 가져오기](#import-components) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="5957a-138">사용자 지정 폴더를 사용 하려면 부모 구성 요소나 앱의 *_Imports razor* 파일에 사용자 지정 폴더의 네임 스페이스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="5957a-139">예를 들어 다음 네임 스페이스는 응용 프로그램의 루트 네임 스페이스가 `BlazorApp`때 *구성 요소* 폴더의 구성 요소를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="5957a-140">Razor Pages 및 MVC 앱에 구성 요소 통합</span><span class="sxs-lookup"><span data-stu-id="5957a-140">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="5957a-141">Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-141">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="5957a-142">페이지나 보기가 렌더링 되 면 구성 요소를 동시에 미리 렌더링 된 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-142">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="5957a-143">Razor 구성 요소를 호스팅하도록 Razor Pages 또는 MVC 앱을 준비 하려면 <xref:blazor/hosting-models#integrate-razor-components-into-razor-pages-and-mvc-apps> 문서의 *Razor Pages 및 mvc 앱에 razor 구성 요소 통합* 섹션의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-143">To prepare a Razor Pages or MVC app to host Razor components, follow the guidance in the *Integrate Razor components into Razor Pages and MVC apps* section of the <xref:blazor/hosting-models#integrate-razor-components-into-razor-pages-and-mvc-apps> article.</span></span>

<span data-ttu-id="5957a-144">사용자 지정 폴더를 사용 하 여 앱의 구성 요소를 저장 하는 경우 폴더를 나타내는 네임 스페이스를 페이지/보기 또는 _ViewImports로 추가 합니다. *cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="5957a-144">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="5957a-145">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="5957a-145">In the following example:</span></span>

* <span data-ttu-id="5957a-146">`MyAppNamespace`를 앱의 네임 스페이스로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-146">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="5957a-147">구성 요소 라는 폴더를 사용 하 여 구성 요소를 저장 *하지 않은 경우* 에는 구성 요소가 상주 하는 폴더로 `Components`를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-147">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```csharp
@using MyAppNamespace.Components
```

<span data-ttu-id="5957a-148">*_ViewImports cshtml* 파일은 Razor Pages 앱의 *PAGES* 폴더 또는 MVC 앱의 *Views* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-148">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="5957a-149">페이지 또는 뷰에서 구성 요소를 렌더링 하려면 `Component` 태그 도우미를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-149">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="5957a-150">매개 변수 전달 (예: 앞의 예제에서 `IncrementAmount`)은 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-150">Passing parameters (for example, `IncrementAmount` in the preceding example) is supported.</span></span>

<span data-ttu-id="5957a-151">구성 요소가 있는지 여부를 구성 하 `RenderMode` 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-151">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="5957a-152">는 페이지에 미리 렌더링 된 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-152">Is prerendered into the page.</span></span>
* <span data-ttu-id="5957a-153">는 페이지에서 정적 HTML로 렌더링 되거나 사용자 에이전트에서 Blazor 앱을 부트스트랩 하는 데 필요한 정보가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-153">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="5957a-154">설명</span><span class="sxs-lookup"><span data-stu-id="5957a-154">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="5957a-155">구성 요소를 정적 HTML로 렌더링 하 고 Blazor Server 앱에 대 한 마커를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-155">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5957a-156">사용자 에이전트가 시작 되 면이 마커는 Blazor 앱을 부트스트랩 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-156">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="5957a-157">Blazor Server 앱에 대 한 마커를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-157">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5957a-158">구성 요소의 출력은 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-158">Output from the component isn't included.</span></span> <span data-ttu-id="5957a-159">사용자 에이전트가 시작 되 면이 마커는 Blazor 앱을 부트스트랩 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-159">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="5957a-160">구성 요소를 정적 HTML로 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-160">Renders the component into static HTML.</span></span> |

<span data-ttu-id="5957a-161">페이지 및 뷰에서 구성 요소를 사용할 수 있지만 반대의 경우는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-161">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="5957a-162">구성 요소는 부분 보기 및 섹션과 같은 보기 및 페이지 관련 시나리오를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-162">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="5957a-163">구성 요소의 부분 뷰에서 논리를 사용 하려면 부분 뷰 논리를 구성 요소에 대 한 요소로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-163">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="5957a-164">정적 HTML 페이지에서 서버 구성 요소를 렌더링 하는 것은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-164">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="5957a-165">구성 요소를 렌더링 하는 방법, 구성 요소 상태 및 `Component` 태그 도우미에 대 한 자세한 내용은 <xref:blazor/hosting-models>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-165">For more information on how components are rendered, component state, and the `Component` Tag Helper, see <xref:blazor/hosting-models>.</span></span>

## <a name="use-components"></a><span data-ttu-id="5957a-166">구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="5957a-166">Use components</span></span>

<span data-ttu-id="5957a-167">구성 요소에는 HTML 요소 구문을 사용 하 여 선언 함으로써 다른 구성 요소가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-167">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="5957a-168">구성 요소 사용을 위한 태그는 태그 이름이 구성 요소 유형인 HTML 태그처럼 보입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-168">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="5957a-169">특성 바인딩은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-169">Attribute binding is case sensitive.</span></span> <span data-ttu-id="5957a-170">예를 들어 `@bind` 유효 하 고 `@Bind` 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-170">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="5957a-171">다음은 *인덱스 razor* 의 다음 태그를 `HeadingComponent` 인스턴스를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-171">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="5957a-172">*Components/HeadingComponent*:</span><span class="sxs-lookup"><span data-stu-id="5957a-172">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="5957a-173">구성 요소에 구성 요소 이름과 일치 하지 않는 대문자 첫 글자가 포함 된 HTML 요소가 포함 된 경우 요소에 예기치 않은 이름이 있음을 나타내는 경고가 내보내집니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-173">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="5957a-174">구성 요소 네임 스페이스에 대 한 `@using` 문을 추가 하면 구성 요소를 사용할 수 있게 되므로 경고가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-174">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="5957a-175">구성 요소 매개 변수</span><span class="sxs-lookup"><span data-stu-id="5957a-175">Component parameters</span></span>

<span data-ttu-id="5957a-176">구성 요소는 구성 요소 클래스의 공용 속성을 사용 하 여 정의 되는 *구성 요소 매개 변수*를 포함할 수 있습니다 .이 매개 변수는 `[Parameter]` 특성</span><span class="sxs-lookup"><span data-stu-id="5957a-176">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="5957a-177">특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-177">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="5957a-178">*Components/ChildComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5957a-178">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="5957a-179">샘플 앱의 다음 예제에서는 `ParentComponent` `ChildComponent`의 `Title` 속성 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-179">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="5957a-180">*Pages/ParentComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5957a-180">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="child-content"></a><span data-ttu-id="5957a-181">자식 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="5957a-181">Child content</span></span>

<span data-ttu-id="5957a-182">구성 요소는 다른 구성 요소의 콘텐츠를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-182">Components can set the content of another component.</span></span> <span data-ttu-id="5957a-183">할당 구성 요소는 받는 구성 요소를 지정 하는 태그 사이에 콘텐츠를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-183">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="5957a-184">다음 예제에서 `ChildComponent`에는 렌더링할 UI 세그먼트를 나타내는 `RenderFragment`를 나타내는 `ChildContent` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-184">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="5957a-185">`ChildContent`의 값은 콘텐츠를 렌더링 해야 하는 구성 요소의 태그에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-185">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="5957a-186">`ChildContent` 값은 부모 구성 요소에서 수신 되 고 부트스트랩 패널의 `panel-body`내에서 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-186">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="5957a-187">*Components/ChildComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5957a-187">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="5957a-188">`RenderFragment` 콘텐츠를 받는 속성은 규칙에 따라 `ChildContent` 이름이 지정 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-188">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="5957a-189">샘플 앱의 `ParentComponent`는 콘텐츠를 `<ChildComponent>` 태그 안에 배치 하 여 `ChildComponent`를 렌더링 하기 위한 콘텐츠를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-189">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="5957a-190">*Pages/ParentComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5957a-190">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="5957a-191">특성 스 플랫 및 임의 매개 변수</span><span class="sxs-lookup"><span data-stu-id="5957a-191">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="5957a-192">구성 요소는 구성 요소의 선언 된 매개 변수 외에 추가 특성도 캡처하고 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-192">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="5957a-193">[`@attributes`](xref:mvc/views/razor#attributes) Razor 지시어를 사용 하 여 구성 요소를 렌더링할 때 사전에 추가 특성을 캡처한 다음 요소로 *splatted* 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-193">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="5957a-194">이 시나리오는 다양 한 사용자 지정을 지 원하는 태그 요소를 생성 하는 구성 요소를 정의 하는 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-194">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="5957a-195">예를 들어 많은 매개 변수를 지 원하는 `<input>`에 대해 개별적으로 특성을 정의 하는 것이 번거로울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-195">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="5957a-196">다음 예제에서 첫 번째 `<input>` 요소 (`id="useIndividualParams"`)는 개별 구성 요소 매개 변수를 사용 하 고, 두 번째 `<input>` 요소 (`id="useAttributesDict"`)는 특성 스 플랫를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-196">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="5957a-197">매개 변수의 형식은 문자열 키를 사용 하 여 `IEnumerable<KeyValuePair<string, object>>`를 구현 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-197">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="5957a-198">이 시나리오에서는 `IReadOnlyDictionary<string, object>` 사용도 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-198">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="5957a-199">두 방법을 모두 사용 하 여 렌더링 된 `<input>` 요소는 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-199">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="5957a-200">임의 특성을 허용 하려면 `CaptureUnmatchedValues` 속성이 `true`로 설정 된 `[Parameter]` 특성을 사용 하 여 구성 요소 매개 변수를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-200">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="5957a-201">`[Parameter]`의 `CaptureUnmatchedValues` 속성을 사용 하면 매개 변수는 다른 매개 변수와 일치 하지 않는 모든 특성을 일치 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-201">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="5957a-202">구성 요소는 `CaptureUnmatchedValues`포함 된 단일 매개 변수만 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-202">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="5957a-203">`CaptureUnmatchedValues`에 사용 되는 속성 형식은 문자열 키가 있는 `Dictionary<string, object>`에서 할당할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-203">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="5957a-204">이 시나리오에서 `IEnumerable<KeyValuePair<string, object>>` 또는 `IReadOnlyDictionary<string, object>`도 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-204">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="5957a-205">요소 특성의 위치를 기준으로 `@attributes`의 위치는 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-205">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="5957a-206">요소에 대 한 `@attributes` splatted 때 오른쪽에서 왼쪽으로 (last to first) 특성을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-206">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="5957a-207">`Child` 구성 요소를 사용 하는 구성 요소의 다음 예를 살펴보십시오.</span><span class="sxs-lookup"><span data-stu-id="5957a-207">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="5957a-208">*Parentcomponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5957a-208">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="5957a-209">*Childcomponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5957a-209">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="5957a-210">`Child` 구성 요소의 `extra` 특성은 `@attributes`오른쪽으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-210">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="5957a-211">특성은 오른쪽에서 왼쪽으로 처리 되기 때문에 `Parent` 구성 요소의 렌더링 된 `<div>`는 추가 특성을 통해 전달 될 때 `extra="5"`를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-211">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="5957a-212">다음 예에서는 `Child` 구성 요소의 `<div>`에서 `extra`와 `@attributes`의 순서를 반대로 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-212">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="5957a-213">*Parentcomponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5957a-213">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="5957a-214">*Childcomponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5957a-214">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="5957a-215">`Parent` 구성 요소의 렌더링 된 `<div>`에는 추가 특성을 통해 전달 될 경우 `extra="10"` 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-215">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="data-binding"></a><span data-ttu-id="5957a-216">데이터 바인딩</span><span class="sxs-lookup"><span data-stu-id="5957a-216">Data binding</span></span>

<span data-ttu-id="5957a-217">구성 요소와 DOM 요소 모두에 대 한 데이터 바인딩은 [`@bind`](xref:mvc/views/razor#bind) 특성을 사용 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-217">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="5957a-218">다음 예제에서는 `CurrentValue` 속성을 텍스트 상자의 값에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-218">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="5957a-219">텍스트 상자가 포커스를 잃으면 속성의 값이 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-219">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="5957a-220">텍스트 상자는 구성 요소가 렌더링 되는 경우에만 UI에서 업데이트 되며 속성의 값을 변경 하는 것에 대 한 응답으로는 업데이트 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-220">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="5957a-221">이벤트 처리기 코드를 실행 한 후 구성 요소를 자체적으로 렌더링 하므로 속성 업데이트는 *일반적으로* 이벤트 처리기가 트리거되는 즉시 UI에 반영 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-221">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="5957a-222">`CurrentValue` 속성 (`<input @bind="CurrentValue" />`)과 함께 `@bind`를 사용 하는 것은 기본적으로 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-222">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="5957a-223">구성 요소가 렌더링 되 면 input 요소의 `value` `CurrentValue` 속성에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-223">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="5957a-224">사용자가 텍스트 상자에를 입력 하 고 요소 포커스를 변경 하면 `onchange` 이벤트가 발생 하 고 `CurrentValue` 속성이 변경 된 값으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-224">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="5957a-225">실제로는 `@bind`에서 형식 변환이 수행 되는 경우를 처리 하므로 코드 생성은 더 복잡 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-225">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="5957a-226">원칙적으로 `@bind`은 식의 현재 값을 `value` 특성과 연결 하 고 등록 된 처리기를 사용 하 여 변경 내용을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-226">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="5957a-227">`@bind` 구문을 사용 하 여 `onchange` 이벤트를 처리 하는 것 외에도 `event` 매개 변수 ([`@bind-value:event`](xref:mvc/views/razor#bind))를 사용 하 여 [`@bind-value`](xref:mvc/views/razor#bind) 특성을 지정 하 여 다른 이벤트를 사용 하 여 속성 또는 필드를 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-227">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [`@bind-value`](xref:mvc/views/razor#bind) attribute with an `event` parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="5957a-228">다음 예에서는 `oninput` 이벤트의 `CurrentValue` 속성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-228">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="5957a-229">요소가 포커스를 잃을 때 발생 하는 `onchange`와는 달리 텍스트 상자의 값이 변경 될 때 발생 `oninput`.</span><span class="sxs-lookup"><span data-stu-id="5957a-229">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="5957a-230">이전 예제 바인딩의 `@bind-value`:</span><span class="sxs-lookup"><span data-stu-id="5957a-230">`@bind-value` in the preceding example binds:</span></span>

* <span data-ttu-id="5957a-231">요소의 `value` 특성에 대 한 지정 된 식 (`CurrentValue`)입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-231">The specified expression (`CurrentValue`) to the element's `value` attribute.</span></span>
* <span data-ttu-id="5957a-232">`@bind-value:event`으로 지정 된 이벤트에 대 한 변경 이벤트 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-232">A change event delegate to the event specified by `@bind-value:event`.</span></span>

<span data-ttu-id="5957a-233">**구문 분석할 값**</span><span class="sxs-lookup"><span data-stu-id="5957a-233">**Unparsable values**</span></span>

<span data-ttu-id="5957a-234">사용자가 데이터 바인딩된 요소에 구문 분석할 수 없는 값을 제공 하면 bind 이벤트가 트리거될 때 구문 분석할 수 없는 값이 자동으로 이전 값으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-234">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="5957a-235">다음 시나리오를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-235">Consider the following scenario:</span></span>

* <span data-ttu-id="5957a-236">`<input>` 요소는 `123`초기 값을 사용 하 여 `int` 형식에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-236">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="5957a-237">사용자는 요소의 값을 업데이트 하 여 페이지에서 `123.45` 하 고 요소 포커스를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-237">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="5957a-238">위의 시나리오에서 요소의 값은 `123`로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-238">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="5957a-239">`123`의 원래 값을 사용 하 여 `123.45` 값이 거부 되 면 사용자는 해당 값이 허용 되지 않는다는 것을 이해 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-239">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="5957a-240">기본적으로 바인딩은 요소의 `onchange` 이벤트 (`@bind="{PROPERTY OR FIELD}"`)에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-240">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="5957a-241">`@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`를 사용 하 여 다른 이벤트를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-241">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="5957a-242">`oninput` 이벤트 (`@bind-value:event="oninput"`)의 경우 변경할지는 구문 분석할 값을 제공 하는 키 입력 후에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-242">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="5957a-243">`int`바인딩된 형식의 `oninput` 이벤트를 대상으로 지정 하는 경우 사용자는 `.` 문자를 입력할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-243">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="5957a-244">`.` 문자는 즉시 제거 되므로 사용자는 전체 숫자만 허용 되는 즉각적인 피드백을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-244">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="5957a-245">사용자가 구문 분석할 수 없는 `<input>` 값을 지울 수 있어야 하는 경우와 같이 `oninput` 이벤트의 값을 되돌리는 것이 적합 하지 않은 시나리오가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-245">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="5957a-246">대안은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-246">Alternatives include:</span></span>

* <span data-ttu-id="5957a-247">`oninput` 이벤트를 사용 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-247">Don't use the `oninput` event.</span></span> <span data-ttu-id="5957a-248">요소가 포커스를 잃을 때까지 잘못 된 값이 되돌아가지 않는 기본 `onchange` 이벤트 (`@bind="{PROPERTY OR FIELD}"`)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-248">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="5957a-249">`int?` 또는 `string`와 같은 nullable 형식에 바인딩하고 잘못 된 항목을 처리 하는 사용자 지정 논리를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-249">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="5957a-250">`InputNumber` 또는 `InputDate`와 같은 [폼 유효성 검사 구성 요소](xref:blazor/forms-validation)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-250">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="5957a-251">양식 유효성 검사 구성 요소에는 잘못 된 입력을 관리 하기 위한 기본 제공 지원이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-251">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="5957a-252">양식 유효성 검사 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="5957a-252">Form validation components:</span></span>
  * <span data-ttu-id="5957a-253">사용자가 연결 된 `EditContext`에서 잘못 된 입력 및 수신 유효성 검사 오류를 제공할 수 있도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-253">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="5957a-254">사용자가 추가 webform 데이터를 입력 하는 것을 방해 하지 않고 UI에서 유효성 검사 오류를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-254">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="5957a-255">**전역화**</span><span class="sxs-lookup"><span data-stu-id="5957a-255">**Globalization**</span></span>

<span data-ttu-id="5957a-256">`@bind` 값은 현재 문화권의 규칙을 사용 하 여 표시 및 구문 분석을 위해 형식이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-256">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="5957a-257">현재 문화권은 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 속성에서 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-257">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="5957a-258">[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) 는 다음 필드 형식 (`<input type="{TYPE}" />`)에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-258">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="5957a-259">이전 필드 형식:</span><span class="sxs-lookup"><span data-stu-id="5957a-259">The preceding field types:</span></span>

* <span data-ttu-id="5957a-260">적절 한 브라우저 기반 서식 지정 규칙을 사용 하 여 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-260">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="5957a-261">자유 형식 텍스트를 포함할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-261">Can't contain free-form text.</span></span>
* <span data-ttu-id="5957a-262">브라우저의 구현에 따라 사용자 상호 작용 특성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-262">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="5957a-263">다음 필드 형식은 특정 형식 지정 요구 사항을 가지 며, 모든 주요 브라우저에서 지원 되지 않기 때문에 현재 Blazor에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-263">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="5957a-264">`@bind`는 `@bind:culture` 매개 변수를 지원 하 여 값을 구문 분석 하 고 서식을 지정 하는 <xref:System.Globalization.CultureInfo?displayProperty=fullName>을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-264">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="5957a-265">`date` 및 `number` 필드 형식을 사용할 때는 문화권을 지정 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-265">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="5957a-266">`date` 및 `number`에는 필수 문화권을 제공 하는 Blazor 지원이 기본적으로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-266">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="5957a-267">사용자의 문화권을 설정 하는 방법에 대 한 자세한 내용은 [지역화](#localization) 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="5957a-267">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="5957a-268">**서식 문자열**</span><span class="sxs-lookup"><span data-stu-id="5957a-268">**Format strings**</span></span>

<span data-ttu-id="5957a-269">데이터 바인딩은 [`@bind:format`](xref:mvc/views/razor#bind)를 사용 하 여 <xref:System.DateTime> 형식 문자열과 함께 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-269">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="5957a-270">통화 또는 숫자 형식과 같은 다른 형식 식은 현재 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-270">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="5957a-271">위의 코드에서 `<input>` 요소의 필드 형식 (`type`)은 기본적으로 `text`입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-271">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="5957a-272">다음 .NET 형식의 바인딩에 대해 `@bind:format` 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-272">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="5957a-273"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="5957a-273"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="5957a-274"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="5957a-274"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="5957a-275">`@bind:format` 특성은 `<input>` 요소의 `value`에 적용할 날짜 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-275">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="5957a-276">이 형식은 `onchange` 이벤트가 발생할 때 값을 구문 분석 하는 데도 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-276">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="5957a-277">Blazor에서 날짜 형식을 기본적으로 지원 하기 때문에 `date` 필드 형식에 대 한 형식을 지정 하는 것은 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-277">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="5957a-278">권장 사항에도 불구 하 고 `date` 필드 형식과 함께 형식이 제공 되는 경우 바인딩이 제대로 작동 하려면 `yyyy-MM-dd` 날짜 형식만 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-278">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

<span data-ttu-id="5957a-279">**구성 요소 매개 변수**</span><span class="sxs-lookup"><span data-stu-id="5957a-279">**Component parameters**</span></span>

<span data-ttu-id="5957a-280">바인딩은 구성 요소 매개 변수를 인식 하며, 여기서 `@bind-{property}`는 구성 요소에서 속성 값을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-280">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="5957a-281">다음 자식 구성 요소 (`ChildComponent`)에는 `Year` 구성 요소 매개 변수와 `YearChanged` 콜백이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-281">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="5957a-282">`EventCallback<T>`는 [Eventcallback](#eventcallback) 섹션에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-282">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="5957a-283">다음 부모 구성 요소는 `ChildComponent`을 사용 하 고 부모의 `ParentYear` 매개 변수를 자식 구성 요소의 `Year` 매개 변수에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-283">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```razor
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

<span data-ttu-id="5957a-284">`ParentComponent` 로드 하면 다음과 같은 태그가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-284">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="5957a-285">`ParentComponent`의 단추를 선택 하 여 `ParentYear` 속성 값이 변경 되 면 `ChildComponent`의 `Year` 속성이 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-285">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="5957a-286">`Year`의 새 값은 `ParentComponent`을 수행 하는 경우 UI에서 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-286">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="5957a-287">`Year` 매개 변수는 `Year` 매개 변수 형식과 일치 하는 도우미 `YearChanged` 이벤트를 포함 하기 때문에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-287">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="5957a-288">규칙에 따라 `<ChildComponent @bind-Year="ParentYear" />`는 기본적으로 작성 하는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-288">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="5957a-289">일반적으로 속성은 `@bind-property:event` 특성을 사용 하 여 해당 이벤트 처리기에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-289">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="5957a-290">예를 들어 `MyProp` 속성은 다음 두 가지 특성을 사용 하 여 `MyEventHandler`에 바인딩될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-290">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

<span data-ttu-id="5957a-291">**라디오 단추**</span><span class="sxs-lookup"><span data-stu-id="5957a-291">**Radio buttons**</span></span>

<span data-ttu-id="5957a-292">폼의 라디오 단추에 바인딩하는 방법에 대 한 자세한 내용은 <xref:blazor/forms-validation#work-with-radio-buttons>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-292">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>

## <a name="event-handling"></a><span data-ttu-id="5957a-293">이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="5957a-293">Event handling</span></span>

<span data-ttu-id="5957a-294">Razor 구성 요소는 이벤트 처리 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-294">Razor components provide event handling features.</span></span> <span data-ttu-id="5957a-295">대리자 형식 값을 사용 하는 `on{EVENT}` (예: `onclick` 및 `onsubmit`) 이라는 HTML 요소 특성의 경우 Razor 구성 요소는 특성의 값을 이벤트 처리기로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-295">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="5957a-296">특성의 이름은 항상 [`@on{EVENT}`](xref:mvc/views/razor#onevent)서식 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-296">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="5957a-297">다음 코드는 UI에서 단추가 선택 될 때 `UpdateHeading` 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-297">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
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

<span data-ttu-id="5957a-298">다음 코드는 UI에서 확인란이 변경 될 때 `CheckChanged` 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-298">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="5957a-299">이벤트 처리기는 비동기 일 수도 있고 <xref:System.Threading.Tasks.Task>반환 될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-299">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="5957a-300">[Statehaschanged](xref:blazor/lifecycle#state-changes)를 수동으로 호출할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-300">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="5957a-301">예외가 발생 하면 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-301">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="5957a-302">다음 예제에서는 단추를 선택 하면 `UpdateHeading`를 비동기적으로 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-302">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
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

### <a name="event-argument-types"></a><span data-ttu-id="5957a-303">이벤트 인수 형식</span><span class="sxs-lookup"><span data-stu-id="5957a-303">Event argument types</span></span>

<span data-ttu-id="5957a-304">일부 이벤트의 경우 이벤트 인수 형식이 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-304">For some events, event argument types are permitted.</span></span> <span data-ttu-id="5957a-305">이러한 이벤트 유형 중 하나에 대 한 액세스가 필요 하지 않은 경우에는 메서드 호출에 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-305">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="5957a-306">지원 되는 `EventArgs` 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-306">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="5957a-307">Event</span><span class="sxs-lookup"><span data-stu-id="5957a-307">Event</span></span>            | <span data-ttu-id="5957a-308">클래스</span><span class="sxs-lookup"><span data-stu-id="5957a-308">Class</span></span>                | <span data-ttu-id="5957a-309">DOM 이벤트 및 참고 사항</span><span class="sxs-lookup"><span data-stu-id="5957a-309">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="5957a-310">클립보드</span><span class="sxs-lookup"><span data-stu-id="5957a-310">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="5957a-311">`oncut`에서 `oncopy`에서 `onpaste`</span><span class="sxs-lookup"><span data-stu-id="5957a-311">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="5957a-312">끌기</span><span class="sxs-lookup"><span data-stu-id="5957a-312">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="5957a-313">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="5957a-313">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="5957a-314">끌어온 항목 데이터를 포함 하는 `DataTransfer` 및 `DataTransferItem`.</span><span class="sxs-lookup"><span data-stu-id="5957a-314">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="5957a-315">오류</span><span class="sxs-lookup"><span data-stu-id="5957a-315">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="5957a-316">Event</span><span class="sxs-lookup"><span data-stu-id="5957a-316">Event</span></span>            | `EventArgs`          | <span data-ttu-id="5957a-317">*일반*</span><span class="sxs-lookup"><span data-stu-id="5957a-317">*General*</span></span><br><span data-ttu-id="5957a-318">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="5957a-318">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="5957a-319">*클립보드*</span><span class="sxs-lookup"><span data-stu-id="5957a-319">*Clipboard*</span></span><br><span data-ttu-id="5957a-320">`onbeforecut`에서 `onbeforecopy`에서 `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="5957a-320">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="5957a-321">*입력*</span><span class="sxs-lookup"><span data-stu-id="5957a-321">*Input*</span></span><br><span data-ttu-id="5957a-322">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="5957a-322">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="5957a-323">*미디어*</span><span class="sxs-lookup"><span data-stu-id="5957a-323">*Media*</span></span><br><span data-ttu-id="5957a-324">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="5957a-324">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="5957a-325">포커스</span><span class="sxs-lookup"><span data-stu-id="5957a-325">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="5957a-326">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="5957a-326">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="5957a-327">`relatedTarget`에 대 한 지원을 포함 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-327">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="5957a-328">입력</span><span class="sxs-lookup"><span data-stu-id="5957a-328">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="5957a-329">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="5957a-329">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="5957a-330">키보드</span><span class="sxs-lookup"><span data-stu-id="5957a-330">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="5957a-331">`onkeydown`에서 `onkeypress`에서 `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="5957a-331">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="5957a-332">마우스</span><span class="sxs-lookup"><span data-stu-id="5957a-332">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="5957a-333">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-333">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="5957a-334">마우스 포인터</span><span class="sxs-lookup"><span data-stu-id="5957a-334">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="5957a-335">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="5957a-335">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="5957a-336">마우스 휠</span><span class="sxs-lookup"><span data-stu-id="5957a-336">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="5957a-337">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="5957a-337">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="5957a-338">진행 중</span><span class="sxs-lookup"><span data-stu-id="5957a-338">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="5957a-339">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="5957a-339">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="5957a-340">터치</span><span class="sxs-lookup"><span data-stu-id="5957a-340">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="5957a-341">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="5957a-341">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="5957a-342">`TouchPoint` 터치를 구분 하는 장치에서 단일 접촉 지점을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-342">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="5957a-343">위의 표에서 이벤트의 속성 및 이벤트 처리 동작에 대 한 자세한 내용은 [참조 소스 (dotnet/aspnetcore release/3.1 분기)의 EventArgs 클래스](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-343">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="5957a-344">람다 식</span><span class="sxs-lookup"><span data-stu-id="5957a-344">Lambda expressions</span></span>

<span data-ttu-id="5957a-345">람다 식을 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-345">Lambda expressions can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="5957a-346">요소 집합을 반복 하는 경우와 같이 추가 값을 닫는 것이 편리한 경우가 종종 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-346">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="5957a-347">다음 예제에서는 UI에서 선택 된 경우 각각 이벤트 인수 (`MouseEventArgs`) 및 해당 단추 번호 (`buttonNumber`)를 전달 하 `UpdateHeading`를 호출 하는 세 개의 단추를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-347">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
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
> <span data-ttu-id="5957a-348">람다 식에서 직접 루프 변수 (`i`)를 `for` 루프에서 사용 **하지** 마십시오.</span><span class="sxs-lookup"><span data-stu-id="5957a-348">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="5957a-349">그렇지 않으면 모든 람다 식에서 같은 변수를 사용 하 여 `i`값을 모든 람다에서 동일 하 게 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-349">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="5957a-350">항상 지역 변수 (이전 예제에서는`buttonNumber`)에서 해당 값을 캡처한 다음 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-350">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="5957a-351">EventCallback</span><span class="sxs-lookup"><span data-stu-id="5957a-351">EventCallback</span></span>

<span data-ttu-id="5957a-352">중첩 된 구성 요소를 사용 하는 일반적인 시나리오는 자식 구성 요소 이벤트가 발생할 때 부모 구성 요소의 메서드를 실행 하는 것입니다. 예를 들어, `onclick` 이벤트가 자식에서 발생할 때&mdash;합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-352">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="5957a-353">구성 요소 간에 이벤트를 노출 하려면 `EventCallback`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-353">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="5957a-354">부모 구성 요소는 자식 구성 요소의 `EventCallback`에 콜백 메서드를 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-354">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="5957a-355">샘플 앱 (component */ChildComponent. razor*)의 `ChildComponent`은 단추의 `onclick` 처리기가 샘플의 `ParentComponent`에서 `EventCallback` 대리자를 수신 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-355">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="5957a-356">`EventCallback`는 주변 장치의 `onclick` 이벤트에 적절 한 `MouseEventArgs`으로 입력 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-356">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="5957a-357">`ParentComponent`은 자식의 `EventCallback<T>` `ShowMessage` 메서드로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-357">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method.</span></span>

<span data-ttu-id="5957a-358">*Pages/ParentComponent. razor*:</span><span class="sxs-lookup"><span data-stu-id="5957a-358">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="5957a-359">`ChildComponent`에서 단추가 선택 된 경우:</span><span class="sxs-lookup"><span data-stu-id="5957a-359">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="5957a-360">`ParentComponent`의 `ShowMessage` 메서드가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-360">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="5957a-361">`messageText` 업데이트 되 고 `ParentComponent`에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-361">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="5957a-362">[Statehaschanged](xref:blazor/lifecycle#state-changes) 호출은 콜백의 메서드 (`ShowMessage`)에서 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-362">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="5957a-363">자식 이벤트가 자식 내에서 실행 되는 이벤트 처리기의 rerendering 구성 요소를 트리거하는 것 처럼 `StateHasChanged`는 자동으로 호출 되어 `ParentComponent`을 rerender 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-363">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="5957a-364">`EventCallback` 및 `EventCallback<T>`는 비동기 대리자를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-364">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="5957a-365">`EventCallback<T>`은 강력한 형식이 며 특정 인수 형식이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-365">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="5957a-366">`EventCallback` 약하게 형식화 되며 모든 인수 유형을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-366">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="5957a-367">`InvokeAsync`를 사용 하 여 `EventCallback` 또는 `EventCallback<T>`를 호출 하 고 <xref:System.Threading.Tasks.Task>를 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-367">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="5957a-368">이벤트 처리 및 바인딩 구성 요소 매개 변수에 `EventCallback` 및 `EventCallback<T>`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-368">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="5957a-369">`EventCallback`보다 강력한 형식의 `EventCallback<T>`를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-369">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="5957a-370">`EventCallback<T>` 구성 요소의 사용자에 게 더 나은 오류 피드백을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-370">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="5957a-371">다른 UI 이벤트 처리기와 마찬가지로 이벤트 매개 변수를 지정 하는 것은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-371">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="5957a-372">콜백에 전달 된 값이 없는 경우 `EventCallback`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-372">Use `EventCallback` when there's no value passed to the callback.</span></span>

### <a name="prevent-default-actions"></a><span data-ttu-id="5957a-373">기본 동작 방지</span><span class="sxs-lookup"><span data-stu-id="5957a-373">Prevent default actions</span></span>

<span data-ttu-id="5957a-374">이벤트에 대 한 기본 동작을 방지 하려면 [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 지시어 특성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-374">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="5957a-375">입력 장치에서 키를 선택 하 고 요소 포커스가 텍스트 상자에 있는 경우 브라우저는 일반적으로 텍스트 상자에 키 문자를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-375">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="5957a-376">다음 예에서는 `@onkeypress:preventDefault` 지시어 특성을 지정 하 여 기본 동작을 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-376">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="5957a-377">카운터가 증가 하 고 **+** 키가 `<input>` 요소의 값으로 캡처되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-377">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

<span data-ttu-id="5957a-378">값 없이 `@on{EVENT}:preventDefault` 특성을 지정 하는 것은 `@on{EVENT}:preventDefault="true"`와 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-378">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="5957a-379">특성 값은 식일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-379">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="5957a-380">다음 예에서는 `true` 또는 `false`로 설정 된 `bool` 필드를 `_shouldPreventDefault` 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-380">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="5957a-381">기본 동작을 방지 하기 위해 이벤트 처리기가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-381">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="5957a-382">이벤트 처리기와 기본 작업 시나리오를 독립적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-382">The event handler and prevent default action scenarios can be used independently.</span></span>

### <a name="stop-event-propagation"></a><span data-ttu-id="5957a-383">이벤트 전파 중지</span><span class="sxs-lookup"><span data-stu-id="5957a-383">Stop event propagation</span></span>

<span data-ttu-id="5957a-384">[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 지시어 특성을 사용 하 여 이벤트 전파를 중지 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-384">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="5957a-385">다음 예제에서 확인란을 선택 하면 두 번째 자식 `<div>`의 click 이벤트가 부모 `<div>`에 전파 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-385">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

## <a name="chained-bind"></a><span data-ttu-id="5957a-386">연결 된 바인딩</span><span class="sxs-lookup"><span data-stu-id="5957a-386">Chained bind</span></span>

<span data-ttu-id="5957a-387">일반적인 시나리오는 데이터 바인딩된 매개 변수를 구성 요소 출력의 페이지 요소에 연결 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-387">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="5957a-388">이 시나리오를 여러 수준의 바인딩이 동시에 발생 하기 때문에 연결 된 *바인딩* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-388">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="5957a-389">페이지의 요소에 `@bind` 구문을 사용 하 여 연결 된 바인딩을 구현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-389">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="5957a-390">이벤트 처리기 및 값은 별도로 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-390">The event handler and value must be specified separately.</span></span> <span data-ttu-id="5957a-391">그러나 부모 구성 요소는 구성 요소의 매개 변수와 함께 `@bind` 구문을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-391">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="5957a-392">다음 `PasswordField` 구성 요소 (*Passwordfield. razor*):</span><span class="sxs-lookup"><span data-stu-id="5957a-392">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="5957a-393">`<input>` 요소의 값을 `Password` 속성으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-393">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="5957a-394">[Eventcallback](#eventcallback)을 사용 하 여 `Password` 속성의 변경 내용을 부모 구성 요소에 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-394">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

```razor
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

<span data-ttu-id="5957a-395">`PasswordField` 구성 요소는 다른 구성 요소에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-395">The `PasswordField` component is used in another component:</span></span>

```razor
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="5957a-396">위의 예에서 암호에 대 한 검사 또는 트랩 오류를 수행 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-396">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="5957a-397">`Password`에 대 한 지원 필드를 만듭니다 (다음 예제 코드에서`password`).</span><span class="sxs-lookup"><span data-stu-id="5957a-397">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="5957a-398">`Password` setter에서 확인 또는 트랩 오류를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-398">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="5957a-399">다음 예에서는 암호 값에 공백을 사용 하는 경우 사용자에 게 즉각적인 피드백을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-399">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
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

## <a name="capture-references-to-components"></a><span data-ttu-id="5957a-400">구성 요소에 대 한 참조 캡처</span><span class="sxs-lookup"><span data-stu-id="5957a-400">Capture references to components</span></span>

<span data-ttu-id="5957a-401">구성 요소 참조는 `Show` 또는 `Reset`와 같이 해당 인스턴스에 대 한 명령을 실행할 수 있도록 구성 요소 인스턴스를 참조 하는 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-401">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="5957a-402">구성 요소 참조를 캡처하려면:</span><span class="sxs-lookup"><span data-stu-id="5957a-402">To capture a component reference:</span></span>

* <span data-ttu-id="5957a-403">자식 구성 요소에 [`@ref`](xref:mvc/views/razor#ref) 특성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-403">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="5957a-404">자식 구성 요소와 동일한 유형으로 필드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-404">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="5957a-405">구성 요소가 렌더링 되 면 `loginDialog` 필드가 `MyLoginDialog` 자식 구성 요소 인스턴스로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-405">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="5957a-406">그런 다음 구성 요소 인스턴스에서 .NET 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-406">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5957a-407">`loginDialog` 변수는 구성 요소가 렌더링 된 후에만 채워지고 출력에는 `MyLoginDialog` 요소가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-407">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="5957a-408">이 시점까지 참조할 항목이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-408">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="5957a-409">구성 요소에서 렌더링을 완료 한 후에 구성 요소 참조를 조작 하려면 [OnAfterRenderAsync 또는 OnAfterRender 메서드](xref:blazor/lifecycle#after-component-render)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-409">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="5957a-410">구성 요소 참조를 캡처하는 것은 [요소 참조를 캡처하](xref:blazor/javascript-interop#capture-references-to-elements)는 데 유사한 구문을 사용 하지만 [JavaScript interop](xref:blazor/javascript-interop) 기능은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-410">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="5957a-411">구성 요소 참조는 JavaScript 코드에 전달 되지&mdash;.NET 코드 에서만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-411">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="5957a-412">구성 요소 참조를 사용 하 여 자식 구성 요소의 상태를 변경할 수 **없습니다** .</span><span class="sxs-lookup"><span data-stu-id="5957a-412">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="5957a-413">대신, 일반 선언적 매개 변수를 사용 하 여 자식 구성 요소에 데이터를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-413">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="5957a-414">일반적인 선언적 매개 변수를 사용 하면 자식 구성 요소가 자동으로 올바른 시간에 rerender 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-414">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="5957a-415">외부에서 구성 요소 메서드를 호출 하 여 상태 업데이트</span><span class="sxs-lookup"><span data-stu-id="5957a-415">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="5957a-416">는 `SynchronizationContext`를 사용 하 여 단일 논리적 실행 스레드를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-416"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="5957a-417">구성 요소의 [수명 주기 메서드](xref:blazor/lifecycle) 및 Blazor에 의해 발생 하는 모든 이벤트 콜백이이 `SynchronizationContext`실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-417">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="5957a-418">외부 이벤트 (예: 타이머 또는 다른 알림)를 기반으로 구성 요소를 업데이트 해야 하는 경우 Blazor의 `SynchronizationContext`에 디스패치할 `InvokeAsync` 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-418">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="5957a-419">예를 들어 업데이트 된 상태의 수신 구성 요소를 알릴 수 있는 알림 *서비스* 를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-419">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="5957a-420">`NotifierService` 사용 하 여 구성 요소를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-420">Usage of the `NotifierService` to update a component:</span></span>

```razor
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

<span data-ttu-id="5957a-421">위의 예제에서 `NotifierService`는 Blazor의 `SynchronizationContext`외부에서 구성 요소의 `OnNotify` 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-421">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="5957a-422">`InvokeAsync`은 올바른 컨텍스트로 전환 하 고 렌더링을 큐에 대기 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-422">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="5957a-423">\@키를 사용 하 여 요소 및 구성 요소 유지 관리</span><span class="sxs-lookup"><span data-stu-id="5957a-423">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="5957a-424">요소 또는 구성 요소 목록을 렌더링할 때 이후에 요소 또는 구성 요소가 변경 되는 경우 Blazor의 diff 알고리즘은 유지할 수 있는 이전 요소 또는 구성 요소와 모델 개체가 이러한 요소에 매핑되는 방법을 결정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-424">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="5957a-425">일반적으로이 프로세스는 자동 이며 무시 해도 되지만 프로세스를 제어 하는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-425">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="5957a-426">다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-426">Consider the following example:</span></span>

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

<span data-ttu-id="5957a-427">`People` 컬렉션의 콘텐츠는 삽입, 삭제 또는 다시 정렬 된 항목으로 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-427">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="5957a-428">구성 요소가 렌더링 면 `<DetailsEditor>` 구성 요소가 다른 `Details` 매개 변수 값을 받도록 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-428">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="5957a-429">이로 인해 예상 보다 더 복잡 한 rerendering 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-429">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="5957a-430">경우에 따라 rerendering는 손실 요소 포커스와 같은 표시 되는 동작 차이를 일으킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-430">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="5957a-431">매핑 프로세스는 `@key` 지시어 특성을 사용 하 여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-431">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="5957a-432">`@key` diff 알고리즘은 키의 값에 따라 요소 또는 구성 요소를 보존 하도록 보장 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-432">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="5957a-433">`People` 컬렉션이 변경 되 면 diff 알고리즘은 `<DetailsEditor>` 인스턴스와 `person` 인스턴스 간의 연결을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-433">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="5957a-434">`People` 목록에서 `Person` 삭제 된 경우 해당 `<DetailsEditor>` 인스턴스만 UI에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-434">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="5957a-435">다른 인스턴스는 변경 되지 않은 상태로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-435">Other instances are left unchanged.</span></span>
* <span data-ttu-id="5957a-436">`Person` 목록에서 특정 위치에 삽입 되는 경우 해당 위치에 하나의 새 `<DetailsEditor>` 인스턴스가 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-436">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="5957a-437">다른 인스턴스는 변경 되지 않은 상태로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-437">Other instances are left unchanged.</span></span>
* <span data-ttu-id="5957a-438">`Person` 항목이 다시 정렬 되 면 해당 `<DetailsEditor>` 인스턴스가 유지 되 고 UI에서 다시 정렬 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-438">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="5957a-439">일부 시나리오에서는 `@key`를 사용 하 여 rerendering의 복잡성을 최소화 하 고 DOM의 상태 저장 부분 (예: 포커스 위치)에 대 한 잠재적인 문제를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-439">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5957a-440">키는 각 컨테이너 요소 또는 구성 요소에 대해 로컬입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-440">Keys are local to each container element or component.</span></span> <span data-ttu-id="5957a-441">문서 전체에서 키를 전역적으로 비교 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-441">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="5957a-442">\@키를 사용 하는 경우</span><span class="sxs-lookup"><span data-stu-id="5957a-442">When to use \@key</span></span>

<span data-ttu-id="5957a-443">일반적으로 목록이 렌더링 될 때마다 (예: `@foreach` 블록에서) `@key`를 사용 하 고 `@key`을 정의 하는 데 적합 한 값을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-443">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="5957a-444">`@key`를 사용 하 여 개체가 변경 될 때 요소가 요소 또는 구성 요소 하위 트리를 유지 Blazor 하지 않도록 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-444">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="5957a-445">`@currentPerson` 변경 하는 경우 `@key` 특성 지시문은 Blazor 강제로 전체 `<div>` 및 해당 하위 항목을 삭제 하 고 새 요소와 구성 요소를 사용 하 여 UI 내에서 하위 트리를 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-445">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="5957a-446">이는 `@currentPerson` 변경 될 때 UI 상태가 유지 되지 않도록 해야 하는 경우에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-446">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="5957a-447">\@키를 사용 하지 않는 경우</span><span class="sxs-lookup"><span data-stu-id="5957a-447">When not to use \@key</span></span>

<span data-ttu-id="5957a-448">`@key`으로 diff 때 성능 비용이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-448">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="5957a-449">성능 비용은 크지 않지만 요소 또는 구성 요소 유지 규칙을 제어 하면 앱이 혜택을 받을 수 있는 경우에만 `@key`을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-449">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="5957a-450">`@key` 사용 되지 않더라도 Blazor는 자식 요소와 구성 요소 인스턴스를 최대한 많이 보존 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-450">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="5957a-451">`@key`를 사용 하는 유일한 장점은 매핑을 선택 하는 diff 알고리즘 대신 모델 인스턴스가 유지 된 구성 요소 인스턴스에 매핑되 *는 방식을* 제어 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-451">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="5957a-452">\@키에 사용할 값</span><span class="sxs-lookup"><span data-stu-id="5957a-452">What values to use for \@key</span></span>

<span data-ttu-id="5957a-453">일반적으로 `@key`에 대해 다음과 같은 종류의 값 중 하나를 제공 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-453">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="5957a-454">모델 개체 인스턴스 (예: 이전 예제와 같은 `Person` 인스턴스)</span><span class="sxs-lookup"><span data-stu-id="5957a-454">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="5957a-455">이렇게 하면 개체 참조 일치를 기반으로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-455">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="5957a-456">고유 식별자 (예: `int`, `string`, `Guid`)의 기본 키 값입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-456">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="5957a-457">`@key`에 사용 되는 값이 충돌 하지 않는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-457">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="5957a-458">동일한 부모 요소 내에서 충돌 방지 값이 검색 되는 경우 기존 요소나 구성 요소를 새 요소나 구성 요소에 명확 하 게 매핑할 수 없기 때문에 Blazor 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-458">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="5957a-459">개체 인스턴스 또는 기본 키 값과 같은 고유 값만 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-459">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="routing"></a><span data-ttu-id="5957a-460">라우팅</span><span class="sxs-lookup"><span data-stu-id="5957a-460">Routing</span></span>

<span data-ttu-id="5957a-461">Blazor 라우팅은 앱에서 액세스 가능한 각 구성 요소에 경로 템플릿을 제공 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-461">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="5957a-462">`@page` 지시어를 사용 하 여 Razor 파일이 컴파일되면 생성 된 클래스에 경로 템플릿을 지정 하는 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-462">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="5957a-463">런타임에 라우터는 `RouteAttribute`를 사용 하 여 구성 요소 클래스를 검색 하 고 요청 된 URL과 일치 하는 경로 템플릿을 포함 하는 구성 요소를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-463">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="5957a-464">여러 경로 템플릿을 구성 요소에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-464">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="5957a-465">다음 구성 요소는 `/BlazorRoute` 및 `/DifferentBlazorRoute`에 대 한 요청에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-465">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="5957a-466">*Pages/BlazorRoute*:</span><span class="sxs-lookup"><span data-stu-id="5957a-466">*Pages/BlazorRoute.razor*:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

## <a name="route-parameters"></a><span data-ttu-id="5957a-467">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="5957a-467">Route parameters</span></span>

<span data-ttu-id="5957a-468">구성 요소는 `@page` 지시문에 제공 된 경로 템플릿에서 경로 매개 변수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-468">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="5957a-469">라우터는 경로 매개 변수를 사용 하 여 해당 하는 구성 요소 매개 변수를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-469">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="5957a-470">*Pages/RouteParameter*:</span><span class="sxs-lookup"><span data-stu-id="5957a-470">*Pages/RouteParameter.razor*:</span></span>

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

<span data-ttu-id="5957a-471">선택적 매개 변수는 지원 되지 않으므로 위의 예제에서 두 개의 `@page` 지시문이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-471">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="5957a-472">첫 번째는 매개 변수 없이 구성 요소에 대 한 탐색을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-472">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="5957a-473">두 번째 `@page` 지시어는 `{text}` route 매개 변수를 사용 하 여 `Text` 속성에 값을 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-473">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="5957a-474">*Catch-* 여러 폴더 경계에서 경로를 캡처하는 모든 매개 변수 구문 (`*`/`**`)은 razor 구성 요소 (*razor*)에서 지원 **되지 않습니다** .</span><span class="sxs-lookup"><span data-stu-id="5957a-474">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="5957a-475">Partial 클래스 지원</span><span class="sxs-lookup"><span data-stu-id="5957a-475">Partial class support</span></span>

<span data-ttu-id="5957a-476">Razor 구성 요소는 부분 클래스로 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-476">Razor components are generated as partial classes.</span></span> <span data-ttu-id="5957a-477">Razor 구성 요소는 다음 방법 중 하나를 사용 하 여 작성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-477">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="5957a-478">C#코드는 HTML 태그와 Razor 코드를 포함 하는 [`@code`](xref:mvc/views/razor#code) 블록에서 단일 파일로 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-478">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="5957a-479"> 템플릿은이 접근 방식을 사용 하 여 Razor 구성 요소를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-479"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="5957a-480">C#코드는 partial 클래스로 정의 된 코드 숨김이 파일에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-480">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="5957a-481">다음 예제에서는 Blazor 템플릿에서 생성 된 앱에서 `@code` 블록을 사용 하는 기본 `Counter` 구성 요소를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-481">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="5957a-482">HTML 태그, Razor 코드 및 C# 코드는 동일한 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-482">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="5957a-483">*카운터. razor*:</span><span class="sxs-lookup"><span data-stu-id="5957a-483">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="5957a-484">Partial 클래스를 포함 하는 코드를 사용 하 여 `Counter` 구성 요소를 만들 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-484">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="5957a-485">*카운터. razor*:</span><span class="sxs-lookup"><span data-stu-id="5957a-485">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="5957a-486">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="5957a-486">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="5957a-487">필요한 경우 partial 클래스 파일에 필요한 네임 스페이스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-487">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="5957a-488">Razor 구성 요소에 사용 되는 일반적인 네임 스페이스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-488">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="import-components"></a><span data-ttu-id="5957a-489">구성 요소 가져오기</span><span class="sxs-lookup"><span data-stu-id="5957a-489">Import components</span></span>

<span data-ttu-id="5957a-490">Razor로 작성 된 구성 요소의 네임 스페이스는 (우선 순위)를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-490">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="5957a-491">Razor 파일 (*razor*) 태그에 지정 [`@namespace`](xref:mvc/views/razor#namespace) (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="5957a-491">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="5957a-492">프로젝트 파일 (`<RootNamespace>BlazorSample</RootNamespace>`)에서 프로젝트의 `RootNamespace`입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-492">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="5957a-493">프로젝트 파일의 파일 이름 ( *.csproj*)에서 가져온 프로젝트 이름 및 프로젝트 루트에서 구성 요소로의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-493">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="5957a-494">예를 들어 프레임 워크는 네임 스페이스 `BlazorSample.Pages` *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample*)를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-494">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="5957a-495">구성 요소 C# 는 이름 바인딩 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-495">Components follow C# name binding rules.</span></span> <span data-ttu-id="5957a-496">이 예의 `Index` 구성 요소에 대 한 범위에 있는 구성 요소는 모든 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-496">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="5957a-497">같은 폴더 *에 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="5957a-497">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="5957a-498">프로젝트 루트에서 다른 네임 스페이스를 명시적으로 지정 하지 않은 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-498">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="5957a-499">다른 네임 스페이스에 정의 된 구성 요소는 Razor의 [`@using`](xref:mvc/views/razor#using) 지시어를 사용 하 여 범위로 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-499">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="5957a-500">*BlazorSample/Shared/* folder에 다른 구성 요소인 `NavMenu.razor`있는 경우 다음 `@using` 문을 사용 하 여 `Index.razor`에서 구성 요소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-500">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="5957a-501">[`@using`](xref:mvc/views/razor#using) 지시문이 필요 하지 않은 정규화 된 이름을 사용 하 여 구성 요소를 참조할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-501">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="5957a-502">`global::` 한정자는 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-502">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="5957a-503">별칭이 지정 된 `using` 문 (예: `@using Foo = Bar`)을 가져오는 구성 요소가 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-503">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="5957a-504">부분적으로 정규화 된 이름은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-504">Partially qualified names aren't supported.</span></span> <span data-ttu-id="5957a-505">예를 들어 `@using BlazorSample`를 추가 하 고 `<Shared.NavMenu></Shared.NavMenu>`를 사용 하 `NavMenu.razor`를 참조 하는 것은 지원 되지 않습니다</span><span class="sxs-lookup"><span data-stu-id="5957a-505">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="5957a-506">조건부 HTML 요소 특성</span><span class="sxs-lookup"><span data-stu-id="5957a-506">Conditional HTML element attributes</span></span>

<span data-ttu-id="5957a-507">HTML 요소 특성은 .NET 값에 따라 조건부로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-507">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="5957a-508">값이 `false` 또는 `null`이면 특성이 렌더링 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-508">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="5957a-509">값이 `true`이면 특성이 최소화 된 상태로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-509">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="5957a-510">다음 예제에서 `IsCompleted`는 `checked` 요소의 태그에서 렌더링 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-510">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="5957a-511">`IsCompleted` `true`이면 확인란은 다음과 같이 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-511">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="5957a-512">`IsCompleted` `false`이면 확인란은 다음과 같이 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-512">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="5957a-513">자세한 내용은 <xref:mvc/views/razor>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-513">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="5957a-514">ASP.NET 형식이 `bool`경우에는 [aria를 누르는](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)등의 일부 HTML 특성이 제대로 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-514">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="5957a-515">이러한 경우 `bool`대신 `string` 유형을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-515">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="5957a-516">원시 HTML</span><span class="sxs-lookup"><span data-stu-id="5957a-516">Raw HTML</span></span>

<span data-ttu-id="5957a-517">일반적으로 문자열은 DOM 텍스트 노드를 사용 하 여 렌더링 됩니다. 즉, 포함 될 수 있는 모든 태그는 무시 되 고 리터럴 텍스트로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-517">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="5957a-518">원시 HTML을 렌더링 하려면 HTML 콘텐츠를 `MarkupString` 값으로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-518">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="5957a-519">값은 HTML 또는 SVG로 구문 분석 되 고 DOM에 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-519">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="5957a-520">신뢰할 수 없는 소스에서 생성 된 원시 HTML을 렌더링 하면 **보안상 위험할** 수 있으므로 피해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-520">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="5957a-521">다음 예제에서는 `MarkupString` 형식을 사용 하 여 정적 HTML 콘텐츠 블록을 구성 요소의 렌더링 된 출력에 추가 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-521">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="5957a-522">템플릿 기반 구성 요소</span><span class="sxs-lookup"><span data-stu-id="5957a-522">Templated components</span></span>

<span data-ttu-id="5957a-523">템플릿 기반 구성 요소는 하나 이상의 UI 템플릿을 매개 변수로 허용 하는 구성 요소 이며,이 구성 요소는 구성 요소의 렌더링 논리의 일부로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-523">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="5957a-524">템플릿 기반 구성 요소를 사용 하면 일반 구성 요소 보다 다시 사용할 수 있는 상위 수준 구성 요소를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-524">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="5957a-525">몇 가지 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-525">A couple of examples include:</span></span>

* <span data-ttu-id="5957a-526">사용자가 테이블의 머리글, 행 및 바닥글에 대 한 템플릿을 지정 하는 데 사용할 수 있는 테이블 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-526">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="5957a-527">사용자가 목록에서 항목을 렌더링 하기 위한 템플릿을 지정할 수 있도록 하는 목록 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-527">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="5957a-528">템플릿 매개 변수</span><span class="sxs-lookup"><span data-stu-id="5957a-528">Template parameters</span></span>

<span data-ttu-id="5957a-529">템플릿 기반 구성 요소는 `RenderFragment` 또는 `RenderFragment<T>`형식의 구성 요소 매개 변수를 하나 이상 지정 하 여 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-529">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="5957a-530">렌더링 조각은 렌더링할 UI 세그먼트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-530">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="5957a-531">`RenderFragment<T>`는 렌더링 조각이 호출 될 때 지정할 수 있는 형식 매개 변수를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-531">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="5957a-532">`TableTemplate` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="5957a-532">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="5957a-533">템플릿 기반 구성 요소를 사용 하는 경우 매개 변수의 이름과 일치 하는 자식 요소를 사용 하 여 템플릿 매개 변수를 지정할 수 있습니다 (`TableHeader` 및 다음 예제에서는 `RowTemplate`).</span><span class="sxs-lookup"><span data-stu-id="5957a-533">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
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

### <a name="template-context-parameters"></a><span data-ttu-id="5957a-534">템플릿 컨텍스트 매개 변수</span><span class="sxs-lookup"><span data-stu-id="5957a-534">Template context parameters</span></span>

<span data-ttu-id="5957a-535">요소로 전달 된 `RenderFragment<T>` 형식의 구성 요소 인수에는 `context` 라는 암시적 매개 변수가 있습니다 (예: 앞의 코드 샘플에서 `@context.PetId`). 그러나 자식 요소의 `Context` 특성을 사용 하 여 매개 변수 이름을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-535">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="5957a-536">다음 예제에서 `RowTemplate` 요소의 `Context` 특성은 `pet` 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-536">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
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

<span data-ttu-id="5957a-537">또는 구성 요소 요소에 `Context` 특성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-537">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="5957a-538">지정 된 `Context` 특성은 모든 지정 된 템플릿 매개 변수에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-538">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="5957a-539">이는 자식 요소를 래핑하는 대신 암시적 자식 콘텐츠에 대 한 콘텐츠 매개 변수 이름을 지정 하려는 경우에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-539">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="5957a-540">다음 예제에서는 `Context` 특성이 `TableTemplate` 요소에 나타나고 모든 템플릿 매개 변수에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-540">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
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

### <a name="generic-typed-components"></a><span data-ttu-id="5957a-541">제네릭 형식의 구성 요소</span><span class="sxs-lookup"><span data-stu-id="5957a-541">Generic-typed components</span></span>

<span data-ttu-id="5957a-542">일반적으로 템플릿 구성 요소는 형식화 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-542">Templated components are often generically typed.</span></span> <span data-ttu-id="5957a-543">예를 들어 일반 `ListViewTemplate` 구성 요소를 사용 하 여 `IEnumerable<T>` 값을 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-543">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="5957a-544">제네릭 구성 요소를 정의 하려면 [`@typeparam`](xref:mvc/views/razor#typeparam) 지시어를 사용 하 여 형식 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-544">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="5957a-545">제네릭 형식의 구성 요소를 사용 하는 경우 가능 하면 형식 매개 변수가 유추 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-545">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="5957a-546">그렇지 않은 경우 형식 매개 변수의 이름과 일치 하는 특성을 사용 하 여 형식 매개 변수를 명시적으로 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-546">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="5957a-547">다음 예제에서 `TItem="Pet"`는 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-547">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="5957a-548">연계 값 및 매개 변수</span><span class="sxs-lookup"><span data-stu-id="5957a-548">Cascading values and parameters</span></span>

<span data-ttu-id="5957a-549">일부 시나리오에서는 [구성 요소 매개 변수](#component-parameters)를 사용 하 여 상위 구성 요소에서 하위 구성 요소로 데이터를 이동 하는 것이 불편 합니다. 특히 구성 요소 계층이 여러 개 있는 경우에는 더욱 그렇습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-549">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="5957a-550">연계 값 및 매개 변수는 상위 구성 요소에서 모든 하위 구성 요소에 값을 제공 하는 편리한 방법을 제공 하 여이 문제를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-550">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="5957a-551">연계 값 및 매개 변수를 사용 하 여 구성 요소를 조정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-551">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="5957a-552">테마 예</span><span class="sxs-lookup"><span data-stu-id="5957a-552">Theme example</span></span>

<span data-ttu-id="5957a-553">샘플 앱의 다음 예제에서 `ThemeInfo` 클래스는 응용 프로그램의 지정 된 부분에 있는 모든 단추가 동일한 스타일을 공유할 수 있도록 구성 요소 계층 구조의 아래로 이동 하는 테마 정보를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-553">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="5957a-554">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="5957a-554">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="5957a-555">상위 구성 요소는 연계 값 구성 요소를 사용 하 여 연계 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-555">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="5957a-556">`CascadingValue` 구성 요소는 구성 요소 계층의 하위 트리를 래핑하고 해당 하위 트리 내의 모든 구성 요소에 단일 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-556">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="5957a-557">예를 들어 샘플 앱은 `@Body` 속성의 레이아웃 본문을 구성 하는 모든 구성 요소에 대 한 연계 매개 변수로 앱 레이아웃 중 하나에서 테마 정보 (`ThemeInfo`)를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-557">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="5957a-558">`ButtonClass`에 레이아웃 구성 요소에서 `btn-success` 값이 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-558">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="5957a-559">모든 하위 구성 요소는 `ThemeInfo` 연계 개체를 통해이 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-559">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="5957a-560">`CascadingValuesParametersLayout` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="5957a-560">`CascadingValuesParametersLayout` component:</span></span>

```razor
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

<span data-ttu-id="5957a-561">연계 값을 사용 하기 위해 구성 요소는 `[CascadingParameter]` 특성을 사용 하 여 연계 매개 변수를 선언 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-561">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="5957a-562">연계 값은 유형별 매개 변수에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-562">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="5957a-563">샘플 앱에서 `CascadingValuesParametersTheme` 구성 요소는 `ThemeInfo` 연계 값을 연계 매개 변수에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-563">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="5957a-564">매개 변수는 구성 요소에 의해 표시 되는 단추 중 하나에 대해 CSS 클래스를 설정 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-564">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="5957a-565">`CascadingValuesParametersTheme` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="5957a-565">`CascadingValuesParametersTheme` component:</span></span>

```razor
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

<span data-ttu-id="5957a-566">동일한 하위 트리 내에서 동일한 형식의 여러 값을 계단식으로 배열 하려면 각 `CascadingValue` 구성 요소와 해당 `CascadingParameter`에 고유한 `Name` 문자열을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-566">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="5957a-567">다음 예에서는 두 개의 `CascadingValue` 구성 요소가 이름별로 `MyCascadingType`의 다른 인스턴스를 계단식으로 배열 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-567">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
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

<span data-ttu-id="5957a-568">하위 구성 요소에서 종속 매개 변수는 상위 구성 요소의 해당 하는 종속 된 값에서 이름으로 해당 값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-568">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="5957a-569">TabSet 예제</span><span class="sxs-lookup"><span data-stu-id="5957a-569">TabSet example</span></span>

<span data-ttu-id="5957a-570">연계 매개 변수를 사용 하면 구성 요소 계층 구조 전체에서 공동 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-570">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="5957a-571">예를 들어 샘플 앱에서 다음 *Tabset* 예제를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-571">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="5957a-572">샘플 앱에는 탭에서 구현 하는 `ITab` 인터페이스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-572">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="5957a-573">`CascadingValuesParametersTabSet` 구성 요소는 여러 `Tab` 구성 요소를 포함 하는 `TabSet` 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-573">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="5957a-574">자식 `Tab` 구성 요소는 `TabSet`에 대 한 매개 변수로 명시적으로 전달 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-574">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="5957a-575">대신 자식 `Tab` 구성 요소는 `TabSet`의 자식 콘텐츠에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-575">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="5957a-576">그러나 `TabSet`는 헤더와 활성 탭을 렌더링할 수 있도록 각 `Tab` 구성 요소에 대 한 정보를 계속 알고 있어야 합니다. 추가 코드를 요구 하지 않고이 조정을 사용 하기 위해 `TabSet` 구성 요소는 하위 `Tab` 구성 요소에 의해 선택 되는 *연계 값으로 자신을 제공할 수 있습니다* .</span><span class="sxs-lookup"><span data-stu-id="5957a-576">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="5957a-577">`TabSet` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="5957a-577">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="5957a-578">하위 `Tab` 구성 요소는 포함 하는 `TabSet`를 연계 매개 변수로 캡처하여 `Tab` 구성 요소가 `TabSet`에 추가 되 고 활성화 되는 탭을 조정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-578">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="5957a-579">`Tab` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="5957a-579">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="5957a-580">Razor 템플릿</span><span class="sxs-lookup"><span data-stu-id="5957a-580">Razor templates</span></span>

<span data-ttu-id="5957a-581">렌더링 조각은 Razor 템플릿 구문을 사용 하 여 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-581">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="5957a-582">Razor 템플릿은 UI 코드 조각을 정의 하 고 다음 형식을 가정 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-582">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="5957a-583">다음 예제에서는 `RenderFragment` 및 `RenderFragment<T>` 값을 지정 하 고 구성 요소에서 직접 템플릿을 렌더링 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-583">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="5957a-584">렌더링 조각은 [템플릿 기반 구성 요소](#templated-components)에 인수로 전달 될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-584">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

```razor
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

<span data-ttu-id="5957a-585">이전 코드의 렌더링 된 출력:</span><span class="sxs-lookup"><span data-stu-id="5957a-585">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="5957a-586">수동 RenderTreeBuilder 논리</span><span class="sxs-lookup"><span data-stu-id="5957a-586">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="5957a-587">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` 구성 요소를 코드에서 C# 수동으로 빌드하는 등 구성 요소 및 요소를 조작 하는 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-587">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="5957a-588">구성 요소를 만드는 데 `RenderTreeBuilder`를 사용 하는 것은 고급 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-588">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="5957a-589">형식이 잘못 된 구성 요소 (예: 닫히지 않은 태그 태그)는 정의 되지 않은 동작을 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-589">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="5957a-590">다른 구성 요소에 수동으로 빌드할 수 있는 다음 `PetDetails` 구성 요소를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-590">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="5957a-591">다음 예제에서 `CreateComponent` 메서드의 루프는 세 개의 `PetDetails` 구성 요소를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-591">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="5957a-592">`RenderTreeBuilder` 메서드를 호출 하 여 구성 요소를 만드는 경우 (`OpenComponent` 및 `AddAttribute`) 시퀀스 번호는 소스 코드 줄 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-592">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="5957a-593">Blazor 차이 알고리즘은 고유한 호출 호출을 제외 하 고 고유한 코드 줄에 해당 하는 시퀀스 번호를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-593">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="5957a-594">`RenderTreeBuilder` 메서드를 사용 하 여 구성 요소를 만드는 경우 시퀀스 번호에 대 한 인수를 하드 코딩 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-594">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="5957a-595">**계산 또는 카운터를 사용 하 여 시퀀스 번호를 생성 하면 성능이 저하 될 수 있습니다.**</span><span class="sxs-lookup"><span data-stu-id="5957a-595">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="5957a-596">자세한 내용은 [시퀀스 번호를 코드 줄 번호와 관련 되 고 실행 순서와는 관련이 없습니다](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) . 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-596">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="5957a-597">`BuiltContent` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="5957a-597">`BuiltContent` component:</span></span>

```razor
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

> [!WARNING]
> <span data-ttu-id="5957a-598">`Microsoft.AspNetCore.Components.RenderTree` 형식을 사용 하면 렌더링 작업의 *결과* 를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-598">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="5957a-599">Blazor 프레임 워크 구현의 내부 세부 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-599">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="5957a-600">이러한 형식은 *불안정* 한 것으로 간주 되며 이후 릴리스에서 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-600">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="5957a-601">시퀀스 번호는 코드 줄 번호와 관련 되 고 실행 순서와는 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-601">Sequence numbers relate to code line numbers and not execution order</span></span>

Blazor<span data-ttu-id="5957a-602"> `.razor` 파일은 항상 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-602"> `.razor` files are always compiled.</span></span> <span data-ttu-id="5957a-603">이는 컴파일 단계를 사용 하 여 런타임 시 앱 성능을 향상 시키는 정보를 주입할 수 있기 때문에 `.razor`에 매우 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-603">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="5957a-604">이러한 향상 된 기능을 포함 하는 주요 예로 *시퀀스 번호가*있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-604">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="5957a-605">시퀀스 번호는 코드의 고유 하 고 정렬 된 줄에서 제공 되는 출력을 런타임에 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-605">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="5957a-606">런타임은이 정보를 사용 하 여 선형 시간에 효율적인 트리 차이을 생성 합니다 .이는 일반적으로 일반적인 트리 diff 알고리즘에 대해 가능한 것 보다 훨씬 빠릅니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-606">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="5957a-607">다음 Razor 구성 요소 (*razor*) 파일을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-607">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="5957a-608">위의 코드는 다음과 같은 항목으로 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-608">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="5957a-609">코드가 처음 실행 될 때 `someFlag` `true`경우 작성기는 다음을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-609">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="5957a-610">Sequence</span><span class="sxs-lookup"><span data-stu-id="5957a-610">Sequence</span></span> | <span data-ttu-id="5957a-611">형식</span><span class="sxs-lookup"><span data-stu-id="5957a-611">Type</span></span>      | <span data-ttu-id="5957a-612">데이터</span><span class="sxs-lookup"><span data-stu-id="5957a-612">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="5957a-613">0</span><span class="sxs-lookup"><span data-stu-id="5957a-613">0</span></span>        | <span data-ttu-id="5957a-614">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="5957a-614">Text node</span></span> | <span data-ttu-id="5957a-615">First</span><span class="sxs-lookup"><span data-stu-id="5957a-615">First</span></span>  |
| <span data-ttu-id="5957a-616">1</span><span class="sxs-lookup"><span data-stu-id="5957a-616">1</span></span>        | <span data-ttu-id="5957a-617">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="5957a-617">Text node</span></span> | <span data-ttu-id="5957a-618">Second</span><span class="sxs-lookup"><span data-stu-id="5957a-618">Second</span></span> |

<span data-ttu-id="5957a-619">`someFlag` `false`되 고 태그가 다시 렌더링 된다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-619">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="5957a-620">이번에는 작성기가 다음을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-620">This time, the builder receives:</span></span>

| <span data-ttu-id="5957a-621">Sequence</span><span class="sxs-lookup"><span data-stu-id="5957a-621">Sequence</span></span> | <span data-ttu-id="5957a-622">형식</span><span class="sxs-lookup"><span data-stu-id="5957a-622">Type</span></span>       | <span data-ttu-id="5957a-623">데이터</span><span class="sxs-lookup"><span data-stu-id="5957a-623">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="5957a-624">1</span><span class="sxs-lookup"><span data-stu-id="5957a-624">1</span></span>        | <span data-ttu-id="5957a-625">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="5957a-625">Text node</span></span>  | <span data-ttu-id="5957a-626">Second</span><span class="sxs-lookup"><span data-stu-id="5957a-626">Second</span></span> |

<span data-ttu-id="5957a-627">런타임에서 diff를 수행 하는 경우 시퀀스 `0`의 항목이 제거 된 것으로 확인 되므로 다음과 같은 trivial *편집 스크립트*를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-627">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="5957a-628">첫 번째 텍스트 노드를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-628">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="5957a-629">프로그래밍 방식으로 시퀀스 번호를 생성 하는 경우 문제가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-629">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="5957a-630">대신 다음과 같은 렌더링 트리 작성기 논리를 작성 했다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-630">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="5957a-631">이제 첫 번째 출력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-631">Now, the first output is:</span></span>

| <span data-ttu-id="5957a-632">Sequence</span><span class="sxs-lookup"><span data-stu-id="5957a-632">Sequence</span></span> | <span data-ttu-id="5957a-633">형식</span><span class="sxs-lookup"><span data-stu-id="5957a-633">Type</span></span>      | <span data-ttu-id="5957a-634">데이터</span><span class="sxs-lookup"><span data-stu-id="5957a-634">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="5957a-635">0</span><span class="sxs-lookup"><span data-stu-id="5957a-635">0</span></span>        | <span data-ttu-id="5957a-636">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="5957a-636">Text node</span></span> | <span data-ttu-id="5957a-637">First</span><span class="sxs-lookup"><span data-stu-id="5957a-637">First</span></span>  |
| <span data-ttu-id="5957a-638">1</span><span class="sxs-lookup"><span data-stu-id="5957a-638">1</span></span>        | <span data-ttu-id="5957a-639">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="5957a-639">Text node</span></span> | <span data-ttu-id="5957a-640">Second</span><span class="sxs-lookup"><span data-stu-id="5957a-640">Second</span></span> |

<span data-ttu-id="5957a-641">이 결과는 이전 사례와 동일 하므로 부정적인 문제가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-641">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="5957a-642">`someFlag`은 두 번째 렌더링에서 `false` 되며 출력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-642">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="5957a-643">Sequence</span><span class="sxs-lookup"><span data-stu-id="5957a-643">Sequence</span></span> | <span data-ttu-id="5957a-644">형식</span><span class="sxs-lookup"><span data-stu-id="5957a-644">Type</span></span>      | <span data-ttu-id="5957a-645">데이터</span><span class="sxs-lookup"><span data-stu-id="5957a-645">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="5957a-646">0</span><span class="sxs-lookup"><span data-stu-id="5957a-646">0</span></span>        | <span data-ttu-id="5957a-647">텍스트 노드</span><span class="sxs-lookup"><span data-stu-id="5957a-647">Text node</span></span> | <span data-ttu-id="5957a-648">Second</span><span class="sxs-lookup"><span data-stu-id="5957a-648">Second</span></span> |

<span data-ttu-id="5957a-649">이번에는 diff 알고리즘은 *두 가지* 변경이 발생 했음을 확인 하 고 알고리즘은 다음과 같은 편집 스크립트를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-649">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="5957a-650">첫 번째 텍스트 노드의 값을 `Second`변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-650">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="5957a-651">두 번째 텍스트 노드를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-651">Remove the second text node.</span></span>

<span data-ttu-id="5957a-652">시퀀스 번호를 생성 하면 `if/else` 분기와 루프가 원래 코드에 표시 된 위치에 대 한 모든 유용한 정보가 손실 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-652">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="5957a-653">이로 인해 이전 처럼 비교가 **두 번** 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-653">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="5957a-654">이는 간단한 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-654">This is a trivial example.</span></span> <span data-ttu-id="5957a-655">복잡 하 고 많이 중첩 된 구조와 특히 루프를 사용 하는 현실적인 경우에는 성능 비용이 더 심각 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-655">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="5957a-656">삽입 또는 제거 된 루프 블록 또는 분기를 즉시 식별 하는 대신, diff 알고리즘은 렌더링 트리를 깊이 있게 구분 해야 하며, 일반적으로 이전 및 새 구조를 misinformed 하기 때문에 훨씬 더 긴 편집 스크립트를 작성 해야 합니다. 서로 관련 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-656">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="5957a-657">지침 및 결론</span><span class="sxs-lookup"><span data-stu-id="5957a-657">Guidance and conclusions</span></span>

* <span data-ttu-id="5957a-658">시퀀스 번호가 동적으로 생성 되 면 앱 성능이 저하 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-658">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="5957a-659">컴파일 시간에 캡처되는 경우를 제외 하 고 필요한 정보가 존재 하지 않기 때문에 프레임 워크는 런타임에 고유한 시퀀스 번호를 자동으로 만들 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-659">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="5957a-660">수동으로 구현 된 `RenderTreeBuilder` 논리의 긴 블록을 작성 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-660">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="5957a-661">`.razor` 파일을 선호 하 고 컴파일러가 시퀀스 번호를 처리할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-661">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="5957a-662">수동 `RenderTreeBuilder` 논리를 방지할 수 없는 경우 긴 코드 블록을 `OpenRegion`/`CloseRegion` 호출에 래핑된 작은 조각으로 분할 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-662">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="5957a-663">각 영역에는 고유한 시퀀스 번호 공간이 있으므로 각 지역 내에서 0 (또는 다른 임의의 임의의 숫자)으로 다시 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-663">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="5957a-664">시퀀스 번호가 하드 코딩 된 경우 diff 알고리즘에서는 값이 증가 하는 시퀀스 번호만 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-664">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="5957a-665">초기 값과 간격은 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-665">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="5957a-666">한 가지 합법적인 옵션은 코드 줄 번호를 시퀀스 번호로 사용 하거나 0부터 시작 하 여 또는 수백 (또는 선호 하는 간격) 만큼 증가 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-666">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="5957a-667">는 시퀀스 번호를 사용 하는 반면 다른 트리 diff UI 프레임 워크는이를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-667"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="5957a-668">Diff는 시퀀스 번호를 사용 하는 경우 훨씬 더 빠르며, Blazor은 *razor* 파일을 작성 하는 개발자를 위해 자동으로 시퀀스 번호를 처리 하는 컴파일 단계를 활용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-668">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="localization"></a><span data-ttu-id="5957a-669">지역화</span><span class="sxs-lookup"><span data-stu-id="5957a-669">Localization</span></span>

Blazor<span data-ttu-id="5957a-670"> Server 앱은 [지역화 미들웨어](xref:fundamentals/localization#localization-middleware)를 사용 하 여 지역화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-670"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="5957a-671">미들웨어는 앱에서 리소스를 요청 하는 사용자에 게 적절 한 문화권을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-671">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="5957a-672">문화권은 다음 방법 중 하나를 사용 하 여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-672">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="5957a-673">쿠키</span><span class="sxs-lookup"><span data-stu-id="5957a-673">Cookies</span></span>](#cookies)
* [<span data-ttu-id="5957a-674">문화권을 선택 하는 UI 제공</span><span class="sxs-lookup"><span data-stu-id="5957a-674">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="5957a-675">자세한 내용과 예제를 보려면 <xref:fundamentals/localization>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="5957a-675">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="5957a-676">국제화를 위한 링커 구성 (Blazor Weasembmbambmbemboma)</span><span class="sxs-lookup"><span data-stu-id="5957a-676">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="5957a-677">기본적으로 Blazor WebAssembly 앱에 대한 Blazor의 링커 구성은 명시적으로 요청된 로캘을 제외하고 국제화 정보를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-677">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="5957a-678">링커 동작을 제어 하는 방법에 대 한 자세한 내용과 지침은 <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-678">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="5957a-679">쿠키</span><span class="sxs-lookup"><span data-stu-id="5957a-679">Cookies</span></span>

<span data-ttu-id="5957a-680">지역화 문화권 쿠키는 사용자의 문화권을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-680">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="5957a-681">쿠키는 앱의 호스트 페이지 (*Pages/host-a*)의 `OnGet` 메서드에서 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-681">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="5957a-682">지역화 미들웨어는 후속 요청에서 쿠키를 읽어 사용자의 culture를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-682">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="5957a-683">쿠키를 사용 하면 WebSocket 연결이 문화권을 올바르게 전파할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-683">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="5957a-684">지역화 체계가 URL 경로 또는 쿼리 문자열을 기반으로 하는 경우 스키마는 Websocket을 사용 하지 못할 수 있으므로 문화권이 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-684">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="5957a-685">따라서 지역화 문화권 쿠키를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-685">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="5957a-686">문화권이 지역화 쿠키에 유지 되는 경우 모든 기술을 사용 하 여 문화권을 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-686">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="5957a-687">서버 쪽 ASP.NET Core에 대해 설정 된 지역화 체계가 앱에 이미 있는 경우 앱의 기존 지역화 인프라를 계속 사용 하 고 앱의 체계 내에서 지역화 문화권 쿠키를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-687">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="5957a-688">다음 예제에서는 지역화 미들웨어에서 읽을 수 있는 쿠키의 현재 문화권을 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-688">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="5957a-689">Blazor Server 앱에서 다음 내용을 사용 하 여 *Pages/Host. cshtml* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-689">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="5957a-690">지역화는 앱에서 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-690">Localization is handled in the app:</span></span>

1. <span data-ttu-id="5957a-691">브라우저가 앱에 초기 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-691">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="5957a-692">문화권이 지역화 미들웨어에 의해 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-692">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="5957a-693">*_Host* 의 `OnGet` 메서드는 응답의 일부로 쿠키의 문화권을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-693">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="5957a-694">브라우저는 WebSocket 연결을 열어 대화형 Blazor 서버 세션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-694">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="5957a-695">지역화 미들웨어는 쿠키를 읽고 문화권을 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-695">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="5957a-696">Blazor 서버 세션이 올바른 문화권으로 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-696">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="5957a-697">문화권을 선택 하는 UI 제공</span><span class="sxs-lookup"><span data-stu-id="5957a-697">Provide UI to choose the culture</span></span>

<span data-ttu-id="5957a-698">사용자가 문화권을 선택할 수 있도록 UI를 제공 하려면 *리디렉션 기반 방법을* 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-698">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="5957a-699">이 프로세스는 사용자가 로그인 페이지로 리디렉션되고 다시 원래 리소스로 리디렉션되는&mdash;사용자가 보안 리소스에 액세스 하려고 할 때 웹 앱에서 발생 하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-699">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="5957a-700">앱은 컨트롤러에 대 한 리디렉션을 통해 사용자가 선택한 문화권을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-700">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="5957a-701">컨트롤러는 사용자가 선택한 문화권을 쿠키로 설정 하 고 사용자를 다시 원래 URI로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-701">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="5957a-702">서버에서 HTTP 끝점을 설정 하 여 사용자가 선택한 문화권을 쿠키에 설정 하 고 다시 원래 URI로 리디렉션을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-702">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="5957a-703">`LocalRedirect` 작업 결과를 사용 하 여 열린 리디렉션 공격을 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-703">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="5957a-704">자세한 내용은 <xref:security/preventing-open-redirects>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-704">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="5957a-705">다음 구성 요소는 사용자가 문화권을 선택할 때 초기 리디렉션을 수행 하는 방법의 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-705">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
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

### <a name="use-net-localization-scenarios-in-opno-locblazor-apps"></a><span data-ttu-id="5957a-706">Blazor apps에서 .NET 지역화 시나리오 사용</span><span class="sxs-lookup"><span data-stu-id="5957a-706">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="5957a-707">Blazor apps 내에서 다음과 같은 .NET 지역화 및 세계화 시나리오를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-707">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="5957a-708">. NET의 리소스 시스템</span><span class="sxs-lookup"><span data-stu-id="5957a-708">.NET's resources system</span></span>
* <span data-ttu-id="5957a-709">문화권별 숫자 및 날짜 형식 지정</span><span class="sxs-lookup"><span data-stu-id="5957a-709">Culture-specific number and date formatting</span></span>

Blazor<span data-ttu-id="5957a-710">의 `@bind` 기능은 사용자의 현재 문화권에 따라 세계화를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-710">'s `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="5957a-711">자세한 내용은 [데이터 바인딩](#data-binding) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-711">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="5957a-712">제한 된 ASP.NET Core의 지역화 시나리오 집합이 현재 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-712">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="5957a-713">`IStringLocalizer<>`은 Blazor 앱에서 *지원 됩니다* .</span><span class="sxs-lookup"><span data-stu-id="5957a-713">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="5957a-714">`IHtmlLocalizer<>`, `IViewLocalizer<>`및 데이터 주석 지역화는 MVC 시나리오 ASP.NET Core Blazor 앱에서 **지원 되지 않습니다** .</span><span class="sxs-lookup"><span data-stu-id="5957a-714">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="5957a-715">자세한 내용은 <xref:fundamentals/localization>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5957a-715">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="5957a-716">SVG (스케일러블 벡터 그래픽) 이미지</span><span class="sxs-lookup"><span data-stu-id="5957a-716">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="5957a-717">Blazor는 HTML을 렌더링 하므로 SVG (확장 가능한 벡터 그래픽) 이미지 (*svg*)를 비롯 한 브라우저 지원 이미지는 `<img>` 태그를 통해 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-717">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="5957a-718">마찬가지로, 스타일 시트 파일 ( *.css*)의 css 규칙에서 SVG 이미지가 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-718">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="5957a-719">그러나 인라인 SVG 태그는 일부 시나리오에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-719">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="5957a-720">구성 요소 파일 (*razor*)에 `<svg>` 태그를 직접 저장 하는 경우 기본 이미지 렌더링이 지원 되지만 많은 고급 시나리오는 아직 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-720">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="5957a-721">예를 들어 `<use>` 태그는 현재 적용 되지 않으며 `@bind` 일부 SVG 태그와 함께 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-721">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="5957a-722">향후 릴리스에서 이러한 제한을 해결할 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-722">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5957a-723">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5957a-723">Additional resources</span></span>

* <span data-ttu-id="5957a-724"><xref:security/blazor/server> &ndash;에는 리소스 고갈에 경합 해야 하는 Blazor Server 앱 빌드에 대 한 지침이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5957a-724"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
