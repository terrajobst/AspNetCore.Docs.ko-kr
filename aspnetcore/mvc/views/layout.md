---
title: ASP.NET Core의 레이아웃
author: ardalis
description: ASP.NET Core 앱에서 뷰를 렌더링하기 전에 일반적인 레이아웃을 사용하고, 지시문을 공유하고, 공용 코드를 실행하는 방법을 알아봅니다.
ms.author: riande
ms.date: 07/30/2019
uid: mvc/views/layout
ms.openlocfilehash: 9a7b8003b24329f6e9cbd349ee47f6844b7c5f6d
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238036"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="2035c-103">ASP.NET Core의 레이아웃</span><span class="sxs-lookup"><span data-stu-id="2035c-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="2035c-104">작성자: [Steve Smith](https://ardalis.com/) 및 [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="2035c-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="2035c-105">페이지 및 보기는 시각적 개체 및 프로그래밍 요소를 자주 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="2035c-106">이 문서에서는 다음을 수행하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="2035c-107">일반적인 레이아웃 사용.</span><span class="sxs-lookup"><span data-stu-id="2035c-107">Use common layouts.</span></span>
* <span data-ttu-id="2035c-108">지시문 공유.</span><span class="sxs-lookup"><span data-stu-id="2035c-108">Share directives.</span></span>
* <span data-ttu-id="2035c-109">페이지 또는 보기를 렌더링하기 전에 일반적인 코드 실행.</span><span class="sxs-lookup"><span data-stu-id="2035c-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="2035c-110">이 문서에서는 ASP.NET Core MVC: Razor Pages 및 보기를 사용하는 컨트롤러에 대한 두 가지 방식의 레이아웃을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: Razor Pages and controllers with views.</span></span> <span data-ttu-id="2035c-111">이 항목에서는 차이점이 최소화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-111">For this topic, the differences are minimal:</span></span>

* <span data-ttu-id="2035c-112">Razor Pages는 *Pages* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-112">Razor Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="2035c-113">보기를 사용하는 컨트롤러는 *Views* 폴더의 보기를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="2035c-114">레이아웃이란</span><span class="sxs-lookup"><span data-stu-id="2035c-114">What is a Layout</span></span>

<span data-ttu-id="2035c-115">대부분의 웹앱에는 사용자가 페이지를 탐색하는 동안 일관된 환경을 제공하는 일반적인 레이아웃이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="2035c-116">일반적으로 레이아웃에는 앱 헤더, 탐색 또는 메뉴 요소, 바닥글과 같은 공통 사용자 인터페이스 요소가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![페이지 레이아웃 예제](layout/_static/page-layout.png)

<span data-ttu-id="2035c-118">스크립트 및 스타일시트와 같은 공통 HTML 구조는 앱 내에서 여러 페이지에서도 자주 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="2035c-119">이러한 모든 공유 요소를 *레이아웃* 파일에 정의한 후 앱 내에 사용된 모든 뷰에서 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="2035c-120">레이아웃은 보기에서 중복 코드를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="2035c-121">규칙에 따라, ASP.NET Core 앱의 기본 레이아웃 이름을 *_Layout.cshtml*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml*.</span></span> <span data-ttu-id="2035c-122">템플릿을 사용하여 생성된 새로운 ASP.NET Core 프로젝트의 레이아웃 파일:</span><span class="sxs-lookup"><span data-stu-id="2035c-122">The layout files for new ASP.NET Core projects created with the templates are:</span></span>

* <span data-ttu-id="2035c-123">Razor Pages: *Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2035c-123">Razor Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![솔루션 탐색기의 Pages 폴더](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="2035c-125">보기를 사용하는 컨트롤러: *Views/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2035c-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

  ![솔루션 탐색기의 Views 폴더](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="2035c-127">이 레이아웃은 앱의 뷰에 대한 최상위 수준 템플릿을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="2035c-128">앱에는 레이아웃이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-128">Apps don't require a layout.</span></span> <span data-ttu-id="2035c-129">앱은 다른 레이아웃을 지정하는 서로 다른 뷰를 포함하는 둘 이상의 레이아웃을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="2035c-130">다음 코드는 컨트롤러 및 보기를 사용하여 만든 템플릿 프로젝트의 레이아웃 파일을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="2035c-131">레이아웃 지정</span><span class="sxs-lookup"><span data-stu-id="2035c-131">Specifying a Layout</span></span>

<span data-ttu-id="2035c-132">Razor 뷰는 `Layout` 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-132">Razor views have a `Layout` property.</span></span> <span data-ttu-id="2035c-133">이 속성을 설정하여 레이아웃을 지정하는 개별 뷰:</span><span class="sxs-lookup"><span data-stu-id="2035c-133">Individual views specify a layout by setting this property:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="2035c-134">지정된 레이아웃은 전체 경로(예: */Pages/Shared/_Layout.cshtml* 또는 */Views/Shared/_Layout.cshtml*) 또는 부분적인 이름(예: `_Layout`)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml*) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="2035c-135">부분 이름이 제공되면 Razor 뷰 엔진이 표준 검색 프로세스를 사용하여 레이아웃 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-135">When a partial name is provided, the Razor view engine searches for the layout file using its standard discovery process.</span></span> <span data-ttu-id="2035c-136">처리기 메서드(또는 컨트롤러)가 있는 폴더가 먼저 검색된 후 *공유* 폴더가 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="2035c-137">이 검색 프로세스는 [부분 뷰](xref:mvc/views/partial#partial-view-discovery)를 검색하는 데 사용된 프로세스와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-137">This discovery process is identical to the process used to discover [partial views](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="2035c-138">기본적으로 모든 레이아웃에서 `RenderBody`를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="2035c-139">`RenderBody` 호출이 배치될 때마다 뷰의 내용이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a><span data-ttu-id="2035c-140">섹션</span><span class="sxs-lookup"><span data-stu-id="2035c-140">Sections</span></span>

<span data-ttu-id="2035c-141">레이아웃은 `RenderSection`을 호출하여 필요에 따라 하나 이상의 *섹션*을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-141">A layout can optionally reference one or more *sections*, by calling `RenderSection`.</span></span> <span data-ttu-id="2035c-142">섹션에서는 특정 페이지 요소를 배치할 위치를 구성하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="2035c-143">`RenderSection` 호출 때마다 섹션이 필수 또는 옵션인지 여부를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
@section Scripts {
    @RenderSection("Scripts", required: false)
}
```

<span data-ttu-id="2035c-144">필수 섹션이 없는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="2035c-145">개별 뷰는 `@section` Razor 구문을 사용하여 섹션 내에 렌더링될 콘텐츠를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-145">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="2035c-146">페이지 또는 보기에서 섹션을 정의하는 경우 렌더링되어야 합니다(그렇지 않은 경우 오류 발생).</span><span class="sxs-lookup"><span data-stu-id="2035c-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="2035c-147">Razor Pages 보기의 `@section` 정의 예:</span><span class="sxs-lookup"><span data-stu-id="2035c-147">An example `@section` definition in Razor Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
}
```

<span data-ttu-id="2035c-148">이전 코드에서는 *scripts/main.js*가 페이지 또는 보기의 `scripts` 섹션에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="2035c-149">동일한 앱의 다른 페이지 또는 보기는 이 스크립트가 필요하지 않을 수 있으며, 스크립트 섹션을 정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="2035c-150">다음 태그는 [부분 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)를 사용하여 *_ValidationScriptsPartial.cshtml*을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml*:</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="2035c-151">이전의 태그는 [ID를 스캐폴딩](xref:security/authentication/scaffold-identity)하여 생성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-151">The preceding markup was generated by [scaffolding Identity](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="2035c-152">페이지 또는 보기에 정의된 섹션은 즉시 레이아웃 페이지에서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="2035c-153">이들은 부분 뷰, 뷰 구성 요소 또는 뷰 시스템의 다른 부분에서 참조할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="2035c-154">섹션 무시</span><span class="sxs-lookup"><span data-stu-id="2035c-154">Ignoring sections</span></span>

<span data-ttu-id="2035c-155">기본적으로 콘텐츠 페이지 본문 및 모든 섹션은 레이아웃 페이지에서 모두 렌더링되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="2035c-156">Razor 뷰 엔진은 본문과 각 섹션이 렌더링되었는지 여부를 추적하여 이를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-156">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="2035c-157">뷰 엔진이 본문 또는 섹션을 무시하도록 지시하려면 `IgnoreBody` 및 `IgnoreSection` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="2035c-158">Razor 페이지의 본문 및 모든 섹션은 렌더링되거나 무시되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-158">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="2035c-159">공유 지시문 가져오기</span><span class="sxs-lookup"><span data-stu-id="2035c-159">Importing Shared Directives</span></span>

<span data-ttu-id="2035c-160">보기 및 페이지는 Razor 지시문을 사용하여 네임스페이스를 가져오고 [종속성 주입](dependency-injection.md)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-160">Views and pages can use Razor directives to importing namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="2035c-161">여러 보기에서 공유하는 지시문을 공용 *_ViewImports.cshtml* 파일에 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="2035c-162">`_ViewImports` 파일은 다음 지시문을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="2035c-163">이 파일은 다른 Razor 기능(예: 함수 및 섹션 정의)을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-163">The file doesn't support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="2035c-164">샘플 `_ViewImports.cshtml` 파일:</span><span class="sxs-lookup"><span data-stu-id="2035c-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="2035c-165">ASP.NET Core MVC 앱에 대한 *_ViewImports.cshtml* 파일은 일반적으로 *Pages*(또는 *Views*) 폴더에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="2035c-166">*_ViewImports.cshtml* 파일은 모든 폴더 내에 배치할 수 있으며, 이 경우 해당 폴더 및 해당 하위 폴더 내의 페이지 또는 보기에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="2035c-167">`_ViewImports` 파일은 루트 수준부터 처리된 후 각 폴더의 페이지 또는 보기 자체의 위치까지 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="2035c-168">루트 수준에 지정된 `_ViewImports` 설정은 폴더 수준에서 재정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="2035c-169">예를 들어 다음을 가정해 보세요.</span><span class="sxs-lookup"><span data-stu-id="2035c-169">For example, suppose:</span></span>

* <span data-ttu-id="2035c-170">루트 수준 *_ViewImports.cshtml* 파일에 `@model MyModel1` 및 `@addTagHelper *, MyTagHelper1`이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="2035c-171">하위 폴더 *_ViewImports.cshtml* 파일에 `@model MyModel2` 및 `@addTagHelper *, MyTagHelper2`이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="2035c-172">하위 폴더의 페이지 및 보기는 태그 도우미와 `MyModel2` 모델에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="2035c-173">파일 계층 구조에 *_ViewImports.cshtml* 파일이 여러 개 있을 경우 지시문의 결합된 동작은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="2035c-174">`@addTagHelper`, `@removeTagHelper`: 순서대로 모두 실행</span><span class="sxs-lookup"><span data-stu-id="2035c-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="2035c-175">`@tagHelperPrefix`: 뷰에 가장 가까운 것이 다른 것보다 우선함</span><span class="sxs-lookup"><span data-stu-id="2035c-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="2035c-176">`@model`: 뷰에 가장 가까운 것이 다른 것보다 우선함</span><span class="sxs-lookup"><span data-stu-id="2035c-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="2035c-177">`@inherits`: 뷰에 가장 가까운 것이 다른 것보다 우선함</span><span class="sxs-lookup"><span data-stu-id="2035c-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="2035c-178">`@using`: 모두 포함됨. 중복 항목은 무시됨</span><span class="sxs-lookup"><span data-stu-id="2035c-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="2035c-179">`@inject`: 각 속성에 대해 뷰에 가장 가까운 것이 같은 속성 이름의 다른 것보다 우선함</span><span class="sxs-lookup"><span data-stu-id="2035c-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="2035c-180">각 뷰 이전에 코드 실행</span><span class="sxs-lookup"><span data-stu-id="2035c-180">Running Code Before Each View</span></span>

<span data-ttu-id="2035c-181">*_ViewStart.cshtml* 파일에 각 보기 또는 페이지를 배치하기 전에 실행되어야 하는 코드.</span><span class="sxs-lookup"><span data-stu-id="2035c-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="2035c-182">규칙에 따라, *_ViewStart.cshtml* 파일은 *Pages*(또는 *Views*) 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="2035c-183">*_ViewStart.cshtml*에 나열된 문은 모든 전체 뷰(레이아웃 및 부분 뷰가 아님) 이전에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="2035c-184">[ViewImports.cshtml](xref:mvc/views/layout#viewimports)처럼 *_ViewStart.cshtml*은 계층적입니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="2035c-185">*_ViewStart.cshtml* 파일이 보기 또는 페이지 폴더에 정의된 경우 *Pages*(또는 *Views*) 폴더의 루트에 정의된 항목 뒤에 실행됩니다(있는 경우).</span><span class="sxs-lookup"><span data-stu-id="2035c-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views*) folder (if any).</span></span>

<span data-ttu-id="2035c-186">샘플 *_ViewStart.cshtml* 파일:</span><span class="sxs-lookup"><span data-stu-id="2035c-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="2035c-187">위의 파일은 모든 뷰가 *_Layout.cshtml* 레이아웃을 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="2035c-188">*_ViewStart.cshtml* 및 *_ViewImports.cshtml*은 일반적으로 */Pages/Shared*(또는 */Views/Shared*) 폴더에 배치되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="2035c-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared*) folder.</span></span> <span data-ttu-id="2035c-189">이러한 파일의 앱 수준 버전은 */Pages*(또는 */Views*) 폴더에 직접 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2035c-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views*) folder.</span></span>
