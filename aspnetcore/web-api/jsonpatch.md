---
title: ASP.NET Core Web API의 JsonPatch
author: rick-anderson
description: ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 11/01/2019
uid: web-api/jsonpatch
ms.openlocfilehash: e57556e4b3fba55c6c187092593ffab4e31ee2d9
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727081"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="be575-103">ASP.NET Core Web API의 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="be575-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="be575-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="be575-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="be575-105">이 문서에서는 ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="be575-106">패키지 설치</span><span class="sxs-lookup"><span data-stu-id="be575-106">Package installation</span></span>

<span data-ttu-id="be575-107">JsonPatch 지원은 `Microsoft.AspNetCore.Mvc.NewtonsoftJson` 패키지를 통해 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="be575-107">Support for JsonPatch is enabled using the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package.</span></span> <span data-ttu-id="be575-108">이 기능을 사용하도록 설정하려면 앱에서 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-108">To enable this feature, apps must:</span></span>

* <span data-ttu-id="be575-109">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-109">Install the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
* <span data-ttu-id="be575-110">`AddNewtonsoftJson`에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-110">Update the project's `Startup.ConfigureServices` method to include a call to `AddNewtonsoftJson`:</span></span>

  ```csharp
  services
      .AddControllersWithViews()
      .AddNewtonsoftJson();
  ```

<span data-ttu-id="be575-111">`AddNewtonsoftJson`은 MVC 서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="be575-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

  * `AddRazorPages`
  * `AddControllersWithViews`
  * `AddControllers`

## <a name="jsonpatch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="be575-112">JsonPatch, AddNewtonsoftJson 및 System.Text.Json과 호환됩니다.</span><span class="sxs-lookup"><span data-stu-id="be575-112">JsonPatch, AddNewtonsoftJson, and System.Text.Json</span></span>
  
<span data-ttu-id="be575-113">`AddNewtonsoftJson`은 **모든** JSON 콘텐츠의 형식을 지정하는 데 사용되는 `System.Text.Json` 기반 입력 및 출력 포맷터를 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-113">`AddNewtonsoftJson` replaces the `System.Text.Json` based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="be575-114">`Newtonsoft.Json`를 사용하여 `JsonPatch` 지원을 추가하지만 다른 포맷터를 변경하지 않으려면 프로젝트의 `Startup.ConfigureServices`를 다음과 같이 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-114">To add support for `JsonPatch` using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="be575-115">앞의 코드에는 [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)에 대한 참조 및 다음 using 문이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-115">The preceding code requires a reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) and the following using statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="be575-116">PATCH HTTP 요청 메서드</span><span class="sxs-lookup"><span data-stu-id="be575-116">PATCH HTTP request method</span></span>

