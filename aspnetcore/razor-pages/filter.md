---
title: ASP.NET Core에서 Razor 페이지를 위한 필터 메서드
author: Rick-Anderson
description: ASP.NET Core에서 Razor 페이지를 위한 필터 메서드를 만드는 방법을 배웁니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 12/28/2019
uid: razor-pages/filter
ms.openlocfilehash: 02771219454556b236080c2668243f788693b2c1
ms.sourcegitcommit: 077b45eceae044475f04c1d7ef2d153d7c0515a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/29/2019
ms.locfileid: "75542711"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="baba9-103">ASP.NET Core에서 Razor 페이지를 위한 필터 메서드</span><span class="sxs-lookup"><span data-stu-id="baba9-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="baba9-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="baba9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="baba9-105">Razor 페이지 필터 [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0)와 [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0)는 Razor 페이지 처리기 실행 전후에 Razor 페이지에서 코드를 실행하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="baba9-106">Razor 페이지 필터는 개별 페이지 처리기 메서드에 적용할 수 없다는 점을 제외하고는 [ASP.NET Core MVC 작업 필터](xref:mvc/controllers/filters#action-filters)와 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="baba9-107">Razor 페이지 필터:</span><span class="sxs-lookup"><span data-stu-id="baba9-107">Razor Page filters:</span></span>

* <span data-ttu-id="baba9-108">처리기 메서드를 선택한 후 모델 바인딩이 발생하기 전에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="baba9-109">모델 바인딩이 완료된 후 처리기 메서드가 실행되기 전에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="baba9-110">처리기 메서드가 실행된 후 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="baba9-111">한 페이지에 또는 전역으로 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="baba9-112">특정 페이지 처리기 메서드에는 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="baba9-113">에는 DI ( [종속성 주입](xref:fundamentals/dependency-injection) )로 채워진 생성자 종속성이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="baba9-114">자세한 내용은 [servicefilterattribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) 및 [typefilterattribute](/aspnet/core/mvc/controllers/filters#typefilterattribute)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="baba9-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="baba9-115">처리기 메서드가 페이지 생성자 또는 미들웨어를 사용 하 여 실행 되기 전에 코드를 실행할 수 있지만 Razor 페이지 필터만 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext>에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-115">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext>.</span></span> <span data-ttu-id="baba9-116">필터에는 `HttpContext`에 대 한 액세스를 제공 하는 <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> 파생 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-116">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="baba9-117">예를 들어 [필터 특성 구현](#ifa) 샘플은 생성자 또는 미들웨어로 수행할 수 없는 응답에 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-117">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="baba9-118">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="baba9-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="baba9-119">Razor 페이지 필터는 전역 또는 페이지 수준에서 적용할 수 있는 다음과 같은 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-119">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="baba9-120">동기 메서드:</span><span class="sxs-lookup"><span data-stu-id="baba9-120">Synchronous methods:</span></span>

  * <span data-ttu-id="baba9-121">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): 처리기 메서드를 선택한 후 모델 바인딩이 발생하기 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-121">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="baba9-122">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): 모델 바인딩이 완료된 후 처리기 메서드가 실행되기 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-122">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="baba9-123">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): 처리기 메서드가 실행된 후 작업 결과 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-123">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="baba9-124">비동기 메서드:</span><span class="sxs-lookup"><span data-stu-id="baba9-124">Asynchronous methods:</span></span>

  * <span data-ttu-id="baba9-125">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): 처리기 메서드를 선택한 후 모델 바인딩이 발생하기 전에 비동기적으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-125">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="baba9-126">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): 모델 바인딩이 완료된 후 처리기 메서드가 호출되기 전에 비동기적으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-126">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="baba9-127">필터 인터페이스의 동기 또는 비동기 버전을 모두 구현하지 **말고** 그 중 **한 가지**만 구현하세요.</span><span class="sxs-lookup"><span data-stu-id="baba9-127">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="baba9-128">프레임워크는 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-128">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="baba9-129">그렇지 않으면 동기 인터페이스의 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-129">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="baba9-130">두 인터페이스를 모두 구현 하는 경우에는 비동기 메서드만 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-130">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="baba9-131">페이지의 재정의에 동일한 규칙이 적용되며, 재정의의 동기 또는 비동기 버전 중 하나만 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-131">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="baba9-132">Razor 페이지 필터를 전역으로 구현</span><span class="sxs-lookup"><span data-stu-id="baba9-132">Implement Razor Page filters globally</span></span>

