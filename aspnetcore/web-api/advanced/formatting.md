---
title: ASP.NET Core Web API에서 응답 데이터 서식 지정
author: ardalis
description: ASP.NET Core Web API에서 응답 데이터의 서식을 지정하는 방법을 알아봅니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 10/14/2016
uid: web-api/advanced/formatting
ms.openlocfilehash: 04f5b3c544cf3fc47c8321c8233535400fcf55f4
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890768"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="f2293-103">ASP.NET Core Web API에서 응답 데이터 서식 지정</span><span class="sxs-lookup"><span data-stu-id="f2293-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="f2293-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f2293-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f2293-105">ASP.NET Core MVC에는 지정된 형식을 사용하거나 클라이언트 사양에 대한 응답으로 응답 데이터의 서식을 지정하기 위한 기본적인 지원 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-105">ASP.NET Core MVC has built-in support for formatting response data, using fixed formats or in response to client specifications.</span></span>

<span data-ttu-id="f2293-106">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f2293-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="f2293-107">형식별 작업 결과</span><span class="sxs-lookup"><span data-stu-id="f2293-107">Format-Specific Action Results</span></span>

<span data-ttu-id="f2293-108">일부 작업 결과 형식은 `JsonResult` 및 `ContentResult`와 같이 특정 형식과 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-108">Some action result types are specific to a particular format, such as `JsonResult` and `ContentResult`.</span></span> <span data-ttu-id="f2293-109">작업은 항상 특정한 방식으로 형식이 지정된 특정 결과를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-109">Actions can return specific results that are always formatted in a particular manner.</span></span> <span data-ttu-id="f2293-110">예를 들어 `JsonResult` 반환은 클라이언트 기본 설정에 관계 없이 JSON 형식의 데이터를 반환하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-110">For example, returning a `JsonResult` will return JSON-formatted data, regardless of client preferences.</span></span> <span data-ttu-id="f2293-111">마찬가지로, `ContentResult` 반환은 일반 텍스트 형식 문자열 데이터를 반환합니다(단순히 문자열을 반환).</span><span class="sxs-lookup"><span data-stu-id="f2293-111">Likewise, returning a `ContentResult` will return plain-text-formatted string data (as will simply returning a string).</span></span>

> [!NOTE]
> <span data-ttu-id="f2293-112">작업은 특정 형식을 반환할 필요가 없습니다. MVC는 모든 개체 반환 값을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-112">An action isn't required to return any particular type; MVC supports any object return value.</span></span> <span data-ttu-id="f2293-113">작업이 `IActionResult` 구현을 반환하고 컨트롤러가 `Controller`에서 상속하는 경우, 개발자는 많은 선택 사항에 해당하는 도우미 메서드를 다양하게 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-113">If an action returns an `IActionResult` implementation and the controller inherits from `Controller`, developers have many helper methods corresponding to many of the choices.</span></span> <span data-ttu-id="f2293-114">`IActionResult` 형식이 아닌 개체를 반환하는 작업의 결과는 적절한 `IOutputFormatter` 구현을 사용하여 직렬화됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-114">Results from actions that return objects that are not `IActionResult` types will be serialized using the appropriate `IOutputFormatter` implementation.</span></span>

<span data-ttu-id="f2293-115">데이터를 `Controller` 기본 클래스에서 상속하는 컨트롤러의 특정 형식으로 반환하려면 기본 제공 도우미 메서드 `Json`을 사용하여 일반 텍스트에 대해 `Content` 및 JSON을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-115">To return data in a specific format from a controller that inherits from the `Controller` base class, use the built-in helper method `Json` to return JSON and `Content` for plain text.</span></span> <span data-ttu-id="f2293-116">작업 메서드는 특정 결과 형식(예: `JsonResult`) 또는 `IActionResult`를 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-116">Your action method should return either the specific result type (for instance, `JsonResult`) or `IActionResult`.</span></span>

<span data-ttu-id="f2293-117">JSON 형식의 데이터 반환:</span><span class="sxs-lookup"><span data-stu-id="f2293-117">Returning JSON-formatted data:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

