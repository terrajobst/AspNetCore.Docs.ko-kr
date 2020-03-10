---
title: .NET 일반 호스트
author: rick-anderson
description: 앱 시작 및 수명 관리를 담당하는 .NET Core 일반 호스트에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/02/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 6a0ef02db883db3bc91722786cd042ccec092735
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647577"
---
# <a name="net-generic-host"></a><span data-ttu-id="9156e-103">.NET 일반 호스트</span><span class="sxs-lookup"><span data-stu-id="9156e-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9156e-104">이 문서에서는 .NET Core 일반 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 소개하고 이를 사용하는 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="9156e-105">호스트란?</span><span class="sxs-lookup"><span data-stu-id="9156e-105">What's a host?</span></span>

<span data-ttu-id="9156e-106">*호스트*는 다음과 같이 앱의 리소스를 캡슐화하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="9156e-107">DI(종속성 주입)</span><span class="sxs-lookup"><span data-stu-id="9156e-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="9156e-108">로깅</span><span class="sxs-lookup"><span data-stu-id="9156e-108">Logging</span></span>
* <span data-ttu-id="9156e-109">Configuration</span><span class="sxs-lookup"><span data-stu-id="9156e-109">Configuration</span></span>
* <span data-ttu-id="9156e-110">`IHostedService` 구현</span><span class="sxs-lookup"><span data-stu-id="9156e-110">`IHostedService` implementations</span></span>

