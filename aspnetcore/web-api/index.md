---
title: ASP.NET Core로 Web API 만들기
author: scottaddie
description: ASP.NET Core에서 Web API 만들기에 대한 기본 사항을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 05/07/2019
uid: web-api/index
ms.openlocfilehash: 593fd33babc81cddfc4db2150a37e5ec3bc1a0be
ms.sourcegitcommit: a3926eae3f687013027a2828830c12a89add701f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65450842"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="3b31a-103">ASP.NET Core로 Web API 만들기</span><span class="sxs-lookup"><span data-stu-id="3b31a-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="3b31a-104">작성자: [Scott Addie](https://github.com/scottaddie), [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="3b31a-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="3b31a-105">ASP.NET Core는 C#를 사용하여 Web API라고도 하는 RESTful 서비스 만들기를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="3b31a-106">요청을 처리하기 위해 Web API는 컨트롤러를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="3b31a-107">Web API의 *컨트롤러*는 `ControllerBase`에서 파생되는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="3b31a-108">이 문서에서는 API 요청을 처리하기 위해 컨트롤러를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-108">This article shows how to use controllers for handling API requests.</span></span>

<span data-ttu-id="3b31a-109">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples)</span><span class="sxs-lookup"><span data-stu-id="3b31a-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="3b31a-110">([다운로드하는 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3b31a-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="3b31a-111">ControllerBase 클래스</span><span class="sxs-lookup"><span data-stu-id="3b31a-111">ControllerBase class</span></span>

<span data-ttu-id="3b31a-112">Web API에는 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>에서 파생되는 하나 이상의 컨트롤러 클래스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-112">A web API has one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="3b31a-113">예를 들어, Web API 프로젝트 템플릿은 값 컨트롤러를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-113">For example, the web API project template creates a Values controller:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=3)]

<span data-ttu-id="3b31a-114"><xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스에서 파생하여 Web API 컨트롤러를 만들지 마세요.</span><span class="sxs-lookup"><span data-stu-id="3b31a-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class.</span></span> <span data-ttu-id="3b31a-115">`Controller`는 `ControllerBase`에서 파생되며 뷰에 대한 지원을 추가하므로 Web API 요청이 아닌 웹 페이지를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span>  <span data-ttu-id="3b31a-116">이 규칙에 대한 예외가 있습니다. 뷰와 API 모두에 동일한 컨트롤러를 사용하려는 경우 `Controller`에서 파생합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-116">There's an exception to this rule: if you plan to use the same controller for both views and APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="3b31a-117">`ControllerBase` 클래스는 HTTP 요청을 처리하는 데 유용한 많은 속성 및 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="3b31a-118">예를 들어, `ControllerBase.CreatedAtAction`은 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=8-9)]

 <span data-ttu-id="3b31a-119">다음은 `ControllerBase`가 제공하는 메서드의 몇 가지 예입니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="3b31a-120">메서드</span><span class="sxs-lookup"><span data-stu-id="3b31a-120">Method</span></span>  |<span data-ttu-id="3b31a-121">참고 사항</span><span class="sxs-lookup"><span data-stu-id="3b31a-121">Notes</span></span>  |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| <span data-ttu-id="3b31a-122">400 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> |<span data-ttu-id="3b31a-123">404 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|<span data-ttu-id="3b31a-124">파일을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|<span data-ttu-id="3b31a-125">[모델 바인딩](xref:mvc/models/model-binding)을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|<span data-ttu-id="3b31a-126">[모델 유효성 검사](xref:mvc/models/validation)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="3b31a-127">사용 가능한 모든 메서드 및 속성 목록을 보려면 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3b31a-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="3b31a-128">특성</span><span class="sxs-lookup"><span data-stu-id="3b31a-128">Attributes</span></span>