<span data-ttu-id="f2293-118">이 작업에서 샘플 응답:</span><span class="sxs-lookup"><span data-stu-id="f2293-118">Sample response from this action:</span></span>

![응답의 콘텐츠 형식이 application/json임이 표시된 Microsoft Edge에 있는 개발자 도구의 네트워크 탭](formatting/_static/json-response.png)

<span data-ttu-id="f2293-120">응답의 콘텐츠 형식은 `application/json`이며, 네트워크 요청 목록과 Response 헤더 섹션에 모두 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-120">Note that the content type of the response is `application/json`, shown both in the list of network requests and in the Response Headers section.</span></span> <span data-ttu-id="f2293-121">또한 Request 헤더 섹션의 Accept 헤더에서 브라우저(이 경우 Microsoft Edge)에 의해 제공되는 옵션의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-121">Also note the list of options presented by the browser (in this case, Microsoft Edge) in the Accept header in the Request Headers section.</span></span> <span data-ttu-id="f2293-122">현재 기술은 이 헤더를 무시합니다. 이를 따르는 데 관해서는 아래에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-122">The current technique is ignoring this header; obeying it is discussed below.</span></span>

<span data-ttu-id="f2293-123">서식 있는 일반 텍스트 데이터를 반환하려면 `ContentResult` 및 `Content` 도우미를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-123">To return plain text formatted data, use `ContentResult` and the `Content` helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

<span data-ttu-id="f2293-124">이 작업에서 응답:</span><span class="sxs-lookup"><span data-stu-id="f2293-124">A response from this action:</span></span>

![응답의 콘텐츠 형식이 text/plain임이 표시된 Microsoft Edge에 있는 개발자 도구의 네트워크 탭](formatting/_static/text-response.png)

<span data-ttu-id="f2293-126">이 경우는 반환된 `Content-Type`은 `text/plain`입니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-126">Note in this case the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="f2293-127">또한 문자열 응답 형식을 사용하여 이것과 동일한 동작을 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-127">You can also achieve this same behavior using just a string response type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> <span data-ttu-id="f2293-128">여러 반환 형식 또는 옵션이 포함된 특수 작업의 경우(예: 수행되는 작업의 결과를 기반으로 하는 다른 HTTP 상태 코드) 반환 형식으로 `IActionResult`를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-128">For non-trivial actions with multiple return types or options (for example, different HTTP status codes based on the result of operations performed), prefer `IActionResult` as the return type.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="f2293-129">콘텐츠 협상</span><span class="sxs-lookup"><span data-stu-id="f2293-129">Content Negotiation</span></span>

