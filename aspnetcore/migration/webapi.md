---
title: ASP.NET Web API에서 ASP.NET Core로 마이그레이션
author: ardalis
description: ASP.NET 4.x Web API에서 ASP.NET Core MVC로 웹 API 구현을 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/webapi
ms.openlocfilehash: c68cf83f427f53b110075168c6d5e4d021808782
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881139"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="8d913-103">ASP.NET Web API에서 ASP.NET Core로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="8d913-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="8d913-104">[Scott Addie](https://twitter.com/scott_addie) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8d913-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8d913-105">ASP.NET 4.x Web API는 브라우저 및 모바일 장치를 비롯 한 광범위 한 클라이언트에 도달 하는 HTTP 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="8d913-106">ASP.NET Core ASP.NET 4.x의 MVC 및 Web API 앱 모델을 ASP.NET Core MVC 라고 하는 간단한 프로그래밍 모델로 통합 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="8d913-107">이 문서에서는 ASP.NET 4.x Web API에서 ASP.NET Core MVC로 마이그레이션하는 데 필요한 단계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="8d913-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8d913-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8d913-109">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="8d913-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="8d913-110">ASP.NET 4.x 웹 API 프로젝트를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="8d913-111">시작 지점으로이 문서에서는 [ASP.NET Web API 2 시작](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)에서 만든 *ProductsApp* 프로젝트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="8d913-112">해당 프로젝트에서 간단한 ASP.NET 4.x 웹 API 프로젝트는 다음과 같이 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="8d913-113">*Global.asax.cs*에서 `WebApiConfig.Register`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="8d913-114">`WebApiConfig` 클래스는 *App_Start* 폴더에 있으며 정적 `Register` 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="8d913-115">이 클래스는 실제로 프로젝트에서 사용 되지 않지만 [특성 라우팅을](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="8d913-116">또한 ASP.NET Web API에서 사용 하는 라우팅 테이블을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="8d913-117">이 경우 ASP.NET 4.x Web API는 Url이 `/api/{controller}/{id}`형식에 일치 하는 것으로 예상 `{id}` 선택적입니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="8d913-118">*ProductsApp* 프로젝트는 하나의 컨트롤러를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="8d913-119">컨트롤러는 `ApiController`에서 상속 되며 다음 두 가지 동작을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="8d913-120">`ProductsController`에서 사용 하는 `Product` 모델은 간단한 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="8d913-121">다음 섹션에서는 ASP.NET Core MVC로 웹 API 프로젝트를 마이그레이션하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="8d913-122">대상 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="8d913-122">Create destination project</span></span>

<span data-ttu-id="8d913-123">Visual Studio에서 다음 단계를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="8d913-124">**파일** > **새** > **프로젝트** > **Visual Studio 솔루션** > **다른 프로젝트 형식** 으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="8d913-125">**비어 있는 솔루션**을 선택 하 고 솔루션의 이름을 *WebAPIMigration*로 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="8d913-126">**확인** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-126">Click the **OK** button.</span></span>
* <span data-ttu-id="8d913-127">기존 *ProductsApp* 프로젝트를 솔루션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="8d913-128">새 **ASP.NET Core 웹 응용 프로그램** 프로젝트를 솔루션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="8d913-129">드롭다운에서 **.Net Core** 대상 프레임 워크를 선택 하 고 **API** 프로젝트 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="8d913-130">프로젝트의 이름을 *제품 점수*로 매기고 **확인** 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="8d913-131">이제 솔루션에는 두 개의 프로젝트가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-131">The solution now contains two projects.</span></span> <span data-ttu-id="8d913-132">다음 섹션에서는 *ProductsApp* 프로젝트의 콘텐츠를 *제품 점수* 프로젝트로 마이그레이션하는 방법에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="8d913-133">구성 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="8d913-133">Migrate configuration</span></span>

<span data-ttu-id="8d913-134">ASP.NET Core은 *App_Start* 폴더나 *global.asax* 파일을 사용 하지 않으며 web.config 파일은 게시 시간에 추가 *됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8d913-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="8d913-135">*Startup.cs* 는 *global.asax* 의 대체 항목으로, 프로젝트 루트에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="8d913-136">`Startup` 클래스는 모든 응용 프로그램 시작 작업을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="8d913-137">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8d913-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="8d913-138">ASP.NET Core MVC에서 특성 라우팅은 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>를 호출할 때 기본적으로 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="8d913-139">다음 `UseMvc` 호출은 *ProductsApp* 프로젝트의 *App_Start/Webapicon.cs* 파일을 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="8d913-140">모델 및 컨트롤러 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="8d913-140">Migrate models and controllers</span></span>

<span data-ttu-id="8d913-141">*제품 앱* 프로젝트의 컨트롤러 및이 컨트롤러에서 사용 하는 모델을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="8d913-142">다음 단계를 수행하십시오.</span><span class="sxs-lookup"><span data-stu-id="8d913-142">Follow these steps:</span></span>

1. <span data-ttu-id="8d913-143">원본 프로젝트의 *Controllers/ProductsController* 를 새 프로젝트에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="8d913-144">원본 프로젝트의 전체 *모델* 폴더를 새 프로젝트에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="8d913-145">새 프로젝트 이름 (*제품 점수*)과 일치 하도록 복사 된 파일의 네임 스페이스를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="8d913-146">*ProductsController.cs* 의 `using ProductsApp.Models;` 문만 조정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="8d913-147">이 시점에서 앱을 빌드하면 많은 컴파일 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="8d913-148">이러한 오류는 다음 구성 요소가 ASP.NET Core에 존재 하지 않기 때문에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="8d913-149">`ApiController` 클래스</span><span class="sxs-lookup"><span data-stu-id="8d913-149">`ApiController` class</span></span>
* <span data-ttu-id="8d913-150">`System.Web.Http` 네임스페이스</span><span class="sxs-lookup"><span data-stu-id="8d913-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="8d913-151">`IHttpActionResult` 인터페이스</span><span class="sxs-lookup"><span data-stu-id="8d913-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="8d913-152">오류를 다음과 같이 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="8d913-153">변경 `ApiController` 에 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>입니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="8d913-154">`using Microsoft.AspNetCore.Mvc;`를 추가 하 여 `ControllerBase` 참조를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="8d913-155">`using System.Web.Http;`를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="8d913-156">`GetProduct` 작업의 반환 형식을 `IHttpActionResult`에서 `ActionResult<Product>`로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="8d913-157">`GetProduct` 동작의 `return` 문을 다음과 같이 단순화 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="8d913-158">라우팅 구성</span><span class="sxs-lookup"><span data-stu-id="8d913-158">Configure routing</span></span>

<span data-ttu-id="8d913-159">다음과 같이 라우팅을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="8d913-160">`ProductsController` 클래스를 다음 특성으로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-160">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="8d913-161">위의 [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 특성은 컨트롤러의 특성 라우팅 패턴을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-161">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="8d913-162">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성은이 컨트롤러의 모든 동작에 대 한 요구 사항을 라우팅하는 특성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-162">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="8d913-163">특성 라우팅은 `[controller]` 및 `[action]`와 같은 토큰을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="8d913-164">런타임에 각 토큰은 특성이 적용 되는 각각의 컨트롤러나 동작의 이름으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="8d913-165">토큰은 프로젝트의 매직 문자열 수를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="8d913-166">또한 토큰은 자동 이름 바꾸기 리팩터링 적용 될 때 해당 컨트롤러 및 작업과 동기화 된 상태로 유지 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="8d913-167">프로젝트의 호환성 모드를 ASP.NET Core 2.2으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="8d913-168">이전 변경 내용:</span><span class="sxs-lookup"><span data-stu-id="8d913-168">The preceding change:</span></span>

    * <span data-ttu-id="8d913-169">는 컨트롤러 수준에서 `[ApiController]` 특성을 사용 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="8d913-170">ASP.NET Core 2.2에 도입 된 잠재적으로 Opts 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="8d913-171">`ProductController` 작업에 대 한 HTTP Get 요청을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="8d913-172">`GetAllProducts` 작업에 [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-172">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="8d913-173">`GetProduct` 작업에 `[HttpGet("{id}")]` 특성을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="8d913-174">위의 변경 내용 및 사용 하지 않은 `using` 문의 제거 후 *ProductsController.cs* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="8d913-175">마이그레이션된 프로젝트를 실행 하 고 `/api/products`로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="8d913-176">세 제품의 전체 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-176">A full list of three products appears.</span></span> <span data-ttu-id="8d913-177">`/api/products/1`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="8d913-178">첫 번째 제품이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="8d913-179">호환성 shim</span><span class="sxs-lookup"><span data-stu-id="8d913-179">Compatibility shim</span></span>

<span data-ttu-id="8d913-180">[AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) 라이브러리는 ASP.NET 4.X 웹 API 프로젝트를 ASP.NET Core로 이동 하는 호환성 shim을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="8d913-181">호환성 shim은 ASP.NET 4.x Web API 2의 다양 한 규칙을 지원 하도록 ASP.NET Core를 확장 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="8d913-182">이 문서에서 이전에 이식 한 샘플은 호환성 shim이 필요 하지 않은 경우에만 기본입니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="8d913-183">대규모 프로젝트의 경우 호환성 shim을 사용 하면 ASP.NET Core와 ASP.NET 4.x Web API 2 간의 API 격차를 일시적으로 브리징 하는 데 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="8d913-184">Web API 호환성 shim은 ASP.NET Core로의 large ASP.NET 4.x 웹 API 프로젝트 마이그레이션을 지원 하기 위한 임시 조치로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="8d913-185">시간이 지남에 따라 프로젝트는 호환성 shim을 사용 하는 대신 ASP.NET Core 패턴을 사용 하도록 업데이트 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="8d913-186">`Microsoft.AspNetCore.Mvc.WebApiCompatShim`에 포함 된 호환성 기능은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="8d913-187">컨트롤러의 기본 형식을 업데이트할 필요가 없도록 `ApiController` 형식을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="8d913-188">웹 API 스타일 모델 바인딩을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="8d913-189">ASP.NET Core MVC 모델 바인딩 함수는 기본적으로 ASP.NET 4.x MVC 5와 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="8d913-190">호환성 shim은 모델 바인딩을 ASP.NET 4.x Web API 2 모델 바인딩 규칙과 더 유사 하 게 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="8d913-191">예를 들어 복합 형식은 요청 본문에서 자동으로 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="8d913-192">컨트롤러 작업에서 `HttpRequestMessage`형식의 매개 변수를 사용할 수 있도록 모델 바인딩을 확장 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="8d913-193">`HttpResponseMessage`형식의 결과를 반환 하는 작업을 허용 하는 메시지 포맷터를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="8d913-194">웹 API 2 작업에서 응답을 제공 하는 데 사용할 수 있는 추가 응답 메서드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="8d913-195">`HttpResponseMessage` 생성기:</span><span class="sxs-lookup"><span data-stu-id="8d913-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="8d913-196">작업 결과 메서드:</span><span class="sxs-lookup"><span data-stu-id="8d913-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="8d913-197">`IContentNegotiator` 인스턴스를 앱의 DI (종속성 주입) 컨테이너에 추가 하 고 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)의 콘텐츠 협상 관련 형식을 사용 가능 하 게 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="8d913-198">이러한 형식의 예로는 `DefaultContentNegotiator` 및 `MediaTypeFormatter`가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="8d913-199">호환성 shim을 사용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="8d913-200">[AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="8d913-201">`Startup.ConfigureServices`에서 `services.AddMvc().AddWebApiConventions()`를 호출 하 여 앱의 DI 컨테이너에 호환성 shim의 서비스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="8d913-202">앱의 `IApplicationBuilder.UseMvc` 호출에서 `IRouteBuilder` `MapWebApiRoute`를 사용 하 여 웹 API 관련 경로를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d913-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8d913-203">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8d913-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
