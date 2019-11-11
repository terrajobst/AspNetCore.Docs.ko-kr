---
title: ASP.NET Core에서 Kestrel 웹 서버 구현
author: guardrex
description: ASP.NET Core의 플랫폼 간 웹 서버인 Kestrel에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/31/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: bab751bc1453481a11114a7a8c0787fa5576e500
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73427067"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="6bea0-103">ASP.NET Core에서 Kestrel 웹 서버 구현</span><span class="sxs-lookup"><span data-stu-id="6bea0-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="6bea0-104">작성자: [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6bea0-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6bea0-105">Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="6bea0-106">Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="6bea0-107">Kestrel에서는 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="6bea0-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="6bea0-108">HTTPS</span></span>
* <span data-ttu-id="6bea0-109">[Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드</span><span class="sxs-lookup"><span data-stu-id="6bea0-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="6bea0-110">Nginx 뒤의 고성능을 위한 Unix 소켓</span><span class="sxs-lookup"><span data-stu-id="6bea0-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="6bea0-111">HTTP/2(macOS&dagger; 제외)</span><span class="sxs-lookup"><span data-stu-id="6bea0-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="6bea0-112">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="6bea0-113">Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="6bea0-114">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6bea0-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="6bea0-115">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="6bea0-115">HTTP/2 support</span></span>

<span data-ttu-id="6bea0-116">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해 [HTTP/2](https://httpwg.org/specs/rfc7540.html)를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="6bea0-117">운영 체제&dagger;</span><span class="sxs-lookup"><span data-stu-id="6bea0-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="6bea0-118">Windows Server 2016/Windows 10 이상&Dagger;</span><span class="sxs-lookup"><span data-stu-id="6bea0-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="6bea0-119">Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)</span><span class="sxs-lookup"><span data-stu-id="6bea0-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="6bea0-120">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="6bea0-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="6bea0-121">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="6bea0-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="6bea0-122">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="6bea0-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="6bea0-123">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="6bea0-124">&Dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="6bea0-125">이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="6bea0-126">TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="6bea0-127">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="6bea0-128">HTTP/2는 기본적으로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="6bea0-129">구성에 대한 자세한 내용은 [Kestrel 옵션](#kestrel-options) 및 [ListenOptions.Protocols](#listenoptionsprotocols) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="6bea0-130">Kestrel을 역방향 프록시와 함께 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="6bea0-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="6bea0-131">Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버*와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="6bea0-132">역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="6bea0-133">Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="6bea0-135">Kestrel은 역방향 프록시 구성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="6bea0-137">역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="6bea0-138">역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="6bea0-139">Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="6bea0-140">포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="6bea0-141">역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="6bea0-142">역방향 프록시:</span><span class="sxs-lookup"><span data-stu-id="6bea0-142">A reverse proxy:</span></span>

* <span data-ttu-id="6bea0-143">호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="6bea0-144">구성 및 방어의 추가 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="6bea0-145">기존 인프라와 잘 통합될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="6bea0-146">부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="6bea0-147">역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="6bea0-148">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="6bea0-149">ASP.NET Core 앱에서 Kestrel을 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="6bea0-149">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="6bea0-150">ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="6bea0-151">*Program.cs*에서 앱은 백그라운드에서 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>을 호출하는 `ConfigureWebHostDefaults`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-151">In *Program.cs*, the app calls `ConfigureWebHostDefaults`, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="6bea0-152">호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/generic-host#set-up-a-host>의 *호스트 설정* 및 *기본 작성기 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="6bea0-153">`ConfigureWebHostDefaults`를 호출한 후 추가 구성을 제공하려면 `ConfigureKestrel`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="6bea0-154">Kestrel 옵션</span><span class="sxs-lookup"><span data-stu-id="6bea0-154">Kestrel options</span></span>

<span data-ttu-id="6bea0-155">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="6bea0-156"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="6bea0-157">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="6bea0-158">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="6bea0-159">다음 예에서 C# 코드로 구성된 Kestrel 옵션도 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-159">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="6bea0-160">예를 들어 파일 구성 공급자는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-160">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="6bea0-161">다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-161">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="6bea0-162">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-162">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="6bea0-163">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-163">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="6bea0-164">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-164">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="6bea0-165">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-165">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="6bea0-166">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-166">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="6bea0-167">*Program.cs*에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-167">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="6bea0-168">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-168">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="6bea0-169">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="6bea0-169">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="6bea0-170">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-170">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="6bea0-171">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-171">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="6bea0-172">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="6bea0-172">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="6bea0-173">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-173">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="6bea0-174">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-174">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="6bea0-175">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-175">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="6bea0-176">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-176">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="6bea0-177">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="6bea0-177">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="6bea0-178">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-178">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="6bea0-179">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-179">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="6bea0-180">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-180">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="6bea0-181">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-181">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="6bea0-182">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-182">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="6bea0-183">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-183">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="6bea0-184">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-184">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="6bea0-185">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="6bea0-185">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="6bea0-186">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-186">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="6bea0-187">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-187">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="6bea0-188">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-188">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="6bea0-189">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-189">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="6bea0-190">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-190">A minimum rate also applies to the response.</span></span> <span data-ttu-id="6bea0-191">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-191">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="6bea0-192">*Program.cs*에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-192">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="6bea0-193">미들웨어에서 요청당 최소 속도 제한을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-193">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="6bea0-194">요청 멀티플렉싱에 대한 프로토콜의 지원으로 인해 요청별 속도 제한을 수정하는 것이 HTTP/2에 대해 일반적으로 지원되지 않으므로, 이전 샘플에서 참조된 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>는 HTTP/2 요청에 대해 `HttpContext.Features`에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-194">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="6bea0-195">그러나 HTTP/2 요청에 대해서도 `IHttpMinRequestBodyDataRateFeature.MinDataRate`를 `null`로 설정하여 요청별 읽기 속도 제한을 *완전히 비활성화*할 수 있으므로 HTTP/2 요청에 대한 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>는 여전히 존재합니다`HttpContext.Features`.</span><span class="sxs-lookup"><span data-stu-id="6bea0-195">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="6bea0-196">`IHttpMinRequestBodyDataRateFeature.MinDataRate` 읽기를 시도하거나 `null`이 아닌 값으로 설정하려고 하면 HTTP/2 요청이 있을 때 `NotSupportedException`이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-196">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="6bea0-197">`KestrelServerOptions.Limits`를 통해 구성된 서버 전체 속도 제한은 여전히 HTTP/1.x 및 HTTP/2 연결 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-197">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="6bea0-198">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="6bea0-198">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="6bea0-199">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-199">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="6bea0-200">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-200">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="6bea0-201">연결당 최대 스트림</span><span class="sxs-lookup"><span data-stu-id="6bea0-201">Maximum streams per connection</span></span>

<span data-ttu-id="6bea0-202">`Http2.MaxStreamsPerConnection`은 HTTP/2 연결당 동시 요청 스트림 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-202">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="6bea0-203">초과 스트림은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-203">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="6bea0-204">기본값은 100입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-204">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="6bea0-205">헤더 테이블 크기</span><span class="sxs-lookup"><span data-stu-id="6bea0-205">Header table size</span></span>

<span data-ttu-id="6bea0-206">HPACK 디코더는 HTTP/2 연결에 대한 HTTP 헤더의 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-206">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="6bea0-207">`Http2.HeaderTableSize`는 HPACK 디코더가 사용하는 헤더 압축 테이블의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-207">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="6bea0-208">값은 8진수로 제공되며 영(0)보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-208">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="6bea0-209">기본값은 4096입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-209">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="6bea0-210">최대 프레임 크기</span><span class="sxs-lookup"><span data-stu-id="6bea0-210">Maximum frame size</span></span>

<span data-ttu-id="6bea0-211">`Http2.MaxFrameSize`는 서버에서 받거나 보낸 HTTP/2 연결 프레임 페이로드의 최대 허용 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-211">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="6bea0-212">값은 8진수로 제공되며 2^14(16,384)와 2^24-1(16,777,215) 사이여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-212">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="6bea0-213">기본값은 2^14(16,384)입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-213">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="6bea0-214">최대 요청 헤더 크기</span><span class="sxs-lookup"><span data-stu-id="6bea0-214">Maximum request header size</span></span>

<span data-ttu-id="6bea0-215">`Http2.MaxRequestHeaderFieldSize`는 요청 헤더 값의 8진수로 허용되는 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-215">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="6bea0-216">이 한도는 모두 압축된 표현과 압축되지 않은 표현으로 이름과 값 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-216">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="6bea0-217">값은 0보다 커야 합니다(0).</span><span class="sxs-lookup"><span data-stu-id="6bea0-217">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="6bea0-218">기본값은 8,192입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-218">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="6bea0-219">초기 연결 창 크기</span><span class="sxs-lookup"><span data-stu-id="6bea0-219">Initial connection window size</span></span>

<span data-ttu-id="6bea0-220">`Http2.InitialConnectionWindowSize`는 연결당 모든 요청(스트림)을 통해 한 번에 집계하는 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-220">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="6bea0-221">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-221">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="6bea0-222">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-222">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="6bea0-223">기본값은 128KB(131,072)입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-223">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="6bea0-224">초기 스트림 창 크기</span><span class="sxs-lookup"><span data-stu-id="6bea0-224">Initial stream window size</span></span>

<span data-ttu-id="6bea0-225">`Http2.InitialStreamWindowSize`는 요청(스트림)당 한 번에 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-225">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="6bea0-226">요청은 `Http2.InitialConnectionWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-226">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="6bea0-227">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-227">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="6bea0-228">기본값은 96KB(98,304)입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-228">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="6bea0-229">동기 IO</span><span class="sxs-lookup"><span data-stu-id="6bea0-229">Synchronous IO</span></span>

<span data-ttu-id="6bea0-230"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 IO가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-230"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="6bea0-231">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-231">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="6bea0-232">차단 동기 IO 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-232">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="6bea0-233">비동기 IO를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 활성화하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-233">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="6bea0-234">다음 예제에서는 동기 IO를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-234">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="6bea0-235">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-235">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="6bea0-236">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="6bea0-236">Endpoint configuration</span></span>

<span data-ttu-id="6bea0-237">기본적으로 ASP.NET Core는 다음으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-237">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="6bea0-238">`https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)</span><span class="sxs-lookup"><span data-stu-id="6bea0-238">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="6bea0-239">다음을 사용하여 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-239">Specify URLs using the:</span></span>

* <span data-ttu-id="6bea0-240">`ASPNETCORE_URLS` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="6bea0-240">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="6bea0-241">`--urls` 명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="6bea0-241">`--urls` command-line argument.</span></span>
* <span data-ttu-id="6bea0-242">`urls` 호스트 구성 키.</span><span class="sxs-lookup"><span data-stu-id="6bea0-242">`urls` host configuration key.</span></span>
* <span data-ttu-id="6bea0-243">`UseUrls` 확장명 메서드.</span><span class="sxs-lookup"><span data-stu-id="6bea0-243">`UseUrls` extension method.</span></span>

<span data-ttu-id="6bea0-244">이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-244">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="6bea0-245">값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="6bea0-245">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="6bea0-246">이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-246">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="6bea0-247">개발 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-247">A development certificate is created:</span></span>

* <span data-ttu-id="6bea0-248">[.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.</span><span class="sxs-lookup"><span data-stu-id="6bea0-248">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="6bea0-249">[dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-249">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="6bea0-250">일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-250">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="6bea0-251">프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-251">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="6bea0-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-252">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="6bea0-253">`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).</span><span class="sxs-lookup"><span data-stu-id="6bea0-253">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="6bea0-254">`KestrelServerOptions` 구성:</span><span class="sxs-lookup"><span data-stu-id="6bea0-254">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="6bea0-255">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="6bea0-255">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="6bea0-256">지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-256">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="6bea0-257">`ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-257">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="6bea0-258">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="6bea0-258">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="6bea0-259">각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-259">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="6bea0-260">`ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-260">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="6bea0-261">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="6bea0-261">Configure(IConfiguration)</span></span>

<span data-ttu-id="6bea0-262">입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-262">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="6bea0-263">구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-263">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="6bea0-264">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="6bea0-264">ListenOptions.UseHttps</span></span>

<span data-ttu-id="6bea0-265">HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-265">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="6bea0-266">`ListenOptions.UseHttps` 확장:</span><span class="sxs-lookup"><span data-stu-id="6bea0-266">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="6bea0-267">`UseHttps` &ndash; 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-267">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="6bea0-268">기본 인증서가 구성되지 않은 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-268">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="6bea0-269">`ListenOptions.UseHttps` 매개 변수:</span><span class="sxs-lookup"><span data-stu-id="6bea0-269">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="6bea0-270">`filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-270">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="6bea0-271">`password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-271">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="6bea0-272">`configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-272">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="6bea0-273">`ListenOptions`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-273">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="6bea0-274">`storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-274">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="6bea0-275">`subject`은 인증서의 주체 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-275">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="6bea0-276">`allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-276">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="6bea0-277">`location`은 인증서를 로드할 수 있는 저장소 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-277">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="6bea0-278">`serverCertificate`은 X.509 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-278">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="6bea0-279">프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-279">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="6bea0-280">최소한 기본 인증서를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-280">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="6bea0-281">다음에 설명된 지원되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-281">Supported configurations described next:</span></span>

* <span data-ttu-id="6bea0-282">구성 없음</span><span class="sxs-lookup"><span data-stu-id="6bea0-282">No configuration</span></span>
* <span data-ttu-id="6bea0-283">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="6bea0-283">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="6bea0-284">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="6bea0-284">Change the defaults in code</span></span>

<span data-ttu-id="6bea0-285">*구성 없음*</span><span class="sxs-lookup"><span data-stu-id="6bea0-285">*No configuration*</span></span>

<span data-ttu-id="6bea0-286">Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).</span><span class="sxs-lookup"><span data-stu-id="6bea0-286">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="6bea0-287">*구성에서 기본 인증서를 바꿈*</span><span class="sxs-lookup"><span data-stu-id="6bea0-287">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="6bea0-288">`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-288">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="6bea0-289">기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-289">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="6bea0-290">디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-290">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="6bea0-291">다음 *appsettings.json* 예제에서:</span><span class="sxs-lookup"><span data-stu-id="6bea0-291">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="6bea0-292">잘못된 인증서 사용을 허가하려면 **AllowInvalid**를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).</span><span class="sxs-lookup"><span data-stu-id="6bea0-292">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="6bea0-293">인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본**에서 정의된 인증서 또는 개발 인증서로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-293">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="6bea0-294">모든 인증서 노드에 대해 **경로** 및 **암호**를 사용하는 대신 인증서 저장소 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-294">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="6bea0-295">예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-295">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="6bea0-296">스키마 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="6bea0-296">Schema notes:</span></span>

* <span data-ttu-id="6bea0-297">엔드포인트 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-297">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="6bea0-298">예를 들어, `HTTPS` 및 `Https`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-298">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="6bea0-299">`Url` 매개 변수는 각 엔드포인트에 대해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-299">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="6bea0-300">이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-300">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="6bea0-301">이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-301">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="6bea0-302">`Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-302">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="6bea0-303">`Certificate` 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-303">The `Certificate` section is optional.</span></span> <span data-ttu-id="6bea0-304">`Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-304">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="6bea0-305">기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-305">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="6bea0-306">`Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-306">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="6bea0-307">많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-307">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="6bea0-308">`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-308">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="6bea0-309">`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-309">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="6bea0-310">각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-310">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="6bea0-311">여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-311">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="6bea0-312">`Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-312">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="6bea0-313">메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-313">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="6bea0-314">`KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-314">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="6bea0-315">이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-315">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="6bea0-316">*코드에서 기본값 변경*</span><span class="sxs-lookup"><span data-stu-id="6bea0-316">*Change the defaults in code*</span></span>

<span data-ttu-id="6bea0-317">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-317">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="6bea0-318">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-318">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="6bea0-319">*SNI에 대한 Kestrel 지원*</span><span class="sxs-lookup"><span data-stu-id="6bea0-319">*Kestrel support for SNI*</span></span>

<span data-ttu-id="6bea0-320">[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-320">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="6bea0-321">함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-321">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="6bea0-322">TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-322">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="6bea0-323">Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-323">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="6bea0-324">앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-324">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="6bea0-325">SNI 지원에는 다음 항목이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-325">SNI support requires:</span></span>

* <span data-ttu-id="6bea0-326">대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-326">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="6bea0-327">`net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-327">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="6bea0-328">클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-328">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="6bea0-329">모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-329">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="6bea0-330">Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-330">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="6bea0-331">연결 로깅</span><span class="sxs-lookup"><span data-stu-id="6bea0-331">Connection logging</span></span>

<span data-ttu-id="6bea0-332"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-332">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="6bea0-333">연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-333">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="6bea0-334">`UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-334">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="6bea0-335">`UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-335">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="6bea0-336">TCP 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="6bea0-336">Bind to a TCP socket</span></span>

<span data-ttu-id="6bea0-337"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-337">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="6bea0-338">예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-338">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="6bea0-339">동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-339">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="6bea0-340">Unix 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="6bea0-340">Bind to a Unix socket</span></span>

<span data-ttu-id="6bea0-341">이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-341">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="6bea0-342">포트 0</span><span class="sxs-lookup"><span data-stu-id="6bea0-342">Port 0</span></span>

<span data-ttu-id="6bea0-343">포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-343">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="6bea0-344">다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-344">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="6bea0-345">앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-345">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="6bea0-346">제한 사항</span><span class="sxs-lookup"><span data-stu-id="6bea0-346">Limitations</span></span>

<span data-ttu-id="6bea0-347">다음 방법으로 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-347">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="6bea0-348">`--urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="6bea0-348">`--urls` command-line argument</span></span>
* <span data-ttu-id="6bea0-349">`urls` 호스트 구성 키</span><span class="sxs-lookup"><span data-stu-id="6bea0-349">`urls` host configuration key</span></span>
* <span data-ttu-id="6bea0-350">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="6bea0-350">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="6bea0-351">이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-351">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="6bea0-352">그러나 다음과 같은 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-352">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="6bea0-353">HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).</span><span class="sxs-lookup"><span data-stu-id="6bea0-353">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="6bea0-354">`Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-354">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="6bea0-355">IIS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="6bea0-355">IIS endpoint configuration</span></span>

<span data-ttu-id="6bea0-356">IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-356">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="6bea0-357">자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-357">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="6bea0-358">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="6bea0-358">ListenOptions.Protocols</span></span>

<span data-ttu-id="6bea0-359">`Protocols` 속성은 연결 엔드포인트 또는 서버에 대해 사용할 수 있는 HTTP 프로토콜(`HttpProtocols`)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-359">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="6bea0-360">`HttpProtocols` 열거형의 `Protocols` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-360">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="6bea0-361">`HttpProtocols` 열거형 값</span><span class="sxs-lookup"><span data-stu-id="6bea0-361">`HttpProtocols` enum value</span></span> | <span data-ttu-id="6bea0-362">허용되는 연결 프로토콜</span><span class="sxs-lookup"><span data-stu-id="6bea0-362">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="6bea0-363">HTTP/1.1 전용.</span><span class="sxs-lookup"><span data-stu-id="6bea0-363">HTTP/1.1 only.</span></span> <span data-ttu-id="6bea0-364">TLS와 함께 또는 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-364">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="6bea0-365">HTTP/2 전용.</span><span class="sxs-lookup"><span data-stu-id="6bea0-365">HTTP/2 only.</span></span> <span data-ttu-id="6bea0-366">클라이언트가 [이전 기술 모드](https://tools.ietf.org/html/rfc7540#section-3.4)를 지원하는 경우에만 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-366">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="6bea0-367">HTTP/1.1 및 HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6bea0-367">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="6bea0-368">HTTP/2를 사용하려면 클라이언트가 TLS [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 핸드셰이크에서 HTTP/2를 선택해야 합니다. 그렇지 않으면 연결 기본값은 HTTP/1.1입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-368">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="6bea0-369">모든 엔드포인트의 기본 `ListenOptions.Protocols` 값은 `HttpProtocols.Http1AndHttp2`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-369">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="6bea0-370">HTTP/2에 대한 TLS 제한 사항:</span><span class="sxs-lookup"><span data-stu-id="6bea0-370">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="6bea0-371">TLS 버전 1.2 이상</span><span class="sxs-lookup"><span data-stu-id="6bea0-371">TLS version 1.2 or later</span></span>
* <span data-ttu-id="6bea0-372">재협상 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="6bea0-372">Renegotiation disabled</span></span>
* <span data-ttu-id="6bea0-373">압축 사용 안함</span><span class="sxs-lookup"><span data-stu-id="6bea0-373">Compression disabled</span></span>
* <span data-ttu-id="6bea0-374">최소 임시 키 교환 크기:</span><span class="sxs-lookup"><span data-stu-id="6bea0-374">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="6bea0-375">ECDHE(타원 곡선 Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 최소 224비트</span><span class="sxs-lookup"><span data-stu-id="6bea0-375">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="6bea0-376">유한 필드 DHE(Diffie-Hellman) &lbrack;`TLS12`&rbrack; &ndash; 최소 2048비트</span><span class="sxs-lookup"><span data-stu-id="6bea0-376">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="6bea0-377">암호 도구 모음이 차단 목록에 없음</span><span class="sxs-lookup"><span data-stu-id="6bea0-377">Cipher suite not blacklisted</span></span>

<span data-ttu-id="6bea0-378">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;(P-256 타원 곡선 &lbrack;`FIPS186`&rbrack; 포함)는 기본적으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-378">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="6bea0-379">다음 예제는 포트 8000에서 HTTP/1.1 및 HTTP/2 연결을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-379">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="6bea0-380">연결은 제공된 인증서를 사용하여 TLS로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-380">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="6bea0-381">필요할 경우 연결 미들웨어를 사용하여 각 연결을 기준으로 특정 암호에 대한 TLS 핸드셰이크를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-381">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="6bea0-382">다음 예는 앱에서 지원하지 않는 모든 암호화 알고리즘에 대해 <xref:System.NotSupportedException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-382">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="6bea0-383">또는 [ITlsHandshakeFeature CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm)을 정의하고 허용되는 암호 그룹 목록과 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-383">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="6bea0-384">[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 암호화 알고리즘과 함께 사용되는 암호화는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-384">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="6bea0-385"><xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> 람다를 통해 연결 필터링을 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-385">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="6bea0-386">Linux에서 <xref:System.Net.Security.CipherSuitesPolicy>를 사용하여 각 연결을 기준으로 TLS 핸드셰이크를 필터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-386">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="6bea0-387">구성에서 프로토콜 설정</span><span class="sxs-lookup"><span data-stu-id="6bea0-387">*Set the protocol from configuration*</span></span>

<span data-ttu-id="6bea0-388">`CreateDefaultBuilder`는 기본적으로 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-388">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="6bea0-389">다음 *appsettings.json* 예제에서는 HTTP/1.1을 모든 엔드포인트에 대한 기본 연결 프로토콜로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-389">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="6bea0-390">다음 *appsettings.json* 예제에서는 특정 엔드포인트에 대한 HTTP/1.1 연결 프로토콜을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-390">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="6bea0-391">코드에서 지정한 프로토콜이 구성에서 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-391">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="6bea0-392">전송 구성</span><span class="sxs-lookup"><span data-stu-id="6bea0-392">Transport configuration</span></span>

<span data-ttu-id="6bea0-393">Libuv를 사용해야 하는 프로젝트의 경우(<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span><span class="sxs-lookup"><span data-stu-id="6bea0-393">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="6bea0-394">[Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 패키지에 대한 종속성을 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-394">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="6bea0-395">`IWebHostBuilder`에서 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 호출:</span><span class="sxs-lookup"><span data-stu-id="6bea0-395">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="6bea0-396">URL 접두사</span><span class="sxs-lookup"><span data-stu-id="6bea0-396">URL prefixes</span></span>

<span data-ttu-id="6bea0-397">`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-397">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="6bea0-398">HTTP URL 접두사만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-398">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="6bea0-399">Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-399">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="6bea0-400">포트 번호가 있는 IPv4 주소</span><span class="sxs-lookup"><span data-stu-id="6bea0-400">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="6bea0-401">`0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-401">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="6bea0-402">포트 번호가 있는 IPv6 주소</span><span class="sxs-lookup"><span data-stu-id="6bea0-402">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="6bea0-403">`[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-403">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="6bea0-404">포트 번호가 있는 호스트 이름</span><span class="sxs-lookup"><span data-stu-id="6bea0-404">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="6bea0-405">호스트 이름, `*` 및 `+`는 특별하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-405">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="6bea0-406">유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-406">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="6bea0-407">서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-407">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="6bea0-408">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-408">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="6bea0-409">포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP</span><span class="sxs-lookup"><span data-stu-id="6bea0-409">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="6bea0-410">`localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-410">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="6bea0-411">요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-411">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="6bea0-412">루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-412">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="6bea0-413">호스트 필터링</span><span class="sxs-lookup"><span data-stu-id="6bea0-413">Host filtering</span></span>

<span data-ttu-id="6bea0-414">Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-414">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="6bea0-415">호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-415">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="6bea0-416">명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-416">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="6bea0-417">`Host` 헤더의 유효성이 검사되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-417">`Host` headers aren't validated.</span></span>

<span data-ttu-id="6bea0-418">해결 방법으로 호스트 필터링 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-418">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="6bea0-419">호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공하며, 이 패키지는 ASP.NET Core 앱을 위해 암시적으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-419">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="6bea0-420">미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-420">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="6bea0-421">호스트 필터링 미들웨어는 기본적으로 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-421">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="6bea0-422">미들웨어를 활성화하려면 *appsettings.json*/*appsettings.\<EnvironmentName>.json*에서 `AllowedHosts` 키를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-422">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="6bea0-423">값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-423">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="6bea0-424">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6bea0-424">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="6bea0-425">[전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-425">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="6bea0-426">전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-426">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="6bea0-427">전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-427">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="6bea0-428">호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-428">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="6bea0-429">전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-429">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6bea0-430">Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-430">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="6bea0-431">Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-431">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="6bea0-432">Kestrel에서는 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-432">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="6bea0-433">HTTPS</span><span class="sxs-lookup"><span data-stu-id="6bea0-433">HTTPS</span></span>
* <span data-ttu-id="6bea0-434">[Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드</span><span class="sxs-lookup"><span data-stu-id="6bea0-434">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="6bea0-435">Nginx 뒤의 고성능을 위한 Unix 소켓</span><span class="sxs-lookup"><span data-stu-id="6bea0-435">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="6bea0-436">HTTP/2(macOS&dagger; 제외)</span><span class="sxs-lookup"><span data-stu-id="6bea0-436">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="6bea0-437">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-437">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="6bea0-438">Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-438">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="6bea0-439">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6bea0-439">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="6bea0-440">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="6bea0-440">HTTP/2 support</span></span>

<span data-ttu-id="6bea0-441">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해 [HTTP/2](https://httpwg.org/specs/rfc7540.html)를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-441">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="6bea0-442">운영 체제&dagger;</span><span class="sxs-lookup"><span data-stu-id="6bea0-442">Operating system&dagger;</span></span>
  * <span data-ttu-id="6bea0-443">Windows Server 2016/Windows 10 이상&Dagger;</span><span class="sxs-lookup"><span data-stu-id="6bea0-443">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="6bea0-444">Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)</span><span class="sxs-lookup"><span data-stu-id="6bea0-444">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="6bea0-445">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="6bea0-445">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="6bea0-446">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="6bea0-446">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="6bea0-447">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="6bea0-447">TLS 1.2 or later connection</span></span>

<span data-ttu-id="6bea0-448">이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-448">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="6bea0-449">&Dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-449">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="6bea0-450">이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-450">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="6bea0-451">TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-451">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="6bea0-452">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-452">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="6bea0-453">HTTP/2는 기본적으로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-453">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="6bea0-454">구성에 대한 자세한 내용은 [Kestrel 옵션](#kestrel-options) 및 [ListenOptions.Protocols](#listenoptionsprotocols) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-454">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="6bea0-455">Kestrel을 역방향 프록시와 함께 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="6bea0-455">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="6bea0-456">Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버*와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-456">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="6bea0-457">역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-457">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="6bea0-458">Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-458">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="6bea0-460">Kestrel은 역방향 프록시 구성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-460">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="6bea0-462">역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-462">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="6bea0-463">역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-463">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="6bea0-464">Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-464">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="6bea0-465">포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-465">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="6bea0-466">역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-466">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="6bea0-467">역방향 프록시:</span><span class="sxs-lookup"><span data-stu-id="6bea0-467">A reverse proxy:</span></span>

* <span data-ttu-id="6bea0-468">호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-468">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="6bea0-469">구성 및 방어의 추가 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-469">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="6bea0-470">기존 인프라와 잘 통합될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-470">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="6bea0-471">부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-471">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="6bea0-472">역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-472">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="6bea0-473">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-473">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="6bea0-474">ASP.NET Core 앱에서 Kestrel을 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="6bea0-474">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="6bea0-475">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-475">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="6bea0-476">ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-476">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="6bea0-477">*Program.cs*에서 템플릿 코드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> 숨은 기능을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-477">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="6bea0-478">`CreateDefaultBuilder`와 호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>의 *호스트 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-478">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="6bea0-479">`CreateDefaultBuilder`를 호출한 후 추가 구성을 제공하려면 `ConfigureKestrel`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-479">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="6bea0-480">앱이 호스트를 설정하기 위해 `CreateDefaultBuilder`를 호출하지 않는 경우 `ConfigureKestrel`을 호출하기 **전에** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-480">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="6bea0-481">Kestrel 옵션</span><span class="sxs-lookup"><span data-stu-id="6bea0-481">Kestrel options</span></span>

<span data-ttu-id="6bea0-482">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-482">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="6bea0-483"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-483">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="6bea0-484">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-484">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="6bea0-485">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-485">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="6bea0-486">다음 예에서 C# 코드로 구성된 Kestrel 옵션도 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-486">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="6bea0-487">예를 들어 파일 구성 공급자는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-487">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="6bea0-488">다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-488">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="6bea0-489">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-489">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="6bea0-490">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-490">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="6bea0-491">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-491">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="6bea0-492">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-492">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="6bea0-493">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-493">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="6bea0-494">*Program.cs*에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-494">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="6bea0-495">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-495">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="6bea0-496">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="6bea0-496">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="6bea0-497">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-497">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="6bea0-498">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-498">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="6bea0-499">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="6bea0-499">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="6bea0-500">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-500">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="6bea0-501">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-501">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="6bea0-502">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-502">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="6bea0-503">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-503">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="6bea0-504">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="6bea0-504">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="6bea0-505">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-505">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="6bea0-506">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-506">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="6bea0-507">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-507">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="6bea0-508">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-508">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="6bea0-509">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-509">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="6bea0-510">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-510">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="6bea0-511">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-511">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="6bea0-512">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="6bea0-512">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="6bea0-513">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-513">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="6bea0-514">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-514">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="6bea0-515">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-515">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="6bea0-516">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-516">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="6bea0-517">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-517">A minimum rate also applies to the response.</span></span> <span data-ttu-id="6bea0-518">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-518">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="6bea0-519">*Program.cs*에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-519">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="6bea0-520">미들웨어에서 요청당 최소 속도 제한을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-520">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="6bea0-521">요청 멀티플렉싱에 대한 프로토콜의 지원으로 인해 요청별 속도 제한을 수정하는 것이 HTTP/2에 대해 지원되지 않으므로, 이전 샘플에서 참조된 속도 기능은 HTTP/2 요청에 대해 `HttpContext.Features`에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-521">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="6bea0-522">`KestrelServerOptions.Limits`를 통해 구성된 서버 전체 속도 제한은 여전히 HTTP/1.x 및 HTTP/2 연결 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-522">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="6bea0-523">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="6bea0-523">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="6bea0-524">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-524">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="6bea0-525">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-525">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="6bea0-526">연결당 최대 스트림</span><span class="sxs-lookup"><span data-stu-id="6bea0-526">Maximum streams per connection</span></span>

<span data-ttu-id="6bea0-527">`Http2.MaxStreamsPerConnection`은 HTTP/2 연결당 동시 요청 스트림 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-527">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="6bea0-528">초과 스트림은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-528">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="6bea0-529">기본값은 100입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-529">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="6bea0-530">헤더 테이블 크기</span><span class="sxs-lookup"><span data-stu-id="6bea0-530">Header table size</span></span>

<span data-ttu-id="6bea0-531">HPACK 디코더는 HTTP/2 연결에 대한 HTTP 헤더의 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-531">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="6bea0-532">`Http2.HeaderTableSize`는 HPACK 디코더가 사용하는 헤더 압축 테이블의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-532">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="6bea0-533">값은 8진수로 제공되며 영(0)보다 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-533">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="6bea0-534">기본값은 4096입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-534">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="6bea0-535">최대 프레임 크기</span><span class="sxs-lookup"><span data-stu-id="6bea0-535">Maximum frame size</span></span>

<span data-ttu-id="6bea0-536">`Http2.MaxFrameSize`는 수신할 HTTP/2 연결 프레임 페이로드의 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-536">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="6bea0-537">값은 8진수로 제공되며 2^14(16,384)와 2^24-1(16,777,215) 사이여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-537">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="6bea0-538">기본값은 2^14(16,384)입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-538">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="6bea0-539">최대 요청 헤더 크기</span><span class="sxs-lookup"><span data-stu-id="6bea0-539">Maximum request header size</span></span>

<span data-ttu-id="6bea0-540">`Http2.MaxRequestHeaderFieldSize`는 요청 헤더 값의 8진수로 허용되는 최대 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-540">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="6bea0-541">이 한도는 이름과 값이 모두 압축된 표현과 압축되지 않은 표현으로 함께 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-541">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="6bea0-542">값은 0보다 커야 합니다(0).</span><span class="sxs-lookup"><span data-stu-id="6bea0-542">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="6bea0-543">기본값은 8,192입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-543">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="6bea0-544">초기 연결 창 크기</span><span class="sxs-lookup"><span data-stu-id="6bea0-544">Initial connection window size</span></span>

<span data-ttu-id="6bea0-545">`Http2.InitialConnectionWindowSize`는 연결당 모든 요청(스트림)을 통해 한 번에 집계하는 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-545">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="6bea0-546">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-546">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="6bea0-547">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-547">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="6bea0-548">기본값은 128KB(131,072)입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-548">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="6bea0-549">초기 스트림 창 크기</span><span class="sxs-lookup"><span data-stu-id="6bea0-549">Initial stream window size</span></span>

<span data-ttu-id="6bea0-550">`Http2.InitialStreamWindowSize`는 요청(스트림)당 한 번에 서버 버퍼의 최대 요청 본문 데이터를 바이트 단위로 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-550">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="6bea0-551">요청은 `Http2.InitialStreamWindowSize`에 의해서도 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-551">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="6bea0-552">값은 65,535보다 크거나 같아야 하며 2^31(2,147,483,648)보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-552">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="6bea0-553">기본값은 96KB(98,304)입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-553">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="6bea0-554">동기 IO</span><span class="sxs-lookup"><span data-stu-id="6bea0-554">Synchronous IO</span></span>

<span data-ttu-id="6bea0-555"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 IO가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-555"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="6bea0-556">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-556">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="6bea0-557">차단 동기 IO 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-557">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="6bea0-558">비동기 IO를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 활성화하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-558">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="6bea0-559">다음 예제에서는 동기 IO를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-559">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="6bea0-560">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-560">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="6bea0-561">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="6bea0-561">Endpoint configuration</span></span>

<span data-ttu-id="6bea0-562">기본적으로 ASP.NET Core는 다음으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-562">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="6bea0-563">`https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)</span><span class="sxs-lookup"><span data-stu-id="6bea0-563">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="6bea0-564">다음을 사용하여 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-564">Specify URLs using the:</span></span>

* <span data-ttu-id="6bea0-565">`ASPNETCORE_URLS` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="6bea0-565">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="6bea0-566">`--urls` 명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="6bea0-566">`--urls` command-line argument.</span></span>
* <span data-ttu-id="6bea0-567">`urls` 호스트 구성 키.</span><span class="sxs-lookup"><span data-stu-id="6bea0-567">`urls` host configuration key.</span></span>
* <span data-ttu-id="6bea0-568">`UseUrls` 확장명 메서드.</span><span class="sxs-lookup"><span data-stu-id="6bea0-568">`UseUrls` extension method.</span></span>

<span data-ttu-id="6bea0-569">이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-569">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="6bea0-570">값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="6bea0-570">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="6bea0-571">이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-571">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="6bea0-572">개발 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-572">A development certificate is created:</span></span>

* <span data-ttu-id="6bea0-573">[.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.</span><span class="sxs-lookup"><span data-stu-id="6bea0-573">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="6bea0-574">[dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-574">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="6bea0-575">일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-575">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="6bea0-576">프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-576">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="6bea0-577"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-577">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="6bea0-578">`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).</span><span class="sxs-lookup"><span data-stu-id="6bea0-578">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="6bea0-579">`KestrelServerOptions` 구성:</span><span class="sxs-lookup"><span data-stu-id="6bea0-579">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="6bea0-580">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="6bea0-580">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="6bea0-581">지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-581">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="6bea0-582">`ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-582">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="6bea0-583">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="6bea0-583">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="6bea0-584">각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-584">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="6bea0-585">`ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-585">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="6bea0-586">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="6bea0-586">Configure(IConfiguration)</span></span>

<span data-ttu-id="6bea0-587">입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-587">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="6bea0-588">구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-588">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="6bea0-589">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="6bea0-589">ListenOptions.UseHttps</span></span>

<span data-ttu-id="6bea0-590">HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-590">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="6bea0-591">`ListenOptions.UseHttps` 확장:</span><span class="sxs-lookup"><span data-stu-id="6bea0-591">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="6bea0-592">`UseHttps` &ndash; 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-592">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="6bea0-593">기본 인증서가 구성되지 않은 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-593">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="6bea0-594">`ListenOptions.UseHttps` 매개 변수:</span><span class="sxs-lookup"><span data-stu-id="6bea0-594">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="6bea0-595">`filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-595">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="6bea0-596">`password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-596">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="6bea0-597">`configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-597">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="6bea0-598">`ListenOptions`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-598">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="6bea0-599">`storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-599">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="6bea0-600">`subject`은 인증서의 주체 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-600">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="6bea0-601">`allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-601">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="6bea0-602">`location`은 인증서를 로드할 수 있는 저장소 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-602">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="6bea0-603">`serverCertificate`은 X.509 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-603">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="6bea0-604">프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-604">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="6bea0-605">최소한 기본 인증서를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-605">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="6bea0-606">다음에 설명된 지원되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-606">Supported configurations described next:</span></span>

* <span data-ttu-id="6bea0-607">구성 없음</span><span class="sxs-lookup"><span data-stu-id="6bea0-607">No configuration</span></span>
* <span data-ttu-id="6bea0-608">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="6bea0-608">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="6bea0-609">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="6bea0-609">Change the defaults in code</span></span>

<span data-ttu-id="6bea0-610">*구성 없음*</span><span class="sxs-lookup"><span data-stu-id="6bea0-610">*No configuration*</span></span>

<span data-ttu-id="6bea0-611">Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).</span><span class="sxs-lookup"><span data-stu-id="6bea0-611">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="6bea0-612">*구성에서 기본 인증서를 바꿈*</span><span class="sxs-lookup"><span data-stu-id="6bea0-612">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="6bea0-613">`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-613">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="6bea0-614">기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-614">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="6bea0-615">디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-615">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="6bea0-616">다음 *appsettings.json* 예제에서:</span><span class="sxs-lookup"><span data-stu-id="6bea0-616">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="6bea0-617">잘못된 인증서 사용을 허가하려면 **AllowInvalid**를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).</span><span class="sxs-lookup"><span data-stu-id="6bea0-617">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="6bea0-618">인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본**에서 정의된 인증서 또는 개발 인증서로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-618">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="6bea0-619">모든 인증서 노드에 대해 **경로** 및 **암호**를 사용하는 대신 인증서 저장소 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-619">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="6bea0-620">예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-620">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="6bea0-621">스키마 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="6bea0-621">Schema notes:</span></span>

* <span data-ttu-id="6bea0-622">엔드포인트 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-622">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="6bea0-623">예를 들어, `HTTPS` 및 `Https`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-623">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="6bea0-624">`Url` 매개 변수는 각 엔드포인트에 대해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-624">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="6bea0-625">이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-625">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="6bea0-626">이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-626">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="6bea0-627">`Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-627">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="6bea0-628">`Certificate` 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-628">The `Certificate` section is optional.</span></span> <span data-ttu-id="6bea0-629">`Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-629">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="6bea0-630">기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-630">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="6bea0-631">`Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-631">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="6bea0-632">많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-632">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="6bea0-633">`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-633">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="6bea0-634">`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-634">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="6bea0-635">각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-635">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="6bea0-636">여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-636">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="6bea0-637">`Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-637">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="6bea0-638">메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-638">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="6bea0-639">`KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-639">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="6bea0-640">이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-640">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="6bea0-641">*코드에서 기본값 변경*</span><span class="sxs-lookup"><span data-stu-id="6bea0-641">*Change the defaults in code*</span></span>

<span data-ttu-id="6bea0-642">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-642">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="6bea0-643">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-643">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="6bea0-644">*SNI에 대한 Kestrel 지원*</span><span class="sxs-lookup"><span data-stu-id="6bea0-644">*Kestrel support for SNI*</span></span>

<span data-ttu-id="6bea0-645">[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-645">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="6bea0-646">함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-646">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="6bea0-647">TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-647">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="6bea0-648">Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-648">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="6bea0-649">앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-649">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="6bea0-650">SNI 지원에는 다음 항목이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-650">SNI support requires:</span></span>

* <span data-ttu-id="6bea0-651">대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-651">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="6bea0-652">`net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-652">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="6bea0-653">클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-653">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="6bea0-654">모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-654">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="6bea0-655">Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-655">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="6bea0-656">연결 로깅</span><span class="sxs-lookup"><span data-stu-id="6bea0-656">Connection logging</span></span>

<span data-ttu-id="6bea0-657"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-657">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="6bea0-658">연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-658">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="6bea0-659">`UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-659">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="6bea0-660">`UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-660">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="6bea0-661">TCP 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="6bea0-661">Bind to a TCP socket</span></span>

<span data-ttu-id="6bea0-662"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-662">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="6bea0-663">예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-663">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="6bea0-664">동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-664">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="6bea0-665">Unix 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="6bea0-665">Bind to a Unix socket</span></span>

<span data-ttu-id="6bea0-666">이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-666">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="6bea0-667">포트 0</span><span class="sxs-lookup"><span data-stu-id="6bea0-667">Port 0</span></span>

<span data-ttu-id="6bea0-668">포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-668">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="6bea0-669">다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-669">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="6bea0-670">앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-670">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="6bea0-671">제한 사항</span><span class="sxs-lookup"><span data-stu-id="6bea0-671">Limitations</span></span>

<span data-ttu-id="6bea0-672">다음 방법으로 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-672">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="6bea0-673">`--urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="6bea0-673">`--urls` command-line argument</span></span>
* <span data-ttu-id="6bea0-674">`urls` 호스트 구성 키</span><span class="sxs-lookup"><span data-stu-id="6bea0-674">`urls` host configuration key</span></span>
* <span data-ttu-id="6bea0-675">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="6bea0-675">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="6bea0-676">이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-676">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="6bea0-677">그러나 다음과 같은 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-677">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="6bea0-678">HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).</span><span class="sxs-lookup"><span data-stu-id="6bea0-678">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="6bea0-679">`Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-679">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="6bea0-680">IIS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="6bea0-680">IIS endpoint configuration</span></span>

<span data-ttu-id="6bea0-681">IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-681">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="6bea0-682">자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-682">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="6bea0-683">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="6bea0-683">ListenOptions.Protocols</span></span>

<span data-ttu-id="6bea0-684">`Protocols` 속성은 연결 엔드포인트 또는 서버에 대해 사용할 수 있는 HTTP 프로토콜(`HttpProtocols`)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-684">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="6bea0-685">`HttpProtocols` 열거형의 `Protocols` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-685">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="6bea0-686">`HttpProtocols` 열거형 값</span><span class="sxs-lookup"><span data-stu-id="6bea0-686">`HttpProtocols` enum value</span></span> | <span data-ttu-id="6bea0-687">허용되는 연결 프로토콜</span><span class="sxs-lookup"><span data-stu-id="6bea0-687">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="6bea0-688">HTTP/1.1 전용.</span><span class="sxs-lookup"><span data-stu-id="6bea0-688">HTTP/1.1 only.</span></span> <span data-ttu-id="6bea0-689">TLS와 함께 또는 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-689">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="6bea0-690">HTTP/2 전용.</span><span class="sxs-lookup"><span data-stu-id="6bea0-690">HTTP/2 only.</span></span> <span data-ttu-id="6bea0-691">클라이언트가 [이전 기술 모드](https://tools.ietf.org/html/rfc7540#section-3.4)를 지원하는 경우에만 TLS 없이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-691">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="6bea0-692">HTTP/1.1 및 HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6bea0-692">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="6bea0-693">HTTP/2를 사용하려면 TLS 및 [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결이 필요합니다. 그렇지 않은 경우 연결은 기본적으로 HTTP/1.1로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-693">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="6bea0-694">기본 프로토콜은 HTTP/1.1입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-694">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="6bea0-695">HTTP/2에 대한 TLS 제한 사항:</span><span class="sxs-lookup"><span data-stu-id="6bea0-695">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="6bea0-696">TLS 버전 1.2 이상</span><span class="sxs-lookup"><span data-stu-id="6bea0-696">TLS version 1.2 or later</span></span>
* <span data-ttu-id="6bea0-697">재협상 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="6bea0-697">Renegotiation disabled</span></span>
* <span data-ttu-id="6bea0-698">압축 사용 안함</span><span class="sxs-lookup"><span data-stu-id="6bea0-698">Compression disabled</span></span>
* <span data-ttu-id="6bea0-699">최소 임시 키 교환 크기:</span><span class="sxs-lookup"><span data-stu-id="6bea0-699">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="6bea0-700">ECDHE(타원 곡선 Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 최소 224비트</span><span class="sxs-lookup"><span data-stu-id="6bea0-700">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="6bea0-701">유한 필드 DHE(Diffie-Hellman) &lbrack;`TLS12`&rbrack; &ndash; 최소 2048비트</span><span class="sxs-lookup"><span data-stu-id="6bea0-701">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="6bea0-702">암호 도구 모음이 차단 목록에 없음</span><span class="sxs-lookup"><span data-stu-id="6bea0-702">Cipher suite not blacklisted</span></span>

<span data-ttu-id="6bea0-703">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;(P-256 타원 곡선 &lbrack;`FIPS186`&rbrack; 포함)는 기본적으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-703">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="6bea0-704">다음 예제는 포트 8000에서 HTTP/1.1 및 HTTP/2 연결을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-704">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="6bea0-705">연결은 제공된 인증서를 사용하여 TLS로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-705">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="6bea0-706">선택적으로, `IConnectionAdapter` 구현을 만들어 각 연결 단위로 특정 암호에 대한 TLS 핸드셰이크를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-706">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="6bea0-707">구성에서 프로토콜 설정</span><span class="sxs-lookup"><span data-stu-id="6bea0-707">*Set the protocol from configuration*</span></span>

<span data-ttu-id="6bea0-708"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>는 기본적으로 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-708"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="6bea0-709">다음 *appsettings.json* 예제에서는 모든 Kestrel의 엔드포인트에 대해 기본 연결 프로토콜(HTTP/1.1 및 HTTP/2)이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-709">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="6bea0-710">다음 구성 파일 예제에서는 특정 엔드포인트에 대해 연결 프로토콜을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-710">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="6bea0-711">코드에서 지정한 프로토콜이 구성에서 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-711">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="6bea0-712">전송 구성</span><span class="sxs-lookup"><span data-stu-id="6bea0-712">Transport configuration</span></span>

<span data-ttu-id="6bea0-713">ASP.NET Core 2.1 릴리스에서 Kestrel의 기본 전송은 더 이상 Libuv에 기반하지 않으며 대신 관리 소켓에 기반합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-713">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="6bea0-714"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출하고 다음 패키지 중 하나를 사용하는 2.1로 업그레이드된 ASP.NET Core 2.0 앱의 주요 변경 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-714">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="6bea0-715">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)(직접 패키지 참조)</span><span class="sxs-lookup"><span data-stu-id="6bea0-715">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="6bea0-716">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="6bea0-716">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="6bea0-717">Libuv를 사용해야 하는 프로젝트의 경우:</span><span class="sxs-lookup"><span data-stu-id="6bea0-717">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="6bea0-718">[Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 패키지에 대한 종속성을 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-718">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="6bea0-719"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-719">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="6bea0-720">URL 접두사</span><span class="sxs-lookup"><span data-stu-id="6bea0-720">URL prefixes</span></span>

<span data-ttu-id="6bea0-721">`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-721">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="6bea0-722">HTTP URL 접두사만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-722">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="6bea0-723">Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-723">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="6bea0-724">포트 번호가 있는 IPv4 주소</span><span class="sxs-lookup"><span data-stu-id="6bea0-724">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="6bea0-725">`0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-725">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="6bea0-726">포트 번호가 있는 IPv6 주소</span><span class="sxs-lookup"><span data-stu-id="6bea0-726">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="6bea0-727">`[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-727">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="6bea0-728">포트 번호가 있는 호스트 이름</span><span class="sxs-lookup"><span data-stu-id="6bea0-728">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="6bea0-729">호스트 이름, `*` 및 `+`는 특별하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-729">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="6bea0-730">유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-730">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="6bea0-731">서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-731">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="6bea0-732">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-732">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="6bea0-733">포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP</span><span class="sxs-lookup"><span data-stu-id="6bea0-733">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="6bea0-734">`localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-734">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="6bea0-735">요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-735">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="6bea0-736">루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-736">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="6bea0-737">호스트 필터링</span><span class="sxs-lookup"><span data-stu-id="6bea0-737">Host filtering</span></span>

<span data-ttu-id="6bea0-738">Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-738">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="6bea0-739">호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-739">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="6bea0-740">명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-740">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="6bea0-741">`Host` 헤더의 유효성이 검사되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-741">`Host` headers aren't validated.</span></span>

<span data-ttu-id="6bea0-742">해결 방법으로 호스트 필터링 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-742">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="6bea0-743">호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공되고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 또는 2.2)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-743">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="6bea0-744">미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-744">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="6bea0-745">호스트 필터링 미들웨어는 기본적으로 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-745">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="6bea0-746">미들웨어를 활성화하려면 *appsettings.json*/*appsettings.\<EnvironmentName>.json*에서 `AllowedHosts` 키를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-746">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="6bea0-747">값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-747">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="6bea0-748">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6bea0-748">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="6bea0-749">[전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-749">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="6bea0-750">전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-750">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="6bea0-751">전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-751">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="6bea0-752">호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-752">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="6bea0-753">전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-753">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="6bea0-754">Kestrel은 [ASP.NET Core의 플랫폼 간 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-754">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="6bea0-755">Kestrel은 기본적으로 ASP.NET Core 프로젝트 템플릿에 포함된 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-755">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="6bea0-756">Kestrel에서는 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-756">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="6bea0-757">HTTPS</span><span class="sxs-lookup"><span data-stu-id="6bea0-757">HTTPS</span></span>
* <span data-ttu-id="6bea0-758">[Websocket](https://github.com/aspnet/websockets)을 활성화하는 데 사용되는 불투명 업그레이드</span><span class="sxs-lookup"><span data-stu-id="6bea0-758">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="6bea0-759">Nginx 뒤의 고성능을 위한 Unix 소켓</span><span class="sxs-lookup"><span data-stu-id="6bea0-759">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="6bea0-760">Kestrel은 .NET Core에서 지원하는 모든 플랫폼 및 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-760">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="6bea0-761">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6bea0-761">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="6bea0-762">Kestrel을 역방향 프록시와 함께 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="6bea0-762">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="6bea0-763">Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버*와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-763">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="6bea0-764">역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-764">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="6bea0-765">Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-765">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="6bea0-767">Kestrel은 역방향 프록시 구성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-767">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="6bea0-769">역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-769">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="6bea0-770">역방향 프록시 서버 없이 에지 서버로 사용된 Kestrel은 여러 프로세스 간에 동일한 IP 및 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-770">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="6bea0-771">Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더에 관계 없이 해당 포트에 대한 모든 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-771">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="6bea0-772">포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-772">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="6bea0-773">역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-773">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="6bea0-774">역방향 프록시:</span><span class="sxs-lookup"><span data-stu-id="6bea0-774">A reverse proxy:</span></span>

* <span data-ttu-id="6bea0-775">호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-775">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="6bea0-776">구성 및 방어의 추가 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-776">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="6bea0-777">기존 인프라와 잘 통합될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-777">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="6bea0-778">부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-778">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="6bea0-779">역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-779">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="6bea0-780">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-780">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="6bea0-781">ASP.NET Core 앱에서 Kestrel을 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="6bea0-781">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="6bea0-782">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-782">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="6bea0-783">ASP.NET Core 프로젝트 템플릿은 기본적으로 Kestrel을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-783">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="6bea0-784">*Program.cs*에서 템플릿 코드는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> 숨은 기능을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-784">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="6bea0-785">`CreateDefaultBuilder`를 호출한 후 추가 구성을 제공하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-785">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="6bea0-786">`CreateDefaultBuilder`와 호스트 빌드에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>의 *호스트 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-786">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="6bea0-787">Kestrel 옵션</span><span class="sxs-lookup"><span data-stu-id="6bea0-787">Kestrel options</span></span>

<span data-ttu-id="6bea0-788">Kestrel 웹 서버에는 인터넷 연결 배포에 특히 유용한 제약 조건 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-788">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="6bea0-789"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 클래스의 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 속성에서 제약 조건을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-789">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="6bea0-790">`Limits` 속성은 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 클래스의 인스턴스를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-790">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="6bea0-791">다음 예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 네임스페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-791">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="6bea0-792">다음 예에서 C# 코드로 구성된 Kestrel 옵션도 [구성 공급자](xref:fundamentals/configuration/index)를 사용하여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-792">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="6bea0-793">예를 들어 파일 구성 공급자는 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일에서 Kestrel 구성을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-793">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="6bea0-794">다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-794">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="6bea0-795">`Startup.ConfigureServices`에서 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-795">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="6bea0-796">`IConfiguration`의 인스턴스를 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-796">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="6bea0-797">다음 예에서는 삽입된 구성이 `Configuration` 속성에 할당된 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-797">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="6bea0-798">`Startup.ConfigureServices`에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-798">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="6bea0-799">호스트를 빌드할 때 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-799">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="6bea0-800">*Program.cs*에서 구성의 `Kestrel` 섹션을 Kestrel의 구성으로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-800">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="6bea0-801">위의 두 방법 모두 [구성 공급자](xref:fundamentals/configuration/index)에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-801">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="6bea0-802">Keep-alive 시간 제한</span><span class="sxs-lookup"><span data-stu-id="6bea0-802">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="6bea0-803">[keep-alive 시간 제한](https://tools.ietf.org/html/rfc7230#section-6.5)을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-803">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="6bea0-804">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-804">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="6bea0-805">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="6bea0-805">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="6bea0-806">다음 코드를 사용하여 전체 앱에 대한 동시 개방 TCP 연결의 최대 수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-806">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="6bea0-807">HTTP 또는 HTTPS에서 다른 프로토콜(예: WebSocket 요청에서)로 업그레이드된 연결에 대한 별도 제한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-807">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="6bea0-808">연결이 업그레이드된 후 `MaxConcurrentConnections` 제한에 대해 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-808">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="6bea0-809">연결의 최대 수는 기본적으로 무제한(null)입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-809">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="6bea0-810">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="6bea0-810">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="6bea0-811">기본 최대 요청 본문 크기는 약 28.6MB인 30,000,000바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-811">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="6bea0-812">ASP.NET Core MVC 앱에서 한도를 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-812">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="6bea0-813">다음 예제는 모든 요청에서 앱에 대한 제약 조건을 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-813">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="6bea0-814">미들웨어에서 특정 요청에 대한 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-814">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="6bea0-815">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-815">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="6bea0-816">`MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 알려주는 `IsReadOnly` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-816">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="6bea0-817">앱이 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 뒤에서 [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)로 실행되는 경우에는 IIS가 이미 한계를 설정했으므로 Kestrel의 요청 본문 크기 제한은 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-817">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="6bea0-818">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="6bea0-818">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="6bea0-819">Kestrel은 데이터가 지정된 속도(바이트/초)로 도착하는지 1초마다 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-819">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="6bea0-820">속도가 최소 아래로 떨어지면 연결이 시간 초과됩니다. 유예 기간은 Kestrel에서 해당 전송 속도를 최소로 높이기 위해 클라이언트에 제공하는 총 시간입니다. 이 시간 동안 속도는 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-820">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="6bea0-821">유예 기간은 TCP 느린 시작으로 인해 느린 속도로 처음에 데이터를 보내는 연결 중단을 방지하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-821">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="6bea0-822">기본 최소 속도는 5초의 유예 기간으로 240바이트/초입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-822">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="6bea0-823">최소 속도는 응답에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-823">A minimum rate also applies to the response.</span></span> <span data-ttu-id="6bea0-824">요청 제한 및 응답 제한을 설정하는 코드는 속성 및 인터페이스 이름에 `RequestBody` 또는 `Response`를 갖는 것을 제외하고 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-824">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="6bea0-825">*Program.cs*에서 최소 데이터 속도를 구성하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-825">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="6bea0-826">요청 헤더 시간 제한</span><span class="sxs-lookup"><span data-stu-id="6bea0-826">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="6bea0-827">서버가 요청 헤더를 수신하는 데 걸리는 최대 시간을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-827">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="6bea0-828">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-828">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="6bea0-829">동기 IO</span><span class="sxs-lookup"><span data-stu-id="6bea0-829">Synchronous IO</span></span>

<span data-ttu-id="6bea0-830"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>는 요청 및 응답에 대해 동기 IO가 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-830"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="6bea0-831">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-831">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="6bea0-832">차단 동기 IO 작업 수가 많으면 스레드 풀이 고갈되어 앱이 응답하지 않게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-832">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="6bea0-833">비동기 IO를 지원하지 않는 라이브러리를 사용할 때만 `AllowSynchronousIO`를 활성화하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-833">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="6bea0-834">다음 예제에서는 동기 IO를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-834">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="6bea0-835">다른 Kestrel 옵션 및 제한에 대한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-835">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="6bea0-836">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="6bea0-836">Endpoint configuration</span></span>

<span data-ttu-id="6bea0-837">기본적으로 ASP.NET Core는 다음으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-837">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="6bea0-838">`https://localhost:5001` (로컬 개발 인증서가 제공되는 경우)</span><span class="sxs-lookup"><span data-stu-id="6bea0-838">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="6bea0-839">다음을 사용하여 URL을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-839">Specify URLs using the:</span></span>

* <span data-ttu-id="6bea0-840">`ASPNETCORE_URLS` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="6bea0-840">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="6bea0-841">`--urls` 명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="6bea0-841">`--urls` command-line argument.</span></span>
* <span data-ttu-id="6bea0-842">`urls` 호스트 구성 키.</span><span class="sxs-lookup"><span data-stu-id="6bea0-842">`urls` host configuration key.</span></span>
* <span data-ttu-id="6bea0-843">`UseUrls` 확장명 메서드.</span><span class="sxs-lookup"><span data-stu-id="6bea0-843">`UseUrls` extension method.</span></span>

<span data-ttu-id="6bea0-844">이러한 접근 방식을 사용하여 제공된 값은 하나 이상의 HTTP 및 HTTPS 엔드포인트(기본 인증서가 사용 가능한 경우의 HTTPS)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-844">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="6bea0-845">값을 세미콜론으로 구분된 목록으로 구성합니다(예를 들어, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="6bea0-845">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="6bea0-846">이러한 접근 방식에 대한 자세한 내용은 [서버 URL](xref:fundamentals/host/web-host#server-urls) 및 [구성 재정의](xref:fundamentals/host/web-host#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-846">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="6bea0-847">개발 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-847">A development certificate is created:</span></span>

* <span data-ttu-id="6bea0-848">[.NET Core SDK](/dotnet/core/sdk)가 설치되는 경우.</span><span class="sxs-lookup"><span data-stu-id="6bea0-848">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="6bea0-849">[dev-certs 도구](xref:aspnetcore-2.1#https)가 인증서를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-849">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="6bea0-850">일부 브라우저에서는 로컬 개발 인증서를 신뢰하도록 명시적 권한을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-850">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="6bea0-851">프로젝트 템플릿은 기본적으로 HTTPS에서 실행되도록 앱을 구성하고 [HTTPS 리디렉션 및 HSTS 지원](xref:security/enforcing-ssl)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-851">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="6bea0-852"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 또는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 메서드를 호출하여 Kestrel의 URL 접두사 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-852">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="6bea0-853">`UseUrls`, `--urls` 명령줄 인수 `urls` 호스트 구성 키 및 `ASPNETCORE_URLS` 환경 변수도 작동하지만 이 섹션의 뒷부분에 명시된 제한 사항이 있습니다(HTTPS 엔드포인트 구성에 대해 기본 인증서를 사용할 수 있어야 합니다).</span><span class="sxs-lookup"><span data-stu-id="6bea0-853">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="6bea0-854">`KestrelServerOptions` 구성:</span><span class="sxs-lookup"><span data-stu-id="6bea0-854">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="6bea0-855">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="6bea0-855">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="6bea0-856">지정된 각 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-856">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="6bea0-857">`ConfigureEndpointDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-857">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="6bea0-858">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="6bea0-858">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="6bea0-859">각 HTTPS 엔드포인트에 대해 실행할 구성 `Action`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-859">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="6bea0-860">`ConfigureHttpsDefaults`의 여러 차례 호출은 `Action`에 앞서 마지막으로 지정된 `Action`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-860">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="6bea0-861">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="6bea0-861">Configure(IConfiguration)</span></span>

<span data-ttu-id="6bea0-862">입력으로 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 사용하는 Kestrel를 설정하기 위한 구성 로더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-862">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="6bea0-863">구성은 Kestrel용 구성 섹션에 대해 범위를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-863">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="6bea0-864">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="6bea0-864">ListenOptions.UseHttps</span></span>

<span data-ttu-id="6bea0-865">HTTPS를 사용하려면 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-865">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="6bea0-866">`ListenOptions.UseHttps` 확장:</span><span class="sxs-lookup"><span data-stu-id="6bea0-866">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="6bea0-867">`UseHttps` &ndash; 기본 인증서를 통해 HTTPS를 사용하려면 Kestrel 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-867">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="6bea0-868">기본 인증서가 구성되지 않은 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-868">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="6bea0-869">`ListenOptions.UseHttps` 매개 변수:</span><span class="sxs-lookup"><span data-stu-id="6bea0-869">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="6bea0-870">`filename`은 앱의 콘텐츠 파일을 포함하는 디렉터리와 관련된 인증서 파일의 경로 및 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-870">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="6bea0-871">`password`은 X.509 인증서 데이터에 액세스하는 데 필요한 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-871">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="6bea0-872">`configureOptions`은 `HttpsConnectionAdapterOptions`를 구성하는 `Action`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-872">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="6bea0-873">`ListenOptions`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-873">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="6bea0-874">`storeName`은 인증서를 로드할 수 있는 인증서 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-874">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="6bea0-875">`subject`은 인증서의 주체 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-875">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="6bea0-876">`allowInvalid`은 자체 서명된 인증서와 같이 잘못된 인증서를 고려해야 할 경우를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-876">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="6bea0-877">`location`은 인증서를 로드할 수 있는 저장소 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-877">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="6bea0-878">`serverCertificate`은 X.509 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-878">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="6bea0-879">프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-879">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="6bea0-880">최소한 기본 인증서를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-880">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="6bea0-881">다음에 설명된 지원되는 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-881">Supported configurations described next:</span></span>

* <span data-ttu-id="6bea0-882">구성 없음</span><span class="sxs-lookup"><span data-stu-id="6bea0-882">No configuration</span></span>
* <span data-ttu-id="6bea0-883">구성에서 기본 인증서를 바꿈</span><span class="sxs-lookup"><span data-stu-id="6bea0-883">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="6bea0-884">코드에서 기본값 변경</span><span class="sxs-lookup"><span data-stu-id="6bea0-884">Change the defaults in code</span></span>

<span data-ttu-id="6bea0-885">*구성 없음*</span><span class="sxs-lookup"><span data-stu-id="6bea0-885">*No configuration*</span></span>

<span data-ttu-id="6bea0-886">Kestrel은 `http://localhost:5000` 및 `https://localhost:5001`에서 수신 대기합니다(기본 인증서가 사용 가능한 경우).</span><span class="sxs-lookup"><span data-stu-id="6bea0-886">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="6bea0-887">*구성에서 기본 인증서를 바꿈*</span><span class="sxs-lookup"><span data-stu-id="6bea0-887">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="6bea0-888">`CreateDefaultBuilder`는 기본적으로 `Configure(context.Configuration.GetSection("Kestrel"))`을 호출하여 Kestrel 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-888">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="6bea0-889">기본 HTTPS 앱 설정 구성 스키마는 Kestrel에 대해 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-889">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="6bea0-890">디스크 상의 파일에서 또는 인증서 저장소에서 사용할 인증서 및 URL을 포함하여 여러 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-890">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="6bea0-891">다음 *appsettings.json* 예제에서:</span><span class="sxs-lookup"><span data-stu-id="6bea0-891">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="6bea0-892">잘못된 인증서 사용을 허가하려면 **AllowInvalid**를 `true`으로 설정합니다(예를 들어, 자체 서명된 인증서).</span><span class="sxs-lookup"><span data-stu-id="6bea0-892">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="6bea0-893">인증서를 지정하지 않는 모든 HTTPS 엔드포인트(다음 예제에서 **HttpsDefaultCert**)는 **인증서** > **기본**에서 정의된 인증서 또는 개발 인증서로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-893">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="6bea0-894">모든 인증서 노드에 대해 **경로** 및 **암호**를 사용하는 대신 인증서 저장소 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-894">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="6bea0-895">예를 들어, **인증서** > **기본** 인증서는 다음과 같이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-895">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="6bea0-896">스키마 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="6bea0-896">Schema notes:</span></span>

* <span data-ttu-id="6bea0-897">엔드포인트 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-897">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="6bea0-898">예를 들어, `HTTPS` 및 `Https`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-898">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="6bea0-899">`Url` 매개 변수는 각 엔드포인트에 대해 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-899">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="6bea0-900">이 매개 변수에 대한 형식은 단일 값으로 제한된 경우를 제외하고 최상위 `Urls` 구성 매개 변수와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-900">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="6bea0-901">이러한 엔드포인트는 추가하기보다는 최상위 `Urls` 구성에서 정의된 엔드포인트를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-901">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="6bea0-902">`Listen`을 통해 코드에서 정의된 엔드포인트는 구성 섹션에서 정의된 엔드포인트로 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-902">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="6bea0-903">`Certificate` 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-903">The `Certificate` section is optional.</span></span> <span data-ttu-id="6bea0-904">`Certificate` 섹션이 지정되지 않은 경우 이전 시나리오에서 정의된 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-904">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="6bea0-905">기본값이 사용 가능하지 않은 경우 서버는 예외를 throw하고 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-905">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="6bea0-906">`Certificate` 섹션은 **경로**&ndash;**암호** 및 **주체**&ndash;**저장소** 인증서 모두를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-906">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="6bea0-907">많은 엔드포인트가 포트 충돌을 일으키지 않는 한 이런 방식으로 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-907">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="6bea0-908">`options.Configure(context.Configuration.GetSection("{SECTION}"))`은 구성된 엔드포인트의 설정을 보완하는 데 사용될 수 있는 `.Endpoint(string name, listenOptions => { })` 메서드를 통해 `KestrelConfigurationLoader`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-908">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="6bea0-909">`KestrelServerOptions.ConfigurationLoader`에 액세스하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에서 제공한 로더와 같이 기존 로더에서 반복을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-909">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="6bea0-910">각 엔드포인트에 대한 구성 섹션은 `Endpoint` 메서드의 옵션에서 사용 가능하므로 사용자 지정 설정을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-910">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="6bea0-911">여러 구성은 다른 섹션을 통해 다시 `options.Configure(context.Configuration.GetSection("{SECTION}"))`을 호출하여 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-911">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="6bea0-912">`Load`은 이전 인스턴스에서 명시적으로 호출되지 않는 한 마지막 구성만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-912">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="6bea0-913">메타패키지는 `Load`을 호출하지 않으므로 기본 구성 섹션을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-913">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="6bea0-914">`KestrelConfigurationLoader`은 `KestrelServerOptions`에서 `Endpoint` 오버로드로 `Listen` API 제품군을 미러링하므로 코드 및 구성 엔드포인트를 동일 장소에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-914">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="6bea0-915">이러한 오버로드는 이름을 사용하지 않고 구성에서 기본 설정만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-915">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="6bea0-916">*코드에서 기본값 변경*</span><span class="sxs-lookup"><span data-stu-id="6bea0-916">*Change the defaults in code*</span></span>

<span data-ttu-id="6bea0-917">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 이전 시나리오에서 지정된 기본 인증서 재정의를 포함한 `ListenOptions` 및 `HttpsConnectionAdapterOptions`에 대해 기본 설정을 변경하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-917">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="6bea0-918">`ConfigureEndpointDefaults` 및 `ConfigureHttpsDefaults`는 모든 엔드포인트가 구성되기 전에 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-918">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="6bea0-919">*SNI에 대한 Kestrel 지원*</span><span class="sxs-lookup"><span data-stu-id="6bea0-919">*Kestrel support for SNI*</span></span>

<span data-ttu-id="6bea0-920">[서버 이름 표시(SNI)](https://tools.ietf.org/html/rfc6066#section-3)는 동일한 IP 주소 및 포트에서 여러 도메인을 호스트하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-920">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="6bea0-921">함수에 대한 SNI의 경우 클라이언트는 TLS 핸드셰이크 동안 보안 세션에 대한 호스트 이름을 서버로 보내므로 서버에서 올바른 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-921">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="6bea0-922">TLS 핸드셰이크 다음에 오는 보안 세션 동안 클라이언트는 서버와 암호화된 통신을 위해 제공된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-922">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="6bea0-923">Kestrel은 `ServerCertificateSelector` 콜백을 통해 SNI를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-923">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="6bea0-924">앱이 호스트 이름을 검사하고 적절한 인증서를 선택하도록 허용하려면 연결당 한 번씩 콜백이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-924">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="6bea0-925">SNI 지원에는 다음 항목이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-925">SNI support requires:</span></span>

* <span data-ttu-id="6bea0-926">대상 프레임워크 `netcoreapp2.1` 이상에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-926">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="6bea0-927">`net461` 이상에서 콜백이 호출되지만 `name`는 항상 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-927">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="6bea0-928">클라이언트가 TLS 핸드셰이크에서 호스트 이름 매개 변수를 제공하지 않는 경우 `name`은 또한 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-928">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="6bea0-929">모든 웹 사이트는 동일한 Kestrel 인스턴스에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-929">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="6bea0-930">Kestrel은 역방향 프록시 없이 여러 인스턴스에서 IP 주소와 포트를 공유하도록 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-930">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="6bea0-931">연결 로깅</span><span class="sxs-lookup"><span data-stu-id="6bea0-931">Connection logging</span></span>

<span data-ttu-id="6bea0-932"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>을 호출하여 연결에 대한 바이트 수준 통신을 위한 디버그 수준을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-932">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="6bea0-933">연결 로깅은 낮은 수준 통신(예: TLS 암호화 중에, 프록시 뒤에서 등)에서 문제 해결을 진행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-933">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="6bea0-934">`UseConnectionLogging`이 `UseHttps` 앞에 오면 암호화된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-934">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="6bea0-935">`UseConnectionLogging`이 `UseHttps` 뒤에 오면 암호 해독된 트래픽이 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-935">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="6bea0-936">TCP 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="6bea0-936">Bind to a TCP socket</span></span>

<span data-ttu-id="6bea0-937"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 메서드는 TCP 소켓에 바인딩하고 옵션 람다는 X.509 인증서 구성을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-937">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="6bea0-938">예제에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>를 사용하여 엔드포인트에 대한 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-938">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="6bea0-939">동일한 API를 사용하여 특정 엔드포인트에 대한 다른 Kestrel 설정을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-939">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="6bea0-940">Unix 소켓에 바인딩</span><span class="sxs-lookup"><span data-stu-id="6bea0-940">Bind to a Unix socket</span></span>

<span data-ttu-id="6bea0-941">이 예제에 나와 있는 것처럼 Nginx를 사용하여 성능을 향상하기 위해 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 사용하여 Unix 소켓을 수신 대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-941">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

### <a name="port-0"></a><span data-ttu-id="6bea0-942">포트 0</span><span class="sxs-lookup"><span data-stu-id="6bea0-942">Port 0</span></span>

<span data-ttu-id="6bea0-943">포트 번호 `0`가 지정되는 경우 Kestrel은 동적으로 사용 가능한 포트에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-943">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="6bea0-944">다음 예제에서는 Kestrel이 실제로 런타임에 바인딩한 포트를 확인하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-944">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="6bea0-945">앱이 실행되는 경우 콘솔 창 출력은 앱이 연결될 수 있는 동적 포트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-945">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="6bea0-946">제한 사항</span><span class="sxs-lookup"><span data-stu-id="6bea0-946">Limitations</span></span>

<span data-ttu-id="6bea0-947">다음 방법으로 엔드포인트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-947">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="6bea0-948">`--urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="6bea0-948">`--urls` command-line argument</span></span>
* <span data-ttu-id="6bea0-949">`urls` 호스트 구성 키</span><span class="sxs-lookup"><span data-stu-id="6bea0-949">`urls` host configuration key</span></span>
* <span data-ttu-id="6bea0-950">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="6bea0-950">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="6bea0-951">이러한 메서드는 코드를 Kestrel이 아닌 서버와 작동하도록 하려는 경우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-951">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="6bea0-952">그러나 다음과 같은 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-952">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="6bea0-953">HTTPS 엔드포인트 구성에서 기본 인증서를 제공하지 않는 한 이러한 방법으로는 HTTPS를 사용할 수 없습니다(예: 이 항목의 앞부분에 표시된 것처럼 `KestrelServerOptions` 구성 또는 구성 파일 사용).</span><span class="sxs-lookup"><span data-stu-id="6bea0-953">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="6bea0-954">`Listen` 및 `UseUrls` 방식 모두를 동시에 사용할 경우 `Listen` 엔드포인트는 `UseUrls` 엔드포인트를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-954">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="6bea0-955">IIS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="6bea0-955">IIS endpoint configuration</span></span>

<span data-ttu-id="6bea0-956">IIS를 사용하는 경우 IIS 재정의 바인딩에 대한 URL 바인딩은 `Listen` 또는 `UseUrls`에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-956">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="6bea0-957">자세한 내용은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-957">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="6bea0-958">전송 구성</span><span class="sxs-lookup"><span data-stu-id="6bea0-958">Transport configuration</span></span>

<span data-ttu-id="6bea0-959">ASP.NET Core 2.1 릴리스에서 Kestrel의 기본 전송은 더 이상 Libuv에 기반하지 않으며 대신 관리 소켓에 기반합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-959">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="6bea0-960"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출하고 다음 패키지 중 하나를 사용하는 2.1로 업그레이드된 ASP.NET Core 2.0 앱의 주요 변경 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-960">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="6bea0-961">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)(직접 패키지 참조)</span><span class="sxs-lookup"><span data-stu-id="6bea0-961">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="6bea0-962">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="6bea0-962">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="6bea0-963">Libuv를 사용해야 하는 프로젝트의 경우:</span><span class="sxs-lookup"><span data-stu-id="6bea0-963">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="6bea0-964">[Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 패키지에 대한 종속성을 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-964">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="6bea0-965"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-965">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="6bea0-966">URL 접두사</span><span class="sxs-lookup"><span data-stu-id="6bea0-966">URL prefixes</span></span>

<span data-ttu-id="6bea0-967">`UseUrls`, `--urls` 명령줄 인수, `urls` 호스트 구성 키 또는 `ASPNETCORE_URLS` 환경 변수를 사용하는 경우 URL 접두사는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-967">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="6bea0-968">HTTP URL 접두사만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-968">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="6bea0-969">Kestrel은 `UseUrls`을 사용하여 URL 바인딩을 구성하는 경우 HTTPS를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-969">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="6bea0-970">포트 번호가 있는 IPv4 주소</span><span class="sxs-lookup"><span data-stu-id="6bea0-970">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="6bea0-971">`0.0.0.0`은 모든 IPv4 주소에 바인딩하는 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-971">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="6bea0-972">포트 번호가 있는 IPv6 주소</span><span class="sxs-lookup"><span data-stu-id="6bea0-972">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="6bea0-973">`[::]`는 IPv4 `0.0.0.0`에 해당하는 IPv6입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-973">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="6bea0-974">포트 번호가 있는 호스트 이름</span><span class="sxs-lookup"><span data-stu-id="6bea0-974">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="6bea0-975">호스트 이름, `*` 및 `+`는 특별하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-975">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="6bea0-976">유효한 IP 주소 또는 `localhost`로 인식하지 않는 모든 항목은 모든 IPv4 및 IPv6 IP에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-976">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="6bea0-977">서로 다른 호스트 이름을 같은 포트에서 서로 다른 ASP.NET Core 앱에 바인딩하려면 IIS, Nginx 또는 Apache와 같은 역방향 프록시 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-977">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="6bea0-978">역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](#host-filtering)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-978">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="6bea0-979">포트 번호가 있는 호스트 `localhost` 이름 또는 포트 번호가 있는 루프백 IP</span><span class="sxs-lookup"><span data-stu-id="6bea0-979">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="6bea0-980">`localhost`가 지정되면 Kestrel은 IPv4 및 IPv6 루프백 인터페이스 모두에 바인딩하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-980">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="6bea0-981">요청된 포트가 루프백 인터페이스 중 하나의 다른 서비스에서 사용 중인 경우 Kestrel은 시작에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-981">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="6bea0-982">루프백 인터페이스 중 하나를 다른 이유(일반적으로 IPv6이 지원되지 않으므로)로 사용할 수 없는 경우 Kestrel은 경고를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-982">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="6bea0-983">호스트 필터링</span><span class="sxs-lookup"><span data-stu-id="6bea0-983">Host filtering</span></span>

<span data-ttu-id="6bea0-984">Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-984">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="6bea0-985">호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-985">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="6bea0-986">명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-986">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="6bea0-987">`Host` 헤더의 유효성이 검사되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-987">`Host` headers aren't validated.</span></span>

<span data-ttu-id="6bea0-988">해결 방법으로 호스트 필터링 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-988">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="6bea0-989">호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공되고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 또는 2.2)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-989">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="6bea0-990">미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-990">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="6bea0-991">호스트 필터링 미들웨어는 기본적으로 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-991">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="6bea0-992">미들웨어를 활성화하려면 *appsettings.json*/*appsettings.\<EnvironmentName>.json*에서 `AllowedHosts` 키를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-992">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="6bea0-993">값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-993">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="6bea0-994">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6bea0-994">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="6bea0-995">[전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-995">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="6bea0-996">전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-996">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="6bea0-997">전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-997">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="6bea0-998">호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="6bea0-998">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="6bea0-999">전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6bea0-999">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6bea0-1000">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6bea0-1000">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="6bea0-1001">RFC 7230: 메시지 구문 및 라우팅(섹션 5.4: 호스트)</span><span class="sxs-lookup"><span data-stu-id="6bea0-1001">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
