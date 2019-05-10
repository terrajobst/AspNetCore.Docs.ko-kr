---
title: ASP.NET Core 미들웨어 기본 사항
author: rick-anderson
description: ASP.NET Core 미들웨어 및 요청 파이프라인에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 02/17/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: bac121441d6856ca79affe1a3130e5cbc76debd9
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64882338"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="25401-103">ASP.NET Core 미들웨어</span><span class="sxs-lookup"><span data-stu-id="25401-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="25401-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="25401-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="25401-105">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 어셈블리되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="25401-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="25401-106">각 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="25401-106">Each component:</span></span>

* <span data-ttu-id="25401-107">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="25401-108">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="25401-109">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="25401-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="25401-110">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="25401-111">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="25401-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="25401-112">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="25401-113">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드는 *미들웨어*이며, *미들웨어 구성 요소*라고도 합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="25401-114">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="25401-115">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="25401-116"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 더 많은 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides more middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="25401-117">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="25401-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="25401-118">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="25401-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="25401-119">다음 다이어그램은 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="25401-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="25401-120">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="25401-120">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어를 처리하고, 응답이 앱에서 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="25401-124">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="25401-125">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 catch할 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="25401-126">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="25401-127">이 경우 실제 요청 파이프라인은 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="25401-128">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="25401-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

<span data-ttu-id="25401-129">첫 번째 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 대리자는 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="25401-130"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="25401-131">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="25401-131">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="25401-132">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="25401-133">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

<span data-ttu-id="25401-134">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를*요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="25401-135">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 보통 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-135">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="25401-136">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어*로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="25401-137">추가 처리를 종료하는 미들웨어 앞의 파이프라인에 추가되는 미들웨어는 `next.Invoke` 문 뒤의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="25401-138">그러나 이미 전송된 응답에 쓰려고 하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="25401-138">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="25401-139">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="25401-139">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="25401-140">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="25401-141">예를 들어 헤더 및 상태 코드를 설정하는 변경 작업은 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-141">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="25401-142">`next`를 호출한 후 응답 본문에 작성하기:</span><span class="sxs-lookup"><span data-stu-id="25401-142">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="25401-143">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-143">May cause a protocol violation.</span></span> <span data-ttu-id="25401-144">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-144">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="25401-145">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-145">May corrupt the body format.</span></span> <span data-ttu-id="25401-146">예를 들어 CSS 파일에 HTML 바닥글 작성하기.</span><span class="sxs-lookup"><span data-stu-id="25401-146">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="25401-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="25401-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

## <a name="order"></a><span data-ttu-id="25401-148">순서</span><span class="sxs-lookup"><span data-stu-id="25401-148">Order</span></span>

<span data-ttu-id="25401-149">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="25401-150">순서는 보안, 성능 및 기능에 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-150">The order is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="25401-151">다음 `Startup.Configure` 메서드는 공통 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-151">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

::: moniker range=">= aspnetcore-2.0"

