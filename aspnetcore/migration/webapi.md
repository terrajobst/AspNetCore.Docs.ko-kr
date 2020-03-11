---
title: ASP.NET Web API에서 ASP.NET Core로 마이그레이션
author: ardalis
description: ASP.NET 4.x Web API에서 ASP.NET Core MVC로 웹 API 구현을 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/webapi
ms.openlocfilehash: 7f61b78c589fc9d01061b50554e5a639e372c3d8
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653007"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>ASP.NET Web API에서 ASP.NET Core로 마이그레이션

[Scott Addie](https://twitter.com/scott_addie) 및 [Steve Smith](https://ardalis.com/)

ASP.NET 4.x Web API는 브라우저 및 모바일 장치를 비롯 한 광범위 한 클라이언트에 도달 하는 HTTP 서비스입니다. ASP.NET Core ASP.NET 4.x의 MVC 및 Web API 앱 모델을 ASP.NET Core MVC 라고 하는 간단한 프로그래밍 모델로 통합 합니다. 이 문서에서는 ASP.NET 4.x Web API에서 ASP.NET Core MVC로 마이그레이션하는 데 필요한 단계를 보여 줍니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>ASP.NET 4.x 웹 API 프로젝트를 검토 합니다.

시작 지점으로이 문서에서는 [ASP.NET Web API 2 시작](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)에서 만든 *ProductsApp* 프로젝트를 사용 합니다. 해당 프로젝트에서 간단한 ASP.NET 4.x 웹 API 프로젝트는 다음과 같이 구성 됩니다.

*Global.asax.cs*에서 `WebApiConfig.Register`를 호출 합니다.

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig` 클래스는 *App_Start* 폴더에 있으며 정적 `Register` 메서드를 포함 합니다.

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

이 클래스는 실제로 프로젝트에서 사용 되지 않지만 [특성 라우팅을](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)구성 합니다. 또한 ASP.NET Web API에서 사용 하는 라우팅 테이블을 구성 합니다. 이 경우 ASP.NET 4.x Web API는 Url이 `/api/{controller}/{id}`형식에 일치 하는 것으로 예상 `{id}` 선택적입니다.

*ProductsApp* 프로젝트는 하나의 컨트롤러를 포함 합니다. 컨트롤러는 `ApiController`에서 상속 되며 다음 두 가지 동작을 포함 합니다.

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

`ProductsController`에서 사용 하는 `Product` 모델은 간단한 클래스입니다.

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

다음 섹션에서는 ASP.NET Core MVC로 웹 API 프로젝트를 마이그레이션하는 방법을 보여 줍니다.

## <a name="create-destination-project"></a>대상 프로젝트 만들기

Visual Studio에서 다음 단계를 완료 합니다.

* **파일** > **새** > **프로젝트** > **Visual Studio 솔루션** > **다른 프로젝트 형식** 으로 이동 합니다. **비어 있는 솔루션**을 선택 하 고 솔루션의 이름을 *WebAPIMigration*로 선택 합니다. **확인** 단추를 클릭합니다.
* 기존 *ProductsApp* 프로젝트를 솔루션에 추가 합니다.
* 새 **ASP.NET Core 웹 응용 프로그램** 프로젝트를 솔루션에 추가 합니다. 드롭다운에서 **.Net Core** 대상 프레임 워크를 선택 하 고 **API** 프로젝트 템플릿을 선택 합니다. 프로젝트의 이름을 *제품 점수*로 매기고 **확인** 단추를 클릭 합니다.

이제 솔루션에는 두 개의 프로젝트가 포함 되어 있습니다. 다음 섹션에서는 *ProductsApp* 프로젝트의 콘텐츠를 *제품 점수* 프로젝트로 마이그레이션하는 방법에 대해 설명 합니다.

## <a name="migrate-configuration"></a>구성 마이그레이션

ASP.NET Core은 *App_Start* 폴더나 *global.asax* 파일을 사용 하지 않으며 web.config 파일은 게시 시간에 추가 *됩니다.* *Startup.cs* 는 *global.asax* 의 대체 항목으로, 프로젝트 루트에 있습니다. `Startup` 클래스는 모든 응용 프로그램 시작 작업을 처리 합니다. 자세한 내용은 <xref:fundamentals/startup>을 참조하세요.

ASP.NET Core MVC에서 특성 라우팅은 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>를 호출할 때 기본적으로 포함 됩니다. 다음 `UseMvc` 호출은 *ProductsApp* 프로젝트의 *App_Start/Webapicon.cs* 파일을 대체 합니다.

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>모델 및 컨트롤러 마이그레이션

*제품 앱* 프로젝트의 컨트롤러 및이 컨트롤러에서 사용 하는 모델을 복사 합니다. 다음 단계를 수행하세요.

1. 원본 프로젝트의 *Controllers/ProductsController* 를 새 프로젝트에 복사 합니다.
1. 원본 프로젝트의 전체 *모델* 폴더를 새 프로젝트에 복사 합니다.
1. 새 프로젝트 이름 (*제품 점수*)과 일치 하도록 복사 된 파일의 네임 스페이스를 변경 합니다. *ProductsController.cs* 의 `using ProductsApp.Models;` 문만 조정 합니다.

이 시점에서 앱을 빌드하면 많은 컴파일 오류가 발생 합니다. 이러한 오류는 다음 구성 요소가 ASP.NET Core에 존재 하지 않기 때문에 발생 합니다.

* `ApiController` 클래스
* `System.Web.Http` 네임스페이스
* `IHttpActionResult` 인터페이스

오류를 다음과 같이 수정 합니다.

1. `ApiController`을 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>으로 변경합니다. `using Microsoft.AspNetCore.Mvc;`를 추가 하 여 `ControllerBase` 참조를 확인 합니다.
1. `using System.Web.Http;`를 삭제합니다.
1. `GetProduct` 작업의 반환 형식을 `IHttpActionResult`에서 `ActionResult<Product>`로 변경 합니다.

`GetProduct` 동작의 `return` 문을 다음과 같이 단순화 합니다.

```csharp
return product;
```

## <a name="configure-routing"></a>라우팅 구성

다음과 같이 라우팅을 구성 합니다.

1. `ProductsController` 클래스를 다음 특성으로 표시 합니다.

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    위의 [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 특성은 컨트롤러의 특성 라우팅 패턴을 구성 합니다. [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성은이 컨트롤러의 모든 동작에 대 한 요구 사항을 라우팅하는 특성을 만듭니다.

    특성 라우팅은 `[controller]` 및 `[action]`와 같은 토큰을 지원 합니다. 런타임에 각 토큰은 특성이 적용 되는 각각의 컨트롤러나 동작의 이름으로 바뀝니다. 토큰은 프로젝트의 매직 문자열 수를 줄입니다. 또한 토큰은 자동 이름 바꾸기 리팩터링 적용 될 때 해당 컨트롤러 및 작업과 동기화 된 상태로 유지 되도록 합니다.
1. 프로젝트의 호환성 모드를 ASP.NET Core 2.2으로 설정 합니다.

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    이전 변경 내용:

    * 는 컨트롤러 수준에서 `[ApiController]` 특성을 사용 하는 데 필요 합니다.
    * ASP.NET Core 2.2에 도입 된 잠재적으로 Opts 수 있습니다.
1. `ProductController` 작업에 대 한 HTTP Get 요청을 사용 하도록 설정 합니다.
    * `GetAllProducts` 작업에 [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성을 적용 합니다.
    * `GetProduct` 작업에 `[HttpGet("{id}")]` 특성을 적용 합니다.

위의 변경 내용 및 사용 하지 않은 `using` 문의 제거 후 *ProductsController.cs* 파일은 다음과 같습니다.

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

마이그레이션된 프로젝트를 실행 하 고 `/api/products`로 이동 합니다. 세 제품의 전체 목록이 표시 됩니다. [https://www.microsoft.com]\(`/api/products/1`) 로 이동합니다. 첫 번째 제품이 표시 됩니다.

## <a name="compatibility-shim"></a>호환성 shim

[AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) 라이브러리는 ASP.NET 4.X 웹 API 프로젝트를 ASP.NET Core로 이동 하는 호환성 shim을 제공 합니다. 호환성 shim은 ASP.NET 4.x Web API 2의 다양 한 규칙을 지원 하도록 ASP.NET Core를 확장 합니다. 이 문서에서 이전에 이식 한 샘플은 호환성 shim이 필요 하지 않은 경우에만 기본입니다. 대규모 프로젝트의 경우 호환성 shim을 사용 하면 ASP.NET Core와 ASP.NET 4.x Web API 2 간의 API 격차를 일시적으로 브리징 하는 데 유용할 수 있습니다.

Web API 호환성 shim은 ASP.NET Core로의 large ASP.NET 4.x 웹 API 프로젝트 마이그레이션을 지원 하기 위한 임시 조치로 사용 됩니다. 시간이 지남에 따라 프로젝트는 호환성 shim을 사용 하는 대신 ASP.NET Core 패턴을 사용 하도록 업데이트 되어야 합니다.

`Microsoft.AspNetCore.Mvc.WebApiCompatShim`에 포함 된 호환성 기능은 다음과 같습니다.

* 컨트롤러의 기본 형식을 업데이트할 필요가 없도록 `ApiController` 형식을 추가 합니다.
* 웹 API 스타일 모델 바인딩을 사용 하도록 설정 합니다. ASP.NET Core MVC 모델 바인딩 함수는 기본적으로 ASP.NET 4.x MVC 5와 유사 합니다. 호환성 shim은 모델 바인딩을 ASP.NET 4.x Web API 2 모델 바인딩 규칙과 더 유사 하 게 변경 합니다. 예를 들어 복합 형식은 요청 본문에서 자동으로 바인딩됩니다.
* 컨트롤러 작업에서 `HttpRequestMessage`형식의 매개 변수를 사용할 수 있도록 모델 바인딩을 확장 합니다.
* `HttpResponseMessage`형식의 결과를 반환 하는 작업을 허용 하는 메시지 포맷터를 추가 합니다.
* 웹 API 2 작업에서 응답을 제공 하는 데 사용할 수 있는 추가 응답 메서드를 추가 합니다.
  * `HttpResponseMessage` 생성기:
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * 작업 결과 메서드:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* `IContentNegotiator` 인스턴스를 앱의 DI (종속성 주입) 컨테이너에 추가 하 고 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)의 콘텐츠 협상 관련 형식을 사용 가능 하 게 설정 합니다. 이러한 형식의 예로는 `DefaultContentNegotiator` 및 `MediaTypeFormatter`가 있습니다.

호환성 shim을 사용 하려면 다음을 수행 합니다.

1. [AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 패키지를 설치 합니다.
1. `Startup.ConfigureServices`에서 `services.AddMvc().AddWebApiConventions()`를 호출 하 여 앱의 DI 컨테이너에 호환성 shim의 서비스를 등록 합니다.
1. 앱의 `IApplicationBuilder.UseMvc` 호출에서 `IRouteBuilder` `MapWebApiRoute`를 사용 하 여 웹 API 관련 경로를 정의 합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
