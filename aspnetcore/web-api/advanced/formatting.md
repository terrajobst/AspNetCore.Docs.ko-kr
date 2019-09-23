---
title: ASP.NET Core Web API에서 응답 데이터 서식 지정
author: ardalis
description: ASP.NET Core Web API에서 응답 데이터의 서식을 지정하는 방법을 알아봅니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 05/29/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: 8bee4efdae5341ddab5bd3aec278ecfef37f0c08
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082356"
---
<!-- DO NOT EDIT BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12077 MERGES -->
# <a name="format-response-data-in-aspnet-core-web-api"></a>ASP.NET Core Web API에서 응답 데이터 서식 지정

작성자: [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC에는 지정된 형식을 사용하거나 클라이언트 사양에 대한 응답으로 응답 데이터의 서식을 지정하기 위한 기본적인 지원 기능이 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>형식별 작업 결과

일부 작업 결과 형식은 `JsonResult` 및 `ContentResult`와 같이 특정 형식과 관련됩니다. 작업은 항상 특정한 방식으로 형식이 지정된 특정 결과를 반환할 수 있습니다. 예를 들어 `JsonResult` 반환은 클라이언트 기본 설정에 관계 없이 JSON 형식의 데이터를 반환하게 됩니다. 마찬가지로, `ContentResult` 반환은 일반 텍스트 형식 문자열 데이터를 반환합니다(단순히 문자열을 반환).

> [!NOTE]
> 작업은 특정 형식을 반환할 필요가 없습니다. MVC는 모든 개체 반환 값을 지원합니다. 작업이 `IActionResult` 구현을 반환하고 컨트롤러가 `Controller`에서 상속하는 경우, 개발자는 많은 선택 사항에 해당하는 도우미 메서드를 다양하게 선택할 수 있습니다. `IActionResult` 형식이 아닌 개체를 반환하는 작업의 결과는 적절한 `IOutputFormatter` 구현을 사용하여 직렬화됩니다.

데이터를 `Controller` 기본 클래스에서 상속하는 컨트롤러의 특정 형식으로 반환하려면 기본 제공 도우미 메서드 `Json`을 사용하여 일반 텍스트에 대해 `Content` 및 JSON을 반환합니다. 작업 메서드는 특정 결과 형식(예: `JsonResult`) 또는 `IActionResult`를 반환해야 합니다.

JSON 형식의 데이터 반환:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

이 작업에서 샘플 응답:

![응답의 콘텐츠 형식이 application/json임이 표시된 Microsoft Edge에 있는 개발자 도구의 네트워크 탭](formatting/_static/json-response.png)

응답의 콘텐츠 형식은 `application/json`이며, 네트워크 요청 목록과 Response 헤더 섹션에 모두 표시됩니다. 또한 Request 헤더 섹션의 Accept 헤더에서 브라우저(이 경우 Microsoft Edge)에 의해 제공되는 옵션의 목록입니다. 현재 기술은 이 헤더를 무시합니다. 이를 따르는 데 관해서는 아래에 설명되어 있습니다.

서식 있는 일반 텍스트 데이터를 반환하려면 `ContentResult` 및 `Content` 도우미를 사용합니다.

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

이 작업에서 응답:

![응답의 콘텐츠 형식이 text/plain임이 표시된 Microsoft Edge에 있는 개발자 도구의 네트워크 탭](formatting/_static/text-response.png)

이 경우는 반환된 `Content-Type`은 `text/plain`입니다. 또한 문자열 응답 형식을 사용하여 이것과 동일한 동작을 얻을 수 있습니다.

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> 여러 반환 형식 또는 옵션이 포함된 특수 작업의 경우(예: 수행되는 작업의 결과를 기반으로 하는 다른 HTTP 상태 코드) 반환 형식으로 `IActionResult`를 선호합니다.

## <a name="content-negotiation"></a>콘텐츠 협상

콘텐츠 협상(줄여서 *conneg*)은 클라이언트가 [Accept 헤더](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)를 지정하는 경우에 발생합니다. ASP.NET Core MVC에서 사용하는 기본 형식은 JSON입니다. 콘텐츠 협상은 `ObjectResult`에 의해 구현됩니다. 또한 도우미 메서드에서 반환된 상태 코드별 작업 결과 상태 코드에 기본적으로 제공됩니다(모두 `ObjectResult` 기반). 또한 모델 형식(데이터 전송 형식으로 정의된 클래스)을 반환할 수 있으며, 프레임워크는 사용자를 위해 자동으로 `ObjectResult`에 래핑합니다.

다음 작업 메서드는 `Ok` 및 `NotFound` 도우미 메서드를 사용합니다.

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

다른 형식이 요청되지 않는 한 JSON 형식 응답이 반환되고 서버는 요청된 형식을 반환할 수 있습니다. [Fiddler](https://www.telerik.com/fiddler)와 같은 도구를 사용하여 Accept 헤더가 포함된 요청을 만들고 다른 형식을 지정할 수 있습니다. 해당 경우에서 서버에 요청된 형식으로 응답을 생성할 수 있는 *포맷터*가 있으면 결과는 클라이언트 기본 형식으로 반환됩니다.

![application/xml의 Accept 헤더 값과 함께 수동으로 만든 GET 요청을 보여 주는 Fiddler 콘솔](formatting/_static/fiddler-composer.png)

위의 스크린 샷에서 `Accept: application/xml`을 지정하며 요청을 생성하는 데 Fiddler 작성기가 사용되었습니다. 기본적으로 ASP.NET Core MVC는 JSON만 지원하므로 다른 형식이 지정된 경우라도 결과는 여전히 JSON 형식입니다. 다음 섹션에서는 추가적인 포맷터를 추가하는 방법이 나옵니다.

컨트롤러 작업은 POCO(Plain Old CLR Objects)를 반환할 수 있습니다. 그런 경우 ASP.NET Core MVC는 사용자를 위해 개체를 래핑하는 `ObjectResult`를 자동으로 만듭니다. 클라이언트는 서식 있는 직렬화된 개체를 가져옵니다. (JSON 형식이 기본값이며, XML 또는 다른 형식을 구성할 수 있습니다.) 반환되는 개체가 `null`이면, 프레임워크는 `204 No Content` 응답을 반환합니다.

개체 형식 반환하기:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

이 샘플에서는 유효한 작성자 별칭에 대한 요청이 작성자의 데이터로 200 정상 응답을 받게 됩니다. 잘못된 별칭에 대한 요청은 204 콘텐츠 없음 응답을 받게 됩니다. XML 및 JSON 형식의 응답을 보여 주는 스크린샷은 다음과 같습니다.

### <a name="content-negotiation-process"></a>콘텐츠 협상 프로세스

콘텐츠 *협상*은 `Accept` 헤더가 요청에 나타나는 경우에만 발생합니다. 요청에 accept 헤더가 포함되어 있는 경우 프레임워크는 기본 설정 순서대로 accept 헤더의 미디어 유형을 열거하고, accept 헤더에 의해 지정된 형식 중 하나로 응답을 생성할 수 있는 포맷터를 찾으려고 시도합니다. 클라이언트의 요청을 충족할 수 있는 포맷터가 없는 경우 프레임워크는 응답을 생성할 수 있는 첫 번째 포맷터를 찾으려고 시도합니다(개발자가 대신 406 허용되지 않음을 반환하도록 `MvcOptions`에서 옵션을 구성하지 않을 경우). 이 요청은 XML을 지정하나, XML 포맷터가 구성되지 않은 경우에는 JSON 포맷터가 사용됩니다. 더 일반적으로 요청된 형식을 제공할 수 있는 포맷터가 구성되지 않은 경우에는 개체의 서식을 지정할 수 있는 첫 번째 포맷터가 사용됩니다. 헤더가 없으면 반환될 개체를 처리할 수 있는 첫 번째 포맷터가 응답을 직렬화하는 데 사용됩니다. 이 경우 모든 협상이 발생하지 않으므로 서버에서 어떤 형식이 사용될지를 결정합니다.

> [!NOTE]
> Accept 헤더에 `*/*`가 포함되는 경우, `RespectBrowserAcceptHeader`가 `MvcOptions`에서 true로 설정되지 않으면 헤더는 무시됩니다.

### <a name="browsers-and-content-negotiation"></a>브라우저 및 콘텐츠 협상

일반적인 API 클라이언트와 달리 웹 브라우저는 와일드카드를 비롯한 다양한 형식 범위를 포함하는 `Accept` 헤더를 제공합니다. 기본적으로 프레임워크가 브라우저에서 오는 요청을 감지하면 `Accept` 헤더를 무시하고, 대신에 콘텐츠를 애플리케이션에 구성된 기본 형식으로 반환합니다(달리 구성되지 않은 경우 JSON). 이는 API를 통해 다양한 브라우저를 사용하는 경우에 더 일관된 환경을 제공합니다.

애플리케이션 적용 브라우저 accept 헤더를 선호하는 경우, *Startup.cs*의 `ConfigureServices` 메서드에서 `RespectBrowserAcceptHeader`를 `true`로 설정하여 MVC 구성의 일부로 구성할 수 있습니다.

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
});
```

## <a name="configuring-formatters"></a>포맷터 구성

애플리케이션이 기본 JSON 외에 추가 형식을 지원해야 하는 경우 NuGet 패키지를 추가하고 이를 지원하도록 MVC를 구성할 수 있습니다. 입력 및 출력에 대한 개별 포맷터가 있습니다. 입력 포맷터는 [모델 바인딩](xref:mvc/models/model-binding)에서 사용되며, 출력 포맷터는 응답 형식을 지정하는 데 사용됩니다. [사용자 지정 포맷터](xref:web-api/advanced/custom-formatters)를 구성할 수 있습니다.

::: moniker range=">= aspnetcore-3.0"

### <a name="configure-systemtextjson-based-formatters"></a>System.Text.Json 기반 포맷터 구성

`System.Text.Json` 기반 포맷터의 기능은 `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`를 사용하여 구성할 수 있습니다.

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.SerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

`JsonResult`를 사용하여 동작 단위로 출력 serialization 옵션을 구성할 수 있습니다. 예:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Newtonsoft.Json 기반 JSON 형식 지원 추가

ASP.NET Core 3.0 이전의 MVC는 `Newtonsoft.Json` 패키지를 사용하여 구현된 JSON 포맷터를 기본적으로 사용합니다. ASP.NET Core 3.0 이후의 기본 JSON 포맷터는 `System.Text.Json`을 기반으로 합니다. `Newtonsoft.Json` 기반 포맷터 및 기능에 대한 지원은 [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 패키지를 설치하고 `Startup.ConfigureServices`에서 구성하여 이용할 수 있습니다.

```csharp
services.AddControllers()
    .AddNewtonsoftJson();
