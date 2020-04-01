---
title: .NET 일반 호스트
author: rick-anderson
description: 앱 시작 및 수명 관리를 담당하는 .NET Core 일반 호스트에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 0f8f03dabf65f2cbfe4c41d36b02a25d7902cefb
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219222"
---
# <a name="net-generic-host"></a><span data-ttu-id="7e403-103">.NET 일반 호스트</span><span class="sxs-lookup"><span data-stu-id="7e403-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7e403-104">이 문서에서는 .NET Core 일반 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 소개하고 이를 사용하는 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="7e403-105">호스트란?</span><span class="sxs-lookup"><span data-stu-id="7e403-105">What's a host?</span></span>

<span data-ttu-id="7e403-106">*호스트*는 다음과 같이 앱의 리소스를 캡슐화하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="7e403-107">DI(종속성 주입)</span><span class="sxs-lookup"><span data-stu-id="7e403-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="7e403-108">로깅</span><span class="sxs-lookup"><span data-stu-id="7e403-108">Logging</span></span>
* <span data-ttu-id="7e403-109">Configuration</span><span class="sxs-lookup"><span data-stu-id="7e403-109">Configuration</span></span>
* <span data-ttu-id="7e403-110">`IHostedService` 구현</span><span class="sxs-lookup"><span data-stu-id="7e403-110">`IHostedService` implementations</span></span>

<span data-ttu-id="7e403-111">호스트가 시작되면 DI 컨테이너에서 찾은 <xref:Microsoft.Extensions.Hosting.IHostedService>의 각 구현 시 `IHostedService.StartAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="7e403-112">웹앱에서 `IHostedService` 구현 중 하나는 [HTTP 서버 구현](xref:fundamentals/index#servers)을 시작하는 웹 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="7e403-113">하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="7e403-114">3\.0 이전 버전의 ASP.NET Core에서 [웹 호스트](xref:fundamentals/host/web-host)는 HTTP 워크로드에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="7e403-115">웹 호스트는 더 이상 웹앱용으로 권장되지 않으며 이전 버전과의 호환성을 위해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="7e403-116">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="7e403-116">Set up a host</span></span>

<span data-ttu-id="7e403-117">호스트는 일반적으로 `Program` 클래스의 코드로 구성, 빌드 및 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="7e403-118">`Main` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-118">The `Main` method:</span></span>

* <span data-ttu-id="7e403-119">`CreateHostBuilder` 메서드를 호출하여 작성기 개체를 만들고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="7e403-120">작성기 개체에서 `Build` 및 `Run` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="7e403-121">다음은 DI 컨테이너에 단일 *구현이 추가된 비 HTTP 워크로드에 대한*Program.cs`IHostedService` 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="7e403-122">HTTP 워크로드의 경우 `Main` 메서드는 동일하지만 `CreateHostBuilder`는 `ConfigureWebHostDefaults`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="7e403-123">앱에서 Entity Framework Core를 사용하는 경우 `CreateHostBuilder` 메서드의 이름이나 서명을 변경하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="7e403-124">[Entity Framework Core 도구](/ef/core/miscellaneous/cli/)는 앱을 실행하지 않고 호스트를 구성하는 `CreateHostBuilder` 메서드를 찾으려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="7e403-125">자세한 내용은 [디자인 타임 DbContext 만들기](/ef/core/miscellaneous/cli/dbcontext-creation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="7e403-126">기본 작성기 설정</span><span class="sxs-lookup"><span data-stu-id="7e403-126">Default builder settings</span></span>

<span data-ttu-id="7e403-127"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="7e403-128">[콘텐츠 루트](xref:fundamentals/index#content-root)를 <xref:System.IO.Directory.GetCurrentDirectory*>에서 반환된 경로로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="7e403-129">다음에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="7e403-130">접두사가 `DOTNET_`인 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="7e403-130">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="7e403-131">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="7e403-131">Command-line arguments.</span></span>
* <span data-ttu-id="7e403-132">다음에서 앱 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="7e403-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7e403-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="7e403-134">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="7e403-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="7e403-135">[비밀 관리자](xref:security/app-secrets): 앱이 `Development` 환경에서 실행되는 경우</span><span class="sxs-lookup"><span data-stu-id="7e403-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="7e403-136">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="7e403-136">Environment variables.</span></span>
  * <span data-ttu-id="7e403-137">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="7e403-137">Command-line arguments.</span></span>
* <span data-ttu-id="7e403-138">다음 [로깅](xref:fundamentals/logging/index) 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="7e403-139">Console</span><span class="sxs-lookup"><span data-stu-id="7e403-139">Console</span></span>
  * <span data-ttu-id="7e403-140">Debug</span><span class="sxs-lookup"><span data-stu-id="7e403-140">Debug</span></span>
  * <span data-ttu-id="7e403-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="7e403-141">EventSource</span></span>
  * <span data-ttu-id="7e403-142">EventLog(Windows에서 실행 중인 경우에만)</span><span class="sxs-lookup"><span data-stu-id="7e403-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="7e403-143">환경이 개발 중일 때 [범위 유효성 검사](xref:fundamentals/dependency-injection#scope-validation) 및 [종속성 유효성 검사](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="7e403-144">`ConfigureWebHostDefaults` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="7e403-145">접두사가 `ASPNETCORE_`인 환경 변수에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-145">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="7e403-146">[Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 설정하고 앱의 호스팅 구성 공급자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="7e403-147">Kestrel 서버의 기본 옵션은 <xref:fundamentals/servers/kestrel#kestrel-options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="7e403-148">[호스트 필터링 미들웨어](xref:fundamentals/servers/kestrel#host-filtering)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="7e403-149">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`가 `true`와 같으면 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="7e403-150">IIS 통합을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-150">Enables IIS integration.</span></span> <span data-ttu-id="7e403-151">IIS 기본 옵션은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="7e403-152">이 문서 뒷부분의 [모든 앱 유형에 대한 설정](#settings-for-all-app-types) 및 [웹앱 설정](#settings-for-web-apps) 섹션에서는 기본 작성기 설정을 재정의하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="7e403-153">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="7e403-153">Framework-provided services</span></span>

<span data-ttu-id="7e403-154">다음 서비스가 자동으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-154">The following services are registered automatically:</span></span>