<span data-ttu-id="baba9-133">다음 코드는 `IAsyncPageFilter`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-133">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="baba9-134">위의 코드에서 `ProcessUserAgent.Write` 사용자 에이전트 문자열로 작동 하는 사용자 제공 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-134">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="baba9-135">다음 코드는 `Startup` 클래스의 `SampleAsyncPageFilter`를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-135">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="baba9-136">다음 코드는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>를 호출 하 여 `SampleAsyncPageFilter`를 */동영상*의 페이지에만 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-136">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="baba9-137">다음 코드는 동기 `IPageFilter`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-137">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="baba9-138">다음 코드는 `SamplePageFilter`를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-138">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="baba9-139">필터 메서드를 재정의하여 Razor 페이지 필터 구현</span><span class="sxs-lookup"><span data-stu-id="baba9-139">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="baba9-140">다음 코드는 비동기 Razor 페이지 필터를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-140">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="baba9-141">필터 특성 구현</span><span class="sxs-lookup"><span data-stu-id="baba9-141">Implement a filter attribute</span></span>

<span data-ttu-id="baba9-142">기본 제공 특성 기반 필터 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> 필터를 서브클래싱 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-142">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="baba9-143">다음 필터는 응답에 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-143">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="baba9-144">다음 코드는 `AddHeader` 특성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-144">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="baba9-145">브라우저 개발자 도구와 같은 도구를 사용 하 여 헤더를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-145">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="baba9-146">**응답 헤더**아래에 `author: Rick` 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-146">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="baba9-147">순서 재정의에 대한 지침은 [기본 순서 재정의](xref:mvc/controllers/filters#overriding-the-default-order)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="baba9-147">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="baba9-148">필터에서 필터 파이프라인을 단락(short-circuit)시키는 지침은 [취소 및 단락](xref:mvc/controllers/filters#cancellation-and-short-circuiting)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="baba9-148">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="baba9-149">권한 부여 필터 특성</span><span class="sxs-lookup"><span data-stu-id="baba9-149">Authorize filter attribute</span></span>