```

일부 기능은 `System.Text.Json` 기반 포맷터와 잘 호환되지 않고 ASP.NET Core 3.0 릴리스용 `Newtonsoft.Json` 기반 포맷터에 대한 참조를 필요로 할 수 있습니다. ASP.NET Core 3.0 이상의 앱이 다음과 같은 경우에는 `Newtonsoft.Json` 기반 포맷터를 계속 사용할 수 있습니다.

* `Newtonsoft.Json` 특성(예: `[JsonProperty]` 또는 `[JsonIgnore]`)을 사용하거나 serialization 설정을 사용자 지정하거나 `Newtonsoft.Json`이 제공하는 기능을 사용하는 경우.
* `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`를 구성하는 경우. ASP.NET Core 3.0 이전의 `JsonResult.SerializerSettings`는 `Newtonsoft.Json` 고유의 `JsonSerializerSettings`의 인스턴스를 허용합니다.
* [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) 문서를 생성하는 경우.

`Newtonsoft.Json` 기반 포맷터의 기능은 `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`를 사용하여 구성할 수 있습니다.

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefautlContractResolver();

    // Configure a custom converter
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

`JsonResult`를 사용하여 동작 단위로 출력 serialization 옵션을 구성할 수 있습니다. 예:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

### <a name="add-xml-format-support"></a>XML 형식 지원 추가

::: moniker range="<= aspnetcore-2.2"

ASP.NET Core 2.2 이상에서 XML 서식 지정 지원을 추가하려면 [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet 패키지를 설치합니다.

::: moniker-end

`System.Xml.Serialization.XmlSerializer`를 사용하여 구현된 XML 포맷터는 `Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>를 호출하여 구성할 수 있습니다.

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

