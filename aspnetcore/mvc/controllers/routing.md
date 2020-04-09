---
title: ASP.NET Core의 컨트롤러 작업에 라우팅
author: rick-anderson
description: ASP.NET Core MVC가 라우팅 미들웨어를 사용하여 들어오는 요청의 URL을 일치시키고 이를 작업에 매핑하는 방법을 알아봅니다.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: c63313ec060c5be368fcbd20edf5f0d557046d2e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977221"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="7041a-103">ASP.NET Core의 컨트롤러 작업에 라우팅</span><span class="sxs-lookup"><span data-stu-id="7041a-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="7041a-104">[라이언 노왁,](https://github.com/rynowak) [커크 라킨,](https://twitter.com/serpent5) [릭 앤더슨](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7041a-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7041a-105">ASP.NET 코어 컨트롤러는 라우팅 [미들웨어를](xref:fundamentals/middleware/index) 사용하여 들어오는 요청의 URL을 일치시키고 [작업에](#action)매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="7041a-106">경로 템플릿:</span><span class="sxs-lookup"><span data-stu-id="7041a-106">Routes templates:</span></span>

* <span data-ttu-id="7041a-107">시작 코드 또는 특성에 정의되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="7041a-108">URL 경로가 [작업과](#action)일치하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="7041a-109">링크에 대한 URL을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="7041a-110">생성된 링크는 일반적으로 응답으로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="7041a-111">작업은 [종래로 라우팅되거나](#cr) [특성 라우팅됩니다.](#ar)</span><span class="sxs-lookup"><span data-stu-id="7041a-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="7041a-112">컨트롤러 또는 [작업에](#action) 경로를 배치하면 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="7041a-113">자세한 내용은 [혼합 라우팅](#routing-mixed-ref-label)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="7041a-114">이 문서는 다음과 같은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-114">This document:</span></span>

* <span data-ttu-id="7041a-115">MVC와 라우팅 간의 상호 작용에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="7041a-116">일반적인 MVC 앱이 라우팅 기능을 사용하는 방법.</span><span class="sxs-lookup"><span data-stu-id="7041a-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="7041a-117">둘 다 다 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-117">Covers both:</span></span>
    * <span data-ttu-id="7041a-118">[일반적으로 라우팅은](#cr) 일반적으로 컨트롤러 및 뷰와 함께 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="7041a-119">REST API와 함께 사용되는 *특성 라우팅입니다.*</span><span class="sxs-lookup"><span data-stu-id="7041a-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="7041a-120">REST API에 대한 라우팅에 주로 관심이 있는 경우 [REST API에 대한 특성 라우팅](#ar) 섹션으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="7041a-121">고급 라우팅 세부 정보는 [라우팅을](xref:fundamentals/routing) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="7041a-122">ASP.NET 코어 3.0에 추가 된 기본 라우팅 시스템을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="7041a-123">호환성을 위해 이전 버전의 라우팅과 함께 컨트롤러를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="7041a-124">지침은 [2.2-3.0 마이그레이션 가이드를](xref:migration/22-to-30) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="7041a-125">레거시 라우팅 시스템의 참조 자료는 [이 문서의 2.2 버전을](xref:mvc/controllers/routing?view=aspnetcore-2.2) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="7041a-126">기존 경로 설정</span><span class="sxs-lookup"><span data-stu-id="7041a-126">Set up conventional route</span></span>

<span data-ttu-id="7041a-127">`Startup.Configure`일반적으로 [기존 라우팅을](#crd)사용할 때 다음과 유사한 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="7041a-128">호출 에서 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>"를 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 호출하는 경우 단일 경로를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="7041a-129">단일 경로의 `default` 이름은 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-129">The single route is named `default` route.</span></span> <span data-ttu-id="7041a-130">컨트롤러 및 뷰가 있는 대부분의 앱은 `default` 경로와 유사한 경로 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="7041a-131">REST API는 [특성 라우팅을](#ar)사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="7041a-132">경로 템플릿 `"{controller=Home}/{action=Index}/{id?}"`:</span><span class="sxs-lookup"><span data-stu-id="7041a-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="7041a-133">다음과 같은 URL 경로와 일치합니다.`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="7041a-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="7041a-134">경로를 토큰화하여 `{ controller = Products, action = Details, id = 5 }` 경로 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="7041a-135">경로 값을 추출하면 앱에 명명된 `ProductsController` 컨트롤러와 작업이 `Details` 있는 경우 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="7041a-136">`/Products/Details/5`모델은 `id = 5` `id` 매개 변수를 로 설정하는 `5`값을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="7041a-137">자세한 내용은 [모델 바인딩을](xref:mvc/models/model-binding) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="7041a-138">`{controller=Home}`기본값으로 `Home` `controller`정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="7041a-139">`{action=Index}`기본값으로 `Index` `action`정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="7041a-140">의 `?` `{id?}` 문자는 `id` 선택 사항으로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="7041a-141">기본 및 선택적 경로 매개 변수는 매칭을 위해 URL 경로에 반드시 있어야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="7041a-142">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="7041a-143">URL 경로와 `/`일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="7041a-144">경로 값을 `{ controller = Home, action = Index }`생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="7041a-145">기본값과 `controller` `action` 값을 사용하는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="7041a-146">`id`URL 경로에 해당 세그먼트가 없기 때문에 값을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="7041a-147">`/``HomeController` 및 `Index` 작업이 있는 경우에만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="7041a-148">앞의 컨트롤러 정의 및 경로 `HomeController.Index` 템플릿을 사용하여 다음 URL 경로에 대해 작업이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="7041a-149">URL 경로는 `/` 경로 템플릿 `Home` 기본 `Index` 컨트롤러 및 작업을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="7041a-150">URL 경로는 `/Home` 경로 템플릿 `Index` 기본 작업을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="7041a-151">편의 메서드인 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>를 사용하여:</span><span class="sxs-lookup"><span data-stu-id="7041a-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="7041a-152">대체:</span><span class="sxs-lookup"><span data-stu-id="7041a-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="7041a-153">라우팅은 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> 및 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 미들웨어를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="7041a-154">컨트롤러를 사용하려면 다음을 수행하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-154">To use controllers:</span></span>

* <span data-ttu-id="7041a-155"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> 내부를 `UseEndpoints` 호출하여 [특성 라우팅된](#ar) 컨트롤러를 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
* <span data-ttu-id="7041a-156"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 호출하거나 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, [기존에 라우팅된 컨트롤러를](#cr) 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="7041a-157">규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="7041a-157">Conventional routing</span></span>

<span data-ttu-id="7041a-158">기존의 라우팅은 컨트롤러 및 뷰와 함께 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="7041a-159">`default` 경로인:</span><span class="sxs-lookup"><span data-stu-id="7041a-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="7041a-160">는 *규칙 기반 라우팅*의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="7041a-161">URL 경로에 대한 *규칙을* 설정하기 때문에 *기존 라우팅이라고* 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="7041a-162">첫 번째 경로 `{controller=Home}`세그먼트는 컨트롤러 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="7041a-163">두 번째 `{action=Index}`세그먼트 , " [작업](#action) 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="7041a-164">세 번째 `{id?}` 세그먼트는 선택적 `id`세그먼트에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="7041a-165">`?` in은 `{id?}` 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="7041a-166">`id`모델 엔터티에 매핑하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="7041a-167">이 `default` 경로를 사용하는 URL 경로는 다음과 같은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="7041a-168">`/Products/List`맵을 `ProductsController.List` 사용하여 작업에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="7041a-169">`/Blog/Article/17``BlogController.Article` 맵을 17에 바인딩합니다. `id`</span><span class="sxs-lookup"><span data-stu-id="7041a-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="7041a-170">이 매핑:</span><span class="sxs-lookup"><span data-stu-id="7041a-170">This mapping:</span></span>

* <span data-ttu-id="7041a-171">컨트롤러 및 [작업](#action) **이름만**을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="7041a-172">네임스페이스, 소스 파일 위치 또는 메서드 매개 변수를 기반으로 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="7041a-173">기본 경로와 함께 기존 라우팅을 사용하면 각 작업에 대한 새 URL 패턴을 마련할 필요 없이 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="7041a-174">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 스타일 동작이 있는 앱의 경우 컨트롤러 전체에서 URL에 대한 일관성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="7041a-175">코드를 단순화하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-175">Helps simplify the code.</span></span>
* <span data-ttu-id="7041a-176">UI를 더 쉽게 예측할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="7041a-177">위의 `id` 코드의 경우 경로 템플릿에 의해 선택 사항으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="7041a-178">작업은 URL의 일부로 제공된 선택적 ID 없이 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="7041a-179">일반적으로 URL에서 생략되는 경우:`id`</span><span class="sxs-lookup"><span data-stu-id="7041a-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="7041a-180">`id`모델 바인딩으로 `0` 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="7041a-181">데이터베이스 일치에 `id == 0`엔터티가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="7041a-182">[특성 라우팅은](#ar) 일부 작업에 필요한 ID를 만들기 위해 세밀한 컨트롤을 제공하며 다른 작업에는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="7041a-183">규칙에 따라 설명서에는 올바른 `id` 사용법에 표시될 가능성이 있는 경우와 같은 선택적 매개 변수가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="7041a-184">대부분의 앱은 URL이 읽을 수 있고 의미 있도록 기본적이고 설명적인 라우팅 체계를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="7041a-185">기본 기존 경로 `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="7041a-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="7041a-186">기본적이고 설명이 포함된 라우팅 체계를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="7041a-187">UI 기반 앱에 대한 유용한 시작점입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="7041a-188">많은 웹 UI 앱에 필요한 유일한 경로 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="7041a-189">대규모 웹 UI 앱의 경우 필요한 경우 [영역을](#areas) 사용하는 다른 경로가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-189">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="7041a-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>및 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="7041a-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="7041a-191">호출된 순서에 따라 **끝점에 주문** 값을 자동으로 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="7041a-192">코어 3.0 이후의 ASP.NET 엔드포인트 라우팅:</span><span class="sxs-lookup"><span data-stu-id="7041a-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="7041a-193">경로개념이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="7041a-194">확장성 실행에 대한 순서 보증을 제공하지 않으므로 모든 끝점이 한 번에 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="7041a-195">[로깅](xref:fundamentals/logging/index)을 사용하도록 설정하여 <xref:Microsoft.AspNetCore.Routing.Route>와 같은 기본 제공 라우팅 구현에서 요청과 일치시키는 방법을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="7041a-196">[특성 라우팅은](#ar) 이 문서의 후반부에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="7041a-197">여러 개의 기존 경로</span><span class="sxs-lookup"><span data-stu-id="7041a-197">Multiple conventional routes</span></span>

<span data-ttu-id="7041a-198">여러 [개의 기존](#cr) 경로를 `UseEndpoints` 추가하여 내부에 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 추가할 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="7041a-199">이렇게 하면 여러 규칙을 정의하거나 다음과 같은 특정 [작업에](#action)전용인 기존 경로를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="7041a-200">앞의 코드의 `blog` 경로는 **전용 기존 경로입니다.**</span><span class="sxs-lookup"><span data-stu-id="7041a-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="7041a-201">다음과 같은 이유로 전용 기존 경로라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="7041a-202">그것은 [기존의 라우팅을](#cr)사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="7041a-203">그것은 특정 [작업에](#action)전념하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="7041a-204">왜냐하면 `action` 경로 템플릿에 `"blog/{*article}"` 매개 변수로 나타나지 않기 때문입니다. `controller`</span><span class="sxs-lookup"><span data-stu-id="7041a-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="7041a-205">기본값만 `{ controller = "Blog", action = "Article" }`가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="7041a-206">이 경로는 항상 `BlogController.Article`작업에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="7041a-207">`/Blog`및 `/Blog/Article`블로그 `/Blog/{any-string}` 경로와 일치하는 유일한 URL 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="7041a-208">위의 예는 다음과 같은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-208">The preceding example:</span></span>

* <span data-ttu-id="7041a-209">`blog`경로가 먼저 추가되기 때문에 `default` 라우트가 라우트보다 일치하는 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="7041a-210">IS 및 URL의 일부로 아티클 이름을 가지는 것이 일반적인 [Slug](https://developer.mozilla.org/docs/Glossary/Slug) 스타일 라우팅의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-210">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="7041a-211">ASP.NET 코어 3.0 이상에서는 라우팅이 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="7041a-212">*경로라는*개념을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-212">Define a concept called a *route*.</span></span> <span data-ttu-id="7041a-213">`UseRouting`미들웨어 파이프라인에 경로 일치를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="7041a-214">미들웨어는 `UseRouting` 앱에 정의된 끝점 집합을 살펴보고 요청에 따라 최적의 끝점 일치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="7041a-215">또는 .와 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>같은 확장성의 실행 순서에 대한 보증을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="7041a-216">라우팅에 대한 참조 자료에 대한 [라우팅을](xref:fundamentals/routing) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="7041a-217">기존 라우팅 순서</span><span class="sxs-lookup"><span data-stu-id="7041a-217">Conventional routing order</span></span>

<span data-ttu-id="7041a-218">기존 라우팅은 앱에서 정의한 동작과 컨트롤러의 조합과만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="7041a-219">이는 기존 경로가 겹치는 경우를 단순화하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="7041a-220">을 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>사용하여 경로를 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> 추가하고 호출된 순서에 따라 끝점에 주문 값을 자동으로 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="7041a-221">이전에 나타나는 경로의 일치 항목의 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="7041a-222">규칙 기반 라우팅은 순서에 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7041a-223">일반적으로 영역이 있는 경로는 영역이 없는 경로보다 더 구체적이므로 더 일찍 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="7041a-224">캐치 와 [전용 된 기존](#dcr) `{*article}` 경로 같은 모든 경로 매개 변수 너무 [욕심](xref:fundamentals/routing#greedy)을 만들 수 있습니다., 다른 경로에 의해 일치 하도록 의도 한 URL일치 의미.</span><span class="sxs-lookup"><span data-stu-id="7041a-224">[Dedicated conventional routes](#dcr) with catch all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="7041a-225">욕심 이 일치를 방지하기 위해 경로 테이블의 나중에 욕심 경로를 넣어.</span><span class="sxs-lookup"><span data-stu-id="7041a-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="7041a-226">모호한 작업 해결</span><span class="sxs-lookup"><span data-stu-id="7041a-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="7041a-227">라우팅을 통해 두 끝점이 일치하는 경우 라우팅은 다음 중 하나를 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="7041a-228">가장 적합한 후보를 선택하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-228">Choose the best candidate.</span></span>
* <span data-ttu-id="7041a-229">예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-229">Throw an exception.</span></span>

<span data-ttu-id="7041a-230">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="7041a-231">앞의 컨트롤러는 일치하는 두 가지 작업을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="7041a-232">URL 경로`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="7041a-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="7041a-233">라우팅 `{ controller = Products33, action = Edit, id = 17 }`데이터 .</span><span class="sxs-lookup"><span data-stu-id="7041a-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="7041a-234">MVC 컨트롤러의 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="7041a-235">`Edit(int)`을 통해 제품을 편집할 수 있는 양식이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="7041a-236">`Edit(int, Product)`게시된 양식을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="7041a-237">올바른 경로를 해결하려면 다음을 수행하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-237">To resolve the correct route:</span></span>

* <span data-ttu-id="7041a-238">`Edit(int, Product)`요청이 HTTP `POST`인 경우 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="7041a-239">`Edit(int)`[HTTP 동사가](#verb) 다른 것이면 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="7041a-240">`Edit(int)`일반적으로 를 `GET`통해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="7041a-241">은 <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`요청의 HTTP 메서드에 따라 선택할 수 있도록 라우팅에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="7041a-242">`Edit(int, Product)` `Edit(int)`은 `HttpPostAttribute` 보다 더 나은 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="7041a-243">과 같은 `HttpPostAttribute`속성의 역할을 이해하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="7041a-244">다른 [HTTP 동사에](#verb)대해 유사한 특성이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="7041a-245">[기존 라우팅에서는](#cr)작업에서 쇼 양식의 일부인 경우 동일한 작업 이름을 사용하고 양식 워크플로를 제출하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="7041a-246">예를 들어 [두 편집 작업 메서드 검사를](xref:tutorials/first-mvc-app/controller-methods-views#get-post)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="7041a-247">라우팅이 최적의 후보를 선택할 수 없는 <xref:System.Reflection.AmbiguousMatchException> 경우 일치하는 여러 끝점을 나열하는 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="7041a-248">기존 루트 이름</span><span class="sxs-lookup"><span data-stu-id="7041a-248">Conventional route names</span></span>

<span data-ttu-id="7041a-249">`"blog"` 문자열과 `"default"` 다음 예제의 일반적인 경로 이름은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="7041a-250">경로 이름은 경로에 논리적 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-250">The route names give the route a logical name.</span></span> <span data-ttu-id="7041a-251">명명된 경로는 URL 생성에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="7041a-252">명명된 경로를 사용하면 경로 순서를 지정하면 URL 생성이 복잡해질 수 있는 경우 URL 생성이 간소화됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="7041a-253">경로 이름은 응용 프로그램 전체에 고유한 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="7041a-254">경로 이름:</span><span class="sxs-lookup"><span data-stu-id="7041a-254">Route names:</span></span>

* <span data-ttu-id="7041a-255">URL 일치 또는 요청 처리에 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="7041a-256">URL 생성에만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-256">Are used only for URL generation.</span></span>

<span data-ttu-id="7041a-257">경로 이름 개념은 [라우팅에서 IEndpointNameMetadata로](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="7041a-258">용어 **경로 이름** 및 **끝점 이름**:</span><span class="sxs-lookup"><span data-stu-id="7041a-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="7041a-259">상호 교환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-259">Are interchangeable.</span></span>
* <span data-ttu-id="7041a-260">설명서및 코드에 사용되는 것은 설명하는 API에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="7041a-261">REST API에 대한 특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="7041a-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="7041a-262">REST API는 특성 라우팅을 사용하여 [HTTP 동사에](#verb)의해 작업이 표시되는 리소스 집합으로 앱의 기능을 모델링해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="7041a-263">특성 라우팅은 특성 모음을 사용하여 작업을 경로 템플릿에 직접 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="7041a-264">다음 `StartUp.Configure` 코드는 REST API에 일반적으로 사용되며 다음 샘플에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="7041a-265">앞의 코드에서는 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> 특성 라우트된 컨트롤러를 매핑하기 위해 내부로 `UseEndpoints` 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="7041a-266">다음 예제에서,</span><span class="sxs-lookup"><span data-stu-id="7041a-266">In the following example:</span></span>

* <span data-ttu-id="7041a-267">앞의 `Configure` 방법이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="7041a-268">`HomeController`기본 기존 경로가 `{controller=Home}/{action=Index}/{id?}` 일치하는 URL 집합과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="7041a-269">작업은 `HomeController.Index` 모든 URL 경로에 대해 `/`실행됩니다. `/Home` `/Home/Index` `/Home/Index/3`</span><span class="sxs-lookup"><span data-stu-id="7041a-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="7041a-270">이 예제에서는 특성 라우팅과 기존 [라우팅](#cr)간의 주요 프로그래밍 차이점을 강조 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="7041a-271">특성 라우팅은 경로를 지정하기 위해 더 많은 입력이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="7041a-272">기존의 기본 경로는 경로를 보다 간결하게 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="7041a-273">그러나 특성 라우팅을 허용하고 각 [작업에](#action)적용되는 경로 템플릿을 정확하게 제어해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="7041a-274">특성 라우팅을 사용하면 컨트롤러 이름과 작업 이름이 일치하는 작업을 **수행하지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="7041a-274">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="7041a-275">다음 예제는 이전 예제와 동일한 URL과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="7041a-276">다음 코드는 다음에 `action` 대한 `controller`토큰 대체를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="7041a-277">다음 코드는 `[Route("[controller]/[action]")]` 컨트롤러에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="7041a-278">앞의 코드에서 `Index` 메서드 템플릿은 미리 `/` 준비하거나 `~/` 경로 템플릿에 대해 준비해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="7041a-279">작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="7041a-280">경로 템플릿 선택에 대한 정보는 [경로 템플릿 우선 순위를](xref:fundamentals/routing#rtp) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="7041a-281">예약된 라우팅 이름</span><span class="sxs-lookup"><span data-stu-id="7041a-281">Reserved routing names</span></span>

<span data-ttu-id="7041a-282">다음 키워드는 컨트롤러 또는 Razor 페이지를 사용할 때 예약된 경로 매개 변수 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="7041a-283">특성 `page` 라우팅을 사용하여 경로 매개 변수로 사용하는 것은 일반적인 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="7041a-284">이렇게 하면 URL 생성과 일관되지 않고 혼동되는 동작이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="7041a-285">URL 생성 작업이 Razor 페이지 또는 컨트롤러를 참조하는지 여부를 확인하기 위해 URL 생성에서 특수 매개 변수 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="7041a-286">HTTP 동사 템플릿</span><span class="sxs-lookup"><span data-stu-id="7041a-286">HTTP verb templates</span></span>

<span data-ttu-id="7041a-287">ASP.NET 코어에는 다음과 같은 HTTP 동사 템플릿이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="7041a-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="7041a-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="7041a-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="7041a-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="7041a-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="7041a-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="7041a-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="7041a-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="7041a-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="7041a-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="7041a-293">[[Http패치]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="7041a-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="7041a-294">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="7041a-294">Route templates</span></span>

<span data-ttu-id="7041a-295">ASP.NET 코어에는 다음과 같은 경로 템플릿이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="7041a-296">모든 [HTTP 동사 템플릿은](#verb) 경로 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="7041a-297">[[루트]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="7041a-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="7041a-298">http 동사 속성이 있는 특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="7041a-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="7041a-299">다음 컨트롤러를 고려하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="7041a-300">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="7041a-300">In the preceding code:</span></span>

* <span data-ttu-id="7041a-301">각 작업에는 `[HttpGet]` HTTP GET 요청에만 일치를 제한하는 특성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="7041a-302">작업에는 `GetProduct` `"{id}"` 템플릿이 포함되므로 `id` 컨트롤러의 `"api/[controller]"` 템플릿에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="7041a-303">메서드 템플릿은 `"api/[controller]/"{id}""`.</span><span class="sxs-lookup"><span data-stu-id="7041a-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="7041a-304">따라서 이 작업은 양식 `/api/test2/xyz`,`/api/test2/123`및`/api/test2/{any string}`기타의 GET 요청만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-304">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="7041a-305">작업에는 `GetIntProduct` 템플릿이 포함되어 있습니다. `"int/{id:int}")`</span><span class="sxs-lookup"><span data-stu-id="7041a-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="7041a-306">템플릿 `:int` 부분은 `id` 루트 값을 정수로 변환할 수 있는 문자열로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="7041a-307">GET 요청 `/api/test2/int/abc`:</span><span class="sxs-lookup"><span data-stu-id="7041a-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="7041a-308">이 작업과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="7041a-309">[404 를 찾을 수 없는 오류를 반환합니다.](https://developer.mozilla.org/docs/Web/HTTP/Status/404)</span><span class="sxs-lookup"><span data-stu-id="7041a-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="7041a-310">`GetInt2Product` 작업은 템플릿에 포함되지만 `{id}` 정수로 변환할 수 있는 값으로 제한되지는 `id` 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="7041a-311">GET 요청 `/api/test2/int2/abc`:</span><span class="sxs-lookup"><span data-stu-id="7041a-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="7041a-312">이 경로와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-312">Matches this route.</span></span>
  * <span data-ttu-id="7041a-313">모델 바인딩이 `abc` 정수로 변환되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="7041a-314">메서드의 매개 변수는 `id` 정수입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="7041a-315">모델 바인딩이 정수로 변환하지`abc` 못했기 때문에 [400 잘못된 요청을](https://developer.mozilla.org/docs/Web/HTTP/Status/400) 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="7041a-316">특성 라우팅은 <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>에서 <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>와 같은 특성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="7041a-317">모든 [HTTP 동사](#verb) 특성은 경로 템플릿을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="7041a-318">다음 예제에서는 동일한 경로 템플릿과 일치하는 두 가지 작업을 보여 주십습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="7041a-319">URL 경로 `/products3`사용:</span><span class="sxs-lookup"><span data-stu-id="7041a-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="7041a-320">이 `MyProductsController.ListProducts` 작업은 [HTTP 동사가](#verb) `GET`.일 때 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="7041a-321">이 `MyProductsController.CreateProduct` 작업은 [HTTP 동사가](#verb) `POST`.일 때 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="7041a-322">REST API를 빌드할 때 작업이 모든 HTTP 메서드를 허용하므로 작업 메서드에서 사용해야 `[Route(...)]` 하는 경우는 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="7041a-323">API가 지원하는 내용에 대해 정확하게 하려면 보다 구체적인 [HTTP 동사 특성을](#verb) 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="7041a-324">REST API의 클라이언트는 특정 논리 작업에 어떤 경로 및 HTTP 동사가 매핑되는지 알아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="7041a-325">REST API는 특성 라우팅을 사용하여 HTTP 동사에 의해 작업이 표시되는 리소스 집합으로 앱의 기능을 모델링해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="7041a-326">즉, 동일한 논리 리소스의 GET 및 POST와 같은 많은 작업이 동일한 URL을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="7041a-327">특성 라우팅은 API의 공용 엔드포인트 레이아웃을 신중하게 설계하는 데 필요한 제어 수준을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="7041a-328">특성 경로는 특정 작업에 적용되므로 경로 템플릿 정의의 일환으로 필요한 매개 변수를 간단하게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="7041a-329">다음 예제에서는 `id` URL 경로의 일부로 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="7041a-330">작업: `Products2ApiController.GetProduct(int)`</span><span class="sxs-lookup"><span data-stu-id="7041a-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="7041a-331">다음과 같은 URL 경로로 실행됩니다.`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="7041a-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="7041a-332">URL 경로로 `/products2`실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="7041a-333">[[사용]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 특성을 사용하면 작업에서 지원되는 요청 콘텐츠 형식을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="7041a-334">자세한 내용은 [사용 특성을 사용 하 여 지원 되는 요청 콘텐츠 형식 정의](xref:web-api/index#consumes)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="7041a-335">경로 템플릿 및 관련 옵션에 대한 전체 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="7041a-336">자세한 `[ApiController]`내용은 [ApiController 특성을](xref:web-api/index##apicontroller-attribute)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="7041a-337">경로 이름</span><span class="sxs-lookup"><span data-stu-id="7041a-337">Route name</span></span>

<span data-ttu-id="7041a-338">다음 코드는 의 `Products_List`경로 이름을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="7041a-339">경로 이름을 사용하여 특정 경로 기반의 URL을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="7041a-340">경로 이름:</span><span class="sxs-lookup"><span data-stu-id="7041a-340">Route names:</span></span>

* <span data-ttu-id="7041a-341">라우팅의 URL 일치 동작에 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="7041a-342">URL 생성에만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-342">Are only used for URL generation.</span></span>

<span data-ttu-id="7041a-343">경로 이름은 애플리케이션 전체에서 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="7041a-344">`id` 매개 변수를 선택적()로`{id?}`정의하는 기존 기본 경로와 앞의 코드를 대조합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="7041a-345">API를 정확하게 지정하는 기능에는 `/products` 허용하고 `/products/5` 다른 작업에 전달되는 등의 장점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="7041a-346">특성 경로 결합</span><span class="sxs-lookup"><span data-stu-id="7041a-346">Combining attribute routes</span></span>

<span data-ttu-id="7041a-347">특성 라우팅의 반복 횟수를 줄이기 위해 컨트롤러의 경로 특성은 개별 작업의 경로 특성과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="7041a-348">컨트롤러에 정의된 경로 템플릿은 작업의 경로 템플릿에 앞에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="7041a-349">경로 특성을 컨트롤러에 배치하면 컨트롤러의 **모든** 작업이 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="7041a-350">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="7041a-350">In the preceding example:</span></span>

* <span data-ttu-id="7041a-351">URL 경로가 `/products` 일치할 수 있습니다.`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="7041a-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="7041a-352">URL 경로가 `/products/5` `ProductsApi.GetProduct(int)`일치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="7041a-353">이 두 작업은 특성으로 표시되어 있으므로 `GET` `[HttpGet]` HTTP와만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="7041a-354">작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="7041a-355">다음 예제는 기본 경로와 유사한 URL 경로 집합과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="7041a-356">다음 표에서는 `[Route]` 앞의 코드의 특성을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="7041a-357">attribute</span><span class="sxs-lookup"><span data-stu-id="7041a-357">Attribute</span></span>               | <span data-ttu-id="7041a-358">결합`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="7041a-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="7041a-359">경로 템플릿 정의</span><span class="sxs-lookup"><span data-stu-id="7041a-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="7041a-360">예</span><span class="sxs-lookup"><span data-stu-id="7041a-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="7041a-361">예</span><span class="sxs-lookup"><span data-stu-id="7041a-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="7041a-362">**아니요**</span><span class="sxs-lookup"><span data-stu-id="7041a-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="7041a-363">예</span><span class="sxs-lookup"><span data-stu-id="7041a-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="7041a-364">특성 경로 순서</span><span class="sxs-lookup"><span data-stu-id="7041a-364">Attribute route order</span></span>

<span data-ttu-id="7041a-365">라우팅은 트리를 빌드하고 모든 끝점을 동시에 일치시다.</span><span class="sxs-lookup"><span data-stu-id="7041a-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="7041a-366">경로 항목은 이상적인 순서로 배치된 것처럼 행동합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="7041a-367">가장 구체적인 경로는 보다 일반적인 경로 보다 먼저 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="7041a-368">예를 들어 와 같은 `blog/search/{topic}` 특성 경로는 와 `blog/{*article}`같은 특성 경로보다 더 구체적입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="7041a-369">경로는 `blog/search/{topic}` 더 구체적이기 때문에 기본적으로 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="7041a-370">[기존 라우팅을](#cr)사용하여 개발자는 원하는 순서로 경로를 배치할 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="7041a-371">특성 경로는 속성을 사용하여 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> 순서를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="7041a-372">제공된 모든 프레임워크는 경로 `Order` [특성을 포함합니다.](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="7041a-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="7041a-373">경로는 `Order` 속성의 오름차순 정렬에 따라 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="7041a-374">기본 순서는 `0`입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-374">The default order is `0`.</span></span> <span data-ttu-id="7041a-375">순서를 설정하지 않은 경로 전에 실행을 사용하여 `Order = -1` 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="7041a-376">기본 경로 `Order = 1` 순서 를 변경한 후 실행을 사용하여 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="7041a-377">`Order`에 따라 **피하십시오.**</span><span class="sxs-lookup"><span data-stu-id="7041a-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="7041a-378">앱의 URL 공간에서 올바르게 라우팅하기 위해 명시적 주문 값이 필요한 경우 클라이언트에게도 혼동을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="7041a-379">일반적으로 특성 라우팅은 URL 일치를 가진 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="7041a-380">URL 생성에 사용되는 기본 순서가 작동하지 않는 경우 경로 이름을 재정의로 사용하는 것이 `Order` 일반적으로 속성을 적용하는 것보다 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="7041a-381">둘 다 경로 일치를 `/home`정의하는 다음 두 컨트롤러를 고려하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="7041a-382">앞의 `/home` 코드를 요청하려면 다음과 유사한 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="7041a-383">경로 `Order` 특성 중 하나에 추가하면 모호성이 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="7041a-384">앞의 코드를 사용하면 `/home` 끝점을 실행합니다. `HomeController.Index`</span><span class="sxs-lookup"><span data-stu-id="7041a-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="7041a-385">`MyDemoController.MyIndex`로 이동하려면 를 `/home/MyIndex`요청하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="7041a-386">**고**:</span><span class="sxs-lookup"><span data-stu-id="7041a-386">**Note**:</span></span>

* <span data-ttu-id="7041a-387">앞의 코드는 예제 또는 잘못된 라우팅 디자인입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="7041a-388">`Order` 속성을 설명하는 데 사용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="7041a-389">속성은 `Order` 모호성만 해결하며 해당 템플릿은 일치시킬 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="7041a-390">템플릿을 `[Route("Home")]` 제거하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="7041a-391">[Razor 페이지 경로 및 앱 규칙:](xref:razor-pages/razor-pages-conventions#route-order) Razor 페이지를 사용하면 경로 순서에 대한 정보를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="7041a-392">경우에 따라 HTTP 500 오류가 모호한 경로와 함께 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="7041a-393">[로깅을](xref:fundamentals/logging/index) 사용하여 `AmbiguousMatchException`의 원인을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="7041a-394">경로 템플릿의 토큰 교체 [컨트롤러], [작업], [영역]</span><span class="sxs-lookup"><span data-stu-id="7041a-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="7041a-395">편의를 위해 특성 경로는 다음 중 하나에 토큰을 둘러싸서 예약된 경로 매개 변수에 대한 토큰 교체를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="7041a-396">정사각형 중괄호:`[]`</span><span class="sxs-lookup"><span data-stu-id="7041a-396">Square braces: `[]`</span></span>
* <span data-ttu-id="7041a-397">중괄호:`{}`</span><span class="sxs-lookup"><span data-stu-id="7041a-397">Curly braces: `{}`</span></span>

<span data-ttu-id="7041a-398">`[action]`토큰은 `[controller]` 및 `[area]`경로가 정의된 작업에서 작업 이름, 영역 이름 및 컨트롤러 이름의 값으로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="7041a-399">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="7041a-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="7041a-400">일치`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="7041a-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="7041a-401">일치`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="7041a-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="7041a-402">토큰 교체는 특성 경로 빌드 과정의 마지막 단계로 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="7041a-403">앞의 예제는 다음 코드와 동일하게 작업합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="7041a-404">특성 경로를 상속과 결합할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="7041a-405">이는 토큰 교체와 함께 강력하게 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="7041a-406">토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="7041a-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`각 작업에 대해 고유한 경로 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="7041a-408">토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="7041a-409">는 각 작업의 고유한 경로 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="7041a-410">리터럴 토큰 교체 구분 기호 `[` 또는 `]`와 매칭하려면 문자(`[[` 또는 `]]`)를 반복하여 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="7041a-411">매개 변수 변환기를 사용하여 토큰 교체 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="7041a-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="7041a-412">매개 변수 변환기를 사용하여 토큰 교체를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="7041a-413">매개 변수 변환기는 <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer>를 구현하며 매개 변수의 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="7041a-414">예를 들어 사용자 `SlugifyParameterTransformer` 지정 매개 `SubscriptionManagement` 변수 변압기는 경로 값을 다음으로 `subscription-management`변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="7041a-415"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention>은 다음과 같은 응용 프로그램 모델 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="7041a-416">애플리케이션의 모든 특성 경로에 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="7041a-417">대체되는 특성 경로 토큰 값을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="7041a-418">앞의 `ListAll` 메서드가 `/subscription-management/list-all`일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="7041a-419">`RouteTokenTransformerConvention`은 `ConfigureServices`에 옵션으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="7041a-420">[슬러그의 정의는 슬러그에서 MDN 웹 문서를](https://developer.mozilla.org/docs/Glossary/Slug) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="7041a-421">여러 특성 경로</span><span class="sxs-lookup"><span data-stu-id="7041a-421">Multiple attribute routes</span></span>

<span data-ttu-id="7041a-422">특성 경로는 동일한 작업에 도달하는 여러 경로를 정의하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="7041a-423">가장 일반적인 사용 방법은 다음 예제와 같이 기본 규칙 기반 경로의 동작을 모방하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="7041a-424">컨트롤러에 여러 경로 특성을 배치한다는 것은 각 경로 가 작업 메서드의 각 경로 특성과 결합된다는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="7041a-425">모든 [HTTP 동사](#verb) 경로 `IActionConstraint`제약 조건은 을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="7041a-426">구현하는 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 여러 경로 특성이 작업에 배치되는 경우:</span><span class="sxs-lookup"><span data-stu-id="7041a-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="7041a-427">각 작업 제약 조건은 컨트롤러에 적용된 경로 템플릿과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="7041a-428">작업에 여러 경로를 사용하면 유용하고 강력해 보일 수 있으므로 앱의 URL 공간을 기본적이고 잘 정의된 상태로 유지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="7041a-429">예를 들어 기존 클라이언트를 지원하기 위해 필요한 **경우에만** 작업에 여러 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="7041a-430">특성 경로 선택적 매개 변수, 기본값 및 제약 조건 지정</span><span class="sxs-lookup"><span data-stu-id="7041a-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="7041a-431">특성 경로는 선택적 매개 변수, 기본값 및 제약 조건을 지정할 수 있는 규칙 기반 경로와 동일한 인라인 구문을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="7041a-432">앞의 코드에서 `[HttpPost("product/{id:int}")]` 경로 제약 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="7041a-433">`ProductsController.ShowProduct` 작업은 와 같은 `/product/3`URL 경로에 의해서만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="7041a-434">배관 템플릿 `{id:int}` 부분은 해당 세그먼트를 정수로만 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="7041a-435">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="7041a-436">IRouteTemplateProvider를 사용하여 사용자 지정 경로 특성</span><span class="sxs-lookup"><span data-stu-id="7041a-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="7041a-437">모든 [경로 특성구현](#rt) <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span><span class="sxs-lookup"><span data-stu-id="7041a-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="7041a-438">ASP.NET 코어 런타임:</span><span class="sxs-lookup"><span data-stu-id="7041a-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="7041a-439">앱이 시작될 때 컨트롤러 클래스 및 작업 메서드에서 특성을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="7041a-440">구현하는 `IRouteTemplateProvider` 특성을 사용하여 초기 경로 집합을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="7041a-441">사용자 `IRouteTemplateProvider` 지정 경로 특성을 정의하기 위해 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="7041a-442">각각의 `IRouteTemplateProvider`를 사용하여 사용자 지정 경로 템플릿, 순서 및 이름으로 단일 경로를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="7041a-443">앞의 `Get` 메서드는 `Order = 2, Template = api/MyTestApi`을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="7041a-444">응용 프로그램 모델을 사용하여 특성 경로를 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="7041a-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="7041a-445">응용 프로그램 모델:</span><span class="sxs-lookup"><span data-stu-id="7041a-445">The application model:</span></span>

* <span data-ttu-id="7041a-446">시작 시 작성된 개체 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="7041a-447">ASP.NET Core에서 앱에서 작업을 라우팅하고 실행하는 데 사용하는 모든 메타데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="7041a-448">응용 프로그램 모델에는 경로 특성에서 수집된 모든 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="7041a-449">경로 특성의 데이터는 구현에서 `IRouteTemplateProvider` 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="7041a-450">규칙:</span><span class="sxs-lookup"><span data-stu-id="7041a-450">Conventions:</span></span>

* <span data-ttu-id="7041a-451">라우팅 동작 방식을 사용자 지정하기 위해 응용 프로그램 모델을 수정하도록 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="7041a-452">앱 시작 시 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-452">Are read at app startup.</span></span>

<span data-ttu-id="7041a-453">이 섹션에서는 응용 프로그램 모델을 사용하여 라우팅을 사용자 지정하는 기본 예제를 보여 주며, 이 섹션에서는 다음과 같은 기본 예제를 보여 주실 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="7041a-454">다음 코드는 라우트를 프로젝트의 폴더 구조와 대략일치하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="7041a-455">다음 코드는 라우트된 `namespace` 특성이 있는 컨트롤러에 규칙이 적용되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="7041a-456">예를 들어 다음 컨트롤러는 다음 `NamespaceRoutingConvention`컨트롤러를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="7041a-457">`NamespaceRoutingConvention.Apply` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="7041a-458">컨트롤러가 라우트된 특성인 경우 아무 것도 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="7041a-459">을 기반으로 `namespace`컨트롤러 템플릿을 설정하고 `namespace` 베이스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="7041a-460">적용 `NamespaceRoutingConvention` 할 수 `Startup.ConfigureServices`있습니다 :</span><span class="sxs-lookup"><span data-stu-id="7041a-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="7041a-461">예를 들어 다음 컨트롤러를 고려하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="7041a-462">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="7041a-462">In the preceding code:</span></span>

* <span data-ttu-id="7041a-463">베이스는 `namespace` `My.Application`.</span><span class="sxs-lookup"><span data-stu-id="7041a-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="7041a-464">앞의 컨트롤러의 전체 이름은 `My.Application.Admin.Controllers.UsersController`.</span><span class="sxs-lookup"><span data-stu-id="7041a-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="7041a-465">컨트롤러 `NamespaceRoutingConvention` 템플릿을 `Admin/Controllers/Users/[action]/{id?`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="7041a-466">컨트롤러에 `NamespaceRoutingConvention` 특성으로 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="7041a-467">혼합 라우팅: 특성 라우팅 및 규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="7041a-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="7041a-468">ASP.NET 핵심 앱은 기존 라우팅 및 특성 라우팅의 사용을 혼합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="7041a-469">일반적으로 브라우저의 HTML 페이지를 처리하는 컨트롤러에는 규칙 기반 경로를 사용하고 REST API를 제공하는 컨트롤러에는 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="7041a-470">작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7041a-471">컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7041a-472">특성 경로를 정의하는 동작은 규칙 기반 경로를 통해 도달할 수 없으며 그 반대도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="7041a-473">컨트롤러의 **모든** 경로 특성은 라우팅된 컨트롤러 특성의 **모든** 작업을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="7041a-474">특성 라우팅 및 기존 라우팅은 동일한 라우팅 엔진을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="7041a-475">URL 생성 및 주변 값</span><span class="sxs-lookup"><span data-stu-id="7041a-475">URL Generation and ambient values</span></span>

<span data-ttu-id="7041a-476">앱은 라우팅 URL 생성 기능을 사용하여 작업에 대한 URL 링크를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="7041a-477">URL을 생성하면 하드코딩 URL이 제거되므로 코드가 더 강력하고 유지 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="7041a-478">이 섹션에서는 MVC에서 제공하는 URL 생성 기능에 중점을 두고 URL 생성 작동 방식에 대한 기본 사항만 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="7041a-479">URL 생성에 대한 자세한 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="7041a-480">인터페이스는 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> MVC와 URL 생성을 위한 라우팅 사이의 인프라의 기본 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="7041a-481">`IUrlHelper` 인스턴스는 제어기, `Url` 뷰 및 뷰 구성 요소에서 속성을 통해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="7041a-482">다음 예제에서는 `IUrlHelper` 인터페이스를 `Controller.Url` 속성을 통해 다른 작업에 대한 URL을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="7041a-483">앱이 기본 기존 경로를 사용하는 경우 `url` 변수의 값은 URL `/UrlGeneration/Destination`경로 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="7041a-484">이 URL 경로는 다음을 결합하여 라우팅하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="7041a-485">현재 요청의 경로 값을 **앰비언트 값이라고**합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="7041a-486">이러한 값을 `Url.Action` 경로 템플릿으로 전달하고 대체하는 값은 다음과 같은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="7041a-487">경로 템플릿에 있는 각 경로 매개 변수의 값은 이름을 값 및 앰비언트 값과 매칭한 값으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="7041a-488">값이 없는 경로 매개 변수는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="7041a-489">기본값이 있는 경우 기본값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="7041a-490">선택 사항인 경우 건너뛸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-490">Be skipped if it's optional.</span></span> <span data-ttu-id="7041a-491">예를 들어, `id` 경로 템플릿에서 `{controller}/{action}/{id?}`.</span><span class="sxs-lookup"><span data-stu-id="7041a-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="7041a-492">필요한 경로 매개 변수에 해당 값이 없는 경우 URL 생성이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="7041a-493">경로에 대한 URL 생성이 실패하면 모든 경로를 시도하거나 일치 항목을 찾을 때까지 그 다음 경로를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="7041a-494">위의 예는 `Url.Action` 기존 [라우팅을](#cr)가정합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="7041a-495">URL 생성은 개념이 다르지만 [특성 라우팅과](#ar)유사하게 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="7041a-496">기존 라우팅:</span><span class="sxs-lookup"><span data-stu-id="7041a-496">With conventional routing:</span></span>

* <span data-ttu-id="7041a-497">경로 값은 템플릿을 확장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="7041a-498">경로 `controller` 값과 `action` 일반적으로 해당 템플릿에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="7041a-499">라우팅에 의해 일치하는 URL이 규칙을 준수하기 때문에 이 효과가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="7041a-500">다음 예제에서는 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="7041a-501">이전 `Source` 코드의 작업이 생성됩니다. `custom/url/to/destination`</span><span class="sxs-lookup"><span data-stu-id="7041a-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="7041a-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>ASP.NET 코어 3.0에 대한 대안으로 `IUrlHelper`추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="7041a-503">`LinkGenerator`유사하지만 보다 유연한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="7041a-504">on의 `IUrlHelper` 각 메서드에는 해당 `LinkGenerator` 메서드 패밀리도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="7041a-505">작업 이름으로 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7041a-505">Generating URLs by action name</span></span>

<span data-ttu-id="7041a-506">[Url.Action,](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)및 모든 관련 오버로드는 모두 컨트롤러 이름과 작업 이름을 지정하여 대상 끝점을 생성하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="7041a-507">을 `Url.Action`사용할 때 런타임에 대해 `controller` 현재 경로 값과 `action` 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="7041a-508">의 `controller` `action` 값과 [주변 값과](#ambient) 값의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="7041a-509">메서드는 `Url.Action` 항상 현재 `action` 값을 `controller` 사용 하 고 현재 작업에 라우팅 하는 URL 경로 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="7041a-510">라우팅은 주변 값의 값을 사용하여 URL을 생성할 때 제공되지 않은 정보를 채우려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="7041a-511">주변 값과 `{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }`같은 경로를 고려하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="7041a-512">라우팅에는 추가 값 없이 URL을 생성하기에 충분한 정보가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="7041a-513">라우팅에는 모든 경로 매개 변수에 값이 있기 때문에 충분한 정보가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="7041a-514">값이 `{ d = Donovan }` 추가되는 경우:</span><span class="sxs-lookup"><span data-stu-id="7041a-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="7041a-515">값은 `{ d = David }` 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="7041a-516">생성된 URL 경로는 . `Alice/Bob/Carol/Donovan`</span><span class="sxs-lookup"><span data-stu-id="7041a-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="7041a-517">**경고**: URL 경로는 계층적입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-517">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="7041a-518">앞의 예제에서 값이 `{ c = Cheryl }` 추가된 경우:</span><span class="sxs-lookup"><span data-stu-id="7041a-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="7041a-519">두 값 `{ c = Carol, d = David }` 모두 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="7041a-520">더 이상 값이 `d` 없으며 URL 생성이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="7041a-521">URL을 생성하려면 원하는 `c` 값과 `d` 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="7041a-522">기본 경로에서 `{controller}/{action}/{id?}`이 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="7041a-523">항상 `Url.Action` `controller` 명시적으로 a 및 `action` 값을 지정하기 때문에 이 문제는 실제로 는 드뭅니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="7041a-524">[Url.Action의](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) 여러 오버로드는 경로 값 개체를 사용하여 및 `controller` `action`를 제외한 경로 매개 변수에 대한 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="7041a-525">경로 값 개체는 `id`에서 자주 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="7041a-526">`Url.Action("Buy", "Products", new { id = 17 })`)을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="7041a-527">경로값 개체:</span><span class="sxs-lookup"><span data-stu-id="7041a-527">The route values object:</span></span>

* <span data-ttu-id="7041a-528">규칙에 의하면 일반적으로 익명 형식의 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="7041a-529">`IDictionary<>` 또는 [POCO)일](https://wikipedia.org/wiki/Plain_old_CLR_object)수 있다.</span><span class="sxs-lookup"><span data-stu-id="7041a-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="7041a-530">경로 매개 변수와 일치하지 않는 추가 경로 값들은 쿼리 문자열에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="7041a-531">앞의 코드는 `/Products/Buy/17?color=red`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="7041a-532">다음 코드는 절대 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="7041a-533">절대 URL을 만들려면 다음 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="7041a-534">을 허용하는 오버로드입니다. `protocol`</span><span class="sxs-lookup"><span data-stu-id="7041a-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="7041a-535">예를 들어, 앞의 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-535">For example, the preceding code.</span></span>
* <span data-ttu-id="7041a-536">[링크 제너레이터.GetUriByAction,](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)기본적으로 절대 URI를 생성.</span><span class="sxs-lookup"><span data-stu-id="7041a-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="7041a-537">경로별 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7041a-537">Generate URLs by route</span></span>

<span data-ttu-id="7041a-538">앞의 코드는 컨트롤러 및 작업 이름을 전달하여 URL을 생성하는 것을 보여 주었으며, 이는 다음과 같은 설명입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="7041a-539">`IUrlHelper`또한 [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) 메서드 제품군을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="7041a-540">이러한 메서드는 [Url.Action과](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)유사하지만 현재 `action` 값과 `controller` 경로 값을 복사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="7041a-541">가장 일반적인 용도 `Url.RouteUrl`:</span><span class="sxs-lookup"><span data-stu-id="7041a-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="7041a-542">URL을 생성할 경로 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="7041a-543">일반적으로 컨트롤러 또는 작업 이름을 지정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="7041a-544">다음 Razor 파일은 다음에 대한 `Destination_Route`HTML 링크를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="7041a-545">HTML 및 면도기에서 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7041a-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="7041a-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> `<form>` 에서는 각각 생성 및 `<a>` 요소를 생성하는 [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) 및 [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="7041a-547">이러한 메서드는 [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) 메서드를 사용하여 URL을 생성하고 유사한 인수를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="7041a-548">`HtmlHelper`에 대한 `Url.RouteUrl` 보조 도구는 `Html.BeginRouteForm` 및 `Html.RouteLink`이며 서로 기능이 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="7041a-549">TagHelper는 `form` TagHelper 및 `<a>` TagHelper를 통해 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="7041a-550">둘 다 구현에 `IUrlHelper`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="7041a-551">자세한 내용은 [양식의 도움말 태그를](xref:mvc/views/working-with-forms) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="7041a-552">보기 내에서 `IUrlHelper`는 `Url` 속성을 통해서 위에서 다루지 않은 임시 URL을 생성하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="7041a-553">작업 결과의 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7041a-553">URL generation in Action Results</span></span>

<span data-ttu-id="7041a-554">위의 예제는 컨트롤러에서 `IUrlHelper` 사용 하 여 보여 주었다.</span><span class="sxs-lookup"><span data-stu-id="7041a-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="7041a-555">컨트롤러에서 가장 일반적으로 사용하는 방법은 작업 결과의 일부로 URL을 생성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="7041a-556"><xref:Microsoft.AspNetCore.Mvc.ControllerBase> 및 <xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스는 다른 작업을 참조하는 작업 결과에 대한 편의 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="7041a-557">한 가지 일반적인 용도는 사용자 입력을 수락한 후 리디렉션하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="7041a-558">이 작업은 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> `IUrlHelper`의 메서드와 유사한 패턴을 따르고 와 같은 팩터리 메서드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="7041a-559">전용 규칙 기반 경로의 특별한 사례</span><span class="sxs-lookup"><span data-stu-id="7041a-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="7041a-560">[종래의 라우팅은](#cr) [전용 종래의](#dcr)경로라고 하는 특별한 종류의 경로 정의를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="7041a-561">다음 예제에서 명명된 `blog` 경로는 전용 기존 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="7041a-562">앞의 경로 정의를 사용하여 `Url.Action("Index", "Home")` `default` 경로를 사용하여 `/` URL 경로를 생성하지만 왜 발생합니까?</span><span class="sxs-lookup"><span data-stu-id="7041a-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="7041a-563">`{ controller = Home, action = Index }` 경로 값이면 `blog`를 사용하여 URL을 생성하기에 충분하며, 그 결과는 `/blog?action=Index&controller=Home`가 될 것이라고 추측할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="7041a-564">[전용 기존 경로는](#dcr) URL 생성시 경로가 너무 [욕심을](xref:fundamentals/routing#greedy) 내지 못하도록 하는 해당 경로 매개 변수가 없는 기본값의 특수 한 동작에 의존 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="7041a-565">이 예제에서 기본값은 `{ controller = Blog, action = Article }`이고, `controller` 및 `action`이 경로 매개 변수로 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="7041a-566">라우팅이 URL 생성을 수행할 때 입력한 값이 기본값과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="7041a-567">값이 `blog` `{ controller = Home, action = Index }` 일치하지 `{ controller = Blog, action = Article }`않기 때문에 URL 생성이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="7041a-568">그 후 라우팅이 `default`로 대체되고, 이것은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="7041a-569">Areas</span><span class="sxs-lookup"><span data-stu-id="7041a-569">Areas</span></span>

<span data-ttu-id="7041a-570">[영역은](xref:mvc/controllers/areas) 관련 기능을 그룹으로 구성하는 데 사용되는 MVC 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="7041a-571">컨트롤러 작업에 대한 네임스페이스 라우팅</span><span class="sxs-lookup"><span data-stu-id="7041a-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="7041a-572">뷰에 대한 폴더 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-572">Folder structure for views.</span></span>

<span data-ttu-id="7041a-573">영역을 사용하면 앱에 서로 다른 영역이 있는 한 이름이 같은 여러 컨트롤러를 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="7041a-574">영역을 사용하면 또 다른 경로 매개 변수 `area`를 `controller` 및 `action`에 추가하여 라우팅을 위한 계층 구조를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="7041a-575">이 섹션에서는 라우팅이 영역과 상호 작용하는 방법에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="7041a-576">[뷰에서 영역을](xref:mvc/controllers/areas) 사용하는 방법에 대한 자세한 내용은 영역을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7041a-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="7041a-577">다음 예제는 MVC를 구성하여 기본 기존 `area` 경로와 `area` `Blog`명명된 경로에 대해 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="7041a-578">앞의 코드에서는 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> `"blog_route"`을 만들기 위해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="7041a-579">두 번째 `"Blog"`매개 변수는 영역 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="7041a-580">와 같은 `/Manage/Users/AddUser`URL 경로를 `"blog_route"` 일치하면 경로가 `{ area = Blog, controller = Users, action = AddUser }`경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="7041a-581">경로 `area` 값은 에 대한 `area`기본값으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="7041a-582">생성된 `MapAreaControllerRoute` 경로는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="7041a-583">`MapAreaControllerRoute`는 제공된 영역 이름(여기에서는 `Blog`)을 사용하는 `area`에 대해 기본값 및 제약 조건을 모두 사용하여 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="7041a-584">기본값은 경로가 항상 `{ area = Blog, ... }`를 생성함을 보장하고, 제약 조건은 URL 생성을 위해 `{ area = Blog, ... }` 값을 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="7041a-585">규칙 기반 라우팅은 순서에 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7041a-586">일반적으로 영역이 있는 경로는 영역이 없는 경로보다 더 구체적이므로 더 일찍 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="7041a-587">앞의 예제를 사용하여 경로 `{ area = Blog, controller = Users, action = AddUser }` 값은 다음 작업과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="7041a-588">[[영역]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 특성은 영역의 일부로 컨트롤러를 나타내는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="7041a-589">이 컨트롤러가 `Blog` 해당 영역에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="7041a-590">특성이 `[Area]` 없는 컨트롤러는 모든 영역의 구성원이 아니며 라우팅을 통해 `area` 경로 값을 제공할 때 일치하지 **않습니다.**</span><span class="sxs-lookup"><span data-stu-id="7041a-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="7041a-591">다음 예제에서는 나열된 첫 번째 컨트롤러만 `{ area = Blog, controller = Users, action = AddUser }` 경로 값과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="7041a-592">완전성을 위해 각 컨트롤러의 네임스페이스가 여기에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="7041a-593">앞의 컨트롤러가 동일한 네임스페이스를 사용하는 경우 컴파일러 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="7041a-594">클래스 네임스페이스는 MVC의 라우팅에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="7041a-595">처음 두 컨트롤러는 영역의 구성원이며, `area` 경로 값으로 해당 영역 이름을 제공하는 경우에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="7041a-596">세 번째 컨트롤러는 어떤 영역의 구성원도 아니며, 라우팅에서 `area`에 대한 값을 제공하지 않을 때에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="7041a-597">매칭 측면에서 *값 없음*, 즉 `area` 값이 없는 것은 `area`의 값이 null 또는 빈 문자열인 경우와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-597">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="7041a-598">영역 내에서 작업을 실행하는 경우 경로 `area` 값은 URL 생성에 사용할 라우팅에 대한 주변 [값으로](#ambient) 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="7041a-599">즉, 기본적으로 영역은 다음 예제에서 볼 수 있듯이 URL 생성을 위한 *스티커처럼* 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="7041a-600">다음 코드는 다음에 `/Zebra/Users/AddUser`대한 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="7041a-601">작업 정의</span><span class="sxs-lookup"><span data-stu-id="7041a-601">Action definition</span></span>

<span data-ttu-id="7041a-602">[비동작](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) 특성을 제외한 컨트롤러의 공용 메서드는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="7041a-603">예제 코드</span><span class="sxs-lookup"><span data-stu-id="7041a-603">Sample code</span></span>

 * <span data-ttu-id="7041a-604">[MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) 메서드는 샘플 [다운로드에](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) 포함 되며 라우팅 정보를 표시 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-604">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="7041a-605">[샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) [방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="7041a-605">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7041a-606">ASP.NET Core MVC는 라우팅 [미들웨어](xref:fundamentals/middleware/index)를 사용하여 들어오는 요청의 URL을 매칭하고 작업에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-606">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="7041a-607">경로는 시작 코드 또는 특성에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-607">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="7041a-608">경로는 URL 경로를 작업에 매칭하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-608">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="7041a-609">경로는 응답으로 전송되는 URL(링크 용)을 생성하기 위해서도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-609">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="7041a-610">작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-610">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7041a-611">컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-611">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7041a-612">자세한 내용은 [혼합 라우팅](#routing-mixed-ref-label)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-612">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="7041a-613">이 항목에서는 MVC와 라우팅 간의 상호 작용 및 일반적인 MVC 앱이 라우팅 기능을 사용하는 방식에 관해서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-613">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="7041a-614">고급 라우팅에 대한 자세한 내용은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-614">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="7041a-615">라우팅 미들웨어 설정</span><span class="sxs-lookup"><span data-stu-id="7041a-615">Setting up Routing Middleware</span></span>

<span data-ttu-id="7041a-616">*Configure* 메서드에서 다음과 비슷한 코드를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-616">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7041a-617">`UseMvc` 호출 내부에서 `MapRoute`를 사용하여 `default` 경로라고 부르는 단일 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-617">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="7041a-618">대부분의 MVC 앱은 `default` 경로와 비슷한 템플릿을 갖는 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-618">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="7041a-619">`"{controller=Home}/{action=Index}/{id?}"` 경로 템플릿은 `/Products/Details/5`와 같은 URL 경로를 매칭하고 경로를 토큰화하여 `{ controller = Products, action = Details, id = 5 }` 경로 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-619">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="7041a-620">MVC는 `ProductsController`라는 컨트롤러를 찾아 `Details` 작업을 실행하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-620">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="7041a-621">이 예제에서 이 작업을 호출할 때 모델 바인딩이 `id = 5` 값을 사용하여 `id` 매개 변수를 `5`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-621">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="7041a-622">자세한 내용은 [모델 바인딩](../models/model-binding.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-622">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="7041a-623">`default` 경로를 사용하여:</span><span class="sxs-lookup"><span data-stu-id="7041a-623">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="7041a-624">경로 템플릿의:</span><span class="sxs-lookup"><span data-stu-id="7041a-624">The route template:</span></span>

* <span data-ttu-id="7041a-625">`{controller=Home}`는 `Home`를 기본 `controller`으로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-625">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="7041a-626">`{action=Index}`는 `Index`를 기본 `action`으로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-626">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="7041a-627">`{id?}`는 `id`를 선택 사항으로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-627">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="7041a-628">기본 및 선택적 경로 매개 변수는 매칭을 위해 URL 경로에 반드시 있어야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-628">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="7041a-629">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-629">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="7041a-630">`"{controller=Home}/{action=Index}/{id?}"`는 URL 경로 `/`를 매칭할 수 있으며 `{ controller = Home, action = Index }` 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-630">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="7041a-631">`controller` 및 `action` 값으로 기본값이 사용되며, `id`는 URL 경로에 해당 세그먼트가 없기 때문에 값을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-631">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="7041a-632">MVC는 이러한 경로 값을 사용하여 `HomeController` 및 `Index` 작업을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-632">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="7041a-633">이러한 컨트롤러 정의와 경로 템플릿을 사용하면 다음 URL 경로에 대해 `HomeController.Index` 작업이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-633">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="7041a-634">편의 메서드인 `UseMvcWithDefaultRoute`를 사용하여:</span><span class="sxs-lookup"><span data-stu-id="7041a-634">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="7041a-635">다음을 대체할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-635">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7041a-636">`UseMvc` 및 `UseMvcWithDefaultRoute`는 미들웨어 파이프라인에 `RouterMiddleware` 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-636">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="7041a-637">MVC는 미들웨어와 직접 상호 작용하지 않고 라우팅을 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-637">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="7041a-638">MVC는 `MvcRouteHandler`의 인스턴스를 통해 경로에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-638">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="7041a-639">`UseMvc` 내부의 코드는 다음과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-639">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="7041a-640">`UseMvc`는 아무런 경로도 직접 정의하지 않고 경로 컬렉션에 `attribute` 경로에 대한 자리 표시자만 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-640">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="7041a-641">`UseMvc(Action<IRouteBuilder>)` 오버로드를 사용하면 고유의 경로를 추가하고 특성 라우팅도 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-641">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="7041a-642">`UseMvc` 및 모든 변형은 특성 경로에 대한 자리 표시자를 추가합니다. 특성 라우팅은 `UseMvc`를 구성하는 방법에 관계없이 항상 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-642">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="7041a-643">`UseMvcWithDefaultRoute`는 기본 경로를 정의하고 특성 라우팅을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-643">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="7041a-644">[특성 라우팅](#attribute-routing-ref-label) 섹션에는 특성 라우팅에 대한 자세한 내용이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-644">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="7041a-645">규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="7041a-645">Conventional routing</span></span>

<span data-ttu-id="7041a-646">`default` 경로인:</span><span class="sxs-lookup"><span data-stu-id="7041a-646">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="7041a-647">앞의 코드는 기존 라우팅의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-647">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="7041a-648">이 스타일은 URL 경로에 대한 *규칙을* 설정하기 때문에 기존 라우팅이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-648">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="7041a-649">첫 번째 경로 세그먼트는 컨트롤러 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-649">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="7041a-650">두 번째 는 작업 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-650">The second maps to the action name.</span></span>
* <span data-ttu-id="7041a-651">세 번째 세그먼트는 선택적 `id`으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-651">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="7041a-652">`id`모델 엔터티에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-652">`id` maps to a model entity.</span></span>

<span data-ttu-id="7041a-653">이 `default` 경로를 사용하면 URL 경로 `/Products/List`는 `ProductsController.List` 작업에 매핑되고 `/Blog/Article/17`은 `BlogController.Article`에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-653">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="7041a-654">매핑은 **오직** 컨트롤러 및 작업 이름만을 기준으로 하며 네임스페이스, 원본 파일 위치 또는 메서드 매개 변수를 기준으로 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-654">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="7041a-655">기본 경로와 함께 규칙 기반 라우팅을 사용하면 정의하는 각 작업마다 새 URL 패턴을 만들지 않고도 신속하게 애플리케이션을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-655">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="7041a-656">CRUD 스타일의 작업을 수행하는 애플리케이션의 경우 컨트롤러 전반에서 URL을 일관적으로 유지하면 코드를 단순화하고 UI의 예측 가능성을 높이는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-656">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="7041a-657">`id`는 경로 템플릿에서 선택 사항으로 정의됩니다. 즉, URL의 일부로 제공되는 ID 없이도 작업을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-657">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="7041a-658">일반적으로 URL에서 `id`가 생략되면 모델 바인딩에 의해 `0`으로 설정되고, 그 결과 데이터베이스에서 `id == 0`과 일치하는 엔터티를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-658">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="7041a-659">특성 라우팅을 사용하면 일부 작업에는 필요하고 다른 작업에는 필요하지 않은 ID를 만들기 위해 세밀하게 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-659">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="7041a-660">일반적으로 `id` 같은 선택적 매개 변수가 올바른 사용법으로 나타날 가능성이 있는 경우 설명서에 이러한 선택적 매개 변수가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-660">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="7041a-661">여러 경로</span><span class="sxs-lookup"><span data-stu-id="7041a-661">Multiple routes</span></span>

<span data-ttu-id="7041a-662">`MapRoute`에 대한 더 많은 호출을 추가하여 `UseMvc` 내부에서 여러 경로를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-662">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="7041a-663">이렇게 하면 여러 규칙을 정의하거나 다음과 같이 특정 작업에만 사용되는 규칙 기반 경로를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-663">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7041a-664">여기서 `blog` 경로는 규칙 기반 라우팅 시스템을 사용하지만 특정 작업에만 활용되는 *전용 규칙 기반 경로*입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-664">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="7041a-665">`controller` 및 `action`이 경로 템플릿에 매개 변수로 표시되지 않기 때문에 기본값만 가질 수 있으며, 따라서 이 경로는 항상 `BlogController.Article` 작업에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-665">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="7041a-666">경로 컬렉션의 경로는 순서대로 정렬되며 추가된 순서대로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-666">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="7041a-667">따라서 이 예제의 `blog` 경로는 `default` 경로보다 먼저 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-667">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="7041a-668">*전용 기존 경로는* 종종 URL 경로의 나머지 부분을 캡처하는 것과 같은 `{*article}` **포괄** 경로 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-668">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="7041a-669">이 경우 경로가 '너무 많은 욕심'을 부리게 됩니다. 즉, 다른 경로와 매칭하려는 URL과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-669">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="7041a-670">이 '욕심 많은' 경로를 경로 테이블의 뒷부분에 배치하면 이 문제를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-670">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="7041a-671">대체</span><span class="sxs-lookup"><span data-stu-id="7041a-671">Fallback</span></span>

<span data-ttu-id="7041a-672">요청 처리의 일부로, MVC는 경로 값을 사용하여 애플리케이션의 컨트롤러 및 작업을 찾을 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-672">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="7041a-673">경로 값이 작업과 일치하지 않으면 해당 경로는 불일치하는 것으로 간주되고, 그 다음 경로를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-673">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="7041a-674">이것을 *대체*라고 부르며, 규칙 기반 경로가 겹치는 상황을 단순화하는 것이 목적입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-674">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="7041a-675">명확한 작업 구분</span><span class="sxs-lookup"><span data-stu-id="7041a-675">Disambiguating actions</span></span>

<span data-ttu-id="7041a-676">두 작업이 라우팅을 통해 일치하는 경우 MVC는 작업을 명확히 구분하여 '최적의' 후보를 선택해야 하며, 그렇지 못하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-676">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="7041a-677">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-677">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="7041a-678">이 컨트롤러는 URL 경로 `/Products/Edit/17` 및 경로 데이터 `{ controller = Products, action = Edit, id = 17 }`과 일치하는 두 작업을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-678">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="7041a-679">이는 MVC 컨트롤러의 일반적인 패턴으로, `Edit(int)`는 제품을 편집할 양식을 보여주고 `Edit(int, Product)`는 게시된 양식을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-679">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="7041a-680">이것이 가능하려면 MVC가 요청이 HTTP `POST`이면 `Edit(int, Product)`를 선택해야 하고, HTTP 동사가 그 외의 다른 것이면 `Edit(int)`를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-680">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="7041a-681">`HttpPostAttribute`(`[HttpPost]`)는 HTTP 동사가 `POST`인 경우에만 작업을 선택하는 것이 가능한 `IActionConstraint`의 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-681">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="7041a-682">`IActionConstraint`가 존재하면 `Edit(int, Product)`가 `Edit(int)`보다 '더 정확하게' 일치하므로 `Edit(int, Product)`를 가장 먼저 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-682">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="7041a-683">개발자는 특별한 시나리오의 사용자 지정 `IActionConstraint` 구현을 작성하기만 하면 되지만, `HttpPostAttribute` 같은 특성 역할을 이해하는 것이 중요합니다. 다른 HTTP 동사에 대해 비슷한 특성이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-683">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="7041a-684">규칙 기반 라우팅에서는 작업이 `show form -> submit form` 워크플로의 일부인 경우 작업에서 동일한 작업을 사용하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-684">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="7041a-685">[IActionConstraint 이해](#understanding-iactionconstraint) 섹션을 검토하시면 이 패턴의 편리함을 보다 명확하게 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-685">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="7041a-686">여러 경로가 일치하고 MVC가 '최적의' 경로를 찾을 수 없는 경우 MVC는 `AmbiguousActionException`을 던집니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-686">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="7041a-687">경로 이름</span><span class="sxs-lookup"><span data-stu-id="7041a-687">Route names</span></span>

<span data-ttu-id="7041a-688">다음 예제의 `"blog"` 및 `"default"` 문자열은 경로 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-688">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7041a-689">경로 이름은 명명된 경로를 URL 생성에 사용할 수 있도록 경로에 논리 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-689">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="7041a-690">이렇게 하면 경로 순서 지정으로 인해 URL 생성이 복잡해질 수 있는 상황에서 URL 생성 방법이 매우 간단해집니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-690">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="7041a-691">경로 이름은 애플리케이션 전체에서 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-691">Route names must be unique application-wide.</span></span>

<span data-ttu-id="7041a-692">경로 이름은 URL 일치 또는 요청 처리에 영향을 미치지 않으며 URL 생성에서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-692">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="7041a-693">[라우팅](xref:fundamentals/routing)은 MVC 관련 도우미에서 URL 생성을 포함하여 URL 생성에 대한 보다 자세한 정보를 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-693">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="7041a-694">특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="7041a-694">Attribute routing</span></span>

<span data-ttu-id="7041a-695">특성 라우팅은 특성 모음을 사용하여 작업을 경로 템플릿에 직접 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-695">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="7041a-696">다음 예제에서는 `Configure` 메서드에서 `app.UseMvc();`가 사용되어 경로가 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-696">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="7041a-697">`HomeController`는 기본 경로 `{controller=Home}/{action=Index}/{id?}`의 일치 항목과 비슷한 URL 집합과 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-697">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="7041a-698">`HomeController.Index()` 작업은 URL 경로 `/`, `/Home` 또는 `/Home/Index`에 대해서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-698">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="7041a-699">이 예제에서는 특성 라우팅과 규칙 기반 라우팅 간의 주요 프로그래밍 차이점을 강조합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-699">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="7041a-700">특성 라우팅은 경로를 지정하기 위해 더 많은 입력이 필요하고, 규칙 기반 기본 경로는 경로를 보다 간결하게 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-700">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="7041a-701">그러나 특성 라우팅을 사용하면 각 작업에 적용되는 경로 템플릿을 정확하게 제어할 수 있습니다(또 그래야만 합니다).</span><span class="sxs-lookup"><span data-stu-id="7041a-701">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="7041a-702">특성 라우팅을 사용하면 컨트롤러 이름 및 작업 이름은 작업 선택에 있어서 아무 역할도 하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="7041a-702">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="7041a-703">이 예제는 이전 예제와 동일한 URL을 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-703">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="7041a-704">위의 경로 템플릿은 `action`, `area` 및 `controller`에 대한 경로 매개 변수를 정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-704">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="7041a-705">사실, 이러한 경로 매개 변수는 특성 경로에 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-705">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="7041a-706">경로 템플릿은 이미 작업에 연결되어 있기 때문에 URL에서 작업 이름을 구문 분석할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-706">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="7041a-707">Http[Verb] 특성을 사용한 특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="7041a-707">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="7041a-708">특성 라우팅은 `HttpPostAttribute` 같은 `Http[Verb]`를 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-708">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="7041a-709">이러한 특성은 모두 경로 템플릿을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-709">All of these attributes can accept a route template.</span></span> <span data-ttu-id="7041a-710">이 예제는 동일한 경로 템플릿과 일치하는 두 가지 작업을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-710">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="7041a-711">`/products` 같은 URL 경로의 경우 HTTP 동사가 `GET`이면 `ProductsApi.ListProducts` 작업이 실행되고 HTTP 동사가 `POST`이면 `ProductsApi.CreateProduct`가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-711">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="7041a-712">특성 라우팅은 먼저 URL을 경로 특성에 정의된 경로 템플릿 집합과 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-712">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="7041a-713">경로 템플릿이 일치하면 `IActionConstraint` 제약 조건을 적용하여 실행 가능한 작업을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-713">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="7041a-714">REST API를 빌드할 때 작업 메서드에 `[Route(...)]`를 사용하려는 경우는 거의 없습니다. 작업이 모든 HTTP 메서드를 받기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-714">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="7041a-715">보다 구체적인 `Http*Verb*Attributes`를 사용하여 API에서 지원하는 항목을 정확하게 지정하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-715">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="7041a-716">REST API의 클라이언트는 특정 논리 작업에 어떤 경로 및 HTTP 동사가 매핑되는지 알아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-716">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="7041a-717">특성 경로는 특정 작업에 적용되므로 경로 템플릿 정의의 일환으로 필요한 매개 변수를 간단하게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-717">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="7041a-718">이 예제에서 `id`는 URL 경로에 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-718">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7041a-719">`ProductsApi.GetProduct(int)` 작업은 `/products/3` 같은 URL 경로에 대해 실행되지만 `/products` 같은 URL 경로에 대해서는 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-719">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="7041a-720">경로 템플릿 및 관련 옵션에 대한 전체 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-720">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="7041a-721">경로 이름</span><span class="sxs-lookup"><span data-stu-id="7041a-721">Route Name</span></span>

<span data-ttu-id="7041a-722">다음 코드는 `Products_List`의 *경로 이름*을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-722">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7041a-723">경로 이름을 사용하여 특정 경로 기반의 URL을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-723">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="7041a-724">경로 이름은 라우팅의 URL 매칭 동작에 영향을 미치지 않으며 URL 생성에만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-724">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="7041a-725">경로 이름은 애플리케이션 전체에서 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-725">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="7041a-726">`id` 매개 변수를 선택 사항(`{id?}`)으로 정의하는 규칙 기반 *기본 경로*와는 대조적입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-726">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="7041a-727">API를 정확하게 지정하는 이 기능은 `/products`와 `/products/5`를 서로 다른 작업에 디스패치할 수 있는 등의 장점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-727">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="7041a-728">경로 결합</span><span class="sxs-lookup"><span data-stu-id="7041a-728">Combining routes</span></span>

<span data-ttu-id="7041a-729">특성 라우팅의 반복 횟수를 줄이기 위해 컨트롤러의 경로 특성은 개별 작업의 경로 특성과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-729">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="7041a-730">컨트롤러에 정의된 경로 템플릿은 작업의 경로 템플릿에 앞에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-730">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="7041a-731">경로 특성을 컨트롤러에 배치하면 컨트롤러의 **모든** 작업이 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-731">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7041a-732">이 예제에서 URL 경로 `/products`는 `ProductsApi.ListProducts`와 매칭할 수 있고, URL 경로 `/products/5`는 `ProductsApi.GetProduct(int)`와 매칭할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-732">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="7041a-733">두 작업은 모두 `HttpGetAttribute`로 표시되기 때문에 HTTP `GET`만 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-733">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="7041a-734">작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-734">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="7041a-735">이 예제는 *기본 경로*와 비슷한 URL 경로 집합을 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-735">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="7041a-736">특성 경로 순서 지정</span><span class="sxs-lookup"><span data-stu-id="7041a-736">Ordering attribute routes</span></span>

<span data-ttu-id="7041a-737">정의된 순서로 실행되는 기존 경로와 달리 특성 라우팅은 트리를 빌드하고 모든 경로를 동시에 일치시다.</span><span class="sxs-lookup"><span data-stu-id="7041a-737">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="7041a-738">이 동작은 경로 전체가 이상적인 순서로 정렬된 것처럼 수행됩니다. 가장 구체적인 경로가 일반적인 경로보다 먼저 실행될 가능성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-738">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="7041a-739">예를 들어 `blog/search/{topic}` 같은 경로는 `blog/{*article}` 같은 경로보다 구체적입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-739">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="7041a-740">논리적으로 말해서, 기본적으로 `blog/search/{topic}` 경로가 가장 먼저 '실행'됩니다. 유일하게 합리적인 순서이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-740">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="7041a-741">규칙 기반 라우팅을 사용하면 개발자가 원하는 순서대로 경로를 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-741">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="7041a-742">특성 경로는 프레임워크가 제공하는 모든 경로 특성의 `Order` 속성을 사용하여 순서를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-742">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="7041a-743">경로는 `Order` 속성의 오름차순 정렬에 따라 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-743">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="7041a-744">기본 순서는 `0`입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-744">The default order is `0`.</span></span> <span data-ttu-id="7041a-745">`Order = -1`을 사용한 경로 설정은 순서를 설정하지 않는 경로보다 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-745">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="7041a-746">`Order = 1`을 사용한 경로 설정은 기본 경로 순서보다 늦게 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-746">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="7041a-747">`Order`를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-747">Avoid depending on `Order`.</span></span> <span data-ttu-id="7041a-748">URL 공간에 올바른 라우팅을 위한 명시적 순서 값이 필요한 경우 클라이언트에서도 혼란이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-748">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="7041a-749">일반적으로 특성 라우팅은 URL이 일치하는 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-749">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="7041a-750">URL 생성에 사용되는 기본 순서가 작동하지 않는 경우 일반적으로 경로 이름을 재정의로 사용하는 것이 `Order` 속성을 적용하는 것보다 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-750">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="7041a-751">Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-751">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="7041a-752">Razor Pages 항목의 경로 순서에 대한 정보는 [Razor Pages 라우팅 및 앱 규칙: 경로 순서](xref:razor-pages/razor-pages-conventions#route-order)를 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-752">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="7041a-753">경로 템플릿에서 토큰 바꾸기([컨트롤러] [작업] [지역])</span><span class="sxs-lookup"><span data-stu-id="7041a-753">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="7041a-754">편의를 위해 특성 경로는 토큰을 사각형 중괄호`[`(, `]`)로 둘러싸서 *토큰 교체를* 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-754">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="7041a-755">`[action]`, `[area]` 및 `[controller]` 토큰은 경로가 정의된 작업의 작업 이름, 영역 이름 및 컨트롤러 이름으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-755">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="7041a-756">다음 예의 작업은 주석에 설명된 대로 URL 경로를 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-756">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="7041a-757">토큰 교체는 특성 경로 빌드 과정의 마지막 단계로 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-757">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="7041a-758">위의 예제는 다음 코드와 동일하게 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-758">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="7041a-759">특성 경로를 상속과 결합할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-759">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="7041a-760">특히 토큰 교체와 결합하면 더욱 강력합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-760">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="7041a-761">토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-761">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="7041a-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]`는 각 작업의 고유한 경로 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="7041a-763">리터럴 토큰 교체 구분 기호 `[` 또는 `]`와 매칭하려면 문자(`[[` 또는 `]]`)를 반복하여 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-763">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="7041a-764">매개 변수 변환기를 사용하여 토큰 교체 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="7041a-764">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="7041a-765">매개 변수 변환기를 사용하여 토큰 교체를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-765">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="7041a-766">매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하며 매개 변수의 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-766">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="7041a-767">예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-767">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="7041a-768">`RouteTokenTransformerConvention`은 다음과 같은 응용 프로그램 모델 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-768">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="7041a-769">애플리케이션의 모든 특성 경로에 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-769">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="7041a-770">대체되는 특성 경로 토큰 값을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-770">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="7041a-771">`RouteTokenTransformerConvention`은 `ConfigureServices`에 옵션으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-771">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="7041a-772">여러 경로</span><span class="sxs-lookup"><span data-stu-id="7041a-772">Multiple Routes</span></span>

<span data-ttu-id="7041a-773">특성 경로는 동일한 작업에 도달하는 여러 경로를 정의하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-773">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="7041a-774">가장 일반적인 사용 방법은 다음 예제와 같이 *기본 규칙 기반 경로*의 동작을 모방하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-774">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="7041a-775">컨트롤러에 여러 경로 특성을 배치하면 각 경로 특성이 작업 메서드의 각 경로 특성과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-775">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="7041a-776">여러 경로 특성(`IActionConstraint`를 구현하는)이 작업에 배치되면 각 작업 제약 조건은 제약 조건을 정의한 특성의 경로 템플릿과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-776">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="7041a-777">작업에 여러 경로를 사용하는 것이 좋아 보일 수 있지만, 애플리케이션의 URL 공간을 간단하고 잘 정의된 상태로 유지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-777">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="7041a-778">기존 클라이언트를 지원하려는 경우처럼 꼭 필요한 경우에만 작업에 여러 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-778">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="7041a-779">특성 경로 선택적 매개 변수, 기본값 및 제약 조건 지정</span><span class="sxs-lookup"><span data-stu-id="7041a-779">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="7041a-780">특성 경로는 선택적 매개 변수, 기본값 및 제약 조건을 지정할 수 있는 규칙 기반 경로와 동일한 인라인 구문을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-780">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="7041a-781">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-781">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="7041a-782">`IRouteTemplateProvider`를 사용하는 사용자 지정 경로 특성</span><span class="sxs-lookup"><span data-stu-id="7041a-782">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="7041a-783">프레임워크에서 제공하는 모든 경로 특성(`[Route(...)]`, `[HttpGet(...)]` 등)은 `IRouteTemplateProvider` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-783">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="7041a-784">앱이 시작되면 MVC는 컨트롤러 클래스 및 작업 메서드에서 특성을 찾아 `IRouteTemplateProvider`를 구현하는 특성을 사용하여 초기 경로 집합을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-784">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="7041a-785">개발자 고유의 경로 특성을 정의하는 `IRouteTemplateProvider`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-785">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="7041a-786">각각의 `IRouteTemplateProvider`를 사용하여 사용자 지정 경로 템플릿, 순서 및 이름으로 단일 경로를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-786">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="7041a-787">위의 예제의 특성은 `[MyApiController]`가 적용되면 자동으로 `Template`을 `"api/[controller]"`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-787">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="7041a-788">응용 프로그램 모델을 사용하여 특성 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="7041a-788">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="7041a-789">*응용 프로그램 모델*은 MVC에 의해서 작업을 라우팅하고 실행하는 데 사용되는 모든 메타데이터를 이용하여 시작 시 만들어지는 개체 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-789">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="7041a-790">*응용 프로그램 모델*은 `IRouteTemplateProvider`를 통해서 경로 특성으로부터 수집한 모든 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-790">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="7041a-791">개발자는 시작 시에 응용 프로그램 모델을 수정하는 *규칙*을 작성하여 라우팅 동작을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-791">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="7041a-792">이 섹션에서는 애플리케이션 모델을 사용하여 라우팅을 사용자 지정하는 간단한 예를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-792">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="7041a-793">혼합 라우팅: 특성 라우팅 및 규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="7041a-793">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="7041a-794">MVC 애플리케이션은 규칙 기반 라우팅과 특성 라우팅을 혼합해서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-794">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="7041a-795">일반적으로 브라우저의 HTML 페이지를 처리하는 컨트롤러에는 규칙 기반 경로를 사용하고 REST API를 제공하는 컨트롤러에는 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-795">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="7041a-796">작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-796">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7041a-797">컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-797">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7041a-798">특성 경로를 정의하는 동작은 규칙 기반 경로를 통해 도달할 수 없으며 그 반대도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-798">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="7041a-799">컨트롤러의 **모든** 경로 특성은 컨트롤러에 있는 모든 작업에서 특성 라우팅을 사용하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-799">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="7041a-800">두 라우팅 시스템 간의 차이는 URL이 경로 템플릿과 일치한 후 적용되는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-800">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="7041a-801">규칙 기반 라우팅에서는 일치 항목의 경로 값을 사용하여 모든 규칙 기반 라우팅된 작업의 조회 테이블에서 작업 및 컨트롤러를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-801">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="7041a-802">특성 라우팅에서 각 템플릿은 이미 작업과 연결되어 있으며, 더 이상의 조회가 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-802">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="7041a-803">복잡한 세그먼트</span><span class="sxs-lookup"><span data-stu-id="7041a-803">Complex segments</span></span>

<span data-ttu-id="7041a-804">복잡한 세그먼트(예: `[Route("/dog{token}cat")]`)는 non-greedy 방식으로 오른쪽에서 왼쪽으로 리터럴을 매칭하여 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-804">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="7041a-805">자세한 내용은 [소스 코드](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-805">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="7041a-806">자세한 내용은 [이 문제](https://github.com/dotnet/AspNetCore.Docs/issues/8197)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-806">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="7041a-807">URL 생성</span><span class="sxs-lookup"><span data-stu-id="7041a-807">URL Generation</span></span>

<span data-ttu-id="7041a-808">MVC 애플리케이션은 라우팅의 URL 생성 기능을 사용하여 작업에 대한 URL 링크를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-808">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="7041a-809">URL을 생성하면 하드 코딩된 URL이 제거되어 코드를 더욱 견고하게 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-809">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="7041a-810">이 섹션에서는 MVC에서 제공하는 URL 생성 기능을 중심으로 기본적인 URL 생성 원리에 대해서만 다룰 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-810">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="7041a-811">URL 생성에 대한 자세한 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-811">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="7041a-812">`IUrlHelper` 인터페이스는 URL 생성을 위한 MVC와 라우팅 간의 기본 인프라입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-812">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="7041a-813">컨트롤러, 보기 및 보기 구성 요소의 `Url` 속성을 통해서 사용 가능한 `IUrlHelper`의 인스턴스를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-813">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="7041a-814">이 예제에서 `IUrlHelper` 인터페이스는 `Controller.Url` 속성을 통해 다른 작업에 대한 URL을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-814">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="7041a-815">응용 프로그램이 기본 규칙 기반 경로를 사용할 경우 `url` 변수의 값은 URL 경로 문자열 `/UrlGeneration/Destination`입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-815">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="7041a-816">이 URL 경로는 현재 요청의 경로 값(앰비언트 값)을 `Url.Action`에 전달된 값과 결합하고 그 값을 경로 템플릿에 대체하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-816">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="7041a-817">경로 템플릿에 있는 각 경로 매개 변수의 값은 이름을 값 및 앰비언트 값과 매칭한 값으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-817">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="7041a-818">값이 없는 경로 매개 변수는 기본값이 있는 경우 기본값을 사용할 수 있고, 값이 선택 사항인 경우 건너뛰어도 됩니다(이 예제의 `id`처럼).</span><span class="sxs-lookup"><span data-stu-id="7041a-818">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="7041a-819">필요한 경로 매개 변수에 해당 값이 없는 경우 URL 생성에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-819">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="7041a-820">경로에 대한 URL 생성이 실패하면 모든 경로를 시도하거나 일치 항목을 찾을 때까지 그 다음 경로를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-820">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="7041a-821">위의 `Url.Action`예제는 규칙 기반 라우팅을 가정합니다. 개념은 다르지만 URL 생성 원리는 특성 라우팅과 비슷하게 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-821">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="7041a-822">규칙 기반 라우팅에서 경로 값은 템플릿을 확장하는 데 사용되고, 일반적으로`controller` 및 `action`의 경로 값은 해당 템플릿에 표시됩니다. 라우팅에서 매핑된 URL이 *규칙*을 따르기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-822">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="7041a-823">특성 라우팅에서는 `controller` 및 `action`의 경로 값이 템플릿에 표시되지 않는 대신, 사용할 템플릿을 조회하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-823">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="7041a-824">이 예제는 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-824">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="7041a-825">MVC는 모든 특성 라우팅 작업의 조회 테이블을 작성하고 `controller` 및 `action` 값을 매칭하여 URL 생성에 사용할 경로 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-825">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="7041a-826">위의 예제에서는 `custom/url/to/destination`이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-826">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="7041a-827">작업 이름으로 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7041a-827">Generating URLs by action name</span></span>

<span data-ttu-id="7041a-828">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="7041a-828">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="7041a-829">`Action`) 및 모든 관련 오버로드는 개발자가 컨트롤러 이름과 작업 이름을 지정하여 연결 대상을 지정하려 한다는 생각을 바탕으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-829">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="7041a-830">`Url.Action`을 사용하면 `controller` 및 `action`의 현재 경로 값이 자동으로 지정됩니다. `controller` 및 `action`의 값은 모두 *앰비언트 값* **및** *값*의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-830">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="7041a-831">`Url.Action` 메서드는 항상 `action` 및 `controller`의 현재 값을 사용하며 현재 작업에 라우팅하는 URL 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-831">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="7041a-832">라우팅은 앰비언트 값의 값을 사용하여 개발자가 URL을 생성할 때 제공하지 않은 정보를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-832">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="7041a-833">`{a}/{b}/{c}/{d}` 같은 경로와 `{ a = Alice, b = Bob, c = Carol, d = David }` 앰비언트 값을 사용하면 모든 경로 매개 변수가 값을 갖기 때문에 추가 값이 없어도 라우팅에서 URL을 생성하기에 충분한 정보가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-833">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="7041a-834">`{ d = Donovan }` 값을 추가하면 `{ d = David }` 값이 무시되고 `Alice/Bob/Carol/Donovan` URL 경로가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-834">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="7041a-835">URL 경로는 계층적입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-835">URL paths are hierarchical.</span></span> <span data-ttu-id="7041a-836">위의 예에서 `{ c = Cheryl }` 값을 추가하면 두 `{ c = Carol, d = David }` 값이 모두 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-836">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="7041a-837">이 경우 `d`의 값이 없기 때문에 URL 생성이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-837">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="7041a-838">원하는 `c` 및 `d` 값을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-838">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="7041a-839">기본 경로(`{controller}/{action}/{id?}`)를 사용해도 이 문제가 발생할 가능성은 있지만, `Url.Action`이 항상 `controller` 및 `action` 값을 명시적으로 지정하기 때문에 실제로 이 동작이 발생하는 경우는 극히 드뭅니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-839">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="7041a-840">`Url.Action` 의 긴 오버로드에서는 `controller` 및 `action` 이외의 경로 매개 변수의 값을 제공하기 위한 추가 *경로 값* 개체가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-840">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="7041a-841">`Url.Action("Buy", "Products", new { id = 17 })`처럼 `id`와 함께 사용되는 것을 흔히 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-841">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="7041a-842">관례상 *경로 값* 개체는 대부분 무명 형식의 개체지만, `IDictionary<>` 또는 *오래된 일반 .NET 개체*일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-842">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="7041a-843">경로 매개 변수와 일치하지 않는 추가 경로 값들은 쿼리 문자열에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-843">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="7041a-844">절대 URL을 만들려면 `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`을 받는 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-844">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="7041a-845">경로로 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7041a-845">Generating URLs by route</span></span>

<span data-ttu-id="7041a-846">위의 코드에서는 컨트롤러 및 작업 이름을 전달하여 URL을 생성하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-846">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="7041a-847">`IUrlHelper`는 `Url.RouteUrl` 메서드 모음도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-847">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="7041a-848">이 메서드는 `Url.Action`과 비슷하지만, `action` 및 `controller`의 현재 값을 경로 값에 복사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-848">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="7041a-849">가장 일반적인 사용법은 URL을 생성할 경로를 지정하기 위한 경로 이름을 지정하는 것으로, 일반적으로 컨트롤러 또는 작업 이름을 지정하지 *않습니다*.</span><span class="sxs-lookup"><span data-stu-id="7041a-849">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="7041a-850">HTML에서 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7041a-850">Generating URLs in HTML</span></span>

<span data-ttu-id="7041a-851">`IHtmlHelper`는 각각 `<form>` 및 `<a>` 요소를 생성하는 `HtmlHelper` 메서드인 `Html.BeginForm` 및 `Html.ActionLink`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-851">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="7041a-852">이러한 메서드는 `Url.Action` 메서드를 사용하여 URL을 생성하며 비슷한 인수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-852">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="7041a-853">`HtmlHelper`에 대한 `Url.RouteUrl` 보조 도구는 `Html.BeginRouteForm` 및 `Html.RouteLink`이며 서로 기능이 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-853">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="7041a-854">TagHelper는 `form` TagHelper 및 `<a>` TagHelper를 통해 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-854">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="7041a-855">둘 다 구현에 `IUrlHelper`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-855">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="7041a-856">자세한 내용은 [양식 작업](../views/working-with-forms.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-856">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="7041a-857">보기 내에서 `IUrlHelper`는 `Url` 속성을 통해서 위에서 다루지 않은 임시 URL을 생성하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-857">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="7041a-858">작업 결과에 URL 생성</span><span class="sxs-lookup"><span data-stu-id="7041a-858">Generating URLS in Action Results</span></span>

<span data-ttu-id="7041a-859">위의 예제에서는 컨트롤러에서 `IUrlHelper`를 사용하는 방법을 보여주었으며, 컨트롤러에서 가장 일반적인 사용법은 URL을 작업 결과의 일부로 생성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-859">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="7041a-860">`ControllerBase` 및 `Controller` 기본 클래스는 다른 작업을 참조하는 작업 결과에 대한 편의 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-860">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="7041a-861">한 가지 일반적인 사용법은 사용자 입력을 수락한 후 리디렉션하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-861">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="7041a-862">작업 결과 팩터리 메서드는 `IUrlHelper`의 메서드와 비슷한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-862">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="7041a-863">전용 규칙 기반 경로의 특별한 사례</span><span class="sxs-lookup"><span data-stu-id="7041a-863">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="7041a-864">규칙 기반 라우팅은 *전용 규칙 기반 경로*라고 하는 특별한 경로 정의를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-864">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="7041a-865">아래 예제에서 `blog`라는 이름의 경로는 전용 규칙 기반 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-865">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7041a-866">이러한 경로 정의를 사용하면 `Url.Action("Index", "Home")`은 `default` 경로를 사용하여 URL 경로 `/`를 생성합니다. 그런데 그 이유는 무엇일까요?</span><span class="sxs-lookup"><span data-stu-id="7041a-866">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="7041a-867">`{ controller = Home, action = Index }` 경로 값이면 `blog`를 사용하여 URL을 생성하기에 충분하며, 그 결과는 `/blog?action=Index&controller=Home`가 될 것이라고 추측할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-867">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="7041a-868">전용 규칙 기반 경로는 URL 생성과 관련하여 경로의 "지나친 욕심"을 방지하는 해당 경로 매개 변수가 없는 기본 값의 특별한 동작을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-868">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="7041a-869">이 예제에서 기본값은 `{ controller = Blog, action = Article }`이고, `controller` 및 `action`이 경로 매개 변수로 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-869">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="7041a-870">라우팅이 URL 생성을 수행할 때 입력한 값이 기본값과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-870">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="7041a-871">`{ controller = Home, action = Index }` 값이 `{ controller = Blog, action = Article }`과 일치하지 않기 때문에 `blog`를 사용한 URL 생성은 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-871">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="7041a-872">그 후 라우팅이 `default`로 대체되고, 이것은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-872">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="7041a-873">Areas</span><span class="sxs-lookup"><span data-stu-id="7041a-873">Areas</span></span>

<span data-ttu-id="7041a-874">[영역](areas.md)은 관련 기능을 별도의 라우팅-네임스페이스(컨트롤러 작업용) 및 폴더 구조(보기용)로 그룹화하는 데 사용되는 MVC 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-874">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="7041a-875">영역을 사용하면 컨트롤러의 *영역*이 서로 다른 한, 응용 프로그램 하나에서 이름이 같은 컨트롤러를 여러 개 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-875">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="7041a-876">영역을 사용하면 또 다른 경로 매개 변수 `area`를 `controller` 및 `action`에 추가하여 라우팅을 위한 계층 구조를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-876">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="7041a-877">이 섹션에서는 라우팅이 영역과 상호 작용하는 방법을 설명합니다. 보기에 영역을 사용하는 자세한 방법은 [영역](areas.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7041a-877">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="7041a-878">다음 예제는 기본 규칙 기반 경로 및 `Blog`라는 이름의 영역에 대한 *영역 경로*를 사용하도록 MVC를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-878">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="7041a-879">`/Manage/Users/AddUser` 같은 URL 경로를 매칭할 때, 첫 번째 경로는 `{ area = Blog, controller = Users, action = AddUser }` 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-879">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="7041a-880">`area` 경로 값은 `area`의 기본값을 사용하여 생성되며, 사실 `MapAreaRoute`를 통해 생성되는 경로는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-880">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="7041a-881">`MapAreaRoute`는 제공된 영역 이름(여기에서는 `Blog`)을 사용하는 `area`에 대해 기본값 및 제약 조건을 모두 사용하여 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-881">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="7041a-882">기본값은 경로가 항상 `{ area = Blog, ... }`를 생성함을 보장하고, 제약 조건은 URL 생성을 위해 `{ area = Blog, ... }` 값을 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-882">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="7041a-883">규칙 기반 라우팅은 순서에 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-883">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7041a-884">일반적으로 영역이 있는 경로는 영역이 없는 경로에 비해 구체적이기 때문에 경로 테이블의 앞부분에 배치되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-884">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="7041a-885">위의 예제를 사용하면 경로 값이 다음 작업과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-885">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="7041a-886">`AreaAttribute`는 컨트롤러가 영역의 일부임을 나타내며, 이때 이 컨트롤러가 `Blog` 영역에 있다고 표현합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-886">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="7041a-887">`[Area]` 특성이 없는 컨트롤러는 어떠한 영역의 구성원도 아니며, 라우팅에서 `area` 경로 값을 제공해도 매칭되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="7041a-887">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="7041a-888">다음 예제에서는 나열된 첫 번째 컨트롤러만 `{ area = Blog, controller = Users, action = AddUser }` 경로 값과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-888">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="7041a-889">각 컨트롤러의 네임스페이스는 완전성을 위해서 사용되었습니다. 네임스페이스가 없으면 컨트롤러에서 이름 충돌이 발생하고 컴파일러 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-889">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="7041a-890">클래스 네임스페이스는 MVC의 라우팅에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-890">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="7041a-891">처음 두 컨트롤러는 영역의 구성원이며, `area` 경로 값으로 해당 영역 이름을 제공하는 경우에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-891">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="7041a-892">세 번째 컨트롤러는 어떤 영역의 구성원도 아니며, 라우팅에서 `area`에 대한 값을 제공하지 않을 때에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-892">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="7041a-893">매칭 측면에서 *값 없음*, 즉 `area` 값이 없는 것은 `area`의 값이 null 또는 빈 문자열인 경우와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-893">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="7041a-894">영역 내에서 작업을 실행하면 `area`의 경로 값이 라우팅에서 URL을 생성하기 위해 사용되는 *앰비언트 값*으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-894">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="7041a-895">즉, 기본적으로 영역은 다음 예제에서 볼 수 있듯이 URL 생성을 위한 *스티커처럼* 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-895">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="7041a-896">IActionConstraint 이해</span><span class="sxs-lookup"><span data-stu-id="7041a-896">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="7041a-897">이 섹션에서는 프레임워크의 내부 구조 및 MVC가 실행할 작업을 선택하는 원리를 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-897">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="7041a-898">일반적인 응용 프로그램에는 사용자 지정 `IActionConstraint`가 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-898">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="7041a-899">이 인터페이스에 익숙하지 않은 분들도 이미 `IActionConstraint`를 사용해 보셨을 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-899">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="7041a-900">`[HttpGet]` 특성 그리고 그와 비슷한 `[Http-VERB]` 특성들은 `IActionConstraint`를 구현하여 작업 메서드 실행을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-900">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="7041a-901">기본 규칙 기반 경로를 사용한다고 가정할 때, URL 경로 `/Products/Edit`는 여기에 보이는 두 작업 **모두**와 매칭하는 `{ controller = Products, action = Edit }` 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-901">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="7041a-902">`IActionConstraint` 용어에서는 두 작업이 경로 데이터와 매칭되므로 두 작업이 후보로 간주된다고 말할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-902">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="7041a-903">`HttpGetAttribute`가 실행되면 *Edit()* 는 *GET*에 대해서만 일치하고 다른 HTTP 동사에 대해서는 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-903">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="7041a-904">`Edit(...)` 작업은 정의된 제약 조건이 없으므로 모든 HTTP 동사와 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-904">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="7041a-905">따라서 `POST`를 고려해본다면 `Edit(...)`만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-905">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="7041a-906">하지만 `GET`의 경우에는 여전히 두 작업 모두 매칭될 수 있습니다. 그러나`IActionConstraint`가 적용된 작업이 적용되지 않은 작업보다 항상 *더 적합한* 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-906">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="7041a-907">따라서 `Edit()`에는 `[HttpGet]`가 있으므로 보다 구체적인 것으로 간주되며, 두 작업이 모두 매칭될 수 있는 경우에도 이 작업이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-907">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="7041a-908">개념적으로 `IActionConstraint`는 *오버로딩*의 한 가지 형태이지만, 같은 이름의 메서드를 오버로딩하는 대신, 동일한 URL을 매칭하는 작업 간에 오버로딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-908">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="7041a-909">특성 라우팅에서도 `IActionConstraint`를 사용하며, 다른 두 컨트롤러의 작업이 모두 후보로 간주될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-909">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="7041a-910">IActionConstraint 구현</span><span class="sxs-lookup"><span data-stu-id="7041a-910">Implementing IActionConstraint</span></span>

<span data-ttu-id="7041a-911">`IActionConstraint`를 구현하는 가장 간단한 방법은 `System.Attribute`에서 파생된 클래스를 만들어서 작업 및 컨트롤러에 배치하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-911">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="7041a-912">MVC는 특성으로 적용된 `IActionConstraint`를 자동으로 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-912">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="7041a-913">애플리케이션 모델을 사용하여 제약 조건을 적용할 수 있으며, 제약 조건이 적용되는 방식을 개발자가 메타프로그래밍할 수 있는 가장 유연한 접근 방식일 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-913">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="7041a-914">다음 예제에서 제약 조건은 경로 데이터에서 *국가 코드를* 기반으로 작업을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-914">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="7041a-915">[GitHub의 전체 예제](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="7041a-915">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="7041a-916">개발자는 `Accept` 메서드를 구현하고 제약 조건이 실행되는 '순서'를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-916">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="7041a-917">이 예제에서 `Accept` 메서드는 `country` 경로 값이 일치하면 작업이 일치함을 나타내기 위해 `true`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-917">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="7041a-918">비-특성 작업으로 대체할 수 있다는 점에서 `RouteValueAttribute`와는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-918">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="7041a-919">이 예제는 `en-US` 작업을 정의하면 `fr-FR` 같은 국가 코드는 `[CountrySpecific(...)]`이 적용되지 않은 보다 일반적인 컨트롤러로 대체되는 것을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-919">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="7041a-920">`Order` 속성은 제약 조건이 소속되는 *단계*를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-920">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="7041a-921">작업 제약 조건은 `Order`에 따라 그룹으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-921">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="7041a-922">예를 들어 프레임워크에서 제공하는 모든 HTTP 메서드 특성은 같은 단계에서 실행될 수 있도록 동일한 `Order` 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-922">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="7041a-923">원하는 정책을 구현하는 데 필요한 만큼 단계를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-923">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="7041a-924">`Order`에 대한 값을 결정하려면 HTTP 메서드보다 제약 조건이 먼저 적용되어야 하는지 생각해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-924">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="7041a-925">숫자가 낮을수록 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7041a-925">Lower numbers run first.</span></span>

::: moniker-end
