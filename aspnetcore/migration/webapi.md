---
title: ASP.NET Core에 ASP.NET Web API에서 마이그레이션
author: ardalis
description: ASP.NET Core MVC로 웹 API 구현을 ASP.NET 4.x Web API에서 마이그레이션하는 방법에 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/10/2018
uid: migration/webapi
ms.openlocfilehash: ea14b7128582a21b36c70041d59fb638eebdbef0
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895010"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>ASP.NET Core에 ASP.NET Web API에서 마이그레이션

하 여 [Scott Addie](https://twitter.com/scott_addie) 고 [Steve Smith](https://ardalis.com/)

ASP.NET 4.x Web API에는 광범위 한 브라우저 및 모바일 장치 등의 클라이언트에 도달 하는 HTTP 서비스입니다. Asp.net 4.x의 ASP.NET MVC를 통합 하 고 웹 API 앱을 ASP.NET Core MVC로 알려진 더 간단한 프로그래밍 모델에 모델링 합니다. 이 문서에서는 ASP.NET 4.x Web API에서 ASP.NET Core MVC로 마이그레이션하는 데 필요한 단계를 보여 줍니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>전제 조건

[!INCLUDE [net-core-prereqs-vs-2.2](../includes/net-core-prereqs-vs-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>ASP.NET 4.x Web API 프로젝트를 검토 합니다.

이 문서에서는 시작 지점으로 다음을 사용 합니다.는 *ProductsApp* 에서 만든 프로젝트 [ASP.NET Web API 2 시작](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)합니다. 해당 프로젝트에 간단한 ASP.NET 4.x Web API 프로젝트는 다음과 같이 구성 됩니다.

*Global.asax.cs*를 호출 하려고 `WebApiConfig.Register`:

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

합니다 `WebApiConfig` 클래스에서 발견 되는 *App_Start* 폴더에 및 `Register` 메서드:

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

이 클래스를 구성 [특성 라우팅은](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)이지만 프로젝트에 실제로 사용 되는 합니다. 또한 ASP.NET Web API에서 사용 되는 라우팅 테이블을 구성 합니다. 이 경우 ASP.NET 4.x Web API 예상 형식과 일치 하도록 Url `/api/{controller}/{id}`를 사용 하 여 `{id}` 선택 사항입니다.

합니다 *ProductsApp* 프로젝트에 하나의 컨트롤러에 포함 됩니다. 컨트롤러에서 상속 `ApiController` 두 작업을 포함 합니다.

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

합니다 `Product` 사용 되는 모델 `ProductsController` 간단한 클래스입니다.

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

다음 섹션에서는 웹 API 프로젝트를 ASP.NET Core MVC로의 마이그레이션을 보여 줍니다.

## <a name="create-destination-project"></a>대상 프로젝트 만들기

Visual Studio에서 다음 단계를 완료 합니다.

* 로 이동 **파일** > **새** > **프로젝트** > **기타 프로젝트 형식**  >  **Visual Studio 솔루션**합니다. 선택 **빈 솔루션**, 솔루션 이름을 *WebAPIMigration*합니다. **확인** 단추를 클릭합니다.
* 기존 항목 추가 *ProductsApp* 프로젝트를 솔루션입니다.
* 새 **ASP.NET Core 웹 응용 프로그램** 프로젝트를 솔루션입니다. 선택 합니다 **.NET Core** 대상 프레임 워크 드롭다운 목록에서 선택한 합니다 **API** 프로젝트 템플릿. 프로젝트 이름을 *ProductsCore*를 클릭 합니다 **확인** 단추입니다.

이제 솔루션에는 두 개의 프로젝트가 포함 되어 있습니다. 다음 섹션에서는 마이그레이션에 대해 설명 합니다 *ProductsApp* 프로젝트의 내용을 합니다 *ProductsCore* 프로젝트입니다.

## <a name="migrate-configuration"></a>구성 마이그레이션

ASP.NET Core를 사용 하지는 *App_Start* 폴더 또는 *Global.asax* 파일인 하며 *web.config* 파일 게시 시간에 추가 됩니다. *Startup.cs* 에 대 한 대체가 *Global.asax* 프로젝트 루트에 있습니다. `Startup` 클래스는 모든 앱 시작 작업을 처리 합니다. 자세한 내용은 <xref:fundamentals/startup>을 참조하세요.

ASP.NET Core mvc에서 기본적으로 포함 된 특성 라우팅은 때 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> 라고 `Startup.Configure`합니다. 다음 `UseMvc` 대체를 호출 합니다 *ProductsApp* 프로젝트의 *app_start/webapiconfig.cs* 파일:

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>모델 및 컨트롤러를 마이그레이션

복사 합니다 *ProductApp* 프로젝트의 컨트롤러 및 사용 모델입니다. 아래 단계를 수행합니다.

1. 복사본 *Controllers/ProductsController.cs* 새 원래 프로젝트에서.
1. 전체 복사 *모델* 원래 프로젝트에서 새 폴더입니다.
1. 새 프로젝트 이름과 일치 하도록 복사 된 파일의 네임 스페이스 변경 (*ProductsCore*). 조정 된 `using ProductsApp.Models;` 문에서 *ProductsController.cs* 너무 합니다.

이 시점에서 응용 프로그램 결과 다양 한 컴파일 오류를 빌드합니다. ASP.NET Core에는 다음 구성 요소가 없는 발생할 오류:

* `ApiController` 클래스
* `System.Web.Http` 네임스페이스
* `IHttpActionResult` 인터페이스

다음과 같이 오류를 수정 합니다.

1. 변경 `ApiController` 에 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>입니다. 추가 `using Microsoft.AspNetCore.Mvc;` 해결 하려면는 `ControllerBase` 참조 합니다.
1. `using System.Web.Http;`를 삭제합니다.
1. 변경 된 `GetProduct` 에서 작업의 반환 형식을 `IHttpActionResult` 에 `ActionResult<Product>`입니다.

간소화 된 `GetProduct` 작업의 `return` 다음 문을:

```csharp
return product;
```

## <a name="configure-routing"></a>라우팅 구성

다음과 같이 라우팅을 구성 합니다.

1. 데코 레이트 된 `ProductsController` 다음 특성을 사용 하 여 클래스:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    위의 [[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 특성이 컨트롤러의 특성 라우팅 패턴을 구성 합니다. 합니다 [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성을 사용 하면이 컨트롤러에서 요구 하는 모든 작업에 대 한 라우팅 특성입니다.

    특성 라우팅은 같은 토큰을 지원 `[controller]` 고 `[action]`입니다. 런타임 시 각 토큰이 바뀝니다 컨트롤러 또는 작업의 이름으로 각각 특성이 적용 되었습니다. 토큰을 프로젝트에서 매직 문자열의 수를 줄입니다. 경로 해당 컨트롤러를 사용 하 여 동기화 된 상태로 유지 하 고 자동 리팩터링 이름 바꾸기 때 작업을 적용할 때에 토큰을 확인 합니다.
1. ASP.NET Core 2.2을 프로젝트의 호환성 모드를 설정 합니다.

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    이전 변경:

    * 사용 해야는 `[ApiController]` 컨트롤러 수준 특성입니다.
    * 잠재적으로 중요 ASP.NET Core 2.2에 정의 된 동작으로 옵트인 합니다.
1. HTTP Get 요청을 사용 하도록 설정 된 `ProductController` 작업:
    * 적용 된 [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성을 `GetAllProducts` 동작 합니다.
    * 적용 된 `[HttpGet("{id}")]` 특성을 `GetProduct` 동작 합니다.

위의 변경 후 사용 되지 않는 제거 `using` 문 *ProductsController.cs* 파일은 다음과 같습니다.

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

마이그레이션된 프로젝트를 실행 하 고 이동 `/api/products`합니다. 세 가지 제품의 전체 목록이 표시 됩니다. `/api/products/1`으로 이동합니다. 첫 번째 제품에는 다음이 표시 됩니다.

## <a name="compatibility-shim"></a>호환성 shim

합니다 [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) 라이브러리는 ASP.NET Core에 ASP.NET 4.x Web API 프로젝트를 이동 하려면 호환성 shim을 제공 합니다. 호환성 shim을 지원할 수 있는 ASP.NET 4.x Web API 2에서에서 규칙의 ASP.NET Core를 확장 합니다. 이 문서에서는 이전에 이식 샘플은 기본 충분히 호환성 shim 필요 없습니다. 대규모 프로젝트 호환성 shim을 사용 하 여 가능 일시적으로 ASP.NET Core와 ASP.NET 4.x Web API 2 사이의 API 차이 조정 하는 데 유용 합니다.

Web API 호환성 shim 마이그레이션 대규모 ASP.NET 4.x Web API 프로젝트를 ASP.NET Core를 지원 하기 위해 임시 수단으로 사용할 것입니다. 시간이 지남에 따라 프로젝트 호환성 shim에 의존 하지 않고 ASP.NET Core 패턴을 사용 하도록 업데이트 되어야 합니다.

에 포함 된 호환성 기능 `Microsoft.AspNetCore.Mvc.WebApiCompatShim` 포함:

* 추가 `ApiController` 을 입력 하 여 컨트롤러의 기본 형식 업데이트할 필요가 없습니다.
* 웹 API 스타일 모델 바인딩을 활성화합니다. ASP.NET Core MVC 모델 바인딩 함수 유사 하 게 하는 asp.net 4.x MVC 5의 경우 기본적으로 합니다. 호환성 shim 변경 모델 바인딩 ASP.NET 4.x Web API 2 모델 바인딩 규칙 더 유사 합니다. 예를 들어, 복합 형식은 자동으로 요청 본문에서 바인딩됩니다.
* 컨트롤러 작업의 형식 매개 변수를 취할 수 있도록 모델 바인딩 확장 `HttpRequestMessage`합니다.
* 형식의 결과 반환할 작업을 허용 메시지 포맷터를 추가 `HttpResponseMessage`합니다.
* Web API 2 작업 응답에 맞도록 사용 했을 수 있는 추가 응답 메서드를 추가 합니다.
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
* 인스턴스를 추가 `IContentNegotiator` 앱의 종속성 주입 (DI) 컨테이너 및 콘텐츠 협상 관련 형식에서 사용할 수 있게 [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)합니다. 이러한 형식의 예로 `DefaultContentNegotiator` 고 `MediaTypeFormatter`입니다.

에 호환성 shim을 사용 합니다.

1. 설치 합니다 [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 패키지.
1. 호환성 shim의 서비스를 호출 하 여 앱의 DI 컨테이너를 사용 하 여 등록 `services.AddMvc().AddWebApiConventions()` 에서 `Startup.ConfigureServices`합니다.
1. 웹 API 관련 경로 사용 하 여 정의할 `MapWebApiRoute` 에 `IRouteBuilder` 앱의 `IApplicationBuilder.UseMvc` 호출 합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