<span data-ttu-id="f2293-130">콘텐츠 협상(줄여서 *conneg*)은 클라이언트가 [Accept 헤더](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)를 지정하는 경우에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-130">Content negotiation (*conneg* for short) occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="f2293-131">ASP.NET Core MVC에서 사용하는 기본 형식은 JSON입니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-131">The default format used by ASP.NET Core MVC is JSON.</span></span> <span data-ttu-id="f2293-132">콘텐츠 협상은 `ObjectResult`에 의해 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-132">Content negotiation is implemented by `ObjectResult`.</span></span> <span data-ttu-id="f2293-133">또한 도우미 메서드에서 반환된 상태 코드별 작업 결과 상태 코드에 기본적으로 제공됩니다(모두 `ObjectResult` 기반).</span><span class="sxs-lookup"><span data-stu-id="f2293-133">It's also built into the status code specific action results returned from the helper methods (which are all based on `ObjectResult`).</span></span> <span data-ttu-id="f2293-134">또한 모델 형식(데이터 전송 형식으로 정의된 클래스)을 반환할 수 있으며, 프레임워크는 사용자를 위해 자동으로 `ObjectResult`에 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-134">You can also return a model type (a class you've defined as your data transfer type) and the framework will automatically wrap it in an `ObjectResult` for you.</span></span>

<span data-ttu-id="f2293-135">다음 작업 메서드는 `Ok` 및 `NotFound` 도우미 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-135">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

<span data-ttu-id="f2293-136">다른 형식이 요청되지 않는 한 JSON 형식 응답이 반환되고 서버는 요청된 형식을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-136">A JSON-formatted response will be returned unless another format was requested and the server can return the requested format.</span></span> <span data-ttu-id="f2293-137">[Fiddler](http://www.telerik.com/fiddler)와 같은 도구를 사용하여 Accept 헤더가 포함된 요청을 만들고 다른 형식을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-137">You can use a tool like [Fiddler](http://www.telerik.com/fiddler) to create a request that includes an Accept header and specify another format.</span></span> <span data-ttu-id="f2293-138">해당 경우에서 서버에 요청된 형식으로 응답을 생성할 수 있는 *포맷터*가 있으면 결과는 클라이언트 기본 형식으로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-138">In that case, if the server has a *formatter* that can produce a response in the requested format, the result will be returned in the client-preferred format.</span></span>

![application/xml의 Accept 헤더 값과 함께 수동으로 만든 GET 요청을 보여 주는 Fiddler 콘솔](formatting/_static/fiddler-composer.png)

<span data-ttu-id="f2293-140">위의 스크린 샷에서 `Accept: application/xml`을 지정하며 요청을 생성하는 데 Fiddler 작성기가 사용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-140">In the above screenshot, the Fiddler Composer has been used to generate a request, specifying `Accept: application/xml`.</span></span> <span data-ttu-id="f2293-141">기본적으로 ASP.NET Core MVC는 JSON만 지원하므로 다른 형식이 지정된 경우라도 결과는 여전히 JSON 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-141">By default, ASP.NET Core MVC only supports JSON, so even when another format is specified, the result returned is still JSON-formatted.</span></span> <span data-ttu-id="f2293-142">다음 섹션에서는 추가적인 포맷터를 추가하는 방법이 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-142">You'll see how to add additional formatters in the next section.</span></span>

<span data-ttu-id="f2293-143">컨트롤러 작업은 POCO(Plain Old CLR Objects)를 반환할 수 있습니다. 그런 경우 ASP.NET Core MVC는 사용자를 위해 개체를 래핑하는 `ObjectResult`를 자동으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-143">Controller actions can return POCOs (Plain Old CLR Objects), in which case ASP.NET Core MVC automatically creates an `ObjectResult` for you that wraps the object.</span></span> <span data-ttu-id="f2293-144">클라이언트는 서식 있는 직렬화된 개체를 가져옵니다. (JSON 형식이 기본값이며, XML 또는 다른 형식을 구성할 수 있습니다.)</span><span class="sxs-lookup"><span data-stu-id="f2293-144">The client will get the formatted serialized object (JSON format is the default; you can configure XML or other formats).</span></span> <span data-ttu-id="f2293-145">반환되는 개체가 `null`이면, 프레임워크는 `204 No Content` 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-145">If the object being returned is `null`, then the framework will return a `204 No Content` response.</span></span>

<span data-ttu-id="f2293-146">개체 형식 반환하기:</span><span class="sxs-lookup"><span data-stu-id="f2293-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

<span data-ttu-id="f2293-147">이 샘플에서는 유효한 작성자 별칭에 대한 요청이 작성자의 데이터로 200 정상 응답을 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-147">In the sample, a request for a valid author alias will receive a 200 OK response with the author's data.</span></span> <span data-ttu-id="f2293-148">잘못된 별칭에 대한 요청은 204 콘텐츠 없음 응답을 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-148">A request for an invalid alias will receive a 204 No Content response.</span></span> <span data-ttu-id="f2293-149">XML 및 JSON 형식의 응답을 보여 주는 스크린샷은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-149">Screenshots showing the response in XML and JSON formats are shown below.</span></span>

### <a name="content-negotiation-process"></a><span data-ttu-id="f2293-150">콘텐츠 협상 프로세스</span><span class="sxs-lookup"><span data-stu-id="f2293-150">Content Negotiation Process</span></span>

<span data-ttu-id="f2293-151">콘텐츠 *협상*은 `Accept` 헤더가 요청에 나타나는 경우에만 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-151">Content *negotiation* only takes place if an `Accept` header appears in the request.</span></span> <span data-ttu-id="f2293-152">요청에 accept 헤더가 포함되어 있는 경우 프레임워크는 기본 설정 순서대로 accept 헤더의 미디어 유형을 열거하고, accept 헤더에 의해 지정된 형식 중 하나로 응답을 생성할 수 있는 포맷터를 찾으려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-152">When a request contains an accept header, the framework will enumerate the media types in the accept header in preference order and will try to find a formatter that can produce a response in one of the formats specified by the accept header.</span></span> <span data-ttu-id="f2293-153">클라이언트의 요청을 충족할 수 있는 포맷터가 없는 경우 프레임워크는 응답을 생성할 수 있는 첫 번째 포맷터를 찾으려고 시도합니다(개발자가 대신 406 허용되지 않음을 반환하도록 `MvcOptions`에서 옵션을 구성하지 않을 경우).</span><span class="sxs-lookup"><span data-stu-id="f2293-153">In case no formatter is found that can satisfy the client's request, the framework will try to find the first formatter that can produce a response (unless the developer has configured the option on `MvcOptions` to return 406 Not Acceptable instead).</span></span> <span data-ttu-id="f2293-154">이 요청은 XML을 지정하나, XML 포맷터가 구성되지 않은 경우에는 JSON 포맷터가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-154">If the request specifies XML, but the XML formatter has not been configured, then the JSON formatter will be used.</span></span> <span data-ttu-id="f2293-155">더 일반적으로 요청된 형식을 제공할 수 있는 포맷터가 구성되지 않은 경우에는 개체의 서식을 지정할 수 있는 첫 번째 포맷터가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-155">More generally, if no formatter is configured that can provide the requested format, then the first formatter that can format the object is used.</span></span> <span data-ttu-id="f2293-156">헤더가 없으면 반환될 개체를 처리할 수 있는 첫 번째 포맷터가 응답을 직렬화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-156">If no header is given, the first formatter that can handle the object to be returned will be used to serialize the response.</span></span> <span data-ttu-id="f2293-157">이 경우 모든 협상이 발생하지 않으므로 서버에서 어떤 형식이 사용될지를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-157">In this case, there isn't any negotiation taking place - the server is determining what format it will use.</span></span>

> [!NOTE]
> <span data-ttu-id="f2293-158">Accept 헤더에 `*/*`가 포함되는 경우, `RespectBrowserAcceptHeader`가 `MvcOptions`에서 true로 설정되지 않으면 헤더는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-158">If the Accept header contains `*/*`, the Header will be ignored unless `RespectBrowserAcceptHeader` is set to true on `MvcOptions`.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="f2293-159">브라우저 및 콘텐츠 협상</span><span class="sxs-lookup"><span data-stu-id="f2293-159">Browsers and Content Negotiation</span></span>

<span data-ttu-id="f2293-160">일반적인 API 클라이언트와 달리 웹 브라우저는 와일드카드를 비롯한 다양한 형식 범위를 포함하는 `Accept` 헤더를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-160">Unlike typical API clients, web browsers tend to supply `Accept` headers that include a wide array of formats, including wildcards.</span></span> <span data-ttu-id="f2293-161">기본적으로 프레임워크가 브라우저에서 오는 요청을 감지하면 `Accept` 헤더를 무시하고, 대신에 콘텐츠를 애플리케이션에 구성된 기본 형식으로 반환합니다(달리 구성되지 않은 경우 JSON).</span><span class="sxs-lookup"><span data-stu-id="f2293-161">By default, when the framework detects that the request is coming from a browser, it will ignore the `Accept` header and instead return the content in the application's configured default format (JSON unless otherwise configured).</span></span> <span data-ttu-id="f2293-162">이는 API를 통해 다양한 브라우저를 사용하는 경우에 더 일관된 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-162">This provides a more consistent experience when using different browsers to consume APIs.</span></span>

<span data-ttu-id="f2293-163">애플리케이션 적용 브라우저 accept 헤더를 선호하는 경우, *Startup.cs*의 `ConfigureServices` 메서드에서 `RespectBrowserAcceptHeader`를 `true`로 설정하여 MVC 구성의 일부로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-163">If you would prefer your application honor browser accept headers, you can configure this as part of MVC's configuration by setting `RespectBrowserAcceptHeader` to `true` in the `ConfigureServices` method in *Startup.cs*.</span></span>

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
});
```

## <a name="configuring-formatters"></a><span data-ttu-id="f2293-164">포맷터 구성</span><span class="sxs-lookup"><span data-stu-id="f2293-164">Configuring Formatters</span></span>

<span data-ttu-id="f2293-165">애플리케이션이 기본 JSON 외에 추가 형식을 지원해야 하는 경우 NuGet 패키지를 추가하고 이를 지원하도록 MVC를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-165">If your application needs to support additional formats beyond the default of JSON, you can add NuGet packages and configure MVC to support them.</span></span> <span data-ttu-id="f2293-166">입력 및 출력에 대한 개별 포맷터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-166">There are separate formatters for input and output.</span></span> <span data-ttu-id="f2293-167">입력 포맷터는 [모델 바인딩](xref:mvc/models/model-binding)에서 사용되며, 출력 포맷터는 응답 형식을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-167">Input formatters are used by [Model Binding](xref:mvc/models/model-binding); output formatters are used to format responses.</span></span> <span data-ttu-id="f2293-168">[사용자 지정 포맷터](xref:web-api/advanced/custom-formatters)를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-168">You can also configure [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

### <a name="adding-xml-format-support"></a><span data-ttu-id="f2293-169">XML 형식 지원 추가</span><span class="sxs-lookup"><span data-stu-id="f2293-169">Adding XML Format Support</span></span>

<span data-ttu-id="f2293-170">XML 서식 지정에 대한 지원을 추가하려면 `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-170">To add support for XML formatting, install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

