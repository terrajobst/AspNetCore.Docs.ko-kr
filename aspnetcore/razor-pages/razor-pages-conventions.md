---
title: ASP.NET Core에서 Razor 페이지 경로 및 앱 규칙
author: guardrex
description: 경로 및 앱 모델 공급자 규칙을 통해 페이지 라우팅, 검색 및 처리를 제어하는 방법을 검색합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: d8377c0a0b8a29fe4b6a7fa67beeff84927c8b74
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114772"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="ee0d4-103">ASP.NET Core에서 Razor 페이지 경로 및 앱 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="ee0d4-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="ee0d4-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ee0d4-105">페이지 [경로 및 앱 모델 공급자 규칙](xref:mvc/controllers/application-model#conventions)을 사용하여 Razor 페이지 앱에서 페이지 라우팅, 검색 및 처리를 제어하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="ee0d4-106">개별 페이지에 대한 사용자 지정 페이지 경로를 구성해야 하는 경우 이 항목의 뒷부분에서 설명할 [AddPageRoute 규칙](#configure-a-page-route)을 사용하여 페이지에 대한 라우팅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="ee0d4-107">페이지 경로를 지정 하거나, 경로 세그먼트를 추가 하거나, 경로에 매개 변수를 추가 하려면 페이지의 `@page` 지시어를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="ee0d4-108">자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="ee0d4-109">경로 세그먼트 또는 매개 변수 이름으로 사용할 수 없는 예약어가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="ee0d4-110">자세한 내용은 [라우팅: 예약 된 라우팅 이름](xref:fundamentals/routing#reserved-routing-names)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="ee0d4-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ee0d4-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="ee0d4-112">시나리오</span><span class="sxs-lookup"><span data-stu-id="ee0d4-112">Scenario</span></span> | <span data-ttu-id="ee0d4-113">샘플에서는 다음 사항을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="ee0d4-114">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="ee0d4-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="ee0d4-115">Conventions.Add</span></span><ul><li><span data-ttu-id="ee0d4-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="ee0d4-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="ee0d4-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="ee0d4-119">경로 템플릿 및 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="ee0d4-120">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="ee0d4-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="ee0d4-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="ee0d4-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="ee0d4-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="ee0d4-124">폴더에 있는 페이지 및 단일 페이지에 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="ee0d4-125">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="ee0d4-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="ee0d4-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="ee0d4-128">ConfigureFilter(필터 클래스, 람다 식 또는 필터 팩터리)</span><span class="sxs-lookup"><span data-stu-id="ee0d4-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="ee0d4-129">폴더의 페이지에 헤더를 추가하고, 단일 페이지에 헤더를 추가하고, [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 구성하여 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="ee0d4-130"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 확장 메서드를 사용 하 여 Razor Pages 규칙을 추가 하 고 구성 하 여 `Startup` 클래스의 서비스 컬렉션을 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="ee0d4-131">다음 규칙 예제는 이 토픽의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-131">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
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

## <a name="route-order"></a><span data-ttu-id="ee0d4-132">경로 순서</span><span class="sxs-lookup"><span data-stu-id="ee0d4-132">Route order</span></span>

<span data-ttu-id="ee0d4-133">경로는 처리를 위한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (경로 일치)를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="ee0d4-134">Order</span><span class="sxs-lookup"><span data-stu-id="ee0d4-134">Order</span></span>            | <span data-ttu-id="ee0d4-135">동작</span><span class="sxs-lookup"><span data-stu-id="ee0d4-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="ee0d4-136">-1</span><span class="sxs-lookup"><span data-stu-id="ee0d4-136">-1</span></span>               | <span data-ttu-id="ee0d4-137">경로는 다른 경로가 처리 되기 전에 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="ee0d4-138">0</span><span class="sxs-lookup"><span data-stu-id="ee0d4-138">0</span></span>                | <span data-ttu-id="ee0d4-139">순서가 지정 되지 않은 경우 (기본값)</span><span class="sxs-lookup"><span data-stu-id="ee0d4-139">Order isn't specified (default value).</span></span> <span data-ttu-id="ee0d4-140">`Order` (`Order = null`)를 할당 하지 않으면 `Order`는 처리를 위해 경로를 0 (영)으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="ee0d4-141">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="ee0d4-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="ee0d4-142">경로 처리 순서를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="ee0d4-143">경로 처리는 규칙에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="ee0d4-144">경로는 순서 대로 처리 됩니다 (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="ee0d4-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="ee0d4-145">경로에 동일한 `Order`있는 경우 가장 구체적인 경로는 먼저 일치 하 고 그 다음에 더 작은 경로를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="ee0d4-146">동일한 `Order`의 경로와 동일한 수의 매개 변수를 요청 URL과 일치 하는 경우 경로는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>에 추가 된 순서 대로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="ee0d4-147">가능 하면 설정 된 경로 처리 순서에 따라 피하십시오.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="ee0d4-148">일반적으로 라우팅은 URL이 일치 하는 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="ee0d4-149">경로 `Order` 속성을 경로 요청으로 올바르게 설정 해야 하는 경우에는 응용 프로그램의 라우팅 체계가 클라이언트와 혼란 스 러 울 수 있으며 유지 관리가 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="ee0d4-150">응용 프로그램의 라우팅 체계를 간소화 하기 위해 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="ee0d4-151">샘플 앱에는 단일 앱을 사용 하는 여러 라우팅 시나리오를 보여 주기 위해 명시적인 경로 처리가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="ee0d4-152">그러나 프로덕션 앱에서 경로 `Order`를 설정 하는 방법은 사용 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="ee0d4-153">Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="ee0d4-154">MVC 항목의 경로 순서에 대 한 정보는 [컨트롤러 작업에 대 한 라우팅: 특성 경로 순서](xref:mvc/controllers/routing#ordering-attribute-routes)지정에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="ee0d4-155">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-155">Model conventions</span></span>

<span data-ttu-id="ee0d4-156"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>에 대 한 대리자를 추가 하 여 Razor Pages에 적용 되는 [모델 규칙](xref:mvc/controllers/application-model#conventions) 을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="ee0d4-157">모든 페이지에 경로 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="ee0d4-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="ee0d4-158"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>를 사용 하 여 페이지 경로 모델 생성 중에 적용 되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>를 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="ee0d4-159">샘플 앱은 앱의 모든 페이지에 `{globalTemplate?}` 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="ee0d4-160"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 속성을 `1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="ee0d4-161">이렇게 하면 샘플 앱에서 다음과 같은 경로 일치 동작을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="ee0d4-162">`TheContactPage/{text?}`에 대 한 경로 템플릿은 항목의 뒷부분에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="ee0d4-163">연락처 페이지 경로는 기본 순서 `null` (`Order = 0`) 이므로 `{globalTemplate?}` 경로 템플릿 이전에 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="ee0d4-164">`{aboutTemplate?}` 경로 템플릿은 항목의 뒷부분에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="ee0d4-165">`{aboutTemplate?}` 템플릿이 `Order`의 `2`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="ee0d4-166">`/About/RouteDataValue`에서 정보 페이지를 요청하는 경우 "RouteDataValue"는 `RouteData.Values["globalTemplate"]` 속성 설정으로 인해 `Order = 1`(`RouteData.Values["aboutTemplate"]`)이 아닌 `Order = 2`(`Order`)으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="ee0d4-167">`{otherPagesTemplate?}` 경로 템플릿은 항목의 뒷부분에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="ee0d4-168">`{otherPagesTemplate?}` 템플릿이 `Order`의 `2`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="ee0d4-169">경로 매개 변수를 사용 하 여 *Pages/otherpages* 폴더에 있는 페이지를 요청 하면 (예: `/OtherPages/Page1/RouteDataValue`)`Order = 2`속성을 설정 하 여 "RouteDataValue"가 `RouteData.Values["globalTemplate"]` (`Order = 1`)에 로드 되 고 `RouteData.Values["otherPagesTemplate"]` (`Order`)에 로드 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ee0d4-170">가능 하면 `Order`를 설정 하지 마십시오. 그러면 `Order = 0`됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ee0d4-171">경로를 사용 하 여 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ee0d4-172"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>추가와 같은 Razor Pages 옵션은 MVC가 `Startup.ConfigureServices`의 서비스 컬렉션에 추가 될 때 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ee0d4-173">예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="ee0d4-174">`localhost:5000/About/GlobalRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![GlobalRouteValue의 경로 세그먼트를 사용하여 정보 페이지를 요청합니다.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="ee0d4-177">모든 페이지에 앱 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="ee0d4-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="ee0d4-178"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>를 사용 하 여 페이지 앱 모델 생성 중에 적용 되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>를 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="ee0d4-179">이 항목의 뒷부분에서 이 규칙 및 다른 규칙을 설명하기 위해 샘플 앱에는 `AddHeaderAttribute` 클래스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="ee0d4-180">클래스 생성자가 `name` 문자열 및 `values` 문자열 배열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="ee0d4-181">이러한 값은 응답 헤더를 설정하는 `OnResultExecuting` 메서드에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="ee0d4-182">전체 클래스는 항목의 뒷부분에 나오는 [페이지 모델 작업 규칙](#page-model-action-conventions) 섹션에서 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="ee0d4-183">샘플 앱에서는 앱의 모든 페이지에 `AddHeaderAttribute` 헤더를 추가하는 `GlobalHeader` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="ee0d4-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee0d4-184">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="ee0d4-185">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 GlobalHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="ee0d4-187">모든 페이지에 처리기 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="ee0d4-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="ee0d4-188"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>를 사용 하 여 페이지 처리기 모델 생성 중에 적용 되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>를 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="ee0d4-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee0d4-189">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="ee0d4-190">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-190">Page route action conventions</span></span>

<span data-ttu-id="ee0d4-191"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>에서 파생 되는 기본 경로 모델 공급자는 페이지 경로를 구성 하기 위한 확장성 지점만 제공 하도록 디자인 된 규칙을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="ee0d4-192">폴더 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-192">Folder route model convention</span></span>

<span data-ttu-id="ee0d4-193"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용 하 여 지정 된 폴더의 모든 페이지에 대해 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="ee0d4-194">샘플 앱에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용하여 `{otherPagesTemplate?}` 경로 템플릿을 *OtherPages* 폴더의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="ee0d4-195"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="ee0d4-196">이렇게 하면 단일 경로 값을 제공 하는 경우 첫 번째 경로 데이터 값 위치에 대 한 우선 순위가 `{globalTemplate?}` (`1`항목의 앞부분에 설정 됨)에 대 한 우선 순위가 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="ee0d4-197">경로 매개 변수 값을 사용 하 여 *Pages/otherpages* 폴더의 페이지를 요청 하는 경우 (예: `/OtherPages/Page1/RouteDataValue`)`Order = 2`속성을 설정 하 여 "RouteDataValue"가 `RouteData.Values["globalTemplate"]` (`Order = 1`)로 로드 되 고 `RouteData.Values["otherPagesTemplate"]` (`Order`) 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ee0d4-198">가능 하면 `Order`를 설정 하지 마십시오. 그러면 `Order = 0`됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ee0d4-199">경로를 사용 하 여 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ee0d4-200">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`에서 샘플의 Page1 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages 폴더의 Page1은 GlobalRouteValue 및 OtherPagesRouteValue라는 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="ee0d4-203">페이지 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-203">Page route model convention</span></span>

<span data-ttu-id="ee0d4-204"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>를 사용 하 여 지정 된 이름을 가진 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 대 한 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="ee0d4-205">샘플 앱에서는 `AddPageRouteModelConvention`를 사용하여 `{aboutTemplate?}` 경로 템플릿을 정보 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="ee0d4-206"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="ee0d4-207">이렇게 하면 단일 경로 값을 제공 하는 경우 첫 번째 경로 데이터 값 위치에 대 한 우선 순위가 `{globalTemplate?}` (`1`항목의 앞부분에 설정 됨)에 대 한 우선 순위가 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="ee0d4-208">`/About/RouteDataValue`에서 경로 매개 변수 값을 사용 하 여 About 페이지를 요청 하는 경우`Order = 2`속성을 설정 하 여 "RouteDataValue"가 `RouteData.Values["globalTemplate"]` (`Order = 1`)에 로드 되 고 `RouteData.Values["aboutTemplate"]` (`Order`) 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ee0d4-209">가능 하면 `Order`를 설정 하지 마십시오. 그러면 `Order = 0`됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ee0d4-210">경로를 사용 하 여 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ee0d4-211">`localhost:5000/About/GlobalRouteValue/AboutRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![정보 페이지는 GlobalRouteValue 및 AboutRouteValue에 대한 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="ee0d4-214">매개 변수 변환기를 사용 하 여 페이지 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="ee0d4-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="ee0d4-215">ASP.NET Core에서 생성 된 페이지 경로는 매개 변수 변환기를 사용 하 여 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="ee0d4-216">매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하며 매개 변수의 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="ee0d4-217">예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="ee0d4-218">`PageRouteTransformerConvention` 페이지 경로 모델 규칙은 응용 프로그램에서 자동으로 생성 된 페이지 경로의 폴더 및 파일 이름 세그먼트에 매개 변수 변환기를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="ee0d4-219">예를 들어 */Pages/SubscriptionManagement/ViewAll.cshtml* 에 있는 Razor Pages 파일의 경로는 `/SubscriptionManagement/ViewAll`에서 `/subscription-management/view-all`로 다시 작성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="ee0d4-220">`PageRouteTransformerConvention`은 Razor Pages 폴더 및 파일 이름에서 제공 된 페이지 경로의 자동으로 생성 된 세그먼트만 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="ee0d4-221">`@page` 지시어를 사용 하 여 추가 된 경로 세그먼트는 변환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="ee0d4-222">또한이 규칙은 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>에 의해 추가 된 경로를 변환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="ee0d4-223">`PageRouteTransformerConvention`은 `Startup.ConfigureServices`의 옵션으로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
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

## <a name="configure-a-page-route"></a><span data-ttu-id="ee0d4-224">페이지 경로 구성</span><span class="sxs-lookup"><span data-stu-id="ee0d4-224">Configure a page route</span></span>

<span data-ttu-id="ee0d4-225">지정 된 페이지 경로에서 페이지에 대 한 경로를 구성 하려면 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="ee0d4-226">페이지에 생성된 링크는 지정된 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="ee0d4-227">`AddPageRoute`는 `AddPageRouteModelConvention`을 사용하여 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="ee0d4-228">샘플 앱은 `/TheContactPage`Contact.cshtml*의* 에 대한 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="ee0d4-229">연락처 페이지도 기본 경로를 통해 `/Contact`에 도달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="ee0d4-230">연락처 페이지에 대한 샘플 앱의 사용자 지정 경로는 선택적 `text` 경로 세그먼트(`{text?}`)에 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="ee0d4-231">방문자가 해당 `@page` 경로에 있는 페이지에 액세스하는 경우 페이지에는 해당 `/Contact` 지시문에 있는 이 선택적 세그먼트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="ee0d4-232">렌더링된 페이지의 **연락처** 링크에 생성된 URL이 업데이트된 경로를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![탐색 모음의 샘플 앱 연락처 링크](razor-pages-conventions/_static/contact-link.png)

![렌더링된 HTML에서 연락처 링크를 검사하면 href가 '/TheContactPage'로 설정되어 있음을 나타냅니다.](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="ee0d4-235">일반적인 경로 `/Contact` 또는 사용자 지정 경로 `/TheContactPage`에서 연락처 페이지를 방문하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="ee0d4-236">추가 `text` 경로 세그먼트를 제공하는 경우 페이지는 제공한 HTML 인코딩 세그먼트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL에서 'TextValue'라는 선택적 'text' 경로 세그먼트를 제공하는 에지 브라우저 예제입니다.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="ee0d4-239">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-239">Page model action conventions</span></span>

<span data-ttu-id="ee0d4-240"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>를 구현 하는 기본 페이지 모델 공급자는 페이지 모델을 구성 하는 데 필요한 확장성을 제공 하도록 설계 된 규칙을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="ee0d4-241">이러한 규칙은 페이지 검색 및 처리 시나리오를 빌드하고 수정하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="ee0d4-242">이 섹션의 예제에서 샘플 앱은 응답 헤더를 적용 하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>`AddHeaderAttribute` 클래스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="ee0d4-243">샘플을 규칙을 사용하여 폴더에 있는 모든 페이지 및 단일 페이지에 특성을 적용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="ee0d4-244">**폴더 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="ee0d4-244">**Folder app model convention**</span></span>

<span data-ttu-id="ee0d4-245"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>를 사용 하 여 지정 된 폴더의 모든 페이지에 대해 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 인스턴스에서 동작을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="ee0d4-246">이 샘플은 `AddFolderApplicationModelConvention`OtherPages`OtherPagesHeader` 폴더 내의 페이지에 *헤더를 추가하여 앱의*을 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="ee0d4-247">`localhost:5000/OtherPages/Page1`에서 샘플의 Page1 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 페이지의 응답 헤더는 OtherPagesHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="ee0d4-249">**페이지 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="ee0d4-249">**Page app model convention**</span></span>

<span data-ttu-id="ee0d4-250"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>를 사용 하 여 지정 된 이름을 가진 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>에 대 한 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="ee0d4-251">샘플은 정보 페이지에 `AddPageApplicationModelConvention` 헤더를 추가하여 `AboutHeader`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="ee0d4-252">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 AboutHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="ee0d4-254">**필터 구성**</span><span class="sxs-lookup"><span data-stu-id="ee0d4-254">**Configure a filter**</span></span>

<span data-ttu-id="ee0d4-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 지정 된 필터를 적용 하도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="ee0d4-256">필터 클래스를 구현할 수 있지만 샘플 앱은 람다 식으로 필터를 구현하는 방법을 보여줍니다. 그러면 백그라운드에서 필터를 반환하는 팩터리로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="ee0d4-257">페이지 앱 모델은 *OtherPages* 폴더에 있는 Page2 페이지로 이동하는 세그먼트에 대한 상대 경로를 확인하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="ee0d4-258">조건이 통과하는 경우 헤더가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="ee0d4-259">그렇지 않으면 `EmptyFilter`이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="ee0d4-260">`EmptyFilter`은 [작업 필터](xref:mvc/controllers/filters#action-filters)입니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="ee0d4-261">작업 필터는 Razor Pages에서 무시 되므로 경로에 `OtherPages/Page2`가 포함 되지 않은 경우 `EmptyFilter`는 의도 한 대로 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="ee0d4-262">`localhost:5000/OtherPages/Page2`에서 샘플의 Page2 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header는 Page2에 대한 응답에 추가됩니다.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="ee0d4-264">**필터 팩터리 구성**</span><span class="sxs-lookup"><span data-stu-id="ee0d4-264">**Configure a filter factory**</span></span>

<span data-ttu-id="ee0d4-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 지정 된 팩터리를 구성 하 여 모든 Razor Pages에 [필터](xref:mvc/controllers/filters) 를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="ee0d4-266">샘플 앱은 앱의 페이지에 두 개의 값이 포함된 [ 헤더를 추가하여 ](xref:mvc/controllers/filters#ifilterfactory)필터 팩터리`FilterFactoryHeader`를 사용하는 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="ee0d4-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee0d4-267">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="ee0d4-268">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더에서는 두 개의 FilterFactoryHeader 헤더가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="ee0d4-270">MVC 필터 및 페이지 필터(IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="ee0d4-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="ee0d4-271">Razor 페이지가 처리기 메서드를 사용하므로 MVC [작업 필터](xref:mvc/controllers/filters#action-filters)는 Razor 페이지에서 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="ee0d4-272">[권한 부여](xref:mvc/controllers/filters#authorization-filters), [예외](xref:mvc/controllers/filters#exception-filters), [리소스](xref:mvc/controllers/filters#resource-filters) 및 [결과](xref:mvc/controllers/filters#result-filters)에 다른 유형의 MVC 필터를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="ee0d4-273">자세한 내용은 [필터](xref:mvc/controllers/filters) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="ee0d4-274">페이지 필터 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>)는 Razor Pages에 적용 되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="ee0d4-275">자세한 내용은 [Razor 페이지에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ee0d4-276">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ee0d4-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ee0d4-277">페이지 [경로 및 앱 모델 공급자 규칙](xref:mvc/controllers/application-model#conventions)을 사용하여 Razor 페이지 앱에서 페이지 라우팅, 검색 및 처리를 제어하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-277">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="ee0d4-278">개별 페이지에 대한 사용자 지정 페이지 경로를 구성해야 하는 경우 이 항목의 뒷부분에서 설명할 [AddPageRoute 규칙](#configure-a-page-route)을 사용하여 페이지에 대한 라우팅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-278">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="ee0d4-279">페이지 경로를 지정 하거나, 경로 세그먼트를 추가 하거나, 경로에 매개 변수를 추가 하려면 페이지의 `@page` 지시어를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-279">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="ee0d4-280">자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-280">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="ee0d4-281">경로 세그먼트 또는 매개 변수 이름으로 사용할 수 없는 예약어가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-281">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="ee0d4-282">자세한 내용은 [라우팅: 예약 된 라우팅 이름](xref:fundamentals/routing#reserved-routing-names)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-282">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="ee0d4-283">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ee0d4-283">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="ee0d4-284">시나리오</span><span class="sxs-lookup"><span data-stu-id="ee0d4-284">Scenario</span></span> | <span data-ttu-id="ee0d4-285">샘플에서는 다음 사항을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-285">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="ee0d4-286">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-286">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="ee0d4-287">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="ee0d4-287">Conventions.Add</span></span><ul><li><span data-ttu-id="ee0d4-288">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-288">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="ee0d4-289">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-289">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="ee0d4-290">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-290">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="ee0d4-291">경로 템플릿 및 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-291">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="ee0d4-292">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-292">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="ee0d4-293">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-293">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="ee0d4-294">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-294">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="ee0d4-295">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="ee0d4-295">AddPageRoute</span></span></li></ul> | <span data-ttu-id="ee0d4-296">폴더에 있는 페이지 및 단일 페이지에 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-296">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="ee0d4-297">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-297">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="ee0d4-298">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-298">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="ee0d4-299">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-299">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="ee0d4-300">ConfigureFilter(필터 클래스, 람다 식 또는 필터 팩터리)</span><span class="sxs-lookup"><span data-stu-id="ee0d4-300">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="ee0d4-301">폴더의 페이지에 헤더를 추가하고, 단일 페이지에 헤더를 추가하고, [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 구성하여 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-301">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="ee0d4-302"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 확장 메서드를 사용 하 여 Razor Pages 규칙을 추가 하 고 구성 하 여 `Startup` 클래스의 서비스 컬렉션을 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-302">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="ee0d4-303">다음 규칙 예제는 이 토픽의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-303">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="ee0d4-304">경로 순서</span><span class="sxs-lookup"><span data-stu-id="ee0d4-304">Route order</span></span>

<span data-ttu-id="ee0d4-305">경로는 처리를 위한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (경로 일치)를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-305">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="ee0d4-306">Order</span><span class="sxs-lookup"><span data-stu-id="ee0d4-306">Order</span></span>            | <span data-ttu-id="ee0d4-307">동작</span><span class="sxs-lookup"><span data-stu-id="ee0d4-307">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="ee0d4-308">-1</span><span class="sxs-lookup"><span data-stu-id="ee0d4-308">-1</span></span>               | <span data-ttu-id="ee0d4-309">경로는 다른 경로가 처리 되기 전에 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-309">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="ee0d4-310">0</span><span class="sxs-lookup"><span data-stu-id="ee0d4-310">0</span></span>                | <span data-ttu-id="ee0d4-311">순서가 지정 되지 않은 경우 (기본값)</span><span class="sxs-lookup"><span data-stu-id="ee0d4-311">Order isn't specified (default value).</span></span> <span data-ttu-id="ee0d4-312">`Order` (`Order = null`)를 할당 하지 않으면 `Order`는 처리를 위해 경로를 0 (영)으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-312">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="ee0d4-313">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="ee0d4-313">1, 2, &hellip; n</span></span> | <span data-ttu-id="ee0d4-314">경로 처리 순서를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-314">Specifies the route processing order.</span></span> |

<span data-ttu-id="ee0d4-315">경로 처리는 규칙에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-315">Route processing is established by convention:</span></span>

* <span data-ttu-id="ee0d4-316">경로는 순서 대로 처리 됩니다 (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="ee0d4-316">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="ee0d4-317">경로에 동일한 `Order`있는 경우 가장 구체적인 경로는 먼저 일치 하 고 그 다음에 더 작은 경로를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-317">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="ee0d4-318">동일한 `Order`의 경로와 동일한 수의 매개 변수를 요청 URL과 일치 하는 경우 경로는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>에 추가 된 순서 대로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-318">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="ee0d4-319">가능 하면 설정 된 경로 처리 순서에 따라 피하십시오.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-319">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="ee0d4-320">일반적으로 라우팅은 URL이 일치 하는 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-320">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="ee0d4-321">경로 `Order` 속성을 경로 요청으로 올바르게 설정 해야 하는 경우에는 응용 프로그램의 라우팅 체계가 클라이언트와 혼란 스 러 울 수 있으며 유지 관리가 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-321">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="ee0d4-322">응용 프로그램의 라우팅 체계를 간소화 하기 위해 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-322">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="ee0d4-323">샘플 앱에는 단일 앱을 사용 하는 여러 라우팅 시나리오를 보여 주기 위해 명시적인 경로 처리가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-323">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="ee0d4-324">그러나 프로덕션 앱에서 경로 `Order`를 설정 하는 방법은 사용 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-324">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="ee0d4-325">Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-325">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="ee0d4-326">MVC 항목의 경로 순서에 대 한 정보는 [컨트롤러 작업에 대 한 라우팅: 특성 경로 순서](xref:mvc/controllers/routing#ordering-attribute-routes)지정에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-326">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="ee0d4-327">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-327">Model conventions</span></span>

<span data-ttu-id="ee0d4-328"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>에 대 한 대리자를 추가 하 여 Razor Pages에 적용 되는 [모델 규칙](xref:mvc/controllers/application-model#conventions) 을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-328">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="ee0d4-329">모든 페이지에 경로 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="ee0d4-329">Add a route model convention to all pages</span></span>

<span data-ttu-id="ee0d4-330"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>를 사용 하 여 페이지 경로 모델 생성 중에 적용 되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>를 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-330">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="ee0d4-331">샘플 앱은 앱의 모든 페이지에 `{globalTemplate?}` 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-331">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="ee0d4-332"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 속성을 `1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-332">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="ee0d4-333">이렇게 하면 샘플 앱에서 다음과 같은 경로 일치 동작을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-333">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="ee0d4-334">`TheContactPage/{text?}`에 대 한 경로 템플릿은 항목의 뒷부분에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-334">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="ee0d4-335">연락처 페이지 경로는 기본 순서 `null` (`Order = 0`) 이므로 `{globalTemplate?}` 경로 템플릿 이전에 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-335">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="ee0d4-336">`{aboutTemplate?}` 경로 템플릿은 항목의 뒷부분에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-336">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="ee0d4-337">`{aboutTemplate?}` 템플릿이 `Order`의 `2`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-337">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="ee0d4-338">`/About/RouteDataValue`에서 정보 페이지를 요청하는 경우 "RouteDataValue"는 `RouteData.Values["globalTemplate"]` 속성 설정으로 인해 `Order = 1`(`RouteData.Values["aboutTemplate"]`)이 아닌 `Order = 2`(`Order`)으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-338">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="ee0d4-339">`{otherPagesTemplate?}` 경로 템플릿은 항목의 뒷부분에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-339">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="ee0d4-340">`{otherPagesTemplate?}` 템플릿이 `Order`의 `2`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-340">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="ee0d4-341">경로 매개 변수를 사용 하 여 *Pages/otherpages* 폴더에 있는 페이지를 요청 하면 (예: `/OtherPages/Page1/RouteDataValue`)`Order = 2`속성을 설정 하 여 "RouteDataValue"가 `RouteData.Values["globalTemplate"]` (`Order = 1`)에 로드 되 고 `RouteData.Values["otherPagesTemplate"]` (`Order`)에 로드 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-341">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ee0d4-342">가능 하면 `Order`를 설정 하지 마십시오. 그러면 `Order = 0`됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-342">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ee0d4-343">경로를 사용 하 여 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-343">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ee0d4-344"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>추가와 같은 Razor Pages 옵션은 MVC가 `Startup.ConfigureServices`의 서비스 컬렉션에 추가 될 때 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-344">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ee0d4-345">예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-345">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="ee0d4-346">`localhost:5000/About/GlobalRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-346">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![GlobalRouteValue의 경로 세그먼트를 사용하여 정보 페이지를 요청합니다.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="ee0d4-349">모든 페이지에 앱 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="ee0d4-349">Add an app model convention to all pages</span></span>

<span data-ttu-id="ee0d4-350"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>를 사용 하 여 페이지 앱 모델 생성 중에 적용 되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>를 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-350">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="ee0d4-351">이 항목의 뒷부분에서 이 규칙 및 다른 규칙을 설명하기 위해 샘플 앱에는 `AddHeaderAttribute` 클래스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-351">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="ee0d4-352">클래스 생성자가 `name` 문자열 및 `values` 문자열 배열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-352">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="ee0d4-353">이러한 값은 응답 헤더를 설정하는 `OnResultExecuting` 메서드에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-353">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="ee0d4-354">전체 클래스는 항목의 뒷부분에 나오는 [페이지 모델 작업 규칙](#page-model-action-conventions) 섹션에서 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-354">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="ee0d4-355">샘플 앱에서는 앱의 모든 페이지에 `AddHeaderAttribute` 헤더를 추가하는 `GlobalHeader` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-355">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="ee0d4-356">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee0d4-356">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="ee0d4-357">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-357">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 GlobalHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="ee0d4-359">모든 페이지에 처리기 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="ee0d4-359">Add a handler model convention to all pages</span></span>

<span data-ttu-id="ee0d4-360"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>를 사용 하 여 페이지 처리기 모델 생성 중에 적용 되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>를 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-360">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="ee0d4-361">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee0d4-361">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="ee0d4-362">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-362">Page route action conventions</span></span>

<span data-ttu-id="ee0d4-363"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>에서 파생 되는 기본 경로 모델 공급자는 페이지 경로를 구성 하기 위한 확장성 지점만 제공 하도록 디자인 된 규칙을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-363">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="ee0d4-364">폴더 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-364">Folder route model convention</span></span>

<span data-ttu-id="ee0d4-365"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용 하 여 지정 된 폴더의 모든 페이지에 대해 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-365">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="ee0d4-366">샘플 앱에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용하여 `{otherPagesTemplate?}` 경로 템플릿을 *OtherPages* 폴더의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-366">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="ee0d4-367"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-367">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="ee0d4-368">이렇게 하면 단일 경로 값을 제공 하는 경우 첫 번째 경로 데이터 값 위치에 대 한 우선 순위가 `{globalTemplate?}` (`1`항목의 앞부분에 설정 됨)에 대 한 우선 순위가 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-368">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="ee0d4-369">경로 매개 변수 값을 사용 하 여 *Pages/otherpages* 폴더의 페이지를 요청 하는 경우 (예: `/OtherPages/Page1/RouteDataValue`)`Order = 2`속성을 설정 하 여 "RouteDataValue"가 `RouteData.Values["globalTemplate"]` (`Order = 1`)로 로드 되 고 `RouteData.Values["otherPagesTemplate"]` (`Order`) 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-369">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ee0d4-370">가능 하면 `Order`를 설정 하지 마십시오. 그러면 `Order = 0`됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-370">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ee0d4-371">경로를 사용 하 여 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-371">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ee0d4-372">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`에서 샘플의 Page1 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-372">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages 폴더의 Page1은 GlobalRouteValue 및 OtherPagesRouteValue라는 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="ee0d4-375">페이지 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-375">Page route model convention</span></span>

<span data-ttu-id="ee0d4-376"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>를 사용 하 여 지정 된 이름을 가진 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 대 한 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-376">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="ee0d4-377">샘플 앱에서는 `AddPageRouteModelConvention`를 사용하여 `{aboutTemplate?}` 경로 템플릿을 정보 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-377">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="ee0d4-378"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-378">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="ee0d4-379">이렇게 하면 단일 경로 값을 제공 하는 경우 첫 번째 경로 데이터 값 위치에 대 한 우선 순위가 `{globalTemplate?}` (`1`항목의 앞부분에 설정 됨)에 대 한 우선 순위가 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-379">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="ee0d4-380">`/About/RouteDataValue`에서 경로 매개 변수 값을 사용 하 여 About 페이지를 요청 하는 경우`Order = 2`속성을 설정 하 여 "RouteDataValue"가 `RouteData.Values["globalTemplate"]` (`Order = 1`)에 로드 되 고 `RouteData.Values["aboutTemplate"]` (`Order`) 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-380">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ee0d4-381">가능 하면 `Order`를 설정 하지 마십시오. 그러면 `Order = 0`됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-381">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ee0d4-382">경로를 사용 하 여 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-382">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ee0d4-383">`localhost:5000/About/GlobalRouteValue/AboutRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-383">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![정보 페이지는 GlobalRouteValue 및 AboutRouteValue에 대한 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="ee0d4-386">매개 변수 변환기를 사용 하 여 페이지 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="ee0d4-386">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="ee0d4-387">ASP.NET Core에서 생성 된 페이지 경로는 매개 변수 변환기를 사용 하 여 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-387">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="ee0d4-388">매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하며 매개 변수의 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-388">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="ee0d4-389">예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-389">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="ee0d4-390">`PageRouteTransformerConvention` 페이지 경로 모델 규칙은 응용 프로그램에서 자동으로 생성 된 페이지 경로의 폴더 및 파일 이름 세그먼트에 매개 변수 변환기를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-390">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="ee0d4-391">예를 들어 */Pages/SubscriptionManagement/ViewAll.cshtml* 에 있는 Razor Pages 파일의 경로는 `/SubscriptionManagement/ViewAll`에서 `/subscription-management/view-all`로 다시 작성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-391">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="ee0d4-392">`PageRouteTransformerConvention`은 Razor Pages 폴더 및 파일 이름에서 제공 된 페이지 경로의 자동으로 생성 된 세그먼트만 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-392">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="ee0d4-393">`@page` 지시어를 사용 하 여 추가 된 경로 세그먼트는 변환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-393">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="ee0d4-394">또한이 규칙은 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>에 의해 추가 된 경로를 변환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-394">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="ee0d4-395">`PageRouteTransformerConvention`은 `Startup.ConfigureServices`의 옵션으로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-395">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="ee0d4-396">페이지 경로 구성</span><span class="sxs-lookup"><span data-stu-id="ee0d4-396">Configure a page route</span></span>

<span data-ttu-id="ee0d4-397">지정 된 페이지 경로에서 페이지에 대 한 경로를 구성 하려면 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-397">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="ee0d4-398">페이지에 생성된 링크는 지정된 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-398">Generated links to the page use your specified route.</span></span> <span data-ttu-id="ee0d4-399">`AddPageRoute`는 `AddPageRouteModelConvention`을 사용하여 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-399">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="ee0d4-400">샘플 앱은 `/TheContactPage`Contact.cshtml*의* 에 대한 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-400">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="ee0d4-401">연락처 페이지도 기본 경로를 통해 `/Contact`에 도달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-401">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="ee0d4-402">연락처 페이지에 대한 샘플 앱의 사용자 지정 경로는 선택적 `text` 경로 세그먼트(`{text?}`)에 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-402">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="ee0d4-403">방문자가 해당 `@page` 경로에 있는 페이지에 액세스하는 경우 페이지에는 해당 `/Contact` 지시문에 있는 이 선택적 세그먼트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-403">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="ee0d4-404">렌더링된 페이지의 **연락처** 링크에 생성된 URL이 업데이트된 경로를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-404">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![탐색 모음의 샘플 앱 연락처 링크](razor-pages-conventions/_static/contact-link.png)

![렌더링된 HTML에서 연락처 링크를 검사하면 href가 '/TheContactPage'로 설정되어 있음을 나타냅니다.](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="ee0d4-407">일반적인 경로 `/Contact` 또는 사용자 지정 경로 `/TheContactPage`에서 연락처 페이지를 방문하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-407">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="ee0d4-408">추가 `text` 경로 세그먼트를 제공하는 경우 페이지는 제공한 HTML 인코딩 세그먼트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-408">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL에서 'TextValue'라는 선택적 'text' 경로 세그먼트를 제공하는 에지 브라우저 예제입니다.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="ee0d4-411">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-411">Page model action conventions</span></span>

<span data-ttu-id="ee0d4-412"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>를 구현 하는 기본 페이지 모델 공급자는 페이지 모델을 구성 하는 데 필요한 확장성을 제공 하도록 설계 된 규칙을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-412">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="ee0d4-413">이러한 규칙은 페이지 검색 및 처리 시나리오를 빌드하고 수정하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-413">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="ee0d4-414">이 섹션의 예제에서 샘플 앱은 응답 헤더를 적용 하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>`AddHeaderAttribute` 클래스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-414">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="ee0d4-415">샘플을 규칙을 사용하여 폴더에 있는 모든 페이지 및 단일 페이지에 특성을 적용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-415">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="ee0d4-416">**폴더 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="ee0d4-416">**Folder app model convention**</span></span>

<span data-ttu-id="ee0d4-417"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>를 사용 하 여 지정 된 폴더의 모든 페이지에 대해 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 인스턴스에서 동작을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-417">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="ee0d4-418">이 샘플은 `AddFolderApplicationModelConvention`OtherPages`OtherPagesHeader` 폴더 내의 페이지에 *헤더를 추가하여 앱의*을 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-418">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="ee0d4-419">`localhost:5000/OtherPages/Page1`에서 샘플의 Page1 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-419">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 페이지의 응답 헤더는 OtherPagesHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="ee0d4-421">**페이지 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="ee0d4-421">**Page app model convention**</span></span>

<span data-ttu-id="ee0d4-422"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>를 사용 하 여 지정 된 이름을 가진 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>에 대 한 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-422">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="ee0d4-423">샘플은 정보 페이지에 `AddPageApplicationModelConvention` 헤더를 추가하여 `AboutHeader`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-423">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="ee0d4-424">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-424">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 AboutHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="ee0d4-426">**필터 구성**</span><span class="sxs-lookup"><span data-stu-id="ee0d4-426">**Configure a filter**</span></span>

<span data-ttu-id="ee0d4-427"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 지정 된 필터를 적용 하도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-427"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="ee0d4-428">필터 클래스를 구현할 수 있지만 샘플 앱은 람다 식으로 필터를 구현하는 방법을 보여줍니다. 그러면 백그라운드에서 필터를 반환하는 팩터리로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-428">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="ee0d4-429">페이지 앱 모델은 *OtherPages* 폴더에 있는 Page2 페이지로 이동하는 세그먼트에 대한 상대 경로를 확인하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-429">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="ee0d4-430">조건이 통과하는 경우 헤더가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-430">If the condition passes, a header is added.</span></span> <span data-ttu-id="ee0d4-431">그렇지 않으면 `EmptyFilter`이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-431">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="ee0d4-432">`EmptyFilter`은 [작업 필터](xref:mvc/controllers/filters#action-filters)입니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-432">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="ee0d4-433">작업 필터는 Razor Pages에서 무시 되므로 경로에 `OtherPages/Page2`가 포함 되지 않은 경우 `EmptyFilter`는 의도 한 대로 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-433">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="ee0d4-434">`localhost:5000/OtherPages/Page2`에서 샘플의 Page2 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-434">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header는 Page2에 대한 응답에 추가됩니다.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="ee0d4-436">**필터 팩터리 구성**</span><span class="sxs-lookup"><span data-stu-id="ee0d4-436">**Configure a filter factory**</span></span>

<span data-ttu-id="ee0d4-437"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 지정 된 팩터리를 구성 하 여 모든 Razor Pages에 [필터](xref:mvc/controllers/filters) 를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-437"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="ee0d4-438">샘플 앱은 앱의 페이지에 두 개의 값이 포함된 [ 헤더를 추가하여 ](xref:mvc/controllers/filters#ifilterfactory)필터 팩터리`FilterFactoryHeader`를 사용하는 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-438">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="ee0d4-439">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee0d4-439">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="ee0d4-440">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-440">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더에서는 두 개의 FilterFactoryHeader 헤더가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="ee0d4-442">MVC 필터 및 페이지 필터(IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="ee0d4-442">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="ee0d4-443">Razor 페이지가 처리기 메서드를 사용하므로 MVC [작업 필터](xref:mvc/controllers/filters#action-filters)는 Razor 페이지에서 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-443">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="ee0d4-444">[권한 부여](xref:mvc/controllers/filters#authorization-filters), [예외](xref:mvc/controllers/filters#exception-filters), [리소스](xref:mvc/controllers/filters#resource-filters) 및 [결과](xref:mvc/controllers/filters#result-filters)에 다른 유형의 MVC 필터를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-444">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="ee0d4-445">자세한 내용은 [필터](xref:mvc/controllers/filters) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-445">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="ee0d4-446">페이지 필터 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>)는 Razor Pages에 적용 되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-446">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="ee0d4-447">자세한 내용은 [Razor 페이지에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-447">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ee0d4-448">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ee0d4-448">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ee0d4-449">페이지 [경로 및 앱 모델 공급자 규칙](xref:mvc/controllers/application-model#conventions)을 사용하여 Razor 페이지 앱에서 페이지 라우팅, 검색 및 처리를 제어하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-449">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="ee0d4-450">개별 페이지에 대한 사용자 지정 페이지 경로를 구성해야 하는 경우 이 항목의 뒷부분에서 설명할 [AddPageRoute 규칙](#configure-a-page-route)을 사용하여 페이지에 대한 라우팅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-450">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="ee0d4-451">페이지 경로를 지정 하거나, 경로 세그먼트를 추가 하거나, 경로에 매개 변수를 추가 하려면 페이지의 `@page` 지시어를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-451">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="ee0d4-452">자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-452">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="ee0d4-453">경로 세그먼트 또는 매개 변수 이름으로 사용할 수 없는 예약어가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-453">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="ee0d4-454">자세한 내용은 [라우팅: 예약 된 라우팅 이름](xref:fundamentals/routing#reserved-routing-names)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-454">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="ee0d4-455">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ee0d4-455">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="ee0d4-456">시나리오</span><span class="sxs-lookup"><span data-stu-id="ee0d4-456">Scenario</span></span> | <span data-ttu-id="ee0d4-457">샘플에서는 다음 사항을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-457">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="ee0d4-458">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-458">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="ee0d4-459">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="ee0d4-459">Conventions.Add</span></span><ul><li><span data-ttu-id="ee0d4-460">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-460">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="ee0d4-461">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-461">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="ee0d4-462">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-462">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="ee0d4-463">경로 템플릿 및 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-463">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="ee0d4-464">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-464">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="ee0d4-465">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-465">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="ee0d4-466">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-466">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="ee0d4-467">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="ee0d4-467">AddPageRoute</span></span></li></ul> | <span data-ttu-id="ee0d4-468">폴더에 있는 페이지 및 단일 페이지에 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-468">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="ee0d4-469">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-469">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="ee0d4-470">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-470">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="ee0d4-471">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="ee0d4-471">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="ee0d4-472">ConfigureFilter(필터 클래스, 람다 식 또는 필터 팩터리)</span><span class="sxs-lookup"><span data-stu-id="ee0d4-472">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="ee0d4-473">폴더의 페이지에 헤더를 추가하고, 단일 페이지에 헤더를 추가하고, [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 구성하여 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-473">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="ee0d4-474"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 확장 메서드를 사용 하 여 Razor Pages 규칙을 추가 하 고 구성 하 여 `Startup` 클래스의 서비스 컬렉션을 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-474">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="ee0d4-475">다음 규칙 예제는 이 토픽의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-475">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="ee0d4-476">경로 순서</span><span class="sxs-lookup"><span data-stu-id="ee0d4-476">Route order</span></span>

<span data-ttu-id="ee0d4-477">경로는 처리를 위한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (경로 일치)를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-477">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="ee0d4-478">Order</span><span class="sxs-lookup"><span data-stu-id="ee0d4-478">Order</span></span>            | <span data-ttu-id="ee0d4-479">동작</span><span class="sxs-lookup"><span data-stu-id="ee0d4-479">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="ee0d4-480">-1</span><span class="sxs-lookup"><span data-stu-id="ee0d4-480">-1</span></span>               | <span data-ttu-id="ee0d4-481">경로는 다른 경로가 처리 되기 전에 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-481">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="ee0d4-482">0</span><span class="sxs-lookup"><span data-stu-id="ee0d4-482">0</span></span>                | <span data-ttu-id="ee0d4-483">순서가 지정 되지 않은 경우 (기본값)</span><span class="sxs-lookup"><span data-stu-id="ee0d4-483">Order isn't specified (default value).</span></span> <span data-ttu-id="ee0d4-484">`Order` (`Order = null`)를 할당 하지 않으면 `Order`는 처리를 위해 경로를 0 (영)으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-484">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="ee0d4-485">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="ee0d4-485">1, 2, &hellip; n</span></span> | <span data-ttu-id="ee0d4-486">경로 처리 순서를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-486">Specifies the route processing order.</span></span> |

<span data-ttu-id="ee0d4-487">경로 처리는 규칙에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-487">Route processing is established by convention:</span></span>

* <span data-ttu-id="ee0d4-488">경로는 순서 대로 처리 됩니다 (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="ee0d4-488">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="ee0d4-489">경로에 동일한 `Order`있는 경우 가장 구체적인 경로는 먼저 일치 하 고 그 다음에 더 작은 경로를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-489">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="ee0d4-490">동일한 `Order`의 경로와 동일한 수의 매개 변수를 요청 URL과 일치 하는 경우 경로는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>에 추가 된 순서 대로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-490">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="ee0d4-491">가능 하면 설정 된 경로 처리 순서에 따라 피하십시오.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-491">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="ee0d4-492">일반적으로 라우팅은 URL이 일치 하는 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-492">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="ee0d4-493">경로 `Order` 속성을 경로 요청으로 올바르게 설정 해야 하는 경우에는 응용 프로그램의 라우팅 체계가 클라이언트와 혼란 스 러 울 수 있으며 유지 관리가 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-493">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="ee0d4-494">응용 프로그램의 라우팅 체계를 간소화 하기 위해 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-494">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="ee0d4-495">샘플 앱에는 단일 앱을 사용 하는 여러 라우팅 시나리오를 보여 주기 위해 명시적인 경로 처리가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-495">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="ee0d4-496">그러나 프로덕션 앱에서 경로 `Order`를 설정 하는 방법은 사용 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-496">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="ee0d4-497">Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-497">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="ee0d4-498">MVC 항목의 경로 순서에 대 한 정보는 [컨트롤러 작업에 대 한 라우팅: 특성 경로 순서](xref:mvc/controllers/routing#ordering-attribute-routes)지정에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-498">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="ee0d4-499">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-499">Model conventions</span></span>

<span data-ttu-id="ee0d4-500"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>에 대 한 대리자를 추가 하 여 Razor Pages에 적용 되는 [모델 규칙](xref:mvc/controllers/application-model#conventions) 을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-500">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="ee0d4-501">모든 페이지에 경로 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="ee0d4-501">Add a route model convention to all pages</span></span>

<span data-ttu-id="ee0d4-502"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>를 사용 하 여 페이지 경로 모델 생성 중에 적용 되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>를 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-502">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="ee0d4-503">샘플 앱은 앱의 모든 페이지에 `{globalTemplate?}` 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-503">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="ee0d4-504"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 속성을 `1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-504">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="ee0d4-505">이렇게 하면 샘플 앱에서 다음과 같은 경로 일치 동작을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-505">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="ee0d4-506">`TheContactPage/{text?}`에 대 한 경로 템플릿은 항목의 뒷부분에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-506">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="ee0d4-507">연락처 페이지 경로는 기본 순서 `null` (`Order = 0`) 이므로 `{globalTemplate?}` 경로 템플릿 이전에 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-507">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="ee0d4-508">`{aboutTemplate?}` 경로 템플릿은 항목의 뒷부분에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-508">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="ee0d4-509">`{aboutTemplate?}` 템플릿이 `Order`의 `2`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-509">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="ee0d4-510">`/About/RouteDataValue`에서 정보 페이지를 요청하는 경우 "RouteDataValue"는 `RouteData.Values["globalTemplate"]` 속성 설정으로 인해 `Order = 1`(`RouteData.Values["aboutTemplate"]`)이 아닌 `Order = 2`(`Order`)으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-510">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="ee0d4-511">`{otherPagesTemplate?}` 경로 템플릿은 항목의 뒷부분에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-511">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="ee0d4-512">`{otherPagesTemplate?}` 템플릿이 `Order`의 `2`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-512">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="ee0d4-513">경로 매개 변수를 사용 하 여 *Pages/otherpages* 폴더에 있는 페이지를 요청 하면 (예: `/OtherPages/Page1/RouteDataValue`)`Order = 2`속성을 설정 하 여 "RouteDataValue"가 `RouteData.Values["globalTemplate"]` (`Order = 1`)에 로드 되 고 `RouteData.Values["otherPagesTemplate"]` (`Order`)에 로드 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-513">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ee0d4-514">가능 하면 `Order`를 설정 하지 마십시오. 그러면 `Order = 0`됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-514">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ee0d4-515">경로를 사용 하 여 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-515">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ee0d4-516"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>추가와 같은 Razor Pages 옵션은 MVC가 `Startup.ConfigureServices`의 서비스 컬렉션에 추가 될 때 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-516">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ee0d4-517">예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-517">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="ee0d4-518">`localhost:5000/About/GlobalRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-518">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![GlobalRouteValue의 경로 세그먼트를 사용하여 정보 페이지를 요청합니다.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="ee0d4-521">모든 페이지에 앱 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="ee0d4-521">Add an app model convention to all pages</span></span>

<span data-ttu-id="ee0d4-522"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>를 사용 하 여 페이지 앱 모델 생성 중에 적용 되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>를 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-522">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="ee0d4-523">이 항목의 뒷부분에서 이 규칙 및 다른 규칙을 설명하기 위해 샘플 앱에는 `AddHeaderAttribute` 클래스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-523">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="ee0d4-524">클래스 생성자가 `name` 문자열 및 `values` 문자열 배열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-524">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="ee0d4-525">이러한 값은 응답 헤더를 설정하는 `OnResultExecuting` 메서드에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-525">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="ee0d4-526">전체 클래스는 항목의 뒷부분에 나오는 [페이지 모델 작업 규칙](#page-model-action-conventions) 섹션에서 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-526">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="ee0d4-527">샘플 앱에서는 앱의 모든 페이지에 `AddHeaderAttribute` 헤더를 추가하는 `GlobalHeader` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-527">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="ee0d4-528">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee0d4-528">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="ee0d4-529">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-529">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 GlobalHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="ee0d4-531">모든 페이지에 처리기 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="ee0d4-531">Add a handler model convention to all pages</span></span>

<span data-ttu-id="ee0d4-532"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>를 사용 하 여 페이지 처리기 모델 생성 중에 적용 되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>를 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-532">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="ee0d4-533">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee0d4-533">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="ee0d4-534">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-534">Page route action conventions</span></span>

<span data-ttu-id="ee0d4-535"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>에서 파생 되는 기본 경로 모델 공급자는 페이지 경로를 구성 하기 위한 확장성 지점만 제공 하도록 디자인 된 규칙을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-535">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="ee0d4-536">폴더 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-536">Folder route model convention</span></span>

<span data-ttu-id="ee0d4-537"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용 하 여 지정 된 폴더의 모든 페이지에 대해 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-537">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="ee0d4-538">샘플 앱에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용하여 `{otherPagesTemplate?}` 경로 템플릿을 *OtherPages* 폴더의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-538">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="ee0d4-539"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-539">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="ee0d4-540">이렇게 하면 단일 경로 값을 제공 하는 경우 첫 번째 경로 데이터 값 위치에 대 한 우선 순위가 `{globalTemplate?}` (`1`항목의 앞부분에 설정 됨)에 대 한 우선 순위가 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-540">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="ee0d4-541">경로 매개 변수 값을 사용 하 여 *Pages/otherpages* 폴더의 페이지를 요청 하는 경우 (예: `/OtherPages/Page1/RouteDataValue`)`Order = 2`속성을 설정 하 여 "RouteDataValue"가 `RouteData.Values["globalTemplate"]` (`Order = 1`)로 로드 되 고 `RouteData.Values["otherPagesTemplate"]` (`Order`) 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-541">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ee0d4-542">가능 하면 `Order`를 설정 하지 마십시오. 그러면 `Order = 0`됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-542">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ee0d4-543">경로를 사용 하 여 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-543">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ee0d4-544">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`에서 샘플의 Page1 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-544">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages 폴더의 Page1은 GlobalRouteValue 및 OtherPagesRouteValue라는 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="ee0d4-547">페이지 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-547">Page route model convention</span></span>

<span data-ttu-id="ee0d4-548"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>를 사용 하 여 지정 된 이름을 가진 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 대 한 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-548">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="ee0d4-549">샘플 앱에서는 `AddPageRouteModelConvention`를 사용하여 `{aboutTemplate?}` 경로 템플릿을 정보 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-549">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="ee0d4-550"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-550">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="ee0d4-551">이렇게 하면 단일 경로 값을 제공 하는 경우 첫 번째 경로 데이터 값 위치에 대 한 우선 순위가 `{globalTemplate?}` (`1`항목의 앞부분에 설정 됨)에 대 한 우선 순위가 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-551">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="ee0d4-552">`/About/RouteDataValue`에서 경로 매개 변수 값을 사용 하 여 About 페이지를 요청 하는 경우`Order = 2`속성을 설정 하 여 "RouteDataValue"가 `RouteData.Values["globalTemplate"]` (`Order = 1`)에 로드 되 고 `RouteData.Values["aboutTemplate"]` (`Order`) 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-552">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="ee0d4-553">가능 하면 `Order`를 설정 하지 마십시오. 그러면 `Order = 0`됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-553">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="ee0d4-554">경로를 사용 하 여 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-554">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="ee0d4-555">`localhost:5000/About/GlobalRouteValue/AboutRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-555">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![정보 페이지는 GlobalRouteValue 및 AboutRouteValue에 대한 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="ee0d4-558">페이지 경로 구성</span><span class="sxs-lookup"><span data-stu-id="ee0d4-558">Configure a page route</span></span>

<span data-ttu-id="ee0d4-559">지정 된 페이지 경로에서 페이지에 대 한 경로를 구성 하려면 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-559">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="ee0d4-560">페이지에 생성된 링크는 지정된 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-560">Generated links to the page use your specified route.</span></span> <span data-ttu-id="ee0d4-561">`AddPageRoute`는 `AddPageRouteModelConvention`을 사용하여 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-561">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="ee0d4-562">샘플 앱은 `/TheContactPage`Contact.cshtml*의* 에 대한 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-562">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="ee0d4-563">연락처 페이지도 기본 경로를 통해 `/Contact`에 도달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-563">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="ee0d4-564">연락처 페이지에 대한 샘플 앱의 사용자 지정 경로는 선택적 `text` 경로 세그먼트(`{text?}`)에 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-564">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="ee0d4-565">방문자가 해당 `@page` 경로에 있는 페이지에 액세스하는 경우 페이지에는 해당 `/Contact` 지시문에 있는 이 선택적 세그먼트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-565">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="ee0d4-566">렌더링된 페이지의 **연락처** 링크에 생성된 URL이 업데이트된 경로를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-566">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![탐색 모음의 샘플 앱 연락처 링크](razor-pages-conventions/_static/contact-link.png)

![렌더링된 HTML에서 연락처 링크를 검사하면 href가 '/TheContactPage'로 설정되어 있음을 나타냅니다.](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="ee0d4-569">일반적인 경로 `/Contact` 또는 사용자 지정 경로 `/TheContactPage`에서 연락처 페이지를 방문하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-569">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="ee0d4-570">추가 `text` 경로 세그먼트를 제공하는 경우 페이지는 제공한 HTML 인코딩 세그먼트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-570">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL에서 'TextValue'라는 선택적 'text' 경로 세그먼트를 제공하는 에지 브라우저 예제입니다.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="ee0d4-573">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="ee0d4-573">Page model action conventions</span></span>

<span data-ttu-id="ee0d4-574"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>를 구현 하는 기본 페이지 모델 공급자는 페이지 모델을 구성 하는 데 필요한 확장성을 제공 하도록 설계 된 규칙을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-574">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="ee0d4-575">이러한 규칙은 페이지 검색 및 처리 시나리오를 빌드하고 수정하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-575">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="ee0d4-576">이 섹션의 예제에서 샘플 앱은 응답 헤더를 적용 하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>`AddHeaderAttribute` 클래스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-576">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="ee0d4-577">샘플을 규칙을 사용하여 폴더에 있는 모든 페이지 및 단일 페이지에 특성을 적용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-577">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="ee0d4-578">**폴더 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="ee0d4-578">**Folder app model convention**</span></span>

<span data-ttu-id="ee0d4-579"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>를 사용 하 여 지정 된 폴더의 모든 페이지에 대해 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 인스턴스에서 동작을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-579">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="ee0d4-580">이 샘플은 `AddFolderApplicationModelConvention`OtherPages`OtherPagesHeader` 폴더 내의 페이지에 *헤더를 추가하여 앱의*을 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-580">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="ee0d4-581">`localhost:5000/OtherPages/Page1`에서 샘플의 Page1 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-581">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 페이지의 응답 헤더는 OtherPagesHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="ee0d4-583">**페이지 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="ee0d4-583">**Page app model convention**</span></span>

<span data-ttu-id="ee0d4-584"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>를 사용 하 여 지정 된 이름을 가진 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>에 대 한 작업을 호출 하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-584">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="ee0d4-585">샘플은 정보 페이지에 `AddPageApplicationModelConvention` 헤더를 추가하여 `AboutHeader`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-585">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="ee0d4-586">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-586">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 AboutHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="ee0d4-588">**필터 구성**</span><span class="sxs-lookup"><span data-stu-id="ee0d4-588">**Configure a filter**</span></span>

<span data-ttu-id="ee0d4-589"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 지정 된 필터를 적용 하도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-589"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="ee0d4-590">필터 클래스를 구현할 수 있지만 샘플 앱은 람다 식으로 필터를 구현하는 방법을 보여줍니다. 그러면 백그라운드에서 필터를 반환하는 팩터리로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-590">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="ee0d4-591">페이지 앱 모델은 *OtherPages* 폴더에 있는 Page2 페이지로 이동하는 세그먼트에 대한 상대 경로를 확인하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-591">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="ee0d4-592">조건이 통과하는 경우 헤더가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-592">If the condition passes, a header is added.</span></span> <span data-ttu-id="ee0d4-593">그렇지 않으면 `EmptyFilter`이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-593">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="ee0d4-594">`EmptyFilter`은 [작업 필터](xref:mvc/controllers/filters#action-filters)입니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-594">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="ee0d4-595">작업 필터는 Razor Pages에서 무시 되므로 경로에 `OtherPages/Page2`가 포함 되지 않은 경우 `EmptyFilter`는 의도 한 대로 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-595">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="ee0d4-596">`localhost:5000/OtherPages/Page2`에서 샘플의 Page2 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-596">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header는 Page2에 대한 응답에 추가됩니다.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="ee0d4-598">**필터 팩터리 구성**</span><span class="sxs-lookup"><span data-stu-id="ee0d4-598">**Configure a filter factory**</span></span>

<span data-ttu-id="ee0d4-599"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 지정 된 팩터리를 구성 하 여 모든 Razor Pages에 [필터](xref:mvc/controllers/filters) 를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-599"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="ee0d4-600">샘플 앱은 앱의 페이지에 두 개의 값이 포함된 [ 헤더를 추가하여 ](xref:mvc/controllers/filters#ifilterfactory)필터 팩터리`FilterFactoryHeader`를 사용하는 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-600">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="ee0d4-601">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee0d4-601">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="ee0d4-602">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-602">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더에서는 두 개의 FilterFactoryHeader 헤더가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="ee0d4-604">MVC 필터 및 페이지 필터(IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="ee0d4-604">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="ee0d4-605">Razor 페이지가 처리기 메서드를 사용하므로 MVC [작업 필터](xref:mvc/controllers/filters#action-filters)는 Razor 페이지에서 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-605">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="ee0d4-606">[권한 부여](xref:mvc/controllers/filters#authorization-filters), [예외](xref:mvc/controllers/filters#exception-filters), [리소스](xref:mvc/controllers/filters#resource-filters) 및 [결과](xref:mvc/controllers/filters#result-filters)에 다른 유형의 MVC 필터를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-606">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="ee0d4-607">자세한 내용은 [필터](xref:mvc/controllers/filters) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-607">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="ee0d4-608">페이지 필터 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>)는 Razor Pages에 적용 되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-608">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="ee0d4-609">자세한 내용은 [Razor 페이지에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ee0d4-609">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ee0d4-610">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ee0d4-610">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
