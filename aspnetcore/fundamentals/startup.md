---
title: ASP.NET Core에서 앱 시작
author: rick-anderson
description: ASP.NET Core의 시작 클래스에서 서비스 및 앱의 요청 파이프라인을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2019
uid: fundamentals/startup
ms.openlocfilehash: 081eaa772d136477a37a3392877886327e0cda7c
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634045"
---
# <a name="app-startup-in-aspnet-core"></a><span data-ttu-id="8634c-103">ASP.NET Core에서 앱 시작</span><span class="sxs-lookup"><span data-stu-id="8634c-103">App startup in ASP.NET Core</span></span>

<span data-ttu-id="8634c-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex) 및 [Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="8634c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="8634c-105">`Startup` 클래스는 서비스와 응용 프로그램의 요청 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-105">The `Startup` class configures services and the app's request pipeline.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="8634c-106">시작 클래스</span><span class="sxs-lookup"><span data-stu-id="8634c-106">The Startup class</span></span>

<span data-ttu-id="8634c-107">ASP.NET Core 앱은 규칙에 따라 `Startup`으로 이름이 지정된 `Startup` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="8634c-108">`Startup` 클래스는:</span><span class="sxs-lookup"><span data-stu-id="8634c-108">The `Startup` class:</span></span>

* <span data-ttu-id="8634c-109">선택적으로 앱의 *서비스*를 구성하는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-109">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="8634c-110">서비스는 앱 기능을 제공하는 재사용 가능한 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-110">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="8634c-111">서비스는 `ConfigureServices`에서 *등록*되며 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 앱 전체에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-111">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="8634c-112">앱의 요청 처리 파이프라인을 만드는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-112">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="8634c-113">`ConfigureServices` 및 `Configure`는 앱 시작 시 ASP.NET Core 런타임에 의해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-113">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="8634c-114">앞의 샘플은 [Razor Pages](xref:razor-pages/index)에 해당하며 MVC 버전도 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-114">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

::: moniker-end

