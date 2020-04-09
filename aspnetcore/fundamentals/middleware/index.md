---
title: ASP.NET Core 미들웨어 기본 사항
author: rick-anderson
description: ASP.NET Core 미들웨어 및 요청 파이프라인에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
uid: fundamentals/middleware/index
ms.openlocfilehash: 6bf8ed823386ca4e1cf78982f7fba41fba429db8
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751060"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="d7927-103">ASP.NET Core 미들웨어</span><span class="sxs-lookup"><span data-stu-id="d7927-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d7927-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d7927-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d7927-105">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="d7927-106">각 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-106">Each component:</span></span>

* <span data-ttu-id="d7927-107">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="d7927-108">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="d7927-109">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="d7927-110">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="d7927-111">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="d7927-112">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="d7927-113">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어*라고 하며, *미들웨어 구성 요소*라고 부르기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="d7927-114">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="d7927-115">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="d7927-116"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="d7927-117">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="d7927-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="d7927-118">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="d7927-119">다음 다이어그램은 그 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="d7927-120">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-120">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="d7927-124">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="d7927-125">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="d7927-126">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="d7927-127">이 경우 실제 요청 파이프라인은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="d7927-128">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="d7927-129"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="d7927-130">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="d7927-131">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="d7927-132">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="d7927-133">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="d7927-134">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="d7927-135">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어*로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="d7927-136">추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="d7927-137">그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7927-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="d7927-138">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="d7927-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="d7927-139">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="d7927-140">예를 들어 헤더 및 상태 코드를 설정하는 변경 작업은 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-140">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="d7927-141">`next`를 호출한 후 응답 본문에 작성할 경우:</span><span class="sxs-lookup"><span data-stu-id="d7927-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="d7927-142">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-142">May cause a protocol violation.</span></span> <span data-ttu-id="d7927-143">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="d7927-144">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-144">May corrupt the body format.</span></span> <span data-ttu-id="d7927-145">예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="d7927-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="d7927-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 대리자는 `next` 매개 변수를 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="d7927-148">첫 번째 `Run` 대리자는 항상 터미널이며 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="d7927-149">`Run`이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-149">`Run` is a convention.</span></span> <span data-ttu-id="d7927-150">일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="d7927-151">위 예제에서 `Run` 대리자는 응답에 `"Hello from 2nd delegate."`를 쓴 다음 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="d7927-152">`Run` 대리자 뒤에 추가된 다른 `Use` 또는 `Run` 대리자는 호출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="d7927-153">미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="d7927-153">Middleware order</span></span>

<span data-ttu-id="d7927-154">다음 다이어그램은 ASP.NET Core MVC 및 Razor Pages 앱의 전체 요청 처리 파이프라인을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-154">The following diagram shows the complete request processing pipeline for ASP.NET Core MVC and Razor Pages apps.</span></span> <span data-ttu-id="d7927-155">일반적인 앱에서 기존 미들웨어의 순서가 지정되는 방식과 사용자 지정 미들웨어가 추가되는 위치를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-155">You can see how, in a typical app, existing middlewares are ordered and where custom middlewares are added.</span></span> <span data-ttu-id="d7927-156">사용자는 원하는 대로 기존 미들웨어의 순서를 바꾸거나 시나리오의 필요에 따라 새 사용자 지정 미들웨어를 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-156">You have full control over how to reorder existing middlewares or inject new custom middlewares as necessary for your scenarios.</span></span>

![ASP.NET Core 미들웨어 파이프라인](index/_static/middleware-pipeline.svg)

<span data-ttu-id="d7927-158">앞에 나온 다이어그램의 **엔드포인트** 미들웨어는 해당 앱 형식&mdash;MVC 또는 Razor Pages에 따라 필터 파이프라인을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-158">The **Endpoint** middleware in the preceding diagram executes the filter pipeline for the corresponding app type&mdash;MVC or Razor Pages.</span></span>

![ASP.NET Core 필터 파이프라인](index/_static/mvc-endpoint.svg)

<span data-ttu-id="d7927-160">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-160">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="d7927-161">순서는 보안, 성능 및 기능에 **중요**합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-161">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="d7927-162">다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 권장 순서에 따라 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-162">The following `Startup.Configure` method adds security-related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="d7927-163">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="d7927-163">In the preceding code:</span></span>