<span data-ttu-id="f2293-171">*Startup.cs*에서 MVC의 구성에 XmlSerializerFormatters를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-171">Add the XmlSerializerFormatters to MVC's configuration in *Startup.cs*:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

<span data-ttu-id="f2293-172">또는 출력 포맷터만 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-172">Alternately, you can add just the output formatter:</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlSerializerOutputFormatter());
});
```

<span data-ttu-id="f2293-173">이러한 두 방법은 `System.Xml.Serialization.XmlSerializer`를 사용하여 결과를 직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-173">These two approaches will serialize results using `System.Xml.Serialization.XmlSerializer`.</span></span> <span data-ttu-id="f2293-174">원하는 경우 연결된 해당 포맷터를 추가하여 `System.Runtime.Serialization.DataContractSerializer`를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-174">If you prefer, you can use the `System.Runtime.Serialization.DataContractSerializer` by adding its associated formatter:</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlDataContractSerializerOutputFormatter());
});
```

<span data-ttu-id="f2293-175">XML 서식 지정에 대한 지원을 추가하면 컨트롤러 메서드는 요청의 `Accept` 헤더에 따라 적절한 형식을 반환해야 합니다. 이 Fiddler 예제에서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-175">Once you've added support for XML formatting, your controller methods should return the appropriate format based on the request's `Accept` header, as this Fiddler example demonstrates:</span></span>

