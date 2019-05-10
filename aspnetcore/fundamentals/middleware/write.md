---
title: 사용자 지정 ASP.NET Core 미들웨어 작성
author: rick-anderson
description: 사용자 지정 ASP.NET Core 미들웨어 작성 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 02/14/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: 2c5577394a10370d92c8a83f9d806b63f3245c8b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64889168"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="ccffc-103">사용자 지정 ASP.NET Core 미들웨어 작성</span><span class="sxs-lookup"><span data-stu-id="ccffc-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="ccffc-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ccffc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ccffc-105">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 어셈블리되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="ccffc-106">ASP.NET Core는 풍부한 기본 제공 미들웨어 구성 요소 세트를 제공하지만 일부 시나리오에서는 사용자 지정 미들웨어를 작성하려고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="ccffc-107">미들웨어 클래스</span><span class="sxs-lookup"><span data-stu-id="ccffc-107">Middleware class</span></span>

<span data-ttu-id="ccffc-108">미들웨어는 일반적으로 클래스에서 캡슐화되고 확장 메서드로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-108">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="ccffc-109">쿼리 문자열에서 현재 요청에 대한 문화권을 설정하는 다음 미들웨어를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-109">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

<span data-ttu-id="ccffc-110">위의 샘플 코드는 미들웨어 구성 요소를 만드는 방법을 보여주는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-110">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="ccffc-111">ASP.NET Core의 기본 제공 지역화 지원은 <xref:fundamentals/localization>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ccffc-111">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="ccffc-112">문화권을 전달하여 미들웨어를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-112">You can test the middleware by passing in the culture.</span></span> <span data-ttu-id="ccffc-113">예를 들어, `http://localhost:7997/?culture=no`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-113">For example, `http://localhost:7997/?culture=no`.</span></span>

<span data-ttu-id="ccffc-114">다음 코드는 미들웨어 대리자를 클래스로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-114">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="ccffc-115">미들웨어 `Task` 메서드의 이름은 `Invoke`여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-115">The middleware `Task` method's name must be `Invoke`.</span></span> <span data-ttu-id="ccffc-116">ASP.NET 코어 2.0 이상에서는 이름이 `Invoke` 또는 `InvokeAsync`일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-116">In ASP.NET Core 2.0 or later, the name can be either `Invoke` or `InvokeAsync`.</span></span>

::: moniker-end

## <a name="middleware-extension-method"></a><span data-ttu-id="ccffc-117">미들웨어 확장 메서드</span><span class="sxs-lookup"><span data-stu-id="ccffc-117">Middleware extension method</span></span>

<span data-ttu-id="ccffc-118">다음 확장 메서드는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>를 통해 미들웨어를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-118">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="ccffc-119">다음 코드는 `Startup.Configure`에서 미들웨어를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-119">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="ccffc-120">미들웨어는 해당 생성자에서 해당 종속성을 노출하여 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 따라야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-120">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="ccffc-121">미들웨어는 *애플리케이션 수명*당 한 번 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-121">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="ccffc-122">요청 내에서 서비스를 미들웨어와 공유해야 하는 경우 [요청당 종속성](#per-request-dependencies) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ccffc-122">See the [Per-request dependencies](#per-request-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="ccffc-123">미들웨어 구성 요소는 생성자 매개 변수를 통해 [DI(종속성 주입)](xref:fundamentals/dependency-injection)에서 해당 종속성을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-123">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="ccffc-124">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___)는 추가 매개 변수를 직접 수락할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-124">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-dependencies"></a><span data-ttu-id="ccffc-125">요청당 종속성</span><span class="sxs-lookup"><span data-stu-id="ccffc-125">Per-request dependencies</span></span>

<span data-ttu-id="ccffc-126">미들웨어는 요청당이 아닌 앱 시작 시 생성되므로 미들웨어 생성자에 의해 사용되는 *범위가 지정된* 수명 서비스는 각 요청 중에 다른 종속성 주입된 형식과 공유되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-126">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="ccffc-127">*범위가 지정된* 서비스를 미들웨어와 다른 형식 간에 공유해야 하는 경우 이러한 서비스를 `Invoke` 메서드의 서명에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-127">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="ccffc-128">`Invoke` 메서드는 DI로 채워지는 추가 매개 변수를 수락할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ccffc-128">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="ccffc-129">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ccffc-129">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
