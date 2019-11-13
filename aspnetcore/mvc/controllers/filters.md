---
title: ASP.NET Core에서 필터링
author: ardalis
description: 필터 작동 방법 및 ASP.NET Core에서 사용하는 방법을 자세히 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: mvc/controllers/filters
ms.openlocfilehash: 6a83b8e85b68a9b8796aeed2fd39108dbeed3266
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190529"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="68dfb-103">ASP.NET Core에서 필터링</span><span class="sxs-lookup"><span data-stu-id="68dfb-103">Filters in ASP.NET Core</span></span>

<span data-ttu-id="68dfb-104">작성자: [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="68dfb-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="68dfb-105">ASP.NET Core에서 *필터*를 사용하면 요청 처리 파이프라인의 특정 단계 전후에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="68dfb-106">기본 제공 필터는 다음과 같은 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="68dfb-107">권한 부여(사용자가 권한이 없는 리소스에 액세스하는 것을 방지).</span><span class="sxs-lookup"><span data-stu-id="68dfb-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="68dfb-108">응답 캐싱(요청 파이프라인을 단락하여 캐시된 응답 반환).</span><span class="sxs-lookup"><span data-stu-id="68dfb-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="68dfb-109">사용자 지정 필터를 만들어 횡단 관심사를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="68dfb-110">횡단 관심사의 사례로는 오류 처리, 캐싱, 구성, 권한 부여 및 로깅을 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="68dfb-111">필터는 코드 중복을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="68dfb-112">예를 들어 오류 처리 예외 필터는 오류 처리를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="68dfb-113">이 문서는 Razor Pages, API 컨트롤러 및 보기를 사용하는 컨트롤러에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="68dfb-114">[예제 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68dfb-114">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="68dfb-115">필터 작동 방법</span><span class="sxs-lookup"><span data-stu-id="68dfb-115">How filters work</span></span>

<span data-ttu-id="68dfb-116">필터는 ‘필터 파이프라인’이라고도 하는 ‘ASP.NET Core 동작 호출 파이프라인’내에서 실행됩니다.  </span><span class="sxs-lookup"><span data-stu-id="68dfb-116">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="68dfb-117">필터 파이프라인은 ASP.NET Core가 실행할 작업을 선택한 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-117">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![요청은 기타 미들웨어, 라우팅 미들웨어, 작업 선택 및 ASP.NET Core 작업 호출 파이프라인을 통해서 처리됩니다.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="68dfb-120">필터 형식</span><span class="sxs-lookup"><span data-stu-id="68dfb-120">Filter types</span></span>

<span data-ttu-id="68dfb-121">각 필터 형식은 필터 파이프라인의 다른 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-121">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="68dfb-122">[권한 부여 필터](#authorization-filters)는 가장 먼저 실행되고 사용자가 요청에 대한 권한이 있는지 여부를 결정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-122">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="68dfb-123">권한 부여 필터는 요청이 인증되지 않는 경우 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-123">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="68dfb-124">[리소스 필터](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="68dfb-124">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="68dfb-125">권한 부여 후 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-125">Run after authorization.</span></span>  
  * <span data-ttu-id="68dfb-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>는 나머지 필터 파이프라인보다 먼저 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="68dfb-127">예를 들어 `OnResourceExecuting`는 모델 바인딩 전에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-127">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="68dfb-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>는 파이프라인의 나머지 부분이 완료된 후에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="68dfb-129">[작업 필터](#action-filters)는 개별 작업 메서드가 호출되는 전후에 즉시 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-129">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="68dfb-130">작업에 전달된 인수 및 작업에서 반환된 결과를 조작하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-130">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="68dfb-131">Razor Pages에서는 작업 필터가 지원되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="68dfb-131">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="68dfb-132">[예외 필터](#exception-filters)는 응답 본문에 무언가 쓰여지기 전에 발생한 처리되지 않은 예외에 대한 전역 정책을 적용하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-132">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="68dfb-133">[결과 필터](#result-filters)는 개별 작업 결과의 실행 전후에 즉시 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-133">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="68dfb-134">작업 메서드가 성공적으로 실행되는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-134">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="68dfb-135">보기 또는 포맷터 실행을 둘러싸야 하는 논리에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-135">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="68dfb-136">다음 다이어그램은 필터 형식이 필터 파이프라인에서 상호 작용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-136">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![요청은 권한 부여 필터, 리소스 필터, 모델 바인딩, 작업 필터, 작업 실행 및 작업 결과 변환, 예외 필터, 결과 필터 및 결과 실행을 통해 처리됩니다.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="68dfb-139">구현</span><span class="sxs-lookup"><span data-stu-id="68dfb-139">Implementation</span></span>

<span data-ttu-id="68dfb-140">필터는 서로 다른 인터페이스 정의를 통해 동기 및 비동기 구현을 모두 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-140">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="68dfb-141">동기 필터는 해당 파이프라인 단계 전(`On-Stage-Executing`)과 후(`On-Stage-Executed`)에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-141">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="68dfb-142">예를 들어 `OnActionExecuting`는 작업 메서드가 호출되기 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-142">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="68dfb-143">`OnActionExecuted`는 작업 메서드가 반환된 후 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-143">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="68dfb-144">비동기 필터는 `On-Stage-ExecutionAsync` 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-144">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="68dfb-145">이전 코드에서 `SampleAsyncActionFilter`에는 작업 메서드를 실행하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>(`next`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-145">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="68dfb-146">각 `On-Stage-ExecutionAsync` 메서드는 필터의 파이프라인 단계를 실행하는 `FilterType-ExecutionDelegate`를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-146">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="68dfb-147">여러 필터 단계</span><span class="sxs-lookup"><span data-stu-id="68dfb-147">Multiple filter stages</span></span>

<span data-ttu-id="68dfb-148">단일 클래스에서 여러 필터 단계에 대한 인터페이스를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-148">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="68dfb-149">예를 들어 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 클래스는 `IActionFilter`, `IResultFilter` 및 해당 비동기 항목을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-149">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="68dfb-150">필터 인터페이스의 동기 또는 비동기 버전을 모두 구현하지 **말고** 그 중 **한 가지**만 구현하세요.</span><span class="sxs-lookup"><span data-stu-id="68dfb-150">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="68dfb-151">런타임은 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-151">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="68dfb-152">그렇지 않으면 동기 인터페이스의 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-152">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="68dfb-153">비동기 및 동기 인터페이스가 모두 하나의 클래스에 구현된 경우에는 비동기 메서드만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-153">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="68dfb-154"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 같은 추상 클래스를 사용하는 경우 각 필터 형식에 대한 동기 메서드 또는 비동기 메서드만 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-154">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="68dfb-155">기본 제공 필터 특성</span><span class="sxs-lookup"><span data-stu-id="68dfb-155">Built-in filter attributes</span></span>

<span data-ttu-id="68dfb-156">ASP.NET Core에는 서브클래싱 및 사용자 지정할 수 있는 기본 제공 특성 기반 필터가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-156">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="68dfb-157">예를 들어 다음 결과 필터는 응답에 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-157">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="68dfb-158">특성을 사용하면 이전 예제와 같이 필터에서 인수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-158">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="68dfb-159">컨트롤러나 작업 메서드에 `AddHeaderAttribute`를 적용하고 HTTP 헤더의 이름 및 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-159">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="68dfb-160">여러 필터 인터페이스는 사용자 지정 구현을 위한 기본 클래스로 사용할 수 있는 상응하는 특성을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-160">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="68dfb-161">필터 특성:</span><span class="sxs-lookup"><span data-stu-id="68dfb-161">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="68dfb-162">필터 범위 및 실행 순서</span><span class="sxs-lookup"><span data-stu-id="68dfb-162">Filter scopes and order of execution</span></span>

<span data-ttu-id="68dfb-163">세 가지 *범위* 중 하나에서 필터를 파이프라인에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-163">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="68dfb-164">작업에서 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="68dfb-164">Using an attribute on an action.</span></span>
* <span data-ttu-id="68dfb-165">컨트롤러에서 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="68dfb-165">Using an attribute on a controller.</span></span>
* <span data-ttu-id="68dfb-166">다음 코드와 같이 모든 컨트롤러와 작업에 전역으로 사용:</span><span class="sxs-lookup"><span data-stu-id="68dfb-166">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="68dfb-167">이전 코드는 [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) 컬렉션을 사용하여 3개의 필터를 전역적으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-167">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="68dfb-168">기본 실행 순서</span><span class="sxs-lookup"><span data-stu-id="68dfb-168">Default order of execution</span></span>

<span data-ttu-id="68dfb-169">파이프라인의 특정 단계에 여러 개의 필터가 있는 경우 범위가 기본 필터 실행 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-169">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="68dfb-170">전역 필터는 클래스 필터를 둘러싸고 클래스 필터는 다시 메서드 필터를 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-170">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="68dfb-171">필터 중첩의 결과로 필터의 *after* 코드는 *before* 코드의 역순으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-171">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="68dfb-172">필터의 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-172">The filter sequence:</span></span>

* <span data-ttu-id="68dfb-173">전역 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="68dfb-173">The *before* code of global filters.</span></span>
  * <span data-ttu-id="68dfb-174">컨트롤러 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="68dfb-174">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="68dfb-175">작업 메서드 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="68dfb-175">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="68dfb-176">작업 메서드 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="68dfb-176">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="68dfb-177">컨트롤러 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="68dfb-177">The *after* code of controller filters.</span></span>
* <span data-ttu-id="68dfb-178">전역 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="68dfb-178">The *after* code of global filters.</span></span>
  
<span data-ttu-id="68dfb-179">다음 예제는 필터 메서드가 동기 작업 필터에 대해 호출되는 순서를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-179">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="68dfb-180">순서</span><span class="sxs-lookup"><span data-stu-id="68dfb-180">Sequence</span></span> | <span data-ttu-id="68dfb-181">필터 범위</span><span class="sxs-lookup"><span data-stu-id="68dfb-181">Filter scope</span></span> | <span data-ttu-id="68dfb-182">필터 메서드</span><span class="sxs-lookup"><span data-stu-id="68dfb-182">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="68dfb-183">1</span><span class="sxs-lookup"><span data-stu-id="68dfb-183">1</span></span> | <span data-ttu-id="68dfb-184">전역</span><span class="sxs-lookup"><span data-stu-id="68dfb-184">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="68dfb-185">2</span><span class="sxs-lookup"><span data-stu-id="68dfb-185">2</span></span> | <span data-ttu-id="68dfb-186">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="68dfb-186">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="68dfb-187">3</span><span class="sxs-lookup"><span data-stu-id="68dfb-187">3</span></span> | <span data-ttu-id="68dfb-188">메서드</span><span class="sxs-lookup"><span data-stu-id="68dfb-188">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="68dfb-189">4</span><span class="sxs-lookup"><span data-stu-id="68dfb-189">4</span></span> | <span data-ttu-id="68dfb-190">메서드</span><span class="sxs-lookup"><span data-stu-id="68dfb-190">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="68dfb-191">5</span><span class="sxs-lookup"><span data-stu-id="68dfb-191">5</span></span> | <span data-ttu-id="68dfb-192">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="68dfb-192">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="68dfb-193">6</span><span class="sxs-lookup"><span data-stu-id="68dfb-193">6</span></span> | <span data-ttu-id="68dfb-194">전역</span><span class="sxs-lookup"><span data-stu-id="68dfb-194">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="68dfb-195">이 순서는 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-195">This sequence shows:</span></span>

* <span data-ttu-id="68dfb-196">메서드 필터는 컨트롤러 필터 내에서 중첩됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-196">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="68dfb-197">컨트롤러 필터는 전역 필터 내에서 중첩됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-197">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="68dfb-198">컨트롤러 및 Razor Page 수준 필터</span><span class="sxs-lookup"><span data-stu-id="68dfb-198">Controller and Razor Page level filters</span></span>

<span data-ttu-id="68dfb-199"><xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스에서 상속되는 모든 컨트롤러에는 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 및 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 메서드가 포함됩니다. 이러한 메서드는:</span><span class="sxs-lookup"><span data-stu-id="68dfb-199">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="68dfb-200">다음 메서드는</span><span class="sxs-lookup"><span data-stu-id="68dfb-200">These methods:</span></span>

* <span data-ttu-id="68dfb-201">지정된 작업을 위해 실행되는 필터를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-201">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="68dfb-202">`OnActionExecuting`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-202">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="68dfb-203">`OnActionExecuted`는 모든 작업 필터 이후에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-203">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="68dfb-204">`OnActionExecutionAsync`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-204">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="68dfb-205">작업 메서드 이후 `next`가 실행된 후의 필터 코드.</span><span class="sxs-lookup"><span data-stu-id="68dfb-205">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="68dfb-206">예를 들어 다운로드 예제에서 `MySampleActionFilter`은 시작할 때 전역적으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-206">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="68dfb-207">`TestController`는:</span><span class="sxs-lookup"><span data-stu-id="68dfb-207">The `TestController`:</span></span>

* <span data-ttu-id="68dfb-208">`SampleActionFilterAttribute`(`[SampleActionFilter]`)를 `FilterTest2` 작업에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-208">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="68dfb-209">`OnActionExecuting` 및 `OnActionExecuted`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-209">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="68dfb-210">`https://localhost:5001/Test/FilterTest2`로 이동하면 다음 코드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-210">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="68dfb-211">Razor Pages에 대해서는 [필터 메서드를 재정의하여 Razor 페이지 필터 구현](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="68dfb-211">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="68dfb-212">기본 순서 재정의</span><span class="sxs-lookup"><span data-stu-id="68dfb-212">Overriding the default order</span></span>

<span data-ttu-id="68dfb-213"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>를 구현하여 실행의 기본 순서를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-213">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="68dfb-214">`IOrderedFilter`은 실행 순서를 결정하는 데 범위보다 우선 순위가 높은 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-214">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="68dfb-215">낮은 `Order` 값을 가진 필터는:</span><span class="sxs-lookup"><span data-stu-id="68dfb-215">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="68dfb-216">더 높은 `Order` 값을 가진 필터 이전에 *before* 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-216">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="68dfb-217">더 높은 `Order` 값을 가진 필터 이후에 *after* 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-217">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="68dfb-218">생성자 매개 변수로 `Order` 속성을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-218">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="68dfb-219">이전 예제와 동일한 3개의 작업 필터를 가정해보세요.</span><span class="sxs-lookup"><span data-stu-id="68dfb-219">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="68dfb-220">컨트롤러와 전역 필터의 `Order` 속성을 각각 1과 2로 설정하면 실행 순서가 반대가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-220">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="68dfb-221">순서</span><span class="sxs-lookup"><span data-stu-id="68dfb-221">Sequence</span></span> | <span data-ttu-id="68dfb-222">필터 범위</span><span class="sxs-lookup"><span data-stu-id="68dfb-222">Filter scope</span></span> | <span data-ttu-id="68dfb-223">`Order` 속성</span><span class="sxs-lookup"><span data-stu-id="68dfb-223">`Order` property</span></span> | <span data-ttu-id="68dfb-224">필터 메서드</span><span class="sxs-lookup"><span data-stu-id="68dfb-224">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="68dfb-225">1</span><span class="sxs-lookup"><span data-stu-id="68dfb-225">1</span></span> | <span data-ttu-id="68dfb-226">메서드</span><span class="sxs-lookup"><span data-stu-id="68dfb-226">Method</span></span> | <span data-ttu-id="68dfb-227">0</span><span class="sxs-lookup"><span data-stu-id="68dfb-227">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="68dfb-228">2</span><span class="sxs-lookup"><span data-stu-id="68dfb-228">2</span></span> | <span data-ttu-id="68dfb-229">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="68dfb-229">Controller</span></span> | <span data-ttu-id="68dfb-230">1</span><span class="sxs-lookup"><span data-stu-id="68dfb-230">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="68dfb-231">3</span><span class="sxs-lookup"><span data-stu-id="68dfb-231">3</span></span> | <span data-ttu-id="68dfb-232">전역</span><span class="sxs-lookup"><span data-stu-id="68dfb-232">Global</span></span> | <span data-ttu-id="68dfb-233">2</span><span class="sxs-lookup"><span data-stu-id="68dfb-233">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="68dfb-234">4</span><span class="sxs-lookup"><span data-stu-id="68dfb-234">4</span></span> | <span data-ttu-id="68dfb-235">전역</span><span class="sxs-lookup"><span data-stu-id="68dfb-235">Global</span></span> | <span data-ttu-id="68dfb-236">2</span><span class="sxs-lookup"><span data-stu-id="68dfb-236">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="68dfb-237">5</span><span class="sxs-lookup"><span data-stu-id="68dfb-237">5</span></span> | <span data-ttu-id="68dfb-238">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="68dfb-238">Controller</span></span> | <span data-ttu-id="68dfb-239">1</span><span class="sxs-lookup"><span data-stu-id="68dfb-239">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="68dfb-240">6</span><span class="sxs-lookup"><span data-stu-id="68dfb-240">6</span></span> | <span data-ttu-id="68dfb-241">메서드</span><span class="sxs-lookup"><span data-stu-id="68dfb-241">Method</span></span> | <span data-ttu-id="68dfb-242">0</span><span class="sxs-lookup"><span data-stu-id="68dfb-242">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="68dfb-243">`Order` 속성은 필터가 실행되는 순서를 결정할 때 범위를 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-243">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="68dfb-244">필터는 먼저 순서에 따라 정렬된 다음, 순서가 동일할 경우 범위가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-244">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="68dfb-245">모든 기본 제공 필터는 `IOrderedFilter`을 구현하고 기본 `Order` 값을 0으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-245">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="68dfb-246">기본 제공 필터의 경우 `Order`를 0이 아닌 값으로 설정하지 않는 한 범위가 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-246">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="68dfb-247">취소 및 단락</span><span class="sxs-lookup"><span data-stu-id="68dfb-247">Cancellation and short-circuiting</span></span>

<span data-ttu-id="68dfb-248">필터 메서드에 제공되는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 매개 변수의 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 속성을 설정하여 필터 파이프라인을 단락시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-248">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="68dfb-249">예를 들어 다음 리소스 필터는 파이프라인의 나머지 부분이 실행되지 않도록 막습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-249">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="68dfb-250">다음 코드에서 `ShortCircuitingResourceFilter` 및 `AddHeader` 필터는 `SomeResource` 작업 메서드를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-250">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="68dfb-251">`ShortCircuitingResourceFilter`는:</span><span class="sxs-lookup"><span data-stu-id="68dfb-251">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="68dfb-252">리소스 필터이고 `AddHeader`는 작업 필터이기 때문에 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-252">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="68dfb-253">나머지 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-253">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="68dfb-254">따라서 `AddHeader` 필터는 `SomeResource` 작업에 대해 절대 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-254">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="68dfb-255">이 동작은 `ShortCircuitingResourceFilter`가 먼저 실행되기 때문에 작업 메서드 수준에서 두 필터를 적용하더라도 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-255">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="68dfb-256">해당 필터 형식 또는 `Order` 속성의 명시적 사용으로 인해 `ShortCircuitingResourceFilter`가 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-256">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="68dfb-257">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="68dfb-257">Dependency injection</span></span>

<span data-ttu-id="68dfb-258">형식별 또는 인스턴스별 필터를 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-258">Filters can be added by type or by instance.</span></span> <span data-ttu-id="68dfb-259">인스턴스가 추가되면 모든 요청에 해당 인스턴스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-259">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="68dfb-260">형식이 추가되면 해당 필터는 형식으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-260">If a type is added, it's type-activated.</span></span> <span data-ttu-id="68dfb-261">형식으로 활성화된 필터는 다음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-261">A type-activated filter means:</span></span>

* <span data-ttu-id="68dfb-262">각 요청에 대해 인스턴스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-262">An instance is created for each request.</span></span>
* <span data-ttu-id="68dfb-263">모든 생성자 종속성이 DI([종속성 주입](xref:fundamentals/dependency-injection))를 통해서 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-263">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="68dfb-264">특성으로 구현되고 컨트롤러 클래스 또는 작업 메서드에 직접 추가된 필터는 DI([종속성 주입](xref:fundamentals/dependency-injection))에서 제공하는 생성자 종속성을 가질 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-264">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="68dfb-265">DI는 다음과 같은 이유로 생성자 종속성을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-265">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="68dfb-266">특성이 적용될 때 해당 생성자 매개 변수가 제공되어야 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-266">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="68dfb-267">특성이 작동하는 방법의 제한 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-267">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="68dfb-268">다음 필터는 DI에서 제공하는 생성자 종속성을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-268">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="68dfb-269">특성에서 구현된 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="68dfb-269"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="68dfb-270">위의 필터는 컨트롤러 또는 작업 메서드에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-270">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="68dfb-271">로거는 DI를 통해서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-271">Loggers are available from DI.</span></span> <span data-ttu-id="68dfb-272">그러나 로깅 용도로만 필터를 만들거나 사용하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="68dfb-272">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="68dfb-273">[기본 제공 프레임워크 로깅](xref:fundamentals/logging/index)은 일반적으로 로깅에 필요한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-273">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="68dfb-274">필터에 추가된 로깅은:</span><span class="sxs-lookup"><span data-stu-id="68dfb-274">Logging added to filters:</span></span>

* <span data-ttu-id="68dfb-275">비즈니스 도메인 문제 또는 필터 고유의 동작에 중점을 두어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-275">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="68dfb-276">작업 또는 다른 프레임워크 이벤트를 로깅해서는 **안 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="68dfb-276">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="68dfb-277">기본 제공 필터는 작업 및 프레임워크 이벤트를 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-277">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="68dfb-278">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="68dfb-278">ServiceFilterAttribute</span></span>

<span data-ttu-id="68dfb-279">서비스 필터 구현 형식은 `ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-279">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="68dfb-280"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 DI에서 필터의 인스턴스를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-280">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="68dfb-281">다음 코드는 `AddHeaderResultServiceFilter`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-281">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="68dfb-282">다음 코드에서 `AddHeaderResultServiceFilter`는 DI 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-282">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="68dfb-283">다음 코드에서 `ServiceFilter` 특성은 DI에서 `AddHeaderResultServiceFilter` 필터의 인스턴스를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-283">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="68dfb-284">`ServiceFilterAttribute`를 사용할 때 [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="68dfb-284">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="68dfb-285">필터 인스턴스가 원래 생성된 요청 범위 외부에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-285">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="68dfb-286">ASP.NET Core 런타임은 다음을 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-286">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="68dfb-287">필터의 단일 인스턴스 생성.</span><span class="sxs-lookup"><span data-stu-id="68dfb-287">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="68dfb-288">나중에 DI 컨테이너에서 필터가 다시 요청되지 않음.</span><span class="sxs-lookup"><span data-stu-id="68dfb-288">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="68dfb-289">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="68dfb-289">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="68dfb-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="68dfb-291">`IFilterFactory`는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-291">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="68dfb-292">`CreateInstance`는 DI에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-292">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="68dfb-293">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="68dfb-293">TypeFilterAttribute</span></span>

<span data-ttu-id="68dfb-294"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>와 비슷하지만 해당 형식은 DI 컨테이너에서 직접 해결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-294"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="68dfb-295"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>를 사용하여 형식을 인스턴스화합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-295">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="68dfb-296">`TypeFilterAttribute` 형식은 DI 컨테이너에서 직접 해결되지 않기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-296">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="68dfb-297">`TypeFilterAttribute`을 사용하여 참조되는 형식은 DI 컨테이너를 사용하여 등록할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-297">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="68dfb-298">DI 컨테이너에서 충족하는 종속성을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-298">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="68dfb-299">`TypeFilterAttribute`는 형식에 대한 생성자 인수를 필요에 따라 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-299">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="68dfb-300">`TypeFilterAttribute`를 사용할 때 [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="68dfb-300">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="68dfb-301">필터 인스턴스가 원래 생성된 요청 범위 밖에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-301">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="68dfb-302">ASP.NET Core 런타임은 단일 필터 인스턴스가 생성되도록 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-302">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="68dfb-303">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="68dfb-303">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="68dfb-304">다음 예제는 `TypeFilterAttribute`를 사용하여 형식에 인수를 전달하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-304">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="68dfb-305">권한 부여 필터</span><span class="sxs-lookup"><span data-stu-id="68dfb-305">Authorization filters</span></span>

<span data-ttu-id="68dfb-306">권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="68dfb-306">Authorization filters:</span></span>

* <span data-ttu-id="68dfb-307">필터 파이프라인에서 첫 번째로 실행되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-307">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="68dfb-308">작업 메서드에 대한 액세스를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-308">Control access to action methods.</span></span>
* <span data-ttu-id="68dfb-309">before 메서드는 있지만 after 메서드는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-309">Have a before method, but no after method.</span></span>

<span data-ttu-id="68dfb-310">사용자 지정 권한 부여 필터는 사용자 지정 권한 부여 프레임워크를 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-310">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="68dfb-311">사용자 지정 필터를 작성하는 대신 권한 부여 정책을 구성하거나 사용자 지정 권한 부여 정책을 작성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-311">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="68dfb-312">기본 제공 권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="68dfb-312">The built-in authorization filter:</span></span>

* <span data-ttu-id="68dfb-313">권한 부여 시스템을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-313">Calls the authorization system.</span></span>
* <span data-ttu-id="68dfb-314">요청을 승인하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-314">Does not authorize requests.</span></span>

<span data-ttu-id="68dfb-315">권한 부여 필터 내에서 예외를 던지지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="68dfb-315">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="68dfb-316">해당 예외는 처리되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-316">The exception will not be handled.</span></span>
* <span data-ttu-id="68dfb-317">예외 필터가 해당 예외를 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-317">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="68dfb-318">권한 부여 필터에서 예외가 발생할 경우 챌린지 발행을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="68dfb-318">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="68dfb-319">[권한 부여](xref:security/authorization/introduction)에 대해 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-319">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="68dfb-320">리소스 필터</span><span class="sxs-lookup"><span data-stu-id="68dfb-320">Resource filters</span></span>

<span data-ttu-id="68dfb-321">리소스 필터는:</span><span class="sxs-lookup"><span data-stu-id="68dfb-321">Resource filters:</span></span>

* <span data-ttu-id="68dfb-322"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-322">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="68dfb-323">실행이 필터 파이프라인 대부분을 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-323">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="68dfb-324">[권한 부여 필터](#authorization-filters)만 리소스 필터 이전에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-324">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="68dfb-325">리소스 필터는 파이프라인의 대부분을 단락시켜야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-325">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="68dfb-326">예를 들어 캐싱 필터는 캐시 적중 시 파이프라인의 나머지 부분을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-326">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="68dfb-327">리소스 필터 예제:</span><span class="sxs-lookup"><span data-stu-id="68dfb-327">Resource filter examples:</span></span>

* <span data-ttu-id="68dfb-328">이전에 살펴본 [단락 리소스 필터](#short-circuiting-resource-filter).</span><span class="sxs-lookup"><span data-stu-id="68dfb-328">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="68dfb-329">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="68dfb-329">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="68dfb-330">모델 바인딩이 양식 데이터에 액세스하는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-330">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="68dfb-331">대용량 파일 업로드에서 양식 데이터를 메모리로 읽어들이는 것을 방지하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-331">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="68dfb-332">작업 필터</span><span class="sxs-lookup"><span data-stu-id="68dfb-332">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="68dfb-333">작업 필터는 Razor Pages에 **적용되지 않습니다**.</span><span class="sxs-lookup"><span data-stu-id="68dfb-333">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="68dfb-334">Razor Pages는 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-334">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="68dfb-335">자세한 내용은 [Razor 페이지에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="68dfb-335">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="68dfb-336">작업 필터는:</span><span class="sxs-lookup"><span data-stu-id="68dfb-336">Action filters:</span></span>

* <span data-ttu-id="68dfb-337"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-337">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="68dfb-338">실행이 작업 메서드의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-338">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="68dfb-339">다음 코드는 예제 작업 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-339">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="68dfb-340"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>는 다음과 같은 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-340">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="68dfb-341"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 작업 메서드에 대한 입력을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-341"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="68dfb-342"><xref:Microsoft.AspNetCore.Mvc.Controller> - 컨트롤러 인스턴스를 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-342"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="68dfb-343"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` 설정은 작업 메서드 및 후속 작업 필터의 실행을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-343"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="68dfb-344">작업 메서드에서 예외를 던지면:</span><span class="sxs-lookup"><span data-stu-id="68dfb-344">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="68dfb-345">후속 필터의 실행을 막습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-345">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="68dfb-346">`Result` 설정과 달리 성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-346">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="68dfb-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>는 `Controller` 및 `Result`에 더하여 다음과 같은 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="68dfb-348"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 작업 실행이 다른 필터에 의해 단락된 경우 true입니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-348"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="68dfb-349"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 작업 또는 이전에 실행된 작업 필터에서 예외가 던져진 경우 null이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-349"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="68dfb-350">이 속성을 null로 설정하면:</span><span class="sxs-lookup"><span data-stu-id="68dfb-350">Setting this property to null:</span></span>

  * <span data-ttu-id="68dfb-351">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-351">Effectively handles the exception.</span></span>
  * <span data-ttu-id="68dfb-352">`Result`는 작업 메서드에서 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-352">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="68dfb-353">`IAsyncActionFilter`의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>에 대한 호출은:</span><span class="sxs-lookup"><span data-stu-id="68dfb-353">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="68dfb-354">모든 후속 작업 필터 및 작업 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-354">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="68dfb-355">`ActionExecutedContext`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-355">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="68dfb-356">단락시키려면 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName>을 결과 인스턴스에 할당하고 `next`(`ActionExecutionDelegate`)를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-356">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="68dfb-357">프레임워크는 서브클래싱 할 수 있는 추상 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-357">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="68dfb-358">`OnActionExecuting` 작업 필터는 다음에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-358">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="68dfb-359">모델 상태의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-359">Validate model state.</span></span>
* <span data-ttu-id="68dfb-360">상태가 유효하지 않은 경우 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-360">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="68dfb-361">`OnActionExecuted` 메서드는 작업 메서드 이후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-361">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="68dfb-362">그리고 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 속성을 통해 작업 결과를 확인하고 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-362">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="68dfb-363"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>는 작업 실행이 다른 필터에 의해 단락된 경우 true로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-363"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="68dfb-364"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>은 작업 또는 후속 작업 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-364"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="68dfb-365">`Exception`을 null로 설정하면:</span><span class="sxs-lookup"><span data-stu-id="68dfb-365">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="68dfb-366">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-366">Effectively handles an exception.</span></span>
  * <span data-ttu-id="68dfb-367">`ActionExecutedContext.Result`는 작업 메서드에서 정상적으로 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-367">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="68dfb-368">예외 필터</span><span class="sxs-lookup"><span data-stu-id="68dfb-368">Exception filters</span></span>

<span data-ttu-id="68dfb-369">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="68dfb-369">Exception filters:</span></span>

* <span data-ttu-id="68dfb-370"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-370">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="68dfb-371">공통 오류 처리 정책을 구현하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-371">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="68dfb-372">다음 예제 예외 필터는 사용자 지정 오류 보기를 사용하여 앱을 개발 중인 경우에 발생하는 예외에 대한 세부 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-372">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="68dfb-373">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="68dfb-373">Exception filters:</span></span>

* <span data-ttu-id="68dfb-374">before 및 after 이벤트가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-374">Don't have before and after events.</span></span>
* <span data-ttu-id="68dfb-375"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-375">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="68dfb-376">Razor Page 또는 컨트롤러 생성, [모델 바인딩](xref:mvc/models/model-binding), 작업 필터 또는 작업 메서드에서 발생하는 처리되지 않은 예외를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-376">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="68dfb-377">리소스 필터, 결과 필터 또는 MVC 결과 실행에서 발생하는 예외를 잡지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="68dfb-377">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="68dfb-378">예외를 처리하려면 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 속성을 `true`로 설정하거나 응답을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-378">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="68dfb-379">그러면 예외가 전파되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-379">This stops propagation of the exception.</span></span> <span data-ttu-id="68dfb-380">예외 필터는 예외를 “성공”으로 변환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-380">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="68dfb-381">이는 작업 필터에서만 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-381">Only an action filter can do that.</span></span>

<span data-ttu-id="68dfb-382">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="68dfb-382">Exception filters:</span></span>

* <span data-ttu-id="68dfb-383">작업 내에서 발생하는 예외를 잡는 데 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-383">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="68dfb-384">오류 처리 미들웨어만큼 유연하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-384">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="68dfb-385">예외 처리의 경우 미들웨어를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-385">Prefer middleware for exception handling.</span></span> <span data-ttu-id="68dfb-386">어떤 작업 메서드가 호출되는지에 따라 오류 처리 방식이 ‘다른’ 경우에만 예외 필터를 사용합니다. </span><span class="sxs-lookup"><span data-stu-id="68dfb-386">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="68dfb-387">예를 들어 앱에는 API 엔드포인트 및 보기/HTML 모두에 대한 작업 메서드가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-387">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="68dfb-388">API 엔드포인트는 JSON으로 오류 정보를 반환할 수 있습니다. 반면 보기 기반 작업은 HTML로 오류 페이지를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-388">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="68dfb-389">결과 필터</span><span class="sxs-lookup"><span data-stu-id="68dfb-389">Result filters</span></span>

<span data-ttu-id="68dfb-390">결과 필터는:</span><span class="sxs-lookup"><span data-stu-id="68dfb-390">Result filters:</span></span>

* <span data-ttu-id="68dfb-391">다음 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-391">Implement an interface:</span></span>
  * <span data-ttu-id="68dfb-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="68dfb-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="68dfb-393"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="68dfb-393"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="68dfb-394">실행이 작업 결과의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-394">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="68dfb-395">IResultFilter 및 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="68dfb-395">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="68dfb-396">다음 코드는 HTTP 헤더를 추가하는 결과 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-396">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="68dfb-397">실행되는 결과의 종류는 작업에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-397">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="68dfb-398">보기를 반환하는 작업에는 실행 중인 <xref:Microsoft.AspNetCore.Mvc.ViewResult>의 일부로 모든 Razor 프로세스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-398">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="68dfb-399">API 메서드는 결과 실행의 일부로 어떤 직렬화를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-399">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="68dfb-400">[작업 결과](xref:mvc/controllers/actions)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="68dfb-400">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="68dfb-401">결과 필터는 작업 또는 작업 필터가 작업 결과를 생성하는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-401">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="68dfb-402">다음 경우에는 결과 필터가 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-402">Result filters are not executed when:</span></span>

* <span data-ttu-id="68dfb-403">권한 부여 필터 또는 리소스 필터가 파이프라인을 단락시킬 경우.</span><span class="sxs-lookup"><span data-stu-id="68dfb-403">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="68dfb-404">예외 필터가 작업 결과를 생성하여 예외를 처리하는 경우.</span><span class="sxs-lookup"><span data-stu-id="68dfb-404">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="68dfb-405"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 메서드는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName>을 `true`로 설정하여 작업 결과 및 후속 결과 필터를 단락시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-405">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="68dfb-406">단락시킬 경우 빈 응답을 생성하지 않도록 응답 개체에 작성하세요.</span><span class="sxs-lookup"><span data-stu-id="68dfb-406">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="68dfb-407">`IResultFilter.OnResultExecuting`에서 예외를 던지면:</span><span class="sxs-lookup"><span data-stu-id="68dfb-407">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="68dfb-408">작업 결과 및 후속 필터의 실행을 막습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-408">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="68dfb-409">성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-409">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="68dfb-410"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 메서드가 실행될 때에는 응답이 이미 클라이언트에 전송되었을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-410">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="68dfb-411">이미 클라이언트에 전송된 응답은 더이상 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-411">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="68dfb-412">`ResultExecutedContext.Canceled`는 작업 결과 실행이 다른 필터에 의해 단락된 경우 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-412">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="68dfb-413">`ResultExecutedContext.Exception`은 작업 결과 또는 후속 결과 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-413">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="68dfb-414">`Exception`을 null로 설정하면 효과적으로 예외를 처리하고 이후의 파이프라인에서 ASP.NET Core에 의해 예외가 다시 던져지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-414">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="68dfb-415">결과 필터에서 예외를 처리하는 경우 데이터를 응답에 쓸 수 있는 신뢰할 수 있는 방법이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-415">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="68dfb-416">작업 결과가 예외를 던질 때 헤더가 클라이언트에 플러시된 경우 오류 코드를 전송하기 위한 신뢰할 수 있는 메커니즘이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-416">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="68dfb-417"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate>의 `await next` 호출은 후속 결과 필터 및 작업 결과를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-417">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="68dfb-418">단락시키려면 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel)을 `true`로 설정하고 `ResultExecutionDelegate`를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="68dfb-418">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="68dfb-419">프레임워크는 서브클래싱 할 수 있는 추상 `ResultFilterAttribute`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-419">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="68dfb-420">이전에 표시된 [AddHeaderAttribute](#add-header-attribute) 클래스는 결과 필터 특성의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-420">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="68dfb-421">IAlwaysRunResultFilter 및 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="68dfb-421">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="68dfb-422"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 인터페이스는 모든 작업 결과에 대해 실행되는 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 구현을 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-422">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="68dfb-423">여기에는 다음에 의해 생성되는 작업 결과가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-423">This includes action results produced by:</span></span>

* <span data-ttu-id="68dfb-424">단락하는 권한 부여 필터 및 리소스 필터</span><span class="sxs-lookup"><span data-stu-id="68dfb-424">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="68dfb-425">예외 필터</span><span class="sxs-lookup"><span data-stu-id="68dfb-425">Exception filters.</span></span>

<span data-ttu-id="68dfb-426">예를 들어 다음 필터는 항상 실행되어 콘텐츠 협상이 실패할 경우 작업 결과(<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)를 *422 Unprocessable Entity* 상태 코드로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-426">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="68dfb-427">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="68dfb-427">IFilterFactory</span></span>

<span data-ttu-id="68dfb-428"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-428"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="68dfb-429">따라서 필터 파이프라인 어디에서나 `IFilterFactory` 인스턴스를 `IFilterMetadata` 인스턴스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-429">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="68dfb-430">런타임이 필터를 호출하려고 준비할 때 `IFilterFactory`로 캐스팅을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-430">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="68dfb-431">해당 캐스팅에 성공하면 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 호출하여 호출되는 `IFilterMetadata` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-431">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="68dfb-432">앱이 시작될 때 정확한 필터 파이프라인을 명시적으로 설정할 필요가 없으므로 유연한 디자인을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-432">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="68dfb-433">필터를 만드는 다른 방법으로 사용자 지정 특성 구현을 사용하여 `IFilterFactory`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-433">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="68dfb-434">[다운로드 예제](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)를 실행하여 이전 코드를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-434">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="68dfb-435">F12 개발자 도구를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-435">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="68dfb-436">`https://localhost:5001/Sample/HeaderWithFactory`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-436">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="68dfb-437">F12 개발자 도구는 예제 코드에 의해 추가된 다음 응답 헤더를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-437">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="68dfb-438">**author:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="68dfb-438">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="68dfb-439">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="68dfb-439">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="68dfb-440">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="68dfb-440">**internal:** `My header`</span></span>

<span data-ttu-id="68dfb-441">이전 코드는 **internal:** `My header` 응답 헤더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-441">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="68dfb-442">특성에서 구현된 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="68dfb-442">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="68dfb-443">`IFilterFactory`를 구현하는 필터는 다음과 같은 필터에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-443">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="68dfb-444">매개 변수 전달을 필요로 하지 않는 필터.</span><span class="sxs-lookup"><span data-stu-id="68dfb-444">Don't require passing parameters.</span></span>
* <span data-ttu-id="68dfb-445">DI에 의해 채워져야 할 생성자 종속성이 있는 필터.</span><span class="sxs-lookup"><span data-stu-id="68dfb-445">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="68dfb-446"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-446"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="68dfb-447">`IFilterFactory`은 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-447">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="68dfb-448">`CreateInstance`는 서비스 컨테이너(DI)에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-448">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="68dfb-449">다음 코드는 `[SampleActionFilter]`을 적용하는 세 가지 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-449">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="68dfb-450">이전 코드에서 `[SampleActionFilter]`로 메서드를 장식하는 것이 `SampleActionFilter`을 적용하는 데 선호되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-450">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="68dfb-451">필터 파이프라인에서 미들웨어 사용</span><span class="sxs-lookup"><span data-stu-id="68dfb-451">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="68dfb-452">리소스 필터는 파이프라인의 뒷부분에 제공되는 모든 실행을 둘러싼다는 점에서 [미들웨어](xref:fundamentals/middleware/index)처럼 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-452">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="68dfb-453">하지만 필터는 ASP.NET Core 런타임의 일부라는 점에서 미들웨어와 다릅니다. 즉, ASP.NET Core 컨텍스트 및 구문에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-453">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="68dfb-454">미들웨어를 필터로 사용하려면 필터 파이프라인에 삽입할 미들웨어를 지정하는 `Configure` 메서드를 포함한 형식을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-454">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="68dfb-455">다음 예제는 지역화 미들웨어를 사용하여 요청에 대한 현재 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-455">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="68dfb-456"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>를 사용하여 미들웨어를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-456">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="68dfb-457">미들웨어 필터는 모델 바인딩 이전 및 나머지 파이프라인 이후에 리소스 필터와 동일한 필터 파이프라인 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-457">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="68dfb-458">다음 작업</span><span class="sxs-lookup"><span data-stu-id="68dfb-458">Next actions</span></span>

* <span data-ttu-id="68dfb-459">[Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="68dfb-459">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="68dfb-460">필터를 실험하려면 [GitHub 예제를 다운로드하고, 테스트하고, 수정](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="68dfb-460">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>
