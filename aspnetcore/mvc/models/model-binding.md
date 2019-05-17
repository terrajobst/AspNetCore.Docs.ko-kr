---
title: ASP.NET Core의 모델 바인딩
author: tdykstra
description: ASP.NET Core MVC의 모델 바인딩이 HTTP 요청의 데이터를 작업 메서드 매개 변수에 매핑하는 방법을 알아봅니다.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: tdykstra
ms.date: 11/13/2018
uid: mvc/models/model-binding
ms.openlocfilehash: 1dc9b41328ed78440622acc1865b6f088d394403
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64883148"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="0a058-103">ASP.NET Core의 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="0a058-103">Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="0a058-104">작성자: [Rachel Appel](https://github.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="0a058-104">By [Rachel Appel](https://github.com/rachelappel)</span></span>

## <a name="introduction-to-model-binding"></a><span data-ttu-id="0a058-105">모델 바인딩 소개</span><span class="sxs-lookup"><span data-stu-id="0a058-105">Introduction to model binding</span></span>

<span data-ttu-id="0a058-106">ASP.NET Core MVC에서 모델 바인딩은 작업 메서드 매개 변수에 HTTP 요청의 데이터를 자동으로 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-106">Model binding in ASP.NET Core MVC maps data from HTTP requests to action method parameters.</span></span> <span data-ttu-id="0a058-107">매개 변수는 문자열, 정수, float 등과 같은 단순 형식이거나 복합 형식일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-107">The parameters may be simple types such as strings, integers, or floats, or they may be complex types.</span></span> <span data-ttu-id="0a058-108">들어오는 데이터를 대상에 매핑하는 것은 데이터의 크기나 복잡성에 관계없이 자주 반복되는 시나리오이므로 MVC의 훌륭한 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-108">This is a great feature of MVC because mapping incoming data to a counterpart is an often repeated scenario, regardless of size or complexity of the data.</span></span> <span data-ttu-id="0a058-109">MVC는 바인딩을 추상화하여 이 문제를 해결하므로 개발자는 모든 앱에서 동일한 코드의 약간 다른 버전을 다시 작성하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-109">MVC solves this problem by abstracting binding away so developers don't have to keep rewriting a slightly different version of that same code in every app.</span></span> <span data-ttu-id="0a058-110">형식 변환기 코드에 사용자 고유의 텍스트를 작성하는 것은 지루하고 오류가 발생하기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-110">Writing your own text to type converter code is tedious, and error prone.</span></span>

## <a name="how-model-binding-works"></a><span data-ttu-id="0a058-111">모델 바인딩의 작동 방식</span><span class="sxs-lookup"><span data-stu-id="0a058-111">How model binding works</span></span>

<span data-ttu-id="0a058-112">MVC는 HTTP 요청을 받으면 이를 컨트롤러의 특정 작업 메서드에 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-112">When MVC receives an HTTP request, it routes it to a specific action method of a controller.</span></span> <span data-ttu-id="0a058-113">경로 데이터에 기반하여 실행할 작업 메서드를 결정한 다음, HTTP 요청의 값을 해당 작업 메서드의 매개 변수에 바인드합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-113">It determines which action method to run based on what is in the route data, then it binds values from the HTTP request to that action method's parameters.</span></span> <span data-ttu-id="0a058-114">예를 들어 다음 URL을 가정해 봅니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-114">For example, consider the following URL:</span></span>

`http://contoso.com/movies/edit/2`

<span data-ttu-id="0a058-115">경로 템플릿은 `{controller=Home}/{action=Index}/{id?}`와 같으므로, `movies/edit/2`는 `Movies` 컨트롤러와 해당 `Edit` 작업 메서드로 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-115">Since the route template looks like this, `{controller=Home}/{action=Index}/{id?}`, `movies/edit/2` routes to the `Movies` controller, and its `Edit` action method.</span></span> <span data-ttu-id="0a058-116">또한 `id`라는 선택적 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-116">It also accepts an optional parameter called `id`.</span></span> <span data-ttu-id="0a058-117">작업 메서드의 코드는 다음과 같아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-117">The code for the action method should look something like this:</span></span>

