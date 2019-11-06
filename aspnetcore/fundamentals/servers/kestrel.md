---
title: ASP.NET Core에서 Kestrel 웹 서버 구현
author: guardrex
description: ASP.NET Core의 플랫폼 간 웹 서버인 Kestrel에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: beaf6ac49359adfdc2dc24221eab04cc853646a9
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143445"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="c1e5e-103">ASP.NET Core에서 Kestrel 웹 서버 구현</span><span class="sxs-lookup"><span data-stu-id="c1e5e-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="c1e5e-104">작성자: [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c1e5e-105">Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c1e5e-106">Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="c1e5e-107">Kestrel에서는 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="c1e5e-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c1e5e-108">HTTPS</span></span>
* <span data-ttu-id="c1e5e-109">[Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드</span><span class="sxs-lookup"><span data-stu-id="c1e5e-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="c1e5e-110">Nginx 뒤의 고성능을 위한 Unix 소켓</span><span class="sxs-lookup"><span data-stu-id="c1e5e-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="c1e5e-111">HTTP/2(macOS&dagger; 제외)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="c1e5e-112">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="c1e5e-113">Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="c1e5e-114">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c1e5e-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="c1e5e-115">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="c1e5e-115">HTTP/2 support</span></span>

<span data-ttu-id="c1e5e-116">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해 [HTTP/2](https://httpwg.org/specs/rfc7540.html)를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="c1e5e-117">운영 체제&dagger;</span><span class="sxs-lookup"><span data-stu-id="c1e5e-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="c1e5e-118">Windows Server 2016/Windows 10 이상&Dagger;</span><span class="sxs-lookup"><span data-stu-id="c1e5e-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="c1e5e-119">Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="c1e5e-120">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="c1e5e-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="c1e5e-121">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="c1e5e-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="c1e5e-122">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="c1e5e-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c1e5e-123">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="c1e5e-124">&Dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="c1e5e-125">이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="c1e5e-126">TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="c1e5e-127">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="c1e5e-128">HTTP/2는 기본적으로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="c1e5e-129">구성에 대한 자세한 내용은 [Kestrel 옵션](#kestrel-options) 및 [ListenOptions.Protocols](#listenoptionsprotocols) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="c1e5e-130">Kestrel을 역방향 프록시와 함께 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="c1e5e-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="c1e5e-131">Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버*와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c1e5e-132">역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="c1e5e-133">Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="c1e5e-135">Kestrel은 역방향 프록시 구성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c1e5e-137">역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="c1e5e-138">역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="c1e5e-139">Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="c1e5e-140">포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="c1e5e-141">역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="c1e5e-142">역방향 프록시:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-142">A reverse proxy:</span></span>

* <span data-ttu-id="c1e5e-143">호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="c1e5e-144">구성 및 방어의 추가 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="c1e5e-145">기존 인프라와 잘 통합될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="c1e5e-146">부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="c1e5e-147">역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="c1e5e-148">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="c1e5e-149">ASP.NET Core 앱에서 Kestrel을 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="c1e5e-149">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="c1e5e-150">ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="c1e5e-151">*Program.cs*에서 템플릿 코드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> 숨은 기능을 호출하는 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-151">In *Program.cs*, the template code calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="c1e5e-152">`CreateDefaultBuilder`와 호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/generic-host#set-up-a-host>의 *호스트 설정* 및 *기본 작성기 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-152">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="c1e5e-153">`CreateDefaultBuilder` 및 `ConfigureWebHostDefaults`를 호출한 후 추가 구성을 제공하려면 `ConfigureKestrel`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-153">To provide additional configuration after calling `CreateDefaultBuilder` and `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="c1e5e-154">앱이 호스트를 설정하기 위해 `CreateDefaultBuilder`를 호출하지 않는 경우 `ConfigureKestrel`을 호출하기 **전에** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-154">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new HostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseIISIntegration()
            .UseStartup<Startup>();
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="c1e5e-155">Kestrel 옵션</span><span class="sxs-lookup"><span data-stu-id="c1e5e-155">Kestrel options</span></span>

<span data-ttu-id="c1e5e-156">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-156">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="c1e5e-157"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-157">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="c1e5e-158">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-158">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="c1e5e-159">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-159">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="c1e5e-160">다음 예에서 C# 코드로 구성된 Kestrel 옵션도 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-160">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="c1e5e-161">예를 들어 파일 구성 공급자는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-161">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

<span data-ttu-id="c1e5e-162">다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-162">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="c1e5e-163">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-163">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="c1e5e-164">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-164">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="c1e5e-165">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-165">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="c1e5e-166">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-166">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="c1e5e-167">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-167">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="c1e5e-168">*Program.cs*에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-168">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="c1e5e-169">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-169">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="c1e5e-170">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="c1e5e-170">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="c1e5e-171">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-171">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="c1e5e-172">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-172">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="c1e5e-173">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="c1e5e-173">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="c1e5e-174">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-174">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="c1e5e-175">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-175">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="c1e5e-176">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-176">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="c1e5e-177">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-177">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="c1e5e-178">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="c1e5e-178">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="c1e5e-179">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-179">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="c1e5e-180">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-180">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c1e5e-181">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-181">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="c1e5e-182">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-182">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="c1e5e-183">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-183">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="c1e5e-184">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-184">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c1e5e-185">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-185">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="c1e5e-186">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="c1e5e-186">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="c1e5e-187">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-187">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="c1e5e-188">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-188">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="c1e5e-189">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-189">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="c1e5e-190">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-190">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="c1e5e-191">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-191">A minimum rate also applies to the response.</span></span> <span data-ttu-id="c1e5e-192">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-192">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="c1e5e-193">*Program.cs*에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-193">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="c1e5e-194">미들웨어에서 요청당 최소 속도 제한을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-194">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="c1e5e-195">요청 멀티플렉싱에 대한 프로토콜의 지원으로 인해 요청별 속도 제한을 수정하는 것이 HTTP/2에 대해 일반적으로 지원되지 않으므로, 이전 샘플에서 참조된 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>는 HTTP/2 요청에 대해 `HttpContext.Features`에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-195">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="c1e5e-196">그러나 HTTP/2 요청에 대해서도 `IHttpMinRequestBodyDataRateFeature.MinDataRate`를 `null`로 설정하여 요청별 읽기 속도 제한을 *완전히 비활성화*할 수 있으므로 HTTP/2 요청에 대한 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>는 여전히 존재합니다`HttpContext.Features`.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-196">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="c1e5e-197">`IHttpMinRequestBodyDataRateFeature.MinDataRate` 읽기를 시도하거나 `null`이 아닌 값으로 설정하려고 하면 HTTP/2 요청이 있을 때 `NotSupportedException`이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-197">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="c1e5e-198">`KestrelServerOptions.Limits`를 통해 구성된 서버 전체 속도 제한은 여전히 HTTP/1.x 및 HTTP/2 연결 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-198">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="c1e5e-199">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="c1e5e-199">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="c1e5e-200">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-200">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="c1e5e-201">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-201">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="c1e5e-202">연결당 최대 스트림</span><span class="sxs-lookup"><span data-stu-id="c1e5e-202">Maximum streams per connection</span></span>

<span data-ttu-id="c1e5e-203">`Http2.MaxStreamsPerConnection`은 HTTP/2 연결당 동시 요청 스트림 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-203">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="c1e5e-204">초과 스트림은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-204">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="c1e5e-205">기본값은 100입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-205">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="c1e5e-206">헤더 테이블 크기</span><span class="sxs-lookup"><span data-stu-id="c1e5e-206">Header table size</span></span>

<span data-ttu-id="c1e5e-207">HPACK 디코더는 HTTP/2 연결에 대한 HTTP 헤더의 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-207">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="c1e5e-208">`Http2.HeaderTableSize`는 HPACK 디코더가 사용하는 헤더 압축 테이블의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-208">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="c1e5e-209">값은 8진수로 제공되며 영(0)보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-209">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="c1e5e-210">기본값은 4096입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-210">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="c1e5e-211">최대 프레임 크기</span><span class="sxs-lookup"><span data-stu-id="c1e5e-211">Maximum frame size</span></span>

<span data-ttu-id="c1e5e-212">`Http2.MaxFrameSize`는 서버에서 받거나 보낸 HTTP/2 연결 프레임 페이로드의 최대 허용 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-212">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="c1e5e-213">값은 8진수로 제공되며 2^14(16,384)와 2^24-1(16,777,215) 사이여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-213">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="c1e5e-214">기본값은 2^14(16,384)입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-214">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="c1e5e-215">최대 요청 헤더 크기</span><span class="sxs-lookup"><span data-stu-id="c1e5e-215">Maximum request header size</span></span>

<span data-ttu-id="c1e5e-216">`Http2.MaxRequestHeaderFieldSize`는 요청 헤더 값의 8진수로 허용되는 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-216">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="c1e5e-217">이 한도는 모두 압축된 표현과 압축되지 않은 표현으로 이름과 값 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-217">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="c1e5e-218">값은 0보다 커야 합니다(0).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-218">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="c1e5e-219">기본값은 8,192입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-219">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="c1e5e-220">초기 연결 창 크기</span><span class="sxs-lookup"><span data-stu-id="c1e5e-220">Initial connection window size</span></span>

<span data-ttu-id="c1e5e-221">`Http2.InitialConnectionWindowSize`는 연결당 모든 요청(스트림)을 통해 한 번에 집계하는 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-221">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="c1e5e-222">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-222">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="c1e5e-223">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-223">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="c1e5e-224">기본값은 128KB(131,072)입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-224">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="c1e5e-225">초기 스트림 창 크기</span><span class="sxs-lookup"><span data-stu-id="c1e5e-225">Initial stream window size</span></span>

<span data-ttu-id="c1e5e-226">`Http2.InitialStreamWindowSize`는 요청(스트림)당 한 번에 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-226">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="c1e5e-227">요청은 `Http2.InitialConnectionWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-227">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="c1e5e-228">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-228">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="c1e5e-229">기본값은 96KB(98,304)입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-229">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="c1e5e-230">동기 IO</span><span class="sxs-lookup"><span data-stu-id="c1e5e-230">Synchronous IO</span></span>

<span data-ttu-id="c1e5e-231"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 IO가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-231"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="c1e5e-232">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-232">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="c1e5e-233">차단 동기 IO 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-233">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="c1e5e-234">비동기 IO를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 활성화하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-234">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="c1e5e-235">다음 예제에서는 동기 IO를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-235">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="c1e5e-236">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-236">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="c1e5e-237">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="c1e5e-237">Endpoint configuration</span></span>

<span data-ttu-id="c1e5e-238">기본적으로 ASP.NET Core는 다음으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-238">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="c1e5e-239">`https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-239">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="c1e5e-240">다음을 사용하여 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-240">Specify URLs using the:</span></span>

* <span data-ttu-id="c1e5e-241">`ASPNETCORE_URLS` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-241">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="c1e5e-242">`--urls` 명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-242">`--urls` command-line argument.</span></span>
* <span data-ttu-id="c1e5e-243">`urls` 호스트 구성 키.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-243">`urls` host configuration key.</span></span>
* <span data-ttu-id="c1e5e-244">`UseUrls` 확장명 메서드.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-244">`UseUrls` extension method.</span></span>

<span data-ttu-id="c1e5e-245">이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-245">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="c1e5e-246">값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-246">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="c1e5e-247">이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-247">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="c1e5e-248">개발 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-248">A development certificate is created:</span></span>

* <span data-ttu-id="c1e5e-249">[.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-249">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="c1e5e-250">[dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-250">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="c1e5e-251">일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-251">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="c1e5e-252">프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-252">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c1e5e-253"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-253">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="c1e5e-254">`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-254">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="c1e5e-255">`KestrelServerOptions` 구성:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-255">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="c1e5e-256">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-256">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="c1e5e-257">지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-257">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="c1e5e-258">`ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-258">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="c1e5e-259">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-259">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="c1e5e-260">각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-260">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="c1e5e-261">`ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-261">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

### <a name="configureiconfiguration"></a><span data-ttu-id="c1e5e-262">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-262">Configure(IConfiguration)</span></span>

<span data-ttu-id="c1e5e-263">입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-263">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="c1e5e-264">구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-264">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="c1e5e-265">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="c1e5e-265">ListenOptions.UseHttps</span></span>

<span data-ttu-id="c1e5e-266">HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-266">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="c1e5e-267">`ListenOptions.UseHttps` 확장:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-267">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="c1e5e-268">`UseHttps` &ndash; 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-268">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="c1e5e-269">기본 인증서가 구성되지 않은 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-269">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="c1e5e-270">`ListenOptions.UseHttps` 매개 변수:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-270">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="c1e5e-271">`filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-271">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="c1e5e-272">`password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-272">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="c1e5e-273">`configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-273">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="c1e5e-274">`ListenOptions`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-274">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="c1e5e-275">`storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-275">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="c1e5e-276">`subject`은 인증서의 주체 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-276">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="c1e5e-277">`allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-277">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="c1e5e-278">`location`은 인증서를 로드할 수 있는 저장소 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-278">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="c1e5e-279">`serverCertificate`은 X.509 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-279">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="c1e5e-280">프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-280">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c1e5e-281">최소한 기본 인증서를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-281">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="c1e5e-282">다음에 설명된 지원되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-282">Supported configurations described next:</span></span>

* <span data-ttu-id="c1e5e-283">구성 없음</span><span class="sxs-lookup"><span data-stu-id="c1e5e-283">No configuration</span></span>
* <span data-ttu-id="c1e5e-284">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="c1e5e-284">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="c1e5e-285">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="c1e5e-285">Change the defaults in code</span></span>

<span data-ttu-id="c1e5e-286">*구성 없음*</span><span class="sxs-lookup"><span data-stu-id="c1e5e-286">*No configuration*</span></span>

<span data-ttu-id="c1e5e-287">Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-287">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="c1e5e-288">*구성에서 기본 인증서를 바꿈*</span><span class="sxs-lookup"><span data-stu-id="c1e5e-288">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="c1e5e-289">`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-289">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="c1e5e-290">기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-290">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="c1e5e-291">디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-291">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="c1e5e-292">다음 *appsettings.json* 예제에서:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-292">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="c1e5e-293">잘못된 인증서 사용을 허가하려면 **AllowInvalid**를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-293">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="c1e5e-294">인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본**에서 정의된 인증서 또는 개발 인증서로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-294">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="c1e5e-295">모든 인증서 노드에 대해 **경로** 및 **암호**를 사용하는 대신 인증서 저장소 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-295">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="c1e5e-296">예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-296">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="c1e5e-297">스키마 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-297">Schema notes:</span></span>

* <span data-ttu-id="c1e5e-298">엔드포인트 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-298">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="c1e5e-299">예를 들어, `HTTPS` 및 `Https`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-299">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="c1e5e-300">`Url` 매개 변수는 각 엔드포인트에 대해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-300">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="c1e5e-301">이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-301">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="c1e5e-302">이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-302">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="c1e5e-303">`Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-303">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="c1e5e-304">`Certificate` 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-304">The `Certificate` section is optional.</span></span> <span data-ttu-id="c1e5e-305">`Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-305">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="c1e5e-306">기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-306">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="c1e5e-307">`Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-307">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="c1e5e-308">많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-308">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="c1e5e-309">`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-309">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="c1e5e-310">`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-310">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="c1e5e-311">각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-311">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="c1e5e-312">여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-312">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="c1e5e-313">`Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-313">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="c1e5e-314">메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-314">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="c1e5e-315">`KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-315">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="c1e5e-316">이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-316">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="c1e5e-317">*코드에서 기본값 변경*</span><span class="sxs-lookup"><span data-stu-id="c1e5e-317">*Change the defaults in code*</span></span>

<span data-ttu-id="c1e5e-318">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-318">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="c1e5e-319">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-319">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="c1e5e-320">*SNI에 대한 Kestrel 지원*</span><span class="sxs-lookup"><span data-stu-id="c1e5e-320">*Kestrel support for SNI*</span></span>

<span data-ttu-id="c1e5e-321">[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-321">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="c1e5e-322">함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-322">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="c1e5e-323">TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-323">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="c1e5e-324">Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-324">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="c1e5e-325">앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-325">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="c1e5e-326">SNI 지원에는 다음 항목이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-326">SNI support requires:</span></span>

* <span data-ttu-id="c1e5e-327">대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-327">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="c1e5e-328">`net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-328">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="c1e5e-329">클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-329">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="c1e5e-330">모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-330">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="c1e5e-331">Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-331">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="c1e5e-332">연결 로깅</span><span class="sxs-lookup"><span data-stu-id="c1e5e-332">Connection logging</span></span>

<span data-ttu-id="c1e5e-333"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-333">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="c1e5e-334">연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-334">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="c1e5e-335">`UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-335">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="c1e5e-336">`UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-336">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="c1e5e-337">TCP 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="c1e5e-337">Bind to a TCP socket</span></span>

<span data-ttu-id="c1e5e-338"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-338">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="c1e5e-339">예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-339">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="c1e5e-340">동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-340">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="c1e5e-341">Unix 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="c1e5e-341">Bind to a Unix socket</span></span>

<span data-ttu-id="c1e5e-342">이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-342">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="c1e5e-343">포트 0</span><span class="sxs-lookup"><span data-stu-id="c1e5e-343">Port 0</span></span>

<span data-ttu-id="c1e5e-344">포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-344">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="c1e5e-345">다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-345">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="c1e5e-346">앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-346">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="c1e5e-347">제한 사항</span><span class="sxs-lookup"><span data-stu-id="c1e5e-347">Limitations</span></span>

<span data-ttu-id="c1e5e-348">다음 방법으로 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-348">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="c1e5e-349">`--urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="c1e5e-349">`--urls` command-line argument</span></span>
* <span data-ttu-id="c1e5e-350">`urls` 호스트 구성 키</span><span class="sxs-lookup"><span data-stu-id="c1e5e-350">`urls` host configuration key</span></span>
* <span data-ttu-id="c1e5e-351">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="c1e5e-351">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="c1e5e-352">이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-352">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="c1e5e-353">그러나 다음과 같은 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-353">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="c1e5e-354">HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-354">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="c1e5e-355">`Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-355">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="c1e5e-356">IIS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="c1e5e-356">IIS endpoint configuration</span></span>

<span data-ttu-id="c1e5e-357">IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-357">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="c1e5e-358">자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-358">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="c1e5e-359">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="c1e5e-359">ListenOptions.Protocols</span></span>

<span data-ttu-id="c1e5e-360">`Protocols` 속성은 연결 엔드포인트 또는 서버에 대해 사용할 수 있는 HTTP 프로토콜(`HttpProtocols`)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-360">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="c1e5e-361">`HttpProtocols` 열거형의 `Protocols` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-361">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="c1e5e-362">`HttpProtocols` 열거형 값</span><span class="sxs-lookup"><span data-stu-id="c1e5e-362">`HttpProtocols` enum value</span></span> | <span data-ttu-id="c1e5e-363">허용되는 연결 프로토콜</span><span class="sxs-lookup"><span data-stu-id="c1e5e-363">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="c1e5e-364">HTTP/1.1 전용.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-364">HTTP/1.1 only.</span></span> <span data-ttu-id="c1e5e-365">TLS와 함께 또는 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-365">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="c1e5e-366">HTTP/2 전용.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-366">HTTP/2 only.</span></span> <span data-ttu-id="c1e5e-367">클라이언트가 [이전 기술 모드](https://tools.ietf.org/html/rfc7540#section-3.4)를 지원하는 경우에만 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-367">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="c1e5e-368">HTTP/1.1 및 HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-368">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="c1e5e-369">HTTP/2를 사용하려면 클라이언트가 TLS [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 핸드셰이크에서 HTTP/2를 선택해야 합니다. 그렇지 않으면 연결 기본값은 HTTP/1.1입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-369">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="c1e5e-370">모든 엔드포인트의 기본 `ListenOptions.Protocols` 값은 `HttpProtocols.Http1AndHttp2`입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-370">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="c1e5e-371">HTTP/2에 대한 TLS 제한 사항:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-371">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="c1e5e-372">TLS 버전 1.2 이상</span><span class="sxs-lookup"><span data-stu-id="c1e5e-372">TLS version 1.2 or later</span></span>
* <span data-ttu-id="c1e5e-373">재협상 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="c1e5e-373">Renegotiation disabled</span></span>
* <span data-ttu-id="c1e5e-374">압축 사용 안함</span><span class="sxs-lookup"><span data-stu-id="c1e5e-374">Compression disabled</span></span>
* <span data-ttu-id="c1e5e-375">최소 임시 키 교환 크기:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-375">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="c1e5e-376">ECDHE(타원 곡선 Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 최소 224비트</span><span class="sxs-lookup"><span data-stu-id="c1e5e-376">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="c1e5e-377">유한 필드 DHE(Diffie-Hellman) &lbrack;`TLS12`&rbrack; &ndash; 최소 2048비트</span><span class="sxs-lookup"><span data-stu-id="c1e5e-377">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="c1e5e-378">암호 도구 모음이 차단 목록에 없음</span><span class="sxs-lookup"><span data-stu-id="c1e5e-378">Cipher suite not blacklisted</span></span>

<span data-ttu-id="c1e5e-379">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;(P-256 타원 곡선 &lbrack;`FIPS186`&rbrack; 포함)는 기본적으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-379">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="c1e5e-380">다음 예제는 포트 8000에서 HTTP/1.1 및 HTTP/2 연결을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-380">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="c1e5e-381">연결은 제공된 인증서를 사용하여 TLS로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-381">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="c1e5e-382">필요할 경우 연결 미들웨어를 사용하여 각 연결을 기준으로 특정 암호에 대한 TLS 핸드셰이크를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-382">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="c1e5e-383">다음 예는 앱에서 지원하지 않는 모든 암호화 알고리즘에 대해 <xref:System.NotSupportedException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-383">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="c1e5e-384">또는 [ITlsHandshakeFeature CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm)을 정의하고 허용되는 암호 그룹 목록과 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-384">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="c1e5e-385">[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 암호화 알고리즘과 함께 사용되는 암호화는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-385">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="c1e5e-386"><xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> 람다를 통해 연결 필터링을 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-386">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="c1e5e-387">Linux에서 <xref:System.Net.Security.CipherSuitesPolicy>를 사용하여 각 연결을 기준으로 TLS 핸드셰이크를 필터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-387">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="c1e5e-388">구성에서 프로토콜 설정 </span><span class="sxs-lookup"><span data-stu-id="c1e5e-388">*Set the protocol from configuration*</span></span>

<span data-ttu-id="c1e5e-389">`CreateDefaultBuilder`는 기본적으로 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-389">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="c1e5e-390">다음 *appsettings.json* 예제에서는 HTTP/1.1을 모든 엔드포인트에 대한 기본 연결 프로토콜로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-390">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="c1e5e-391">다음 *appsettings.json* 예제에서는 특정 엔드포인트에 대한 HTTP/1.1 연결 프로토콜을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-391">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="c1e5e-392">코드에서 지정한 프로토콜이 구성에서 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-392">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="c1e5e-393">전송 구성</span><span class="sxs-lookup"><span data-stu-id="c1e5e-393">Transport configuration</span></span>

<span data-ttu-id="c1e5e-394">Libuv를 사용해야 하는 프로젝트의 경우(<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span><span class="sxs-lookup"><span data-stu-id="c1e5e-394">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="c1e5e-395">[Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 패키지에 대한 종속성을 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-395">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="c1e5e-396">`IWebHostBuilder`에서 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 호출:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-396">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="c1e5e-397">URL 접두사</span><span class="sxs-lookup"><span data-stu-id="c1e5e-397">URL prefixes</span></span>

<span data-ttu-id="c1e5e-398">`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-398">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="c1e5e-399">HTTP URL 접두사만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-399">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="c1e5e-400">Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-400">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="c1e5e-401">포트 번호가 있는 IPv4 주소</span><span class="sxs-lookup"><span data-stu-id="c1e5e-401">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="c1e5e-402">`0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-402">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="c1e5e-403">포트 번호가 있는 IPv6 주소</span><span class="sxs-lookup"><span data-stu-id="c1e5e-403">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="c1e5e-404">`[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-404">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="c1e5e-405">포트 번호가 있는 호스트 이름</span><span class="sxs-lookup"><span data-stu-id="c1e5e-405">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="c1e5e-406">호스트 이름, `*` 및 `+`는 특별하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-406">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="c1e5e-407">유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-407">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="c1e5e-408">서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-408">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="c1e5e-409">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-409">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="c1e5e-410">포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP</span><span class="sxs-lookup"><span data-stu-id="c1e5e-410">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="c1e5e-411">`localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-411">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="c1e5e-412">요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-412">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="c1e5e-413">루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-413">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="c1e5e-414">호스트 필터링</span><span class="sxs-lookup"><span data-stu-id="c1e5e-414">Host filtering</span></span>

<span data-ttu-id="c1e5e-415">Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-415">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="c1e5e-416">호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-416">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="c1e5e-417">명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-417">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="c1e5e-418">`Host` 헤더의 유효성이 검사되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-418">`Host` headers aren't validated.</span></span>

<span data-ttu-id="c1e5e-419">해결 방법으로 호스트 필터링 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-419">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="c1e5e-420">호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공하며, 이 패키지는 ASP.NET Core 앱을 위해 암시적으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-420">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="c1e5e-421">미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-421">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="c1e5e-422">호스트 필터링 미들웨어는 기본적으로 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-422">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="c1e5e-423">미들웨어를 활성화하려면 *appsettings.json*/*appsettings.\<EnvironmentName>.json*에서 `AllowedHosts` 키를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-423">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="c1e5e-424">값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-424">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="c1e5e-425">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-425">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="c1e5e-426">[전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-426">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="c1e5e-427">전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-427">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="c1e5e-428">전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-428">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="c1e5e-429">호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-429">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="c1e5e-430">전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-430">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c1e5e-431">Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-431">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c1e5e-432">Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-432">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="c1e5e-433">Kestrel에서는 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-433">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="c1e5e-434">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c1e5e-434">HTTPS</span></span>
* <span data-ttu-id="c1e5e-435">[Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드</span><span class="sxs-lookup"><span data-stu-id="c1e5e-435">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="c1e5e-436">Nginx 뒤의 고성능을 위한 Unix 소켓</span><span class="sxs-lookup"><span data-stu-id="c1e5e-436">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="c1e5e-437">HTTP/2(macOS&dagger; 제외)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-437">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="c1e5e-438">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-438">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="c1e5e-439">Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-439">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="c1e5e-440">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c1e5e-440">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="c1e5e-441">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="c1e5e-441">HTTP/2 support</span></span>

<span data-ttu-id="c1e5e-442">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해 [HTTP/2](https://httpwg.org/specs/rfc7540.html)를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-442">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="c1e5e-443">운영 체제&dagger;</span><span class="sxs-lookup"><span data-stu-id="c1e5e-443">Operating system&dagger;</span></span>
  * <span data-ttu-id="c1e5e-444">Windows Server 2016/Windows 10 이상&Dagger;</span><span class="sxs-lookup"><span data-stu-id="c1e5e-444">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="c1e5e-445">Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-445">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="c1e5e-446">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="c1e5e-446">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="c1e5e-447">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="c1e5e-447">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="c1e5e-448">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="c1e5e-448">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c1e5e-449">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-449">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="c1e5e-450">&Dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-450">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="c1e5e-451">이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-451">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="c1e5e-452">TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-452">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="c1e5e-453">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-453">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="c1e5e-454">HTTP/2는 기본적으로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-454">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="c1e5e-455">구성에 대한 자세한 내용은 [Kestrel 옵션](#kestrel-options) 및 [ListenOptions.Protocols](#listenoptionsprotocols) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-455">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="c1e5e-456">Kestrel을 역방향 프록시와 함께 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="c1e5e-456">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="c1e5e-457">Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버*와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-457">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c1e5e-458">역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-458">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="c1e5e-459">Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-459">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="c1e5e-461">Kestrel은 역방향 프록시 구성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-461">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c1e5e-463">역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-463">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="c1e5e-464">역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-464">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="c1e5e-465">Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-465">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="c1e5e-466">포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-466">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="c1e5e-467">역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-467">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="c1e5e-468">역방향 프록시:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-468">A reverse proxy:</span></span>

* <span data-ttu-id="c1e5e-469">호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-469">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="c1e5e-470">구성 및 방어의 추가 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-470">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="c1e5e-471">기존 인프라와 잘 통합될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-471">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="c1e5e-472">부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-472">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="c1e5e-473">역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-473">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="c1e5e-474">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-474">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="c1e5e-475">ASP.NET Core 앱에서 Kestrel을 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="c1e5e-475">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="c1e5e-476">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-476">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c1e5e-477">ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-477">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="c1e5e-478">*Program.cs*에서 템플릿 코드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> 숨은 기능을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-478">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="c1e5e-479">`CreateDefaultBuilder`와 호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>의 *호스트 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-479">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="c1e5e-480">`CreateDefaultBuilder`를 호출한 후 추가 구성을 제공하려면 `ConfigureKestrel`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-480">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="c1e5e-481">앱이 호스트를 설정하기 위해 `CreateDefaultBuilder`를 호출하지 않는 경우 `ConfigureKestrel`을 호출하기 **전에** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-481">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="c1e5e-482">Kestrel 옵션</span><span class="sxs-lookup"><span data-stu-id="c1e5e-482">Kestrel options</span></span>

<span data-ttu-id="c1e5e-483">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-483">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="c1e5e-484"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-484">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="c1e5e-485">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-485">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="c1e5e-486">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-486">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="c1e5e-487">다음 예에서 C# 코드로 구성된 Kestrel 옵션도 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-487">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="c1e5e-488">예를 들어 파일 구성 공급자는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-488">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="c1e5e-489">다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-489">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="c1e5e-490">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-490">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="c1e5e-491">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-491">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="c1e5e-492">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-492">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="c1e5e-493">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-493">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="c1e5e-494">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-494">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="c1e5e-495">*Program.cs*에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-495">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="c1e5e-496">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-496">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="c1e5e-497">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="c1e5e-497">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="c1e5e-498">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-498">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="c1e5e-499">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-499">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="c1e5e-500">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="c1e5e-500">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="c1e5e-501">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-501">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="c1e5e-502">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-502">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="c1e5e-503">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-503">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="c1e5e-504">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-504">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="c1e5e-505">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="c1e5e-505">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="c1e5e-506">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-506">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="c1e5e-507">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-507">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c1e5e-508">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-508">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="c1e5e-509">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-509">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="c1e5e-510">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-510">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="c1e5e-511">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-511">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c1e5e-512">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-512">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="c1e5e-513">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="c1e5e-513">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="c1e5e-514">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-514">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="c1e5e-515">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-515">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="c1e5e-516">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-516">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="c1e5e-517">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-517">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="c1e5e-518">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-518">A minimum rate also applies to the response.</span></span> <span data-ttu-id="c1e5e-519">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-519">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="c1e5e-520">*Program.cs*에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-520">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="c1e5e-521">미들웨어에서 요청당 최소 속도 제한을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-521">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="c1e5e-522">요청 멀티플렉싱에 대한 프로토콜의 지원으로 인해 요청별 속도 제한을 수정하는 것이 HTTP/2에 대해 지원되지 않으므로, 이전 샘플에서 참조된 속도 기능은 HTTP/2 요청에 대해 `HttpContext.Features`에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-522">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="c1e5e-523">`KestrelServerOptions.Limits`를 통해 구성된 서버 전체 속도 제한은 여전히 HTTP/1.x 및 HTTP/2 연결 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-523">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="c1e5e-524">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="c1e5e-524">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="c1e5e-525">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-525">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="c1e5e-526">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-526">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="c1e5e-527">연결당 최대 스트림</span><span class="sxs-lookup"><span data-stu-id="c1e5e-527">Maximum streams per connection</span></span>

<span data-ttu-id="c1e5e-528">`Http2.MaxStreamsPerConnection`은 HTTP/2 연결당 동시 요청 스트림 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-528">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="c1e5e-529">초과 스트림은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-529">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="c1e5e-530">기본값은 100입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-530">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="c1e5e-531">헤더 테이블 크기</span><span class="sxs-lookup"><span data-stu-id="c1e5e-531">Header table size</span></span>

<span data-ttu-id="c1e5e-532">HPACK 디코더는 HTTP/2 연결에 대한 HTTP 헤더의 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-532">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="c1e5e-533">`Http2.HeaderTableSize`는 HPACK 디코더가 사용하는 헤더 압축 테이블의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-533">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="c1e5e-534">값은 8진수로 제공되며 영(0)보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-534">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="c1e5e-535">기본값은 4096입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-535">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="c1e5e-536">최대 프레임 크기</span><span class="sxs-lookup"><span data-stu-id="c1e5e-536">Maximum frame size</span></span>

<span data-ttu-id="c1e5e-537">`Http2.MaxFrameSize`는 수신할 HTTP/2 연결 프레임 페이로드의 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-537">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="c1e5e-538">값은 8진수로 제공되며 2^14(16,384)와 2^24-1(16,777,215) 사이여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-538">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="c1e5e-539">기본값은 2^14(16,384)입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-539">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="c1e5e-540">최대 요청 헤더 크기</span><span class="sxs-lookup"><span data-stu-id="c1e5e-540">Maximum request header size</span></span>

<span data-ttu-id="c1e5e-541">`Http2.MaxRequestHeaderFieldSize`는 요청 헤더 값의 8진수로 허용되는 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-541">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="c1e5e-542">이 한도는 이름과 값이 모두 압축된 표현과 압축되지 않은 표현으로 함께 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-542">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="c1e5e-543">값은 0보다 커야 합니다(0).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-543">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="c1e5e-544">기본값은 8,192입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-544">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="c1e5e-545">초기 연결 창 크기</span><span class="sxs-lookup"><span data-stu-id="c1e5e-545">Initial connection window size</span></span>

<span data-ttu-id="c1e5e-546">`Http2.InitialConnectionWindowSize`는 연결당 모든 요청(스트림)을 통해 한 번에 집계하는 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-546">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="c1e5e-547">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-547">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="c1e5e-548">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-548">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="c1e5e-549">기본값은 128KB(131,072)입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-549">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="c1e5e-550">초기 스트림 창 크기</span><span class="sxs-lookup"><span data-stu-id="c1e5e-550">Initial stream window size</span></span>

<span data-ttu-id="c1e5e-551">`Http2.InitialStreamWindowSize`는 요청(스트림)당 한 번에 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-551">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="c1e5e-552">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-552">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="c1e5e-553">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-553">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="c1e5e-554">기본값은 96KB(98,304)입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-554">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="c1e5e-555">동기 IO</span><span class="sxs-lookup"><span data-stu-id="c1e5e-555">Synchronous IO</span></span>

<span data-ttu-id="c1e5e-556"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 IO가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-556"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="c1e5e-557">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-557">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="c1e5e-558">차단 동기 IO 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-558">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="c1e5e-559">비동기 IO를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 활성화하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-559">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="c1e5e-560">다음 예제에서는 동기 IO를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-560">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="c1e5e-561">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-561">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="c1e5e-562">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="c1e5e-562">Endpoint configuration</span></span>

<span data-ttu-id="c1e5e-563">기본적으로 ASP.NET Core는 다음으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-563">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="c1e5e-564">`https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-564">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="c1e5e-565">다음을 사용하여 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-565">Specify URLs using the:</span></span>

* <span data-ttu-id="c1e5e-566">`ASPNETCORE_URLS` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-566">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="c1e5e-567">`--urls` 명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-567">`--urls` command-line argument.</span></span>
* <span data-ttu-id="c1e5e-568">`urls` 호스트 구성 키.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-568">`urls` host configuration key.</span></span>
* <span data-ttu-id="c1e5e-569">`UseUrls` 확장명 메서드.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-569">`UseUrls` extension method.</span></span>

<span data-ttu-id="c1e5e-570">이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-570">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="c1e5e-571">값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-571">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="c1e5e-572">이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-572">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="c1e5e-573">개발 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-573">A development certificate is created:</span></span>

* <span data-ttu-id="c1e5e-574">[.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-574">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="c1e5e-575">[dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-575">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="c1e5e-576">일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-576">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="c1e5e-577">프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-577">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c1e5e-578"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-578">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="c1e5e-579">`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-579">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="c1e5e-580">`KestrelServerOptions` 구성:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-580">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="c1e5e-581">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-581">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="c1e5e-582">지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-582">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="c1e5e-583">`ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-583">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="c1e5e-584">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-584">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="c1e5e-585">각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-585">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="c1e5e-586">`ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-586">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

### <a name="configureiconfiguration"></a><span data-ttu-id="c1e5e-587">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-587">Configure(IConfiguration)</span></span>

<span data-ttu-id="c1e5e-588">입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-588">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="c1e5e-589">구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-589">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="c1e5e-590">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="c1e5e-590">ListenOptions.UseHttps</span></span>

<span data-ttu-id="c1e5e-591">HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-591">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="c1e5e-592">`ListenOptions.UseHttps` 확장:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-592">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="c1e5e-593">`UseHttps` &ndash; 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-593">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="c1e5e-594">기본 인증서가 구성되지 않은 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-594">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="c1e5e-595">`ListenOptions.UseHttps` 매개 변수:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-595">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="c1e5e-596">`filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-596">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="c1e5e-597">`password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-597">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="c1e5e-598">`configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-598">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="c1e5e-599">`ListenOptions`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-599">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="c1e5e-600">`storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-600">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="c1e5e-601">`subject`은 인증서의 주체 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-601">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="c1e5e-602">`allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-602">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="c1e5e-603">`location`은 인증서를 로드할 수 있는 저장소 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-603">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="c1e5e-604">`serverCertificate`은 X.509 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-604">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="c1e5e-605">프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-605">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c1e5e-606">최소한 기본 인증서를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-606">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="c1e5e-607">다음에 설명된 지원되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-607">Supported configurations described next:</span></span>

* <span data-ttu-id="c1e5e-608">구성 없음</span><span class="sxs-lookup"><span data-stu-id="c1e5e-608">No configuration</span></span>
* <span data-ttu-id="c1e5e-609">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="c1e5e-609">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="c1e5e-610">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="c1e5e-610">Change the defaults in code</span></span>

<span data-ttu-id="c1e5e-611">*구성 없음*</span><span class="sxs-lookup"><span data-stu-id="c1e5e-611">*No configuration*</span></span>

<span data-ttu-id="c1e5e-612">Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-612">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="c1e5e-613">*구성에서 기본 인증서를 바꿈*</span><span class="sxs-lookup"><span data-stu-id="c1e5e-613">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="c1e5e-614">`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-614">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="c1e5e-615">기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-615">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="c1e5e-616">디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-616">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="c1e5e-617">다음 *appsettings.json* 예제에서:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-617">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="c1e5e-618">잘못된 인증서 사용을 허가하려면 **AllowInvalid**를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-618">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="c1e5e-619">인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본**에서 정의된 인증서 또는 개발 인증서로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-619">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="c1e5e-620">모든 인증서 노드에 대해 **경로** 및 **암호**를 사용하는 대신 인증서 저장소 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-620">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="c1e5e-621">예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-621">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="c1e5e-622">스키마 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-622">Schema notes:</span></span>

* <span data-ttu-id="c1e5e-623">엔드포인트 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-623">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="c1e5e-624">예를 들어, `HTTPS` 및 `Https`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-624">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="c1e5e-625">`Url` 매개 변수는 각 엔드포인트에 대해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-625">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="c1e5e-626">이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-626">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="c1e5e-627">이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-627">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="c1e5e-628">`Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-628">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="c1e5e-629">`Certificate` 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-629">The `Certificate` section is optional.</span></span> <span data-ttu-id="c1e5e-630">`Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-630">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="c1e5e-631">기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-631">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="c1e5e-632">`Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-632">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="c1e5e-633">많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-633">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="c1e5e-634">`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-634">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="c1e5e-635">`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-635">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="c1e5e-636">각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-636">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="c1e5e-637">여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-637">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="c1e5e-638">`Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-638">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="c1e5e-639">메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-639">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="c1e5e-640">`KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-640">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="c1e5e-641">이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-641">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="c1e5e-642">*코드에서 기본값 변경*</span><span class="sxs-lookup"><span data-stu-id="c1e5e-642">*Change the defaults in code*</span></span>

<span data-ttu-id="c1e5e-643">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-643">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="c1e5e-644">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-644">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="c1e5e-645">*SNI에 대한 Kestrel 지원*</span><span class="sxs-lookup"><span data-stu-id="c1e5e-645">*Kestrel support for SNI*</span></span>

<span data-ttu-id="c1e5e-646">[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-646">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="c1e5e-647">함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-647">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="c1e5e-648">TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-648">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="c1e5e-649">Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-649">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="c1e5e-650">앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-650">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="c1e5e-651">SNI 지원에는 다음 항목이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-651">SNI support requires:</span></span>

* <span data-ttu-id="c1e5e-652">대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-652">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="c1e5e-653">`net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-653">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="c1e5e-654">클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-654">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="c1e5e-655">모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-655">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="c1e5e-656">Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-656">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="c1e5e-657">연결 로깅</span><span class="sxs-lookup"><span data-stu-id="c1e5e-657">Connection logging</span></span>

<span data-ttu-id="c1e5e-658"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-658">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="c1e5e-659">연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-659">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="c1e5e-660">`UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-660">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="c1e5e-661">`UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-661">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="c1e5e-662">TCP 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="c1e5e-662">Bind to a TCP socket</span></span>

<span data-ttu-id="c1e5e-663"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-663">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="c1e5e-664">예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-664">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="c1e5e-665">동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-665">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="c1e5e-666">Unix 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="c1e5e-666">Bind to a Unix socket</span></span>

<span data-ttu-id="c1e5e-667">이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-667">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="c1e5e-668">포트 0</span><span class="sxs-lookup"><span data-stu-id="c1e5e-668">Port 0</span></span>

<span data-ttu-id="c1e5e-669">포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-669">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="c1e5e-670">다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-670">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="c1e5e-671">앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-671">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="c1e5e-672">제한 사항</span><span class="sxs-lookup"><span data-stu-id="c1e5e-672">Limitations</span></span>

<span data-ttu-id="c1e5e-673">다음 방법으로 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-673">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="c1e5e-674">`--urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="c1e5e-674">`--urls` command-line argument</span></span>
* <span data-ttu-id="c1e5e-675">`urls` 호스트 구성 키</span><span class="sxs-lookup"><span data-stu-id="c1e5e-675">`urls` host configuration key</span></span>
* <span data-ttu-id="c1e5e-676">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="c1e5e-676">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="c1e5e-677">이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-677">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="c1e5e-678">그러나 다음과 같은 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-678">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="c1e5e-679">HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-679">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="c1e5e-680">`Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-680">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="c1e5e-681">IIS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="c1e5e-681">IIS endpoint configuration</span></span>

<span data-ttu-id="c1e5e-682">IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-682">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="c1e5e-683">자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-683">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="c1e5e-684">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="c1e5e-684">ListenOptions.Protocols</span></span>

<span data-ttu-id="c1e5e-685">`Protocols` 속성은 연결 엔드포인트 또는 서버에 대해 사용할 수 있는 HTTP 프로토콜(`HttpProtocols`)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-685">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="c1e5e-686">`HttpProtocols` 열거형의 `Protocols` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-686">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="c1e5e-687">`HttpProtocols` 열거형 값</span><span class="sxs-lookup"><span data-stu-id="c1e5e-687">`HttpProtocols` enum value</span></span> | <span data-ttu-id="c1e5e-688">허용되는 연결 프로토콜</span><span class="sxs-lookup"><span data-stu-id="c1e5e-688">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="c1e5e-689">HTTP/1.1 전용.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-689">HTTP/1.1 only.</span></span> <span data-ttu-id="c1e5e-690">TLS와 함께 또는 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-690">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="c1e5e-691">HTTP/2 전용.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-691">HTTP/2 only.</span></span> <span data-ttu-id="c1e5e-692">클라이언트가 [이전 기술 모드](https://tools.ietf.org/html/rfc7540#section-3.4)를 지원하는 경우에만 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-692">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="c1e5e-693">HTTP/1.1 및 HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-693">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="c1e5e-694">HTTP/2를 사용하려면 TLS 및 [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결이 필요합니다. 그렇지 않은 경우 연결은 기본적으로 HTTP/1.1로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-694">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="c1e5e-695">기본 프로토콜은 HTTP/1.1입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-695">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="c1e5e-696">HTTP/2에 대한 TLS 제한 사항:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-696">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="c1e5e-697">TLS 버전 1.2 이상</span><span class="sxs-lookup"><span data-stu-id="c1e5e-697">TLS version 1.2 or later</span></span>
* <span data-ttu-id="c1e5e-698">재협상 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="c1e5e-698">Renegotiation disabled</span></span>
* <span data-ttu-id="c1e5e-699">압축 사용 안함</span><span class="sxs-lookup"><span data-stu-id="c1e5e-699">Compression disabled</span></span>
* <span data-ttu-id="c1e5e-700">최소 임시 키 교환 크기:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-700">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="c1e5e-701">ECDHE(타원 곡선 Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 최소 224비트</span><span class="sxs-lookup"><span data-stu-id="c1e5e-701">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="c1e5e-702">유한 필드 DHE(Diffie-Hellman) &lbrack;`TLS12`&rbrack; &ndash; 최소 2048비트</span><span class="sxs-lookup"><span data-stu-id="c1e5e-702">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="c1e5e-703">암호 도구 모음이 차단 목록에 없음</span><span class="sxs-lookup"><span data-stu-id="c1e5e-703">Cipher suite not blacklisted</span></span>

<span data-ttu-id="c1e5e-704">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;(P-256 타원 곡선 &lbrack;`FIPS186`&rbrack; 포함)는 기본적으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-704">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="c1e5e-705">다음 예제는 포트 8000에서 HTTP/1.1 및 HTTP/2 연결을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-705">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="c1e5e-706">연결은 제공된 인증서를 사용하여 TLS로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-706">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="c1e5e-707">선택적으로, `IConnectionAdapter` 구현을 만들어 각 연결 단위로 특정 암호에 대한 TLS 핸드셰이크를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-707">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="c1e5e-708">구성에서 프로토콜 설정 </span><span class="sxs-lookup"><span data-stu-id="c1e5e-708">*Set the protocol from configuration*</span></span>

<span data-ttu-id="c1e5e-709"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>는 기본적으로 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-709"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="c1e5e-710">다음 *appsettings.json* 예제에서는 모든 Kestrel의 엔드포인트에 대해 기본 연결 프로토콜(HTTP/1.1 및 HTTP/2)이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-710">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="c1e5e-711">다음 구성 파일 예제에서는 특정 엔드포인트에 대해 연결 프로토콜을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-711">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="c1e5e-712">코드에서 지정한 프로토콜이 구성에서 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-712">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="c1e5e-713">전송 구성</span><span class="sxs-lookup"><span data-stu-id="c1e5e-713">Transport configuration</span></span>

<span data-ttu-id="c1e5e-714">ASP.NET Core 2.1 릴리스에서 Kestrel의 기본 전송은 더 이상 Libuv에 기반하지 않으며 대신 관리 소켓에 기반합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-714">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="c1e5e-715"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출하고 다음 패키지 중 하나를 사용하는 2.1로 업그레이드된 ASP.NET Core 2.0 앱의 주요 변경 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-715">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="c1e5e-716">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)(직접 패키지 참조)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-716">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="c1e5e-717">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="c1e5e-717">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="c1e5e-718">Libuv를 사용해야 하는 프로젝트의 경우:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-718">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="c1e5e-719">[Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 패키지에 대한 종속성을 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-719">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="c1e5e-720"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-720">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="c1e5e-721">URL 접두사</span><span class="sxs-lookup"><span data-stu-id="c1e5e-721">URL prefixes</span></span>

<span data-ttu-id="c1e5e-722">`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-722">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="c1e5e-723">HTTP URL 접두사만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-723">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="c1e5e-724">Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-724">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="c1e5e-725">포트 번호가 있는 IPv4 주소</span><span class="sxs-lookup"><span data-stu-id="c1e5e-725">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="c1e5e-726">`0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-726">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="c1e5e-727">포트 번호가 있는 IPv6 주소</span><span class="sxs-lookup"><span data-stu-id="c1e5e-727">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="c1e5e-728">`[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-728">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="c1e5e-729">포트 번호가 있는 호스트 이름</span><span class="sxs-lookup"><span data-stu-id="c1e5e-729">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="c1e5e-730">호스트 이름, `*` 및 `+`는 특별하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-730">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="c1e5e-731">유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-731">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="c1e5e-732">서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-732">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="c1e5e-733">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-733">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="c1e5e-734">포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP</span><span class="sxs-lookup"><span data-stu-id="c1e5e-734">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="c1e5e-735">`localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-735">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="c1e5e-736">요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-736">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="c1e5e-737">루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-737">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="c1e5e-738">호스트 필터링</span><span class="sxs-lookup"><span data-stu-id="c1e5e-738">Host filtering</span></span>

<span data-ttu-id="c1e5e-739">Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-739">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="c1e5e-740">호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-740">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="c1e5e-741">명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-741">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="c1e5e-742">`Host` 헤더의 유효성이 검사되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-742">`Host` headers aren't validated.</span></span>

<span data-ttu-id="c1e5e-743">해결 방법으로 호스트 필터링 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-743">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="c1e5e-744">호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공되고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 또는 2.2)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-744">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="c1e5e-745">미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-745">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="c1e5e-746">호스트 필터링 미들웨어는 기본적으로 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-746">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="c1e5e-747">미들웨어를 활성화하려면 *appsettings.json*/*appsettings.\<EnvironmentName>.json*에서 `AllowedHosts` 키를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-747">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="c1e5e-748">값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-748">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="c1e5e-749">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-749">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="c1e5e-750">[전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-750">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="c1e5e-751">전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-751">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="c1e5e-752">전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-752">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="c1e5e-753">호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-753">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="c1e5e-754">전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-754">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="c1e5e-755">Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-755">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c1e5e-756">Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-756">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="c1e5e-757">Kestrel에서는 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-757">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="c1e5e-758">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c1e5e-758">HTTPS</span></span>
* <span data-ttu-id="c1e5e-759">[Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드</span><span class="sxs-lookup"><span data-stu-id="c1e5e-759">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="c1e5e-760">Nginx 뒤의 고성능을 위한 Unix 소켓</span><span class="sxs-lookup"><span data-stu-id="c1e5e-760">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="c1e5e-761">Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-761">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="c1e5e-762">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c1e5e-762">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="c1e5e-763">Kestrel을 역방향 프록시와 함께 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="c1e5e-763">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="c1e5e-764">Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버*와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-764">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c1e5e-765">역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-765">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="c1e5e-766">Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-766">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="c1e5e-768">Kestrel은 역방향 프록시 구성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-768">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c1e5e-770">역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-770">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="c1e5e-771">역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-771">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="c1e5e-772">Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-772">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="c1e5e-773">포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-773">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="c1e5e-774">역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-774">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="c1e5e-775">역방향 프록시:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-775">A reverse proxy:</span></span>

* <span data-ttu-id="c1e5e-776">호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-776">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="c1e5e-777">구성 및 방어의 추가 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-777">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="c1e5e-778">기존 인프라와 잘 통합될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-778">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="c1e5e-779">부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-779">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="c1e5e-780">역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-780">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="c1e5e-781">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-781">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="c1e5e-782">ASP.NET Core 앱에서 Kestrel을 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="c1e5e-782">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="c1e5e-783">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-783">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c1e5e-784">ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-784">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="c1e5e-785">*Program.cs*에서 템플릿 코드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> 숨은 기능을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-785">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="c1e5e-786">`CreateDefaultBuilder`를 호출한 후 추가 구성을 제공하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-786">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="c1e5e-787">`CreateDefaultBuilder`와 호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>의 *호스트 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-787">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="c1e5e-788">Kestrel 옵션</span><span class="sxs-lookup"><span data-stu-id="c1e5e-788">Kestrel options</span></span>

<span data-ttu-id="c1e5e-789">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-789">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="c1e5e-790"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-790">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="c1e5e-791">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-791">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="c1e5e-792">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-792">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="c1e5e-793">다음 예에서 C# 코드로 구성된 Kestrel 옵션도 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-793">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="c1e5e-794">예를 들어 파일 구성 공급자는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-794">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="c1e5e-795">다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-795">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="c1e5e-796">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-796">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="c1e5e-797">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-797">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="c1e5e-798">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-798">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="c1e5e-799">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-799">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="c1e5e-800">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-800">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="c1e5e-801">*Program.cs*에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-801">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="c1e5e-802">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-802">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="c1e5e-803">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="c1e5e-803">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="c1e5e-804">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-804">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="c1e5e-805">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-805">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="c1e5e-806">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="c1e5e-806">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="c1e5e-807">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-807">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="c1e5e-808">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-808">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="c1e5e-809">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-809">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="c1e5e-810">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-810">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="c1e5e-811">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="c1e5e-811">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="c1e5e-812">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-812">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="c1e5e-813">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-813">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c1e5e-814">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-814">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="c1e5e-815">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-815">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="c1e5e-816">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-816">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="c1e5e-817">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-817">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c1e5e-818">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-818">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="c1e5e-819">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="c1e5e-819">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="c1e5e-820">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-820">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="c1e5e-821">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-821">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="c1e5e-822">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-822">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="c1e5e-823">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-823">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="c1e5e-824">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-824">A minimum rate also applies to the response.</span></span> <span data-ttu-id="c1e5e-825">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-825">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="c1e5e-826">*Program.cs*에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-826">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="c1e5e-827">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="c1e5e-827">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="c1e5e-828">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-828">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="c1e5e-829">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-829">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="c1e5e-830">동기 IO</span><span class="sxs-lookup"><span data-stu-id="c1e5e-830">Synchronous IO</span></span>

<span data-ttu-id="c1e5e-831"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 IO가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-831"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="c1e5e-832">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-832">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="c1e5e-833">차단 동기 IO 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-833">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="c1e5e-834">비동기 IO를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 활성화하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-834">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="c1e5e-835">다음 예제에서는 동기 IO를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-835">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="c1e5e-836">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-836">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="c1e5e-837">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="c1e5e-837">Endpoint configuration</span></span>

<span data-ttu-id="c1e5e-838">기본적으로 ASP.NET Core는 다음으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-838">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="c1e5e-839">`https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-839">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="c1e5e-840">다음을 사용하여 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-840">Specify URLs using the:</span></span>

* <span data-ttu-id="c1e5e-841">`ASPNETCORE_URLS` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-841">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="c1e5e-842">`--urls` 명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-842">`--urls` command-line argument.</span></span>
* <span data-ttu-id="c1e5e-843">`urls` 호스트 구성 키.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-843">`urls` host configuration key.</span></span>
* <span data-ttu-id="c1e5e-844">`UseUrls` 확장명 메서드.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-844">`UseUrls` extension method.</span></span>

<span data-ttu-id="c1e5e-845">이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-845">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="c1e5e-846">값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-846">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="c1e5e-847">이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-847">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="c1e5e-848">개발 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-848">A development certificate is created:</span></span>

* <span data-ttu-id="c1e5e-849">[.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-849">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="c1e5e-850">[dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-850">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="c1e5e-851">일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-851">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="c1e5e-852">프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-852">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c1e5e-853"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-853">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="c1e5e-854">`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-854">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="c1e5e-855">`KestrelServerOptions` 구성:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-855">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="c1e5e-856">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-856">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="c1e5e-857">지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-857">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="c1e5e-858">`ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-858">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="c1e5e-859">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-859">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="c1e5e-860">각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-860">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="c1e5e-861">`ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-861">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

### <a name="configureiconfiguration"></a><span data-ttu-id="c1e5e-862">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-862">Configure(IConfiguration)</span></span>

<span data-ttu-id="c1e5e-863">입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-863">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="c1e5e-864">구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-864">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="c1e5e-865">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="c1e5e-865">ListenOptions.UseHttps</span></span>

<span data-ttu-id="c1e5e-866">HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-866">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="c1e5e-867">`ListenOptions.UseHttps` 확장:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-867">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="c1e5e-868">`UseHttps` &ndash; 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-868">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="c1e5e-869">기본 인증서가 구성되지 않은 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-869">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="c1e5e-870">`ListenOptions.UseHttps` 매개 변수:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-870">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="c1e5e-871">`filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-871">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="c1e5e-872">`password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-872">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="c1e5e-873">`configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-873">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="c1e5e-874">`ListenOptions`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-874">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="c1e5e-875">`storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-875">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="c1e5e-876">`subject`은 인증서의 주체 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-876">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="c1e5e-877">`allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-877">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="c1e5e-878">`location`은 인증서를 로드할 수 있는 저장소 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-878">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="c1e5e-879">`serverCertificate`은 X.509 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-879">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="c1e5e-880">프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-880">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c1e5e-881">최소한 기본 인증서를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-881">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="c1e5e-882">다음에 설명된 지원되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-882">Supported configurations described next:</span></span>

* <span data-ttu-id="c1e5e-883">구성 없음</span><span class="sxs-lookup"><span data-stu-id="c1e5e-883">No configuration</span></span>
* <span data-ttu-id="c1e5e-884">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="c1e5e-884">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="c1e5e-885">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="c1e5e-885">Change the defaults in code</span></span>

<span data-ttu-id="c1e5e-886">*구성 없음*</span><span class="sxs-lookup"><span data-stu-id="c1e5e-886">*No configuration*</span></span>

<span data-ttu-id="c1e5e-887">Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-887">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="c1e5e-888">*구성에서 기본 인증서를 바꿈*</span><span class="sxs-lookup"><span data-stu-id="c1e5e-888">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="c1e5e-889">`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-889">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="c1e5e-890">기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-890">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="c1e5e-891">디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-891">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="c1e5e-892">다음 *appsettings.json* 예제에서:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-892">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="c1e5e-893">잘못된 인증서 사용을 허가하려면 **AllowInvalid**를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-893">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="c1e5e-894">인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본**에서 정의된 인증서 또는 개발 인증서로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-894">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="c1e5e-895">모든 인증서 노드에 대해 **경로** 및 **암호**를 사용하는 대신 인증서 저장소 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-895">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="c1e5e-896">예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-896">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="c1e5e-897">스키마 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-897">Schema notes:</span></span>

* <span data-ttu-id="c1e5e-898">엔드포인트 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-898">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="c1e5e-899">예를 들어, `HTTPS` 및 `Https`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-899">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="c1e5e-900">`Url` 매개 변수는 각 엔드포인트에 대해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-900">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="c1e5e-901">이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-901">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="c1e5e-902">이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-902">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="c1e5e-903">`Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-903">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="c1e5e-904">`Certificate` 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-904">The `Certificate` section is optional.</span></span> <span data-ttu-id="c1e5e-905">`Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-905">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="c1e5e-906">기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-906">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="c1e5e-907">`Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-907">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="c1e5e-908">많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-908">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="c1e5e-909">`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-909">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="c1e5e-910">`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-910">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="c1e5e-911">각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-911">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="c1e5e-912">여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-912">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="c1e5e-913">`Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-913">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="c1e5e-914">메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-914">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="c1e5e-915">`KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-915">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="c1e5e-916">이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-916">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="c1e5e-917">*코드에서 기본값 변경*</span><span class="sxs-lookup"><span data-stu-id="c1e5e-917">*Change the defaults in code*</span></span>

<span data-ttu-id="c1e5e-918">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-918">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="c1e5e-919">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-919">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="c1e5e-920">*SNI에 대한 Kestrel 지원*</span><span class="sxs-lookup"><span data-stu-id="c1e5e-920">*Kestrel support for SNI*</span></span>

<span data-ttu-id="c1e5e-921">[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-921">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="c1e5e-922">함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-922">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="c1e5e-923">TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-923">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="c1e5e-924">Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-924">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="c1e5e-925">앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-925">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="c1e5e-926">SNI 지원에는 다음 항목이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-926">SNI support requires:</span></span>

* <span data-ttu-id="c1e5e-927">대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-927">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="c1e5e-928">`net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-928">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="c1e5e-929">클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-929">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="c1e5e-930">모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-930">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="c1e5e-931">Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-931">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="c1e5e-932">연결 로깅</span><span class="sxs-lookup"><span data-stu-id="c1e5e-932">Connection logging</span></span>

<span data-ttu-id="c1e5e-933"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-933">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="c1e5e-934">연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-934">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="c1e5e-935">`UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-935">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="c1e5e-936">`UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-936">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="c1e5e-937">TCP 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="c1e5e-937">Bind to a TCP socket</span></span>

<span data-ttu-id="c1e5e-938"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-938">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="c1e5e-939">예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-939">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="c1e5e-940">동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-940">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="c1e5e-941">Unix 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="c1e5e-941">Bind to a Unix socket</span></span>

<span data-ttu-id="c1e5e-942">이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-942">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

### <a name="port-0"></a><span data-ttu-id="c1e5e-943">포트 0</span><span class="sxs-lookup"><span data-stu-id="c1e5e-943">Port 0</span></span>

<span data-ttu-id="c1e5e-944">포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-944">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="c1e5e-945">다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-945">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="c1e5e-946">앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-946">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="c1e5e-947">제한 사항</span><span class="sxs-lookup"><span data-stu-id="c1e5e-947">Limitations</span></span>

<span data-ttu-id="c1e5e-948">다음 방법으로 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-948">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="c1e5e-949">`--urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="c1e5e-949">`--urls` command-line argument</span></span>
* <span data-ttu-id="c1e5e-950">`urls` 호스트 구성 키</span><span class="sxs-lookup"><span data-stu-id="c1e5e-950">`urls` host configuration key</span></span>
* <span data-ttu-id="c1e5e-951">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="c1e5e-951">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="c1e5e-952">이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-952">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="c1e5e-953">그러나 다음과 같은 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-953">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="c1e5e-954">HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).</span><span class="sxs-lookup"><span data-stu-id="c1e5e-954">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="c1e5e-955">`Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-955">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="c1e5e-956">IIS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="c1e5e-956">IIS endpoint configuration</span></span>

<span data-ttu-id="c1e5e-957">IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-957">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="c1e5e-958">자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-958">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="c1e5e-959">전송 구성</span><span class="sxs-lookup"><span data-stu-id="c1e5e-959">Transport configuration</span></span>

<span data-ttu-id="c1e5e-960">ASP.NET Core 2.1 릴리스에서 Kestrel의 기본 전송은 더 이상 Libuv에 기반하지 않으며 대신 관리 소켓에 기반합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-960">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="c1e5e-961"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출하고 다음 패키지 중 하나를 사용하는 2.1로 업그레이드된 ASP.NET Core 2.0 앱의 주요 변경 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-961">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="c1e5e-962">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)(직접 패키지 참조)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-962">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="c1e5e-963">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="c1e5e-963">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="c1e5e-964">Libuv를 사용해야 하는 프로젝트의 경우:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-964">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="c1e5e-965">[Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 패키지에 대한 종속성을 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-965">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="c1e5e-966"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-966">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="c1e5e-967">URL 접두사</span><span class="sxs-lookup"><span data-stu-id="c1e5e-967">URL prefixes</span></span>

<span data-ttu-id="c1e5e-968">`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-968">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="c1e5e-969">HTTP URL 접두사만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-969">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="c1e5e-970">Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-970">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="c1e5e-971">포트 번호가 있는 IPv4 주소</span><span class="sxs-lookup"><span data-stu-id="c1e5e-971">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="c1e5e-972">`0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-972">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="c1e5e-973">포트 번호가 있는 IPv6 주소</span><span class="sxs-lookup"><span data-stu-id="c1e5e-973">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="c1e5e-974">`[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-974">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="c1e5e-975">포트 번호가 있는 호스트 이름</span><span class="sxs-lookup"><span data-stu-id="c1e5e-975">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="c1e5e-976">호스트 이름, `*` 및 `+`는 특별하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-976">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="c1e5e-977">유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-977">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="c1e5e-978">서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-978">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="c1e5e-979">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-979">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="c1e5e-980">포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP</span><span class="sxs-lookup"><span data-stu-id="c1e5e-980">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="c1e5e-981">`localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-981">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="c1e5e-982">요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-982">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="c1e5e-983">루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-983">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="c1e5e-984">호스트 필터링</span><span class="sxs-lookup"><span data-stu-id="c1e5e-984">Host filtering</span></span>

<span data-ttu-id="c1e5e-985">Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-985">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="c1e5e-986">호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-986">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="c1e5e-987">명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-987">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="c1e5e-988">`Host` 헤더의 유효성이 검사되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-988">`Host` headers aren't validated.</span></span>

<span data-ttu-id="c1e5e-989">해결 방법으로 호스트 필터링 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-989">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="c1e5e-990">호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공되고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 또는 2.2)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-990">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="c1e5e-991">미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-991">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="c1e5e-992">호스트 필터링 미들웨어는 기본적으로 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-992">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="c1e5e-993">미들웨어를 활성화하려면 *appsettings.json*/*appsettings.\<EnvironmentName>.json*에서 `AllowedHosts` 키를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-993">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="c1e5e-994">값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-994">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="c1e5e-995">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c1e5e-995">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="c1e5e-996">[전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-996">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="c1e5e-997">전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-997">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="c1e5e-998">전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-998">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="c1e5e-999">호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-999">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="c1e5e-1000">전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c1e5e-1000">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c1e5e-1001">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c1e5e-1001">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="c1e5e-1002">RFC 7230: 메시지 구문 및 라우팅(섹션 5.4: 호스트)</span><span class="sxs-lookup"><span data-stu-id="c1e5e-1002">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