* [<span data-ttu-id="7e403-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="7e403-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="7e403-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="7e403-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="7e403-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="7e403-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="7e403-158">프레임워크에서 제공하는 서비스에 대한 자세한 내용은 <xref:fundamentals/dependency-injection#framework-provided-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="7e403-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="7e403-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="7e403-160"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>(이전의 `IApplicationLifetime`) 서비스를 모든 클래스에 삽입하여 사후 시작 및 정상 종료 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="7e403-161">인터페이스의 세 가지 속성은 앱 시작 및 앱 중지 이벤트 처리기 메서드를 등록하는 데 사용되는 취소 토큰입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="7e403-162">인터페이스에는 `StopApplication` 메서드도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="7e403-163">다음 예제는 `IHostApplicationLifetime` 이벤트를 등록하는 `IHostedService` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="7e403-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="7e403-164">IHostLifetime</span></span>

<span data-ttu-id="7e403-165"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 구현은 호스트가 시작될 때와 중지될 때를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="7e403-166">등록된 마지막 구현이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-166">The last implementation registered is used.</span></span>

<span data-ttu-id="7e403-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 `IHostLifetime` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="7e403-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="7e403-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="7e403-169"><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*>을 호출하여 종료 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-169">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="7e403-170">[RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="7e403-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="7e403-171">IHostEnvironment</span></span>

<span data-ttu-id="7e403-172"><xref:Microsoft.Extensions.Hosting.IHostEnvironment> 서비스를 클래스에 삽입하여 다음 설정에 대한 정보를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="7e403-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="7e403-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="7e403-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="7e403-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="7e403-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="7e403-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="7e403-176">웹앱은 `IHostEnvironment`를 상속하고 [WebRootPath](#webroot)를 추가하는 `IWebHostEnvironment` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="7e403-177">호스트 구성</span><span class="sxs-lookup"><span data-stu-id="7e403-177">Host configuration</span></span>

<span data-ttu-id="7e403-178">호스트 구성은 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 구현의 속성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="7e403-179">호스트 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 내부에 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="7e403-180">`ConfigureAppConfiguration` 다음에 `HostBuilderContext.Configuration`이 앱 구성으로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="7e403-181">호스트 구성을 추가하려면 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="7e403-182">`ConfigureHostConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="7e403-183">호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="7e403-184">접두사 `DOTNET_` 및 명령줄 인수가 있는 환경 변수 공급자는 `CreateDefaultBuilder`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-184">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="7e403-185">웹앱의 경우 접두사 `ASPNETCORE_`가 있는 환경 변수 공급자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="7e403-186">접두사는 환경 변수를 읽을 때 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="7e403-187">예를 들어 `ASPNETCORE_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="7e403-188">다음 예제에서는 호스트 구성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="7e403-189">앱 구성</span><span class="sxs-lookup"><span data-stu-id="7e403-189">App configuration</span></span>

<span data-ttu-id="7e403-190">앱 구성은 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="7e403-191">`ConfigureAppConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="7e403-192">앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="7e403-193">`ConfigureAppConfiguration`에 의해 생성된 구성은 다음 작업을 위해 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)에서 사용 가능하거나 DI의 서비스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="7e403-194">호스트 구성도 앱 구성에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="7e403-195">자세한 내용은 [ASP.NET Core의 구성](xref:fundamentals/configuration/index#configureappconfiguration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="7e403-196">모든 앱 유형에 대한 설정</span><span class="sxs-lookup"><span data-stu-id="7e403-196">Settings for all app types</span></span>

<span data-ttu-id="7e403-197">이 섹션에는 HTTP 및 비 HTTP 워크로드 모두에 적용되는 호스트 설정이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="7e403-198">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="7e403-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="7e403-199">ApplicationName</span></span>

<span data-ttu-id="7e403-200">호스트를 생성하는 동안 호스트 구성에서 [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="7e403-201">**키**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="7e403-201">**Key**: `applicationName`</span></span>  
<span data-ttu-id="7e403-202">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-202">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-203">**기본값**: 앱의 진입점을 포함하는 어셈블리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="7e403-204">**환경 변수**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="7e403-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="7e403-205">이 값을 설정하려면 환경 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="7e403-206">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="7e403-206">ContentRootPath</span></span>

<span data-ttu-id="7e403-207">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 속성은 콘텐츠 파일 검색을 시작하는 위치를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="7e403-208">경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="7e403-209">**키**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="7e403-209">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="7e403-210">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-210">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-211">**기본값**: 앱 어셈블리가 있는 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="7e403-212">**환경 변수**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="7e403-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="7e403-213">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseContentRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="7e403-214">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-214">For more information, see:</span></span>

* [<span data-ttu-id="7e403-215">기본 사항: 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="7e403-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="7e403-216">WebRoot</span><span class="sxs-lookup"><span data-stu-id="7e403-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="7e403-217">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="7e403-217">EnvironmentName</span></span>

<span data-ttu-id="7e403-218">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 속성을 임의의 값으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="7e403-219">프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="7e403-220">값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-220">Values aren't case-sensitive.</span></span>

<span data-ttu-id="7e403-221">**키**: `environment`</span><span class="sxs-lookup"><span data-stu-id="7e403-221">**Key**: `environment`</span></span>  
<span data-ttu-id="7e403-222">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-222">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-223">**기본**: `Production`</span><span class="sxs-lookup"><span data-stu-id="7e403-223">**Default**: `Production`</span></span>  
<span data-ttu-id="7e403-224">**환경 변수**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="7e403-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="7e403-225">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseEnvironment`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="7e403-226">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="7e403-226">ShutdownTimeout</span></span>

<span data-ttu-id="7e403-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*)이 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="7e403-228">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-228">The default value is five seconds.</span></span>  <span data-ttu-id="7e403-229">시간 제한 기간 동안 호스트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="7e403-230">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping)을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="7e403-231">중지하지 못한 서비스에 대한 오류를 로깅하면서 호스팅된 서비스 중지를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="7e403-232">모든 호스팅된 서비스가 중지하기 전에 시간 제한 기간이 만료되면 앱이 종료될 때 모든 활성화된 나머지 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="7e403-233">처리를 완료하지 않은 경우에도 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="7e403-234">서비스를 중지하는 데 시간이 더 필요한 경우 시간 제한을 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="7e403-235">**키**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="7e403-235">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="7e403-236">**형식**: `int`</span><span class="sxs-lookup"><span data-stu-id="7e403-236">**Type**: `int`</span></span>  
<span data-ttu-id="7e403-237">**기본값**: 5초</span><span class="sxs-lookup"><span data-stu-id="7e403-237">**Default**: 5 seconds</span></span>  
<span data-ttu-id="7e403-238">**환경 변수**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="7e403-238">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="7e403-239">이 값을 설정하려면 환경 변수를 사용하거나 `HostOptions`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="7e403-240">다음 예제에서는 시간 제한을 20초로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="7e403-241">변경 시 앱 구성 다시 로드 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="7e403-241">Disable app configuration reload on change</span></span>

<span data-ttu-id="7e403-242">[기본적으로](xref:fundamentals/configuration/index#default) 파일이 변경되면 *appsettings.json* 및 *appsettings.{Environment}.json*이 다시 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-242">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="7e403-243">ASP.NET Core 5.0 미리 보기 3 이상에서 이 다시 로드 동작을 사용하지 않도록 설정하려면 `hostBuilder:reloadConfigOnChange` 키를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-243">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="7e403-244">**키**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="7e403-244">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="7e403-245">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="7e403-245">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="7e403-246">**기본**: `true`</span><span class="sxs-lookup"><span data-stu-id="7e403-246">**Default**: `true`</span></span>  
<span data-ttu-id="7e403-247">**명령줄 인수**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="7e403-247">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="7e403-248">**환경 변수**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="7e403-248">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="7e403-249">콜론(`:`) 구분 기호는 모든 플랫폼의 환경 변수 계층적 키에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-249">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="7e403-250">자세한 내용은 [환경 변수](xref:fundamentals/configuration/index#environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-250">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="7e403-251">웹앱 설정</span><span class="sxs-lookup"><span data-stu-id="7e403-251">Settings for web apps</span></span>

<span data-ttu-id="7e403-252">일부 호스트 설정은 HTTP 워크로드에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-252">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="7e403-253">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-253">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="7e403-254">이러한 설정에 대해 `IWebHostBuilder`의 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-254">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="7e403-255">확장 메서드를 호출하는 방법을 보여주는 코드 샘플은 다음 예제와 같이 `webBuilder`가 `IWebHostBuilder`의 인스턴스라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-255">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="7e403-256">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="7e403-256">CaptureStartupErrors</span></span>

<span data-ttu-id="7e403-257">`false`인 경우 시작 시 오류가 발생하면 호스트가 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-257">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="7e403-258">`true`이면 호스트가 시작 시 예외를 캡처하고 서버 시작을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-258">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="7e403-259">**키**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="7e403-259">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="7e403-260">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="7e403-260">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="7e403-261">**기본값**: 기본값이 `true`인 IIS 뒤에 있는 Kestrel로 앱이 실행하지 않는 한 기본값은 `false`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-261">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="7e403-262">**환경 변수**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="7e403-262">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="7e403-263">이 값을 설정하려면 구성을 사용하거나 `CaptureStartupErrors`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-263">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="7e403-264">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="7e403-264">DetailedErrors</span></span>

<span data-ttu-id="7e403-265">활성화하거나 환경이 `Development`인 경우 앱은 자세한 오류를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-265">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="7e403-266">**키**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="7e403-266">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="7e403-267">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="7e403-267">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="7e403-268">**기본**: `false`</span><span class="sxs-lookup"><span data-stu-id="7e403-268">**Default**: `false`</span></span>  
<span data-ttu-id="7e403-269">**환경 변수**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="7e403-269">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="7e403-270">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="7e403-271">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="7e403-271">HostingStartupAssemblies</span></span>

<span data-ttu-id="7e403-272">시작 시 로드할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-272">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="7e403-273">구성 값의 기본값이 빈 문자열이지만, 호스팅 시작 어셈블리는 항상 앱의 어셈블리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-273">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="7e403-274">호스팅 시작 어셈블리가 제공되는 경우, 시작 시 앱이 일반적인 서비스를 빌드할 때 로드를 위해 앱의 어셈블리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-274">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="7e403-275">**키**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="7e403-275">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="7e403-276">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-276">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-277">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="7e403-277">**Default**: Empty string</span></span>  
<span data-ttu-id="7e403-278">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="7e403-278">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="7e403-279">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="7e403-280">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="7e403-280">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="7e403-281">시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-281">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="7e403-282">**키**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="7e403-282">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="7e403-283">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-283">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-284">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="7e403-284">**Default**: Empty string</span></span>  
<span data-ttu-id="7e403-285">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="7e403-285">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="7e403-286">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-286">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="7e403-287">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="7e403-287">HTTPS_Port</span></span>

<span data-ttu-id="7e403-288">HTTPS 리디렉션 포트.</span><span class="sxs-lookup"><span data-stu-id="7e403-288">The HTTPS redirect port.</span></span> <span data-ttu-id="7e403-289">[HTTPS 적용](xref:security/enforcing-ssl)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-289">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="7e403-290">**키**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="7e403-290">**Key**: `https_port`</span></span>  
<span data-ttu-id="7e403-291">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-291">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-292">**기본값**: 기본값은 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-292">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="7e403-293">**환경 변수**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="7e403-293">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="7e403-294">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-294">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="7e403-295">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="7e403-295">PreferHostingUrls</span></span>

<span data-ttu-id="7e403-296">호스트가 `IServer` 구현으로 구성된 URL 대신에 `IWebHostBuilder`로 구성된 URL에서 수신 대기할지 아닌지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-296">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="7e403-297">**키**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="7e403-297">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="7e403-298">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="7e403-298">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="7e403-299">**기본**: `true`</span><span class="sxs-lookup"><span data-stu-id="7e403-299">**Default**: `true`</span></span>  
<span data-ttu-id="7e403-300">**환경 변수**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="7e403-300">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="7e403-301">이 값을 설정하려면 환경 변수를 사용하거나 `PreferHostingUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-301">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="7e403-302">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="7e403-302">PreventHostingStartup</span></span>

<span data-ttu-id="7e403-303">앱의 어셈블리에 의해 구성된 호스팅 시작 어셈블리를 포함한 호스팅 시작 어셈블리의 자동 로딩을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-303">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="7e403-304">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-304">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="7e403-305">**키**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="7e403-305">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="7e403-306">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="7e403-306">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="7e403-307">**기본**: `false`</span><span class="sxs-lookup"><span data-stu-id="7e403-307">**Default**: `false`</span></span>  
<span data-ttu-id="7e403-308">**환경 변수**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="7e403-308">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="7e403-309">이 값을 설정하려면 환경 변수를 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-309">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="7e403-310">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="7e403-310">StartupAssembly</span></span>

<span data-ttu-id="7e403-311">`Startup` 클래스를 검색할 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-311">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="7e403-312">**키**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="7e403-312">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="7e403-313">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-313">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-314">**기본값**: 앱의 어셈블리</span><span class="sxs-lookup"><span data-stu-id="7e403-314">**Default**: The app's assembly</span></span>  
<span data-ttu-id="7e403-315">**환경 변수**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="7e403-315">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="7e403-316">이 값을 설정하려면 환경 변수를 사용하거나 `UseStartup`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-316">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="7e403-317">`UseStartup`은 어셈블리 이름(`string`) 또는 형식(`TStartup`)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-317">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="7e403-318">`UseStartup` 메서드가 여러 개 호출된 경우 마지막 메서드가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-318">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="7e403-319">URL</span><span class="sxs-lookup"><span data-stu-id="7e403-319">URLs</span></span>

<span data-ttu-id="7e403-320">서버에서 요청을 수신해야 하는 포트와 프로토콜을 포함하는 세미클론으로 구분된 IP 주소 또는 호스트 주소의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-320">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="7e403-321">예: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="7e403-321">For example, `http://localhost:123`.</span></span> <span data-ttu-id="7e403-322">“\*”를 사용하여 서버가 지정된 포트 및 프로토콜을 사용하는 IP 주소 또는 호스트 이름에서 요청을 수신해야 함을 나타냅니다(예: `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="7e403-322">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="7e403-323">프로토콜(`http://` 또는 `https://`)은 각 URL에 포함되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-323">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="7e403-324">지원되는 형식은 서버마다 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-324">Supported formats vary among servers.</span></span>

<span data-ttu-id="7e403-325">**키**: `urls`</span><span class="sxs-lookup"><span data-stu-id="7e403-325">**Key**: `urls`</span></span>  
<span data-ttu-id="7e403-326">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-326">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-327">**기본값**: `http://localhost:5000` 및 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="7e403-327">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="7e403-328">**환경 변수**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="7e403-328">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="7e403-329">이 값을 설정하려면 환경 변수를 사용하거나 `UseUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-329">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="7e403-330">Kestrel에는 자체 엔드포인트 구성 API가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-330">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="7e403-331">자세한 내용은 <xref:fundamentals/servers/kestrel#endpoint-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-331">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="7e403-332">WebRoot</span><span class="sxs-lookup"><span data-stu-id="7e403-332">WebRoot</span></span>

<span data-ttu-id="7e403-333">앱의 정적 자산에 대한 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-333">The relative path to the app's static assets.</span></span>

<span data-ttu-id="7e403-334">**키**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="7e403-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="7e403-335">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-335">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-336">**기본값**: 기본값은 `wwwroot`입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="7e403-337">*{content root}/wwwroot* 경로가 존재해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-337">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="7e403-338">경로가 존재하지 않으면 no-op 파일 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-338">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="7e403-339">**환경 변수**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="7e403-339">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="7e403-340">이 값을 설정하려면 환경 변수를 사용하거나 `UseWebRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-340">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="7e403-341">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-341">For more information, see:</span></span>

* [<span data-ttu-id="7e403-342">기본 사항: 웹 루트</span><span class="sxs-lookup"><span data-stu-id="7e403-342">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="7e403-343">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="7e403-343">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="7e403-344">호스트 수명 관리</span><span class="sxs-lookup"><span data-stu-id="7e403-344">Manage the host lifetime</span></span>

<span data-ttu-id="7e403-345">기본 제공된 <xref:Microsoft.Extensions.Hosting.IHost> 구현에 대한 메서드를 호출하여 애플리케이션을 시작하고 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-345">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="7e403-346">이러한 메서드는 서비스 컨테이너에 등록된 모든 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-346">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="7e403-347">Run</span><span class="sxs-lookup"><span data-stu-id="7e403-347">Run</span></span>

<span data-ttu-id="7e403-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="7e403-349">RunAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-349">RunAsync</span></span>

<span data-ttu-id="7e403-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="7e403-351">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-351">RunConsoleAsync</span></span>

<span data-ttu-id="7e403-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔을 지원하고 호스트를 빌드 및 시작하며 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="7e403-353">Start</span><span class="sxs-lookup"><span data-stu-id="7e403-353">Start</span></span>

<span data-ttu-id="7e403-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="7e403-355">StartAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-355">StartAsync</span></span>

<span data-ttu-id="7e403-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>는 호스트를 시작하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="7e403-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 `StartAsync`의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="7e403-358">이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-358">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="7e403-359">StopAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-359">StopAsync</span></span>

<span data-ttu-id="7e403-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="7e403-361">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="7e403-361">WaitForShutdown</span></span>

<span data-ttu-id="7e403-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 통해 IHostLifetime에 의해 종료가 트리거될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="7e403-363">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-363">WaitForShutdownAsync</span></span>

<span data-ttu-id="7e403-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="7e403-365">외부 제어</span><span class="sxs-lookup"><span data-stu-id="7e403-365">External control</span></span>

<span data-ttu-id="7e403-366">호스트 수명에 대한 직접 제어는 외부에서 호출할 수 있는 메서드를 사용하여 달성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-366">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="7e403-367">이 문서에서는 .NET Core 일반 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 소개하고 이를 사용하는 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-367">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="7e403-368">호스트란?</span><span class="sxs-lookup"><span data-stu-id="7e403-368">What's a host?</span></span>

<span data-ttu-id="7e403-369">*호스트*는 다음과 같이 앱의 리소스를 캡슐화하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-369">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="7e403-370">DI(종속성 주입)</span><span class="sxs-lookup"><span data-stu-id="7e403-370">Dependency injection (DI)</span></span>
* <span data-ttu-id="7e403-371">로깅</span><span class="sxs-lookup"><span data-stu-id="7e403-371">Logging</span></span>
* <span data-ttu-id="7e403-372">Configuration</span><span class="sxs-lookup"><span data-stu-id="7e403-372">Configuration</span></span>
* <span data-ttu-id="7e403-373">`IHostedService` 구현</span><span class="sxs-lookup"><span data-stu-id="7e403-373">`IHostedService` implementations</span></span>

<span data-ttu-id="7e403-374">호스트가 시작되면 DI 컨테이너에서 찾은 <xref:Microsoft.Extensions.Hosting.IHostedService>의 각 구현 시 `IHostedService.StartAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-374">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="7e403-375">웹앱에서 `IHostedService` 구현 중 하나는 [HTTP 서버 구현](xref:fundamentals/index#servers)을 시작하는 웹 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-375">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="7e403-376">하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-376">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="7e403-377">3\.0 이전 버전의 ASP.NET Core에서 [웹 호스트](xref:fundamentals/host/web-host)는 HTTP 워크로드에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-377">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="7e403-378">웹 호스트는 더 이상 웹앱용으로 권장되지 않으며 이전 버전과의 호환성을 위해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-378">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="7e403-379">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="7e403-379">Set up a host</span></span>

<span data-ttu-id="7e403-380">호스트는 일반적으로 `Program` 클래스의 코드로 구성, 빌드 및 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-380">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="7e403-381">`Main` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-381">The `Main` method:</span></span>

* <span data-ttu-id="7e403-382">`CreateHostBuilder` 메서드를 호출하여 작성기 개체를 만들고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-382">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="7e403-383">작성기 개체에서 `Build` 및 `Run` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-383">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="7e403-384">다음은 DI 컨테이너에 단일 *구현이 추가된 비 HTTP 워크로드에 대한*Program.cs`IHostedService` 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-384">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="7e403-385">HTTP 워크로드의 경우 `Main` 메서드는 동일하지만 `CreateHostBuilder`는 `ConfigureWebHostDefaults`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-385">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="7e403-386">앱에서 Entity Framework Core를 사용하는 경우 `CreateHostBuilder` 메서드의 이름이나 서명을 변경하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="7e403-387">[Entity Framework Core 도구](/ef/core/miscellaneous/cli/)는 앱을 실행하지 않고 호스트를 구성하는 `CreateHostBuilder` 메서드를 찾으려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="7e403-388">자세한 내용은 [디자인 타임 DbContext 만들기](/ef/core/miscellaneous/cli/dbcontext-creation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="7e403-389">기본 작성기 설정</span><span class="sxs-lookup"><span data-stu-id="7e403-389">Default builder settings</span></span>

<span data-ttu-id="7e403-390"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="7e403-391">[콘텐츠 루트](xref:fundamentals/index#content-root)를 <xref:System.IO.Directory.GetCurrentDirectory*>에서 반환된 경로로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="7e403-392">다음에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="7e403-393">접두사가 `DOTNET_`인 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="7e403-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="7e403-394">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="7e403-394">Command-line arguments.</span></span>
* <span data-ttu-id="7e403-395">다음에서 앱 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="7e403-396">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7e403-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="7e403-397">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="7e403-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="7e403-398">[비밀 관리자](xref:security/app-secrets): 앱이 `Development` 환경에서 실행되는 경우</span><span class="sxs-lookup"><span data-stu-id="7e403-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="7e403-399">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="7e403-399">Environment variables.</span></span>
  * <span data-ttu-id="7e403-400">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="7e403-400">Command-line arguments.</span></span>
* <span data-ttu-id="7e403-401">다음 [로깅](xref:fundamentals/logging/index) 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="7e403-402">Console</span><span class="sxs-lookup"><span data-stu-id="7e403-402">Console</span></span>
  * <span data-ttu-id="7e403-403">Debug</span><span class="sxs-lookup"><span data-stu-id="7e403-403">Debug</span></span>
  * <span data-ttu-id="7e403-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="7e403-404">EventSource</span></span>
  * <span data-ttu-id="7e403-405">EventLog(Windows에서 실행 중인 경우에만)</span><span class="sxs-lookup"><span data-stu-id="7e403-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="7e403-406">환경이 개발 중일 때 [범위 유효성 검사](xref:fundamentals/dependency-injection#scope-validation) 및 [종속성 유효성 검사](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="7e403-407">`ConfigureWebHostDefaults` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="7e403-408">접두사가 `ASPNETCORE_`인 환경 변수에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="7e403-409">[Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 설정하고 앱의 호스팅 구성 공급자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="7e403-410">Kestrel 서버의 기본 옵션은 <xref:fundamentals/servers/kestrel#kestrel-options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="7e403-411">[호스트 필터링 미들웨어](xref:fundamentals/servers/kestrel#host-filtering)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="7e403-412">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`가 `true`와 같으면 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="7e403-413">IIS 통합을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-413">Enables IIS integration.</span></span> <span data-ttu-id="7e403-414">IIS 기본 옵션은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="7e403-415">이 문서 뒷부분의 [모든 앱 유형에 대한 설정](#settings-for-all-app-types) 및 [웹앱 설정](#settings-for-web-apps) 섹션에서는 기본 작성기 설정을 재정의하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="7e403-416">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="7e403-416">Framework-provided services</span></span>

<span data-ttu-id="7e403-417">다음 서비스가 자동으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="7e403-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="7e403-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="7e403-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="7e403-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="7e403-420">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="7e403-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="7e403-421">프레임워크에서 제공하는 서비스에 대한 자세한 내용은 <xref:fundamentals/dependency-injection#framework-provided-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="7e403-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="7e403-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="7e403-423"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>(이전의 `IApplicationLifetime`) 서비스를 모든 클래스에 삽입하여 사후 시작 및 정상 종료 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="7e403-424">인터페이스의 세 가지 속성은 앱 시작 및 앱 중지 이벤트 처리기 메서드를 등록하는 데 사용되는 취소 토큰입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="7e403-425">인터페이스에는 `StopApplication` 메서드도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="7e403-426">다음 예제는 `IHostApplicationLifetime` 이벤트를 등록하는 `IHostedService` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="7e403-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="7e403-427">IHostLifetime</span></span>

<span data-ttu-id="7e403-428"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 구현은 호스트가 시작될 때와 중지될 때를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="7e403-429">등록된 마지막 구현이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-429">The last implementation registered is used.</span></span>

<span data-ttu-id="7e403-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 `IHostLifetime` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="7e403-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="7e403-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="7e403-432"><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*>을 호출하여 종료 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="7e403-433">[RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="7e403-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="7e403-434">IHostEnvironment</span></span>

<span data-ttu-id="7e403-435"><xref:Microsoft.Extensions.Hosting.IHostEnvironment> 서비스를 클래스에 삽입하여 다음 설정에 대한 정보를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="7e403-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="7e403-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="7e403-437">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="7e403-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="7e403-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="7e403-438">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="7e403-439">웹앱은 `IHostEnvironment`를 상속하고 [WebRootPath](#webroot)를 추가하는 `IWebHostEnvironment` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="7e403-440">호스트 구성</span><span class="sxs-lookup"><span data-stu-id="7e403-440">Host configuration</span></span>

<span data-ttu-id="7e403-441">호스트 구성은 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 구현의 속성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="7e403-442">호스트 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 내부에 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="7e403-443">`ConfigureAppConfiguration` 다음에 `HostBuilderContext.Configuration`이 앱 구성으로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="7e403-444">호스트 구성을 추가하려면 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="7e403-445">`ConfigureHostConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="7e403-446">호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="7e403-447">접두사 `DOTNET_` 및 명령줄 인수가 있는 환경 변수 공급자는 `CreateDefaultBuilder`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="7e403-448">웹앱의 경우 접두사 `ASPNETCORE_`가 있는 환경 변수 공급자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="7e403-449">접두사는 환경 변수를 읽을 때 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="7e403-450">예를 들어 `ASPNETCORE_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="7e403-451">다음 예제에서는 호스트 구성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="7e403-452">앱 구성</span><span class="sxs-lookup"><span data-stu-id="7e403-452">App configuration</span></span>

<span data-ttu-id="7e403-453">앱 구성은 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="7e403-454">`ConfigureAppConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="7e403-455">앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="7e403-456">`ConfigureAppConfiguration`에 의해 생성된 구성은 다음 작업을 위해 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)에서 사용 가능하거나 DI의 서비스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="7e403-457">호스트 구성도 앱 구성에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="7e403-458">자세한 내용은 [ASP.NET Core의 구성](xref:fundamentals/configuration/index#configureappconfiguration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="7e403-459">모든 앱 유형에 대한 설정</span><span class="sxs-lookup"><span data-stu-id="7e403-459">Settings for all app types</span></span>

<span data-ttu-id="7e403-460">이 섹션에는 HTTP 및 비 HTTP 워크로드 모두에 적용되는 호스트 설정이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="7e403-461">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="7e403-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="7e403-462">ApplicationName</span></span>

<span data-ttu-id="7e403-463">호스트를 생성하는 동안 호스트 구성에서 [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="7e403-464">**키**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="7e403-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="7e403-465">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-465">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-466">**기본값**: 앱의 진입점을 포함하는 어셈블리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="7e403-467">**환경 변수**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="7e403-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="7e403-468">이 값을 설정하려면 환경 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-468">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="7e403-469">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="7e403-469">ContentRootPath</span></span>

<span data-ttu-id="7e403-470">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 속성은 콘텐츠 파일 검색을 시작하는 위치를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="7e403-471">경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="7e403-472">**키**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="7e403-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="7e403-473">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-473">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-474">**기본값**: 앱 어셈블리가 있는 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="7e403-475">**환경 변수**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="7e403-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="7e403-476">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseContentRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="7e403-477">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-477">For more information, see:</span></span>

* [<span data-ttu-id="7e403-478">기본 사항: 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="7e403-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="7e403-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="7e403-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="7e403-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="7e403-480">EnvironmentName</span></span>

<span data-ttu-id="7e403-481">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 속성을 임의의 값으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="7e403-482">프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="7e403-483">값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="7e403-484">**키**: `environment`</span><span class="sxs-lookup"><span data-stu-id="7e403-484">**Key**: `environment`</span></span>  
<span data-ttu-id="7e403-485">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-485">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-486">**기본**: `Production`</span><span class="sxs-lookup"><span data-stu-id="7e403-486">**Default**: `Production`</span></span>  
<span data-ttu-id="7e403-487">**환경 변수**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="7e403-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="7e403-488">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseEnvironment`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="7e403-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="7e403-489">ShutdownTimeout</span></span>

<span data-ttu-id="7e403-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*)이 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="7e403-491">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-491">The default value is five seconds.</span></span>  <span data-ttu-id="7e403-492">시간 제한 기간 동안 호스트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="7e403-493">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping)을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="7e403-494">중지하지 못한 서비스에 대한 오류를 로깅하면서 호스팅된 서비스 중지를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="7e403-495">모든 호스팅된 서비스가 중지하기 전에 시간 제한 기간이 만료되면 앱이 종료될 때 모든 활성화된 나머지 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="7e403-496">처리를 완료하지 않은 경우에도 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="7e403-497">서비스를 중지하는 데 시간이 더 필요한 경우 시간 제한을 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="7e403-498">**키**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="7e403-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="7e403-499">**형식**: `int`</span><span class="sxs-lookup"><span data-stu-id="7e403-499">**Type**: `int`</span></span>  
<span data-ttu-id="7e403-500">**기본값**: 5초</span><span class="sxs-lookup"><span data-stu-id="7e403-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="7e403-501">**환경 변수**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="7e403-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="7e403-502">이 값을 설정하려면 환경 변수를 사용하거나 `HostOptions`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="7e403-503">다음 예제에서는 시간 제한을 20초로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="7e403-504">웹앱 설정</span><span class="sxs-lookup"><span data-stu-id="7e403-504">Settings for web apps</span></span>

<span data-ttu-id="7e403-505">일부 호스트 설정은 HTTP 워크로드에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="7e403-506">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="7e403-507">이러한 설정에 대해 `IWebHostBuilder`의 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="7e403-508">확장 메서드를 호출하는 방법을 보여주는 코드 샘플은 다음 예제와 같이 `webBuilder`가 `IWebHostBuilder`의 인스턴스라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="7e403-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="7e403-509">CaptureStartupErrors</span></span>

<span data-ttu-id="7e403-510">`false`인 경우 시작 시 오류가 발생하면 호스트가 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="7e403-511">`true`이면 호스트가 시작 시 예외를 캡처하고 서버 시작을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="7e403-512">**키**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="7e403-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="7e403-513">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="7e403-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="7e403-514">**기본값**: 기본값이 `true`인 IIS 뒤에 있는 Kestrel로 앱이 실행하지 않는 한 기본값은 `false`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="7e403-515">**환경 변수**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="7e403-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="7e403-516">이 값을 설정하려면 구성을 사용하거나 `CaptureStartupErrors`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="7e403-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="7e403-517">DetailedErrors</span></span>

<span data-ttu-id="7e403-518">활성화하거나 환경이 `Development`인 경우 앱은 자세한 오류를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="7e403-519">**키**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="7e403-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="7e403-520">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="7e403-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="7e403-521">**기본**: `false`</span><span class="sxs-lookup"><span data-stu-id="7e403-521">**Default**: `false`</span></span>  
<span data-ttu-id="7e403-522">**환경 변수**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="7e403-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="7e403-523">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="7e403-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="7e403-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="7e403-525">시작 시 로드할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="7e403-526">구성 값의 기본값이 빈 문자열이지만, 호스팅 시작 어셈블리는 항상 앱의 어셈블리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="7e403-527">호스팅 시작 어셈블리가 제공되는 경우, 시작 시 앱이 일반적인 서비스를 빌드할 때 로드를 위해 앱의 어셈블리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="7e403-528">**키**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="7e403-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="7e403-529">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-529">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-530">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="7e403-530">**Default**: Empty string</span></span>  
<span data-ttu-id="7e403-531">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="7e403-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="7e403-532">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="7e403-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="7e403-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="7e403-534">시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="7e403-535">**키**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="7e403-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="7e403-536">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-536">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-537">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="7e403-537">**Default**: Empty string</span></span>  
<span data-ttu-id="7e403-538">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="7e403-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="7e403-539">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="7e403-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="7e403-540">HTTPS_Port</span></span>

<span data-ttu-id="7e403-541">HTTPS 리디렉션 포트.</span><span class="sxs-lookup"><span data-stu-id="7e403-541">The HTTPS redirect port.</span></span> <span data-ttu-id="7e403-542">[HTTPS 적용](xref:security/enforcing-ssl)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="7e403-543">**키**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="7e403-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="7e403-544">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-544">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-545">**기본값**: 기본값은 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="7e403-546">**환경 변수**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="7e403-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="7e403-547">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="7e403-548">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="7e403-548">PreferHostingUrls</span></span>

<span data-ttu-id="7e403-549">호스트가 `IServer` 구현으로 구성된 URL 대신에 `IWebHostBuilder`로 구성된 URL에서 수신 대기할지 아닌지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="7e403-550">**키**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="7e403-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="7e403-551">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="7e403-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="7e403-552">**기본**: `true`</span><span class="sxs-lookup"><span data-stu-id="7e403-552">**Default**: `true`</span></span>  
<span data-ttu-id="7e403-553">**환경 변수**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="7e403-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="7e403-554">이 값을 설정하려면 환경 변수를 사용하거나 `PreferHostingUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="7e403-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="7e403-555">PreventHostingStartup</span></span>

<span data-ttu-id="7e403-556">앱의 어셈블리에 의해 구성된 호스팅 시작 어셈블리를 포함한 호스팅 시작 어셈블리의 자동 로딩을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="7e403-557">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="7e403-558">**키**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="7e403-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="7e403-559">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="7e403-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="7e403-560">**기본**: `false`</span><span class="sxs-lookup"><span data-stu-id="7e403-560">**Default**: `false`</span></span>  
<span data-ttu-id="7e403-561">**환경 변수**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="7e403-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="7e403-562">이 값을 설정하려면 환경 변수를 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="7e403-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="7e403-563">StartupAssembly</span></span>

<span data-ttu-id="7e403-564">`Startup` 클래스를 검색할 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="7e403-565">**키**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="7e403-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="7e403-566">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-566">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-567">**기본값**: 앱의 어셈블리</span><span class="sxs-lookup"><span data-stu-id="7e403-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="7e403-568">**환경 변수**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="7e403-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="7e403-569">이 값을 설정하려면 환경 변수를 사용하거나 `UseStartup`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="7e403-570">`UseStartup`은 어셈블리 이름(`string`) 또는 형식(`TStartup`)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="7e403-571">`UseStartup` 메서드가 여러 개 호출된 경우 마지막 메서드가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="7e403-572">URL</span><span class="sxs-lookup"><span data-stu-id="7e403-572">URLs</span></span>

<span data-ttu-id="7e403-573">서버에서 요청을 수신해야 하는 포트와 프로토콜을 포함하는 세미클론으로 구분된 IP 주소 또는 호스트 주소의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="7e403-574">예: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="7e403-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="7e403-575">“\*”를 사용하여 서버가 지정된 포트 및 프로토콜을 사용하는 IP 주소 또는 호스트 이름에서 요청을 수신해야 함을 나타냅니다(예: `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="7e403-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="7e403-576">프로토콜(`http://` 또는 `https://`)은 각 URL에 포함되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="7e403-577">지원되는 형식은 서버마다 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="7e403-578">**키**: `urls`</span><span class="sxs-lookup"><span data-stu-id="7e403-578">**Key**: `urls`</span></span>  
<span data-ttu-id="7e403-579">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-579">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-580">**기본값**: `http://localhost:5000` 및 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="7e403-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="7e403-581">**환경 변수**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="7e403-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="7e403-582">이 값을 설정하려면 환경 변수를 사용하거나 `UseUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="7e403-583">Kestrel에는 자체 엔드포인트 구성 API가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="7e403-584">자세한 내용은 <xref:fundamentals/servers/kestrel#endpoint-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="7e403-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="7e403-585">WebRoot</span></span>

<span data-ttu-id="7e403-586">앱의 정적 자산에 대한 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-586">The relative path to the app's static assets.</span></span>

<span data-ttu-id="7e403-587">**키**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="7e403-587">**Key**: `webroot`</span></span>  
<span data-ttu-id="7e403-588">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-588">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-589">**기본값**: 기본값은 `wwwroot`입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-589">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="7e403-590">*{content root}/wwwroot* 경로가 존재해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-590">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="7e403-591">경로가 존재하지 않으면 no-op 파일 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-591">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="7e403-592">**환경 변수**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="7e403-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="7e403-593">이 값을 설정하려면 환경 변수를 사용하거나 `UseWebRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-593">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="7e403-594">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-594">For more information, see:</span></span>

* [<span data-ttu-id="7e403-595">기본 사항: 웹 루트</span><span class="sxs-lookup"><span data-stu-id="7e403-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="7e403-596">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="7e403-596">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="7e403-597">호스트 수명 관리</span><span class="sxs-lookup"><span data-stu-id="7e403-597">Manage the host lifetime</span></span>

<span data-ttu-id="7e403-598">기본 제공된 <xref:Microsoft.Extensions.Hosting.IHost> 구현에 대한 메서드를 호출하여 애플리케이션을 시작하고 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="7e403-599">이러한 메서드는 서비스 컨테이너에 등록된 모든 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="7e403-600">Run</span><span class="sxs-lookup"><span data-stu-id="7e403-600">Run</span></span>

<span data-ttu-id="7e403-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="7e403-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-602">RunAsync</span></span>

<span data-ttu-id="7e403-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="7e403-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-604">RunConsoleAsync</span></span>

<span data-ttu-id="7e403-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔을 지원하고 호스트를 빌드 및 시작하며 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="7e403-606">Start</span><span class="sxs-lookup"><span data-stu-id="7e403-606">Start</span></span>

<span data-ttu-id="7e403-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="7e403-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-608">StartAsync</span></span>

<span data-ttu-id="7e403-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>는 호스트를 시작하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="7e403-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 `StartAsync`의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="7e403-611">이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="7e403-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-612">StopAsync</span></span>

<span data-ttu-id="7e403-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="7e403-614">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="7e403-614">WaitForShutdown</span></span>

<span data-ttu-id="7e403-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 통해 IHostLifetime에 의해 종료가 트리거될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="7e403-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="7e403-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="7e403-618">외부 제어</span><span class="sxs-lookup"><span data-stu-id="7e403-618">External control</span></span>

<span data-ttu-id="7e403-619">호스트 수명에 대한 직접 제어는 외부에서 호출할 수 있는 메서드를 사용하여 달성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="7e403-620">ASP.NET Core 앱은 호스트를 구성 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="7e403-621">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="7e403-622">이 문서에서는 HTTP 요청을 처리하지 않는 앱에 사용되는 ASP.NET Core 일반 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="7e403-623">일반 호스트의 목적은 웹 호스트 API에서 HTTP 파이프라인을 분리하여 호스트 시나리오의 더 광범위한 배열을 구현하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="7e403-624">일반 호스트에 기반을 둔 메시징, 백그라운드 작업 및 기타 HTTP 이외 워크로드는 구성, DI(종속성 주입) 및 로깅과 같은 교차 편집 기능에서 이점을 얻습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="7e403-625">일반 호스트는 ASP.NET Core 2.1의 새로운 기능이며 웹 호스팅 시나리오에 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="7e403-626">웹 호스팅 시나리오의 경우 [웹 호스트](xref:fundamentals/host/web-host)를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="7e403-627">일반 호스트는 향후 릴리스에서 웹 호스트를 대체하고 HTTP 및 HTTP 이외 시나리오에서 기본 호스트 API 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="7e403-628">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7e403-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7e403-629">[Visual Studio Code](https://code.visualstudio.com/)에서 샘플 앱을 실행할 때는 *외부 또는 통합 터미널*을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="7e403-630">`internalConsole`에서는 샘플을 실행하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="7e403-631">Visual Studio Code에서 콘솔을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="7e403-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="7e403-632">*.vscode/launch.json* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="7e403-633">**.NET Core 시작(콘솔)** 구성에서 **콘솔** 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="7e403-634">값을 `externalTerminal` 또는 `integratedTerminal` 중 하나로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="7e403-635">소개</span><span class="sxs-lookup"><span data-stu-id="7e403-635">Introduction</span></span>

<span data-ttu-id="7e403-636">일반 호스트 라이브러리는 <xref:Microsoft.Extensions.Hosting> 네임스페이스에서 사용할 수 있으며, [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 패키지에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="7e403-637">`Microsoft.Extensions.Hosting` 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 이상)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="7e403-638"><xref:Microsoft.Extensions.Hosting.IHostedService>는 코드 실행 진입점입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="7e403-639">각 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현은 [ConfigureServices의 서비스 등록](#configureservices) 순서대로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="7e403-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>는 호스트가 시작될 때 각 <xref:Microsoft.Extensions.Hosting.IHostedService>에서 호출되고, <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>는 호스트가 점진적으로 종료될 때 등록 순서의 역순으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="7e403-641">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="7e403-641">Set up a host</span></span>

<span data-ttu-id="7e403-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder>는 라이브러리 및 앱이 호스트를 초기화, 빌드 및 실행하는 데 사용하는 주 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="7e403-643">옵션</span><span class="sxs-lookup"><span data-stu-id="7e403-643">Options</span></span>

<span data-ttu-id="7e403-644"><xref:Microsoft.Extensions.Hosting.HostOptions>는 <xref:Microsoft.Extensions.Hosting.IHost>에 대한 옵션을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="7e403-645">시스템 종료 시간 제한</span><span class="sxs-lookup"><span data-stu-id="7e403-645">Shutdown timeout</span></span>

<span data-ttu-id="7e403-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>은 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="7e403-647">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-647">The default value is five seconds.</span></span>

<span data-ttu-id="7e403-648">`Program.Main`의 다음 옵션 구성은 기본 5초 시스템 종료 시간 제한을 20초로 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="7e403-649">기본 서비스</span><span class="sxs-lookup"><span data-stu-id="7e403-649">Default services</span></span>

<span data-ttu-id="7e403-650">호스트 초기화 중에 다음 서비스가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="7e403-651">[환경](xref:fundamentals/environments)(<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="7e403-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="7e403-652">[구성](xref:fundamentals/configuration/index)(<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="7e403-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="7e403-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="7e403-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="7e403-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="7e403-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="7e403-655">[옵션](xref:fundamentals/configuration/options)(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="7e403-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="7e403-656">[로깅](xref:fundamentals/logging/index)(<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="7e403-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="7e403-657">호스트 구성</span><span class="sxs-lookup"><span data-stu-id="7e403-657">Host configuration</span></span>

<span data-ttu-id="7e403-658">호스트 구성은 다음에 의해 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-658">Host configuration is created by:</span></span>

* <span data-ttu-id="7e403-659"><xref:Microsoft.Extensions.Hosting.IHostBuilder>에서 확장 메서드를 호출하여 [콘텐츠 루트](#content-root) 및 [환경](#environment)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="7e403-660"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>의 구성 공급자에서 구성을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="7e403-661">확장 메서드</span><span class="sxs-lookup"><span data-stu-id="7e403-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="7e403-662">애플리케이션 키(이름)</span><span class="sxs-lookup"><span data-stu-id="7e403-662">Application key (name)</span></span>

<span data-ttu-id="7e403-663">호스트를 생성하는 동안 호스트 구성에서 [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="7e403-664">값을 명시적으로 설정하려면 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="7e403-665">**키**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="7e403-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="7e403-666">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-666">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-667">**기본값**: 앱의 진입점을 포함하는 어셈블리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="7e403-668">**설정 방법**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="7e403-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="7e403-669">**환경 변수**: `<PREFIX_>APPLICATIONNAME`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="7e403-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="7e403-670">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="7e403-670">Content root</span></span>

<span data-ttu-id="7e403-671">이 설정은 호스트가 콘텐츠 파일을 검색하기 시작하는 지점을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="7e403-672">**키**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="7e403-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="7e403-673">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-673">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-674">**기본값**: 앱 어셈블리가 있는 폴더가 기본값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="7e403-675">**설정 방법**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="7e403-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="7e403-676">**환경 변수**: `<PREFIX_>CONTENTROOT`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="7e403-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="7e403-677">경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="7e403-678">자세한 내용은 [기본 사항: 콘텐츠 루트](xref:fundamentals/index#content-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="7e403-679">환경</span><span class="sxs-lookup"><span data-stu-id="7e403-679">Environment</span></span>

<span data-ttu-id="7e403-680">앱의 [환경](xref:fundamentals/environments)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="7e403-681">**키**: `environment`</span><span class="sxs-lookup"><span data-stu-id="7e403-681">**Key**: `environment`</span></span>  
<span data-ttu-id="7e403-682">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="7e403-682">**Type**: `string`</span></span>  
<span data-ttu-id="7e403-683">**기본**: `Production`</span><span class="sxs-lookup"><span data-stu-id="7e403-683">**Default**: `Production`</span></span>  
<span data-ttu-id="7e403-684">**설정 방법**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="7e403-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="7e403-685">**환경 변수**: `<PREFIX_>ENVIRONMENT`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="7e403-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="7e403-686">환경은 어떠한 값으로도 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-686">The environment can be set to any value.</span></span> <span data-ttu-id="7e403-687">프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="7e403-688">값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="7e403-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="7e403-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="7e403-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>은 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하여 호스트에 대한 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="7e403-691">호스트 구성은 앱의 빌드 프로세스에 사용할 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>를 초기화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="7e403-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="7e403-693">호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="7e403-694">기본적으로 공급자는 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-694">No providers are included by default.</span></span> <span data-ttu-id="7e403-695">다음을 포함하여 앱에 필요한 모든 구성 공급자를 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>에 명시적으로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="7e403-696">구성 파일(예: *hostsettings.json* 파일에서).</span><span class="sxs-lookup"><span data-stu-id="7e403-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="7e403-697">환경 변수 구성.</span><span class="sxs-lookup"><span data-stu-id="7e403-697">Environment variable configuration.</span></span>
* <span data-ttu-id="7e403-698">명령줄 인수 구성.</span><span class="sxs-lookup"><span data-stu-id="7e403-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="7e403-699">기타 필수 구성 공급자.</span><span class="sxs-lookup"><span data-stu-id="7e403-699">Any other required configuration providers.</span></span>

<span data-ttu-id="7e403-700">호스트의 파일 구성은 `SetBasePath`를 사용하여 앱의 기본 경로를 지정한 후 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider) 중 하나를 호출하여 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="7e403-701">샘플 앱은 JSON 파일인 *hostsettings.json*을 사용하고 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>을 호출하여 파일의 호스트 구성 설정을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="7e403-702">[환경 변수 구성](xref:fundamentals/configuration/index#environment-variables-configuration-provider)을 추가하려면 호스트 작성기에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="7e403-703">`AddEnvironmentVariables`는 선택적 사용자 정의 접두사를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="7e403-704">샘플 앱은 `PREFIX_` 접두사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="7e403-705">접두사는 환경 변수를 읽을 때 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="7e403-706">샘플 앱의 호스트가 구성되면 `PREFIX_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="7e403-707">[Visual Studio](https://visualstudio.microsoft.com)를 사용하거나 `dotnet run`을 통해 앱을 실행할 때 개발하는 동안에 환경 변수는 *Properties/launchSettings.json* 파일에 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="7e403-708">[Visual Studio Code](https://code.visualstudio.com/)에서 환경 변수는 개발하는 동안에 *.vscode/launch.json* 파일에 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="7e403-709">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="7e403-710">[명령줄 구성](xref:fundamentals/configuration/index#command-line-configuration-provider)은 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>을 호출하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="7e403-711">명령줄 구성이 마지막으로 추가되어 명령줄 인수가 이전 구성 공급자가 제공한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="7e403-712">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7e403-712">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="7e403-713">[applicationName](#application-key-name) 및 [contentRoot](#content-root) 키를 사용하여 추가 구성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="7e403-714"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 사용한 `HostBuilder` 구성 예:</span><span class="sxs-lookup"><span data-stu-id="7e403-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="7e403-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="7e403-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="7e403-716">앱 구성은 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 구현에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="7e403-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>은 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하여 앱에 대한 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="7e403-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="7e403-719">앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="7e403-720"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>에 의해 생성된 구성은 다음 작업에 대한 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 및 <xref:Microsoft.Extensions.Hosting.IHost.Services*>에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="7e403-721">앱 구성은 [ConfigureHostConfiguration](#configurehostconfiguration)에서 제공하는 호스트 구성을 자동으로 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="7e403-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 사용한 앱 구성 예:</span><span class="sxs-lookup"><span data-stu-id="7e403-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="7e403-723">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7e403-723">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="7e403-724">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="7e403-724">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="7e403-725">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="7e403-725">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="7e403-726">출력 디렉터리로 설정 파일을 이동하려면, 설정 파일을 프로젝트 파일 내 [MSBuild 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items)으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="7e403-727">샘플 앱은 다음 `<Content>` 항목과 함께 JSON 앱 설정 파일과 *hostsettings.json*을 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="7e403-728"><xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 및 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>와 같은 구성 확장 메서드에는 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) 및 [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)와 같은 추가 NuGet 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="7e403-729">앱에서 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 사용하는 경우가 아니면 이 패키지는 코어 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) 패키지에 추가로 프로젝트에 추가되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="7e403-730">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="7e403-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="7e403-731">ConfigureServices</span></span>

<span data-ttu-id="7e403-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>는 앱의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7e403-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="7e403-734">호스티드 서비스는 <xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스를 구현하는 백그라운드 작업 논리가 있는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="7e403-735">자세한 내용은 <xref:fundamentals/host/hosted-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="7e403-736">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)은 `AddHostedService` 확장 메서드를 사용하여 수명 이벤트, `LifetimeEventsHostedService` 및 시간 제한 백그라운드 작업에 대한 서비스 `TimedHostedService`를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="7e403-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="7e403-737">ConfigureLogging</span></span>

<span data-ttu-id="7e403-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>에는 제공된 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>를 구성하는 대리자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="7e403-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="7e403-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="7e403-740">UseConsoleLifetime</span></span>

<span data-ttu-id="7e403-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>을 호출하여 종료 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="7e403-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>은 [RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="7e403-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 수명 구현으로 미리 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="7e403-744">등록된 마지막 수명이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="7e403-745">컨테이너 구성</span><span class="sxs-lookup"><span data-stu-id="7e403-745">Container configuration</span></span>

<span data-ttu-id="7e403-746">호스트는 다른 컨테이너 연결을 지원하기 위해 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="7e403-747">팩터리 제공은 DI 컨테이너 등록의 일부가 아니지만 구체적 DI 컨테이너를 만드는 데 사용되는 내장 호스트입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="7e403-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*)는 앱의 서비스 공급자를 만드는 데 사용되는 기본 팩터리를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="7e403-749">사용자 지정 컨테이너 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 메서드로 관리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="7e403-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>는 기본 호스트 API 위에 컨테이너를 구성하기 위한 강력한 형식의 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="7e403-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="7e403-752">앱에 대한 서비스 컨테이너를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="7e403-753">서비스 컨테이너 팩터리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="7e403-754">팩터리를 사용하고 앱에 대 한 사용자 지정 서비스 컨테이너를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="7e403-755">확장성</span><span class="sxs-lookup"><span data-stu-id="7e403-755">Extensibility</span></span>

<span data-ttu-id="7e403-756"><xref:Microsoft.Extensions.Hosting.IHostBuilder>에서 확장 메서드를 사용하여 호스트 확장성이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="7e403-757">다음 예제는 <xref:fundamentals/host/hosted-services>에 설명된 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 예제를 사용하여 확장 메서드가 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 구현을 확장하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="7e403-758">앱은 `UseHostedService` 확장 메서드를 설정하여 `T`에서 전달되는 호스티드 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="7e403-759">호스트 관리</span><span class="sxs-lookup"><span data-stu-id="7e403-759">Manage the host</span></span>

<span data-ttu-id="7e403-760"><xref:Microsoft.Extensions.Hosting.IHost> 구현은 서비스 컨테이너에 등록된 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현의 시작 및 중지를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="7e403-761">Run</span><span class="sxs-lookup"><span data-stu-id="7e403-761">Run</span></span>

<span data-ttu-id="7e403-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="7e403-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-763">RunAsync</span></span>

<span data-ttu-id="7e403-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="7e403-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-765">RunConsoleAsync</span></span>

<span data-ttu-id="7e403-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔을 지원하고 호스트를 빌드 및 시작하며 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="7e403-767">Start 및 StopAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-767">Start and StopAsync</span></span>

<span data-ttu-id="7e403-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="7e403-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="7e403-770">StartAsync 및 StopAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="7e403-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>가 서버를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="7e403-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>가 서버를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="7e403-773">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="7e403-773">WaitForShutdown</span></span>

<span data-ttu-id="7e403-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`(<kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM 수신 대기)과 같이 <xref:Microsoft.Extensions.Hosting.IHostLifetime>을 통해 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="7e403-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="7e403-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="7e403-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="7e403-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="7e403-778">외부 제어</span><span class="sxs-lookup"><span data-stu-id="7e403-778">External control</span></span>

<span data-ttu-id="7e403-779">외부에서 호출할 수 있는 메서드를 사용하여 호스트의 외부 제어를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-779">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="7e403-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="7e403-781">이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="7e403-782">IHostingEnvironment 인터페이스</span><span class="sxs-lookup"><span data-stu-id="7e403-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="7e403-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>는 앱의 호스팅 환경에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="7e403-784">해당 속성 및 확장 메서드를 사용하기 위해 [생성자 주입](xref:fundamentals/dependency-injection)을 사용하여 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="7e403-785">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e403-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="7e403-786">IApplicationLifetime 인터페이스</span><span class="sxs-lookup"><span data-stu-id="7e403-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="7e403-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>은 점진적인 종료 요청을 비롯한 사후 시작 및 종료 작업을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="7e403-788">인터페이스에서 세 가지 속성은 취소 토큰으로, 시작 및 종료 이벤트를 정의하는 <xref:System.Action> 메서드를 등록하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="7e403-789">취소 토큰</span><span class="sxs-lookup"><span data-stu-id="7e403-789">Cancellation Token</span></span> | <span data-ttu-id="7e403-790">트리거되는 경우:</span><span class="sxs-lookup"><span data-stu-id="7e403-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="7e403-791">호스트가 완벽하게 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="7e403-792">호스트가 정상적으로 종료되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="7e403-793">모든 요청이 처리되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-793">All requests should be processed.</span></span> <span data-ttu-id="7e403-794">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="7e403-795">호스트가 정상적으로 종료되고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="7e403-796">요청은 계속 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-796">Requests may still be processing.</span></span> <span data-ttu-id="7e403-797">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="7e403-798">클래스에 대한 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 서비스 생성자 주입을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="7e403-799">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)은 `LifetimeEventsHostedService` 클래스(<xref:Microsoft.Extensions.Hosting.IHostedService> 구현)에 대한 생성자 주입을 사용하여 이벤트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="7e403-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="7e403-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="7e403-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>은 앱의 종료를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="7e403-802">다음 클래스에서는 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>을 사용하여 해당 클래스의 `Shutdown` 메서드를 호출하는 경우 앱을 정상 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="7e403-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="7e403-803">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7e403-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
