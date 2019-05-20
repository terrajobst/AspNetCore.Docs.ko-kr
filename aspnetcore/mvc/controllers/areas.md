---
title: ASP.NET Core의 영역
author: rick-anderson
description: 관련 기능을 별도의 네임스페이스(라우팅용) 및 폴더 구조(보기용)로 그룹화하는 데 사용되는 ASP.NET MVC 기능인 영역에 대해 알아봅니다.
ms.author: riande
ms.date: 05/10/2019
uid: mvc/controllers/areas
ms.openlocfilehash: f3a75bc307a206e43241b421f448b09011868d08
ms.sourcegitcommit: ffe3ed7921ec6c7c70abaac1d10703ec9a43374c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65535963"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="b002a-103">ASP.NET Core의 영역</span><span class="sxs-lookup"><span data-stu-id="b002a-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="b002a-104">작성자: [Dhananjay Kumar](https://twitter.com/debug_mode) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b002a-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b002a-105">영역은 관련 기능을 별도의 네임스페이스(라우팅용) 및 폴더 구조(보기용)로 그룹화하는 데 사용되는 ASP.NET 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="b002a-106">영역을 사용하면 다른 경로 매개 변수 `area`를 `controller` 및 `action` 또는 Razor Page `page`에 추가하여 라우팅을 위한 계층 구조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="b002a-107">영역에서는 ASP.NET Core 웹앱을 각각의 Razor Pages, 컨트롤러, 보기 및 모델의 자체 세트가 있는 작은 기능 그룹으로 분할하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-107">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="b002a-108">영역은 앱 내에서 효과적인 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-108">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="b002a-109">ASP.NET Core 웹 프로젝트에서 페이지, 모델, 컨트롤러 및 보기와 같은 논리적 구성 요소는 서로 다른 폴더에 보관됩니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-109">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="b002a-110">ASP.NET Core 런타임은 명명 규칙을 사용하여 이러한 구성 요소 간의 관계를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-110">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="b002a-111">대형 앱의 경우 앱을 별도의 고급 기능 영역으로 나누는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-111">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="b002a-112">결제, 청구 및 검색과 같은 여러 비즈니스 단위가 있는 전자상거래 앱을 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="b002a-113">이러한 각 단위에는 보기, 컨트롤러, Razor Pages 및 모델을 포함하는 고유의 영역이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-113">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="b002a-114">다음과 같은 경우 프로젝트에서 영역을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-114">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="b002a-115">앱은 논리적으로 분리할 수 있는 여러 개의 고급 기능 구성 요소로 이루어져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-115">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="b002a-116">각 기능 영역을 독립적으로 작업할 수 있도록 앱을 파티션하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-116">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="b002a-117">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b002a-117">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="b002a-118">다운로드 샘플은 테스트 영역에 대한 기본 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-118">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="b002a-119">Razor Pages를 사용하는 경우 이 문서에서 [Razor Pages가 있는 영역](#areas-with-razor-pages)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b002a-119">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="b002a-120">보기가 있는 컨트롤러 영역</span><span class="sxs-lookup"><span data-stu-id="b002a-120">Areas for controllers with views</span></span>

<span data-ttu-id="b002a-121">영역, 컨트롤러 및 보기를 사용하는 일반적인 ASP.NET Core 웹앱에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-121">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="b002a-122">[영역 폴더 구조](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="b002a-122">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="b002a-123">컨트롤러를 [&lbrack;Area&rbrack;](#attribute) 특성으로 데코레이팅하여 해당 영역과 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-123">Controllers decorated with the [&lbrack;Area&rbrack;](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="b002a-124">[시작 시 영역 경로 추가](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="b002a-124">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="b002a-125">영역 폴더 구조</span><span class="sxs-lookup"><span data-stu-id="b002a-125">Area folder structure</span></span>

<span data-ttu-id="b002a-126">*제품* 및 *서비스*의 두 논리 그룹이 있는 앱을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-126">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="b002a-127">영역을 사용하면 폴더 구조는 다음과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-127">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="b002a-128">프로젝트 이름</span><span class="sxs-lookup"><span data-stu-id="b002a-128">Project name</span></span>
  * <span data-ttu-id="b002a-129">영역</span><span class="sxs-lookup"><span data-stu-id="b002a-129">Areas</span></span>
    * <span data-ttu-id="b002a-130">제품</span><span class="sxs-lookup"><span data-stu-id="b002a-130">Products</span></span>
      * <span data-ttu-id="b002a-131">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="b002a-131">Controllers</span></span>
        * <span data-ttu-id="b002a-132">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="b002a-132">HomeController.cs</span></span>
        * <span data-ttu-id="b002a-133">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="b002a-133">ManageController.cs</span></span>
      * <span data-ttu-id="b002a-134">보기</span><span class="sxs-lookup"><span data-stu-id="b002a-134">Views</span></span>
        * <span data-ttu-id="b002a-135">홈</span><span class="sxs-lookup"><span data-stu-id="b002a-135">Home</span></span>
          * <span data-ttu-id="b002a-136">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="b002a-136">Index.cshtml</span></span>
        * <span data-ttu-id="b002a-137">관리</span><span class="sxs-lookup"><span data-stu-id="b002a-137">Manage</span></span>
          * <span data-ttu-id="b002a-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="b002a-138">Index.cshtml</span></span>
          * <span data-ttu-id="b002a-139">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="b002a-139">About.cshtml</span></span>
    * <span data-ttu-id="b002a-140">서비스</span><span class="sxs-lookup"><span data-stu-id="b002a-140">Services</span></span>
      * <span data-ttu-id="b002a-141">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="b002a-141">Controllers</span></span>
        * <span data-ttu-id="b002a-142">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="b002a-142">HomeController.cs</span></span>
      * <span data-ttu-id="b002a-143">보기</span><span class="sxs-lookup"><span data-stu-id="b002a-143">Views</span></span>
        * <span data-ttu-id="b002a-144">홈</span><span class="sxs-lookup"><span data-stu-id="b002a-144">Home</span></span>
          * <span data-ttu-id="b002a-145">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="b002a-145">Index.cshtml</span></span>

<span data-ttu-id="b002a-146">영역을 사용할 때 이전 레이아웃이 일반적이지만, 이 폴더 구조를 사용하려면 보기 파일만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-146">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="b002a-147">보기 검색은 일치하는 영역 보기 파일을 다음 순서로 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-147">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="b002a-148">*컨트롤러* 및 *모델*과 같은 보기가 아닌 폴더의 위치는 중요하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b002a-148">The location of non-view folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="b002a-149">예를 들어 *컨트롤러* 및 *모델* 폴더는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-149">For example, the *Controllers* and *Models* folder are not required.</span></span> <span data-ttu-id="b002a-150">*컨트롤러* 및 *모델*의 내용은 .dll로 컴파일되는 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-150">The content of *Controllers* and *Models* is code which gets compiled into a .dll.</span></span> <span data-ttu-id="b002a-151">*보기*의 내용은 해당 보기에 대한 요청이 있을 때까지 컴파일되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-151">The content of the *Views* isn't compiled until a request to that view has been made.</span></span>

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="b002a-152">컨트롤러를 영역과 연결</span><span class="sxs-lookup"><span data-stu-id="b002a-152">Associate the controller with an Area</span></span>

<span data-ttu-id="b002a-153">영역 컨트롤러는 [&lbrack;영역&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 특성으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-153">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="b002a-154">영역 경로 추가</span><span class="sxs-lookup"><span data-stu-id="b002a-154">Add Area route</span></span>

<span data-ttu-id="b002a-155">영역 경로는 일반적으로 특성 라우팅보다는 규칙 기반 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-155">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="b002a-156">규칙 기반 라우팅은 순서를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-156">Conventional routing is order-dependent.</span></span> <span data-ttu-id="b002a-157">일반적으로 영역이 있는 경로는 영역이 없는 경로에 비해 구체적이기 때문에 경로 테이블의 앞부분에 배치되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-157">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="b002a-158">모든 영역에서 url 공간이 통일된 경우 `{area:...}`를 경로 템플릿의 토큰으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-158">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="b002a-159">앞의 코드에서 `exists`는 경로가 영역과 일치해야 한다는 제한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-159">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="b002a-160">`{area:...}`를 사용하는 것은 영역에 라우팅을 추가하는 가장 간단한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-160">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="b002a-161">다음 코드는 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>를 사용하여 명명된 두 개의 영역 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-161">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="b002a-162">ASP.NET Core 2.2와 함께 `MapAreaRoute`를 사용하는 경우 [이 GitHub 문제](https://github.com/aspnet/AspNetCore/issues/7772)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b002a-162">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="b002a-163">자세한 내용은 [영역 라우팅](xref:mvc/controllers/routing#areas)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b002a-163">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="b002a-164">MVC 영역과 링크 생성</span><span class="sxs-lookup"><span data-stu-id="b002a-164">Link generation with MVC areas</span></span>

<span data-ttu-id="b002a-165">[샘플 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)의 다음 코드는 지정된 영역과 링크 생성을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-165">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="b002a-166">앞의 코드로 생성된 링크는 앱의 어느 위치에서나 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-166">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="b002a-167">샘플 다운로드에는 영역을 지정하지 않고 위의 링크 및 동일한 링크를 포함하는 [부분 뷰](xref:mvc/views/partial)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-167">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="b002a-168">부분 뷰는 [레이아웃 파일](xref:mvc/views/layout)에서 참조되므로, 앱의 모든 페이지에 생성된 링크가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="b002a-169">영역을 지정하지 않고 생성된 링크는 동일한 영역 및 컨트롤러 페이지에서 참조할 때만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="b002a-170">영역 또는 컨트롤러를 지정하지 않으면 라우팅은 *앰비언트* 값에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-170">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="b002a-171">현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="b002a-172">샘플 앱의 대부분의 경우 앰비언트 값을 사용하면 잘못된 링크가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-172">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="b002a-173">자세한 정보는 [컨트롤러 작업에 라우팅](xref:mvc/controllers/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b002a-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-viewstartcshtml-file"></a><span data-ttu-id="b002a-174">_ViewStart.cshtml 파일을 사용하여 영역에 대한 레이아웃 공유</span><span class="sxs-lookup"><span data-stu-id="b002a-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="b002a-175">전체 앱의 일반적인 레이아웃을 공유하려면 *_ViewStart.cshtml*을 애플리케이션 루트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-175">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="b002a-176">보기가 저장된 기본 영역 폴더 변경</span><span class="sxs-lookup"><span data-stu-id="b002a-176">Change default area folder where views are stored</span></span>

<span data-ttu-id="b002a-177">다음 코드는 기본 영역 폴더를 `"Areas"`에서 `"MyAreas"`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-177">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="b002a-178">Razor Pages가 있는 영역</span><span class="sxs-lookup"><span data-stu-id="b002a-178">Areas with Razor Pages</span></span>

<span data-ttu-id="b002a-179">Razor Pages가 있는 영역은 앱 루트에 *Areas/&lt;area name&gt;/Pages* 폴더가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-179">Areas with Razor Pages require and *Areas/&lt;area name&gt;/Pages* folder in the root of the app.</span></span> <span data-ttu-id="b002a-180">[샘플 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)에서는 다음 폴더 구조가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-180">The following folder structure is used with the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)</span></span>

* <span data-ttu-id="b002a-181">프로젝트 이름</span><span class="sxs-lookup"><span data-stu-id="b002a-181">Project name</span></span>
  * <span data-ttu-id="b002a-182">영역</span><span class="sxs-lookup"><span data-stu-id="b002a-182">Areas</span></span>
    * <span data-ttu-id="b002a-183">제품</span><span class="sxs-lookup"><span data-stu-id="b002a-183">Products</span></span>
      * <span data-ttu-id="b002a-184">Pages</span><span class="sxs-lookup"><span data-stu-id="b002a-184">Pages</span></span>
        * <span data-ttu-id="b002a-185">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="b002a-185">_ViewImports</span></span>
        * <span data-ttu-id="b002a-186">정보</span><span class="sxs-lookup"><span data-stu-id="b002a-186">About</span></span>
        * <span data-ttu-id="b002a-187">인덱스</span><span class="sxs-lookup"><span data-stu-id="b002a-187">Index</span></span>
    * <span data-ttu-id="b002a-188">서비스</span><span class="sxs-lookup"><span data-stu-id="b002a-188">Services</span></span>
      * <span data-ttu-id="b002a-189">Pages</span><span class="sxs-lookup"><span data-stu-id="b002a-189">Pages</span></span>
        * <span data-ttu-id="b002a-190">관리</span><span class="sxs-lookup"><span data-stu-id="b002a-190">Manage</span></span>
          * <span data-ttu-id="b002a-191">정보</span><span class="sxs-lookup"><span data-stu-id="b002a-191">About</span></span>
          * <span data-ttu-id="b002a-192">인덱스</span><span class="sxs-lookup"><span data-stu-id="b002a-192">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="b002a-193">Razor Pages 및 영역과 링크 생성</span><span class="sxs-lookup"><span data-stu-id="b002a-193">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="b002a-194">[샘플 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)의 다음 코드는 지정된 영역(예: `asp-area="Products"`)과 링크 생성을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-194">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="b002a-195">앞의 코드로 생성된 링크는 앱의 어느 위치에서나 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-195">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="b002a-196">샘플 다운로드에는 영역을 지정하지 않고 위의 링크 및 동일한 링크를 포함하는 [부분 뷰](xref:mvc/views/partial)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-196">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="b002a-197">부분 뷰는 [레이아웃 파일](xref:mvc/views/layout)에서 참조되므로, 앱의 모든 페이지에 생성된 링크가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-197">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="b002a-198">영역을 지정하지 않고 생성된 링크는 동일한 영역의 페이지에서 참조할 때만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-198">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="b002a-199">영역을 지정하지 않으면 ‘앰비언트’ 값에 따라 라우팅이 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-199">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="b002a-200">현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-200">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="b002a-201">샘플 앱의 대부분의 경우 앰비언트 값을 사용하면 잘못된 링크가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-201">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="b002a-202">예를 들어 다음 코드에서 생성된 링크를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-202">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="b002a-203">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="b002a-203">For the preceding code:</span></span>

* <span data-ttu-id="b002a-204">`<a asp-page="/Manage/About">`에서 생성된 링크는 `Services` 영역의 페이지를 마지막으로 요청한 경우에만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-204">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="b002a-205">예를 들어 `/Services/Manage/`, `/Services/Manage/Index` 또는 `/Services/Manage/About`입니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-205">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="b002a-206">`<a asp-page="/About">`에서 생성된 링크는 `/Home`의 페이지를 마지막으로 요청한 경우에만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-206">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="b002a-207">코드는 [샘플 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)에서 가져온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-207">The code is from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-viewimports-file"></a><span data-ttu-id="b002a-208">_ViewImports 파일을 사용하여 네임스페이스 및 태그 도우미 가져오기</span><span class="sxs-lookup"><span data-stu-id="b002a-208">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="b002a-209">각 영역의 *Pages* 폴더에 *_ViewImports.cshtml* 파일을 추가하여 네임스페이스 및 태그 도우미를 폴더의 각 Razor 페이지로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-209">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="b002a-210">*_ViewImports.cshtml* 파일이 없는 샘플 코드의 *Services* 영역을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-210">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="b002a-211">다음 태그는 */Services/Manage/About* Razor 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-211">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="b002a-212">위의 태그에서</span><span class="sxs-lookup"><span data-stu-id="b002a-212">In the preceding markup:</span></span>

* <span data-ttu-id="b002a-213">모델(`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)을 지정하려면 정규화된 도메인 이름을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-213">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="b002a-214">[태그 도우미](xref:mvc/views/tag-helpers/intro)는 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`를 통해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-214">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="b002a-215">샘플 다운로드에서 Products 영역에는 다음과 같은 *_ViewImports.cshtml* 파일이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-215">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="b002a-216">다음 태그는 */Products/About* Razor 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-216">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="b002a-217">위의 파일에서 네임스페이스와 `@addTagHelper` 지시문은 *Areas/Products/Pages/_ViewImports.cshtml* 파일을 통해 파일로 가져온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-217">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="b002a-218">자세한 내용은 [태그 도우미 범위 관리](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) 및 [공유 지시문 가져오기](xref:mvc/views/layout#importing-shared-directives)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b002a-218">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="b002a-219">Razor Pages 영역에 대한 공유 레이아웃</span><span class="sxs-lookup"><span data-stu-id="b002a-219">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="b002a-220">전체 앱의 일반적인 레이아웃을 공유하려면 *_ViewStart.cshtml*을 애플리케이션 루트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-220">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="b002a-221">영역 게시</span><span class="sxs-lookup"><span data-stu-id="b002a-221">Publishing Areas</span></span>

<span data-ttu-id="b002a-222">\*.cshtml 파일에 `<Project Sdk="Microsoft.NET.Sdk.Web">`이 포함되면 *wwwroot* 디렉터리 내의 모든 \*.csproj 파일 및 파일이 출력되도록 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="b002a-222">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