```csharp
public IActionResult Edit(int? id)
   ```

<span data-ttu-id="0a058-118">참고: URL 경로에 있는 문자열은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-118">Note: The strings in the URL route are not case sensitive.</span></span>

<span data-ttu-id="0a058-119">MVC는 이름을 기준으로 요청 데이터를 작업 매개 변수에 바인딩하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-119">MVC will try to bind request data to the action parameters by name.</span></span> <span data-ttu-id="0a058-120">MVC는 매개 변수 이름과 설정 가능한 공용 속성 이름을 사용하여 각 매개 변수의 값을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-120">MVC will look for values for each parameter using the parameter name and the names of its public settable properties.</span></span> <span data-ttu-id="0a058-121">위의 예에서 유일한 작업 매개 변수의 이름은 `id`이며 MVC는 경로 값에서 같은 이름의 값에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-121">In the above example, the only action parameter is named `id`, which MVC binds to the value with the same name in the route values.</span></span> <span data-ttu-id="0a058-122">경로 값 외에도, MVC는 요청의 여러 부분에서 설정된 순서대로 데이터를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-122">In addition to route values MVC will bind data from various parts of the request and it does so in a set order.</span></span> <span data-ttu-id="0a058-123">아래는 모델 바인딩이 보이는 순서대로 나열된 데이터 원본 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-123">Below is a list of the data sources in the order that model binding looks through them:</span></span>

1. <span data-ttu-id="0a058-124">`Form values`: POST 메서드를 사용하여 HTTP 요청에 포함되는 양식 값입니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-124">`Form values`: These are form values that go in the HTTP request using the POST method.</span></span> <span data-ttu-id="0a058-125">(jQuery POST 요청 포함).</span><span class="sxs-lookup"><span data-stu-id="0a058-125">(including jQuery POST requests).</span></span>

2. <span data-ttu-id="0a058-126">`Route values`: [라우팅](xref:fundamentals/routing)에서 제공한 경로 값 집합</span><span class="sxs-lookup"><span data-stu-id="0a058-126">`Route values`: The set of route values provided by [Routing](xref:fundamentals/routing)</span></span>

3. <span data-ttu-id="0a058-127">`Query strings`: URI의 쿼리 문자열 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-127">`Query strings`: The query string part of the URI.</span></span>

<!-- DocFX BUG
The link works but generates an error when building with DocFX
@fundamentals/routing
[Routing](xref:fundamentals/routing)
-->

<span data-ttu-id="0a058-128">참고: 양식 값, 경로 데이터 및 쿼리 문자열은 모두 이름-값 쌍으로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-128">Note: Form values, route data, and query strings are all stored as name-value pairs.</span></span>

<span data-ttu-id="0a058-129">모델 바인딩에서는 `id`라는 이름의 키를 요청했고 양식 값에 `id`라는 항목이 없으므로 해당 키를 찾는 경로 값으로 이동했습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-129">Since model binding asked for a key named `id` and there's nothing named `id` in the form values, it moved on to the route values looking for that key.</span></span> <span data-ttu-id="0a058-130">이 예제에서는 일치 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-130">In our example, it's a match.</span></span> <span data-ttu-id="0a058-131">바인딩이 발생하고 값이 정수 2로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-131">Binding happens, and the value is converted to the integer 2.</span></span> <span data-ttu-id="0a058-132">편집(문자열 ID)을 사용하는 동일한 요청이 문자열 "2"로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-132">The same request using Edit(string id) would convert to the string "2".</span></span>

