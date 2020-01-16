---
title: ASP.NET Core에서 필터링
author: Rick-Anderson
description: 필터 작동 방법 및 ASP.NET Core에서 사용하는 방법을 자세히 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 1/1/2020
uid: mvc/controllers/filters
ms.openlocfilehash: 759c150e7f35f3f6a52947edc5ef41448dc227fe
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828973"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="65a63-103">ASP.NET Core에서 필터링</span><span class="sxs-lookup"><span data-stu-id="65a63-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65a63-104">작성자: [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="65a63-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="65a63-105">ASP.NET Core에서 *필터*를 사용하면 요청 처리 파이프라인의 특정 단계 전후에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="65a63-106">기본 제공 필터는 다음과 같은 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="65a63-107">권한 부여(사용자가 권한이 없는 리소스에 액세스하는 것을 방지).</span><span class="sxs-lookup"><span data-stu-id="65a63-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="65a63-108">응답 캐싱(요청 파이프라인을 단락하여 캐시된 응답 반환).</span><span class="sxs-lookup"><span data-stu-id="65a63-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="65a63-109">사용자 지정 필터를 만들어 횡단 관심사를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="65a63-110">횡단 관심사의 사례로는 오류 처리, 캐싱, 구성, 권한 부여 및 로깅을 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="65a63-111">필터는 코드 중복을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="65a63-112">예를 들어 오류 처리 예외 필터는 오류 처리를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="65a63-113">이 문서는 Razor Pages, API 컨트롤러 및 보기를 사용하는 컨트롤러에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="65a63-114">[예제 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="65a63-114">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="65a63-115">필터 작동 방법</span><span class="sxs-lookup"><span data-stu-id="65a63-115">How filters work</span></span>

<span data-ttu-id="65a63-116">필터는 ‘필터 파이프라인’이라고도 하는 ‘ASP.NET Core 동작 호출 파이프라인’내에서 실행됩니다.  </span><span class="sxs-lookup"><span data-stu-id="65a63-116">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="65a63-117">필터 파이프라인은 ASP.NET Core가 실행할 작업을 선택한 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-117">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![다른 미들웨어, 라우팅 미들웨어, 작업 선택 영역 및 작업 호출 파이프라인을 통해 요청이 처리됩니다.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="65a63-120">필터 형식</span><span class="sxs-lookup"><span data-stu-id="65a63-120">Filter types</span></span>

<span data-ttu-id="65a63-121">각 필터 형식은 필터 파이프라인의 다른 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-121">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="65a63-122">[권한 부여 필터](#authorization-filters)는 가장 먼저 실행되고 사용자가 요청에 대한 권한이 있는지 여부를 결정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-122">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="65a63-123">권한 부여 필터는 요청이 인증되지 않는 경우 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-123">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="65a63-124">[리소스 필터](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="65a63-124">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="65a63-125">권한 부여 후 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-125">Run after authorization.</span></span>  
  * <span data-ttu-id="65a63-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>는 나머지 필터 파이프라인보다 먼저 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="65a63-127">예를 들어 `OnResourceExecuting`는 모델 바인딩 전에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-127">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="65a63-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>는 파이프라인의 나머지 부분이 완료된 후에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="65a63-129">[작업 필터](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="65a63-129">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="65a63-130">작업 메서드가 호출되기 전후에 즉시 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-130">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="65a63-131">작업에 전달된 인수를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-131">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="65a63-132">작업에서 반환된 결과를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-132">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="65a63-133">Razor Pages에서는 지원되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="65a63-133">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="65a63-134">[예외 필터](#exception-filters)는 응답 본문이 쓰여지기 전에 발생한 처리되지 않은 예외에 대한 전역 정책을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-134">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="65a63-135">[결과 필터](#result-filters)는 작업 결과의 실행 전후에 즉시 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-135">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="65a63-136">작업 메서드가 성공적으로 실행되는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-136">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="65a63-137">보기 또는 포맷터 실행을 둘러싸야 하는 논리에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-137">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="65a63-138">다음 다이어그램은 필터 형식이 필터 파이프라인에서 상호 작용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-138">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![요청은 권한 부여 필터, 리소스 필터, 모델 바인딩, 작업 필터, 작업 실행 및 작업 결과 변환, 예외 필터, 결과 필터 및 결과 실행을 통해 처리됩니다.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="65a63-141">구현</span><span class="sxs-lookup"><span data-stu-id="65a63-141">Implementation</span></span>

<span data-ttu-id="65a63-142">필터는 서로 다른 인터페이스 정의를 통해 동기 및 비동기 구현을 모두 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-142">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="65a63-143">동기 필터는 해당 파이프라인 단계 전후에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-143">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="65a63-144">예를 들어 <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*>는 작업 메서드가 호출되기 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-144">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="65a63-145"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>는 작업 메서드가 반환된 후 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-145"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="65a63-146">비동기 필터는 `On-Stage-ExecutionAsync` 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-146">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="65a63-147">예: <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*></span><span class="sxs-lookup"><span data-stu-id="65a63-147">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="65a63-148">이전 코드에서 `SampleAsyncActionFilter`에는 작업 메서드를 실행하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>(`next`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-148">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="65a63-149">여러 필터 단계</span><span class="sxs-lookup"><span data-stu-id="65a63-149">Multiple filter stages</span></span>

<span data-ttu-id="65a63-150">단일 클래스에서 여러 필터 단계에 대한 인터페이스를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-150">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="65a63-151">예를 들어 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-151">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="65a63-152">동기: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="65a63-152">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="65a63-153">비동기: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="65a63-153">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="65a63-154">필터 인터페이스의 동기 또는 비동기 버전을 모두 구현하지 **말고** 그 중 **한 가지**만 구현하세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-154">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="65a63-155">런타임은 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-155">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="65a63-156">그렇지 않으면 동기 인터페이스의 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-156">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="65a63-157">비동기 및 동기 인터페이스가 모두 하나의 클래스에 구현된 경우에는 비동기 메서드만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-157">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="65a63-158"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 같은 추상 클래스를 사용하는 경우 각 필터 형식에 대한 동기 메서드 또는 비동기 메서드만 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-158">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="65a63-159">기본 제공 필터 특성</span><span class="sxs-lookup"><span data-stu-id="65a63-159">Built-in filter attributes</span></span>

<span data-ttu-id="65a63-160">ASP.NET Core에는 서브클래싱 및 사용자 지정할 수 있는 기본 제공 특성 기반 필터가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-160">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="65a63-161">예를 들어 다음 결과 필터는 응답에 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-161">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="65a63-162">특성을 사용하면 이전 예제와 같이 필터에서 인수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-162">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="65a63-163">컨트롤러나 작업 메서드에 `AddHeaderAttribute`를 적용하고 HTTP 헤더의 이름 및 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-163">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="65a63-164">[브라우저 개발자 도구](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools)와 같은 도구를 사용하여 헤더를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-164">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="65a63-165">**응답 헤더**에 `author: Rick Anderson`이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-165">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="65a63-166">다음 코드는 다음과 같은 작업을 수행하는 `ActionFilterAttribute`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-166">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="65a63-167">구성 시스템에서 제목과 이름을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-167">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="65a63-168">앞의 샘플과 달리 다음 코드는 필터 매개 변수를 코드에 추가하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-168">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="65a63-169">응답 헤더에 제목과 이름을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-169">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="65a63-170">구성 옵션은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용하여 [구성 시스템](xref:fundamentals/configuration/index)에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-170">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="65a63-171">예를 들어 *appsettings.json* 파일에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-171">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="65a63-172">`StartUp.ConfigureServices`에서</span><span class="sxs-lookup"><span data-stu-id="65a63-172">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="65a63-173">`PositionOptions` 클래스는 `"Position"` 구성 영역을 사용하여 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-173">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="65a63-174">`MyActionFilterAttribute`는 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-174">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="65a63-175">다음 코드에서는 `PositionOptions` 클래스를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-175">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="65a63-176">다음 코드는 `MyActionFilterAttribute`를 `Index2` 메서드에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-176">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="65a63-177">`Sample/Index2` 엔드포인트가 호출될 때 **응답 헤더**에 `author: Rick Anderson` 및 `Editor: Joe Smith`가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-177">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="65a63-178">다음 코드는 `MyActionFilterAttribute` 및 `AddHeaderAttribute`를 Razor 페이지에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-178">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="65a63-179">Razor 페이지 처리기 메서드에는 필터를 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-179">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="65a63-180">이러한 메서드는 Razor 페이지 모델 또는 전역적으로 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-180">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="65a63-181">여러 필터 인터페이스는 사용자 지정 구현을 위한 기본 클래스로 사용할 수 있는 상응하는 특성을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-181">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="65a63-182">필터 특성:</span><span class="sxs-lookup"><span data-stu-id="65a63-182">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="65a63-183">필터 범위 및 실행 순서</span><span class="sxs-lookup"><span data-stu-id="65a63-183">Filter scopes and order of execution</span></span>

<span data-ttu-id="65a63-184">세 가지 *범위* 중 하나에서 필터를 파이프라인에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-184">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="65a63-185">컨트롤러 작업에서 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="65a63-185">Using an attribute on a controller action.</span></span> <span data-ttu-id="65a63-186">필터 특성은 Razor Pages 처리기 메서드에 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-186">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="65a63-187">컨트롤러 또는 Razor 페이지에서 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="65a63-187">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="65a63-188">다음 코드와 같이 모든 컨트롤러, 작업 및 Razor Pages에 전역으로 사용:</span><span class="sxs-lookup"><span data-stu-id="65a63-188">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="65a63-189">기본 실행 순서</span><span class="sxs-lookup"><span data-stu-id="65a63-189">Default order of execution</span></span>

<span data-ttu-id="65a63-190">파이프라인의 특정 단계에 여러 개의 필터가 있는 경우 범위가 기본 필터 실행 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-190">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="65a63-191">전역 필터는 클래스 필터를 둘러싸고 클래스 필터는 다시 메서드 필터를 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-191">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="65a63-192">필터 중첩의 결과로 필터의 *after* 코드는 *before* 코드의 역순으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-192">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="65a63-193">필터의 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-193">The filter sequence:</span></span>

* <span data-ttu-id="65a63-194">전역 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-194">The *before* code of global filters.</span></span>
  * <span data-ttu-id="65a63-195">컨트롤러 및 Razor 페이지 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-195">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="65a63-196">작업 메서드 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-196">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="65a63-197">작업 메서드 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-197">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="65a63-198">컨트롤러 및 Razor 페이지 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-198">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="65a63-199">전역 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-199">The *after* code of global filters.</span></span>
  
<span data-ttu-id="65a63-200">다음 예제는 필터 메서드가 동기 작업 필터에 대해 호출되는 순서를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-200">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="65a63-201">순서</span><span class="sxs-lookup"><span data-stu-id="65a63-201">Sequence</span></span> | <span data-ttu-id="65a63-202">필터 범위</span><span class="sxs-lookup"><span data-stu-id="65a63-202">Filter scope</span></span> | <span data-ttu-id="65a63-203">필터 메서드</span><span class="sxs-lookup"><span data-stu-id="65a63-203">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="65a63-204">1</span><span class="sxs-lookup"><span data-stu-id="65a63-204">1</span></span> | <span data-ttu-id="65a63-205">전역</span><span class="sxs-lookup"><span data-stu-id="65a63-205">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="65a63-206">2</span><span class="sxs-lookup"><span data-stu-id="65a63-206">2</span></span> | <span data-ttu-id="65a63-207">컨트롤러 또는 Razor 페이지</span><span class="sxs-lookup"><span data-stu-id="65a63-207">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="65a63-208">3</span><span class="sxs-lookup"><span data-stu-id="65a63-208">3</span></span> | <span data-ttu-id="65a63-209">메서드</span><span class="sxs-lookup"><span data-stu-id="65a63-209">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="65a63-210">4</span><span class="sxs-lookup"><span data-stu-id="65a63-210">4</span></span> | <span data-ttu-id="65a63-211">메서드</span><span class="sxs-lookup"><span data-stu-id="65a63-211">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="65a63-212">5</span><span class="sxs-lookup"><span data-stu-id="65a63-212">5</span></span> | <span data-ttu-id="65a63-213">컨트롤러 또는 Razor 페이지</span><span class="sxs-lookup"><span data-stu-id="65a63-213">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="65a63-214">6</span><span class="sxs-lookup"><span data-stu-id="65a63-214">6</span></span> | <span data-ttu-id="65a63-215">전역</span><span class="sxs-lookup"><span data-stu-id="65a63-215">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="65a63-216">컨트롤러 수준 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-216">Controller level filters</span></span>

<span data-ttu-id="65a63-217"><xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스에서 상속되는 모든 컨트롤러에는 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 및 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 메서드가 포함됩니다. 이러한 메서드는:</span><span class="sxs-lookup"><span data-stu-id="65a63-217">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="65a63-218">다음 메서드는</span><span class="sxs-lookup"><span data-stu-id="65a63-218">These methods:</span></span>

* <span data-ttu-id="65a63-219">지정된 작업을 위해 실행되는 필터를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-219">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="65a63-220">`OnActionExecuting`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-220">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="65a63-221">`OnActionExecuted`는 모든 작업 필터 이후에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-221">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="65a63-222">`OnActionExecutionAsync`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-222">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="65a63-223">작업 메서드 이후 `next`가 실행된 후의 필터 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-223">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="65a63-224">예를 들어 다운로드 예제에서 `MySampleActionFilter`은 시작할 때 전역적으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-224">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="65a63-225">`TestController`는:</span><span class="sxs-lookup"><span data-stu-id="65a63-225">The `TestController`:</span></span>

* <span data-ttu-id="65a63-226">`SampleActionFilterAttribute`(`[SampleActionFilter]`)를 `FilterTest2` 작업에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-226">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="65a63-227">`OnActionExecuting` 및 `OnActionExecuted`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-227">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="65a63-228">`https://localhost:5001/Test2/FilterTest2`로 이동하면 다음 코드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-228">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="65a63-229">컨트롤러 수준 필터는 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 속성을 `int.MinValue`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-229">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="65a63-230">컨트롤러 수준 필터는 메서드에 적용된 후에 실행되도록 설정할 수 **없습니다**.</span><span class="sxs-lookup"><span data-stu-id="65a63-230">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="65a63-231">순서는 다음 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-231">Order is explained in the next section.</span></span>

<span data-ttu-id="65a63-232">Razor Pages에 대해서는 [필터 메서드를 재정의하여 Razor 페이지 필터 구현](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-232">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="65a63-233">기본 순서 재정의</span><span class="sxs-lookup"><span data-stu-id="65a63-233">Overriding the default order</span></span>

<span data-ttu-id="65a63-234"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>를 구현하여 실행의 기본 순서를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-234">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="65a63-235">`IOrderedFilter`은 실행 순서를 결정하는 데 범위보다 우선 순위가 높은 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-235">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="65a63-236">낮은 `Order` 값을 가진 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-236">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="65a63-237">더 높은 `Order` 값을 가진 필터 이전에 *before* 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-237">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="65a63-238">더 높은 `Order` 값을 가진 필터 이후에 *after* 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-238">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="65a63-239">`Order` 속성은 생성자 매개 변수를 사용하여 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-239">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="65a63-240">다음 컨트롤러의 두 작업 필터를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-240">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="65a63-241">전역 필터는 `StartUp.ConfigureServices`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-241">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="65a63-242">3개의 필터는 다음 순서로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-242">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="65a63-243">`Order` 속성은 필터가 실행되는 순서를 결정할 때 범위를 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-243">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="65a63-244">필터는 먼저 순서에 따라 정렬된 다음, 순서가 동일할 경우 범위가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-244">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="65a63-245">모든 기본 제공 필터는 `IOrderedFilter`을 구현하고 기본 `Order` 값을 0으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-245">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="65a63-246">앞서 언급했듯이 컨트롤러 수준 필터는 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 속성을 `int.MinValue`로 설정합니다. 기본 제공 필터의 경우 `Order`가 0이 아닌 값으로 설정되지 않은 한 범위가 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-246">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="65a63-247">이전 코드에서 `MySampleActionFilter`는 전역 범위를 가지므로 컨트롤러 범위를 포함하는 `MyAction2FilterAttribute` 전에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-247">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="65a63-248">`MyAction2FilterAttribute`를 먼저 실행하려면 순서를 `int.MinValue`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-248">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="65a63-249">전역 필터 `MySampleActionFilter`를 먼저 실행하려면 `Order`를 `int.MinValue`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-249">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="65a63-250">취소 및 단락</span><span class="sxs-lookup"><span data-stu-id="65a63-250">Cancellation and short-circuiting</span></span>

<span data-ttu-id="65a63-251">필터 메서드에 제공되는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 매개 변수의 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 속성을 설정하여 필터 파이프라인을 단락시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-251">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="65a63-252">예를 들어 다음 리소스 필터는 파이프라인의 나머지 부분이 실행되지 않도록 막습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-252">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="65a63-253">다음 코드에서 `ShortCircuitingResourceFilter` 및 `AddHeader` 필터는 `SomeResource` 작업 메서드를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-253">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="65a63-254">`ShortCircuitingResourceFilter`는:</span><span class="sxs-lookup"><span data-stu-id="65a63-254">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="65a63-255">리소스 필터이고 `AddHeader`는 작업 필터이기 때문에 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-255">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="65a63-256">나머지 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-256">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="65a63-257">따라서 `AddHeader` 필터는 `SomeResource` 작업에 대해 절대 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-257">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="65a63-258">이 동작은 `ShortCircuitingResourceFilter`가 먼저 실행되기 때문에 작업 메서드 수준에서 두 필터를 적용하더라도 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-258">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="65a63-259">해당 필터 형식 또는 `Order` 속성의 명시적 사용으로 인해 `ShortCircuitingResourceFilter`가 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-259">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="65a63-260">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="65a63-260">Dependency injection</span></span>

<span data-ttu-id="65a63-261">형식별 또는 인스턴스별 필터를 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-261">Filters can be added by type or by instance.</span></span> <span data-ttu-id="65a63-262">인스턴스가 추가되면 모든 요청에 해당 인스턴스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-262">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="65a63-263">형식이 추가되면 해당 필터는 형식으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-263">If a type is added, it's type-activated.</span></span> <span data-ttu-id="65a63-264">형식으로 활성화된 필터는 다음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-264">A type-activated filter means:</span></span>

* <span data-ttu-id="65a63-265">각 요청에 대해 인스턴스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-265">An instance is created for each request.</span></span>
* <span data-ttu-id="65a63-266">모든 생성자 종속성이 DI([종속성 주입](xref:fundamentals/dependency-injection))를 통해서 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-266">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="65a63-267">특성으로 구현되고 컨트롤러 클래스 또는 작업 메서드에 직접 추가된 필터는 DI([종속성 주입](xref:fundamentals/dependency-injection))에서 제공하는 생성자 종속성을 가질 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-267">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="65a63-268">DI는 다음과 같은 이유로 생성자 종속성을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-268">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="65a63-269">특성이 적용될 때 해당 생성자 매개 변수가 제공되어야 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-269">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="65a63-270">특성이 작동하는 방법의 제한 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-270">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="65a63-271">다음 필터는 DI에서 제공하는 생성자 종속성을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-271">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="65a63-272">특성에서 구현된 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="65a63-272"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="65a63-273">위의 필터는 컨트롤러 또는 작업 메서드에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-273">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="65a63-274">로거는 DI를 통해서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-274">Loggers are available from DI.</span></span> <span data-ttu-id="65a63-275">그러나 로깅 용도로만 필터를 만들거나 사용하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-275">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="65a63-276">[기본 제공 프레임워크 로깅](xref:fundamentals/logging/index)은 일반적으로 로깅에 필요한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-276">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="65a63-277">필터에 추가된 로깅은:</span><span class="sxs-lookup"><span data-stu-id="65a63-277">Logging added to filters:</span></span>

* <span data-ttu-id="65a63-278">비즈니스 도메인 문제 또는 필터 고유의 동작에 중점을 두어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-278">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="65a63-279">작업 또는 다른 프레임워크 이벤트를 로깅해서는 **안 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="65a63-279">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="65a63-280">기본 제공 필터는 작업 및 프레임워크 이벤트를 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-280">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="65a63-281">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="65a63-281">ServiceFilterAttribute</span></span>

<span data-ttu-id="65a63-282">서비스 필터 구현 형식은 `ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-282">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="65a63-283"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 DI에서 필터의 인스턴스를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-283">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="65a63-284">다음 코드는 `AddHeaderResultServiceFilter`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-284">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="65a63-285">다음 코드에서 `AddHeaderResultServiceFilter`는 DI 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-285">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="65a63-286">다음 코드에서 `ServiceFilter` 특성은 DI에서 `AddHeaderResultServiceFilter` 필터의 인스턴스를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-286">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="65a63-287">`ServiceFilterAttribute`를 사용할 때 [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="65a63-287">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="65a63-288">필터 인스턴스가 원래 생성된 요청 범위 외부에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-288">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="65a63-289">ASP.NET Core 런타임은 다음을 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-289">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="65a63-290">필터의 단일 인스턴스 생성.</span><span class="sxs-lookup"><span data-stu-id="65a63-290">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="65a63-291">나중에 DI 컨테이너에서 필터가 다시 요청되지 않음.</span><span class="sxs-lookup"><span data-stu-id="65a63-291">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="65a63-292">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-292">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="65a63-293"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-293"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="65a63-294">`IFilterFactory`는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-294">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="65a63-295">`CreateInstance`는 DI에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-295">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="65a63-296">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="65a63-296">TypeFilterAttribute</span></span>

<span data-ttu-id="65a63-297"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>와 비슷하지만 해당 형식은 DI 컨테이너에서 직접 해결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-297"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="65a63-298"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>를 사용하여 형식을 인스턴스화합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-298">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="65a63-299">`TypeFilterAttribute` 형식은 DI 컨테이너에서 직접 해결되지 않기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-299">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="65a63-300">`TypeFilterAttribute`을 사용하여 참조되는 형식은 DI 컨테이너를 사용하여 등록할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-300">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="65a63-301">DI 컨테이너에서 충족하는 종속성을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-301">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="65a63-302">`TypeFilterAttribute`는 형식에 대한 생성자 인수를 필요에 따라 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-302">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="65a63-303">`TypeFilterAttribute`를 사용할 때 [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="65a63-303">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="65a63-304">필터 인스턴스가 원래 생성된 요청 범위 밖에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-304">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="65a63-305">ASP.NET Core 런타임은 단일 필터 인스턴스가 생성되도록 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-305">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="65a63-306">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-306">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="65a63-307">다음 예제는 `TypeFilterAttribute`를 사용하여 형식에 인수를 전달하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-307">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="65a63-308">권한 부여 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-308">Authorization filters</span></span>

<span data-ttu-id="65a63-309">권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-309">Authorization filters:</span></span>

* <span data-ttu-id="65a63-310">필터 파이프라인에서 첫 번째로 실행되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-310">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="65a63-311">작업 메서드에 대한 액세스를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-311">Control access to action methods.</span></span>
* <span data-ttu-id="65a63-312">before 메서드는 있지만 after 메서드는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-312">Have a before method, but no after method.</span></span>

<span data-ttu-id="65a63-313">사용자 지정 권한 부여 필터는 사용자 지정 권한 부여 프레임워크를 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-313">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="65a63-314">사용자 지정 필터를 작성하는 대신 권한 부여 정책을 구성하거나 사용자 지정 권한 부여 정책을 작성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-314">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="65a63-315">기본 제공 권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-315">The built-in authorization filter:</span></span>

* <span data-ttu-id="65a63-316">권한 부여 시스템을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-316">Calls the authorization system.</span></span>
* <span data-ttu-id="65a63-317">요청을 승인하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-317">Does not authorize requests.</span></span>

<span data-ttu-id="65a63-318">권한 부여 필터 내에서 예외를 던지지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="65a63-318">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="65a63-319">해당 예외는 처리되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-319">The exception will not be handled.</span></span>
* <span data-ttu-id="65a63-320">예외 필터가 해당 예외를 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-320">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="65a63-321">권한 부여 필터에서 예외가 발생할 경우 챌린지 발행을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-321">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="65a63-322">[권한 부여](xref:security/authorization/introduction)에 대해 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-322">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="65a63-323">리소스 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-323">Resource filters</span></span>

<span data-ttu-id="65a63-324">리소스 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-324">Resource filters:</span></span>

* <span data-ttu-id="65a63-325"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-325">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="65a63-326">실행이 필터 파이프라인 대부분을 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-326">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="65a63-327">[권한 부여 필터](#authorization-filters)만 리소스 필터 이전에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-327">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="65a63-328">리소스 필터는 파이프라인의 대부분을 단락시켜야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-328">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="65a63-329">예를 들어 캐싱 필터는 캐시 적중 시 파이프라인의 나머지 부분을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-329">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="65a63-330">리소스 필터 예제:</span><span class="sxs-lookup"><span data-stu-id="65a63-330">Resource filter examples:</span></span>

* <span data-ttu-id="65a63-331">이전에 살펴본 [단락 리소스 필터](#short-circuiting-resource-filter).</span><span class="sxs-lookup"><span data-stu-id="65a63-331">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="65a63-332">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="65a63-332">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="65a63-333">모델 바인딩이 양식 데이터에 액세스하는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-333">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="65a63-334">대용량 파일 업로드에서 양식 데이터를 메모리로 읽어들이는 것을 방지하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-334">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="65a63-335">작업 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-335">Action filters</span></span>

<span data-ttu-id="65a63-336">작업 필터는 Razor Pages에 **적용되지 않습니다**.</span><span class="sxs-lookup"><span data-stu-id="65a63-336">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="65a63-337">Razor Pages는 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-337">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="65a63-338">자세한 내용은 [Razor 페이지에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-338">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="65a63-339">작업 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-339">Action filters:</span></span>

* <span data-ttu-id="65a63-340"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-340">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="65a63-341">실행이 작업 메서드의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-341">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="65a63-342">다음 코드는 예제 작업 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-342">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="65a63-343"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>는 다음과 같은 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-343">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="65a63-344"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 작업 메서드에 대한 입력을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-344"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="65a63-345"><xref:Microsoft.AspNetCore.Mvc.Controller> - 컨트롤러 인스턴스를 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-345"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="65a63-346"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` 설정은 작업 메서드 및 후속 작업 필터의 실행을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-346"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="65a63-347">작업 메서드에서 예외를 던지면:</span><span class="sxs-lookup"><span data-stu-id="65a63-347">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="65a63-348">후속 필터의 실행을 막습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-348">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="65a63-349">`Result` 설정과 달리 성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-349">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="65a63-350"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>는 `Controller` 및 `Result`에 더하여 다음과 같은 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-350">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="65a63-351"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 작업 실행이 다른 필터에 의해 단락된 경우 true입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-351"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="65a63-352"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 작업 또는 이전에 실행된 작업 필터에서 예외가 던져진 경우 null이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-352"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="65a63-353">이 속성을 null로 설정하면:</span><span class="sxs-lookup"><span data-stu-id="65a63-353">Setting this property to null:</span></span>

  * <span data-ttu-id="65a63-354">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-354">Effectively handles the exception.</span></span>
  * <span data-ttu-id="65a63-355">`Result`는 작업 메서드에서 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-355">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="65a63-356">`IAsyncActionFilter`의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>에 대한 호출은:</span><span class="sxs-lookup"><span data-stu-id="65a63-356">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="65a63-357">모든 후속 작업 필터 및 작업 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-357">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="65a63-358">`ActionExecutedContext`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-358">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="65a63-359">단락시키려면 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName>을 결과 인스턴스에 할당하고 `next`(`ActionExecutionDelegate`)를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-359">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="65a63-360">프레임워크는 서브클래싱 할 수 있는 추상 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-360">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="65a63-361">`OnActionExecuting` 작업 필터는 다음에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-361">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="65a63-362">모델 상태의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-362">Validate model state.</span></span>
* <span data-ttu-id="65a63-363">상태가 유효하지 않은 경우 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-363">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="65a63-364">`OnActionExecuted` 메서드는 작업 메서드 이후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-364">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="65a63-365">그리고 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 속성을 통해 작업 결과를 확인하고 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-365">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="65a63-366"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>는 작업 실행이 다른 필터에 의해 단락된 경우 true로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-366"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="65a63-367"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>은 작업 또는 후속 작업 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-367"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="65a63-368">`Exception`을 null로 설정하면:</span><span class="sxs-lookup"><span data-stu-id="65a63-368">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="65a63-369">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-369">Effectively handles an exception.</span></span>
  * <span data-ttu-id="65a63-370">`ActionExecutedContext.Result`는 작업 메서드에서 정상적으로 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-370">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="65a63-371">예외 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-371">Exception filters</span></span>

<span data-ttu-id="65a63-372">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-372">Exception filters:</span></span>

* <span data-ttu-id="65a63-373"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-373">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="65a63-374">공통 오류 처리 정책을 구현하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-374">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="65a63-375">다음 예제 예외 필터는 사용자 지정 오류 보기를 사용하여 앱을 개발 중인 경우에 발생하는 예외에 대한 세부 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-375">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="65a63-376">다음 코드에서는 예외 필터를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-376">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="65a63-377">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-377">Exception filters:</span></span>

* <span data-ttu-id="65a63-378">before 및 after 이벤트가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-378">Don't have before and after events.</span></span>
* <span data-ttu-id="65a63-379"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="65a63-380">Razor Page 또는 컨트롤러 생성, [모델 바인딩](xref:mvc/models/model-binding), 작업 필터 또는 작업 메서드에서 발생하는 처리되지 않은 예외를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-380">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="65a63-381">리소스 필터, 결과 필터 또는 MVC 결과 실행에서 발생하는 예외를 잡지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="65a63-381">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="65a63-382">예외를 처리하려면 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 속성을 `true`로 설정하거나 응답을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-382">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="65a63-383">그러면 예외가 전파되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-383">This stops propagation of the exception.</span></span> <span data-ttu-id="65a63-384">예외 필터는 예외를 “성공”으로 변환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-384">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="65a63-385">이는 작업 필터에서만 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-385">Only an action filter can do that.</span></span>

<span data-ttu-id="65a63-386">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-386">Exception filters:</span></span>

* <span data-ttu-id="65a63-387">작업 내에서 발생하는 예외를 잡는 데 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-387">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="65a63-388">오류 처리 미들웨어만큼 유연하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-388">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="65a63-389">예외 처리의 경우 미들웨어를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-389">Prefer middleware for exception handling.</span></span> <span data-ttu-id="65a63-390">어떤 작업 메서드가 호출되는지에 따라 오류 처리 방식이 ‘다른’ 경우에만 예외 필터를 사용합니다. </span><span class="sxs-lookup"><span data-stu-id="65a63-390">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="65a63-391">예를 들어 앱에는 API 엔드포인트 및 보기/HTML 모두에 대한 작업 메서드가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-391">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="65a63-392">API 엔드포인트는 JSON으로 오류 정보를 반환할 수 있습니다. 반면 보기 기반 작업은 HTML로 오류 페이지를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-392">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="65a63-393">결과 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-393">Result filters</span></span>

<span data-ttu-id="65a63-394">결과 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-394">Result filters:</span></span>

* <span data-ttu-id="65a63-395">다음 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-395">Implement an interface:</span></span>
  * <span data-ttu-id="65a63-396"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="65a63-396"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="65a63-397"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="65a63-397"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="65a63-398">실행이 작업 결과의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-398">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="65a63-399">IResultFilter 및 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="65a63-399">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="65a63-400">다음 코드는 HTTP 헤더를 추가하는 결과 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-400">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="65a63-401">실행되는 결과의 종류는 작업에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-401">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="65a63-402">보기를 반환하는 작업에는 실행 중인 <xref:Microsoft.AspNetCore.Mvc.ViewResult>의 일부로 모든 Razor 프로세스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-402">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="65a63-403">API 메서드는 결과 실행의 일부로 어떤 직렬화를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-403">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="65a63-404">[작업 결과](xref:mvc/controllers/actions)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-404">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="65a63-405">결과 필터는 작업 또는 작업 필터가 작업 결과를 생성하는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-405">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="65a63-406">다음 경우에는 결과 필터가 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-406">Result filters are not executed when:</span></span>

* <span data-ttu-id="65a63-407">권한 부여 필터 또는 리소스 필터가 파이프라인을 단락시킬 경우.</span><span class="sxs-lookup"><span data-stu-id="65a63-407">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="65a63-408">예외 필터가 작업 결과를 생성하여 예외를 처리하는 경우.</span><span class="sxs-lookup"><span data-stu-id="65a63-408">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="65a63-409"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 메서드는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName>을 `true`로 설정하여 작업 결과 및 후속 결과 필터를 단락시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-409">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="65a63-410">단락시킬 경우 빈 응답을 생성하지 않도록 응답 개체에 작성하세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-410">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="65a63-411">`IResultFilter.OnResultExecuting`에서 예외 throw:</span><span class="sxs-lookup"><span data-stu-id="65a63-411">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="65a63-412">작업 결과 및 후속 필터의 실행을 막습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-412">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="65a63-413">성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-413">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="65a63-414"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 메서드가 실행될 때에는 응답이 이미 클라이언트에 전송되었을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-414">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="65a63-415">이미 클라이언트에 전송된 응답은 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-415">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="65a63-416">`ResultExecutedContext.Canceled`는 작업 결과 실행이 다른 필터에 의해 단락된 경우 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-416">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="65a63-417">`ResultExecutedContext.Exception`은 작업 결과 또는 후속 결과 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-417">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="65a63-418">`Exception`을 효과적으로 null로 설정하면 예외를 '처리'하고 예외가 파이프라인의 뒷부분에서 다시 throw되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-418">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="65a63-419">결과 필터에서 예외를 처리하는 경우 데이터를 응답에 쓸 수 있는 신뢰할 수 있는 방법이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-419">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="65a63-420">작업 결과가 예외를 던질 때 헤더가 클라이언트에 플러시된 경우 오류 코드를 전송하기 위한 신뢰할 수 있는 메커니즘이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-420">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="65a63-421"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate>의 `await next` 호출은 후속 결과 필터 및 작업 결과를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-421">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="65a63-422">단락시키려면 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel)을 `true`로 설정하고 `ResultExecutionDelegate`를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-422">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="65a63-423">프레임워크는 서브클래싱 할 수 있는 추상 `ResultFilterAttribute`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-423">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="65a63-424">이전에 표시된 [AddHeaderAttribute](#add-header-attribute) 클래스는 결과 필터 특성의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-424">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="65a63-425">IAlwaysRunResultFilter 및 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="65a63-425">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="65a63-426"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 인터페이스는 모든 작업 결과에 대해 실행되는 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 구현을 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-426">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="65a63-427">여기에는 다음에 의해 생성되는 작업 결과가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-427">This includes action results produced by:</span></span>

* <span data-ttu-id="65a63-428">단락하는 권한 부여 필터 및 리소스 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-428">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="65a63-429">예외 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-429">Exception filters.</span></span>

<span data-ttu-id="65a63-430">예를 들어 다음 필터는 항상 실행되어 콘텐츠 협상이 실패할 경우 작업 결과(<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)를 *422 Unprocessable Entity* 상태 코드로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-430">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="65a63-431">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="65a63-431">IFilterFactory</span></span>

<span data-ttu-id="65a63-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="65a63-433">따라서 필터 파이프라인 어디에서나 `IFilterFactory` 인스턴스를 `IFilterMetadata` 인스턴스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-433">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="65a63-434">런타임이 필터를 호출하려고 준비할 때 `IFilterFactory`로 캐스팅을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-434">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="65a63-435">해당 캐스팅에 성공하면 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 호출하여 호출되는 `IFilterMetadata` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-435">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="65a63-436">앱이 시작될 때 정확한 필터 파이프라인을 명시적으로 설정할 필요가 없으므로 유연한 디자인을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-436">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="65a63-437">필터를 만드는 다른 방법으로 사용자 지정 특성 구현을 사용하여 `IFilterFactory`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-437">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="65a63-438">필터는 다음 코드에서 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-438">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="65a63-439">[샘플 다운로드 샘플](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)를 실행하여 이전 코드를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-439">Test the preceding code by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="65a63-440">F12 개발자 도구를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-440">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="65a63-441">`https://localhost:5001/Sample/HeaderWithFactory`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-441">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="65a63-442">F12 개발자 도구는 예제 코드에 의해 추가된 다음 응답 헤더를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-442">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="65a63-443">**author:** `Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="65a63-443">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="65a63-444">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="65a63-444">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="65a63-445">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="65a63-445">**internal:** `My header`</span></span>

<span data-ttu-id="65a63-446">이전 코드에서는 **internal:** `My header` 응답 헤더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-446">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="65a63-447">특성에서 구현된 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="65a63-447">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="65a63-448">`IFilterFactory`를 구현하는 필터는 다음과 같은 필터에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-448">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="65a63-449">매개 변수 전달을 필요로 하지 않는 필터.</span><span class="sxs-lookup"><span data-stu-id="65a63-449">Don't require passing parameters.</span></span>
* <span data-ttu-id="65a63-450">DI에 의해 채워져야 할 생성자 종속성이 있는 필터.</span><span class="sxs-lookup"><span data-stu-id="65a63-450">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="65a63-451"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-451"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="65a63-452">`IFilterFactory`는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-452">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="65a63-453">`CreateInstance`는 서비스 컨테이너(DI)에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-453">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="65a63-454">다음 코드는 `[SampleActionFilter]`을 적용하는 세 가지 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-454">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="65a63-455">이전 코드에서 `[SampleActionFilter]`로 메서드를 장식하는 것이 `SampleActionFilter`을 적용하는 데 선호되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-455">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="65a63-456">필터 파이프라인에서 미들웨어 사용</span><span class="sxs-lookup"><span data-stu-id="65a63-456">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="65a63-457">리소스 필터는 파이프라인의 뒷부분에 제공되는 모든 실행을 둘러싼다는 점에서 [미들웨어](xref:fundamentals/middleware/index)처럼 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-457">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="65a63-458">하지만 필터는 런타임의 일부라는 점에서 미들웨어와 다릅니다. 즉, 컨텍스트 및 구문에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-458">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="65a63-459">미들웨어를 필터로 사용하려면 필터 파이프라인에 삽입할 미들웨어를 지정하는 `Configure` 메서드를 포함한 형식을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-459">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="65a63-460">다음 예제는 지역화 미들웨어를 사용하여 요청에 대한 현재 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-460">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="65a63-461"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>를 사용하여 미들웨어를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-461">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="65a63-462">미들웨어 필터는 모델 바인딩 이전 및 나머지 파이프라인 이후에 리소스 필터와 동일한 필터 파이프라인 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-462">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="65a63-463">다음 작업</span><span class="sxs-lookup"><span data-stu-id="65a63-463">Next actions</span></span>

* <span data-ttu-id="65a63-464">[Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-464">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="65a63-465">필터를 실험하려면 [GitHub 예제를 다운로드하고, 테스트하고, 수정](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-465">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="65a63-466">작성자: [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="65a63-466">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="65a63-467">ASP.NET Core에서 *필터*를 사용하면 요청 처리 파이프라인의 특정 단계 전후에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-467">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="65a63-468">기본 제공 필터는 다음과 같은 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-468">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="65a63-469">권한 부여(사용자가 권한이 없는 리소스에 액세스하는 것을 방지).</span><span class="sxs-lookup"><span data-stu-id="65a63-469">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="65a63-470">응답 캐싱(요청 파이프라인을 단락하여 캐시된 응답 반환).</span><span class="sxs-lookup"><span data-stu-id="65a63-470">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="65a63-471">사용자 지정 필터를 만들어 횡단 관심사를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-471">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="65a63-472">횡단 관심사의 사례로는 오류 처리, 캐싱, 구성, 권한 부여 및 로깅을 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-472">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="65a63-473">필터는 코드 중복을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-473">Filters avoid duplicating code.</span></span> <span data-ttu-id="65a63-474">예를 들어 오류 처리 예외 필터는 오류 처리를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-474">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="65a63-475">이 문서는 Razor Pages, API 컨트롤러 및 보기를 사용하는 컨트롤러에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-475">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="65a63-476">[예제 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="65a63-476">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="65a63-477">필터 작동 방법</span><span class="sxs-lookup"><span data-stu-id="65a63-477">How filters work</span></span>

<span data-ttu-id="65a63-478">필터는 ‘필터 파이프라인’이라고도 하는 ‘ASP.NET Core 동작 호출 파이프라인’내에서 실행됩니다.  </span><span class="sxs-lookup"><span data-stu-id="65a63-478">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="65a63-479">필터 파이프라인은 ASP.NET Core가 실행할 작업을 선택한 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-479">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![요청은 기타 미들웨어, 라우팅 미들웨어, 작업 선택 및 ASP.NET Core 작업 호출 파이프라인을 통해서 처리됩니다.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="65a63-482">필터 형식</span><span class="sxs-lookup"><span data-stu-id="65a63-482">Filter types</span></span>

<span data-ttu-id="65a63-483">각 필터 형식은 필터 파이프라인의 다른 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-483">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="65a63-484">[권한 부여 필터](#authorization-filters)는 가장 먼저 실행되고 사용자가 요청에 대한 권한이 있는지 여부를 결정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-484">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="65a63-485">권한 부여 필터는 요청이 인증되지 않는 경우 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-485">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="65a63-486">[리소스 필터](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="65a63-486">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="65a63-487">권한 부여 후 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-487">Run after authorization.</span></span>  
  * <span data-ttu-id="65a63-488"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>는 나머지 필터 파이프라인보다 먼저 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-488"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="65a63-489">예를 들어 `OnResourceExecuting`는 모델 바인딩 전에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-489">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="65a63-490"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>는 파이프라인의 나머지 부분이 완료된 후에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-490"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="65a63-491">[작업 필터](#action-filters)는 개별 작업 메서드가 호출되는 전후에 즉시 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-491">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="65a63-492">작업에 전달된 인수 및 작업에서 반환된 결과를 조작하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-492">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="65a63-493">Razor Pages에서는 작업 필터가 지원되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="65a63-493">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="65a63-494">[예외 필터](#exception-filters)는 응답 본문에 무언가 쓰여지기 전에 발생한 처리되지 않은 예외에 대한 전역 정책을 적용하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-494">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="65a63-495">[결과 필터](#result-filters)는 개별 작업 결과의 실행 전후에 즉시 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-495">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="65a63-496">작업 메서드가 성공적으로 실행되는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-496">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="65a63-497">보기 또는 포맷터 실행을 둘러싸야 하는 논리에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-497">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="65a63-498">다음 다이어그램은 필터 형식이 필터 파이프라인에서 상호 작용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-498">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![요청은 권한 부여 필터, 리소스 필터, 모델 바인딩, 작업 필터, 작업 실행 및 작업 결과 변환, 예외 필터, 결과 필터 및 결과 실행을 통해 처리됩니다.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="65a63-501">구현</span><span class="sxs-lookup"><span data-stu-id="65a63-501">Implementation</span></span>

<span data-ttu-id="65a63-502">필터는 서로 다른 인터페이스 정의를 통해 동기 및 비동기 구현을 모두 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-502">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="65a63-503">동기 필터는 해당 파이프라인 단계 전(`On-Stage-Executing`)과 후(`On-Stage-Executed`)에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-503">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="65a63-504">예를 들어 `OnActionExecuting`는 작업 메서드가 호출되기 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-504">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="65a63-505">`OnActionExecuted`는 작업 메서드가 반환된 후 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-505">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="65a63-506">비동기 필터는 `On-Stage-ExecutionAsync` 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-506">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="65a63-507">이전 코드에서 `SampleAsyncActionFilter`에는 작업 메서드를 실행하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>(`next`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-507">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="65a63-508">각 `On-Stage-ExecutionAsync` 메서드는 필터의 파이프라인 단계를 실행하는 `FilterType-ExecutionDelegate`를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-508">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="65a63-509">여러 필터 단계</span><span class="sxs-lookup"><span data-stu-id="65a63-509">Multiple filter stages</span></span>

<span data-ttu-id="65a63-510">단일 클래스에서 여러 필터 단계에 대한 인터페이스를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-510">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="65a63-511">예를 들어 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 클래스는 `IActionFilter`, `IResultFilter` 및 해당 비동기 항목을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-511">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="65a63-512">필터 인터페이스의 동기 또는 비동기 버전을 모두 구현하지 **말고** 그 중 **한 가지**만 구현하세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-512">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="65a63-513">런타임은 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-513">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="65a63-514">그렇지 않으면 동기 인터페이스의 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-514">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="65a63-515">비동기 및 동기 인터페이스가 모두 하나의 클래스에 구현된 경우에는 비동기 메서드만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-515">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="65a63-516"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 같은 추상 클래스를 사용하는 경우 각 필터 형식에 대한 동기 메서드 또는 비동기 메서드만 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-516">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="65a63-517">기본 제공 필터 특성</span><span class="sxs-lookup"><span data-stu-id="65a63-517">Built-in filter attributes</span></span>

<span data-ttu-id="65a63-518">ASP.NET Core에는 서브클래싱 및 사용자 지정할 수 있는 기본 제공 특성 기반 필터가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-518">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="65a63-519">예를 들어 다음 결과 필터는 응답에 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-519">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="65a63-520">특성을 사용하면 이전 예제와 같이 필터에서 인수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-520">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="65a63-521">컨트롤러나 작업 메서드에 `AddHeaderAttribute`를 적용하고 HTTP 헤더의 이름 및 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-521">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="65a63-522">여러 필터 인터페이스는 사용자 지정 구현을 위한 기본 클래스로 사용할 수 있는 상응하는 특성을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-522">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="65a63-523">필터 특성:</span><span class="sxs-lookup"><span data-stu-id="65a63-523">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="65a63-524">필터 범위 및 실행 순서</span><span class="sxs-lookup"><span data-stu-id="65a63-524">Filter scopes and order of execution</span></span>

<span data-ttu-id="65a63-525">세 가지 *범위* 중 하나에서 필터를 파이프라인에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-525">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="65a63-526">작업에서 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="65a63-526">Using an attribute on an action.</span></span>
* <span data-ttu-id="65a63-527">컨트롤러에서 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="65a63-527">Using an attribute on a controller.</span></span>
* <span data-ttu-id="65a63-528">다음 코드와 같이 모든 컨트롤러와 작업에 전역으로 사용:</span><span class="sxs-lookup"><span data-stu-id="65a63-528">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="65a63-529">이전 코드는 [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) 컬렉션을 사용하여 3개의 필터를 전역적으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-529">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="65a63-530">기본 실행 순서</span><span class="sxs-lookup"><span data-stu-id="65a63-530">Default order of execution</span></span>

<span data-ttu-id="65a63-531">*동일한 유형*의 필터가 여러 개 있는 경우 범위가 필터 실행의 기본 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-531">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="65a63-532">전역 필터는 클래스 필터를 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-532">Global filters surround class filters.</span></span> <span data-ttu-id="65a63-533">클래스 필터는 메서드 필터를 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-533">Class filters surround method filters.</span></span>

<span data-ttu-id="65a63-534">필터 중첩의 결과로 필터의 *after* 코드는 *before* 코드의 역순으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-534">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="65a63-535">필터의 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-535">The filter sequence:</span></span>

* <span data-ttu-id="65a63-536">전역 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-536">The *before* code of global filters.</span></span>
  * <span data-ttu-id="65a63-537">컨트롤러 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-537">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="65a63-538">작업 메서드 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-538">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="65a63-539">작업 메서드 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-539">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="65a63-540">컨트롤러 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-540">The *after* code of controller filters.</span></span>
* <span data-ttu-id="65a63-541">전역 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-541">The *after* code of global filters.</span></span>
  
<span data-ttu-id="65a63-542">다음 예제는 필터 메서드가 동기 작업 필터에 대해 호출되는 순서를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-542">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="65a63-543">순서</span><span class="sxs-lookup"><span data-stu-id="65a63-543">Sequence</span></span> | <span data-ttu-id="65a63-544">필터 범위</span><span class="sxs-lookup"><span data-stu-id="65a63-544">Filter scope</span></span> | <span data-ttu-id="65a63-545">필터 메서드</span><span class="sxs-lookup"><span data-stu-id="65a63-545">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="65a63-546">1</span><span class="sxs-lookup"><span data-stu-id="65a63-546">1</span></span> | <span data-ttu-id="65a63-547">전역</span><span class="sxs-lookup"><span data-stu-id="65a63-547">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="65a63-548">2</span><span class="sxs-lookup"><span data-stu-id="65a63-548">2</span></span> | <span data-ttu-id="65a63-549">Controller</span><span class="sxs-lookup"><span data-stu-id="65a63-549">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="65a63-550">3</span><span class="sxs-lookup"><span data-stu-id="65a63-550">3</span></span> | <span data-ttu-id="65a63-551">메서드</span><span class="sxs-lookup"><span data-stu-id="65a63-551">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="65a63-552">4</span><span class="sxs-lookup"><span data-stu-id="65a63-552">4</span></span> | <span data-ttu-id="65a63-553">메서드</span><span class="sxs-lookup"><span data-stu-id="65a63-553">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="65a63-554">5</span><span class="sxs-lookup"><span data-stu-id="65a63-554">5</span></span> | <span data-ttu-id="65a63-555">Controller</span><span class="sxs-lookup"><span data-stu-id="65a63-555">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="65a63-556">6</span><span class="sxs-lookup"><span data-stu-id="65a63-556">6</span></span> | <span data-ttu-id="65a63-557">전역</span><span class="sxs-lookup"><span data-stu-id="65a63-557">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="65a63-558">이 순서는 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-558">This sequence shows:</span></span>

* <span data-ttu-id="65a63-559">메서드 필터는 컨트롤러 필터 내에서 중첩됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-559">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="65a63-560">컨트롤러 필터는 전역 필터 내에서 중첩됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-560">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="65a63-561">컨트롤러 및 Razor Page 수준 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-561">Controller and Razor Page level filters</span></span>

<span data-ttu-id="65a63-562"><xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스에서 상속되는 모든 컨트롤러에는 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 및 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 메서드가 포함됩니다. 이러한 메서드는:</span><span class="sxs-lookup"><span data-stu-id="65a63-562">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="65a63-563">다음 메서드는</span><span class="sxs-lookup"><span data-stu-id="65a63-563">These methods:</span></span>

* <span data-ttu-id="65a63-564">지정된 작업을 위해 실행되는 필터를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-564">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="65a63-565">`OnActionExecuting`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-565">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="65a63-566">`OnActionExecuted`는 모든 작업 필터 이후에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-566">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="65a63-567">`OnActionExecutionAsync`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-567">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="65a63-568">작업 메서드 이후 `next`가 실행된 후의 필터 코드.</span><span class="sxs-lookup"><span data-stu-id="65a63-568">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="65a63-569">예를 들어 다운로드 예제에서 `MySampleActionFilter`은 시작할 때 전역적으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-569">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="65a63-570">`TestController`는:</span><span class="sxs-lookup"><span data-stu-id="65a63-570">The `TestController`:</span></span>

* <span data-ttu-id="65a63-571">`SampleActionFilterAttribute`(`[SampleActionFilter]`)를 `FilterTest2` 작업에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-571">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="65a63-572">`OnActionExecuting` 및 `OnActionExecuted`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-572">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="65a63-573">`https://localhost:5001/Test/FilterTest2`로 이동하면 다음 코드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-573">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="65a63-574">Razor Pages에 대해서는 [필터 메서드를 재정의하여 Razor 페이지 필터 구현](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-574">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="65a63-575">기본 순서 재정의</span><span class="sxs-lookup"><span data-stu-id="65a63-575">Overriding the default order</span></span>

<span data-ttu-id="65a63-576"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>를 구현하여 실행의 기본 순서를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-576">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="65a63-577">`IOrderedFilter`은 실행 순서를 결정하는 데 범위보다 우선 순위가 높은 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-577">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="65a63-578">낮은 `Order` 값을 가진 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-578">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="65a63-579">더 높은 `Order` 값을 가진 필터 이전에 *before* 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-579">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="65a63-580">더 높은 `Order` 값을 가진 필터 이후에 *after* 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-580">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="65a63-581">생성자 매개 변수로 `Order` 속성을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-581">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="65a63-582">이전 예제와 동일한 3개의 작업 필터를 가정해보세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-582">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="65a63-583">컨트롤러와 전역 필터의 `Order` 속성을 각각 1과 2로 설정하면 실행 순서가 반대가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-583">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="65a63-584">순서</span><span class="sxs-lookup"><span data-stu-id="65a63-584">Sequence</span></span> | <span data-ttu-id="65a63-585">필터 범위</span><span class="sxs-lookup"><span data-stu-id="65a63-585">Filter scope</span></span> | <span data-ttu-id="65a63-586">`Order` 속성</span><span class="sxs-lookup"><span data-stu-id="65a63-586">`Order` property</span></span> | <span data-ttu-id="65a63-587">필터 메서드</span><span class="sxs-lookup"><span data-stu-id="65a63-587">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="65a63-588">1</span><span class="sxs-lookup"><span data-stu-id="65a63-588">1</span></span> | <span data-ttu-id="65a63-589">메서드</span><span class="sxs-lookup"><span data-stu-id="65a63-589">Method</span></span> | <span data-ttu-id="65a63-590">0</span><span class="sxs-lookup"><span data-stu-id="65a63-590">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="65a63-591">2</span><span class="sxs-lookup"><span data-stu-id="65a63-591">2</span></span> | <span data-ttu-id="65a63-592">Controller</span><span class="sxs-lookup"><span data-stu-id="65a63-592">Controller</span></span> | <span data-ttu-id="65a63-593">1</span><span class="sxs-lookup"><span data-stu-id="65a63-593">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="65a63-594">3</span><span class="sxs-lookup"><span data-stu-id="65a63-594">3</span></span> | <span data-ttu-id="65a63-595">전역</span><span class="sxs-lookup"><span data-stu-id="65a63-595">Global</span></span> | <span data-ttu-id="65a63-596">2</span><span class="sxs-lookup"><span data-stu-id="65a63-596">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="65a63-597">4</span><span class="sxs-lookup"><span data-stu-id="65a63-597">4</span></span> | <span data-ttu-id="65a63-598">전역</span><span class="sxs-lookup"><span data-stu-id="65a63-598">Global</span></span> | <span data-ttu-id="65a63-599">2</span><span class="sxs-lookup"><span data-stu-id="65a63-599">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="65a63-600">5</span><span class="sxs-lookup"><span data-stu-id="65a63-600">5</span></span> | <span data-ttu-id="65a63-601">Controller</span><span class="sxs-lookup"><span data-stu-id="65a63-601">Controller</span></span> | <span data-ttu-id="65a63-602">1</span><span class="sxs-lookup"><span data-stu-id="65a63-602">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="65a63-603">6</span><span class="sxs-lookup"><span data-stu-id="65a63-603">6</span></span> | <span data-ttu-id="65a63-604">메서드</span><span class="sxs-lookup"><span data-stu-id="65a63-604">Method</span></span> | <span data-ttu-id="65a63-605">0</span><span class="sxs-lookup"><span data-stu-id="65a63-605">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="65a63-606">`Order` 속성은 필터가 실행되는 순서를 결정할 때 범위를 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-606">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="65a63-607">필터는 먼저 순서에 따라 정렬된 다음, 순서가 동일할 경우 범위가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-607">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="65a63-608">모든 기본 제공 필터는 `IOrderedFilter`을 구현하고 기본 `Order` 값을 0으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-608">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="65a63-609">기본 제공 필터의 경우 `Order`를 0이 아닌 값으로 설정하지 않는 한 범위가 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-609">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="65a63-610">취소 및 단락</span><span class="sxs-lookup"><span data-stu-id="65a63-610">Cancellation and short-circuiting</span></span>

<span data-ttu-id="65a63-611">필터 메서드에 제공되는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 매개 변수의 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 속성을 설정하여 필터 파이프라인을 단락시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-611">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="65a63-612">예를 들어 다음 리소스 필터는 파이프라인의 나머지 부분이 실행되지 않도록 막습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-612">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="65a63-613">다음 코드에서 `ShortCircuitingResourceFilter` 및 `AddHeader` 필터는 `SomeResource` 작업 메서드를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-613">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="65a63-614">`ShortCircuitingResourceFilter`는:</span><span class="sxs-lookup"><span data-stu-id="65a63-614">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="65a63-615">리소스 필터이고 `AddHeader`는 작업 필터이기 때문에 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-615">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="65a63-616">나머지 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-616">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="65a63-617">따라서 `AddHeader` 필터는 `SomeResource` 작업에 대해 절대 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-617">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="65a63-618">이 동작은 `ShortCircuitingResourceFilter`가 먼저 실행되기 때문에 작업 메서드 수준에서 두 필터를 적용하더라도 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-618">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="65a63-619">해당 필터 형식 또는 `Order` 속성의 명시적 사용으로 인해 `ShortCircuitingResourceFilter`가 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-619">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="65a63-620">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="65a63-620">Dependency injection</span></span>

<span data-ttu-id="65a63-621">형식별 또는 인스턴스별 필터를 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-621">Filters can be added by type or by instance.</span></span> <span data-ttu-id="65a63-622">인스턴스가 추가되면 모든 요청에 해당 인스턴스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-622">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="65a63-623">형식이 추가되면 해당 필터는 형식으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-623">If a type is added, it's type-activated.</span></span> <span data-ttu-id="65a63-624">형식으로 활성화된 필터는 다음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-624">A type-activated filter means:</span></span>

* <span data-ttu-id="65a63-625">각 요청에 대해 인스턴스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-625">An instance is created for each request.</span></span>
* <span data-ttu-id="65a63-626">모든 생성자 종속성이 DI([종속성 주입](xref:fundamentals/dependency-injection))를 통해서 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-626">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="65a63-627">특성으로 구현되고 컨트롤러 클래스 또는 작업 메서드에 직접 추가된 필터는 DI([종속성 주입](xref:fundamentals/dependency-injection))에서 제공하는 생성자 종속성을 가질 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-627">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="65a63-628">DI는 다음과 같은 이유로 생성자 종속성을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-628">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="65a63-629">특성이 적용될 때 해당 생성자 매개 변수가 제공되어야 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-629">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="65a63-630">특성이 작동하는 방법의 제한 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-630">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="65a63-631">다음 필터는 DI에서 제공하는 생성자 종속성을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-631">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="65a63-632">특성에서 구현된 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="65a63-632"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="65a63-633">위의 필터는 컨트롤러 또는 작업 메서드에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-633">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="65a63-634">로거는 DI를 통해서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-634">Loggers are available from DI.</span></span> <span data-ttu-id="65a63-635">그러나 로깅 용도로만 필터를 만들거나 사용하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-635">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="65a63-636">[기본 제공 프레임워크 로깅](xref:fundamentals/logging/index)은 일반적으로 로깅에 필요한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-636">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="65a63-637">필터에 추가된 로깅은:</span><span class="sxs-lookup"><span data-stu-id="65a63-637">Logging added to filters:</span></span>

* <span data-ttu-id="65a63-638">비즈니스 도메인 문제 또는 필터 고유의 동작에 중점을 두어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-638">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="65a63-639">작업 또는 다른 프레임워크 이벤트를 로깅해서는 **안 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="65a63-639">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="65a63-640">기본 제공 필터는 작업 및 프레임워크 이벤트를 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-640">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="65a63-641">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="65a63-641">ServiceFilterAttribute</span></span>

<span data-ttu-id="65a63-642">서비스 필터 구현 형식은 `ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-642">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="65a63-643"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 DI에서 필터의 인스턴스를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-643">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="65a63-644">다음 코드는 `AddHeaderResultServiceFilter`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-644">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="65a63-645">다음 코드에서 `AddHeaderResultServiceFilter`는 DI 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-645">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="65a63-646">다음 코드에서 `ServiceFilter` 특성은 DI에서 `AddHeaderResultServiceFilter` 필터의 인스턴스를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-646">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="65a63-647">`ServiceFilterAttribute`를 사용할 때 [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="65a63-647">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="65a63-648">필터 인스턴스가 원래 생성된 요청 범위 외부에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-648">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="65a63-649">ASP.NET Core 런타임은 다음을 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-649">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="65a63-650">필터의 단일 인스턴스 생성.</span><span class="sxs-lookup"><span data-stu-id="65a63-650">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="65a63-651">나중에 DI 컨테이너에서 필터가 다시 요청되지 않음.</span><span class="sxs-lookup"><span data-stu-id="65a63-651">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="65a63-652">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-652">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="65a63-653"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-653"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="65a63-654">`IFilterFactory`는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-654">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="65a63-655">`CreateInstance`는 DI에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-655">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="65a63-656">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="65a63-656">TypeFilterAttribute</span></span>

<span data-ttu-id="65a63-657"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>와 비슷하지만 해당 형식은 DI 컨테이너에서 직접 해결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-657"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="65a63-658"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>를 사용하여 형식을 인스턴스화합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-658">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="65a63-659">`TypeFilterAttribute` 형식은 DI 컨테이너에서 직접 해결되지 않기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-659">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="65a63-660">`TypeFilterAttribute`을 사용하여 참조되는 형식은 DI 컨테이너를 사용하여 등록할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-660">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="65a63-661">DI 컨테이너에서 충족하는 종속성을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-661">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="65a63-662">`TypeFilterAttribute`는 형식에 대한 생성자 인수를 필요에 따라 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-662">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="65a63-663">`TypeFilterAttribute`를 사용할 때 [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="65a63-663">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="65a63-664">필터 인스턴스가 원래 생성된 요청 범위 밖에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-664">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="65a63-665">ASP.NET Core 런타임은 단일 필터 인스턴스가 생성되도록 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-665">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="65a63-666">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-666">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="65a63-667">다음 예제는 `TypeFilterAttribute`를 사용하여 형식에 인수를 전달하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-667">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="65a63-668">권한 부여 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-668">Authorization filters</span></span>

<span data-ttu-id="65a63-669">권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-669">Authorization filters:</span></span>

* <span data-ttu-id="65a63-670">필터 파이프라인에서 첫 번째로 실행되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-670">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="65a63-671">작업 메서드에 대한 액세스를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-671">Control access to action methods.</span></span>
* <span data-ttu-id="65a63-672">before 메서드는 있지만 after 메서드는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-672">Have a before method, but no after method.</span></span>

<span data-ttu-id="65a63-673">사용자 지정 권한 부여 필터는 사용자 지정 권한 부여 프레임워크를 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-673">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="65a63-674">사용자 지정 필터를 작성하는 대신 권한 부여 정책을 구성하거나 사용자 지정 권한 부여 정책을 작성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-674">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="65a63-675">기본 제공 권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-675">The built-in authorization filter:</span></span>

* <span data-ttu-id="65a63-676">권한 부여 시스템을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-676">Calls the authorization system.</span></span>
* <span data-ttu-id="65a63-677">요청을 승인하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-677">Does not authorize requests.</span></span>

<span data-ttu-id="65a63-678">권한 부여 필터 내에서 예외를 던지지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="65a63-678">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="65a63-679">해당 예외는 처리되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-679">The exception will not be handled.</span></span>
* <span data-ttu-id="65a63-680">예외 필터가 해당 예외를 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-680">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="65a63-681">권한 부여 필터에서 예외가 발생할 경우 챌린지 발행을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-681">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="65a63-682">[권한 부여](xref:security/authorization/introduction)에 대해 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-682">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="65a63-683">리소스 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-683">Resource filters</span></span>

<span data-ttu-id="65a63-684">리소스 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-684">Resource filters:</span></span>

* <span data-ttu-id="65a63-685"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-685">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="65a63-686">실행이 필터 파이프라인 대부분을 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-686">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="65a63-687">[권한 부여 필터](#authorization-filters)만 리소스 필터 이전에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-687">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="65a63-688">리소스 필터는 파이프라인의 대부분을 단락시켜야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-688">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="65a63-689">예를 들어 캐싱 필터는 캐시 적중 시 파이프라인의 나머지 부분을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-689">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="65a63-690">리소스 필터 예제:</span><span class="sxs-lookup"><span data-stu-id="65a63-690">Resource filter examples:</span></span>

* <span data-ttu-id="65a63-691">이전에 살펴본 [단락 리소스 필터](#short-circuiting-resource-filter).</span><span class="sxs-lookup"><span data-stu-id="65a63-691">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="65a63-692">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="65a63-692">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="65a63-693">모델 바인딩이 양식 데이터에 액세스하는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-693">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="65a63-694">대용량 파일 업로드에서 양식 데이터를 메모리로 읽어들이는 것을 방지하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-694">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="65a63-695">작업 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-695">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="65a63-696">작업 필터는 Razor Pages에 **적용되지 않습니다**.</span><span class="sxs-lookup"><span data-stu-id="65a63-696">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="65a63-697">Razor Pages는 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-697">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="65a63-698">자세한 내용은 [Razor 페이지에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-698">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="65a63-699">작업 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-699">Action filters:</span></span>

* <span data-ttu-id="65a63-700"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-700">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="65a63-701">실행이 작업 메서드의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-701">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="65a63-702">다음 코드는 예제 작업 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-702">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="65a63-703"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>는 다음과 같은 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-703">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="65a63-704"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 작업 메서드에 대한 입력을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-704"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="65a63-705"><xref:Microsoft.AspNetCore.Mvc.Controller> - 컨트롤러 인스턴스를 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-705"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="65a63-706"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` 설정은 작업 메서드 및 후속 작업 필터의 실행을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-706"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="65a63-707">작업 메서드에서 예외를 던지면:</span><span class="sxs-lookup"><span data-stu-id="65a63-707">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="65a63-708">후속 필터의 실행을 막습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-708">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="65a63-709">`Result` 설정과 달리 성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-709">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="65a63-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>는 `Controller` 및 `Result`에 더하여 다음과 같은 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="65a63-711"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 작업 실행이 다른 필터에 의해 단락된 경우 true입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-711"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="65a63-712"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 작업 또는 이전에 실행된 작업 필터에서 예외가 던져진 경우 null이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-712"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="65a63-713">이 속성을 null로 설정하면:</span><span class="sxs-lookup"><span data-stu-id="65a63-713">Setting this property to null:</span></span>

  * <span data-ttu-id="65a63-714">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-714">Effectively handles the exception.</span></span>
  * <span data-ttu-id="65a63-715">`Result`는 작업 메서드에서 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-715">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="65a63-716">`IAsyncActionFilter`의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>에 대한 호출은:</span><span class="sxs-lookup"><span data-stu-id="65a63-716">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="65a63-717">모든 후속 작업 필터 및 작업 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-717">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="65a63-718">`ActionExecutedContext`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-718">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="65a63-719">단락시키려면 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName>을 결과 인스턴스에 할당하고 `next`(`ActionExecutionDelegate`)를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-719">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="65a63-720">프레임워크는 서브클래싱 할 수 있는 추상 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-720">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="65a63-721">`OnActionExecuting` 작업 필터는 다음에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-721">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="65a63-722">모델 상태의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-722">Validate model state.</span></span>
* <span data-ttu-id="65a63-723">상태가 유효하지 않은 경우 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-723">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="65a63-724">`OnActionExecuted` 메서드는 작업 메서드 이후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-724">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="65a63-725">그리고 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 속성을 통해 작업 결과를 확인하고 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-725">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="65a63-726"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>는 작업 실행이 다른 필터에 의해 단락된 경우 true로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-726"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="65a63-727"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>은 작업 또는 후속 작업 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-727"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="65a63-728">`Exception`을 null로 설정하면:</span><span class="sxs-lookup"><span data-stu-id="65a63-728">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="65a63-729">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-729">Effectively handles an exception.</span></span>
  * <span data-ttu-id="65a63-730">`ActionExecutedContext.Result`는 작업 메서드에서 정상적으로 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-730">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="65a63-731">예외 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-731">Exception filters</span></span>

<span data-ttu-id="65a63-732">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-732">Exception filters:</span></span>

* <span data-ttu-id="65a63-733"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-733">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="65a63-734">공통 오류 처리 정책을 구현하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-734">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="65a63-735">다음 예제 예외 필터는 사용자 지정 오류 보기를 사용하여 앱을 개발 중인 경우에 발생하는 예외에 대한 세부 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-735">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="65a63-736">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-736">Exception filters:</span></span>

* <span data-ttu-id="65a63-737">before 및 after 이벤트가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-737">Don't have before and after events.</span></span>
* <span data-ttu-id="65a63-738"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-738">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="65a63-739">Razor Page 또는 컨트롤러 생성, [모델 바인딩](xref:mvc/models/model-binding), 작업 필터 또는 작업 메서드에서 발생하는 처리되지 않은 예외를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-739">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="65a63-740">리소스 필터, 결과 필터 또는 MVC 결과 실행에서 발생하는 예외를 잡지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="65a63-740">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="65a63-741">예외를 처리하려면 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 속성을 `true`로 설정하거나 응답을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-741">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="65a63-742">그러면 예외가 전파되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-742">This stops propagation of the exception.</span></span> <span data-ttu-id="65a63-743">예외 필터는 예외를 “성공”으로 변환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-743">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="65a63-744">이는 작업 필터에서만 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-744">Only an action filter can do that.</span></span>

<span data-ttu-id="65a63-745">예외 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-745">Exception filters:</span></span>

* <span data-ttu-id="65a63-746">작업 내에서 발생하는 예외를 잡는 데 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-746">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="65a63-747">오류 처리 미들웨어만큼 유연하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-747">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="65a63-748">예외 처리의 경우 미들웨어를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-748">Prefer middleware for exception handling.</span></span> <span data-ttu-id="65a63-749">어떤 작업 메서드가 호출되는지에 따라 오류 처리 방식이 ‘다른’ 경우에만 예외 필터를 사용합니다. </span><span class="sxs-lookup"><span data-stu-id="65a63-749">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="65a63-750">예를 들어 앱에는 API 엔드포인트 및 보기/HTML 모두에 대한 작업 메서드가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-750">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="65a63-751">API 엔드포인트는 JSON으로 오류 정보를 반환할 수 있습니다. 반면 보기 기반 작업은 HTML로 오류 페이지를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-751">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="65a63-752">결과 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-752">Result filters</span></span>

<span data-ttu-id="65a63-753">결과 필터는:</span><span class="sxs-lookup"><span data-stu-id="65a63-753">Result filters:</span></span>

* <span data-ttu-id="65a63-754">다음 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-754">Implement an interface:</span></span>
  * <span data-ttu-id="65a63-755"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="65a63-755"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="65a63-756"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="65a63-756"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="65a63-757">실행이 작업 결과의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-757">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="65a63-758">IResultFilter 및 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="65a63-758">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="65a63-759">다음 코드는 HTTP 헤더를 추가하는 결과 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-759">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="65a63-760">실행되는 결과의 종류는 작업에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-760">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="65a63-761">보기를 반환하는 작업에는 실행 중인 <xref:Microsoft.AspNetCore.Mvc.ViewResult>의 일부로 모든 Razor 프로세스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-761">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="65a63-762">API 메서드는 결과 실행의 일부로 어떤 직렬화를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-762">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="65a63-763">[작업 결과](xref:mvc/controllers/actions)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-763">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="65a63-764">결과 필터는 작업 또는 작업 필터가 작업 결과를 생성하는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-764">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="65a63-765">다음 경우에는 결과 필터가 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-765">Result filters are not executed when:</span></span>

* <span data-ttu-id="65a63-766">권한 부여 필터 또는 리소스 필터가 파이프라인을 단락시킬 경우.</span><span class="sxs-lookup"><span data-stu-id="65a63-766">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="65a63-767">예외 필터가 작업 결과를 생성하여 예외를 처리하는 경우.</span><span class="sxs-lookup"><span data-stu-id="65a63-767">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="65a63-768"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 메서드는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName>을 `true`로 설정하여 작업 결과 및 후속 결과 필터를 단락시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-768">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="65a63-769">단락시킬 경우 빈 응답을 생성하지 않도록 응답 개체에 작성하세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-769">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="65a63-770">`IResultFilter.OnResultExecuting`에서 예외를 던지면:</span><span class="sxs-lookup"><span data-stu-id="65a63-770">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="65a63-771">작업 결과 및 후속 필터의 실행을 막습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-771">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="65a63-772">성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-772">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="65a63-773"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 메서드가 실행될 때에는 응답이 이미 클라이언트에 전송되었을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-773">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="65a63-774">이미 클라이언트에 전송된 응답은 더이상 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-774">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="65a63-775">`ResultExecutedContext.Canceled`는 작업 결과 실행이 다른 필터에 의해 단락된 경우 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-775">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="65a63-776">`ResultExecutedContext.Exception`은 작업 결과 또는 후속 결과 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-776">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="65a63-777">`Exception`을 null로 설정하면 효과적으로 예외를 처리하고 이후의 파이프라인에서 ASP.NET Core에 의해 예외가 다시 던져지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-777">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="65a63-778">결과 필터에서 예외를 처리하는 경우 데이터를 응답에 쓸 수 있는 신뢰할 수 있는 방법이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-778">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="65a63-779">작업 결과가 예외를 던질 때 헤더가 클라이언트에 플러시된 경우 오류 코드를 전송하기 위한 신뢰할 수 있는 메커니즘이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-779">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="65a63-780"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate>의 `await next` 호출은 후속 결과 필터 및 작업 결과를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-780">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="65a63-781">단락시키려면 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel)을 `true`로 설정하고 `ResultExecutionDelegate`를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-781">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="65a63-782">프레임워크는 서브클래싱 할 수 있는 추상 `ResultFilterAttribute`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-782">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="65a63-783">이전에 표시된 [AddHeaderAttribute](#add-header-attribute) 클래스는 결과 필터 특성의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-783">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="65a63-784">IAlwaysRunResultFilter 및 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="65a63-784">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="65a63-785"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 인터페이스는 모든 작업 결과에 대해 실행되는 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 구현을 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-785">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="65a63-786">여기에는 다음에 의해 생성되는 작업 결과가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-786">This includes action results produced by:</span></span>

* <span data-ttu-id="65a63-787">단락하는 권한 부여 필터 및 리소스 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-787">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="65a63-788">예외 필터</span><span class="sxs-lookup"><span data-stu-id="65a63-788">Exception filters.</span></span>

<span data-ttu-id="65a63-789">예를 들어 다음 필터는 항상 실행되어 콘텐츠 협상이 실패할 경우 작업 결과(<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)를 *422 Unprocessable Entity* 상태 코드로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-789">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="65a63-790">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="65a63-790">IFilterFactory</span></span>

<span data-ttu-id="65a63-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="65a63-792">따라서 필터 파이프라인 어디에서나 `IFilterFactory` 인스턴스를 `IFilterMetadata` 인스턴스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-792">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="65a63-793">런타임이 필터를 호출하려고 준비할 때 `IFilterFactory`로 캐스팅을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-793">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="65a63-794">해당 캐스팅에 성공하면 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 호출하여 호출되는 `IFilterMetadata` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-794">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="65a63-795">앱이 시작될 때 정확한 필터 파이프라인을 명시적으로 설정할 필요가 없으므로 유연한 디자인을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-795">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="65a63-796">필터를 만드는 다른 방법으로 사용자 지정 특성 구현을 사용하여 `IFilterFactory`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-796">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="65a63-797">[다운로드 예제](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)를 실행하여 이전 코드를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-797">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="65a63-798">F12 개발자 도구를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-798">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="65a63-799">`https://localhost:5001/Sample/HeaderWithFactory`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-799">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="65a63-800">F12 개발자 도구는 예제 코드에 의해 추가된 다음 응답 헤더를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-800">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="65a63-801">**author:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="65a63-801">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="65a63-802">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="65a63-802">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="65a63-803">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="65a63-803">**internal:** `My header`</span></span>

<span data-ttu-id="65a63-804">이전 코드에서는 **internal:** `My header` 응답 헤더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-804">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="65a63-805">특성에서 구현된 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="65a63-805">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="65a63-806">`IFilterFactory`를 구현하는 필터는 다음과 같은 필터에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-806">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="65a63-807">매개 변수 전달을 필요로 하지 않는 필터.</span><span class="sxs-lookup"><span data-stu-id="65a63-807">Don't require passing parameters.</span></span>
* <span data-ttu-id="65a63-808">DI에 의해 채워져야 할 생성자 종속성이 있는 필터.</span><span class="sxs-lookup"><span data-stu-id="65a63-808">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="65a63-809"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-809"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="65a63-810">`IFilterFactory`는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-810">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="65a63-811">`CreateInstance`는 서비스 컨테이너(DI)에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-811">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="65a63-812">다음 코드는 `[SampleActionFilter]`을 적용하는 세 가지 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-812">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="65a63-813">이전 코드에서 `[SampleActionFilter]`로 메서드를 장식하는 것이 `SampleActionFilter`을 적용하는 데 선호되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-813">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="65a63-814">필터 파이프라인에서 미들웨어 사용</span><span class="sxs-lookup"><span data-stu-id="65a63-814">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="65a63-815">리소스 필터는 파이프라인의 뒷부분에 제공되는 모든 실행을 둘러싼다는 점에서 [미들웨어](xref:fundamentals/middleware/index)처럼 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-815">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="65a63-816">하지만 필터는 ASP.NET Core 런타임의 일부라는 점에서 미들웨어와 다릅니다. 즉, ASP.NET Core 컨텍스트 및 구문에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-816">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="65a63-817">미들웨어를 필터로 사용하려면 필터 파이프라인에 삽입할 미들웨어를 지정하는 `Configure` 메서드를 포함한 형식을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-817">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="65a63-818">다음 예제는 지역화 미들웨어를 사용하여 요청에 대한 현재 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-818">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="65a63-819"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>를 사용하여 미들웨어를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-819">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="65a63-820">미들웨어 필터는 모델 바인딩 이전 및 나머지 파이프라인 이후에 리소스 필터와 동일한 필터 파이프라인 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-820">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="65a63-821">다음 작업</span><span class="sxs-lookup"><span data-stu-id="65a63-821">Next actions</span></span>

* <span data-ttu-id="65a63-822">[Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65a63-822">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="65a63-823">필터를 실험하려면 [GitHub 예제를 다운로드하고, 테스트하고, 수정](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="65a63-823">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
