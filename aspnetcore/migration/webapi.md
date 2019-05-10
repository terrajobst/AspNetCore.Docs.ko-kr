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
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="27dc3-103">ASP.NET Core에 ASP.NET Web API에서 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="27dc3-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="27dc3-104">하 여 [Scott Addie](https://twitter.com/scott_addie) 고 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="27dc3-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="27dc3-105">ASP.NET 4.x Web API에는 광범위 한 브라우저 및 모바일 장치 등의 클라이언트에 도달 하는 HTTP 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="27dc3-106">Asp.net 4.x의 ASP.NET MVC를 통합 하 고 웹 API 앱을 ASP.NET Core MVC로 알려진 더 간단한 프로그래밍 모델에 모델링 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="27dc3-107">이 문서에서는 ASP.NET 4.x Web API에서 ASP.NET Core MVC로 마이그레이션하는 데 필요한 단계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="27dc3-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="27dc3-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="27dc3-109">전제 조건</span><span class="sxs-lookup"><span data-stu-id="27dc3-109">Prerequisites</span></span>

[!INCLUDE [net-core-prereqs-vs-2.2](../includes/net-core-prereqs-vs-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="27dc3-110">ASP.NET 4.x Web API 프로젝트를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="27dc3-111">이 문서에서는 시작 지점으로 다음을 사용 합니다.는 *ProductsApp* 에서 만든 프로젝트 [ASP.NET Web API 2 시작](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="27dc3-112">해당 프로젝트에 간단한 ASP.NET 4.x Web API 프로젝트는 다음과 같이 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="27dc3-113">*Global.asax.cs*를 호출 하려고 `WebApiConfig.Register`:</span><span class="sxs-lookup"><span data-stu-id="27dc3-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="27dc3-114">합니다 `WebApiConfig` 클래스에서 발견 되는 *App_Start* 폴더에 및 `Register` 메서드:</span><span class="sxs-lookup"><span data-stu-id="27dc3-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="27dc3-115">이 클래스를 구성 [특성 라우팅은](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)이지만 프로젝트에 실제로 사용 되는 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="27dc3-116">또한 ASP.NET Web API에서 사용 되는 라우팅 테이블을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="27dc3-117">이 경우 ASP.NET 4.x Web API 예상 형식과 일치 하도록 Url `/api/{controller}/{id}`를 사용 하 여 `{id}` 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="27dc3-118">합니다 *ProductsApp* 프로젝트에 하나의 컨트롤러에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="27dc3-119">컨트롤러에서 상속 `ApiController` 두 작업을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="27dc3-120">합니다 `Product` 사용 되는 모델 `ProductsController` 간단한 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="27dc3-121">다음 섹션에서는 웹 API 프로젝트를 ASP.NET Core MVC로의 마이그레이션을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="27dc3-122">대상 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="27dc3-122">Create destination project</span></span>

<span data-ttu-id="27dc3-123">Visual Studio에서 다음 단계를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="27dc3-124">로 이동 **파일** > **새** > **프로젝트** > **기타 프로젝트 형식**  >  **Visual Studio 솔루션**합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="27dc3-125">선택 **빈 솔루션**, 솔루션 이름을 *WebAPIMigration*합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="27dc3-126">**확인** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-126">Click the **OK** button.</span></span>
* <span data-ttu-id="27dc3-127">기존 항목 추가 *ProductsApp* 프로젝트를 솔루션입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="27dc3-128">새 **ASP.NET Core 웹 응용 프로그램** 프로젝트를 솔루션입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="27dc3-129">선택 합니다 **.NET Core** 대상 프레임 워크 드롭다운 목록에서 선택한 합니다 **API** 프로젝트 템플릿.</span><span class="sxs-lookup"><span data-stu-id="27dc3-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="27dc3-130">프로젝트 이름을 *ProductsCore*를 클릭 합니다 **확인** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="27dc3-131">이제 솔루션에는 두 개의 프로젝트가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-131">The solution now contains two projects.</span></span> <span data-ttu-id="27dc3-132">다음 섹션에서는 마이그레이션에 대해 설명 합니다 *ProductsApp* 프로젝트의 내용을 합니다 *ProductsCore* 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="27dc3-133">구성 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="27dc3-133">Migrate configuration</span></span>

<span data-ttu-id="27dc3-134">ASP.NET Core를 사용 하지는 *App_Start* 폴더 또는 *Global.asax* 파일인 하며 *web.config* 파일 게시 시간에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="27dc3-135">*Startup.cs* 에 대 한 대체가 *Global.asax* 프로젝트 루트에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="27dc3-136">`Startup` 클래스는 모든 앱 시작 작업을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="27dc3-137">자세한 내용은 <xref:fundamentals/startup>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="27dc3-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="27dc3-138">ASP.NET Core mvc에서 기본적으로 포함 된 특성 라우팅은 때 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> 라고 `Startup.Configure`합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="27dc3-139">다음 `UseMvc` 대체를 호출 합니다 *ProductsApp* 프로젝트의 *app_start/webapiconfig.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="27dc3-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="27dc3-140">모델 및 컨트롤러를 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="27dc3-140">Migrate models and controllers</span></span>

<span data-ttu-id="27dc3-141">복사 합니다 *ProductApp* 프로젝트의 컨트롤러 및 사용 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="27dc3-142">아래 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-142">Follow these steps:</span></span>

1. <span data-ttu-id="27dc3-143">복사본 *Controllers/ProductsController.cs* 새 원래 프로젝트에서.</span><span class="sxs-lookup"><span data-stu-id="27dc3-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="27dc3-144">전체 복사 *모델* 원래 프로젝트에서 새 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="27dc3-145">새 프로젝트 이름과 일치 하도록 복사 된 파일의 네임 스페이스 변경 (*ProductsCore*).</span><span class="sxs-lookup"><span data-stu-id="27dc3-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="27dc3-146">조정 된 `using ProductsApp.Models;` 문에서 *ProductsController.cs* 너무 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="27dc3-147">이 시점에서 응용 프로그램 결과 다양 한 컴파일 오류를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="27dc3-148">ASP.NET Core에는 다음 구성 요소가 없는 발생할 오류:</span><span class="sxs-lookup"><span data-stu-id="27dc3-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="27dc3-149">`ApiController` 클래스</span><span class="sxs-lookup"><span data-stu-id="27dc3-149">`ApiController` class</span></span>
* <span data-ttu-id="27dc3-150">`System.Web.Http` 네임스페이스</span><span class="sxs-lookup"><span data-stu-id="27dc3-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="27dc3-151">`IHttpActionResult` 인터페이스</span><span class="sxs-lookup"><span data-stu-id="27dc3-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="27dc3-152">다음과 같이 오류를 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="27dc3-153">변경 `ApiController` 에 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="27dc3-154">추가 `using Microsoft.AspNetCore.Mvc;` 해결 하려면는 `ControllerBase` 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="27dc3-155">`using System.Web.Http;`를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="27dc3-156">변경 된 `GetProduct` 에서 작업의 반환 형식을 `IHttpActionResult` 에 `ActionResult<Product>`입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="27dc3-157">간소화 된 `GetProduct` 작업의 `return` 다음 문을:</span><span class="sxs-lookup"><span data-stu-id="27dc3-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="27dc3-158">라우팅 구성</span><span class="sxs-lookup"><span data-stu-id="27dc3-158">Configure routing</span></span>

<span data-ttu-id="27dc3-159">다음과 같이 라우팅을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="27dc3-160">데코 레이트 된 `ProductsController` 다음 특성을 사용 하 여 클래스:</span><span class="sxs-lookup"><span data-stu-id="27dc3-160">Decorate the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="27dc3-161">위의 [[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 특성이 컨트롤러의 특성 라우팅 패턴을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-161">The preceding [[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="27dc3-162">합니다 [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성을 사용 하면이 컨트롤러에서 요구 하는 모든 작업에 대 한 라우팅 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-162">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="27dc3-163">특성 라우팅은 같은 토큰을 지원 `[controller]` 고 `[action]`입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="27dc3-164">런타임 시 각 토큰이 바뀝니다 컨트롤러 또는 작업의 이름으로 각각 특성이 적용 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="27dc3-165">토큰을 프로젝트에서 매직 문자열의 수를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="27dc3-166">경로 해당 컨트롤러를 사용 하 여 동기화 된 상태로 유지 하 고 자동 리팩터링 이름 바꾸기 때 작업을 적용할 때에 토큰을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="27dc3-167">ASP.NET Core 2.2을 프로젝트의 호환성 모드를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="27dc3-168">이전 변경:</span><span class="sxs-lookup"><span data-stu-id="27dc3-168">The preceding change:</span></span>

    * <span data-ttu-id="27dc3-169">사용 해야는 `[ApiController]` 컨트롤러 수준 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="27dc3-170">잠재적으로 중요 ASP.NET Core 2.2에 정의 된 동작으로 옵트인 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="27dc3-171">HTTP Get 요청을 사용 하도록 설정 된 `ProductController` 작업:</span><span class="sxs-lookup"><span data-stu-id="27dc3-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="27dc3-172">적용 된 [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성을 `GetAllProducts` 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-172">Apply the [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="27dc3-173">적용 된 `[HttpGet("{id}")]` 특성을 `GetProduct` 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="27dc3-174">위의 변경 후 사용 되지 않는 제거 `using` 문 *ProductsController.cs* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="27dc3-175">마이그레이션된 프로젝트를 실행 하 고 이동 `/api/products`합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="27dc3-176">세 가지 제품의 전체 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-176">A full list of three products appears.</span></span> <span data-ttu-id="27dc3-177">`/api/products/1`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="27dc3-178">첫 번째 제품에는 다음이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="27dc3-179">호환성 shim</span><span class="sxs-lookup"><span data-stu-id="27dc3-179">Compatibility shim</span></span>

<span data-ttu-id="27dc3-180">합니다 [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) 라이브러리는 ASP.NET Core에 ASP.NET 4.x Web API 프로젝트를 이동 하려면 호환성 shim을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="27dc3-181">호환성 shim을 지원할 수 있는 ASP.NET 4.x Web API 2에서에서 규칙의 ASP.NET Core를 확장 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="27dc3-182">이 문서에서는 이전에 이식 샘플은 기본 충분히 호환성 shim 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="27dc3-183">대규모 프로젝트 호환성 shim을 사용 하 여 가능 일시적으로 ASP.NET Core와 ASP.NET 4.x Web API 2 사이의 API 차이 조정 하는 데 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="27dc3-184">Web API 호환성 shim 마이그레이션 대규모 ASP.NET 4.x Web API 프로젝트를 ASP.NET Core를 지원 하기 위해 임시 수단으로 사용할 것입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="27dc3-185">시간이 지남에 따라 프로젝트 호환성 shim에 의존 하지 않고 ASP.NET Core 패턴을 사용 하도록 업데이트 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="27dc3-186">에 포함 된 호환성 기능 `Microsoft.AspNetCore.Mvc.WebApiCompatShim` 포함:</span><span class="sxs-lookup"><span data-stu-id="27dc3-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="27dc3-187">추가 `ApiController` 을 입력 하 여 컨트롤러의 기본 형식 업데이트할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="27dc3-188">웹 API 스타일 모델 바인딩을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="27dc3-189">ASP.NET Core MVC 모델 바인딩 함수 유사 하 게 하는 asp.net 4.x MVC 5의 경우 기본적으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="27dc3-190">호환성 shim 변경 모델 바인딩 ASP.NET 4.x Web API 2 모델 바인딩 규칙 더 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="27dc3-191">예를 들어, 복합 형식은 자동으로 요청 본문에서 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="27dc3-192">컨트롤러 작업의 형식 매개 변수를 취할 수 있도록 모델 바인딩 확장 `HttpRequestMessage`합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="27dc3-193">형식의 결과 반환할 작업을 허용 메시지 포맷터를 추가 `HttpResponseMessage`합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="27dc3-194">Web API 2 작업 응답에 맞도록 사용 했을 수 있는 추가 응답 메서드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="27dc3-195">`HttpResponseMessage` 생성기:</span><span class="sxs-lookup"><span data-stu-id="27dc3-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="27dc3-196">작업 결과 메서드:</span><span class="sxs-lookup"><span data-stu-id="27dc3-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="27dc3-197">인스턴스를 추가 `IContentNegotiator` 앱의 종속성 주입 (DI) 컨테이너 및 콘텐츠 협상 관련 형식에서 사용할 수 있게 [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="27dc3-198">이러한 형식의 예로 `DefaultContentNegotiator` 고 `MediaTypeFormatter`입니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="27dc3-199">에 호환성 shim을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="27dc3-200">설치 합니다 [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="27dc3-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="27dc3-201">호환성 shim의 서비스를 호출 하 여 앱의 DI 컨테이너를 사용 하 여 등록 `services.AddMvc().AddWebApiConventions()` 에서 `Startup.ConfigureServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="27dc3-202">웹 API 관련 경로 사용 하 여 정의할 `MapWebApiRoute` 에 `IRouteBuilder` 앱의 `IApplicationBuilder.UseMvc` 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="27dc3-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="27dc3-203">추가 자료</span><span class="sxs-lookup"><span data-stu-id="27dc3-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