1. <span data-ttu-id="25401-152">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="25401-152">Exception/error handling</span></span>
1. <span data-ttu-id="25401-153">HTTP 엄격한 전송 보안 프로토콜</span><span class="sxs-lookup"><span data-stu-id="25401-153">HTTP Strict Transport Security Protocol</span></span>
1. <span data-ttu-id="25401-154">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="25401-154">HTTPS redirection</span></span>
1. <span data-ttu-id="25401-155">정적 파일 서버</span><span class="sxs-lookup"><span data-stu-id="25401-155">Static file server</span></span>
1. <span data-ttu-id="25401-156">쿠키 정책 적용</span><span class="sxs-lookup"><span data-stu-id="25401-156">Cookie policy enforcement</span></span>
1. <span data-ttu-id="25401-157">인증</span><span class="sxs-lookup"><span data-stu-id="25401-157">Authentication</span></span>
1. <span data-ttu-id="25401-158">세션</span><span class="sxs-lookup"><span data-stu-id="25401-158">Session</span></span>
1. <span data-ttu-id="25401-159">MVC</span><span class="sxs-lookup"><span data-stu-id="25401-159">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    if (env.IsDevelopment())
    {
        // When the app runs in the Development environment:
        //   Use the Developer Exception Page to report app runtime errors.
        //   Use the Database Error Page to report database runtime errors.
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        // When the app doesn't run in the Development environment:
        //   Enable the Exception Handler Middleware to catch exceptions
        //     thrown in the following middlewares.
        //   Use the HTTP Strict Transport Security Protocol (HSTS)
        //     Middleware.
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    // Use HTTPS Redirection Middleware to redirect HTTP requests to HTTPS.
    app.UseHttpsRedirection();

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Use Cookie Policy Middleware to conform to EU General Data 
    // Protection Regulation (GDPR) regulations.
    app.UseCookiePolicy();

    // Authenticate before the user accesses secure resources.
    app.UseAuthentication();

    // If the app uses session state, call Session Middleware after Cookie 
    // Policy Middleware and before MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvc();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. <span data-ttu-id="25401-160">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="25401-160">Exception/error handling</span></span>
1. <span data-ttu-id="25401-161">정적 파일</span><span class="sxs-lookup"><span data-stu-id="25401-161">Static files</span></span>
1. <span data-ttu-id="25401-162">인증</span><span class="sxs-lookup"><span data-stu-id="25401-162">Authentication</span></span>
1. <span data-ttu-id="25401-163">세션</span><span class="sxs-lookup"><span data-stu-id="25401-163">Session</span></span>
1. <span data-ttu-id="25401-164">MVC</span><span class="sxs-lookup"><span data-stu-id="25401-164">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable the Exception Handler Middleware to catch exceptions
    //   thrown in the following middlewares.
    app.UseExceptionHandler("/Home/Error");

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Authenticate before you access secure resources.
    app.UseIdentity();

    // If the app uses session state, call UseSession before 
    // MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

<span data-ttu-id="25401-165">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="25401-165">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="25401-166"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="25401-166"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="25401-167">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 catch 합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-167">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="25401-168">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-168">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="25401-169">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="25401-169">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="25401-170">*wwwroot* 아래의 항목을 비롯한 제공되는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-170">Any files served by it, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="25401-171">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="25401-171">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="25401-172">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="25401-172">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="25401-173">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-173">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="25401-174">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-174">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="25401-175">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 ID 미들웨어(<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="25401-175">If the request isn't handled by Static File Middleware, it's passed on to the Identity Middleware (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), which performs authentication.</span></span> <span data-ttu-id="25401-176">ID는 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-176">Identity doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="25401-177">ID가 요청을 인증하지만 MVC가 특정 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-177">Although Identity authenticates requests, authorization (and rejection) occurs only after MVC selects a specific controller and action.</span></span>

::: moniker-end

<span data-ttu-id="25401-178">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-178">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="25401-179">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-179">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="25401-180"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*>의 MVC 응답은 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-180">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

### <a name="use-run-and-map"></a><span data-ttu-id="25401-181">Use, Run 및 Map</span><span class="sxs-lookup"><span data-stu-id="25401-181">Use, Run, and Map</span></span>

<span data-ttu-id="25401-182">`Use`, `Run` 및 `Map`을 사용하여 HTTP 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-182">Configure the HTTP pipeline using `Use`, `Run`, and `Map`.</span></span> <span data-ttu-id="25401-183">`Use` 메서드는 파이프라인을 단락(short-circuit)할 수 있습니다(즉, `next` 요청 대리자를 호출하지 않는 경우).</span><span class="sxs-lookup"><span data-stu-id="25401-183">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="25401-184">`Run`은 규칙이며 일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-184">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="25401-185"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="25401-185"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="25401-186">`Map*`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-186">`Map*` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="25401-187">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="25401-187">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

<span data-ttu-id="25401-188">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="25401-188">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="25401-189">요청</span><span class="sxs-lookup"><span data-stu-id="25401-189">Request</span></span>             | <span data-ttu-id="25401-190">응답</span><span class="sxs-lookup"><span data-stu-id="25401-190">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="25401-191">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="25401-191">localhost:1234</span></span>      | <span data-ttu-id="25401-192">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="25401-192">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="25401-193">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="25401-193">localhost:1234/map1</span></span> | <span data-ttu-id="25401-194">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="25401-194">Map Test 1</span></span>                   |
| <span data-ttu-id="25401-195">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="25401-195">localhost:1234/map2</span></span> | <span data-ttu-id="25401-196">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="25401-196">Map Test 2</span></span>                   |
| <span data-ttu-id="25401-197">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="25401-197">localhost:1234/map3</span></span> | <span data-ttu-id="25401-198">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="25401-198">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="25401-199">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="25401-199">When `Map` is used, the matched path segment(s) are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="25401-200">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions)은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-200">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="25401-201">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-201">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="25401-202">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="25401-202">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

<span data-ttu-id="25401-203">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="25401-203">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="25401-204">요청</span><span class="sxs-lookup"><span data-stu-id="25401-204">Request</span></span>                       | <span data-ttu-id="25401-205">응답</span><span class="sxs-lookup"><span data-stu-id="25401-205">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="25401-206">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="25401-206">localhost:1234</span></span>                | <span data-ttu-id="25401-207">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="25401-207">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="25401-208">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="25401-208">localhost:1234/?branch=master</span></span> | <span data-ttu-id="25401-209">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="25401-209">Branch used = master</span></span>         |

<span data-ttu-id="25401-210">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-210">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="25401-211">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25401-211">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="25401-212">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="25401-212">Built-in middleware</span></span>

