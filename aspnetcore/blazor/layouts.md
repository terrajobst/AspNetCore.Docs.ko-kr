---
title: ASP.NET Core Blazor 레이아웃
author: guardrex
description: Blazor apps를 위한 재사용 가능한 레이아웃 구성 요소를 만드는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/layouts
ms.openlocfilehash: 90acfb0d4e9daadb12be79de6bd0c99fc545697a
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944059"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a><span data-ttu-id="4bd4a-103">ASP.NET Core Blazor 레이아웃</span><span class="sxs-lookup"><span data-stu-id="4bd4a-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="4bd4a-104">[Rainer Stropek](https://www.timecockpit.com) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4bd4a-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4bd4a-105">메뉴, 저작권 메시지 및 회사 로고와 같은 일부 앱 요소는 일반적으로 앱의 전체 레이아웃에 포함 되며 앱의 모든 구성 요소에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="4bd4a-106">응용 프로그램의 모든 구성 요소에 이러한 요소의 코드를 복사 하는 것은 요소 중 하나에 업데이트가 필요할 때마다 모든 구성 요소를 업데이트 해야 하기 때문에&mdash;효율적인 방법이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="4bd4a-107">이러한 중복은 유지 관리 하기 어렵고 시간이 지남에 따라 일관 되지 않은 콘텐츠가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="4bd4a-108">*레이아웃* 은이 문제를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="4bd4a-109">기술적으로 레이아웃은 또 다른 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="4bd4a-110">레이아웃은 Razor 템플릿 또는 C# 코드에서 정의 되며 [데이터 바인딩](xref:blazor/components#data-binding), [종속성 주입](xref:blazor/dependency-injection)및 기타 구성 요소 시나리오를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="4bd4a-111">*구성* 요소를 *레이아웃*으로 전환 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="4bd4a-112">레이아웃 안에 렌더링 된 콘텐츠의 `Body` 속성을 정의 하는 `LayoutComponentBase`에서 상속 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="4bd4a-113">Razor 구문 `@Body`를 사용 하 여 콘텐츠가 렌더링 되는 레이아웃 태그의 위치를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="4bd4a-114">다음 코드 샘플은 레이아웃 구성 요소인 *Mainlayout razor*의 razor 템플릿을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="4bd4a-115">레이아웃은 `LayoutComponentBase`를 상속 하 고 탐색 표시줄과 바닥글 사이에 `@Body`를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="4bd4a-116">Blazor 앱 템플릿 중 하나를 기반으로 하는 앱에서 `MainLayout` 구성 요소 (*Mainlayout. razor*)는 앱의 *공유* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="4bd4a-117">기본 레이아웃</span><span class="sxs-lookup"><span data-stu-id="4bd4a-117">Default layout</span></span>

<span data-ttu-id="4bd4a-118">앱의 *응용 프로그램 razor* 파일의 `Router` 구성 요소에서 기본 앱 레이아웃을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="4bd4a-119">기본 Blazor 템플릿에서 제공 하는 다음과 같은 `Router` 구성 요소는 기본 레이아웃을 `MainLayout` 구성 요소로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="4bd4a-120">`NotFound` 콘텐츠에 대 한 기본 레이아웃을 제공 하려면 `NotFound` 콘텐츠에 대 한 `LayoutView`를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="4bd4a-121">`Router` 구성 요소에 대 한 자세한 내용은 <xref:blazor/routing>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="4bd4a-122">구성 요소 또는 폴더 단위로 재정의 될 수 있으므로, 레이아웃을 라우터에 기본 레이아웃으로 지정 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="4bd4a-123">가장 일반적인 방법인 라우터를 사용 하 여 앱의 기본 레이아웃을 설정 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="4bd4a-124">구성 요소에 레이아웃 지정</span><span class="sxs-lookup"><span data-stu-id="4bd4a-124">Specify a layout in a component</span></span>

<span data-ttu-id="4bd4a-125">Razor 지시문 `@layout`를 사용 하 여 레이아웃을 구성 요소에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="4bd4a-126">컴파일러는 `@layout`를 구성 요소 클래스에 적용 되는 `LayoutAttribute`으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="4bd4a-127">다음 `MasterList` 구성 요소의 내용이 `@Body`위치에서 `MasterLayout` 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="4bd4a-128">구성 요소에서 직접 레이아웃을 지정 하면 라우터에서 설정 된 *기본 레이아웃* 또는 *_Imports*에서 가져온 `@layout` 지시문이 재정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="4bd4a-129">중앙 레이아웃 선택</span><span class="sxs-lookup"><span data-stu-id="4bd4a-129">Centralized layout selection</span></span>

<span data-ttu-id="4bd4a-130">앱의 모든 폴더에는 선택적으로 *_Imports*이름이 지정 된 템플릿 파일이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="4bd4a-131">컴파일러는 동일한 폴더에 있는 모든 Razor 템플릿의 imports 파일에 지정 된 지시문을 포함 하 고 모든 하위 폴더에서 재귀적으로 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="4bd4a-132">따라서 `@layout MyCoolLayout` 포함 된 *_Imports razor* 파일은 폴더의 모든 구성 요소가 `MyCoolLayout`를 사용 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="4bd4a-133">폴더 및 하위 폴더 내의 모든 *razor* 파일에 `@layout MyCoolLayout`를 반복적으로 추가할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="4bd4a-134">`@using` 지시문은 동일한 방식으로 구성 요소에도 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="4bd4a-135">다음 *_Imports razor* 파일 가져오기:</span><span class="sxs-lookup"><span data-stu-id="4bd4a-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="4bd4a-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="4bd4a-137">동일한 폴더와 하위 폴더에 있는 모든 Razor 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="4bd4a-138">`BlazorApp1.Data` 네임스페이스.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="4bd4a-139">*_Imports razor* 파일은 [razor 뷰 및 페이지에 대 한 _ViewImports.](xref:mvc/views/layout#importing-shared-directives) s s o p a s 파일과 유사 하지만 특히 razor 구성 요소 파일에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="4bd4a-140">_Imports 레이아웃을 지정 하면 라우터의 *기본 레이아웃*으로 지정 된 레이아웃이 재정의 *됩니다.*</span><span class="sxs-lookup"><span data-stu-id="4bd4a-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="4bd4a-141">중첩 레이아웃</span><span class="sxs-lookup"><span data-stu-id="4bd4a-141">Nested layouts</span></span>

<span data-ttu-id="4bd4a-142">앱은 중첩 된 레이아웃으로 구성 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="4bd4a-143">구성 요소는 다른 레이아웃을 참조 하는 레이아웃을 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="4bd4a-144">예를 들어 중첩 레이아웃은 다중 수준 메뉴 구조를 만드는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="4bd4a-145">다음 예제에서는 중첩 된 레이아웃을 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="4bd4a-146">*EpisodesComponent* 파일은 표시할 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="4bd4a-147">구성 요소가 `MasterListLayout`를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="4bd4a-148">*Masterlistlayout. razor* 파일은 `MasterListLayout`를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="4bd4a-149">레이아웃은 렌더링 되는 다른 레이아웃 `MasterLayout`를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="4bd4a-150">`EpisodesComponent`은 `@Body` 표시 되는 위치에 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="4bd4a-151">마지막으로 *masterlayout에 `MasterLayout` 합니다. razor* 는 머리글, 주 메뉴, 바닥글 등의 최상위 레이아웃 요소를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="4bd4a-152">`EpisodesComponent` `MasterListLayout`는 `@Body` 표시 되는 위치에 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4bd4a-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a><span data-ttu-id="4bd4a-153">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4bd4a-153">Additional resources</span></span>

* <xref:mvc/views/layout>
