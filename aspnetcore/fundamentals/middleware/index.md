---
title: ASP.NET Core 미들웨어 기본 사항
author: rick-anderson
description: ASP.NET Core 미들웨어 및 요청 파이프라인에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
uid: fundamentals/middleware/index
ms.openlocfilehash: 5c8e9e58ab222e482ef029f5099d0a8acd07d8a6
ms.sourcegitcommit: 990a4c2e623c202a27f60bdf3902f250359c13be
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2020
ms.locfileid: "76972030"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="192b6-103">ASP.NET Core 미들웨어</span><span class="sxs-lookup"><span data-stu-id="192b6-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="192b6-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="192b6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="192b6-105">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="192b6-106">각 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-106">Each component:</span></span>

* <span data-ttu-id="192b6-107">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="192b6-108">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="192b6-109">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="192b6-110">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="192b6-111">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="192b6-112">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="192b6-113">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어*라고 하며, *미들웨어 구성 요소*라고 부르기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="192b6-114">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="192b6-115">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="192b6-116"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="192b6-117">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="192b6-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="192b6-118">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="192b6-119">다음 다이어그램은 그 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="192b6-120">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-120">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="192b6-124">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="192b6-125">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="192b6-126">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="192b6-127">이 경우 실제 요청 파이프라인은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="192b6-128">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="192b6-129"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="192b6-130">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="192b6-131">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="192b6-132">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="192b6-133">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="192b6-134">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="192b6-135">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어*로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="192b6-136">추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="192b6-137">그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="192b6-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="192b6-138">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="192b6-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="192b6-139">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="192b6-140">예를 들어 헤더 및 상태 코드를 설정하는 변경 작업은 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-140">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="192b6-141">`next`를 호출한 후 응답 본문에 작성할 경우:</span><span class="sxs-lookup"><span data-stu-id="192b6-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="192b6-142">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-142">May cause a protocol violation.</span></span> <span data-ttu-id="192b6-143">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="192b6-144">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-144">May corrupt the body format.</span></span> <span data-ttu-id="192b6-145">예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="192b6-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="192b6-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 대리자는 `next` 매개 변수를 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="192b6-148">첫 번째 `Run` 대리자는 항상 터미널이며 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="192b6-149">`Run`이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-149">`Run` is a convention.</span></span> <span data-ttu-id="192b6-150">일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]

<span data-ttu-id="192b6-151">위 예제에서 `Run` 대리자는 응답에 `"Hello from 2nd delegate."`를 쓴 다음 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="192b6-152">`Run` 대리자 뒤에 추가된 다른 `Use` 또는 `Run` 대리자는 호출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="192b6-153">미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="192b6-153">Middleware order</span></span>

<span data-ttu-id="192b6-154">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-154">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="192b6-155">순서는 보안, 성능 및 기능에 **중요**합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-155">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="192b6-156">다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 권장 순서에 따라 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-156">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="192b6-157">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="192b6-157">In the preceding code:</span></span>

* <span data-ttu-id="192b6-158">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-158">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="192b6-159">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-159">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="192b6-160">예를 들어 `UseCors`, `UseAuthentication` 및 `UseAuthorization`은 표시된 순서 대로 이동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-160">For example, `UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>