* <span data-ttu-id="d7927-164">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-164">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="d7927-165">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-165">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="d7927-166">예를 들어 `UseCors`, `UseAuthentication` 및 `UseAuthorization`은 표시된 순서 대로 이동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-166">For example, `UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>

<span data-ttu-id="d7927-167">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-167">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="d7927-168">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="d7927-168">Exception/error handling</span></span>
   * <span data-ttu-id="d7927-169">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="d7927-169">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="d7927-170">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-170">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="d7927-171">데이터베이스 오류 페이지 미들웨어가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-171">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="d7927-172">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="d7927-172">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="d7927-173">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>)가 다음 미들웨어에서 throw된 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-173">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="d7927-174">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-174">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="d7927-175">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-175">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="d7927-176">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-176">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="d7927-177">쿠키 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-177">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="d7927-178">요청을 라우팅하도록 미들웨어(`UseRouting`) 라우팅.</span><span class="sxs-lookup"><span data-stu-id="d7927-178">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="d7927-179">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-179">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="d7927-180">권한 부여 미들웨어(`UseAuthorization`)는 사용자에게 보안 리소스에 액세스할 수 있는 권한을 부여합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-180">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="d7927-181">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-181">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="d7927-182">앱이 세션 상태를 사용하는 경우에는 쿠키 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="d7927-182">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="d7927-183">엔드포인트 라우팅 미들웨어(`MapRazorPages`를 포함하는 `UseEndpoints`)를 사용하여 요청 파이프라인에 Razor Pages 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-183">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="d7927-184">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-184">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="d7927-185"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-185"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="d7927-186">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-186">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="d7927-187">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-187">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="d7927-188">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="d7927-188">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="d7927-189">*wwwroot*에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-189">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="d7927-190">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7927-190">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="d7927-191">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-191">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="d7927-192">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-192">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="d7927-193">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-193">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="d7927-194">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-194">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="d7927-195">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-195">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="d7927-196">Razor Pages 응답을 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-196">The Razor Pages responses can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="d7927-197">SPA(단일 페이지 애플리케이션)의 경우 SPA 미들웨어 <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*>는 일반적으로 미들웨어 파이프라인에서 마지막으로 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-197">For Single Page Applications (SPAs), the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="d7927-198">SPA 미들웨어가 마지막으로 나오는 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-198">The SPA middleware comes last:</span></span>

* <span data-ttu-id="d7927-199">다른 모든 미들웨어가 일치하는 요청에 먼저 응답하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-199">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="d7927-200">클라이언트 쪽 라우팅이 있는 SPA가 서버 앱에서 인식할 수 없는 모든 경로에 대해 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-200">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="d7927-201">SPA에 대한 자세한 내용은 [React](xref:spa/react) 및 [Angular](xref:spa/angular) 프로젝트 템플릿 관련 가이드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7927-201">For more details on SPAs, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="d7927-202">미들웨어 파이프라인 분기</span><span class="sxs-lookup"><span data-stu-id="d7927-202">Branch the middleware pipeline</span></span>

<span data-ttu-id="d7927-203"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-203"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="d7927-204">`Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-204">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="d7927-205">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-205">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="d7927-206">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-206">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="d7927-207">요청</span><span class="sxs-lookup"><span data-stu-id="d7927-207">Request</span></span>             | <span data-ttu-id="d7927-208">응답</span><span class="sxs-lookup"><span data-stu-id="d7927-208">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="d7927-209">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="d7927-209">localhost:1234</span></span>      | <span data-ttu-id="d7927-210">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="d7927-210">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="d7927-211">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="d7927-211">localhost:1234/map1</span></span> | <span data-ttu-id="d7927-212">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="d7927-212">Map Test 1</span></span>                   |
| <span data-ttu-id="d7927-213">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="d7927-213">localhost:1234/map2</span></span> | <span data-ttu-id="d7927-214">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="d7927-214">Map Test 2</span></span>                   |
| <span data-ttu-id="d7927-215">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="d7927-215">localhost:1234/map3</span></span> | <span data-ttu-id="d7927-216">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="d7927-216">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="d7927-217">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-217">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="d7927-218">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-218">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="d7927-219">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-219">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="d7927-220"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-220"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="d7927-221">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-221">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="d7927-222">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-222">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="d7927-223">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-223">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="d7927-224">요청</span><span class="sxs-lookup"><span data-stu-id="d7927-224">Request</span></span>                       | <span data-ttu-id="d7927-225">응답</span><span class="sxs-lookup"><span data-stu-id="d7927-225">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="d7927-226">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="d7927-226">localhost:1234</span></span>                | <span data-ttu-id="d7927-227">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="d7927-227">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="d7927-228">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="d7927-228">localhost:1234/?branch=master</span></span> | <span data-ttu-id="d7927-229">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="d7927-229">Branch used = master</span></span>         |

