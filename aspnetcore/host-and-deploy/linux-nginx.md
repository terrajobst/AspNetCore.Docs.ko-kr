---
title: Nginx를 사용하여 Linux에서 ASP.NET Core 호스트
author: guardrex
description: Ubuntu 16.04에서 Nginx를 역방향 프록시로 설정하여 Kestrel에서 실행되는 ASP.NET Core 웹앱에 HTTP 트래픽을 전달하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2019
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: d9db2a251820d0dab26f8a6bd2eb755090154165
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813339"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="e25f2-103">Nginx를 사용하여 Linux에서 ASP.NET Core 호스트</span><span class="sxs-lookup"><span data-stu-id="e25f2-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="e25f2-104">작성자: [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="e25f2-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="e25f2-105">이 가이드에서는 Ubuntu 16.04 Server에서 프로덕션 준비 ASP.NET Core 환경을 설정하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="e25f2-106">이 지침은 최신 버전의 Ubuntu에서 작동할 수 있지만 최신 버전에서 테스트되지는 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="e25f2-107">ASP.NET Core에서 지원하는 다른 Linux 배포에 대한 자세한 내용은 [Linux에서 .NET Core의 필수 구성 요소](/dotnet/core/linux-prerequisites)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e25f2-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="e25f2-108">Ubuntu 14.04의 경우 Kestrel 프로세스를 모니터링하기 위한 솔루션으로 *supervisord*를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="e25f2-109">*systemd*는 Ubuntu 14.04에서 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="e25f2-110">Ubuntu 14.04 지침의 경우 [이 항목의 이전 버전](https://github.com/aspnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e25f2-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/aspnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="e25f2-111">이 가이드의 내용:</span><span class="sxs-lookup"><span data-stu-id="e25f2-111">This guide:</span></span>

* <span data-ttu-id="e25f2-112">기존 ASP.NET Core 앱을 역방향 프록시 서버 뒤에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="e25f2-113">역방향 프록시 서버를 설정하여 Kestrel 웹 서버에 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="e25f2-114">웹앱이 시작 시 디먼으로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="e25f2-115">웹앱 다시 시작을 지원하도록 프로세스 관리 도구를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e25f2-116">전제 조건</span><span class="sxs-lookup"><span data-stu-id="e25f2-116">Prerequisites</span></span>

1. <span data-ttu-id="e25f2-117">sudo 권한을 가진 표준 사용자 계정으로 Ubuntu 16.04 Server에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="e25f2-118">서버에서 .NET Core 런타임을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="e25f2-119">[.NET Core 모든 다운로드 페이지](https://www.microsoft.com/net/download/all)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-119">Visit the [.NET Core All Downloads page](https://www.microsoft.com/net/download/all).</span></span>
   1. <span data-ttu-id="e25f2-120">**런타임** 아래의 목록에서 최신 미리 보기 상태가 아닌 런타임을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-120">Select the latest non-preview runtime from the list under **Runtime**.</span></span>
   1. <span data-ttu-id="e25f2-121">Ubuntu 버전의 서버와 일치하는 Ubuntu에 대한 지침을 선택하고 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-121">Select and follow the instructions for Ubuntu that match the Ubuntu version of the server.</span></span>
1. <span data-ttu-id="e25f2-122">기존 ASP.NET Core 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-122">An existing ASP.NET Core app.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="e25f2-123">앱 게시 및 복사</span><span class="sxs-lookup"><span data-stu-id="e25f2-123">Publish and copy over the app</span></span>

<span data-ttu-id="e25f2-124">[프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-124">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="e25f2-125">앱을 로컬에서 실행하고 보안 연결(HTTPS)을 확인하도록 구성하지 않은 경우 다음 방법 중 하나를 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-125">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="e25f2-126">보안 로컬 연결을 처리하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-126">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="e25f2-127">자세한 내용은 [HTTPS 구성](#https-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e25f2-127">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="e25f2-128">*Properties/launchSettings.json* 파일의 `applicationUrl` 속성에서 `https://localhost:5001`(있는 경우)을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-128">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="e25f2-129">서버에서 실행할 수 있는 디렉터리(예: *bin/Release/&lt;target_framework_moniker&gt;/publish*)로 앱을 패키징하기 위해 개발 환경에서 [dotnet publish](/dotnet/core/tools/dotnet-publish)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-129">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```console
dotnet publish --configuration Release
```

<span data-ttu-id="e25f2-130">.NET Core 런타임을 서버에서 유지 관리하지 않으려는 경우 앱은 [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)로 게시될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-130">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="e25f2-131">조직의 워크플로에 통합된 도구(예: SCP, SFTP)를 사용하여 ASP.NET Core 앱을 서버에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-131">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="e25f2-132">*var* 디렉터리(예: *var/www/helloapp*)에서 웹앱을 찾는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-132">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="e25f2-133">프로덕션 배포 시나리오에서 지속적인 통합 워크플로는 앱을 게시하고 자산을 서버로 복사하는 워크플로를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-133">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="e25f2-134">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-134">Test the app:</span></span>

1. <span data-ttu-id="e25f2-135">명령줄에서 `dotnet <app_assembly>.dll` 앱을 실행하세요.</span><span class="sxs-lookup"><span data-stu-id="e25f2-135">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="e25f2-136">브라우저에서 `http://<serveraddress>:<port>`로 이동하여 앱이 Linux에서 로컬로 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-136">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="e25f2-137">역방향 프록시 서버 구성</span><span class="sxs-lookup"><span data-stu-id="e25f2-137">Configure a reverse proxy server</span></span>

<span data-ttu-id="e25f2-138">역방향 프록시는 동적 웹앱을 지원하기 위한 일반적인 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-138">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="e25f2-139">역방향 프록시는 HTTP 요청을 종료하고 이 요청을 ASP.NET Core 앱에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-139">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="e25f2-140">역방향 프록시 서버를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-140">Use a reverse proxy server</span></span>

<span data-ttu-id="e25f2-141">Kestrel은 ASP.NET Core에서 동적 콘텐츠를 제공하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-141">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="e25f2-142">그러나 웹 지원 기능은 IIS, Apache 또는 Nginx와 같은 서버만큼 기능이 다양하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-142">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="e25f2-143">역방향 프록시 서버는 정적 콘텐츠 지원, 요청 캐시, 요청 압축 및 HTTP 서버에서 HTTPS 종료 같은 작업을 오프로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-143">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="e25f2-144">역방향 프록시 서버는 전용 컴퓨터에 있거나 HTTP 서버와 함께 배포될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-144">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="e25f2-145">이 가이드에서는 Nginx의 단일 인스턴스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-145">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="e25f2-146">이 인스턴스는 HTTP 서버와 함께 동일한 서버에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-146">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="e25f2-147">요구 사항에 따라 다른 설정을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-147">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="e25f2-148">요청이 역방향 프록시를 통해 전달되므로 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 패키지의 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-148">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="e25f2-149">이 미들웨어는 `X-Forwarded-Proto` 헤더를 사용하여 `Request.Scheme`을 업데이트하므로 리디렉션 URI 및 기타 보안 정책이 제대로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-149">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="e25f2-150">전달된 헤더 미들웨어를 호출한 후에 인증, 링크 생성, 리디렉션 및 지리적 위치 등 체계에 따라 달라지는 구성 요소를 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-150">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="e25f2-151">일반 규칙으로 전달된 헤더 미들웨어는 진단 및 오류 처리 미들웨어를 제외한 다른 미들웨어 전에 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-151">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="e25f2-152">이 순서를 지정하면 전달된 헤더 정보에 따라 달라지는 미들웨어는 처리하기 위해 헤더 값을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-152">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="e25f2-153">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 메서드를 호출한 후 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 또는 비슷한 인증 체계 미들웨어를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-153">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="e25f2-154">`X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-154">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="e25f2-155">미들웨어에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>가 지정되지 않은 경우 전달할 기본 헤더는 `None`입니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="e25f2-156">표준 localhost 주소(127.0.0.1)를 포함하여 루프백 주소(127.0.0.0/8, [::1])에서 실행 중인 프록시는 기본적으로 신뢰됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-156">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="e25f2-157">조직 내의 다른 신뢰할 수 있는 프록시 또는 네트워크가 인터넷과 웹 서버 간의 요청을 처리하는 경우 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 사용하여 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> 또는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> 목록에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-157">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="e25f2-158">다음 예제는 IP 주소 10.0.0.100의 신뢰할 수 있는 프록시 서버를 `Startup.ConfigureServices`의 전달된 헤더 미들웨어 `KnownProxies`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-158">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="e25f2-159">자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e25f2-159">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="e25f2-160">Nginx 설치</span><span class="sxs-lookup"><span data-stu-id="e25f2-160">Install Nginx</span></span>

<span data-ttu-id="e25f2-161">`apt-get`을 사용하여 Nginx를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-161">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="e25f2-162">설치 관리자는 시스템 시작 시 Nginx를 디먼으로 실행하는 *systemd* 시작 스크립트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-162">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="e25f2-163">Ubuntu의 설치 지침은 [Nginx: 공식 Debian/Ubuntu 패키지](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-163">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="e25f2-164">선택적 Nginx 모듈이 필요한 경우 소스에서 Nginx를 빌드해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-164">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="e25f2-165">Nginx가 처음 설치되었으므로 다음을 실행하여 명시적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-165">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="e25f2-166">브라우저에 Nginx에 대한 기본 방문 페이지가 표시되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-166">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="e25f2-167">방문 페이지는 `http://<server_IP_address>/index.nginx-debian.html`에 도달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-167">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="e25f2-168">Nginx 구성</span><span class="sxs-lookup"><span data-stu-id="e25f2-168">Configure Nginx</span></span>

<span data-ttu-id="e25f2-169">Nginx를 역방향 프록시로 구성하여 요청을 ASP.NET Core 앱에 프로그램에 전달하려면 */etc/nginx/sites-available/default*를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-169">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default*.</span></span> <span data-ttu-id="e25f2-170">텍스트 편집기에서 해당 항목을 열고 콘텐츠를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-170">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="e25f2-171">`server_name`이 일치하지 않으면 Nginx는 기본 서버를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-171">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="e25f2-172">기본 서버가 정의되지 않은 경우 구성 파일의 첫 번째 서버는 기본 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-172">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="e25f2-173">구성 파일에 있는 444 상태 코드를 반환하는 특정 기본 서버를 추가하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-173">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="e25f2-174">기본 서버 구성 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-174">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="e25f2-175">이전 구성 파일과 기본 서버를 사용하여 Nginx는 포트 80에서 호스트 헤더 `example.com` 또는 `*.example.com`가 포함된 공용 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-175">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="e25f2-176">이러한 호스트와 일치하지 않는 요청은 Kestrel로 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-176">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="e25f2-177">Nginx는 일치하는 요청을 `http://localhost:5000`의 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-177">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="e25f2-178">자세한 내용은 [How nginx processes a request](https://nginx.org/docs/http/request_processing.html)(nginx가 요청을 처리하는 방법)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e25f2-178">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="e25f2-179">Kestrel의 IP/포트를 변경하려면 [Kestrel: 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e25f2-179">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="e25f2-180">적절한 [server_name 지시문](https://nginx.org/docs/http/server_names.html)을 지정하지 않으면 앱이 보안 취약성에 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-180">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="e25f2-181">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.example.com`)에는 이러한 보안 위험이 발생하지 않습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="e25f2-181">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e25f2-182">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e25f2-182">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="e25f2-183">Nginx 구성이 설정되면 `sudo nginx -t`를 실행하여 구성 파일의 구문을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-183">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="e25f2-184">구성 파일 테스트에 성공하면 `sudo nginx -s reload`를 실행하여 Nginx가 변경 내용을 선택하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-184">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="e25f2-185">앱을 서버에서 직접 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="e25f2-185">To directly run the app on the server:</span></span>

1. <span data-ttu-id="e25f2-186">앱의 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-186">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="e25f2-187">`dotnet <app_assembly.dll>` 앱을 실행합니다. 여기서 `app_assembly.dll`은 앱의 어셈블리 파일 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-187">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="e25f2-188">앱이 서버에서 실행되지만 인터넷을 통해 응답하지 않는 경우 서버의 방화벽을 확인하고 포트 80이 열려 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-188">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="e25f2-189">Ubuntu Azure VM을 사용하는 경우 인바운드 포트 80 트래픽을 사용하는 NSG(네트워크 보안 그룹) 규칙을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-189">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="e25f2-190">인바운드 규칙을 사용할 때 아웃바운드 트래픽이 자동으로 부여되므로 아웃바운드 포트 80 규칙을 사용하도록 설정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-190">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="e25f2-191">앱 테스트를 완료한 후에 명령 프롬프트에서 `Ctrl+C`를 사용하여 앱을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-191">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="e25f2-192">앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="e25f2-192">Monitor the app</span></span>

<span data-ttu-id="e25f2-193">서버는 `http://<serveraddress>:80`에 대해 실행된 요청을 `http://127.0.0.1:5000`의 Kestrel에서 실행되는 ASP.NET Core 앱에 전달하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-193">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="e25f2-194">그러나 Nginx는 Kestrel 프로세스를 관리하도록 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-194">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="e25f2-195">*systemd*를 사용하여 기본 웹앱을 시작 및 모니터링하기 위한 서비스 파일을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-195">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="e25f2-196">*systemd*는 프로세스를 시작, 중지 및 관리하기 위한 다양하고 강력한 기능을 제공하는 init 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-196">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="e25f2-197">서비스 파일 만들기</span><span class="sxs-lookup"><span data-stu-id="e25f2-197">Create the service file</span></span>

<span data-ttu-id="e25f2-198">서비스 정의 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-198">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="e25f2-199">앱에 대한 예제 서비스 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-199">The following is an example service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="e25f2-200">사용자 *www-data*가 구성에서 사용되지 않을 경우 여기서 정의된 사용자를 먼저 만들고 파일에 대한 적절한 소유권을 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-200">If the user *www-data* isn't used by the configuration, the user defined here must be created first and given proper ownership for files.</span></span>

<span data-ttu-id="e25f2-201">`TimeoutStopSec`를 사용하여 초기 인터럽트 신호를 받은 후 앱이 종료되기를 기다리는 기간을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-201">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="e25f2-202">이 기간 내에 앱이 종료되지 않으면 앱을 종료하기 위해 SIGKILL이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-202">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="e25f2-203">단위 없는 초로 된 값(예: `150`) 또는 시간 범위 값(예: `2min 30s`)으로 값을 입력하거나, 시간 제한을 사용하지 않으려면 `infinity`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-203">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="e25f2-204">`TimeoutStopSec`의 기본값은 관리자 구성 파일(*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*)의 `DefaultTimeoutStopSec` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-204">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="e25f2-205">대부분의 배포에서 기본 시간 제한은 90초입니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-205">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="e25f2-206">Linux에는 대/소문자를 구분하는 파일 시스템이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-206">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="e25f2-207">ASPNETCORE_ENVIRONMENT를 “프로덕션”으로 설정하면 *appsettings.production.json* 대신 구성 파일 *appsettings.Production.json*을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-207">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json*, not *appsettings.production.json*.</span></span>

<span data-ttu-id="e25f2-208">일부 값(예: SQL 연결 문자열)은 환경 변수를 읽기 위해 구성 공급자에 대해 이스케이프되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-208">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="e25f2-209">다음 명령을 사용하여 구성 파일에서 사용할 제대로 이스케이프된 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-209">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="e25f2-210">콜론(`:`) 구분 기호는 환경 변수 이름에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-210">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="e25f2-211">콜론 대신 이중 밑줄(`__`)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-211">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="e25f2-212">[환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)는 환경 변수를 구성으로 읽을 때 이중 밑줄을 콜론으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-212">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="e25f2-213">다음 예제에서 연결 문자열 키 `ConnectionStrings:DefaultConnection`은 서비스 정의 파일에 `ConnectionStrings__DefaultConnection`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-213">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="e25f2-214">파일을 저장하고 서비스를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-214">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="e25f2-215">서비스를 시작하고 실행 중인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-215">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="e25f2-216">역방향 프록시를 구성하고 systemd를 통해 Kestrel을 관리하면 웹앱이 완전히 구성되고 로컬 컴퓨터(`http://localhost`)의 브라우저에서 웹앱에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-216">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="e25f2-217">차단 중인 방화벽이 없다면 원격 컴퓨터에서 액세스할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-217">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="e25f2-218">응답 헤더를 검사하는 `Server` 헤더는 Kestrel에서 지원하는 ASP.NET Core 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-218">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="e25f2-219">로그 보기</span><span class="sxs-lookup"><span data-stu-id="e25f2-219">View logs</span></span>

<span data-ttu-id="e25f2-220">Kestrel을 사용하는 웹앱은 `systemd`를 사용하여 관리되므로 모든 이벤트 및 프로세스가 중앙형 저널에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-220">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="e25f2-221">그러나 이 저널에는 `systemd`를 통해 관리하는 모든 서비스 및 프로세스에 대한 모든 항목이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-221">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="e25f2-222">`kestrel-helloapp.service` 관련 항목을 보려면 다음 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-222">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="e25f2-223">추가 필터링을 위해 `--since today`, `--until 1 hour ago` 같은 시간 옵션이나 이러한 옵션의 조합을 사용하여 반환되는 항목 수를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-223">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="e25f2-224">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="e25f2-224">Data protection</span></span>

<span data-ttu-id="e25f2-225">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증 미들웨어(예: 쿠키 미들웨어) 및 CSRF(교차 사이트 요청 위조) 보호를 비롯한 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-225">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="e25f2-226">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-226">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="e25f2-227">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-227">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="e25f2-228">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-228">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="e25f2-229">모든 쿠키 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-229">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="e25f2-230">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-230">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="e25f2-231">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-231">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="e25f2-232">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData 쿠키](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-232">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="e25f2-233">키 링을 유지하고 암호화하도록 데이터 보호를 구성하려면 다음을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="e25f2-233">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="e25f2-234">긴 요청 헤더 필드</span><span class="sxs-lookup"><span data-stu-id="e25f2-234">Long request header fields</span></span>

<span data-ttu-id="e25f2-235">앱이 프록시 서버의 기본 설정(일반적으로 플랫폼에 따라 4K 또는 8K)에서 허용하는 것보다 긴 헤더 필드를 요청해야 하는 경우, 다음 지시문을 조정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-235">If the app requires request header fields longer than permitted by the proxy server's default settings (typically 4K or 8K depending on the platform), the following directives require adjustment.</span></span> <span data-ttu-id="e25f2-236">적용할 값은 시나리오에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-236">The values to apply are scenario-dependent.</span></span> <span data-ttu-id="e25f2-237">자세한 내용은 서버의 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e25f2-237">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="e25f2-238">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="e25f2-238">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="e25f2-239">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="e25f2-239">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="e25f2-240">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="e25f2-240">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="e25f2-241">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="e25f2-241">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="e25f2-242">필요한 경우가 아니면 프록시 버퍼의 기본값을 늘리지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e25f2-242">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="e25f2-243">이러한 값을 늘리면 악의적인 사용자의 버퍼 오버런(오버플로) 및 DoS(서비스 거부) 공격의 위험이 증가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-243">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="e25f2-244">앱 보안 유지</span><span class="sxs-lookup"><span data-stu-id="e25f2-244">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="e25f2-245">AppArmor 사용</span><span class="sxs-lookup"><span data-stu-id="e25f2-245">Enable AppArmor</span></span>

<span data-ttu-id="e25f2-246">LSM(Linux Security Modules)은 Linux 2.6 이후 Linux 커널에 포함된 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-246">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="e25f2-247">LSM은 보안 모듈의 다양한 구현을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-247">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="e25f2-248">[AppArmor](https://wiki.ubuntu.com/AppArmor)는 프로그램을 제한된 리소스 집합으로 한정할 수 있는 필수 Access Control 시스템을 구현하는 LSM입니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-248">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="e25f2-249">AppArmor가 사용하도록 설정되고 제대로 구성되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-249">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="e25f2-250">방화벽 구성</span><span class="sxs-lookup"><span data-stu-id="e25f2-250">Configure the firewall</span></span>

<span data-ttu-id="e25f2-251">사용되지 않는 모든 외부 포트를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-251">Close off all external ports that are not in use.</span></span> <span data-ttu-id="e25f2-252">복잡하지 않은 방화벽(ufw)은 방화벽을 구성하기 위한 명령줄 인터페이스를 제공하여 `iptables`에 대한 프런트 엔드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-252">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a command line interface for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="e25f2-253">방화벽이 올바르게 구성되지 않으면 전체 시스템에 대한 액세스가 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-253">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="e25f2-254">올바른 SSH 포트를 지정하지 못하면 SSH를 사용하여 시스템에 연결하는 경우 실직적으로 시스템에 액세스할 수 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-254">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="e25f2-255">기본 포트는 22입니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-255">The default port is 22.</span></span> <span data-ttu-id="e25f2-256">자세한 내용은 [ufw 소개](https://help.ubuntu.com/community/UFW) 및 [매뉴얼](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e25f2-256">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="e25f2-257">`ufw`를 설치하고 필요한 모든 포트에서 트래픽을 허용하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-257">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="e25f2-258">Nginx 보안 유지</span><span class="sxs-lookup"><span data-stu-id="e25f2-258">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="e25f2-259">Nginx 응답 이름 변경</span><span class="sxs-lookup"><span data-stu-id="e25f2-259">Change the Nginx response name</span></span>

<span data-ttu-id="e25f2-260">*src/http/ngx_http_header_filter_module.c*를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-260">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="e25f2-261">옵션 구성</span><span class="sxs-lookup"><span data-stu-id="e25f2-261">Configure options</span></span>

<span data-ttu-id="e25f2-262">추가 필수 모듈을 사용하여 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-262">Configure the server with additional required modules.</span></span> <span data-ttu-id="e25f2-263">[ModSecurity](https://www.modsecurity.org/)와 같은 웹앱 방화벽을 사용하여 앱을 강화해 보세요.</span><span class="sxs-lookup"><span data-stu-id="e25f2-263">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="e25f2-264">HTTPS 구성</span><span class="sxs-lookup"><span data-stu-id="e25f2-264">HTTPS configuration</span></span>

<span data-ttu-id="e25f2-265">**보안(HTTPS) 로컬 연결을 위해 앱 구성**</span><span class="sxs-lookup"><span data-stu-id="e25f2-265">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="e25f2-266">[dotnet 실행](/dotnet/core/tools/dotnet-run) 명령은 `applicationUrl` 속성(예: `https://localhost:5001;http://localhost:5000`)이 제공하는 URL에서 수신 대기하도록 앱을 구성하는 앱의 *Properties/launchSettings.json* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-266">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="e25f2-267">다음 방법 중 하나를 사용하여 `dotnet run` 명령 또는 개발 환경(Visual Studio Code의 F5 또는 Ctrl+F5)에 대해 개발 중인 인증서를 사용하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-267">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="e25f2-268">[구성에서 기본 인증서를 바꿈](xref:fundamentals/servers/kestrel#configuration)(*권장*)</span><span class="sxs-lookup"><span data-stu-id="e25f2-268">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="e25f2-269">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="e25f2-269">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="e25f2-270">**보안 (HTTPS) 클라이언트 연결을 위해 역방향 프록시 구성**</span><span class="sxs-lookup"><span data-stu-id="e25f2-270">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="e25f2-271">신뢰할 수 있는 CA(인증 기관)에서 발급된 유효한 인증서를 지정하여 포트 `443`에서 HTTPS 트래픽을 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-271">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="e25f2-272">다음 */etc/nginx/nginx.conf* 파일에 설명된 일부 사례를 채택하여 보안을 강화합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-272">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="e25f2-273">예를 들어 더 강력한 암호화를 선택하고 HTTP를 사용한 모든 트래픽을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-273">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="e25f2-274">HSTS(`HTTP Strict-Transport-Security`) 헤더를 추가하면 클라이언트에서 만든 모든 후속 요청이 HTTPS를 통해 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-274">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="e25f2-275">나중에 HTTPS를 사용하지 않도록 설정할 경우 HSTS 헤더를 추가하지 않거나 적절한 `max-age`를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-275">Don't add the HSTS header or chose an appropriate `max-age` if HTTPS will be disabled in the future.</span></span>

<span data-ttu-id="e25f2-276">*/etc/nginx/proxy.conf* 구성 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-276">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="e25f2-277">*/etc/nginx/nginx.conf* 구성 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-277">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="e25f2-278">예제에서는 `http` 및 `server` 섹션이 하나의 구성 파일에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-278">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="e25f2-279">클릭재킹(clickjacking)으로부터 Nginx 보호</span><span class="sxs-lookup"><span data-stu-id="e25f2-279">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="e25f2-280">또한 ‘UI 교정 공격’이라고도 하는[클릭재킹(Clickjacking)](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)은 웹 사이트 방문자를 속여서 현재 방문 중인 것과 다른 페이지에서 링크 또는 단추를 클릭하게 하는 악의적인 공격입니다. </span><span class="sxs-lookup"><span data-stu-id="e25f2-280">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="e25f2-281">`X-FRAME-OPTIONS`를 사용하여 사이트를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-281">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="e25f2-282">클릭재킹 공격을 완화하려면:</span><span class="sxs-lookup"><span data-stu-id="e25f2-282">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="e25f2-283">*nginx.conf* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-283">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="e25f2-284">`add_header X-Frame-Options "SAMEORIGIN";` 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-284">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="e25f2-285">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-285">Save the file.</span></span>
1. <span data-ttu-id="e25f2-286">Nginx를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-286">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="e25f2-287">MIME 형식 검색</span><span class="sxs-lookup"><span data-stu-id="e25f2-287">MIME-type sniffing</span></span>

<span data-ttu-id="e25f2-288">이 헤더는 응답 콘텐츠 형식을 재정의하지 않도록 브라우저에 지시하므로 대부분의 브라우저에서 선언된 콘텐츠 형식이 아닌 응답에 대한 MIME 검색을 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-288">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="e25f2-289">`nosniff` 옵션을 사용하면 서버에 콘텐츠가 “text/html”이라고 표시될 경우 브라우저는 콘텐츠를 “text/html”으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-289">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="e25f2-290">*nginx.conf* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-290">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="e25f2-291">줄 `add_header X-Content-Type-Options "nosniff";`를 추가하고 파일을 저장한 다음 Nginx를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e25f2-291">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e25f2-292">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e25f2-292">Additional resources</span></span>

* [<span data-ttu-id="e25f2-293">Linux에서 .NET Core의 필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="e25f2-293">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="e25f2-294">Nginx: 이진 릴리스: 공식 Debian/Ubuntu 패키지</span><span class="sxs-lookup"><span data-stu-id="e25f2-294">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="e25f2-295">NGINX: 전달된 헤더 사용</span><span class="sxs-lookup"><span data-stu-id="e25f2-295">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
