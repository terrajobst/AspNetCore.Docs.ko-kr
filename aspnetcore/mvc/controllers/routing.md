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
# <a name="routing-to-controller-actions-in-aspnet-core"></a>ASP.NET Core의 컨트롤러 작업에 라우팅

[라이언 노왁,](https://github.com/rynowak) [커크 라킨,](https://twitter.com/serpent5) [릭 앤더슨](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET 코어 컨트롤러는 라우팅 [미들웨어를](xref:fundamentals/middleware/index) 사용하여 들어오는 요청의 URL을 일치시키고 [작업에](#action)매핑합니다.  경로 템플릿:

* 시작 코드 또는 특성에 정의되어 있습니다.
* URL 경로가 [작업과](#action)일치하는 방법을 설명합니다.
* 링크에 대한 URL을 생성하는 데 사용됩니다. 생성된 링크는 일반적으로 응답으로 반환됩니다.

작업은 [종래로 라우팅되거나](#cr) [특성 라우팅됩니다.](#ar) 컨트롤러 또는 [작업에](#action) 경로를 배치하면 특성 라우팅됩니다. 자세한 내용은 [혼합 라우팅](#routing-mixed-ref-label)을 참조하세요.

이 문서는 다음과 같은 것입니다.

* MVC와 라우팅 간의 상호 작용에 대해 설명합니다.
  * 일반적인 MVC 앱이 라우팅 기능을 사용하는 방법.
  * 둘 다 다 다룹니다.
    * [일반적으로 라우팅은](#cr) 일반적으로 컨트롤러 및 뷰와 함께 사용됩니다.
    * REST API와 함께 사용되는 *특성 라우팅입니다.* REST API에 대한 라우팅에 주로 관심이 있는 경우 [REST API에 대한 특성 라우팅](#ar) 섹션으로 이동합니다.
  * 고급 라우팅 세부 정보는 [라우팅을](xref:fundamentals/routing) 참조하십시오.
* ASP.NET 코어 3.0에 추가 된 기본 라우팅 시스템을 참조 합니다. 호환성을 위해 이전 버전의 라우팅과 함께 컨트롤러를 사용할 수 있습니다. 지침은 [2.2-3.0 마이그레이션 가이드를](xref:migration/22-to-30) 참조하십시오. 레거시 라우팅 시스템의 참조 자료는 [이 문서의 2.2 버전을](xref:mvc/controllers/routing?view=aspnetcore-2.2) 참조하십시오.

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>기존 경로 설정

`Startup.Configure`일반적으로 [기존 라우팅을](#crd)사용할 때 다음과 유사한 코드가 있습니다.

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

호출 에서 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>"를 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 호출하는 경우 단일 경로를 만드는 데 사용됩니다. 단일 경로의 `default` 이름은 경로입니다. 컨트롤러 및 뷰가 있는 대부분의 앱은 `default` 경로와 유사한 경로 템플릿을 사용합니다. REST API는 [특성 라우팅을](#ar)사용해야 합니다.

경로 템플릿 `"{controller=Home}/{action=Index}/{id?}"`:

* 다음과 같은 URL 경로와 일치합니다.`/Products/Details/5`
* 경로를 토큰화하여 `{ controller = Products, action = Details, id = 5 }` 경로 값을 추출합니다. 경로 값을 추출하면 앱에 명명된 `ProductsController` 컨트롤러와 작업이 `Details` 있는 경우 일치합니다.

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`모델은 `id = 5` `id` 매개 변수를 로 설정하는 `5`값을 바인딩합니다. 자세한 내용은 [모델 바인딩을](xref:mvc/models/model-binding) 참조하십시오.
* `{controller=Home}`기본값으로 `Home` `controller`정의합니다.
* `{action=Index}`기본값으로 `Index` `action`정의합니다.
*  의 `?` `{id?}` 문자는 `id` 선택 사항으로 정의합니다.
  * 기본 및 선택적 경로 매개 변수는 매칭을 위해 URL 경로에 반드시 있어야 하는 것은 아닙니다. 경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.
* URL 경로와 `/`일치합니다.
* 경로 값을 `{ controller = Home, action = Index }`생성합니다.

기본값과 `controller` `action` 값을 사용하는 값입니다. `id`URL 경로에 해당 세그먼트가 없기 때문에 값을 생성하지 않습니다. `/``HomeController` 및 `Index` 작업이 있는 경우에만 일치합니다.

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

앞의 컨트롤러 정의 및 경로 `HomeController.Index` 템플릿을 사용하여 다음 URL 경로에 대해 작업이 실행됩니다.

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

URL 경로는 `/` 경로 템플릿 `Home` 기본 `Index` 컨트롤러 및 작업을 사용합니다. URL 경로는 `/Home` 경로 템플릿 `Index` 기본 작업을 사용합니다.

편의 메서드인 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>를 사용하여:

```csharp
endpoints.MapDefaultControllerRoute();
```

대체:

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

라우팅은 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> 및 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 미들웨어를 사용하여 구성됩니다. 컨트롤러를 사용하려면 다음을 수행하십시오.

* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> 내부를 `UseEndpoints` 호출하여 [특성 라우팅된](#ar) 컨트롤러를 매핑합니다.
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 호출하거나 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, [기존에 라우팅된 컨트롤러를](#cr) 매핑합니다.

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>규칙 기반 라우팅

기존의 라우팅은 컨트롤러 및 뷰와 함께 사용됩니다. `default` 경로인:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

는 *규칙 기반 라우팅*의 예제입니다. URL 경로에 대한 *규칙을* 설정하기 때문에 *기존 라우팅이라고* 합니다.

* 첫 번째 경로 `{controller=Home}`세그먼트는 컨트롤러 이름에 매핑됩니다.
* 두 번째 `{action=Index}`세그먼트 , " [작업](#action) 이름에 매핑됩니다.
* 세 번째 `{id?}` 세그먼트는 선택적 `id`세그먼트에 사용됩니다. `?` in은 `{id?}` 선택 사항입니다. `id`모델 엔터티에 매핑하는 데 사용됩니다.

이 `default` 경로를 사용하는 URL 경로는 다음과 같은 것입니다.

* `/Products/List`맵을 `ProductsController.List` 사용하여 작업에 매핑됩니다.
* `/Blog/Article/17``BlogController.Article` 맵을 17에 바인딩합니다. `id`

이 매핑:

* 컨트롤러 및 [작업](#action) **이름만**을 기반으로 합니다.
* 네임스페이스, 소스 파일 위치 또는 메서드 매개 변수를 기반으로 하지 않습니다.

기본 경로와 함께 기존 라우팅을 사용하면 각 작업에 대한 새 URL 패턴을 마련할 필요 없이 앱을 만들 수 있습니다. [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 스타일 동작이 있는 앱의 경우 컨트롤러 전체에서 URL에 대한 일관성이 있습니다.

* 코드를 단순화하는 데 도움이 됩니다.
* UI를 더 쉽게 예측할 수 있습니다.

> [!WARNING]
> 위의 `id` 코드의 경우 경로 템플릿에 의해 선택 사항으로 정의됩니다. 작업은 URL의 일부로 제공된 선택적 ID 없이 실행할 수 있습니다. 일반적으로 URL에서 생략되는 경우:`id`
>
> * `id`모델 바인딩으로 `0` 설정됩니다.
> * 데이터베이스 일치에 `id == 0`엔터티가 없습니다.
>
> [특성 라우팅은](#ar) 일부 작업에 필요한 ID를 만들기 위해 세밀한 컨트롤을 제공하며 다른 작업에는 필요하지 않습니다. 규칙에 따라 설명서에는 올바른 `id` 사용법에 표시될 가능성이 있는 경우와 같은 선택적 매개 변수가 포함되어 있습니다.

대부분의 앱은 URL이 읽을 수 있고 의미 있도록 기본적이고 설명적인 라우팅 체계를 선택해야 합니다. 기본 기존 경로 `{controller=Home}/{action=Index}/{id?}`:

* 기본적이고 설명이 포함된 라우팅 체계를 지원합니다.
* UI 기반 앱에 대한 유용한 시작점입니다.
* 많은 웹 UI 앱에 필요한 유일한 경로 템플릿입니다. 대규모 웹 UI 앱의 경우 필요한 경우 [영역을](#areas) 사용하는 다른 경로가 있습니다.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>및 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :

* 호출된 순서에 따라 **끝점에 주문** 값을 자동으로 할당합니다.

코어 3.0 이후의 ASP.NET 엔드포인트 라우팅:

* 경로개념이 없습니다.
* 확장성 실행에 대한 순서 보증을 제공하지 않으므로 모든 끝점이 한 번에 처리됩니다.

[로깅](xref:fundamentals/logging/index)을 사용하도록 설정하여 <xref:Microsoft.AspNetCore.Routing.Route>와 같은 기본 제공 라우팅 구현에서 요청과 일치시키는 방법을 확인하세요.

[특성 라우팅은](#ar) 이 문서의 후반부에서 설명합니다.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>여러 개의 기존 경로

여러 [개의 기존](#cr) 경로를 `UseEndpoints` 추가하여 내부에 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 추가할 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>수 있습니다. 이렇게 하면 여러 규칙을 정의하거나 다음과 같은 특정 [작업에](#action)전용인 기존 경로를 추가할 수 있습니다.

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

앞의 코드의 `blog` 경로는 **전용 기존 경로입니다.** 다음과 같은 이유로 전용 기존 경로라고 합니다.

* 그것은 [기존의 라우팅을](#cr)사용합니다.
* 그것은 특정 [작업에](#action)전념하고 있습니다.

왜냐하면 `action` 경로 템플릿에 `"blog/{*article}"` 매개 변수로 나타나지 않기 때문입니다. `controller`

* 기본값만 `{ controller = "Blog", action = "Article" }`가질 수 있습니다.
* 이 경로는 항상 `BlogController.Article`작업에 매핑됩니다.

`/Blog`및 `/Blog/Article`블로그 `/Blog/{any-string}` 경로와 일치하는 유일한 URL 경로입니다.

위의 예는 다음과 같은 것입니다.

* `blog`경로가 먼저 추가되기 때문에 `default` 라우트가 라우트보다 일치하는 우선 순위가 높습니다.
* IS 및 URL의 일부로 아티클 이름을 가지는 것이 일반적인 [Slug](https://developer.mozilla.org/docs/Glossary/Slug) 스타일 라우팅의 예입니다.

> [!WARNING]
> ASP.NET 코어 3.0 이상에서는 라우팅이 수행되지 않습니다.
> * *경로라는*개념을 정의합니다. `UseRouting`미들웨어 파이프라인에 경로 일치를 추가합니다. 미들웨어는 `UseRouting` 앱에 정의된 끝점 집합을 살펴보고 요청에 따라 최적의 끝점 일치를 선택합니다.
> * 또는 .와 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>같은 확장성의 실행 순서에 대한 보증을 제공합니다.
>
>라우팅에 대한 참조 자료에 대한 [라우팅을](xref:fundamentals/routing) 참조하십시오.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>기존 라우팅 순서

기존 라우팅은 앱에서 정의한 동작과 컨트롤러의 조합과만 일치합니다. 이는 기존 경로가 겹치는 경우를 단순화하기 위한 것입니다.
을 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>사용하여 경로를 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> 추가하고 호출된 순서에 따라 끝점에 주문 값을 자동으로 할당합니다. 이전에 나타나는 경로의 일치 항목의 우선 순위가 높습니다. 규칙 기반 라우팅은 순서에 영향을 받습니다. 일반적으로 영역이 있는 경로는 영역이 없는 경로보다 더 구체적이므로 더 일찍 배치해야 합니다. 캐치 와 [전용 된 기존](#dcr) `{*article}` 경로 같은 모든 경로 매개 변수 너무 [욕심](xref:fundamentals/routing#greedy)을 만들 수 있습니다., 다른 경로에 의해 일치 하도록 의도 한 URL일치 의미. 욕심 이 일치를 방지하기 위해 경로 테이블의 나중에 욕심 경로를 넣어.

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>모호한 작업 해결

라우팅을 통해 두 끝점이 일치하는 경우 라우팅은 다음 중 하나를 수행해야 합니다.

* 가장 적합한 후보를 선택하십시오.
* 예외를 throw합니다.

다음은 그 예입니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

앞의 컨트롤러는 일치하는 두 가지 작업을 정의합니다.

* URL 경로`/Products33/Edit/17`
* 라우팅 `{ controller = Products33, action = Edit, id = 17 }`데이터 .

MVC 컨트롤러의 일반적인 패턴입니다.

* `Edit(int)`을 통해 제품을 편집할 수 있는 양식이 표시됩니다.
* `Edit(int, Product)`게시된 양식을 처리합니다.

올바른 경로를 해결하려면 다음을 수행하십시오.

* `Edit(int, Product)`요청이 HTTP `POST`인 경우 선택됩니다.
* `Edit(int)`[HTTP 동사가](#verb) 다른 것이면 선택됩니다. `Edit(int)`일반적으로 를 `GET`통해 호출됩니다.

은 <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`요청의 HTTP 메서드에 따라 선택할 수 있도록 라우팅에 제공됩니다. `Edit(int, Product)` `Edit(int)`은 `HttpPostAttribute` 보다 더 나은 일치합니다.

과 같은 `HttpPostAttribute`속성의 역할을 이해하는 것이 중요합니다. 다른 [HTTP 동사에](#verb)대해 유사한 특성이 정의됩니다. [기존 라우팅에서는](#cr)작업에서 쇼 양식의 일부인 경우 동일한 작업 이름을 사용하고 양식 워크플로를 제출하는 것이 일반적입니다. 예를 들어 [두 편집 작업 메서드 검사를](xref:tutorials/first-mvc-app/controller-methods-views#get-post)참조하십시오.

라우팅이 최적의 후보를 선택할 수 없는 <xref:System.Reflection.AmbiguousMatchException> 경우 일치하는 여러 끝점을 나열하는 throw됩니다.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>기존 루트 이름

`"blog"` 문자열과 `"default"` 다음 예제의 일반적인 경로 이름은 다음과 같습니다.

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

경로 이름은 경로에 논리적 이름을 지정합니다. 명명된 경로는 URL 생성에 사용할 수 있습니다. 명명된 경로를 사용하면 경로 순서를 지정하면 URL 생성이 복잡해질 수 있는 경우 URL 생성이 간소화됩니다. 경로 이름은 응용 프로그램 전체에 고유한 여야 합니다.

경로 이름:

* URL 일치 또는 요청 처리에 영향을 미치지 않습니다.
* URL 생성에만 사용됩니다.

경로 이름 개념은 [라우팅에서 IEndpointNameMetadata로](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)표시됩니다. 용어 **경로 이름** 및 **끝점 이름**:

* 상호 교환할 수 있습니다.
* 설명서및 코드에 사용되는 것은 설명하는 API에 따라 다릅니다.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>REST API에 대한 특성 라우팅

REST API는 특성 라우팅을 사용하여 [HTTP 동사에](#verb)의해 작업이 표시되는 리소스 집합으로 앱의 기능을 모델링해야 합니다.

특성 라우팅은 특성 모음을 사용하여 작업을 경로 템플릿에 직접 매핑합니다. 다음 `StartUp.Configure` 코드는 REST API에 일반적으로 사용되며 다음 샘플에서 사용됩니다.

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

앞의 코드에서는 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> 특성 라우트된 컨트롤러를 매핑하기 위해 내부로 `UseEndpoints` 호출됩니다.

다음 예제에서,

* 앞의 `Configure` 방법이 사용됩니다.
* `HomeController`기본 기존 경로가 `{controller=Home}/{action=Index}/{id?}` 일치하는 URL 집합과 일치합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

작업은 `HomeController.Index` 모든 URL 경로에 대해 `/`실행됩니다. `/Home` `/Home/Index` `/Home/Index/3`

이 예제에서는 특성 라우팅과 기존 [라우팅](#cr)간의 주요 프로그래밍 차이점을 강조 합니다. 특성 라우팅은 경로를 지정하기 위해 더 많은 입력이 필요합니다. 기존의 기본 경로는 경로를 보다 간결하게 처리합니다. 그러나 특성 라우팅을 허용하고 각 [작업에](#action)적용되는 경로 템플릿을 정확하게 제어해야 합니다.

특성 라우팅을 사용하면 컨트롤러 이름과 작업 이름이 일치하는 작업을 **수행하지 않습니다.** 다음 예제는 이전 예제와 동일한 URL과 일치합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

다음 코드는 다음에 `action` 대한 `controller`토큰 대체를 사용합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

다음 코드는 `[Route("[controller]/[action]")]` 컨트롤러에 적용됩니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

앞의 코드에서 `Index` 메서드 템플릿은 미리 `/` 준비하거나 `~/` 경로 템플릿에 대해 준비해야 합니다. 작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다.

경로 템플릿 선택에 대한 정보는 [경로 템플릿 우선 순위를](xref:fundamentals/routing#rtp) 참조하십시오.

## <a name="reserved-routing-names"></a>예약된 라우팅 이름

다음 키워드는 컨트롤러 또는 Razor 페이지를 사용할 때 예약된 경로 매개 변수 이름입니다.

* `action`
* `area`
* `controller`
* `handler`
* `page`

특성 `page` 라우팅을 사용하여 경로 매개 변수로 사용하는 것은 일반적인 오류입니다. 이렇게 하면 URL 생성과 일관되지 않고 혼동되는 동작이 발생합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

URL 생성 작업이 Razor 페이지 또는 컨트롤러를 참조하는지 여부를 확인하기 위해 URL 생성에서 특수 매개 변수 이름을 사용합니다.

<a name="verb"></a>

## <a name="http-verb-templates"></a>HTTP 동사 템플릿

ASP.NET 코어에는 다음과 같은 HTTP 동사 템플릿이 있습니다.

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[Http패치]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>경로 템플릿

ASP.NET 코어에는 다음과 같은 경로 템플릿이 있습니다.

* 모든 [HTTP 동사 템플릿은](#verb) 경로 템플릿입니다.
* [[루트]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>http 동사 속성이 있는 특성 라우팅

다음 컨트롤러를 고려하십시오.

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

위의 코드에서

* 각 작업에는 `[HttpGet]` HTTP GET 요청에만 일치를 제한하는 특성이 포함되어 있습니다.
* 작업에는 `GetProduct` `"{id}"` 템플릿이 포함되므로 `id` 컨트롤러의 `"api/[controller]"` 템플릿에 추가됩니다. 메서드 템플릿은 `"api/[controller]/"{id}""`. 따라서 이 작업은 양식 `/api/test2/xyz`,`/api/test2/123`및`/api/test2/{any string}`기타의 GET 요청만 일치합니다.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* 작업에는 `GetIntProduct` 템플릿이 포함되어 있습니다. `"int/{id:int}")` 템플릿 `:int` 부분은 `id` 루트 값을 정수로 변환할 수 있는 문자열로 제한합니다. GET 요청 `/api/test2/int/abc`:
  * 이 작업과 일치하지 않습니다.
  * [404 를 찾을 수 없는 오류를 반환합니다.](https://developer.mozilla.org/docs/Web/HTTP/Status/404)
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* `GetInt2Product` 작업은 템플릿에 포함되지만 `{id}` 정수로 변환할 수 있는 값으로 제한되지는 `id` 않습니다. GET 요청 `/api/test2/int2/abc`:
  * 이 경로와 일치합니다.
  * 모델 바인딩이 `abc` 정수로 변환되지 않습니다. 메서드의 매개 변수는 `id` 정수입니다.
  * 모델 바인딩이 정수로 변환하지`abc` 못했기 때문에 [400 잘못된 요청을](https://developer.mozilla.org/docs/Web/HTTP/Status/400) 반환합니다.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

특성 라우팅은 <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>에서 <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>와 같은 특성을 사용할 수 있습니다. 모든 [HTTP 동사](#verb) 특성은 경로 템플릿을 허용합니다. 다음 예제에서는 동일한 경로 템플릿과 일치하는 두 가지 작업을 보여 주십습니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

URL 경로 `/products3`사용:

* 이 `MyProductsController.ListProducts` 작업은 [HTTP 동사가](#verb) `GET`.일 때 실행됩니다.
* 이 `MyProductsController.CreateProduct` 작업은 [HTTP 동사가](#verb) `POST`.일 때 실행됩니다.

REST API를 빌드할 때 작업이 모든 HTTP 메서드를 허용하므로 작업 메서드에서 사용해야 `[Route(...)]` 하는 경우는 거의 없습니다. API가 지원하는 내용에 대해 정확하게 하려면 보다 구체적인 [HTTP 동사 특성을](#verb) 사용하는 것이 좋습니다. REST API의 클라이언트는 특정 논리 작업에 어떤 경로 및 HTTP 동사가 매핑되는지 알아야 합니다.

REST API는 특성 라우팅을 사용하여 HTTP 동사에 의해 작업이 표시되는 리소스 집합으로 앱의 기능을 모델링해야 합니다. 즉, 동일한 논리 리소스의 GET 및 POST와 같은 많은 작업이 동일한 URL을 사용합니다. 특성 라우팅은 API의 공용 엔드포인트 레이아웃을 신중하게 설계하는 데 필요한 제어 수준을 제공합니다.

특성 경로는 특정 작업에 적용되므로 경로 템플릿 정의의 일환으로 필요한 매개 변수를 간단하게 만들 수 있습니다. 다음 예제에서는 `id` URL 경로의 일부로 필요합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

작업: `Products2ApiController.GetProduct(int)`

* 다음과 같은 URL 경로로 실행됩니다.`/products2/3`
* URL 경로로 `/products2`실행되지 않습니다.

[[사용]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 특성을 사용하면 작업에서 지원되는 요청 콘텐츠 형식을 제한할 수 있습니다. 자세한 내용은 [사용 특성을 사용 하 여 지원 되는 요청 콘텐츠 형식 정의](xref:web-api/index#consumes)를 참조 하십시오.

 경로 템플릿 및 관련 옵션에 대한 전체 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.

자세한 `[ApiController]`내용은 [ApiController 특성을](xref:web-api/index##apicontroller-attribute)참조하십시오.

## <a name="route-name"></a>경로 이름

다음 코드는 의 `Products_List`경로 이름을 정의합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

경로 이름을 사용하여 특정 경로 기반의 URL을 생성할 수 있습니다. 경로 이름:

* 라우팅의 URL 일치 동작에 영향을 미치지 않습니다.
* URL 생성에만 사용됩니다.

경로 이름은 애플리케이션 전체에서 고유해야 합니다.

`id` 매개 변수를 선택적()로`{id?}`정의하는 기존 기본 경로와 앞의 코드를 대조합니다. API를 정확하게 지정하는 기능에는 `/products` 허용하고 `/products/5` 다른 작업에 전달되는 등의 장점이 있습니다.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>특성 경로 결합

특성 라우팅의 반복 횟수를 줄이기 위해 컨트롤러의 경로 특성은 개별 작업의 경로 특성과 결합됩니다. 컨트롤러에 정의된 경로 템플릿은 작업의 경로 템플릿에 앞에 추가됩니다. 경로 특성을 컨트롤러에 배치하면 컨트롤러의 **모든** 작업이 특성 라우팅을 사용합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

앞의 예제에서:

* URL 경로가 `/products` 일치할 수 있습니다.`ProductsApi.ListProducts`
* URL 경로가 `/products/5` `ProductsApi.GetProduct(int)`일치할 수 있습니다.

이 두 작업은 특성으로 표시되어 있으므로 `GET` `[HttpGet]` HTTP와만 일치합니다.

작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다. 다음 예제는 기본 경로와 유사한 URL 경로 집합과 일치합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

다음 표에서는 `[Route]` 앞의 코드의 특성을 설명합니다.

| attribute               | 결합`[Route("Home")]` | 경로 템플릿 정의 |
| ----------------- | ------------ | --------- |
| `[Route("")]` | 예 | `"Home"` |
| `[Route("Index")]` | 예 | `"Home/Index"` |
| `[Route("/")]` | **아니요** | `""` |
| `[Route("About")]` | 예 | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>특성 경로 순서

라우팅은 트리를 빌드하고 모든 끝점을 동시에 일치시다.

* 경로 항목은 이상적인 순서로 배치된 것처럼 행동합니다.
* 가장 구체적인 경로는 보다 일반적인 경로 보다 먼저 실행할 수 있습니다.

예를 들어 와 같은 `blog/search/{topic}` 특성 경로는 와 `blog/{*article}`같은 특성 경로보다 더 구체적입니다. 경로는 `blog/search/{topic}` 더 구체적이기 때문에 기본적으로 우선 순위가 높습니다. [기존 라우팅을](#cr)사용하여 개발자는 원하는 순서로 경로를 배치할 책임이 있습니다.

특성 경로는 속성을 사용하여 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> 순서를 구성할 수 있습니다. 제공된 모든 프레임워크는 경로 `Order` [특성을 포함합니다.](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 경로는 `Order` 속성의 오름차순 정렬에 따라 처리됩니다. 기본 순서는 `0`입니다. 순서를 설정하지 않은 경로 전에 실행을 사용하여 `Order = -1` 경로를 설정합니다. 기본 경로 `Order = 1` 순서 를 변경한 후 실행을 사용하여 경로를 설정합니다.

`Order`에 따라 **피하십시오.** 앱의 URL 공간에서 올바르게 라우팅하기 위해 명시적 주문 값이 필요한 경우 클라이언트에게도 혼동을 줄 수 있습니다. 일반적으로 특성 라우팅은 URL 일치를 가진 올바른 경로를 선택합니다. URL 생성에 사용되는 기본 순서가 작동하지 않는 경우 경로 이름을 재정의로 사용하는 것이 `Order` 일반적으로 속성을 적용하는 것보다 간단합니다.

둘 다 경로 일치를 `/home`정의하는 다음 두 컨트롤러를 고려하십시오.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

앞의 `/home` 코드를 요청하려면 다음과 유사한 예외가 발생합니다.

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

경로 `Order` 특성 중 하나에 추가하면 모호성이 해결됩니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

앞의 코드를 사용하면 `/home` 끝점을 실행합니다. `HomeController.Index` `MyDemoController.MyIndex`로 이동하려면 를 `/home/MyIndex`요청하십시오. **고**:

* 앞의 코드는 예제 또는 잘못된 라우팅 디자인입니다. `Order` 속성을 설명하는 데 사용되었습니다.
* 속성은 `Order` 모호성만 해결하며 해당 템플릿은 일치시킬 수 없습니다. 템플릿을 `[Route("Home")]` 제거하는 것이 좋습니다.

[Razor 페이지 경로 및 앱 규칙:](xref:razor-pages/razor-pages-conventions#route-order) Razor 페이지를 사용하면 경로 순서에 대한 정보를 확인할 수 있습니다.

경우에 따라 HTTP 500 오류가 모호한 경로와 함께 반환됩니다. [로깅을](xref:fundamentals/logging/index) 사용하여 `AmbiguousMatchException`의 원인을 확인할 수 있습니다.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>경로 템플릿의 토큰 교체 [컨트롤러], [작업], [영역]

편의를 위해 특성 경로는 다음 중 하나에 토큰을 둘러싸서 예약된 경로 매개 변수에 대한 토큰 교체를 지원합니다.

* 정사각형 중괄호:`[]`
* 중괄호:`{}`

`[action]`토큰은 `[controller]` 및 `[area]`경로가 정의된 작업에서 작업 이름, 영역 이름 및 컨트롤러 이름의 값으로 대체됩니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

위의 코드에서

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * 일치`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * 일치`/Products0/Edit/{id}`

토큰 교체는 특성 경로 빌드 과정의 마지막 단계로 발생합니다. 앞의 예제는 다음 코드와 동일하게 작업합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

특성 경로를 상속과 결합할 수도 있습니다. 이는 토큰 교체와 함께 강력하게 결합됩니다. 토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`각 작업에 대해 고유한 경로 이름을 생성합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
는 각 작업의 고유한 경로 이름을 생성합니다.

리터럴 토큰 교체 구분 기호 `[` 또는 `]`와 매칭하려면 문자(`[[` 또는 `]]`)를 반복하여 이스케이프합니다.

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>매개 변수 변환기를 사용하여 토큰 교체 사용자 지정

매개 변수 변환기를 사용하여 토큰 교체를 사용자 지정할 수 있습니다. 매개 변수 변환기는 <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer>를 구현하며 매개 변수의 값을 변환합니다. 예를 들어 사용자 `SlugifyParameterTransformer` 지정 매개 `SubscriptionManagement` 변수 변압기는 경로 값을 다음으로 `subscription-management`변경합니다.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention>은 다음과 같은 응용 프로그램 모델 규칙입니다.

* 애플리케이션의 모든 특성 경로에 매개 변수 변환기를 적용합니다.
* 대체되는 특성 경로 토큰 값을 사용자 지정합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

앞의 `ListAll` 메서드가 `/subscription-management/list-all`일치합니다.

`RouteTokenTransformerConvention`은 `ConfigureServices`에 옵션으로 등록됩니다.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

[슬러그의 정의는 슬러그에서 MDN 웹 문서를](https://developer.mozilla.org/docs/Glossary/Slug) 참조하십시오.

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>여러 특성 경로

특성 경로는 동일한 작업에 도달하는 여러 경로를 정의하는 것을 지원합니다. 가장 일반적인 사용 방법은 다음 예제와 같이 기본 규칙 기반 경로의 동작을 모방하는 것입니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

컨트롤러에 여러 경로 특성을 배치한다는 것은 각 경로 가 작업 메서드의 각 경로 특성과 결합된다는 것을 의미합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

모든 [HTTP 동사](#verb) 경로 `IActionConstraint`제약 조건은 을 구현합니다.

구현하는 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 여러 경로 특성이 작업에 배치되는 경우:

* 각 작업 제약 조건은 컨트롤러에 적용된 경로 템플릿과 결합됩니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

작업에 여러 경로를 사용하면 유용하고 강력해 보일 수 있으므로 앱의 URL 공간을 기본적이고 잘 정의된 상태로 유지하는 것이 좋습니다. 예를 들어 기존 클라이언트를 지원하기 위해 필요한 **경우에만** 작업에 여러 경로를 사용합니다.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>특성 경로 선택적 매개 변수, 기본값 및 제약 조건 지정

특성 경로는 선택적 매개 변수, 기본값 및 제약 조건을 지정할 수 있는 규칙 기반 경로와 동일한 인라인 구문을 지원합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

앞의 코드에서 `[HttpPost("product/{id:int}")]` 경로 제약 조건을 적용합니다. `ProductsController.ShowProduct` 작업은 와 같은 `/product/3`URL 경로에 의해서만 일치합니다. 배관 템플릿 `{id:int}` 부분은 해당 세그먼트를 정수로만 제한합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>IRouteTemplateProvider를 사용하여 사용자 지정 경로 특성

모든 [경로 특성구현](#rt) <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>. ASP.NET 코어 런타임:

* 앱이 시작될 때 컨트롤러 클래스 및 작업 메서드에서 특성을 찾습니다.
* 구현하는 `IRouteTemplateProvider` 특성을 사용하여 초기 경로 집합을 빌드합니다.

사용자 `IRouteTemplateProvider` 지정 경로 특성을 정의하기 위해 구현합니다. 각각의 `IRouteTemplateProvider`를 사용하여 사용자 지정 경로 템플릿, 순서 및 이름으로 단일 경로를 정의할 수 있습니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

앞의 `Get` 메서드는 `Order = 2, Template = api/MyTestApi`을 반환합니다.

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>응용 프로그램 모델을 사용하여 특성 경로를 사용자 지정

응용 프로그램 모델:

* 시작 시 작성된 개체 모델입니다.
* ASP.NET Core에서 앱에서 작업을 라우팅하고 실행하는 데 사용하는 모든 메타데이터를 포함합니다.

응용 프로그램 모델에는 경로 특성에서 수집된 모든 데이터가 포함됩니다. 경로 특성의 데이터는 구현에서 `IRouteTemplateProvider` 제공합니다. 규칙:

* 라우팅 동작 방식을 사용자 지정하기 위해 응용 프로그램 모델을 수정하도록 작성할 수 있습니다.
* 앱 시작 시 읽을 수 있습니다.

이 섹션에서는 응용 프로그램 모델을 사용하여 라우팅을 사용자 지정하는 기본 예제를 보여 주며, 이 섹션에서는 다음과 같은 기본 예제를 보여 주실 수 있습니다. 다음 코드는 라우트를 프로젝트의 폴더 구조와 대략일치하게 만듭니다.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

다음 코드는 라우트된 `namespace` 특성이 있는 컨트롤러에 규칙이 적용되지 않도록 합니다.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

예를 들어 다음 컨트롤러는 다음 `NamespaceRoutingConvention`컨트롤러를 사용하지 않습니다.

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

`NamespaceRoutingConvention.Apply` 메서드는 다음 작업을 수행합니다.

* 컨트롤러가 라우트된 특성인 경우 아무 것도 수행하지 않습니다.
* 을 기반으로 `namespace`컨트롤러 템플릿을 설정하고 `namespace` 베이스를 제거합니다.

적용 `NamespaceRoutingConvention` 할 수 `Startup.ConfigureServices`있습니다 :

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

예를 들어 다음 컨트롤러를 고려하십시오.

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

위의 코드에서

* 베이스는 `namespace` `My.Application`.
* 앞의 컨트롤러의 전체 이름은 `My.Application.Admin.Controllers.UsersController`.
* 컨트롤러 `NamespaceRoutingConvention` 템플릿을 `Admin/Controllers/Users/[action]/{id?`로 설정합니다.

컨트롤러에 `NamespaceRoutingConvention` 특성으로 적용할 수도 있습니다.

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>혼합 라우팅: 특성 라우팅 및 규칙 기반 라우팅

ASP.NET 핵심 앱은 기존 라우팅 및 특성 라우팅의 사용을 혼합할 수 있습니다. 일반적으로 브라우저의 HTML 페이지를 처리하는 컨트롤러에는 규칙 기반 경로를 사용하고 REST API를 제공하는 컨트롤러에는 특성 라우팅을 사용합니다.

작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다. 컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다. 특성 경로를 정의하는 동작은 규칙 기반 경로를 통해 도달할 수 없으며 그 반대도 마찬가지입니다. 컨트롤러의 **모든** 경로 특성은 라우팅된 컨트롤러 특성의 **모든** 작업을 만듭니다.

특성 라우팅 및 기존 라우팅은 동일한 라우팅 엔진을 사용합니다.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>URL 생성 및 주변 값

앱은 라우팅 URL 생성 기능을 사용하여 작업에 대한 URL 링크를 생성할 수 있습니다. URL을 생성하면 하드코딩 URL이 제거되므로 코드가 더 강력하고 유지 관리할 수 있습니다. 이 섹션에서는 MVC에서 제공하는 URL 생성 기능에 중점을 두고 URL 생성 작동 방식에 대한 기본 사항만 다룹니다. URL 생성에 대한 자세한 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.

인터페이스는 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> MVC와 URL 생성을 위한 라우팅 사이의 인프라의 기본 요소입니다. `IUrlHelper` 인스턴스는 제어기, `Url` 뷰 및 뷰 구성 요소에서 속성을 통해 사용할 수 있습니다.

다음 예제에서는 `IUrlHelper` 인터페이스를 `Controller.Url` 속성을 통해 다른 작업에 대한 URL을 생성하는 데 사용됩니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

앱이 기본 기존 경로를 사용하는 경우 `url` 변수의 값은 URL `/UrlGeneration/Destination`경로 문자열입니다. 이 URL 경로는 다음을 결합하여 라우팅하여 만들어집니다.

* 현재 요청의 경로 값을 **앰비언트 값이라고**합니다.
* 이러한 값을 `Url.Action` 경로 템플릿으로 전달하고 대체하는 값은 다음과 같은 것입니다.

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

경로 템플릿에 있는 각 경로 매개 변수의 값은 이름을 값 및 앰비언트 값과 매칭한 값으로 바뀝니다. 값이 없는 경로 매개 변수는 다음을 수행할 수 있습니다.

* 기본값이 있는 경우 기본값을 사용합니다.
* 선택 사항인 경우 건너뛸 수 있습니다. 예를 들어, `id` 경로 템플릿에서 `{controller}/{action}/{id?}`.

필요한 경로 매개 변수에 해당 값이 없는 경우 URL 생성이 실패합니다. 경로에 대한 URL 생성이 실패하면 모든 경로를 시도하거나 일치 항목을 찾을 때까지 그 다음 경로를 시도합니다.

위의 예는 `Url.Action` 기존 [라우팅을](#cr)가정합니다. URL 생성은 개념이 다르지만 [특성 라우팅과](#ar)유사하게 작동합니다. 기존 라우팅:

* 경로 값은 템플릿을 확장하는 데 사용됩니다.
* 경로 `controller` 값과 `action` 일반적으로 해당 템플릿에 나타납니다. 라우팅에 의해 일치하는 URL이 규칙을 준수하기 때문에 이 효과가 있습니다.

다음 예제에서는 특성 라우팅을 사용합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

이전 `Source` 코드의 작업이 생성됩니다. `custom/url/to/destination`

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>ASP.NET 코어 3.0에 대한 대안으로 `IUrlHelper`추가되었습니다. `LinkGenerator`유사하지만 보다 유연한 기능을 제공합니다. on의 `IUrlHelper` 각 메서드에는 해당 `LinkGenerator` 메서드 패밀리도 있습니다.

### <a name="generating-urls-by-action-name"></a>작업 이름으로 URL 생성

[Url.Action,](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)및 모든 관련 오버로드는 모두 컨트롤러 이름과 작업 이름을 지정하여 대상 끝점을 생성하도록 설계되었습니다.

을 `Url.Action`사용할 때 런타임에 대해 `controller` 현재 경로 값과 `action` 제공됩니다.

* 의 `controller` `action` 값과 [주변 값과](#ambient) 값의 일부입니다. 메서드는 `Url.Action` 항상 현재 `action` 값을 `controller` 사용 하 고 현재 작업에 라우팅 하는 URL 경로 생성 합니다.

라우팅은 주변 값의 값을 사용하여 URL을 생성할 때 제공되지 않은 정보를 채우려고 시도합니다. 주변 값과 `{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }`같은 경로를 고려하십시오.

* 라우팅에는 추가 값 없이 URL을 생성하기에 충분한 정보가 있습니다.
* 라우팅에는 모든 경로 매개 변수에 값이 있기 때문에 충분한 정보가 있습니다.

값이 `{ d = Donovan }` 추가되는 경우:

* 값은 `{ d = David }` 무시됩니다.
* 생성된 URL 경로는 . `Alice/Bob/Carol/Donovan`

**경고**: URL 경로는 계층적입니다. 앞의 예제에서 값이 `{ c = Cheryl }` 추가된 경우:

* 두 값 `{ c = Carol, d = David }` 모두 무시됩니다.
* 더 이상 값이 `d` 없으며 URL 생성이 실패합니다.
* URL을 생성하려면 원하는 `c` 값과 `d` 지정해야 합니다.  

기본 경로에서 `{controller}/{action}/{id?}`이 문제가 발생할 수 있습니다. 항상 `Url.Action` `controller` 명시적으로 a 및 `action` 값을 지정하기 때문에 이 문제는 실제로 는 드뭅니다.

[Url.Action의](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) 여러 오버로드는 경로 값 개체를 사용하여 및 `controller` `action`를 제외한 경로 매개 변수에 대한 값을 제공합니다. 경로 값 개체는 `id`에서 자주 사용됩니다. `Url.Action("Buy", "Products", new { id = 17 })`)을 입력합니다. 경로값 개체:

* 규칙에 의하면 일반적으로 익명 형식의 개체입니다.
* `IDictionary<>` 또는 [POCO)일](https://wikipedia.org/wiki/Plain_old_CLR_object)수 있다.

경로 매개 변수와 일치하지 않는 추가 경로 값들은 쿼리 문자열에 포함됩니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

앞의 코드는 `/Products/Buy/17?color=red`을 생성합니다.

다음 코드는 절대 URL을 생성합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

절대 URL을 만들려면 다음 중 하나를 사용합니다.

* 을 허용하는 오버로드입니다. `protocol` 예를 들어, 앞의 코드입니다.
* [링크 제너레이터.GetUriByAction,](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)기본적으로 절대 URI를 생성.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>경로별 URL 생성

앞의 코드는 컨트롤러 및 작업 이름을 전달하여 URL을 생성하는 것을 보여 주었으며, 이는 다음과 같은 설명입니다. `IUrlHelper`또한 [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) 메서드 제품군을 제공합니다. 이러한 메서드는 [Url.Action과](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)유사하지만 현재 `action` 값과 `controller` 경로 값을 복사하지 않습니다. 가장 일반적인 용도 `Url.RouteUrl`:

* URL을 생성할 경로 이름을 지정합니다.
* 일반적으로 컨트롤러 또는 작업 이름을 지정하지 않습니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

다음 Razor 파일은 다음에 대한 `Destination_Route`HTML 링크를 생성합니다.

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>HTML 및 면도기에서 URL 생성

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> `<form>` 에서는 각각 생성 및 `<a>` 요소를 생성하는 [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) 및 [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) 메서드를 제공합니다. 이러한 메서드는 [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) 메서드를 사용하여 URL을 생성하고 유사한 인수를 수락합니다. `HtmlHelper`에 대한 `Url.RouteUrl` 보조 도구는 `Html.BeginRouteForm` 및 `Html.RouteLink`이며 서로 기능이 비슷합니다.

TagHelper는 `form` TagHelper 및 `<a>` TagHelper를 통해 URL을 생성합니다. 둘 다 구현에 `IUrlHelper`를 사용합니다. 자세한 내용은 [양식의 도움말 태그를](xref:mvc/views/working-with-forms) 참조하십시오.

보기 내에서 `IUrlHelper`는 `Url` 속성을 통해서 위에서 다루지 않은 임시 URL을 생성하기 위해 사용할 수 있습니다.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>작업 결과의 URL 생성

위의 예제는 컨트롤러에서 `IUrlHelper` 사용 하 여 보여 주었다. 컨트롤러에서 가장 일반적으로 사용하는 방법은 작업 결과의 일부로 URL을 생성하는 것입니다.

<xref:Microsoft.AspNetCore.Mvc.ControllerBase> 및 <xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스는 다른 작업을 참조하는 작업 결과에 대한 편의 메서드를 제공합니다. 한 가지 일반적인 용도는 사용자 입력을 수락한 후 리디렉션하는 것입니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

이 작업은 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> `IUrlHelper`의 메서드와 유사한 패턴을 따르고 와 같은 팩터리 메서드를 생성합니다.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>전용 규칙 기반 경로의 특별한 사례

[종래의 라우팅은](#cr) [전용 종래의](#dcr)경로라고 하는 특별한 종류의 경로 정의를 사용할 수 있습니다. 다음 예제에서 명명된 `blog` 경로는 전용 기존 경로입니다.

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

앞의 경로 정의를 사용하여 `Url.Action("Index", "Home")` `default` 경로를 사용하여 `/` URL 경로를 생성하지만 왜 발생합니까? `{ controller = Home, action = Index }` 경로 값이면 `blog`를 사용하여 URL을 생성하기에 충분하며, 그 결과는 `/blog?action=Index&controller=Home`가 될 것이라고 추측할 수도 있습니다.

[전용 기존 경로는](#dcr) URL 생성시 경로가 너무 [욕심을](xref:fundamentals/routing#greedy) 내지 못하도록 하는 해당 경로 매개 변수가 없는 기본값의 특수 한 동작에 의존 합니다. 이 예제에서 기본값은 `{ controller = Blog, action = Article }`이고, `controller` 및 `action`이 경로 매개 변수로 표시되지 않습니다. 라우팅이 URL 생성을 수행할 때 입력한 값이 기본값과 일치해야 합니다. 값이 `blog` `{ controller = Home, action = Index }` 일치하지 `{ controller = Blog, action = Article }`않기 때문에 URL 생성이 실패합니다. 그 후 라우팅이 `default`로 대체되고, 이것은 성공합니다.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Areas

[영역은](xref:mvc/controllers/areas) 관련 기능을 그룹으로 구성하는 데 사용되는 MVC 기능입니다.

* 컨트롤러 작업에 대한 네임스페이스 라우팅
* 뷰에 대한 폴더 구조입니다.

영역을 사용하면 앱에 서로 다른 영역이 있는 한 이름이 같은 여러 컨트롤러를 가질 수 있습니다. 영역을 사용하면 또 다른 경로 매개 변수 `area`를 `controller` 및 `action`에 추가하여 라우팅을 위한 계층 구조를 생성합니다. 이 섹션에서는 라우팅이 영역과 상호 작용하는 방법에 대해 설명합니다. [뷰에서 영역을](xref:mvc/controllers/areas) 사용하는 방법에 대한 자세한 내용은 영역을 참조하십시오.

다음 예제는 MVC를 구성하여 기본 기존 `area` 경로와 `area` `Blog`명명된 경로에 대해 사용합니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

앞의 코드에서는 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> `"blog_route"`을 만들기 위해 호출됩니다. 두 번째 `"Blog"`매개 변수는 영역 이름입니다.

와 같은 `/Manage/Users/AddUser`URL 경로를 `"blog_route"` 일치하면 경로가 `{ area = Blog, controller = Users, action = AddUser }`경로 값을 생성합니다. 경로 `area` 값은 에 대한 `area`기본값으로 생성됩니다. 생성된 `MapAreaControllerRoute` 경로는 다음과 같습니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute`는 제공된 영역 이름(여기에서는 `Blog`)을 사용하는 `area`에 대해 기본값 및 제약 조건을 모두 사용하여 경로를 생성합니다. 기본값은 경로가 항상 `{ area = Blog, ... }`를 생성함을 보장하고, 제약 조건은 URL 생성을 위해 `{ area = Blog, ... }` 값을 필요로 합니다.

규칙 기반 라우팅은 순서에 영향을 받습니다. 일반적으로 영역이 있는 경로는 영역이 없는 경로보다 더 구체적이므로 더 일찍 배치해야 합니다.

앞의 예제를 사용하여 경로 `{ area = Blog, controller = Users, action = AddUser }` 값은 다음 작업과 일치합니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[[영역]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 특성은 영역의 일부로 컨트롤러를 나타내는 것입니다. 이 컨트롤러가 `Blog` 해당 영역에 있습니다. 특성이 `[Area]` 없는 컨트롤러는 모든 영역의 구성원이 아니며 라우팅을 통해 `area` 경로 값을 제공할 때 일치하지 **않습니다.** 다음 예제에서는 나열된 첫 번째 컨트롤러만 `{ area = Blog, controller = Users, action = AddUser }` 경로 값과 매칭됩니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

완전성을 위해 각 컨트롤러의 네임스페이스가 여기에 표시됩니다. 앞의 컨트롤러가 동일한 네임스페이스를 사용하는 경우 컴파일러 오류가 생성됩니다. 클래스 네임스페이스는 MVC의 라우팅에 영향을 주지 않습니다.

처음 두 컨트롤러는 영역의 구성원이며, `area` 경로 값으로 해당 영역 이름을 제공하는 경우에만 매칭됩니다. 세 번째 컨트롤러는 어떤 영역의 구성원도 아니며, 라우팅에서 `area`에 대한 값을 제공하지 않을 때에만 매칭됩니다.

<a name="aa"></a>

매칭 측면에서 *값 없음*, 즉 `area` 값이 없는 것은 `area`의 값이 null 또는 빈 문자열인 경우와 동일합니다.

영역 내에서 작업을 실행하는 경우 경로 `area` 값은 URL 생성에 사용할 라우팅에 대한 주변 [값으로](#ambient) 사용할 수 있습니다. 즉, 기본적으로 영역은 다음 예제에서 볼 수 있듯이 URL 생성을 위한 *스티커처럼* 동작합니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

다음 코드는 다음에 `/Zebra/Users/AddUser`대한 URL을 생성합니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>작업 정의

[비동작](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) 특성을 제외한 컨트롤러의 공용 메서드는 작업입니다.

## <a name="sample-code"></a>예제 코드

 * [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) 메서드는 샘플 [다운로드에](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) 포함 되며 라우팅 정보를 표시 하는 데 사용 됩니다.
* [샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) [방법)](xref:index#how-to-download-a-sample)

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

* `{controller=Home}`는 `Home`를 기본 `controller`으로 정의합니다.

* `{action=Index}`는 `Index`를 기본 `action`으로 정의합니다.

* `{id?}`는 `id`를 선택 사항으로 정의합니다.

기본 및 선택적 경로 매개 변수는 매칭을 위해 URL 경로에 반드시 있어야 하는 것은 아닙니다. 경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.

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

앞의 코드는 기존 라우팅의 예입니다. 이 스타일은 URL 경로에 대한 *규칙을* 설정하기 때문에 기존 라우팅이라고 합니다.

* 첫 번째 경로 세그먼트는 컨트롤러 이름에 매핑됩니다.
* 두 번째 는 작업 이름에 매핑됩니다.
* 세 번째 세그먼트는 선택적 `id`으로 사용됩니다. `id`모델 엔터티에 매핑됩니다.

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
> *전용 기존 경로는* 종종 URL 경로의 나머지 부분을 캡처하는 것과 같은 `{*article}` **포괄** 경로 매개 변수를 사용합니다. 이 경우 경로가 '너무 많은 욕심'을 부리게 됩니다. 즉, 다른 경로와 매칭하려는 URL과 매칭됩니다. 이 '욕심 많은' 경로를 경로 테이블의 뒷부분에 배치하면 이 문제를 해결할 수 있습니다.

### <a name="fallback"></a>대체

요청 처리의 일부로, MVC는 경로 값을 사용하여 애플리케이션의 컨트롤러 및 작업을 찾을 수 있는지 확인합니다. 경로 값이 작업과 일치하지 않으면 해당 경로는 불일치하는 것으로 간주되고, 그 다음 경로를 시도합니다. 이것을 *대체*라고 부르며, 규칙 기반 경로가 겹치는 상황을 단순화하는 것이 목적입니다.

### <a name="disambiguating-actions"></a>명확한 작업 구분

두 작업이 라우팅을 통해 일치하는 경우 MVC는 작업을 명확히 구분하여 '최적의' 후보를 선택해야 하며, 그렇지 못하면 예외가 throw됩니다. 다음은 그 예입니다.

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

`ProductsApi.GetProduct(int)` 작업은 `/products/3` 같은 URL 경로에 대해 실행되지만 `/products` 같은 URL 경로에 대해서는 실행되지 않습니다. 경로 템플릿 및 관련 옵션에 대한 전체 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.

## <a name="route-name"></a>경로 이름

다음 코드는 `Products_List`의 *경로 이름*을 정의합니다.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

경로 이름을 사용하여 특정 경로 기반의 URL을 생성할 수 있습니다. 경로 이름은 라우팅의 URL 매칭 동작에 영향을 미치지 않으며 URL 생성에만 사용됩니다. 경로 이름은 애플리케이션 전체에서 고유해야 합니다.

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

정의된 순서로 실행되는 기존 경로와 달리 특성 라우팅은 트리를 빌드하고 모든 경로를 동시에 일치시다. 이 동작은 경로 전체가 이상적인 순서로 정렬된 것처럼 수행됩니다. 가장 구체적인 경로가 일반적인 경로보다 먼저 실행될 가능성이 있습니다.

예를 들어 `blog/search/{topic}` 같은 경로는 `blog/{*article}` 같은 경로보다 구체적입니다. 논리적으로 말해서, 기본적으로 `blog/search/{topic}` 경로가 가장 먼저 '실행'됩니다. 유일하게 합리적인 순서이기 때문입니다. 규칙 기반 라우팅을 사용하면 개발자가 원하는 순서대로 경로를 배치해야 합니다.

특성 경로는 프레임워크가 제공하는 모든 경로 특성의 `Order` 속성을 사용하여 순서를 구성할 수 있습니다. 경로는 `Order` 속성의 오름차순 정렬에 따라 처리됩니다. 기본 순서는 `0`입니다. `Order = -1`을 사용한 경로 설정은 순서를 설정하지 않는 경로보다 먼저 실행됩니다. `Order = 1`을 사용한 경로 설정은 기본 경로 순서보다 늦게 실행됩니다.

> [!TIP]
> `Order`를 사용하지 마세요. URL 공간에 올바른 라우팅을 위한 명시적 순서 값이 필요한 경우 클라이언트에서도 혼란이 발생할 수 있습니다. 일반적으로 특성 라우팅은 URL이 일치하는 올바른 경로를 선택합니다. URL 생성에 사용되는 기본 순서가 작동하지 않는 경우 일반적으로 경로 이름을 재정의로 사용하는 것이 `Order` 속성을 적용하는 것보다 간단합니다.

Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다. Razor Pages 항목의 경로 순서에 대한 정보는 [Razor Pages 라우팅 및 앱 규칙: 경로 순서](xref:razor-pages/razor-pages-conventions#route-order)를 참조할 수 있습니다.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>경로 템플릿에서 토큰 바꾸기([컨트롤러] [작업] [지역])

편의를 위해 특성 경로는 토큰을 사각형 중괄호`[`(, `]`)로 둘러싸서 *토큰 교체를* 지원합니다. `[action]`, `[area]` 및 `[controller]` 토큰은 경로가 정의된 작업의 작업 이름, 영역 이름 및 컨트롤러 이름으로 바뀝니다. 다음 예의 작업은 주석에 설명된 대로 URL 경로를 매칭합니다.

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

토큰 교체는 특성 경로 빌드 과정의 마지막 단계로 발생합니다. 위의 예제는 다음 코드와 동일하게 동작합니다.

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

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

토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다. `[Route("[controller]/[action]", Name="[controller]_[action]")]`는 각 작업의 고유한 경로 이름을 생성합니다.

리터럴 토큰 교체 구분 기호 `[` 또는 `]`와 매칭하려면 문자(`[[` 또는 `]]`)를 반복하여 이스케이프합니다.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

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

`RouteTokenTransformerConvention`은 `ConfigureServices`에 옵션으로 등록됩니다.

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

경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.

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

*응용 프로그램 모델*은 MVC에 의해서 작업을 라우팅하고 실행하는 데 사용되는 모든 메타데이터를 이용하여 시작 시 만들어지는 개체 모델입니다. *응용 프로그램 모델*은 `IRouteTemplateProvider`를 통해서 경로 특성으로부터 수집한 모든 데이터를 포함합니다. 개발자는 시작 시에 응용 프로그램 모델을 수정하는 *규칙*을 작성하여 라우팅 동작을 사용자 지정할 수 있습니다. 이 섹션에서는 애플리케이션 모델을 사용하여 라우팅을 사용자 지정하는 간단한 예를 보여줍니다.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>혼합 라우팅: 특성 라우팅 및 규칙 기반 라우팅

MVC 애플리케이션은 규칙 기반 라우팅과 특성 라우팅을 혼합해서 사용할 수 있습니다. 일반적으로 브라우저의 HTML 페이지를 처리하는 컨트롤러에는 규칙 기반 경로를 사용하고 REST API를 제공하는 컨트롤러에는 특성 라우팅을 사용합니다.

작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다. 컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다. 특성 경로를 정의하는 동작은 규칙 기반 경로를 통해 도달할 수 없으며 그 반대도 마찬가지입니다. 컨트롤러의 **모든** 경로 특성은 컨트롤러에 있는 모든 작업에서 특성 라우팅을 사용하게 만듭니다.

> [!NOTE]
> 두 라우팅 시스템 간의 차이는 URL이 경로 템플릿과 일치한 후 적용되는 프로세스입니다. 규칙 기반 라우팅에서는 일치 항목의 경로 값을 사용하여 모든 규칙 기반 라우팅된 작업의 조회 테이블에서 작업 및 컨트롤러를 선택합니다. 특성 라우팅에서 각 템플릿은 이미 작업과 연결되어 있으며, 더 이상의 조회가 필요 없습니다.

## <a name="complex-segments"></a>복잡한 세그먼트

복잡한 세그먼트(예: `[Route("/dog{token}cat")]`)는 non-greedy 방식으로 오른쪽에서 왼쪽으로 리터럴을 매칭하여 처리됩니다. 자세한 내용은 [소스 코드](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)를 참조하세요. 자세한 내용은 [이 문제](https://github.com/dotnet/AspNetCore.Docs/issues/8197)를 참조하세요.

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>URL 생성

MVC 애플리케이션은 라우팅의 URL 생성 기능을 사용하여 작업에 대한 URL 링크를 생성할 수 있습니다. URL을 생성하면 하드 코딩된 URL이 제거되어 코드를 더욱 견고하게 유지할 수 있습니다. 이 섹션에서는 MVC에서 제공하는 URL 생성 기능을 중심으로 기본적인 URL 생성 원리에 대해서만 다룰 것입니다. URL 생성에 대한 자세한 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.

`IUrlHelper` 인터페이스는 URL 생성을 위한 MVC와 라우팅 간의 기본 인프라입니다. 컨트롤러, 보기 및 보기 구성 요소의 `Url` 속성을 통해서 사용 가능한 `IUrlHelper`의 인스턴스를 찾을 수 있습니다.

이 예제에서 `IUrlHelper` 인터페이스는 `Controller.Url` 속성을 통해 다른 작업에 대한 URL을 생성하는 데 사용됩니다.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

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

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC는 모든 특성 라우팅 작업의 조회 테이블을 작성하고 `controller` 및 `action` 값을 매칭하여 URL 생성에 사용할 경로 템플릿을 선택합니다. 위의 예제에서는 `custom/url/to/destination`이 생성됩니다.

### <a name="generating-urls-by-action-name"></a>작업 이름으로 URL 생성

`Url.Action` (`IUrlHelper` . `Action`) 및 모든 관련 오버로드는 개발자가 컨트롤러 이름과 작업 이름을 지정하여 연결 대상을 지정하려 한다는 생각을 바탕으로 합니다.

> [!NOTE]
> `Url.Action`을 사용하면 `controller` 및 `action`의 현재 경로 값이 자동으로 지정됩니다. `controller` 및 `action`의 값은 모두 *앰비언트 값* **및** *값*의 일부입니다. `Url.Action` 메서드는 항상 `action` 및 `controller`의 현재 값을 사용하며 현재 작업에 라우팅하는 URL 경로를 생성합니다.

라우팅은 앰비언트 값의 값을 사용하여 개발자가 URL을 생성할 때 제공하지 않은 정보를 채웁니다. `{a}/{b}/{c}/{d}` 같은 경로와 `{ a = Alice, b = Bob, c = Carol, d = David }` 앰비언트 값을 사용하면 모든 경로 매개 변수가 값을 갖기 때문에 추가 값이 없어도 라우팅에서 URL을 생성하기에 충분한 정보가 제공됩니다. `{ d = Donovan }` 값을 추가하면 `{ d = David }` 값이 무시되고 `Alice/Bob/Carol/Donovan` URL 경로가 생성됩니다.

> [!WARNING]
> URL 경로는 계층적입니다. 위의 예에서 `{ c = Cheryl }` 값을 추가하면 두 `{ c = Carol, d = David }` 값이 모두 무시됩니다. 이 경우 `d`의 값이 없기 때문에 URL 생성이 실패합니다. 원하는 `c` 및 `d` 값을 지정해야 합니다.  기본 경로(`{controller}/{action}/{id?}`)를 사용해도 이 문제가 발생할 가능성은 있지만, `Url.Action`이 항상 `controller` 및 `action` 값을 명시적으로 지정하기 때문에 실제로 이 동작이 발생하는 경우는 극히 드뭅니다.

`Url.Action` 의 긴 오버로드에서는 `controller` 및 `action` 이외의 경로 매개 변수의 값을 제공하기 위한 추가 *경로 값* 개체가 사용됩니다. `Url.Action("Buy", "Products", new { id = 17 })`처럼 `id`와 함께 사용되는 것을 흔히 볼 수 있습니다. 관례상 *경로 값* 개체는 대부분 무명 형식의 개체지만, `IDictionary<>` 또는 *오래된 일반 .NET 개체*일 수도 있습니다. 경로 매개 변수와 일치하지 않는 추가 경로 값들은 쿼리 문자열에 포함됩니다.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> 절대 URL을 만들려면 `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`을 받는 오버로드를 사용합니다.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>경로로 URL 생성

위의 코드에서는 컨트롤러 및 작업 이름을 전달하여 URL을 생성하는 방법을 보여줍니다. `IUrlHelper`는 `Url.RouteUrl` 메서드 모음도 제공합니다. 이 메서드는 `Url.Action`과 비슷하지만, `action` 및 `controller`의 현재 값을 경로 값에 복사하지 않습니다. 가장 일반적인 사용법은 URL을 생성할 경로를 지정하기 위한 경로 이름을 지정하는 것으로, 일반적으로 컨트롤러 또는 작업 이름을 지정하지 *않습니다*.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

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

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

`/Manage/Users/AddUser` 같은 URL 경로를 매칭할 때, 첫 번째 경로는 `{ area = Blog, controller = Users, action = AddUser }` 경로 값을 생성합니다. `area` 경로 값은 `area`의 기본값을 사용하여 생성되며, 사실 `MapAreaRoute`를 통해 생성되는 경로는 다음과 같습니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`는 제공된 영역 이름(여기에서는 `Blog`)을 사용하는 `area`에 대해 기본값 및 제약 조건을 모두 사용하여 경로를 생성합니다. 기본값은 경로가 항상 `{ area = Blog, ... }`를 생성함을 보장하고, 제약 조건은 URL 생성을 위해 `{ area = Blog, ... }` 값을 필요로 합니다.

> [!TIP]
> 규칙 기반 라우팅은 순서에 영향을 받습니다. 일반적으로 영역이 있는 경로는 영역이 없는 경로에 비해 구체적이기 때문에 경로 테이블의 앞부분에 배치되어야 합니다.

위의 예제를 사용하면 경로 값이 다음 작업과 매칭됩니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute`는 컨트롤러가 영역의 일부임을 나타내며, 이때 이 컨트롤러가 `Blog` 영역에 있다고 표현합니다. `[Area]` 특성이 없는 컨트롤러는 어떠한 영역의 구성원도 아니며, 라우팅에서 `area` 경로 값을 제공해도 매칭되지 **않습니다**. 다음 예제에서는 나열된 첫 번째 컨트롤러만 `{ area = Blog, controller = Users, action = AddUser }` 경로 값과 매칭됩니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> 각 컨트롤러의 네임스페이스는 완전성을 위해서 사용되었습니다. 네임스페이스가 없으면 컨트롤러에서 이름 충돌이 발생하고 컴파일러 오류가 생성됩니다. 클래스 네임스페이스는 MVC의 라우팅에 영향을 주지 않습니다.

처음 두 컨트롤러는 영역의 구성원이며, `area` 경로 값으로 해당 영역 이름을 제공하는 경우에만 매칭됩니다. 세 번째 컨트롤러는 어떤 영역의 구성원도 아니며, 라우팅에서 `area`에 대한 값을 제공하지 않을 때에만 매칭됩니다.

> [!NOTE]
> 매칭 측면에서 *값 없음*, 즉 `area` 값이 없는 것은 `area`의 값이 null 또는 빈 문자열인 경우와 동일합니다.

영역 내에서 작업을 실행하면 `area`의 경로 값이 라우팅에서 URL을 생성하기 위해 사용되는 *앰비언트 값*으로 제공됩니다. 즉, 기본적으로 영역은 다음 예제에서 볼 수 있듯이 URL 생성을 위한 *스티커처럼* 동작합니다.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

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

다음 예제에서 제약 조건은 경로 데이터에서 *국가 코드를* 기반으로 작업을 선택합니다. [GitHub의 전체 예제](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

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

::: moniker-end
