---
title: ASP.NET Core의 컨트롤러 작업에 라우팅
author: rick-anderson
description: ASP.NET Core MVC가 라우팅 미들웨어를 사용하여 들어오는 요청의 URL을 일치시키고 이를 작업에 매핑하는 방법을 알아봅니다.
ms.author: riande
ms.date: 01/24/2019
uid: mvc/controllers/routing
ms.openlocfilehash: a0dbfbe60c151990581b494f81e500fe0b315f55
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72589862"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="9cab7-103">ASP.NET Core의 컨트롤러 작업에 라우팅</span><span class="sxs-lookup"><span data-stu-id="9cab7-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="9cab7-104">작성자: [Ryan Nowak](https://github.com/rynowak) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9cab7-104">By [Ryan Nowak](https://github.com/rynowak) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9cab7-105">ASP.NET Core MVC는 라우팅 [미들웨어](xref:fundamentals/middleware/index)를 사용하여 들어오는 요청의 URL을 매칭하고 작업에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-105">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="9cab7-106">경로는 시작 코드 또는 특성에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-106">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="9cab7-107">경로는 URL 경로를 작업에 매칭하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-107">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="9cab7-108">경로는 응답으로 전송되는 URL(링크 용)을 생성하기 위해서도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-108">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="9cab7-109">작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-109">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="9cab7-110">컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-110">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="9cab7-111">자세한 내용은 [혼합 라우팅](#routing-mixed-ref-label)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9cab7-111">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="9cab7-112">이 항목에서는 MVC와 라우팅 간의 상호 작용 및 일반적인 MVC 앱이 라우팅 기능을 사용하는 방식에 관해서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-112">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="9cab7-113">고급 라우팅에 대한 자세한 내용은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9cab7-113">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="9cab7-114">라우팅 미들웨어 설정</span><span class="sxs-lookup"><span data-stu-id="9cab7-114">Setting up Routing Middleware</span></span>

<span data-ttu-id="9cab7-115">*Configure* 메서드에서 다음과 비슷한 코드를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-115">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="9cab7-116">`UseMvc` 호출 내부에서 `MapRoute`를 사용하여 `default` 경로라고 부르는 단일 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-116">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="9cab7-117">대부분의 MVC 앱은 `default` 경로와 비슷한 템플릿을 갖는 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-117">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="9cab7-118">`"{controller=Home}/{action=Index}/{id?}"` 경로 템플릿은 경로를 토큰화하여 `/Products/Details/5` 같은 URL 경로를 매칭하고 `{ controller = Products, action = Details, id = 5 }` 경로 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-118">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="9cab7-119">MVC는 `ProductsController`라는 컨트롤러를 찾아 `Details` 작업을 실행하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-119">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="9cab7-120">이 예제에서 이 작업을 호출할 때 모델 바인딩이 `id = 5` 값을 사용하여 `id` 매개 변수를 `5`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-120">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="9cab7-121">자세한 내용은 [모델 바인딩](../models/model-binding.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9cab7-121">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="9cab7-122">`default` 경로를 사용하여:</span><span class="sxs-lookup"><span data-stu-id="9cab7-122">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="9cab7-123">경로 템플릿의:</span><span class="sxs-lookup"><span data-stu-id="9cab7-123">The route template:</span></span>

* <span data-ttu-id="9cab7-124">`{controller=Home}`은 `Home`을 기본 `controller`로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-124">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="9cab7-125">`{action=Index}`는 `Index`를 기본 `action`으로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-125">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="9cab7-126">`{id?}`는 `id`를 선택 사항으로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-126">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="9cab7-127">기본 및 선택적 경로 매개 변수는 매칭을 위해 URL 경로에 반드시 있어야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-127">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="9cab7-128">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](../../fundamentals/routing.md#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9cab7-128">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="9cab7-129">`"{controller=Home}/{action=Index}/{id?}"`는 URL 경로 `/`를 매칭할 수 있으며 `{ controller = Home, action = Index }` 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-129">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="9cab7-130">`controller` 및 `action`의 값으로 기본값이 사용되며, URL 경로에 해당 세그먼트 없기 때문에 `id`가 값을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-130">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="9cab7-131">MVC는 이러한 경로 값을 사용하여 `HomeController` 및 `Index` 작업을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-131">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="9cab7-132">이러한 컨트롤러 정의와 경로 템플릿을 사용하면 다음 URL 경로에 대해 `HomeController.Index` 작업이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-132">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="9cab7-133">편의 메서드인 `UseMvcWithDefaultRoute`를 사용하여:</span><span class="sxs-lookup"><span data-stu-id="9cab7-133">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="9cab7-134">다음을 대체할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-134">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="9cab7-135">`UseMvc` 및 `UseMvcWithDefaultRoute`는 미들웨어 파이프라인에 `RouterMiddleware` 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-135">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="9cab7-136">MVC는 미들웨어와 직접 상호 작용하지 않고 라우팅을 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-136">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="9cab7-137">MVC는 `MvcRouteHandler`의 인스턴스를 통해 경로에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-137">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="9cab7-138">`UseMvc` 내부의 코드는 다음과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-138">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="9cab7-139">`UseMvc`는 아무런 경로도 직접 정의하지 않고 경로 컬렉션에 `attribute` 경로에 대한 자리 표시자만 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-139">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="9cab7-140">`UseMvc(Action<IRouteBuilder>)` 오버로드를 사용하면 고유의 경로를 추가하고 특성 라우팅도 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-140">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="9cab7-141">`UseMvc` 및 모든 변형은 특성 경로에 대한 자리 표시자를 추가합니다. 특성 라우팅은 `UseMvc`를 구성하는 방법에 관계없이 항상 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-141">`UseMvc` and all of its variations adds a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="9cab7-142">`UseMvcWithDefaultRoute`는 기본 경로를 정의하고 특성 라우팅을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-142">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="9cab7-143">[특성 라우팅](#attribute-routing-ref-label) 섹션에는 특성 라우팅에 대한 자세한 내용이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-143">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="9cab7-144">규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="9cab7-144">Conventional routing</span></span>

<span data-ttu-id="9cab7-145">`default` 경로인:</span><span class="sxs-lookup"><span data-stu-id="9cab7-145">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="9cab7-146">는 *규칙 기반 라우팅*의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-146">is an example of a *conventional routing*.</span></span> <span data-ttu-id="9cab7-147">URL 경로에 대한 *규칙*을 설정하기 때문에 이 스타일을 *규칙 기반 라우팅*이라고 부릅니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-147">We call this style *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="9cab7-148">첫 번째 경로 세그먼트는 컨트롤러 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-148">the first path segment maps to the controller name</span></span>

* <span data-ttu-id="9cab7-149">두 번째 세그먼트는 작업 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-149">the second maps to the action name.</span></span>

* <span data-ttu-id="9cab7-150">세 번째 세그먼트는 모델 엔터티에 매핑하는 데 사용되는 선택적 `id`에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-150">the third segment is used for an optional `id` used to map to a model entity</span></span>

<span data-ttu-id="9cab7-151">이 `default` 경로를 사용하면 URL 경로 `/Products/List`는 `ProductsController.List` 작업에 매핑되고 `/Blog/Article/17`은 `BlogController.Article`에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-151">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="9cab7-152">매핑은 **오직** 컨트롤러 및 작업 이름만을 기준으로 하며 네임스페이스, 원본 파일 위치 또는 메서드 매개 변수를 기준으로 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-152">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="9cab7-153">기본 경로와 함께 규칙 기반 라우팅을 사용하면 정의하는 각 작업마다 새 URL 패턴을 만들지 않고도 신속하게 응용 프로그램을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-153">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="9cab7-154">CRUD 스타일의 작업을 수행하는 응용 프로그램의 경우 컨트롤러 전반에서 URL을 일관적으로 유지하면 코드를 단순화하고 UI의 예측 가능성을 높이는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-154">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="9cab7-155">`id`는 경로 템플릿에서 선택 사항으로 정의됩니다. 즉, URL의 일부로 제공되는 ID 없이도 작업을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-155">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="9cab7-156">일반적으로 URL에서 `id`가 생략되면 모델 바인딩에 의해 `0`으로 설정되고, 그 결과 데이터베이스에서 `id == 0`과 일치하는 엔터티를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-156">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="9cab7-157">특성 라우팅을 사용하면 일부 작업에는 필요하고 다른 작업에는 필요하지 않은 ID를 만들기 위해 세밀하게 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-157">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="9cab7-158">일반적으로 `id` 같은 선택적 매개 변수가 올바른 사용법으로 나타날 가능성이 있는 경우 설명서에 이러한 선택적 매개 변수가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-158">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="9cab7-159">여러 경로</span><span class="sxs-lookup"><span data-stu-id="9cab7-159">Multiple routes</span></span>

<span data-ttu-id="9cab7-160">`MapRoute`에 대한 더 많은 호출을 추가하여 `UseMvc` 내부에서 여러 경로를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-160">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="9cab7-161">이렇게 하면 여러 규칙을 정의하거나 다음과 같이 특정 작업에만 사용되는 규칙 기반 경로를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-161">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="9cab7-162">여기서 `blog` 경로는 규칙 기반 라우팅 시스템을 사용하지만 특정 작업에만 활용되는 *전용 규칙 기반 경로*입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-162">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="9cab7-163">`controller` 및 `action`이 경로 템플릿에 매개 변수로 표시되지 않기 때문에 기본값만 가질 수 있으며, 따라서 이 경로는 항상 `BlogController.Article` 작업에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-163">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="9cab7-164">경로 컬렉션의 경로는 순서대로 정렬되며 추가된 순서대로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-164">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="9cab7-165">따라서 이 예제의 `blog` 경로는 `default` 경로보다 먼저 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-165">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="9cab7-166">*전용 규칙 기반 경로*는 종종 `{*article}` 같은 범용 경로 매개 변수를 사용하여 URL 경로의 나머지 부분을 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-166">*Dedicated conventional routes* often use catch-all route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="9cab7-167">이 경우 경로가 '너무 많은 욕심'을 부리게 됩니다. 즉, 다른 경로와 매칭하려는 URL과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-167">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="9cab7-168">이 '욕심 많은' 경로를 경로 테이블의 뒷부분에 배치하면 이 문제를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-168">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="9cab7-169">대체</span><span class="sxs-lookup"><span data-stu-id="9cab7-169">Fallback</span></span>

<span data-ttu-id="9cab7-170">요청 처리의 일부로, MVC는 경로 값을 사용하여 응용 프로그램의 컨트롤러 및 작업을 찾을 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-170">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="9cab7-171">경로 값이 작업과 일치하지 않으면 해당 경로는 불일치하는 것으로 간주되고, 그 다음 경로를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-171">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="9cab7-172">이것을 *대체*라고 부르며, 규칙 기반 경로가 겹치는 상황을 단순화하는 것이 목적입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-172">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="9cab7-173">명확한 작업 구분</span><span class="sxs-lookup"><span data-stu-id="9cab7-173">Disambiguating actions</span></span>

<span data-ttu-id="9cab7-174">라우팅을 통해 두 작업이 일치하는 경우 MVC는 작업을 명확히 구분하여 '최적의' 후보를 선택해야 하며, 그렇지 못하면 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-174">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="9cab7-175">예:</span><span class="sxs-lookup"><span data-stu-id="9cab7-175">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="9cab7-176">이 컨트롤러는 URL 경로 `/Products/Edit/17` 및 경로 데이터 `{ controller = Products, action = Edit, id = 17 }`과 일치하는 두 작업을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-176">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="9cab7-177">이는 MVC 컨트롤러의 일반적인 패턴으로, `Edit(int)`는 제품을 편집할 양식을 보여주고 `Edit(int, Product)`는 게시된 양식을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-177">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="9cab7-178">이것이 가능하려면 MVC가 요청이 HTTP `POST`이면 `Edit(int, Product)`를 선택해야 하고, HTTP 동사가 그 외의 다른 것이면 `Edit(int)`를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-178">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="9cab7-179">`HttpPostAttribute`(`[HttpPost]`)는 HTTP 동사가 `POST`인 경우에만 작업을 선택하는 것이 가능한 `IActionConstraint`의 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-179">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="9cab7-180">`IActionConstraint`가 존재하면 `Edit(int, Product)`가 `Edit(int)`보다 '더 정확하게' 일치하므로 `Edit(int, Product)`를 가장 먼저 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-180">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="9cab7-181">개발자는 특별한 시나리오의 사용자 지정 `IActionConstraint` 구현을 작성하기만 하면 되지만, `HttpPostAttribute` 같은 특성 역할을 이해하는 것이 중요합니다. 다른 HTTP 동사에 대해 비슷한 특성이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-181">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="9cab7-182">규칙 기반 라우팅에서는 작업이 `show form -> submit form` 워크플로의 일부인 경우 작업에서 동일한 작업을 사용하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-182">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="9cab7-183">[IActionConstraint 이해](#understanding-iactionconstraint) 섹션을 검토하시면 이 패턴의 편리함을 보다 명확하게 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-183">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="9cab7-184">여러 경로가 일치하고 MVC가 '최적의' 경로를 찾을 수 없는 경우 MVC는 `AmbiguousActionException`을 던집니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-184">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="9cab7-185">경로 이름</span><span class="sxs-lookup"><span data-stu-id="9cab7-185">Route names</span></span>

<span data-ttu-id="9cab7-186">다음 예제의 `"blog"` 및 `"default"` 문자열은 경로 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-186">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="9cab7-187">경로 이름은 명명된 경로를 URL 생성에 사용할 수 있도록 경로에 논리 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-187">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="9cab7-188">이렇게 하면 경로 순서 지정으로 인해 URL 생성이 복잡해질 수 있는 상황에서 URL 생성 방법이 매우 간단해집니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-188">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="9cab7-189">경로 이름은 응용 프로그램 전체에서 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-189">Route names must be unique application-wide.</span></span>

<span data-ttu-id="9cab7-190">경로 이름은 URL 일치 또는 요청 처리에 영향을 미치지 않으며 URL 생성에서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-190">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="9cab7-191">[라우팅](xref:fundamentals/routing)은 MVC 관련 도우미에서 URL 생성을 포함하여 URL 생성에 대한 보다 자세한 정보를 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-191">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="9cab7-192">특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="9cab7-192">Attribute routing</span></span>

<span data-ttu-id="9cab7-193">특성 라우팅은 특성 모음을 사용하여 작업을 경로 템플릿에 직접 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-193">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="9cab7-194">다음 예제에서는 `Configure` 메서드에서 `app.UseMvc();`가 사용되어 경로가 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-194">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="9cab7-195">`HomeController`는 기본 경로 `{controller=Home}/{action=Index}/{id?}`의 일치 항목과 비슷한 URL 집합과 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-195">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="9cab7-196">`HomeController.Index()` 작업은 URL 경로 `/`, `/Home` 또는 `/Home/Index`에 대해서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-196">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="9cab7-197">이 예제에서는 특성 라우팅과 규칙 기반 라우팅 간의 주요 프로그래밍 차이점을 강조합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-197">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="9cab7-198">특성 라우팅은 경로를 지정하기 위해 더 많은 입력이 필요하고, 규칙 기반 기본 경로는 경로를 보다 간결하게 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-198">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="9cab7-199">그러나 특성 라우팅을 사용하면 각 작업에 적용되는 경로 템플릿을 정확하게 제어할 수 있습니다(또 그래야만 합니다).</span><span class="sxs-lookup"><span data-stu-id="9cab7-199">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="9cab7-200">특성 라우팅을 사용하면 컨트롤러 이름 및 작업 이름은 작업 선택에 있어서 아무 역할도 하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="9cab7-200">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="9cab7-201">이 예제는 이전 예제와 동일한 URL을 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-201">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="9cab7-202">위의 경로 템플릿은 `action`, `area` 및 `controller`에 대한 경로 매개 변수를 정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-202">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="9cab7-203">사실, 이러한 경로 매개 변수는 특성 경로에 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-203">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="9cab7-204">경로 템플릿은 이미 작업에 연결되어 있기 때문에 URL에서 작업 이름을 구문 분석할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-204">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="9cab7-205">Http[Verb] 특성을 사용한 특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="9cab7-205">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="9cab7-206">특성 라우팅은 `HttpPostAttribute` 같은 `Http[Verb]`를 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-206">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="9cab7-207">이러한 특성은 모두 경로 템플릿을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-207">All of these attributes can accept a route template.</span></span> <span data-ttu-id="9cab7-208">이 예제는 동일한 경로 템플릿과 일치하는 두 가지 작업을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-208">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="9cab7-209">`/products` 같은 URL 경로의 경우 HTTP 동사가 `GET`이면 `ProductsApi.ListProducts` 작업이 실행되고 HTTP 동사가 `POST`이면 `ProductsApi.CreateProduct`가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-209">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="9cab7-210">특성 라우팅은 먼저 URL을 경로 특성에 정의된 경로 템플릿 집합과 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-210">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="9cab7-211">경로 템플릿이 일치하면 `IActionConstraint` 제약 조건을 적용하여 실행 가능한 작업을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-211">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="9cab7-212">REST API를 빌드할 때 작업 메서드에 `[Route(...)]`를 사용하려는 경우는 거의 없습니다. 작업이 모든 HTTP 메서드를 받기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-212">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="9cab7-213">보다 구체적인 `Http*Verb*Attributes`를 사용하여 API에서 지원하는 항목을 정확하게 지정하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-213">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="9cab7-214">REST API의 클라이언트는 특정 논리 작업에 어떤 경로 및 HTTP 동사가 매핑되는지 알아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-214">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="9cab7-215">특성 경로는 특정 작업에 적용되므로 경로 템플릿 정의의 일환으로 필요한 매개 변수를 간단하게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-215">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="9cab7-216">이 예제에서 `id`는 URL 경로에 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-216">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="9cab7-217">`ProductsApi.GetProduct(int)` 작업은 `/products/3` 같은 URL 경로에 대해 실행되지만 `/products` 같은 URL 경로에 대해서는 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-217">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="9cab7-218">경로 템플릿 및 관련 옵션에 대한 전체 설명은 [라우팅](../../fundamentals/routing.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9cab7-218">See [Routing](../../fundamentals/routing.md) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="9cab7-219">경로 이름</span><span class="sxs-lookup"><span data-stu-id="9cab7-219">Route Name</span></span>

<span data-ttu-id="9cab7-220">다음 코드는 `Products_List`의 *경로 이름*을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-220">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="9cab7-221">경로 이름을 사용하여 특정 경로 기반의 URL을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-221">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="9cab7-222">경로 이름은 라우팅의 URL 매칭 동작에 영향을 미치지 않으며 URL 생성에만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-222">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="9cab7-223">경로 이름은 응용 프로그램 전체에서 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-223">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="9cab7-224">`id` 매개 변수를 선택 사항(`{id?}`)으로 정의하는 규칙 기반 *기본 경로*와는 대조적입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-224">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="9cab7-225">API를 정확하게 지정하는 이 기능은 `/products`와 `/products/5`를 서로 다른 작업에 디스패치할 수 있는 등의 장점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-225">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="9cab7-226">경로 결합</span><span class="sxs-lookup"><span data-stu-id="9cab7-226">Combining routes</span></span>

<span data-ttu-id="9cab7-227">특성 라우팅의 반복 횟수를 줄이기 위해 컨트롤러의 경로 특성은 개별 작업의 경로 특성과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-227">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="9cab7-228">컨트롤러에 정의된 경로 템플릿은 작업의 경로 템플릿에 앞에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-228">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="9cab7-229">경로 특성을 컨트롤러에 배치하면 컨트롤러의 **모든** 작업이 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-229">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="9cab7-230">이 예제에서 URL 경로 `/products`는 `ProductsApi.ListProducts`와 매칭할 수 있고, URL 경로 `/products/5`는 `ProductsApi.GetProduct(int)`와 매칭할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-230">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="9cab7-231">두 작업은 모두 `HttpGetAttribute`로 데코레이팅되기 때문에 HTTP `GET`만 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-231">Both of these actions only match HTTP `GET` because they're decorated with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="9cab7-232">작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-232">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="9cab7-233">이 예제는 *기본 경로*와 비슷한 URL 경로 집합을 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-233">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="9cab7-234">특성 경로 순서 지정</span><span class="sxs-lookup"><span data-stu-id="9cab7-234">Ordering attribute routes</span></span>

<span data-ttu-id="9cab7-235">정의된 순서대로 실행되는 규칙 기반 경로와는 달리, 특성 라우팅은 트리를 만들고 모든 경로를 동시에 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-235">In contrast to conventional routes which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="9cab7-236">이 동작은 경로 전체가 이상적인 순서로 정렬된 것처럼 수행됩니다. 가장 구체적인 경로는 일반적인 경로보다 먼저 실행될 가능성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-236">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="9cab7-237">예를 들어 `blog/search/{topic}` 같은 경로는 `blog/{*article}` 같은 경로보다 구체적입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-237">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="9cab7-238">논리적으로 말해서, 기본적으로 `blog/search/{topic}` 경로가 가장 먼저 '실행'됩니다. 유일하게 합리적인 순서이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-238">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="9cab7-239">규칙 기반 라우팅을 사용하면 개발자가 원하는 순서대로 경로를 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-239">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="9cab7-240">특성 경로는 프레임워크가 제공하는 모든 경로 특성의 `Order` 속성을 사용하여 순서를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-240">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="9cab7-241">경로는 `Order` 속성의 오름차순 정렬에 따라 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-241">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="9cab7-242">기본 순서는 `0`입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-242">The default order is `0`.</span></span> <span data-ttu-id="9cab7-243">`Order = -1`을 사용한 경로 설정은 순서를 설정하지 않는 경로보다 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-243">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="9cab7-244">`Order = 1`을 사용한 경로 설정은 기본 경로 순서보다 늦게 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-244">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="9cab7-245">`Order`를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="9cab7-245">Avoid depending on `Order`.</span></span> <span data-ttu-id="9cab7-246">URL 공간에 올바른 라우팅을 위한 명시적 순서 값이 필요한 경우 클라이언트에서도 혼란이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-246">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="9cab7-247">일반적으로 특성 라우팅은 URL이 일치하는 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-247">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="9cab7-248">URL 생성에 사용되는 기본 순서가 작동하지 않는 경우 일반적으로 경로 이름을 우선적으로 사용하는 것이 `Order` 속성을 적용하는 것보다 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-248">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="9cab7-249">Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-249">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="9cab7-250">Razor Pages 항목의 경로 순서에 대한 정보는 [Razor Pages 라우팅 및 앱 규칙: 경로 순서](xref:razor-pages/razor-pages-conventions#route-order)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-250">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="9cab7-251">경로 템플릿에서 토큰 교체([controller] [action] [area])</span><span class="sxs-lookup"><span data-stu-id="9cab7-251">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="9cab7-252">편의를 위해 특성 경로는 토큰을 대괄호(`[`, `]`)로 묶는 방식으로 *토큰 교체*를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-252">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="9cab7-253">`[action]`, `[area]` 및 `[controller]` 토큰은 경로가 정의된 작업의 작업 이름, 영역 이름 및 컨트롤러 이름으로 교체됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-253">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="9cab7-254">다음 예제의 작업은 주석에 설명된 URL 경로와 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-254">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="9cab7-255">토큰 교체는 특성 경로 빌드 과정의 마지막 단계로 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-255">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="9cab7-256">위의 예제는 다음 코드와 동일하게 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-256">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="9cab7-257">특성 경로를 상속과 결합할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-257">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="9cab7-258">특히 토큰 교체와 결합하면 더욱 강력합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-258">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="9cab7-259">토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-259">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="9cab7-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]`는 각 작업의 고유한 경로 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="9cab7-261">리터럴 토큰 교체 구분 기호 `[` 또는 `]`와 매칭하려면 문자(`[[` 또는 `]]`)를 반복하여 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-261">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker range=">= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="9cab7-262">매개 변수 변환기를 사용하여 토큰 교체 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="9cab7-262">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="9cab7-263">매개 변수 변환기를 사용하여 토큰 교체를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-263">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="9cab7-264">매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하며 매개 변수의 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-264">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="9cab7-265">예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-265">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="9cab7-266">`RouteTokenTransformerConvention`은 다음과 같은 응용 프로그램 모델 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-266">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="9cab7-267">응용 프로그램의 모든 특성 경로에 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-267">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="9cab7-268">대체되는 특성 경로 토큰 값을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-268">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="9cab7-269">`RouteTokenTransformerConvention`은 `ConfigureServices`에서 옵션으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-269">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="9cab7-270">여러 경로</span><span class="sxs-lookup"><span data-stu-id="9cab7-270">Multiple Routes</span></span>

<span data-ttu-id="9cab7-271">특성 경로는 동일한 작업에 도달하는 여러 경로를 정의하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-271">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="9cab7-272">가장 일반적인 사용 방법은 다음 예제와 같이 *기본 규칙 기반 경로*의 동작을 모방하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-272">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="9cab7-273">컨트롤러에 여러 경로 특성을 배치하면 각 경로 특성이 작업 메서드의 각 경로 특성과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-273">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="9cab7-274">여러 경로 특성(`IActionConstraint`를 구현하는)이 작업에 배치되면 각 작업 제약 조건은 제약 조건을 정의한 특성의 경로 템플릿과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-274">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="9cab7-275">작업에 여러 경로를 사용하는 것이 좋아 보일 수 있지만, 응용 프로그램의 URL 공간을 간단하고 잘 정의된 상태로 유지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-275">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="9cab7-276">기존 클라이언트를 지원하려는 경우처럼 꼭 필요한 경우에만 작업에 여러 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-276">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="9cab7-277">특성 경로 선택적 매개 변수, 기본값 및 제약 조건 지정</span><span class="sxs-lookup"><span data-stu-id="9cab7-277">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="9cab7-278">특성 경로는 선택적 매개 변수, 기본값 및 제약 조건을 지정할 수 있는 규칙 기반 경로와 동일한 인라인 구문을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-278">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="9cab7-279">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](../../fundamentals/routing.md#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9cab7-279">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="9cab7-280">`IRouteTemplateProvider`를 사용하는 사용자 지정 경로 특성</span><span class="sxs-lookup"><span data-stu-id="9cab7-280">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="9cab7-281">프레임워크에서 제공하는 모든 경로 특성(`[Route(...)]`, `[HttpGet(...)]` 등)은 `IRouteTemplateProvider` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-281">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="9cab7-282">앱이 시작되면 MVC는 컨트롤러 클래스 및 작업 메서드에서 특성을 찾아 `IRouteTemplateProvider`를 구현하는 특성을 사용하여 초기 경로 집합을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-282">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="9cab7-283">개발자 고유의 경로 특성을 정의하는 `IRouteTemplateProvider`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-283">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="9cab7-284">각각의 `IRouteTemplateProvider`를 사용하여 사용자 지정 경로 템플릿, 순서 및 이름으로 단일 경로를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-284">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="9cab7-285">위의 예제에 나오는 특성은 `[MyApiController]`가 적용되면 자동으로 `Template`을 `"api/[controller]"`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-285">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="9cab7-286">응용 프로그램 모델을 사용하여 특성 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="9cab7-286">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="9cab7-287">*응용 프로그램 모델*은 MVC에 의해서 작업을 라우팅하고 실행하는 데 사용되는 모든 메타데이터를 이용하여 시작 시 만들어지는 개체 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-287">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="9cab7-288">*응용 프로그램 모델*은 `IRouteTemplateProvider`를 통해서 경로 특성으로부터 수집한 모든 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-288">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="9cab7-289">개발자는 시작 시에 응용 프로그램 모델을 수정하는 *규칙*을 작성하여 라우팅 동작을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-289">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="9cab7-290">이 섹션에서는 응용 프로그램 모델을 사용하여 라우팅을 사용자 지정하는 간단한 예제를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-290">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="9cab7-291">혼합 라우팅: 특성 라우팅 및 규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="9cab7-291">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="9cab7-292">MVC 응용 프로그램은 규칙 기반 라우팅과 특성 라우팅을 혼합해서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-292">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="9cab7-293">일반적으로 브라우저의 HTML 페이지를 처리하는 컨트롤러에는 규칙 기반 경로를 사용하고 REST API를 제공하는 컨트롤러에는 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-293">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="9cab7-294">작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-294">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="9cab7-295">컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-295">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="9cab7-296">특성 경로를 정의하는 동작은 규칙 기반 경로를 통해 도달할 수 없으며 그 반대도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-296">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="9cab7-297">컨트롤러의 **모든** 경로 특성은 컨트롤러에 있는 모든 작업에서 특성 라우팅을 사용하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-297">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="9cab7-298">두 라우팅 시스템 간의 차이는 URL이 경로 템플릿과 일치한 후 적용되는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-298">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="9cab7-299">규칙 기반 라우팅에서는 일치 항목의 경로 값을 사용하여 모든 규칙 기반 라우팅된 작업의 조회 테이블에서 작업 및 컨트롤러를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-299">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="9cab7-300">특성 라우팅에서는 각 템플릿이 이미 작업과 연결되어 있으며, 더 이상의 조회가 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-300">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="9cab7-301">복잡한 세그먼트</span><span class="sxs-lookup"><span data-stu-id="9cab7-301">Complex segments</span></span>

<span data-ttu-id="9cab7-302">복잡한 세그먼트(예: `[Route("/dog{token}cat")]`)는 non-greedy 방식으로 오른쪽에서 왼쪽으로 리터럴을 매칭하여 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-302">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="9cab7-303">자세한 내용은 [소스 코드](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9cab7-303">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="9cab7-304">자세한 내용은 [이 문제](https://github.com/aspnet/AspNetCore.Docs/issues/8197)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9cab7-304">For more information, see [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="9cab7-305">URL 생성</span><span class="sxs-lookup"><span data-stu-id="9cab7-305">URL Generation</span></span>

<span data-ttu-id="9cab7-306">MVC 응용 프로그램은 라우팅의 URL 생성 기능을 사용하여 작업에 대한 URL 링크를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-306">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="9cab7-307">URL을 생성하면 하드 코딩된 URL이 제거되어 코드를 더욱 견고하게 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-307">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="9cab7-308">이 섹션에서는 MVC에서 제공하는 URL 생성 기능을 중심으로 기본적인 URL 생성 원리에 대해서만 다룰 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-308">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="9cab7-309">URL 생성에 대한 자세한 설명은 [라우팅](../../fundamentals/routing.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9cab7-309">See [Routing](../../fundamentals/routing.md) for a detailed description of URL generation.</span></span>

<span data-ttu-id="9cab7-310">`IUrlHelper` 인터페이스는 URL 생성을 위한 MVC와 라우팅 간의 기본 인프라입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-310">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="9cab7-311">컨트롤러, 보기 및 보기 구성 요소의 `Url` 속성을 통해서 사용 가능한 `IUrlHelper`의 인스턴스를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-311">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="9cab7-312">이 예제에서 `IUrlHelper` 인터페이스는 `Controller.Url` 속성을 통해 다른 작업에 대한 URL을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-312">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="9cab7-313">응용 프로그램이 기본 규칙 기반 경로를 사용할 경우 `url` 변수의 값은 URL 경로 문자열 `/UrlGeneration/Destination`입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-313">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="9cab7-314">이 URL 경로는 현재 요청의 경로 값(앰비언트 값)을 `Url.Action`에 전달된 값과 결합하고 그 값을 경로 템플릿에 대체하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-314">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="9cab7-315">경로 템플릿에 있는 각 경로 매개 변수의 값은 이름을 값 및 앰비언트 값과 매칭한 값으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-315">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="9cab7-316">값이 없는 경로 매개 변수는 기본값이 있는 경우 기본값을 사용할 수 있고, 값이 선택 사항인 경우 건너뛰어도 됩니다(이 예제의 `id`처럼).</span><span class="sxs-lookup"><span data-stu-id="9cab7-316">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="9cab7-317">필요한 경로 매개 변수에 해당 값이 없는 경우 URL 생성에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-317">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="9cab7-318">경로에 대한 URL 생성이 실패하면 모든 경로를 시도하거나 일치 항목을 찾을 때까지 그 다음 경로를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-318">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="9cab7-319">위의 `Url.Action`예제는 규칙 기반 라우팅을 가정합니다. 개념은 다르지만 URL 생성 원리는 특성 라우팅과 비슷하게 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-319">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="9cab7-320">규칙 기반 라우팅에서 경로 값은 템플릿을 확장하는 데 사용되고, 일반적으로`controller` 및 `action`의 경로 값은 해당 템플릿에 표시됩니다. 라우팅에서 매핑된 URL이 *규칙*을 따르기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-320">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="9cab7-321">특성 라우팅에서는 `controller` 및 `action`의 경로 값이 템플릿에 표시되지 않는 대신, 사용할 템플릿을 조회하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-321">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="9cab7-322">이 예제는 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-322">This example uses attribute routing:</span></span>

[!code-csharp[](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="9cab7-323">MVC는 모든 특성 라우팅 작업의 조회 테이블을 작성하고 `controller` 및 `action` 값을 매칭하여 URL 생성에 사용할 경로 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-323">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="9cab7-324">위의 예제에서는 `custom/url/to/destination`이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-324">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="9cab7-325">작업 이름으로 URL 생성</span><span class="sxs-lookup"><span data-stu-id="9cab7-325">Generating URLs by action name</span></span>

<span data-ttu-id="9cab7-326">`Url.Action`(`IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="9cab7-326">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="9cab7-327">`Action`) 및 모든 관련 오버로드는 개발자가 컨트롤러 이름과 작업 이름을 지정하여 연결 대상을 지정하려 한다는 생각을 바탕으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-327">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="9cab7-328">`Url.Action`을 사용하면 `controller` 및 `action`의 현재 경로 값이 자동으로 지정됩니다. `controller` 및 `action`의 값은 *앰비언트 값* **및** *값*의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-328">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="9cab7-329">`Url.Action` 메서드는 항상 `action` 및 `controller`의 현재 값을 사용하며 현재 작업에 라우팅하는 URL 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-329">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="9cab7-330">라우팅은 앰비언트 값의 값을 사용하여 개발자가 URL을 생성할 때 제공하지 않은 정보를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-330">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="9cab7-331">`{a}/{b}/{c}/{d}` 같은 경로와 `{ a = Alice, b = Bob, c = Carol, d = David }` 앰비언트 값을 사용하면 모든 경로 매개 변수가 값을 갖기 때문에 추가 값이 없어도 라우팅에서 URL을 생성하기에 충분한 정보가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-331">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="9cab7-332">`{ d = Donovan }` 값을 추가하면 `{ d = David }` 값이 무시되고 `Alice/Bob/Carol/Donovan` URL 경로가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-332">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="9cab7-333">URL 경로는 계층적입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-333">URL paths are hierarchical.</span></span> <span data-ttu-id="9cab7-334">위의 예에서 `{ c = Cheryl }` 값을 추가하면 두 `{ c = Carol, d = David }` 값이 모두 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-334">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="9cab7-335">이 경우 `d`의 값이 없기 때문에 URL 생성이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-335">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="9cab7-336">원하는 `c` 및 `d` 값을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-336">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="9cab7-337">기본 경로(`{controller}/{action}/{id?}`)를 사용해도 이 문제가 발생할 가능성은 있지만, `Url.Action`이 항상 `controller` 및 `action` 값을 명시적으로 지정하기 때문에 실제로 이 동작이 발생하는 경우는 극히 드뭅니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-337">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="9cab7-338">`Url.Action` 의 긴 오버로드에서는 `controller` 및 `action` 이외의 경로 매개 변수의 값을 제공하기 위한 추가 *경로 값* 개체가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-338">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="9cab7-339">`Url.Action("Buy", "Products", new { id = 17 })`처럼 `id`와 함께 사용되는 것을 흔히 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-339">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="9cab7-340">관례상 *경로 값* 개체는 대부분 무명 형식의 개체지만, `IDictionary<>` 또는 *오래된 일반 .NET 개체*일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-340">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="9cab7-341">경로 매개 변수와 일치하지 않는 추가 경로 값들은 쿼리 문자열에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-341">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="9cab7-342">절대 URL을 만들려면 `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`을 받는 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-342">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="9cab7-343">경로로 URL 생성</span><span class="sxs-lookup"><span data-stu-id="9cab7-343">Generating URLs by route</span></span>

<span data-ttu-id="9cab7-344">위의 코드에서는 컨트롤러 및 작업 이름을 전달하여 URL을 생성하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-344">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="9cab7-345">`IUrlHelper`는 `Url.RouteUrl` 메서드 모음도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-345">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="9cab7-346">이 메서드는 `Url.Action`과 비슷하지만, `action` 및 `controller`의 현재 값을 경로 값에 복사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-346">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="9cab7-347">가장 일반적인 사용법은 URL을 생성할 경로를 지정하기 위한 경로 이름을 지정하는 것으로, 일반적으로 컨트롤러 또는 작업 이름을 지정하지 *않습니다*.</span><span class="sxs-lookup"><span data-stu-id="9cab7-347">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="9cab7-348">HTML에서 URL 생성</span><span class="sxs-lookup"><span data-stu-id="9cab7-348">Generating URLs in HTML</span></span>

<span data-ttu-id="9cab7-349">`IHtmlHelper`는 각각 `<form>` 및 `<a>` 요소를 생성하는 `HtmlHelper` 메서드인 `Html.BeginForm` 및 `Html.ActionLink`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-349">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="9cab7-350">이러한 메서드는 `Url.Action` 메서드를 사용하여 URL을 생성하며 비슷한 인수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-350">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="9cab7-351">`HtmlHelper`에 대한 `Url.RouteUrl` 보조 도구는 `Html.BeginRouteForm` 및 `Html.RouteLink`이며 서로 기능이 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-351">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="9cab7-352">TagHelper는 `form` TagHelper 및 `<a>` TagHelper를 통해 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-352">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="9cab7-353">둘 다 구현에 `IUrlHelper`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-353">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="9cab7-354">자세한 내용은 [양식 작업](../views/working-with-forms.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9cab7-354">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="9cab7-355">보기 내에서 `IUrlHelper`는 `Url` 속성을 통해서 위에서 다루지 않은 임시 URL을 생성하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-355">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="9cab7-356">작업 결과에 URL 생성</span><span class="sxs-lookup"><span data-stu-id="9cab7-356">Generating URLS in Action Results</span></span>

<span data-ttu-id="9cab7-357">위의 예제에서는 컨트롤러에서 `IUrlHelper`를 사용하는 방법을 보여주었으며, 컨트롤러에서 가장 일반적인 사용법은 URL을 작업 결과의 일부로 생성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-357">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="9cab7-358">`ControllerBase` 및 `Controller` 기본 클래스는 다른 작업을 참조하는 작업 결과에 대한 편의 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-358">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="9cab7-359">한 가지 일반적인 사용법은 사용자 입력을 수락한 후 리디렉션하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-359">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="9cab7-360">작업 결과 팩터리 메서드는 `IUrlHelper`의 메서드와 비슷한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-360">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="9cab7-361">전용 규칙 기반 경로의 특별한 사례</span><span class="sxs-lookup"><span data-stu-id="9cab7-361">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="9cab7-362">규칙 기반 라우팅은 *전용 규칙 기반 경로*라고 하는 특별한 경로 정의를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-362">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="9cab7-363">아래 예제에서 `blog`라는 이름의 경로는 전용 규칙 기반 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-363">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="9cab7-364">이러한 경로 정의를 사용하면 `Url.Action("Index", "Home")`은 `default` 경로를 사용하여 URL 경로 `/`를 생성합니다. 그런데 그 이유는 무엇일까요?</span><span class="sxs-lookup"><span data-stu-id="9cab7-364">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="9cab7-365">`{ controller = Home, action = Index }` 경로 값이면 `blog`를 사용하여 URL을 생성하기에 충분하며, 그 결과는 `/blog?action=Index&controller=Home`가 될 것이라고 추측할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-365">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="9cab7-366">전용 규칙 기반 경로는 URL 생성과 관련하여 경로의 "지나친 욕심"을 방지하는 해당 경로 매개 변수가 없는 기본 값의 특별한 동작을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-366">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="9cab7-367">이 예제에서 기본값은 `{ controller = Blog, action = Article }`이고, `controller` 및 `action`이 경로 매개 변수로 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-367">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="9cab7-368">라우팅이 URL 생성을 수행할 때 입력한 값이 기본값과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-368">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="9cab7-369">`{ controller = Home, action = Index }` 값이 `{ controller = Blog, action = Article }`과 일치하지 않기 때문에 `blog`를 사용한 URL 생성은 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-369">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="9cab7-370">그 후 라우팅이 `default`로 대체되고, 이것은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-370">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="9cab7-371">영역</span><span class="sxs-lookup"><span data-stu-id="9cab7-371">Areas</span></span>

<span data-ttu-id="9cab7-372">[영역](areas.md)은 관련 기능을 별도의 라우팅-네임스페이스(컨트롤러 작업용) 및 폴더 구조(보기용)로 그룹화하는 데 사용되는 MVC 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-372">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="9cab7-373">영역을 사용하면 컨트롤러의 *영역*이 서로 다른 한, 응용 프로그램 하나에서 이름이 같은 컨트롤러를 여러 개 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-373">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="9cab7-374">영역을 사용하면 또 다른 경로 매개 변수 `area`를 `controller` 및 `action`에 추가하여 라우팅을 위한 계층 구조를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-374">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="9cab7-375">이 섹션에서는 라우팅이 영역과 상호 작용하는 방법을 설명합니다. 보기에 영역을 사용하는 자세한 방법은 [영역](areas.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9cab7-375">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="9cab7-376">다음 예제는 기본 규칙 기반 경로 및 `Blog`라는 이름의 영역에 대한 *영역 경로*를 사용하도록 MVC를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-376">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="9cab7-377">`/Manage/Users/AddUser` 같은 URL 경로를 매칭할 때, 첫 번째 경로는 `{ area = Blog, controller = Users, action = AddUser }` 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-377">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="9cab7-378">`area` 경로 값은 `area`의 기본값을 사용하여 생성되며, 사실 `MapAreaRoute`를 통해 생성되는 경로는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-378">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="9cab7-379">`MapAreaRoute`는 제공된 영역 이름(여기에서는 `Blog`)을 사용하는 `area`에 대해 기본값 및 제약 조건을 모두 사용하여 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-379">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="9cab7-380">기본값은 경로가 항상 `{ area = Blog, ... }`를 생성함을 보장하고, 제약 조건은 URL 생성을 위해 `{ area = Blog, ... }` 값을 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-380">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="9cab7-381">규칙 기반 라우팅은 순서에 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-381">Conventional routing is order-dependent.</span></span> <span data-ttu-id="9cab7-382">일반적으로 영역이 있는 경로는 영역이 없는 경로에 비해 구체적이기 때문에 경로 테이블의 앞부분에 배치되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-382">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="9cab7-383">위의 예제를 사용하면 경로 값이 다음 작업과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-383">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="9cab7-384">`AreaAttribute`는 컨트롤러가 영역의 일부임을 나타내며, 이때 이 컨트롤러가 `Blog` 영역에 있다고 표현합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-384">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="9cab7-385">`[Area]` 특성이 없는 컨트롤러는 어떠한 영역의 구성원도 아니며, 라우팅에서 `area` 경로 값을 제공해도 매칭되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="9cab7-385">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="9cab7-386">다음 예제에서는 나열된 첫 번째 컨트롤러만 `{ area = Blog, controller = Users, action = AddUser }` 경로 값과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-386">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="9cab7-387">각 컨트롤러의 네임스페이스는 완전성을 위해서 사용되었습니다. 네임스페이스가 없으면 컨트롤러에서 이름 충돌이 발생하고 컴파일러 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-387">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="9cab7-388">클래스 네임스페이스는 MVC의 라우팅에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-388">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="9cab7-389">처음 두 컨트롤러는 영역의 구성원이며, `area` 경로 값으로 해당 영역 이름을 제공하는 경우에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-389">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="9cab7-390">세 번째 컨트롤러는 어떤 영역의 구성원도 아니며, 라우팅에서 `area`에 대한 값을 제공하지 않을 때에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-390">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="9cab7-391">매칭 측면에서 *값 없음*, 즉 `area` 값이 없는 것은 `area`의 값이 null 또는 빈 문자열인 경우와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-391">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="9cab7-392">영역 내에서 작업을 실행하면 `area`의 경로 값이 라우팅에서 URL을 생성하기 위해 사용되는 *앰비언트 값*으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-392">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="9cab7-393">즉, 기본적으로 영역은 다음 예제에서 볼 수 있듯이 URL 생성을 위한 *스티커처럼* 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-393">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="9cab7-394">IActionConstraint 이해</span><span class="sxs-lookup"><span data-stu-id="9cab7-394">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="9cab7-395">이 섹션에서는 프레임워크의 내부 구조 및 MVC가 실행할 작업을 선택하는 원리를 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-395">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="9cab7-396">일반적인 응용 프로그램에는 사용자 지정 `IActionConstraint`가 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-396">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="9cab7-397">이 인터페이스에 익숙하지 않은 분들도 이미 `IActionConstraint`를 사용해 보셨을 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-397">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="9cab7-398">`[HttpGet]` 특성 그리고 그와 비슷한 `[Http-VERB]` 특성들은 `IActionConstraint`를 구현하여 작업 메서드 실행을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-398">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="9cab7-399">기본 규칙 기반 경로를 사용한다고 가정할 때, URL 경로 `/Products/Edit`는 여기에 보이는 두 작업 **모두**와 매칭하는 `{ controller = Products, action = Edit }` 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-399">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="9cab7-400">`IActionConstraint` 용어에서는 두 작업이 경로 데이터와 매칭되므로 두 작업이 후보로 간주된다고 말할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-400">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="9cab7-401">`HttpGetAttribute`가 실행되면 *Edit()* 는 *GET*에 대해서만 일치하고 다른 HTTP 동사에 대해서는 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-401">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="9cab7-402">`Edit(...)` 작업은 정의된 제약 조건이 없으므로 모든 HTTP 동사와 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-402">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="9cab7-403">따라서 `POST`를 고려해본다면 `Edit(...)`만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-403">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="9cab7-404">하지만 `GET`의 경우에는 여전히 두 작업 모두 매칭될 수 있습니다. 그러나`IActionConstraint`가 적용된 작업이 적용되지 않은 작업보다 항상 *더 적합한* 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-404">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="9cab7-405">따라서 `Edit()`에는 `[HttpGet]`가 있으므로 보다 구체적인 것으로 간주되며, 두 작업이 모두 매칭될 수 있는 경우에도 이 작업이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-405">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="9cab7-406">개념적으로 `IActionConstraint`는 *오버로딩*의 한 가지 형태이지만, 같은 이름의 메서드를 오버로딩하는 대신, 동일한 URL을 매칭하는 작업 간에 오버로딩합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-406">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="9cab7-407">특성 라우팅에서도 `IActionConstraint`를 사용하며, 다른 두 컨트롤러의 작업이 모두 후보로 간주될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-407">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="9cab7-408">IActionConstraint 구현</span><span class="sxs-lookup"><span data-stu-id="9cab7-408">Implementing IActionConstraint</span></span>

<span data-ttu-id="9cab7-409">`IActionConstraint`를 구현하는 가장 간단한 방법은 `System.Attribute`에서 파생된 클래스를 만들어서 작업 및 컨트롤러에 배치하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-409">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="9cab7-410">MVC는 특성으로 적용된 `IActionConstraint`를 자동으로 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-410">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="9cab7-411">응용 프로그램 모델을 사용하여 제약 조건을 적용할 수 있으며, 제약 조건이 적용되는 방식을 개발자가 메타프로그래밍할 수 있는 가장 유연한 접근 방식일 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-411">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="9cab7-412">다음 예제의 제약 조건은 경로 데이터의 *국가 코드*를 기반으로 작업을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-412">In the following example a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="9cab7-413">[GitHub의 전체 예제](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="9cab7-413">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="9cab7-414">개발자는 `Accept` 메서드를 구현하고 제약 조건이 실행되는 '순서'를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-414">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="9cab7-415">이 예제에서 `Accept` 메서드는 `country` 경로 값이 일치하면 작업이 일치함을 나타내기 위해 `true`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-415">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="9cab7-416">비-특성 작업으로 대체할 수 있다는 점에서 `RouteValueAttribute`와는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-416">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="9cab7-417">이 예제는 `en-US` 작업을 정의하면 `fr-FR` 같은 국가 코드는 `[CountrySpecific(...)]`이 적용되지 않은 보다 일반적인 컨트롤러로 대체되는 것을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-417">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="9cab7-418">`Order` 속성은 제약 조건이 소속되는 *단계*를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-418">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="9cab7-419">작업 제약 조건은 `Order`에 따라 그룹으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-419">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="9cab7-420">예를 들어 프레임워크에서 제공하는 모든 HTTP 메서드 특성은 같은 단계에서 실행될 수 있도록 동일한 `Order` 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-420">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="9cab7-421">원하는 정책을 구현하는 데 필요한 만큼 단계를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-421">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="9cab7-422">`Order`에 대한 값을 결정하려면 HTTP 메서드보다 제약 조건이 먼저 적용되어야 하는지 생각해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-422">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="9cab7-423">숫자가 낮을수록 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9cab7-423">Lower numbers run first.</span></span>
