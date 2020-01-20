---
title: ASP.NET Core의 컨트롤러 작업에 라우팅
author: rick-anderson
description: ASP.NET Core MVC가 라우팅 미들웨어를 사용하여 들어오는 요청의 URL을 일치시키고 이를 작업에 매핑하는 방법을 알아봅니다.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/routing
ms.openlocfilehash: 8cf7e74df292a614f287eff8561a22187f6558ce
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75866061"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>ASP.NET Core의 컨트롤러 작업에 라우팅

작성자: [Ryan Nowak](https://github.com/rynowak) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core MVC는 라우팅 [미들웨어](xref:fundamentals/middleware/index)를 사용하여 들어오는 요청의 URL을 매칭하고 작업에 매핑합니다. 경로는 시작 코드 또는 특성에서 정의됩니다. 경로는 URL 경로를 작업에 매칭하는 방법을 설명합니다. 경로는 응답으로 전송되는 URL(링크 용)을 생성하기 위해서도 사용됩니다.

작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다. 컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다. 자세한 내용은 [혼합 라우팅](#routing-mixed-ref-label)을 참조하세요.

이 항목에서는 MVC와 라우팅 간의 상호 작용 및 일반적인 MVC 앱이 라우팅 기능을 사용하는 방식에 관해서 설명합니다. 고급 라우팅에 대한 자세한 내용은 [라우팅](xref:fundamentals/routing)을 참조하세요.

## <a name="setting-up-routing-middleware"></a>라우팅 미들웨어 설정

*Configure* 메서드에서 다음과 비슷한 코드를 볼 수 있습니다.

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` 호출 내부에서 `MapRoute`를 사용하여 `default` 경로라고 부르는 단일 경로를 생성합니다. 대부분의 MVC 앱은 `default` 경로와 비슷한 템플릿을 갖는 경로를 사용합니다.

`"{controller=Home}/{action=Index}/{id?}"` 경로 템플릿은 `/Products/Details/5`와 같은 URL 경로를 매칭하고 경로를 토큰화하여 `{ controller = Products, action = Details, id = 5 }` 경로 값을 추출합니다. MVC는 `ProductsController`라는 컨트롤러를 찾아 `Details` 작업을 실행하려고 시도합니다.

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

이 예제에서 이 작업을 호출할 때 모델 바인딩이 `id = 5` 값을 사용하여 `id` 매개 변수를 `5`로 설정합니다. 자세한 내용은 [모델 바인딩](../models/model-binding.md)을 참조하세요.

`default` 경로를 사용하여:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

경로 템플릿의:

* `{controller=Home}`은 `Home`을 기본 `controller`로 정의합니다.

* `{action=Index}`는 `Index`를 기본 `action`으로 정의합니다.

* `{id?}`는 `id`를 선택 사항으로 정의합니다.

기본 및 선택적 경로 매개 변수는 매칭을 위해 URL 경로에 반드시 있어야 하는 것은 아닙니다. 경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](../../fundamentals/routing.md#route-template-reference)를 참조하세요.

`"{controller=Home}/{action=Index}/{id?}"`는 URL 경로 `/`를 매칭할 수 있으며 `{ controller = Home, action = Index }` 경로 값을 생성합니다. `controller` 및 `action` 값으로 기본값이 사용되며, `id`는 URL 경로에 해당 세그먼트가 없기 때문에 값을 생성하지 않습니다. MVC는 이러한 경로 값을 사용하여 `HomeController` 및 `Index` 작업을 선택합니다.

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

이러한 컨트롤러 정의와 경로 템플릿을 사용하면 다음 URL 경로에 대해 `HomeController.Index` 작업이 실행됩니다.

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

편의 메서드인 `UseMvcWithDefaultRoute`를 사용하여:

```csharp
app.UseMvcWithDefaultRoute();
```

다음을 대체할 수 있습니다.

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` 및 `UseMvcWithDefaultRoute`는 미들웨어 파이프라인에 `RouterMiddleware` 인스턴스를 추가합니다. MVC는 미들웨어와 직접 상호 작용하지 않고 라우팅을 사용하여 요청을 처리합니다. MVC는 `MvcRouteHandler`의 인스턴스를 통해 경로에 연결됩니다. `UseMvc` 내부의 코드는 다음과 비슷합니다.

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc`는 아무런 경로도 직접 정의하지 않고 경로 컬렉션에 `attribute` 경로에 대한 자리 표시자만 추가합니다. `UseMvc(Action<IRouteBuilder>)` 오버로드를 사용하면 고유의 경로를 추가하고 특성 라우팅도 지원할 수 있습니다.  `UseMvc` 및 모든 변형은 특성 경로에 대한 자리 표시자를 추가합니다. 특성 라우팅은 `UseMvc`를 구성하는 방법에 관계없이 항상 사용할 수 있습니다. `UseMvcWithDefaultRoute`는 기본 경로를 정의하고 특성 라우팅을 지원합니다. [특성 라우팅](#attribute-routing-ref-label) 섹션에는 특성 라우팅에 대한 자세한 내용이 포함되어 있습니다.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>규칙 기반 라우팅

`default` 경로인:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

는 *규칙 기반 라우팅*의 예제입니다. URL 경로에 대한 *규칙*을 설정하기 때문에 이 스타일을 *규칙 기반 라우팅*이라고 부릅니다.

* 첫 번째 경로 세그먼트는 컨트롤러 이름에 매핑됩니다.

* 두 번째 세그먼트는 작업 이름에 매핑됩니다.

* 세 번째 세그먼트는 모델 엔터티에 매핑하는 데 사용되는 선택적 `id`에 사용됩니다.

이 `default` 경로를 사용하면 URL 경로 `/Products/List`는 `ProductsController.List` 작업에 매핑되고 `/Blog/Article/17`은 `BlogController.Article`에 매핑됩니다. 매핑은 **오직** 컨트롤러 및 작업 이름만을 기준으로 하며 네임스페이스, 원본 파일 위치 또는 메서드 매개 변수를 기준으로 하지 않습니다.

> [!TIP]
> 기본 경로와 함께 규칙 기반 라우팅을 사용하면 정의하는 각 작업마다 새 URL 패턴을 만들지 않고도 신속하게 애플리케이션을 만들 수 있습니다. CRUD 스타일의 작업을 수행하는 애플리케이션의 경우 컨트롤러 전반에서 URL을 일관적으로 유지하면 코드를 단순화하고 UI의 예측 가능성을 높이는 데 도움이 됩니다.

> [!WARNING]
> `id`는 경로 템플릿에서 선택 사항으로 정의됩니다. 즉, URL의 일부로 제공되는 ID 없이도 작업을 실행할 수 있습니다. 일반적으로 URL에서 `id`가 생략되면 모델 바인딩에 의해 `0`으로 설정되고, 그 결과 데이터베이스에서 `id == 0`과 일치하는 엔터티를 찾을 수 없습니다. 특성 라우팅을 사용하면 일부 작업에는 필요하고 다른 작업에는 필요하지 않은 ID를 만들기 위해 세밀하게 제어할 수 있습니다. 일반적으로 `id` 같은 선택적 매개 변수가 올바른 사용법으로 나타날 가능성이 있는 경우 설명서에 이러한 선택적 매개 변수가 포함됩니다.

## <a name="multiple-routes"></a>여러 경로

`MapRoute`에 대한 더 많은 호출을 추가하여 `UseMvc` 내부에서 여러 경로를 추가할 수 있습니다. 이렇게 하면 여러 규칙을 정의하거나 다음과 같이 특정 작업에만 사용되는 규칙 기반 경로를 추가할 수 있습니다.

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

여기서 `blog` 경로는 규칙 기반 라우팅 시스템을 사용하지만 특정 작업에만 활용되는 *전용 규칙 기반 경로*입니다. `controller` 및 `action`이 경로 템플릿에 매개 변수로 표시되지 않기 때문에 기본값만 가질 수 있으며, 따라서 이 경로는 항상 `BlogController.Article` 작업에 매핑됩니다.

경로 컬렉션의 경로는 순서대로 정렬되며 추가된 순서대로 처리됩니다. 따라서 이 예제의 `blog` 경로는 `default` 경로보다 먼저 시도됩니다.

> [!NOTE]
> *전용 규칙 기반 경로*는 종종 `{*article}` 같은 범용 경로 매개 변수를 사용하여 URL 경로의 나머지 부분을 캡처합니다. 이 경우 경로가 '너무 많은 욕심'을 부리게 됩니다. 즉, 다른 경로와 매칭하려는 URL과 매칭됩니다. 이 '욕심 많은' 경로를 경로 테이블의 뒷부분에 배치하면 이 문제를 해결할 수 있습니다.

### <a name="fallback"></a>대체

요청 처리의 일부로, MVC는 경로 값을 사용하여 애플리케이션의 컨트롤러 및 작업을 찾을 수 있는지 확인합니다. 경로 값이 작업과 일치하지 않으면 해당 경로는 불일치하는 것으로 간주되고, 그 다음 경로를 시도합니다. 이것을 *대체*라고 부르며, 규칙 기반 경로가 겹치는 상황을 단순화하는 것이 목적입니다.

### <a name="disambiguating-actions"></a>명확한 작업 구분

라우팅을 통해 두 작업이 일치하는 경우 MVC는 작업을 명확히 구분하여 '최적의' 후보를 선택해야 하며, 그렇지 못하면 예외를 던집니다. 예:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

이 컨트롤러는 URL 경로 `/Products/Edit/17` 및 경로 데이터 `{ controller = Products, action = Edit, id = 17 }`과 일치하는 두 작업을 정의합니다. 이는 MVC 컨트롤러의 일반적인 패턴으로, `Edit(int)`는 제품을 편집할 양식을 보여주고 `Edit(int, Product)`는 게시된 양식을 처리합니다. 이것이 가능하려면 MVC가 요청이 HTTP `POST`이면 `Edit(int, Product)`를 선택해야 하고, HTTP 동사가 그 외의 다른 것이면 `Edit(int)`를 선택해야 합니다.

`HttpPostAttribute`(`[HttpPost]`)는 HTTP 동사가 `POST`인 경우에만 작업을 선택하는 것이 가능한 `IActionConstraint`의 구현입니다. `IActionConstraint`가 존재하면 `Edit(int, Product)`가 `Edit(int)`보다 '더 정확하게' 일치하므로 `Edit(int, Product)`를 가장 먼저 시도합니다.

개발자는 특별한 시나리오의 사용자 지정 `IActionConstraint` 구현을 작성하기만 하면 되지만, `HttpPostAttribute` 같은 특성 역할을 이해하는 것이 중요합니다. 다른 HTTP 동사에 대해 비슷한 특성이 정의됩니다. 규칙 기반 라우팅에서는 작업이 `show form -> submit form` 워크플로의 일부인 경우 작업에서 동일한 작업을 사용하는 것이 일반적입니다. [IActionConstraint 이해](#understanding-iactionconstraint) 섹션을 검토하시면 이 패턴의 편리함을 보다 명확하게 이해할 수 있습니다.

여러 경로가 일치하고 MVC가 '최적의' 경로를 찾을 수 없는 경우 MVC는 `AmbiguousActionException`을 던집니다.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>경로 이름

다음 예제의 `"blog"` 및 `"default"` 문자열은 경로 이름입니다.

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

경로 이름은 명명된 경로를 URL 생성에 사용할 수 있도록 경로에 논리 이름을 제공합니다. 이렇게 하면 경로 순서 지정으로 인해 URL 생성이 복잡해질 수 있는 상황에서 URL 생성 방법이 매우 간단해집니다. 경로 이름은 애플리케이션 전체에서 고유해야 합니다.

경로 이름은 URL 일치 또는 요청 처리에 영향을 미치지 않으며 URL 생성에서만 사용됩니다. [라우팅](xref:fundamentals/routing)은 MVC 관련 도우미에서 URL 생성을 포함하여 URL 생성에 대한 보다 자세한 정보를 갖고 있습니다.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>특성 라우팅

특성 라우팅은 특성 모음을 사용하여 작업을 경로 템플릿에 직접 매핑합니다. 다음 예제에서는 `Configure` 메서드에서 `app.UseMvc();`가 사용되어 경로가 전달되지 않습니다. `HomeController`는 기본 경로 `{controller=Home}/{action=Index}/{id?}`의 일치 항목과 비슷한 URL 집합과 매핑됩니다.

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

`HomeController.Index()` 작업은 URL 경로 `/`, `/Home` 또는 `/Home/Index`에 대해서 실행됩니다.

> [!NOTE]
> 이 예제에서는 특성 라우팅과 규칙 기반 라우팅 간의 주요 프로그래밍 차이점을 강조합니다. 특성 라우팅은 경로를 지정하기 위해 더 많은 입력이 필요하고, 규칙 기반 기본 경로는 경로를 보다 간결하게 처리합니다. 그러나 특성 라우팅을 사용하면 각 작업에 적용되는 경로 템플릿을 정확하게 제어할 수 있습니다(또 그래야만 합니다).

특성 라우팅을 사용하면 컨트롤러 이름 및 작업 이름은 작업 선택에 있어서 아무 역할도 하지 **않습니다**. 이 예제는 이전 예제와 동일한 URL을 매칭합니다.

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
> 위의 경로 템플릿은 `action`, `area` 및 `controller`에 대한 경로 매개 변수를 정의하지 않습니다. 사실, 이러한 경로 매개 변수는 특성 경로에 허용되지 않습니다. 경로 템플릿은 이미 작업에 연결되어 있기 때문에 URL에서 작업 이름을 구문 분석할 수 없습니다.

## <a name="attribute-routing-with-httpverb-attributes"></a>Http[Verb] 특성을 사용한 특성 라우팅

특성 라우팅은 `HttpPostAttribute` 같은 `Http[Verb]`를 사용할 수도 있습니다. 이러한 특성은 모두 경로 템플릿을 허용합니다. 이 예제는 동일한 경로 템플릿과 일치하는 두 가지 작업을 보여줍니다.

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

`/products` 같은 URL 경로의 경우 HTTP 동사가 `GET`이면 `ProductsApi.ListProducts` 작업이 실행되고 HTTP 동사가 `POST`이면 `ProductsApi.CreateProduct`가 실행됩니다. 특성 라우팅은 먼저 URL을 경로 특성에 정의된 경로 템플릿 집합과 매칭합니다. 경로 템플릿이 일치하면 `IActionConstraint` 제약 조건을 적용하여 실행 가능한 작업을 확인합니다.

> [!TIP]
> REST API를 빌드할 때 작업 메서드에 `[Route(...)]`를 사용하려는 경우는 거의 없습니다. 작업이 모든 HTTP 메서드를 받기 때문입니다. 보다 구체적인 `Http*Verb*Attributes`를 사용하여 API에서 지원하는 항목을 정확하게 지정하는 것이 좋습니다. REST API의 클라이언트는 특정 논리 작업에 어떤 경로 및 HTTP 동사가 매핑되는지 알아야 합니다.

특성 경로는 특정 작업에 적용되므로 경로 템플릿 정의의 일환으로 필요한 매개 변수를 간단하게 만들 수 있습니다. 이 예제에서 `id`는 URL 경로에 포함되어야 합니다.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

`ProductsApi.GetProduct(int)` 작업은 `/products/3` 같은 URL 경로에 대해 실행되지만 `/products` 같은 URL 경로에 대해서는 실행되지 않습니다. 경로 템플릿 및 관련 옵션에 대한 전체 설명은 [라우팅](../../fundamentals/routing.md)을 참조하세요.

## <a name="route-name"></a>경로 이름

다음 코드는 `Products_List`의 *경로 이름*을 정의합니다.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

경로 이름을 사용하여 특정 경로 기반의 URL을 생성할 수 있습니다. 경로 이름은 라우팅의 URL 매칭 동작에 영향을 미치지 않으며 URL 생성에만 사용됩니다. 경로 이름은 응용 프로그램 전체에서 고유해야 합니다.

> [!NOTE]
> `id` 매개 변수를 선택 사항(`{id?}`)으로 정의하는 규칙 기반 *기본 경로*와는 대조적입니다. API를 정확하게 지정하는 이 기능은 `/products`와 `/products/5`를 서로 다른 작업에 디스패치할 수 있는 등의 장점이 있습니다.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>경로 결합

특성 라우팅의 반복 횟수를 줄이기 위해 컨트롤러의 경로 특성은 개별 작업의 경로 특성과 결합됩니다. 컨트롤러에 정의된 경로 템플릿은 작업의 경로 템플릿에 앞에 추가됩니다. 경로 특성을 컨트롤러에 배치하면 컨트롤러의 **모든** 작업이 특성 라우팅을 사용합니다.

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

이 예제에서 URL 경로 `/products`는 `ProductsApi.ListProducts`와 매칭할 수 있고, URL 경로 `/products/5`는 `ProductsApi.GetProduct(int)`와 매칭할 수 있습니다. 두 작업은 모두 `HttpGetAttribute`로 표시되기 때문에 HTTP `GET`만 매칭합니다.

작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다. 이 예제는 *기본 경로*와 비슷한 URL 경로 집합을 매칭합니다.

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

### <a name="ordering-attribute-routes"></a>특성 경로 순서 지정

정의된 순서대로 실행되는 규칙 기반 경로와는 달리, 특성 라우팅은 트리를 만들고 모든 경로를 동시에 매칭합니다. 이 동작은 경로 전체가 이상적인 순서로 정렬된 것처럼 수행됩니다. 가장 구체적인 경로가 일반적인 경로보다 먼저 실행될 가능성이 있습니다.

예를 들어 `blog/search/{topic}` 같은 경로는 `blog/{*article}` 같은 경로보다 구체적입니다. 논리적으로 말해서, 기본적으로 `blog/search/{topic}` 경로가 가장 먼저 '실행'됩니다. 유일하게 합리적인 순서이기 때문입니다. 규칙 기반 라우팅을 사용하면 개발자가 원하는 순서대로 경로를 배치해야 합니다.

특성 경로는 프레임워크가 제공하는 모든 경로 특성의 `Order` 속성을 사용하여 순서를 구성할 수 있습니다. 경로는 `Order` 속성의 오름차순 정렬에 따라 처리됩니다. 기본 순서는 `0`입니다. `Order = -1`을 사용한 경로 설정은 순서를 설정하지 않는 경로보다 먼저 실행됩니다. `Order = 1`을 사용한 경로 설정은 기본 경로 순서보다 늦게 실행됩니다.

> [!TIP]
> `Order`를 사용하지 마세요. URL 공간에 올바른 라우팅을 위한 명시적 순서 값이 필요한 경우 클라이언트에서도 혼란이 발생할 수 있습니다. 일반적으로 특성 라우팅은 URL이 일치하는 올바른 경로를 선택합니다. URL 생성에 사용되는 기본 순서가 작동하지 않는 경우 일반적으로 경로 이름을 우선적으로 사용하는 것이 `Order` 속성을 적용하는 것보다 간단합니다.

Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다. Razor Pages 항목의 경로 순서에 대한 정보는 [Razor Pages 라우팅 및 앱 규칙: 경로 순서](xref:razor-pages/razor-pages-conventions#route-order)에서 확인할 수 있습니다.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>경로 템플릿에서 토큰 교체([controller] [action] [area])

편의를 위해 특성 경로는 토큰을 대괄호(`[`, `]`)로 묶는 방식으로 *토큰 교체*를 지원합니다. `[action]`, `[area]` 및 `[controller]` 토큰은 경로가 정의된 작업의 작업 이름, 영역 이름 및 컨트롤러 이름으로 교체됩니다. 다음 예제의 작업은 주석에 설명된 URL 경로와 매칭됩니다.

[!code-csharp[](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

토큰 교체는 특성 경로 빌드 과정의 마지막 단계로 발생합니다. 위의 예제는 다음 코드와 동일하게 동작합니다.

[!code-csharp[](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

특성 경로를 상속과 결합할 수도 있습니다. 특히 토큰 교체와 결합하면 더욱 강력합니다.

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

토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다. `[Route("[controller]/[action]", Name="[controller]_[action]")]`는 각 작업에 대해 고유한 경로 이름을 생성합니다.

리터럴 토큰 교체 구분 기호 `[` 또는 `]`와 매칭하려면 문자(`[[` 또는 `]]`)를 반복하여 이스케이프합니다.

::: moniker range=">= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>매개 변수 변환기를 사용하여 토큰 교체 사용자 지정

매개 변수 변환기를 사용하여 토큰 교체를 사용자 지정할 수 있습니다. 매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하며 매개 변수의 값을 변환합니다. 예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.

`RouteTokenTransformerConvention`은 다음과 같은 응용 프로그램 모델 규칙입니다.

* 애플리케이션의 모든 특성 경로에 매개 변수 변환기를 적용합니다.
* 대체되는 특성 경로 토큰 값을 사용자 지정합니다.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

`RouteTokenTransformerConvention`은 `ConfigureServices`에서 옵션으로 등록됩니다.

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

### <a name="multiple-routes"></a>여러 경로

특성 경로는 동일한 작업에 도달하는 여러 경로를 정의하는 것을 지원합니다. 가장 일반적인 사용 방법은 다음 예제와 같이 *기본 규칙 기반 경로*의 동작을 모방하는 것입니다.

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

컨트롤러에 여러 경로 특성을 배치하면 각 경로 특성이 작업 메서드의 각 경로 특성과 결합됩니다.

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

여러 경로 특성(`IActionConstraint`를 구현하는)이 작업에 배치되면 각 작업 제약 조건은 제약 조건을 정의한 특성의 경로 템플릿과 결합됩니다.

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
> 작업에 여러 경로를 사용하는 것이 좋아 보일 수 있지만, 애플리케이션의 URL 공간을 간단하고 잘 정의된 상태로 유지하는 것이 좋습니다. 기존 클라이언트를 지원하려는 경우처럼 꼭 필요한 경우에만 작업에 여러 경로를 사용합니다.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>특성 경로 선택적 매개 변수, 기본값 및 제약 조건 지정

특성 경로는 선택적 매개 변수, 기본값 및 제약 조건을 지정할 수 있는 규칙 기반 경로와 동일한 인라인 구문을 지원합니다.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](../../fundamentals/routing.md#route-template-reference)를 참조하세요.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>`IRouteTemplateProvider`를 사용하는 사용자 지정 경로 특성

프레임워크에서 제공하는 모든 경로 특성(`[Route(...)]`, `[HttpGet(...)]` 등)은 `IRouteTemplateProvider` 인터페이스를 구현합니다. 앱이 시작되면 MVC는 컨트롤러 클래스 및 작업 메서드에서 특성을 찾아 `IRouteTemplateProvider`를 구현하는 특성을 사용하여 초기 경로 집합을 빌드합니다.

개발자 고유의 경로 특성을 정의하는 `IRouteTemplateProvider`를 구현할 수 있습니다. 각각의 `IRouteTemplateProvider`를 사용하여 사용자 지정 경로 템플릿, 순서 및 이름으로 단일 경로를 정의할 수 있습니다.

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

위의 예제의 특성은 `[MyApiController]`가 적용되면 자동으로 `Template`을 `"api/[controller]"`로 설정합니다.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>응용 프로그램 모델을 사용하여 특성 경로 사용자 지정

*응용 프로그램 모델*은 MVC에 의해서 작업을 라우팅하고 실행하는 데 사용되는 모든 메타데이터를 이용하여 시작 시 만들어지는 개체 모델입니다. *응용 프로그램 모델*은 `IRouteTemplateProvider`를 통해서 경로 특성으로부터 수집한 모든 데이터를 포함합니다. 개발자는 시작 시에 응용 프로그램 모델을 수정하는 *규칙*을 작성하여 라우팅 동작을 사용자 지정할 수 있습니다. 이 섹션에서는 애플리케이션 모델을 사용하여 라우팅을 사용자 지정하는 간단한 예제를 보여줍니다.

[!code-csharp[](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>혼합 라우팅: 특성 라우팅 및 규칙 기반 라우팅

MVC 애플리케이션은 규칙 기반 라우팅과 특성 라우팅을 혼합해서 사용할 수 있습니다. 일반적으로 브라우저의 HTML 페이지를 처리하는 컨트롤러에는 규칙 기반 경로를 사용하고 REST API를 제공하는 컨트롤러에는 특성 라우팅을 사용합니다.

작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다. 컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다. 특성 경로를 정의하는 동작은 규칙 기반 경로를 통해 도달할 수 없으며 그 반대도 마찬가지입니다. 컨트롤러의 **모든** 경로 특성은 컨트롤러에 있는 모든 작업에서 특성 라우팅을 사용하게 만듭니다.

> [!NOTE]
> 두 라우팅 시스템 간의 차이는 URL이 경로 템플릿과 일치한 후 적용되는 프로세스입니다. 규칙 기반 라우팅에서는 일치 항목의 경로 값을 사용하여 모든 규칙 기반 라우팅된 작업의 조회 테이블에서 작업 및 컨트롤러를 선택합니다. 특성 라우팅에서는 각 템플릿이 이미 작업과 연결되어 있으며, 더 이상의 조회가 필요 없습니다.

## <a name="complex-segments"></a>복잡한 세그먼트

복잡한 세그먼트(예: `[Route("/dog{token}cat")]`)는 non-greedy 방식으로 오른쪽에서 왼쪽으로 리터럴을 매칭하여 처리됩니다. 자세한 내용은 [소스 코드](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)를 참조하세요. 자세한 내용은 [이 문제](https://github.com/aspnet/AspNetCore.Docs/issues/8197)를 참조하세요.

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>URL 생성

MVC 애플리케이션은 라우팅의 URL 생성 기능을 사용하여 작업에 대한 URL 링크를 생성할 수 있습니다. URL을 생성하면 하드 코딩된 URL이 제거되어 코드를 더욱 견고하게 유지할 수 있습니다. 이 섹션에서는 MVC에서 제공하는 URL 생성 기능을 중심으로 기본적인 URL 생성 원리에 대해서만 다룰 것입니다. URL 생성에 대한 자세한 설명은 [라우팅](../../fundamentals/routing.md)을 참조하세요.

`IUrlHelper` 인터페이스는 URL 생성을 위한 MVC와 라우팅 간의 기본 인프라입니다. 컨트롤러, 보기 및 보기 구성 요소의 `Url` 속성을 통해서 사용 가능한 `IUrlHelper`의 인스턴스를 찾을 수 있습니다.

이 예제에서 `IUrlHelper` 인터페이스는 `Controller.Url` 속성을 통해 다른 작업에 대한 URL을 생성하는 데 사용됩니다.

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

응용 프로그램이 기본 규칙 기반 경로를 사용할 경우 `url` 변수의 값은 URL 경로 문자열 `/UrlGeneration/Destination`입니다. 이 URL 경로는 현재 요청의 경로 값(앰비언트 값)을 `Url.Action`에 전달된 값과 결합하고 그 값을 경로 템플릿에 대체하여 생성됩니다.

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

경로 템플릿에 있는 각 경로 매개 변수의 값은 이름을 값 및 앰비언트 값과 매칭한 값으로 바뀝니다. 값이 없는 경로 매개 변수는 기본값이 있는 경우 기본값을 사용할 수 있고, 값이 선택 사항인 경우 건너뛰어도 됩니다(이 예제의 `id`처럼). 필요한 경로 매개 변수에 해당 값이 없는 경우 URL 생성에 실패합니다. 경로에 대한 URL 생성이 실패하면 모든 경로를 시도하거나 일치 항목을 찾을 때까지 그 다음 경로를 시도합니다.

위의 `Url.Action`예제는 규칙 기반 라우팅을 가정합니다. 개념은 다르지만 URL 생성 원리는 특성 라우팅과 비슷하게 동작합니다. 규칙 기반 라우팅에서 경로 값은 템플릿을 확장하는 데 사용되고, 일반적으로`controller` 및 `action`의 경로 값은 해당 템플릿에 표시됩니다. 라우팅에서 매핑된 URL이 *규칙*을 따르기 때문입니다. 특성 라우팅에서는 `controller` 및 `action`의 경로 값이 템플릿에 표시되지 않는 대신, 사용할 템플릿을 조회하는 데 사용됩니다.

이 예제는 특성 라우팅을 사용합니다.

[!code-csharp[](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC는 모든 특성 라우팅 작업의 조회 테이블을 작성하고 `controller` 및 `action` 값을 매칭하여 URL 생성에 사용할 경로 템플릿을 선택합니다. 위의 예제에서는 `custom/url/to/destination`이 생성됩니다.

### <a name="generating-urls-by-action-name"></a>작업 이름으로 URL 생성

`Url.Action`(`IUrlHelper`. `Action`) 및 모든 관련 오버로드는 개발자가 컨트롤러 이름과 작업 이름을 지정하여 연결 대상을 지정하려 한다는 생각을 바탕으로 합니다.

> [!NOTE]
> `Url.Action`을 사용하면 `controller` 및 `action`의 현재 경로 값이 자동으로 지정됩니다. `controller` 및 `action`의 값은 모두 *앰비언트 값* **및** *값*의 일부입니다. `Url.Action` 메서드는 항상 `action` 및 `controller`의 현재 값을 사용하며 현재 작업에 라우팅하는 URL 경로를 생성합니다.

라우팅은 앰비언트 값의 값을 사용하여 개발자가 URL을 생성할 때 제공하지 않은 정보를 채웁니다. `{a}/{b}/{c}/{d}` 같은 경로와 `{ a = Alice, b = Bob, c = Carol, d = David }` 앰비언트 값을 사용하면 모든 경로 매개 변수가 값을 갖기 때문에 추가 값이 없어도 라우팅에서 URL을 생성하기에 충분한 정보가 제공됩니다. `{ d = Donovan }` 값을 추가하면 `{ d = David }` 값이 무시되고 `Alice/Bob/Carol/Donovan` URL 경로가 생성됩니다.

> [!WARNING]
> URL 경로는 계층적입니다. 위의 예에서 `{ c = Cheryl }` 값을 추가하면 두 `{ c = Carol, d = David }` 값이 모두 무시됩니다. 이 경우 `d`의 값이 없기 때문에 URL 생성이 실패합니다. 원하는 `c` 및 `d` 값을 지정해야 합니다.  기본 경로(`{controller}/{action}/{id?}`)를 사용해도 이 문제가 발생할 가능성은 있지만, `Url.Action`이 항상 `controller` 및 `action` 값을 명시적으로 지정하기 때문에 실제로 이 동작이 발생하는 경우는 극히 드뭅니다.

`Url.Action` 의 긴 오버로드에서는 `controller` 및 `action` 이외의 경로 매개 변수의 값을 제공하기 위한 추가 *경로 값* 개체가 사용됩니다. `Url.Action("Buy", "Products", new { id = 17 })`처럼 `id`와 함께 사용되는 것을 흔히 볼 수 있습니다. 관례상 *경로 값* 개체는 대부분 무명 형식의 개체지만, `IDictionary<>` 또는 *오래된 일반 .NET 개체*일 수도 있습니다. 경로 매개 변수와 일치하지 않는 추가 경로 값들은 쿼리 문자열에 포함됩니다.

[!code-csharp[](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> 절대 URL을 만들려면 `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`을 받는 오버로드를 사용합니다.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>경로로 URL 생성

위의 코드에서는 컨트롤러 및 작업 이름을 전달하여 URL을 생성하는 방법을 보여줍니다. `IUrlHelper`는 `Url.RouteUrl` 메서드 모음도 제공합니다. 이 메서드는 `Url.Action`과 비슷하지만, `action` 및 `controller`의 현재 값을 경로 값에 복사하지 않습니다. 가장 일반적인 사용법은 URL을 생성할 경로를 지정하기 위한 경로 이름을 지정하는 것으로, 일반적으로 컨트롤러 또는 작업 이름을 지정하지 *않습니다*.

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>HTML에서 URL 생성

`IHtmlHelper`는 각각 `<form>` 및 `<a>` 요소를 생성하는 `HtmlHelper` 메서드인 `Html.BeginForm` 및 `Html.ActionLink`를 제공합니다. 이러한 메서드는 `Url.Action` 메서드를 사용하여 URL을 생성하며 비슷한 인수를 받습니다. `HtmlHelper`에 대한 `Url.RouteUrl` 보조 도구는 `Html.BeginRouteForm` 및 `Html.RouteLink`이며 서로 기능이 비슷합니다.

TagHelper는 `form` TagHelper 및 `<a>` TagHelper를 통해 URL을 생성합니다. 둘 다 구현에 `IUrlHelper`를 사용합니다. 자세한 내용은 [양식 작업](../views/working-with-forms.md)을 참조하세요.

보기 내에서 `IUrlHelper`는 `Url` 속성을 통해서 위에서 다루지 않은 임시 URL을 생성하기 위해 사용할 수 있습니다.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>작업 결과에 URL 생성

위의 예제에서는 컨트롤러에서 `IUrlHelper`를 사용하는 방법을 보여주었으며, 컨트롤러에서 가장 일반적인 사용법은 URL을 작업 결과의 일부로 생성하는 것입니다.

`ControllerBase` 및 `Controller` 기본 클래스는 다른 작업을 참조하는 작업 결과에 대한 편의 메서드를 제공합니다. 한 가지 일반적인 사용법은 사용자 입력을 수락한 후 리디렉션하는 것입니다.

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

작업 결과 팩터리 메서드는 `IUrlHelper`의 메서드와 비슷한 패턴을 따릅니다.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>전용 규칙 기반 경로의 특별한 사례

규칙 기반 라우팅은 *전용 규칙 기반 경로*라고 하는 특별한 경로 정의를 사용할 수 있습니다. 아래 예제에서 `blog`라는 이름의 경로는 전용 규칙 기반 경로입니다.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

이러한 경로 정의를 사용하면 `Url.Action("Index", "Home")`은 `default` 경로를 사용하여 URL 경로 `/`를 생성합니다. 그런데 그 이유는 무엇일까요? `{ controller = Home, action = Index }` 경로 값이면 `blog`를 사용하여 URL을 생성하기에 충분하며, 그 결과는 `/blog?action=Index&controller=Home`가 될 것이라고 추측할 수도 있습니다.

전용 규칙 기반 경로는 URL 생성과 관련하여 경로의 "지나친 욕심"을 방지하는 해당 경로 매개 변수가 없는 기본 값의 특별한 동작을 사용합니다. 이 예제에서 기본값은 `{ controller = Blog, action = Article }`이고, `controller` 및 `action`이 경로 매개 변수로 표시되지 않습니다. 라우팅이 URL 생성을 수행할 때 입력한 값이 기본값과 일치해야 합니다. `{ controller = Home, action = Index }` 값이 `{ controller = Blog, action = Article }`과 일치하지 않기 때문에 `blog`를 사용한 URL 생성은 실패합니다. 그 후 라우팅이 `default`로 대체되고, 이것은 성공합니다.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Areas

[영역](areas.md)은 관련 기능을 별도의 라우팅-네임스페이스(컨트롤러 작업용) 및 폴더 구조(보기용)로 그룹화하는 데 사용되는 MVC 기능입니다. 영역을 사용하면 컨트롤러의 *영역*이 서로 다른 한, 응용 프로그램 하나에서 이름이 같은 컨트롤러를 여러 개 사용할 수 있습니다. 영역을 사용하면 또 다른 경로 매개 변수 `area`를 `controller` 및 `action`에 추가하여 라우팅을 위한 계층 구조를 생성합니다. 이 섹션에서는 라우팅이 영역과 상호 작용하는 방법을 설명합니다. 보기에 영역을 사용하는 자세한 방법은 [영역](areas.md)을 참조하세요.

다음 예제는 기본 규칙 기반 경로 및 `Blog`라는 이름의 영역에 대한 *영역 경로*를 사용하도록 MVC를 구성합니다.

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

`/Manage/Users/AddUser` 같은 URL 경로를 매칭할 때, 첫 번째 경로는 `{ area = Blog, controller = Users, action = AddUser }` 경로 값을 생성합니다. `area` 경로 값은 `area`의 기본값을 사용하여 생성되며, 사실 `MapAreaRoute`를 통해 생성되는 경로는 다음과 같습니다.

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`는 제공된 영역 이름(여기에서는 `Blog`)을 사용하는 `area`에 대해 기본값 및 제약 조건을 모두 사용하여 경로를 생성합니다. 기본값은 경로가 항상 `{ area = Blog, ... }`를 생성함을 보장하고, 제약 조건은 URL 생성을 위해 `{ area = Blog, ... }` 값을 필요로 합니다.

> [!TIP]
> 규칙 기반 라우팅은 순서에 영향을 받습니다. 일반적으로 영역이 있는 경로는 영역이 없는 경로에 비해 구체적이기 때문에 경로 테이블의 앞부분에 배치되어야 합니다.

위의 예제를 사용하면 경로 값이 다음 작업과 매칭됩니다.

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute`는 컨트롤러가 영역의 일부임을 나타내며, 이때 이 컨트롤러가 `Blog` 영역에 있다고 표현합니다. `[Area]` 특성이 없는 컨트롤러는 어떠한 영역의 구성원도 아니며, 라우팅에서 `area` 경로 값을 제공해도 매칭되지 **않습니다**. 다음 예제에서는 나열된 첫 번째 컨트롤러만 `{ area = Blog, controller = Users, action = AddUser }` 경로 값과 매칭됩니다.

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> 각 컨트롤러의 네임스페이스는 완전성을 위해서 사용되었습니다. 네임스페이스가 없으면 컨트롤러에서 이름 충돌이 발생하고 컴파일러 오류가 생성됩니다. 클래스 네임스페이스는 MVC의 라우팅에 영향을 주지 않습니다.

처음 두 컨트롤러는 영역의 구성원이며, `area` 경로 값으로 해당 영역 이름을 제공하는 경우에만 매칭됩니다. 세 번째 컨트롤러는 어떤 영역의 구성원도 아니며, 라우팅에서 `area`에 대한 값을 제공하지 않을 때에만 매칭됩니다.

> [!NOTE]
> 매칭 측면에서 *값 없음*, 즉 `area` 값이 없는 것은 `area`의 값이 null 또는 빈 문자열인 경우와 동일합니다.

영역 내에서 작업을 실행하면 `area`의 경로 값이 라우팅에서 URL을 생성하기 위해 사용되는 *앰비언트 값*으로 제공됩니다. 즉, 기본적으로 영역은 다음 예제에서 볼 수 있듯이 URL 생성을 위한 *스티커처럼* 동작합니다.

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>IActionConstraint 이해

> [!NOTE]
> 이 섹션에서는 프레임워크의 내부 구조 및 MVC가 실행할 작업을 선택하는 원리를 자세히 알아봅니다. 일반적인 응용 프로그램에는 사용자 지정 `IActionConstraint`가 필요 없습니다.

이 인터페이스에 익숙하지 않은 분들도 이미 `IActionConstraint`를 사용해 보셨을 것입니다. `[HttpGet]` 특성 그리고 그와 비슷한 `[Http-VERB]` 특성들은 `IActionConstraint`를 구현하여 작업 메서드 실행을 제한합니다.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

기본 규칙 기반 경로를 사용한다고 가정할 때, URL 경로 `/Products/Edit`는 여기에 보이는 두 작업 **모두**와 매칭하는 `{ controller = Products, action = Edit }` 값을 생성합니다. `IActionConstraint` 용어에서는 두 작업이 경로 데이터와 매칭되므로 두 작업이 후보로 간주된다고 말할 수 있습니다.

`HttpGetAttribute`가 실행되면 *Edit()* 는 *GET*에 대해서만 일치하고 다른 HTTP 동사에 대해서는 일치하지 않습니다. `Edit(...)` 작업은 정의된 제약 조건이 없으므로 모든 HTTP 동사와 매칭됩니다. 따라서 `POST`를 고려해본다면 `Edit(...)`만 일치합니다. 하지만 `GET`의 경우에는 여전히 두 작업 모두 매칭될 수 있습니다. 그러나`IActionConstraint`가 적용된 작업이 적용되지 않은 작업보다 항상 *더 적합한* 것으로 간주됩니다. 따라서 `Edit()`에는 `[HttpGet]`가 있으므로 보다 구체적인 것으로 간주되며, 두 작업이 모두 매칭될 수 있는 경우에도 이 작업이 선택됩니다.

개념적으로 `IActionConstraint`는 *오버로딩*의 한 가지 형태이지만, 같은 이름의 메서드를 오버로딩하는 대신, 동일한 URL을 매칭하는 작업 간에 오버로딩합니다. 특성 라우팅에서도 `IActionConstraint`를 사용하며, 다른 두 컨트롤러의 작업이 모두 후보로 간주될 수 있습니다.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>IActionConstraint 구현

`IActionConstraint`를 구현하는 가장 간단한 방법은 `System.Attribute`에서 파생된 클래스를 만들어서 작업 및 컨트롤러에 배치하는 것입니다. MVC는 특성으로 적용된 `IActionConstraint`를 자동으로 검색합니다. 애플리케이션 모델을 사용하여 제약 조건을 적용할 수 있으며, 제약 조건이 적용되는 방식을 개발자가 메타프로그래밍할 수 있는 가장 유연한 접근 방식일 것입니다.

다음 예제의 제약 조건은 경로 데이터의 *국가 코드*를 기반으로 작업을 선택합니다. [GitHub의 전체 예제](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

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

개발자는 `Accept` 메서드를 구현하고 제약 조건이 실행되는 '순서'를 지정해야 합니다. 이 예제에서 `Accept` 메서드는 `country` 경로 값이 일치하면 작업이 일치함을 나타내기 위해 `true`를 반환합니다. 비-특성 작업으로 대체할 수 있다는 점에서 `RouteValueAttribute`와는 다릅니다. 이 예제는 `en-US` 작업을 정의하면 `fr-FR` 같은 국가 코드는 `[CountrySpecific(...)]`이 적용되지 않은 보다 일반적인 컨트롤러로 대체되는 것을 보여줍니다.

`Order` 속성은 제약 조건이 소속되는 *단계*를 결정합니다. 작업 제약 조건은 `Order`에 따라 그룹으로 실행됩니다. 예를 들어 프레임워크에서 제공하는 모든 HTTP 메서드 특성은 같은 단계에서 실행될 수 있도록 동일한 `Order` 값을 사용합니다. 원하는 정책을 구현하는 데 필요한 만큼 단계를 포함할 수 있습니다.

> [!TIP]
> `Order`에 대한 값을 결정하려면 HTTP 메서드보다 제약 조건이 먼저 적용되어야 하는지 생각해야 합니다. 숫자가 낮을수록 먼저 실행됩니다.
