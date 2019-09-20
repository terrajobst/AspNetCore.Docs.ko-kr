---
title: ASP.NET Core로 Web API 만들기
author: scottaddie
description: ASP.NET Core에서 Web API 만들기에 대한 기본 사항을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2019
uid: web-api/index
ms.openlocfilehash: aab9b848eb6e69055b019c9253c716898e9847e2
ms.sourcegitcommit: a11f09c10ef3d4eeab7ae9ce993e7f30427741c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149344"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="f3501-103">ASP.NET Core로 Web API 만들기</span><span class="sxs-lookup"><span data-stu-id="f3501-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="f3501-104">작성자: [Scott Addie](https://github.com/scottaddie), [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f3501-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="f3501-105">ASP.NET Core는 C#를 사용하여 Web API라고도 하는 RESTful 서비스 만들기를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="f3501-106">요청을 처리하기 위해 Web API는 컨트롤러를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="f3501-107">Web API의 *컨트롤러*는 `ControllerBase`에서 파생되는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="f3501-108">이 문서에서는 웹 API 요청을 처리하기 위해 컨트롤러를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="f3501-109">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples)</span><span class="sxs-lookup"><span data-stu-id="f3501-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="f3501-110">([다운로드하는 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f3501-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="f3501-111">ControllerBase 클래스</span><span class="sxs-lookup"><span data-stu-id="f3501-111">ControllerBase class</span></span>

<span data-ttu-id="f3501-112">Web API는 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>에서 파생되는 하나 이상의 컨트롤러 클래스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="f3501-113">웹 API 프로젝트 템플릿은 시작 컨트롤러를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="f3501-114"><xref:Microsoft.AspNetCore.Mvc.Controller> 클래스에서 파생된 Web API 컨트롤러를 만들지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f3501-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="f3501-115">`Controller`는 `ControllerBase`에서 파생되며 뷰에 대한 지원을 추가하므로 Web API 요청이 아닌 웹 페이지를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="f3501-116">이 규칙에 대한 예외가 있습니다. 뷰와 웹 API 모두에 동일한 컨트롤러를 사용하려는 경우 `Controller`에서 파생합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="f3501-117">`ControllerBase` 클래스는 HTTP 요청을 처리하는 데 유용한 많은 속성 및 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="f3501-118">예를 들어, `ControllerBase.CreatedAtAction`은 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="f3501-119">다음은 `ControllerBase`가 제공하는 메서드의 몇 가지 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="f3501-120">메서드</span><span class="sxs-lookup"><span data-stu-id="f3501-120">Method</span></span>   |<span data-ttu-id="f3501-121">참고 사항</span><span class="sxs-lookup"><span data-stu-id="f3501-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| <span data-ttu-id="f3501-122">400 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>|<span data-ttu-id="f3501-123">404 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|<span data-ttu-id="f3501-124">파일을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|<span data-ttu-id="f3501-125">[모델 바인딩](xref:mvc/models/model-binding)을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|<span data-ttu-id="f3501-126">[모델 유효성 검사](xref:mvc/models/validation)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="f3501-127">사용 가능한 모든 메서드 및 속성 목록을 보려면 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3501-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="f3501-128">특성</span><span class="sxs-lookup"><span data-stu-id="f3501-128">Attributes</span></span>

<span data-ttu-id="f3501-129"><xref:Microsoft.AspNetCore.Mvc> 네임스페이스는 Web API 컨트롤러 및 작업 메서드의 동작을 구성하는 데 사용할 수 있는 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="f3501-130">다음 예제에서는 특성을 사용하여 지원되는 HTTP 작업 동사와 반환될 수 있는 알려진 HTTP 상태 코드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="f3501-131">다음은 사용 가능한 특성의 몇 가지 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="f3501-132">특성</span><span class="sxs-lookup"><span data-stu-id="f3501-132">Attribute</span></span>|<span data-ttu-id="f3501-133">참고 사항</span><span class="sxs-lookup"><span data-stu-id="f3501-133">Notes</span></span>|
|---------|-----|
|<span data-ttu-id="f3501-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f3501-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span></span>      |<span data-ttu-id="f3501-135">컨트롤러 또는 작업의 URL 패턴을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-135">Specifies URL pattern for a controller or action.</span></span>|
|<span data-ttu-id="f3501-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f3501-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span></span>        |<span data-ttu-id="f3501-137">모델 바인딩에 포함할 접두사 및 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-137">Specifies prefix and properties to include for model binding.</span></span>|
|<span data-ttu-id="f3501-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f3501-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span></span>  |<span data-ttu-id="f3501-139">HTTP GET 작업 동사를 지원하는 작업을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-139">Identifies an action that supports the HTTP GET action verb.</span></span>|
|<span data-ttu-id="f3501-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f3501-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span></span>|<span data-ttu-id="f3501-141">작업에서 허용하는 데이터 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-141">Specifies data types that an action accepts.</span></span>|
|<span data-ttu-id="f3501-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f3501-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span></span>|<span data-ttu-id="f3501-143">작업에서 반환하는 데이터 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-143">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="f3501-144">사용 가능한 특성이 포함된 목록은 <xref:Microsoft.AspNetCore.Mvc> 네임스페이스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3501-144">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="f3501-145">ApiController 특성</span><span class="sxs-lookup"><span data-stu-id="f3501-145">ApiController attribute</span></span>

<span data-ttu-id="f3501-146">[[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성을 컨트롤러 클래스에 적용하여 다음과 같이 독자적인 API 관련 동작을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-146">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

* [<span data-ttu-id="f3501-147">특성 라우팅 요구 사항</span><span class="sxs-lookup"><span data-stu-id="f3501-147">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="f3501-148">자동 HTTP 400 응답</span><span class="sxs-lookup"><span data-stu-id="f3501-148">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="f3501-149">바인딩 소스 매개 변수 유추</span><span class="sxs-lookup"><span data-stu-id="f3501-149">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="f3501-150">다중 파트/폼 데이터 요청 유추</span><span class="sxs-lookup"><span data-stu-id="f3501-150">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="f3501-151">오류 상태 코드에 대한 문제 세부 정보</span><span class="sxs-lookup"><span data-stu-id="f3501-151">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="f3501-152">이러한 기능을 사용하려면 [호환성 버전](xref:mvc/compatibility-version) 2.1 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-152">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="f3501-153">특정 컨트롤러의 특성</span><span class="sxs-lookup"><span data-stu-id="f3501-153">Attribute on specific controllers</span></span>

<span data-ttu-id="f3501-154">프로젝트 템플릿의 다음 예제에서처럼 `[ApiController]` 특성을 특정 컨트롤러에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-154">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="f3501-155">여러 컨트롤러의 특성</span><span class="sxs-lookup"><span data-stu-id="f3501-155">Attribute on multiple controllers</span></span>

<span data-ttu-id="f3501-156">둘 이상의 컨트롤러에서 특성을 사용하는 방법으로 `[ApiController]` 특성으로 주석이 추가된 사용자 지정 기본 컨트롤러 클래스를 만드는 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-156">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="f3501-157">다음 예제는 사용자 지정 기본 클래스와 여기에서 파생되는 컨트롤러를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-157">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="f3501-158">어셈블리의 특성</span><span class="sxs-lookup"><span data-stu-id="f3501-158">Attribute on an assembly</span></span>

<span data-ttu-id="f3501-159">[호환성 버전](xref:mvc/compatibility-version)이 2.2 이상으로 설정된 경우 `[ApiController]` 특성을 어셈블리에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-159">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="f3501-160">이 방식의 주석은 웹 API 동작을 어셈블리의 모든 컨트롤러에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-160">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="f3501-161">개별 컨트롤러를 대상으로 옵트아웃하는 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-161">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="f3501-162">어셈블리 수준 특성을 `Startup` 클래스 주위의 네임스페이스 선언에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-162">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a><span data-ttu-id="f3501-163">특성 라우팅 요구 사항</span><span class="sxs-lookup"><span data-stu-id="f3501-163">Attribute routing requirement</span></span>

<span data-ttu-id="f3501-164">`[ApiController]` 특성은 특성 라우팅을 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-164">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="f3501-165">예:</span><span class="sxs-lookup"><span data-stu-id="f3501-165">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="f3501-166">작업은 `Startup.Configure`의 `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> 또는 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*>에 의해 정의된 [규칙 기반 경로](xref:mvc/controllers/routing#conventional-routing)를 통해 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-166">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="f3501-167">작업은 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>에 의해 정의된 [규칙 기반 경로](xref:mvc/controllers/routing#conventional-routing)를 통해 또는 `Startup.Configure`의 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*>에서 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

### <a name="automatic-http-400-responses"></a><span data-ttu-id="f3501-168">자동 HTTP 400 응답</span><span class="sxs-lookup"><span data-stu-id="f3501-168">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="f3501-169">`[ApiController]` 특성은 HTTP 400 응답을 자동으로 트리거하는 모델 유효성 검사 오류를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-169">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="f3501-170">따라서 다음 코드는 작업 메서드에서 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-170">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="f3501-171">ASP.NET Core MVC는 <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> 작업 필터를 사용하여 위의 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-171">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="f3501-172">기본 BadRequest 응답</span><span class="sxs-lookup"><span data-stu-id="f3501-172">Default BadRequest response</span></span>

<span data-ttu-id="f3501-173">호환성 버전이 2.1이면 HTTP 400 응답에 대한 기본 응답 형식은 <xref:Microsoft.AspNetCore.Mvc.SerializableError>입니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-173">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="f3501-174">다음 요청 본문은 직렬화된 형식의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-174">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f3501-175">호환성 버전이 2.2 이상이면 HTTP 400 응답에 대한 기본 응답 형식은 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>입니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-175">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="f3501-176">다음 요청 본문은 직렬화된 형식의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-176">The following request body is an example of the serialized type:</span></span>

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

<span data-ttu-id="f3501-177">`ValidationProblemDetails` 형식:</span><span class="sxs-lookup"><span data-stu-id="f3501-177">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="f3501-178">웹 API 응답에서 오류를 지정하기 위해 머신이 읽을 수 있는 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-178">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="f3501-179">[RFC 7807 사양](https://tools.ietf.org/html/rfc7807)을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-179">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="f3501-180">자동 400 응답 기록</span><span class="sxs-lookup"><span data-stu-id="f3501-180">Log automatic 400 responses</span></span>

<span data-ttu-id="f3501-181">[How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157)(모델 유효성 검사 오류에서 자동 400 응답을 기록하는 방법(aspnet/AspNetCore.Docs #12157))를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f3501-181">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="f3501-182">자동 400 응답 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="f3501-182">Disable automatic 400 response</span></span>

<span data-ttu-id="f3501-183">자동 400 동작을 사용하지 않으려면 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-183">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="f3501-184">`Startup.ConfigureServices`에 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-184">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="f3501-185">바인딩 소스 매개 변수 유추</span><span class="sxs-lookup"><span data-stu-id="f3501-185">Binding source parameter inference</span></span>

<span data-ttu-id="f3501-186">바인딩 소스 특성은 작업 매개 변수 값이 발견되는 위치를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-186">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="f3501-187">다음 바인딩 소스 특성이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-187">The following binding source attributes exist:</span></span>

|<span data-ttu-id="f3501-188">특성</span><span class="sxs-lookup"><span data-stu-id="f3501-188">Attribute</span></span>|<span data-ttu-id="f3501-189">바인딩 원본</span><span class="sxs-lookup"><span data-stu-id="f3501-189">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="f3501-190">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span><span class="sxs-lookup"><span data-stu-id="f3501-190">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span></span>     | <span data-ttu-id="f3501-191">요청 본문</span><span class="sxs-lookup"><span data-stu-id="f3501-191">Request body</span></span> |
|<span data-ttu-id="f3501-192">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span><span class="sxs-lookup"><span data-stu-id="f3501-192">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span></span>     | <span data-ttu-id="f3501-193">요청 본문에서 양식 데이터</span><span class="sxs-lookup"><span data-stu-id="f3501-193">Form data in the request body</span></span> |
|<span data-ttu-id="f3501-194">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span><span class="sxs-lookup"><span data-stu-id="f3501-194">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span></span> | <span data-ttu-id="f3501-195">요청 헤더</span><span class="sxs-lookup"><span data-stu-id="f3501-195">Request header</span></span> |
|<span data-ttu-id="f3501-196">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span><span class="sxs-lookup"><span data-stu-id="f3501-196">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span></span>   | <span data-ttu-id="f3501-197">요청 쿼리 문자열 매개 변수</span><span class="sxs-lookup"><span data-stu-id="f3501-197">Request query string parameter</span></span> |
|<span data-ttu-id="f3501-198">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="f3501-198">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span></span>   | <span data-ttu-id="f3501-199">현재 요청의 경로 데이터</span><span class="sxs-lookup"><span data-stu-id="f3501-199">Route data from the current request</span></span> |
|<span data-ttu-id="f3501-200">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span><span class="sxs-lookup"><span data-stu-id="f3501-200">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span></span> | <span data-ttu-id="f3501-201">작업 매개 변수로 삽입된 요청 서비스</span><span class="sxs-lookup"><span data-stu-id="f3501-201">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="f3501-202">값에 `%2f`(즉, `/`)이 포함될 수 있는 경우 `[FromRoute]`를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-202">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="f3501-203">`%2f`는 `/`로 이스케이프가 해제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-203">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="f3501-204">값에 `%2f`가 포함될 수 있으면 `[FromQuery]`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-204">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="f3501-205">`[ApiController]` 특성 또는 `[FromQuery]` 같은 다른 바인딩 소스 특성 없이, ASP.NET Core 런타임에서 복합 개체 모델 바인더 사용을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-205">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="f3501-206">복합 개체 모델 바인더는 정의된 순서로 값 공급자로부터 데이터를 풀합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-206">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="f3501-207">다음 예제에서 `[FromQuery]` 특성은 `discontinuedOnly` 매개 변수 값이 요청 URL의 쿼리 문자열에 제공됨을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-207">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="f3501-208">`[ApiController]` 특성은 작업 매개 변수의 기본 데이터 원본에 유추 규칙을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-208">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="f3501-209">이러한 규칙을 통해 작업 매개 변수에 특성을 적용하여 바인딩 소스를 수동으로 식별할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-209">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="f3501-210">바인딩 소스 유추 규칙은 다음과 같이 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-210">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="f3501-211">`[FromBody]`는 복합 형식 매개 변수에 대해 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-211">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="f3501-212">`[FromBody]` 유추 규칙은 <xref:Microsoft.AspNetCore.Http.IFormCollection> 및 <xref:System.Threading.CancellationToken> 같이 특별한 의미를 지닌 복합 기본 제공 형식에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-212">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="f3501-213">바인딩 소스 유추 코드는 이러한 특별한 형식을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-213">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="f3501-214">`[FromForm]`은 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 <xref:Microsoft.AspNetCore.Http.IFormFileCollection> 형식의 작업 매개 변수에 대해 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-214">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="f3501-215">단순 또는 사용자 정의 형식에 대해서는 유추되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-215">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="f3501-216">`[FromRoute]`는 경로 템플릿에서 매개 변수와 일치하는 작업 매개 변수 이름에 대해 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-216">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="f3501-217">둘 이상의 경로가 작업 매개 변수와 일치하는 경우 모든 경로 값은 `[FromRoute]`로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-217">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="f3501-218">`[FromQuery]`는 다른 작업 매개 변수에 대해 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-218">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="f3501-219">FromBody 유추 메모</span><span class="sxs-lookup"><span data-stu-id="f3501-219">FromBody inference notes</span></span>

<span data-ttu-id="f3501-220">`[FromBody]`는 `string` 또는 `int` 같은 단순 형식에 대해 유추되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-220">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="f3501-221">따라서 해당 기능이 필요한 경우 단순 형식에 `[FromBody]` 특성을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-221">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="f3501-222">작업에 요청 본문에서 바인딩된 매개 변수가 두 개 이상 있는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-222">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="f3501-223">예를 들어 다음 모든 작업 메서드 시그니처로 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-223">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="f3501-224">복합 형식이므로 둘 다에서 유추된 `[FromBody]`</span><span class="sxs-lookup"><span data-stu-id="f3501-224">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="f3501-225">하나의 `[FromBody]` 특성, 복합 형식이므로 다른 하나에서 유추됨</span><span class="sxs-lookup"><span data-stu-id="f3501-225">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="f3501-226">둘 다의 `[FromBody]` 특성</span><span class="sxs-lookup"><span data-stu-id="f3501-226">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="f3501-227">ASP.NET Core 2.1에서 목록, 배열 등의 컬렉션 형식 매개 변수를 `[FromQuery]`로 잘못 유추합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-227">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="f3501-228">이러한 매개 변수가 요청 본문에서 바인딩되는 경우 해당 매개 변수에 `[FromBody]` 특성을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-228">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="f3501-229">이 문제는 ASP.NET Core 2.2 이상에서 수정됩니다. 즉, 기본적으로 컬렉션 형식 매개 변수를 본문에서 바인딩되도록 유추합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-229">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="f3501-230">유추 규칙 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="f3501-230">Disable inference rules</span></span>

<span data-ttu-id="f3501-231">바인딩 소스 유추를 사용하지 않으려면 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters>를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-231">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="f3501-232">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-232">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="f3501-233">다중 파트/폼 데이터 요청 유추</span><span class="sxs-lookup"><span data-stu-id="f3501-233">Multipart/form-data request inference</span></span>

<span data-ttu-id="f3501-234">작업 매개 변수를 [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) 특성으로 주석 처리하는 경우 `[ApiController]` 특성이 유추 규칙에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-234">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="f3501-235">`multipart/form-data` 요청 콘텐츠 형식이 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-235">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="f3501-236">기본 동작을 사용하지 않으려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-236">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="f3501-237">오류 상태 코드에 대한 문제 세부 정보</span><span class="sxs-lookup"><span data-stu-id="f3501-237">Problem details for error status codes</span></span>

<span data-ttu-id="f3501-238">호환성 버전이 2.2 이상인 경우 MVC는 오류 결과(상태 코드 400 이상의 결과)를 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>가 있는 결과로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-238">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="f3501-239">`ProblemDetails` 형식은 HTTP 응답에 머신에서 읽을 수 있는 오류 세부 정보를 제공하기 위해 [RFC 7807 사양](https://tools.ietf.org/html/rfc7807)을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-239">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="f3501-240">다음 컨트롤러 작업에서 다음 코드를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="f3501-240">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="f3501-241">`NotFound` 메서드는 `ProblemDetails` 본문이 포함된 HTTP 404 상태 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-241">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="f3501-242">예:</span><span class="sxs-lookup"><span data-stu-id="f3501-242">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="f3501-243">ProblemDetails 응답 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="f3501-243">Disable ProblemDetails response</span></span>

<span data-ttu-id="f3501-244"><xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors*> 속성이 `true`로 설정된 경우 `ProblemDetails` 인스턴스의 자동 생성이 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-244">The automatic creation of a `ProblemDetails` instance is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors*> property is set to `true`.</span></span> <span data-ttu-id="f3501-245">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f3501-245">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f3501-246">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f3501-246">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
