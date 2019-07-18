---
title: ASP.NET Core Web API에서 응답 데이터 서식 지정
author: ardalis
description: ASP.NET Core Web API에서 응답 데이터의 서식을 지정하는 방법을 알아봅니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 05/29/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: b050011aa38743353fb2a7d133abcdca0b8c6d33
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814812"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="3a230-103">ASP.NET Core Web API에서 응답 데이터 서식 지정</span><span class="sxs-lookup"><span data-stu-id="3a230-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="3a230-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3a230-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3a230-105">ASP.NET Core MVC에는 지정된 형식을 사용하거나 클라이언트 사양에 대한 응답으로 응답 데이터의 서식을 지정하기 위한 기본적인 지원 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-105">ASP.NET Core MVC has built-in support for formatting response data, using fixed formats or in response to client specifications.</span></span>

<span data-ttu-id="3a230-106">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a230-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="3a230-107">형식별 작업 결과</span><span class="sxs-lookup"><span data-stu-id="3a230-107">Format-Specific Action Results</span></span>

<span data-ttu-id="3a230-108">일부 작업 결과 형식은 `JsonResult` 및 `ContentResult`와 같이 특정 형식과 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-108">Some action result types are specific to a particular format, such as `JsonResult` and `ContentResult`.</span></span> <span data-ttu-id="3a230-109">작업은 항상 특정한 방식으로 형식이 지정된 특정 결과를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-109">Actions can return specific results that are always formatted in a particular manner.</span></span> <span data-ttu-id="3a230-110">예를 들어 `JsonResult` 반환은 클라이언트 기본 설정에 관계 없이 JSON 형식의 데이터를 반환하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-110">For example, returning a `JsonResult` will return JSON-formatted data, regardless of client preferences.</span></span> <span data-ttu-id="3a230-111">마찬가지로, `ContentResult` 반환은 일반 텍스트 형식 문자열 데이터를 반환합니다(단순히 문자열을 반환).</span><span class="sxs-lookup"><span data-stu-id="3a230-111">Likewise, returning a `ContentResult` will return plain-text-formatted string data (as will simply returning a string).</span></span>

> [!NOTE]
> <span data-ttu-id="3a230-112">작업은 특정 형식을 반환할 필요가 없습니다. MVC는 모든 개체 반환 값을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-112">An action isn't required to return any particular type; MVC supports any object return value.</span></span> <span data-ttu-id="3a230-113">작업이 `IActionResult` 구현을 반환하고 컨트롤러가 `Controller`에서 상속하는 경우, 개발자는 많은 선택 사항에 해당하는 도우미 메서드를 다양하게 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-113">If an action returns an `IActionResult` implementation and the controller inherits from `Controller`, developers have many helper methods corresponding to many of the choices.</span></span> <span data-ttu-id="3a230-114">`IActionResult` 형식이 아닌 개체를 반환하는 작업의 결과는 적절한 `IOutputFormatter` 구현을 사용하여 직렬화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-114">Results from actions that return objects that are not `IActionResult` types will be serialized using the appropriate `IOutputFormatter` implementation.</span></span>

<span data-ttu-id="3a230-115">데이터를 `Controller` 기본 클래스에서 상속하는 컨트롤러의 특정 형식으로 반환하려면 기본 제공 도우미 메서드 `Json`을 사용하여 일반 텍스트에 대해 `Content` 및 JSON을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-115">To return data in a specific format from a controller that inherits from the `Controller` base class, use the built-in helper method `Json` to return JSON and `Content` for plain text.</span></span> <span data-ttu-id="3a230-116">작업 메서드는 특정 결과 형식(예: `JsonResult`) 또는 `IActionResult`를 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-116">Your action method should return either the specific result type (for instance, `JsonResult`) or `IActionResult`.</span></span>

<span data-ttu-id="3a230-117">JSON 형식의 데이터 반환:</span><span class="sxs-lookup"><span data-stu-id="3a230-117">Returning JSON-formatted data:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

<span data-ttu-id="3a230-118">이 작업에서 샘플 응답:</span><span class="sxs-lookup"><span data-stu-id="3a230-118">Sample response from this action:</span></span>

![응답의 콘텐츠 형식이 application/json임이 표시된 Microsoft Edge에 있는 개발자 도구의 네트워크 탭](formatting/_static/json-response.png)

