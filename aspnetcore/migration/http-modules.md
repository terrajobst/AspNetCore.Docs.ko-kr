---
title: HTTP 처리기 및 모듈을 ASP.NET Core 미들웨어로 마이그레이션
author: rick-anderson
description: ''
ms.author: tdykstra
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: 84381210910c66a7d121120b8c6b0f046cae8c4f
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207800"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="70c5f-102">HTTP 처리기 및 모듈을 ASP.NET Core 미들웨어로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="70c5f-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="70c5f-103">이 문서에서는 기존 ASP.NET 마이그레이션하는 방법을 보여 줍니다 [HTTP 모듈 및 처리기 system.webserver에서](/iis/configuration/system.webserver/) ASP.NET core [미들웨어](xref:fundamentals/middleware/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="70c5f-104">모듈 및 처리기 revisited</span><span class="sxs-lookup"><span data-stu-id="70c5f-104">Modules and handlers revisited</span></span>

<span data-ttu-id="70c5f-105">ASP.NET Core 미들웨어를 계속 하기 전에 먼저 요약해 보면 HTTP 모듈 및 처리기의 작동 방식:</span><span class="sxs-lookup"><span data-stu-id="70c5f-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![모듈 처리기](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="70c5f-107">**처리기는:**</span><span class="sxs-lookup"><span data-stu-id="70c5f-107">**Handlers are:**</span></span>

* <span data-ttu-id="70c5f-108">구현 하는 클래스 [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="70c5f-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="70c5f-109">지정 된 파일 이름 또는 확장명으로 요청을 처리 하는 데 *보고서*</span><span class="sxs-lookup"><span data-stu-id="70c5f-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="70c5f-110">[구성할](/iis/configuration/system.webserver/handlers/) 에서 *Web.config*</span><span class="sxs-lookup"><span data-stu-id="70c5f-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="70c5f-111">**모듈은 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="70c5f-111">**Modules are:**</span></span>

* <span data-ttu-id="70c5f-112">구현 하는 클래스 [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="70c5f-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="70c5f-113">모든 요청에 대 한 호출</span><span class="sxs-lookup"><span data-stu-id="70c5f-113">Invoked for every request</span></span>

* <span data-ttu-id="70c5f-114">(이후 처리를 중지 요청)를 단락 (short-circuit) 할</span><span class="sxs-lookup"><span data-stu-id="70c5f-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="70c5f-115">HTTP 응답에 추가 하거나 직접 만들 수</span><span class="sxs-lookup"><span data-stu-id="70c5f-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="70c5f-116">[구성할](/iis/configuration/system.webserver/modules/) 에서 *Web.config*</span><span class="sxs-lookup"><span data-stu-id="70c5f-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="70c5f-117">**모듈에는 들어오는 요청을 처리 하는 순서는 의해 결정 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="70c5f-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="70c5f-118">합니다 [응용 프로그램 수명 주기](https://msdn.microsoft.com/library/ms227673.aspx), ASP.NET에 의해 발생 하는 시리즈 이벤트는: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. 각 모듈에는 하나 이상의 이벤트 처리기를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="70c5f-119">동일한 이벤트에서 구성 하는 순서에 대 한 *Web.config*합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="70c5f-120">모듈 외에도 수명 주기 이벤트에 대 한 처리기를 추가할 수 있습니다 하 *Global.asax.cs* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="70c5f-121">이러한 처리기는 구성 된 모듈의 처리기 후 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="70c5f-122">처리기 및 모듈을 미들웨어로</span><span class="sxs-lookup"><span data-stu-id="70c5f-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="70c5f-123">**미들웨어는 HTTP 모듈 및 처리기 보다 간단 합니다.**</span><span class="sxs-lookup"><span data-stu-id="70c5f-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="70c5f-124">모듈, 처리기 *Global.asax.cs*를 *Web.config* (제외 IIS 구성)은 응용 프로그램 수명 주기 및</span><span class="sxs-lookup"><span data-stu-id="70c5f-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="70c5f-125">미들웨어를 통해 수행 된 모듈 및 처리기의 역할</span><span class="sxs-lookup"><span data-stu-id="70c5f-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="70c5f-126">미들웨어는 코드를 사용 하 여 구성 된 대신 *Web.config*</span><span class="sxs-lookup"><span data-stu-id="70c5f-126">Middleware are configured using code rather than in *Web.config*</span></span>

* <span data-ttu-id="70c5f-127">[파이프라인 분기](xref:fundamentals/middleware/index#use-run-and-map) 뿐 아니라 요청 헤더, 쿼리 문자열에도 URL을 기준으로 특정 미들웨어에서 요청을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-127">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="70c5f-128">**미들웨어 모듈 매우 비슷합니다.**</span><span class="sxs-lookup"><span data-stu-id="70c5f-128">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="70c5f-129">모든 요청에 대 한 원칙에서 호출</span><span class="sxs-lookup"><span data-stu-id="70c5f-129">Invoked in principle for every request</span></span>

* <span data-ttu-id="70c5f-130">요청에 의해 단락 (short-circuit) 할 [다음 미들웨어에 요청을 전달 하지 않고](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="70c5f-130">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="70c5f-131">HTTP 응답 자체를 만들려면</span><span class="sxs-lookup"><span data-stu-id="70c5f-131">Able to create their own HTTP response</span></span>

<span data-ttu-id="70c5f-132">**미들웨어 및 모듈을 다른 순서로 처리 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="70c5f-132">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="70c5f-133">에 삽입할 요청 파이프라인을 모듈의 순서는 주로 기반으로 하는 동안 순서를 기반으로 하는 미들웨어 순서 [응용 프로그램 수명 주기](https://msdn.microsoft.com/library/ms227673.aspx) 이벤트</span><span class="sxs-lookup"><span data-stu-id="70c5f-133">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="70c5f-134">미들웨어의 응답에 대 한 순서가 반대로 하는 요청에 대 한 요청 및 응답에 대 한 동일한 모듈의 순서는</span><span class="sxs-lookup"><span data-stu-id="70c5f-134">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="70c5f-135">참조 [IApplicationBuilder로 미들웨어 파이프라인 만들기](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="70c5f-135">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![미들웨어](http-modules/_static/middleware.png)

<span data-ttu-id="70c5f-137">어떻게 요청 short-circuited 인증 미들웨어에서 위의 이미지 note 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-137">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="70c5f-138">모듈 코드를 미들웨어로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="70c5f-138">Migrating module code to middleware</span></span>

<span data-ttu-id="70c5f-139">기존 HTTP 모듈은 다음과 유사 하 게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-139">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="70c5f-140">에 표시 된 대로 [미들웨어](xref:fundamentals/middleware/index) 페이지는 ASP.NET Core 미들웨어가 노출 하는 클래스는 `Invoke` 메서드 만들기는 `HttpContext` 반환 하 고는 `Task`합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-140">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="70c5f-141">새 미들웨어는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-141">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="70c5f-142">이전 미들웨어 템플릿 섹션에서 수행한 [미들웨어를 작성](xref:fundamentals/middleware/write)합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-142">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="70c5f-143">합니다 *MyMiddlewareExtensions* 도우미 클래스를 사용 하면에서 미들웨어를 구성 하기 더 쉬울 프로그램 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-143">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="70c5f-144">`UseMyMiddleware` 메서드 요청 파이프라인에 미들웨어 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-144">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="70c5f-145">미들웨어에서 필요한 서비스는 미들웨어의 생성자에 지정 된 가져오기.</span><span class="sxs-lookup"><span data-stu-id="70c5f-145">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="70c5f-146">모듈에는 예를 들어 사용자 권한이 없는 경우 요청을 종료할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-146">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="70c5f-147">미들웨어를 호출 하지 않음으로써이 처리 `Invoke` 에서 파이프라인의 다음 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-147">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="70c5f-148">이전 미들웨어의 응답은 파이프라인을 통해 다시 때 호출 될 여전히 때문에 완벽 하 게 요청을 종료 하지 않고이 염두에서에 둡니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-148">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="70c5f-149">새 미들웨어를 모듈의 기능으로 마이그레이션한 경우 때문에 코드는 컴파일되지 않습니다 알 수 있습니다는 `HttpContext` 클래스 ASP.NET Core에서 크게 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-149">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="70c5f-150">[나중에](#migrating-to-the-new-httpcontext), 새 ASP.NET Core HttpContext에 마이그레이션하는 방법을 배웁니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-150">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="70c5f-151">마이그레이션 모듈 삽입 요청 파이프라인</span><span class="sxs-lookup"><span data-stu-id="70c5f-151">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="70c5f-152">HTTP 모듈은 일반적으로 사용 하 여 요청 파이프라인에 추가 됩니다 *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="70c5f-152">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="70c5f-153">이 변환 [새 미들웨어를 추가](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 를 요청 파이프라인에 프로그램 `Startup` 클래스:</span><span class="sxs-lookup"><span data-stu-id="70c5f-153">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="70c5f-154">새 미들웨어를 삽입 하면 파이프라인에서 정확한 지점 모듈로 처리 하는 이벤트에 따라 달라 집니다 (`BeginRequest`, `EndRequest`등) 및 모듈에는 목록에서 해당 순서 *Web.config*합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-154">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="70c5f-155">앞에서 설명한 대로 ASP.NET Core에 없는 응용 프로그램 수명 주기를 없는 언급 하는 미들웨어에서 처리 되는 응답 순서 모듈에서 사용 하는 순서에서 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-155">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="70c5f-156">이 순서 결정 까다롭게를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-156">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="70c5f-157">순서 지정 문제가 되 면 독립적으로 주문할 수 있습니다. 여러 미들웨어 구성 요소에 모듈을 분할할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-157">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="70c5f-158">처리기 코드를 미들웨어로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="70c5f-158">Migrating handler code to middleware</span></span>

<span data-ttu-id="70c5f-159">HTTP 처리기는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-159">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="70c5f-160">ASP.NET Core 프로젝트에서 다음과 유사 하 게 하는 미들웨어로이 변환는 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-160">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="70c5f-161">이 미들웨어는 해당 모듈을 미들웨어와 매우 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-161">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="70c5f-162">유일한 차이점은에 대 한 호출이 같습니다 `_next.Invoke(context)`합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-162">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="70c5f-163">합리적이 지 처리기가 요청 파이프라인의 끝에는 없는 다음 미들웨어를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-163">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="70c5f-164">마이그레이션 처리기 삽입 요청 파이프라인</span><span class="sxs-lookup"><span data-stu-id="70c5f-164">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="70c5f-165">HTTP 처리기 구성 이루어집니다 *Web.config* 같습니다 및:</span><span class="sxs-lookup"><span data-stu-id="70c5f-165">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="70c5f-166">새 처리기 미들웨어를 요청 파이프라인에 추가 하 여이 변환할 수 있습니다 프로그램 `Startup` 클래스, 모듈에서 변환 하는 미들웨어와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-166">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="70c5f-167">해당 방법 사용 하 여 문제는 새 처리기 미들웨어에 모든 요청을 보내기는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-167">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="70c5f-168">그러나 미들웨어를 연결할 지정된 된 확장을 사용 하 여 요청 하려는 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-168">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="70c5f-169">HTTP 처리기를 사용 하 여 사용 했던 동일한 기능을 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-169">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="70c5f-170">한 가지 해결책은 지정된 된 확장을 사용 하 여 요청에 대 한 파이프라인 분기를 사용 하 여는 `MapWhen` 확장 메서드.</span><span class="sxs-lookup"><span data-stu-id="70c5f-170">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="70c5f-171">이렇게 하면 동일한 `Configure` 메서드는 다른 미들웨어를 추가 하는 위치:</span><span class="sxs-lookup"><span data-stu-id="70c5f-171">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="70c5f-172">`MapWhen` 이러한 매개 변수를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-172">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="70c5f-173">사용 하는 람다는 `HttpContext` 반환 `true` 요청 분기 아래로 이동 해야 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="70c5f-173">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="70c5f-174">즉, 요청 뿐 아니라 해당 확장에서 뿐만 아니라 요청 헤더, 쿼리 문자열 매개 변수를 기반으로 분기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-174">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="70c5f-175">사용 하는 람다는 `IApplicationBuilder` 분기에 대 한 모든 미들웨어를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-175">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="70c5f-176">즉, 처리기 미들웨어 앞에 분기에 추가 하는 미들웨어를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-176">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="70c5f-177">분기 모든 요청에서 호출 될 미들웨어 파이프라인에 추가 분기에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-177">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="70c5f-178">로드 옵션 패턴을 사용 하 여 미들웨어 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-178">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="70c5f-179">일부 모듈 및 처리기에 저장 되는 구성 옵션을 사용할 *Web.config*합니다. 그러나 ASP.NET Core에서 새 구성 모델을는 대신 *Web.config*합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-179">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="70c5f-180">새 [구성 시스템](xref:fundamentals/configuration/index) 이 문제를 해결 하려면 다음이 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-180">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="70c5f-181">에 표시 된 대로 옵션을 미들웨어로 직접 삽입 합니다 [다음 섹션에서는](#loading-middleware-options-through-direct-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-181">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="70c5f-182">사용 된 [옵션 패턴](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="70c5f-182">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="70c5f-183">예를 들어 미들웨어 옵션을 보유 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-183">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="70c5f-184">옵션 값을 저장</span><span class="sxs-lookup"><span data-stu-id="70c5f-184">Store the option values</span></span>

   <span data-ttu-id="70c5f-185">구성 시스템을 사용 하면 옵션 어디서 나 원하는 값을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-185">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="70c5f-186">그러나 사용 하 여 대부분의 사이트 *appsettings.json*이므로 그 방법을 알아보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-186">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="70c5f-187">*MyMiddlewareOptionsSection* 섹션 이름은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-187">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="70c5f-188">옵션 클래스의 이름으로 동일할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-188">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="70c5f-189">옵션 값 옵션 클래스를 사용 하 여 연결</span><span class="sxs-lookup"><span data-stu-id="70c5f-189">Associate the option values with the options class</span></span>

    <span data-ttu-id="70c5f-190">옵션 패턴 ASP.NET Core의 종속성 주입 프레임 워크를 사용 하 여 연결 옵션의 유형을 (같은 `MyMiddlewareOptions`)와 `MyMiddlewareOptions` 실제 옵션 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-190">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="70c5f-191">업데이트 프로그램 `Startup` 클래스:</span><span class="sxs-lookup"><span data-stu-id="70c5f-191">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="70c5f-192">사용 중인 경우 *appsettings.json*에서 구성 작성기에 추가 된 `Startup` 생성자:</span><span class="sxs-lookup"><span data-stu-id="70c5f-192">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="70c5f-193">옵션 서비스를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-193">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="70c5f-194">옵션 클래스를 사용 하 여 옵션을 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-194">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="70c5f-195">미들웨어 생성자에 대 한 옵션을 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-195">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="70c5f-196">이 옵션을 컨트롤러에 삽입 하는 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-196">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="70c5f-197">합니다 [UseMiddleware](#http-modules-usemiddleware) 미들웨어를 추가 하는 확장 메서드는 `IApplicationBuilder` 종속성 주입을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-197">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="70c5f-198">이에 국한 되지 않습니다 `IOptions` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-198">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="70c5f-199">이러한 방식으로 미들웨어가 필요한 다른 모든 개체를 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-199">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="70c5f-200">로드 직접 주입을 통해 미들웨어 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-200">Loading middleware options through direct injection</span></span>

<span data-ttu-id="70c5f-201">옵션 패턴 옵션 값 및 소비자 간에 느슨한 연결 생성 되는 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-201">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="70c5f-202">실제 옵션 값을 사용 하는 옵션 클래스를 연결한 후 다른 클래스는 종속성 주입 프레임 워크를 통해 옵션에 대 한 액세스를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-202">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="70c5f-203">옵션 값을 전달할 필요가 없습니다 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-203">There's no need to pass around options values.</span></span>

<span data-ttu-id="70c5f-204">이 세분화 하지만 다양 한 옵션을 사용 하 여 동일한 미들웨어를 두 번 사용 하려는 경우.</span><span class="sxs-lookup"><span data-stu-id="70c5f-204">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="70c5f-205">예를 들어 인증 미들웨어를 다양 한 역할을 허용 하는 다른 분기에서 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-205">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="70c5f-206">하나의 옵션 클래스를 사용 하 여 두 가지 다른 옵션 개체를 연결할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-206">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="70c5f-207">솔루션에 실제 옵션 값이 있는 옵션 개체를 가져오는 데는 프로그램 `Startup` 클래스 및 미들웨어의 각 인스턴스를 직접 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-207">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="70c5f-208">두 번째 키를 추가 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="70c5f-208">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="70c5f-209">옵션 중 두 번째 집합을 추가 하는 *appsettings.json* 파일을 고유 하 게 식별 하는 데 새 키를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-209">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="70c5f-210">옵션 값을 검색 하 고 미들웨어에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-210">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="70c5f-211">`Use...` 확장 메서드 (파이프라인에 미들웨어를 추가) 옵션 값을 전달할 수 있는 논리 위치는:</span><span class="sxs-lookup"><span data-stu-id="70c5f-211">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="70c5f-212">미들웨어 옵션 매개 변수를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-212">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="70c5f-213">오버 로드를 제공 합니다 `Use...` 확장 메서드 (옵션 매개 변수를 사용 하 고 전달 `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="70c5f-213">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="70c5f-214">때 `UseMiddleware` 라고 매개 변수를 사용 하 여 해당 매개 변수를 전달 미들웨어 생성자에 미들웨어 개체를 인스턴스화할 때.</span><span class="sxs-lookup"><span data-stu-id="70c5f-214">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="70c5f-215">이 옵션 개체에 배치 되는 방법을 참고는 `OptionsWrapper` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-215">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="70c5f-216">이 구현 `IOptions`처럼 미들웨어 생성자가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-216">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="70c5f-217">새 HttpContext로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="70c5f-217">Migrating to the new HttpContext</span></span>

<span data-ttu-id="70c5f-218">앞서 살펴본 하는 `Invoke` 형식의 매개 변수를 사용 하는 미들웨어에서 메서드 `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="70c5f-218">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="70c5f-219">`HttpContext` ASP.NET Core에서 크게 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-219">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="70c5f-220">이 섹션에서는 자주 사용 하는 속성을 변환 하는 방법을 보여 줍니다 [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) 새 `Microsoft.AspNetCore.Http.HttpContext`합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-220">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="70c5f-221">HttpContext</span><span class="sxs-lookup"><span data-stu-id="70c5f-221">HttpContext</span></span>

<span data-ttu-id="70c5f-222">**HttpContext.Items** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-222">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="70c5f-223">**고유한 요청 ID (System.Web.HttpContext 해당)**</span><span class="sxs-lookup"><span data-stu-id="70c5f-223">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="70c5f-224">제공 된 고유 id 각 요청에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-224">Gives you a unique id for each request.</span></span> <span data-ttu-id="70c5f-225">로그에 포함 하도록 매우 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-225">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="70c5f-226">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="70c5f-226">HttpContext.Request</span></span>

<span data-ttu-id="70c5f-227">**HttpContext.Request.HttpMethod** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-227">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="70c5f-228">**HttpContext.Request.QueryString** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-228">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="70c5f-229">**HttpContext.Request.Url** 하 고 **HttpContext.Request.RawUrl** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-229">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="70c5f-230">**HttpContext.Request.IsSecureConnection** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-230">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="70c5f-231">**HttpContext.Request.UserHostAddress** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-231">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="70c5f-232">**HttpContext.Request.Cookies** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-232">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="70c5f-233">**HttpContext.Request.RequestContext.RouteData** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="70c5f-234">**HttpContext.Request.Headers** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-234">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="70c5f-235">**HttpContext.Request.UserAgent** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-235">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="70c5f-236">**HttpContext.Request.UrlReferrer** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-236">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="70c5f-237">**HttpContext.Request.ContentType** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-237">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="70c5f-238">**HttpContext.Request.Form** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-238">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="70c5f-239">콘텐츠 하위 형식이 있는 경우에 양식 값을 읽을 *x-www-형식-urlencoded* 하거나 *양식 데이터*입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-239">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="70c5f-240">**HttpContext.Request.InputStream** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-240">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="70c5f-241">이 코드는 처리기 형식 미들웨어 파이프라인의 끝 에서만 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-241">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="70c5f-242">요청당 한 번만 위에 표시 된 대로 원시 본문을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-242">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="70c5f-243">첫 번째 읽기 후 본문을 읽는 동안 미들웨어는 빈 본문을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-243">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="70c5f-244">이를 폼 앞에 표시 된 것 처럼 버퍼에서 수행 되기 때문에 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-244">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="70c5f-245">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="70c5f-245">HttpContext.Response</span></span>

<span data-ttu-id="70c5f-246">**HttpContext.Response.Status** 하 고 **HttpContext.Response.StatusDescription** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-246">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="70c5f-247">**HttpContext.Response.ContentEncoding** 하 고 **HttpContext.Response.ContentType** 로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-247">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="70c5f-248">**HttpContext.Response.ContentType** 에서 자체도로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-248">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="70c5f-249">**HttpContext.Response.Output** 로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-249">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="70c5f-250">**HttpContext.Response.TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="70c5f-250">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="70c5f-251">파일 처리 부분은 [여기](../fundamentals/request-features.md#middleware-and-request-features)합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-251">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="70c5f-252">**HttpContext.Response.Headers**</span><span class="sxs-lookup"><span data-stu-id="70c5f-252">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="70c5f-253">응답 헤더를 보내는 것은 복잡 팩트를 설정할 경우 응답 본문에 아무 것도 기록 된 후에 전송 되지 것입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-253">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="70c5f-254">솔루션은 오른쪽 응답 시작에 쓰기 전에 호출 되는 콜백 메서드를 설정 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-254">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="70c5f-255">시작 부분에 가장 잘 이루어집니다는 `Invoke` 미들웨어에서 메서드.</span><span class="sxs-lookup"><span data-stu-id="70c5f-255">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="70c5f-256">에 대 한 응답 헤더를 설정 하는이 콜백 메서드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-256">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="70c5f-257">다음 코드에서는 호출 하는 콜백 메서드 설정 `SetHeaders`:</span><span class="sxs-lookup"><span data-stu-id="70c5f-257">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="70c5f-258">`SetHeaders` 콜백 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-258">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="70c5f-259">**HttpContext.Response.Cookies**</span><span class="sxs-lookup"><span data-stu-id="70c5f-259">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="70c5f-260">브라우저에서 쿠키 이동 된 *Set-cookie* 응답 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-260">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="70c5f-261">결과적으로, 쿠키를 보낼 응답 헤더를 보내는 데 사용 되는 동일한 콜백이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-261">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="70c5f-262">`SetCookies` 콜백 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="70c5f-262">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="70c5f-263">추가 자료</span><span class="sxs-lookup"><span data-stu-id="70c5f-263">Additional resources</span></span>

* [<span data-ttu-id="70c5f-264">HTTP 처리기 및 HTTP 모듈 개요</span><span class="sxs-lookup"><span data-stu-id="70c5f-264">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="70c5f-265">구성</span><span class="sxs-lookup"><span data-stu-id="70c5f-265">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="70c5f-266">애플리케이션 시작</span><span class="sxs-lookup"><span data-stu-id="70c5f-266">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="70c5f-267">미들웨어</span><span class="sxs-lookup"><span data-stu-id="70c5f-267">Middleware</span></span>](xref:fundamentals/middleware/index)