<span data-ttu-id="3b31a-129"><xref:Microsoft.AspNetCore.Mvc> 네임스페이스는 Web API 컨트롤러 및 작업 메서드의 동작을 구성하는 데 사용할 수 있는 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="3b31a-130">다음 예제에서는 특성을 사용하여 승인되는 HTTP 메서드와 반환되는 상태 코드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-130">The following example uses attributes to specify the HTTP method accepted and the status codes returned:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="3b31a-131">다음은 사용 가능한 특성의 몇 가지 예입니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="3b31a-132">특성</span><span class="sxs-lookup"><span data-stu-id="3b31a-132">Attribute</span></span>|<span data-ttu-id="3b31a-133">참고 사항</span><span class="sxs-lookup"><span data-stu-id="3b31a-133">Notes</span></span>|
|---------|-----|
|<span data-ttu-id="3b31a-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span><span class="sxs-lookup"><span data-stu-id="3b31a-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span></span>      |<span data-ttu-id="3b31a-135">컨트롤러 또는 작업의 URL 패턴을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-135">Specifies URL pattern for a controller or action.</span></span>|
|<span data-ttu-id="3b31a-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span><span class="sxs-lookup"><span data-stu-id="3b31a-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span></span>        |<span data-ttu-id="3b31a-137">모델 바인딩에 포함할 접두사 및 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-137">Specifies prefix and properties to include for model binding.</span></span>|
|<span data-ttu-id="3b31a-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span><span class="sxs-lookup"><span data-stu-id="3b31a-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span></span>  |<span data-ttu-id="3b31a-139">HTTP GET 메서드를 지원하는 작업을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-139">Identifies an action that supports the HTTP GET method.</span></span>|
|<span data-ttu-id="3b31a-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="3b31a-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span></span>|<span data-ttu-id="3b31a-141">작업에서 허용하는 데이터 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-141">Specifies data types that an action accepts.</span></span>|
|<span data-ttu-id="3b31a-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="3b31a-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span></span>|<span data-ttu-id="3b31a-143">작업에서 반환하는 데이터 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-143">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="3b31a-144">사용 가능한 특성이 포함된 목록은 <xref:Microsoft.AspNetCore.Mvc> 네임스페이스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3b31a-144">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="3b31a-145">ApiController 특성</span><span class="sxs-lookup"><span data-stu-id="3b31a-145">ApiController attribute</span></span>