<span data-ttu-id="3a230-120">응답의 콘텐츠 형식은 `application/json`이며, 네트워크 요청 목록과 Response 헤더 섹션에 모두 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-120">Note that the content type of the response is `application/json`, shown both in the list of network requests and in the Response Headers section.</span></span> <span data-ttu-id="3a230-121">또한 Request 헤더 섹션의 Accept 헤더에서 브라우저(이 경우 Microsoft Edge)에 의해 제공되는 옵션의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-121">Also note the list of options presented by the browser (in this case, Microsoft Edge) in the Accept header in the Request Headers section.</span></span> <span data-ttu-id="3a230-122">현재 기술은 이 헤더를 무시합니다. 이를 따르는 데 관해서는 아래에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-122">The current technique is ignoring this header; obeying it is discussed below.</span></span>

<span data-ttu-id="3a230-123">서식 있는 일반 텍스트 데이터를 반환하려면 `ContentResult` 및 `Content` 도우미를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-123">To return plain text formatted data, use `ContentResult` and the `Content` helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

<span data-ttu-id="3a230-124">이 작업에서 응답:</span><span class="sxs-lookup"><span data-stu-id="3a230-124">A response from this action:</span></span>

![응답의 콘텐츠 형식이 text/plain임이 표시된 Microsoft Edge에 있는 개발자 도구의 네트워크 탭](formatting/_static/text-response.png)

<span data-ttu-id="3a230-126">이 경우는 반환된 `Content-Type`은 `text/plain`입니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-126">Note in this case the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="3a230-127">또한 문자열 응답 형식을 사용하여 이것과 동일한 동작을 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-127">You can also achieve this same behavior using just a string response type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> <span data-ttu-id="3a230-128">여러 반환 형식 또는 옵션이 포함된 특수 작업의 경우(예: 수행되는 작업의 결과를 기반으로 하는 다른 HTTP 상태 코드) 반환 형식으로 `IActionResult`를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-128">For non-trivial actions with multiple return types or options (for example, different HTTP status codes based on the result of operations performed), prefer `IActionResult` as the return type.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="3a230-129">콘텐츠 협상</span><span class="sxs-lookup"><span data-stu-id="3a230-129">Content Negotiation</span></span>