![Fiddler 콘솔: 요청에 대한 Raw 탭에 Accept 헤더 값이 application/xml임이 표시됩니다.](formatting/_static/xml-response.png)

<span data-ttu-id="f2293-178">Inspectors 탭에서 `Accept: application/xml` 헤더 집합으로 Raw GET 요청이 만들어진 것을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-178">You can see in the Inspectors tab that the Raw GET request was made with an `Accept: application/xml` header set.</span></span> <span data-ttu-id="f2293-179">응답 창에 `Content-Type: application/xml` 헤더 및 XML로 직렬화된 `Author` 개체가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-179">The response pane shows the `Content-Type: application/xml` header, and the `Author` object has been serialized to XML.</span></span>

<span data-ttu-id="f2293-180">`Accept` 헤더에서 `application/json`을 지정하도록 요청을 수정하려면 Composer 탭을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-180">Use the Composer tab to modify the request to specify `application/json` in the `Accept` header.</span></span> <span data-ttu-id="f2293-181">요청을 실행하면 응답이 JSON으로 형식이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-181">Execute the request, and the response will be formatted as JSON:</span></span>

![Fiddler 콘솔: 요청에 대한 Raw 탭에 Accept 헤더 값이 application/json임이 표시됩니다.](formatting/_static/json-response-fiddler.png)