<span data-ttu-id="8634c-115">앱의 [호스트](xref:fundamentals/index#host)가 빌드될 때 `Startup` 클래스가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-115">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="8634c-116">`Startup` 클래스는 일반적으로 호스트 작성기에서 [`WebHostBuilderExtensions.UseStartup<TStartup>`](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) 메서드를 호출하여 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-116">The `Startup` class is typically specified by calling the [`WebHostBuilderExtensions.UseStartup<TStartup>`](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="8634c-117">호스트는 `Startup` 클래스 생성자에 사용할 수 있는 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-117">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="8634c-118">앱은 `ConfigureServices`를 통해 추가 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-118">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="8634c-119">그러면 호스트 및 앱 서비스 모두를 `Configure` 및 앱 전체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-119">Both the host and app services are available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="8634c-120">[제네릭 호스트](xref:fundamentals/host/generic-host)(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-120">Only the following service types can be injected into the `Startup` constructor when using the [Generic Host](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

<span data-ttu-id="8634c-121">`Configure` 메서드가 호출될 때까지 대부분의 서비스를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-121">Most services are not available until the `Configure` method is called.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8634c-122">호스트는 `Startup` 클래스 생성자에 사용할 수 있는 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-122">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="8634c-123">앱은 `ConfigureServices`를 통해 추가 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-123">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="8634c-124">그러면 호스트 및 앱 서비스 모두를 `Configure` 및 앱 전체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-124">Both the host and app services are then available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="8634c-125">`Startup` 클래스에 대한 [종속성 주입](xref:fundamentals/dependency-injection)의 일반적인 용도는 다음을 삽입하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-125">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject:</span></span>

* <span data-ttu-id="8634c-126">환경별로 서비스를 구성하는 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="8634c-126"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> to configure services by environment.</span></span>
* <span data-ttu-id="8634c-127">구성을 읽는 <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="8634c-127"><xref:Microsoft.Extensions.Configuration.IConfiguration> to read configuration.</span></span>
* <span data-ttu-id="8634c-128">`Startup.ConfigureServices`에서 로거를 만드는 <xref:Microsoft.Extensions.Logging.ILoggerFactory>.</span><span class="sxs-lookup"><span data-stu-id="8634c-128"><xref:Microsoft.Extensions.Logging.ILoggerFactory> to create a logger in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

<span data-ttu-id="8634c-129">`Configure` 메서드가 호출될 때까지 대부분의 서비스를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-129">Most services are not available until the `Configure` method is called.</span></span>

::: moniker-end

### <a name="multiple-startup"></a><span data-ttu-id="8634c-130">여러 Startup</span><span class="sxs-lookup"><span data-stu-id="8634c-130">Multiple Startup</span></span>

<span data-ttu-id="8634c-131">앱에서 다양한 환경(예: `StartupDevelopment`)에 대해 별도의 `Startup` 클래스를 정의하면 런타임에 적절한 `Startup` 클래스가 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-131">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="8634c-132">이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-132">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="8634c-133">앱이 개발 환경에서 실행되고 `Startup` 클래스 및 `StartupDevelopment` 클래스 모두를 포함하는 경우 `StartupDevelopment` 클래스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-133">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="8634c-134">자세한 내용은 [여러 환경 사용](xref:fundamentals/environments#environment-based-startup-class-and-methods)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8634c-134">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="8634c-135">호스트에 대한 자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8634c-135">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="8634c-136">시작하는 동안 오류를 처리하는 방법은 [시작 예외 처리](xref:fundamentals/error-handling#startup-exception-handling)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8634c-136">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="8634c-137">ConfigureServices 메서드</span><span class="sxs-lookup"><span data-stu-id="8634c-137">The ConfigureServices method</span></span>

<span data-ttu-id="8634c-138"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-138">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="8634c-139">선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-139">Optional.</span></span>
* <span data-ttu-id="8634c-140">`Configure` 메서드 전에 호스트에 의해 호출되어 앱의 서비스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-140">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="8634c-141">여기서 [구성 옵션](xref:fundamentals/configuration/index)이 규칙에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-141">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="8634c-142">호스트는 `Startup` 메서드가 호출되기 전에 일부 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-142">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="8634c-143">자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8634c-143">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="8634c-144">실질적인 설정이 필요한 기능의 경우 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>에 `Add{Service}` 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-144">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="8634c-145">예를 들어 **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores 및 **Add**RazorPages가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-145">For example, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores, and **Add**RazorPages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

::: moniker-end

<span data-ttu-id="8634c-146">서비스 컨테이너에 서비스를 추가하면 앱 내 및 `Configure` 메서드에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-146">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="8634c-147">서비스는 [종속성 주입](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-147">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8634c-148">`SetCompatibilityVersion`에 대한 자세한 내용은 [SetCompatibilityVersion](xref:mvc/compatibility-version)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8634c-148">See [SetCompatibilityVersion](xref:mvc/compatibility-version) for more information on `SetCompatibilityVersion`.</span></span>

::: moniker-end

## <a name="the-configure-method"></a><span data-ttu-id="8634c-149">Configure 메서드</span><span class="sxs-lookup"><span data-stu-id="8634c-149">The Configure method</span></span>

<span data-ttu-id="8634c-150"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드는 앱이 HTTP 요청에 응답하는 방식을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-150">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="8634c-151">요청 파이프라인은 [미들웨어](xref:fundamentals/middleware/index) 구성 요소를 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 인스턴스에 추가하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-151">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="8634c-152">`IApplicationBuilder`는 `Configure` 메서드에 사용할 수 있지만 서비스 컨테이너에 등록되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-152">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="8634c-153">호스팅이 `IApplicationBuilder`를 만들고 `Configure`에 직접 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-153">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="8634c-154">[ASP.NET Core 템플릿](/dotnet/core/tools/dotnet-new)은 다음을 지원하는 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-154">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="8634c-155">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="8634c-155">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="8634c-156">예외 처리기</span><span class="sxs-lookup"><span data-stu-id="8634c-156">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="8634c-157">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="8634c-157">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="8634c-158">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="8634c-158">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="8634c-159">정적 파일</span><span class="sxs-lookup"><span data-stu-id="8634c-159">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="8634c-160">ASP.NET Core [MVC](xref:mvc/overview) 및 [Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="8634c-160">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="8634c-161">GDPR(일반 데이터 보호 규정)</span><span class="sxs-lookup"><span data-stu-id="8634c-161">General Data Protection Regulation (GDPR)</span></span>](xref:security/gdpr)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="8634c-162">앞의 샘플은 [Razor Pages](xref:razor-pages/index)에 해당하며 MVC 버전도 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-162">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

::: moniker-end

<span data-ttu-id="8634c-163">각 `Use` 확장 메서드는 요청 파이프라인에 하나 이상의 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-163">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="8634c-164">예를 들어 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>는 [정적 파일](xref:fundamentals/static-files)을 제공하도록 [미들웨어](xref:fundamentals/middleware/index)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-164">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="8634c-165">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 다음 구성 요소를 호출하거나 적절한 경우 체인을 단락(short-circuiting)하는 일을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-165">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8634c-166">`IWebHostEnvironment`, `ILoggerFactory` 같은 추가 서비스 또는 `ConfigureServices`에 정의된 항목은 `Configure` 메서드 시그니처에서 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-166">Additional services, such as `IWebHostEnvironment`, `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="8634c-167">이 서비스를 사용할 수 있는 경우 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-167">These services are injected if they're available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8634c-168">`IHostingEnvironment` 및 `ILoggerFactory` 같은 추가 서비스 또는 `ConfigureServices`에 정의된 항목은 `Configure` 메서드 시그니처에서 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-168">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="8634c-169">이 서비스를 사용할 수 있는 경우 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-169">These services are injected if they're available.</span></span>

::: moniker-end

<span data-ttu-id="8634c-170">`IApplicationBuilder`를 사용하는 방법 및 미들웨어 처리 순서에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8634c-170">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="8634c-171">Startup을 사용하지 않고 서비스 구성</span><span class="sxs-lookup"><span data-stu-id="8634c-171">Configure services without Startup</span></span>

<span data-ttu-id="8634c-172">`Startup` 클래스를 사용하지 않고 서비스 및 요청 처리 파이프라인을 구성하려면 호스트 작성기에서 `ConfigureServices` 및 `Configure` 편의성 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-172">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="8634c-173">`ConfigureServices`에 대한 여러 호출은 서로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-173">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="8634c-174">여러 `Configure` 메서드 호출이 있는 경우 마지막 `Configure` 호출이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-174">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

::: moniker-end

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="8634c-175">시작 필터를 이용한 Startup 확장</span><span class="sxs-lookup"><span data-stu-id="8634c-175">Extend Startup with startup filters</span></span>

<span data-ttu-id="8634c-176"><xref:Microsoft.AspNetCore.Hosting.IStartupFilter>는 다음 용도로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-176">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="8634c-177">`Use{Middleware}`를 명시적으로 호출하지 않고 앱의 [Configure](#the-configure-method) 미들웨어 파이프라인의 시작 또는 끝 부분에서 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-177">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="8634c-178">`IStartupFilter`는 ASP.NET Core에서 앱 작성자가 명시적으로 기본 미들웨어를 등록할 필요 없이 파이프라인의 시작 부분에 기본값을 추가하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-178">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="8634c-179">`IStartupFilter`는 앱 작성자를 대신하여 다른 구성 요소 호출 `Use{Middleware}`를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-179">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="8634c-180">`Configure` 메서드의 파이프라인을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-180">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="8634c-181">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*)는 라이브러리에서 추가된 미들웨어 이전 또는 이후에 실행할 미들웨어를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-181">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="8634c-182">`IStartupFilter`는 `Action<IApplicationBuilder>`를 받고 반환하는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-182">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="8634c-183"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>는 앱의 요청 파이프라인을 구성하는 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-183">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="8634c-184">자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8634c-184">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="8634c-185">각 `IStartupFilter`는 요청 파이프라인에서 하나 이상의 미들웨어를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-185">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="8634c-186">필터는 서비스 컨테이너에 추가된 순서대로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-186">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="8634c-187">필터는 다음 필터에 컨트롤을 전달하기 전이나 후에 미들웨어를 추가할 수 있으므로 앱 파이프라인의 시작 또는 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-187">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="8634c-188">다음 예제에서는 `IStartupFilter`를 사용하여 미들웨어를 등록하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-188">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="8634c-189">`RequestSetOptionsMiddleware` 미들웨어는 쿼리 문자열 매개 변수에서 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-189">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

<span data-ttu-id="8634c-190">`RequestSetOptionsMiddleware`는 `RequestSetOptionsStartupFilter` 클래스에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-190">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

<span data-ttu-id="8634c-191">`RequestSetOptionsMiddleware`는 `RequestSetOptionsStartupFilter` 클래스에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-191">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

<span data-ttu-id="8634c-192">`IStartupFilter`는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>의 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-192">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

::: moniker-end

<span data-ttu-id="8634c-193">`option`에 대한 쿼리 문자열 매개 변수가 제공되는 경우 미들웨어는 ASP.NET Core 미들웨어가 응답을 렌더링하기 전에 값 할당을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-193">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="8634c-194">미들웨어 실행 순서는 `IStartupFilter` 등록 순서로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-194">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="8634c-195">여러 `IStartupFilter` 구현은 동일한 개체와 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-195">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="8634c-196">순서 지정이 중요한 경우 해당 미들웨어가 실행해야 하는 순서와 일치하도록 해당 `IStartupFilter` 서비스 등록의 순서를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-196">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="8634c-197">라이브러리는 `IStartupFilter`로 등록된 다른 앱 미들웨어 전이나 후에 실행하는 하나 이상의 `IStartupFilter` 구현으로 미들웨어를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-197">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="8634c-198">라이브러리의 `IStartupFilter`에 의해 추가되는 미들웨어 전에 `IStartupFilter` 미들웨어를 호출하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-198">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="8634c-199">라이브러리가 서비스 컨테이너에 추가되기 전에 서비스 등록의 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-199">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="8634c-200">나중에 호출하려면 라이브러리가 추가된 후에 서비스 등록의 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-200">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="8634c-201">시작 시 외부 어셈블리로부터 구성 추가</span><span class="sxs-lookup"><span data-stu-id="8634c-201">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="8634c-202"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8634c-202">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8634c-203">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8634c-203">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8634c-204">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8634c-204">Additional resources</span></span>

* [<span data-ttu-id="8634c-205">호스트</span><span class="sxs-lookup"><span data-stu-id="8634c-205">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>
