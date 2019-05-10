---
title: ASP.NET Core 웹 호스트
author: guardrex
description: 앱 시작 및 수명 관리를 담당하는 ASP.NET Core의 웹 호스트에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/host/web-host
ms.openlocfilehash: b391b5e514e750f64f30d33cf4eb91e489242eba
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64888978"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="a167e-103">ASP.NET Core 웹 호스트</span><span class="sxs-lookup"><span data-stu-id="a167e-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="a167e-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="a167e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a167e-105">ASP.NET Core 앱은 *호스트*를 구성 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-105">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="a167e-106">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-106">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="a167e-107">최소한으로 호스트는 서버 및 요청 처리 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-107">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="a167e-108">호스트는 로깅, 종속성 주입 및 구성을 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-108">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="a167e-109">이 항목의 1.1 버전인 경우 [ASP.NET Core 웹 호스트(버전 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Web-Host_1.1.pdf)를 다운로드하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-109">For the 1.1 version of this topic, download [ASP.NET Core Web Host (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Web-Host_1.1.pdf).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="a167e-110">이 문서에서는 웹앱 호스팅을 위한 ASP.NET Core 웹 호스트(<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>)를 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-110">This article covers the ASP.NET Core Web Host (<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>), which is for hosting web apps.</span></span> <span data-ttu-id="a167e-111">.NET 일반 호스트([IHostBuilder](/dotnet/api/microsoft.extensions.hosting.ihostbuilder))에 대한 자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-111">For information about the .NET Generic Host ([IHostBuilder](/dotnet/api/microsoft.extensions.hosting.ihostbuilder)), see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

<span data-ttu-id="a167e-112">이 문서에서는 ASP.NET Core 웹 호스트([IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder))를 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-112">This article covers the ASP.NET Core Web Host ([IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)).</span></span> <span data-ttu-id="a167e-113">ASP.NET Core 3.0에서는 일반 호스트가 웹 호스트를 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-113">In ASP.NET Core 3.0, Generic Host replaces Web Host.</span></span> <span data-ttu-id="a167e-114">자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-114">For more information, see [The host](xref:fundamentals/index#host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="a167e-115">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="a167e-115">Set up a host</span></span>

<span data-ttu-id="a167e-116">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)의 인스턴스를 사용하여 호스트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-116">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="a167e-117">이는 일반적으로 앱의 진입점에서 수행되는 `Main` 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-117">This is typically performed in the app's entry point, the `Main` method.</span></span> <span data-ttu-id="a167e-118">프로젝트 템플릿에서 `Main`은 *Program.cs*에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-118">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="a167e-119">일반적인 *Program.cs*는 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder)를 호출하여 호스트 설정을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-119">A typical *Program.cs* calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="a167e-120">`CreateDefaultBuilder`는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-120">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="a167e-121">앱의 호스팅 구성 공급자를 사용하여 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-121">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="a167e-122">Kestrel 서버의 기본 옵션은 <xref:fundamentals/servers/kestrel#kestrel-options>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-122">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="a167e-123">콘텐츠 루트를 [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)에서 반환된 경로로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-123">Sets the content root to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="a167e-124">다음에서 [호스트 구성](#host-configuration-values)을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-124">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="a167e-125">접두사가 `ASPNETCORE_`인 환경 변수(예: `ASPNETCORE_ENVIRONMENT`)입니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-125">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="a167e-126">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="a167e-126">Command-line arguments.</span></span>
* <span data-ttu-id="a167e-127">다음 순서대로 앱 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-127">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="a167e-128">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a167e-128">*appsettings.json*.</span></span>
  * <span data-ttu-id="a167e-129">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="a167e-129">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="a167e-130">앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [Secret Manager](xref:security/app-secrets)입니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-130">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="a167e-131">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="a167e-131">Environment variables.</span></span>
  * <span data-ttu-id="a167e-132">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="a167e-132">Command-line arguments.</span></span>
