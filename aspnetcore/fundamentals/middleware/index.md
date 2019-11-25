---
title: ASP.NET Core 미들웨어 기본 사항
author: rick-anderson
description: ASP.NET Core 미들웨어 및 요청 파이프라인에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: d678f3d1f6ca10e486543a2965506236e4e61b82
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239845"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="4fa90-103">ASP.NET Core 미들웨어</span><span class="sxs-lookup"><span data-stu-id="4fa90-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="4fa90-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4fa90-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4fa90-105">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="4fa90-106">각 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-106">Each component:</span></span>

* <span data-ttu-id="4fa90-107">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="4fa90-108">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="4fa90-109">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="4fa90-110">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="4fa90-111">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="4fa90-112">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="4fa90-113">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어*라고 하며, *미들웨어 구성 요소*라고 부르기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="4fa90-114">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="4fa90-115">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="4fa90-116"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="4fa90-117">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="4fa90-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="4fa90-118">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="4fa90-119">다음 다이어그램은 그 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="4fa90-120">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-120">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="4fa90-124">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="4fa90-125">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="4fa90-126">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="4fa90-127">이 경우 실제 요청 파이프라인은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="4fa90-128">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="4fa90-129">첫 번째 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 대리자는 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="4fa90-130"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="4fa90-131">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-131">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="4fa90-132">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="4fa90-133">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="4fa90-134">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="4fa90-135">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-135">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="4fa90-136">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어*로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="4fa90-137">추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="4fa90-138">그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4fa90-138">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="4fa90-139">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="4fa90-139">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="4fa90-140">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="4fa90-141">예를 들어 헤더 및 상태 코드를 설정하는 변경 작업은 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-141">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="4fa90-142">`next`를 호출한 후 응답 본문에 작성할 경우:</span><span class="sxs-lookup"><span data-stu-id="4fa90-142">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="4fa90-143">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-143">May cause a protocol violation.</span></span> <span data-ttu-id="4fa90-144">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-144">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="4fa90-145">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-145">May corrupt the body format.</span></span> <span data-ttu-id="4fa90-146">예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-146">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="4fa90-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="4fa90-148">미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="4fa90-148">Middleware order</span></span>

<span data-ttu-id="4fa90-149">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="4fa90-150">순서는 보안, 성능 및 기능에 **중요**합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-150">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="4fa90-151">다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 권장 순서에 따라 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-151">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="4fa90-152">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="4fa90-152">In the preceding code:</span></span>

* <span data-ttu-id="4fa90-153">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-153">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="4fa90-154">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-154">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="4fa90-155">예를 들어 `UseCors`, `UseAuthentication` 및 `UseAuthorization`은 표시된 순서 대로 이동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-155">For example, `UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>

<span data-ttu-id="4fa90-156">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-156">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="4fa90-157">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="4fa90-157">Exception/error handling</span></span>
   * <span data-ttu-id="4fa90-158">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="4fa90-158">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="4fa90-159">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-159">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="4fa90-160">데이터베이스 오류 페이지 미들웨어가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-160">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="4fa90-161">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="4fa90-161">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="4fa90-162">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>)가 다음 미들웨어에서 던져진 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-162">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="4fa90-163">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-163">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="4fa90-164">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-164">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="4fa90-165">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-165">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="4fa90-166">쿠키 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-166">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="4fa90-167">요청을 라우팅하도록 미들웨어(`UseRouting`) 라우팅.</span><span class="sxs-lookup"><span data-stu-id="4fa90-167">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="4fa90-168">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-168">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="4fa90-169">권한 부여 미들웨어(`UseAuthorization`)는 사용자에게 보안 리소스에 액세스할 수 있는 권한을 부여합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-169">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="4fa90-170">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-170">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="4fa90-171">앱이 세션 상태를 사용하는 경우에는 쿠키 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="4fa90-171">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="4fa90-172">엔드포인트 라우팅 미들웨어(`MapRazorPages`를 포함하는 `UseEndpoints`)를 사용하여 요청 파이프라인에 Razor Pages 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-172">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="4fa90-173">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-173">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="4fa90-174"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-174"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="4fa90-175">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-175">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="4fa90-176">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-176">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="4fa90-177">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="4fa90-177">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="4fa90-178">*wwwroot*에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-178">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="4fa90-179">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4fa90-179">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="4fa90-180">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-180">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="4fa90-181">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-181">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="4fa90-182">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-182">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="4fa90-183">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-183">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="4fa90-184">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-184">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="4fa90-185">Razor Pages 응답을 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-185">The Razor Pages responses can be compressed.</span></span>

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="4fa90-186">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="4fa90-186">In the preceding code:</span></span>

