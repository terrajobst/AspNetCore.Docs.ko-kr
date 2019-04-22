---
title: ASP.NET Core에서 앱 시작
author: tdykstra
description: ASP.NET Core의 시작 클래스에서 서비스 및 앱의 요청 파이프라인을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/17/2019
uid: fundamentals/startup
ms.openlocfilehash: 362186be6feeeefeca3c56688ee6420de5fb9659
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468626"
---
# <a name="app-startup-in-aspnet-core"></a><span data-ttu-id="82f0a-103">ASP.NET Core에서 앱 시작</span><span class="sxs-lookup"><span data-stu-id="82f0a-103">App startup in ASP.NET Core</span></span>

<span data-ttu-id="82f0a-104">작성자: [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex) 및 [Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="82f0a-104">By [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="82f0a-105">`Startup` 클래스는 서비스와 응용 프로그램의 요청 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-105">The `Startup` class configures services and the app's request pipeline.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="82f0a-106">시작 클래스</span><span class="sxs-lookup"><span data-stu-id="82f0a-106">The Startup class</span></span>

<span data-ttu-id="82f0a-107">ASP.NET Core 앱은 규칙에 따라 `Startup`으로 이름이 지정된 `Startup` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="82f0a-108">`Startup` 클래스:</span><span class="sxs-lookup"><span data-stu-id="82f0a-108">The `Startup` class:</span></span>

* <span data-ttu-id="82f0a-109">선택적으로 앱의 *서비스*를 구성하는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-109">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="82f0a-110">서비스는 앱 기능을 제공하는 재사용 가능한 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-110">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="82f0a-111">서비스는 `ConfigureServices`에 구성(&mdash;*등록*된 것으로도 설명&mdash;)되고 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 앱 전체에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-111">Services are configured&mdash;also described as *registered*&mdash;in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="82f0a-112">앱의 요청 처리 파이프라인을 만드는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-112">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="82f0a-113">`ConfigureServices` 및 `Configure`는 앱 시작 시 런타임에 의해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-113">`ConfigureServices` and `Configure` are called by the runtime when the app starts:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup1.cs?highlight=4,10)]

<span data-ttu-id="82f0a-114">앱의 [호스트](xref:fundamentals/index#host)가 빌드될 때 `Startup` 클래스가 앱에 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-114">The `Startup` class is specified to the app when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="82f0a-115">앱의 호스트는 `Build`가 `Program` 클래스의 호스트 작성기에서 호출될 때 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-115">The app's host is built when `Build` is called on the host builder in the `Program` class.</span></span> <span data-ttu-id="82f0a-116">`Startup` 클래스는 일반적으로 호스트 작성기에서 [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) 메서드를 호출하여 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-116">The `Startup` class is usually specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=10)]

<span data-ttu-id="82f0a-117">호스트는 `Startup` 클래스 생성자에 사용할 수 있는 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-117">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="82f0a-118">앱은 `ConfigureServices`를 통해 추가 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-118">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="82f0a-119">그러면 호스트 및 앱 서비스 모두를 `Configure` 및 앱 전체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-119">Both the host and app services are then available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="82f0a-120">`Startup` 클래스에 대한 [종속성 주입](xref:fundamentals/dependency-injection)의 일반적인 용도는 다음을 삽입하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-120">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject:</span></span>

* <span data-ttu-id="82f0a-121">환경별로 서비스를 구성하는 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="82f0a-121"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> to configure services by environment.</span></span>
* <span data-ttu-id="82f0a-122">구성을 읽는 <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="82f0a-122"><xref:Microsoft.Extensions.Configuration.IConfiguration> to read configuration.</span></span>
* <span data-ttu-id="82f0a-123">`Startup.ConfigureServices`의 로거를 만드는 <xref:Microsoft.Extensions.Logging.ILoggerFactory>.</span><span class="sxs-lookup"><span data-stu-id="82f0a-123"><xref:Microsoft.Extensions.Logging.ILoggerFactory> to create a logger in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

<span data-ttu-id="82f0a-124">`IHostingEnvironment` 삽입의 대안은 규칙 기반 접근 방식을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-124">An alternative to injecting `IHostingEnvironment` is to use a conventions-based approach.</span></span> <span data-ttu-id="82f0a-125">앱에서 다양한 환경(예: `StartupDevelopment`)에 대해 별도의 `Startup` 클래스를 정의할 때 런타임에 적절한 `Startup` 클래스가 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-125">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="82f0a-126">해당 이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-126">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="82f0a-127">앱이 개발 환경에서 실행되고 `Startup` 클래스 및 `StartupDevelopment` 클래스 모두를 포함하는 경우 `StartupDevelopment` 클래스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-127">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="82f0a-128">자세한 내용은 [여러 환경 사용](xref:fundamentals/environments#environment-based-startup-class-and-methods)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="82f0a-128">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="82f0a-129">호스트에 대한 자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="82f0a-129">To learn more about the host, see [The host](xref:fundamentals/index#host).</span></span> <span data-ttu-id="82f0a-130">시작하는 동안 오류를 처리하는 방법은 [시작 예외 처리](xref:fundamentals/error-handling#startup-exception-handling)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="82f0a-130">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="82f0a-131">ConfigureServices 메서드</span><span class="sxs-lookup"><span data-stu-id="82f0a-131">The ConfigureServices method</span></span>

<span data-ttu-id="82f0a-132"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-132">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="82f0a-133">선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-133">Optional.</span></span>
* <span data-ttu-id="82f0a-134">`Configure` 메서드 전에 호스트에 의해 호출되어 앱의 서비스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-134">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="82f0a-135">여기서 [구성 옵션](xref:fundamentals/configuration/index)은 규칙에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-135">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="82f0a-136">일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 다음, 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-136">The typical pattern is to call all the `Add{Service}` methods and then call all of the `services.Configure{Service}` methods.</span></span> <span data-ttu-id="82f0a-137">예는 [ID 서비스 구성](xref:security/authentication/identity#pw)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="82f0a-137">For example, see [Configure Identity services](xref:security/authentication/identity#pw).</span></span>

<span data-ttu-id="82f0a-138">호스트는 `Startup` 메서드가 호출되기 전에 일부 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-138">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="82f0a-139">자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="82f0a-139">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="82f0a-140">대부분의 설치가 필요한 기능의 경우 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>에 `Add{Service}` 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-140">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="82f0a-141">일반적인 ASP.NET Core 앱은 Entity Framework, ID 및 MVC에 대한 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-141">A typical ASP.NET Core app registers services for Entity Framework, Identity, and MVC:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

<span data-ttu-id="82f0a-142">서비스 컨테이너에 서비스를 추가하면 앱 내 및 `Configure` 메서드에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-142">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="82f0a-143">서비스는 [종속성 주입](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-143">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

<span data-ttu-id="82f0a-144">`SetCompatibilityVersion`에 대한 자세한 내용은 [SetCompatibilityVersion](xref:mvc/compatibility-version)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="82f0a-144">See [SetCompatibilityVersion](xref:mvc/compatibility-version) for more information on `SetCompatibilityVersion`.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="82f0a-145">Configure 메서드</span><span class="sxs-lookup"><span data-stu-id="82f0a-145">The Configure method</span></span>

<span data-ttu-id="82f0a-146"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드는 앱이 HTTP 요청에 응답하는 방식을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-146">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="82f0a-147">요청 파이프라인은 [미들웨어](xref:fundamentals/middleware/index) 구성 요소를 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 인스턴스에 추가하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-147">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="82f0a-148">`IApplicationBuilder`는 `Configure` 메서드에 사용할 수 있지만 서비스 컨테이너에 등록되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-148">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="82f0a-149">호스팅은 `IApplicationBuilder`를 만들고 `Configure`에 직접 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-149">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="82f0a-150">[ASP.NET Core 템플릿](/dotnet/core/tools/dotnet-new)은 다음을 지원하는 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-150">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="82f0a-151">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="82f0a-151">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="82f0a-152">예외 처리기</span><span class="sxs-lookup"><span data-stu-id="82f0a-152">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="82f0a-153">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="82f0a-153">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="82f0a-154">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="82f0a-154">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="82f0a-155">정적 파일</span><span class="sxs-lookup"><span data-stu-id="82f0a-155">Static files</span></span>](xref:fundamentals/static-files)
* [<span data-ttu-id="82f0a-156">GDPR(일반 데이터 보호 규정)</span><span class="sxs-lookup"><span data-stu-id="82f0a-156">General Data Protection Regulation (GDPR)</span></span>](xref:security/gdpr)
* <span data-ttu-id="82f0a-157">ASP.NET Core [MVC](xref:mvc/overview) 및 [Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="82f0a-157">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

<span data-ttu-id="82f0a-158">각 `Use` 확장 메서드는 요청 파이프라인에 하나 이상의 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-158">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="82f0a-159">예를 들어 `UseMvc` 확장 메서드는 [라우팅 미들웨어](xref:fundamentals/routing)를 요청 파이프라인에 추가하고 기본 처리기로 [MVC](xref:mvc/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-159">For instance, the `UseMvc` extension method adds [Routing Middleware](xref:fundamentals/routing) to the request pipeline and configures [MVC](xref:mvc/overview) as the default handler.</span></span>

<span data-ttu-id="82f0a-160">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 다음 구성 요소를 호출하거나 적절한 경우 체인을 단락(short-circuiting)하는 일을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-160">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span> <span data-ttu-id="82f0a-161">미들웨어 체인을 따라 단락(short-circuiting)이 발생하지 않으면, 각 미들웨어는 클라이언트에 전송되기 전에 요청을 처리할 수 있는 두 번째 기회를 얻게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-161">If short-circuiting doesn't occur along the middleware chain, each middleware has a second chance to process the request before it's sent to the client.</span></span>

<span data-ttu-id="82f0a-162">`IHostingEnvironment` 및 `ILoggerFactory`와 같은 추가 서비스는 `Configure` 메서드 서명에서 지정될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-162">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, may also be specified in the `Configure` method signature.</span></span> <span data-ttu-id="82f0a-163">지정되면 사용 가능한 경우 추가 서비스가 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-163">When specified, additional services are injected if they're available.</span></span>

<span data-ttu-id="82f0a-164">`IApplicationBuilder`를 사용하는 방법 및 미들웨어 처리 순서에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="82f0a-164">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="convenience-methods"></a><span data-ttu-id="82f0a-165">편리한 메서드</span><span class="sxs-lookup"><span data-stu-id="82f0a-165">Convenience methods</span></span>

<span data-ttu-id="82f0a-166">`Startup` 클래스를 사용하지 않고 서비스 및 요청 처리 파이프라인을 구성하려면 호스트 작성기에서 `ConfigureServices` 및 `Configure` 편의성 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-166">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="82f0a-167">`ConfigureServices`에 대한 여러 호출은 서로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-167">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="82f0a-168">여러 `Configure` 메서드 호출이 있는 경우 마지막 `Configure` 호출이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-168">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="82f0a-169">시작 필터로 시작 확장</span><span class="sxs-lookup"><span data-stu-id="82f0a-169">Extend Startup with startup filters</span></span>

<span data-ttu-id="82f0a-170"><xref:Microsoft.AspNetCore.Hosting.IStartupFilter>를 사용하여 앱의 [구성](#the-configure-method) 미들웨어 파이프라인의 시작 또는 끝에 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-170">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> to configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline.</span></span> <span data-ttu-id="82f0a-171">`IStartupFilter`는 미들웨어가 앱의 요청 처리 파이프라인의 시작 또는 끝에 라이브러리에 의해 추가되는 미들웨어 전이나 후에 실행되도록 하는데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-171">`IStartupFilter` is useful to ensure that a middleware runs before or after middleware added by libraries at the start or end of the app's request processing pipeline.</span></span>

<span data-ttu-id="82f0a-172">`IStartupFilter`는 `Action<IApplicationBuilder>`를 받고 반환하는 단일 메서드, <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-172">`IStartupFilter` implements a single method, <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="82f0a-173"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>는 앱의 요청 파이프라인을 구성하는 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-173">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="82f0a-174">자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="82f0a-174">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="82f0a-175">각 `IStartupFilter`는 요청 파이프라인에서 하나 이상의 미들웨어를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-175">Each `IStartupFilter` implements one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="82f0a-176">필터는 서비스 컨테이너에 추가된 순서 대로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-176">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="82f0a-177">필터는 다음 필터에 컨트롤을 전달하기 전이나 후에 미들웨어를 추가할 수 있으므로 앱 파이프라인의 시작 또는 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-177">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="82f0a-178">다음 예제에서는 `IStartupFilter`를 사용하여 미들웨어를 등록하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-178">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span>

<span data-ttu-id="82f0a-179">`RequestSetOptionsMiddleware` 미들웨어는 쿼리 문자열 매개 변수에서 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-179">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

<span data-ttu-id="82f0a-180">`RequestSetOptionsMiddleware`는 `RequestSetOptionsStartupFilter` 클래스에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-180">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="82f0a-181">`IStartupFilter`는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>의 서비스 컨테이너에 등록되고 `Startup`은 `Startup` 클래스 외부에서 보강됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-181">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> and augments `Startup` from outside of the `Startup` class:</span></span>

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

<span data-ttu-id="82f0a-182">`option`에 대한 쿼리 문자열 매개 변수가 제공되는 경우 미들웨어는 MVC 미들웨어가 응답을 렌더링하기 전에 값 할당을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-182">When a query string parameter for `option` is provided, the middleware processes the value assignment before the MVC middleware renders the response:</span></span>

![렌더링된 인덱스 페이지를 표시하는 브라우저 창](startup/_static/index.png)

<span data-ttu-id="82f0a-185">미들웨어 실행 순서는 `IStartupFilter` 등록 순서로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-185">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="82f0a-186">여러 `IStartupFilter` 구현은 동일한 개체와 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-186">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="82f0a-187">순서 지정이 중요한 경우 해당 미들웨어가 실행해야 하는 순서와 일치하도록 해당 `IStartupFilter` 서비스 등록의 순서를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-187">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="82f0a-188">라이브러리는 `IStartupFilter`로 등록된 다른 앱 미들웨어 전이나 후에 실행하는 하나 이상의 `IStartupFilter` 구현으로 미들웨어를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-188">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="82f0a-189">라이브러리의 `IStartupFilter`에 의해 추가되는 미들웨어 전에 `IStartupFilter` 미들웨어를 호출하려면 라이브러리가 서비스 컨테이너에 추가되기 전에 서비스 등록의 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-189">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`, position the service registration before the library is added to the service container.</span></span> <span data-ttu-id="82f0a-190">나중에 호출하려면 라이브러리가 추가된 후에 서비스 등록의 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-190">To invoke it afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="82f0a-191">외부 어셈블리의 시작 시 구성 추가</span><span class="sxs-lookup"><span data-stu-id="82f0a-191">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="82f0a-192"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>구현에서는 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="82f0a-192">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="82f0a-193">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="82f0a-193">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82f0a-194">추가 자료</span><span class="sxs-lookup"><span data-stu-id="82f0a-194">Additional resources</span></span>

* [<span data-ttu-id="82f0a-195">호스트</span><span class="sxs-lookup"><span data-stu-id="82f0a-195">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>