* <span data-ttu-id="a167e-133">콘솔 및 디버그 출력에 대한 [로깅](xref:fundamentals/logging/index)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-133">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="a167e-134">로깅은 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일의 로깅 구성 섹션에 지정된 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 규칙을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-134">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="a167e-135">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 사용하여 IIS에서 실행하는 경우 `CreateDefaultBuilder`는 앱의 기본 주소와 포트를 구성하는 [IIS 통합](xref:host-and-deploy/iis/index)을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-135">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="a167e-136">또한 IIS 통합은 [시작 오류를 캡처](#capture-startup-errors)하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-136">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="a167e-137">IIS 기본 옵션은 <xref:host-and-deploy/iis/index#iis-options>를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-137">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="a167e-138">앱의 환경이 개발인 경우 [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes)을 `true`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-138">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="a167e-139">자세한 내용은 [범위 유효성 검사](#scope-validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-139">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="a167e-140">`CreateDefaultBuilder`에 의해 정의된 구성은 [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), 기타 메서드 및 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)의 확장 메서드로 재정의되고 확대될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-140">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="a167e-141">몇 가지 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-141">A few examples follow:</span></span>

* <span data-ttu-id="a167e-142">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration)은 앱에 대한 추가 `IConfiguration`을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-142">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="a167e-143">다음 `ConfigureAppConfiguration` 호출은 *appsettings.xml* 파일에 앱 구성을 포함하는 대리자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-143">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="a167e-144">`ConfigureAppConfiguration`이 여러 번 호출될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-144">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="a167e-145">이 구성은 호스트(예: 서버 URL 또는 환경)에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-145">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="a167e-146">[호스트 구성 값](#host-configuration-values) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-146">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="a167e-147">다음 `ConfigureLogging` 호출은 최소 로깅 수준([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel))을 [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel)으로 구성하는 대리자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-147">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="a167e-148">이 설정은 `CreateDefaultBuilder`에 의해 구성된 *appsettings.Development.json*(`LogLevel.Debug`) 및 *appsettings.Production.json*(`LogLevel.Error`)의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-148">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a167e-149">`ConfigureLogging`이 여러 번 호출될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-149">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="a167e-150">`ConfigureKestrel`에 대한 다음 호출은 Kestrel이 `CreateDefaultBuilder`에 의해 구성될 때 설정된 30,000,000바이트의 기본 [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize)를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-150">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="a167e-151">[UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel)에 대한 다음 호출은 Kestrel이 `CreateDefaultBuilder`에 의해 구성될 때 설정된 30,000,000바이트의 기본 [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize)를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-151">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="a167e-152">*콘텐츠 루트*는 호스트가 MVC 뷰 파일과 같은 콘텐츠 파일을 검색하는 위치를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-152">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="a167e-153">앱이 프로젝트의 루트 폴더에서 시작되면 프로젝트의 루트 폴더가 콘텐츠 루트로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-153">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="a167e-154">이것이 [Visual Studio](https://visualstudio.microsoft.com) 및 [dotnet 새 템플릿](/dotnet/core/tools/dotnet-new)에서 사용되는 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-154">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="a167e-155">앱 구성에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-155">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="a167e-156">ASP.NET Core 2.x에서는 정적 `CreateDefaultBuilder` 메서드 사용에 대한 대안으로 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)에서 호스트를 만들 수 있도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-156">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span> <span data-ttu-id="a167e-157">자세한 내용은 ASP.NET Core 1.x 탭을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-157">For more information, see the ASP.NET Core 1.x tab.</span></span>

<span data-ttu-id="a167e-158">호스트를 설정할 때 [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) 및 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-158">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) methods can be provided.</span></span> <span data-ttu-id="a167e-159">`Startup` 클래스가 지정된 경우 `Configure` 메서드를 정의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-159">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="a167e-160">자세한 내용은 <xref:fundamentals/startup>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-160">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="a167e-161">`ConfigureServices`에 대한 여러 호출은 서로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-161">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="a167e-162">`WebHostBuilder`에서 `Configure` 또는 `UseStartup`에 대한 여러 호출은 이전 설정을 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-162">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="a167e-163">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="a167e-163">Host configuration values</span></span>

<span data-ttu-id="a167e-164">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)는 호스트 구성 값을 설정하기 위해 다음 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-164">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="a167e-165">`ASPNETCORE_{configurationKey}` 형식의 환경 변수를 포함하는 호스트 빌더 구성.</span><span class="sxs-lookup"><span data-stu-id="a167e-165">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="a167e-166">예를 들어, `ASPNETCORE_ENVIRONMENT`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-166">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="a167e-167">[UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) 및 [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) 같은 확장입니다([구성 재정의](#override-configuration) 섹션 참조).</span><span class="sxs-lookup"><span data-stu-id="a167e-167">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="a167e-168">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) 및 연결된 키.</span><span class="sxs-lookup"><span data-stu-id="a167e-168">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="a167e-169">`UseSetting`을 사용하여 값을 설정할 때 값은 형식에 관계 없이 문자열로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-169">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="a167e-170">호스트는 마지막에 값을 설정한 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-170">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="a167e-171">자세한 내용은 다음 섹션의 [구성 재정의](#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-171">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="a167e-172">애플리케이션 키(이름)</span><span class="sxs-lookup"><span data-stu-id="a167e-172">Application Key (Name)</span></span>

<span data-ttu-id="a167e-173">[UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) 또는 [구성](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure)이 호스트 생성 중에 호출되는 경우 [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) 속성이 자동으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-173">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="a167e-174">해당 값은 앱의 진입점을 포함하는 어셈블리의 이름으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-174">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="a167e-175">값을 명시적으로 설정하려면 [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-175">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

<span data-ttu-id="a167e-176">**키**: applicationName</span><span class="sxs-lookup"><span data-stu-id="a167e-176">**Key**: applicationName</span></span>  
<span data-ttu-id="a167e-177">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="a167e-177">**Type**: *string*</span></span>  
<span data-ttu-id="a167e-178">**기본값**: 앱의 진입점을 포함하는 어셈블리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-178">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="a167e-179">**설정 방법**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a167e-179">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a167e-180">**환경 변수**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="a167e-180">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="a167e-181">시작 오류 캡처</span><span class="sxs-lookup"><span data-stu-id="a167e-181">Capture Startup Errors</span></span>

<span data-ttu-id="a167e-182">이 설정은 시작 오류의 캡처를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-182">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="a167e-183">**키**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="a167e-183">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="a167e-184">**형식**: *bool*(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="a167e-184">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a167e-185">**기본값**: 기본값이 `true`인 IIS 뒤에 있는 Kestrel로 앱이 실행하지 않는 한 기본값은 `false`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-185">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="a167e-186">**설정 방법**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="a167e-186">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="a167e-187">**환경 변수**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="a167e-187">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="a167e-188">`false`인 경우 시작 시 오류가 발생하면 호스트가 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-188">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="a167e-189">`true`이면 호스트가 시작 시 예외를 캡처하고 서버 시작을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-189">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="a167e-190">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="a167e-190">Content Root</span></span>

<span data-ttu-id="a167e-191">이 설정은 ASP.NET Core가 MVC 뷰와 같은 콘텐츠 파일을 검색하기 시작하는 지점을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-191">This setting determines where ASP.NET Core begins searching for content files, such as MVC views.</span></span> 

<span data-ttu-id="a167e-192">**키**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="a167e-192">**Key**: contentRoot</span></span>  
<span data-ttu-id="a167e-193">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="a167e-193">**Type**: *string*</span></span>  
<span data-ttu-id="a167e-194">**기본값**: 앱 어셈블리가 있는 폴더가 기본값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-194">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="a167e-195">**설정 방법**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="a167e-195">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="a167e-196">**환경 변수**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="a167e-196">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="a167e-197">콘텐츠 루트는 또한 [웹 루트 설정](#web-root)에 대한 기본 경로로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-197">The content root is also used as the base path for the [Web Root setting](#web-root).</span></span> <span data-ttu-id="a167e-198">경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-198">If the path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

### <a name="detailed-errors"></a><span data-ttu-id="a167e-199">자세한 오류</span><span class="sxs-lookup"><span data-stu-id="a167e-199">Detailed Errors</span></span>

<span data-ttu-id="a167e-200">자세한 오류를 캡처해야 하는지를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-200">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="a167e-201">**키**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="a167e-201">**Key**: detailedErrors</span></span>  
<span data-ttu-id="a167e-202">**형식**: *bool*(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="a167e-202">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a167e-203">**기본값**: false</span><span class="sxs-lookup"><span data-stu-id="a167e-203">**Default**: false</span></span>  
<span data-ttu-id="a167e-204">**설정 방법**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a167e-204">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a167e-205">**환경 변수**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="a167e-205">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="a167e-206">사용하는 경우(또는 <a href="#environment">환경</a>이 `Development`로 설정된 경우) 앱은 자세한 예외를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-206">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="a167e-207">환경</span><span class="sxs-lookup"><span data-stu-id="a167e-207">Environment</span></span>

<span data-ttu-id="a167e-208">앱의 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-208">Sets the app's environment.</span></span>

<span data-ttu-id="a167e-209">**키**: environment</span><span class="sxs-lookup"><span data-stu-id="a167e-209">**Key**: environment</span></span>  
<span data-ttu-id="a167e-210">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="a167e-210">**Type**: *string*</span></span>  
<span data-ttu-id="a167e-211">**기본값**: 프로덕션</span><span class="sxs-lookup"><span data-stu-id="a167e-211">**Default**: Production</span></span>  
<span data-ttu-id="a167e-212">**설정 방법**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="a167e-212">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="a167e-213">**환경 변수**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="a167e-213">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="a167e-214">환경은 어떠한 값으로도 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-214">The environment can be set to any value.</span></span> <span data-ttu-id="a167e-215">프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-215">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="a167e-216">값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-216">Values aren't case sensitive.</span></span> <span data-ttu-id="a167e-217">기본적으로 *환경*은 `ASPNETCORE_ENVIRONMENT` 환경 변수에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-217">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="a167e-218">[Visual Studio](https://visualstudio.microsoft.com)를 사용하는 경우 환경 변수는 *launchSettings.json* 파일에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-218">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="a167e-219">자세한 내용은 <xref:fundamentals/environments>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-219">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="a167e-220">호스팅 시작 어셈블리</span><span class="sxs-lookup"><span data-stu-id="a167e-220">Hosting Startup Assemblies</span></span>

<span data-ttu-id="a167e-221">앱의 호스팅 시작 어셈블리를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-221">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="a167e-222">**키**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="a167e-222">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="a167e-223">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="a167e-223">**Type**: *string*</span></span>  
<span data-ttu-id="a167e-224">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="a167e-224">**Default**: Empty string</span></span>  
<span data-ttu-id="a167e-225">**설정 방법**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a167e-225">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a167e-226">**환경 변수**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="a167e-226">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="a167e-227">시작 시 로드할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-227">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="a167e-228">구성 값의 기본값이 빈 문자열이지만, 호스팅 시작 어셈블리는 항상 앱의 어셈블리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-228">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="a167e-229">호스팅 시작 어셈블리가 제공되는 경우, 시작 시 앱이 일반적인 서비스를 빌드할 때 로드를 위해 앱의 어셈블리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-229">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="a167e-230">HTTPS 포트</span><span class="sxs-lookup"><span data-stu-id="a167e-230">HTTPS Port</span></span>

<span data-ttu-id="a167e-231">HTTPS 리디렉션 포트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-231">Set the HTTPS redirect port.</span></span> <span data-ttu-id="a167e-232">[HTTPS 적용](xref:security/enforcing-ssl)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-232">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="a167e-233">**키**: https_port **형식**: *문자열*
**기본값**: 기본값은 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-233">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="a167e-234">**설정 방법**: `UseSetting`
**환경 변수**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="a167e-234">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="a167e-235">호스팅 시작 어셈블리 제외</span><span class="sxs-lookup"><span data-stu-id="a167e-235">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="a167e-236">시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-236">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="a167e-237">**키**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="a167e-237">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="a167e-238">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="a167e-238">**Type**: *string*</span></span>  
<span data-ttu-id="a167e-239">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="a167e-239">**Default**: Empty string</span></span>  
<span data-ttu-id="a167e-240">**설정 방법**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a167e-240">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a167e-241">**환경 변수**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="a167e-241">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="a167e-242">호스팅 URL 선호</span><span class="sxs-lookup"><span data-stu-id="a167e-242">Prefer Hosting URLs</span></span>

<span data-ttu-id="a167e-243">호스트가 `IServer` 구현으로 구성된 URL 대신에 `WebHostBuilder`로 구성된 URL에서 수신 대기할지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-243">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="a167e-244">**키**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="a167e-244">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="a167e-245">**형식**: *bool*(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="a167e-245">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a167e-246">**기본값**: true</span><span class="sxs-lookup"><span data-stu-id="a167e-246">**Default**: true</span></span>  
<span data-ttu-id="a167e-247">**설정 방법**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="a167e-247">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="a167e-248">**환경 변수**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="a167e-248">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="a167e-249">호스팅 시작 방지</span><span class="sxs-lookup"><span data-stu-id="a167e-249">Prevent Hosting Startup</span></span>

<span data-ttu-id="a167e-250">앱의 어셈블리에 의해 구성된 호스팅 시작 어셈블리를 포함한 호스팅 시작 어셈블리의 자동 로딩을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-250">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="a167e-251">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-251">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="a167e-252">**키**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="a167e-252">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="a167e-253">**형식**: *bool*(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="a167e-253">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a167e-254">**기본값**: false</span><span class="sxs-lookup"><span data-stu-id="a167e-254">**Default**: false</span></span>  
<span data-ttu-id="a167e-255">**설정 방법**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a167e-255">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a167e-256">**환경 변수**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="a167e-256">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="a167e-257">서버 URL</span><span class="sxs-lookup"><span data-stu-id="a167e-257">Server URLs</span></span>

<span data-ttu-id="a167e-258">서버에서 요청을 수신해야 하는 포트와 프로토콜이 있는 IP 주소 또는 호스트 주소를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-258">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="a167e-259">**키**: urls</span><span class="sxs-lookup"><span data-stu-id="a167e-259">**Key**: urls</span></span>  
<span data-ttu-id="a167e-260">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="a167e-260">**Type**: *string*</span></span>  
<span data-ttu-id="a167e-261">**기본**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="a167e-261">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="a167e-262">**설정 방법**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="a167e-262">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="a167e-263">**환경 변수**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="a167e-263">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="a167e-264">서버가 응답해야 하는 세미콜론으로 구분된(;) URL 접두사의 목록으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-264">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="a167e-265">예를 들어, `http://localhost:123`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-265">For example, `http://localhost:123`.</span></span> <span data-ttu-id="a167e-266">“\*”를 사용하여 서버가 지정된 포트 및 프로토콜을 사용하는 IP 주소 또는 호스트 이름에서 요청을 수신해야 함을 나타냅니다(예: `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="a167e-266">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="a167e-267">프로토콜(`http://` 또는 `https://`)은 각 URL에 포함되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-267">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="a167e-268">지원되는 형식은 서버마다 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-268">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="a167e-269">Kestrel에는 자체 엔드포인트 구성 API가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-269">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="a167e-270">자세한 내용은 <xref:fundamentals/servers/kestrel#endpoint-configuration>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-270">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="a167e-271">시스템 종료 제한 시간</span><span class="sxs-lookup"><span data-stu-id="a167e-271">Shutdown Timeout</span></span>

<span data-ttu-id="a167e-272">웹 호스트가 종료될 때까지 기다리는 시간을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-272">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="a167e-273">**키**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="a167e-273">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="a167e-274">**형식**: *int*</span><span class="sxs-lookup"><span data-stu-id="a167e-274">**Type**: *int*</span></span>  
<span data-ttu-id="a167e-275">**기본값**: 5</span><span class="sxs-lookup"><span data-stu-id="a167e-275">**Default**: 5</span></span>  
<span data-ttu-id="a167e-276">**설정 방법**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="a167e-276">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="a167e-277">**환경 변수**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="a167e-277">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="a167e-278">키가 `UseSetting`을 통해 *int*를 허용하더라도(예: `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`) [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) 확장 메서드는 [TimeSpan](/dotnet/api/system.timespan)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-278">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="a167e-279">시간 제한 기간 동안 호스팅:</span><span class="sxs-lookup"><span data-stu-id="a167e-279">During the timeout period, hosting:</span></span>

* <span data-ttu-id="a167e-280">[IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping)을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-280">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="a167e-281">중지하지 못한 서비스에 대한 모든 오류를 기록하면서 호스팅된 서비스 중지를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-281">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="a167e-282">모든 호스팅된 서비스가 중지하기 전에 시간 제한 기간이 만료되면 앱이 종료될 때 모든 활성화된 나머지 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-282">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="a167e-283">처리를 완료하지 않은 경우에도 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-283">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="a167e-284">서비스를 중지하는 데 시간이 더 필요한 경우 시간 제한을 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-284">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="a167e-285">시작 어셈블리</span><span class="sxs-lookup"><span data-stu-id="a167e-285">Startup Assembly</span></span>

<span data-ttu-id="a167e-286">`Startup` 클래스를 검색할 어셈블리를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-286">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="a167e-287">**키**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="a167e-287">**Key**: startupAssembly</span></span>  
<span data-ttu-id="a167e-288">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="a167e-288">**Type**: *string*</span></span>  
<span data-ttu-id="a167e-289">**기본값**: 앱의 어셈블리</span><span class="sxs-lookup"><span data-stu-id="a167e-289">**Default**: The app's assembly</span></span>  
<span data-ttu-id="a167e-290">**설정 방법**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="a167e-290">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="a167e-291">**환경 변수**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="a167e-291">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="a167e-292">이름(`string`) 또는 형식(`TStartup`)별 어셈블리를 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-292">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="a167e-293">`UseStartup` 메서드가 여러 개 호출된 경우 마지막 메서드가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-293">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="a167e-294">웹 루트</span><span class="sxs-lookup"><span data-stu-id="a167e-294">Web Root</span></span>

<span data-ttu-id="a167e-295">앱의 정적 자산에 대한 상대 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-295">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="a167e-296">**키**: webroot</span><span class="sxs-lookup"><span data-stu-id="a167e-296">**Key**: webroot</span></span>  
<span data-ttu-id="a167e-297">**형식**: *string*</span><span class="sxs-lookup"><span data-stu-id="a167e-297">**Type**: *string*</span></span>  
<span data-ttu-id="a167e-298">**기본값**: 기본값이 지정되지 않은 경우 기본값은 “(Content Root)/wwwroot”입니다(경로가 존재하는 경우).</span><span class="sxs-lookup"><span data-stu-id="a167e-298">**Default**: If not specified, the default is "(Content Root)/wwwroot", if the path exists.</span></span> <span data-ttu-id="a167e-299">경로가 존재하지 않다면 no-op 파일 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-299">If the path doesn't exist, then a no-op file provider is used.</span></span>  
<span data-ttu-id="a167e-300">**설정 방법**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="a167e-300">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="a167e-301">**환경 변수**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="a167e-301">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

## <a name="override-configuration"></a><span data-ttu-id="a167e-302">구성 재정의</span><span class="sxs-lookup"><span data-stu-id="a167e-302">Override configuration</span></span>

<span data-ttu-id="a167e-303">[구성](xref:fundamentals/configuration/index)을 사용하여 웹 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-303">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="a167e-304">다음 예제에서는 호스트 구성이 필요에 따라 *hostsettings.json* 파일에 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-304">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="a167e-305">*hostsettings.json* 파일에서 로드된 모든 구성은 명령줄 인수에 의해 재정의될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-305">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="a167e-306">빌드된 구성(`config`에 있음)은 [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration)을 통해 호스트를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-306">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="a167e-307">`IWebHostBuilder` 구성이 앱의 구성에 추가되지만 반대의 경우에는 true가 아닙니다. &mdash;`ConfigureAppConfiguration`은 `IWebHostBuilder` 구성에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-307">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="a167e-308">먼저 *hostsettings.json* 구성으로 `UseUrls`에 의해 제공되는 구성을 재정의하고, 두 번째로 명령줄 인수 구성을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-308">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="a167e-309">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a167e-309">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="a167e-310">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) 확장 메서드는 현재 `GetSection`에서 반환되는 구성 섹션을 구문 분석할 수 없습니다(예: `.UseConfiguration(Configuration.GetSection("section"))`).</span><span class="sxs-lookup"><span data-stu-id="a167e-310">The [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="a167e-311">`GetSection` 메서드는 요청된 섹션에 대한 구성 키를 필터링하지만 키에 있는 섹션 이름을 그대로 둡니다(예: `section:urls`, `section:environment`).</span><span class="sxs-lookup"><span data-stu-id="a167e-311">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="a167e-312">`UseConfiguration` 메서드에서는 키가 `WebHostBuilder` 키와 일치해야 합니다(예: `urls`, `environment`).</span><span class="sxs-lookup"><span data-stu-id="a167e-312">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="a167e-313">키에서 섹션 이름의 존재는 섹션 값으로 호스트를 구성할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-313">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="a167e-314">이 문제는 향후 릴리스에서 해결될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-314">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="a167e-315">자세한 내용 및 해결 방법은 [전체 키를 사용하여 WebHostBuilder.UseConfiguration에 구성 섹션을 전달](https://github.com/aspnet/Hosting/issues/839)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-315">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>
>
> <span data-ttu-id="a167e-316">`UseConfiguration`은 제공된 `IConfiguration`의 키만 호스트 빌더 구성으로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-316">`UseConfiguration` only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="a167e-317">따라서 JSON, INI 및 XML 설정 파일에 대해 `reloadOnChange: true`를 설정해도 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-317">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="a167e-318">특정 URL에서 실행하는 호스트를 지정하려면 [dotnet run](/dotnet/core/tools/dotnet-run) 실행 시 원하는 값을 명령 프롬프트에서 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-318">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="a167e-319">명령줄 인수는 *hostsettings.json* 파일의 `urls` 값을 재정의하고, 서버는 포트 8080에서 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-319">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```console
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="a167e-320">호스트 관리</span><span class="sxs-lookup"><span data-stu-id="a167e-320">Manage the host</span></span>

<span data-ttu-id="a167e-321">**실행**</span><span class="sxs-lookup"><span data-stu-id="a167e-321">**Run**</span></span>

<span data-ttu-id="a167e-322">`Run` 메서드는 웹앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-322">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="a167e-323">**시작**</span><span class="sxs-lookup"><span data-stu-id="a167e-323">**Start**</span></span>

<span data-ttu-id="a167e-324">해당 `Start` 메서드를 호출하여 비차단 방식으로 호스트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-324">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="a167e-325">URL 목록이 `Start` 메서드에 전달되면 지정된 URL에서 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-325">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="a167e-326">앱은 정적 편의 메서드를 사용하여 `CreateDefaultBuilder`의 미리 구성된 기본 값을 사용하는 새 호스트를 초기화하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-326">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="a167e-327">이러한 메서드는 콘솔 출력 없이 서버를 시작하고 [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown)으로 중단(Ctrl-C/SIGINT 또는 SIGTERM)될 때까지 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-327">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="a167e-328">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="a167e-328">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="a167e-329">`RequestDelegate`로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-329">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a167e-330">`http://localhost:5000`에 대한 브라우저에서 요청을 수행하여 “Hello World!” 응답을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-330">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="a167e-331">`WaitForShutdown`은 중단(Ctrl-C/SIGINT 또는 SIGTERM)이 발생할 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-331">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="a167e-332">앱은 `Console.WriteLine` 메시지를 표시하고 keypress가 종료될 때까지 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-332">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="a167e-333">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="a167e-333">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="a167e-334">URL 및 `RequestDelegate`로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-334">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a167e-335">앱이 `http://localhost:8080`에서 응답한다는 점을 제외하고 **Start(RequestDelegate app)** 와 동일한 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-335">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="a167e-336">**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="a167e-336">**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="a167e-337">`IRouteBuilder`의 인스턴스([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/))를 사용하여 라우팅 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-337">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a167e-338">예제에서는 다음 브라우저 요청을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-338">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="a167e-339">요청</span><span class="sxs-lookup"><span data-stu-id="a167e-339">Request</span></span>                                    | <span data-ttu-id="a167e-340">응답</span><span class="sxs-lookup"><span data-stu-id="a167e-340">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="a167e-341">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="a167e-341">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="a167e-342">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="a167e-342">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="a167e-343">“ooops!” 문자열을 사용하여 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-343">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="a167e-344">“Uh oh!” 문자열을 사용하여 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-344">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="a167e-345">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="a167e-345">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="a167e-346">Hello World!</span><span class="sxs-lookup"><span data-stu-id="a167e-346">Hello World!</span></span>                             |

<span data-ttu-id="a167e-347">`WaitForShutdown`은 중단(Ctrl-C/SIGINT 또는 SIGTERM)이 발생할 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-347">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="a167e-348">앱은 `Console.WriteLine` 메시지를 표시하고 keypress가 종료될 때까지 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-348">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="a167e-349">**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="a167e-349">**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="a167e-350">URL 및 `IRouteBuilder`의 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-350">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a167e-351">앱이 `http://localhost:8080`에서 응답한다는 점을 제외하고 **Start(Action&lt;IRouteBuilder&gt; routeBuilder)** 와 동일한 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-351">Produces the same result as **Start(Action&lt;IRouteBuilder&gt; routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="a167e-352">**StartWith(Action&lt;IApplicationBuilder&gt; app)**</span><span class="sxs-lookup"><span data-stu-id="a167e-352">**StartWith(Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="a167e-353">대리자를 제공하여 `IApplicationBuilder`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-353">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a167e-354">`http://localhost:5000`에 대한 브라우저에서 요청을 수행하여 “Hello World!” 응답을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-354">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="a167e-355">`WaitForShutdown`은 중단(Ctrl-C/SIGINT 또는 SIGTERM)이 발생할 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-355">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="a167e-356">앱은 `Console.WriteLine` 메시지를 표시하고 keypress가 종료될 때까지 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-356">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="a167e-357">**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**</span><span class="sxs-lookup"><span data-stu-id="a167e-357">**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="a167e-358">URL 및 대리자를 제공하여 `IApplicationBuilder`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-358">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a167e-359">앱이 `http://localhost:8080`에서 응답한다는 점을 제외하고 **StartWith(Action&lt;IApplicationBuilder&gt; app)** 와 동일한 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-359">Produces the same result as **StartWith(Action&lt;IApplicationBuilder&gt; app)**, except the app responds on `http://localhost:8080`.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="a167e-360">IHostingEnvironment 인터페이스</span><span class="sxs-lookup"><span data-stu-id="a167e-360">IHostingEnvironment interface</span></span>

<span data-ttu-id="a167e-361">[IHostingEnvironment 인터페이스](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment)는 앱의 웹 호스팅 환경에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-361">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="a167e-362">해당 속성 및 확장 메서드를 사용하기 위해 [생성자 주입](xref:fundamentals/dependency-injection)을 사용하여 `IHostingEnvironment`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-362">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="a167e-363">[규칙 기반 접근 방식](xref:fundamentals/environments#environment-based-startup-class-and-methods)은 시작할 때 환경에 따라 앱을 구성하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-363">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="a167e-364">또는 `ConfigureServices`에서 사용할 수 있도록 `IHostingEnvironment`를 `Startup` 생성자에 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-364">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="a167e-365">`IsDevelopment` 확장 메서드 외에 `IHostingEnvironment`는 `IsStaging`, `IsProduction` 및 `IsEnvironment(string environmentName)` 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-365">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="a167e-366">자세한 내용은 <xref:fundamentals/environments>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a167e-366">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a167e-367">또한 `IHostingEnvironment` 서비스를 파이프라인 처리를 설정하기 위한 `Configure` 메서드에 직접 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-367">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="a167e-368">사용자 지정 [미들웨어](xref:fundamentals/middleware/write)를 만들 때 `IHostingEnvironment`를 `Invoke` 메서드에 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-368">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="a167e-369">IApplicationLifetime 인터페이스</span><span class="sxs-lookup"><span data-stu-id="a167e-369">IApplicationLifetime interface</span></span>

<span data-ttu-id="a167e-370">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime)은 사후 시작 및 종료 작업을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-370">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="a167e-371">인터페이스에서 세 가지 속성은 취소 토큰으로, 시작 및 종료 이벤트를 정의하는 `Action` 메서드를 등록하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-371">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="a167e-372">취소 토큰</span><span class="sxs-lookup"><span data-stu-id="a167e-372">Cancellation Token</span></span>    | <span data-ttu-id="a167e-373">트리거되는 경우:</span><span class="sxs-lookup"><span data-stu-id="a167e-373">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="a167e-374">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="a167e-374">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="a167e-375">호스트가 완벽하게 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-375">The host has fully started.</span></span> |
| [<span data-ttu-id="a167e-376">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="a167e-376">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="a167e-377">호스트가 정상적으로 종료되었습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-377">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="a167e-378">모든 요청이 처리되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-378">All requests should be processed.</span></span> <span data-ttu-id="a167e-379">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-379">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="a167e-380">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="a167e-380">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="a167e-381">호스트가 정상적으로 종료되고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-381">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="a167e-382">요청은 계속 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-382">Requests may still be processing.</span></span> <span data-ttu-id="a167e-383">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-383">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="a167e-384">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication)은 앱의 종료를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-384">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="a167e-385">다음 클래스에서는 `StopApplication`을 사용하여 해당 클래스의 `Shutdown` 메서드를 호출하는 경우 앱을 정상 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-385">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="a167e-386">범위 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="a167e-386">Scope validation</span></span>

<span data-ttu-id="a167e-387">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder)는 앱의 환경이 개발인 경우 [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes)를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-387">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="a167e-388">`ValidateScopes`이 `true`로 설정된 경우 기본 서비스 공급자는 다음을 확인하기 위해 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-388">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="a167e-389">범위가 지정된 서비스는 직접 또는 간접적으로 루트 서비스 공급자에서 해결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-389">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="a167e-390">범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-390">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="a167e-391">루트 서비스 공급자는 [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider)를 호출할 때 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-391">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="a167e-392">루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱/서버의 수명에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-392">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="a167e-393">범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-393">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="a167e-394">범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 효과적으로 싱글톤으로 승격됩니다. 해당 서비스는 앱/서버가 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-394">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="a167e-395">서비스 범위의 유효성 검사는 `BuildServiceProvider`이 호출될 경우 이러한 상황을 catch합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-395">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="a167e-396">프로덕션 환경을 포함하여 범위의 유효성을 검사하려면 호스트 작성기에서 [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions)을 [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider)로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a167e-396">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="a167e-397">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a167e-397">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