<span data-ttu-id="192b6-161">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-161">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="192b6-162">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="192b6-162">Exception/error handling</span></span>
   * <span data-ttu-id="192b6-163">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="192b6-163">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="192b6-164">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-164">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="192b6-165">데이터베이스 오류 페이지 미들웨어가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-165">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="192b6-166">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="192b6-166">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="192b6-167">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>)가 다음 미들웨어에서 던져진 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-167">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="192b6-168">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-168">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="192b6-169">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-169">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="192b6-170">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-170">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="192b6-171">쿠키 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-171">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="192b6-172">요청을 라우팅하도록 미들웨어(`UseRouting`) 라우팅.</span><span class="sxs-lookup"><span data-stu-id="192b6-172">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="192b6-173">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-173">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="192b6-174">권한 부여 미들웨어(`UseAuthorization`)는 사용자에게 보안 리소스에 액세스할 수 있는 권한을 부여합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-174">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="192b6-175">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-175">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="192b6-176">앱이 세션 상태를 사용하는 경우에는 쿠키 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="192b6-176">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="192b6-177">엔드포인트 라우팅 미들웨어(`MapRazorPages`를 포함하는 `UseEndpoints`)를 사용하여 요청 파이프라인에 Razor Pages 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-177">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="192b6-178">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-178">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="192b6-179"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-179"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="192b6-180">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-180">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="192b6-181">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-181">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="192b6-182">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="192b6-182">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="192b6-183">*wwwroot*에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-183">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="192b6-184">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="192b6-184">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="192b6-185">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-185">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="192b6-186">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-186">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="192b6-187">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-187">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="192b6-188">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-188">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="192b6-189">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-189">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="192b6-190">Razor Pages 응답을 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-190">The Razor Pages responses can be compressed.</span></span>

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

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="192b6-191">미들웨어 파이프라인 분기</span><span class="sxs-lookup"><span data-stu-id="192b6-191">Branch the middleware pipeline</span></span>

<span data-ttu-id="192b6-192"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-192"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="192b6-193">`Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-193">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="192b6-194">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-194">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="192b6-195">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-195">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="192b6-196">요청</span><span class="sxs-lookup"><span data-stu-id="192b6-196">Request</span></span>             | <span data-ttu-id="192b6-197">응답</span><span class="sxs-lookup"><span data-stu-id="192b6-197">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="192b6-198">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="192b6-198">localhost:1234</span></span>      | <span data-ttu-id="192b6-199">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="192b6-199">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="192b6-200">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="192b6-200">localhost:1234/map1</span></span> | <span data-ttu-id="192b6-201">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="192b6-201">Map Test 1</span></span>                   |
| <span data-ttu-id="192b6-202">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="192b6-202">localhost:1234/map2</span></span> | <span data-ttu-id="192b6-203">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="192b6-203">Map Test 2</span></span>                   |
| <span data-ttu-id="192b6-204">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="192b6-204">localhost:1234/map3</span></span> | <span data-ttu-id="192b6-205">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="192b6-205">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="192b6-206">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-206">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="192b6-207">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-207">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="192b6-208">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-208">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="192b6-209"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-209"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="192b6-210">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-210">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="192b6-211">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-211">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="192b6-212">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-212">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="192b6-213">요청</span><span class="sxs-lookup"><span data-stu-id="192b6-213">Request</span></span>                       | <span data-ttu-id="192b6-214">응답</span><span class="sxs-lookup"><span data-stu-id="192b6-214">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="192b6-215">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="192b6-215">localhost:1234</span></span>                | <span data-ttu-id="192b6-216">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="192b6-216">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="192b6-217">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="192b6-217">localhost:1234/?branch=master</span></span> | <span data-ttu-id="192b6-218">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="192b6-218">Branch used = master</span></span>         |

<span data-ttu-id="192b6-219"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*>도 지정된 조건자의 결과를 기준으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-219"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="192b6-220">`MapWhen`과 달리, 이 분기는 단락을 수행하거나 터미널 미들웨어를 포함하는 경우 기본 파이프라인에 다시 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-220">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it does short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=23-24)]

<span data-ttu-id="192b6-221">위 예제에서는 “Hello from main pipeline.” 응답이</span><span class="sxs-lookup"><span data-stu-id="192b6-221">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="192b6-222">모든 요청에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-222">is written for all requests.</span></span> <span data-ttu-id="192b6-223">요청에 쿼리 문자열 변수 `branch`가 포함되어 있으면, 기본 파이프라인이 다시 연결되기 전에 변수 값이 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-223">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="192b6-224">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="192b6-224">Built-in middleware</span></span>

