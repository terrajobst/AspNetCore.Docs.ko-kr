---
title: ASP.NET Core 미들웨어 기본 사항
author: rick-anderson
description: ASP.NET Core 미들웨어 및 요청 파이프라인에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
uid: fundamentals/middleware/index
ms.openlocfilehash: 47f465c00138acf434c6ec59f757e37361ad97db
ms.sourcegitcommit: 0e21d4f8111743bcb205a2ae0f8e57910c3e8c25
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77034106"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="3aea8-103">ASP.NET Core 미들웨어</span><span class="sxs-lookup"><span data-stu-id="3aea8-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3aea8-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3aea8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3aea8-105">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="3aea8-106">각 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-106">Each component:</span></span>

* <span data-ttu-id="3aea8-107">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="3aea8-108">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="3aea8-109">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="3aea8-110">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="3aea8-111">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="3aea8-112">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="3aea8-113">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어*라고 하며, *미들웨어 구성 요소*라고 부르기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="3aea8-114">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="3aea8-115">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="3aea8-116"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="3aea8-117">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="3aea8-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="3aea8-118">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="3aea8-119">다음 다이어그램은 그 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="3aea8-120">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-120">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="3aea8-124">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="3aea8-125">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="3aea8-126">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="3aea8-127">이 경우 실제 요청 파이프라인은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="3aea8-128">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="3aea8-129"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="3aea8-130">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="3aea8-131">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="3aea8-132">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="3aea8-133">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="3aea8-134">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="3aea8-135">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어*로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="3aea8-136">추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="3aea8-137">그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3aea8-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="3aea8-138">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="3aea8-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="3aea8-139">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="3aea8-140">예를 들어 헤더 및 상태 코드를 설정하는 변경 작업은 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-140">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="3aea8-141">`next`를 호출한 후 응답 본문에 작성할 경우:</span><span class="sxs-lookup"><span data-stu-id="3aea8-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="3aea8-142">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-142">May cause a protocol violation.</span></span> <span data-ttu-id="3aea8-143">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="3aea8-144">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-144">May corrupt the body format.</span></span> <span data-ttu-id="3aea8-145">예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="3aea8-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="3aea8-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 대리자는 `next` 매개 변수를 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="3aea8-148">첫 번째 `Run` 대리자는 항상 터미널이며 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="3aea8-149">`Run`이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-149">`Run` is a convention.</span></span> <span data-ttu-id="3aea8-150">일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]

<span data-ttu-id="3aea8-151">위 예제에서 `Run` 대리자는 응답에 `"Hello from 2nd delegate."`를 쓴 다음 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="3aea8-152">`Run` 대리자 뒤에 추가된 다른 `Use` 또는 `Run` 대리자는 호출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="3aea8-153">미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="3aea8-153">Middleware order</span></span>

<span data-ttu-id="3aea8-154">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-154">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="3aea8-155">순서는 보안, 성능 및 기능에 **중요**합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-155">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="3aea8-156">다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 권장 순서에 따라 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-156">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="3aea8-157">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="3aea8-157">In the preceding code:</span></span>

* <span data-ttu-id="3aea8-158">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-158">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="3aea8-159">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-159">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="3aea8-160">예를 들어 `UseCors`, `UseAuthentication` 및 `UseAuthorization`은 표시된 순서 대로 이동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-160">For example, `UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>

<span data-ttu-id="3aea8-161">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-161">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="3aea8-162">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="3aea8-162">Exception/error handling</span></span>
   * <span data-ttu-id="3aea8-163">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="3aea8-163">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="3aea8-164">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-164">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="3aea8-165">데이터베이스 오류 페이지 미들웨어가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-165">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="3aea8-166">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="3aea8-166">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="3aea8-167">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>)가 다음 미들웨어에서 던져진 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-167">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="3aea8-168">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-168">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="3aea8-169">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-169">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="3aea8-170">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-170">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="3aea8-171">쿠키 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-171">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="3aea8-172">요청을 라우팅하도록 미들웨어(`UseRouting`) 라우팅.</span><span class="sxs-lookup"><span data-stu-id="3aea8-172">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="3aea8-173">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-173">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="3aea8-174">권한 부여 미들웨어(`UseAuthorization`)는 사용자에게 보안 리소스에 액세스할 수 있는 권한을 부여합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-174">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="3aea8-175">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-175">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="3aea8-176">앱이 세션 상태를 사용하는 경우에는 쿠키 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="3aea8-176">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="3aea8-177">엔드포인트 라우팅 미들웨어(`MapRazorPages`를 포함하는 `UseEndpoints`)를 사용하여 요청 파이프라인에 Razor Pages 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-177">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="3aea8-178">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-178">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="3aea8-179"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-179"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="3aea8-180">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-180">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="3aea8-181">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-181">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="3aea8-182">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="3aea8-182">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="3aea8-183">*wwwroot*에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-183">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="3aea8-184">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3aea8-184">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="3aea8-185">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-185">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="3aea8-186">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-186">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="3aea8-187">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-187">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="3aea8-188">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-188">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="3aea8-189">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-189">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="3aea8-190">Razor Pages 응답을 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-190">The Razor Pages responses can be compressed.</span></span>

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