<span data-ttu-id="d7927-230"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*>도 지정된 조건자의 결과를 기준으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-230"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="d7927-231">`MapWhen`과 달리, 이 분기는 단락을 수행하거나 터미널 미들웨어를 포함하지 않는 경우 기본 파이프라인에 다시 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-231">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it doesn't short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

<span data-ttu-id="d7927-232">위 예제에서는 “Hello from main pipeline.” 응답이</span><span class="sxs-lookup"><span data-stu-id="d7927-232">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="d7927-233">모든 요청에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-233">is written for all requests.</span></span> <span data-ttu-id="d7927-234">요청에 쿼리 문자열 변수 `branch`가 포함되어 있으면, 기본 파이프라인이 다시 연결되기 전에 변수 값이 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-234">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="d7927-235">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="d7927-235">Built-in middleware</span></span>

<span data-ttu-id="d7927-236">ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-236">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="d7927-237">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-237">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="d7927-238">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-238">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="d7927-239">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7927-239">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="d7927-240">미들웨어</span><span class="sxs-lookup"><span data-stu-id="d7927-240">Middleware</span></span> | <span data-ttu-id="d7927-241">설명</span><span class="sxs-lookup"><span data-stu-id="d7927-241">Description</span></span> | <span data-ttu-id="d7927-242">순서</span><span class="sxs-lookup"><span data-stu-id="d7927-242">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="d7927-243">인증</span><span class="sxs-lookup"><span data-stu-id="d7927-243">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="d7927-244">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-244">Provides authentication support.</span></span> | <span data-ttu-id="d7927-245">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="d7927-245">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="d7927-246">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="d7927-246">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="d7927-247">권한 부여</span><span class="sxs-lookup"><span data-stu-id="d7927-247">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*) | <span data-ttu-id="d7927-248">권한 부여 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-248">Provides authorization support.</span></span> | <span data-ttu-id="d7927-249">인증 미들웨어 바로 뒤에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-249">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="d7927-250">쿠키 정책</span><span class="sxs-lookup"><span data-stu-id="d7927-250">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="d7927-251">개인 정보 저장과 관련한 사용자의 동의를 추적하고 쿠키 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-251">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="d7927-252">쿠키를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="d7927-252">Before middleware that issues cookies.</span></span> <span data-ttu-id="d7927-253">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="d7927-253">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="d7927-254">CORS</span><span class="sxs-lookup"><span data-stu-id="d7927-254">CORS</span></span>](xref:security/cors) | <span data-ttu-id="d7927-255">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-255">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="d7927-256">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-256">Before components that use CORS.</span></span> |
| [<span data-ttu-id="d7927-257">진단</span><span class="sxs-lookup"><span data-stu-id="d7927-257">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="d7927-258">개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="d7927-258">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="d7927-259">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-259">Before components that generate errors.</span></span> <span data-ttu-id="d7927-260">예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-260">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="d7927-261">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="d7927-261">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="d7927-262">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-262">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="d7927-263">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="d7927-263">Before components that consume the updated fields.</span></span> <span data-ttu-id="d7927-264">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="d7927-264">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="d7927-265">상태 검사</span><span class="sxs-lookup"><span data-stu-id="d7927-265">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="d7927-266">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-266">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="d7927-267">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-267">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="d7927-268">헤더 전파</span><span class="sxs-lookup"><span data-stu-id="d7927-268">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="d7927-269">들어오는 요청에서 나가는 HTTP 클라이언트 요청으로 HTTP 헤더를 전파합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-269">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="d7927-270">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="d7927-270">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="d7927-271">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-271">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="d7927-272">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-272">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="d7927-273">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="d7927-273">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="d7927-274">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-274">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="d7927-275">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-275">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="d7927-276">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="d7927-276">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="d7927-277">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-277">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="d7927-278">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="d7927-278">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="d7927-279">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="d7927-279">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="d7927-280">MVC</span><span class="sxs-lookup"><span data-stu-id="d7927-280">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="d7927-281">MVC/Razor Pages를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-281">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="d7927-282">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-282">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="d7927-283">OWIN</span><span class="sxs-lookup"><span data-stu-id="d7927-283">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="d7927-284">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-284">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="d7927-285">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-285">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="d7927-286">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="d7927-286">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="d7927-287">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-287">Provides support for caching responses.</span></span> | <span data-ttu-id="d7927-288">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-288">Before components that require caching.</span></span> |
| [<span data-ttu-id="d7927-289">응답 압축</span><span class="sxs-lookup"><span data-stu-id="d7927-289">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="d7927-290">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-290">Provides support for compressing responses.</span></span> | <span data-ttu-id="d7927-291">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-291">Before components that require compression.</span></span> |
| [<span data-ttu-id="d7927-292">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="d7927-292">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="d7927-293">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-293">Provides localization support.</span></span> | <span data-ttu-id="d7927-294">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-294">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="d7927-295">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="d7927-295">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="d7927-296">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-296">Defines and constrains request routes.</span></span> | <span data-ttu-id="d7927-297">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="d7927-297">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="d7927-298">SPA</span><span class="sxs-lookup"><span data-stu-id="d7927-298">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa*) | <span data-ttu-id="d7927-299">SPA(단일 페이지 애플리케이션)의 기반 페이지를 반환하여 이 시점부터 미들웨어 체인의 모든 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-299">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="d7927-300">정적 파일, MVC 작업 등을 처리하는 다른 미들웨어가 우선할 수 있도록 이 미들웨어는 체인에서 늦게 배치되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-300">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="d7927-301">세션</span><span class="sxs-lookup"><span data-stu-id="d7927-301">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="d7927-302">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-302">Provides support for managing user sessions.</span></span> | <span data-ttu-id="d7927-303">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-303">Before components that require Session.</span></span> | 
| [<span data-ttu-id="d7927-304">정적 파일</span><span class="sxs-lookup"><span data-stu-id="d7927-304">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="d7927-305">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-305">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="d7927-306">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-306">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="d7927-307">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="d7927-307">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="d7927-308">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-308">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="d7927-309">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-309">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="d7927-310">WebSockets</span><span class="sxs-lookup"><span data-stu-id="d7927-310">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="d7927-311">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-311">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="d7927-312">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-312">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="d7927-313">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d7927-313">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d7927-314">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d7927-314">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d7927-315">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-315">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="d7927-316">각 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-316">Each component:</span></span>

* <span data-ttu-id="d7927-317">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-317">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="d7927-318">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-318">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="d7927-319">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-319">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="d7927-320">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-320">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="d7927-321">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-321">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="d7927-322">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-322">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="d7927-323">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어*라고 하며, *미들웨어 구성 요소*라고 부르기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-323">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="d7927-324">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-324">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="d7927-325">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-325">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="d7927-326"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-326"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="d7927-327">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="d7927-327">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="d7927-328">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-328">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="d7927-329">다음 다이어그램은 그 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-329">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="d7927-330">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-330">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="d7927-334">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-334">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="d7927-335">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-335">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="d7927-336">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-336">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="d7927-337">이 경우 실제 요청 파이프라인은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-337">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="d7927-338">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-338">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="d7927-339">첫 번째 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 대리자는 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-339">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="d7927-340"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-340">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="d7927-341">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-341">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="d7927-342">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-342">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="d7927-343">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-343">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="d7927-344">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-344">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="d7927-345">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-345">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="d7927-346">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어*로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-346">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="d7927-347">추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-347">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="d7927-348">그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7927-348">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="d7927-349">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="d7927-349">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="d7927-350">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-350">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="d7927-351">예를 들어 헤더 및 상태 코드를 설정하는 변경 작업은 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-351">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="d7927-352">`next`를 호출한 후 응답 본문에 작성할 경우:</span><span class="sxs-lookup"><span data-stu-id="d7927-352">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="d7927-353">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-353">May cause a protocol violation.</span></span> <span data-ttu-id="d7927-354">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-354">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="d7927-355">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-355">May corrupt the body format.</span></span> <span data-ttu-id="d7927-356">예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-356">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="d7927-357"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-357"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="d7927-358">미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="d7927-358">Middleware order</span></span>

<span data-ttu-id="d7927-359">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-359">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="d7927-360">순서는 보안, 성능 및 기능에 **중요**합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-360">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="d7927-361">다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 권장 순서에 따라 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-361">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="d7927-362">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="d7927-362">In the preceding code:</span></span>

* <span data-ttu-id="d7927-363">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-363">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="d7927-364">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-364">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="d7927-365">예를 들어 `UseCors` 및 `UseAuthentication`은 표시된 순서대로 이동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-365">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="d7927-366">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-366">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="d7927-367">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="d7927-367">Exception/error handling</span></span>
   * <span data-ttu-id="d7927-368">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="d7927-368">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="d7927-369">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-369">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="d7927-370">데이터베이스 오류 페이지 미들웨어(`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`)가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-370">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="d7927-371">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="d7927-371">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="d7927-372">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>)가 다음 미들웨어에서 throw된 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-372">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="d7927-373">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-373">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="d7927-374">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-374">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="d7927-375">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-375">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="d7927-376">쿠키 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-376">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="d7927-377">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-377">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="d7927-378">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-378">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="d7927-379">앱이 세션 상태를 사용하는 경우에는 쿠키 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="d7927-379">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="d7927-380">MVC(<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>)는 MVC를 요청 파이프라인에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-380">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

<span data-ttu-id="d7927-381">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-381">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="d7927-382"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-382"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="d7927-383">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-383">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="d7927-384">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-384">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="d7927-385">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="d7927-385">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="d7927-386">*wwwroot*에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-386">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="d7927-387">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7927-387">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="d7927-388">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-388">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="d7927-389">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-389">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="d7927-390">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-390">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="d7927-391">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-391">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="d7927-392">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-392">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="d7927-393"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*>의 MVC 응답은 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-393">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="d7927-394">Use, Run 및 Map</span><span class="sxs-lookup"><span data-stu-id="d7927-394">Use, Run, and Map</span></span>

<span data-ttu-id="d7927-395"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 및 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>을 사용하여 HTTP 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-395">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="d7927-396">`Use` 메서드는 파이프라인을 단락(short-circuit)할 수 있습니다(즉, `next` 요청 대리자를 호출하지 않는 경우).</span><span class="sxs-lookup"><span data-stu-id="d7927-396">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="d7927-397">`Run`은 규칙이며 일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-397">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="d7927-398"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-398"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="d7927-399">`Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-399">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="d7927-400">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-400">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="d7927-401">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-401">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="d7927-402">요청</span><span class="sxs-lookup"><span data-stu-id="d7927-402">Request</span></span>             | <span data-ttu-id="d7927-403">응답</span><span class="sxs-lookup"><span data-stu-id="d7927-403">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="d7927-404">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="d7927-404">localhost:1234</span></span>      | <span data-ttu-id="d7927-405">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="d7927-405">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="d7927-406">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="d7927-406">localhost:1234/map1</span></span> | <span data-ttu-id="d7927-407">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="d7927-407">Map Test 1</span></span>                   |
| <span data-ttu-id="d7927-408">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="d7927-408">localhost:1234/map2</span></span> | <span data-ttu-id="d7927-409">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="d7927-409">Map Test 2</span></span>                   |
| <span data-ttu-id="d7927-410">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="d7927-410">localhost:1234/map3</span></span> | <span data-ttu-id="d7927-411">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="d7927-411">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="d7927-412">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-412">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="d7927-413"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-413"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="d7927-414">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-414">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="d7927-415">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-415">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="d7927-416">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-416">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="d7927-417">요청</span><span class="sxs-lookup"><span data-stu-id="d7927-417">Request</span></span>                       | <span data-ttu-id="d7927-418">응답</span><span class="sxs-lookup"><span data-stu-id="d7927-418">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="d7927-419">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="d7927-419">localhost:1234</span></span>                | <span data-ttu-id="d7927-420">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="d7927-420">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="d7927-421">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="d7927-421">localhost:1234/?branch=master</span></span> | <span data-ttu-id="d7927-422">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="d7927-422">Branch used = master</span></span>         |

<span data-ttu-id="d7927-423">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-423">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="d7927-424">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-424">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="d7927-425">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="d7927-425">Built-in middleware</span></span>

<span data-ttu-id="d7927-426">ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-426">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="d7927-427">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-427">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="d7927-428">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-428">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="d7927-429">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7927-429">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="d7927-430">미들웨어</span><span class="sxs-lookup"><span data-stu-id="d7927-430">Middleware</span></span> | <span data-ttu-id="d7927-431">설명</span><span class="sxs-lookup"><span data-stu-id="d7927-431">Description</span></span> | <span data-ttu-id="d7927-432">순서</span><span class="sxs-lookup"><span data-stu-id="d7927-432">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="d7927-433">인증</span><span class="sxs-lookup"><span data-stu-id="d7927-433">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="d7927-434">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-434">Provides authentication support.</span></span> | <span data-ttu-id="d7927-435">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="d7927-435">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="d7927-436">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="d7927-436">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="d7927-437">쿠키 정책</span><span class="sxs-lookup"><span data-stu-id="d7927-437">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="d7927-438">개인 정보 저장과 관련한 사용자의 동의를 추적하고 쿠키 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-438">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="d7927-439">쿠키를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="d7927-439">Before middleware that issues cookies.</span></span> <span data-ttu-id="d7927-440">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="d7927-440">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="d7927-441">CORS</span><span class="sxs-lookup"><span data-stu-id="d7927-441">CORS</span></span>](xref:security/cors) | <span data-ttu-id="d7927-442">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-442">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="d7927-443">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-443">Before components that use CORS.</span></span> |
| [<span data-ttu-id="d7927-444">진단</span><span class="sxs-lookup"><span data-stu-id="d7927-444">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="d7927-445">개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="d7927-445">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="d7927-446">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-446">Before components that generate errors.</span></span> <span data-ttu-id="d7927-447">예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-447">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="d7927-448">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="d7927-448">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="d7927-449">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-449">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="d7927-450">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="d7927-450">Before components that consume the updated fields.</span></span> <span data-ttu-id="d7927-451">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="d7927-451">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="d7927-452">상태 검사</span><span class="sxs-lookup"><span data-stu-id="d7927-452">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="d7927-453">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-453">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="d7927-454">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-454">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="d7927-455">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="d7927-455">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="d7927-456">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-456">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="d7927-457">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-457">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="d7927-458">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="d7927-458">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="d7927-459">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-459">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="d7927-460">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-460">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="d7927-461">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="d7927-461">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="d7927-462">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-462">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="d7927-463">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="d7927-463">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="d7927-464">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="d7927-464">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="d7927-465">MVC</span><span class="sxs-lookup"><span data-stu-id="d7927-465">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="d7927-466">MVC/Razor Pages를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-466">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="d7927-467">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-467">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="d7927-468">OWIN</span><span class="sxs-lookup"><span data-stu-id="d7927-468">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="d7927-469">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-469">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="d7927-470">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-470">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="d7927-471">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="d7927-471">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="d7927-472">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-472">Provides support for caching responses.</span></span> | <span data-ttu-id="d7927-473">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-473">Before components that require caching.</span></span> |
| [<span data-ttu-id="d7927-474">응답 압축</span><span class="sxs-lookup"><span data-stu-id="d7927-474">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="d7927-475">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-475">Provides support for compressing responses.</span></span> | <span data-ttu-id="d7927-476">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-476">Before components that require compression.</span></span> |
| [<span data-ttu-id="d7927-477">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="d7927-477">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="d7927-478">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-478">Provides localization support.</span></span> | <span data-ttu-id="d7927-479">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-479">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="d7927-480">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="d7927-480">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="d7927-481">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-481">Defines and constrains request routes.</span></span> | <span data-ttu-id="d7927-482">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="d7927-482">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="d7927-483">세션</span><span class="sxs-lookup"><span data-stu-id="d7927-483">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="d7927-484">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-484">Provides support for managing user sessions.</span></span> | <span data-ttu-id="d7927-485">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-485">Before components that require Session.</span></span> |
| [<span data-ttu-id="d7927-486">정적 파일</span><span class="sxs-lookup"><span data-stu-id="d7927-486">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="d7927-487">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-487">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="d7927-488">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-488">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="d7927-489">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="d7927-489">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="d7927-490">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-490">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="d7927-491">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-491">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="d7927-492">WebSockets</span><span class="sxs-lookup"><span data-stu-id="d7927-492">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="d7927-493">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="d7927-493">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="d7927-494">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="d7927-494">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="d7927-495">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d7927-495">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