<span data-ttu-id="baba9-150">[권한 부여](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 특성은 `PageModel`에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-150">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="baba9-151">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="baba9-151">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="baba9-152">Razor 페이지 필터 [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0)와 [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0)는 Razor 페이지 처리기 실행 전후에 Razor 페이지에서 코드를 실행하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-152">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="baba9-153">Razor 페이지 필터는 개별 페이지 처리기 메서드에 적용할 수 없다는 점을 제외하고는 [ASP.NET Core MVC 작업 필터](xref:mvc/controllers/filters#action-filters)와 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-153">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="baba9-154">Razor 페이지 필터:</span><span class="sxs-lookup"><span data-stu-id="baba9-154">Razor Page filters:</span></span>

* <span data-ttu-id="baba9-155">처리기 메서드를 선택한 후 모델 바인딩이 발생하기 전에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-155">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="baba9-156">모델 바인딩이 완료된 후 처리기 메서드가 실행되기 전에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-156">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="baba9-157">처리기 메서드가 실행된 후 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-157">Run code after the handler method executes.</span></span>
* <span data-ttu-id="baba9-158">한 페이지에 또는 전역으로 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-158">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="baba9-159">특정 페이지 처리기 메서드에는 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-159">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="baba9-160">페이지 생성자 또는 미들웨어를 사용하여 처리기 메서드를 실행하기 전에 코드를 실행할 수 있지만, Razor 페이지 필터만 [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext)에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-160">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="baba9-161">필터에는 `HttpContext`에 대한 액세스를 제공하는 [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) 파생 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-161">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="baba9-162">예를 들어 [필터 특성 구현](#ifa) 샘플은 생성자 또는 미들웨어로 수행할 수 없는 응답에 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-162">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="baba9-163">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="baba9-163">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="baba9-164">Razor 페이지 필터는 전역 또는 페이지 수준에서 적용할 수 있는 다음과 같은 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-164">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="baba9-165">동기 메서드:</span><span class="sxs-lookup"><span data-stu-id="baba9-165">Synchronous methods:</span></span>

  * <span data-ttu-id="baba9-166">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): 처리기 메서드를 선택한 후 모델 바인딩이 발생하기 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-166">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="baba9-167">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): 모델 바인딩이 완료된 후 처리기 메서드가 실행되기 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-167">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="baba9-168">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): 처리기 메서드가 실행된 후 작업 결과 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-168">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="baba9-169">비동기 메서드:</span><span class="sxs-lookup"><span data-stu-id="baba9-169">Asynchronous methods:</span></span>

  * <span data-ttu-id="baba9-170">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): 처리기 메서드를 선택한 후 모델 바인딩이 발생하기 전에 비동기적으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-170">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="baba9-171">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): 모델 바인딩이 완료된 후 처리기 메서드가 호출되기 전에 비동기적으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-171">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="baba9-172">필터 인터페이스의 동기 또는 비동기 버전 중 **하나**를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-172">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="baba9-173">프레임워크는 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-173">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="baba9-174">그렇지 않으면 동기 인터페이스의 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-174">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="baba9-175">두 인터페이스를 모두 구현 하는 경우에는 비동기 메서드만 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-175">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="baba9-176">페이지의 재정의에 동일한 규칙이 적용되며, 재정의의 동기 또는 비동기 버전 중 하나만 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-176">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="baba9-177">Razor 페이지 필터를 전역으로 구현</span><span class="sxs-lookup"><span data-stu-id="baba9-177">Implement Razor Page filters globally</span></span>

<span data-ttu-id="baba9-178">다음 코드는 `IAsyncPageFilter`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-178">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="baba9-179">위의 코드에서 [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0)는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-179">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="baba9-180">이는 샘플에서 애플리케이션에 대한 추적 정보를 제공하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-180">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="baba9-181">다음 코드는 `Startup` 클래스의 `SampleAsyncPageFilter`를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-181">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="baba9-182">다음 코드에서는 전체 `Startup` 클래스를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-182">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="baba9-183">다음 코드는 `AddFolderApplicationModelConvention`을 호출하여 `SampleAsyncPageFilter`를 */subFolder*의 페이지에만 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-183">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="baba9-184">다음 코드는 동기 `IPageFilter`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-184">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="baba9-185">다음 코드는 `SamplePageFilter`를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-185">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="baba9-186">필터 메서드를 재정의하여 Razor 페이지 필터 구현</span><span class="sxs-lookup"><span data-stu-id="baba9-186">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="baba9-187">다음 코드는 동기 Razor 페이지 필터를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-187">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="baba9-188">필터 특성 구현</span><span class="sxs-lookup"><span data-stu-id="baba9-188">Implement a filter attribute</span></span>

<span data-ttu-id="baba9-189">기본 제공 특성 기반 필터 [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) 필터는 서브클래싱할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-189">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="baba9-190">다음 필터는 응답에 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-190">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="baba9-191">다음 코드는 `AddHeader` 특성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-191">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="baba9-192">순서 재정의에 대한 지침은 [기본 순서 재정의](xref:mvc/controllers/filters#overriding-the-default-order)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="baba9-192">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="baba9-193">필터에서 필터 파이프라인을 단락(short-circuit)시키는 지침은 [취소 및 단락](xref:mvc/controllers/filters#cancellation-and-short-circuiting)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="baba9-193">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="baba9-194">권한 부여 필터 특성</span><span class="sxs-lookup"><span data-stu-id="baba9-194">Authorize filter attribute</span></span>

<span data-ttu-id="baba9-195">[권한 부여](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 특성은 `PageModel`에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baba9-195">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