<span data-ttu-id="3aea8-191">단일 페이지 애플리케이션의 경우 SPA 미들웨어 <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*>는 일반적으로 미들웨어 파이프라인에서 마지막으로 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-191">For single page applications, the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="3aea8-192">SPA 미들웨어가 마지막으로 나오는 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-192">The SPA middleware comes last:</span></span>

* <span data-ttu-id="3aea8-193">다른 모든 미들웨어가 일치하는 요청에 먼저 응답하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-193">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="3aea8-194">클라이언트 쪽 라우팅이 있는 SPA가 서버 앱에서 인식할 수 없는 모든 경로에 대해 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-194">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="3aea8-195">단일 페이지 애플리케이션에 대한 자세한 내용은 [React](xref:spa/react) 및 [Angular](xref: client-side/spa/angular) 프로젝트 템플릿 관련 가이드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3aea8-195">For more details on single page applications, see the guides for the [React](xref:spa/react) and [Angular](xref: client-side/spa/angular) project templates.</span></span>

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="3aea8-196">미들웨어 파이프라인 분기</span><span class="sxs-lookup"><span data-stu-id="3aea8-196">Branch the middleware pipeline</span></span>

<span data-ttu-id="3aea8-197"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-197"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="3aea8-198">`Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-198">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="3aea8-199">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-199">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="3aea8-200">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-200">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="3aea8-201">요청</span><span class="sxs-lookup"><span data-stu-id="3aea8-201">Request</span></span>             | <span data-ttu-id="3aea8-202">응답</span><span class="sxs-lookup"><span data-stu-id="3aea8-202">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="3aea8-203">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="3aea8-203">localhost:1234</span></span>      | <span data-ttu-id="3aea8-204">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="3aea8-204">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="3aea8-205">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="3aea8-205">localhost:1234/map1</span></span> | <span data-ttu-id="3aea8-206">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="3aea8-206">Map Test 1</span></span>                   |
| <span data-ttu-id="3aea8-207">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="3aea8-207">localhost:1234/map2</span></span> | <span data-ttu-id="3aea8-208">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="3aea8-208">Map Test 2</span></span>                   |
| <span data-ttu-id="3aea8-209">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="3aea8-209">localhost:1234/map3</span></span> | <span data-ttu-id="3aea8-210">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="3aea8-210">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="3aea8-211">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-211">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="3aea8-212">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-212">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="3aea8-213">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-213">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="3aea8-214"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-214"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="3aea8-215">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-215">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="3aea8-216">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-216">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="3aea8-217">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-217">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="3aea8-218">요청</span><span class="sxs-lookup"><span data-stu-id="3aea8-218">Request</span></span>                       | <span data-ttu-id="3aea8-219">응답</span><span class="sxs-lookup"><span data-stu-id="3aea8-219">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="3aea8-220">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="3aea8-220">localhost:1234</span></span>                | <span data-ttu-id="3aea8-221">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="3aea8-221">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="3aea8-222">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="3aea8-222">localhost:1234/?branch=master</span></span> | <span data-ttu-id="3aea8-223">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="3aea8-223">Branch used = master</span></span>         |

<span data-ttu-id="3aea8-224"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*>도 지정된 조건자의 결과를 기준으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-224"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="3aea8-225">`MapWhen`과 달리, 이 분기는 단락을 수행하거나 터미널 미들웨어를 포함하는 경우 기본 파이프라인에 다시 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-225">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it does short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=23-24)]