* <span data-ttu-id="4fa90-187">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-187">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="4fa90-188">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-188">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="4fa90-189">예를 들어 `UseCors` 및 `UseAuthentication`은 표시된 순서대로 이동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-189">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="4fa90-190">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-190">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="4fa90-191">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="4fa90-191">Exception/error handling</span></span>
   * <span data-ttu-id="4fa90-192">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="4fa90-192">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="4fa90-193">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-193">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="4fa90-194">데이터베이스 오류 페이지 미들웨어(`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`)가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-194">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="4fa90-195">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="4fa90-195">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="4fa90-196">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>)가 다음 미들웨어에서 던져진 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-196">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="4fa90-197">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-197">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="4fa90-198">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-198">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="4fa90-199">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-199">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="4fa90-200">쿠키 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-200">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="4fa90-201">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-201">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="4fa90-202">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-202">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="4fa90-203">앱이 세션 상태를 사용하는 경우에는 쿠키 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="4fa90-203">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="4fa90-204">MVC(<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>)는 MVC를 요청 파이프라인에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-204">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="4fa90-205">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-205">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="4fa90-206"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-206"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="4fa90-207">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-207">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="4fa90-208">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-208">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="4fa90-209">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="4fa90-209">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="4fa90-210">*wwwroot*에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-210">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="4fa90-211">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4fa90-211">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="4fa90-212">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-212">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="4fa90-213">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-213">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="4fa90-214">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-214">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="4fa90-215">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-215">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="4fa90-216">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-216">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="4fa90-217"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*>의 MVC 응답은 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-217">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

## <a name="use-run-and-map"></a><span data-ttu-id="4fa90-218">Use, Run 및 Map</span><span class="sxs-lookup"><span data-stu-id="4fa90-218">Use, Run, and Map</span></span>

<span data-ttu-id="4fa90-219"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 및 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>을 사용하여 HTTP 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-219">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="4fa90-220">`Use` 메서드는 파이프라인을 단락(short-circuit)할 수 있습니다(즉, `next` 요청 대리자를 호출하지 않는 경우).</span><span class="sxs-lookup"><span data-stu-id="4fa90-220">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="4fa90-221">`Run`은 규칙이며 일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-221">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="4fa90-222"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-222"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="4fa90-223">`Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-223">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="4fa90-224">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-224">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="4fa90-225">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-225">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="4fa90-226">요청</span><span class="sxs-lookup"><span data-stu-id="4fa90-226">Request</span></span>             | <span data-ttu-id="4fa90-227">응답</span><span class="sxs-lookup"><span data-stu-id="4fa90-227">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="4fa90-228">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="4fa90-228">localhost:1234</span></span>      | <span data-ttu-id="4fa90-229">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="4fa90-229">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="4fa90-230">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="4fa90-230">localhost:1234/map1</span></span> | <span data-ttu-id="4fa90-231">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="4fa90-231">Map Test 1</span></span>                   |
| <span data-ttu-id="4fa90-232">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="4fa90-232">localhost:1234/map2</span></span> | <span data-ttu-id="4fa90-233">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="4fa90-233">Map Test 2</span></span>                   |
| <span data-ttu-id="4fa90-234">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="4fa90-234">localhost:1234/map3</span></span> | <span data-ttu-id="4fa90-235">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="4fa90-235">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="4fa90-236">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-236">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="4fa90-237"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-237"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="4fa90-238">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-238">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="4fa90-239">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-239">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="4fa90-240">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-240">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="4fa90-241">요청</span><span class="sxs-lookup"><span data-stu-id="4fa90-241">Request</span></span>                       | <span data-ttu-id="4fa90-242">응답</span><span class="sxs-lookup"><span data-stu-id="4fa90-242">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="4fa90-243">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="4fa90-243">localhost:1234</span></span>                | <span data-ttu-id="4fa90-244">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="4fa90-244">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="4fa90-245">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="4fa90-245">localhost:1234/?branch=master</span></span> | <span data-ttu-id="4fa90-246">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="4fa90-246">Branch used = master</span></span>         |

<span data-ttu-id="4fa90-247">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-247">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="4fa90-248">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-248">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="4fa90-249">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="4fa90-249">Built-in middleware</span></span>

