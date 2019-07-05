---
title: Apache를 사용하여 Linux에서 ASP.NET Core 호스트
author: guardrex
description: CentOS에서 Apache를 역방향 프록시 서버로 설정하여 Kestrel에서 실행되는 ASP.NET Core 웹앱에 HTTP 트래픽을 리디렉션하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 03/31/2019
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 0b523627f8236d79dfc61f0693309d6f4d715e7e
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538704"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="9e9a1-103">Apache를 사용하여 Linux에서 ASP.NET Core 호스트</span><span class="sxs-lookup"><span data-stu-id="9e9a1-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="9e9a1-104">작성자: [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="9e9a1-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="9e9a1-105">이 가이드를 사용하여 [CentOS 7](https://www.centos.org/)에서 [Apache](https://httpd.apache.org/)를 역방향 프록시 서버로 설정하여 [Kestrel](xref:fundamentals/servers/kestrel) 서버에서 실행되는 ASP.NET Core 웹앱에 HTTP 트래픽을 리디렉션하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="9e9a1-106">[mod_proxy 확장](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) 및 관련 모듈은 서버의 역방향 프록시를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-106">The [mod_proxy extension](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9e9a1-107">전제 조건</span><span class="sxs-lookup"><span data-stu-id="9e9a1-107">Prerequisites</span></span>

* <span data-ttu-id="9e9a1-108">sudo 권한을 가진 표준 사용자 계정으로 CentOS 7을 실행하는 서버가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="9e9a1-109">서버에서 .NET Core 런타임을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="9e9a1-110">[.NET Core 모든 다운로드 페이지](https://www.microsoft.com/net/download/all)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-110">Visit the [.NET Core All Downloads page](https://www.microsoft.com/net/download/all).</span></span>
   1. <span data-ttu-id="9e9a1-111">**런타임** 아래의 목록에서 최신 미리 보기 상태가 아닌 런타임을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-111">Select the latest non-preview runtime from the list under **Runtime**.</span></span>
   1. <span data-ttu-id="9e9a1-112">CentOS/Oracle에 대한 지침을 선택하고 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-112">Select and follow the instructions for CentOS/Oracle.</span></span>
* <span data-ttu-id="9e9a1-113">기존 ASP.NET Core 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-113">An existing ASP.NET Core app.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="9e9a1-114">앱 게시 및 복사</span><span class="sxs-lookup"><span data-stu-id="9e9a1-114">Publish and copy over the app</span></span>

<span data-ttu-id="9e9a1-115">[프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-115">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="9e9a1-116">앱을 로컬에서 실행하고 보안 연결(HTTPS)을 확인하도록 구성하지 않은 경우 다음 방법 중 하나를 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-116">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="9e9a1-117">보안 로컬 연결을 처리하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-117">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="9e9a1-118">자세한 내용은 [HTTPS 구성](#https-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-118">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="9e9a1-119">*Properties/launchSettings.json* 파일의 `applicationUrl` 속성에서 `https://localhost:5001`(있는 경우)을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-119">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="9e9a1-120">서버에서 실행할 수 있는 디렉터리(예: *bin/Release/&lt;target_framework_moniker&gt;/publish*)로 앱을 패키징하기 위해 개발 환경에서 [dotnet publish](/dotnet/core/tools/dotnet-publish)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-120">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```console
dotnet publish --configuration Release
```

<span data-ttu-id="9e9a1-121">.NET Core 런타임을 서버에서 유지 관리하지 않으려는 경우 앱은 [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)로 게시될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-121">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="9e9a1-122">조직의 워크플로에 통합된 도구(예: SCP, SFTP)를 사용하여 ASP.NET Core 앱을 서버에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-122">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="9e9a1-123">*var* 디렉터리(예: *var/www/helloapp*)에서 웹앱을 찾는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-123">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="9e9a1-124">프로덕션 배포 시나리오에서 지속적인 통합 워크플로는 앱을 게시하고 자산을 서버로 복사하는 워크플로를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-124">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="9e9a1-125">프록시 서버 구성</span><span class="sxs-lookup"><span data-stu-id="9e9a1-125">Configure a proxy server</span></span>

<span data-ttu-id="9e9a1-126">역방향 프록시는 동적 웹앱을 지원하기 위한 일반적인 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-126">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="9e9a1-127">역방향 프록시는 HTTP 요청을 종료하고 이 요청을 ASP.NET 앱에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-127">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="9e9a1-128">프록시 서버는 클라이언트 요청을 자체 수행하는 대신 다른 서버에 전달하는 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-128">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="9e9a1-129">역방향 프록시는 일반적으로 임의의 클라이언트 대신 고정 대상에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-129">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="9e9a1-130">이 가이드에서 Apache는 Kestrel이 ASP.NET Core 앱을 제공하는 동일한 서버에서 실행되는 역방향 프록시로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-130">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="9e9a1-131">요청이 역방향 프록시를 통해 전달되므로 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 패키지의 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-131">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="9e9a1-132">이 미들웨어는 `X-Forwarded-Proto` 헤더를 사용하여 `Request.Scheme`을 업데이트하므로 리디렉션 URI 및 기타 보안 정책이 제대로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-132">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="9e9a1-133">전달된 헤더 미들웨어를 호출한 후에 인증, 링크 생성, 리디렉션 및 지리적 위치 등 체계에 따라 달라지는 구성 요소를 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-133">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="9e9a1-134">일반 규칙으로 전달된 헤더 미들웨어는 진단 및 오류 처리 미들웨어를 제외한 다른 미들웨어 전에 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-134">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="9e9a1-135">이 순서를 지정하면 전달된 헤더 정보에 따라 달라지는 미들웨어는 처리하기 위해 헤더 값을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-135">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="9e9a1-136">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 메서드를 호출한 후 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 또는 비슷한 인증 체계 미들웨어를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-136">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="9e9a1-137">`X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-137">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="9e9a1-138">미들웨어에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>가 지정되지 않은 경우 전달할 기본 헤더는 `None`입니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-138">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="9e9a1-139">표준 localhost 주소(127.0.0.1)를 포함하여 루프백 주소(127.0.0.0/8, [::1])에서 실행 중인 프록시는 기본적으로 신뢰됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-139">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="9e9a1-140">조직 내의 다른 신뢰할 수 있는 프록시 또는 네트워크가 인터넷과 웹 서버 간의 요청을 처리하는 경우 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 사용하여 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> 또는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> 목록에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-140">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="9e9a1-141">다음 예제는 IP 주소 10.0.0.100의 신뢰할 수 있는 프록시 서버를 `Startup.ConfigureServices`의 전달된 헤더 미들웨어 `KnownProxies`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-141">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="9e9a1-142">자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-142">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="9e9a1-143">Apache 설치</span><span class="sxs-lookup"><span data-stu-id="9e9a1-143">Install Apache</span></span>

<span data-ttu-id="9e9a1-144">CentOS 패키지를 안정적인 최신 버전으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-144">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="9e9a1-145">단일 `yum` 명령을 사용하여 CentOS에 Apache 웹 서버를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-145">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="9e9a1-146">명령을 실행한 후 샘플 출력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-146">Sample output after running the command:</span></span>

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> <span data-ttu-id="9e9a1-147">이 예제에서 CentOS 7 버전은 64비트이므로 출력에는 httpd.86_64가 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-147">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="9e9a1-148">Apache를 설치한 위치를 확인하려면 명령 프롬프트에서 `whereis httpd`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-148">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="9e9a1-149">Apache 구성</span><span class="sxs-lookup"><span data-stu-id="9e9a1-149">Configure Apache</span></span>

<span data-ttu-id="9e9a1-150">Apache의 구성 파일은 `/etc/httpd/conf.d/` 디렉터리 내에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-150">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="9e9a1-151">`/etc/httpd/conf.modules.d/`의 모듈 구성 파일 외에도 *.conf* 확장을 포함한 모든 파일은 알파벳순으로 처리됩니다. 여기에는 모듈을 로드하는 데 필요한 구성 파일도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-151">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="9e9a1-152">앱에 대해 *helloapp.conf*라는 구성 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-152">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

<span data-ttu-id="9e9a1-153">`VirtualHost` 블록은 서버에 있는 하나 이상의 파일에 여러 번 나타날 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-153">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="9e9a1-154">이전 구성 파일에서 Apache는 포트 80에서 공용 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-154">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="9e9a1-155">도메인 `www.example.com`을 제공하고 있고 `*.example.com` 별칭이 동일한 웹 사이트로 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-155">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="9e9a1-156">자세한 내용은 [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html)(이름 기반 가상 호스트 지원)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-156">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="9e9a1-157">요청은 127.0.0.1에 있는 서버의 포트 5000에 대한 루트에서 프록시 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-157">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="9e9a1-158">양방향 통신의 경우 `ProxyPass` 및 `ProxyPassReverse`가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-158">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="9e9a1-159">Kestrel의 IP/포트를 변경하려면 [Kestrel: 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-159">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="9e9a1-160">**VirtualHost** 블록에서 적절한 [ServerName 지시문](https://httpd.apache.org/docs/current/mod/core.html#servername)을 지정하지 않으면 앱이 보안 취약성에 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-160">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="9e9a1-161">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.example.com`)에는 이러한 보안 위험이 발생하지 않습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="9e9a1-161">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="9e9a1-162">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-162">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="9e9a1-163">로깅은 `ErrorLog` 및 `CustomLog` 지시문을 사용하여 `VirtualHost`별로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-163">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="9e9a1-164">`ErrorLog`는 서버가 오류를 기록하는 위치이고 `CustomLog`는 로그 파일의 파일 이름과 형식을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-164">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="9e9a1-165">이 경우에는 요청 정보가 기록되는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-165">In this case, this is where request information is logged.</span></span> <span data-ttu-id="9e9a1-166">각 요청이 한 줄에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-166">There's one line for each request.</span></span>

<span data-ttu-id="9e9a1-167">파일을 저장하고 구성을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-167">Save the file and test the configuration.</span></span> <span data-ttu-id="9e9a1-168">모든 항목이 통과하는 경우 응답은 `Syntax [OK]`이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-168">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="9e9a1-169">Apache를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-169">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="9e9a1-170">앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="9e9a1-170">Monitor the app</span></span>

<span data-ttu-id="9e9a1-171">이제 Apache는 `http://localhost:80`에 대해 실행된 요청을 `http://127.0.0.1:5000`의 Kestrel에서 실행되는 ASP.NET Core 앱에 전달하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-171">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="9e9a1-172">그러나 Apache는 Kestrel 프로세스를 관리하도록 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-172">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="9e9a1-173">*systemd*를 사용하고 서비스 파일을 만들어 기본 웹앱을 시작하고 모니터링합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-173">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="9e9a1-174">*systemd*는 프로세스를 시작, 중지 및 관리하기 위한 다양하고 강력한 기능을 제공하는 init 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-174">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="9e9a1-175">서비스 파일 만들기</span><span class="sxs-lookup"><span data-stu-id="9e9a1-175">Create the service file</span></span>

<span data-ttu-id="9e9a1-176">서비스 정의 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-176">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="9e9a1-177">앱의 예제 서비스 파일:</span><span class="sxs-lookup"><span data-stu-id="9e9a1-177">An example service file for the app:</span></span>

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="9e9a1-178">사용자 *apache*가 구성에서 사용되지 않을 경우 사용자를 먼저 만들고 적절한 파일 소유권을 부여해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-178">If the user *apache* isn't used by the configuration, the user must be created first and given proper ownership of files.</span></span>

<span data-ttu-id="9e9a1-179">`TimeoutStopSec`를 사용하여 초기 인터럽트 신호를 받은 후 앱이 종료되기를 기다리는 기간을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-179">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="9e9a1-180">이 기간 내에 앱이 종료되지 않으면 앱을 종료하기 위해 SIGKILL이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-180">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="9e9a1-181">단위 없는 초로 된 값(예: `150`) 또는 시간 범위 값(예: `2min 30s`)으로 값을 입력하거나, 시간 제한을 사용하지 않으려면 `infinity`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-181">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="9e9a1-182">`TimeoutStopSec`의 기본값은 관리자 구성 파일(*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*)의 `DefaultTimeoutStopSec` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-182">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="9e9a1-183">대부분의 배포에서 기본 시간 제한은 90초입니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-183">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="9e9a1-184">일부 값(예: SQL 연결 문자열)은 환경 변수를 읽기 위해 구성 공급자에 대해 이스케이프되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-184">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="9e9a1-185">다음 명령을 사용하여 구성 파일에서 사용할 제대로 이스케이프된 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-185">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="9e9a1-186">콜론(`:`) 구분 기호는 환경 변수 이름에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-186">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="9e9a1-187">콜론 대신 이중 밑줄(`__`)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-187">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="9e9a1-188">[환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)는 환경 변수를 구성으로 읽을 때 이중 밑줄을 콜론으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-188">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="9e9a1-189">다음 예제에서 연결 문자열 키 `ConnectionStrings:DefaultConnection`은 서비스 정의 파일에 `ConnectionStrings__DefaultConnection`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-189">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="9e9a1-190">파일을 저장하고 서비스를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-190">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="9e9a1-191">서비스를 시작하고 실행 중인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-191">Start the service and verify that it's running:</span></span>

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="9e9a1-192">역방향 프록시를 구성하고 *systemd*를 통해 Kestrel을 관리하면 웹앱이 완전히 구성되고 로컬 컴퓨터(`http://localhost`)의 브라우저에서 웹앱에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-192">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="9e9a1-193">응답 헤더를 검사하는 **Server** 헤더는 ASP.NET Core 앱이 Kestrel에서 제공됨을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-193">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="9e9a1-194">로그 보기</span><span class="sxs-lookup"><span data-stu-id="9e9a1-194">View logs</span></span>

<span data-ttu-id="9e9a1-195">Kestrel을 사용하는 웹앱은 *systemd*를 사용하여 관리되므로 이벤트 및 프로세스가 중앙형 저널에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-195">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="9e9a1-196">그러나 이 저널에는 *systemd*에서 관리하는 모든 서비스 및 프로세스에 대한 항목이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-196">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="9e9a1-197">`kestrel-helloapp.service` 관련 항목을 보려면 다음 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-197">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="9e9a1-198">시간 필터링의 경우 명령을 사용하여 시간 옵션을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-198">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="9e9a1-199">예를 들어 `--since today`를 사용하여 현재 날짜를 기준으로 필터링하거나 `--until 1 hour ago`를 사용하여 이전 시간의 항목을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-199">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="9e9a1-200">자세한 내용은 [journalctl에 대한 기본 페이지](https://www.unix.com/man-page/centos/1/journalctl/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-200">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="9e9a1-201">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="9e9a1-201">Data protection</span></span>

<span data-ttu-id="9e9a1-202">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증 미들웨어(예: 쿠키 미들웨어) 및 CSRF(교차 사이트 요청 위조) 보호를 비롯한 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-202">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="9e9a1-203">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-203">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="9e9a1-204">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-204">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="9e9a1-205">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-205">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="9e9a1-206">모든 쿠키 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-206">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="9e9a1-207">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-207">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="9e9a1-208">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-208">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="9e9a1-209">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData 쿠키](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-209">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="9e9a1-210">키 링을 유지하고 암호화하도록 데이터 보호를 구성하려면 다음을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-210">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="9e9a1-211">앱 보안 유지</span><span class="sxs-lookup"><span data-stu-id="9e9a1-211">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="9e9a1-212">방화벽 구성</span><span class="sxs-lookup"><span data-stu-id="9e9a1-212">Configure firewall</span></span>

<span data-ttu-id="9e9a1-213">*Firewalld*는 네트워크 영역에 대한 지원을 통해 방화벽을 관리하는 동적 디먼입니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-213">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="9e9a1-214">포트 및 패킷 필터링은 iptables로 계속 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-214">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="9e9a1-215">*Firewalld*는 기본적으로 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-215">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="9e9a1-216">`yum`을 사용하여 패키지를 설치하거나 설치되었는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-216">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="9e9a1-217">`firewalld`를 사용하여 앱에 필요한 포트만 엽니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-217">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="9e9a1-218">이 경우에는 포트 80 및 443을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-218">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="9e9a1-219">다음 명령은 포트 80 및 443이 영구적으로 열리도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-219">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="9e9a1-220">방화벽 설정을 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-220">Reload the firewall settings.</span></span> <span data-ttu-id="9e9a1-221">기본 영역의 사용 가능한 서비스 및 포트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-221">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="9e9a1-222">`firewall-cmd -h`를 검사하여 옵션을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-222">Options are available by inspecting `firewall-cmd -h`.</span></span>

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a><span data-ttu-id="9e9a1-223">HTTPS 구성</span><span class="sxs-lookup"><span data-stu-id="9e9a1-223">HTTPS configuration</span></span>

<span data-ttu-id="9e9a1-224">**보안(HTTPS) 로컬 연결을 위해 앱 구성**</span><span class="sxs-lookup"><span data-stu-id="9e9a1-224">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="9e9a1-225">[dotnet 실행](/dotnet/core/tools/dotnet-run) 명령은 `applicationUrl` 속성(예: `https://localhost:5001; http://localhost:5000`)이 제공하는 URL에서 수신 대기하도록 앱을 구성하는 앱의 *Properties/launchSettings.json* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-225">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="9e9a1-226">다음 방법 중 하나를 사용하여 `dotnet run` 명령 또는 개발 환경(Visual Studio Code의 F5 또는 Ctrl+F5)에 대해 개발 중인 인증서를 사용하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-226">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="9e9a1-227">[구성에서 기본 인증서를 바꿈](xref:fundamentals/servers/kestrel#configuration)(*권장*)</span><span class="sxs-lookup"><span data-stu-id="9e9a1-227">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="9e9a1-228">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="9e9a1-228">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="9e9a1-229">**보안 (HTTPS) 클라이언트 연결을 위해 역방향 프록시 구성**</span><span class="sxs-lookup"><span data-stu-id="9e9a1-229">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="9e9a1-230">HTTPS에 Apache를 구성하려면 *mod_ssl* 모듈을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-230">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="9e9a1-231">*httpd* 모듈이 설치될 때 *mod_ssl* 모듈도 설치되었습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-231">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="9e9a1-232">설치되지 않은 경우 `yum`을 사용하여 구성에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-232">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="9e9a1-233">HTTPS를 적용하려면 URL 재작성을 사용할 수 있도록 `mod_rewrite` 모듈을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-233">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="9e9a1-234">포트 443에서 URL 재작성 및 보안 통신을 사용할 수 있도록 *helloapp.conf* 파일을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-234">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> <span data-ttu-id="9e9a1-235">이 예제에서는 로컬로 생성된 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-235">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="9e9a1-236">**SSLCertificateFile**은 도메인 이름에 대한 기본 인증서 파일이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-236">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="9e9a1-237">**SSLCertificateKeyFile**은 CSR을 만들 때 생성된 키 파일이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-237">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="9e9a1-238">**SSLCertificateChainFile**은 인증 기관에서 제공된 중간 인증서 파일(있는 경우)이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-238">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="9e9a1-239">파일을 저장하고 구성을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-239">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="9e9a1-240">Apache를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-240">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="9e9a1-241">추가 Apache 제안</span><span class="sxs-lookup"><span data-stu-id="9e9a1-241">Additional Apache suggestions</span></span>

### <a name="additional-headers"></a><span data-ttu-id="9e9a1-242">추가 헤더</span><span class="sxs-lookup"><span data-stu-id="9e9a1-242">Additional headers</span></span>

<span data-ttu-id="9e9a1-243">악의적인 공격으로부터 보호하기 위해 몇 가지 헤더를 수정하거나 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-243">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="9e9a1-244">`mod_headers` 모듈이 설치되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-244">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="9e9a1-245">클릭재킹(clickjacking) 공격으로부터 Apache 보호</span><span class="sxs-lookup"><span data-stu-id="9e9a1-245">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="9e9a1-246">또한 ‘UI 교정 공격’이라고도 하는[클릭재킹(Clickjacking)](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)은 웹 사이트 방문자를 속여서 현재 방문 중인 것과 다른 페이지에서 링크 또는 단추를 클릭하게 하는 악의적인 공격입니다. </span><span class="sxs-lookup"><span data-stu-id="9e9a1-246">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="9e9a1-247">`X-FRAME-OPTIONS`를 사용하여 사이트를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-247">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="9e9a1-248">클릭재킹 공격을 완화하려면:</span><span class="sxs-lookup"><span data-stu-id="9e9a1-248">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="9e9a1-249">*httpd.conf* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-249">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="9e9a1-250">`Header append X-FRAME-OPTIONS "SAMEORIGIN"` 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-250">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="9e9a1-251">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-251">Save the file.</span></span>
1. <span data-ttu-id="9e9a1-252">Apache를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-252">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="9e9a1-253">MIME 형식 검색</span><span class="sxs-lookup"><span data-stu-id="9e9a1-253">MIME-type sniffing</span></span>

<span data-ttu-id="9e9a1-254">`X-Content-Type-Options` 헤더는 Internet Explorer에서 ‘MIME 스니핑’을 방지합니다(파일 콘텐츠에서 파일의 `Content-Type` 확인). </span><span class="sxs-lookup"><span data-stu-id="9e9a1-254">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="9e9a1-255">서버에서 `nosniff` 옵션 집합을 사용하여 `Content-Type` 헤더를 `text/html`로 설정하는 경우 Internet Explorer는 파일 콘텐츠에 관계없이 콘텐츠를 `text/html`로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-255">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="9e9a1-256">*httpd.conf* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-256">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="9e9a1-257">`Header set X-Content-Type-Options "nosniff"` 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-257">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="9e9a1-258">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-258">Save the file.</span></span> <span data-ttu-id="9e9a1-259">Apache를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-259">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="9e9a1-260">부하 분산</span><span class="sxs-lookup"><span data-stu-id="9e9a1-260">Load Balancing</span></span>

<span data-ttu-id="9e9a1-261">이 예제에서는 동일한 인스턴스 컴퓨터에서 CentOS 7와 Kestrel의 Apache를 설정하고 구성하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-261">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="9e9a1-262">단일 실패 지점이 없도록 하기 위해 *mod_proxy_balancer*를 사용하고 **VirtualHost**를 수정하면 Apache 프록시 서버 뒤에 있는 웹앱의 여러 인스턴스를 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-262">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="9e9a1-263">아래 표시된 구성 파일에서 `helloapp`의 추가 인스턴스는 포트 5001에서 실행되도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-263">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="9e9a1-264">*Proxy* 섹션은 *byrequests*의 부하를 분산하는 두 개의 멤버가 있는 분산 장치 구성을 사용하여 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-264">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="9e9a1-265">속도 제한</span><span class="sxs-lookup"><span data-stu-id="9e9a1-265">Rate Limits</span></span>

<span data-ttu-id="9e9a1-266">*httpd* 모듈에 포함된 *mod_ratelimit*을 사용하여 클라이언트의 대역폭을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-266">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="9e9a1-267">예제 파일에서는 루트 위치 아래에서 대역폭을 600KB/초로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-267">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="9e9a1-268">긴 요청 헤더 필드</span><span class="sxs-lookup"><span data-stu-id="9e9a1-268">Long request header fields</span></span>

<span data-ttu-id="9e9a1-269">앱이 프록시 서버의 기본 설정(일반적으로 8,190바이트)에서 허용하는 것보다 긴 요청 헤더 필드가 필요한 경우 [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) 지시문의 값을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-269">If the app requires request header fields longer than permitted by the proxy server's default setting (typically 8,190 bytes), adjust the value of the [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive.</span></span> <span data-ttu-id="9e9a1-270">적용할 값은 시나리오에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-270">The value to apply is scenario-dependent.</span></span> <span data-ttu-id="9e9a1-271">자세한 내용은 서버의 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-271">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="9e9a1-272">필요한 경우가 아니면 `LimitRequestFieldSize`의 기본값을 늘리지 마세요.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-272">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="9e9a1-273">값을 늘리면 악의적인 사용자의 버퍼 오버런(오버플로) 및 DoS(서비스 거부) 공격의 위험이 증가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e9a1-273">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e9a1-274">추가 자료</span><span class="sxs-lookup"><span data-stu-id="9e9a1-274">Additional resources</span></span>

* [<span data-ttu-id="9e9a1-275">Linux에서 .NET Core의 필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9e9a1-275">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