<span data-ttu-id="3aea8-226">위 예제에서는 “Hello from main pipeline.” 응답이</span><span class="sxs-lookup"><span data-stu-id="3aea8-226">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="3aea8-227">모든 요청에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-227">is written for all requests.</span></span> <span data-ttu-id="3aea8-228">요청에 쿼리 문자열 변수 `branch`가 포함되어 있으면, 기본 파이프라인이 다시 연결되기 전에 변수 값이 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-228">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="3aea8-229">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="3aea8-229">Built-in middleware</span></span>

<span data-ttu-id="3aea8-230">ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-230">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="3aea8-231">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-231">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="3aea8-232">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-232">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="3aea8-233">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3aea8-233">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="3aea8-234">미들웨어</span><span class="sxs-lookup"><span data-stu-id="3aea8-234">Middleware</span></span> | <span data-ttu-id="3aea8-235">설명</span><span class="sxs-lookup"><span data-stu-id="3aea8-235">Description</span></span> | <span data-ttu-id="3aea8-236">순서</span><span class="sxs-lookup"><span data-stu-id="3aea8-236">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="3aea8-237">인증</span><span class="sxs-lookup"><span data-stu-id="3aea8-237">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="3aea8-238">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-238">Provides authentication support.</span></span> | <span data-ttu-id="3aea8-239">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="3aea8-239">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="3aea8-240">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="3aea8-240">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="3aea8-241">권한 부여</span><span class="sxs-lookup"><span data-stu-id="3aea8-241">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*) | <span data-ttu-id="3aea8-242">권한 부여 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-242">Provides authorization support.</span></span> | <span data-ttu-id="3aea8-243">인증 미들웨어 바로 뒤에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-243">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="3aea8-244">쿠키 정책</span><span class="sxs-lookup"><span data-stu-id="3aea8-244">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="3aea8-245">개인 정보 저장과 관련한 사용자의 동의를 추적하고 쿠키 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-245">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="3aea8-246">쿠키를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="3aea8-246">Before middleware that issues cookies.</span></span> <span data-ttu-id="3aea8-247">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="3aea8-247">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="3aea8-248">CORS</span><span class="sxs-lookup"><span data-stu-id="3aea8-248">CORS</span></span>](xref:security/cors) | <span data-ttu-id="3aea8-249">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-249">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="3aea8-250">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-250">Before components that use CORS.</span></span> |
| [<span data-ttu-id="3aea8-251">진단</span><span class="sxs-lookup"><span data-stu-id="3aea8-251">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="3aea8-252">개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="3aea8-252">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="3aea8-253">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-253">Before components that generate errors.</span></span> <span data-ttu-id="3aea8-254">예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-254">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="3aea8-255">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="3aea8-255">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="3aea8-256">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-256">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="3aea8-257">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="3aea8-257">Before components that consume the updated fields.</span></span> <span data-ttu-id="3aea8-258">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="3aea8-258">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="3aea8-259">상태 검사</span><span class="sxs-lookup"><span data-stu-id="3aea8-259">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="3aea8-260">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-260">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="3aea8-261">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-261">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="3aea8-262">헤더 전파</span><span class="sxs-lookup"><span data-stu-id="3aea8-262">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="3aea8-263">들어오는 요청에서 나가는 HTTP 클라이언트 요청으로 HTTP 헤더를 전파합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-263">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="3aea8-264">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="3aea8-264">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="3aea8-265">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-265">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="3aea8-266">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-266">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="3aea8-267">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="3aea8-267">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="3aea8-268">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-268">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="3aea8-269">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-269">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="3aea8-270">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="3aea8-270">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="3aea8-271">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-271">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="3aea8-272">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="3aea8-272">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="3aea8-273">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="3aea8-273">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="3aea8-274">MVC</span><span class="sxs-lookup"><span data-stu-id="3aea8-274">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="3aea8-275">MVC/Razor Pages를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-275">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="3aea8-276">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-276">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="3aea8-277">OWIN</span><span class="sxs-lookup"><span data-stu-id="3aea8-277">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="3aea8-278">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-278">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="3aea8-279">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-279">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="3aea8-280">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="3aea8-280">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="3aea8-281">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-281">Provides support for caching responses.</span></span> | <span data-ttu-id="3aea8-282">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-282">Before components that require caching.</span></span> |
| [<span data-ttu-id="3aea8-283">응답 압축</span><span class="sxs-lookup"><span data-stu-id="3aea8-283">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="3aea8-284">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-284">Provides support for compressing responses.</span></span> | <span data-ttu-id="3aea8-285">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-285">Before components that require compression.</span></span> |
| [<span data-ttu-id="3aea8-286">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="3aea8-286">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="3aea8-287">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-287">Provides localization support.</span></span> | <span data-ttu-id="3aea8-288">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-288">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="3aea8-289">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="3aea8-289">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="3aea8-290">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-290">Defines and constrains request routes.</span></span> | <span data-ttu-id="3aea8-291">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="3aea8-291">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="3aea8-292">SPA</span><span class="sxs-lookup"><span data-stu-id="3aea8-292">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa*) | <span data-ttu-id="3aea8-293">SPA(단일 페이지 애플리케이션)의 기반 페이지를 반환하여 이 시점부터 미들웨어 체인의 모든 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-293">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="3aea8-294">정적 파일, MVC 작업 등을 처리하는 다른 미들웨어가 우선할 수 있도록 이 미들웨어는 체인에서 늦게 배치되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-294">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="3aea8-295">세션</span><span class="sxs-lookup"><span data-stu-id="3aea8-295">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="3aea8-296">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-296">Provides support for managing user sessions.</span></span> | <span data-ttu-id="3aea8-297">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-297">Before components that require Session.</span></span> | 
| [<span data-ttu-id="3aea8-298">정적 파일</span><span class="sxs-lookup"><span data-stu-id="3aea8-298">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="3aea8-299">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-299">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="3aea8-300">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-300">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="3aea8-301">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="3aea8-301">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="3aea8-302">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-302">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="3aea8-303">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-303">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="3aea8-304">WebSockets</span><span class="sxs-lookup"><span data-stu-id="3aea8-304">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="3aea8-305">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-305">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="3aea8-306">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-306">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="3aea8-307">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3aea8-307">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3aea8-308">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3aea8-308">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3aea8-309">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-309">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="3aea8-310">각 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-310">Each component:</span></span>

* <span data-ttu-id="3aea8-311">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-311">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="3aea8-312">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-312">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="3aea8-313">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-313">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="3aea8-314">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-314">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="3aea8-315">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-315">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="3aea8-316">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-316">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="3aea8-317">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어*라고 하며, *미들웨어 구성 요소*라고 부르기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-317">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="3aea8-318">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-318">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="3aea8-319">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-319">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="3aea8-320"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-320"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="3aea8-321">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="3aea8-321">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="3aea8-322">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-322">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="3aea8-323">다음 다이어그램은 그 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-323">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="3aea8-324">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-324">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="3aea8-328">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-328">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="3aea8-329">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-329">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="3aea8-330">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-330">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="3aea8-331">이 경우 실제 요청 파이프라인은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-331">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="3aea8-332">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-332">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="3aea8-333">첫 번째 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 대리자는 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-333">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="3aea8-334"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-334">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="3aea8-335">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-335">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="3aea8-336">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-336">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="3aea8-337">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-337">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="3aea8-338">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-338">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="3aea8-339">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-339">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="3aea8-340">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어*로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-340">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="3aea8-341">추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-341">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="3aea8-342">그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3aea8-342">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="3aea8-343">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="3aea8-343">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="3aea8-344">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-344">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="3aea8-345">예를 들어 헤더 및 상태 코드를 설정하는 변경 작업은 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-345">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="3aea8-346">`next`를 호출한 후 응답 본문에 작성할 경우:</span><span class="sxs-lookup"><span data-stu-id="3aea8-346">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="3aea8-347">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-347">May cause a protocol violation.</span></span> <span data-ttu-id="3aea8-348">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-348">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="3aea8-349">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-349">May corrupt the body format.</span></span> <span data-ttu-id="3aea8-350">예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-350">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="3aea8-351"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-351"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="3aea8-352">미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="3aea8-352">Middleware order</span></span>

<span data-ttu-id="3aea8-353">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-353">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="3aea8-354">순서는 보안, 성능 및 기능에 **중요**합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-354">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="3aea8-355">다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 권장 순서에 따라 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-355">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="3aea8-356">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="3aea8-356">In the preceding code:</span></span>

* <span data-ttu-id="3aea8-357">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-357">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="3aea8-358">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-358">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="3aea8-359">예를 들어 `UseCors` 및 `UseAuthentication`은 표시된 순서대로 이동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-359">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="3aea8-360">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-360">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="3aea8-361">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="3aea8-361">Exception/error handling</span></span>
   * <span data-ttu-id="3aea8-362">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="3aea8-362">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="3aea8-363">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-363">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="3aea8-364">데이터베이스 오류 페이지 미들웨어(`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`)가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-364">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="3aea8-365">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="3aea8-365">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="3aea8-366">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>)가 다음 미들웨어에서 던져진 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-366">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="3aea8-367">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-367">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="3aea8-368">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-368">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="3aea8-369">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-369">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="3aea8-370">쿠키 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-370">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="3aea8-371">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-371">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="3aea8-372">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-372">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="3aea8-373">앱이 세션 상태를 사용하는 경우에는 쿠키 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="3aea8-373">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="3aea8-374">MVC(<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>)는 MVC를 요청 파이프라인에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-374">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="3aea8-375">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-375">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="3aea8-376"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-376"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="3aea8-377">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-377">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="3aea8-378">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-378">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="3aea8-379">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="3aea8-379">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="3aea8-380">*wwwroot*에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-380">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="3aea8-381">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3aea8-381">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="3aea8-382">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-382">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="3aea8-383">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-383">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="3aea8-384">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-384">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="3aea8-385">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-385">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="3aea8-386">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-386">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="3aea8-387"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*>의 MVC 응답은 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-387">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="3aea8-388">Use, Run 및 Map</span><span class="sxs-lookup"><span data-stu-id="3aea8-388">Use, Run, and Map</span></span>

<span data-ttu-id="3aea8-389"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 및 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>을 사용하여 HTTP 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-389">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="3aea8-390">`Use` 메서드는 파이프라인을 단락(short-circuit)할 수 있습니다(즉, `next` 요청 대리자를 호출하지 않는 경우).</span><span class="sxs-lookup"><span data-stu-id="3aea8-390">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="3aea8-391">`Run`은 규칙이며 일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-391">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="3aea8-392"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-392"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="3aea8-393">`Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-393">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="3aea8-394">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-394">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="3aea8-395">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-395">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="3aea8-396">요청</span><span class="sxs-lookup"><span data-stu-id="3aea8-396">Request</span></span>             | <span data-ttu-id="3aea8-397">응답</span><span class="sxs-lookup"><span data-stu-id="3aea8-397">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="3aea8-398">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="3aea8-398">localhost:1234</span></span>      | <span data-ttu-id="3aea8-399">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="3aea8-399">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="3aea8-400">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="3aea8-400">localhost:1234/map1</span></span> | <span data-ttu-id="3aea8-401">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="3aea8-401">Map Test 1</span></span>                   |
| <span data-ttu-id="3aea8-402">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="3aea8-402">localhost:1234/map2</span></span> | <span data-ttu-id="3aea8-403">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="3aea8-403">Map Test 2</span></span>                   |
| <span data-ttu-id="3aea8-404">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="3aea8-404">localhost:1234/map3</span></span> | <span data-ttu-id="3aea8-405">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="3aea8-405">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="3aea8-406">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-406">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="3aea8-407"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-407"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="3aea8-408">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-408">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="3aea8-409">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-409">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="3aea8-410">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-410">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="3aea8-411">요청</span><span class="sxs-lookup"><span data-stu-id="3aea8-411">Request</span></span>                       | <span data-ttu-id="3aea8-412">응답</span><span class="sxs-lookup"><span data-stu-id="3aea8-412">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="3aea8-413">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="3aea8-413">localhost:1234</span></span>                | <span data-ttu-id="3aea8-414">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="3aea8-414">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="3aea8-415">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="3aea8-415">localhost:1234/?branch=master</span></span> | <span data-ttu-id="3aea8-416">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="3aea8-416">Branch used = master</span></span>         |

<span data-ttu-id="3aea8-417">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-417">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="3aea8-418">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-418">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="3aea8-419">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="3aea8-419">Built-in middleware</span></span>

<span data-ttu-id="3aea8-420">ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-420">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="3aea8-421">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-421">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="3aea8-422">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-422">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="3aea8-423">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3aea8-423">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="3aea8-424">미들웨어</span><span class="sxs-lookup"><span data-stu-id="3aea8-424">Middleware</span></span> | <span data-ttu-id="3aea8-425">설명</span><span class="sxs-lookup"><span data-stu-id="3aea8-425">Description</span></span> | <span data-ttu-id="3aea8-426">순서</span><span class="sxs-lookup"><span data-stu-id="3aea8-426">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="3aea8-427">인증</span><span class="sxs-lookup"><span data-stu-id="3aea8-427">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="3aea8-428">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-428">Provides authentication support.</span></span> | <span data-ttu-id="3aea8-429">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="3aea8-429">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="3aea8-430">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="3aea8-430">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="3aea8-431">쿠키 정책</span><span class="sxs-lookup"><span data-stu-id="3aea8-431">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="3aea8-432">개인 정보 저장과 관련한 사용자의 동의를 추적하고 쿠키 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-432">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="3aea8-433">쿠키를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="3aea8-433">Before middleware that issues cookies.</span></span> <span data-ttu-id="3aea8-434">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="3aea8-434">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="3aea8-435">CORS</span><span class="sxs-lookup"><span data-stu-id="3aea8-435">CORS</span></span>](xref:security/cors) | <span data-ttu-id="3aea8-436">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-436">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="3aea8-437">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-437">Before components that use CORS.</span></span> |
| [<span data-ttu-id="3aea8-438">진단</span><span class="sxs-lookup"><span data-stu-id="3aea8-438">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="3aea8-439">개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="3aea8-439">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="3aea8-440">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-440">Before components that generate errors.</span></span> <span data-ttu-id="3aea8-441">예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-441">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="3aea8-442">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="3aea8-442">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="3aea8-443">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-443">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="3aea8-444">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="3aea8-444">Before components that consume the updated fields.</span></span> <span data-ttu-id="3aea8-445">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="3aea8-445">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="3aea8-446">상태 검사</span><span class="sxs-lookup"><span data-stu-id="3aea8-446">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="3aea8-447">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-447">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="3aea8-448">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-448">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="3aea8-449">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="3aea8-449">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="3aea8-450">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-450">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="3aea8-451">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-451">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="3aea8-452">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="3aea8-452">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="3aea8-453">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-453">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="3aea8-454">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-454">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="3aea8-455">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="3aea8-455">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="3aea8-456">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-456">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="3aea8-457">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="3aea8-457">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="3aea8-458">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="3aea8-458">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="3aea8-459">MVC</span><span class="sxs-lookup"><span data-stu-id="3aea8-459">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="3aea8-460">MVC/Razor Pages를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-460">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="3aea8-461">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-461">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="3aea8-462">OWIN</span><span class="sxs-lookup"><span data-stu-id="3aea8-462">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="3aea8-463">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-463">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="3aea8-464">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-464">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="3aea8-465">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="3aea8-465">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="3aea8-466">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-466">Provides support for caching responses.</span></span> | <span data-ttu-id="3aea8-467">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-467">Before components that require caching.</span></span> |
| [<span data-ttu-id="3aea8-468">응답 압축</span><span class="sxs-lookup"><span data-stu-id="3aea8-468">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="3aea8-469">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-469">Provides support for compressing responses.</span></span> | <span data-ttu-id="3aea8-470">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-470">Before components that require compression.</span></span> |
| [<span data-ttu-id="3aea8-471">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="3aea8-471">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="3aea8-472">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-472">Provides localization support.</span></span> | <span data-ttu-id="3aea8-473">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-473">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="3aea8-474">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="3aea8-474">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="3aea8-475">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-475">Defines and constrains request routes.</span></span> | <span data-ttu-id="3aea8-476">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="3aea8-476">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="3aea8-477">세션</span><span class="sxs-lookup"><span data-stu-id="3aea8-477">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="3aea8-478">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-478">Provides support for managing user sessions.</span></span> | <span data-ttu-id="3aea8-479">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-479">Before components that require Session.</span></span> |
| [<span data-ttu-id="3aea8-480">정적 파일</span><span class="sxs-lookup"><span data-stu-id="3aea8-480">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="3aea8-481">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-481">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="3aea8-482">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-482">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="3aea8-483">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="3aea8-483">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="3aea8-484">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-484">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="3aea8-485">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-485">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="3aea8-486">WebSockets</span><span class="sxs-lookup"><span data-stu-id="3aea8-486">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="3aea8-487">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="3aea8-487">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="3aea8-488">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="3aea8-488">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="3aea8-489">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3aea8-489">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
