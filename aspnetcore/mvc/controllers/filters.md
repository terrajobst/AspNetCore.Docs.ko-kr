---
title: ASP.NET Core에서 필터링
author: Rick-Anderson
description: 필터 작동 방법 및 ASP.NET Core에서 사용하는 방법을 자세히 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
uid: mvc/controllers/filters
ms.openlocfilehash: 03335811766ea3a1455901199863c6da0e35f7e4
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653343"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="40f79-103">ASP.NET Core에서 필터링</span><span class="sxs-lookup"><span data-stu-id="40f79-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="40f79-104">작성자: [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="40f79-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="40f79-105">ASP.NET Core에서 *필터*를 사용하면 요청 처리 파이프라인의 특정 단계 전후에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="40f79-106">기본 제공 필터는 다음과 같은 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="40f79-107">권한 부여(사용자가 권한이 없는 리소스에 액세스하는 것을 방지).</span><span class="sxs-lookup"><span data-stu-id="40f79-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="40f79-108">응답 캐싱(요청 파이프라인을 단락하여 캐시된 응답 반환).</span><span class="sxs-lookup"><span data-stu-id="40f79-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="40f79-109">사용자 지정 필터를 만들어 횡단 관심사를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="40f79-110">횡단 관심사의 사례로는 오류 처리, 캐싱, 구성, 권한 부여 및 로깅을 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="40f79-111">필터는 코드 중복을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="40f79-112">예를 들어 오류 처리 예외 필터는 오류 처리를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="40f79-113">이 문서는 Razor Pages, API 컨트롤러 및 보기를 사용하는 컨트롤러에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="40f79-114">필터는 작동 시 [Razor 구성 요소](xref:blazor/components)에 직접적인 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-114">Filters don't work directly with [Razor components](xref:blazor/components).</span></span> <span data-ttu-id="40f79-115">필터는 다음과 같은 경우에만 간접적으로 구성 요소에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="40f79-116">구성 요소가 페이지 또는 보기에 포함되어 있는 경우</span><span class="sxs-lookup"><span data-stu-id="40f79-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="40f79-117">페이지 또는 컨트롤러/보기에서 필터를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="40f79-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="40f79-118">[예제 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="40f79-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="40f79-119">필터 작동 방법</span><span class="sxs-lookup"><span data-stu-id="40f79-119">How filters work</span></span>

<span data-ttu-id="40f79-120">필터는 ‘필터 파이프라인’이라고도 하는 ‘ASP.NET Core 동작 호출 파이프라인’내에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="40f79-121">필터 파이프라인은 ASP.NET Core가 실행할 작업을 선택한 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![다른 미들웨어, 라우팅 미들웨어, 작업 선택 영역 및 작업 호출 파이프라인을 통해 요청이 처리됩니다.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="40f79-124">필터 형식</span><span class="sxs-lookup"><span data-stu-id="40f79-124">Filter types</span></span>

<span data-ttu-id="40f79-125">각 필터 형식은 필터 파이프라인의 다른 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="40f79-126">[권한 부여 필터](#authorization-filters)는 가장 먼저 실행되고 사용자가 요청에 대한 권한이 있는지 여부를 결정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="40f79-127">권한 부여 필터는 요청이 인증되지 않는 경우 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="40f79-128">[리소스 필터](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="40f79-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="40f79-129">권한 부여 후 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-129">Run after authorization.</span></span>  
  * <span data-ttu-id="40f79-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>는 나머지 필터 파이프라인보다 먼저 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="40f79-131">예를 들어 `OnResourceExecuting`는 모델 바인딩 전에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="40f79-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>는 파이프라인의 나머지 부분이 완료된 후에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="40f79-133">[작업 필터](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="40f79-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="40f79-134">작업 메서드가 호출되기 전후에 즉시 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="40f79-135">작업에 전달된 인수를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="40f79-136">작업에서 반환된 결과를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="40f79-137">Razor Pages에서는 지원되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="40f79-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="40f79-138">[예외 필터](#exception-filters)는 응답 본문이 쓰여지기 전에 발생한 처리되지 않은 예외에 대한 전역 정책을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="40f79-139">[결과 필터](#result-filters)는 작업 결과의 실행 전후에 즉시 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="40f79-140">작업 메서드가 성공적으로 실행되는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="40f79-141">보기 또는 포맷터 실행을 둘러싸야 하는 논리에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="40f79-142">다음 다이어그램은 필터 형식이 필터 파이프라인에서 상호 작용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![요청은 권한 부여 필터, 리소스 필터, 모델 바인딩, 작업 필터, 작업 실행 및 작업 결과 변환, 예외 필터, 결과 필터 및 결과 실행을 통해 처리됩니다.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="40f79-145">구현</span><span class="sxs-lookup"><span data-stu-id="40f79-145">Implementation</span></span>

<span data-ttu-id="40f79-146">필터는 서로 다른 인터페이스 정의를 통해 동기 및 비동기 구현을 모두 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="40f79-147">동기 필터는 해당 파이프라인 단계 전후에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="40f79-148">예를 들어 <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*>는 작업 메서드가 호출되기 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="40f79-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>는 작업 메서드가 반환된 후 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="40f79-150">비동기 필터는 `On-Stage-ExecutionAsync` 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="40f79-151">예 <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="40f79-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="40f79-152">이전 코드에서 `SampleAsyncActionFilter`에는 작업 메서드를 실행하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>(`next`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="40f79-153">여러 필터 단계</span><span class="sxs-lookup"><span data-stu-id="40f79-153">Multiple filter stages</span></span>

<span data-ttu-id="40f79-154">단일 클래스에서 여러 필터 단계에 대한 인터페이스를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="40f79-155">예를 들어 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="40f79-156">동기: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="40f79-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="40f79-157">비동기: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="40f79-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="40f79-158">필터 인터페이스의 동기 또는 비동기 버전을 모두 구현하지 **말고** 그 중 **한 가지**만 구현하세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="40f79-159">런타임은 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="40f79-160">그렇지 않으면 동기 인터페이스의 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="40f79-161">비동기 및 동기 인터페이스가 모두 하나의 클래스에 구현된 경우에는 비동기 메서드만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="40f79-162"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 같은 추상 클래스를 사용하는 경우 각 필터 형식에 대한 동기 메서드 또는 비동기 메서드만 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="40f79-163">기본 제공 필터 특성</span><span class="sxs-lookup"><span data-stu-id="40f79-163">Built-in filter attributes</span></span>

<span data-ttu-id="40f79-164">ASP.NET Core에는 서브클래싱 및 사용자 지정할 수 있는 기본 제공 특성 기반 필터가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="40f79-165">예를 들어 다음 결과 필터는 응답에 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="40f79-166">특성을 사용하면 이전 예제와 같이 필터에서 인수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="40f79-167">컨트롤러나 작업 메서드에 `AddHeaderAttribute`를 적용하고 HTTP 헤더의 이름 및 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="40f79-168">[브라우저 개발자 도구](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools)와 같은 도구를 사용하여 헤더를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="40f79-169">**응답 헤더**에 `author: Rick Anderson`이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="40f79-170">다음 코드는 다음과 같은 작업을 수행하는 `ActionFilterAttribute`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="40f79-171">구성 시스템에서 제목과 이름을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="40f79-172">앞의 샘플과 달리 다음 코드는 필터 매개 변수를 코드에 추가하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="40f79-173">응답 헤더에 제목과 이름을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="40f79-174">구성 옵션은 [옵션 패턴](xref:fundamentals/configuration/index)을 사용하여 [구성 시스템](xref:fundamentals/configuration/options)에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="40f79-175">예를 들어 *appsettings.json* 파일에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="40f79-176">`StartUp.ConfigureServices`에서 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="40f79-177">`PositionOptions` 클래스는 `"Position"` 구성 영역을 사용하여 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="40f79-178">`MyActionFilterAttribute`는 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="40f79-179">다음 코드에서는 `PositionOptions` 클래스를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="40f79-180">다음 코드는 `MyActionFilterAttribute`를 `Index2` 메서드에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="40f79-181">**엔드포인트가 호출될 때**응답 헤더`author: Rick Anderson`에 `Editor: Joe Smith` 및 `Sample/Index2`가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="40f79-182">다음 코드는 `MyActionFilterAttribute` 및 `AddHeaderAttribute`를 Razor 페이지에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="40f79-183">Razor 페이지 처리기 메서드에는 필터를 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="40f79-184">이러한 메서드는 Razor 페이지 모델 또는 전역적으로 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="40f79-185">여러 필터 인터페이스는 사용자 지정 구현을 위한 기본 클래스로 사용할 수 있는 상응하는 특성을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="40f79-186">필터 특성:</span><span class="sxs-lookup"><span data-stu-id="40f79-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="40f79-187">필터 범위 및 실행 순서</span><span class="sxs-lookup"><span data-stu-id="40f79-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="40f79-188">세 가지 *범위* 중 하나에서 필터를 파이프라인에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="40f79-189">컨트롤러 작업에서 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="40f79-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="40f79-190">필터 특성은 Razor Pages 처리기 메서드에 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="40f79-191">컨트롤러 또는 Razor 페이지에서 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="40f79-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="40f79-192">다음 코드와 같이 모든 컨트롤러, 작업 및 Razor Pages에 전역으로 사용:</span><span class="sxs-lookup"><span data-stu-id="40f79-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="40f79-193">기본 실행 순서</span><span class="sxs-lookup"><span data-stu-id="40f79-193">Default order of execution</span></span>

<span data-ttu-id="40f79-194">파이프라인의 특정 단계에 여러 개의 필터가 있는 경우 범위가 기본 필터 실행 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="40f79-195">전역 필터는 클래스 필터를 둘러싸고 클래스 필터는 다시 메서드 필터를 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="40f79-196">필터 중첩의 결과로 필터의 *after* 코드는 *before* 코드의 역순으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="40f79-197">필터의 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-197">The filter sequence:</span></span>

* <span data-ttu-id="40f79-198">전역 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="40f79-199">컨트롤러 및 Razor 페이지 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="40f79-200">작업 메서드 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="40f79-201">작업 메서드 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="40f79-202">컨트롤러 및 Razor 페이지 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="40f79-203">전역 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="40f79-204">다음 예제는 필터 메서드가 동기 작업 필터에 대해 호출되는 순서를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="40f79-205">시퀀스</span><span class="sxs-lookup"><span data-stu-id="40f79-205">Sequence</span></span> | <span data-ttu-id="40f79-206">필터 범위</span><span class="sxs-lookup"><span data-stu-id="40f79-206">Filter scope</span></span> | <span data-ttu-id="40f79-207">Filter 메서드</span><span class="sxs-lookup"><span data-stu-id="40f79-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="40f79-208">1</span><span class="sxs-lookup"><span data-stu-id="40f79-208">1</span></span> | <span data-ttu-id="40f79-209">Global</span><span class="sxs-lookup"><span data-stu-id="40f79-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="40f79-210">2</span><span class="sxs-lookup"><span data-stu-id="40f79-210">2</span></span> | <span data-ttu-id="40f79-211">컨트롤러 또는 Razor 페이지</span><span class="sxs-lookup"><span data-stu-id="40f79-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="40f79-212">3</span><span class="sxs-lookup"><span data-stu-id="40f79-212">3</span></span> | <span data-ttu-id="40f79-213">방법</span><span class="sxs-lookup"><span data-stu-id="40f79-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="40f79-214">4</span><span class="sxs-lookup"><span data-stu-id="40f79-214">4</span></span> | <span data-ttu-id="40f79-215">방법</span><span class="sxs-lookup"><span data-stu-id="40f79-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="40f79-216">5</span><span class="sxs-lookup"><span data-stu-id="40f79-216">5</span></span> | <span data-ttu-id="40f79-217">컨트롤러 또는 Razor 페이지</span><span class="sxs-lookup"><span data-stu-id="40f79-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="40f79-218">6</span><span class="sxs-lookup"><span data-stu-id="40f79-218">6</span></span> | <span data-ttu-id="40f79-219">Global</span><span class="sxs-lookup"><span data-stu-id="40f79-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="40f79-220">컨트롤러 수준 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-220">Controller level filters</span></span>

<span data-ttu-id="40f79-221"><xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스에서 상속되는 모든 컨트롤러에는 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 및 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 메서드가 포함됩니다. 이러한 메서드는:</span><span class="sxs-lookup"><span data-stu-id="40f79-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="40f79-222">다음 메서드는</span><span class="sxs-lookup"><span data-stu-id="40f79-222">These methods:</span></span>

* <span data-ttu-id="40f79-223">지정된 작업을 위해 실행되는 필터를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="40f79-224">`OnActionExecuting`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="40f79-225">`OnActionExecuted`는 모든 작업 필터 이후에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="40f79-226">`OnActionExecutionAsync`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="40f79-227">작업 메서드 이후 `next`가 실행된 후의 필터 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="40f79-228">예를 들어 다운로드 예제에서 `MySampleActionFilter`은 시작할 때 전역적으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="40f79-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="40f79-229">The `TestController`:</span></span>

* <span data-ttu-id="40f79-230">`SampleActionFilterAttribute`(`[SampleActionFilter]`)를 `FilterTest2` 작업에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="40f79-231">`OnActionExecuting` 및 `OnActionExecuted`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="40f79-232">`https://localhost:5001/Test2/FilterTest2`로 이동하면 다음 코드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="40f79-233">컨트롤러 수준 필터는 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 속성을 `int.MinValue`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="40f79-234">컨트롤러 수준 필터는 메서드에 적용된 후에 실행되도록 설정할 수 **없습니다**.</span><span class="sxs-lookup"><span data-stu-id="40f79-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="40f79-235">순서는 다음 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-235">Order is explained in the next section.</span></span>

<span data-ttu-id="40f79-236">Razor Pages에 대해서는 [필터 메서드를 재정의하여 Razor 페이지 필터 구현](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="40f79-237">기본 순서 재정의</span><span class="sxs-lookup"><span data-stu-id="40f79-237">Overriding the default order</span></span>

<span data-ttu-id="40f79-238"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>를 구현하여 실행의 기본 순서를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="40f79-239">`IOrderedFilter`은 실행 순서를 결정하는 데 범위보다 우선 순위가 높은 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="40f79-240">낮은 `Order` 값을 가진 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="40f79-241">더 높은 *값을 가진 필터 이전에*before`Order` 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="40f79-242">더 높은 *값을 가진 필터 이후에*after`Order` 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="40f79-243">`Order` 속성은 생성자 매개 변수를 사용하여 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="40f79-244">다음 컨트롤러의 두 작업 필터를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="40f79-245">전역 필터는 `StartUp.ConfigureServices`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="40f79-246">3개의 필터는 다음 순서로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="40f79-247">`Order` 속성은 필터가 실행되는 순서를 결정할 때 범위를 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="40f79-248">필터는 먼저 순서에 따라 정렬된 다음, 순서가 동일할 경우 범위가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="40f79-249">모든 기본 제공 필터는 `IOrderedFilter`을 구현하고 기본 `Order` 값을 0으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="40f79-250">앞서 언급했듯이 컨트롤러 수준 필터는 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 속성을 `int.MinValue`로 설정합니다. 기본 제공 필터의 경우 `Order`가 0이 아닌 값으로 설정되지 않은 한 범위가 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="40f79-251">이전 코드에서 `MySampleActionFilter`는 전역 범위를 가지므로 컨트롤러 범위를 포함하는 `MyAction2FilterAttribute` 전에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="40f79-252">`MyAction2FilterAttribute`를 먼저 실행하려면 순서를 `int.MinValue`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="40f79-253">전역 필터 `MySampleActionFilter`를 먼저 실행하려면 `Order`를 `int.MinValue`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="40f79-254">취소 및 단락</span><span class="sxs-lookup"><span data-stu-id="40f79-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="40f79-255">필터 메서드에 제공되는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 매개 변수의 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 속성을 설정하여 필터 파이프라인을 단락시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="40f79-256">예를 들어 다음 리소스 필터는 파이프라인의 나머지 부분이 실행되지 않도록 막습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="40f79-257">다음 코드에서 `ShortCircuitingResourceFilter` 및 `AddHeader` 필터는 `SomeResource` 작업 메서드를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="40f79-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="40f79-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="40f79-259">리소스 필터이고 `AddHeader`는 작업 필터이기 때문에 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="40f79-260">나머지 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="40f79-261">따라서 `AddHeader` 필터는 `SomeResource` 작업에 대해 절대 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="40f79-262">이 동작은 `ShortCircuitingResourceFilter`가 먼저 실행되기 때문에 작업 메서드 수준에서 두 필터를 적용하더라도 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="40f79-263">해당 필터 형식 또는 `ShortCircuitingResourceFilter` 속성의 명시적 사용으로 인해 `Order`가 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="40f79-264">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="40f79-264">Dependency injection</span></span>

<span data-ttu-id="40f79-265">형식별 또는 인스턴스별 필터를 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="40f79-266">인스턴스가 추가되면 모든 요청에 해당 인스턴스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="40f79-267">형식이 추가되면 해당 필터는 형식으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="40f79-268">형식으로 활성화된 필터는 다음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-268">A type-activated filter means:</span></span>

* <span data-ttu-id="40f79-269">각 요청에 대해 인스턴스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-269">An instance is created for each request.</span></span>
* <span data-ttu-id="40f79-270">모든 생성자 종속성이 DI([종속성 주입](xref:fundamentals/dependency-injection))를 통해서 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="40f79-271">특성으로 구현되고 컨트롤러 클래스 또는 작업 메서드에 직접 추가된 필터는 DI([종속성 주입](xref:fundamentals/dependency-injection))에서 제공하는 생성자 종속성을 가질 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="40f79-272">DI는 다음과 같은 이유로 생성자 종속성을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="40f79-273">특성이 적용될 때 해당 생성자 매개 변수가 제공되어야 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="40f79-274">특성이 작동하는 방법의 제한 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="40f79-275">다음 필터는 DI에서 제공하는 생성자 종속성을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="40f79-276">특성에서 구현된 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="40f79-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="40f79-277">위의 필터는 컨트롤러 또는 작업 메서드에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="40f79-278">로거는 DI를 통해서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-278">Loggers are available from DI.</span></span> <span data-ttu-id="40f79-279">그러나 로깅 용도로만 필터를 만들거나 사용하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="40f79-280">[기본 제공 프레임워크 로깅](xref:fundamentals/logging/index)은 일반적으로 로깅에 필요한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="40f79-281">필터에 추가된 로깅은:</span><span class="sxs-lookup"><span data-stu-id="40f79-281">Logging added to filters:</span></span>

* <span data-ttu-id="40f79-282">비즈니스 도메인 문제 또는 필터 고유의 동작에 중점을 두어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="40f79-283">작업 또는 다른 프레임워크 이벤트를 로깅해서는 **안 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="40f79-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="40f79-284">기본 제공 필터는 작업 및 프레임워크 이벤트를 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="40f79-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="40f79-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="40f79-286">서비스 필터 구현 형식은 `ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="40f79-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 DI에서 필터의 인스턴스를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="40f79-288">다음 코드는 `AddHeaderResultServiceFilter`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="40f79-289">다음 코드에서 `AddHeaderResultServiceFilter`는 DI 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="40f79-290">다음 코드에서 `ServiceFilter` 특성은 DI에서 `AddHeaderResultServiceFilter` 필터의 인스턴스를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="40f79-291">`ServiceFilterAttribute`를 사용할 때 [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="40f79-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="40f79-292">필터 인스턴스가 원래 생성된 요청 범위 외부에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="40f79-293">ASP.NET Core 런타임은 다음을 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="40f79-294">필터의 단일 인스턴스 생성.</span><span class="sxs-lookup"><span data-stu-id="40f79-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="40f79-295">나중에 DI 컨테이너에서 필터가 다시 요청되지 않음.</span><span class="sxs-lookup"><span data-stu-id="40f79-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="40f79-296">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="40f79-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="40f79-298">`IFilterFactory`는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="40f79-299">`CreateInstance`는 DI에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="40f79-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="40f79-300">TypeFilterAttribute</span></span>

<span data-ttu-id="40f79-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>와 비슷하지만 해당 형식은 DI 컨테이너에서 직접 해결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="40f79-302"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>를 사용하여 형식을 인스턴스화합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="40f79-303">`TypeFilterAttribute` 형식은 DI 컨테이너에서 직접 해결되지 않기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="40f79-304">`TypeFilterAttribute`을 사용하여 참조되는 형식은 DI 컨테이너를 사용하여 등록할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="40f79-305">DI 컨테이너에서 충족하는 종속성을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="40f79-306">`TypeFilterAttribute`는 형식에 대한 생성자 인수를 필요에 따라 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="40f79-307">`TypeFilterAttribute`를 사용할 때 [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="40f79-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="40f79-308">필터 인스턴스가 원래 생성된 요청 범위 밖에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="40f79-309">ASP.NET Core 런타임은 단일 필터 인스턴스가 생성되도록 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="40f79-310">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="40f79-311">다음 예제는 `TypeFilterAttribute`를 사용하여 형식에 인수를 전달하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="40f79-312">권한 부여 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-312">Authorization filters</span></span>

<span data-ttu-id="40f79-313">권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-313">Authorization filters:</span></span>

* <span data-ttu-id="40f79-314">필터 파이프라인에서 첫 번째로 실행되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="40f79-315">작업 메서드에 대한 액세스를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-315">Control access to action methods.</span></span>
* <span data-ttu-id="40f79-316">before 메서드는 있지만 after 메서드는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="40f79-317">사용자 지정 권한 부여 필터는 사용자 지정 권한 부여 프레임워크를 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="40f79-318">사용자 지정 필터를 작성하는 대신 권한 부여 정책을 구성하거나 사용자 지정 권한 부여 정책을 작성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="40f79-319">기본 제공 권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="40f79-320">권한 부여 시스템을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-320">Calls the authorization system.</span></span>
* <span data-ttu-id="40f79-321">요청을 승인하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-321">Does not authorize requests.</span></span>

<span data-ttu-id="40f79-322">권한 부여 필터 내에서 예외를 던지지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="40f79-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="40f79-323">해당 예외는 처리되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-323">The exception will not be handled.</span></span>
* <span data-ttu-id="40f79-324">예외 필터가 해당 예외를 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="40f79-325">권한 부여 필터에서 예외가 발생할 경우 챌린지 발행을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="40f79-326">[권한 부여](xref:security/authorization/introduction)에 대해 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="40f79-327">리소스 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-327">Resource filters</span></span>

<span data-ttu-id="40f79-328">리소스 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-328">Resource filters:</span></span>

* <span data-ttu-id="40f79-329"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="40f79-330">실행이 필터 파이프라인 대부분을 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="40f79-331">[권한 부여 필터](#authorization-filters)만 리소스 필터 이전에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="40f79-332">리소스 필터는 파이프라인의 대부분을 단락시켜야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="40f79-333">예를 들어 캐싱 필터는 캐시 적중 시 파이프라인의 나머지 부분을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="40f79-334">리소스 필터 예제:</span><span class="sxs-lookup"><span data-stu-id="40f79-334">Resource filter examples:</span></span>

* <span data-ttu-id="40f79-335">이전에 살펴본 [단락 리소스 필터](#short-circuiting-resource-filter).</span><span class="sxs-lookup"><span data-stu-id="40f79-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="40f79-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="40f79-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="40f79-337">모델 바인딩이 양식 데이터에 액세스하는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="40f79-338">대용량 파일 업로드에서 양식 데이터를 메모리로 읽어들이는 것을 방지하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="40f79-339">작업 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-339">Action filters</span></span>

<span data-ttu-id="40f79-340">작업 필터는 Razor Pages에 **적용되지 않습니다**.</span><span class="sxs-lookup"><span data-stu-id="40f79-340">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="40f79-341">Razor Pages는 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-341">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="40f79-342">자세한 내용은 [Razor 페이지에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="40f79-343">작업 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-343">Action filters:</span></span>

* <span data-ttu-id="40f79-344"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="40f79-345">실행이 작업 메서드의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="40f79-346">다음 코드는 예제 작업 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="40f79-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>는 다음과 같은 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="40f79-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 작업 메서드에 대한 입력을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="40f79-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - 컨트롤러 인스턴스를 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="40f79-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` 설정은 작업 메서드 및 후속 작업 필터의 실행을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="40f79-351">작업 메서드에서 예외를 던지면:</span><span class="sxs-lookup"><span data-stu-id="40f79-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="40f79-352">후속 필터의 실행을 막습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="40f79-353">`Result` 설정과 달리 성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="40f79-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>는 `Controller` 및 `Result`에 더하여 다음과 같은 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="40f79-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 작업 실행이 다른 필터에 의해 단락된 경우 true입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="40f79-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 작업 또는 이전에 실행된 작업 필터에서 예외가 던져진 경우 null이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="40f79-357">이 속성을 null로 설정하면:</span><span class="sxs-lookup"><span data-stu-id="40f79-357">Setting this property to null:</span></span>

  * <span data-ttu-id="40f79-358">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="40f79-359">`Result`는 작업 메서드에서 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="40f79-360">`IAsyncActionFilter`의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>에 대한 호출은:</span><span class="sxs-lookup"><span data-stu-id="40f79-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="40f79-361">모든 후속 작업 필터 및 작업 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="40f79-362">`ActionExecutedContext`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="40f79-363">단락시키려면 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName>을 결과 인스턴스에 할당하고 `next`(`ActionExecutionDelegate`)를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="40f79-364">프레임워크는 서브클래싱 할 수 있는 추상 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="40f79-365">`OnActionExecuting` 작업 필터는 다음에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="40f79-366">모델 상태의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-366">Validate model state.</span></span>
* <span data-ttu-id="40f79-367">상태가 유효하지 않은 경우 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="40f79-368">`OnActionExecuted` 메서드는 작업 메서드 이후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="40f79-369">그리고 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 속성을 통해 작업 결과를 확인하고 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="40f79-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>는 작업 실행이 다른 필터에 의해 단락된 경우 true로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="40f79-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>은 작업 또는 후속 작업 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="40f79-372">`Exception`을 null로 설정하면:</span><span class="sxs-lookup"><span data-stu-id="40f79-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="40f79-373">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="40f79-374">`ActionExecutedContext.Result`는 작업 메서드에서 정상적으로 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="40f79-375">예외 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-375">Exception filters</span></span>

<span data-ttu-id="40f79-376">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-376">Exception filters:</span></span>

* <span data-ttu-id="40f79-377"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="40f79-378">공통 오류 처리 정책을 구현하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="40f79-379">다음 예제 예외 필터는 사용자 지정 오류 보기를 사용하여 앱을 개발 중인 경우에 발생하는 예외에 대한 세부 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="40f79-380">다음 코드에서는 예외 필터를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="40f79-381">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-381">Exception filters:</span></span>

* <span data-ttu-id="40f79-382">before 및 after 이벤트가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-382">Don't have before and after events.</span></span>
* <span data-ttu-id="40f79-383"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="40f79-384">Razor Page 또는 컨트롤러 생성, [모델 바인딩](xref:mvc/models/model-binding), 작업 필터 또는 작업 메서드에서 발생하는 처리되지 않은 예외를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="40f79-385">리소스 필터, 결과 필터 또는 MVC 결과 실행에서 발생하는 예외를 잡지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="40f79-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="40f79-386">예외를 처리하려면 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 속성을 `true`로 설정하거나 응답을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="40f79-387">그러면 예외가 전파되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-387">This stops propagation of the exception.</span></span> <span data-ttu-id="40f79-388">예외 필터는 예외를 “성공”으로 변환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="40f79-389">이는 작업 필터에서만 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-389">Only an action filter can do that.</span></span>

<span data-ttu-id="40f79-390">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-390">Exception filters:</span></span>

* <span data-ttu-id="40f79-391">작업 내에서 발생하는 예외를 잡는 데 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="40f79-392">오류 처리 미들웨어만큼 유연하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="40f79-393">예외 처리의 경우 미들웨어를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="40f79-394">어떤 작업 메서드가 호출되는지에 따라 오류 처리 방식이 ‘다른’ 경우에만 예외 필터를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="40f79-395">예를 들어 앱에는 API 엔드포인트 및 보기/HTML 모두에 대한 작업 메서드가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="40f79-396">API 엔드포인트는 JSON으로 오류 정보를 반환할 수 있습니다. 반면 보기 기반 작업은 HTML로 오류 페이지를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="40f79-397">결과 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-397">Result filters</span></span>

<span data-ttu-id="40f79-398">결과 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-398">Result filters:</span></span>

* <span data-ttu-id="40f79-399">다음 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-399">Implement an interface:</span></span>
  * <span data-ttu-id="40f79-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="40f79-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="40f79-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="40f79-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="40f79-402">실행이 작업 결과의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="40f79-403">IResultFilter 및 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="40f79-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="40f79-404">다음 코드는 HTTP 헤더를 추가하는 결과 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="40f79-405">실행되는 결과의 종류는 작업에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="40f79-406">보기를 반환하는 작업에는 실행 중인 <xref:Microsoft.AspNetCore.Mvc.ViewResult>의 일부로 모든 Razor 프로세스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="40f79-407">API 메서드는 결과 실행의 일부로 어떤 직렬화를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="40f79-408">[작업 결과](xref:mvc/controllers/actions)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="40f79-409">결과 필터는 작업 또는 작업 필터가 작업 결과를 생성하는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="40f79-410">다음 경우에는 결과 필터가 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="40f79-411">권한 부여 필터 또는 리소스 필터가 파이프라인을 단락시킬 경우.</span><span class="sxs-lookup"><span data-stu-id="40f79-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="40f79-412">예외 필터가 작업 결과를 생성하여 예외를 처리하는 경우.</span><span class="sxs-lookup"><span data-stu-id="40f79-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="40f79-413"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 메서드는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName>을 `true`로 설정하여 작업 결과 및 후속 결과 필터를 단락시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="40f79-414">단락시킬 경우 빈 응답을 생성하지 않도록 응답 개체에 작성하세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="40f79-415">`IResultFilter.OnResultExecuting`에서 예외 throw:</span><span class="sxs-lookup"><span data-stu-id="40f79-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="40f79-416">작업 결과 및 후속 필터의 실행을 막습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="40f79-417">성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="40f79-418"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 메서드가 실행될 때에는 응답이 이미 클라이언트에 전송되었을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="40f79-419">이미 클라이언트에 전송된 응답은 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="40f79-420">`ResultExecutedContext.Canceled`는 작업 결과 실행이 다른 필터에 의해 단락된 경우 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="40f79-421">`ResultExecutedContext.Exception`은 작업 결과 또는 후속 결과 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="40f79-422">`Exception`을 효과적으로 null로 설정하면 예외를 '처리'하고 예외가 파이프라인의 뒷부분에서 다시 throw되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="40f79-423">결과 필터에서 예외를 처리하는 경우 데이터를 응답에 쓸 수 있는 신뢰할 수 있는 방법이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="40f79-424">작업 결과가 예외를 던질 때 헤더가 클라이언트에 플러시된 경우 오류 코드를 전송하기 위한 신뢰할 수 있는 메커니즘이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="40f79-425"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>의 경우 `await next`의 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> 호출은 후속 결과 필터 및 작업 결과를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="40f79-426">단락시키려면 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel)을 `true`로 설정하고 `ResultExecutionDelegate`를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="40f79-427">프레임워크는 서브클래싱 할 수 있는 추상 `ResultFilterAttribute`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="40f79-428">이전에 표시된 [AddHeaderAttribute](#add-header-attribute) 클래스는 결과 필터 특성의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="40f79-429">IAlwaysRunResultFilter 및 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="40f79-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="40f79-430"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 인터페이스는 모든 작업 결과에 대해 실행되는 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 구현을 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="40f79-431">여기에는 다음에 의해 생성되는 작업 결과가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-431">This includes action results produced by:</span></span>

* <span data-ttu-id="40f79-432">단락하는 권한 부여 필터 및 리소스 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="40f79-433">예외 필터.</span><span class="sxs-lookup"><span data-stu-id="40f79-433">Exception filters.</span></span>

<span data-ttu-id="40f79-434">예를 들어 다음 필터는 항상 실행되어 콘텐츠 협상이 실패할 경우 작업 결과(<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)를 *422 Unprocessable Entity* 상태 코드로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="40f79-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="40f79-435">IFilterFactory</span></span>

<span data-ttu-id="40f79-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="40f79-437">따라서 필터 파이프라인 어디에서나 `IFilterFactory` 인스턴스를 `IFilterMetadata` 인스턴스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="40f79-438">런타임이 필터를 호출하려고 준비할 때 `IFilterFactory`로 캐스팅을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="40f79-439">해당 캐스팅에 성공하면 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 호출하여 호출되는 `IFilterMetadata` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="40f79-440">앱이 시작될 때 정확한 필터 파이프라인을 명시적으로 설정할 필요가 없으므로 유연한 디자인을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="40f79-441">필터를 만드는 다른 방법으로 사용자 지정 특성 구현을 사용하여 `IFilterFactory`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="40f79-442">필터는 다음 코드에서 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="40f79-443">[샘플 다운로드 샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)를 실행하여 이전 코드를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-443">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="40f79-444">F12 개발자 도구를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="40f79-445">`https://localhost:5001/Sample/HeaderWithFactory`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="40f79-446">F12 개발자 도구는 예제 코드에 의해 추가된 다음 응답 헤더를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="40f79-447">**author:** `Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="40f79-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="40f79-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="40f79-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="40f79-449">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="40f79-449">**internal:** `My header`</span></span>

<span data-ttu-id="40f79-450">이전 코드에서는 **internal:** `My header` 응답 헤더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="40f79-451">특성에서 구현된 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="40f79-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="40f79-452">`IFilterFactory`를 구현하는 필터는 다음과 같은 필터에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="40f79-453">매개 변수 전달을 필요로 하지 않는 필터.</span><span class="sxs-lookup"><span data-stu-id="40f79-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="40f79-454">DI에 의해 채워져야 할 생성자 종속성이 있는 필터.</span><span class="sxs-lookup"><span data-stu-id="40f79-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="40f79-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="40f79-456">`IFilterFactory`는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="40f79-457">`CreateInstance`는 서비스 컨테이너(DI)에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="40f79-458">다음 코드는 `[SampleActionFilter]`을 적용하는 세 가지 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="40f79-459">이전 코드에서 `[SampleActionFilter]`로 메서드를 장식하는 것이 `SampleActionFilter`을 적용하는 데 선호되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="40f79-460">필터 파이프라인에서 미들웨어 사용</span><span class="sxs-lookup"><span data-stu-id="40f79-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="40f79-461">리소스 필터는 파이프라인의 뒷부분에 제공되는 모든 실행을 둘러싼다는 점에서 [미들웨어](xref:fundamentals/middleware/index)처럼 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="40f79-462">하지만 필터는 런타임의 일부라는 점에서 미들웨어와 다릅니다. 즉, 컨텍스트 및 구문에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="40f79-463">미들웨어를 필터로 사용하려면 필터 파이프라인에 삽입할 미들웨어를 지정하는 `Configure` 메서드를 포함한 형식을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="40f79-464">다음 예제는 지역화 미들웨어를 사용하여 요청에 대한 현재 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="40f79-465"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>를 사용하여 미들웨어를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="40f79-466">미들웨어 필터는 모델 바인딩 이전 및 나머지 파이프라인 이후에 리소스 필터와 동일한 필터 파이프라인 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="40f79-467">다음 작업</span><span class="sxs-lookup"><span data-stu-id="40f79-467">Next actions</span></span>

* <span data-ttu-id="40f79-468">[Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="40f79-469">필터를 실험하려면 [GitHub 예제를 다운로드하고, 테스트하고, 수정](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="40f79-470">작성자: [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="40f79-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="40f79-471">ASP.NET Core에서 *필터*를 사용하면 요청 처리 파이프라인의 특정 단계 전후에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="40f79-472">기본 제공 필터는 다음과 같은 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="40f79-473">권한 부여(사용자가 권한이 없는 리소스에 액세스하는 것을 방지).</span><span class="sxs-lookup"><span data-stu-id="40f79-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="40f79-474">응답 캐싱(요청 파이프라인을 단락하여 캐시된 응답 반환).</span><span class="sxs-lookup"><span data-stu-id="40f79-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="40f79-475">사용자 지정 필터를 만들어 횡단 관심사를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="40f79-476">횡단 관심사의 사례로는 오류 처리, 캐싱, 구성, 권한 부여 및 로깅을 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="40f79-477">필터는 코드 중복을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="40f79-478">예를 들어 오류 처리 예외 필터는 오류 처리를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="40f79-479">이 문서는 Razor Pages, API 컨트롤러 및 보기를 사용하는 컨트롤러에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="40f79-480">[예제 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="40f79-480">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="40f79-481">필터 작동 방법</span><span class="sxs-lookup"><span data-stu-id="40f79-481">How filters work</span></span>

<span data-ttu-id="40f79-482">필터는 ‘필터 파이프라인’이라고도 하는 ‘ASP.NET Core 동작 호출 파이프라인’내에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="40f79-483">필터 파이프라인은 ASP.NET Core가 실행할 작업을 선택한 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![요청은 기타 미들웨어, 라우팅 미들웨어, 작업 선택 및 ASP.NET Core 작업 호출 파이프라인을 통해서 처리됩니다.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="40f79-486">필터 형식</span><span class="sxs-lookup"><span data-stu-id="40f79-486">Filter types</span></span>

<span data-ttu-id="40f79-487">각 필터 형식은 필터 파이프라인의 다른 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="40f79-488">[권한 부여 필터](#authorization-filters)는 가장 먼저 실행되고 사용자가 요청에 대한 권한이 있는지 여부를 결정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="40f79-489">권한 부여 필터는 요청이 인증되지 않는 경우 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="40f79-490">[리소스 필터](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="40f79-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="40f79-491">권한 부여 후 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-491">Run after authorization.</span></span>  
  * <span data-ttu-id="40f79-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>는 나머지 필터 파이프라인보다 먼저 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="40f79-493">예를 들어 `OnResourceExecuting`는 모델 바인딩 전에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="40f79-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>는 파이프라인의 나머지 부분이 완료된 후에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="40f79-495">[작업 필터](#action-filters)는 개별 작업 메서드가 호출되는 전후에 즉시 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="40f79-496">작업에 전달된 인수 및 작업에서 반환된 결과를 조작하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="40f79-497">Razor Pages에서는 작업 필터가 지원되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="40f79-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="40f79-498">[예외 필터](#exception-filters)는 응답 본문에 무언가 쓰여지기 전에 발생한 처리되지 않은 예외에 대한 전역 정책을 적용하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="40f79-499">[결과 필터](#result-filters)는 개별 작업 결과의 실행 전후에 즉시 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="40f79-500">작업 메서드가 성공적으로 실행되는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="40f79-501">보기 또는 포맷터 실행을 둘러싸야 하는 논리에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="40f79-502">다음 다이어그램은 필터 형식이 필터 파이프라인에서 상호 작용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![요청은 권한 부여 필터, 리소스 필터, 모델 바인딩, 작업 필터, 작업 실행 및 작업 결과 변환, 예외 필터, 결과 필터 및 결과 실행을 통해 처리됩니다.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="40f79-505">구현</span><span class="sxs-lookup"><span data-stu-id="40f79-505">Implementation</span></span>

<span data-ttu-id="40f79-506">필터는 서로 다른 인터페이스 정의를 통해 동기 및 비동기 구현을 모두 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="40f79-507">동기 필터는 해당 파이프라인 단계 전(`On-Stage-Executing`)과 후(`On-Stage-Executed`)에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="40f79-508">예를 들어 `OnActionExecuting`는 작업 메서드가 호출되기 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="40f79-509">`OnActionExecuted`는 작업 메서드가 반환된 후 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="40f79-510">비동기 필터는 `On-Stage-ExecutionAsync` 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="40f79-511">이전 코드에서 `SampleAsyncActionFilter`에는 작업 메서드를 실행하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>(`next`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="40f79-512">각 `On-Stage-ExecutionAsync` 메서드는 필터의 파이프라인 단계를 실행하는 `FilterType-ExecutionDelegate`를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="40f79-513">여러 필터 단계</span><span class="sxs-lookup"><span data-stu-id="40f79-513">Multiple filter stages</span></span>

<span data-ttu-id="40f79-514">단일 클래스에서 여러 필터 단계에 대한 인터페이스를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="40f79-515">예를 들어 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 클래스는 `IActionFilter`, `IResultFilter` 및 해당 비동기 항목을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="40f79-516">필터 인터페이스의 동기 또는 비동기 버전을 모두 구현하지 **말고** 그 중 **한 가지**만 구현하세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="40f79-517">런타임은 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="40f79-518">그렇지 않으면 동기 인터페이스의 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="40f79-519">비동기 및 동기 인터페이스가 모두 하나의 클래스에 구현된 경우에는 비동기 메서드만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="40f79-520"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 같은 추상 클래스를 사용하는 경우 각 필터 형식에 대한 동기 메서드 또는 비동기 메서드만 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="40f79-521">기본 제공 필터 특성</span><span class="sxs-lookup"><span data-stu-id="40f79-521">Built-in filter attributes</span></span>

<span data-ttu-id="40f79-522">ASP.NET Core에는 서브클래싱 및 사용자 지정할 수 있는 기본 제공 특성 기반 필터가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="40f79-523">예를 들어 다음 결과 필터는 응답에 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="40f79-524">특성을 사용하면 이전 예제와 같이 필터에서 인수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="40f79-525">컨트롤러나 작업 메서드에 `AddHeaderAttribute`를 적용하고 HTTP 헤더의 이름 및 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="40f79-526">여러 필터 인터페이스는 사용자 지정 구현을 위한 기본 클래스로 사용할 수 있는 상응하는 특성을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="40f79-527">필터 특성:</span><span class="sxs-lookup"><span data-stu-id="40f79-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="40f79-528">필터 범위 및 실행 순서</span><span class="sxs-lookup"><span data-stu-id="40f79-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="40f79-529">세 가지 *범위* 중 하나에서 필터를 파이프라인에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="40f79-530">작업에서 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="40f79-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="40f79-531">컨트롤러에서 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="40f79-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="40f79-532">다음 코드와 같이 모든 컨트롤러와 작업에 전역으로 사용:</span><span class="sxs-lookup"><span data-stu-id="40f79-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="40f79-533">이전 코드는 [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) 컬렉션을 사용하여 3개의 필터를 전역적으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="40f79-534">기본 실행 순서</span><span class="sxs-lookup"><span data-stu-id="40f79-534">Default order of execution</span></span>

<span data-ttu-id="40f79-535">*동일한 유형*의 필터가 여러 개 있는 경우 범위가 필터 실행의 기본 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="40f79-536">전역 필터는 클래스 필터를 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-536">Global filters surround class filters.</span></span> <span data-ttu-id="40f79-537">클래스 필터는 메서드 필터를 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-537">Class filters surround method filters.</span></span>

<span data-ttu-id="40f79-538">필터 중첩의 결과로 필터의 *after* 코드는 *before* 코드의 역순으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="40f79-539">필터의 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-539">The filter sequence:</span></span>

* <span data-ttu-id="40f79-540">전역 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="40f79-541">컨트롤러 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="40f79-542">작업 메서드 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="40f79-543">작업 메서드 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="40f79-544">컨트롤러 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="40f79-545">전역 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="40f79-546">다음 예제는 필터 메서드가 동기 작업 필터에 대해 호출되는 순서를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="40f79-547">시퀀스</span><span class="sxs-lookup"><span data-stu-id="40f79-547">Sequence</span></span> | <span data-ttu-id="40f79-548">필터 범위</span><span class="sxs-lookup"><span data-stu-id="40f79-548">Filter scope</span></span> | <span data-ttu-id="40f79-549">Filter 메서드</span><span class="sxs-lookup"><span data-stu-id="40f79-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="40f79-550">1</span><span class="sxs-lookup"><span data-stu-id="40f79-550">1</span></span> | <span data-ttu-id="40f79-551">Global</span><span class="sxs-lookup"><span data-stu-id="40f79-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="40f79-552">2</span><span class="sxs-lookup"><span data-stu-id="40f79-552">2</span></span> | <span data-ttu-id="40f79-553">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="40f79-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="40f79-554">3</span><span class="sxs-lookup"><span data-stu-id="40f79-554">3</span></span> | <span data-ttu-id="40f79-555">방법</span><span class="sxs-lookup"><span data-stu-id="40f79-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="40f79-556">4</span><span class="sxs-lookup"><span data-stu-id="40f79-556">4</span></span> | <span data-ttu-id="40f79-557">방법</span><span class="sxs-lookup"><span data-stu-id="40f79-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="40f79-558">5</span><span class="sxs-lookup"><span data-stu-id="40f79-558">5</span></span> | <span data-ttu-id="40f79-559">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="40f79-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="40f79-560">6</span><span class="sxs-lookup"><span data-stu-id="40f79-560">6</span></span> | <span data-ttu-id="40f79-561">Global</span><span class="sxs-lookup"><span data-stu-id="40f79-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="40f79-562">이 순서는 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-562">This sequence shows:</span></span>

* <span data-ttu-id="40f79-563">메서드 필터는 컨트롤러 필터 내에서 중첩됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="40f79-564">컨트롤러 필터는 전역 필터 내에서 중첩됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="40f79-565">컨트롤러 및 Razor Page 수준 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="40f79-566"><xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스에서 상속되는 모든 컨트롤러에는 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 및 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 메서드가 포함됩니다. 이러한 메서드는:</span><span class="sxs-lookup"><span data-stu-id="40f79-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="40f79-567">다음 메서드는</span><span class="sxs-lookup"><span data-stu-id="40f79-567">These methods:</span></span>

* <span data-ttu-id="40f79-568">지정된 작업을 위해 실행되는 필터를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="40f79-569">`OnActionExecuting`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="40f79-570">`OnActionExecuted`는 모든 작업 필터 이후에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="40f79-571">`OnActionExecutionAsync`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="40f79-572">작업 메서드 이후 `next`가 실행된 후의 필터 코드.</span><span class="sxs-lookup"><span data-stu-id="40f79-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="40f79-573">예를 들어 다운로드 예제에서 `MySampleActionFilter`은 시작할 때 전역적으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="40f79-574">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="40f79-574">The `TestController`:</span></span>

* <span data-ttu-id="40f79-575">`SampleActionFilterAttribute`(`[SampleActionFilter]`)를 `FilterTest2` 작업에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="40f79-576">`OnActionExecuting` 및 `OnActionExecuted`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="40f79-577">`https://localhost:5001/Test/FilterTest2`로 이동하면 다음 코드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="40f79-578">Razor Pages에 대해서는 [필터 메서드를 재정의하여 Razor 페이지 필터 구현](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="40f79-579">기본 순서 재정의</span><span class="sxs-lookup"><span data-stu-id="40f79-579">Overriding the default order</span></span>

<span data-ttu-id="40f79-580"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>를 구현하여 실행의 기본 순서를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="40f79-581">`IOrderedFilter`은 실행 순서를 결정하는 데 범위보다 우선 순위가 높은 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="40f79-582">낮은 `Order` 값을 가진 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="40f79-583">더 높은 *값을 가진 필터 이전에*before`Order` 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="40f79-584">더 높은 *값을 가진 필터 이후에*after`Order` 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="40f79-585">생성자 매개 변수로 `Order` 속성을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="40f79-586">이전 예제와 동일한 3개의 작업 필터를 가정해보세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="40f79-587">컨트롤러와 전역 필터의 `Order` 속성을 각각 1과 2로 설정하면 실행 순서가 반대가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="40f79-588">시퀀스</span><span class="sxs-lookup"><span data-stu-id="40f79-588">Sequence</span></span> | <span data-ttu-id="40f79-589">필터 범위</span><span class="sxs-lookup"><span data-stu-id="40f79-589">Filter scope</span></span> | <span data-ttu-id="40f79-590">`Order` 속성</span><span class="sxs-lookup"><span data-stu-id="40f79-590">`Order` property</span></span> | <span data-ttu-id="40f79-591">Filter 메서드</span><span class="sxs-lookup"><span data-stu-id="40f79-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="40f79-592">1</span><span class="sxs-lookup"><span data-stu-id="40f79-592">1</span></span> | <span data-ttu-id="40f79-593">방법</span><span class="sxs-lookup"><span data-stu-id="40f79-593">Method</span></span> | <span data-ttu-id="40f79-594">0</span><span class="sxs-lookup"><span data-stu-id="40f79-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="40f79-595">2</span><span class="sxs-lookup"><span data-stu-id="40f79-595">2</span></span> | <span data-ttu-id="40f79-596">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="40f79-596">Controller</span></span> | <span data-ttu-id="40f79-597">1</span><span class="sxs-lookup"><span data-stu-id="40f79-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="40f79-598">3</span><span class="sxs-lookup"><span data-stu-id="40f79-598">3</span></span> | <span data-ttu-id="40f79-599">Global</span><span class="sxs-lookup"><span data-stu-id="40f79-599">Global</span></span> | <span data-ttu-id="40f79-600">2</span><span class="sxs-lookup"><span data-stu-id="40f79-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="40f79-601">4</span><span class="sxs-lookup"><span data-stu-id="40f79-601">4</span></span> | <span data-ttu-id="40f79-602">Global</span><span class="sxs-lookup"><span data-stu-id="40f79-602">Global</span></span> | <span data-ttu-id="40f79-603">2</span><span class="sxs-lookup"><span data-stu-id="40f79-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="40f79-604">5</span><span class="sxs-lookup"><span data-stu-id="40f79-604">5</span></span> | <span data-ttu-id="40f79-605">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="40f79-605">Controller</span></span> | <span data-ttu-id="40f79-606">1</span><span class="sxs-lookup"><span data-stu-id="40f79-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="40f79-607">6</span><span class="sxs-lookup"><span data-stu-id="40f79-607">6</span></span> | <span data-ttu-id="40f79-608">방법</span><span class="sxs-lookup"><span data-stu-id="40f79-608">Method</span></span> | <span data-ttu-id="40f79-609">0</span><span class="sxs-lookup"><span data-stu-id="40f79-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="40f79-610">`Order` 속성은 필터가 실행되는 순서를 결정할 때 범위를 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="40f79-611">필터는 먼저 순서에 따라 정렬된 다음, 순서가 동일할 경우 범위가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="40f79-612">모든 기본 제공 필터는 `IOrderedFilter`을 구현하고 기본 `Order` 값을 0으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="40f79-613">기본 제공 필터의 경우 `Order`를 0이 아닌 값으로 설정하지 않는 한 범위가 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="40f79-614">취소 및 단락</span><span class="sxs-lookup"><span data-stu-id="40f79-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="40f79-615">필터 메서드에 제공되는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 매개 변수의 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 속성을 설정하여 필터 파이프라인을 단락시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="40f79-616">예를 들어 다음 리소스 필터는 파이프라인의 나머지 부분이 실행되지 않도록 막습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="40f79-617">다음 코드에서 `ShortCircuitingResourceFilter` 및 `AddHeader` 필터는 `SomeResource` 작업 메서드를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="40f79-618">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="40f79-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="40f79-619">리소스 필터이고 `AddHeader`는 작업 필터이기 때문에 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="40f79-620">나머지 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="40f79-621">따라서 `AddHeader` 필터는 `SomeResource` 작업에 대해 절대 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="40f79-622">이 동작은 `ShortCircuitingResourceFilter`가 먼저 실행되기 때문에 작업 메서드 수준에서 두 필터를 적용하더라도 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="40f79-623">해당 필터 형식 또는 `ShortCircuitingResourceFilter` 속성의 명시적 사용으로 인해 `Order`가 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="40f79-624">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="40f79-624">Dependency injection</span></span>

<span data-ttu-id="40f79-625">형식별 또는 인스턴스별 필터를 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="40f79-626">인스턴스가 추가되면 모든 요청에 해당 인스턴스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="40f79-627">형식이 추가되면 해당 필터는 형식으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="40f79-628">형식으로 활성화된 필터는 다음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-628">A type-activated filter means:</span></span>

* <span data-ttu-id="40f79-629">각 요청에 대해 인스턴스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-629">An instance is created for each request.</span></span>
* <span data-ttu-id="40f79-630">모든 생성자 종속성이 DI([종속성 주입](xref:fundamentals/dependency-injection))를 통해서 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="40f79-631">특성으로 구현되고 컨트롤러 클래스 또는 작업 메서드에 직접 추가된 필터는 DI([종속성 주입](xref:fundamentals/dependency-injection))에서 제공하는 생성자 종속성을 가질 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="40f79-632">DI는 다음과 같은 이유로 생성자 종속성을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="40f79-633">특성이 적용될 때 해당 생성자 매개 변수가 제공되어야 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="40f79-634">특성이 작동하는 방법의 제한 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="40f79-635">다음 필터는 DI에서 제공하는 생성자 종속성을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="40f79-636">특성에서 구현된 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="40f79-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="40f79-637">위의 필터는 컨트롤러 또는 작업 메서드에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="40f79-638">로거는 DI를 통해서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-638">Loggers are available from DI.</span></span> <span data-ttu-id="40f79-639">그러나 로깅 용도로만 필터를 만들거나 사용하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="40f79-640">[기본 제공 프레임워크 로깅](xref:fundamentals/logging/index)은 일반적으로 로깅에 필요한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="40f79-641">필터에 추가된 로깅은:</span><span class="sxs-lookup"><span data-stu-id="40f79-641">Logging added to filters:</span></span>

* <span data-ttu-id="40f79-642">비즈니스 도메인 문제 또는 필터 고유의 동작에 중점을 두어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="40f79-643">작업 또는 다른 프레임워크 이벤트를 로깅해서는 **안 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="40f79-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="40f79-644">기본 제공 필터는 작업 및 프레임워크 이벤트를 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="40f79-645">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="40f79-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="40f79-646">서비스 필터 구현 형식은 `ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="40f79-647"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 DI에서 필터의 인스턴스를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="40f79-648">다음 코드는 `AddHeaderResultServiceFilter`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="40f79-649">다음 코드에서 `AddHeaderResultServiceFilter`는 DI 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="40f79-650">다음 코드에서 `ServiceFilter` 특성은 DI에서 `AddHeaderResultServiceFilter` 필터의 인스턴스를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="40f79-651">`ServiceFilterAttribute`를 사용할 때 [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="40f79-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="40f79-652">필터 인스턴스가 원래 생성된 요청 범위 외부에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="40f79-653">ASP.NET Core 런타임은 다음을 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="40f79-654">필터의 단일 인스턴스 생성.</span><span class="sxs-lookup"><span data-stu-id="40f79-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="40f79-655">나중에 DI 컨테이너에서 필터가 다시 요청되지 않음.</span><span class="sxs-lookup"><span data-stu-id="40f79-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="40f79-656">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="40f79-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="40f79-658">`IFilterFactory`는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="40f79-659">`CreateInstance`는 DI에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="40f79-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="40f79-660">TypeFilterAttribute</span></span>

<span data-ttu-id="40f79-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>와 비슷하지만 해당 형식은 DI 컨테이너에서 직접 해결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="40f79-662"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>를 사용하여 형식을 인스턴스화합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="40f79-663">`TypeFilterAttribute` 형식은 DI 컨테이너에서 직접 해결되지 않기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="40f79-664">`TypeFilterAttribute`을 사용하여 참조되는 형식은 DI 컨테이너를 사용하여 등록할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="40f79-665">DI 컨테이너에서 충족하는 종속성을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="40f79-666">`TypeFilterAttribute`는 형식에 대한 생성자 인수를 필요에 따라 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="40f79-667">`TypeFilterAttribute`를 사용할 때 [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="40f79-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="40f79-668">필터 인스턴스가 원래 생성된 요청 범위 밖에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="40f79-669">ASP.NET Core 런타임은 단일 필터 인스턴스가 생성되도록 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="40f79-670">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="40f79-671">다음 예제는 `TypeFilterAttribute`를 사용하여 형식에 인수를 전달하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="40f79-672">권한 부여 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-672">Authorization filters</span></span>

<span data-ttu-id="40f79-673">권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-673">Authorization filters:</span></span>

* <span data-ttu-id="40f79-674">필터 파이프라인에서 첫 번째로 실행되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="40f79-675">작업 메서드에 대한 액세스를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-675">Control access to action methods.</span></span>
* <span data-ttu-id="40f79-676">before 메서드는 있지만 after 메서드는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="40f79-677">사용자 지정 권한 부여 필터는 사용자 지정 권한 부여 프레임워크를 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="40f79-678">사용자 지정 필터를 작성하는 대신 권한 부여 정책을 구성하거나 사용자 지정 권한 부여 정책을 작성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="40f79-679">기본 제공 권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="40f79-680">권한 부여 시스템을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-680">Calls the authorization system.</span></span>
* <span data-ttu-id="40f79-681">요청을 승인하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-681">Does not authorize requests.</span></span>

<span data-ttu-id="40f79-682">권한 부여 필터 내에서 예외를 던지지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="40f79-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="40f79-683">해당 예외는 처리되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-683">The exception will not be handled.</span></span>
* <span data-ttu-id="40f79-684">예외 필터가 해당 예외를 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="40f79-685">권한 부여 필터에서 예외가 발생할 경우 챌린지 발행을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="40f79-686">[권한 부여](xref:security/authorization/introduction)에 대해 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="40f79-687">리소스 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-687">Resource filters</span></span>

<span data-ttu-id="40f79-688">리소스 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-688">Resource filters:</span></span>

* <span data-ttu-id="40f79-689"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="40f79-690">실행이 필터 파이프라인 대부분을 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="40f79-691">[권한 부여 필터](#authorization-filters)만 리소스 필터 이전에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="40f79-692">리소스 필터는 파이프라인의 대부분을 단락시켜야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="40f79-693">예를 들어 캐싱 필터는 캐시 적중 시 파이프라인의 나머지 부분을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="40f79-694">리소스 필터 예제:</span><span class="sxs-lookup"><span data-stu-id="40f79-694">Resource filter examples:</span></span>

* <span data-ttu-id="40f79-695">이전에 살펴본 [단락 리소스 필터](#short-circuiting-resource-filter).</span><span class="sxs-lookup"><span data-stu-id="40f79-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="40f79-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="40f79-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="40f79-697">모델 바인딩이 양식 데이터에 액세스하는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="40f79-698">대용량 파일 업로드에서 양식 데이터를 메모리로 읽어들이는 것을 방지하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="40f79-699">작업 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="40f79-700">작업 필터는 Razor Pages에 **적용되지 않습니다**.</span><span class="sxs-lookup"><span data-stu-id="40f79-700">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="40f79-701">Razor Pages는 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-701">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="40f79-702">자세한 내용은 [Razor 페이지에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="40f79-703">작업 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-703">Action filters:</span></span>

* <span data-ttu-id="40f79-704"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="40f79-705">실행이 작업 메서드의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="40f79-706">다음 코드는 예제 작업 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="40f79-707"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>는 다음과 같은 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="40f79-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 작업 메서드에 대한 입력을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="40f79-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - 컨트롤러 인스턴스를 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="40f79-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` 설정은 작업 메서드 및 후속 작업 필터의 실행을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="40f79-711">작업 메서드에서 예외를 던지면:</span><span class="sxs-lookup"><span data-stu-id="40f79-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="40f79-712">후속 필터의 실행을 막습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="40f79-713">`Result` 설정과 달리 성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="40f79-714"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>는 `Controller` 및 `Result`에 더하여 다음과 같은 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="40f79-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 작업 실행이 다른 필터에 의해 단락된 경우 true입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="40f79-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 작업 또는 이전에 실행된 작업 필터에서 예외가 던져진 경우 null이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="40f79-717">이 속성을 null로 설정하면:</span><span class="sxs-lookup"><span data-stu-id="40f79-717">Setting this property to null:</span></span>

  * <span data-ttu-id="40f79-718">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="40f79-719">`Result`는 작업 메서드에서 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="40f79-720">`IAsyncActionFilter`의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>에 대한 호출은:</span><span class="sxs-lookup"><span data-stu-id="40f79-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="40f79-721">모든 후속 작업 필터 및 작업 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="40f79-722">`ActionExecutedContext`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="40f79-723">단락시키려면 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName>을 결과 인스턴스에 할당하고 `next`(`ActionExecutionDelegate`)를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="40f79-724">프레임워크는 서브클래싱 할 수 있는 추상 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="40f79-725">`OnActionExecuting` 작업 필터는 다음에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="40f79-726">모델 상태의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-726">Validate model state.</span></span>
* <span data-ttu-id="40f79-727">상태가 유효하지 않은 경우 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="40f79-728">`OnActionExecuted` 메서드는 작업 메서드 이후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="40f79-729">그리고 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 속성을 통해 작업 결과를 확인하고 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="40f79-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>는 작업 실행이 다른 필터에 의해 단락된 경우 true로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="40f79-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>은 작업 또는 후속 작업 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="40f79-732">`Exception`을 null로 설정하면:</span><span class="sxs-lookup"><span data-stu-id="40f79-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="40f79-733">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="40f79-734">`ActionExecutedContext.Result`는 작업 메서드에서 정상적으로 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="40f79-735">예외 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-735">Exception filters</span></span>

<span data-ttu-id="40f79-736">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-736">Exception filters:</span></span>

* <span data-ttu-id="40f79-737"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="40f79-738">공통 오류 처리 정책을 구현하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="40f79-739">다음 예제 예외 필터는 사용자 지정 오류 보기를 사용하여 앱을 개발 중인 경우에 발생하는 예외에 대한 세부 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="40f79-740">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-740">Exception filters:</span></span>

* <span data-ttu-id="40f79-741">before 및 after 이벤트가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-741">Don't have before and after events.</span></span>
* <span data-ttu-id="40f79-742"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="40f79-743">Razor Page 또는 컨트롤러 생성, [모델 바인딩](xref:mvc/models/model-binding), 작업 필터 또는 작업 메서드에서 발생하는 처리되지 않은 예외를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="40f79-744">리소스 필터, 결과 필터 또는 MVC 결과 실행에서 발생하는 예외를 잡지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="40f79-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="40f79-745">예외를 처리하려면 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 속성을 `true`로 설정하거나 응답을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="40f79-746">그러면 예외가 전파되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-746">This stops propagation of the exception.</span></span> <span data-ttu-id="40f79-747">예외 필터는 예외를 “성공”으로 변환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="40f79-748">이는 작업 필터에서만 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-748">Only an action filter can do that.</span></span>

<span data-ttu-id="40f79-749">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-749">Exception filters:</span></span>

* <span data-ttu-id="40f79-750">작업 내에서 발생하는 예외를 잡는 데 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="40f79-751">오류 처리 미들웨어만큼 유연하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="40f79-752">예외 처리의 경우 미들웨어를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="40f79-753">어떤 작업 메서드가 호출되는지에 따라 오류 처리 방식이 ‘다른’ 경우에만 예외 필터를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="40f79-754">예를 들어 앱에는 API 엔드포인트 및 보기/HTML 모두에 대한 작업 메서드가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="40f79-755">API 엔드포인트는 JSON으로 오류 정보를 반환할 수 있습니다. 반면 보기 기반 작업은 HTML로 오류 페이지를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="40f79-756">결과 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-756">Result filters</span></span>

<span data-ttu-id="40f79-757">결과 필터는:</span><span class="sxs-lookup"><span data-stu-id="40f79-757">Result filters:</span></span>

* <span data-ttu-id="40f79-758">다음 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-758">Implement an interface:</span></span>
  * <span data-ttu-id="40f79-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="40f79-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="40f79-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="40f79-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="40f79-761">실행이 작업 결과의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="40f79-762">IResultFilter 및 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="40f79-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="40f79-763">다음 코드는 HTTP 헤더를 추가하는 결과 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="40f79-764">실행되는 결과의 종류는 작업에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="40f79-765">보기를 반환하는 작업에는 실행 중인 <xref:Microsoft.AspNetCore.Mvc.ViewResult>의 일부로 모든 Razor 프로세스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="40f79-766">API 메서드는 결과 실행의 일부로 어떤 직렬화를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="40f79-767">[작업 결과](xref:mvc/controllers/actions)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="40f79-768">결과 필터는 작업 또는 작업 필터가 작업 결과를 생성하는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="40f79-769">다음 경우에는 결과 필터가 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="40f79-770">권한 부여 필터 또는 리소스 필터가 파이프라인을 단락시킬 경우.</span><span class="sxs-lookup"><span data-stu-id="40f79-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="40f79-771">예외 필터가 작업 결과를 생성하여 예외를 처리하는 경우.</span><span class="sxs-lookup"><span data-stu-id="40f79-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="40f79-772"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 메서드는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName>을 `true`로 설정하여 작업 결과 및 후속 결과 필터를 단락시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="40f79-773">단락시킬 경우 빈 응답을 생성하지 않도록 응답 개체에 작성하세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="40f79-774">`IResultFilter.OnResultExecuting`에서 예외를 던지면:</span><span class="sxs-lookup"><span data-stu-id="40f79-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="40f79-775">작업 결과 및 후속 필터의 실행을 막습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="40f79-776">성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="40f79-777"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 메서드가 실행될 때에는 응답이 이미 클라이언트에 전송되었을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="40f79-778">이미 클라이언트에 전송된 응답은 더이상 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="40f79-779">`ResultExecutedContext.Canceled`는 작업 결과 실행이 다른 필터에 의해 단락된 경우 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="40f79-780">`ResultExecutedContext.Exception`은 작업 결과 또는 후속 결과 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="40f79-781">`Exception`을 null로 설정하면 효과적으로 예외를 처리하고 이후의 파이프라인에서 ASP.NET Core에 의해 예외가 다시 던져지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="40f79-782">결과 필터에서 예외를 처리하는 경우 데이터를 응답에 쓸 수 있는 신뢰할 수 있는 방법이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="40f79-783">작업 결과가 예외를 던질 때 헤더가 클라이언트에 플러시된 경우 오류 코드를 전송하기 위한 신뢰할 수 있는 메커니즘이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="40f79-784"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>의 경우 `await next`의 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> 호출은 후속 결과 필터 및 작업 결과를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="40f79-785">단락시키려면 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel)을 `true`로 설정하고 `ResultExecutionDelegate`를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="40f79-786">프레임워크는 서브클래싱 할 수 있는 추상 `ResultFilterAttribute`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="40f79-787">이전에 표시된 [AddHeaderAttribute](#add-header-attribute) 클래스는 결과 필터 특성의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="40f79-788">IAlwaysRunResultFilter 및 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="40f79-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="40f79-789"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 인터페이스는 모든 작업 결과에 대해 실행되는 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 구현을 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="40f79-790">여기에는 다음에 의해 생성되는 작업 결과가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-790">This includes action results produced by:</span></span>

* <span data-ttu-id="40f79-791">단락하는 권한 부여 필터 및 리소스 필터</span><span class="sxs-lookup"><span data-stu-id="40f79-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="40f79-792">예외 필터.</span><span class="sxs-lookup"><span data-stu-id="40f79-792">Exception filters.</span></span>

<span data-ttu-id="40f79-793">예를 들어 다음 필터는 항상 실행되어 콘텐츠 협상이 실패할 경우 작업 결과(<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)를 *422 Unprocessable Entity* 상태 코드로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="40f79-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="40f79-794">IFilterFactory</span></span>

<span data-ttu-id="40f79-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="40f79-796">따라서 필터 파이프라인 어디에서나 `IFilterFactory` 인스턴스를 `IFilterMetadata` 인스턴스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="40f79-797">런타임이 필터를 호출하려고 준비할 때 `IFilterFactory`로 캐스팅을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="40f79-798">해당 캐스팅에 성공하면 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 호출하여 호출되는 `IFilterMetadata` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="40f79-799">앱이 시작될 때 정확한 필터 파이프라인을 명시적으로 설정할 필요가 없으므로 유연한 디자인을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="40f79-800">필터를 만드는 다른 방법으로 사용자 지정 특성 구현을 사용하여 `IFilterFactory`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="40f79-801">[다운로드 예제](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)를 실행하여 이전 코드를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-801">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="40f79-802">F12 개발자 도구를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="40f79-803">`https://localhost:5001/Sample/HeaderWithFactory`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="40f79-804">F12 개발자 도구는 예제 코드에 의해 추가된 다음 응답 헤더를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="40f79-805">**author:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="40f79-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="40f79-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="40f79-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="40f79-807">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="40f79-807">**internal:** `My header`</span></span>

<span data-ttu-id="40f79-808">이전 코드에서는 **internal:** `My header` 응답 헤더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="40f79-809">특성에서 구현된 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="40f79-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="40f79-810">`IFilterFactory`를 구현하는 필터는 다음과 같은 필터에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="40f79-811">매개 변수 전달을 필요로 하지 않는 필터.</span><span class="sxs-lookup"><span data-stu-id="40f79-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="40f79-812">DI에 의해 채워져야 할 생성자 종속성이 있는 필터.</span><span class="sxs-lookup"><span data-stu-id="40f79-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="40f79-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="40f79-814">`IFilterFactory`는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="40f79-815">`CreateInstance`는 서비스 컨테이너(DI)에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="40f79-816">다음 코드는 `[SampleActionFilter]`을 적용하는 세 가지 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="40f79-817">이전 코드에서 `[SampleActionFilter]`로 메서드를 장식하는 것이 `SampleActionFilter`을 적용하는 데 선호되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="40f79-818">필터 파이프라인에서 미들웨어 사용</span><span class="sxs-lookup"><span data-stu-id="40f79-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="40f79-819">리소스 필터는 파이프라인의 뒷부분에 제공되는 모든 실행을 둘러싼다는 점에서 [미들웨어](xref:fundamentals/middleware/index)처럼 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="40f79-820">하지만 필터는 ASP.NET Core 런타임의 일부라는 점에서 미들웨어와 다릅니다. 즉, ASP.NET Core 컨텍스트 및 구문에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="40f79-821">미들웨어를 필터로 사용하려면 필터 파이프라인에 삽입할 미들웨어를 지정하는 `Configure` 메서드를 포함한 형식을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="40f79-822">다음 예제는 지역화 미들웨어를 사용하여 요청에 대한 현재 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="40f79-823"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>를 사용하여 미들웨어를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="40f79-824">미들웨어 필터는 모델 바인딩 이전 및 나머지 파이프라인 이후에 리소스 필터와 동일한 필터 파이프라인 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="40f79-825">다음 작업</span><span class="sxs-lookup"><span data-stu-id="40f79-825">Next actions</span></span>

* <span data-ttu-id="40f79-826">[Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40f79-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="40f79-827">필터를 실험하려면 [GitHub 예제를 다운로드하고, 테스트하고, 수정](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="40f79-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