<span data-ttu-id="0a058-133">지금까지 예에서는 단순 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-133">So far the example uses simple types.</span></span> <span data-ttu-id="0a058-134">MVC에서 단순 형식은 모든 .NET 기본 형식 또는 문자열 형식 변환기를 사용한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-134">In MVC simple types are any .NET primitive type or type with a string type converter.</span></span> <span data-ttu-id="0a058-135">작업 메서드의 매개 변수가 `Movie` 형식과 같은 클래스이고 속성으로 단순 및 복합 형식을 모두 포함하는 경우, MVC의 모델 바인딩에서 이를 원활하게 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-135">If the action method's parameter were a class such as the `Movie` type, which contains both simple and complex types as properties, MVC's model binding will still handle it nicely.</span></span> <span data-ttu-id="0a058-136">리플렉션 및 재귀를 사용하여 복합 형식의 속성을 검색하고 일치하는 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-136">It uses reflection and recursion to traverse the properties of complex types looking for matches.</span></span> <span data-ttu-id="0a058-137">모델 바인딩은 값을 속성에 바인딩하기 위해 *parameter_name.property_name* 패턴을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-137">Model binding looks for the pattern *parameter_name.property_name* to bind values to properties.</span></span> <span data-ttu-id="0a058-138">이 양식의 일치하는 값을 찾지 못하면 속성 이름만 사용하여 바인딩을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-138">If it doesn't find matching values of this form, it will attempt to bind using just the property name.</span></span> <span data-ttu-id="0a058-139">`Collection` 형식과 같은 형식에 대해, 모델 바인딩에서는 *parameter_name[index]* 또는 *[index]* 에 대해 일치 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-139">For those types such as `Collection` types, model binding looks for matches to *parameter_name[index]* or just *[index]*.</span></span> <span data-ttu-id="0a058-140">모델 바인딩에서는 `Dictionary` 형식을 유사하게 처리하며 키가 단순 형식인 경우, *parameter_name[key]* 또는 *[key]* 를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-140">Model binding treats  `Dictionary` types similarly, asking for *parameter_name[key]* or just *[key]*, as long as the keys are simple types.</span></span> <span data-ttu-id="0a058-141">지원되는 키는 동일한 모델 형식에 대해 생성된 필드 이름 HTML 및 태그 도우미와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-141">Keys that are supported match the field names HTML and tag helpers generated for the same model type.</span></span> <span data-ttu-id="0a058-142">이렇게 하면 라운드트립 값이 가능해지므로 생성 또는 편집에서 바인딩된 데이터가 유효성 검사를 통과하지 못했을 때와 같이, 사용자 편의를 위해 양식 필드는 사용자 입력으로 채워져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-142">This enables round-tripping values so that the form fields remain filled with the user's input for their convenience, for example, when bound data from a create or edit didn't pass validation.</span></span>

<span data-ttu-id="0a058-143">모델 바인딩을 가능하게 하려면 클래스에 공용 기본 생성자와 바인딩할 공용 쓰기 가능 속성이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-143">To make model binding possible, the class must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="0a058-144">모델 바인딩이 발생하면 클래스는 공용 기본 생성자를 사용하여 인스턴스화된 다음, 속성을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-144">When model binding occurs, the class is instantiated using the public default constructor, then the properties can be set.</span></span>

<span data-ttu-id="0a058-145">매개 변수가 바인딩되면 모델 바인딩은 해당 이름의 값을 찾는 것을 중지하고 다음 매개 변수를 바인딩하도록 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-145">When a parameter is bound, model binding stops looking for values with that name and it moves on to bind the next parameter.</span></span> <span data-ttu-id="0a058-146">그렇지 않은 경우 기본 모델 바인딩 동작은 해당 형식에 따라 매개 변수를 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-146">Otherwise, the default model binding behavior sets parameters to their default values depending on their type:</span></span>

* <span data-ttu-id="0a058-147">`T[]`: `byte[]` 형식의 배열을 제외하고, 바인딩은 `T[]` 형식의 매개 변수를 `Array.Empty<T>()`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-147">`T[]`: With the exception of arrays of type `byte[]`, binding sets parameters of type `T[]` to `Array.Empty<T>()`.</span></span> <span data-ttu-id="0a058-148">`byte[]` 형식의 배열은 `null`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-148">Arrays of type `byte[]` are set to `null`.</span></span>

