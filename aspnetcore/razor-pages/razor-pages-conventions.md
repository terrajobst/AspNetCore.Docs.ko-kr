---
title: ASP.NET Core에서 Razor 페이지 경로 및 앱 규칙
author: guardrex
description: 경로 및 앱 모델 공급자 규칙을 통해 페이지 라우팅, 검색 및 처리를 제어하는 방법을 검색합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/07/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 59c8af648b50deb51f3762c14348d08acd48886e
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724456"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="be04f-103">ASP.NET Core에서 Razor 페이지 경로 및 앱 규칙</span><span class="sxs-lookup"><span data-stu-id="be04f-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="be04f-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="be04f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="be04f-105">페이지 [경로 및 앱 모델 공급자 규칙](xref:mvc/controllers/application-model#conventions)을 사용하여 Razor 페이지 앱에서 페이지 라우팅, 검색 및 처리를 제어하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="be04f-106">개별 페이지에 대한 사용자 지정 페이지 경로를 구성해야 하는 경우 이 항목의 뒷부분에서 설명할 [AddPageRoute 규칙](#configure-a-page-route)을 사용하여 페이지에 대한 라우팅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="be04f-107">페이지 경로 지정, 경로 세그먼트를 추가 하거나 매개 변수는 경로를 추가 하려면 페이지의 사용 `@page` 지시문입니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="be04f-108">자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes)합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="be04f-109">경로 세그먼트 또는 매개 변수 이름으로 사용할 수 없는 예약어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="be04f-110">자세한 내용은 참조 하세요. [라우팅: 예약 된 라우팅 이름](xref:fundamentals/routing#reserved-routing-names)합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="be04f-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="be04f-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="be04f-112">시나리오</span><span class="sxs-lookup"><span data-stu-id="be04f-112">Scenario</span></span> | <span data-ttu-id="be04f-113">샘플에서는 다음 사항을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="be04f-114">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="be04f-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="be04f-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="be04f-115">Conventions.Add</span></span><ul><li><span data-ttu-id="be04f-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="be04f-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="be04f-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="be04f-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="be04f-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="be04f-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="be04f-119">경로 템플릿 및 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="be04f-120">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="be04f-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="be04f-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="be04f-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="be04f-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="be04f-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="be04f-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="be04f-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="be04f-124">폴더에 있는 페이지 및 단일 페이지에 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="be04f-125">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="be04f-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="be04f-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="be04f-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="be04f-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="be04f-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="be04f-128">ConfigureFilter(필터 클래스, 람다 식 또는 필터 팩터리)</span><span class="sxs-lookup"><span data-stu-id="be04f-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="be04f-129">폴더의 페이지에 헤더를 추가하고, 단일 페이지에 헤더를 추가하고, [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 구성하여 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="be04f-130">Razor 페이지 규칙을 추가 하 고 사용 하 여 구성 합니다 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 확장 메서드를 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 서비스 컬렉션에는 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="be04f-131">다음 규칙 예제는 이 토픽의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-131">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention("/About", model => { ... });
                options.Conventions.AddPageRoute("/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention("/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a><span data-ttu-id="be04f-132">경로 순서</span><span class="sxs-lookup"><span data-stu-id="be04f-132">Route order</span></span>

<span data-ttu-id="be04f-133">경로 지정을 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (경로 일치)을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="be04f-134">순서</span><span class="sxs-lookup"><span data-stu-id="be04f-134">Order</span></span>            | <span data-ttu-id="be04f-135">동작</span><span class="sxs-lookup"><span data-stu-id="be04f-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="be04f-136">-1</span><span class="sxs-lookup"><span data-stu-id="be04f-136">-1</span></span>               | <span data-ttu-id="be04f-137">경로 다른 경로 처리 되기 전에 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="be04f-138">0</span><span class="sxs-lookup"><span data-stu-id="be04f-138">0</span></span>                | <span data-ttu-id="be04f-139">순서 지정 하지 않으면 (기본값).</span><span class="sxs-lookup"><span data-stu-id="be04f-139">Order isn't specified (default value).</span></span> <span data-ttu-id="be04f-140">할당 되지 `Order` (`Order = null`) 경로 기본값 `Order` 처리를 위해 0 (영)입니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="be04f-141">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="be04f-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="be04f-142">경로 처리 순서를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="be04f-143">경로 처리 규칙에 따라 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="be04f-144">경로 순서 대로 처리 됩니다 (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="be04f-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="be04f-145">경로 있는 경우 동일한 `Order`가장 덜 구체적인 경로 뒤에 먼저 특정 경로가 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="be04f-146">때 동일한 경로 `Order` 매개 변수 수가 같은 일치 요청 URL을 경로에 추가 된 순서 대로 처리 되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="be04f-147">가능 하면 설정 된 경로 처리 순서에 따라 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be04f-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="be04f-148">일반적으로 라우팅 URL 일치 하는 올바른 경로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="be04f-149">경로 설정 해야 하는 경우 `Order` 아마도 클라이언트로 복잡 하 고 유지 하기 위해 취약 한 응용 프로그램의 라우팅 체계는 속성 경로를 올바르게 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="be04f-150">앱의 라우팅 체계를 간소화 하기 위해 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="be04f-151">샘플 앱은 단일 앱을 사용 하는 여러 라우팅 시나리오를 시연 하려면 처리 명시적인 경로 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="be04f-152">그러나 설정 경로의 연습을 방지 하려고 해야 `Order` 프로덕션 앱에서.</span><span class="sxs-lookup"><span data-stu-id="be04f-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="be04f-153">Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="be04f-154">MVC 항목의 경로 순서에 대 한 정보는 [컨트롤러 작업에 라우팅: 특성 경로 순서 지정](xref:mvc/controllers/routing#ordering-attribute-routes)합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="be04f-155">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="be04f-155">Model conventions</span></span>

<span data-ttu-id="be04f-156">에 대 한 대리자를 추가 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 추가할 [규칙 모델](xref:mvc/controllers/application-model#conventions) Razor 페이지에 적용 되는 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="be04f-157">모든 페이지에 경로 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="be04f-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="be04f-158">사용 하 여 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 페이지 경로 중 적용 되는 인스턴스 모델 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="be04f-159">샘플 앱은 앱의 모든 페이지에 `{globalTemplate?}` 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="be04f-160"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="be04f-161">이렇게 하면 샘플 앱의 동작을 일치 하는 다음 경로:</span><span class="sxs-lookup"><span data-stu-id="be04f-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="be04f-162">에 대 한 경로 템플릿을 `TheContactPage/{text?}` 항목의 뒷부분에 나오는 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="be04f-163">연락처 페이지 경로는 기본 순서 `null` (`Order = 0`) 하기 전에 일치 하도록는 `{globalTemplate?}` 경로 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="be04f-164">`{aboutTemplate?}` 항목의 뒷부분에 나오는 경로 템플릿이 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="be04f-165">`{aboutTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="be04f-166">`/About/RouteDataValue`에서 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="be04f-167">`{otherPagesTemplate?}` 항목의 뒷부분에 나오는 경로 템플릿이 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="be04f-168">`{otherPagesTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="be04f-169">모든 페이지는 *페이지/OtherPages* 폴더 경로 매개 변수를 사용 하 여 요청 (예를 들어 `/OtherPages/Page1/RouteDataValue`), "RouteDataValue"는 로드 `RouteData.Values["globalTemplate"]` (`Order = 1`) 및 not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 설정으로 인해는 `Order` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="be04f-170">가능 하면 설정 하지 않은 합니다 `Order`, 그러면 `Order = 0`합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="be04f-171">올바른 경로 선택 하려면 라우팅에 의존 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="be04f-172">Razor 페이지 옵션을 추가 하는 등 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, MVC 서비스 컬렉션에 추가 되 면 추가 `Startup.ConfigureServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="be04f-173">예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be04f-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="be04f-174">`localhost:5000/About/GlobalRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![GlobalRouteValue의 경로 세그먼트를 사용하여 정보 페이지를 요청합니다.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="be04f-177">모든 페이지에 앱 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="be04f-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="be04f-178">사용 하 여 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 중 적용 되는 인스턴스 페이지 앱 모델 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="be04f-179">이 항목의 뒷부분에서 이 규칙 및 다른 규칙을 설명하기 위해 샘플 앱에는 `AddHeaderAttribute` 클래스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="be04f-180">클래스 생성자가 `name` 문자열 및 `values` 문자열 배열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="be04f-181">이러한 값은 응답 헤더를 설정하는 `OnResultExecuting` 메서드에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="be04f-182">전체 클래스는 항목의 뒷부분에 나오는 [페이지 모델 작업 규칙](#page-model-action-conventions) 섹션에서 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="be04f-183">샘플 앱에서는 앱의 모든 페이지에 `GlobalHeader` 헤더를 추가하는 `AddHeaderAttribute` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="be04f-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="be04f-184">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="be04f-185">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 GlobalHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="be04f-187">모든 페이지에는 처리기 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="be04f-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="be04f-188">사용 하 여 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 중 적용 되는 인스턴스 페이지 처리기 모델 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="be04f-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="be04f-189">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="be04f-190">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="be04f-190">Page route action conventions</span></span>

<span data-ttu-id="be04f-191">파생 되는 기본 경로 모델 공급자 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> 페이지 경로 구성 하는 것에 대 한 확장성 지점을 제공 하도록 설계 된 규칙을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="be04f-192">폴더 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="be04f-192">Folder route model convention</span></span>

<span data-ttu-id="be04f-193">사용 하 여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 에서 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 모든 지정 된 폴더 아래에 있는 페이지에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="be04f-194">샘플 앱에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용하여 `{otherPagesTemplate?}` 경로 템플릿을 *OtherPages* 폴더의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="be04f-195"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="be04f-196">이렇게 하면에 대 한 템플릿을 `{globalTemplate?}` (항목의 앞부분에서 설정 `1`) 단일 경로 값을 제공 하는 경우 첫 번째 경로 데이터 값 위치에 대 한 우선 순위가 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="be04f-197">경우 페이지에는 *페이지/OtherPages* 폴더 경로 매개 변수 값을 사용 하 여 요청 (예를 들어 `/OtherPages/Page1/RouteDataValue`), "RouteDataValue"는 로드 `RouteData.Values["globalTemplate"]` (`Order = 1`) 및 not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 설정으로 인해는 `Order` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="be04f-198">가능 하면 설정 하지 않은 합니다 `Order`, 그러면 `Order = 0`합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="be04f-199">올바른 경로 선택 하려면 라우팅에 의존 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="be04f-200">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`에서 샘플의 Page1 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages 폴더의 Page1은 GlobalRouteValue 및 OtherPagesRouteValue라는 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="be04f-203">페이지 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="be04f-203">Page route model convention</span></span>

<span data-ttu-id="be04f-204">사용 하 여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 에서 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 지정한 이름 가진 페이지에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="be04f-205">샘플 앱에서는 `AddPageRouteModelConvention`를 사용하여 `{aboutTemplate?}` 경로 템플릿을 정보 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="be04f-206"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="be04f-207">이렇게 하면에 대 한 템플릿을 `{globalTemplate?}` (항목의 앞부분에서 설정 `1`) 단일 경로 값을 제공 하는 경우 첫 번째 경로 데이터 값 위치에 대 한 우선 순위가 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="be04f-208">경로 매개 변수 값에서 정보 페이지를 요청 하는 경우 `/About/RouteDataValue`, "RouteDataValue"는 로드 `RouteData.Values["globalTemplate"]` (`Order = 1`) 및 not `RouteData.Values["aboutTemplate"]` (`Order = 2`) 설정으로 인해는 `Order` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="be04f-209">가능 하면 설정 하지 않은 합니다 `Order`, 그러면 `Order = 0`합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="be04f-210">올바른 경로 선택 하려면 라우팅에 의존 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="be04f-211">`localhost:5000/About/GlobalRouteValue/AboutRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![정보 페이지는 GlobalRouteValue 및 AboutRouteValue에 대한 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="be04f-214">매개 변수 변환기를 사용 하 여 페이지 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="be04f-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="be04f-215">ASP.NET Core에서 생성 된 페이지 경로 매개 변수 변환기를 사용 하 여 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="be04f-216">매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하고 매개 변수의 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="be04f-217">예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="be04f-218">`PageRouteTransformerConvention` 페이지 경로 모델 규칙 폴더 및 파일 이름 부분은 앱에서 자동으로 생성 된 페이지 경로 매개 변수 변환기를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="be04f-219">예를 들어, Razor 페이지 파일을 */Pages/SubscriptionManagement/ViewAll.cshtml* 해당 경로에서 다시 작성 해야 `/SubscriptionManagement/ViewAll` 하려면 `/subscription-management/view-all`합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="be04f-220">`PageRouteTransformerConvention` Razor 페이지 폴더 및 파일 이름에서 제공 되는 자동으로 생성 된 페이지 경로 세그먼트를만 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="be04f-221">사용 하 여 추가 경로 세그먼트를 변환 하지 않습니다는 `@page` 지시문입니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="be04f-222">규칙 또한 변형 하지 않습니다 하 여 추가 된 경로 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="be04f-223">합니다 `PageRouteTransformerConvention` 에 옵션으로 등록 되어 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="be04f-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add(
                    new PageRouteTransformerConvention(
                        new SlugifyParameterTransformer()));
            });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end

## <a name="configure-a-page-route"></a><span data-ttu-id="be04f-224">페이지 경로 구성</span><span class="sxs-lookup"><span data-stu-id="be04f-224">Configure a page route</span></span>

<span data-ttu-id="be04f-225">사용 하 여 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 지정된 페이지 경로에 페이지에 대 한 경로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="be04f-226">페이지에 생성된 링크는 지정된 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="be04f-227">`AddPageRoute`는 `AddPageRouteModelConvention`을 사용하여 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="be04f-228">샘플 앱은 *Contact.cshtml*의 `/TheContactPage`에 대한 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="be04f-229">연락처 페이지도 기본 경로를 통해 `/Contact`에 도달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="be04f-230">연락처 페이지에 대한 샘플 앱의 사용자 지정 경로는 선택적 `text` 경로 세그먼트(`{text?}`)에 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="be04f-231">방문자가 해당 `/Contact` 경로에 있는 페이지에 액세스하는 경우 페이지에는 해당 `@page` 지시문에 있는 이 선택적 세그먼트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="be04f-232">렌더링된 페이지의 **연락처** 링크에 생성된 URL이 업데이트된 경로를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![탐색 모음의 샘플 앱 연락처 링크](razor-pages-conventions/_static/contact-link.png)

![렌더링된 HTML에서 연락처 링크를 검사하면 href가 '/TheContactPage'로 설정되어 있음을 나타냅니다.](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="be04f-235">일반적인 경로 `/Contact` 또는 사용자 지정 경로 `/TheContactPage`에서 연락처 페이지를 방문하세요.</span><span class="sxs-lookup"><span data-stu-id="be04f-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="be04f-236">추가 `text` 경로 세그먼트를 제공하는 경우 페이지는 제공한 HTML 인코딩 세그먼트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL에서 'TextValue'라는 선택적 'text' 경로 세그먼트를 제공하는 에지 브라우저 예제입니다.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="be04f-239">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="be04f-239">Page model action conventions</span></span>

<span data-ttu-id="be04f-240">구현 하는 기본 페이지 모델 공급자 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 페이지 모델을 구성 하는 것에 대 한 확장성 지점을 제공 하도록 설계 된 규칙을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="be04f-241">이러한 규칙은 페이지 검색 및 처리 시나리오를 빌드하고 수정하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="be04f-242">샘플 앱에서는이 섹션의 예는 `AddHeaderAttribute` 클래스는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, 응답 헤더를 적용 하는:</span><span class="sxs-lookup"><span data-stu-id="be04f-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="be04f-243">샘플을 규칙을 사용하여 폴더에 있는 모든 페이지 및 단일 페이지에 특성을 적용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="be04f-244">**폴더 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="be04f-244">**Folder app model convention**</span></span>

<span data-ttu-id="be04f-245">사용 하 여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 에서 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 지정된 된 폴더 아래 모든 페이지에 대 한 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="be04f-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="be04f-246">이 샘플은 *OtherPages* 폴더 내의 페이지에 `OtherPagesHeader` 헤더를 추가하여 앱의 `AddFolderApplicationModelConvention`을 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="be04f-247">`localhost:5000/OtherPages/Page1`에서 샘플의 Page1 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 페이지의 응답 헤더는 OtherPagesHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="be04f-249">**페이지 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="be04f-249">**Page app model convention**</span></span>

<span data-ttu-id="be04f-250">사용 하 여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 만들고 추가 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 에서 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 지정한 이름 가진 페이지에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="be04f-251">샘플은 정보 페이지에 `AboutHeader` 헤더를 추가하여 `AddPageApplicationModelConvention`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="be04f-252">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 AboutHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="be04f-254">**필터 구성**</span><span class="sxs-lookup"><span data-stu-id="be04f-254">**Configure a filter**</span></span>

<span data-ttu-id="be04f-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 지정된 된 필터를 적용을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="be04f-256">필터 클래스를 구현할 수 있지만 샘플 앱은 람다 식으로 필터를 구현하는 방법을 보여줍니다. 그러면 백그라운드에서 필터를 반환하는 팩터리로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="be04f-257">페이지 앱 모델은 *OtherPages* 폴더에 있는 Page2 페이지로 이동하는 세그먼트에 대한 상대 경로를 확인하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="be04f-258">조건이 통과하는 경우 헤더가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="be04f-259">그렇지 않으면 `EmptyFilter`이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="be04f-260">`EmptyFilter`은 [작업 필터](xref:mvc/controllers/filters#action-filters)입니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="be04f-261">Razor 페이지에서 작업 필터를 무시 하므로 합니다 `EmptyFilter` 영향을 주지 않습니다 경우는 경로 포함 하지 않습니다 의도 한 대로 `OtherPages/Page2`입니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="be04f-262">`localhost:5000/OtherPages/Page2`에서 샘플의 Page2 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header는 Page2에 대한 응답에 추가됩니다.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="be04f-264">**필터 팩터리 구성**</span><span class="sxs-lookup"><span data-stu-id="be04f-264">**Configure a filter factory**</span></span>

<span data-ttu-id="be04f-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 적용할 지정 된 팩터리 구성 [필터](xref:mvc/controllers/filters) 모든 Razor 페이지에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="be04f-266">샘플 앱은 앱의 페이지에 두 개의 값이 포함된 `FilterFactoryHeader` 헤더를 추가하여 [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 사용하는 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="be04f-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="be04f-267">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="be04f-268">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더에서는 두 개의 FilterFactoryHeader 헤더가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="be04f-270">MVC 필터 및 페이지 필터(IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="be04f-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="be04f-271">Razor 페이지가 처리기 메서드를 사용하므로 MVC [작업 필터](xref:mvc/controllers/filters#action-filters)는 Razor 페이지에서 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="be04f-272">다른 유형의 MVC 필터는 사용할 수 있습니다. [권한 부여](xref:mvc/controllers/filters#authorization-filters), [예외](xref:mvc/controllers/filters#exception-filters)를 [Resource](xref:mvc/controllers/filters#resource-filters), 및 [결과](xref:mvc/controllers/filters#result-filters)합니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="be04f-273">자세한 내용은 [필터](xref:mvc/controllers/filters) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be04f-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="be04f-274">페이지 필터 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>)는 Razor 페이지에 적용 되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="be04f-275">자세한 내용은 [Razor 페이지 필터의 메서드](xref:razor-pages/filter)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="be04f-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be04f-276">추가 자료</span><span class="sxs-lookup"><span data-stu-id="be04f-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