<span data-ttu-id="be575-117">PUT 및 [PATCH](https://tools.ietf.org/html/rfc5789) 메서드는 기존 리소스를 업데이트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be575-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="be575-118">두 메서드의 차이점은 PUT은 전체 리소스를 바꾸지만, PATCH는 변경 내용만 지정한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="be575-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="be575-119">JSON 패치</span><span class="sxs-lookup"><span data-stu-id="be575-119">JSON Patch</span></span>

<span data-ttu-id="be575-120">[JSON 패치](https://tools.ietf.org/html/rfc6902)는 리소스에 적용할 업데이트를 지정하기 위한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="be575-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="be575-121">JSON 패치 문서에는 작업 배열이 포함됩니다. </span><span class="sxs-lookup"><span data-stu-id="be575-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="be575-122">각 작업은 배열 요소 추가 또는 속성 값 바꾸기와 같은 특정 유형의 변경을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-122">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="be575-123">예를 들어 다음 JSON 문서는 리소스, 리소스의 JSON 패치 문서 및 패치 작업을 적용한 결과를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="be575-123">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="be575-124">리소스 예제</span><span class="sxs-lookup"><span data-stu-id="be575-124">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="be575-125">JSON 패치 예제</span><span class="sxs-lookup"><span data-stu-id="be575-125">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="be575-126">앞의 JSON에서:</span><span class="sxs-lookup"><span data-stu-id="be575-126">In the preceding JSON:</span></span>

* <span data-ttu-id="be575-127">`op` 속성은 작업 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="be575-127">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="be575-128">`path` 속성은 업데이트할 요소를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="be575-128">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="be575-129">`value` 속성은 새 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-129">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="be575-130">패치 후 리소스</span><span class="sxs-lookup"><span data-stu-id="be575-130">Resource after patch</span></span>

<span data-ttu-id="be575-131">앞의 JSON 패치 문서를 적용한 후 리소스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-131">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="be575-132">JSON 패치 문서를 리소스에 적용하여 변경된 내용은 원자성입니다. 목록에 있는 작업이 하나라도 실패하면 목록의 작업이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-132">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="be575-133">경로 구문</span><span class="sxs-lookup"><span data-stu-id="be575-133">Path syntax</span></span>

<span data-ttu-id="be575-134">작업 개체의 [path](https://tools.ietf.org/html/rfc6901) 속성에서 수준 사이에는 슬래시가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-134">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="be575-135">예: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="be575-135">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="be575-136">0부터 시작하는 인덱스는 배열 요소를 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be575-136">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="be575-137">`addresses` 배열의 첫 번째 요소는 `/addresses/0`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-137">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="be575-138">배열 끝에 `add`(추가)하려면 인덱스 번호가 아닌 하이픈(-)을 사용합니다(`/addresses/-`).</span><span class="sxs-lookup"><span data-stu-id="be575-138">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="be575-139">작업</span><span class="sxs-lookup"><span data-stu-id="be575-139">Operations</span></span>

<span data-ttu-id="be575-140">다음 표에서는 [JSON 패치 사양](https://tools.ietf.org/html/rfc6902)에 정의된 지원되는 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be575-140">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="be575-141">연산</span><span class="sxs-lookup"><span data-stu-id="be575-141">Operation</span></span>  | <span data-ttu-id="be575-142">참고</span><span class="sxs-lookup"><span data-stu-id="be575-142">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="be575-143">속성 또는 배열 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-143">Add a property or array element.</span></span> <span data-ttu-id="be575-144">기존 속성의 경우 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-144">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="be575-145">속성 또는 배열 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-145">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="be575-146">동일한 위치에서 `add`가 뒤에 오는 `remove`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-146">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="be575-147">소스의 값을 사용하는 대상에 대한 `add`가 뒤에 오는 소스에서 `remove`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-147">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="be575-148">소스의 값을 사용하는 대상에 대한 `add`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-148">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="be575-149">`path`의 값이 제공된 `value`와 같은 경우 성공 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-149">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="be575-150">ASP.NET Core의 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="be575-150">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="be575-151">JSON 패치의 ASP.NET Core 구현은 [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 패키지로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="be575-151">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="be575-152">작업 메서드 코드</span><span class="sxs-lookup"><span data-stu-id="be575-152">Action method code</span></span>

<span data-ttu-id="be575-153">API 컨트롤러에서 JSON 패치의 작업 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-153">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="be575-154">`HttpPatch` 특성을 사용하여 주석으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="be575-154">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="be575-155">일반적으로 `[FromBody]`를 사용하여 `JsonPatchDocument<T>`를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-155">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="be575-156">패치 문서에서 `ApplyTo`를 호출하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-156">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="be575-157">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-157">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="be575-158">샘플 앱의 이 코드는 다음 `Customer` 모델에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-158">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="be575-159">샘플 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="be575-159">The sample action method:</span></span>

* <span data-ttu-id="be575-160">`Customer`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-160">Constructs a `Customer`.</span></span>
* <span data-ttu-id="be575-161">패치를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-161">Applies the patch.</span></span>
* <span data-ttu-id="be575-162">응답의 본문으로 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-162">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="be575-163">실제 앱에서 이 코드는 데이터베이스와 같은 저장소에서 데이터를 검색하고 패치를 적용한 후 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-163">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="be575-164">모델 상태</span><span class="sxs-lookup"><span data-stu-id="be575-164">Model state</span></span>

<span data-ttu-id="be575-165">앞의 작업 메서드 예제에서는 모델 상태를 매개 변수 중 하나로 가져오는 `ApplyTo`의 오버로드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-165">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="be575-166">이 옵션을 사용하여 응답으로 오류 메시지를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-166">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="be575-167">다음 예제에서는 `test` 작업에 대한 400 잘못된 요청 응답의 본문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be575-167">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="be575-168">동적 개체</span><span class="sxs-lookup"><span data-stu-id="be575-168">Dynamic objects</span></span>

<span data-ttu-id="be575-169">다음 작업 메서드 예제에서는 동적 개체에 패치를 적용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be575-169">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="be575-170">추가 작업</span><span class="sxs-lookup"><span data-stu-id="be575-170">The add operation</span></span>

* <span data-ttu-id="be575-171">`path`가 배열 요소를 가리키는 경우: `path`에 지정된 요소 앞에 새 요소를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-171">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="be575-172">`path`가 속성을 가리키는 경우: 속성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-172">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="be575-173">`path`가 존재하지 않는 위치를 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="be575-173">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="be575-174">패치할 리소스가 동적 개체인 경우: 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-174">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="be575-175">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-175">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="be575-176">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Order` 개체를 `Orders` 배열 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-176">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="be575-177">제거 작업</span><span class="sxs-lookup"><span data-stu-id="be575-177">The remove operation</span></span>

* <span data-ttu-id="be575-178">`path`가 배열 요소를 가리키는 경우: 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-178">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="be575-179">`path`가 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="be575-179">If `path` points to a property:</span></span>
  * <span data-ttu-id="be575-180">패치할 리소스가 동적 개체인 경우: 속성을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-180">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="be575-181">패치할 리소스가 정적 개체인 경우:</span><span class="sxs-lookup"><span data-stu-id="be575-181">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="be575-182">속성이 nullable인 경우: null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-182">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="be575-183">속성이 nullable이 아닌 경우: `default<T>`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-183">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="be575-184">다음 샘플 패치 문서는 `CustomerName`을 null로 설정하고 `Orders[0]`를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-184">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="be575-185">바꾸기 작업</span><span class="sxs-lookup"><span data-stu-id="be575-185">The replace operation</span></span>

<span data-ttu-id="be575-186">이 작업은 `add`가 뒤에 오는 `remove`와 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-186">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="be575-187">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Orders[0]`를 새 `Order` 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="be575-187">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="be575-188">이동 작업</span><span class="sxs-lookup"><span data-stu-id="be575-188">The move operation</span></span>

* <span data-ttu-id="be575-189">`path`가 배열 요소를 가리키는 경우: `from` 요소를 `path` 요소 위치에 복사한 후 `from` 요소에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-189">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="be575-190">`path`가 속성을 가리키는 경우: `from` 속성 값을 `path` 속성에 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-190">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="be575-191">`path`가 존재하지 않는 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="be575-191">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="be575-192">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-192">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="be575-193">패치할 리소스가 동적 개체인 경우: `from` 속성을 `path`에 지정된 위치로 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-193">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="be575-194">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-194">The following sample patch document:</span></span>

* <span data-ttu-id="be575-195">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-195">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="be575-196">`Orders[0].OrderName`을 null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-196">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="be575-197">`Orders[1]`를 `Orders[0]` 앞으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-197">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="be575-198">복사 작업</span><span class="sxs-lookup"><span data-stu-id="be575-198">The copy operation</span></span>

<span data-ttu-id="be575-199">이 작업은 마지막 `remove` 단계 없이 `move` 작업과 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-199">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="be575-200">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-200">The following sample patch document:</span></span>

* <span data-ttu-id="be575-201">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-201">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="be575-202">`Orders[1]` 복사본을 `Orders[0]` 앞에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-202">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="be575-203">테스트 작업</span><span class="sxs-lookup"><span data-stu-id="be575-203">The test operation</span></span>

<span data-ttu-id="be575-204">`path`에 지정된 위치의 값이 `value`에 제공된 값과 다른 경우 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-204">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="be575-205">이 경우 패치 문서의 다른 모든 작업이 성공하더라도 전체 PATCH 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-205">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="be575-206">`test` 작업은 일반적으로 동시성 충돌이 발생하는 경우 업데이트를 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be575-206">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="be575-207">`CustomerName`의 초기 값이 “John”인 경우 테스트에 실패하므로 다음 샘플 패치 문서는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-207">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="be575-208">코드 가져오기</span><span class="sxs-lookup"><span data-stu-id="be575-208">Get the code</span></span>

<span data-ttu-id="be575-209">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)</span><span class="sxs-lookup"><span data-stu-id="be575-209">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="be575-210">([다운로드하는 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="be575-210">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="be575-211">샘플을 테스트하려면 앱을 실행하고 다음 설정을 사용하여 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="be575-211">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="be575-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="be575-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="be575-213">HTTP 메서드: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="be575-213">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="be575-214">헤더: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="be575-214">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="be575-215">본문: *JSON* 프로젝트 폴더에서 JSON 패치 문서 샘플 중 하나를 복사하여 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-215">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be575-216">추가 자료</span><span class="sxs-lookup"><span data-stu-id="be575-216">Additional resources</span></span>

* [<span data-ttu-id="be575-217">IETF RFC 5789 PATCH 메서드 사양</span><span class="sxs-lookup"><span data-stu-id="be575-217">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="be575-218">IETF RFC 6902 JSON 패치 사양</span><span class="sxs-lookup"><span data-stu-id="be575-218">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="be575-219">IETF RFC 6901 JSON 패치 경로 형식 사양</span><span class="sxs-lookup"><span data-stu-id="be575-219">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="be575-220">[JSON 패치 문서](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="be575-220">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="be575-221">JSON 패치 문서를 만들기 위한 리소스의 링크를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-221">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="be575-222">ASP.NET Core JSON 패치 소스 코드</span><span class="sxs-lookup"><span data-stu-id="be575-222">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="be575-223">이 문서에서는 ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-223">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="be575-224">PATCH HTTP 요청 메서드</span><span class="sxs-lookup"><span data-stu-id="be575-224">PATCH HTTP request method</span></span>

<span data-ttu-id="be575-225">PUT 및 [PATCH](https://tools.ietf.org/html/rfc5789) 메서드는 기존 리소스를 업데이트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be575-225">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="be575-226">두 메서드의 차이점은 PUT은 전체 리소스를 바꾸지만, PATCH는 변경 내용만 지정한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="be575-226">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="be575-227">JSON 패치</span><span class="sxs-lookup"><span data-stu-id="be575-227">JSON Patch</span></span>

<span data-ttu-id="be575-228">[JSON 패치](https://tools.ietf.org/html/rfc6902)는 리소스에 적용할 업데이트를 지정하기 위한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="be575-228">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="be575-229">JSON 패치 문서에는 작업 배열이 포함됩니다. </span><span class="sxs-lookup"><span data-stu-id="be575-229">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="be575-230">각 작업은 배열 요소 추가 또는 속성 값 바꾸기와 같은 특정 유형의 변경을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-230">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="be575-231">예를 들어 다음 JSON 문서는 리소스, 리소스의 JSON 패치 문서 및 패치 작업을 적용한 결과를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="be575-231">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="be575-232">리소스 예제</span><span class="sxs-lookup"><span data-stu-id="be575-232">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="be575-233">JSON 패치 예제</span><span class="sxs-lookup"><span data-stu-id="be575-233">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="be575-234">앞의 JSON에서:</span><span class="sxs-lookup"><span data-stu-id="be575-234">In the preceding JSON:</span></span>

* <span data-ttu-id="be575-235">`op` 속성은 작업 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="be575-235">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="be575-236">`path` 속성은 업데이트할 요소를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="be575-236">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="be575-237">`value` 속성은 새 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-237">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="be575-238">패치 후 리소스</span><span class="sxs-lookup"><span data-stu-id="be575-238">Resource after patch</span></span>

<span data-ttu-id="be575-239">앞의 JSON 패치 문서를 적용한 후 리소스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-239">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="be575-240">JSON 패치 문서를 리소스에 적용하여 변경된 내용은 원자성입니다. 목록에 있는 작업이 하나라도 실패하면 목록의 작업이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-240">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="be575-241">경로 구문</span><span class="sxs-lookup"><span data-stu-id="be575-241">Path syntax</span></span>

<span data-ttu-id="be575-242">작업 개체의 [path](https://tools.ietf.org/html/rfc6901) 속성에서 수준 사이에는 슬래시가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-242">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="be575-243">예: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="be575-243">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="be575-244">0부터 시작하는 인덱스는 배열 요소를 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be575-244">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="be575-245">`addresses` 배열의 첫 번째 요소는 `/addresses/0`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-245">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="be575-246">배열 끝에 `add`(추가)하려면 인덱스 번호가 아닌 하이픈(-)을 사용합니다(`/addresses/-`).</span><span class="sxs-lookup"><span data-stu-id="be575-246">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="be575-247">작업</span><span class="sxs-lookup"><span data-stu-id="be575-247">Operations</span></span>

<span data-ttu-id="be575-248">다음 표에서는 [JSON 패치 사양](https://tools.ietf.org/html/rfc6902)에 정의된 지원되는 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be575-248">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="be575-249">연산</span><span class="sxs-lookup"><span data-stu-id="be575-249">Operation</span></span>  | <span data-ttu-id="be575-250">참고</span><span class="sxs-lookup"><span data-stu-id="be575-250">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="be575-251">속성 또는 배열 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-251">Add a property or array element.</span></span> <span data-ttu-id="be575-252">기존 속성의 경우 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-252">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="be575-253">속성 또는 배열 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-253">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="be575-254">동일한 위치에서 `add`가 뒤에 오는 `remove`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-254">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="be575-255">소스의 값을 사용하는 대상에 대한 `add`가 뒤에 오는 소스에서 `remove`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-255">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="be575-256">소스의 값을 사용하는 대상에 대한 `add`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-256">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="be575-257">`path`의 값이 제공된 `value`와 같은 경우 성공 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-257">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="be575-258">ASP.NET Core의 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="be575-258">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="be575-259">JSON 패치의 ASP.NET Core 구현은 [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 패키지로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="be575-259">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="be575-260">패키지는 [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) 메타패키지에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="be575-260">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="be575-261">작업 메서드 코드</span><span class="sxs-lookup"><span data-stu-id="be575-261">Action method code</span></span>

<span data-ttu-id="be575-262">API 컨트롤러에서 JSON 패치의 작업 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-262">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="be575-263">`HttpPatch` 특성을 사용하여 주석으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="be575-263">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="be575-264">일반적으로 `[FromBody]`를 사용하여 `JsonPatchDocument<T>`를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-264">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="be575-265">패치 문서에서 `ApplyTo`를 호출하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-265">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="be575-266">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-266">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="be575-267">샘플 앱의 이 코드는 다음 `Customer` 모델에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-267">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="be575-268">샘플 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="be575-268">The sample action method:</span></span>

* <span data-ttu-id="be575-269">`Customer`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-269">Constructs a `Customer`.</span></span>
* <span data-ttu-id="be575-270">패치를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-270">Applies the patch.</span></span>
* <span data-ttu-id="be575-271">응답의 본문으로 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-271">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="be575-272">실제 앱에서 이 코드는 데이터베이스와 같은 저장소에서 데이터를 검색하고 패치를 적용한 후 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-272">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="be575-273">모델 상태</span><span class="sxs-lookup"><span data-stu-id="be575-273">Model state</span></span>

<span data-ttu-id="be575-274">앞의 작업 메서드 예제에서는 모델 상태를 매개 변수 중 하나로 가져오는 `ApplyTo`의 오버로드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-274">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="be575-275">이 옵션을 사용하여 응답으로 오류 메시지를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-275">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="be575-276">다음 예제에서는 `test` 작업에 대한 400 잘못된 요청 응답의 본문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be575-276">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="be575-277">동적 개체</span><span class="sxs-lookup"><span data-stu-id="be575-277">Dynamic objects</span></span>

<span data-ttu-id="be575-278">다음 작업 메서드 예제에서는 동적 개체에 패치를 적용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be575-278">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="be575-279">추가 작업</span><span class="sxs-lookup"><span data-stu-id="be575-279">The add operation</span></span>

* <span data-ttu-id="be575-280">`path`가 배열 요소를 가리키는 경우: `path`에 지정된 요소 앞에 새 요소를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-280">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="be575-281">`path`가 속성을 가리키는 경우: 속성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-281">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="be575-282">`path`가 존재하지 않는 위치를 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="be575-282">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="be575-283">패치할 리소스가 동적 개체인 경우: 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-283">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="be575-284">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-284">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="be575-285">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Order` 개체를 `Orders` 배열 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-285">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="be575-286">제거 작업</span><span class="sxs-lookup"><span data-stu-id="be575-286">The remove operation</span></span>

* <span data-ttu-id="be575-287">`path`가 배열 요소를 가리키는 경우: 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-287">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="be575-288">`path`가 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="be575-288">If `path` points to a property:</span></span>
  * <span data-ttu-id="be575-289">패치할 리소스가 동적 개체인 경우: 속성을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-289">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="be575-290">패치할 리소스가 정적 개체인 경우:</span><span class="sxs-lookup"><span data-stu-id="be575-290">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="be575-291">속성이 nullable인 경우: null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-291">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="be575-292">속성이 nullable이 아닌 경우: `default<T>`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-292">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="be575-293">다음 샘플 패치 문서는 `CustomerName`을 null로 설정하고 `Orders[0]`를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-293">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="be575-294">바꾸기 작업</span><span class="sxs-lookup"><span data-stu-id="be575-294">The replace operation</span></span>

<span data-ttu-id="be575-295">이 작업은 `add`가 뒤에 오는 `remove`와 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-295">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="be575-296">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Orders[0]`를 새 `Order` 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="be575-296">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="be575-297">이동 작업</span><span class="sxs-lookup"><span data-stu-id="be575-297">The move operation</span></span>

* <span data-ttu-id="be575-298">`path`가 배열 요소를 가리키는 경우: `from` 요소를 `path` 요소 위치에 복사한 후 `from` 요소에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-298">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="be575-299">`path`가 속성을 가리키는 경우: `from` 속성 값을 `path` 속성에 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-299">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="be575-300">`path`가 존재하지 않는 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="be575-300">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="be575-301">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-301">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="be575-302">패치할 리소스가 동적 개체인 경우: `from` 속성을 `path`에 지정된 위치로 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-302">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="be575-303">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-303">The following sample patch document:</span></span>

* <span data-ttu-id="be575-304">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-304">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="be575-305">`Orders[0].OrderName`을 null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-305">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="be575-306">`Orders[1]`를 `Orders[0]` 앞으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-306">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="be575-307">복사 작업</span><span class="sxs-lookup"><span data-stu-id="be575-307">The copy operation</span></span>

<span data-ttu-id="be575-308">이 작업은 마지막 `remove` 단계 없이 `move` 작업과 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-308">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="be575-309">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-309">The following sample patch document:</span></span>

* <span data-ttu-id="be575-310">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-310">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="be575-311">`Orders[1]` 복사본을 `Orders[0]` 앞에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-311">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="be575-312">테스트 작업</span><span class="sxs-lookup"><span data-stu-id="be575-312">The test operation</span></span>

<span data-ttu-id="be575-313">`path`에 지정된 위치의 값이 `value`에 제공된 값과 다른 경우 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-313">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="be575-314">이 경우 패치 문서의 다른 모든 작업이 성공하더라도 전체 PATCH 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-314">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="be575-315">`test` 작업은 일반적으로 동시성 충돌이 발생하는 경우 업데이트를 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be575-315">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="be575-316">`CustomerName`의 초기 값이 “John”인 경우 테스트에 실패하므로 다음 샘플 패치 문서는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-316">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="be575-317">코드 가져오기</span><span class="sxs-lookup"><span data-stu-id="be575-317">Get the code</span></span>

<span data-ttu-id="be575-318">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)</span><span class="sxs-lookup"><span data-stu-id="be575-318">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="be575-319">([다운로드하는 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="be575-319">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="be575-320">샘플을 테스트하려면 앱을 실행하고 다음 설정을 사용하여 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="be575-320">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="be575-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="be575-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="be575-322">HTTP 메서드: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="be575-322">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="be575-323">헤더: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="be575-323">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="be575-324">본문: *JSON* 프로젝트 폴더에서 JSON 패치 문서 샘플 중 하나를 복사하여 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="be575-324">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be575-325">추가 자료</span><span class="sxs-lookup"><span data-stu-id="be575-325">Additional resources</span></span>

* [<span data-ttu-id="be575-326">IETF RFC 5789 PATCH 메서드 사양</span><span class="sxs-lookup"><span data-stu-id="be575-326">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="be575-327">IETF RFC 6902 JSON 패치 사양</span><span class="sxs-lookup"><span data-stu-id="be575-327">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="be575-328">IETF RFC 6901 JSON 패치 경로 형식 사양</span><span class="sxs-lookup"><span data-stu-id="be575-328">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="be575-329">[JSON 패치 문서](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="be575-329">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="be575-330">JSON 패치 문서를 만들기 위한 리소스의 링크를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="be575-330">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="be575-331">ASP.NET Core JSON 패치 소스 코드</span><span class="sxs-lookup"><span data-stu-id="be575-331">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
