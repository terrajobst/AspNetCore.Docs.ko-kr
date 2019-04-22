---
title: ASP.NET Core의 팩터리 기반 미들웨어 활성화
author: guardrex
description: ASP.NET Core에서 팩터리 기반 활성화 구현으로 강력한 형식의 미들웨어를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 9305616ce3f2ef49cf9dfcab719f673c0fb4b51e
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "58809168"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="56b90-103">ASP.NET Core의 팩터리 기반 미들웨어 활성화</span><span class="sxs-lookup"><span data-stu-id="56b90-103">Factory-based middleware activation in ASP.NET Core</span></span>

<span data-ttu-id="56b90-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="56b90-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="56b90-105"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>는 [미들웨어](xref:fundamentals/middleware/index) 활성화에 대한 확장성 지점입니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-105"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="56b90-106"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 확장 메서드는 미들웨어의 등록된 형식이 <xref:Microsoft.AspNetCore.Http.IMiddleware>를 구현하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-106"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="56b90-107">구현하는 경우 컨테이너에 등록된 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 인스턴스는 규칙 기반 미들웨어 활성화 논리를 사용하는 대신 <xref:Microsoft.AspNetCore.Http.IMiddleware> 구현을 확인하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-107">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="56b90-108">미들웨어는 앱의 서비스 컨테이너의 [범위가 지정되거나 일시적인 서비스](xref:fundamentals/dependency-injection#service-lifetimes)로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-108">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="56b90-109">혜택:</span><span class="sxs-lookup"><span data-stu-id="56b90-109">Benefits:</span></span>

* <span data-ttu-id="56b90-110">클라이언트 요청당 활성화(범위가 지정된 서비스 주입)</span><span class="sxs-lookup"><span data-stu-id="56b90-110">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="56b90-111">미들웨어의 강력한 형식 지정</span><span class="sxs-lookup"><span data-stu-id="56b90-111">Strong typing of middleware</span></span>

<span data-ttu-id="56b90-112"><xref:Microsoft.AspNetCore.Http.IMiddleware>는 클라이언트 요청(연결)마다 활성화되므로 범위가 지정된 서비스는 미들웨어의 생성자에 주입될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-112"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="56b90-113">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56b90-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="56b90-114">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="56b90-114">IMiddleware</span></span>

<span data-ttu-id="56b90-115"><xref:Microsoft.AspNetCore.Http.IMiddleware>는 앱의 요청 파이프라인에 대한 미들웨어를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-115"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="56b90-116">[InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) 메서드는 요청을 처리하고 미들웨어의 실행을 나타내는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-116">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="56b90-117">규칙에 따라 미들웨어 활성화:</span><span class="sxs-lookup"><span data-stu-id="56b90-117">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="56b90-118"><xref:Microsoft.AspNetCore.Http.MiddlewareFactory>에 따라 미들웨어 활성화:</span><span class="sxs-lookup"><span data-stu-id="56b90-118">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="56b90-119">확장은 미들웨어에 대해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-119">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="56b90-120">개체를 <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>를 사용하여 팩터리 활성화된 미들웨어에 전달할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-120">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="56b90-121">팩터리 활성화된 미들웨어는 `Startup.ConfigureServices`의 기본 제공 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-121">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="56b90-122">두 미들웨어는 모두 `Startup.Configure`의 요청 처리 파이프라인에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-122">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="56b90-123">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="56b90-123">IMiddlewareFactory</span></span>

<span data-ttu-id="56b90-124"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>는 미들웨어를 만드는 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-124"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="56b90-125">미들웨어 팩터리 구현은 범위가 지정된 서비스로 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-125">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="56b90-126">기본 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 구현인 <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>는 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 패키지에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56b90-126">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56b90-127">추가 자료</span><span class="sxs-lookup"><span data-stu-id="56b90-127">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