* <span data-ttu-id="0a058-149">참조 형식: 바인딩은 속성을 설정하지 않고 기본 생성자를 사용하여 클래스의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-149">Reference Types: Binding creates an instance of a class with the default constructor without setting properties.</span></span> <span data-ttu-id="0a058-150">그러나 모델 바인딩에서는 `string` 매개 변수를 `null`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-150">However, model binding sets `string` parameters to `null`.</span></span>

* <span data-ttu-id="0a058-151">Nullable 형식: Nullable 형식은 `null`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-151">Nullable Types: Nullable types are set to `null`.</span></span> <span data-ttu-id="0a058-152">위의 예제에서 모델 바인딩은 `int?` 형식이므로 `id`를 `null`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-152">In the above example, model binding sets `id` to `null` since it's of type `int?`.</span></span>

* <span data-ttu-id="0a058-153">값 형식: Null을 허용하지 않는 값 형식 `T`는 `default(T)`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-153">Value Types: Non-nullable value types of type `T` are set to `default(T)`.</span></span> <span data-ttu-id="0a058-154">예를 들어 모델 바인딩은 `int id` 매개 변수를 0으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-154">For example, model binding will set a parameter `int id` to 0.</span></span> <span data-ttu-id="0a058-155">기본값에 의존하지 않고 모델 유효성 검사 또는 nullable 형식을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-155">Consider using model validation or nullable types rather than relying on default values.</span></span>

<span data-ttu-id="0a058-156">바인딩에 실패하는 경우 MVC는 오류를 throw하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-156">If binding fails, MVC doesn't throw an error.</span></span> <span data-ttu-id="0a058-157">사용자 입력을 허용하는 모든 작업에서 `ModelState.IsValid` 속성을 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-157">Every action which accepts user input should check the `ModelState.IsValid` property.</span></span>

<span data-ttu-id="0a058-158">참고: 컨트롤러의 `ModelState` 속성에 있는 각 항목은 `Errors` 속성이 포함된 `ModelStateEntry`입니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-158">Note: Each entry in the controller's `ModelState` property is a `ModelStateEntry` containing an `Errors` property.</span></span> <span data-ttu-id="0a058-159">이 컬렉션을 직접 쿼리할 필요는 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-159">It's rarely necessary to query this collection yourself.</span></span> <span data-ttu-id="0a058-160">대신 `ModelState.IsValid`를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="0a058-160">Use `ModelState.IsValid` instead.</span></span>

<span data-ttu-id="0a058-161">또한 모델 바인딩을 수행할 때 MVC가 고려해야 하는 몇 가지 특수 데이터 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-161">Additionally, there are some special data types that MVC must consider when performing model binding:</span></span>

* <span data-ttu-id="0a058-162">`IFormFile`, `IEnumerable<IFormFile>`: HTTP 요청의 일부인 하나 이상의 업로드된 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-162">`IFormFile`, `IEnumerable<IFormFile>`: One or more uploaded files that are part of the HTTP request.</span></span>

* <span data-ttu-id="0a058-163">`CancellationToken`: 비동기 컨트롤러에서 작업을 취소하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-163">`CancellationToken`: Used to cancel activity in asynchronous controllers.</span></span>

<span data-ttu-id="0a058-164">이러한 형식은 작업 매개 변수 또는 클래스 형식의 속성에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-164">These types can be bound to action parameters or to properties on a class type.</span></span>

<span data-ttu-id="0a058-165">모델 바인딩이 완료되면 [유효성 검사](validation.md)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-165">Once model binding is complete, [Validation](validation.md) occurs.</span></span> <span data-ttu-id="0a058-166">기본 모델 바인딩은 대부분의 개발 시나리오에서 잘 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-166">Default model binding works great for the vast majority of development scenarios.</span></span> <span data-ttu-id="0a058-167">또한 확장 가능하므로 고유한 요구 사항이 있는 경우 기본 제공 동작을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-167">It's also extensible so if you have unique needs you can customize the built-in behavior.</span></span>