<span data-ttu-id="9156e-111">호스트가 시작되면 DI 컨테이너에서 찾은 <xref:Microsoft.Extensions.Hosting.IHostedService>의 각 구현 시 `IHostedService.StartAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="9156e-112">웹앱에서 `IHostedService` 구현 중 하나는 [HTTP 서버 구현](xref:fundamentals/index#servers)을 시작하는 웹 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="9156e-113">하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="9156e-114">3\.0 이전 버전의 ASP.NET Core에서 [웹 호스트](xref:fundamentals/host/web-host)는 HTTP 워크로드에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="9156e-115">웹 호스트는 더 이상 웹앱용으로 권장되지 않으며 이전 버전과의 호환성을 위해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9156e-116">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="9156e-116">Set up a host</span></span>

<span data-ttu-id="9156e-117">호스트는 일반적으로 `Program` 클래스의 코드로 구성, 빌드 및 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="9156e-118">`Main` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-118">The `Main` method:</span></span>

* <span data-ttu-id="9156e-119">`CreateHostBuilder` 메서드를 호출하여 작성기 개체를 만들고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="9156e-120">작성기 개체에서 `Build` 및 `Run` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="9156e-121">다음은 DI 컨테이너에 단일 *구현이 추가된 비 HTTP 워크로드에 대한*Program.cs`IHostedService` 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="9156e-122">HTTP 워크로드의 경우 `Main` 메서드는 동일하지만 `CreateHostBuilder`는 `ConfigureWebHostDefaults`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="9156e-123">앱에서 Entity Framework Core를 사용하는 경우 `CreateHostBuilder` 메서드의 이름이나 서명을 변경하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="9156e-124">[Entity Framework Core 도구](/ef/core/miscellaneous/cli/)는 앱을 실행하지 않고 호스트를 구성하는 `CreateHostBuilder` 메서드를 찾으려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="9156e-125">자세한 내용은 [디자인 타임 DbContext 만들기](/ef/core/miscellaneous/cli/dbcontext-creation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="9156e-126">기본 작성기 설정</span><span class="sxs-lookup"><span data-stu-id="9156e-126">Default builder settings</span></span>

<span data-ttu-id="9156e-127"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="9156e-128">[콘텐츠 루트](xref:fundamentals/index#content-root)를 <xref:System.IO.Directory.GetCurrentDirectory*>에서 반환된 경로로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="9156e-129">다음에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="9156e-130">접두사가 "DOTNET_"인 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="9156e-130">Environment variables prefixed with "DOTNET_".</span></span>
  * <span data-ttu-id="9156e-131">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="9156e-131">Command-line arguments.</span></span>
* <span data-ttu-id="9156e-132">다음에서 앱 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="9156e-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9156e-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="9156e-134">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="9156e-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="9156e-135">[비밀 관리자](xref:security/app-secrets): 앱이 `Development` 환경에서 실행되는 경우</span><span class="sxs-lookup"><span data-stu-id="9156e-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="9156e-136">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="9156e-136">Environment variables.</span></span>
  * <span data-ttu-id="9156e-137">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="9156e-137">Command-line arguments.</span></span>
* <span data-ttu-id="9156e-138">다음 [로깅](xref:fundamentals/logging/index) 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="9156e-139">Console</span><span class="sxs-lookup"><span data-stu-id="9156e-139">Console</span></span>
  * <span data-ttu-id="9156e-140">Debug</span><span class="sxs-lookup"><span data-stu-id="9156e-140">Debug</span></span>
  * <span data-ttu-id="9156e-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="9156e-141">EventSource</span></span>
  * <span data-ttu-id="9156e-142">EventLog(Windows에서 실행 중인 경우에만)</span><span class="sxs-lookup"><span data-stu-id="9156e-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="9156e-143">환경이 개발 중일 때 [범위 유효성 검사](xref:fundamentals/dependency-injection#scope-validation) 및 [종속성 유효성 검사](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="9156e-144">`ConfigureWebHostDefaults` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="9156e-145">접두사가 "ASPNETCORE_"인 환경 변수에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-145">Loads host configuration from environment variables prefixed with "ASPNETCORE_".</span></span>
* <span data-ttu-id="9156e-146">[Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 설정하고 앱의 호스팅 구성 공급자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="9156e-147">Kestrel 서버의 기본 옵션은 <xref:fundamentals/servers/kestrel#kestrel-options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="9156e-148">[호스트 필터링 미들웨어](xref:fundamentals/servers/kestrel#host-filtering)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="9156e-149">ASPNETCORE_FORWARDEDHEADERS_ENABLED=true인 경우 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if ASPNETCORE_FORWARDEDHEADERS_ENABLED=true.</span></span>
* <span data-ttu-id="9156e-150">IIS 통합을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-150">Enables IIS integration.</span></span> <span data-ttu-id="9156e-151">IIS 기본 옵션은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="9156e-152">이 문서 뒷부분의 [모든 앱 유형에 대한 설정](#settings-for-all-app-types) 및 [웹앱 설정](#settings-for-web-apps) 섹션에서는 기본 작성기 설정을 재정의하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="9156e-153">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="9156e-153">Framework-provided services</span></span>

<span data-ttu-id="9156e-154">자동으로 등록된 서비스에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-154">Services that are registered automatically include the following:</span></span>

* [<span data-ttu-id="9156e-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="9156e-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="9156e-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="9156e-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="9156e-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="9156e-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="9156e-158">프레임워크에서 제공하는 서비스에 대한 자세한 내용은 <xref:fundamentals/dependency-injection#framework-provided-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="9156e-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="9156e-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="9156e-160"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>(이전의 `IApplicationLifetime`) 서비스를 모든 클래스에 삽입하여 사후 시작 및 정상 종료 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="9156e-161">인터페이스의 세 가지 속성은 앱 시작 및 앱 중지 이벤트 처리기 메서드를 등록하는 데 사용되는 취소 토큰입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="9156e-162">인터페이스에는 `StopApplication` 메서드도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="9156e-163">다음 예제는 `IHostApplicationLifetime` 이벤트를 등록하는 `IHostedService` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="9156e-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="9156e-164">IHostLifetime</span></span>

<span data-ttu-id="9156e-165"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 구현은 호스트가 시작될 때와 중지될 때를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="9156e-166">등록된 마지막 구현이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-166">The last implementation registered is used.</span></span>

<span data-ttu-id="9156e-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 `IHostLifetime` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="9156e-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="9156e-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="9156e-169">Ctrl+C/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*>을 호출하여 종료 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-169">Listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="9156e-170">[RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="9156e-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="9156e-171">IHostEnvironment</span></span>

<span data-ttu-id="9156e-172"><xref:Microsoft.Extensions.Hosting.IHostEnvironment> 서비스를 클래스에 삽입하여 다음에 대한 정보를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following:</span></span>

* [<span data-ttu-id="9156e-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9156e-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="9156e-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9156e-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="9156e-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9156e-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="9156e-176">웹앱은 `IHostEnvironment`를 상속하고 [WebRootPath](#webroot)를 추가하는 `IWebHostEnvironment` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9156e-177">호스트 구성</span><span class="sxs-lookup"><span data-stu-id="9156e-177">Host configuration</span></span>

<span data-ttu-id="9156e-178">호스트 구성은 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 구현의 속성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="9156e-179">호스트 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 내부에 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="9156e-180">`ConfigureAppConfiguration` 다음에 `HostBuilderContext.Configuration`이 앱 구성으로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="9156e-181">호스트 구성을 추가하려면 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9156e-182">`ConfigureHostConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9156e-183">호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9156e-184">접두사 `DOTNET_` 및 명령줄 인수가 있는 환경 변수 공급자는 CreateDefaultBuilder에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-184">The environment variable provider with prefix `DOTNET_` and command line args are included by CreateDefaultBuilder.</span></span> <span data-ttu-id="9156e-185">웹앱의 경우 접두사 `ASPNETCORE_`가 있는 환경 변수 공급자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="9156e-186">접두사는 환경 변수를 읽을 때 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9156e-187">예를 들어 `ASPNETCORE_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9156e-188">다음 예제에서는 호스트 구성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="9156e-189">앱 구성</span><span class="sxs-lookup"><span data-stu-id="9156e-189">App configuration</span></span>

<span data-ttu-id="9156e-190">앱 구성은 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9156e-191">`ConfigureAppConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9156e-192">앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="9156e-193">`ConfigureAppConfiguration`에 의해 생성된 구성은 다음 작업을 위해 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)에서 사용 가능하거나 DI의 서비스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="9156e-194">호스트 구성도 앱 구성에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="9156e-195">자세한 내용은 [ASP.NET Core의 구성](xref:fundamentals/configuration/index#configureappconfiguration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="9156e-196">모든 앱 유형에 대한 설정</span><span class="sxs-lookup"><span data-stu-id="9156e-196">Settings for all app types</span></span>

<span data-ttu-id="9156e-197">이 섹션에는 HTTP 및 비 HTTP 워크로드 모두에 적용되는 호스트 설정이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="9156e-198">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="9156e-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9156e-199">ApplicationName</span></span>