<span data-ttu-id="f2293-184">이 스크린 샷에는 요청이 `Accept: application/json`의 헤더를 설정하고, 응답이 해당 `Content-Type`과 동일하게 지정하는 것을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-184">In this screenshot, you can see the request sets a header of `Accept: application/json` and the response specifies the same as its `Content-Type`.</span></span> <span data-ttu-id="f2293-185">`Author` 개체가 JSON 형식으로 응답 본문에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-185">The `Author` object is shown in the body of the response, in JSON format.</span></span>

### <a name="forcing-a-particular-format"></a><span data-ttu-id="f2293-186">특정 형식 적용</span><span class="sxs-lookup"><span data-stu-id="f2293-186">Forcing a Particular Format</span></span>

<span data-ttu-id="f2293-187">특정 작업에 대한 응답 형식을 제한하려는 경우 `[Produces]` 필터를 적용하여 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-187">If you would like to restrict the response formats for a specific action you can, you can apply the `[Produces]` filter.</span></span> <span data-ttu-id="f2293-188">`[Produces]` 필터는 특정 작업(또는 컨트롤러)에 대한 응답 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-188">The `[Produces]` filter specifies the response formats for a specific action (or controller).</span></span> <span data-ttu-id="f2293-189">대부분 [필터](xref:mvc/controllers/filters)와 마찬가지로, 작업, 컨트롤러 또는 전역 범위에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-189">Like most [Filters](xref:mvc/controllers/filters), this can be applied at the action, controller, or global scope.</span></span>

```csharp
[Produces("application/json")]
public class AuthorsController
```

<span data-ttu-id="f2293-190">`[Produces]` 필터는 다른 포맷터가 애플리케이션에 구성되고 클라이언트가 다른 사용 가능한 형식을 요청하는 `Accept` 헤더를 제공하는 경우에도 JSON 형식의 응답을 반환하도록 `AuthorsController` 내에서 모든 작업을 강제로 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-190">The `[Produces]` filter will force all actions within the `AuthorsController` to return JSON-formatted responses, even if other formatters were configured for the application and the client provided an `Accept` header requesting a different, available format.</span></span> <span data-ttu-id="f2293-191">필터를 전역에서 적용하는 방법을 비롯한 자세한 내용은 [필터](xref:mvc/controllers/filters)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f2293-191">See [Filters](xref:mvc/controllers/filters) to learn more, including how to apply filters globally.</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="f2293-192">특수한 사례 포맷터</span><span class="sxs-lookup"><span data-stu-id="f2293-192">Special Case Formatters</span></span>

