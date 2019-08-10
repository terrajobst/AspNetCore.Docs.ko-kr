---
title: ASP.NET Core에서 Razor 페이지 경로 및 앱 규칙
author: guardrex
description: 경로 및 앱 모델 공급자 규칙을 통해 페이지 라우팅, 검색 및 처리를 제어하는 방법을 검색합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: c93f169c422d260f738faba4812861521f383e51
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914970"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="c0600-103">ASP.NET Core에서 Razor 페이지 경로 및 앱 규칙</span><span class="sxs-lookup"><span data-stu-id="c0600-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="c0600-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="c0600-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c0600-105">페이지 [경로 및 앱 모델 공급자 규칙](xref:mvc/controllers/application-model#conventions)을 사용하여 Razor 페이지 앱에서 페이지 라우팅, 검색 및 처리를 제어하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="c0600-106">개별 페이지에 대한 사용자 지정 페이지 경로를 구성해야 하는 경우 이 항목의 뒷부분에서 설명할 [AddPageRoute 규칙](#configure-a-page-route)을 사용하여 페이지에 대한 라우팅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="c0600-107">페이지 경로를 지정 하거나, 경로 세그먼트를 추가 하거나, 경로에 매개 변수를 추가 하려면 페이지의 `@page` 지시어를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="c0600-108">자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c0600-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="c0600-109">경로 세그먼트 또는 매개 변수 이름으로 사용할 수 없는 예약어가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="c0600-110">자세한 내용은 라우팅을 참조 [하세요. 예약 된 라우팅](xref:fundamentals/routing#reserved-routing-names)이름</span><span class="sxs-lookup"><span data-stu-id="c0600-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="c0600-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c0600-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="c0600-112">시나리오</span><span class="sxs-lookup"><span data-stu-id="c0600-112">Scenario</span></span> | <span data-ttu-id="c0600-113">샘플에서는 다음 사항을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="c0600-114">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="c0600-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="c0600-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="c0600-115">Conventions.Add</span></span><ul><li><span data-ttu-id="c0600-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="c0600-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="c0600-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="c0600-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="c0600-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="c0600-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="c0600-119">경로 템플릿 및 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="c0600-120">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="c0600-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="c0600-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="c0600-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="c0600-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="c0600-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="c0600-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="c0600-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="c0600-124">폴더에 있는 페이지 및 단일 페이지에 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="c0600-125">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="c0600-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="c0600-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="c0600-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="c0600-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="c0600-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="c0600-128">ConfigureFilter(필터 클래스, 람다 식 또는 필터 팩터리)</span><span class="sxs-lookup"><span data-stu-id="c0600-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="c0600-129">폴더의 페이지에 헤더를 추가하고, 단일 페이지에 헤더를 추가하고, [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 구성하여 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="c0600-130">클래스의 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 서비스컬렉션에대한확장메서드를사용하여RazorPages규칙을추가하고구성합니다.`Startup`</span><span class="sxs-lookup"><span data-stu-id="c0600-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="c0600-131">다음 규칙 예제는 이 토픽의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-131">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention(
                    "/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention(
                    "/About", model => { ... });
                options.Conventions.AddPageRoute(
                    "/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention(
                    "/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention(
                    "/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a><span data-ttu-id="c0600-132">경로 순서</span><span class="sxs-lookup"><span data-stu-id="c0600-132">Route order</span></span>

<span data-ttu-id="c0600-133">경로는 처리 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 를 위해를 지정 합니다 (경로 일치).</span><span class="sxs-lookup"><span data-stu-id="c0600-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="c0600-134">주문</span><span class="sxs-lookup"><span data-stu-id="c0600-134">Order</span></span>            | <span data-ttu-id="c0600-135">동작</span><span class="sxs-lookup"><span data-stu-id="c0600-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="c0600-136">-1</span><span class="sxs-lookup"><span data-stu-id="c0600-136">-1</span></span>               | <span data-ttu-id="c0600-137">경로는 다른 경로가 처리 되기 전에 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="c0600-138">0</span><span class="sxs-lookup"><span data-stu-id="c0600-138">0</span></span>                | <span data-ttu-id="c0600-139">순서가 지정 되지 않은 경우 (기본값)</span><span class="sxs-lookup"><span data-stu-id="c0600-139">Order isn't specified (default value).</span></span> <span data-ttu-id="c0600-140">할당 `Order` 안 함`Order = null`()은 처리 `Order` 를 위해 경로를 0 (영)으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="c0600-141">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="c0600-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="c0600-142">경로 처리 순서를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="c0600-143">경로 처리는 규칙에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="c0600-144">경로는 순차적으로 처리 됩니다 (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="c0600-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="c0600-145">경로가 동일한 `Order`경우 가장 구체적인 경로는 먼저 일치 하 고 그 다음에 더 작은 경로를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="c0600-146">동일 `Order` 하 고 동일한 수의 매개 변수를 포함 하는 경로가 요청 URL과 일치 하는 경우 경로는에 추가 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>된 순서 대로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="c0600-147">가능 하면 설정 된 경로 처리 순서에 따라 피하십시오.</span><span class="sxs-lookup"><span data-stu-id="c0600-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="c0600-148">일반적으로 라우팅은 URL이 일치 하는 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="c0600-149">경로 `Order` 속성을 경로 요청을 올바르게 설정 해야 하는 경우에는 응용 프로그램의 라우팅 체계가 클라이언트에 혼란 스 러 울 수 있으며 유지 관리가 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="c0600-150">응용 프로그램의 라우팅 체계를 간소화 하기 위해 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="c0600-151">샘플 앱에는 단일 앱을 사용 하는 여러 라우팅 시나리오를 보여 주기 위해 명시적인 경로 처리가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="c0600-152">그러나 프로덕션 앱에서 경로 `Order` 를 설정 하는 방법은 사용 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="c0600-153">Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="c0600-154">MVC 항목의 경로 순서에 대 한 정보는 컨트롤러 [작업에 대 한 라우팅에서 사용할 수 있습니다. 특성 경로](xref:mvc/controllers/routing#ordering-attribute-routes)순서 지정.</span><span class="sxs-lookup"><span data-stu-id="c0600-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="c0600-155">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="c0600-155">Model conventions</span></span>

<span data-ttu-id="c0600-156">에 대 한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 대리자를 추가 하 여 Razor Pages에 적용 되는 [모델 규칙](xref:mvc/controllers/application-model#conventions) 을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="c0600-157">모든 페이지에 경로 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="c0600-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="c0600-158">를 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 사용 하 여를 만들고 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 페이지 경로 모델 생성 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 중에 적용 되는 인스턴스의 컬렉션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="c0600-159">샘플 앱은 앱의 모든 페이지에 `{globalTemplate?}` 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="c0600-160"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="c0600-161">이렇게 하면 샘플 앱에서 다음과 같은 경로 일치 동작을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="c0600-162">의 `TheContactPage/{text?}` 경로 템플릿은 항목의 뒷부분에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="c0600-163">연락처 페이지 경로는 ( `null` `Order = 0`)의 기본 순서 `{globalTemplate?}` 이므로 경로 템플릿 이전에 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="c0600-164">`{aboutTemplate?}` 경로 템플릿은 항목의 뒷부분에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="c0600-165">`{aboutTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="c0600-166">`/About/RouteDataValue`에서 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="c0600-167">`{otherPagesTemplate?}` 경로 템플릿은 항목의 뒷부분에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="c0600-168">`{otherPagesTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="c0600-169">경로 매개 변수 (예: `/OtherPages/Page1/RouteDataValue`)를 사용 하 여 *Pages/otherpages* 폴더에 있는 페이지를 요청 하면 "RouteDataValue `RouteData.Values["globalTemplate"]` "는`Order = 1` `RouteData.Values["otherPagesTemplate"]` `Order = 2` `Order` 속성.</span><span class="sxs-lookup"><span data-stu-id="c0600-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="c0600-170">가능 하면의 `Order` `Order = 0`결과를 설정 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c0600-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="c0600-171">경로를 사용 하 여 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="c0600-172">추가 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>와 같은 Razor Pages 옵션은 MVC가의 `Startup.ConfigureServices`서비스 컬렉션에 추가 될 때 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c0600-173">예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0600-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="c0600-174">`localhost:5000/About/GlobalRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![GlobalRouteValue의 경로 세그먼트를 사용하여 정보 페이지를 요청합니다.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="c0600-177">모든 페이지에 앱 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="c0600-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="c0600-178">를 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 사용 하 여를 만들고 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 페이지 앱 모델 생성 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 중에 적용 되는 인스턴스 컬렉션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="c0600-179">이 항목의 뒷부분에서 이 규칙 및 다른 규칙을 설명하기 위해 샘플 앱에는 `AddHeaderAttribute` 클래스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="c0600-180">클래스 생성자가 `name` 문자열 및 `values` 문자열 배열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="c0600-181">이러한 값은 응답 헤더를 설정하는 `OnResultExecuting` 메서드에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="c0600-182">전체 클래스는 항목의 뒷부분에 나오는 [페이지 모델 작업 규칙](#page-model-action-conventions) 섹션에서 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="c0600-183">샘플 앱에서는 앱의 모든 페이지에 `GlobalHeader` 헤더를 추가하는 `AddHeaderAttribute` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="c0600-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="c0600-184">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

<span data-ttu-id="c0600-185">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 GlobalHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="c0600-187">모든 페이지에 처리기 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="c0600-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="c0600-188">를 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 사용 하 여를 만들고 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 페이지 처리기 모델 생성 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 중에 적용 되는 인스턴스 컬렉션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="c0600-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="c0600-189">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

## <a name="page-route-action-conventions"></a><span data-ttu-id="c0600-190">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="c0600-190">Page route action conventions</span></span>

<span data-ttu-id="c0600-191">에서 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> 파생 되는 기본 경로 모델 공급자는 페이지 경로를 구성 하기 위한 확장성 지점도 제공 하도록 설계 된 규칙을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="c0600-192">폴더 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="c0600-192">Folder route model convention</span></span>

<span data-ttu-id="c0600-193">를 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 사용 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 폴더의 모든 페이지 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 에 대해에서 작업을 호출 하는을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="c0600-194">샘플 앱에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용하여 `{otherPagesTemplate?}` 경로 템플릿을 *OtherPages* 폴더의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="c0600-195"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="c0600-196">이렇게 하면 단일 경로 값을 `{globalTemplate?}` 제공 하는 경우 첫 번째 경로 `1`데이터 값 위치에 대 한 (항목의 앞부분에 설정)에 대 한 템플릿이 우선 순위를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="c0600-197">경로 매개 변수 값 (예: `/OtherPages/Page1/RouteDataValue`)을 사용 하 여 *Pages/otherpages* 폴더의 페이지를 요청 하면 "RouteDataValue `RouteData.Values["globalTemplate"]` "는`Order = 1` `RouteData.Values["otherPagesTemplate"]` `Order = 2` `Order` 속성.</span><span class="sxs-lookup"><span data-stu-id="c0600-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="c0600-198">가능 하면의 `Order` `Order = 0`결과를 설정 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c0600-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="c0600-199">경로를 사용 하 여 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="c0600-200">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`에서 샘플의 Page1 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages 폴더의 Page1은 GlobalRouteValue 및 OtherPagesRouteValue라는 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="c0600-203">페이지 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="c0600-203">Page route model convention</span></span>

<span data-ttu-id="c0600-204">를 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 사용 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 이름의 페이지 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 에 대 한 작업을 호출 하는을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="c0600-205">샘플 앱에서는 `AddPageRouteModelConvention`를 사용하여 `{aboutTemplate?}` 경로 템플릿을 정보 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

<span data-ttu-id="c0600-206"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="c0600-207">이렇게 하면 단일 경로 값을 `{globalTemplate?}` 제공 하는 경우 첫 번째 경로 `1`데이터 값 위치에 대 한 (항목의 앞부분에 설정)에 대 한 템플릿이 우선 순위를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="c0600-208">에 경로 매개 변수 `/About/RouteDataValue`값을 사용 하 여 About 페이지를 요청 하면 `Order` 속성을 설정 하 여 `RouteData.Values["globalTemplate"]` "`Order = 1`RouteDataValue"가 `RouteData.Values["aboutTemplate"]` `Order = 2`()로 로드 되 고가 아닌 ()으로 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="c0600-209">가능 하면의 `Order` `Order = 0`결과를 설정 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c0600-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="c0600-210">경로를 사용 하 여 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="c0600-211">`localhost:5000/About/GlobalRouteValue/AboutRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![정보 페이지는 GlobalRouteValue 및 AboutRouteValue에 대한 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="c0600-214">매개 변수 변환기를 사용 하 여 페이지 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="c0600-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="c0600-215">ASP.NET Core에서 생성 된 페이지 경로는 매개 변수 변환기를 사용 하 여 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="c0600-216">매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하고 매개 변수의 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="c0600-217">예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="c0600-218">페이지 `PageRouteTransformerConvention` 경로 모델 규칙은 응용 프로그램에서 자동으로 생성 된 페이지 경로의 폴더 및 파일 이름 세그먼트에 매개 변수 변환기를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="c0600-219">예를 들어 */Pages/SubscriptionManagement/ViewAll.cshtml* 의 Razor Pages 파일은에서 `/SubscriptionManagement/ViewAll` 로 `/subscription-management/view-all`다시 작성 된 경로를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="c0600-220">`PageRouteTransformerConvention`는 Razor Pages 폴더 및 파일 이름에서 가져온 페이지 경로의 자동으로 생성 된 세그먼트만 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="c0600-221">`@page` 지시문을 사용 하 여 추가 된 경로 세그먼트는 변환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="c0600-222">또한 규칙은에 의해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>추가 된 경로를 변환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="c0600-223">는 `PageRouteTransformerConvention` 의`Startup.ConfigureServices`옵션으로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="c0600-224">페이지 경로 구성</span><span class="sxs-lookup"><span data-stu-id="c0600-224">Configure a page route</span></span>

<span data-ttu-id="c0600-225">지정 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 된 페이지 경로에서 페이지에 대 한 경로를 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="c0600-226">페이지에 생성된 링크는 지정된 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="c0600-227">`AddPageRoute`는 `AddPageRouteModelConvention`을 사용하여 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="c0600-228">샘플 앱은 *Contact.cshtml*의 `/TheContactPage`에 대한 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

<span data-ttu-id="c0600-229">연락처 페이지도 기본 경로를 통해 `/Contact`에 도달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="c0600-230">연락처 페이지에 대한 샘플 앱의 사용자 지정 경로는 선택적 `text` 경로 세그먼트(`{text?}`)에 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="c0600-231">방문자가 해당 `/Contact` 경로에 있는 페이지에 액세스하는 경우 페이지에는 해당 `@page` 지시문에 있는 이 선택적 세그먼트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

<span data-ttu-id="c0600-232">렌더링된 페이지의 **연락처** 링크에 생성된 URL이 업데이트된 경로를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![탐색 모음의 샘플 앱 연락처 링크](razor-pages-conventions/_static/contact-link.png)

![렌더링된 HTML에서 연락처 링크를 검사하면 href가 '/TheContactPage'로 설정되어 있음을 나타냅니다.](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="c0600-235">일반적인 경로 `/Contact` 또는 사용자 지정 경로 `/TheContactPage`에서 연락처 페이지를 방문하세요.</span><span class="sxs-lookup"><span data-stu-id="c0600-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="c0600-236">추가 `text` 경로 세그먼트를 제공하는 경우 페이지는 제공한 HTML 인코딩 세그먼트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL에서 'TextValue'라는 선택적 'text' 경로 세그먼트를 제공하는 에지 브라우저 예제입니다.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="c0600-239">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="c0600-239">Page model action conventions</span></span>

<span data-ttu-id="c0600-240">을 구현 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 하는 기본 페이지 모델 공급자는 페이지 모델을 구성 하는 데 필요한 확장성을 제공 하도록 설계 된 규칙을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="c0600-241">이러한 규칙은 페이지 검색 및 처리 시나리오를 빌드하고 수정하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="c0600-242">이 단원의 예제에서 샘플 앱은 응답 헤더를 적용 하 `AddHeaderAttribute` 는 클래스 ( <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="c0600-243">샘플을 규칙을 사용하여 폴더에 있는 모든 페이지 및 단일 페이지에 특성을 적용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="c0600-244">**폴더 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="c0600-244">**Folder app model convention**</span></span>

<span data-ttu-id="c0600-245">를 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 사용 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 폴더의 모든 페이지에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 대 한 인스턴스에 대 한 작업을 호출 하는을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="c0600-246">이 샘플은 *OtherPages* 폴더 내의 페이지에 `OtherPagesHeader` 헤더를 추가하여 앱의 `AddFolderApplicationModelConvention`을 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

<span data-ttu-id="c0600-247">`localhost:5000/OtherPages/Page1`에서 샘플의 Page1 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 페이지의 응답 헤더는 OtherPagesHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="c0600-249">**페이지 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="c0600-249">**Page app model convention**</span></span>

<span data-ttu-id="c0600-250">를 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 사용 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 이름의 페이지 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 에 대 한 작업을 호출 하는을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="c0600-251">샘플은 정보 페이지에 `AboutHeader` 헤더를 추가하여 `AddPageApplicationModelConvention`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

<span data-ttu-id="c0600-252">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 AboutHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="c0600-254">**필터 구성**</span><span class="sxs-lookup"><span data-stu-id="c0600-254">**Configure a filter**</span></span>

<span data-ttu-id="c0600-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>지정 된 필터가 적용 되도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="c0600-256">필터 클래스를 구현할 수 있지만 샘플 앱은 람다 식으로 필터를 구현하는 방법을 보여줍니다. 그러면 백그라운드에서 필터를 반환하는 팩터리로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

<span data-ttu-id="c0600-257">페이지 앱 모델은 *OtherPages* 폴더에 있는 Page2 페이지로 이동하는 세그먼트에 대한 상대 경로를 확인하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="c0600-258">조건이 통과하는 경우 헤더가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="c0600-259">그렇지 않으면 `EmptyFilter`이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="c0600-260">`EmptyFilter`은 [작업 필터](xref:mvc/controllers/filters#action-filters)입니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="c0600-261">작업 필터는 Razor Pages에서 무시 되므로 경로에 `EmptyFilter` 가 포함 되어 `OtherPages/Page2`있지 않은 경우는 의도 한 대로 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="c0600-262">`localhost:5000/OtherPages/Page2`에서 샘플의 Page2 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header는 Page2에 대한 응답에 추가됩니다.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="c0600-264">**필터 팩터리 구성**</span><span class="sxs-lookup"><span data-stu-id="c0600-264">**Configure a filter factory**</span></span>

<span data-ttu-id="c0600-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>모든 Razor Pages에 [필터](xref:mvc/controllers/filters) 를 적용 하도록 지정 된 팩터리를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="c0600-266">샘플 앱은 앱의 페이지에 두 개의 값이 포함된 `FilterFactoryHeader` 헤더를 추가하여 [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 사용하는 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

<span data-ttu-id="c0600-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="c0600-267">*AddHeaderWithFactory.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="c0600-268">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더에서는 두 개의 FilterFactoryHeader 헤더가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="c0600-270">MVC 필터 및 페이지 필터(IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="c0600-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="c0600-271">Razor 페이지가 처리기 메서드를 사용하므로 MVC [작업 필터](xref:mvc/controllers/filters#action-filters)는 Razor 페이지에서 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="c0600-272">사용할 수 있는 다른 유형의 MVC 필터는 다음과 같습니다. [권한 부여](xref:mvc/controllers/filters#authorization-filters), [예외](xref:mvc/controllers/filters#exception-filters), [리소스](xref:mvc/controllers/filters#resource-filters)및 [결과가](xref:mvc/controllers/filters#result-filters)있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="c0600-273">자세한 내용은 [필터](xref:mvc/controllers/filters) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0600-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="c0600-274">페이지 필터 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>)는 Razor Pages에 적용 되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="c0600-275">자세한 내용은 [Razor 페이지 필터의 메서드](xref:razor-pages/filter)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="c0600-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c0600-276">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c0600-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
