---
title: ASP.NET Core에서 타사 컨테이너를 사용한 미들웨어 활성화
author: guardrex
description: ASP.NET Core에서 팩터리 기반 활성화 및 타사 컨테이너를 사용하여 강력한 형식의 미들웨어를 사용하는 방법을 배웁니다.
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2018
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 9e4d4c6c0232ebc51ad08923e10164262b652280
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64888078"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="fb1d8-103">ASP.NET Core에서 타사 컨테이너를 사용한 미들웨어 활성화</span><span class="sxs-lookup"><span data-stu-id="fb1d8-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

<span data-ttu-id="fb1d8-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="fb1d8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fb1d8-105">이 아티클에서는 타사 컨테이너를 사용하여 [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) 및 [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware)를 [미들웨어](xref:fundamentals/middleware/index) 활성화를 위한 확장 지점으로 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-105">This article demonstrates how to use [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) and [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="fb1d8-106">`IMiddlewareFactory` 및 `IMiddleware`에 대한 소개 정보는 [팩터리 기반 미들웨어 활성화](xref:fundamentals/middleware/extensibility) 토픽을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-106">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see the [Factory-based middleware activation](xref:fundamentals/middleware/extensibility) topic.</span></span>

<span data-ttu-id="fb1d8-107">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fb1d8-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fb1d8-108">샘플 앱은 `IMiddlewareFactory` 구현 `SimpleInjectorMiddlewareFactory`를 사용한 미들웨어 활성화를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-108">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="fb1d8-109">이 샘플에서는 [간단한 인젝터](https://simpleinjector.org) DI(종속성 주입) 컨테이너를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-109">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="fb1d8-110">이 샘플의 미들웨어 구현은 쿼리 문자열 매개 변수(`key`)에서 제공한 값을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-110">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="fb1d8-111">이 미들웨어는 주입된 데이터베이스 컨텍스트(범위가 지정된 서비스)를 사용하여 메모리 내 데이터베이스에 쿼리 문자열 값을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-111">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="fb1d8-112">이 샘플 앱에서는 순전히 예시 목적으로 [간단한 인젝터](https://github.com/simpleinjector/SimpleInjector)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-112">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="fb1d8-113">간단한 인젝터의 사용은 보증하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-113">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="fb1d8-114">간단한 인젝터 설명서 및 GitHub 문제에 설명된 미들웨어 활성화 방법은 간단한 인젝터의 관리자에서 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-114">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="fb1d8-115">자세한 내용은 [간단한 인젝터 설명서](https://simpleinjector.readthedocs.io/en/latest/index.html) 및 [간단한 인젝터 GitHub 리포지토리](https://github.com/simpleinjector/SimpleInjector)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-115">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="fb1d8-116">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="fb1d8-116">IMiddlewareFactory</span></span>

<span data-ttu-id="fb1d8-117">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory)는 미들웨어를 만드는 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-117">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) provides methods to create middleware.</span></span>

<span data-ttu-id="fb1d8-118">샘플 앱에서는 미들웨어 팩터리를 구현하여 `SimpleInjectorActivatedMiddleware` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-118">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="fb1d8-119">미들웨어 팩터리는 간단한 인젝터 컨테이너를 사용하여 미들웨어를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-119">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="fb1d8-120">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="fb1d8-120">IMiddleware</span></span>

<span data-ttu-id="fb1d8-121">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware)는 앱의 요청 파이프라인에 대한 미들웨어를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-121">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="fb1d8-122">`IMiddlewareFactory` 구현에 의해 활성화된 미들웨어(*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="fb1d8-122">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="fb1d8-123">미들웨어에 대한 확장(*Middleware/MiddlewareExtensions.cs*)이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-123">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="fb1d8-124">`Startup.ConfigureServices`는 여러 작업을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-124">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="fb1d8-125">간단한 인젝터 컨테이너를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-125">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="fb1d8-126">팩터리와 미들웨어를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-126">Register the factory and middleware.</span></span>
* <span data-ttu-id="fb1d8-127">Razor 페이지의 간단한 인젝터 컨테이너에서 앱의 데이터베이스 컨텍스트를 사용할 수 있게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-127">Make the app's database context available from the Simple Injector container for a Razor Page.</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="fb1d8-128">미들웨어는 `Startup.Configure`의 요청 처리 파이프라인에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb1d8-128">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet2&highlight=13)]

## <a name="additional-resources"></a><span data-ttu-id="fb1d8-129">추가 자료</span><span class="sxs-lookup"><span data-stu-id="fb1d8-129">Additional resources</span></span>

* [<span data-ttu-id="fb1d8-130">미들웨어</span><span class="sxs-lookup"><span data-stu-id="fb1d8-130">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="fb1d8-131">팩터리 기반 미들웨어 활성화</span><span class="sxs-lookup"><span data-stu-id="fb1d8-131">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="fb1d8-132">간단한 인젝터 GitHub 리포지토리</span><span class="sxs-lookup"><span data-stu-id="fb1d8-132">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="fb1d8-133">간단한 인젝터 설명서</span><span class="sxs-lookup"><span data-stu-id="fb1d8-133">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