<span data-ttu-id="25401-213">ASP.NET Core는 다음과 같은 미들웨어 구성 요소가 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="25401-213">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="25401-214">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-214">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="25401-215">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 다운스트림 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-215">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="25401-216">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="25401-216">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="25401-217">미들웨어</span><span class="sxs-lookup"><span data-stu-id="25401-217">Middleware</span></span> | <span data-ttu-id="25401-218">설명</span><span class="sxs-lookup"><span data-stu-id="25401-218">Description</span></span> | <span data-ttu-id="25401-219">순서</span><span class="sxs-lookup"><span data-stu-id="25401-219">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="25401-220">인증</span><span class="sxs-lookup"><span data-stu-id="25401-220">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="25401-221">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-221">Provides authentication support.</span></span> | <span data-ttu-id="25401-222">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="25401-222">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="25401-223">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="25401-223">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="25401-224">쿠키 정책</span><span class="sxs-lookup"><span data-stu-id="25401-224">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="25401-225">개인 정보 저장과 관련한 사용자의 동의를 추적하고 쿠키 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-225">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="25401-226">쿠키를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="25401-226">Before middleware that issues cookies.</span></span> <span data-ttu-id="25401-227">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="25401-227">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="25401-228">CORS</span><span class="sxs-lookup"><span data-stu-id="25401-228">CORS</span></span>](xref:security/cors) | <span data-ttu-id="25401-229">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-229">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="25401-230">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="25401-230">Before components that use CORS.</span></span> |
| [<span data-ttu-id="25401-231">예외 처리</span><span class="sxs-lookup"><span data-stu-id="25401-231">Exception Handling</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="25401-232">예외를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-232">Handles exceptions.</span></span> | <span data-ttu-id="25401-233">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="25401-233">Before components that generate errors.</span></span> |
| [<span data-ttu-id="25401-234">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="25401-234">Forwarded Headers</span></span>](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | <span data-ttu-id="25401-235">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-235">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="25401-236">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="25401-236">Before components that consume the updated fields.</span></span> <span data-ttu-id="25401-237">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="25401-237">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="25401-238">상태 검사</span><span class="sxs-lookup"><span data-stu-id="25401-238">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="25401-239">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-239">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="25401-240">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="25401-240">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="25401-241">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="25401-241">HTTP Method Override</span></span>](/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | <span data-ttu-id="25401-242">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-242">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="25401-243">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="25401-243">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="25401-244">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="25401-244">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="25401-245">HTTPS로 모든 HTTP 요청을 리디렉션합니다(ASP.NET Core 2.1 이상).</span><span class="sxs-lookup"><span data-stu-id="25401-245">Redirect all HTTP requests to HTTPS (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="25401-246">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="25401-246">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="25401-247">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="25401-247">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="25401-248">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다(ASP.NET Core 2.1 이상).</span><span class="sxs-lookup"><span data-stu-id="25401-248">Security enhancement middleware that adds a special response header (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="25401-249">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="25401-249">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="25401-250">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="25401-250">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="25401-251">MVC</span><span class="sxs-lookup"><span data-stu-id="25401-251">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="25401-252">MVC/Razor Pages(ASP.NET Core 2.0 이상)를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-252">Processes requests with MVC/Razor Pages (ASP.NET Core 2.0 or later).</span></span> | <span data-ttu-id="25401-253">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="25401-253">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="25401-254">OWIN</span><span class="sxs-lookup"><span data-stu-id="25401-254">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="25401-255">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="25401-255">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="25401-256">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="25401-256">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="25401-257">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="25401-257">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="25401-258">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-258">Provides support for caching responses.</span></span> | <span data-ttu-id="25401-259">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="25401-259">Before components that require caching.</span></span> |
| [<span data-ttu-id="25401-260">응답 압축</span><span class="sxs-lookup"><span data-stu-id="25401-260">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="25401-261">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-261">Provides support for compressing responses.</span></span> | <span data-ttu-id="25401-262">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="25401-262">Before components that require compression.</span></span> |
| [<span data-ttu-id="25401-263">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="25401-263">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="25401-264">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-264">Provides localization support.</span></span> | <span data-ttu-id="25401-265">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="25401-265">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="25401-266">라우팅</span><span class="sxs-lookup"><span data-stu-id="25401-266">Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="25401-267">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-267">Defines and constrains request routes.</span></span> | <span data-ttu-id="25401-268">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="25401-268">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="25401-269">세션</span><span class="sxs-lookup"><span data-stu-id="25401-269">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="25401-270">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-270">Provides support for managing user sessions.</span></span> | <span data-ttu-id="25401-271">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="25401-271">Before components that require Session.</span></span> |
| [<span data-ttu-id="25401-272">정적 파일</span><span class="sxs-lookup"><span data-stu-id="25401-272">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="25401-273">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-273">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="25401-274">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="25401-274">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="25401-275">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="25401-275">URL Rewriting</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="25401-276">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-276">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="25401-277">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="25401-277">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="25401-278">WebSockets</span><span class="sxs-lookup"><span data-stu-id="25401-278">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="25401-279">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="25401-279">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="25401-280">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="25401-280">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="25401-281">추가 자료</span><span class="sxs-lookup"><span data-stu-id="25401-281">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
