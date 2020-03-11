---
title: ASP.NET Core Web API의 JsonPatch
author: rick-anderson
description: ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 11/01/2019
uid: web-api/jsonpatch
ms.openlocfilehash: cf1a00c1928652bf5210b2442087209e23b8868e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652953"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="8a658-103">ASP.NET Core Web API의 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="8a658-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="8a658-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="8a658-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8a658-105">이 문서에서는 ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="8a658-106">패키지 설치</span><span class="sxs-lookup"><span data-stu-id="8a658-106">Package installation</span></span>

<span data-ttu-id="8a658-107">JsonPatch 지원은 `Microsoft.AspNetCore.Mvc.NewtonsoftJson` 패키지를 통해 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-107">Support for JsonPatch is enabled using the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package.</span></span> <span data-ttu-id="8a658-108">이 기능을 사용하도록 설정하려면 앱에서 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-108">To enable this feature, apps must:</span></span>

* <span data-ttu-id="8a658-109">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-109">Install the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
* <span data-ttu-id="8a658-110">`Startup.ConfigureServices`에 대한 호출을 포함하도록 프로젝트의 `AddNewtonsoftJson` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-110">Update the project's `Startup.ConfigureServices` method to include a call to `AddNewtonsoftJson`:</span></span>

  ```csharp
  services
      .AddControllersWithViews()
      .AddNewtonsoftJson();
  ```

<span data-ttu-id="8a658-111">`AddNewtonsoftJson`은 MVC 서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="8a658-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

  * `AddRazorPages`
  * `AddControllersWithViews`
  * `AddControllers`

## <a name="jsonpatch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="8a658-112">JsonPatch, AddNewtonsoftJson 및 System.Text.Json과 호환됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-112">JsonPatch, AddNewtonsoftJson, and System.Text.Json</span></span>
  
<span data-ttu-id="8a658-113">`AddNewtonsoftJson`은 `System.Text.Json`모든**JSON 콘텐츠의 형식을 지정하는 데 사용되는** 기반 입력 및 출력 포맷터를 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-113">`AddNewtonsoftJson` replaces the `System.Text.Json` based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="8a658-114">`JsonPatch`를 사용하여 `Newtonsoft.Json` 지원을 추가하지만 다른 포맷터를 변경하지 않으려면 프로젝트의 `Startup.ConfigureServices`를 다음과 같이 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-114">To add support for `JsonPatch` using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="8a658-115">앞의 코드에는 [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)에 대한 참조 및 다음 using 문이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-115">The preceding code requires a reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) and the following using statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="8a658-116">PATCH HTTP 요청 메서드</span><span class="sxs-lookup"><span data-stu-id="8a658-116">PATCH HTTP request method</span></span>

