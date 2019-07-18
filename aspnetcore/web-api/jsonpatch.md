---
title: ASP.NET Core Web API의 JsonPatch
author: tdykstra
description: ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 알아봅니다.
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/24/2019
uid: web-api/jsonpatch
ms.openlocfilehash: 97264903d85dbb397e85fdbf7b070e2aaae74bc8
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815548"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="37175-103">ASP.NET Core Web API의 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="37175-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="37175-104">작성자: [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="37175-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="37175-105">이 문서에서는 ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="37175-106">PATCH HTTP 요청 메서드</span><span class="sxs-lookup"><span data-stu-id="37175-106">PATCH HTTP request method</span></span>

<span data-ttu-id="37175-107">PUT 및 [PATCH](https://tools.ietf.org/html/rfc5789) 메서드는 기존 리소스를 업데이트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="37175-107">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="37175-108">두 메서드의 차이점은 PUT은 전체 리소스를 바꾸지만, PATCH는 변경 내용만 지정한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="37175-108">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="37175-109">JSON 패치</span><span class="sxs-lookup"><span data-stu-id="37175-109">JSON Patch</span></span>

<span data-ttu-id="37175-110">[JSON 패치](https://tools.ietf.org/html/rfc6902)는 리소스에 적용할 업데이트를 지정하기 위한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="37175-110">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="37175-111">JSON 패치 문서에는 작업 배열이 포함됩니다. </span><span class="sxs-lookup"><span data-stu-id="37175-111">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="37175-112">각 작업은 배열 요소 추가 또는 속성 값 바꾸기와 같은 특정 유형의 변경을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-112">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="37175-113">예를 들어 다음 JSON 문서는 리소스, 리소스의 JSON 패치 문서 및 패치 작업을 적용한 결과를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="37175-113">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="37175-114">리소스 예제</span><span class="sxs-lookup"><span data-stu-id="37175-114">Resource example</span></span>

[!code-csharp[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="37175-115">JSON 패치 예제</span><span class="sxs-lookup"><span data-stu-id="37175-115">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="37175-116">앞의 JSON에서:</span><span class="sxs-lookup"><span data-stu-id="37175-116">In the preceding JSON:</span></span>

* <span data-ttu-id="37175-117">`op` 속성은 작업 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="37175-117">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="37175-118">`path` 속성은 업데이트할 요소를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="37175-118">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="37175-119">`value` 속성은 새 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-119">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="37175-120">패치 후 리소스</span><span class="sxs-lookup"><span data-stu-id="37175-120">Resource after patch</span></span>

<span data-ttu-id="37175-121">앞의 JSON 패치 문서를 적용한 후 리소스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="37175-121">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="37175-122">JSON 패치 문서를 리소스에 적용하여 변경된 내용은 원자성입니다. 목록에 있는 작업이 하나라도 실패하면 목록의 작업이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37175-122">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="37175-123">경로 구문</span><span class="sxs-lookup"><span data-stu-id="37175-123">Path syntax</span></span>

<span data-ttu-id="37175-124">작업 개체의 [path](https://tools.ietf.org/html/rfc6901) 속성에서 수준 사이에는 슬래시가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37175-124">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="37175-125">예를 들어, `"/address/zipCode"`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-125">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="37175-126">0부터 시작하는 인덱스는 배열 요소를 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="37175-126">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="37175-127">`addresses` 배열의 첫 번째 요소는 `/addresses/0`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37175-127">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="37175-128">배열 끝에 `add`(추가)하려면 인덱스 번호가 아닌 하이픈(-)을 사용합니다(`/addresses/-`).</span><span class="sxs-lookup"><span data-stu-id="37175-128">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="37175-129">작업</span><span class="sxs-lookup"><span data-stu-id="37175-129">Operations</span></span>

<span data-ttu-id="37175-130">다음 표에서는 [JSON 패치 사양](https://tools.ietf.org/html/rfc6902)에 정의된 지원되는 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="37175-130">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="37175-131">작업</span><span class="sxs-lookup"><span data-stu-id="37175-131">Operation</span></span>  | <span data-ttu-id="37175-132">참고 사항</span><span class="sxs-lookup"><span data-stu-id="37175-132">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="37175-133">속성 또는 배열 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-133">Add a property or array element.</span></span> <span data-ttu-id="37175-134">기존 속성의 경우 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-134">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="37175-135">속성 또는 배열 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-135">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="37175-136">동일한 위치에서 `add`가 뒤에 오는 `remove`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="37175-136">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="37175-137">소스의 값을 사용하는 대상에 대한 `add`가 뒤에 오는 소스에서 `remove`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="37175-137">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="37175-138">소스의 값을 사용하는 대상에 대한 `add`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="37175-138">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="37175-139">`path`의 값이 제공된 `value`와 같은 경우 성공 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-139">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="37175-140">ASP.NET Core의 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="37175-140">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="37175-141">JSON 패치의 ASP.NET Core 구현은 [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 패키지로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="37175-141">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="37175-142">패키지는 [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) 메타패키지에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="37175-142">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="37175-143">작업 메서드 코드</span><span class="sxs-lookup"><span data-stu-id="37175-143">Action method code</span></span>

<span data-ttu-id="37175-144">API 컨트롤러에서 JSON 패치의 작업 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="37175-144">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="37175-145">`HttpPatch` 특성을 사용하여 주석으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="37175-145">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="37175-146">일반적으로 [FromBody]를 사용하여 `JsonPatchDocument<T>`를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-146">Accepts a `JsonPatchDocument<T>`, typically with [FromBody].</span></span>
* <span data-ttu-id="37175-147">패치 문서에서 `ApplyTo`를 호출하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-147">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="37175-148">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="37175-148">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="37175-149">샘플 앱의 이 코드는 다음 `Customer` 모델에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-149">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="37175-150">샘플 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="37175-150">The sample action method:</span></span>

* <span data-ttu-id="37175-151">`Customer`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-151">Constructs a `Customer`.</span></span>
* <span data-ttu-id="37175-152">패치를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-152">Applies the patch.</span></span>
* <span data-ttu-id="37175-153">응답의 본문으로 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-153">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="37175-154">실제 앱에서 이 코드는 데이터베이스와 같은 저장소에서 데이터를 검색하고 패치를 적용한 후 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-154">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="37175-155">모델 상태</span><span class="sxs-lookup"><span data-stu-id="37175-155">Model state</span></span>

<span data-ttu-id="37175-156">앞의 작업 메서드 예제에서는 모델 상태를 매개 변수 중 하나로 가져오는 `ApplyTo`의 오버로드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-156">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="37175-157">이 옵션을 사용하여 응답으로 오류 메시지를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="37175-157">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="37175-158">다음 예제에서는 `test` 작업에 대한 400 잘못된 요청 응답의 본문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="37175-158">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="37175-159">동적 개체</span><span class="sxs-lookup"><span data-stu-id="37175-159">Dynamic objects</span></span>

<span data-ttu-id="37175-160">다음 작업 메서드 예제에서는 동적 개체에 패치를 적용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="37175-160">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="37175-161">추가 작업</span><span class="sxs-lookup"><span data-stu-id="37175-161">The add operation</span></span>

* <span data-ttu-id="37175-162">`path`가 배열 요소를 가리키는 경우: `path`에 지정된 요소 앞에 새 요소를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-162">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="37175-163">`path`가 속성을 가리키는 경우: 속성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-163">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="37175-164">`path`가 존재하지 않는 위치를 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="37175-164">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="37175-165">패치할 리소스가 동적 개체인 경우: 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-165">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="37175-166">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-166">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="37175-167">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Order` 개체를 `Orders` 배열 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-167">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="37175-168">제거 작업</span><span class="sxs-lookup"><span data-stu-id="37175-168">The remove operation</span></span>

* <span data-ttu-id="37175-169">`path`가 배열 요소를 가리키는 경우: 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-169">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="37175-170">`path`가 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="37175-170">If `path` points to a property:</span></span>
  * <span data-ttu-id="37175-171">패치할 리소스가 동적 개체인 경우: 속성을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-171">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="37175-172">패치할 리소스가 정적 개체인 경우:</span><span class="sxs-lookup"><span data-stu-id="37175-172">If resource to patch is a static object:</span></span> 
    * <span data-ttu-id="37175-173">속성이 nullable인 경우: null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-173">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="37175-174">속성이 nullable이 아닌 경우: `default<T>`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-174">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="37175-175">다음 샘플 패치 문서는 `CustomerName`을 null로 설정하고 `Orders[0]`를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-175">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="37175-176">바꾸기 작업</span><span class="sxs-lookup"><span data-stu-id="37175-176">The replace operation</span></span>

<span data-ttu-id="37175-177">이 작업은 `add`가 뒤에 오는 `remove`와 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-177">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="37175-178">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Orders[0]`를 새 `Order` 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="37175-178">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="37175-179">이동 작업</span><span class="sxs-lookup"><span data-stu-id="37175-179">The move operation</span></span>

* <span data-ttu-id="37175-180">`path`가 배열 요소를 가리키는 경우: `from` 요소를 `path` 요소 위치에 복사한 후 `from` 요소에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-180">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="37175-181">`path`가 속성을 가리키는 경우: `from` 속성 값을 `path` 속성에 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-181">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="37175-182">`path`가 존재하지 않는 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="37175-182">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="37175-183">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-183">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="37175-184">패치할 리소스가 동적 개체인 경우: `from` 속성을 `path`에 지정된 위치로 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-184">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="37175-185">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-185">The following sample patch document:</span></span>

* <span data-ttu-id="37175-186">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-186">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="37175-187">`Orders[0].OrderName`을 null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-187">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="37175-188">`Orders[1]`를 `Orders[0]` 앞으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-188">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="37175-189">복사 작업</span><span class="sxs-lookup"><span data-stu-id="37175-189">The copy operation</span></span>

<span data-ttu-id="37175-190">이 작업은 마지막 `remove` 단계 없이 `move` 작업과 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-190">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="37175-191">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-191">The following sample patch document:</span></span>

* <span data-ttu-id="37175-192">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-192">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="37175-193">`Orders[1]` 복사본을 `Orders[0]` 앞에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-193">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="37175-194">테스트 작업</span><span class="sxs-lookup"><span data-stu-id="37175-194">The test operation</span></span>

<span data-ttu-id="37175-195">`path`에 지정된 위치의 값이 `value`에 제공된 값과 다른 경우 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-195">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="37175-196">이 경우 패치 문서의 다른 모든 작업이 성공하더라도 전체 PATCH 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-196">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="37175-197">`test` 작업은 일반적으로 동시성 충돌이 발생하는 경우 업데이트를 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="37175-197">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="37175-198">`CustomerName`의 초기 값이 “John”인 경우 테스트에 실패하므로 다음 샘플 패치 문서는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="37175-198">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="37175-199">코드 가져오기</span><span class="sxs-lookup"><span data-stu-id="37175-199">Get the code</span></span>

<span data-ttu-id="37175-200">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)</span><span class="sxs-lookup"><span data-stu-id="37175-200">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="37175-201">([다운로드하는 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37175-201">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="37175-202">샘플을 테스트하려면 앱을 실행하고 다음 설정을 사용하여 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="37175-202">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="37175-203">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="37175-203">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="37175-204">HTTP 메서드: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="37175-204">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="37175-205">헤더: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="37175-205">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="37175-206">본문: *JSON* 프로젝트 폴더에서 JSON 패치 문서 샘플 중 하나를 복사하여 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="37175-206">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37175-207">추가 자료</span><span class="sxs-lookup"><span data-stu-id="37175-207">Additional resources</span></span>

* [<span data-ttu-id="37175-208">IETF RFC 5789 PATCH 메서드 사양</span><span class="sxs-lookup"><span data-stu-id="37175-208">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="37175-209">IETF RFC 6902 JSON 패치 사양</span><span class="sxs-lookup"><span data-stu-id="37175-209">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="37175-210">IETF RFC 6901 JSON 패치 경로 형식 사양</span><span class="sxs-lookup"><span data-stu-id="37175-210">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="37175-211">[JSON 패치 문서](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="37175-211">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="37175-212">JSON 패치 문서를 만들기 위한 리소스의 링크를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="37175-212">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="37175-213">ASP.NET Core JSON 패치 소스 코드</span><span class="sxs-lookup"><span data-stu-id="37175-213">ASP.NET Core JSON Patch source code</span></span>](https://github.com/aspnet/AspNetCore/tree/master/src/Features/JsonPatch/src)
