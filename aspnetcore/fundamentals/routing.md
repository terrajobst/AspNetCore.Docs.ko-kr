---
title: ASP.NET Core에서 라우팅
author: rick-anderson
description: ASP.NET Core 라우팅에서 요청 URI를 엔드포인트 선택기에 매핑하고, 들어오는 요청을 엔드포인트로 디스패치하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 06/17/2019
uid: fundamentals/routing
ms.openlocfilehash: 71cb7215651a263e588531c9be644326c0b6eda6
ms.sourcegitcommit: 28a2874765cefe9eaa068dceb989a978ba2096aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67167090"
---
# <a name="routing-in-aspnet-core"></a><span data-ttu-id="c423e-103">ASP.NET Core에서 라우팅</span><span class="sxs-lookup"><span data-stu-id="c423e-103">Routing in ASP.NET Core</span></span>

<span data-ttu-id="c423e-104">작성자: [Ryan Nowak](https://github.com/rynowak), [Steve Smith](https://ardalis.com/) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c423e-104">By [Ryan Nowak](https://github.com/rynowak), [Steve Smith](https://ardalis.com/), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="c423e-105">이 항목의 1.1 버전에 대해서는 [ASP.NET Core에서 라우팅(버전 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Routing_1.x.pdf)을 다운로드하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-105">For the 1.1 version of this topic, download [Routing in ASP.NET Core (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Routing_1.x.pdf).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c423e-106">라우팅은 요청 URI를 엔드포인트 선택기에 매핑하고 들어오는 요청을 엔드포인트로 디스패치합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-106">Routing is responsible for mapping request URIs to endpoint selectors and dispatching incoming requests to endpoints.</span></span> <span data-ttu-id="c423e-107">경로는 앱에서 정의되고 앱 시작 시 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-107">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="c423e-108">경로는 요청에 포함된 URL에서 필요에 따라 값을 추출할 수 있으며 이러한 값은 요청 처리를 위해 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-108">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="c423e-109">또한 라우팅은 앱의 경로 정보를 사용하여 엔드포인트 선택기에 매핑되는 URL을 생성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-109">Using route information from the app, routing is also able to generate URLs that map to endpoint selectors.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c423e-110">ASP.NET Core 2.2에서 최신 라우팅 시나리오를 사용하려면 `Startup.ConfigureServices`의 MVC 서비스 등록에 [호환성 버전](xref:mvc/compatibility-version)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-110">To use the latest routing scenarios in ASP.NET Core 2.2, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="c423e-111"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> 옵션은 라우팅에서 내부적으로 엔드포인트 기반 논리를 사용해야 하는지, 또는 ASP.NET Core 2.1 이하의 <xref:Microsoft.AspNetCore.Routing.IRouter> 기반 논리를 사용해야 하는지 여부를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-111">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> option determines if routing should internally use endpoint-based logic or the <xref:Microsoft.AspNetCore.Routing.IRouter>-based logic of ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="c423e-112">호환성 버전이 2.2 이상으로 설정된 경우 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-112">When the compatibility version is set to 2.2 or later, the default value is `true`.</span></span> <span data-ttu-id="c423e-113">이전 라우팅 논리를 사용하려면 값을 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-113">Set the value to `false` to use the prior routing logic:</span></span>

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="c423e-114"><xref:Microsoft.AspNetCore.Routing.IRouter> 기반 라우팅에 대한 자세한 내용은 [이 항목의 ASP.NET Core 2.1 버전](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-114">For more information on <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, see the [ASP.NET Core 2.1 version of this topic](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c423e-115">라우팅은 요청 URI를 경로 처리기에 매핑하고 들어오는 요청을 디스패치합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-115">Routing is responsible for mapping request URIs to route handlers and dispatching an incoming requests.</span></span> <span data-ttu-id="c423e-116">경로는 앱에서 정의되고 앱 시작 시 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-116">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="c423e-117">경로는 요청에 포함된 URL에서 필요에 따라 값을 추출할 수 있으며 이러한 값은 요청 처리를 위해 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-117">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="c423e-118">라우팅은 앱에 구성된 경로를 사용하여 경로 처리기에 매핑되는 URL을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-118">Using configured routes from the app, routing is able to generate URLs that map to route handlers.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="c423e-119">ASP.NET Core 2.1에서 최신 라우팅 시나리오를 사용하려면 `Startup.ConfigureServices`의 MVC 서비스 등록에 [호환성 버전](xref:mvc/compatibility-version)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-119">To use the latest routing scenarios in ASP.NET Core 2.1, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="c423e-120">이 문서에서는 낮은 수준의 ASP.NET Core 라우팅을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-120">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="c423e-121">ASP.NET Core MVC 라우팅에 대한 내용은 <xref:mvc/controllers/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-121">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="c423e-122">Razor Pages의 라우팅 규칙에 대한 내용은 <xref:razor-pages/razor-pages-conventions>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-122">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="c423e-123">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c423e-123">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="c423e-124">라우팅 기본 사항</span><span class="sxs-lookup"><span data-stu-id="c423e-124">Routing basics</span></span>

<span data-ttu-id="c423e-125">대부분의 앱은 URL을 읽을 수 있고 의미를 담고 있도록 기본적이고 서술적인 라우팅 체계를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-125">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="c423e-126">기본 기존 경로인 `{controller=Home}/{action=Index}/{id?}`는:</span><span class="sxs-lookup"><span data-stu-id="c423e-126">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="c423e-127">기본적이고 서술적인 라우팅 체계를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-127">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="c423e-128">UI 기반 앱에 대한 유용한 시작점입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-128">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="c423e-129">일반적으로 개발자는 [특성 라우팅](xref:mvc/controllers/routing#attribute-routing) 또는 전용 기존 경로를 사용하여 특수한 상황에서 앱의 트래픽이 높은 영역(예: 블로그 및 전자 상거래 엔드포인트)에 간결한 추가 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-129">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations (for example, blog and ecommerce endpoints) using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span>

<span data-ttu-id="c423e-130">Web API는 특성 라우팅을 사용하여 작업이 HTTP 동사로 표현되는 리소스 집합으로 앱의 기능을 모델링해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-130">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="c423e-131">이는 동일한 논리 리소스의 많은 작업(예: GET, POST)이 동일한 URL을 사용한다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-131">This means that many operations (for example, GET, POST) on the same logical resource will use the same URL.</span></span> <span data-ttu-id="c423e-132">특성 라우팅은 API의 공개 엔드포인트 레이아웃을 신중하게 설계하는 데 필요한 제어 수준을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-132">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="c423e-133">Razor Pages 앱은 기본 기존 라우팅을 사용하여 앱의 *Pages* 폴더에서 명명된 리소스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-133">Razor Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="c423e-134">Razor Pages 라우팅 동작을 사용자 지정할 수 있는 추가 규칙을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-134">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="c423e-135">자세한 내용은 <xref:razor-pages/index> 및 <xref:razor-pages/razor-pages-conventions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-135">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="c423e-136">URL 생성 지원을 사용하면 URL을 하드 코딩하지 않고 앱을 개발하여 앱을 서로 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-136">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="c423e-137">이 지원을 사용하면 기본 라우팅 구성으로 시작하고 앱의 리소스 레이아웃이 결정된 후에 해당 경로를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-137">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c423e-138">라우팅은 *엔드포인트*(`Endpoint`)를 사용하여 앱에서 논리적 엔드포인트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-138">Routing uses *endpoints* (`Endpoint`) to represent logical endpoints in an app.</span></span>

<span data-ttu-id="c423e-139">엔드포인트는 요청을 처리할 대리자와 임의의 메타데이터 컬렉션을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-139">An endpoint defines a delegate to process requests and a collection of arbitrary metadata.</span></span> <span data-ttu-id="c423e-140">메타데이터는 각 엔드포인트에 연결된 정책과 구성에 따라 횡단 관심사(Cross-Cutting Concerns)를 구현하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-140">The metadata is used implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="c423e-141">라우팅 시스템에는 다음과 같은 특징이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-141">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="c423e-142">경로 템플릿 구문은 토큰화된 경로 매개 변수를 사용하여 경로를 정의하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-142">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="c423e-143">기본 방식 스타일 및 특성 스타일 엔드포인트 구성이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-143">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="c423e-144"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint>는 URL 매개 변수가 지정한 엔드포인트 제약 조건에 대해 유효한 값을 포함하고 있는지 알아내는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-144"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="c423e-145">MVC/Razor Pages와 같은 앱 모델은 라우팅 시나리오의 예측 가능한 구현을 가진 모든 엔드포인트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-145">App models, such as MVC/Razor Pages, register all of their endpoints, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="c423e-146">라우팅 구현은 미들웨어 파이프라인에서 필요한 곳이라면 어디서든지 라우팅 결정을 내립니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-146">The routing implementation makes routing decisions wherever desired in the middleware pipeline.</span></span>
* <span data-ttu-id="c423e-147">라우팅 미들웨어 이후에 나타나는 미들웨어는 지정된 요청 URI에 대한 라우팅 미들웨어의 엔드포인트 결정 결과를 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-147">Middleware that appears after a Routing Middleware can inspect the result of the Routing Middleware's endpoint decision for a given request URI.</span></span>
* <span data-ttu-id="c423e-148">미들웨어 파이프라인의 어느 곳에서든 앱의 모든 엔드포인트를 열거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-148">It's possible to enumerate all of the endpoints in the app anywhere in the middleware pipeline.</span></span>
* <span data-ttu-id="c423e-149">앱은 라우팅을 사용하여 엔드포인트 정보에 따라 URL을 생성(예: 리디렉션 또는 링크)하므로 하드 코딩된 URL을 방지하여 유지 관리에 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-149">An app can use routing to generate URLs (for example, for redirection or links) based on endpoint information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="c423e-150">URL 생성은 임의의 확장성을 지원하는 주소를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-150">URL generation is based on addresses, which support arbitrary extensibility:</span></span>

  * <span data-ttu-id="c423e-151">링크 생성기 API(<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 URL을 생성하기 위해 어디서나 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-151">The Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) can be resolved anywhere using [dependency injection (DI)](xref:fundamentals/dependency-injection) to generate URLs.</span></span>
  * <span data-ttu-id="c423e-152">DI를 통해 링크 생성기 API를 사용할 수 없는 경우 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>에서 URL을 작성하는 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-152">Where the Link Generator API isn't available via DI, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>

> [!NOTE]
> <span data-ttu-id="c423e-153">ASP.NET Core 2.2의 엔드포인트 라우팅이 릴리스되면서 엔드포인트 연결이 MVC/Razor Pages 작업 및 페이지로 제한되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-153">With the release of endpoint routing in ASP.NET Core 2.2, endpoint linking is limited to MVC/Razor Pages actions and pages.</span></span> <span data-ttu-id="c423e-154">이후 릴리스에서는 엔드포인트 연결 기능이 확장될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-154">The expansions of endpoint-linking capabilities is planned for future releases.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c423e-155">라우팅은 *경로*(<xref:Microsoft.AspNetCore.Routing.IRouter>의 구현)를 사용하여 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-155">Routing uses *routes* (implementations of <xref:Microsoft.AspNetCore.Routing.IRouter>) to:</span></span>

* <span data-ttu-id="c423e-156">들어오는 요청을 *경로 처리기*에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-156">Map incoming requests to *route handlers*.</span></span>
* <span data-ttu-id="c423e-157">응답에 사용되는 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-157">Generate the URLs used in responses.</span></span>

<span data-ttu-id="c423e-158">기본적으로 앱에는 단일 경로 컬렉션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-158">By default, an app has a single collection of routes.</span></span> <span data-ttu-id="c423e-159">요청이 도착하면 컬렉션의 경로가 컬렉션에 존재하는 순서대로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-159">When a request arrives, the routes in the collection are processed in the order that they exist in the collection.</span></span> <span data-ttu-id="c423e-160">프레임워크는 컬렉션의 각 경로에서 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 메서드를 호출하여 들어오는 요청 URL을 컬렉션의 경로와 일치시키려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-160">The framework attempts to match an incoming request URL to a route in the collection by calling the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in the collection.</span></span> <span data-ttu-id="c423e-161">응답은 라우팅을 사용하여 경로 정보에 따라 URL을 생성(예: 리디렉션 또는 링크)하므로 하드 코딩된 URL을 방지하여 유지 관리에 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-161">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>

<span data-ttu-id="c423e-162">라우팅 시스템에는 다음과 같은 특징이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-162">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="c423e-163">경로 템플릿 구문은 토큰화된 경로 매개 변수를 사용하여 경로를 정의하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-163">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="c423e-164">기본 방식 스타일 및 특성 스타일 엔드포인트 구성이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-164">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="c423e-165"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint>는 URL 매개 변수가 지정한 엔드포인트 제약 조건에 대해 유효한 값을 포함하고 있는지 알아내는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-165"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="c423e-166">MVC/Razor Pages와 같은 앱 모델은 라우팅 시나리오의 예측 가능한 구현을 가진 모든 경로를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-166">App models, such as MVC/Razor Pages, register all of their routes, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="c423e-167">응답은 라우팅을 사용하여 경로 정보에 따라 URL을 생성(예: 리디렉션 또는 링크)하므로 하드 코딩된 URL을 방지하여 유지 관리에 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-167">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="c423e-168">URL 생성은 임의의 확장성을 지원하는 경로를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-168">URL generation is based on routes, which support arbitrary extensibility.</span></span> <span data-ttu-id="c423e-169"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>는 URL을 작성하는 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-169"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>

::: moniker-end

<span data-ttu-id="c423e-170">라우팅은 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 클래스에 의해 [미들웨어](xref:fundamentals/middleware/index) 파이프라인에 연결되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-170">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="c423e-171">[ASP.NET Core MVC](xref:mvc/overview)는 라우팅을 해당 구성의 일부로 미들웨어 파이프라인에 추가하고, MVC 및 Razor Pages 앱에서 라우팅을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-171">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="c423e-172">라우팅을 독립 실행형 구성 요소로 사용하는 방법을 알아보려면 [라우팅 미들웨어 사용](#use-routing-middleware) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-172">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="c423e-173">URL 일치</span><span class="sxs-lookup"><span data-stu-id="c423e-173">URL matching</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c423e-174">URL 일치는 라우팅이 들어오는 요청을 *엔드포인트*로 디스패치하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-174">URL matching is the process by which routing dispatches an incoming request to an *endpoint*.</span></span> <span data-ttu-id="c423e-175">이 프로세스는 URL 경로의 데이터를 기반으로 하지만 요청에 있는 모든 데이터를 고려하도록 확장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-175">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="c423e-176">요청을 별도의 처리기로 디스패치하는 기능은 앱의 크기와 복잡성을 확장하는 핵심입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-176">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c423e-177">라우팅 미들웨어가 실행되면 엔드포인트(`Endpoint`) 및 경로 값을 <xref:Microsoft.AspNetCore.Http.HttpContext>의 기능으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-177">When a Routing Middleware executes, it sets an endpoint (`Endpoint`) and route values to a feature on the <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="c423e-178">현재 요청에 대해 다음과 같이 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-178">For the current request:</span></span>

* <span data-ttu-id="c423e-179">`HttpContext.GetEndpoint`를 호출하면 엔드포인트를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-179">Calling `HttpContext.GetEndpoint` gets the endpoint.</span></span>
* <span data-ttu-id="c423e-180">`HttpRequest.RouteValues`는 경로 값의 컬렉션을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-180">`HttpRequest.RouteValues` gets the collection of route values.</span></span>

<span data-ttu-id="c423e-181">라우팅 미들웨어 이후에 실행되는 미들웨어는 엔드포인트를 보고, 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-181">Middleware running after the Routing Middleware can see the endpoint and take action.</span></span> <span data-ttu-id="c423e-182">예를 들어 권한 부여 미들웨어는 엔드포인트의 메타데이터 컬렉션에서 권한 부여 정책에 대한 정보를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-182">For example, an Authorization Middleware can interrogate the endpoint's metadata collection for an authorization policy.</span></span> <span data-ttu-id="c423e-183">요청 처리 파이프라인의 모든 미들웨어가 실행된 후에 선택한 엔드포인트의 대리자가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-183">After all of the middleware in the request processing pipeline is executed, the selected endpoint's delegate is invoked.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c423e-184">엔드포인트 라우팅의 라우팅 시스템은 모든 디스패치를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-184">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="c423e-185">미들웨어가 선택된 엔드포인트를 기반으로 정책을 적용하므로 디스패치 또는 응용 프로그램의 보안 정책에 영향을 미칠 수 있는 모든 결정은 라우팅 시스템 내에서 이루어져야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-185">Since the middleware applies policies based on the selected endpoint, it's important that any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

<span data-ttu-id="c423e-186">엔드포인트 대리자가 실행되면 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData)의 속성이 지금까지 수행된 요청 처리에 따라 적절한 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-186">When the endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c423e-187">URL 일치는 라우팅이 들어오는 요청을 *처리기*로 디스패치하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-187">URL matching is the process by which routing dispatches an incoming request to a *handler*.</span></span> <span data-ttu-id="c423e-188">이 프로세스는 URL 경로의 데이터를 기반으로 하지만 요청에 있는 모든 데이터를 고려하도록 확장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-188">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="c423e-189">요청을 별도의 처리기로 디스패치하는 기능은 앱의 크기와 복잡성을 확장하는 핵심입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-189">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="c423e-190">들어오는 요청은 시퀀스의 각 경로에서 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 메서드를 호출하는 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-190">Incoming requests enter the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, which calls the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in sequence.</span></span> <span data-ttu-id="c423e-191"><xref:Microsoft.AspNetCore.Routing.IRouter> 인스턴스는 [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*)를 null이 아닌 <xref:Microsoft.AspNetCore.Http.RequestDelegate>로 설정하여 요청을 *처리*할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-191">The <xref:Microsoft.AspNetCore.Routing.IRouter> instance chooses whether to *handle* the request by setting the [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) to a non-null <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="c423e-192">경로가 요청에 대한 처리기를 설정하는 경우 경로 처리가 중지되고 처리기가 요청을 처리하도록 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-192">If a route sets a handler for the request, route processing stops, and the handler is invoked to process the request.</span></span> <span data-ttu-id="c423e-193">요청을 처리하는 경로 처리기가 없는 경우 미들웨어는 요청을 요청 파이프라인의 다음 미들웨어에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-193">If no route handler is found to process the request, the middleware hands the request off to the next middleware in the request pipeline.</span></span>

<span data-ttu-id="c423e-194"><xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*>에 대한 기본 입력은 현재 요청과 연결된 [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*)입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-194">The primary input to <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is the [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) associated with the current request.</span></span> <span data-ttu-id="c423e-195">[RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) 및 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*)는 경로가 일치된 후에 설정된 출력입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-195">The [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) and [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) are outputs set after a route is matched.</span></span>

<span data-ttu-id="c423e-196">또한 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*>를 호출하는 일치는 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData)의 속성을 지금까지 수행된 요청 처리에 따라 적절한 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-196">A match that calls <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> also sets the properties of the [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) to appropriate values based on the request processing performed thus far.</span></span>

::: moniker-end

<span data-ttu-id="c423e-197">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*)는 경로에서 생성된 *경로 값*의 사전입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-197">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="c423e-198">이러한 값은 일반적으로 URL을 토큰화하여 결정되고, 사용자 입력을 수락하거나 앱 내부의 추가 디스패치 결정을 내리는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-198">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="c423e-199">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)는 일치하는 경로와 관련된 추가 데이터의 속성 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-199">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="c423e-200"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>는 앱에서 일치된 경로에 따라 결정할 수 있도록 각 경로와 상태 데이터의 연결을 지원하기 위해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-200"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="c423e-201">이러한 값은 개발자 정의되고 어떤 방식으로든 라우팅의 동작에 영향을 주지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="c423e-201">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="c423e-202">또한 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)에 안전하게 배치되는(stashed) 값은 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values)와 달리 문자열 간에 변환될 수 있어야 하는 모든 형식일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-202">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="c423e-203">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers)는 성공적으로 요청 일치에 참여한 경로의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-203">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="c423e-204">경로는 서로 중첩될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-204">Routes can be nested inside of one another.</span></span> <span data-ttu-id="c423e-205"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 속성은 결과적으로 일치한 경로의 논리 트리를 통해 경로를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-205">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="c423e-206">일반적으로 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>의 첫 번째 항목은 경로 컬렉션이며 URL 생성을 위해 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-206">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="c423e-207"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers>의 마지막 항목은 일치한 경로 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-207">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a><span data-ttu-id="c423e-208">LinkGenerator를 사용한 URL 생성</span><span class="sxs-lookup"><span data-stu-id="c423e-208">URL generation with LinkGenerator</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c423e-209">URL 생성은 라우팅이 경로 값의 집합을 기반으로 하는 URL 경로를 만들 수 있는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-209">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="c423e-210">이렇게 하면 엔드포인트와 이에 액세스하는 URL 간에 논리적으로 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-210">This allows for a logical separation between your endpoints and the URLs that access them.</span></span>

