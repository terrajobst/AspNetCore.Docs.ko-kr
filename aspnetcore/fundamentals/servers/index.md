---
title: ASP.NET Core의 웹 서버 구현
author: guardrex
description: ASP.NET Core의 웹 서버 Kestrel 및 HTTP.sys를 검색합니다. 서버를 선택하는 방법 및 역방향 프록시 서버를 사용하는 시기에 대해 알아봅니다.
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/24/2019
uid: fundamentals/servers/index
ms.openlocfilehash: 82a4bd0173b0aab094ac5ac9f89d5358ba585d3d
ms.sourcegitcommit: b8ed594ab9f47fa32510574f3e1b210cff000967
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66251346"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="7b295-104">ASP.NET Core의 웹 서버 구현</span><span class="sxs-lookup"><span data-stu-id="7b295-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="7b295-105">작성자: [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) 및 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="7b295-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="7b295-106">ASP.NET Core 앱은 In-process HTTP 서버 구현을 사용하여 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="7b295-107">서버 구현은 HTTP 요청을 수신하고 <xref:Microsoft.AspNetCore.Http.HttpContext>에 구성된 일련의 [요청 기능](xref:fundamentals/request-features)으로 앱에 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="7b295-108">Windows</span><span class="sxs-lookup"><span data-stu-id="7b295-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="7b295-109">ASP.NET Core는 다음과 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="7b295-110">[Kestrel 서버](xref:fundamentals/servers/kestrel)는 플랫폼 간 기본 HTTP 서버를 구현한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="7b295-111">IIS HTTP 서버는 IIS의 [In-process 서버](#in-process-hosting-model)입니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-111">IIS HTTP Server is an [in-process server](#in-process-hosting-model) for IIS.</span></span>
* <span data-ttu-id="7b295-112">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)는 [Http.Sys 커널 드라이버 및 HTTP Server API](/windows/desktop/Http/http-api-start-page)를 기반으로 하는 Windows 전용 HTTP 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="7b295-113">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 사용하는 경우 앱은 다음 중 하나를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-113">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="7b295-114">[IIS HTTP 서버](#iis-http-server)를 사용하는 IIS 작업자 프로세스([In-process 호스팅 모델](#in-process-hosting-model))와 동일한 프로세스에서</span><span class="sxs-lookup"><span data-stu-id="7b295-114">In the same process as the IIS worker process (the [in-process hosting model](#in-process-hosting-model)) with the [IIS HTTP Server](#iis-http-server).</span></span> <span data-ttu-id="7b295-115">권장되는 구성은 *In process*입니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-115">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="7b295-116">[Kestrel 서버](#kestrel)를 사용하는 IIS 작업자 프로세스([out-of-process 호스팅 모델](#out-of-process-hosting-model))와 다른 별도의 프로세스에서</span><span class="sxs-lookup"><span data-stu-id="7b295-116">In a process separate from the IIS worker process (the [out-of-process hosting model](#out-of-process-hosting-model)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="7b295-117">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)은 IIS와 In-process IIS HTTP 서버 또는 Kestrel 간의 네이티브 IIS 요청을 처리하는 네이티브 IIS 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-117">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="7b295-118">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b295-118">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="7b295-119">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="7b295-119">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="7b295-120">In-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="7b295-120">In-process hosting model</span></span>

<span data-ttu-id="7b295-121">In-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-121">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="7b295-122">나가는 네트워크 트래픽을 동일한 머신에 다시 반환하는 네트워크 인터페이스인 루프백 어댑터를 통해 요청이 프록시되지 않기 때문에 In Process 호스팅에서는 Out-of-process 호스팅을 통해 성능을 개선합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-122">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="7b295-123">IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-123">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7b295-124">ASP.NET Core 모듈:</span><span class="sxs-lookup"><span data-stu-id="7b295-124">The ASP.NET Core Module:</span></span>

* <span data-ttu-id="7b295-125">앱 초기화를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-125">Performs app initialization.</span></span>
  * <span data-ttu-id="7b295-126">[CoreCLR](/dotnet/standard/glossary#coreclr)을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-126">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="7b295-127">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="7b295-127">Calls `Program.Main`.</span></span>
* <span data-ttu-id="7b295-128">IIS 네이티브 요청의 수명을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-128">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="7b295-129">In-process 호스팅 모델은 .NET Framework를 대상으로 하는 ASP.NET Core 앱을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-129">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="7b295-130">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 In-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-130">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![ASP.NET Core 모듈](_static/ancm-inprocess.png)

<span data-ttu-id="7b295-132">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-132">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="7b295-133">드라이버는 웹 사이트의 구성된 포트[일반적으로 80(HTTP) 또는 443(HTTPS)]에서 IIS로 네이티브 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-133">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="7b295-134">모듈에서는 기본 요청을 수신하고 IIS HTTP Server(`IISHttpServer`)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-134">The module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="7b295-135">IIS HTTP 서버는 네이티브 요청을 관리형 요청으로 변환하는 IIS의 In-process 서버를 구현한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-135">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="7b295-136">IIS HTTP Server가 요청을 처리하면 해당 요청이 ASP.NET Core 미들웨어 파이프라인에 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-136">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="7b295-137">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-137">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="7b295-138">앱의 응답은 IIS HTTP 서버를 통해 IIS로 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-138">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="7b295-139">IIS는 요청을 시작한 클라이언트에 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-139">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="7b295-140">In-process 호스팅은 기존 앱에 대한 옵트인(opt-in) 기능이지만 [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿은 기본적으로 모든 IIS 및 IIS Express 시나리오에 대해 In-Process 호스팅 모델로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-140">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="7b295-141">Out-of-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="7b295-141">Out-of-process hosting model</span></span>

<span data-ttu-id="7b295-142">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 이 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-142">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="7b295-143">이 모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-143">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="7b295-144">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-144">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7b295-145">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-145">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 모듈](_static/ancm-outofprocess.png)

<span data-ttu-id="7b295-147">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-147">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="7b295-148">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-148">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="7b295-149">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-149">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="7b295-150">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 확장은 `http://localhost:{PORT}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-150">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="7b295-151">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-151">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="7b295-152">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-152">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="7b295-153">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-153">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="7b295-154">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-154">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="7b295-155">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-155">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="7b295-156">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-156">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="7b295-157">IIS 및 ASP.NET Core 모듈 구성 지침은 다음 토픽을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b295-157">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="7b295-158">macOS</span><span class="sxs-lookup"><span data-stu-id="7b295-158">macOS</span></span>](#tab/macos)

<span data-ttu-id="7b295-159">ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-159">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="7b295-160">Linux</span><span class="sxs-lookup"><span data-stu-id="7b295-160">Linux</span></span>](#tab/linux)

<span data-ttu-id="7b295-161">ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-161">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="7b295-162">Windows</span><span class="sxs-lookup"><span data-stu-id="7b295-162">Windows</span></span>](#tab/windows)

<span data-ttu-id="7b295-163">ASP.NET Core는 다음과 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-163">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="7b295-164">[Kestrel](xref:fundamentals/servers/kestrel) 서버는 기본 플랫폼 간 HTTP 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-164">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="7b295-165">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)는 [Http.Sys 커널 드라이버 및 HTTP Server API](/windows/desktop/Http/http-api-start-page)를 기반으로 하는 Windows 전용 HTTP 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-165">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="7b295-166">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 사용하면 앱이 [Kestrel 서버](#kestrel)를 사용하는 IIS 작업자 프로세스(*out-of-process*)와 다른 별도의 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-166">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="7b295-167">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 이 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-167">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="7b295-168">이 모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-168">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="7b295-169">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-169">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7b295-170">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-170">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 모듈](_static/ancm-outofprocess.png)

<span data-ttu-id="7b295-172">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-172">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="7b295-173">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-173">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="7b295-174">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-174">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="7b295-175">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)는 `http://localhost:{port}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-175">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="7b295-176">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-176">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="7b295-177">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-177">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="7b295-178">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-178">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="7b295-179">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-179">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="7b295-180">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-180">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="7b295-181">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-181">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="7b295-182">IIS 및 ASP.NET Core 모듈 구성 지침은 다음 토픽을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b295-182">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="7b295-183">macOS</span><span class="sxs-lookup"><span data-stu-id="7b295-183">macOS</span></span>](#tab/macos)

<span data-ttu-id="7b295-184">ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-184">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="7b295-185">Linux</span><span class="sxs-lookup"><span data-stu-id="7b295-185">Linux</span></span>](#tab/linux)

<span data-ttu-id="7b295-186">ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-186">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="7b295-187">Kestrel</span><span class="sxs-lookup"><span data-stu-id="7b295-187">Kestrel</span></span>

<span data-ttu-id="7b295-188">Kestrel은 ASP.NET Core 프로젝트 템플릿에 포함된 기본 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-188">Kestrel is the default web server included in ASP.NET Core project templates.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7b295-189">Kestrel은 다음과 같이 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-189">Kestrel can be used:</span></span>

* <span data-ttu-id="7b295-190">인터넷을 포함한 네트워크로부터 직접 요청을 처리하는 에지 서버로서 단독으로 사용</span><span class="sxs-lookup"><span data-stu-id="7b295-190">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel은 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="7b295-192">[IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](http://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버*와 함께 사용</span><span class="sxs-lookup"><span data-stu-id="7b295-192">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="7b295-193">역방향 프록시 서버는 인터넷에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-193">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel은 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="7b295-195">&mdash;역방향 프록시 서버가 있는 호스팅 구성과 없는 호스팅 구성 모두&mdash; ASP.NET Core 2.1 이상 앱에 대해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-195">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported for ASP.NET Core 2.1 or later apps.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="7b295-196">앱이 내부 네트워크의 요청만을 수락하는 경우 Kestrel을 단독으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-196">If the app only accepts requests from an internal network, Kestrel can be used by itself.</span></span>

![Kestrel은 내부 네트워크와 직접 통신합니다.](kestrel/_static/kestrel-to-internal.png)

<span data-ttu-id="7b295-198">앱이 인터넷에 노출된 경우, Kestrel은 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](http://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버*를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-198">If the app is exposed to the Internet, Kestrel must use a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="7b295-199">역방향 프록시 서버는 인터넷에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-199">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

![Kestrel은 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="7b295-201">인터넷에 직접 노출되는 공용 에지 서버 배포에 역방향 프록시를 사용하는 가장 중요한 이유는 보안입니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-201">The most important reason for using a reverse proxy for public-facing edge server deployments that are exposed directly the Internet is security.</span></span> <span data-ttu-id="7b295-202">1.x 버전의 Kestrel에는 인터넷의 공격으로부터 보호하기 위한 중요한 보안 기능이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-202">The 1.x versions of Kestrel don't include important security features to defend against attacks from the Internet.</span></span> <span data-ttu-id="7b295-203">여기에는 적절한 시간 제한, 요청 크기 제한, 동시 연결 제한을 비롯한 다양한 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-203">This includes, but isn't limited to, appropriate timeouts, request size limits, and concurrent connection limits.</span></span>

::: moniker-end

<span data-ttu-id="7b295-204">Kestrel을 역방향 프록시 구성에서 사용하는 경우에 대한 Kestrel 구성 지침 및 정보는 <xref:fundamentals/servers/kestrel>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b295-204">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

### <a name="nginx-with-kestrel"></a><span data-ttu-id="7b295-205">Nginx 및 Kestrel</span><span class="sxs-lookup"><span data-stu-id="7b295-205">Nginx with Kestrel</span></span>

<span data-ttu-id="7b295-206">Linux에서 Kestrel에 대한 역방향 프록시 서버로 Nginx를 사용하는 방법은 <xref:host-and-deploy/linux-nginx>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b295-206">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="7b295-207">Apache 및 Kestrel</span><span class="sxs-lookup"><span data-stu-id="7b295-207">Apache with Kestrel</span></span>

<span data-ttu-id="7b295-208">Linux에서 Kestrel에 대한 역방향 프록시 서버로 Apache를 사용하는 방법은 <xref:host-and-deploy/linux-apache>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b295-208">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="iis-http-server"></a><span data-ttu-id="7b295-209">IIS HTTP 서버</span><span class="sxs-lookup"><span data-stu-id="7b295-209">IIS HTTP Server</span></span>

<span data-ttu-id="7b295-210">IIS HTTP 서버는 IIS의 [In-process 서버](#in-process-hosting-model)이고 In Process 배포에 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-210">IIS HTTP Server is an [in-process server](#in-process-hosting-model) for IIS and required for in-process deployments.</span></span> <span data-ttu-id="7b295-211">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)은 IIS와 IIS HTTP 서버 간에 네이티브 IIS 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-211">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) handles native IIS requests between IIS and IIS HTTP Server.</span></span> <span data-ttu-id="7b295-212">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b295-212">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="7b295-213">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="7b295-213">HTTP.sys</span></span>

<span data-ttu-id="7b295-214">Windows에서 ASP.NET Core 앱을 실행할 경우 Kestrel 대신 HTTP.sys를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-214">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="7b295-215">최상의 성능을 위해 일반적으로 Kestrel을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-215">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="7b295-216">앱이 인터넷에 노출되고 필수 기능이 Kestrel이 아닌 HTTP.sys에서 지원되는 경우 시나리오에서 HTTP.sys를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-216">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="7b295-217">자세한 내용은 <xref:fundamentals/servers/httpsys>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b295-217">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="7b295-219">HTTP.sys는 내부 네트워크에만 노출되는 앱에도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-219">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="7b295-221">HTTP.sys 구성 지침은 <xref:fundamentals/servers/httpsys>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b295-221">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="7b295-222">ASP.NET Core 서버 인프라</span><span class="sxs-lookup"><span data-stu-id="7b295-222">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="7b295-223">`Startup.Configure` 메서드에서 사용 가능한 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>는 <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> 형식의 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-223">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="7b295-224">Kestrel 및 HTTP.sys는 각각 단일 기능인 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>만을 노출하지만 다른 서버 구현은 추가 기능을 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-224">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="7b295-225">`IServerAddressesFeature`를 사용하여 런타임 시 서버 구현이 바인딩된 포트를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-225">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="7b295-226">사용자 지정 서버</span><span class="sxs-lookup"><span data-stu-id="7b295-226">Custom servers</span></span>

<span data-ttu-id="7b295-227">기본 제공 서버가 앱의 요구 사항을 충족하지 않으면 사용자 지정 서버 구현을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-227">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="7b295-228">[OWIN(Open Web Interface for .NET) 가이드](xref:fundamentals/owin)에서는 [Nowin](https://github.com/Bobris/Nowin) 기반 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 구현을 작성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-228">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="7b295-229">앱이 사용하는 기능 인터페이스에만 구현이 필요합니다. 최소한 <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> 및 <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>가 지원되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-229">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="7b295-230">서버 시작</span><span class="sxs-lookup"><span data-stu-id="7b295-230">Server startup</span></span>

<span data-ttu-id="7b295-231">IDE(통합 개발 환경)이나 편집기가 앱을 시작하는 경우 서버가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-231">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="7b295-232">[Visual Studio](https://visualstudio.microsoft.com) &ndash; 실행 프로필을 사용하여 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 또는 콘솔에서 앱 및 서버를 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-232">[Visual Studio](https://visualstudio.microsoft.com) &ndash; Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="7b295-233">[Visual Studio Code](https://code.visualstudio.com/) &ndash; [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode)로 앱 및 서버를 시작합니다. 그러면 CoreCLR 디버거를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-233">[Visual Studio Code](https://code.visualstudio.com/) &ndash; The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="7b295-234">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/) &ndash; [Mono Soft-Mode 디버거](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)로 앱 및 서버를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-234">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="7b295-235">프로젝트의 폴더에 있는 명령 프롬프트에서 앱을 시작할 때 [dotnet run](/dotnet/core/tools/dotnet-run)은 서버 및 앱을 시작합니다(Kestrel 및 HTTP.sys만 해당).</span><span class="sxs-lookup"><span data-stu-id="7b295-235">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="7b295-236">`Debug`(기본값) 또는 `Release`로 설정되어 있는 `-c|--configuration` 옵션으로 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-236">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span> <span data-ttu-id="7b295-237">실행 프로필이 *launchSettings.json* 파일에 있는 경우 `--launch-profile <NAME>` 옵션을 사용하여 실행 프로필(예: `Development` 또는 `Production`)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-237">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile <NAME>` option to set the launch profile (for example, `Development` or `Production`).</span></span> <span data-ttu-id="7b295-238">자세한 내용은 [dotnet run](/dotnet/core/tools/dotnet-run) 및 [.NET Core 배포 패키징](/dotnet/core/build/distribution-packaging)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b295-238">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="7b295-239">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="7b295-239">HTTP/2 support</span></span>

<span data-ttu-id="7b295-240">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-240">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="7b295-241">Kestrel</span><span class="sxs-lookup"><span data-stu-id="7b295-241">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="7b295-242">운영 체제</span><span class="sxs-lookup"><span data-stu-id="7b295-242">Operating system</span></span>
    * <span data-ttu-id="7b295-243">Windows Server 2016/Windows 10 이상&dagger;</span><span class="sxs-lookup"><span data-stu-id="7b295-243">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="7b295-244">Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)</span><span class="sxs-lookup"><span data-stu-id="7b295-244">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="7b295-245">이후 릴리스에서는 macOS에서 HTTP/2가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-245">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="7b295-246">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="7b295-246">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="7b295-247">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="7b295-247">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="7b295-248">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="7b295-248">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="7b295-249">대상 프레임워크: HTTP.sys 배포에는 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-249">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="7b295-250">IIS(In-Process)</span><span class="sxs-lookup"><span data-stu-id="7b295-250">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="7b295-251">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="7b295-251">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7b295-252">대상 프레임워크: .NET Core 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="7b295-252">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="7b295-253">IIS(Out-of-process)</span><span class="sxs-lookup"><span data-stu-id="7b295-253">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="7b295-254">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="7b295-254">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7b295-255">공용 에지 서버 연결은 HTTP/2를 사용하지만 Kestrel에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-255">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="7b295-256">대상 프레임워크: IIS Out-of-process 배포에는 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-256">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="7b295-257">&dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-257">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="7b295-258">이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-258">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="7b295-259">TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-259">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="7b295-260">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="7b295-260">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="7b295-261">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="7b295-261">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="7b295-262">대상 프레임워크: HTTP.sys 배포에는 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-262">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="7b295-263">IIS(Out-of-process)</span><span class="sxs-lookup"><span data-stu-id="7b295-263">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="7b295-264">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="7b295-264">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7b295-265">공용 에지 서버 연결은 HTTP/2를 사용하지만 Kestrel에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-265">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="7b295-266">대상 프레임워크: IIS Out-of-process 배포에는 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-266">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="7b295-267">HTTP/2 연결은 [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 및 TLS 1.2 이상을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b295-267">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="7b295-268">자세한 정보는 서버 배포 시나리오와 관련된 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b295-268">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7b295-269">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7b295-269">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