<span data-ttu-id="192b6-225">ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-225">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="192b6-226">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-226">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="192b6-227">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-227">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="192b6-228">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="192b6-228">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="192b6-229">미들웨어</span><span class="sxs-lookup"><span data-stu-id="192b6-229">Middleware</span></span> | <span data-ttu-id="192b6-230">설명</span><span class="sxs-lookup"><span data-stu-id="192b6-230">Description</span></span> | <span data-ttu-id="192b6-231">순서</span><span class="sxs-lookup"><span data-stu-id="192b6-231">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="192b6-232">인증</span><span class="sxs-lookup"><span data-stu-id="192b6-232">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="192b6-233">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-233">Provides authentication support.</span></span> | <span data-ttu-id="192b6-234">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="192b6-234">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="192b6-235">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="192b6-235">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="192b6-236">권한 부여</span><span class="sxs-lookup"><span data-stu-id="192b6-236">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*) | <span data-ttu-id="192b6-237">권한 부여 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-237">Provides authorization support.</span></span> | <span data-ttu-id="192b6-238">인증 미들웨어 바로 뒤에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-238">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="192b6-239">쿠키 정책</span><span class="sxs-lookup"><span data-stu-id="192b6-239">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="192b6-240">개인 정보 저장과 관련한 사용자의 동의를 추적하고 쿠키 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-240">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="192b6-241">쿠키를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="192b6-241">Before middleware that issues cookies.</span></span> <span data-ttu-id="192b6-242">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="192b6-242">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="192b6-243">CORS</span><span class="sxs-lookup"><span data-stu-id="192b6-243">CORS</span></span>](xref:security/cors) | <span data-ttu-id="192b6-244">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-244">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="192b6-245">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-245">Before components that use CORS.</span></span> |
| [<span data-ttu-id="192b6-246">진단</span><span class="sxs-lookup"><span data-stu-id="192b6-246">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="192b6-247">개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="192b6-247">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="192b6-248">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-248">Before components that generate errors.</span></span> <span data-ttu-id="192b6-249">예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-249">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="192b6-250">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="192b6-250">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="192b6-251">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-251">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="192b6-252">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="192b6-252">Before components that consume the updated fields.</span></span> <span data-ttu-id="192b6-253">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="192b6-253">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="192b6-254">상태 검사</span><span class="sxs-lookup"><span data-stu-id="192b6-254">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="192b6-255">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-255">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="192b6-256">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-256">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="192b6-257">헤더 전파</span><span class="sxs-lookup"><span data-stu-id="192b6-257">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="192b6-258">들어오는 요청에서 나가는 HTTP 클라이언트 요청으로 HTTP 헤더를 전파합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-258">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="192b6-259">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="192b6-259">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="192b6-260">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-260">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="192b6-261">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-261">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="192b6-262">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="192b6-262">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="192b6-263">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-263">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="192b6-264">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-264">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="192b6-265">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="192b6-265">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="192b6-266">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-266">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="192b6-267">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="192b6-267">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="192b6-268">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="192b6-268">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="192b6-269">MVC</span><span class="sxs-lookup"><span data-stu-id="192b6-269">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="192b6-270">MVC/Razor Pages를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-270">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="192b6-271">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-271">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="192b6-272">OWIN</span><span class="sxs-lookup"><span data-stu-id="192b6-272">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="192b6-273">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-273">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="192b6-274">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-274">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="192b6-275">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="192b6-275">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="192b6-276">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-276">Provides support for caching responses.</span></span> | <span data-ttu-id="192b6-277">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-277">Before components that require caching.</span></span> |
| [<span data-ttu-id="192b6-278">응답 압축</span><span class="sxs-lookup"><span data-stu-id="192b6-278">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="192b6-279">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-279">Provides support for compressing responses.</span></span> | <span data-ttu-id="192b6-280">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-280">Before components that require compression.</span></span> |
| [<span data-ttu-id="192b6-281">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="192b6-281">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="192b6-282">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-282">Provides localization support.</span></span> | <span data-ttu-id="192b6-283">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-283">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="192b6-284">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="192b6-284">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="192b6-285">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-285">Defines and constrains request routes.</span></span> | <span data-ttu-id="192b6-286">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="192b6-286">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="192b6-287">세션</span><span class="sxs-lookup"><span data-stu-id="192b6-287">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="192b6-288">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-288">Provides support for managing user sessions.</span></span> | <span data-ttu-id="192b6-289">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-289">Before components that require Session.</span></span> |
| [<span data-ttu-id="192b6-290">정적 파일</span><span class="sxs-lookup"><span data-stu-id="192b6-290">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="192b6-291">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-291">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="192b6-292">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-292">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="192b6-293">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="192b6-293">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="192b6-294">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-294">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="192b6-295">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-295">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="192b6-296">WebSockets</span><span class="sxs-lookup"><span data-stu-id="192b6-296">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="192b6-297">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-297">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="192b6-298">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-298">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="192b6-299">추가 자료</span><span class="sxs-lookup"><span data-stu-id="192b6-299">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="192b6-300">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="192b6-300">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="192b6-301">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-301">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="192b6-302">각 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-302">Each component:</span></span>

* <span data-ttu-id="192b6-303">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-303">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="192b6-304">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-304">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="192b6-305">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-305">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="192b6-306">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-306">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="192b6-307">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-307">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="192b6-308">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-308">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="192b6-309">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어*라고 하며, *미들웨어 구성 요소*라고 부르기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-309">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="192b6-310">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-310">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="192b6-311">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-311">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="192b6-312"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-312"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="192b6-313">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="192b6-313">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="192b6-314">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-314">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="192b6-315">다음 다이어그램은 그 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-315">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="192b6-316">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-316">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="192b6-320">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-320">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="192b6-321">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-321">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="192b6-322">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-322">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="192b6-323">이 경우 실제 요청 파이프라인은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-323">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="192b6-324">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-324">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="192b6-325">첫 번째 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 대리자는 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-325">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="192b6-326"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-326">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="192b6-327">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-327">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="192b6-328">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-328">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="192b6-329">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-329">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="192b6-330">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-330">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="192b6-331">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-331">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="192b6-332">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어*로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-332">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="192b6-333">추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-333">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="192b6-334">그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="192b6-334">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="192b6-335">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="192b6-335">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="192b6-336">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-336">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="192b6-337">예를 들어 헤더 및 상태 코드를 설정하는 변경 작업은 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-337">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="192b6-338">`next`를 호출한 후 응답 본문에 작성할 경우:</span><span class="sxs-lookup"><span data-stu-id="192b6-338">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="192b6-339">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-339">May cause a protocol violation.</span></span> <span data-ttu-id="192b6-340">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-340">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="192b6-341">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-341">May corrupt the body format.</span></span> <span data-ttu-id="192b6-342">예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-342">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="192b6-343"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-343"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="192b6-344">미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="192b6-344">Middleware order</span></span>

<span data-ttu-id="192b6-345">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-345">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="192b6-346">순서는 보안, 성능 및 기능에 **중요**합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-346">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="192b6-347">다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 권장 순서에 따라 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-347">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="192b6-348">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="192b6-348">In the preceding code:</span></span>

* <span data-ttu-id="192b6-349">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-349">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="192b6-350">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-350">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="192b6-351">예를 들어 `UseCors` 및 `UseAuthentication`은 표시된 순서대로 이동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-351">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="192b6-352">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-352">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="192b6-353">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="192b6-353">Exception/error handling</span></span>
   * <span data-ttu-id="192b6-354">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="192b6-354">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="192b6-355">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-355">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="192b6-356">데이터베이스 오류 페이지 미들웨어(`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`)가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-356">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="192b6-357">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="192b6-357">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="192b6-358">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>)가 다음 미들웨어에서 던져진 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-358">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="192b6-359">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-359">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="192b6-360">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-360">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="192b6-361">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-361">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="192b6-362">쿠키 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-362">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="192b6-363">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-363">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="192b6-364">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-364">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="192b6-365">앱이 세션 상태를 사용하는 경우에는 쿠키 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="192b6-365">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="192b6-366">MVC(<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>)는 MVC를 요청 파이프라인에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-366">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="192b6-367">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-367">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="192b6-368"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-368"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="192b6-369">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-369">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="192b6-370">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-370">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="192b6-371">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="192b6-371">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="192b6-372">*wwwroot*에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-372">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="192b6-373">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="192b6-373">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="192b6-374">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-374">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="192b6-375">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-375">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="192b6-376">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-376">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="192b6-377">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-377">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="192b6-378">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-378">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="192b6-379"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*>의 MVC 응답은 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-379">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="192b6-380">Use, Run 및 Map</span><span class="sxs-lookup"><span data-stu-id="192b6-380">Use, Run, and Map</span></span>

<span data-ttu-id="192b6-381"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 및 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>을 사용하여 HTTP 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-381">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="192b6-382">`Use` 메서드는 파이프라인을 단락(short-circuit)할 수 있습니다(즉, `next` 요청 대리자를 호출하지 않는 경우).</span><span class="sxs-lookup"><span data-stu-id="192b6-382">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="192b6-383">`Run`은 규칙이며 일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-383">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="192b6-384"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-384"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="192b6-385">`Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-385">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="192b6-386">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-386">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="192b6-387">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-387">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="192b6-388">요청</span><span class="sxs-lookup"><span data-stu-id="192b6-388">Request</span></span>             | <span data-ttu-id="192b6-389">응답</span><span class="sxs-lookup"><span data-stu-id="192b6-389">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="192b6-390">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="192b6-390">localhost:1234</span></span>      | <span data-ttu-id="192b6-391">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="192b6-391">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="192b6-392">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="192b6-392">localhost:1234/map1</span></span> | <span data-ttu-id="192b6-393">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="192b6-393">Map Test 1</span></span>                   |
| <span data-ttu-id="192b6-394">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="192b6-394">localhost:1234/map2</span></span> | <span data-ttu-id="192b6-395">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="192b6-395">Map Test 2</span></span>                   |
| <span data-ttu-id="192b6-396">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="192b6-396">localhost:1234/map3</span></span> | <span data-ttu-id="192b6-397">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="192b6-397">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="192b6-398">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-398">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="192b6-399"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-399"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="192b6-400">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-400">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="192b6-401">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-401">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="192b6-402">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-402">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="192b6-403">요청</span><span class="sxs-lookup"><span data-stu-id="192b6-403">Request</span></span>                       | <span data-ttu-id="192b6-404">응답</span><span class="sxs-lookup"><span data-stu-id="192b6-404">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="192b6-405">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="192b6-405">localhost:1234</span></span>                | <span data-ttu-id="192b6-406">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="192b6-406">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="192b6-407">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="192b6-407">localhost:1234/?branch=master</span></span> | <span data-ttu-id="192b6-408">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="192b6-408">Branch used = master</span></span>         |

<span data-ttu-id="192b6-409">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-409">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="192b6-410">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-410">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="192b6-411">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="192b6-411">Built-in middleware</span></span>

<span data-ttu-id="192b6-412">ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-412">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="192b6-413">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-413">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="192b6-414">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-414">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="192b6-415">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="192b6-415">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="192b6-416">미들웨어</span><span class="sxs-lookup"><span data-stu-id="192b6-416">Middleware</span></span> | <span data-ttu-id="192b6-417">설명</span><span class="sxs-lookup"><span data-stu-id="192b6-417">Description</span></span> | <span data-ttu-id="192b6-418">순서</span><span class="sxs-lookup"><span data-stu-id="192b6-418">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="192b6-419">인증</span><span class="sxs-lookup"><span data-stu-id="192b6-419">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="192b6-420">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-420">Provides authentication support.</span></span> | <span data-ttu-id="192b6-421">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="192b6-421">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="192b6-422">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="192b6-422">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="192b6-423">쿠키 정책</span><span class="sxs-lookup"><span data-stu-id="192b6-423">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="192b6-424">개인 정보 저장과 관련한 사용자의 동의를 추적하고 쿠키 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-424">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="192b6-425">쿠키를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="192b6-425">Before middleware that issues cookies.</span></span> <span data-ttu-id="192b6-426">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="192b6-426">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="192b6-427">CORS</span><span class="sxs-lookup"><span data-stu-id="192b6-427">CORS</span></span>](xref:security/cors) | <span data-ttu-id="192b6-428">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-428">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="192b6-429">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-429">Before components that use CORS.</span></span> |
| [<span data-ttu-id="192b6-430">진단</span><span class="sxs-lookup"><span data-stu-id="192b6-430">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="192b6-431">개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="192b6-431">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="192b6-432">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-432">Before components that generate errors.</span></span> <span data-ttu-id="192b6-433">예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-433">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="192b6-434">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="192b6-434">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="192b6-435">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-435">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="192b6-436">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="192b6-436">Before components that consume the updated fields.</span></span> <span data-ttu-id="192b6-437">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="192b6-437">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="192b6-438">상태 검사</span><span class="sxs-lookup"><span data-stu-id="192b6-438">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="192b6-439">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-439">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="192b6-440">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-440">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="192b6-441">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="192b6-441">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="192b6-442">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-442">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="192b6-443">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-443">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="192b6-444">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="192b6-444">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="192b6-445">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-445">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="192b6-446">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-446">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="192b6-447">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="192b6-447">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="192b6-448">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-448">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="192b6-449">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="192b6-449">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="192b6-450">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="192b6-450">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="192b6-451">MVC</span><span class="sxs-lookup"><span data-stu-id="192b6-451">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="192b6-452">MVC/Razor Pages를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-452">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="192b6-453">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-453">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="192b6-454">OWIN</span><span class="sxs-lookup"><span data-stu-id="192b6-454">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="192b6-455">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-455">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="192b6-456">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-456">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="192b6-457">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="192b6-457">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="192b6-458">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-458">Provides support for caching responses.</span></span> | <span data-ttu-id="192b6-459">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-459">Before components that require caching.</span></span> |
| [<span data-ttu-id="192b6-460">응답 압축</span><span class="sxs-lookup"><span data-stu-id="192b6-460">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="192b6-461">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-461">Provides support for compressing responses.</span></span> | <span data-ttu-id="192b6-462">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-462">Before components that require compression.</span></span> |
| [<span data-ttu-id="192b6-463">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="192b6-463">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="192b6-464">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-464">Provides localization support.</span></span> | <span data-ttu-id="192b6-465">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-465">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="192b6-466">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="192b6-466">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="192b6-467">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-467">Defines and constrains request routes.</span></span> | <span data-ttu-id="192b6-468">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="192b6-468">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="192b6-469">세션</span><span class="sxs-lookup"><span data-stu-id="192b6-469">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="192b6-470">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-470">Provides support for managing user sessions.</span></span> | <span data-ttu-id="192b6-471">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-471">Before components that require Session.</span></span> |
| [<span data-ttu-id="192b6-472">정적 파일</span><span class="sxs-lookup"><span data-stu-id="192b6-472">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="192b6-473">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-473">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="192b6-474">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-474">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="192b6-475">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="192b6-475">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="192b6-476">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-476">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="192b6-477">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-477">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="192b6-478">WebSockets</span><span class="sxs-lookup"><span data-stu-id="192b6-478">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="192b6-479">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="192b6-479">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="192b6-480">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="192b6-480">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="192b6-481">추가 자료</span><span class="sxs-lookup"><span data-stu-id="192b6-481">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