<span data-ttu-id="3a230-130">콘텐츠 협상(줄여서 *conneg*)은 클라이언트가 [Accept 헤더](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)를 지정하는 경우에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-130">Content negotiation (*conneg* for short) occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="3a230-131">ASP.NET Core MVC에서 사용하는 기본 형식은 JSON입니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-131">The default format used by ASP.NET Core MVC is JSON.</span></span> <span data-ttu-id="3a230-132">콘텐츠 협상은 `ObjectResult`에 의해 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-132">Content negotiation is implemented by `ObjectResult`.</span></span> <span data-ttu-id="3a230-133">또한 도우미 메서드에서 반환된 상태 코드별 작업 결과 상태 코드에 기본적으로 제공됩니다(모두 `ObjectResult` 기반).</span><span class="sxs-lookup"><span data-stu-id="3a230-133">It's also built into the status code specific action results returned from the helper methods (which are all based on `ObjectResult`).</span></span> <span data-ttu-id="3a230-134">또한 모델 형식(데이터 전송 형식으로 정의된 클래스)을 반환할 수 있으며, 프레임워크는 사용자를 위해 자동으로 `ObjectResult`에 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-134">You can also return a model type (a class you've defined as your data transfer type) and the framework will automatically wrap it in an `ObjectResult` for you.</span></span>

<span data-ttu-id="3a230-135">다음 작업 메서드는 `Ok` 및 `NotFound` 도우미 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-135">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

<span data-ttu-id="3a230-136">다른 형식이 요청되지 않는 한 JSON 형식 응답이 반환되고 서버는 요청된 형식을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-136">A JSON-formatted response will be returned unless another format was requested and the server can return the requested format.</span></span> <span data-ttu-id="3a230-137">[Fiddler](https://www.telerik.com/fiddler)와 같은 도구를 사용하여 Accept 헤더가 포함된 요청을 만들고 다른 형식을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-137">You can use a tool like [Fiddler](https://www.telerik.com/fiddler) to create a request that includes an Accept header and specify another format.</span></span> <span data-ttu-id="3a230-138">해당 경우에서 서버에 요청된 형식으로 응답을 생성할 수 있는 *포맷터*가 있으면 결과는 클라이언트 기본 형식으로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-138">In that case, if the server has a *formatter* that can produce a response in the requested format, the result will be returned in the client-preferred format.</span></span>

![application/xml의 Accept 헤더 값과 함께 수동으로 만든 GET 요청을 보여 주는 Fiddler 콘솔](formatting/_static/fiddler-composer.png)

<span data-ttu-id="3a230-140">위의 스크린 샷에서 `Accept: application/xml`을 지정하며 요청을 생성하는 데 Fiddler 작성기가 사용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-140">In the above screenshot, the Fiddler Composer has been used to generate a request, specifying `Accept: application/xml`.</span></span> <span data-ttu-id="3a230-141">기본적으로 ASP.NET Core MVC는 JSON만 지원하므로 다른 형식이 지정된 경우라도 결과는 여전히 JSON 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-141">By default, ASP.NET Core MVC only supports JSON, so even when another format is specified, the result returned is still JSON-formatted.</span></span> <span data-ttu-id="3a230-142">다음 섹션에서는 추가적인 포맷터를 추가하는 방법이 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-142">You'll see how to add additional formatters in the next section.</span></span>

<span data-ttu-id="3a230-143">컨트롤러 작업은 POCO(Plain Old CLR Objects)를 반환할 수 있습니다. 그런 경우 ASP.NET Core MVC는 사용자를 위해 개체를 래핑하는 `ObjectResult`를 자동으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-143">Controller actions can return POCOs (Plain Old CLR Objects), in which case ASP.NET Core MVC automatically creates an `ObjectResult` for you that wraps the object.</span></span> <span data-ttu-id="3a230-144">클라이언트는 서식 있는 직렬화된 개체를 가져옵니다. (JSON 형식이 기본값이며, XML 또는 다른 형식을 구성할 수 있습니다.)</span><span class="sxs-lookup"><span data-stu-id="3a230-144">The client will get the formatted serialized object (JSON format is the default; you can configure XML or other formats).</span></span> <span data-ttu-id="3a230-145">반환되는 개체가 `null`이면, 프레임워크는 `204 No Content` 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-145">If the object being returned is `null`, then the framework will return a `204 No Content` response.</span></span>

<span data-ttu-id="3a230-146">개체 형식 반환하기:</span><span class="sxs-lookup"><span data-stu-id="3a230-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

<span data-ttu-id="3a230-147">이 샘플에서는 유효한 작성자 별칭에 대한 요청이 작성자의 데이터로 200 정상 응답을 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-147">In the sample, a request for a valid author alias will receive a 200 OK response with the author's data.</span></span> <span data-ttu-id="3a230-148">잘못된 별칭에 대한 요청은 204 콘텐츠 없음 응답을 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-148">A request for an invalid alias will receive a 204 No Content response.</span></span> <span data-ttu-id="3a230-149">XML 및 JSON 형식의 응답을 보여 주는 스크린샷은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-149">Screenshots showing the response in XML and JSON formats are shown below.</span></span>

### <a name="content-negotiation-process"></a><span data-ttu-id="3a230-150">콘텐츠 협상 프로세스</span><span class="sxs-lookup"><span data-stu-id="3a230-150">Content Negotiation Process</span></span>

<span data-ttu-id="3a230-151">콘텐츠 *협상*은 `Accept` 헤더가 요청에 나타나는 경우에만 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-151">Content *negotiation* only takes place if an `Accept` header appears in the request.</span></span> <span data-ttu-id="3a230-152">요청에 accept 헤더가 포함되어 있는 경우 프레임워크는 기본 설정 순서대로 accept 헤더의 미디어 유형을 열거하고, accept 헤더에 의해 지정된 형식 중 하나로 응답을 생성할 수 있는 포맷터를 찾으려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-152">When a request contains an accept header, the framework will enumerate the media types in the accept header in preference order and will try to find a formatter that can produce a response in one of the formats specified by the accept header.</span></span> <span data-ttu-id="3a230-153">클라이언트의 요청을 충족할 수 있는 포맷터가 없는 경우 프레임워크는 응답을 생성할 수 있는 첫 번째 포맷터를 찾으려고 시도합니다(개발자가 대신 406 허용되지 않음을 반환하도록 `MvcOptions`에서 옵션을 구성하지 않을 경우).</span><span class="sxs-lookup"><span data-stu-id="3a230-153">In case no formatter is found that can satisfy the client's request, the framework will try to find the first formatter that can produce a response (unless the developer has configured the option on `MvcOptions` to return 406 Not Acceptable instead).</span></span> <span data-ttu-id="3a230-154">이 요청은 XML을 지정하나, XML 포맷터가 구성되지 않은 경우에는 JSON 포맷터가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-154">If the request specifies XML, but the XML formatter has not been configured, then the JSON formatter will be used.</span></span> <span data-ttu-id="3a230-155">더 일반적으로 요청된 형식을 제공할 수 있는 포맷터가 구성되지 않은 경우에는 개체의 서식을 지정할 수 있는 첫 번째 포맷터가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-155">More generally, if no formatter is configured that can provide the requested format, then the first formatter that can format the object is used.</span></span> <span data-ttu-id="3a230-156">헤더가 없으면 반환될 개체를 처리할 수 있는 첫 번째 포맷터가 응답을 직렬화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-156">If no header is given, the first formatter that can handle the object to be returned will be used to serialize the response.</span></span> <span data-ttu-id="3a230-157">이 경우 모든 협상이 발생하지 않으므로 서버에서 어떤 형식이 사용될지를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-157">In this case, there isn't any negotiation taking place - the server is determining what format it will use.</span></span>

> [!NOTE]
> <span data-ttu-id="3a230-158">Accept 헤더에 `*/*`가 포함되는 경우, `RespectBrowserAcceptHeader`가 `MvcOptions`에서 true로 설정되지 않으면 헤더는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-158">If the Accept header contains `*/*`, the Header will be ignored unless `RespectBrowserAcceptHeader` is set to true on `MvcOptions`.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="3a230-159">브라우저 및 콘텐츠 협상</span><span class="sxs-lookup"><span data-stu-id="3a230-159">Browsers and Content Negotiation</span></span>

<span data-ttu-id="3a230-160">일반적인 API 클라이언트와 달리 웹 브라우저는 와일드카드를 비롯한 다양한 형식 범위를 포함하는 `Accept` 헤더를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-160">Unlike typical API clients, web browsers tend to supply `Accept` headers that include a wide array of formats, including wildcards.</span></span> <span data-ttu-id="3a230-161">기본적으로 프레임워크가 브라우저에서 오는 요청을 감지하면 `Accept` 헤더를 무시하고, 대신에 콘텐츠를 애플리케이션에 구성된 기본 형식으로 반환합니다(달리 구성되지 않은 경우 JSON).</span><span class="sxs-lookup"><span data-stu-id="3a230-161">By default, when the framework detects that the request is coming from a browser, it will ignore the `Accept` header and instead return the content in the application's configured default format (JSON unless otherwise configured).</span></span> <span data-ttu-id="3a230-162">이는 API를 통해 다양한 브라우저를 사용하는 경우에 더 일관된 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-162">This provides a more consistent experience when using different browsers to consume APIs.</span></span>

<span data-ttu-id="3a230-163">애플리케이션 적용 브라우저 accept 헤더를 선호하는 경우, *Startup.cs*의 `ConfigureServices` 메서드에서 `RespectBrowserAcceptHeader`를 `true`로 설정하여 MVC 구성의 일부로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-163">If you would prefer your application honor browser accept headers, you can configure this as part of MVC's configuration by setting `RespectBrowserAcceptHeader` to `true` in the `ConfigureServices` method in *Startup.cs*.</span></span>

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
});
```

## <a name="configuring-formatters"></a><span data-ttu-id="3a230-164">포맷터 구성</span><span class="sxs-lookup"><span data-stu-id="3a230-164">Configuring Formatters</span></span>

<span data-ttu-id="3a230-165">애플리케이션이 기본 JSON 외에 추가 형식을 지원해야 하는 경우 NuGet 패키지를 추가하고 이를 지원하도록 MVC를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-165">If your application needs to support additional formats beyond the default of JSON, you can add NuGet packages and configure MVC to support them.</span></span> <span data-ttu-id="3a230-166">입력 및 출력에 대한 개별 포맷터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-166">There are separate formatters for input and output.</span></span> <span data-ttu-id="3a230-167">입력 포맷터는 [모델 바인딩](xref:mvc/models/model-binding)에서 사용되며, 출력 포맷터는 응답 형식을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-167">Input formatters are used by [Model Binding](xref:mvc/models/model-binding); output formatters are used to format responses.</span></span> <span data-ttu-id="3a230-168">[사용자 지정 포맷터](xref:web-api/advanced/custom-formatters)를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-168">You can also configure [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="3a230-169">System.Text.Json 기반 포맷터 구성</span><span class="sxs-lookup"><span data-stu-id="3a230-169">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="3a230-170">`System.Text.Json` 기반 포맷터의 기능은 `Microsoft.AspNetCore.Mvc.MvcOptions.SerializerOptions`를 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-170">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcOptions.SerializerOptions`.</span></span>

