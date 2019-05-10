---
title: ASP.NET Core Web API에서 컨트롤러 작업 반환 형식
author: scottaddie
description: ASP.NET Core Web API에서 다양한 컨트롤러 작업 메서드 반환 형식을 사용하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/04/2019
uid: web-api/action-return-types
ms.openlocfilehash: 180d76c2c2e53dbf64b8fcc5cdc6d2b6f4dab6eb
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887118"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="b7976-103">ASP.NET Core Web API에서 컨트롤러 작업 반환 형식</span><span class="sxs-lookup"><span data-stu-id="b7976-103">Controller action return types in ASP.NET Core Web API</span></span>

<span data-ttu-id="b7976-104">작성자: [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="b7976-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="b7976-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7976-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b7976-106">ASP.NET Core에서는 Web API 컨트롤러 작업 반환 형식에 다음 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-106">ASP.NET Core offers the following options for Web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="b7976-107">특정 형식</span><span class="sxs-lookup"><span data-stu-id="b7976-107">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="b7976-108">IActionResult</span><span class="sxs-lookup"><span data-stu-id="b7976-108">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="b7976-109">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="b7976-109">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="b7976-110">특정 형식</span><span class="sxs-lookup"><span data-stu-id="b7976-110">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="b7976-111">IActionResult</span><span class="sxs-lookup"><span data-stu-id="b7976-111">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="b7976-112">이 문서에서는 각 반환 형식을 사용하는 것이 가장 적합한 경우를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-112">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="b7976-113">특정 형식</span><span class="sxs-lookup"><span data-stu-id="b7976-113">Specific type</span></span>

<span data-ttu-id="b7976-114">가장 간단한 작업은 기본 또는 복합 데이터 형식을 반환합니다(예: `string` 또는 사용자 지정 개체 형식).</span><span class="sxs-lookup"><span data-stu-id="b7976-114">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="b7976-115">다음 작업을 사용하여 사용자 지정 `Product` 개체의 컬렉션을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-115">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="b7976-116">작업 실행 중에 보호할 알려진 조건 없이 특정 형식을 반환하는 것으로 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-116">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="b7976-117">매개 변수 제약 조건 유효성 검사가 필요하지 않으므로 위의 작업은 매개 변수를 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-117">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="b7976-118">알려진 조건이 작업에서 고려되지 않으면 여러 반환 경로가 도입됩니다’.</span><span class="sxs-lookup"><span data-stu-id="b7976-118">When known conditions need to be accounted for in an action, multiple return paths are introduced.</span></span> <span data-ttu-id="b7976-119">이 경우에 기본 또는 복합적인 반환 형식의 [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) 반환 형식을 혼합하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-119">In such a case, it's common to mix an [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) return type with the primitive or complex return type.</span></span> <span data-ttu-id="b7976-120">이 종류의 동작을 수용하기 위해 [IActionResult](#iactionresult-type) 또는 [ActionResult\<T>](#actionresultt-type) 중 하나가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-120">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="b7976-121">IActionResult 형식</span><span class="sxs-lookup"><span data-stu-id="b7976-121">IActionResult type</span></span>

<span data-ttu-id="b7976-122">여러 [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) 반환 형식을 동작에서 사용할 수 있는 경우 [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) 반환 형식이 적절합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-122">The [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) return type is appropriate when multiple [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) return types are possible in an action.</span></span> <span data-ttu-id="b7976-123">`ActionResult` 형식은 다양한 HTTP 상태 코드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-123">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="b7976-124">이 범주에 속하는 몇 가지 일반적인 반환 형식은 [BadRequestResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestresult)(400), [NotFoundResult](/dotnet/api/microsoft.aspnetcore.mvc.notfoundresult)(404) 및 [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult)(200)입니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-124">Some common return types falling into this category are [BadRequestResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestresult) (400), [NotFoundResult](/dotnet/api/microsoft.aspnetcore.mvc.notfoundresult) (404), and [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200).</span></span>

<span data-ttu-id="b7976-125">작업에서 자유롭게 여러 반환 형식 및 경로가 있기 때문에 [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute.-ctor) 특성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-125">Because there are multiple return types and paths in the action, liberal use of the [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute.-ctor) attribute is necessary.</span></span> <span data-ttu-id="b7976-126">이 특성은 [Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger)와 같은 도구에서 생성한 API 도움말 페이지에 대해 설명이 포함된 응답 세부 정보를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-126">This attribute produces more descriptive response details for API help pages generated by tools like [Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="b7976-127">`[ProducesResponseType]`은 작업에서 반환한 알려진 형식 및 HTTP 상태 코드을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-127">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="b7976-128">동기화 작업</span><span class="sxs-lookup"><span data-stu-id="b7976-128">Synchronous action</span></span>

<span data-ttu-id="b7976-129">두 가지 가능한 반환 형식이 포함된 다음과 같은 동기 작업을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-129">Consider the following synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

<span data-ttu-id="b7976-130">위의 작업에서 `id`에서 표시하는 제품이 기본 데이터 저장소에 없는 경우 404 상태 코드가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-130">In the preceding action, a 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="b7976-131">[NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 도우미 메서드는 `return new NotFoundResult();`에 대한 바로 가기로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-131">The [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) helper method is invoked as a shortcut to `return new NotFoundResult();`.</span></span> <span data-ttu-id="b7976-132">제품이 있으면 페이로드를 나타내는 `Product` 개체가 200 상태 코드와 함께 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-132">If the product does exist, a `Product` object representing the payload is returned with a 200 status code.</span></span> <span data-ttu-id="b7976-133">[확인](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.ok) 도우미 메서드가 `return new OkObjectResult(product);`의 축약형으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-133">The [Ok](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.ok) helper method is invoked as the shorthand form of `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="b7976-134">비동기 작업</span><span class="sxs-lookup"><span data-stu-id="b7976-134">Asynchronous action</span></span>

<span data-ttu-id="b7976-135">두 가지 가능한 반환 형식이 포함된 다음과 같은 비동기 작업을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-135">Consider the following asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

<span data-ttu-id="b7976-136">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="b7976-136">In the preceding code:</span></span>

* <span data-ttu-id="b7976-137">제품 설명에 “XYZ 위젯”이 포함된 경우 ASP.NET Core 런타임에서 400 상태 코드([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*))가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-137">A 400 status code ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) is returned by the ASP.NET Core runtime when the product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="b7976-138">제품이 만들어지면 [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) 메서드에서 201 상태 코드가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-138">A 201 status code is generated by the [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) method when a product is created.</span></span> <span data-ttu-id="b7976-139">이 코드 경로에 `Product` 개체가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-139">In this code path, the `Product` object is returned.</span></span>

<span data-ttu-id="b7976-140">예를 들어 다음 모델은 요청이 `Name` 및 `Description` 속성을 포함해야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-140">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="b7976-141">따라서 요청에서 `Name` 및 `Description`을 제공하지 못하면 모델 유효성 검사에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-141">Therefore, failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b7976-142">ASP.NET Core 2.1 이상의 [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성이 적용되면 모델 유효성 검사 오류로 인해 400 상태 코드가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-142">If the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later  is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="b7976-143">자세한 정보는 [자동 HTTP 400 응답](xref:web-api/index#automatic-http-400-responses)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b7976-143">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="b7976-144">ActionResult\<T> 형식</span><span class="sxs-lookup"><span data-stu-id="b7976-144">ActionResult\<T> type</span></span>

<span data-ttu-id="b7976-145">ASP.NET Core 2.1은 Web API 컨트롤러 작업에 대해 [ActionResult\<T>](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1) 반환 형식을 소개합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-145">ASP.NET Core 2.1 introduces the [ActionResult\<T>](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1) return type for Web API controller actions.</span></span> <span data-ttu-id="b7976-146">[ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult)에서 파생된 형식을 반환하거나 [특정 형식](#specific-type)을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-146">It enables you to return a type deriving from [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) or return a [specific type](#specific-type).</span></span> <span data-ttu-id="b7976-147">`ActionResult<T>`는 [IActionResult 형식](#iactionresult-type)을 통해 다음과 같은 혜택을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-147">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="b7976-148">[[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute) 특성의 `Type` 속성을 제외할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-148">The [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="b7976-149">예를 들어 `[ProducesResponseType(200, Type = typeof(Product))]`은 `[ProducesResponseType(200)]`으로 단순화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-149">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="b7976-150">작업의 예상 반환 형식은 대신 `ActionResult<T>`의 `T`에서 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-150">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="b7976-151">[암시적 캐스트 연산자](/dotnet/csharp/language-reference/keywords/implicit)는 `T` 및 `ActionResult` 모두를 `ActionResult<T>`로 변환하도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-151">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="b7976-152">`T`는 [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult)로 변환합니다. 즉, `return new ObjectResult(T);`는 `return T;`로 간소화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-152">`T` converts to [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult), which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="b7976-153">C#은 인터페이스에서 암시적 캐스트 연산자를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-153">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="b7976-154">따라서 인터페이스를 구체적인 형식으로 전환하려면 `ActionResult<T>`를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-154">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="b7976-155">예를 들어 다음 예제에서 `IEnumerable`을 사용하면 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-155">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get()
{
    return _repository.GetProducts();
}
```

<span data-ttu-id="b7976-156">이전 코드를 수정하는 한 가지 옵션으로 `_repository.GetProducts().ToList();`를 반환해 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-156">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="b7976-157">대부분의 작업에는 특정 반환 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-157">Most actions have a specific return type.</span></span> <span data-ttu-id="b7976-158">작업을 실행하는 동안 예기치 않은 조건이 발생할 수 있습니다. 이 경우에 특정 형식이 반환되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-158">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="b7976-159">예를 들어 작업의 입력 매개 변수는 모델 유효성 검사에 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-159">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="b7976-160">이러한 경우에 일반적으로 특정 형식이 아닌 적절한 `ActionResult` 형식을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-160">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="b7976-161">동기화 작업</span><span class="sxs-lookup"><span data-stu-id="b7976-161">Synchronous action</span></span>

<span data-ttu-id="b7976-162">두 가지 가능한 반환 형식이 포함된 동기 작업을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-162">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

<span data-ttu-id="b7976-163">위의 코드에서 404 상태 코드는 제품이 데이터베이스에 존재하지 않는 경우에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-163">In the preceding code, a 404 status code is returned when the product doesn't exist in the database.</span></span> <span data-ttu-id="b7976-164">제품이 없는 경우 해당하는 `Product` 개체가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-164">If the product does exist, the corresponding `Product` object is returned.</span></span> <span data-ttu-id="b7976-165">ASP.NET Core 2.1 이전에 `return product;` 줄은 `return Ok(product);`이었습니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-165">Before ASP.NET Core 2.1, the `return product;` line would have been `return Ok(product);`.</span></span>

> [!TIP]
> <span data-ttu-id="b7976-166">ASP.NET Core 2.1을 기준으로 작업 매개 변수 바인딩 원본 유추는 컨트롤러 클래스를 `[ApiController]` 특성으로 데코레이팅할 때 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-166">As of ASP.NET Core 2.1, action parameter binding source inference is enabled when a controller class is decorated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="b7976-167">경로 템플릿의 이름과 일치하는 매개 변수 이름은 요청 경로 데이터를 사용하여 자동으로 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-167">A parameter name matching a name in the route template is automatically bound using the request route data.</span></span> <span data-ttu-id="b7976-168">따라서 위 작업의 `id` 매개 변수가 [[FromRoute]](/dotnet/api/microsoft.aspnetcore.mvc.fromrouteattribute) 특성을 사용하여 명시적으로 주석으로 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-168">Consequently, the preceding action's `id` parameter isn't explicitly annotated with the [[FromRoute]](/dotnet/api/microsoft.aspnetcore.mvc.fromrouteattribute) attribute.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="b7976-169">비동기 작업</span><span class="sxs-lookup"><span data-stu-id="b7976-169">Asynchronous action</span></span>

<span data-ttu-id="b7976-170">두 가지 가능한 반환 형식이 포함된 비동기 작업을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-170">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

<span data-ttu-id="b7976-171">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="b7976-171">In the preceding code:</span></span>

* <span data-ttu-id="b7976-172">다음과 같은 경우 ASP.NET Core 런타임에서 400 상태 코드([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*))가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-172">A 400 status code ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="b7976-173">[[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성이 적용되었고 모델 유효성 검사에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-173">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="b7976-174">제품 설명에 “XYZ 위젯”이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-174">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="b7976-175">제품이 만들어지면 [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) 메서드에서 201 상태 코드가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-175">A 201 status code is generated by the [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) method when a product is created.</span></span> <span data-ttu-id="b7976-176">이 코드 경로에 `Product` 개체가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-176">In this code path, the `Product` object is returned.</span></span>

> [!TIP]
> <span data-ttu-id="b7976-177">ASP.NET Core 2.1을 기준으로 작업 매개 변수 바인딩 원본 유추는 컨트롤러 클래스를 `[ApiController]` 특성으로 데코레이팅할 때 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-177">As of ASP.NET Core 2.1, action parameter binding source inference is enabled when a controller class is decorated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="b7976-178">복합 형식 매개 변수는 요청 본문을 사용하여 자동으로 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-178">Complex type parameters are automatically bound using the request body.</span></span> <span data-ttu-id="b7976-179">따라서 위 작업의 `product` 매개 변수가 [[FromBody]](/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute) 특성을 사용하여 명시적으로 주석으로 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7976-179">Consequently, the preceding action's `product` parameter isn't explicitly annotated with the [[FromBody]](/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute) attribute.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b7976-180">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b7976-180">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