<span data-ttu-id="8a658-117">PUT 및 [PATCH](https://tools.ietf.org/html/rfc5789) 메서드는 기존 리소스를 업데이트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="8a658-118">두 메서드의 차이점은 PUT은 전체 리소스를 바꾸지만, PATCH는 변경 내용만 지정한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="8a658-119">JSON 패치</span><span class="sxs-lookup"><span data-stu-id="8a658-119">JSON Patch</span></span>

<span data-ttu-id="8a658-120">[JSON 패치](https://tools.ietf.org/html/rfc6902)는 리소스에 적용할 업데이트를 지정하기 위한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="8a658-121">JSON 패치 문서에는 *작업* 배열이 포함됩니다.{3}{4}</span><span class="sxs-lookup"><span data-stu-id="8a658-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="8a658-122">각 작업은 배열 요소 추가 또는 속성 값 바꾸기와 같은 특정 유형의 변경을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-122">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="8a658-123">예를 들어 다음 JSON 문서는 리소스, 리소스의 JSON 패치 문서 및 패치 작업을 적용한 결과를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-123">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="8a658-124">리소스 예제</span><span class="sxs-lookup"><span data-stu-id="8a658-124">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="8a658-125">JSON 패치 예제</span><span class="sxs-lookup"><span data-stu-id="8a658-125">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="8a658-126">앞의 JSON에서:</span><span class="sxs-lookup"><span data-stu-id="8a658-126">In the preceding JSON:</span></span>

* <span data-ttu-id="8a658-127">`op` 속성은 작업 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-127">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="8a658-128">`path` 속성은 업데이트할 요소를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-128">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="8a658-129">`value` 속성은 새 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-129">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="8a658-130">패치 후 리소스</span><span class="sxs-lookup"><span data-stu-id="8a658-130">Resource after patch</span></span>

<span data-ttu-id="8a658-131">앞의 JSON 패치 문서를 적용한 후 리소스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-131">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="8a658-132">JSON 패치 문서를 리소스에 적용하여 변경된 내용은 원자성입니다. 목록에 있는 작업이 하나라도 실패하면 목록의 작업이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-132">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="8a658-133">경로 구문</span><span class="sxs-lookup"><span data-stu-id="8a658-133">Path syntax</span></span>

<span data-ttu-id="8a658-134">작업 개체의 [path](https://tools.ietf.org/html/rfc6901) 속성에서 수준 사이에는 슬래시가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-134">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="8a658-135">`"/address/zipCode"`)을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-135">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="8a658-136">0부터 시작하는 인덱스는 배열 요소를 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-136">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="8a658-137">`addresses` 배열의 첫 번째 요소는 `/addresses/0`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-137">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="8a658-138">배열 끝에 `add`(추가)하려면 인덱스 번호가 아닌 하이픈(-)을 사용합니다(`/addresses/-`).</span><span class="sxs-lookup"><span data-stu-id="8a658-138">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="8a658-139">작업</span><span class="sxs-lookup"><span data-stu-id="8a658-139">Operations</span></span>

<span data-ttu-id="8a658-140">다음 표에서는 [JSON 패치 사양](https://tools.ietf.org/html/rfc6902)에 정의된 지원되는 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-140">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="8a658-141">작업(Operation)</span><span class="sxs-lookup"><span data-stu-id="8a658-141">Operation</span></span>  | <span data-ttu-id="8a658-142">메모</span><span class="sxs-lookup"><span data-stu-id="8a658-142">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="8a658-143">속성 또는 배열 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-143">Add a property or array element.</span></span> <span data-ttu-id="8a658-144">기존 속성의 경우 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-144">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="8a658-145">속성 또는 배열 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-145">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="8a658-146">동일한 위치에서 `remove`가 뒤에 오는 `add`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-146">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="8a658-147">소스의 값을 사용하는 대상에 대한 `remove`가 뒤에 오는 소스에서 `add`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-147">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="8a658-148">소스의 값을 사용하는 대상에 대한 `add`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-148">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="8a658-149">`path`의 값이 제공된 `value`와 같은 경우 성공 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-149">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="8a658-150">ASP.NET Core의 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="8a658-150">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="8a658-151">JSON 패치의 ASP.NET Core 구현은 [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 패키지로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-151">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="8a658-152">작업 메서드 코드</span><span class="sxs-lookup"><span data-stu-id="8a658-152">Action method code</span></span>

<span data-ttu-id="8a658-153">API 컨트롤러에서 JSON 패치의 작업 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-153">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="8a658-154">`HttpPatch` 특성을 사용하여 주석으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-154">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="8a658-155">일반적으로 `JsonPatchDocument<T>`를 사용하여 `[FromBody]`를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-155">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="8a658-156">패치 문서에서 `ApplyTo`를 호출하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-156">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="8a658-157">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-157">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="8a658-158">샘플 앱의 이 코드는 다음 `Customer` 모델에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-158">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="8a658-159">샘플 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="8a658-159">The sample action method:</span></span>

* <span data-ttu-id="8a658-160">`Customer`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-160">Constructs a `Customer`.</span></span>
* <span data-ttu-id="8a658-161">패치를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-161">Applies the patch.</span></span>
* <span data-ttu-id="8a658-162">응답의 본문으로 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-162">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="8a658-163">실제 앱에서 이 코드는 데이터베이스와 같은 저장소에서 데이터를 검색하고 패치를 적용한 후 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-163">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="8a658-164">모델 상태</span><span class="sxs-lookup"><span data-stu-id="8a658-164">Model state</span></span>

<span data-ttu-id="8a658-165">앞의 작업 메서드 예제에서는 모델 상태를 매개 변수 중 하나로 가져오는 `ApplyTo`의 오버로드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-165">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="8a658-166">이 옵션을 사용하여 응답으로 오류 메시지를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-166">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="8a658-167">다음 예제에서는 `test` 작업에 대한 400 잘못된 요청 응답의 본문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-167">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="8a658-168">동적 개체</span><span class="sxs-lookup"><span data-stu-id="8a658-168">Dynamic objects</span></span>

<span data-ttu-id="8a658-169">다음 작업 메서드 예제에서는 동적 개체에 패치를 적용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-169">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="8a658-170">추가 작업</span><span class="sxs-lookup"><span data-stu-id="8a658-170">The add operation</span></span>

* <span data-ttu-id="8a658-171">`path`가 배열 요소를 가리키는 경우: `path`에 지정된 요소 앞에 새 요소를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-171">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="8a658-172">`path`가 속성을 가리키는 경우: 속성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-172">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="8a658-173">`path`가 존재하지 않는 위치를 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="8a658-173">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="8a658-174">패치할 리소스가 동적 개체인 경우: 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-174">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="8a658-175">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-175">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="8a658-176">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Order` 개체를 `Orders` 배열 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-176">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="8a658-177">제거 작업</span><span class="sxs-lookup"><span data-stu-id="8a658-177">The remove operation</span></span>

* <span data-ttu-id="8a658-178">`path`가 배열 요소를 가리키는 경우: 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-178">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="8a658-179">`path`가 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="8a658-179">If `path` points to a property:</span></span>
  * <span data-ttu-id="8a658-180">패치할 리소스가 동적 개체인 경우: 속성을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-180">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="8a658-181">패치할 리소스가 정적 개체인 경우:</span><span class="sxs-lookup"><span data-stu-id="8a658-181">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="8a658-182">속성이 nullable인 경우: null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-182">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="8a658-183">속성이 nullable이 아닌 경우: `default<T>`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-183">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="8a658-184">다음 샘플 패치 문서는 `CustomerName`을 null로 설정하고 `Orders[0]`를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-184">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="8a658-185">바꾸기 작업</span><span class="sxs-lookup"><span data-stu-id="8a658-185">The replace operation</span></span>

<span data-ttu-id="8a658-186">이 작업은 `remove`가 뒤에 오는 `add`와 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-186">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="8a658-187">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Orders[0]`를 새 `Order` 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-187">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="8a658-188">이동 작업</span><span class="sxs-lookup"><span data-stu-id="8a658-188">The move operation</span></span>

* <span data-ttu-id="8a658-189">`path`가 배열 요소를 가리키는 경우: `from` 요소를 `path` 요소 위치에 복사한 후 `remove` 요소에서 `from` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-189">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="8a658-190">`path`가 속성을 가리키는 경우: `from` 속성 값을 `path` 속성에 복사한 후 `remove` 속성에서 `from` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-190">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="8a658-191">`path`가 존재하지 않는 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="8a658-191">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="8a658-192">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-192">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="8a658-193">패치할 리소스가 동적 개체인 경우: `from` 속성을 `path`에 지정된 위치로 복사한 후 `remove` 속성에서 `from` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-193">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="8a658-194">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-194">The following sample patch document:</span></span>

* <span data-ttu-id="8a658-195">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-195">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="8a658-196">`Orders[0].OrderName`을 null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-196">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="8a658-197">`Orders[1]`를 `Orders[0]` 앞으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-197">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="8a658-198">복사 작업</span><span class="sxs-lookup"><span data-stu-id="8a658-198">The copy operation</span></span>

<span data-ttu-id="8a658-199">이 작업은 마지막 `move` 단계 없이 `remove` 작업과 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-199">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="8a658-200">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-200">The following sample patch document:</span></span>

* <span data-ttu-id="8a658-201">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-201">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="8a658-202">`Orders[1]` 복사본을 `Orders[0]` 앞에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-202">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="8a658-203">테스트 작업</span><span class="sxs-lookup"><span data-stu-id="8a658-203">The test operation</span></span>

<span data-ttu-id="8a658-204">`path`에 지정된 위치의 값이 `value`에 제공된 값과 다른 경우 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-204">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="8a658-205">이 경우 패치 문서의 다른 모든 작업이 성공하더라도 전체 PATCH 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-205">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="8a658-206">`test` 작업은 일반적으로 동시성 충돌이 발생하는 경우 업데이트를 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-206">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="8a658-207">`CustomerName`의 초기 값이 “John”인 경우 테스트에 실패하므로 다음 샘플 패치 문서는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-207">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="8a658-208">코드 가져오기</span><span class="sxs-lookup"><span data-stu-id="8a658-208">Get the code</span></span>

<span data-ttu-id="8a658-209">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)</span><span class="sxs-lookup"><span data-stu-id="8a658-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="8a658-210">([다운로드하는 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a658-210">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="8a658-211">샘플을 테스트하려면 앱을 실행하고 다음 설정을 사용하여 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-211">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="8a658-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="8a658-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="8a658-213">HTTP 메서드: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="8a658-213">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="8a658-214">헤더: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="8a658-214">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="8a658-215">Body: *json 프로젝트 폴더에서 json 패치* 문서 샘플 중 하나를 복사 하 여 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-215">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a658-216">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="8a658-216">Additional resources</span></span>

* [<span data-ttu-id="8a658-217">IETF RFC 5789 PATCH 메서드 사양</span><span class="sxs-lookup"><span data-stu-id="8a658-217">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="8a658-218">IETF RFC 6902 JSON 패치 사양</span><span class="sxs-lookup"><span data-stu-id="8a658-218">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="8a658-219">IETF RFC 6901 JSON 패치 경로 형식 사양</span><span class="sxs-lookup"><span data-stu-id="8a658-219">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="8a658-220">[JSON 패치 문서](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="8a658-220">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="8a658-221">JSON 패치 문서를 만들기 위한 리소스의 링크를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-221">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="8a658-222">ASP.NET Core JSON 패치 소스 코드</span><span class="sxs-lookup"><span data-stu-id="8a658-222">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8a658-223">이 문서에서는 ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-223">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="8a658-224">PATCH HTTP 요청 메서드</span><span class="sxs-lookup"><span data-stu-id="8a658-224">PATCH HTTP request method</span></span>

<span data-ttu-id="8a658-225">PUT 및 [PATCH](https://tools.ietf.org/html/rfc5789) 메서드는 기존 리소스를 업데이트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-225">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="8a658-226">두 메서드의 차이점은 PUT은 전체 리소스를 바꾸지만, PATCH는 변경 내용만 지정한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-226">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="8a658-227">JSON 패치</span><span class="sxs-lookup"><span data-stu-id="8a658-227">JSON Patch</span></span>

<span data-ttu-id="8a658-228">[JSON 패치](https://tools.ietf.org/html/rfc6902)는 리소스에 적용할 업데이트를 지정하기 위한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-228">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="8a658-229">JSON 패치 문서에는 *작업* 배열이 포함됩니다.{3}{4}</span><span class="sxs-lookup"><span data-stu-id="8a658-229">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="8a658-230">각 작업은 배열 요소 추가 또는 속성 값 바꾸기와 같은 특정 유형의 변경을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-230">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="8a658-231">예를 들어 다음 JSON 문서는 리소스, 리소스의 JSON 패치 문서 및 패치 작업을 적용한 결과를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-231">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="8a658-232">리소스 예제</span><span class="sxs-lookup"><span data-stu-id="8a658-232">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="8a658-233">JSON 패치 예제</span><span class="sxs-lookup"><span data-stu-id="8a658-233">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="8a658-234">앞의 JSON에서:</span><span class="sxs-lookup"><span data-stu-id="8a658-234">In the preceding JSON:</span></span>

* <span data-ttu-id="8a658-235">`op` 속성은 작업 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-235">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="8a658-236">`path` 속성은 업데이트할 요소를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-236">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="8a658-237">`value` 속성은 새 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-237">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="8a658-238">패치 후 리소스</span><span class="sxs-lookup"><span data-stu-id="8a658-238">Resource after patch</span></span>

<span data-ttu-id="8a658-239">앞의 JSON 패치 문서를 적용한 후 리소스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-239">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="8a658-240">JSON 패치 문서를 리소스에 적용하여 변경된 내용은 원자성입니다. 목록에 있는 작업이 하나라도 실패하면 목록의 작업이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-240">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="8a658-241">경로 구문</span><span class="sxs-lookup"><span data-stu-id="8a658-241">Path syntax</span></span>

<span data-ttu-id="8a658-242">작업 개체의 [path](https://tools.ietf.org/html/rfc6901) 속성에서 수준 사이에는 슬래시가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-242">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="8a658-243">`"/address/zipCode"`)을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-243">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="8a658-244">0부터 시작하는 인덱스는 배열 요소를 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-244">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="8a658-245">`addresses` 배열의 첫 번째 요소는 `/addresses/0`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-245">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="8a658-246">배열 끝에 `add`(추가)하려면 인덱스 번호가 아닌 하이픈(-)을 사용합니다(`/addresses/-`).</span><span class="sxs-lookup"><span data-stu-id="8a658-246">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="8a658-247">작업</span><span class="sxs-lookup"><span data-stu-id="8a658-247">Operations</span></span>

<span data-ttu-id="8a658-248">다음 표에서는 [JSON 패치 사양](https://tools.ietf.org/html/rfc6902)에 정의된 지원되는 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-248">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="8a658-249">작업(Operation)</span><span class="sxs-lookup"><span data-stu-id="8a658-249">Operation</span></span>  | <span data-ttu-id="8a658-250">메모</span><span class="sxs-lookup"><span data-stu-id="8a658-250">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="8a658-251">속성 또는 배열 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-251">Add a property or array element.</span></span> <span data-ttu-id="8a658-252">기존 속성의 경우 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-252">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="8a658-253">속성 또는 배열 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-253">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="8a658-254">동일한 위치에서 `remove`가 뒤에 오는 `add`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-254">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="8a658-255">소스의 값을 사용하는 대상에 대한 `remove`가 뒤에 오는 소스에서 `add`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-255">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="8a658-256">소스의 값을 사용하는 대상에 대한 `add`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-256">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="8a658-257">`path`의 값이 제공된 `value`와 같은 경우 성공 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-257">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="8a658-258">ASP.NET Core의 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="8a658-258">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="8a658-259">JSON 패치의 ASP.NET Core 구현은 [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 패키지로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-259">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="8a658-260">패키지는 [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) 메타패키지에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-260">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="8a658-261">작업 메서드 코드</span><span class="sxs-lookup"><span data-stu-id="8a658-261">Action method code</span></span>

<span data-ttu-id="8a658-262">API 컨트롤러에서 JSON 패치의 작업 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-262">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="8a658-263">`HttpPatch` 특성을 사용하여 주석으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-263">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="8a658-264">일반적으로 `JsonPatchDocument<T>`를 사용하여 `[FromBody]`를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-264">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="8a658-265">패치 문서에서 `ApplyTo`를 호출하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-265">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="8a658-266">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-266">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="8a658-267">샘플 앱의 이 코드는 다음 `Customer` 모델에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-267">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="8a658-268">샘플 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="8a658-268">The sample action method:</span></span>

* <span data-ttu-id="8a658-269">`Customer`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-269">Constructs a `Customer`.</span></span>
* <span data-ttu-id="8a658-270">패치를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-270">Applies the patch.</span></span>
* <span data-ttu-id="8a658-271">응답의 본문으로 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-271">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="8a658-272">실제 앱에서 이 코드는 데이터베이스와 같은 저장소에서 데이터를 검색하고 패치를 적용한 후 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-272">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="8a658-273">모델 상태</span><span class="sxs-lookup"><span data-stu-id="8a658-273">Model state</span></span>

<span data-ttu-id="8a658-274">앞의 작업 메서드 예제에서는 모델 상태를 매개 변수 중 하나로 가져오는 `ApplyTo`의 오버로드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-274">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="8a658-275">이 옵션을 사용하여 응답으로 오류 메시지를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-275">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="8a658-276">다음 예제에서는 `test` 작업에 대한 400 잘못된 요청 응답의 본문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-276">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="8a658-277">동적 개체</span><span class="sxs-lookup"><span data-stu-id="8a658-277">Dynamic objects</span></span>

<span data-ttu-id="8a658-278">다음 작업 메서드 예제에서는 동적 개체에 패치를 적용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-278">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="8a658-279">추가 작업</span><span class="sxs-lookup"><span data-stu-id="8a658-279">The add operation</span></span>

* <span data-ttu-id="8a658-280">`path`가 배열 요소를 가리키는 경우: `path`에 지정된 요소 앞에 새 요소를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-280">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="8a658-281">`path`가 속성을 가리키는 경우: 속성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-281">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="8a658-282">`path`가 존재하지 않는 위치를 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="8a658-282">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="8a658-283">패치할 리소스가 동적 개체인 경우: 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-283">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="8a658-284">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-284">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="8a658-285">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Order` 개체를 `Orders` 배열 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-285">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="8a658-286">제거 작업</span><span class="sxs-lookup"><span data-stu-id="8a658-286">The remove operation</span></span>

* <span data-ttu-id="8a658-287">`path`가 배열 요소를 가리키는 경우: 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-287">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="8a658-288">`path`가 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="8a658-288">If `path` points to a property:</span></span>
  * <span data-ttu-id="8a658-289">패치할 리소스가 동적 개체인 경우: 속성을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-289">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="8a658-290">패치할 리소스가 정적 개체인 경우:</span><span class="sxs-lookup"><span data-stu-id="8a658-290">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="8a658-291">속성이 nullable인 경우: null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-291">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="8a658-292">속성이 nullable이 아닌 경우: `default<T>`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-292">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="8a658-293">다음 샘플 패치 문서는 `CustomerName`을 null로 설정하고 `Orders[0]`를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-293">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="8a658-294">바꾸기 작업</span><span class="sxs-lookup"><span data-stu-id="8a658-294">The replace operation</span></span>

<span data-ttu-id="8a658-295">이 작업은 `remove`가 뒤에 오는 `add`와 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-295">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="8a658-296">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Orders[0]`를 새 `Order` 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-296">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="8a658-297">이동 작업</span><span class="sxs-lookup"><span data-stu-id="8a658-297">The move operation</span></span>

* <span data-ttu-id="8a658-298">`path`가 배열 요소를 가리키는 경우: `from` 요소를 `path` 요소 위치에 복사한 후 `remove` 요소에서 `from` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-298">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="8a658-299">`path`가 속성을 가리키는 경우: `from` 속성 값을 `path` 속성에 복사한 후 `remove` 속성에서 `from` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-299">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="8a658-300">`path`가 존재하지 않는 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="8a658-300">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="8a658-301">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-301">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="8a658-302">패치할 리소스가 동적 개체인 경우: `from` 속성을 `path`에 지정된 위치로 복사한 후 `remove` 속성에서 `from` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-302">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="8a658-303">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-303">The following sample patch document:</span></span>

* <span data-ttu-id="8a658-304">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-304">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="8a658-305">`Orders[0].OrderName`을 null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-305">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="8a658-306">`Orders[1]`를 `Orders[0]` 앞으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-306">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="8a658-307">복사 작업</span><span class="sxs-lookup"><span data-stu-id="8a658-307">The copy operation</span></span>

<span data-ttu-id="8a658-308">이 작업은 마지막 `move` 단계 없이 `remove` 작업과 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-308">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="8a658-309">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-309">The following sample patch document:</span></span>

* <span data-ttu-id="8a658-310">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-310">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="8a658-311">`Orders[1]` 복사본을 `Orders[0]` 앞에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-311">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="8a658-312">테스트 작업</span><span class="sxs-lookup"><span data-stu-id="8a658-312">The test operation</span></span>

<span data-ttu-id="8a658-313">`path`에 지정된 위치의 값이 `value`에 제공된 값과 다른 경우 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-313">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="8a658-314">이 경우 패치 문서의 다른 모든 작업이 성공하더라도 전체 PATCH 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-314">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="8a658-315">`test` 작업은 일반적으로 동시성 충돌이 발생하는 경우 업데이트를 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-315">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="8a658-316">`CustomerName`의 초기 값이 “John”인 경우 테스트에 실패하므로 다음 샘플 패치 문서는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-316">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="8a658-317">코드 가져오기</span><span class="sxs-lookup"><span data-stu-id="8a658-317">Get the code</span></span>

<span data-ttu-id="8a658-318">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)</span><span class="sxs-lookup"><span data-stu-id="8a658-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="8a658-319">([다운로드하는 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a658-319">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="8a658-320">샘플을 테스트하려면 앱을 실행하고 다음 설정을 사용하여 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-320">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="8a658-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="8a658-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="8a658-322">HTTP 메서드: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="8a658-322">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="8a658-323">헤더: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="8a658-323">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="8a658-324">Body: *json 프로젝트 폴더에서 json 패치* 문서 샘플 중 하나를 복사 하 여 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-324">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a658-325">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="8a658-325">Additional resources</span></span>

* [<span data-ttu-id="8a658-326">IETF RFC 5789 PATCH 메서드 사양</span><span class="sxs-lookup"><span data-stu-id="8a658-326">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="8a658-327">IETF RFC 6902 JSON 패치 사양</span><span class="sxs-lookup"><span data-stu-id="8a658-327">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="8a658-328">IETF RFC 6901 JSON 패치 경로 형식 사양</span><span class="sxs-lookup"><span data-stu-id="8a658-328">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="8a658-329">[JSON 패치 문서](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="8a658-329">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="8a658-330">JSON 패치 문서를 만들기 위한 리소스의 링크를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="8a658-330">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="8a658-331">ASP.NET Core JSON 패치 소스 코드</span><span class="sxs-lookup"><span data-stu-id="8a658-331">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