```csharp
services.AddMvc(options =>
{
    options.SerializerOptions.WriterSettings.Indented = true;
});
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="3a230-171">Newtonsoft.Json 기반 JSON 형식 지원 추가</span><span class="sxs-lookup"><span data-stu-id="3a230-171">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="3a230-172">ASP.NET Core 3.0 이전의 MVC는 `Newtonsoft.Json` 패키지를 사용하여 구현된 JSON 포맷터를 기본적으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-172">Prior to ASP.NET Core 3.0, MVC defaulted to using JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="3a230-173">ASP.NET Core 3.0 이후의 기본 JSON 포맷터는 `System.Text.Json`을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-173">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="3a230-174">`Newtonsoft.Json` 기반 포맷터 및 기능에 대한 지원은 [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 패키지를 설치하고 `Startup.ConfigureServices`에서 구성하여 이용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-174">Support for `Newtonsoft.Json`-based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddMvc()
    .AddNewtonsoftJson();
```

<span data-ttu-id="3a230-175">일부 기능은 `System.Text.Json` 기반 포맷터와 잘 호환되지 않고 ASP.NET Core 3.0 릴리스용 `Newtonsoft.Json` 기반 포맷터에 대한 참조를 필요로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-175">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters for the ASP.NET Core 3.0 release.</span></span> <span data-ttu-id="3a230-176">ASP.NET Core 3.0 이상의 앱이 다음과 같은 경우에는 `Newtonsoft.Json` 기반 포맷터를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-176">Continue using the `Newtonsoft.Json`-based formatters if your ASP.NET Core 3.0 or later app:</span></span>