## <a name="customize-model-binding-behavior-with-attributes"></a><span data-ttu-id="0a058-168">특성으로 모델 바인딩 동작 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="0a058-168">Customize model binding behavior with attributes</span></span>

<span data-ttu-id="0a058-169">MVC에는 기본 모델 바인딩 동작을 다른 원본으로 전달하는 데 사용할 수 있는 몇 가지 특성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-169">MVC contains several attributes that you can use to direct its default model binding behavior to a different source.</span></span> <span data-ttu-id="0a058-170">예를 들어 속성에 바인딩이 필요한지, `[BindRequired]` 또는 `[BindNever]` 특성을 사용하여 아예 바인딩이 발생하지 않아야 하는지 여부를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-170">For example, you can specify whether binding is required for a property, or if it should never happen at all by using the `[BindRequired]` or `[BindNever]` attributes.</span></span> <span data-ttu-id="0a058-171">또는 기본 데이터 원본을 재정의하고 모델 바인더의 데이터 원본을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-171">Alternatively, you can override the default data source, and specify the model binder's data source.</span></span> <span data-ttu-id="0a058-172">다음은 모델 바인딩 특성 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-172">Below is a list of model binding attributes:</span></span>

* <span data-ttu-id="0a058-173">`[BindRequired]`: 이 특성은 바인딩이 발생할 수 없는 경우 모델 상태 오류를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-173">`[BindRequired]`: This attribute adds a model state error if binding cannot occur.</span></span>

* <span data-ttu-id="0a058-174">`[BindNever]`: 모델 바인더에 이 매개 변수에 바인딩하지 않도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-174">`[BindNever]`: Tells the model binder to never bind to this parameter.</span></span>

* <span data-ttu-id="0a058-175">`[FromHeader]`, `[FromQuery]`, `[FromRoute]`, `[FromForm]`: 적용하려는 정확한 바인딩 소스를 지정할 때 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-175">`[FromHeader]`, `[FromQuery]`, `[FromRoute]`, `[FromForm]`: Use these to specify the exact binding source you want to apply.</span></span>

* <span data-ttu-id="0a058-176">`[FromServices]`: 이 특성은 [종속성 주입](../../fundamentals/dependency-injection.md)을 사용하여 서비스에서 매개 변수를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-176">`[FromServices]`: This attribute uses [dependency injection](../../fundamentals/dependency-injection.md) to bind parameters from services.</span></span>

* <span data-ttu-id="0a058-177">`[FromBody]`: 구성된 포맷터를 사용하여 요청 본문에서 데이터를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-177">`[FromBody]`: Use the configured formatters to bind data from the request body.</span></span> <span data-ttu-id="0a058-178">포맷터는 요청의 콘텐츠 형식에 따라 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-178">The formatter is selected based on content type of the request.</span></span>

* <span data-ttu-id="0a058-179">`[ModelBinder]`: 기본 모델 바인더, 바인딩 소스 및 이름을 재정의하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-179">`[ModelBinder]`: Used to override the default model binder, binding source and name.</span></span>

<span data-ttu-id="0a058-180">특성은 모델 바인딩의 기본 동작을 재정의해야 할 때 매우 유용한 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-180">Attributes are very helpful tools when you need to override the default behavior of model binding.</span></span>

## <a name="customize-model-binding-and-validation-globally"></a><span data-ttu-id="0a058-181">모델 바인딩 사용자 지정 및 전역으로 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="0a058-181">Customize model binding and validation globally</span></span>

<span data-ttu-id="0a058-182">모델 바인딩 및 시스템 동작의 유효성 검사는 다음을 설명하는 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-182">The model binding and validation system's behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) that describes:</span></span>

* <span data-ttu-id="0a058-183">모델이 바인딩되는 방법</span><span class="sxs-lookup"><span data-stu-id="0a058-183">How a model is to be bound.</span></span>
* <span data-ttu-id="0a058-184">형식 및 해당 속성에서 유효성 검사가 발생하는 방법</span><span class="sxs-lookup"><span data-stu-id="0a058-184">How validation occurs on the type and its properties.</span></span>

