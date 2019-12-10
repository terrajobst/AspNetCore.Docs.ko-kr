---
title: ASP.NET Core의 모델 바인딩
author: rick-anderson
description: ASP.NET Core에서 모델 바인딩의 작동 방법 및 해당 동작을 사용자 지정하는 방법을 알아봅니다.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 11/21/2019
uid: mvc/models/model-binding
ms.openlocfilehash: a49fec38a6d38bbd33e9461cbcceb39bfe810f5c
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717288"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="aeed6-103">ASP.NET Core의 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="aeed6-103">Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="aeed6-104">이 문서는 모델 바인딩이 무엇인지, 작동 방법 및 해당 동작을 사용자 지정하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="aeed6-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="aeed6-106">모델 바인딩이란</span><span class="sxs-lookup"><span data-stu-id="aeed6-106">What is Model binding</span></span>

<span data-ttu-id="aeed6-107">컨트롤러 및 Razor 페이지는 HTTP 요청에서 제공되는 데이터를 사용하여 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="aeed6-108">예를 들어 경로 데이터는 레코드 키를 제공할 수 있으며, 게시된 양식 필드는 모델의 속성에 대한 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="aeed6-109">이러한 각 값을 검색하고 문자열에서 .NET 형식으로 변환하도록 코드를 작성하는 것은 번거롭고 오류가 발생하기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="aeed6-110">모델 바인딩은 이 프로세스를 자동화합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-110">Model binding automates this process.</span></span> <span data-ttu-id="aeed6-111">모델 바인딩 시스템:</span><span class="sxs-lookup"><span data-stu-id="aeed6-111">The model binding system:</span></span>

* <span data-ttu-id="aeed6-112">경로 데이터, 양식 필드 및 쿼리 문자열과 같은 다양한 원본의 데이터를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="aeed6-113">메서드 매개 변수 및 공용 속성에서 컨트롤러 및 Razor 페이지에 데이터를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="aeed6-114">문자열 데이터를 .NET 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="aeed6-115">복합 형식의 속성을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="aeed6-116">예</span><span class="sxs-lookup"><span data-stu-id="aeed6-116">Example</span></span>