<span data-ttu-id="9156e-200">호스트를 생성하는 동안 호스트 구성에서 [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="9156e-201">**키**: applicationName</span><span class="sxs-lookup"><span data-stu-id="9156e-201">**Key**: applicationName</span></span>  
<span data-ttu-id="9156e-202">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="9156e-202">**Type**: *string*</span></span>  
<span data-ttu-id="9156e-203">**기본값**: 앱의 진입점을 포함하는 어셈블리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>
<span data-ttu-id="9156e-204">**환경 변수**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="9156e-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="9156e-205">이 값을 설정하려면 환경 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="9156e-206">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9156e-206">ContentRootPath</span></span>

<span data-ttu-id="9156e-207">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 속성은 콘텐츠 파일 검색을 시작하는 위치를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="9156e-208">경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="9156e-209">**키**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="9156e-209">**Key**: contentRoot</span></span>  
<span data-ttu-id="9156e-210">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="9156e-210">**Type**: *string*</span></span>  
<span data-ttu-id="9156e-211">**기본값**: 앱 어셈블리가 있는 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="9156e-212">**환경 변수**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="9156e-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="9156e-213">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseContentRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="9156e-214">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-214">For more information, see:</span></span>

* [<span data-ttu-id="9156e-215">기본 사항: 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="9156e-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="9156e-216">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9156e-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="9156e-217">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9156e-217">EnvironmentName</span></span>

<span data-ttu-id="9156e-218">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 속성을 임의의 값으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="9156e-219">프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9156e-220">값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-220">Values aren't case sensitive.</span></span>

<span data-ttu-id="9156e-221">**키**: environment</span><span class="sxs-lookup"><span data-stu-id="9156e-221">**Key**: environment</span></span>  
<span data-ttu-id="9156e-222">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="9156e-222">**Type**: *string*</span></span>  
<span data-ttu-id="9156e-223">**기본값**: Production</span><span class="sxs-lookup"><span data-stu-id="9156e-223">**Default**: Production</span></span>  
<span data-ttu-id="9156e-224">**환경 변수**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="9156e-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="9156e-225">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseEnvironment`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="9156e-226">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="9156e-226">ShutdownTimeout</span></span>

<span data-ttu-id="9156e-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*)이 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9156e-228">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-228">The default value is five seconds.</span></span>  <span data-ttu-id="9156e-229">시간 제한 기간 동안 호스트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="9156e-230">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping)을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="9156e-231">중지하지 못한 서비스에 대한 오류를 로깅하면서 호스팅된 서비스 중지를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="9156e-232">모든 호스팅된 서비스가 중지하기 전에 시간 제한 기간이 만료되면 앱이 종료될 때 모든 활성화된 나머지 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="9156e-233">처리를 완료하지 않은 경우에도 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="9156e-234">서비스를 중지하는 데 시간이 더 필요한 경우 시간 제한을 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="9156e-235">**키**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="9156e-235">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="9156e-236">**형식**: *int*</span><span class="sxs-lookup"><span data-stu-id="9156e-236">**Type**: *int*</span></span>  
<span data-ttu-id="9156e-237">**기본값**: 5초 **환경 변수**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="9156e-237">**Default**: 5 seconds **Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="9156e-238">이 값을 설정하려면 환경 변수를 사용하거나 `HostOptions`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="9156e-239">다음 예제에서는 시간 제한을 20초로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="9156e-240">웹앱 설정</span><span class="sxs-lookup"><span data-stu-id="9156e-240">Settings for web apps</span></span>

<span data-ttu-id="9156e-241">일부 호스트 설정은 HTTP 워크로드에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-241">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="9156e-242">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-242">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="9156e-243">이러한 설정에 대해 `IWebHostBuilder`의 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-243">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="9156e-244">확장 메서드를 호출하는 방법을 보여주는 코드 샘플은 다음 예제와 같이 `webBuilder`가 `IWebHostBuilder`의 인스턴스라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-244">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="9156e-245">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="9156e-245">CaptureStartupErrors</span></span>

<span data-ttu-id="9156e-246">`false`인 경우 시작 시 오류가 발생하면 호스트가 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-246">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="9156e-247">`true`이면 호스트가 시작 시 예외를 캡처하고 서버 시작을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-247">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="9156e-248">**키**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="9156e-248">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="9156e-249">**형식**: *bool*(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="9156e-249">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="9156e-250">**기본값**: 기본값이 `true`인 IIS 뒤에 있는 Kestrel로 앱이 실행하지 않는 한 기본값은 `false`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-250">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="9156e-251">**환경 변수**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="9156e-251">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="9156e-252">이 값을 설정하려면 구성을 사용하거나 `CaptureStartupErrors`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-252">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="9156e-253">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="9156e-253">DetailedErrors</span></span>

<span data-ttu-id="9156e-254">활성화하거나 환경이 `Development`인 경우 앱은 자세한 오류를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-254">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="9156e-255">**키**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="9156e-255">**Key**: detailedErrors</span></span>  
<span data-ttu-id="9156e-256">**형식**: *bool*(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="9156e-256">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="9156e-257">**기본값**: false</span><span class="sxs-lookup"><span data-stu-id="9156e-257">**Default**: false</span></span>  
<span data-ttu-id="9156e-258">**환경 변수**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="9156e-258">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="9156e-259">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-259">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="9156e-260">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="9156e-260">HostingStartupAssemblies</span></span>

<span data-ttu-id="9156e-261">시작 시 로드할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-261">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="9156e-262">구성 값의 기본값이 빈 문자열이지만, 호스팅 시작 어셈블리는 항상 앱의 어셈블리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-262">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="9156e-263">호스팅 시작 어셈블리가 제공되는 경우, 시작 시 앱이 일반적인 서비스를 빌드할 때 로드를 위해 앱의 어셈블리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-263">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="9156e-264">**키**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="9156e-264">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="9156e-265">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="9156e-265">**Type**: *string*</span></span>  
<span data-ttu-id="9156e-266">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="9156e-266">**Default**: Empty string</span></span>  
<span data-ttu-id="9156e-267">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9156e-267">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="9156e-268">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-268">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="9156e-269">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="9156e-269">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="9156e-270">시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-270">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="9156e-271">**키**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="9156e-271">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="9156e-272">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="9156e-272">**Type**: *string*</span></span>  
<span data-ttu-id="9156e-273">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="9156e-273">**Default**: Empty string</span></span>  
<span data-ttu-id="9156e-274">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9156e-274">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="9156e-275">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-275">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="9156e-276">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="9156e-276">HTTPS_Port</span></span>

<span data-ttu-id="9156e-277">HTTPS 리디렉션 포트.</span><span class="sxs-lookup"><span data-stu-id="9156e-277">The HTTPS redirect port.</span></span> <span data-ttu-id="9156e-278">[HTTPS 적용](xref:security/enforcing-ssl)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-278">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="9156e-279">**키**: https_port</span><span class="sxs-lookup"><span data-stu-id="9156e-279">**Key**: https_port</span></span>  
<span data-ttu-id="9156e-280">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="9156e-280">**Type**: *string*</span></span>  
<span data-ttu-id="9156e-281">**기본값**: 기본값은 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-281">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="9156e-282">**환경 변수**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="9156e-282">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="9156e-283">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-283">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="9156e-284">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="9156e-284">PreferHostingUrls</span></span>

<span data-ttu-id="9156e-285">호스트가 `IServer` 구현으로 구성된 URL 대신에 `IWebHostBuilder`로 구성된 URL에서 수신 대기할지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-285">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="9156e-286">**키**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="9156e-286">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="9156e-287">**형식**: *bool*(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="9156e-287">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="9156e-288">**기본값**: true</span><span class="sxs-lookup"><span data-stu-id="9156e-288">**Default**: true</span></span>  
<span data-ttu-id="9156e-289">**환경 변수**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="9156e-289">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="9156e-290">이 값을 설정하려면 환경 변수를 사용하거나 `PreferHostingUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-290">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="9156e-291">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="9156e-291">PreventHostingStartup</span></span>

<span data-ttu-id="9156e-292">앱의 어셈블리에 의해 구성된 호스팅 시작 어셈블리를 포함한 호스팅 시작 어셈블리의 자동 로딩을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-292">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="9156e-293">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-293">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="9156e-294">**키**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="9156e-294">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="9156e-295">**형식**: *bool*(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="9156e-295">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="9156e-296">**기본값**: false</span><span class="sxs-lookup"><span data-stu-id="9156e-296">**Default**: false</span></span>  
<span data-ttu-id="9156e-297">**환경 변수**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="9156e-297">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="9156e-298">이 값을 설정하려면 환경 변수를 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-298">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="9156e-299">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="9156e-299">StartupAssembly</span></span>

<span data-ttu-id="9156e-300">`Startup` 클래스를 검색할 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-300">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="9156e-301">**키**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="9156e-301">**Key**: startupAssembly</span></span>  
<span data-ttu-id="9156e-302">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="9156e-302">**Type**: *string*</span></span>  
<span data-ttu-id="9156e-303">**기본값**: 앱의 어셈블리</span><span class="sxs-lookup"><span data-stu-id="9156e-303">**Default**: The app's assembly</span></span>  
<span data-ttu-id="9156e-304">**환경 변수**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="9156e-304">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="9156e-305">이 값을 설정하려면 환경 변수를 사용하거나 `UseStartup`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-305">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="9156e-306">`UseStartup`은 어셈블리 이름(`string`) 또는 형식(`TStartup`)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-306">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="9156e-307">`UseStartup` 메서드가 여러 개 호출된 경우 마지막 메서드가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-307">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="9156e-308">URL</span><span class="sxs-lookup"><span data-stu-id="9156e-308">URLs</span></span>

<span data-ttu-id="9156e-309">서버에서 요청을 수신해야 하는 포트와 프로토콜을 포함하는 세미클론으로 구분된 IP 주소 또는 호스트 주소의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-309">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="9156e-310">예: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="9156e-310">For example, `http://localhost:123`.</span></span> <span data-ttu-id="9156e-311">“\*”를 사용하여 서버가 지정된 포트 및 프로토콜을 사용하는 IP 주소 또는 호스트 이름에서 요청을 수신해야 함을 나타냅니다(예: `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="9156e-311">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="9156e-312">프로토콜(`http://` 또는 `https://`)은 각 URL에 포함되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-312">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="9156e-313">지원되는 형식은 서버마다 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-313">Supported formats vary among servers.</span></span>

<span data-ttu-id="9156e-314">**키**: urls</span><span class="sxs-lookup"><span data-stu-id="9156e-314">**Key**: urls</span></span>  
<span data-ttu-id="9156e-315">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="9156e-315">**Type**: *string*</span></span>  
<span data-ttu-id="9156e-316">**기본값**: `http://localhost:5000` 및 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="9156e-316">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="9156e-317">**환경 변수**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="9156e-317">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="9156e-318">이 값을 설정하려면 환경 변수를 사용하거나 `UseUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-318">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="9156e-319">Kestrel에는 자체 엔드포인트 구성 API가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-319">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="9156e-320">자세한 내용은 <xref:fundamentals/servers/kestrel#endpoint-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-320">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="9156e-321">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9156e-321">WebRoot</span></span>

<span data-ttu-id="9156e-322">앱의 정적 자산에 대한 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-322">The relative path to the app's static assets.</span></span>

<span data-ttu-id="9156e-323">**키**: webroot</span><span class="sxs-lookup"><span data-stu-id="9156e-323">**Key**: webroot</span></span>  
<span data-ttu-id="9156e-324">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="9156e-324">**Type**: *string*</span></span>  
<span data-ttu-id="9156e-325">**기본값**: 기본값은 `wwwroot`입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-325">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="9156e-326">*{content root}/wwwroot* 경로가 존재해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-326">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="9156e-327">경로가 존재하지 않으면 no-op 파일 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-327">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="9156e-328">**환경 변수**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="9156e-328">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="9156e-329">이 값을 설정하려면 환경 변수를 사용하거나 `UseWebRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-329">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="9156e-330">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-330">For more information, see:</span></span>

* [<span data-ttu-id="9156e-331">기본 사항: 웹 루트</span><span class="sxs-lookup"><span data-stu-id="9156e-331">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="9156e-332">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9156e-332">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="9156e-333">호스트 수명 관리</span><span class="sxs-lookup"><span data-stu-id="9156e-333">Manage the host lifetime</span></span>

<span data-ttu-id="9156e-334">기본 제공된 <xref:Microsoft.Extensions.Hosting.IHost> 구현에 대한 메서드를 호출하여 애플리케이션을 시작하고 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-334">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="9156e-335">이러한 메서드는 서비스 컨테이너에 등록된 모든 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-335">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9156e-336">Run</span><span class="sxs-lookup"><span data-stu-id="9156e-336">Run</span></span>

<span data-ttu-id="9156e-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="9156e-338">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9156e-338">RunAsync</span></span>

<span data-ttu-id="9156e-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="9156e-340">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9156e-340">RunConsoleAsync</span></span>

<span data-ttu-id="9156e-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔 지원을 구현하고, 호스트를 빌드 및 시작하며, Ctrl+C/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="9156e-342">Start</span><span class="sxs-lookup"><span data-stu-id="9156e-342">Start</span></span>

<span data-ttu-id="9156e-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="9156e-344">StartAsync</span><span class="sxs-lookup"><span data-stu-id="9156e-344">StartAsync</span></span>

<span data-ttu-id="9156e-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>는 호스트를 시작하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="9156e-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 `StartAsync`의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9156e-347">이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-347">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="9156e-348">StopAsync</span><span class="sxs-lookup"><span data-stu-id="9156e-348">StopAsync</span></span>

<span data-ttu-id="9156e-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="9156e-350">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="9156e-350">WaitForShutdown</span></span>

<span data-ttu-id="9156e-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 Ctrl+C/SIGINT 또는 SIGTERM을 통해 IHostLifetime에 의해 종료가 트리거될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via Ctrl+C/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="9156e-352">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9156e-352">WaitForShutdownAsync</span></span>

<span data-ttu-id="9156e-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="9156e-354">외부 제어</span><span class="sxs-lookup"><span data-stu-id="9156e-354">External control</span></span>

<span data-ttu-id="9156e-355">호스트 수명에 대한 직접 제어는 외부에서 호출할 수 있는 메서드를 사용하여 달성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-355">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9156e-356">ASP.NET Core 앱은 호스트를 구성 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-356">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="9156e-357">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-357">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="9156e-358">이 문서에서는 HTTP 요청을 처리하지 않는 앱에 사용되는 ASP.NET Core 일반 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-358">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="9156e-359">일반 호스트의 목적은 웹 호스트 API에서 HTTP 파이프라인을 분리하여 호스트 시나리오의 더 광범위한 배열을 구현하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-359">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="9156e-360">일반 호스트에 기반을 둔 메시징, 백그라운드 작업 및 기타 HTTP 이외 워크로드는 구성, DI(종속성 주입) 및 로깅과 같은 교차 편집 기능에서 이점을 얻습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-360">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="9156e-361">일반 호스트는 ASP.NET Core 2.1의 새로운 기능이며 웹 호스팅 시나리오에 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-361">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="9156e-362">웹 호스팅 시나리오의 경우 [웹 호스트](xref:fundamentals/host/web-host)를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-362">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="9156e-363">일반 호스트는 향후 릴리스에서 웹 호스트를 대체하고 HTTP 및 HTTP 이외 시나리오에서 기본 호스트 API 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-363">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="9156e-364">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9156e-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9156e-365">[Visual Studio Code](https://code.visualstudio.com/)에서 샘플 앱을 실행할 때는 *외부 또는 통합 터미널*을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-365">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="9156e-366">`internalConsole`에서는 샘플을 실행하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-366">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="9156e-367">Visual Studio Code에서 콘솔을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="9156e-367">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="9156e-368">*.vscode/launch.json* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-368">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="9156e-369">**.NET Core 시작(콘솔)** 구성에서 **콘솔** 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-369">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="9156e-370">값을 `externalTerminal` 또는 `integratedTerminal` 중 하나로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-370">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="9156e-371">소개</span><span class="sxs-lookup"><span data-stu-id="9156e-371">Introduction</span></span>

<span data-ttu-id="9156e-372">일반 호스트 라이브러리는 <xref:Microsoft.Extensions.Hosting> 네임스페이스에서 사용할 수 있으며, [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 패키지에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-372">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="9156e-373">`Microsoft.Extensions.Hosting` 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 이상)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-373">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="9156e-374"><xref:Microsoft.Extensions.Hosting.IHostedService>는 코드 실행 진입점입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-374"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="9156e-375">각 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현은 [ConfigureServices의 서비스 등록](#configureservices) 순서대로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-375">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="9156e-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>는 호스트가 시작될 때 각 <xref:Microsoft.Extensions.Hosting.IHostedService>에서 호출되고, <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>는 호스트가 점진적으로 종료될 때 등록 순서의 역순으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9156e-377">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="9156e-377">Set up a host</span></span>

<span data-ttu-id="9156e-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder>는 라이브러리 및 앱이 호스트를 초기화, 빌드 및 실행하는 데 사용하는 주 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="9156e-379">옵션</span><span class="sxs-lookup"><span data-stu-id="9156e-379">Options</span></span>

<span data-ttu-id="9156e-380"><xref:Microsoft.Extensions.Hosting.HostOptions>는 <xref:Microsoft.Extensions.Hosting.IHost>에 대한 옵션을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-380"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="9156e-381">시스템 종료 시간 제한</span><span class="sxs-lookup"><span data-stu-id="9156e-381">Shutdown timeout</span></span>

<span data-ttu-id="9156e-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>은 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9156e-383">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-383">The default value is five seconds.</span></span>

<span data-ttu-id="9156e-384">`Program.Main`의 다음 옵션 구성은 기본 5초 시스템 종료 시간 제한을 20초로 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-384">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="9156e-385">기본 서비스</span><span class="sxs-lookup"><span data-stu-id="9156e-385">Default services</span></span>

<span data-ttu-id="9156e-386">호스트 초기화 중에 다음 서비스가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-386">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="9156e-387">[환경](xref:fundamentals/environments)(<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="9156e-387">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="9156e-388">[구성](xref:fundamentals/configuration/index)(<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="9156e-388">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="9156e-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="9156e-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="9156e-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="9156e-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="9156e-391">[옵션](xref:fundamentals/configuration/options)(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="9156e-391">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="9156e-392">[로깅](xref:fundamentals/logging/index)(<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="9156e-392">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9156e-393">호스트 구성</span><span class="sxs-lookup"><span data-stu-id="9156e-393">Host configuration</span></span>

<span data-ttu-id="9156e-394">호스트 구성은 다음에 의해 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-394">Host configuration is created by:</span></span>

* <span data-ttu-id="9156e-395"><xref:Microsoft.Extensions.Hosting.IHostBuilder>에서 확장 메서드를 호출하여 [콘텐츠 루트](#content-root) 및 [환경](#environment)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-395">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="9156e-396"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>의 구성 공급자에서 구성을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-396">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="9156e-397">확장 메서드</span><span class="sxs-lookup"><span data-stu-id="9156e-397">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="9156e-398">애플리케이션 키(이름)</span><span class="sxs-lookup"><span data-stu-id="9156e-398">Application key (name)</span></span>

<span data-ttu-id="9156e-399">호스트를 생성하는 동안 호스트 구성에서 [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-399">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="9156e-400">값을 명시적으로 설정하려면 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-400">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="9156e-401">**키**: applicationName</span><span class="sxs-lookup"><span data-stu-id="9156e-401">**Key**: applicationName</span></span>  
<span data-ttu-id="9156e-402">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="9156e-402">**Type**: *string*</span></span>  
<span data-ttu-id="9156e-403">**기본값**: 앱의 진입점을 포함하는 어셈블리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-403">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="9156e-404">**설정 방법**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="9156e-404">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="9156e-405">**환경 변수**: `<PREFIX_>APPLICATIONNAME`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9156e-405">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="9156e-406">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="9156e-406">Content root</span></span>

<span data-ttu-id="9156e-407">이 설정은 호스트가 콘텐츠 파일을 검색하기 시작하는 지점을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-407">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="9156e-408">**키**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="9156e-408">**Key**: contentRoot</span></span>  
<span data-ttu-id="9156e-409">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="9156e-409">**Type**: *string*</span></span>  
<span data-ttu-id="9156e-410">**기본값**: 앱 어셈블리가 있는 폴더가 기본값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-410">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="9156e-411">**설정 방법**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="9156e-411">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="9156e-412">**환경 변수**: `<PREFIX_>CONTENTROOT`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9156e-412">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="9156e-413">경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-413">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="9156e-414">자세한 내용은 [기본 사항: 콘텐츠 루트](xref:fundamentals/index#content-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-414">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="9156e-415">환경</span><span class="sxs-lookup"><span data-stu-id="9156e-415">Environment</span></span>

<span data-ttu-id="9156e-416">앱의 [환경](xref:fundamentals/environments)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-416">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="9156e-417">**키**: environment</span><span class="sxs-lookup"><span data-stu-id="9156e-417">**Key**: environment</span></span>  
<span data-ttu-id="9156e-418">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="9156e-418">**Type**: *string*</span></span>  
<span data-ttu-id="9156e-419">**기본값**: Production</span><span class="sxs-lookup"><span data-stu-id="9156e-419">**Default**: Production</span></span>  
<span data-ttu-id="9156e-420">**설정 방법**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="9156e-420">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="9156e-421">**환경 변수**: `<PREFIX_>ENVIRONMENT`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9156e-421">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="9156e-422">환경은 어떠한 값으로도 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-422">The environment can be set to any value.</span></span> <span data-ttu-id="9156e-423">프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-423">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9156e-424">값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-424">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="9156e-425">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="9156e-425">ConfigureHostConfiguration</span></span>

<span data-ttu-id="9156e-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>은 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하여 호스트에 대한 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="9156e-427">호스트 구성은 앱의 빌드 프로세스에 사용할 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>를 초기화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-427">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="9156e-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="9156e-429">호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-429">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9156e-430">기본적으로 공급자는 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-430">No providers are included by default.</span></span> <span data-ttu-id="9156e-431">다음을 포함하여 앱에 필요한 모든 구성 공급자를 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>에 명시적으로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-431">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="9156e-432">구성 파일(예: *hostsettings.json* 파일에서).</span><span class="sxs-lookup"><span data-stu-id="9156e-432">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="9156e-433">환경 변수 구성.</span><span class="sxs-lookup"><span data-stu-id="9156e-433">Environment variable configuration.</span></span>
* <span data-ttu-id="9156e-434">명령줄 인수 구성.</span><span class="sxs-lookup"><span data-stu-id="9156e-434">Command-line argument configuration.</span></span>
* <span data-ttu-id="9156e-435">기타 필수 구성 공급자.</span><span class="sxs-lookup"><span data-stu-id="9156e-435">Any other required configuration providers.</span></span>

<span data-ttu-id="9156e-436">호스트의 파일 구성은 `SetBasePath`를 사용하여 앱의 기본 경로를 지정한 후 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider) 중 하나를 호출하여 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-436">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="9156e-437">샘플 앱은 JSON 파일인 *hostsettings.json*을 사용하고 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>을 호출하여 파일의 호스트 구성 설정을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-437">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="9156e-438">[환경 변수 구성](xref:fundamentals/configuration/index#environment-variables-configuration-provider)을 추가하려면 호스트 작성기에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-438">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="9156e-439">`AddEnvironmentVariables`는 선택적 사용자 정의 접두사를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-439">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="9156e-440">샘플 앱은 `PREFIX_` 접두사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-440">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="9156e-441">접두사는 환경 변수를 읽을 때 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-441">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9156e-442">샘플 앱의 호스트가 구성되면 `PREFIX_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-442">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9156e-443">[Visual Studio](https://visualstudio.microsoft.com)를 사용하거나 `dotnet run`을 통해 앱을 실행할 때 개발하는 동안에 환경 변수는 *Properties/launchSettings.json* 파일에 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-443">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="9156e-444">[Visual Studio Code](https://code.visualstudio.com/)에서 환경 변수는 개발하는 동안에 *.vscode/launch.json* 파일에 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-444">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="9156e-445">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-445">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="9156e-446">[명령줄 구성](xref:fundamentals/configuration/index#command-line-configuration-provider)은 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>을 호출하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-446">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="9156e-447">명령줄 구성이 마지막으로 추가되어 명령줄 인수가 이전 구성 공급자가 제공한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-447">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="9156e-448">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="9156e-448">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="9156e-449">[applicationName](#application-key-name) 및 [contentRoot](#content-root) 키를 사용하여 추가 구성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-449">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="9156e-450"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 사용한 `HostBuilder` 구성 예:</span><span class="sxs-lookup"><span data-stu-id="9156e-450">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="9156e-451">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="9156e-451">ConfigureAppConfiguration</span></span>

<span data-ttu-id="9156e-452">앱 구성은 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 구현에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-452">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="9156e-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>은 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하여 앱에 대한 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="9156e-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="9156e-455">앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-455">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="9156e-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>에 의해 생성된 구성은 다음 작업에 대한 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 및 <xref:Microsoft.Extensions.Hosting.IHost.Services*>에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-456">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="9156e-457">앱 구성은 [ConfigureHostConfiguration](#configurehostconfiguration)에서 제공하는 호스트 구성을 자동으로 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-457">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="9156e-458"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 사용한 앱 구성 예:</span><span class="sxs-lookup"><span data-stu-id="9156e-458">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="9156e-459">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="9156e-459">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="9156e-460">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="9156e-460">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="9156e-461">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="9156e-461">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="9156e-462">출력 디렉터리로 설정 파일을 이동하려면, 설정 파일을 프로젝트 파일 내 [MSBuild 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items)으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-462">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="9156e-463">샘플 앱은 다음 `<Content>` 항목과 함께 JSON 앱 설정 파일과 *hostsettings.json*을 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-463">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="9156e-464"><xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 및 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>와 같은 구성 확장 메서드에는 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) 및 [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)와 같은 추가 NuGet 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-464">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="9156e-465">앱에서 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 사용하는 경우가 아니면 이 패키지는 코어 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) 패키지에 추가로 프로젝트에 추가되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-465">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="9156e-466">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-466">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="9156e-467">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="9156e-467">ConfigureServices</span></span>

<span data-ttu-id="9156e-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>는 앱의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9156e-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="9156e-470">호스티드 서비스는 <xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스를 구현하는 백그라운드 작업 논리가 있는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-470">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="9156e-471">자세한 내용은 <xref:fundamentals/host/hosted-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-471">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="9156e-472">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)은 `AddHostedService` 확장 메서드를 사용하여 수명 이벤트, `LifetimeEventsHostedService` 및 시간 제한 백그라운드 작업에 대한 서비스 `TimedHostedService`를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-472">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="9156e-473">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="9156e-473">ConfigureLogging</span></span>

<span data-ttu-id="9156e-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>에는 제공된 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>를 구성하는 대리자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="9156e-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="9156e-476">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="9156e-476">UseConsoleLifetime</span></span>

<span data-ttu-id="9156e-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>은 Ctrl+C/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>을 호출하여 종료 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="9156e-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>은 [RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="9156e-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 수명 구현으로 미리 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="9156e-480">등록된 마지막 수명이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-480">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="9156e-481">컨테이너 구성</span><span class="sxs-lookup"><span data-stu-id="9156e-481">Container configuration</span></span>

<span data-ttu-id="9156e-482">호스트는 다른 컨테이너 연결을 지원하기 위해 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-482">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="9156e-483">팩터리 제공은 DI 컨테이너 등록의 일부가 아니지만 구체적 DI 컨테이너를 만드는 데 사용되는 내장 호스트입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-483">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="9156e-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*)는 앱의 서비스 공급자를 만드는 데 사용되는 기본 팩터리를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="9156e-485">사용자 지정 컨테이너 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 메서드로 관리됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-485">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="9156e-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>는 기본 호스트 API 위에 컨테이너를 구성하기 위한 강력한 형식의 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="9156e-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="9156e-488">앱에 대한 서비스 컨테이너를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-488">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="9156e-489">서비스 컨테이너 팩터리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-489">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="9156e-490">팩터리를 사용하고 앱에 대 한 사용자 지정 서비스 컨테이너를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-490">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="9156e-491">확장성</span><span class="sxs-lookup"><span data-stu-id="9156e-491">Extensibility</span></span>

<span data-ttu-id="9156e-492"><xref:Microsoft.Extensions.Hosting.IHostBuilder>에서 확장 메서드를 사용하여 호스트 확장성이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-492">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="9156e-493">다음 예제는 <xref:fundamentals/host/hosted-services>에 설명된 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 예제를 사용하여 확장 메서드가 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 구현을 확장하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-493">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="9156e-494">앱은 `UseHostedService` 확장 메서드를 설정하여 `T`에서 전달되는 호스티드 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-494">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="9156e-495">호스트 관리</span><span class="sxs-lookup"><span data-stu-id="9156e-495">Manage the host</span></span>

<span data-ttu-id="9156e-496"><xref:Microsoft.Extensions.Hosting.IHost> 구현은 서비스 컨테이너에 등록된 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현의 시작 및 중지를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-496">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9156e-497">Run</span><span class="sxs-lookup"><span data-stu-id="9156e-497">Run</span></span>

<span data-ttu-id="9156e-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="9156e-499">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9156e-499">RunAsync</span></span>

<span data-ttu-id="9156e-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="9156e-501">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9156e-501">RunConsoleAsync</span></span>

<span data-ttu-id="9156e-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔 지원을 구현하고, 호스트를 빌드 및 시작하며, Ctrl+C/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="9156e-503">Start 및 StopAsync</span><span class="sxs-lookup"><span data-stu-id="9156e-503">Start and StopAsync</span></span>

<span data-ttu-id="9156e-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="9156e-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="9156e-506">StartAsync 및 StopAsync</span><span class="sxs-lookup"><span data-stu-id="9156e-506">StartAsync and StopAsync</span></span>

<span data-ttu-id="9156e-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>가 서버를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="9156e-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>가 서버를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="9156e-509">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="9156e-509">WaitForShutdown</span></span>

<span data-ttu-id="9156e-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`(Ctrl+C/SIGINT 또는 SIGTERM 수신 대기)과 같이 <xref:Microsoft.Extensions.Hosting.IHostLifetime>을 통해 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="9156e-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="9156e-512">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9156e-512">WaitForShutdownAsync</span></span>

<span data-ttu-id="9156e-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="9156e-514">외부 제어</span><span class="sxs-lookup"><span data-stu-id="9156e-514">External control</span></span>

<span data-ttu-id="9156e-515">외부에서 호출할 수 있는 메서드를 사용하여 호스트의 외부 제어를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-515">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="9156e-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9156e-517">이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-517">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="9156e-518">IHostingEnvironment 인터페이스</span><span class="sxs-lookup"><span data-stu-id="9156e-518">IHostingEnvironment interface</span></span>

<span data-ttu-id="9156e-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>는 앱의 호스팅 환경에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="9156e-520">해당 속성 및 확장 메서드를 사용하기 위해 [생성자 주입](xref:fundamentals/dependency-injection)을 사용하여 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-520">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="9156e-521">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9156e-521">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="9156e-522">IApplicationLifetime 인터페이스</span><span class="sxs-lookup"><span data-stu-id="9156e-522">IApplicationLifetime interface</span></span>

<span data-ttu-id="9156e-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>은 점진적인 종료 요청을 비롯한 사후 시작 및 종료 작업을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="9156e-524">인터페이스에서 세 가지 속성은 취소 토큰으로, 시작 및 종료 이벤트를 정의하는 <xref:System.Action> 메서드를 등록하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-524">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="9156e-525">취소 토큰</span><span class="sxs-lookup"><span data-stu-id="9156e-525">Cancellation Token</span></span> | <span data-ttu-id="9156e-526">트리거되는 경우:</span><span class="sxs-lookup"><span data-stu-id="9156e-526">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="9156e-527">호스트가 완벽하게 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-527">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="9156e-528">호스트가 정상적으로 종료되었습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-528">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="9156e-529">모든 요청이 처리되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-529">All requests should be processed.</span></span> <span data-ttu-id="9156e-530">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-530">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="9156e-531">호스트가 정상적으로 종료되고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-531">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="9156e-532">요청은 계속 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-532">Requests may still be processing.</span></span> <span data-ttu-id="9156e-533">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-533">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="9156e-534">클래스에 대한 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 서비스 생성자 주입을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-534">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="9156e-535">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)은 `LifetimeEventsHostedService` 클래스(<xref:Microsoft.Extensions.Hosting.IHostedService> 구현)에 대한 생성자 주입을 사용하여 이벤트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-535">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="9156e-536">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="9156e-536">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="9156e-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>은 앱의 종료를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="9156e-538">다음 클래스에서는 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>을 사용하여 해당 클래스의 `Shutdown` 메서드를 호출하는 경우 앱을 정상 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="9156e-538">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9156e-539">추가 자료</span><span class="sxs-lookup"><span data-stu-id="9156e-539">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