<span data-ttu-id="c423e-211">엔드포인트 라우팅에는 링크 생성기 API(<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-211">Endpoint routing includes the Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>).</span></span> <span data-ttu-id="c423e-212"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>는 DI에서 검색할 수 있는 싱글톤 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-212"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is a singleton service that can be retrieved from DI.</span></span> <span data-ttu-id="c423e-213">API는 실행 중인 요청의 컨텍스트 외부에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-213">The API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="c423e-214">MVC의 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 및 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>를 사용하는 시나리오(예: [태그 도우미](xref:mvc/views/tag-helpers/intro), HTML 도우미 및 [작업 결과](xref:mvc/controllers/actions))는 링크 생성기를 사용하여 링크 생성 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-214">MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the link generator to provide link generating capabilities.</span></span>

<span data-ttu-id="c423e-215">링크 생성기는 *주소* 및 *주소 체계*의 개념으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-215">The link generator is backed by the concept of an *address* and *address schemes*.</span></span> <span data-ttu-id="c423e-216">주소 체계는 링크 생성을 위해 고려해야 할 엔드포인트를 결정하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-216">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="c423e-217">예를 들어 많은 사용자가 MVC/Razor Pages에서 친숙한 경로 이름 및 경로 값 시나리오는 주소 체계로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-217">For example, the route name and route values scenarios many users are familiar with from MVC/Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="c423e-218">링크 생성기는 다음 확장 메서드를 통해 MVC/Razor Pages 작업 및 페이지에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-218">The link generator can link to MVC/Razor Pages actions and pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="c423e-219">이러한 메서드의 오버로드에는 `HttpContext`를 포함한 인수가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-219">An overload of these methods accepts arguments that include the `HttpContext`.</span></span> <span data-ttu-id="c423e-220">이러한 메서드는 기능적으로 `Url.Action` 및 `Url.Page`와 동일하지만, 추가적인 유연성과 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-220">These methods are functionally equivalent to `Url.Action` and `Url.Page` but offer additional flexibility and options.</span></span>

<span data-ttu-id="c423e-221">`GetPath*` 메서드는 절대 경로가 포함된 URI를 생성한다는 점에서 `Url.Action` 및 `Url.Page`와 가장 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-221">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page` in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="c423e-222">`GetUri*` 메서드는 항상 체계와 호스트를 포함한 절대 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-222">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="c423e-223">`HttpContext`를 허용하는 메서드는 실행 중인 요청의 컨텍스트에서 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-223">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="c423e-224">재정의되지 않는 한 실행 중인 요청의 앰비언트 경로 값, URL 기본 경로, 체계 및 호스트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-224">The ambient route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="c423e-225"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>는 주소를 사용하여 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-225"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="c423e-226">URI 생성은 다음 두 단계로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-226">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="c423e-227">주소는 해당 주소와 일치하는 엔드포인트 목록에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-227">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="c423e-228">제공된 값과 일치하는 경로 패턴을 찾을 때까지 각 엔드포인트의 `RoutePattern`이 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-228">Each endpoint's `RoutePattern` is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="c423e-229">결과 출력은 링크 생성기에 제공된 다른 URI 부분과 결합되어 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-229">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="c423e-230"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>에서 제공하는 메서드는 모든 유형의 주소에 대해 표준 링크 생성 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-230">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="c423e-231">링크 생성기를 사용하는 가장 편리한 방법은 특정 주소 유형에 대한 작업을 수행하는 확장 메서드를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-231">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type.</span></span>

| <span data-ttu-id="c423e-232">확장 메서드</span><span class="sxs-lookup"><span data-stu-id="c423e-232">Extension Method</span></span>   | <span data-ttu-id="c423e-233">설명</span><span class="sxs-lookup"><span data-stu-id="c423e-233">Description</span></span>                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | <span data-ttu-id="c423e-234">제공된 값에 기반한 절대 경로가 있는 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-234">Generates a URI with an absolute path based on the provided values.</span></span> |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | <span data-ttu-id="c423e-235">제공된 값에 기반한 절대 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-235">Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="c423e-236"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> 메서드 호출에 대한 다음과 같은 의미에 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-236">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="c423e-237">`GetUri*` 확장 메서드는 들어오는 요청의 `Host` 헤더에 대한 유효성을 검사하지 않는 앱 구성에서 신중하게 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-237">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="c423e-238">들어오는 요청의 `Host` 헤더에 대한 유효성을 검사하지 않으면 신뢰할 수 없는 요청 입력을 보기/페이지에 있는 URI의 클라이언트에 다시 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-238">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view/page.</span></span> <span data-ttu-id="c423e-239">모든 프로덕션 앱에서 알려진 유효한 값에 대해 `Host` 헤더의 유효성을 검사하도록 자체의 서버를 구성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-239">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="c423e-240"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>는 미들웨어에서 `Map` 또는 `MapWhen`과 함께 신중하게 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-240">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="c423e-241">`Map*`는 실행 중인 요청의 기본 경로를 변경하여 링크 생성의 출력에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-241">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="c423e-242">기본 경로는 모든 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API를 사용하여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-242">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="c423e-243">링크 생성에 대한 `Map*`의 영향을 취소하려면 항상 빈 기본 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-243">Always specify an empty base path to undo `Map*`'s affect on link generation.</span></span>

## <a name="differences-from-earlier-versions-of-routing"></a><span data-ttu-id="c423e-244">이전 버전의 라우팅과의 차이점</span><span class="sxs-lookup"><span data-stu-id="c423e-244">Differences from earlier versions of routing</span></span>

<span data-ttu-id="c423e-245">ASP.NET Core 2.2 이상의 엔드포인트 라우팅과 ASP.NET Core 이전 버전의 라우팅 간에는 다음과 같은 몇 가지 차이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-245">A few differences exist between endpoint routing in ASP.NET Core 2.2 or later and earlier versions of routing in ASP.NET Core:</span></span>

* <span data-ttu-id="c423e-246">엔드포인트 라우팅 시스템은 <xref:Microsoft.AspNetCore.Routing.Route>에서 상속하는 것을 포함하여 <xref:Microsoft.AspNetCore.Routing.IRouter> 기반 확장성을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-246">The endpoint routing system doesn't support <xref:Microsoft.AspNetCore.Routing.IRouter>-based extensibility, including inheriting from <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>

* <span data-ttu-id="c423e-247">엔드포인트 라우팅은 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-247">Endpoint routing doesn't support [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim).</span></span> <span data-ttu-id="c423e-248">호환성 shim을 계속 사용하려면 2.1 [호환성 버전](xref:mvc/compatibility-version)(`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`)을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-248">Use the 2.1 [compatibility version](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) to continue using the compatibility shim.</span></span>

* <span data-ttu-id="c423e-249">엔드포인트 라우팅은 기존 경로를 사용할 때 생성된 URI의 대/소문자 표기에 대해 다른 동작을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-249">Endpoint Routing has different behavior for the casing of generated URIs when using conventional routes.</span></span>

  <span data-ttu-id="c423e-250">다음과 같은 기본 경로 템플릿을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-250">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="c423e-251">다음 경로를 사용하여 작업에 대한 링크를 생성한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-251">Suppose you generate a link to an action using the following route:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  <span data-ttu-id="c423e-252"><xref:Microsoft.AspNetCore.Routing.IRouter> 기반 라우팅을 사용하는 경우 이 코드는 제공된 경로 값의 대/소문자 표기를 고려한 `/blog/ReadPost/17`이라는 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-252">With <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, this code generates a URI of `/blog/ReadPost/17`, which respects the casing of the provided route value.</span></span> <span data-ttu-id="c423e-253">ASP.NET Core 2.2 이상의 엔드포인트 라우팅에서는 `/Blog/ReadPost/17`("Blog"의 첫 글자가 대문자로 지정됨)을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-253">Endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` ("Blog" is capitalized).</span></span> <span data-ttu-id="c423e-254">엔드포인트 라우팅은 이 동작을 글로벌로 사용자 지정하거나 URL 매핑에 대해 다른 규칙을 적용하는 데 사용할 수 있는 `IOutboundParameterTransformer` 인터페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-254">Endpoint routing provides the `IOutboundParameterTransformer` interface that can be used to customize this behavior globally or to apply different conventions for mapping URLs.</span></span>

  <span data-ttu-id="c423e-255">자세한 내용은 [매개 변수 변환기 참조](#parameter-transformer-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-255">For more information, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

* <span data-ttu-id="c423e-256">MVC/Razor Pages에서 기존 경로를 통해 사용하는 링크 생성은 존재하지 않는 컨트롤러/작업 또는 페이지에 연결하려고 할 때 다르게 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-256">Link Generation used by MVC/Razor Pages with conventional routes behaves differently when attempting to link to an controller/action or page that doesn't exist.</span></span>

  <span data-ttu-id="c423e-257">다음과 같은 기본 경로 템플릿을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-257">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="c423e-258">다음과 함께 기본 템플릿을 사용하여 작업에 대한 링크를 생성한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-258">Suppose you generate a link to an action using the default template with the following:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  <span data-ttu-id="c423e-259">`IRouter` 기반 라우팅을 사용하면 `BlogController`가 없거나 `ReadPost` 작업 메서드가 없는 경우에도 결과는 항상 `/Blog/ReadPost/17`입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-259">With `IRouter`-based routing, the result is always `/Blog/ReadPost/17`, even if the `BlogController` doesn't exist or doesn't have a `ReadPost` action method.</span></span> <span data-ttu-id="c423e-260">작업 메서드가 있는 경우 ASP.NET Core 2.2 이상의 엔드포인트 라우팅은 예상대로 `/Blog/ReadPost/17`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-260">As expected, endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` if the action method exists.</span></span> <span data-ttu-id="c423e-261">*그러나 작업이 없는 경우에는 엔드포인트 라우팅에서 빈 문자열을 생성합니다.*</span><span class="sxs-lookup"><span data-stu-id="c423e-261">*However, endpoint routing produces an empty string if the action doesn't exist.*</span></span> <span data-ttu-id="c423e-262">개념적으로 엔드포인트 라우팅에서는 작업이 없는 경우 엔드포인트가 있다고 가정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-262">Conceptually, endpoint routing doesn't assume that the endpoint exists if the action doesn't exist.</span></span>

* <span data-ttu-id="c423e-263">링크 생성 *앰비언트 값 무효화 알고리즘*은 엔드포인트 라우팅에서 사용할 때 다르게 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-263">The link generation *ambient value invalidation algorithm* behaves differently when used with endpoint routing.</span></span>

  <span data-ttu-id="c423e-264">*앰비언트 값 무효화*는 링크 생성 작업에서 사용할 수 있는 현재 실행 중인 요청의 경로 값(앰비언트 값)을 결정하는 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-264">*Ambient value invalidation* is the algorithm that decides which route values from the currently executing request (the ambient values) can be used in link generation operations.</span></span> <span data-ttu-id="c423e-265">기존 라우팅은 다른 작업에 연결할 때 항상 추가 경로 값을 무효화했습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-265">Conventional routing always invalidated extra route values when linking to a different action.</span></span> <span data-ttu-id="c423e-266">ASP.NET Core 2.2를 릴리스하기 전에는 특성 라우팅에 이 동작이 없었습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-266">Attribute routing didn't have this behavior prior to the release of ASP.NET Core 2.2.</span></span> <span data-ttu-id="c423e-267">이전 버전의 ASP.NET Core에서는 동일한 경로 매개 변수 이름을 사용하는 다른 작업에 연결하면 링크 생성 오류가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-267">In earlier versions of ASP.NET Core, links to another action that use the same route parameter names resulted in link generation errors.</span></span> <span data-ttu-id="c423e-268">ASP.NET Core 2.2 이상에서는 두 가지 형태의 라우팅 모두에서 다른 작업에 연결하면 값이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-268">In ASP.NET Core 2.2 or later, both forms of routing invalidate values when linking to another action.</span></span>

  <span data-ttu-id="c423e-269">ASP.NET Core 2.1 이하에서 다음 예제를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-269">Consider the following example in ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="c423e-270">다른 작업(또는 다른 페이지)에 연결할 때 경로 값은 바람직하지 않은 방법으로 다시 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-270">When linking to another action (or another page), route values can be reused in undesirable ways.</span></span>

  <span data-ttu-id="c423e-271">*/Pages/Store/Product.cshtml*에서,</span><span class="sxs-lookup"><span data-stu-id="c423e-271">In */Pages/Store/Product.cshtml*:</span></span>

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  <span data-ttu-id="c423e-272">*/Pages/Login.cshtml*에서,</span><span class="sxs-lookup"><span data-stu-id="c423e-272">In */Pages/Login.cshtml*:</span></span>

  ```cshtml
  @page "{id?}"
  ```

  <span data-ttu-id="c423e-273">ASP.NET Core 2.1 이하에서 URI가 `/Store/Product/18`인 경우 `@Url.Page("/Login")`에서 Store/Info 페이지에 생성된 링크는 `/Login/18`입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-273">If the URI is `/Store/Product/18` in ASP.NET Core 2.1 or earlier, the link generated on the Store/Info page by `@Url.Page("/Login")` is `/Login/18`.</span></span> <span data-ttu-id="c423e-274">링크 대상이 완전히 다른 앱 부분인 경우에도 18이라는 `id` 값이 다시 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-274">The `id` value of 18 is reused, even though the link destination is different part of the app entirely.</span></span> <span data-ttu-id="c423e-275">`/Login` 페이지의 컨텍스트에 있는 `id` 경로 값은 저장소 제품 ID 값이 아닌 사용자 ID 값일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-275">The `id` route value in the context of the `/Login` page is probably a user ID value, not a store product ID value.</span></span>

  <span data-ttu-id="c423e-276">ASP.NET Core 2.2 이상을 사용하는 엔드포인트 라우팅에서 결과는 `/Login`입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-276">In endpoint routing with ASP.NET Core 2.2 or later, the result is `/Login`.</span></span> <span data-ttu-id="c423e-277">연결된 대상이 다른 작업 또는 페이지인 경우 앰비언트 값은 다시 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-277">Ambient values aren't reused when the linked destination is a different action or page.</span></span>

* <span data-ttu-id="c423e-278">라운드트립 경로 매개 변수 구문: 이중 별표(`**`) 범용(catch-all) 매개 변수 구문을 사용하는 경우 슬래시는 인코딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-278">Round-tripping route parameter syntax: Forward slashes aren't encoded when using a double-asterisk (`**`) catch-all parameter syntax.</span></span>

  <span data-ttu-id="c423e-279">링크를 생성하는 동안 라우팅 시스템은 슬래시를 제외한 이중 별표(`**`) 범용 매개 변수(예: `{**myparametername}`)에서 캡처된 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-279">During link generation, the routing system encodes the value captured in a double-asterisk (`**`) catch-all parameter (for example, `{**myparametername}`) except the forward slashes.</span></span> <span data-ttu-id="c423e-280">이중 별표 범용 매개 변수는 ASP.NET Core 2.2 이상의 `IRouter` 기반 라우팅에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-280">The double-asterisk catch-all is supported with `IRouter`-based routing in ASP.NET Core 2.2 or later.</span></span>

  <span data-ttu-id="c423e-281">ASP.NET Core 이전 버전의 단일 별표 범용 매개 변수 구문(`{*myparametername}`)은 계속 지원되며, 슬래시가 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-281">The single asterisk catch-all parameter syntax in prior versions of ASP.NET Core (`{*myparametername}`) remains supported, and forward slashes are encoded.</span></span>

  | <span data-ttu-id="c423e-282">경로</span><span class="sxs-lookup"><span data-stu-id="c423e-282">Route</span></span>              | <span data-ttu-id="c423e-283">다음을 사용하여 생성되는 링크</span><span class="sxs-lookup"><span data-stu-id="c423e-283">Link generated with</span></span><br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | <span data-ttu-id="c423e-284">`/search/admin%2Fproducts`(슬래시가 인코딩됨)</span><span class="sxs-lookup"><span data-stu-id="c423e-284">`/search/admin%2Fproducts` (the forward slash is encoded)</span></span>             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a><span data-ttu-id="c423e-285">미들웨어 예제</span><span class="sxs-lookup"><span data-stu-id="c423e-285">Middleware example</span></span>

<span data-ttu-id="c423e-286">다음 예제에서는 미들웨어에서 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API를 사용하여 상점 제품을 나열하는 작업 메서드에 대한 링크를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-286">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create link to an action method that lists store products.</span></span> <span data-ttu-id="c423e-287">링크 생성기를 클래스에 주입하고 `GenerateLink`를 호출하여 앱의 모든 클래스에서 해당 링크 생성기를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-287">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app.</span></span>

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c423e-288">URL 생성은 라우팅이 경로 값의 집합을 기반으로 하는 URL 경로를 만들 수 있는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-288">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="c423e-289">이렇게 하면 경로 처리기와 이에 액세스하는 URL을 논리적으로 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-289">This allows for a logical separation between route handlers and the URLs that access them.</span></span>

<span data-ttu-id="c423e-290">URL 생성은 비슷한 반복적인 프로세스를 따르지만 경로 컬렉션의 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 메서드로 호출하는 사용자 또는 프레임워크 코드로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-290">URL generation follows a similar iterative process, but it starts with user or framework code calling into the <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method of the route collection.</span></span> <span data-ttu-id="c423e-291">각 *경로*에는 null이 아닌 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>가 반환될 때까지 시퀀스에서 호출되는 해당 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-291">Each *route* has its <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method called in sequence until a non-null <xref:Microsoft.AspNetCore.Routing.VirtualPathData> is returned.</span></span>

<span data-ttu-id="c423e-292"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>에 대한 기본 입력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-292">The primary inputs to <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> are:</span></span>

* [<span data-ttu-id="c423e-293">VirtualPathContext.HttpContext</span><span class="sxs-lookup"><span data-stu-id="c423e-293">VirtualPathContext.HttpContext</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [<span data-ttu-id="c423e-294">VirtualPathContext.Values</span><span class="sxs-lookup"><span data-stu-id="c423e-294">VirtualPathContext.Values</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [<span data-ttu-id="c423e-295">VirtualPathContext.AmbientValues</span><span class="sxs-lookup"><span data-stu-id="c423e-295">VirtualPathContext.AmbientValues</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

<span data-ttu-id="c423e-296">경로는 주로 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 및 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues>에서 제공하는 경로 값을 사용하여 URL을 생성할 수 있는지 여부와 포함할 값을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-296">Routes primarily use the route values provided by <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> and <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> to decide whether it's possible to generate a URL and what values to include.</span></span> <span data-ttu-id="c423e-297"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues>는 현재 요청 일치로부터 생성된 경로 값의 세트입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-297">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> are the set of route values that were produced from matching the current request.</span></span> <span data-ttu-id="c423e-298">반면, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values>는 현재 작업에 대한 원하는 URL을 생성하는 방법을 지정하는 경로 값입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-298">In contrast, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> are the route values that specify how to generate the desired URL for the current operation.</span></span> <span data-ttu-id="c423e-299"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext>는 경로가 서비스 또는 현재 컨텍스트와 연결된 추가 데이터를 가져와야 하는 경우에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-299">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> is provided in case a route should obtain services or additional data associated with the current context.</span></span>

> [!TIP]
> <span data-ttu-id="c423e-300">[VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*)를 [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*)에 대한 재정의 세트로 간주하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-300">Think of [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) as a set of overrides for the [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*).</span></span> <span data-ttu-id="c423e-301">URL 생성은 동일한 경로 또는 경로 값을 사용하는 링크에 대한 URL을 생성하기 위해 현재 요청의 경로 값을 다시 사용하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-301">URL generation attempts to reuse route values from the current request to generate URLs for links using the same route or route values.</span></span>

<span data-ttu-id="c423e-302"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>의 출력은 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-302">The output of <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is a <xref:Microsoft.AspNetCore.Routing.VirtualPathData>.</span></span> <span data-ttu-id="c423e-303"><xref:Microsoft.AspNetCore.Routing.VirtualPathData>는 <xref:Microsoft.AspNetCore.Routing.RouteData>의 병렬입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-303"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> is a parallel of <xref:Microsoft.AspNetCore.Routing.RouteData>.</span></span> <span data-ttu-id="c423e-304"><xref:Microsoft.AspNetCore.Routing.VirtualPathData>는 출력 URL 및 경로에 의해 설정되어야 하는 몇 가지 추가 속성에 대한 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-304"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> contains the <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> for the output URL and some additional properties that should be set by the route.</span></span>

<span data-ttu-id="c423e-305">[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) 속성은 경로에 의해 생성된 *가상 경로*를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-305">The [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) property contains the *virtual path* produced by the route.</span></span> <span data-ttu-id="c423e-306">필요에 따라 경로를 추가로 처리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-306">Depending on your needs, you may need to process the path further.</span></span> <span data-ttu-id="c423e-307">HTML에서 생성된 URL을 렌더링하려는 경우 앱의 기본 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-307">If you want to render the generated URL in HTML, prepend the base path of the app.</span></span>

<span data-ttu-id="c423e-308">[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*)는 성공적으로 URL을 생성한 경로에 대한 참조입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-308">The [VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) is a reference to the route that successfully generated the URL.</span></span>

<span data-ttu-id="c423e-309">[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) 속성은 URL을 생성한 경로와 관련된 추가 데이터의 사전입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-309">The [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) properties is a dictionary of additional data related to the route that generated the URL.</span></span> <span data-ttu-id="c423e-310">이는 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)의 병렬입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-310">This is the parallel of [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).</span></span>

::: moniker-end

### <a name="create-routes"></a><span data-ttu-id="c423e-311">경로 만들기</span><span class="sxs-lookup"><span data-stu-id="c423e-311">Create routes</span></span>

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c423e-312">라우팅은 <xref:Microsoft.AspNetCore.Routing.IRouter>의 표준 구현으로 <xref:Microsoft.AspNetCore.Routing.Route> 클래스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-312">Routing provides the <xref:Microsoft.AspNetCore.Routing.Route> class as the standard implementation of <xref:Microsoft.AspNetCore.Routing.IRouter>.</span></span> <span data-ttu-id="c423e-313"><xref:Microsoft.AspNetCore.Routing.Route>는 *경로 템플릿* 구문을 사용하여 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*>가 호출될 때 URL 경로와 일치하는 패턴을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-313"><xref:Microsoft.AspNetCore.Routing.Route> uses the *route template* syntax to define patterns to match against the URL path when <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is called.</span></span> <span data-ttu-id="c423e-314"><xref:Microsoft.AspNetCore.Routing.Route>는 동일한 경로 템플릿을 사용하여 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>가 호출되었을 때 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-314"><xref:Microsoft.AspNetCore.Routing.Route> uses the same route template to generate a URL when <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is called.</span></span>

::: moniker-end

<span data-ttu-id="c423e-315">대부분의 앱은 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 또는 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>에 정의된 유사한 확장 메서드 중 하나를 호출하여 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-315">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="c423e-316"><xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 확장 메서드 중 하나에서 <xref:Microsoft.AspNetCore.Routing.Route>의 인스턴스를 만들고, 경로 컬렉션에 이를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-316">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c423e-317"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>는 경로 처리기 매개 변수를 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-317"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="c423e-318"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>는 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>에 의해 처리되는 경로만 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-318"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="c423e-319">MVC의 라우팅에 대해 자세히 알아보려면 <xref:mvc/controllers/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-319">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c423e-320"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>는 경로 처리기 매개 변수를 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-320"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="c423e-321"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>는 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>에 의해 처리되는 경로만 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-321"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="c423e-322">기본 처리기는 `IRouter`이며, 처리기에서 요청을 처리하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-322">The default handler is an `IRouter`, and the handler might not handle the request.</span></span> <span data-ttu-id="c423e-323">예를 들어 ASP.NET Core MVC는 일반적으로 사용 가능한 컨트롤러 및 작업과 일치하는 요청만 처리하는 기본 처리기로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-323">For example, ASP.NET Core MVC is typically configured as a default handler that only handles requests that match an available controller and action.</span></span> <span data-ttu-id="c423e-324">MVC의 라우팅에 대해 자세히 알아보려면 <xref:mvc/controllers/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-324">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

::: moniker-end

<span data-ttu-id="c423e-325">다음 코드 예제는 일반적인 ASP.NET Core MVC 경로 정의에서 사용되는 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 호출의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-325">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="c423e-326">이 템플릿은 URL 경로와 일치시키고 경로 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-326">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="c423e-327">예를 들어 `/Products/Details/17` 경로는 `{ controller = Products, action = Details, id = 17 }` 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-327">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="c423e-328">경로 값은 URL 경로를 세그먼트로 분할하고 각 세그먼트를 경로 템플릿의 *경로 매개 변수* 이름과 일치시켜 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-328">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="c423e-329">경로 매개 변수의 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-329">Route parameters are named.</span></span> <span data-ttu-id="c423e-330">매개 변수는 매개 변수 이름을 `{ ... }` 중괄호로 묶어 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-330">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="c423e-331">또한 앞의 템플릿은 `/` URL 경로와 일치시키고 `{ controller = Home, action = Index }` 값을 생성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-331">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="c423e-332">이는 `{controller}` 및 `{action}` 경로 매개 변수에 기본값이 있으며 `id` 경로 매개 변수는 선택 사항이기 때문에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-332">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="c423e-333">경로 매개 변수 이름 뒤에 있는 값이 뒤따르는 등호(`=`)는 매개 변수에 대한 기본값을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-333">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="c423e-334">경로 매개 변수 이름 뒤에 있는 물음표(`?`)는 선택적 매개 변수를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-334">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="c423e-335">기본 값을 사용하는 경로 매개 변수는 경로가 일치하는 경우 *항상* 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-335">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="c423e-336">해당 URL 경로 세그먼트가 없는 경우 선택적 매개 변수는 경로 값을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-336">Optional parameters don't produce a route value if there was no corresponding URL path segment.</span></span> <span data-ttu-id="c423e-337">경로 템플릿 시나리오 및 구문에 대한 자세한 설명은 [경로 템플릿 참조](#route-template-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-337">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="c423e-338">다음 예제에서 `{id:int}` 경로 매개 변수 정의는 `id` 경로 매개 변수에 대한 [경로 제약 조건](#route-constraint-reference)을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-338">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="c423e-339">이 템플릿은 `/Products/Details/Apples`가 아닌 `/Products/Details/17`과 같이 URL 경로와 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-339">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="c423e-340">경로 제약 조건은 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>를 구현하고 경로 값을 검사하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-340">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="c423e-341">이 예제에서 경로 값 `id`는 정수로 변환할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-341">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="c423e-342">프레임워크에서 제공하는 경로 제약 조건에 대한 설명은 [경로 제약 조건 참조](#route-constraint-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-342">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="c423e-343"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>의 추가 오버로드는 `constraints`, `dataTokens` 및 `defaults`에 대한 값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-343">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="c423e-344">이러한 매개 변수의 일반적인 사용법은 익명 형식의 속성 이름이 경로 매개 변수 이름과 일치하는 익명으로 형식화된 개체를 전달하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-344">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="c423e-345">다음 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 예제에서는 동등한 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-345">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> <span data-ttu-id="c423e-346">제약 조건 및 기본값을 정의하기 위한 인라인 구문은 단순 경로에 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-346">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="c423e-347">그러나 인라인 구문에서 지원되지 않는 데이터 토큰과 같은 시나리오가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-347">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="c423e-348">다음 예제에서는 몇 가지 추가 시나리오를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-348">The following example demonstrates a few additional scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="c423e-349">앞의 템플릿은 `/Blog/All-About-Routing/Introduction`과 같은 URL 경로와 일치시키고 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-349">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="c423e-350">`controller` 및 `action`에 대 한 기본 경로 값은 템플릿에 해당 경로 매개 변수가 없는 경우에도 경로에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-350">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="c423e-351">기본값은 경로 템플릿에서 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-351">Default values can be specified in the route template.</span></span> <span data-ttu-id="c423e-352">`article` 경로 매개 변수는 경로 매개 변수 이름 앞에 이중 별표(`**`)를 표시하여 *범용*으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-352">The `article` route parameter is defined as a *catch-all* by the appearance of an double asterisk (`**`) before the route parameter name.</span></span> <span data-ttu-id="c423e-353">범용 경로 매개 변수는 URL 경로의 나머지를 캡처하고 빈 문자열과 일치시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-353">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="c423e-354">앞의 템플릿은 `/Blog/All-About-Routing/Introduction`과 같은 URL 경로와 일치시키고 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-354">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="c423e-355">`controller` 및 `action`에 대 한 기본 경로 값은 템플릿에 해당 경로 매개 변수가 없는 경우에도 경로에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-355">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="c423e-356">기본값은 경로 템플릿에서 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-356">Default values can be specified in the route template.</span></span> <span data-ttu-id="c423e-357">`article` 경로 매개 변수는 경로 매개 변수 이름 앞에 별표(`*`)를 표시하여 *범용*으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-357">The `article` route parameter is defined as a *catch-all* by the appearance of an asterisk (`*`) before the route parameter name.</span></span> <span data-ttu-id="c423e-358">범용 경로 매개 변수는 URL 경로의 나머지를 캡처하고 빈 문자열과 일치시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-358">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

::: moniker-end

<span data-ttu-id="c423e-359">다음 예제에서는 경로 제약 조건 및 데이터 토큰을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-359">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="c423e-360">앞의 템플릿은 `/en-US/Products/5`와 같은 URL 경로와 일치시키고, `{ controller = Products, action = Details, id = 5 }` 값 및 `{ locale = en-US }` 데이터 토큰을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-360">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![지역 Windows 토큰](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="c423e-362">경로 클래스 URL 생성</span><span class="sxs-lookup"><span data-stu-id="c423e-362">Route class URL generation</span></span>

<span data-ttu-id="c423e-363"><xref:Microsoft.AspNetCore.Routing.Route> 클래스는 경로 값의 집합을 해당 경로 템플릿과 결합하여 URL 생성을 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-363">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="c423e-364">이는 논리적으로 URL 경로와 일치시키는 역방향 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-364">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="c423e-365">URL 생성을 보다 잘 이해하려면 생성하려는 URL을 가정한 다음, 경로 템플릿을 해당 URL과 일치시키는 방법을 생각합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-365">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="c423e-366">어떤 값이 생성되나요?</span><span class="sxs-lookup"><span data-stu-id="c423e-366">What values would be produced?</span></span> <span data-ttu-id="c423e-367">이는 URL 생성이 <xref:Microsoft.AspNetCore.Routing.Route> 클래스에서 작동하는 방식과 대략적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-367">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="c423e-368">다음 예제에서는 일반 ASP.NET Core MVC 기본 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-368">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="c423e-369">`{ controller = Products, action = List }` 경로 값을 사용하면 `/Products/List` URL이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-369">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="c423e-370">경로 값은 URL 경로를 구성하기 위해 해당 경로 매개 변수에 대해 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-370">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="c423e-371">`id`는 선택적 경로 매개 변수이므로 `id`에 대한 값 없이 URL이 성공적으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-371">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="c423e-372">`{ controller = Home, action = Index }` 경로 값을 사용하면 `/` URL이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-372">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="c423e-373">제공된 경로 값이 기본값과 일치하고, 기본값에 해당하는 세그먼트는 안전하게 생략됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-373">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="c423e-374">뒤따르는 경로 정의(`/Home/Index` 및 `/`)를 사용하는 URL 생성 왕복에서는 모두 URL을 생성하는 데 사용된 것과 동일한 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-374">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="c423e-375">ASP.NET Core MVC를 사용하는 앱은 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper>를 사용하여 라우팅으로 직접 호출하는 대신 URL을 생성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-375">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="c423e-376">URL 생성에 대한 자세한 내용은 [URL 생성 참조](#url-generation-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-376">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="c423e-377">라우팅 미들웨어 사용</span><span class="sxs-lookup"><span data-stu-id="c423e-377">Use Routing Middleware</span></span>

<span data-ttu-id="c423e-378">앱의 프로젝트 파일에서 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-378">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="c423e-379">`Startup.ConfigureServices`의 서비스 컨테이너에 라우팅을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-379">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="c423e-380">경로는 `Startup.Configure` 메서드에서 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-380">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="c423e-381">샘플 앱에서 사용하는 API는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-381">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="c423e-382"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; HTTP GET 요청만 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-382"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="c423e-383">다음 표는 지정된 URI로 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-383">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="c423e-384">URI</span><span class="sxs-lookup"><span data-stu-id="c423e-384">URI</span></span>                    | <span data-ttu-id="c423e-385">응답</span><span class="sxs-lookup"><span data-stu-id="c423e-385">Response</span></span>                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | <span data-ttu-id="c423e-386">Hello!</span><span class="sxs-lookup"><span data-stu-id="c423e-386">Hello!</span></span> <span data-ttu-id="c423e-387">경로 값: [작업, 만들기], [id, 3]</span><span class="sxs-lookup"><span data-stu-id="c423e-387">Route values: [operation, create], [id, 3]</span></span> |
| `/package/track/-3`    | <span data-ttu-id="c423e-388">Hello!</span><span class="sxs-lookup"><span data-stu-id="c423e-388">Hello!</span></span> <span data-ttu-id="c423e-389">경로 값: [작업, 트랙], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="c423e-389">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/-3/`   | <span data-ttu-id="c423e-390">Hello!</span><span class="sxs-lookup"><span data-stu-id="c423e-390">Hello!</span></span> <span data-ttu-id="c423e-391">경로 값: [작업, 트랙], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="c423e-391">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/`      | <span data-ttu-id="c423e-392">요청이 실패하고, 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-392">The request falls through, no match.</span></span>              |
| `GET /hello/Joe`       | <span data-ttu-id="c423e-393">Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="c423e-393">Hi, Joe!</span></span>                                          |
| `POST /hello/Joe`      | <span data-ttu-id="c423e-394">요청이 실패하고, HTTP GET만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-394">The request falls through, matches HTTP GET only.</span></span> |
| `GET /hello/Joe/Smith` | <span data-ttu-id="c423e-395">요청이 실패하고, 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-395">The request falls through, no match.</span></span>              |

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c423e-396">단일 경로를 구성하는 경우 `IRouter` 인스턴스를 전달하는 <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-396">If you're configuring a single route, call <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> passing in an `IRouter` instance.</span></span> <span data-ttu-id="c423e-397"><xref:Microsoft.AspNetCore.Routing.RouteBuilder>를 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-397">You won't need to use <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.</span></span>

::: moniker-end

<span data-ttu-id="c423e-398">프레임워크는 경로를 만드는 확장 메서드 세트(<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-398">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c423e-399"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>과 같은 나열된 메서드 중 일부에는 <xref:Microsoft.AspNetCore.Http.RequestDelegate>가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-399">Some of listed methods, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, require a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="c423e-400"><xref:Microsoft.AspNetCore.Http.RequestDelegate>는 경로가 일치하는 경우 *경로 처리기*로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-400">The <xref:Microsoft.AspNetCore.Http.RequestDelegate> is used as the *route handler* when the route matches.</span></span> <span data-ttu-id="c423e-401">이 제품군의 다른 메서드는 경로 처리기로 사용할 미들웨어 파이프라인 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-401">Other methods in this family allow configuring a middleware pipeline for use as the route handler.</span></span> <span data-ttu-id="c423e-402">`Map*` 메서드에서 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>와 같은 처리기를 허용하지 않는 경우 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-402">If the `Map*` method doesn't accept a handler, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, it uses the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span>

::: moniker-end

<span data-ttu-id="c423e-403">`Map[Verb]` 메서드는 제약 조건을 사용하여 메서드 이름에서 HTTP 동사에 대한 경로를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-403">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="c423e-404">예제는 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 및 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-404">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="c423e-405">경로 템플릿 참조</span><span class="sxs-lookup"><span data-stu-id="c423e-405">Route template reference</span></span>

<span data-ttu-id="c423e-406">중괄호(`{ ... }`) 내 토큰은 경로가 일치하는 경우 바인딩될 *경로 매개 변수*를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-406">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="c423e-407">경로 세그먼트에 둘 이상의 경로 매개 변수를 정의할 수 있지만 리터럴 값으로 구분되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-407">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="c423e-408">예를 들어 `{controller=Home}{action=Index}`는 `{controller}` 및 `{action}` 사이에 리터럴 값이 없으므로 유효 경로일 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-408">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="c423e-409">이러한 경로 매개 변수는 이름이 있어야 하며 지정된 추가 특성을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-409">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="c423e-410">경로 매개 변수 이외의 리터럴 텍스트(예: `{id}`) 및 경로 구분 기호(`/`)는 URL의 텍스트와 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-410">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="c423e-411">텍스트 일치는 대/소문자를 구분하지 않으며 URL 경로의 디코딩된 표현을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-411">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="c423e-412">리터럴 경로 매개 변수 구분 기호(`{` 또는 `}`)와 일치시키려면 문자(`{{` 또는 `}}`)를 반복하여 구분 기호를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-412">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="c423e-413">선택적 파일 확장명이 있는 파일 이름을 캡처하려고 시도하는 URL 패턴에는 추가 고려 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-413">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="c423e-414">예를 들어 템플릿 `files/{filename}.{ext?}`를 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-414">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="c423e-415">`filename` 및 `ext` 모두에 대한 값이 있으면 두 값이 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-415">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="c423e-416">URL에 `filename`에 대한 값만 있으면 후행 마침표(`.`)가 선택 사항이므로 경로가 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-416">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="c423e-417">다음 URL은 이 경로와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-417">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c423e-418">별표(`*`) 또는 이중 별표(`**`)를 경로 매개 변수의 접두사로 사용하여 URI의 나머지 부분에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-418">You can use an asterisk (`*`) or double asterisk (`**`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="c423e-419">이러한 매개 변수는 *범용* 매개 변수라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-419">These are called a *catch-all* parameters.</span></span> <span data-ttu-id="c423e-420">예를 들어 `blog/{**slug}`는 `/blog`로 시작하고 `slug` 경로 값에 할당된 모든 값이 뒤따르는 모든 URI와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-420">For example, `blog/{**slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="c423e-421">범용 매개 변수는 빈 문자열과 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-421">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="c423e-422">catch-all 매개 변수는 경로 구분 기호(`/`) 문자를 포함하여 URL을 생성하는 데 경로가 사용될 때 적절한 문자를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-422">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="c423e-423">예를 들어 경로 값이 `{ path = "my/path" }`인 경로 `foo/{*path}`는 `foo/my%2Fpath`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-423">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="c423e-424">이스케이프된 슬래시에 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-424">Note the escaped forward slash.</span></span> <span data-ttu-id="c423e-425">경로 구분 기호 문자를 왕복하려면 `**` 경로 매개 변수 접두사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-425">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="c423e-426">`{ path = "my/path" }`가 있는 경로 `foo/{**path}`은 `foo/my/path`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-426">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c423e-427">별표(`*`)를 경로 매개 변수의 접두사로 사용하여 URI의 나머지 부분에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-427">You can use the asterisk (`*`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="c423e-428">이를 *범용* 매개 변수라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-428">This is called a *catch-all* parameter.</span></span> <span data-ttu-id="c423e-429">예를 들어 `blog/{*slug}`는 `/blog`로 시작하고 `slug` 경로 값에 할당된 모든 값이 뒤따르는 모든 URI와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-429">For example, `blog/{*slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="c423e-430">범용 매개 변수는 빈 문자열과 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-430">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="c423e-431">catch-all 매개 변수는 경로 구분 기호(`/`) 문자를 포함하여 URL을 생성하는 데 경로가 사용될 때 적절한 문자를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-431">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="c423e-432">예를 들어 경로 값이 `{ path = "my/path" }`인 경로 `foo/{*path}`는 `foo/my%2Fpath`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-432">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="c423e-433">이스케이프된 슬래시에 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-433">Note the escaped forward slash.</span></span>

::: moniker-end

<span data-ttu-id="c423e-434">경로 매개 변수에는 등호(`=`)로 구분된 매개 변수 이름 뒤에 기본값을 지정하여 지정된 *기본값*이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-434">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="c423e-435">예를 들어 `{controller=Home}`은 `controller`에 대한 기본값으로 `Home`을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-435">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="c423e-436">URL에 매개 변수에 대한 값이 없는 경우 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-436">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="c423e-437">경로 매개 변수는 `id?`에서와 같이 매개 변수 이름의 끝에 물음표(`?`)를 추가하여 선택적으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-437">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="c423e-438">선택적 값과 기본 경로 매개 변수 간의 차이점은 기본값이 있는 경로 매개 변수는 항상 값을 생성한다는 것입니다. 선택적 매개 변수에는 요청 URL에서 값을 제공하는 경우에만 값이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-438">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="c423e-439">경로 매개 변수에는 URL에서 바인딩된 경로 값과 일치해야 한다는 제약 조건이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-439">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="c423e-440">경로 매개 변수 이름 뒤에 콜론(`:`)과 제약 조건 이름을 추가하여 경로 매개 변수에서 *인라인 제약 조건*을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-440">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="c423e-441">제약 조건에 인수가 필요한 경우 제약 조건 이름 뒤에서 인수를 괄호(`(...)`)로 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-441">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="c423e-442">여러 인라인 제약 조건은 다른 콜론(`:`) 및 제약 조건 이름을 추가하여 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-442">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="c423e-443">제약 조건 이름 및 인수는 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>의 인스턴스를 만드는 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 서비스로 전달되어 URL 처리에서 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-443">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="c423e-444">예를 들어 경로 템플릿 `blog/{article:minlength(10)}`는 인수 `10`으로 `minlength` 제약 조건을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-444">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="c423e-445">경로 제약 조건 및 프레임워크에서 제공하는 제약 조건 목록에 대한 자세한 내용은 [경로 제약 조건 참조](#route-constraint-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-445">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c423e-446">또한 경로 매개 변수에는 링크를 생성하고 URL에 대한 작업 및 페이지와 일치할 때 매개 변수 값을 변환하는 매개 변수 변환기가 있을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-446">Route parameters may also have parameter transformers, which transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="c423e-447">제한 조건과 마찬가지로, 매개 변수 변환기는 경로 매개 변수 이름 뒤에 콜론(`:`)과 변환기 이름을 추가하여 라우트 매개 변수에 인라인으로 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-447">Like constraints, parameter transformers can be added inline to a route parameter by adding a colon (`:`) and transformer name after the route parameter name.</span></span> <span data-ttu-id="c423e-448">예를 들어 경로 템플릿 `blog/{article:slugify}`는 `slugify` 변환기를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-448">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="c423e-449">매개 변수 변환기에 대한 자세한 내용은 [매개 변수 변환기 참조](#parameter-transformer-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-449">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

::: moniker-end

<span data-ttu-id="c423e-450">다음 표에서는 경로 템플릿 예제 및 해당 동작을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-450">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="c423e-451">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="c423e-451">Route Template</span></span>                           | <span data-ttu-id="c423e-452">URI 일치 예제</span><span class="sxs-lookup"><span data-stu-id="c423e-452">Example Matching URI</span></span>    | <span data-ttu-id="c423e-453">요청 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="c423e-453">The request URI&hellip;</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="c423e-454">`/hello` 단일 경로만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-454">Only matches the single path `/hello`.</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="c423e-455">일치하고, `Page`를 `Home`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-455">Matches and sets `Page` to `Home`.</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="c423e-456">일치하고, `Page`를 `Contact`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-456">Matches and sets `Page` to `Contact`.</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="c423e-457">`Products` 컨트롤러 및 `List` 작업에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-457">Maps to the `Products` controller and `List` action.</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="c423e-458">`Products` 컨트롤러 및 `Details` 작업에 매핑합니다(`id`가 123으로 설정됨).</span><span class="sxs-lookup"><span data-stu-id="c423e-458">Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="c423e-459">`Home` 컨트롤러 및 `Index` 메서드에 매핑합니다(`id`가 무시됨).</span><span class="sxs-lookup"><span data-stu-id="c423e-459">Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="c423e-460">템플릿 사용은 일반적으로 라우팅에 대한 가장 간단한 방식입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-460">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="c423e-461">제약 조건 및 기본값을 경로 템플릿 외부에서 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-461">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="c423e-462">[로깅](xref:fundamentals/logging/index)을 사용하도록 설정하여 <xref:Microsoft.AspNetCore.Routing.Route>와 같은 기본 제공 라우팅 구현에서 요청과 일치시키는 방법을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-462">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="c423e-463">예약된 라우팅 이름</span><span class="sxs-lookup"><span data-stu-id="c423e-463">Reserved routing names</span></span>

<span data-ttu-id="c423e-464">다음 키워드는 예약된 이름이므로 경로 이름 또는 매개 변수로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-464">The following keywords are reserved names and can't be used as route names or parameters:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a><span data-ttu-id="c423e-465">경로 제약 조건 참조</span><span class="sxs-lookup"><span data-stu-id="c423e-465">Route constraint reference</span></span>

<span data-ttu-id="c423e-466">경로 제약 조건은 들어오는 URL과 일치하고 URL 경로가 경로 값으로 토큰화되면 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-466">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="c423e-467">일반적으로 경로 제약 조건은 경로 템플릿을 통해 연결된 경로 값을 검사하고 값 허용 여부에 대한 예/아니요 의사 결정을 내립니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-467">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="c423e-468">일부 경로 제약 조건은 경로 값 외부의 데이터를 사용하여 요청을 라우팅할 수 있는지 여부를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-468">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="c423e-469">예를 들어 <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint>는 해당 HTTP 동사에 따라 요청을 허용하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-469">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="c423e-470">제약 조건은 라우팅 요청 및 링크 생성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-470">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="c423e-471">제약 조건은 **입력 유효성 검사**에 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-471">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="c423e-472">제약 조건이 **입력 유효성 검사**에 사용되는 경우 잘못된 입력으로 인해 적절한 오류 메시지와 함께 *400 - 잘못된 요청* 대신 *404 - 찾을 수 없음* 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-472">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="c423e-473">경로 제약 조건은 특정 경로에 대한 입력의 유효성을 검사하는 것이 아니라 비슷한 경로를 **명확하게 구분하는 데** 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-473">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="c423e-474">다음 표에서는 경로 제약 조건 예제 및 예상되는 해당 동작을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-474">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="c423e-475">제약 조건</span><span class="sxs-lookup"><span data-stu-id="c423e-475">constraint</span></span> | <span data-ttu-id="c423e-476">예</span><span class="sxs-lookup"><span data-stu-id="c423e-476">Example</span></span> | <span data-ttu-id="c423e-477">일치하는 예제</span><span class="sxs-lookup"><span data-stu-id="c423e-477">Example Matches</span></span> | <span data-ttu-id="c423e-478">참고 사항</span><span class="sxs-lookup"><span data-stu-id="c423e-478">Notes</span></span> |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | <span data-ttu-id="c423e-479">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="c423e-479">`123456789`, `-123456789`</span></span> | <span data-ttu-id="c423e-480">임의의 정수와 일치</span><span class="sxs-lookup"><span data-stu-id="c423e-480">Matches any integer</span></span> |
| `bool` | `{active:bool}` | <span data-ttu-id="c423e-481">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="c423e-481">`true`, `FALSE`</span></span> | <span data-ttu-id="c423e-482">`true` 또는 `false` 일치(대/소문자 구분하지 않음)</span><span class="sxs-lookup"><span data-stu-id="c423e-482">Matches `true` or `false` (case-insensitive)</span></span> |
| `datetime` | `{dob:datetime}` | <span data-ttu-id="c423e-483">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="c423e-483">`2016-12-31`, `2016-12-31 7:32pm`</span></span> | <span data-ttu-id="c423e-484">유효한 `DateTime` 값 일치(고정 문화권에서 - 경고 참조)</span><span class="sxs-lookup"><span data-stu-id="c423e-484">Matches a valid `DateTime` value (in the invariant culture - see warning)</span></span> |
| `decimal` | `{price:decimal}` | <span data-ttu-id="c423e-485">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="c423e-485">`49.99`, `-1,000.01`</span></span> | <span data-ttu-id="c423e-486">유효한 `decimal` 값 일치(고정 문화권에서 - 경고 참조)</span><span class="sxs-lookup"><span data-stu-id="c423e-486">Matches a valid `decimal` value (in the invariant culture - see warning)</span></span> |
| `double` | `{weight:double}` | <span data-ttu-id="c423e-487">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="c423e-487">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="c423e-488">유효한 `double` 값 일치(고정 문화권에서 - 경고 참조)</span><span class="sxs-lookup"><span data-stu-id="c423e-488">Matches a valid `double` value (in the invariant culture - see warning)</span></span> |
| `float` | `{weight:float}` | <span data-ttu-id="c423e-489">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="c423e-489">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="c423e-490">유효한 `float` 값 일치(고정 문화권에서 - 경고 참조)</span><span class="sxs-lookup"><span data-stu-id="c423e-490">Matches a valid `float` value (in the invariant culture - see warning)</span></span> |
| `guid` | `{id:guid}` | <span data-ttu-id="c423e-491">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="c423e-491">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="c423e-492">유효한 `Guid` 값 일치</span><span class="sxs-lookup"><span data-stu-id="c423e-492">Matches a valid `Guid` value</span></span> |
| `long` | `{ticks:long}` | <span data-ttu-id="c423e-493">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="c423e-493">`123456789`, `-123456789`</span></span> | <span data-ttu-id="c423e-494">유효한 `long` 값 일치</span><span class="sxs-lookup"><span data-stu-id="c423e-494">Matches a valid `long` value</span></span> |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="c423e-495">문자열은 4자 이상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-495">String must be at least 4 characters</span></span> |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | <span data-ttu-id="c423e-496">문자열은 8자 이하여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-496">String must be no more than 8 characters</span></span> |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="c423e-497">문자열은 정확히 12자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-497">String must be exactly 12 characters long</span></span> |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="c423e-498">문자열의 길이는 8자 이상이며 16자 이하여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-498">String must be at least 8 and no more than 16 characters long</span></span> |
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="c423e-499">정수 값은 18자 이상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-499">Integer value must be at least 18</span></span> |
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="c423e-500">정수 값은 120자 이하여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-500">Integer value must be no more than 120</span></span> |
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="c423e-501">정수 값은 18자 이상이며 120자 이하여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-501">Integer value must be at least 18 but no more than 120</span></span> |
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="c423e-502">문자열은 하나 이상의 알파벳 문자(`a`-`z`, 대/소문자 구분)로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-502">String must consist of one or more alphabetical characters (`a`-`z`, case-insensitive)</span></span> |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="c423e-503">문자열은 정규식과 일치해야 합니다(정규식 정의에 대한 팁 참조).</span><span class="sxs-lookup"><span data-stu-id="c423e-503">String must match the regular expression (see tips about defining a regular expression)</span></span> |
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="c423e-504">URL을 생성하는 동안 비-매개 변수 값이 있도록 하는 데 사용됨</span><span class="sxs-lookup"><span data-stu-id="c423e-504">Used to enforce that a non-parameter value is present during URL generation</span></span> |

<span data-ttu-id="c423e-505">콜론으로 구분된 여러 개의 제약 조건을 단일 매개 변수에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-505">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="c423e-506">예를 들어 다음 제약 조건은 매개 변수를 1 이상의 정수 값으로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-506">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="c423e-507">CLR 형식(예: `int` 또는 `DateTime`)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-507">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="c423e-508">이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-508">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="c423e-509">프레임워크에서 제공한 경로 제약 조건은 경로 값에 저장된 값을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-509">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="c423e-510">URL에서 구문 분석되는 모든 경로 값은 문자열로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-510">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="c423e-511">예를 들어 `float` 제약 조건은 경로 값을 부동으로 변환하려고 하지만 변환된 값은 부동으로 변환될 수 있는지 확인하는 데만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-511">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="c423e-512">정규식</span><span class="sxs-lookup"><span data-stu-id="c423e-512">Regular expressions</span></span>

<span data-ttu-id="c423e-513">ASP.NET Core 프레임워크는 정규식 생성자에 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-513">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="c423e-514">이러한 멤버에 대한 설명은 <xref:System.Text.RegularExpressions.RegexOptions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-514">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="c423e-515">정규식은 라우팅 및 C# 언어에서 사용하는 것과 유사한 구분 기호 및 토큰을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-515">Regular expressions use delimiters and tokens similar to those used by Routing and the C# language.</span></span> <span data-ttu-id="c423e-516">정규식 토큰은 이스케이프되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-516">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="c423e-517">라우팅에서 `^\d{3}-\d{2}-\d{4}$` 정규식을 사용하려면 `\` 문자열 이스케이프 문자를 이스케이프할 수 있도록 식의 문자열에 제공된 `\`(단일 백슬래시) 문자가 C# 원본 파일의 `\\`(이중 백슬래시) 문자로 있어야 합니다([약어 문자열 리터럴](/dotnet/csharp/language-reference/keywords/string)을 사용하지 않는 한).</span><span class="sxs-lookup"><span data-stu-id="c423e-517">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing, the expression must have the `\` (single backslash) characters provided in the string as `\\` (double backslash) characters in the C# source file in order to escape the `\` string escape character (unless using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string)).</span></span> <span data-ttu-id="c423e-518">라우팅 매개 변수 구분 기호 문자(`{`, `}`, `[`, `]`)를 이스케이프하려면 식에서 해당 문자를 이중으로 사용합니다(`{{`, `}`, `[[`, `]]`).</span><span class="sxs-lookup"><span data-stu-id="c423e-518">To escape routing parameter delimiter characters (`{`, `}`, `[`, `]`), double the characters in the expression (`{{`, `}`, `[[`, `]]`).</span></span> <span data-ttu-id="c423e-519">다음 표는 정규식 및 이스케이프된 버전을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-519">The following table shows a regular expression and the escaped version.</span></span>

| <span data-ttu-id="c423e-520">정규식</span><span class="sxs-lookup"><span data-stu-id="c423e-520">Regular Expression</span></span>    | <span data-ttu-id="c423e-521">이스케이프된 정규식</span><span class="sxs-lookup"><span data-stu-id="c423e-521">Escaped Regular Expression</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="c423e-522">라우팅에 사용되는 정규식은 캐럿(`^`) 문자로 시작하고 문자열의 시작 위치와 일치하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-522">Regular expressions used in routing often start with the caret (`^`) character and match starting position of the string.</span></span> <span data-ttu-id="c423e-523">식은 달러 기호(`$`) 문자로 끝나고 문자열의 끝과 일치하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-523">The expressions often end with the dollar sign (`$`) character and match end of the string.</span></span> <span data-ttu-id="c423e-524">`^` 및 `$` 문자는 정규식이 전체 경로 매개 변수 값과 일치하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-524">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="c423e-525">`^` 및 `$` 문자 없이 정규식은 문자열 내의 모든 하위 문자열과 일치합니다. 이는 종종 원하는 것이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-525">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="c423e-526">다음 표에서는 예제를 제공하고, 일치하거나 일치에 실패하는 이유를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-526">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="c423e-527">식</span><span class="sxs-lookup"><span data-stu-id="c423e-527">Expression</span></span>   | <span data-ttu-id="c423e-528">문자열</span><span class="sxs-lookup"><span data-stu-id="c423e-528">String</span></span>    | <span data-ttu-id="c423e-529">일치</span><span class="sxs-lookup"><span data-stu-id="c423e-529">Match</span></span> | <span data-ttu-id="c423e-530">주석</span><span class="sxs-lookup"><span data-stu-id="c423e-530">Comment</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="c423e-531">hello</span><span class="sxs-lookup"><span data-stu-id="c423e-531">hello</span></span>     | <span data-ttu-id="c423e-532">예</span><span class="sxs-lookup"><span data-stu-id="c423e-532">Yes</span></span>   | <span data-ttu-id="c423e-533">부분 문자열 일치</span><span class="sxs-lookup"><span data-stu-id="c423e-533">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="c423e-534">123abc456</span><span class="sxs-lookup"><span data-stu-id="c423e-534">123abc456</span></span> | <span data-ttu-id="c423e-535">예</span><span class="sxs-lookup"><span data-stu-id="c423e-535">Yes</span></span>   | <span data-ttu-id="c423e-536">부분 문자열 일치</span><span class="sxs-lookup"><span data-stu-id="c423e-536">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="c423e-537">mz</span><span class="sxs-lookup"><span data-stu-id="c423e-537">mz</span></span>        | <span data-ttu-id="c423e-538">예</span><span class="sxs-lookup"><span data-stu-id="c423e-538">Yes</span></span>   | <span data-ttu-id="c423e-539">식 일치</span><span class="sxs-lookup"><span data-stu-id="c423e-539">Matches expression</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="c423e-540">MZ</span><span class="sxs-lookup"><span data-stu-id="c423e-540">MZ</span></span>        | <span data-ttu-id="c423e-541">예</span><span class="sxs-lookup"><span data-stu-id="c423e-541">Yes</span></span>   | <span data-ttu-id="c423e-542">대/소문자 구분하지 않음</span><span class="sxs-lookup"><span data-stu-id="c423e-542">Not case sensitive</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="c423e-543">hello</span><span class="sxs-lookup"><span data-stu-id="c423e-543">hello</span></span>     | <span data-ttu-id="c423e-544">아니요</span><span class="sxs-lookup"><span data-stu-id="c423e-544">No</span></span>    | <span data-ttu-id="c423e-545">위의 `^` 및 `$` 참조</span><span class="sxs-lookup"><span data-stu-id="c423e-545">See `^` and `$` above</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="c423e-546">123abc456</span><span class="sxs-lookup"><span data-stu-id="c423e-546">123abc456</span></span> | <span data-ttu-id="c423e-547">아니요</span><span class="sxs-lookup"><span data-stu-id="c423e-547">No</span></span>    | <span data-ttu-id="c423e-548">위의 `^` 및 `$` 참조</span><span class="sxs-lookup"><span data-stu-id="c423e-548">See `^` and `$` above</span></span> |

<span data-ttu-id="c423e-549">정규식 구문에 대한 자세한 내용은 [.NET Framework 정규식](/dotnet/standard/base-types/regular-expression-language-quick-reference)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-549">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="c423e-550">가능한 값의 알려진 집합으로 매개 변수를 제한하려면 정규식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-550">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="c423e-551">예를 들어 `{action:regex(^(list|get|create)$)}`는 `action` 경로 값을 `list`, `get` 또는 `create`으로만 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-551">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="c423e-552">제약 조건 사전으로 전달되면 `^(list|get|create)$` 문자열은 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-552">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="c423e-553">알려진 제약 조건 중 하나와 일치하지 않는 제약 조건 사전(템플릿 내 인라인이 아님)에서 전달되는 제약 조건도 정규식으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-553">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="c423e-554">사용자 지정 경로 제약 조건</span><span class="sxs-lookup"><span data-stu-id="c423e-554">Custom Route Constraints</span></span>

<span data-ttu-id="c423e-555">기본 제공 경로 제약 조건 외에도 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 인터페이스를 구현하여 사용자 지정 경로 제약 조건을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-555">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="c423e-556"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 인터페이스에는 제약 조건이 충족되는 경우 `true`를 반환하고 그렇지 않은 경우 `false`를 반환하는 `Match` 단일 메서드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-556">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="c423e-557">사용자 지정 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>를 사용하려면 앱의 서비스 컨테이너에 있는 앱의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>에 경로 제약 조건 형식을 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-557">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="c423e-558"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>은 경로 제약 조건 키를 해당 제약 조건의 유효성을 검사하는 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 구현으로 매핑하는 사전입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-558">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="c423e-559">`Startup.ConfigureServices`에서 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 호출의 일부로 또는 `services.Configure<RouteOptions>`를 사용하여 직접 <xref:Microsoft.AspNetCore.Routing.RouteOptions>를 구성하여 앱의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-559">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="c423e-560">예:</span><span class="sxs-lookup"><span data-stu-id="c423e-560">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="c423e-561">제약 조건 형식을 등록할 때 지정한 이름을 사용하여 이제 일반적인 방식으로 제약 조건을 경로에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-561">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="c423e-562">예:</span><span class="sxs-lookup"><span data-stu-id="c423e-562">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

::: moniker range=">= aspnetcore-2.2"

## <a name="parameter-transformer-reference"></a><span data-ttu-id="c423e-563">매개 변수 변환기 참조</span><span class="sxs-lookup"><span data-stu-id="c423e-563">Parameter transformer reference</span></span>

<span data-ttu-id="c423e-564">매개 변수 변환기:</span><span class="sxs-lookup"><span data-stu-id="c423e-564">Parameter transformers:</span></span>

* <span data-ttu-id="c423e-565"><xref:Microsoft.AspNetCore.Routing.Route>에 대한 링크를 생성할 때 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-565">Execute when generating a link for a <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>
* <span data-ttu-id="c423e-566">`Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`를 구현해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-566">Implement `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.</span></span>
* <span data-ttu-id="c423e-567"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-567">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="c423e-568">매개 변수의 경로 값을 가져와서 새 문자열 값으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-568">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="c423e-569">생성된 링크에서 변환된 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-569">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="c423e-570">예를 들어, `Url.Action(new { article = "MyTestArticle" })`을 사용하는 경로 패턴 `blog\{article:slugify}`의 사용자 지정 `slugify` 매개 변수 변환기는 `blog\my-test-article`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-570">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="c423e-571">경로 패턴에서 매개 변수 변환기를 사용하려면 먼저 `Startup.ConfigureServices`의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-571">To use a parameter transformer in a route pattern, configure it first using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

<span data-ttu-id="c423e-572">매개 변수 변환기는 프레임워크에서 사용하여 엔드포인트가 확인되는 URI를 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-572">Parameter transformers are used by the framework to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="c423e-573">예를 들어 ASP.NET Core MVC는 매개 변수 변환기를 사용하여 `area` , `controller` , `action` 및 `page`와 일치하도록 사용되는 경로 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-573">For example, ASP.NET Core MVC uses parameter transformers to transform the route value used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="c423e-574">이전 경로를 사용하면 `SubscriptionManagementController.GetAll()` 작업이 URI `/subscription-management/get-all`과 일치됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-574">With the preceding route, the action `SubscriptionManagementController.GetAll()` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="c423e-575">매개 변수 변환기는 링크를 생성하는 데 사용되는 경로 값을 변경하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-575">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="c423e-576">예를 들어 `Url.Action("GetAll", "SubscriptionManagement")`는 `/subscription-management/get-all`을 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-576">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="c423e-577">ASP.NET Core는 생성된 경로가 있는 매개 변수 변환기를 사용하기 위한 API 규칙을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-577">ASP.NET Core provides API conventions for using a parameter transformers with generated routes:</span></span>

* <span data-ttu-id="c423e-578">ASP.NET Core MVC에는 `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 규칙이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-578">ASP.NET Core MVC has the `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API convention.</span></span> <span data-ttu-id="c423e-579">이 규칙은 앱의 모든 특성 경로에 지정된 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-579">This convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="c423e-580">매개 변수 변환기는 특성 경로 토큰이 교체될 때 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-580">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="c423e-581">자세한 내용은 [매개 변수 변환기를 사용하여 토큰 교체 사용자 지정](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-581">For more information, see [Use a parameter transformer to customize token replacement](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* <span data-ttu-id="c423e-582">Razor Pages에는 `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 규칙이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-582">Razor Pages has the `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API convention.</span></span> <span data-ttu-id="c423e-583">이 규칙은 자동으로 검색된 모든 Razor Pages에 지정된 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-583">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="c423e-584">매개 변수 변환기는 Razor Pages 경로의 폴더와 파일 이름 부분을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-584">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="c423e-585">자세한 내용은 [매개 변수 변환기를 사용하여 페이지 경로 사용자 지정](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-585">For more information, see [Use a parameter transformer to customize page routes](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

::: moniker-end

## <a name="url-generation-reference"></a><span data-ttu-id="c423e-586">URL 생성 참조</span><span class="sxs-lookup"><span data-stu-id="c423e-586">URL generation reference</span></span>

<span data-ttu-id="c423e-587">다음 예제에서는 경로 값의 사전 및 <xref:Microsoft.AspNetCore.Routing.RouteCollection>이 지정된 경로에 대한 링크를 생성하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-587">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="c423e-588">위의 샘플 끝부분에서 생성된 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>는 `/package/create/123`입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-588">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="c423e-589">사전은 "추적 패키지 경로" 템플릿, `package/{operation}/{id}`의 `operation` 및 `id` 경로 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-589">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="c423e-590">자세한 내용은 [라우팅 미들웨어 사용](#use-routing-middleware) 섹션의 샘플 코드 또는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-590">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="c423e-591"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 생성자에 대한 두 번째 매개 변수는 *앰비언트 값*의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-591">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="c423e-592">개발자가 요청 컨텍스트 내에서 지정해야 하는 값의 수를 제한하므로 앰비언트 값은 사용하기 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-592">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="c423e-593">현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-593">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="c423e-594">ASP.NET Core MVC 앱의 `HomeController`에 대한 `About` 작업에서는 `Index` 작업에 연결하기 위해 컨트롤러 경로 값을 지정할 필요가 없으며, `Home`이라는 앰비언트 값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-594">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="c423e-595">매개 변수와 일치하지 않는 앰비언트 값은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-595">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="c423e-596">명시적으로 제공된 값이 앰비언트 값을 재정의하는 경우에도 앰비언트 값이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-596">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="c423e-597">URL에서 일치는 왼쪽에서 오른쪽으로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-597">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="c423e-598">명시적으로 제공되지만 경로의 세그먼트와 일치하지 않는 값은 쿼리 문자열에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-598">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="c423e-599">다음 표에서 경로 템플릿 `{controller}/{action}/{id?}`를 사용하는 경우 결과를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-599">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="c423e-600">앰비언트 값</span><span class="sxs-lookup"><span data-stu-id="c423e-600">Ambient Values</span></span>                     | <span data-ttu-id="c423e-601">명시적 값</span><span class="sxs-lookup"><span data-stu-id="c423e-601">Explicit Values</span></span>                        | <span data-ttu-id="c423e-602">결과</span><span class="sxs-lookup"><span data-stu-id="c423e-602">Result</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="c423e-603">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="c423e-603">controller = "Home"</span></span>                | <span data-ttu-id="c423e-604">action = "About"</span><span class="sxs-lookup"><span data-stu-id="c423e-604">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="c423e-605">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="c423e-605">controller = "Home"</span></span>                | <span data-ttu-id="c423e-606">controller = "Order", action = "About"</span><span class="sxs-lookup"><span data-stu-id="c423e-606">controller = "Order", action = "About"</span></span> | `/Order/About`          |
| <span data-ttu-id="c423e-607">controller = "Home", color = "Red"</span><span class="sxs-lookup"><span data-stu-id="c423e-607">controller = "Home", color = "Red"</span></span> | <span data-ttu-id="c423e-608">action = "About"</span><span class="sxs-lookup"><span data-stu-id="c423e-608">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="c423e-609">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="c423e-609">controller = "Home"</span></span>                | <span data-ttu-id="c423e-610">action = "About", color = "Red"</span><span class="sxs-lookup"><span data-stu-id="c423e-610">action = "About", color = "Red"</span></span>        | `/Home/About?color=Red` |

<span data-ttu-id="c423e-611">경로에 매개 변수에 해당하지 않고 값이 명시적으로 제공된 기본값이 있는 경우 기본값과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-611">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="c423e-612">`controller` 및 `action`에 대해 일치하는 값이 제공되는 경우에만 링크 생성에서 이 경로에 대한 링크를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-612">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="c423e-613">복잡한 세그먼트</span><span class="sxs-lookup"><span data-stu-id="c423e-613">Complex segments</span></span>

<span data-ttu-id="c423e-614">복잡한 세그먼트(예: `[Route("/x{token}y")]`)는 non-greedy 방식으로 오른쪽에서 왼쪽으로 리터럴을 매칭하여 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-614">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="c423e-615">복잡한 세그먼트 일치 방법에 대한 자세한 설명은 [이 코드](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c423e-615">See [this code](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="c423e-616">[코드 샘플](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)은 ASP.NET Core에서 사용되지 않지만 복잡한 세그먼트에 대한 적절한 설명을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c423e-616">The [code sample](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->