<span data-ttu-id="aeed6-117">다음 작업 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="aeed6-118">앱은 이 URL로 요청을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="aeed6-119">모델 바인딩은 라우팅 시스템이 작업 메서드를 선택한 후 다음 단계를 통해 진행됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="aeed6-120">`GetByID`의 첫 번째 매개 변수, `id`라는 정수를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="aeed6-121">HTTP 요청에서 사용 가능한 원본을 찾고 경로 데이터에서 `id` = "2"를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="aeed6-122">문자열 "2"를 정수 2로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="aeed6-123">`GetByID`의 다음 매개 변수, `dogsOnly`라는 부울을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="aeed6-124">원본을 찾고 쿼리 문자열에서 "DogsOnly=true"를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="aeed6-125">이름 일치는 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="aeed6-126">문자열 "true"를 부울 `true`로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="aeed6-127">그런 다음, 프레임워크는 `id` 매개 변수에 대해 2를 `dogsOnly` 매개 변수에 대해 `true`를 전달하는 `GetById` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="aeed6-128">앞의 예제에서 모델 바인딩 대상은 단순 형식인 메서드 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="aeed6-129">대상은 복합 형식의 속성일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="aeed6-130">각 속성이 성공적으로 바인딩된 후 [모델 유효성 검사](xref:mvc/models/validation)가 해당 속성에 대해 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="aeed6-131">모델에 바인딩되는 데이터의 레코드 및 모든 바인딩 또는 유효성 검사 오류는 [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 또는 [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="aeed6-132">이 프로세스가 성공되었는지 확인하기 위해 앱은 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 플래그를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="aeed6-133">대상</span><span class="sxs-lookup"><span data-stu-id="aeed6-133">Targets</span></span>

<span data-ttu-id="aeed6-134">모델 바인딩은 다음 종류의 대상에 대한 값을 찾으려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="aeed6-135">요청이 라우팅되는 컨트롤러 작업 메서드의 매개 변수</span><span class="sxs-lookup"><span data-stu-id="aeed6-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="aeed6-136">요청이 라우팅되는 Razor Pages 처리기 메서드의 매개 변수</span><span class="sxs-lookup"><span data-stu-id="aeed6-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="aeed6-137">특성으로 지정되는 경우 컨트롤러 또는 `PageModel` 클래스의 공용 속성</span><span class="sxs-lookup"><span data-stu-id="aeed6-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="aeed6-138">[BindProperty] 특성</span><span class="sxs-lookup"><span data-stu-id="aeed6-138">[BindProperty] attribute</span></span>

<span data-ttu-id="aeed6-139">모델 바인딩이 해당 속성을 대상으로 하도록 컨트롤러의 공용 속성 또는 `PageModel` 클래스에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=7-8)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="aeed6-140">[BindProperties] 특성</span><span class="sxs-lookup"><span data-stu-id="aeed6-140">[BindProperties] attribute</span></span>

<span data-ttu-id="aeed6-141">ASP.NET Core 2.1 이상에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="aeed6-142">모델 바인딩이 클래스의 모든 공용 속성을 대상으로 하도록 컨트롤러 또는 `PageModel` 클래스에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="aeed6-143">HTTP GET 요청에 대한 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="aeed6-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="aeed6-144">기본적으로 속성은 HTTP GET 요청에 대해 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="aeed6-145">일반적으로 GET 요청에 대해 필요한 것은 레코드 ID 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="aeed6-146">레코드 ID는 데이터베이스에 있는 항목을 찾는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="aeed6-147">따라서 모델의 인스턴스를 포함하는 속성을 바인딩할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="aeed6-148">속성을 GET 요청의 데이터에 바인딩하려는 시나리오에서 `SupportsGet` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="aeed6-149">원본</span><span class="sxs-lookup"><span data-stu-id="aeed6-149">Sources</span></span>

<span data-ttu-id="aeed6-150">기본적으로 모델 바인딩은 HTTP 요청의 다음 원본에서 키-값 쌍의 양식으로 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="aeed6-151">양식 필드</span><span class="sxs-lookup"><span data-stu-id="aeed6-151">Form fields</span></span>
1. <span data-ttu-id="aeed6-152">요청 본문([[ApiController] 특성이 있는 컨트롤러](xref:web-api/index#binding-source-parameter-inference)의 경우)</span><span class="sxs-lookup"><span data-stu-id="aeed6-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="aeed6-153">경로 데이터</span><span class="sxs-lookup"><span data-stu-id="aeed6-153">Route data</span></span>
1. <span data-ttu-id="aeed6-154">쿼리 문자열 매개 변수</span><span class="sxs-lookup"><span data-stu-id="aeed6-154">Query string parameters</span></span>
1. <span data-ttu-id="aeed6-155">업로드된 파일</span><span class="sxs-lookup"><span data-stu-id="aeed6-155">Uploaded files</span></span>

<span data-ttu-id="aeed6-156">각 대상 매개 변수 또는 속성의 경우, 소스가 이 목록에 표시된 순서대로 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="aeed6-157">몇 가지 예외도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-157">There are a few exceptions:</span></span>

* <span data-ttu-id="aeed6-158">경로 데이터 및 쿼리 문자열 값은 단순 형식에 대해서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="aeed6-159">업로드된 파일은 `IFormFile` 또는 `IEnumerable<IFormFile>`을 구현하는 대상 유형에만 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="aeed6-160">기본 소스가 올바르지 않으면 다음 특성 중 하나를 사용하여 소스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="aeed6-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - 쿼리 문자열에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="aeed6-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - 경로 데이터에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="aeed6-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - 게시된 양식 필드에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="aeed6-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - 요청 본문에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="aeed6-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - HTTP 헤더에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="aeed6-166">이러한 특성:</span><span class="sxs-lookup"><span data-stu-id="aeed6-166">These attributes:</span></span>

* <span data-ttu-id="aeed6-167">다음 예제와 같이 모델 속성(모델 클래스가 아닌)에 개별적으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="aeed6-168">필요에 따라 생성자에서 모델 이름 값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="aeed6-169">이 옵션은 속성 이름이 요청의 값과 일치하지 않는 경우에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="aeed6-170">예를 들어 요청의 값은 다음 예제와 같이 해당 이름에 하이픈이 있는 헤더일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="aeed6-171">[FromBody] 특성</span><span class="sxs-lookup"><span data-stu-id="aeed6-171">[FromBody] attribute</span></span>

<span data-ttu-id="aeed6-172">매개 변수에 `[FromBody]` 특성을 적용하여 HTTP 요청의 본문에서 해당 속성을 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="aeed6-173">ASP.NET Core 런타임은 본문을 읽을 책임을 입력 포맷터에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="aeed6-174">입력 포맷터는 [이 문서의 뒷부분](#input-formatters)에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="aeed6-175">`[FromBody]`이(가) 복합 형식 매개 변수에 적용되는 경우 해당 속성에 적용된 모든 바인딩 소스 특성은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="aeed6-176">예를 들어, 다음 `Create` 작업은 해당 `pet` 매개 변수가 본문에서 채워지도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="aeed6-177">`Pet` 클래스는 `Breed` 속성이 쿼리 문자열 매개 변수에서 채워지도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="aeed6-178">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="aeed6-178">In the preceding example:</span></span>

* <span data-ttu-id="aeed6-179">`[FromQuery]` 특성은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="aeed6-180">`Breed` 속성은 쿼리 문자열 매개 변수에서 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="aeed6-181">입력 포맷터는 본문만 읽고 바인딩 소스 특성은 인식하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="aeed6-182">본문에 적절한 값이 있는 경우 해당 값은 `Breed` 속성을 채우는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="aeed6-183">작업 메서드당 둘 이상의 매개 변수에 `[FromBody]`를 적용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="aeed6-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="aeed6-184">입력 포맷터에서 요청 스트림을 읽으면 더 이상 다른 `[FromBody]` 매개 변수를 바인딩하기 위해 다시 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="aeed6-185">추가 원본</span><span class="sxs-lookup"><span data-stu-id="aeed6-185">Additional sources</span></span>

<span data-ttu-id="aeed6-186">원본 데이터는 *값 공급 기업*에 의해 모델 바인딩 시스템에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="aeed6-187">다른 원본에서 모델 바인딩에 대한 데이터를 가져오는 사용자 지정 값 공급 기업을 작성 및 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="aeed6-188">예를 들어 쿠키 또는 세션 상태의 데이터를 원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="aeed6-189">새 원본에서 데이터를 가져오려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-189">To get data from a new source:</span></span>

* <span data-ttu-id="aeed6-190">`IValueProvider`를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="aeed6-191">`IValueProviderFactory`를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="aeed6-192">`Startup.ConfigureServices`에서 팩터리 클래스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="aeed6-193">샘플 앱은 쿠키에서 값을 가져오는 [값 공급 기업](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) 및 [팩터리](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) 예제를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-193">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="aeed6-194">다음은 `Startup.ConfigureServices`의 등록 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="aeed6-195">표시된 코드는 사용자 지정 값 공급 기업을 모든 기본 제공 값 공급 기업 다음에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="aeed6-196">목록에서 첫 번째로 지정하려면 `Add` 대신에 `Insert(0, new CookieValueProviderFactory())`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="aeed6-197">모델 속성에 대한 원본 없음</span><span class="sxs-lookup"><span data-stu-id="aeed6-197">No source for a model property</span></span>

<span data-ttu-id="aeed6-198">기본적으로 모델 속성에 대한 값이 없으면 모델 상태 오류가 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="aeed6-199">속성은 Null 또는 기본값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="aeed6-200">Nullable 단순 형식은 `null`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="aeed6-201">Null을 허용하지 않는 값 형식은 `default(T)`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="aeed6-202">예를 들어 매개 변수 `int id`는 0으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="aeed6-203">복합 형식의 경우 모델 바인딩은 속성을 설정하지 않고 기본 생성자를 사용하여 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="aeed6-204">배열은 `byte[]` 배열이 `null`로 설정되는 점을 제외하고 `Array.Empty<T>()`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="aeed6-205">모델 속성에 대한 양식 필드에 아무것도 없을 때 모델 상태가 무효화되어야 하는 경우 [[BindRequired] 특성](#bindrequired-attribute)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [[BindRequired] attribute](#bindrequired-attribute).</span></span>

<span data-ttu-id="aeed6-206">이 `[BindRequired]` 동작은 요청 본문의 JSON 또는 XML 데이터가 아니라 게시된 양식 데이터의 모델 바인딩에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="aeed6-207">요청 본문 데이터는 [입력 포맷터](#input-formatters)에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="aeed6-208">형식 변환 오류</span><span class="sxs-lookup"><span data-stu-id="aeed6-208">Type conversion errors</span></span>

<span data-ttu-id="aeed6-209">원본이 있지만 대상 형식으로 변환될 수 없는 경우 모델 상태는 잘못된 것으로 플래그가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="aeed6-210">이전 섹션에서 설명한 것처럼 대상 매개 변수 또는 속성은 Null 또는 기본값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="aeed6-211">`[ApiController]` 특성이 있는 API 컨트롤러에서 잘못된 모델 상태로 자동 HTTP 400 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="aeed6-212">Razor 페이지에서 페이지를 오류 메시지와 함께 다시 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="aeed6-213">클라이언트 쪽 유효성 검사는 Razor Pages 양식으로 제출될 수 있는 대부분의 잘못된 데이터를 catch합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="aeed6-214">이 유효성 검사는 위의 강조 표시된 코드를 트리거하기 어렵게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="aeed6-215">샘플 앱은 **Hire Date** 필드에 잘못된 데이터를 배치하고 양식을 제출하는 **잘못된 데이터로 제출** 단추를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="aeed6-216">이 단추는 데이터 변환 오류가 발생하는 경우 페이지를 다시 표시하기 위해 코드가 작동하는 방식을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="aeed6-217">위의 코드로 페이지가 다시 표시될 때 잘못된 입력은 양식 필드에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="aeed6-218">이는 모델 속성이 Null 또는 기본값으로 설정되었기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="aeed6-219">잘못된 입력은 오류 메시지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="aeed6-220">그러나 양식 필드에 잘못된 데이터를 다시 표시하려는 경우 모델 속성을 문자열로 만들고 데이터 변환을 수동으로 수행하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="aeed6-221">형식 변환 오류를 모델 상태 오류로 만들려 하지 않는 경우 동일한 전략을 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="aeed6-222">이 경우 모델 속성을 문자열로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="aeed6-223">단순 형식</span><span class="sxs-lookup"><span data-stu-id="aeed6-223">Simple types</span></span>

<span data-ttu-id="aeed6-224">모델 바인더에서 원본 문자열을 변환할 수 있는 단순 형식은 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="aeed6-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="aeed6-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="aeed6-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="aeed6-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="aeed6-227">Char</span><span class="sxs-lookup"><span data-stu-id="aeed6-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="aeed6-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="aeed6-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="aeed6-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="aeed6-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="aeed6-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="aeed6-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="aeed6-231">double</span><span class="sxs-lookup"><span data-stu-id="aeed6-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="aeed6-232">Enum</span><span class="sxs-lookup"><span data-stu-id="aeed6-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="aeed6-233">Guid</span><span class="sxs-lookup"><span data-stu-id="aeed6-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="aeed6-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="aeed6-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="aeed6-235">Single</span><span class="sxs-lookup"><span data-stu-id="aeed6-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="aeed6-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="aeed6-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="aeed6-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="aeed6-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="aeed6-238">Uri</span><span class="sxs-lookup"><span data-stu-id="aeed6-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="aeed6-239">Version</span><span class="sxs-lookup"><span data-stu-id="aeed6-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="aeed6-240">복합 형식</span><span class="sxs-lookup"><span data-stu-id="aeed6-240">Complex types</span></span>

<span data-ttu-id="aeed6-241">복합 형식에 공용 기본 생성자와 바인딩할 공용 쓰기 가능 속성이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="aeed6-242">모델 바인딩이 발생하면 클래스는 공용 기본 생성자를 사용하여 인스턴스화됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="aeed6-243">복합 형식의 각 속성의 경우 모델 바인딩은 이름 패턴*prefix.property_name*에 대한 원본을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="aeed6-244">아무것도 없는 경우 접두사 없이 *property_name*만을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="aeed6-245">매개 변수에 대한 바인딩의 경우 접두사는 매개 변수 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="aeed6-246">`PageModel` 공용 속성에 대한 바인딩의 경우 접두사는 공용 속성 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="aeed6-247">일부 특성에는 매개 변수의 기본 사용 또는 속성 이름을 재정의할 수 있도록 하는 `Prefix` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="aeed6-248">예를 들어 복합 형식이 다음 `Instructor` 클래스라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="aeed6-249">접두사 = 매개 변수 이름</span><span class="sxs-lookup"><span data-stu-id="aeed6-249">Prefix = parameter name</span></span>

<span data-ttu-id="aeed6-250">바인딩될 모델이 `instructorToUpdate`라는 매개 변수인 경우:</span><span class="sxs-lookup"><span data-stu-id="aeed6-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="aeed6-251">모델 바인딩은 키 `instructorToUpdate.ID`에 대한 원본을 찾아 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="aeed6-252">없는 경우 접두사 없이 `ID`를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="aeed6-253">접두사 = 속성 이름</span><span class="sxs-lookup"><span data-stu-id="aeed6-253">Prefix = property name</span></span>

<span data-ttu-id="aeed6-254">바인딩될 모델이 컨트롤러 또는 `PageModel` 클래스의 `Instructor`라는 속성인 경우:</span><span class="sxs-lookup"><span data-stu-id="aeed6-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="aeed6-255">모델 바인딩은 키 `Instructor.ID`에 대한 원본을 찾아 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="aeed6-256">없는 경우 접두사 없이 `ID`를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="aeed6-257">사용자 지정 접두사</span><span class="sxs-lookup"><span data-stu-id="aeed6-257">Custom prefix</span></span>

<span data-ttu-id="aeed6-258">바인딩될 모델이 `instructorToUpdate`라는 매개 변수이고 `Bind` 특성이 접두사로 `Instructor`를 지정하는 경우:</span><span class="sxs-lookup"><span data-stu-id="aeed6-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="aeed6-259">모델 바인딩은 키 `Instructor.ID`에 대한 원본을 찾아 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="aeed6-260">없는 경우 접두사 없이 `ID`를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="aeed6-261">복합 형식 대상에 대한 특성</span><span class="sxs-lookup"><span data-stu-id="aeed6-261">Attributes for complex type targets</span></span>

<span data-ttu-id="aeed6-262">여러 기본 제공 특성은 복합 형식의 모델 바인딩을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="aeed6-263">이러한 특성은 게시된 양식 데이터가 값의 원본일 때 모델 바인딩에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="aeed6-264">게시된 JSON 및 XML 요청 본문을 처리하는 입력 포맷터에는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="aeed6-265">입력 포맷터는 [이 문서의 뒷부분](#input-formatters)에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="aeed6-266">[모델 유효성 검사](xref:mvc/models/validation#required-attribute)에서 `[Required]` 특성의 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aeed6-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="aeed6-267">[BindRequired] 특성</span><span class="sxs-lookup"><span data-stu-id="aeed6-267">[BindRequired] attribute</span></span>

<span data-ttu-id="aeed6-268">메서드 매개 변수가 아닌 모델 속성에만 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="aeed6-269">모델의 속성에 대한 바인딩이 발생할 수 없는 경우 모델 바인딩이 모델 상태 오류를 추가하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="aeed6-270">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="aeed6-271">[BindNever] 특성</span><span class="sxs-lookup"><span data-stu-id="aeed6-271">[BindNever] attribute</span></span>

<span data-ttu-id="aeed6-272">메서드 매개 변수가 아닌 모델 속성에만 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="aeed6-273">모델 바인딩이 모델의 속성을 설정하는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="aeed6-274">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="aeed6-275">[Bind] 특성</span><span class="sxs-lookup"><span data-stu-id="aeed6-275">[Bind] attribute</span></span>

<span data-ttu-id="aeed6-276">클래스 또는 메서드 매개 변수에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="aeed6-277">모델 바인딩에 포함되어야 하는 모델의 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="aeed6-278">다음 예제에서 모든 처리기 또는 작업 메서드가 호출될 때 지정된 속성의 `Instructor` 모델만 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="aeed6-279">다음 예제에서 `OnPost` 메서드가 호출될 때 지정된 속성의 `Instructor` 모델만 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="aeed6-280">`[Bind]` 특성은 *만들기* 시나리오에서 초과 게시를 방지하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="aeed6-281">제외된 속성은 변경되지 않은 채로 남겨지는 대신 Null 또는 기본값으로 설정되기 때문에 편집 시나리오에서 잘 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="aeed6-282">초과 게시에 대한 방어의 경우 뷰 모델이 `[Bind]` 특성보다 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="aeed6-283">자세한 내용은 [초과 게시에 대한 보안 정보](xref:data/ef-mvc/crud#security-note-about-overposting)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aeed6-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="aeed6-284">컬렉션</span><span class="sxs-lookup"><span data-stu-id="aeed6-284">Collections</span></span>

<span data-ttu-id="aeed6-285">단순 형식의 컬렉션인 대상의 경우 모델 바인딩은 *parameter_name* 또는 *property_name*에 대한 일치 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="aeed6-286">일치하는 항목이 없는 경우 접두사 없이 지원되는 양식 중 하나를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="aeed6-287">예:</span><span class="sxs-lookup"><span data-stu-id="aeed6-287">For example:</span></span>

* <span data-ttu-id="aeed6-288">바인딩되는 매개 변수가 `selectedCourses`라는 배열이라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="aeed6-289">양식 또는 쿼리 문자열 데이터는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-289">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="aeed6-290">다음 형식은 양식 데이터에서만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="aeed6-291">앞의 모든 예제 형식의 경우 모델 바인딩은 두 항목의 배열을 `selectedCourses` 매개 변수에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="aeed6-292">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="aeed6-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="aeed6-293">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="aeed6-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="aeed6-294">아래 첨자 숫자(... [0] ... [1] ...)를 사용하는 데이터 형식은 0부터 시작하여 순차적으로 번호가 매겨지는지 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="aeed6-295">아래 첨자 번호에 간격이 있는 경우 간격 뒤에 있는 모든 항목은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="aeed6-296">예를 들어 아래 첨자가 0과 1 대신 0과 2인 경우 두 번째 항목은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="aeed6-297">사전</span><span class="sxs-lookup"><span data-stu-id="aeed6-297">Dictionaries</span></span>

<span data-ttu-id="aeed6-298">`Dictionary` 대상의 경우 모델 바인딩은 *parameter_name* 또는 *property_name*에 대한 일치 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="aeed6-299">일치하는 항목이 없는 경우 접두사 없이 지원되는 양식 중 하나를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="aeed6-300">예:</span><span class="sxs-lookup"><span data-stu-id="aeed6-300">For example:</span></span>

* <span data-ttu-id="aeed6-301">대상 매개 변수가 `selectedCourses`라는 `Dictionary<int, string>`라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="aeed6-302">게시된 양식 또는 쿼리 문자열 데이터는 다음 예제 중 하나와 같을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-302">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="aeed6-303">앞의 모든 예제 형식의 경우 모델 바인딩은 두 항목의 사전을 `selectedCourses` 매개 변수에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="aeed6-304">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="aeed6-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="aeed6-305">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="aeed6-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="aeed6-306">모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작</span><span class="sxs-lookup"><span data-stu-id="aeed6-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="aeed6-307">ASP.NET Core 경로 값 공급자와 쿼리 문자열 값 공급자:</span><span class="sxs-lookup"><span data-stu-id="aeed6-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="aeed6-308">값을 고정 문화권으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="aeed6-309">URL을 고정 문화권으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="aeed6-310">이와 대조적으로, 양식 데이터에서 가져온 값은 문화권을 구분하여 변환을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="aeed6-311">이는 기본적으로 URL을 로캘 간에 공유할 수 있도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="aeed6-312">ASP.NET Core 경로 값 공급자와 쿼리 문자열 값 공급자가 문화권 구분 변환을 수행하도록 하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="aeed6-313"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>에서 상속됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="aeed6-314">[QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) 또는 [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)에서 코드를 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="aeed6-315">값 공급자 생성자로 전달된 [문화권 값](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)을 [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-315">Replace the [culture value](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="aeed6-316">MVC 옵션의 기본 값 공급자 팩터리를 새 값으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples/StartupMB.cs?name=snippet)]
[!code-csharp[](model-binding/samples/StartupMB.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="aeed6-317">특수 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="aeed6-317">Special data types</span></span>

<span data-ttu-id="aeed6-318">모델 바인딩이 처리할 수 있는 일부 특수 데이터 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="aeed6-319">IFormFile 및 IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="aeed6-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="aeed6-320">HTTP 요청에 포함되는 업로드된 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="aeed6-321">또한 여러 파일에 대해 `IEnumerable<IFormFile>`이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="aeed6-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="aeed6-322">CancellationToken</span></span>

<span data-ttu-id="aeed6-323">비동기 컨트롤러에서 작업을 취소하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="aeed6-324">FormCollection</span><span class="sxs-lookup"><span data-stu-id="aeed6-324">FormCollection</span></span>

<span data-ttu-id="aeed6-325">게시된 양식 데이터에서 모든 값을 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="aeed6-326">입력 포맷터</span><span class="sxs-lookup"><span data-stu-id="aeed6-326">Input formatters</span></span>

<span data-ttu-id="aeed6-327">요청 본문의 데이터는 JSON, XML 또는 일부 다른 형식일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="aeed6-328">이 데이터를 구문 분석하기 위해 모델 바인딩은 특정 콘텐츠 유형을 처리하도록 구성된 *입력 포맷터*를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="aeed6-329">기본적으로 ASP.NET Core는 JSON 데이터를 처리하기 위한 JSON 기반 입력 포맷터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="aeed6-330">다른 콘텐츠 형식에 대해 다른 포맷터를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="aeed6-331">ASP.NET Core는 [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 특성을 기반으로 입력 포맷터를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="aeed6-332">특성이 없는 경우 [Content-Type 헤더](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="aeed6-333">기본 제공 XML 입력 포맷터를 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="aeed6-334">`Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="aeed6-335">`Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> 또는 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="aeed6-336">요청 본문에서 XML을 필요로 하는 컨트롤러 클래스 또는 작업 메서드에 `Consumes` 특성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="aeed6-337">자세한 내용은 [XML Serialization 소개](/dotnet/standard/serialization/introducing-xml-serialization)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aeed6-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="aeed6-338">모델 바인딩에서 지정된 형식 제외</span><span class="sxs-lookup"><span data-stu-id="aeed6-338">Exclude specified types from model binding</span></span>

<span data-ttu-id="aeed6-339">모델 바인딩 및 시스템 동작의 유효성 검사는 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-339">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="aeed6-340">[MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)에 세부 정보 공급 기업을 추가하여 `ModelMetadata`를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-340">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="aeed6-341">기본 제공 세부 정보 공급 기업을 지정된 형식에 대한 모델 바인딩 또는 유효성 검사를 비활성화하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-341">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="aeed6-342">지정된 형식의 모든 모델에 대한 모델 바인딩을 비활성화하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-342">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="aeed6-343">예를 들어 `System.Version` 형식의 모든 모델에 대한 모델 바인딩을 비활성화하려면:</span><span class="sxs-lookup"><span data-stu-id="aeed6-343">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="aeed6-344">지정된 형식의 속성에 대한 유효성 검사를 비활성화하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-344">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="aeed6-345">예를 들어 `System.Guid` 형식의 속성에 대한 유효성 검사를 비활성화하려면:</span><span class="sxs-lookup"><span data-stu-id="aeed6-345">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="aeed6-346">사용자 지정 모델 바인더</span><span class="sxs-lookup"><span data-stu-id="aeed6-346">Custom model binders</span></span>

<span data-ttu-id="aeed6-347">사용자 지정 모델 바인더를 작성하고 지정된 대상에 대해 선택하도록 `[ModelBinder]` 특성을 사용하여 모델 바인딩을 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-347">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="aeed6-348">[사용자 모델 바인딩](xref:mvc/advanced/custom-model-binding)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="aeed6-348">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="aeed6-349">수동 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="aeed6-349">Manual model binding</span></span>

<span data-ttu-id="aeed6-350"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> 메서드를 사용하여 모델 바인딩을 수동으로 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-350">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="aeed6-351">메서드는 `ControllerBase` 및 `PageModel` 클래스에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-351">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="aeed6-352">메서드 오버로드를 통해 사용할 접두사 및 값 공급 기업을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-352">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="aeed6-353">모델 바인딩이 실패하는 경우 메서드는 `false`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-353">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="aeed6-354">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-354">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="aeed6-355">[FromServices] 특성</span><span class="sxs-lookup"><span data-stu-id="aeed6-355">[FromServices] attribute</span></span>

<span data-ttu-id="aeed6-356">이 특성의 이름은 데이터 원본을 지정하는 모델 바인딩 특성의 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-356">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="aeed6-357">그러나 값 공급 기업의 바인딩 데이터에 대한 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-357">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="aeed6-358">[종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에서 형식의 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-358">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="aeed6-359">특정 메서드가 호출되는 경우에만 서비스가 필요할 때 생성자 주입에 대안을 제공하는 것이 목적입니다.</span><span class="sxs-lookup"><span data-stu-id="aeed6-359">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aeed6-360">추가 자료</span><span class="sxs-lookup"><span data-stu-id="aeed6-360">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>