* <span data-ttu-id="3a230-177">`Newtonsoft.Json` 특성(예: `[JsonProperty]` 또는 `[JsonIgnore]`)을 사용하거나 serialization 설정을 사용자 지정하거나 `Newtonsoft.Json`이 제공하는 기능을 사용하는 경우.</span><span class="sxs-lookup"><span data-stu-id="3a230-177">Uses `Newtonsoft.Json` attributes (for example, `[JsonProperty]` or `[JsonIgnore]`), customizes the serialization settings, or relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="3a230-178">`Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`를 구성하는 경우.</span><span class="sxs-lookup"><span data-stu-id="3a230-178">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="3a230-179">ASP.NET Core 3.0 이전의 `JsonResult.SerializerSettings`는 `Newtonsoft.Json` 고유의 `JsonSerializerSettings`의 인스턴스를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-179">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="3a230-180">[OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) 문서를 생성하는 경우.</span><span class="sxs-lookup"><span data-stu-id="3a230-180">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

::: moniker-end

### <a name="add-xml-format-support"></a><span data-ttu-id="3a230-181">XML 형식 지원 추가</span><span class="sxs-lookup"><span data-stu-id="3a230-181">Add XML format support</span></span>

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="3a230-182">ASP.NET Core 2.2 이상에서 XML 서식 지정 지원을 추가하려면 [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-182">To add XML formatting support in ASP.NET Core 2.2 or earlier, install the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

::: moniker-end

<span data-ttu-id="3a230-183">`System.Xml.Serialization.XmlSerializer`를 사용하여 구현된 XML 포맷터는 `Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>를 호출하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-183">XML formatters implemented using `System.Xml.Serialization.XmlSerializer` can be configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

<span data-ttu-id="3a230-184">또는 `System.Runtime.Serialization.DataContractSerializer`를 사용하여 구현된 XML 포맷터는 `Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlDataContractSerializerFormatters*>를 호출하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-184">Alternatively, XML formatters implemented using `System.Runtime.Serialization.DataContractSerializer` can be configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlDataContractSerializerFormatters*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddXmlDataContractSerializerFormatters();
```

<span data-ttu-id="3a230-185">XML 서식 지정에 대한 지원을 추가하면 컨트롤러 메서드는 요청의 `Accept` 헤더에 따라 적절한 형식을 반환해야 합니다. 이 Fiddler 예제에서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-185">Once you've added support for XML formatting, your controller methods should return the appropriate format based on the request's `Accept` header, as this Fiddler example demonstrates:</span></span>

![Fiddler 콘솔: 요청에 대한 Raw 탭에 Accept 헤더 값이 application/xml임이 표시됩니다.](formatting/_static/xml-response.png)

<span data-ttu-id="3a230-188">Inspectors 탭에서 `Accept: application/xml` 헤더 집합으로 Raw GET 요청이 만들어진 것을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-188">You can see in the Inspectors tab that the Raw GET request was made with an `Accept: application/xml` header set.</span></span> <span data-ttu-id="3a230-189">응답 창에 `Content-Type: application/xml` 헤더 및 XML로 직렬화된 `Author` 개체가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-189">The response pane shows the `Content-Type: application/xml` header, and the `Author` object has been serialized to XML.</span></span>

<span data-ttu-id="3a230-190">`Accept` 헤더에서 `application/json`을 지정하도록 요청을 수정하려면 Composer 탭을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-190">Use the Composer tab to modify the request to specify `application/json` in the `Accept` header.</span></span> <span data-ttu-id="3a230-191">요청을 실행하면 응답이 JSON으로 형식이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-191">Execute the request, and the response will be formatted as JSON:</span></span>

![Fiddler 콘솔: 요청에 대한 Raw 탭에 Accept 헤더 값이 application/json임이 표시됩니다.](formatting/_static/json-response-fiddler.png)

<span data-ttu-id="3a230-194">이 스크린 샷에는 요청이 `Accept: application/json`의 헤더를 설정하고, 응답이 해당 `Content-Type`과 동일하게 지정하는 것을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-194">In this screenshot, you can see the request sets a header of `Accept: application/json` and the response specifies the same as its `Content-Type`.</span></span> <span data-ttu-id="3a230-195">`Author` 개체가 JSON 형식으로 응답 본문에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-195">The `Author` object is shown in the body of the response, in JSON format.</span></span>

### <a name="forcing-a-particular-format"></a><span data-ttu-id="3a230-196">특정 형식 적용</span><span class="sxs-lookup"><span data-stu-id="3a230-196">Forcing a Particular Format</span></span>

<span data-ttu-id="3a230-197">특정 작업에 대한 응답 형식을 제한하려는 경우 `[Produces]` 필터를 적용하여 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-197">If you would like to restrict the response formats for a specific action you can, you can apply the `[Produces]` filter.</span></span> <span data-ttu-id="3a230-198">`[Produces]` 필터는 특정 작업(또는 컨트롤러)에 대한 응답 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-198">The `[Produces]` filter specifies the response formats for a specific action (or controller).</span></span> <span data-ttu-id="3a230-199">대부분 [필터](xref:mvc/controllers/filters)와 마찬가지로, 작업, 컨트롤러 또는 전역 범위에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-199">Like most [Filters](xref:mvc/controllers/filters), this can be applied at the action, controller, or global scope.</span></span>

```csharp
[Produces("application/json")]
public class AuthorsController
```

<span data-ttu-id="3a230-200">`[Produces]` 필터는 다른 포맷터가 애플리케이션에 구성되고 클라이언트가 다른 사용 가능한 형식을 요청하는 `Accept` 헤더를 제공하는 경우에도 JSON 형식의 응답을 반환하도록 `AuthorsController` 내에서 모든 작업을 강제로 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-200">The `[Produces]` filter will force all actions within the `AuthorsController` to return JSON-formatted responses, even if other formatters were configured for the application and the client provided an `Accept` header requesting a different, available format.</span></span> <span data-ttu-id="3a230-201">필터를 전역에서 적용하는 방법을 비롯한 자세한 내용은 [필터](xref:mvc/controllers/filters)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a230-201">See [Filters](xref:mvc/controllers/filters) to learn more, including how to apply filters globally.</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="3a230-202">특수한 사례 포맷터</span><span class="sxs-lookup"><span data-stu-id="3a230-202">Special Case Formatters</span></span>

<span data-ttu-id="3a230-203">일부 특수한 경우 기본 제공 포맷터를 사용하여 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-203">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="3a230-204">기본적으로 `string` 반환 형식은 *text/plain*(`Accept` 헤더를 통해 요청된 경우 *text/html*)으로 형식이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-204">By default, `string` return types will be formatted as *text/plain* (*text/html* if requested via `Accept` header).</span></span> <span data-ttu-id="3a230-205">이 동작은 `TextOutputFormatter`를 제거하여 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-205">This behavior can be removed by removing the `TextOutputFormatter`.</span></span> <span data-ttu-id="3a230-206">포맷터는 *Startup.cs*의 `Configure` 메서드에서 제거합니다(아래 참조).</span><span class="sxs-lookup"><span data-stu-id="3a230-206">You remove formatters in the `Configure` method in *Startup.cs* (shown below).</span></span> <span data-ttu-id="3a230-207">모델 개체 반환 형식이 있는 작업은 `null`을 반환하는 경우 204 콘텐츠 없음 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-207">Actions that have a model object return type will return a 204 No Content response when returning `null`.</span></span> <span data-ttu-id="3a230-208">이 동작은 `HttpNoContentOutputFormatter`를 제거하여 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-208">This behavior can be removed by removing the `HttpNoContentOutputFormatter`.</span></span> <span data-ttu-id="3a230-209">다음 코드를 `TextOutputFormatter` 및 `HttpNoContentOutputFormatter`를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-209">The following code removes the `TextOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

<span data-ttu-id="3a230-210">`TextOutputFormatter`가 없으면 `string`은 예를 들어 406 승인 금지를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-210">Without the `TextOutputFormatter`, `string` return types return 406 Not Acceptable, for example.</span></span> <span data-ttu-id="3a230-211">XML 포맷터가 있는 경우 `TextOutputFormatter`가 제거되면 `string` 반환 형식의 서식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-211">Note that if an XML formatter exists, it will format `string` return types if the `TextOutputFormatter` is removed.</span></span>

<span data-ttu-id="3a230-212">`HttpNoContentOutputFormatter`가 없으면 null 개체는 구성된 포맷터를 사용하여 서식이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-212">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="3a230-213">예를 들어 JSON 포맷터는 `null`의 본문으로 응답을 반환하는 반면, XML 포맷터는 특성 `xsi:nil="true"` 집합으로 빈 XML 요소를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-213">For example, the JSON formatter will simply return a response with a body of `null`, while the XML formatter will return an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="3a230-214">응답 형식 URL 매핑</span><span class="sxs-lookup"><span data-stu-id="3a230-214">Response Format URL Mappings</span></span>

<span data-ttu-id="3a230-215">클라이언트는 쿼리 문자열 또는 일부 경로와 같은 URL의 일부로, 또는 .xml이나 .json과 같은 형식별 파일 확장명을 사용하여 특정 형식을 요청할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-215">Clients can request a particular format as part of the URL, such as in the query string or part of the path, or by using a format-specific file extension such as .xml or .json.</span></span> <span data-ttu-id="3a230-216">요청 경로의 매핑은 API가 사용하는 경로에 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-216">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="3a230-217">예:</span><span class="sxs-lookup"><span data-stu-id="3a230-217">For example:</span></span>

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

<span data-ttu-id="3a230-218">이 경로를 사용하면 요청된 형식을 선택적 파일 확장명으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-218">This route would allow the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="3a230-219">`[FormatFilter]` 특성은 `RouteData`에서 형식 값의 존재 여부를 검사하며 응답이 생성될 때 응답 형식을 적절한 포맷터에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="3a230-219">The `[FormatFilter]` attribute checks for the existence of the format value in the `RouteData` and will map the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="3a230-220">경로</span><span class="sxs-lookup"><span data-stu-id="3a230-220">Route</span></span>            |             <span data-ttu-id="3a230-221">포맷터</span><span class="sxs-lookup"><span data-stu-id="3a230-221">Formatter</span></span>              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    <span data-ttu-id="3a230-222">기본 출력 포맷터</span><span class="sxs-lookup"><span data-stu-id="3a230-222">The default output formatter</span></span>    |
| `/products/GetById/5.json` | <span data-ttu-id="3a230-223">JSON 포맷터(구성된 경우)</span><span class="sxs-lookup"><span data-stu-id="3a230-223">The JSON formatter (if configured)</span></span> |
| `/products/GetById/5.xml`  | <span data-ttu-id="3a230-224">XML 포맷터(구성된 경우)</span><span class="sxs-lookup"><span data-stu-id="3a230-224">The XML formatter (if configured)</span></span>  |
