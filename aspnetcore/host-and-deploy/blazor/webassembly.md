---
title: ASP.NET Core Blazor WebAssembly 호스트 및 배포
author: guardrex
description: ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: ea2c625f424447209a362cdc58bdb18be061e47f
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511355"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="a67d8-103">ASP.NET Core Blazor WebAssembly 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="a67d8-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="a67d8-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a67d8-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a67d8-105">[Blazor WebAssembly 호스팅 모델 사용](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="a67d8-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="a67d8-106">Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 브라우저에 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="a67d8-107">해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="a67d8-108">다음 배포 전략이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="a67d8-109">Blazor 앱은 ASP.NET Core 앱에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="a67d8-110">이 전략은 [ASP.NET Core를 사용하여 호스트된 배포](#hosted-deployment-with-aspnet-core) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="a67d8-111">Blazor 앱은 정적 호스팅 웹 서버 또는 서비스에 배치되며, 이 경우 Blazor 앱을 처리하기 위해 .NET을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="a67d8-112">이 전략은 Blazor WebAssembly 앱을 IIS 하위 앱으로 호스트하는 방법에 대한 정보를 포함하는 [독립 실행형 배포](#standalone-deployment) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="a67d8-113">올바른 라우팅을 위해 URL 다시 생성</span><span class="sxs-lookup"><span data-stu-id="a67d8-113">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="a67d8-114">Blazor WebAssembly 앱의 페이지 구성 요소에 대한 라우팅 요청은 Blazor 서버에서 호스트한 앱의 요청을 라우팅하는 것처럼 간단하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-114">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="a67d8-115">다음 두 가지 구성 요소가 있는 Blazor WebAssembly 앱을 생각해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-115">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="a67d8-116">*Main.razor* &ndash; 앱의 루트에 로드되며 `About` 구성 요소에 대한 링크(`href="About"`)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-116">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="a67d8-117">*About.razor* &ndash; `About` 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-117">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="a67d8-118">브라우저의 주소 표시줄을 사용하여 앱의 기본 문서를 요청하는 경우(예: `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="a67d8-118">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="a67d8-119">브라우저가 요청을 합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-119">The browser makes a request.</span></span>
1. <span data-ttu-id="a67d8-120">기본 페이지(일반적으로 *index.html*)가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-120">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="a67d8-121">*index.html* 앱을 부트스트랩으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-121">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="a67d8-122">의 라우터가 로드되고 Razor `Main` 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-122">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="a67d8-123">Blazor 라우터는 브라우저가 인터넷에서 `www.contoso.com`으로 `About`을 요청하는 것을 중단하고 렌더링된 `About` 구성 요소를 직접 제공하므로 기본 페이지에서 `About` 구성 요소에 대한 링크 선택은 클라이언트에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-123">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="a67d8-124">*Blazor WebAssembly 앱 내*의 내부 엔드포인트에 대한 모든 요청도 같은 방법으로 작동합니다. 요청은 인터넷상에서 서버가 호스트하는 리소스에 대한 브라우저 기반 요청을 트리거하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-124">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="a67d8-125">라우터가 내부적으로 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-125">The router handles the requests internally.</span></span>

<span data-ttu-id="a67d8-126">브라우저의 주소 표시줄을 사용하여 `www.contoso.com/About`을 요청하면 해당 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-126">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="a67d8-127">앱의 인터넷 호스트에 해당 리소스가 없으므로 *404 - 찾을 수 없음* 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-127">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="a67d8-128">브라우저는 인터넷 기반 호스트에 대해 클라이언트 쪽 페이지를 요청하므로, 웹 서버 및 호스팅 서비스는 서버에 실제로 존재하지 않는 리소스에 대한 모든 요청을 *index.html* 페이지에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-128">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="a67d8-129">*index.html*이 반환되는 경우 앱의 Blazor 라우터가 인수하여 올바른 리소스로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-129">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="a67d8-130">IIS 서버에 배포하는 경우 앱의 게시된 *web.config* 파일과 함께 URL 재작성 모듈을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-130">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="a67d8-131">자세한 내용은 [IIS](#iis) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a67d8-131">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="a67d8-132">ASP.NET Core를 사용하여 호스트된 배포</span><span class="sxs-lookup"><span data-stu-id="a67d8-132">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="a67d8-133">*호스트된 배포*는 웹 서버에서 실행되는 [ASP.NET Core 앱](xref:index)에서 Blazor WebAssembly 앱을 브라우저에 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-133">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="a67d8-134">클라이언트 Blazor WebAssembly 앱이 서버 앱의 */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* 폴더에 서버 앱의 다른 정적 웹 자산과 함께 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-134">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="a67d8-135">두 앱이 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-135">The two apps are deployed together.</span></span> <span data-ttu-id="a67d8-136">ASP.NET Core 앱을 호스트할 수 있는 웹 서버가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-136">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="a67d8-137">호스트된 배포의 경우 Visual Studio에는 **Hosted**(`dotnet new` 명령을 사용하는 경우 `-ho|--hosted`) 옵션이 선택된 **Blazor WebAssembly 앱** 프로젝트 템플릿([dotnet new](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorwasm` 템플릿)이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-137">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="a67d8-138">ASP.NET Core 앱 호스팅 및 배포에 대한 자세한 내용은 <xref:host-and-deploy/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a67d8-138">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="a67d8-139">Azure App Service 배포에 대한 자세한 내용은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a67d8-139">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="a67d8-140">독립 실행형 배포</span><span class="sxs-lookup"><span data-stu-id="a67d8-140">Standalone deployment</span></span>

<span data-ttu-id="a67d8-141">*독립 실행형 배포*는 Blazor WebAssembly 앱을 클라이언트가 직접 요청하는 정적 파일 세트로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-141">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="a67d8-142">모든 정적 파일 서버는 Blazor 앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-142">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="a67d8-143">독립 실행형 배포 자산은 */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* 폴더에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-143">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="a67d8-144">IIS</span><span class="sxs-lookup"><span data-stu-id="a67d8-144">IIS</span></span>

<span data-ttu-id="a67d8-145">IIS는 Blazor 앱에 사용할 수 있는 정적 파일 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-145">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="a67d8-146">Blazor를 호스트하도록 IIS를 구성하려면 [IIS에서 정적 웹 사이트 작성](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a67d8-146">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="a67d8-147">게시된 자산은 */bin/Release/{TARGET FRAMEWORK}/publish* 폴더에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-147">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="a67d8-148">웹 서버 또는 호스팅 서비스에서 *publish* 폴더의 콘텐츠를 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-148">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="a67d8-149">web.config</span><span class="sxs-lookup"><span data-stu-id="a67d8-149">web.config</span></span>

<span data-ttu-id="a67d8-150">Blazor 프로젝트가 게시되면 다음 IIS 구성을 사용하여 *web.config* 파일이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-150">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="a67d8-151">다음 파일 확장명에 대해 MIME 형식을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-151">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="a67d8-152">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="a67d8-152">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="a67d8-153">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="a67d8-153">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="a67d8-154">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="a67d8-154">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="a67d8-155">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="a67d8-155">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="a67d8-156">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="a67d8-156">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="a67d8-157">다음 MIME 형식에 대해 HTTP 압축을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-157">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="a67d8-158">URL 재작성 모듈 규칙을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-158">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="a67d8-159">앱의 정적 자산이 상주하는 하위 디렉터리(*wwwroot/{PATH REQUESTED}* )를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-159">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="a67d8-160">파일이 아닌 자산에 대한 요청이 정적 자산 폴더에 있는 앱의 기본 문서(*wwwroot/index.html*)로 리디렉션되도록 SPA 대체(fallback) 라우팅을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-160">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="a67d8-161">URL 재작성 모듈 설치</span><span class="sxs-lookup"><span data-stu-id="a67d8-161">Install the URL Rewrite Module</span></span>

<span data-ttu-id="a67d8-162">URL을 다시 생성하려면 [URL 다시 생성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-162">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="a67d8-163">이 모듈은 기본적으로 설치되지 않으며 웹 서버(IIS) 역할 서비스 기능으로 설치하는 데 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-163">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="a67d8-164">이 모듈은 IIS 웹 사이트에서 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-164">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="a67d8-165">웹 플랫폼 설치 관리자를 사용하여 이 모듈을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-165">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="a67d8-166">로컬에서 [URL 다시 생성 모듈 다운로드 페이지](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-166">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="a67d8-167">영어 버전의 경우 **WebPI**를 선택하여 WebPI 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-167">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="a67d8-168">다른 언어의 경우 서버에 맞는 아키텍처(x86 x64)를 선택하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-168">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="a67d8-169">설치 관리자를 서버에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-169">Copy the installer to the server.</span></span> <span data-ttu-id="a67d8-170">설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-170">Run the installer.</span></span> <span data-ttu-id="a67d8-171">**설치** 버튼을 선택하고 사용 조건에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-171">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="a67d8-172">설치가 완료된 후 서버를 다시 시작하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-172">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="a67d8-173">웹 사이트 구성</span><span class="sxs-lookup"><span data-stu-id="a67d8-173">Configure the website</span></span>

<span data-ttu-id="a67d8-174">웹 사이트의 **실제 경로**를 앱의 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-174">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="a67d8-175">이 폴더는 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-175">The folder contains:</span></span>

* <span data-ttu-id="a67d8-176">필요한 리디렉션 규칙 및 파일 콘텐츠 형식 등 IIS가 웹 사이트를 구성하기 위해 사용하는 *web.config* 파일.</span><span class="sxs-lookup"><span data-stu-id="a67d8-176">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="a67d8-177">앱의 정적 자산 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-177">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="a67d8-178">IIS 하위 앱으로 호스트</span><span class="sxs-lookup"><span data-stu-id="a67d8-178">Host as an IIS sub-app</span></span>

<span data-ttu-id="a67d8-179">독립 실행형 앱이 IIS 하위 앱으로 호스트되는 경우 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-179">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="a67d8-180">상속된 ASP.NET Core 모듈 처리기를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-180">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="a67d8-181">Blazor 앱의 게시된 *web.config* 파일에 `<handlers>` 섹션을 추가하여 파일에서 핸들러를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-181">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="a67d8-182">`inheritInChildApplications`를 `false`로 설정한 상태에서 `<location>` 요소를 사용하여 루트(상위) 앱의 `<system.webServer>` 섹션의 상속을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-182">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="a67d8-183">처리기 제거 또는 상속을 사용하지 않도록 하는 설정은 [앱의 기본 경로 구성](xref:host-and-deploy/blazor/index#app-base-path)에 더하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-183">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="a67d8-184">IIS에서 하위 앱을 구성할 때
앱의 *index.html* 파일에서 앱 기본 경로를 사용한 IIS 별칭으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-184">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="a67d8-185">문제 해결</span><span class="sxs-lookup"><span data-stu-id="a67d8-185">Troubleshooting</span></span>

<span data-ttu-id="a67d8-186">500 - 내부 서버 오류가 수신되고 웹 사이트의 구성에 액세스를 시도할 때 IIS 관리자가 오류를 표시하면 URL 다시 생성 모듈이 설치되었는지 확인합니다. </span><span class="sxs-lookup"><span data-stu-id="a67d8-186">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="a67d8-187">모듈이 설치되지 않은 경우 IIS가 *web.config* 파일을 구문 분석할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-187">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="a67d8-188">그러면 IIS 관리자가 웹 사이트의 구성을 로드할 수 없으며 웹 사이트가 Blazor의 정적 파일을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-188">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="a67d8-189">IIS 배포 문제 해결에 대한 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a67d8-189">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="a67d8-190">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="a67d8-190">Azure Storage</span></span>

<span data-ttu-id="a67d8-191">[Azure Storage](/azure/storage/) 정적 파일 호스팅으로 서버리스 Blazor 앱 호스팅이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-191">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="a67d8-192">사용자 지정 도메인 이름, Azure 콘텐츠 배달 네트워크(CDN) 및 HTTPS가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-192">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="a67d8-193">스토리지 계정에서 정적 웹 사이트 호스팅을 위해 BLOB 서비스를 사용할 수 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="a67d8-193">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="a67d8-194">**인덱스 문서 이름**을 `index.html`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-194">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="a67d8-195">**오류 문서 경로**를 `index.html`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-195">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="a67d8-196">Razor 구성 요소 및 기타 파일이 아닌 엔드포인트는 Blob service에 의해 저장된 정적 콘텐츠의 실제 경로에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-196">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="a67d8-197">이러한 리소스 중 하나를 Blazor 라우터가 처리해야 한다는 요청이 수신되면 BLOB 서비스에 의해 생성된 *404 - 찾을 수 없음* 오류가 요청을 **오류 문서 경로**로 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-197">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="a67d8-198">*index.html* BLOB이 반환되고 Blazor 라우터가 로드되어 경로를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-198">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="a67d8-199">자세한 내용은 [Azure Storage에서 정적 웹 사이트 호스팅](/azure/storage/blobs/storage-blob-static-website)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a67d8-199">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="a67d8-200">Nginx</span><span class="sxs-lookup"><span data-stu-id="a67d8-200">Nginx</span></span>

<span data-ttu-id="a67d8-201">다음 *nginx.conf* 파일은 Nginx가 디스크에서 *index.html* 파일을 찾을 수 없을 때마다 해당 파일을 보내도록 구성하는 방법을 보여 주도록 단순화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-201">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="a67d8-202">프로덕션 Nginx 웹 서버 구성에 대한 자세한 내용은 [NGINX Plus 및 NGINX 구성 파일 만들기](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a67d8-202">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="a67d8-203">Docker의 Nginx</span><span class="sxs-lookup"><span data-stu-id="a67d8-203">Nginx in Docker</span></span>

<span data-ttu-id="a67d8-204">Docker에서 Nginx를 사용하여 Blazor를 호스트하려면 Dockerfile을 Alpine 기반 Nginx 이미지를 사용하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-204">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="a67d8-205">Dockerfile을 업데이트하여 *nginx.config* 파일을 컨테이너에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-205">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="a67d8-206">다음 예제와 같이 Dockerfile에 한 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-206">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="a67d8-207">Apache</span><span class="sxs-lookup"><span data-stu-id="a67d8-207">Apache</span></span>

<span data-ttu-id="a67d8-208">Blazor WebAssembly 앱을 CentOS 7 이상에 배포하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-208">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="a67d8-209">Apache 구성 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-209">Create the Apache configuration file.</span></span> <span data-ttu-id="a67d8-210">다음 예제는 단순화된 구성 파일입니다(*blazorapp.config*).</span><span class="sxs-lookup"><span data-stu-id="a67d8-210">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="a67d8-211">Apache 구성 파일을 CentOS 7의 기본 Apache 구성 디렉터리인 `/etc/httpd/conf.d/` 디렉터리에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-211">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="a67d8-212">앱의 파일을 `/var/www/blazorapp` 디렉터리(구성 파일의 `DocumentRoot`에 지정된 위치)에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-212">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="a67d8-213">Apache 서비스를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-213">Restart the Apache service.</span></span>

<span data-ttu-id="a67d8-214">자세한 내용은 [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 및 [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a67d8-214">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="a67d8-215">GitHub 페이지</span><span class="sxs-lookup"><span data-stu-id="a67d8-215">GitHub Pages</span></span>

<span data-ttu-id="a67d8-216">URL 다시 생성을 처리하려면 *index.html* 페이지로 요청 리디렉션을 처리하는 스크립트를 사용하여 *404.html* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-216">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="a67d8-217">커뮤니티에서 제공하는 예제 구현은 [GitHub 페이지에 대한 단일 페이지 앱](https://spa-github-pages.rafrex.com/)([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme))을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a67d8-217">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="a67d8-218">커뮤니티 방식을 사용하는 예는 [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io)([실시간 사이트](https://blazor-demo.github.io/))를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a67d8-218">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="a67d8-219">조직의 사이트 대신 프로젝트 사이트를 사용하는 경우 *index.html*의 `<base>` 태그를 추가하거나 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-219">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="a67d8-220">`href` 특성 값을 후행 슬래시가 있는 GitHub 리포지토리 이름으로 설정합니다(예: `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="a67d8-220">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="a67d8-221">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="a67d8-221">Host configuration values</span></span>

<span data-ttu-id="a67d8-222">[Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)은 개발 환경의 런타임에 다음 호스트 구성 값을 명령줄 인수로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-222">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="a67d8-223">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="a67d8-223">Content root</span></span>

<span data-ttu-id="a67d8-224">`--contentroot` 인수는 앱의 콘텐츠 파일을 포함하는 디렉터리([콘텐츠 루트](xref:fundamentals/index#content-root))에 대한 절대 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-224">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="a67d8-225">다음 예제에서 `/content-root-path`는 앱의 콘텐츠 루트 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-225">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="a67d8-226">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-226">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="a67d8-227">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-227">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="a67d8-228">**IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-228">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="a67d8-229">앱이 Visual Studio 디버거를 통해 실행되거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행되는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-229">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="a67d8-230">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-230">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="a67d8-231">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-231">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="a67d8-232">경로 기준</span><span class="sxs-lookup"><span data-stu-id="a67d8-232">Path base</span></span>

<span data-ttu-id="a67d8-233">`--pathbase` 인수는 루트가 아닌 상대 URL 경로를 사용하여 로컬로 앱을 실행하기 위한 앱 기본 경로를 설정합니다. (준비 및 프로덕션의 경우 `<base>` 태그 `href`는 `/` 이외의 경로로 설정됩니다.)</span><span class="sxs-lookup"><span data-stu-id="a67d8-233">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="a67d8-234">다음 예제에서 `/relative-URL-path`는 앱의 경로 기준입니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-234">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="a67d8-235">자세한 내용은 [앱 기본 경로](xref:host-and-deploy/blazor/index#app-base-path)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a67d8-235">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a67d8-236">`<base>` 태그의 `href`에 대해 제공되는 경로와 달리 `--pathbase` 인수 값을 전달할 때 뒤에 슬래시(`/`)를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-236">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="a67d8-237">앱 기본 경로가 `<base>` 태그에 `<base href="/CoolApp/">`로 제공되는 경우(뒤에 슬래시 포함) 명령줄 인수 값을 `--pathbase=/CoolApp`로 전달합니다(뒤에 슬래시 없음).</span><span class="sxs-lookup"><span data-stu-id="a67d8-237">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="a67d8-238">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-238">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="a67d8-239">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-239">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="a67d8-240">**IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-240">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="a67d8-241">앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-241">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="a67d8-242">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-242">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="a67d8-243">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-243">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="a67d8-244">URL</span><span class="sxs-lookup"><span data-stu-id="a67d8-244">URLs</span></span>

<span data-ttu-id="a67d8-245">`--urls` 인수는 요청을 수신하기 위한 포트 및 프로토콜을 포함하는 IP 주소 또는 호스트 주소를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-245">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="a67d8-246">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-246">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="a67d8-247">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-247">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="a67d8-248">**IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-248">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="a67d8-249">앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-249">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="a67d8-250">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-250">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="a67d8-251">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-251">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="a67d8-252">링커 구성</span><span class="sxs-lookup"><span data-stu-id="a67d8-252">Configure the Linker</span></span>

Blazor<span data-ttu-id="a67d8-253">는 각 릴리스 빌드에 대해 IL(중간 언어) 연결을 수행하여 출력 어셈블리에서 불필요한 IL을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="a67d8-253"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="a67d8-254">자세한 내용은 <xref:host-and-deploy/blazor/configure-linker>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a67d8-254">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