<span data-ttu-id="f2293-193">일부 특수한 경우 기본 제공 포맷터를 사용하여 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-193">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="f2293-194">기본적으로 `string` 반환 형식은 *text/plain*(`Accept` 헤더를 통해 요청된 경우 *text/html*)으로 형식이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-194">By default, `string` return types will be formatted as *text/plain* (*text/html* if requested via `Accept` header).</span></span> <span data-ttu-id="f2293-195">이 동작은 `TextOutputFormatter`를 제거하여 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-195">This behavior can be removed by removing the `TextOutputFormatter`.</span></span> <span data-ttu-id="f2293-196">포맷터는 *Startup.cs*의 `Configure` 메서드에서 제거합니다(아래 참조).</span><span class="sxs-lookup"><span data-stu-id="f2293-196">You remove formatters in the `Configure` method in *Startup.cs* (shown below).</span></span> <span data-ttu-id="f2293-197">모델 개체 반환 형식이 있는 작업은 `null`을 반환하는 경우 204 콘텐츠 없음 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-197">Actions that have a model object return type will return a 204 No Content response when returning `null`.</span></span> <span data-ttu-id="f2293-198">이 동작은 `HttpNoContentOutputFormatter`를 제거하여 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-198">This behavior can be removed by removing the `HttpNoContentOutputFormatter`.</span></span> <span data-ttu-id="f2293-199">다음 코드를 `TextOutputFormatter` 및 `HttpNoContentOutputFormatter`를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-199">The following code removes the `TextOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

<span data-ttu-id="f2293-200">`TextOutputFormatter`가 없으면 `string`은 예를 들어 406 승인 금지를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-200">Without the `TextOutputFormatter`, `string` return types return 406 Not Acceptable, for example.</span></span> <span data-ttu-id="f2293-201">XML 포맷터가 있는 경우 `TextOutputFormatter`가 제거되면 `string` 반환 형식의 서식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-201">Note that if an XML formatter exists, it will format `string` return types if the `TextOutputFormatter` is removed.</span></span>

<span data-ttu-id="f2293-202">`HttpNoContentOutputFormatter`가 없으면 null 개체는 구성된 포맷터를 사용하여 서식이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-202">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="f2293-203">예를 들어 JSON 포맷터는 `null`의 본문으로 응답을 반환하는 반면, XML 포맷터는 특성 `xsi:nil="true"` 집합으로 빈 XML 요소를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-203">For example, the JSON formatter will simply return a response with a body of `null`, while the XML formatter will return an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="f2293-204">응답 형식 URL 매핑</span><span class="sxs-lookup"><span data-stu-id="f2293-204">Response Format URL Mappings</span></span>

<span data-ttu-id="f2293-205">클라이언트는 쿼리 문자열 또는 일부 경로와 같은 URL의 일부로, 또는 .xml이나 .json과 같은 형식별 파일 확장명을 사용하여 특정 형식을 요청할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-205">Clients can request a particular format as part of the URL, such as in the query string or part of the path, or by using a format-specific file extension such as .xml or .json.</span></span> <span data-ttu-id="f2293-206">요청 경로의 매핑은 API가 사용하는 경로에 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-206">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="f2293-207">예:</span><span class="sxs-lookup"><span data-stu-id="f2293-207">For example:</span></span>

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

<span data-ttu-id="f2293-208">이 경로를 사용하면 요청된 형식을 선택적 파일 확장명으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-208">This route would allow the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="f2293-209">`[FormatFilter]` 특성은 `RouteData`에서 형식 값의 존재 여부를 검사하며 응답이 생성될 때 응답 형식을 적절한 포맷터에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="f2293-209">The `[FormatFilter]` attribute checks for the existence of the format value in the `RouteData` and will map the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="f2293-210">경로</span><span class="sxs-lookup"><span data-stu-id="f2293-210">Route</span></span>            |             <span data-ttu-id="f2293-211">포맷터</span><span class="sxs-lookup"><span data-stu-id="f2293-211">Formatter</span></span>              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    <span data-ttu-id="f2293-212">기본 출력 포맷터</span><span class="sxs-lookup"><span data-stu-id="f2293-212">The default output formatter</span></span>    |
| `/products/GetById/5.json` | <span data-ttu-id="f2293-213">JSON 포맷터(구성된 경우)</span><span class="sxs-lookup"><span data-stu-id="f2293-213">The JSON formatter (if configured)</span></span> |
| `/products/GetById/5.xml`  | <span data-ttu-id="f2293-214">XML 포맷터(구성된 경우)</span><span class="sxs-lookup"><span data-stu-id="f2293-214">The XML formatter (if configured)</span></span>  |