<span data-ttu-id="4fa90-250">ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-250">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="4fa90-251">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-251">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="4fa90-252">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-252">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="4fa90-253">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4fa90-253">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="4fa90-254">미들웨어</span><span class="sxs-lookup"><span data-stu-id="4fa90-254">Middleware</span></span> | <span data-ttu-id="4fa90-255">설명</span><span class="sxs-lookup"><span data-stu-id="4fa90-255">Description</span></span> | <span data-ttu-id="4fa90-256">순서</span><span class="sxs-lookup"><span data-stu-id="4fa90-256">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="4fa90-257">인증</span><span class="sxs-lookup"><span data-stu-id="4fa90-257">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="4fa90-258">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-258">Provides authentication support.</span></span> | <span data-ttu-id="4fa90-259">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="4fa90-259">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="4fa90-260">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="4fa90-260">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="4fa90-261">쿠키 정책</span><span class="sxs-lookup"><span data-stu-id="4fa90-261">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="4fa90-262">개인 정보 저장과 관련한 사용자의 동의를 추적하고 쿠키 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-262">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="4fa90-263">쿠키를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="4fa90-263">Before middleware that issues cookies.</span></span> <span data-ttu-id="4fa90-264">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="4fa90-264">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="4fa90-265">CORS</span><span class="sxs-lookup"><span data-stu-id="4fa90-265">CORS</span></span>](xref:security/cors) | <span data-ttu-id="4fa90-266">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-266">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="4fa90-267">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="4fa90-267">Before components that use CORS.</span></span> |
| [<span data-ttu-id="4fa90-268">진단</span><span class="sxs-lookup"><span data-stu-id="4fa90-268">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="4fa90-269">개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="4fa90-269">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="4fa90-270">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="4fa90-270">Before components that generate errors.</span></span> <span data-ttu-id="4fa90-271">예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-271">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="4fa90-272">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="4fa90-272">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="4fa90-273">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-273">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="4fa90-274">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="4fa90-274">Before components that consume the updated fields.</span></span> <span data-ttu-id="4fa90-275">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="4fa90-275">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="4fa90-276">상태 검사</span><span class="sxs-lookup"><span data-stu-id="4fa90-276">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="4fa90-277">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-277">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="4fa90-278">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-278">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="4fa90-279">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="4fa90-279">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="4fa90-280">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-280">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="4fa90-281">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-281">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="4fa90-282">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="4fa90-282">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="4fa90-283">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-283">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="4fa90-284">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="4fa90-284">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="4fa90-285">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="4fa90-285">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="4fa90-286">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-286">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="4fa90-287">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="4fa90-287">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="4fa90-288">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="4fa90-288">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="4fa90-289">MVC</span><span class="sxs-lookup"><span data-stu-id="4fa90-289">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="4fa90-290">MVC/Razor Pages를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-290">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="4fa90-291">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-291">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="4fa90-292">OWIN</span><span class="sxs-lookup"><span data-stu-id="4fa90-292">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="4fa90-293">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-293">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="4fa90-294">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-294">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="4fa90-295">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="4fa90-295">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="4fa90-296">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-296">Provides support for caching responses.</span></span> | <span data-ttu-id="4fa90-297">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="4fa90-297">Before components that require caching.</span></span> |
| [<span data-ttu-id="4fa90-298">응답 압축</span><span class="sxs-lookup"><span data-stu-id="4fa90-298">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="4fa90-299">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-299">Provides support for compressing responses.</span></span> | <span data-ttu-id="4fa90-300">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="4fa90-300">Before components that require compression.</span></span> |
| [<span data-ttu-id="4fa90-301">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="4fa90-301">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="4fa90-302">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-302">Provides localization support.</span></span> | <span data-ttu-id="4fa90-303">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="4fa90-303">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="4fa90-304">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="4fa90-304">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="4fa90-305">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-305">Defines and constrains request routes.</span></span> | <span data-ttu-id="4fa90-306">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="4fa90-306">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="4fa90-307">세션</span><span class="sxs-lookup"><span data-stu-id="4fa90-307">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="4fa90-308">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-308">Provides support for managing user sessions.</span></span> | <span data-ttu-id="4fa90-309">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="4fa90-309">Before components that require Session.</span></span> |
| [<span data-ttu-id="4fa90-310">정적 파일</span><span class="sxs-lookup"><span data-stu-id="4fa90-310">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="4fa90-311">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-311">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="4fa90-312">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-312">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="4fa90-313">URL 다시 쓰기</span><span class="sxs-lookup"><span data-stu-id="4fa90-313">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="4fa90-314">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-314">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="4fa90-315">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="4fa90-315">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="4fa90-316">WebSockets</span><span class="sxs-lookup"><span data-stu-id="4fa90-316">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="4fa90-317">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="4fa90-317">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="4fa90-318">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="4fa90-318">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="4fa90-319">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4fa90-319">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