<span data-ttu-id="0a058-185">[MvcOptions.ModelMetadataDetailsProviders](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions.modelmetadatadetailsproviders#Microsoft_AspNetCore_Mvc_MvcOptions_ModelMetadataDetailsProviders)에 세부 정보 공급자를 추가하여 시스템의 동작 측면을 전역적으로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-185">Aspects of the system's behavior can be configured globally by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions.modelmetadatadetailsproviders#Microsoft_AspNetCore_Mvc_MvcOptions_ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="0a058-186">MVC에는 특정 형식에 대한 모델 바인딩 또는 유효성 검사 비활성화와 같은 동작 구성을 허용하는 몇 가지 기본 제공 정보 공급자가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-186">MVC has a few built-in details providers that allow configuring behavior such as disabling model binding or validation for certain types.</span></span>

<span data-ttu-id="0a058-187">특정 형식의 모든 모델에 대한 모델 바인딩을 비활성화하려면 `Startup.ConfigureServices`에서 [ExcludeBindingMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.excludebindingmetadataprovider)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-187">To disable model binding on all models of a certain type, add an [ExcludeBindingMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.excludebindingmetadataprovider) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0a058-188">예를 들어 `System.Version` 형식의 모든 모델에 대한 모델 바인딩을 비활성화하려면:</span><span class="sxs-lookup"><span data-stu-id="0a058-188">For example, to disable model binding on all models of type `System.Version`:</span></span>

```csharp
services.AddMvc().AddMvcOptions(options =>
    options.ModelMetadataDetailsProviders.Add(
        new ExcludeBindingMetadataProvider(typeof(System.Version))));
```

<span data-ttu-id="0a058-189">특정 형식의 속성에 대한 유효성 검사를 비활성화하려면 `Startup.ConfigureServices`에 [SuppressChildValidationMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.suppresschildvalidationmetadataprovider)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-189">To disable validation on properties of a certain type, add a [SuppressChildValidationMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.suppresschildvalidationmetadataprovider) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0a058-190">예를 들어 `System.Guid` 형식의 속성에 대한 유효성 검사를 비활성화하려면:</span><span class="sxs-lookup"><span data-stu-id="0a058-190">For example, to disable validation on properties of type `System.Guid`:</span></span>

```csharp
services.AddMvc().AddMvcOptions(options =>
    options.ModelMetadataDetailsProviders.Add(
        new SuppressChildValidationMetadataProvider(typeof(System.Guid))));
```

## <a name="bind-formatted-data-from-the-request-body"></a><span data-ttu-id="0a058-191">요청 본문에서 형식이 지정된 데이터 바인딩</span><span class="sxs-lookup"><span data-stu-id="0a058-191">Bind formatted data from the request body</span></span>

<span data-ttu-id="0a058-192">요청 데이터는 JSON, XML 및 기타 여러 가지 형식으로 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-192">Request data can come in a variety of formats including JSON, XML and many others.</span></span> <span data-ttu-id="0a058-193">[FromBody] 특성을 사용하여 매개 변수를 요청 본문의 데이터에 바인딩하려는 것을 표시하는 경우, MVC는 구성된 포맷터 집합을 사용하여 해당 콘텐츠 형식을 기반으로 요청 데이터를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-193">When you use the [FromBody] attribute to indicate that you want to bind a parameter to data in the request body, MVC uses a configured set of formatters to handle the request data based on its content type.</span></span> <span data-ttu-id="0a058-194">기본적으로 MVC에는 JSON 데이터를 처리하기 위한 `JsonInputFormatter` 클래스가 포함되어 있지만 XML 및 기타 사용자 지정 형식을 처리하기 위한 포맷터를 더 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-194">By default MVC includes a `JsonInputFormatter` class for handling JSON data, but you can add additional formatters for handling XML and other custom formats.</span></span>

> [!NOTE]
> <span data-ttu-id="0a058-195">`[FromBody]`로 데코레이팅된 작업당 최대 하나의 매개 변수가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-195">There can be at most one parameter per action decorated with `[FromBody]`.</span></span> <span data-ttu-id="0a058-196">ASP.NET Core MVC 런타임은 요청 스트림을 읽는 책임을 포맷터에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-196">The ASP.NET Core MVC run-time delegates the responsibility of reading the request stream to the formatter.</span></span> <span data-ttu-id="0a058-197">매개 변수에 대해 요청 스트림을 읽은 후에는, 일반적으로 다른 `[FromBody]` 매개 변수를 바인딩하기 위해 요청 스트림을 다시 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-197">Once the request stream is read for a parameter, it's generally not possible to read the request stream again for binding other `[FromBody]` parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="0a058-198">`JsonInputFormatter`은 기본 포맷터이며 [Json.NET](https://www.newtonsoft.com/json)을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-198">The `JsonInputFormatter` is the default formatter and is based on [Json.NET](https://www.newtonsoft.com/json).</span></span>

<span data-ttu-id="0a058-199">ASP.NET Core는 다르게 지정되는 특성이 적용되지 않는 한, [Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) 헤더와 매개 변수의 형식을 기반으로 입력 포맷터를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-199">ASP.NET Core selects input formatters based on the [Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) header and the type of the parameter, unless there's an attribute applied to it specifying otherwise.</span></span> <span data-ttu-id="0a058-200">XML 또는 다른 형식을 사용하려면 *Startup.cs* 파일에서 구성해야 하지만 NuGet을 사용하여 먼저 `Microsoft.AspNetCore.Mvc.Formatters.Xml`에 대한 참조를 얻어야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-200">If you'd like to use XML or another format you must configure it in the *Startup.cs* file, but you may first have to obtain a reference to `Microsoft.AspNetCore.Mvc.Formatters.Xml` using NuGet.</span></span> <span data-ttu-id="0a058-201">시작 코드는 다음과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-201">Your startup code should look something like this:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddXmlSerializerFormatters();
   }
```

<span data-ttu-id="0a058-202">*Startup.cs* 파일의 코드에는 ASP.NET Core 앱용 서비스를 빌드하는 데 사용할 수 있는 `services` 인수가 있는 `ConfigureServices` 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-202">Code in the *Startup.cs* file contains a `ConfigureServices` method with a `services` argument you can use to build up services for your ASP.NET Core app.</span></span> <span data-ttu-id="0a058-203">이 샘플에서는 MVC가 이 앱에 대해 제공할 서비스로 XML 포맷터를 추가하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-203">In the sample, we are adding an XML formatter as a service that MVC will provide for this app.</span></span> <span data-ttu-id="0a058-204">`AddMvc` 메서드에 전달된 `options` 인수를 통해 앱 시작 시 MVC의 필터, 포맷터 및 기타 시스템 옵션을 추가하고 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-204">The `options` argument passed into the `AddMvc` method allows you to add and manage filters, formatters, and other system options from MVC upon app startup.</span></span> <span data-ttu-id="0a058-205">그런 다음, 컨트롤러 클래스나 작업 메서드에 `Consumes` 특성을 적용하여 원하는 형식으로 작업합니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-205">Then apply the `Consumes` attribute to controller classes or action methods to work with the format you want.</span></span>

### <a name="custom-model-binding"></a><span data-ttu-id="0a058-206">사용자 지정 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="0a058-206">Custom Model Binding</span></span>

<span data-ttu-id="0a058-207">사용자 고유의 사용자 지정 모델 바인더를 작성하여 모델 바인딩을 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a058-207">You can extend model binding by writing your own custom model binders.</span></span> <span data-ttu-id="0a058-208">[사용자 모델 바인딩](../advanced/custom-model-binding.md)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="0a058-208">Learn more about [custom model binding](../advanced/custom-model-binding.md).</span></span>