<span data-ttu-id="3b31a-146">[[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성을 컨트롤러 클래스에 적용하여 API 관련 동작을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-146">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable API-specific behaviors:</span></span>

* [<span data-ttu-id="3b31a-147">특성 라우팅 요구 사항</span><span class="sxs-lookup"><span data-stu-id="3b31a-147">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="3b31a-148">자동 HTTP 400 응답</span><span class="sxs-lookup"><span data-stu-id="3b31a-148">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="3b31a-149">바인딩 소스 매개 변수 유추</span><span class="sxs-lookup"><span data-stu-id="3b31a-149">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="3b31a-150">다중 파트/폼 데이터 요청 유추</span><span class="sxs-lookup"><span data-stu-id="3b31a-150">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="3b31a-151">오류 상태 코드에 대한 문제 세부 정보</span><span class="sxs-lookup"><span data-stu-id="3b31a-151">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="3b31a-152">이러한 기능을 사용하려면 [호환성 버전](<xref:mvc/compatibility-version>) 2.1 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-152">These features require a [compatibility version](<xref:mvc/compatibility-version>) of 2.1 or later.</span></span>

### <a name="apicontroller-on-specific-controllers"></a><span data-ttu-id="3b31a-153">특정 컨트롤러의 ApiController</span><span class="sxs-lookup"><span data-stu-id="3b31a-153">ApiController on specific controllers</span></span>

<span data-ttu-id="3b31a-154">프로젝트 템플릿의 다음 예제에서처럼 `[ApiController]` 특성을 특정 컨트롤러에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-154">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=2)]

### <a name="apicontroller-on-multiple-controllers"></a><span data-ttu-id="3b31a-155">여러 컨트롤러의 ApiController</span><span class="sxs-lookup"><span data-stu-id="3b31a-155">ApiController on multiple controllers</span></span>

<span data-ttu-id="3b31a-156">둘 이상의 컨트롤러에서 특성을 사용하는 방법으로 `[ApiController]` 특성으로 주석이 추가된 사용자 지정 기본 컨트롤러 클래스를 만드는 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-156">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="3b31a-157">다음은 사용자 지정 기본 클래스와 여기에서 파생되는 컨트롤러를 보여 주는 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-157">Here's an example showing a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

### <a name="apicontroller-on-an-assembly"></a><span data-ttu-id="3b31a-158">어셈블리의 ApiController</span><span class="sxs-lookup"><span data-stu-id="3b31a-158">ApiController on an assembly</span></span>

<span data-ttu-id="3b31a-159">[호환성 버전](<xref:mvc/compatibility-version>)이 2.2 이상으로 설정된 경우 `[ApiController]` 특성을 어셈블리에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-159">If [compatibility version](<xref:mvc/compatibility-version>) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="3b31a-160">이 방식의 주석은 웹 API 동작을 어셈블리의 모든 컨트롤러에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-160">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="3b31a-161">개별 컨트롤러를 대상으로 옵트아웃하는 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-161">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="3b31a-162">다음 예제와 같이 어셈블리 수준 특성을 `Startup` 클래스에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-162">Apply the assembly-level attribute to the `Startup` class as shown in the following example:</span></span>

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

## <a name="attribute-routing-requirement"></a><span data-ttu-id="3b31a-163">특성 라우팅 요구 사항</span><span class="sxs-lookup"><span data-stu-id="3b31a-163">Attribute routing requirement</span></span>

<span data-ttu-id="3b31a-164">`ApiController` 특성은 특성 라우팅을 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-164">The `ApiController` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="3b31a-165">예:</span><span class="sxs-lookup"><span data-stu-id="3b31a-165">For example:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=1)]

<span data-ttu-id="3b31a-166">작업은 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>에 의해 정의된 [규칙 기반 경로](xref:mvc/controllers/routing#conventional-routing)를 통해 또는 `Startup.Configure`의 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*>에서 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-166">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

## <a name="automatic-http-400-responses"></a><span data-ttu-id="3b31a-167">자동 HTTP 400 응답</span><span class="sxs-lookup"><span data-stu-id="3b31a-167">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="3b31a-168">`ApiController` 특성은 HTTP 400 응답을 자동으로 트리거하는 모델 유효성 검사 오류를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-168">The `ApiController` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="3b31a-169">따라서 다음 코드는 작업 메서드에서 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-169">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

### <a name="default-badrequest-response"></a><span data-ttu-id="3b31a-170">기본 BadRequest 응답</span><span class="sxs-lookup"><span data-stu-id="3b31a-170">Default BadRequest response</span></span> 

<span data-ttu-id="3b31a-171">호환성 버전이 2.2 이상이면 HTTP 400 응답에 대한 기본 응답 형식은 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>입니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-171">With a compatibility version of 2.2 or later, the default response type for HTTP 400 responses is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="3b31a-172">`ValidationProblemDetails` 형식은 [RFC 7807 사양](https://tools.ietf.org/html/rfc7807)을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-172">The `ValidationProblemDetails` type complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

<span data-ttu-id="3b31a-173">기본 응답을 <xref:Microsoft.AspNetCore.Mvc.SerializableError>로 변경하려면 다음 예제에서처럼 `Startup.ConfigureServices`에서 `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-173">To change the default response to <xref:Microsoft.AspNetCore.Mvc.SerializableError>, set the `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` property to `true` in `Startup.ConfigureServices`, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,9)]

### <a name="customize-badrequest-response"></a><span data-ttu-id="3b31a-174">BadRequest 응답 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="3b31a-174">Customize BadRequest response</span></span>

<span data-ttu-id="3b31a-175">유효성 검사 오류로 발생하는 응답을 사용자 지정하려면 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-175">To customize the response that results from a validation error, use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>.</span></span> <span data-ttu-id="3b31a-176">`services.AddMvc().SetCompatibilityVersion` 뒤에 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-176">Add the following highlighted code after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=3-20)]

### <a name="log-automatic-400-responses"></a><span data-ttu-id="3b31a-177">자동 400 응답 기록</span><span class="sxs-lookup"><span data-stu-id="3b31a-177">Log automatic 400 responses</span></span>

<span data-ttu-id="3b31a-178">[How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157)(모델 유효성 검사 오류에서 자동 400 응답을 기록하는 방법(aspnet/AspNetCore.Docs #12157))를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3b31a-178">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400"></a><span data-ttu-id="3b31a-179">자동 400 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="3b31a-179">Disable automatic 400</span></span>

<span data-ttu-id="3b31a-180">자동 400 동작을 사용하지 않으려면 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-180">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="3b31a-181">`Startup.ConfigureServices`에서 `services.AddMvc().SetCompatibilityVersion` 뒤에 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-181">Add the following highlighted code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="3b31a-182">바인딩 소스 매개 변수 유추</span><span class="sxs-lookup"><span data-stu-id="3b31a-182">Binding source parameter inference</span></span>

<span data-ttu-id="3b31a-183">바인딩 소스 특성은 작업 매개 변수 값이 발견되는 위치를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-183">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="3b31a-184">다음 바인딩 소스 특성이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-184">The following binding source attributes exist:</span></span>

|<span data-ttu-id="3b31a-185">특성</span><span class="sxs-lookup"><span data-stu-id="3b31a-185">Attribute</span></span>|<span data-ttu-id="3b31a-186">바인딩 원본</span><span class="sxs-lookup"><span data-stu-id="3b31a-186">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="3b31a-187">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span><span class="sxs-lookup"><span data-stu-id="3b31a-187">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span></span>     | <span data-ttu-id="3b31a-188">요청 본문</span><span class="sxs-lookup"><span data-stu-id="3b31a-188">Request body</span></span> |
|<span data-ttu-id="3b31a-189">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span><span class="sxs-lookup"><span data-stu-id="3b31a-189">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span></span>     | <span data-ttu-id="3b31a-190">요청 본문에서 양식 데이터</span><span class="sxs-lookup"><span data-stu-id="3b31a-190">Form data in the request body</span></span> |
|<span data-ttu-id="3b31a-191">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span><span class="sxs-lookup"><span data-stu-id="3b31a-191">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span></span> | <span data-ttu-id="3b31a-192">요청 헤더</span><span class="sxs-lookup"><span data-stu-id="3b31a-192">Request header</span></span> |
|<span data-ttu-id="3b31a-193">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span><span class="sxs-lookup"><span data-stu-id="3b31a-193">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span></span>   | <span data-ttu-id="3b31a-194">요청 쿼리 문자열 매개 변수</span><span class="sxs-lookup"><span data-stu-id="3b31a-194">Request query string parameter</span></span> |
|<span data-ttu-id="3b31a-195">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="3b31a-195">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span></span>   | <span data-ttu-id="3b31a-196">현재 요청의 경로 데이터</span><span class="sxs-lookup"><span data-stu-id="3b31a-196">Route data from the current request</span></span> |
|<span data-ttu-id="3b31a-197">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span><span class="sxs-lookup"><span data-stu-id="3b31a-197">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span></span> | <span data-ttu-id="3b31a-198">작업 매개 변수로 삽입된 요청 서비스</span><span class="sxs-lookup"><span data-stu-id="3b31a-198">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="3b31a-199">값에 `%2f`(즉, `/`)이 포함될 수 있는 경우 `[FromRoute]`를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-199">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="3b31a-200">`%2f`는 `/`로 이스케이프가 해제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-200">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="3b31a-201">값에 `%2f`가 포함될 수 있으면 `[FromQuery]`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-201">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="3b31a-202">`[ApiController]` 특성 또는 `[FromQuery]` 같은 다른 바인딩 소스 특성 없이, ASP.NET Core 런타임에서 복합 개체 모델 바인더 사용을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-202">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="3b31a-203">복합 개체 모델 바인더는 정의된 순서로 값 공급자로부터 데이터를 풀합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-203">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="3b31a-204">다음 예제에서 `[FromQuery]` 특성은 `discontinuedOnly` 매개 변수 값이 요청 URL의 쿼리 문자열에 제공됨을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-204">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="3b31a-205">`[ApiController]` 특성은 작업 매개 변수의 기본 데이터 원본에 유추 규칙을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-205">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="3b31a-206">이러한 규칙을 통해 작업 매개 변수에 특성을 적용하여 바인딩 소스를 수동으로 식별할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-206">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="3b31a-207">바인딩 소스 유추 규칙은 다음과 같이 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-207">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="3b31a-208">`[FromBody]`는 복합 형식 매개 변수에 대해 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-208">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="3b31a-209">`[FromBody]` 유추 규칙은 <xref:Microsoft.AspNetCore.Http.IFormCollection> 및 <xref:System.Threading.CancellationToken> 같이 특별한 의미를 지닌 복합 기본 제공 형식에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-209">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="3b31a-210">바인딩 소스 유추 코드는 이러한 특별한 형식을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-210">The binding source inference code ignores those special types.</span></span> 
* <span data-ttu-id="3b31a-211">`[FromForm]`은 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 <xref:Microsoft.AspNetCore.Http.IFormFileCollection> 형식의 작업 매개 변수에 대해 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-211">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="3b31a-212">단순 또는 사용자 정의 형식에 대해서는 유추되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-212">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="3b31a-213">`[FromRoute]`는 경로 템플릿에서 매개 변수와 일치하는 작업 매개 변수 이름에 대해 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-213">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="3b31a-214">둘 이상의 경로가 작업 매개 변수와 일치하는 경우 모든 경로 값은 `[FromRoute]`로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-214">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="3b31a-215">`[FromQuery]`는 다른 작업 매개 변수에 대해 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-215">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="3b31a-216">FromBody 유추 메모</span><span class="sxs-lookup"><span data-stu-id="3b31a-216">FromBody inference notes</span></span>

<span data-ttu-id="3b31a-217">`[FromBody]`는 `string` 또는 `int` 같은 단순 형식에 대해 유추되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-217">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="3b31a-218">따라서 해당 기능이 필요한 경우 단순 형식에 `[FromBody]` 특성을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-218">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="3b31a-219">작업에 요청 본문에서 바인딩된 매개 변수가 두 개 이상 있는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-219">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="3b31a-220">예를 들어 다음 모든 작업 메서드 시그니처로 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-220">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="3b31a-221">복합 형식이므로 둘 다에서 유추된 `[FromBody]`</span><span class="sxs-lookup"><span data-stu-id="3b31a-221">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="3b31a-222">하나의 `[FromBody]` 특성, 복합 형식이므로 다른 하나에서 유추됨</span><span class="sxs-lookup"><span data-stu-id="3b31a-222">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="3b31a-223">둘 다의 `[FromBody]` 특성</span><span class="sxs-lookup"><span data-stu-id="3b31a-223">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

> [!NOTE]
> <span data-ttu-id="3b31a-224">ASP.NET Core 2.1에서 목록, 배열 등의 컬렉션 형식 매개 변수를 `[FromQuery]`로 잘못 유추합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-224">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="3b31a-225">이러한 매개 변수가 요청 본문에서 바인딩되는 경우 해당 매개 변수에 `[FromBody]` 특성을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-225">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="3b31a-226">이 문제는 ASP.NET Core 2.2 이상에서 수정됩니다. 즉, 기본적으로 컬렉션 형식 매개 변수를 본문에서 바인딩되도록 유추합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-226">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

### <a name="disable-inference-rules"></a><span data-ttu-id="3b31a-227">유추 규칙 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="3b31a-227">Disable inference rules</span></span>

<span data-ttu-id="3b31a-228">바인딩 소스 유추를 사용하지 않으려면 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters>를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-228">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="3b31a-229">`services.AddMvc().SetCompatibilityVersion` 뒤에 다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-229">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="3b31a-230">다중 파트/폼 데이터 요청 유추</span><span class="sxs-lookup"><span data-stu-id="3b31a-230">Multipart/form-data request inference</span></span>

<span data-ttu-id="3b31a-231">작업 매개 변수를 [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) 특성으로 주석 처리하는 경우 `[ApiController]` 특성이 유추 규칙에 적용됩니다. `multipart/form-data` 요청 콘텐츠 형식이 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-231">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute: the `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="3b31a-232">기본 동작을 사용하지 않으려면 다음 예제처럼 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters>를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-232">To disable the default behavior, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters>  to `true` in `Startup.ConfigureServices`, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="3b31a-233">오류 상태 코드에 대한 문제 세부 정보</span><span class="sxs-lookup"><span data-stu-id="3b31a-233">Problem details for error status codes</span></span>

<span data-ttu-id="3b31a-234">호환성 버전이 2.2 이상인 경우 MVC는 오류 결과(상태 코드 400 이상의 결과)를 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>가 있는 결과로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-234">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="3b31a-235">`ProblemDetails` 형식은 HTTP 응답에 머신에서 읽을 수 있는 오류 세부 정보를 제공하기 위해 [RFC 7807 사양](https://tools.ietf.org/html/rfc7807)을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-235">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="3b31a-236">다음 컨트롤러 작업에서 다음 코드를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="3b31a-236">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="3b31a-237">`NotFound`에 대한 HTTP 응답에는 `ProblemDetails` 본문과 함께 404 상태 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-237">The HTTP response for `NotFound` has a 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="3b31a-238">예:</span><span class="sxs-lookup"><span data-stu-id="3b31a-238">For example:</span></span>

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="customize-problemdetails-response"></a><span data-ttu-id="3b31a-239">ProblemDetails 응답 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="3b31a-239">Customize ProblemDetails response</span></span>

<span data-ttu-id="3b31a-240">`ClientErrorMapping` 속성을 사용하여 `ProblemDetails` 응답의 내용을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-240">Use the `ClientErrorMapping` property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="3b31a-241">예를 들어 다음 코드는 404 응답에 대해 `type` 속성을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-241">For example, the following code updates the `type` property for 404 responses:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

### <a name="disable-problemdetails-response"></a><span data-ttu-id="3b31a-242">ProblemDetails 응답 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="3b31a-242">Disable ProblemDetails response</span></span>

<span data-ttu-id="3b31a-243">`SuppressMapClientErrors` 속성이 `true`로 설정된 경우 `ProblemDetails` 자동 생성이 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-243">The automatic creation of `ProblemDetails` is disabled when the `SuppressMapClientErrors` property is set to `true`.</span></span> <span data-ttu-id="3b31a-244">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3b31a-244">Add the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

## <a name="additional-resources"></a><span data-ttu-id="3b31a-245">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3b31a-245">Additional resources</span></span> 

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