또는 `System.Runtime.Serialization.DataContractSerializer`를 사용하여 구현된 XML 포맷터는 `Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlDataContractSerializerFormatters*>를 호출하여 구성할 수 있습니다.

```csharp
services.AddMvc()
    .AddXmlDataContractSerializerFormatters();
```

XML 서식 지정에 대한 지원을 추가하면 컨트롤러 메서드는 요청의 `Accept` 헤더에 따라 적절한 형식을 반환해야 합니다. 이 Fiddler 예제에서는 다음과 같습니다.

![Fiddler 콘솔: 요청에 대한 Raw 탭에 Accept 헤더 값이 application/xml임이 표시됩니다. 응답에 대한 Raw 탭에 application/xml의 Content-Type 헤더 값이 표시됩니다.](formatting/_static/xml-response.png)

Inspectors 탭에서 `Accept: application/xml` 헤더 집합으로 Raw GET 요청이 만들어진 것을 확인할 수 있습니다. 응답 창에 `Content-Type: application/xml` 헤더 및 XML로 직렬화된 `Author` 개체가 표시됩니다.

`Accept` 헤더에서 `application/json`을 지정하도록 요청을 수정하려면 Composer 탭을 사용합니다. 요청을 실행하면 응답이 JSON으로 형식이 지정됩니다.

![Fiddler 콘솔: 요청에 대한 Raw 탭에 Accept 헤더 값이 application/json임이 표시됩니다. 응답에 대한 Raw 탭에 application/json의 Content-Type 헤더 값이 표시됩니다.](formatting/_static/json-response-fiddler.png)

이 스크린 샷에는 요청이 `Accept: application/json`의 헤더를 설정하고, 응답이 해당 `Content-Type`과 동일하게 지정하는 것을 확인할 수 있습니다. `Author` 개체가 JSON 형식으로 응답 본문에 표시됩니다.

### <a name="forcing-a-particular-format"></a>특정 형식 적용

특정 작업에 대한 응답 형식을 제한하려는 경우 `[Produces]` 필터를 적용하여 수행할 수 있습니다. `[Produces]` 필터는 특정 작업(또는 컨트롤러)에 대한 응답 형식을 지정합니다. 대부분 [필터](xref:mvc/controllers/filters)와 마찬가지로, 작업, 컨트롤러 또는 전역 범위에 적용할 수 있습니다.

```csharp
[Produces("application/json")]
public class AuthorsController
```

`[Produces]` 필터는 다른 포맷터가 애플리케이션에 구성되고 클라이언트가 다른 사용 가능한 형식을 요청하는 `Accept` 헤더를 제공하는 경우에도 JSON 형식의 응답을 반환하도록 `AuthorsController` 내에서 모든 작업을 강제로 수행합니다. 필터를 전역에서 적용하는 방법을 비롯한 자세한 내용은 [필터](xref:mvc/controllers/filters)를 참조하세요.

### <a name="special-case-formatters"></a>특수한 사례 포맷터

일부 특수한 경우 기본 제공 포맷터를 사용하여 구현됩니다. 기본적으로 `string` 반환 형식은 *text/plain*(`Accept` 헤더를 통해 요청된 경우 *text/html*)으로 형식이 지정됩니다. 이 동작은 `TextOutputFormatter`를 제거하여 삭제할 수 있습니다. 포맷터는 *Startup.cs*의 `Configure` 메서드에서 제거합니다(아래 참조). 모델 개체 반환 형식이 있는 작업은 `null`을 반환하는 경우 204 콘텐츠 없음 응답을 반환합니다. 이 동작은 `HttpNoContentOutputFormatter`를 제거하여 삭제할 수 있습니다. 다음 코드를 `TextOutputFormatter` 및 `HttpNoContentOutputFormatter`를 제거합니다.

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

`TextOutputFormatter`가 없으면 `string`은 예를 들어 406 승인 금지를 반환합니다. XML 포맷터가 있는 경우 `TextOutputFormatter`가 제거되면 `string` 반환 형식의 서식을 지정합니다.

`HttpNoContentOutputFormatter`가 없으면 null 개체는 구성된 포맷터를 사용하여 서식이 지정됩니다. 예를 들어 JSON 포맷터는 `null`의 본문으로 응답을 반환하는 반면, XML 포맷터는 특성 `xsi:nil="true"` 집합으로 빈 XML 요소를 반환합니다.

## <a name="response-format-url-mappings"></a>응답 형식 URL 매핑

클라이언트는 쿼리 문자열 또는 일부 경로와 같은 URL의 일부로, 또는 .xml이나 .json과 같은 형식별 파일 확장명을 사용하여 특정 형식을 요청할 수 있습니다. 요청 경로의 매핑은 API가 사용하는 경로에 지정해야 합니다. 예:

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

이 경로를 사용하면 요청된 형식을 선택적 파일 확장명으로 지정할 수 있습니다. `[FormatFilter]` 특성은 `RouteData`에서 형식 값의 존재 여부를 검사하며 응답이 생성될 때 응답 형식을 적절한 포맷터에 매핑합니다.

|           경로            |             포맷터              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    기본 출력 포맷터    |
| `/products/GetById/5.json` | JSON 포맷터(구성된 경우) |
| `/products/GetById/5.xml`  | XML 포맷터(구성된 경우)  |
