---
title: Blazor 클라이언트 쪽 호스트 및 배포
author: guardrex
description: ASP.NET Core, CDN(Content Delivery Network), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: 01a612029f415f583908c3bf2adc2e6d35167acb
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614720"
---
# <a name="host-and-deploy-blazor-client-side"></a><span data-ttu-id="06df5-103">Blazor 클라이언트 쪽 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="06df5-103">Host and deploy Blazor client-side</span></span>

<span data-ttu-id="06df5-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="06df5-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="host-configuration-values"></a><span data-ttu-id="06df5-105">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="06df5-105">Host configuration values</span></span>

<span data-ttu-id="06df5-106">[클라이언트 쪽 호스팅 모델](xref:blazor/hosting-models#client-side-hosting-model)을 사용하는 Blazor 앱은 개발 환경의 런타임에 다음 호스트 구성 값을 명령줄 인수로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-106">Blazor apps that use the [client-side hosting model](xref:blazor/hosting-models#client-side-hosting-model) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="06df5-107">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="06df5-107">Content Root</span></span>

<span data-ttu-id="06df5-108">`--contentroot` 인수는 앱의 콘텐츠 파일을 포함하는 디렉터리에 대한 절대 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-108">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span> <span data-ttu-id="06df5-109">다음 예제에서 `/content-root-path`는 앱의 콘텐츠 루트 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-109">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="06df5-110">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-110">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="06df5-111">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-111">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="06df5-112">**IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-112">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="06df5-113">앱이 Visual Studio 디버거를 통해 실행되거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행되는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-113">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="06df5-114">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-114">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="06df5-115">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-115">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="06df5-116">경로 기준</span><span class="sxs-lookup"><span data-stu-id="06df5-116">Path base</span></span>

<span data-ttu-id="06df5-117">`--pathbase` 인수는 루트가 아닌 가상 경로를 사용하여 로컬로 앱을 실행하기 위한 앱 기본 경로를 설정합니다. (준비 및 프로덕션의 경우 `<base>` 태그 `href`는 `/` 이외의 경로로 설정됩니다).</span><span class="sxs-lookup"><span data-stu-id="06df5-117">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="06df5-118">다음 예제에서 `/virtual-path`는 앱의 경로 기준입니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-118">In the following examples, `/virtual-path` is the app's path base.</span></span> <span data-ttu-id="06df5-119">자세한 내용은 [앱 기본 경로](#app-base-path) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06df5-119">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="06df5-120">`<base>` 태그의 `href`에 대해 제공되는 경로와 달리 `--pathbase` 인수 값을 전달할 때 뒤에 슬래시(`/`)를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-120">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="06df5-121">앱 기본 경로가 `<base>` 태그에 `<base href="/CoolApp/">`로 제공되는 경우(뒤에 슬래시 포함) 명령줄 인수 값을 `--pathbase=/CoolApp`로 전달합니다(뒤에 슬래시 없음).</span><span class="sxs-lookup"><span data-stu-id="06df5-121">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="06df5-122">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-122">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="06df5-123">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-123">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* <span data-ttu-id="06df5-124">**IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-124">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="06df5-125">앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-125">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* <span data-ttu-id="06df5-126">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-126">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="06df5-127">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-127">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a><span data-ttu-id="06df5-128">URL</span><span class="sxs-lookup"><span data-stu-id="06df5-128">URLs</span></span>

<span data-ttu-id="06df5-129">`--urls` 인수는 요청을 수신하기 위한 포트 및 프로토콜을 포함하는 IP 주소 또는 호스트 주소를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-129">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="06df5-130">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-130">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="06df5-131">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-131">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="06df5-132">**IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-132">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="06df5-133">앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-133">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="06df5-134">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-134">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="06df5-135">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-135">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deployment"></a><span data-ttu-id="06df5-136">배포</span><span class="sxs-lookup"><span data-stu-id="06df5-136">Deployment</span></span>

<span data-ttu-id="06df5-137">[클라이언트 쪽 호스팅 모델](xref:blazor/hosting-models#client-side-hosting-model)을 사용하는 경우:</span><span class="sxs-lookup"><span data-stu-id="06df5-137">With the [client-side hosting model](xref:blazor/hosting-models#client-side-hosting-model):</span></span>

* <span data-ttu-id="06df5-138">Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 브라우저에 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-138">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="06df5-139">해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-139">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="06df5-140">다음 방법 중 하나 이상을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-140">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="06df5-141">Blazor 앱은 ASP.NET Core 앱에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-141">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="06df5-142">이 전략은 [ASP.NET Core를 사용하여 호스트된 배포](#hosted-deployment-with-aspnet-core) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-142">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="06df5-143">Blazor 앱은 정적 호스팅 웹 서버 또는 서비스에 배치되며, 이 경우 Blazor 앱을 처리하기 위해 .NET을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-143">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="06df5-144">이 전략은 [독립 실행형 배포](#standalone-deployment) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-144">This strategy is covered in the [Standalone deployment](#standalone-deployment) section.</span></span>

## <a name="configure-the-linker"></a><span data-ttu-id="06df5-145">링커 구성</span><span class="sxs-lookup"><span data-stu-id="06df5-145">Configure the Linker</span></span>

<span data-ttu-id="06df5-146">Blazor는 각 빌드에 대해 IL(중간 언어) 연결을 수행하여 출력 어셈블리에서 불필요한 IL을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-146">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="06df5-147">어셈블리 연결은 빌드 시 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-147">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="06df5-148">자세한 내용은 <xref:host-and-deploy/blazor/configure-linker>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06df5-148">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="06df5-149">올바른 라우팅을 위해 URL 다시 생성</span><span class="sxs-lookup"><span data-stu-id="06df5-149">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="06df5-150">클라이언트 쪽 앱의 페이지 구성 요소에 대한 요청 라우팅은 서버 쪽에서 호스트하는 앱에 대한 요청 라우팅처럼 간단하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-150">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="06df5-151">다음 두 페이지를 사용하여 클라이언트 쪽 앱을 생각해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-151">Consider a client-side app with two pages:</span></span>

* <span data-ttu-id="06df5-152">**_Main.cshtml_** &ndash; 앱의 루트에서 로드하며 정보 페이지(`href="About"`)에 대한 링크를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-152">**_Main.cshtml_** &ndash; Loads at the root of the app and contains a link to the About page (`href="About"`).</span></span>
* <span data-ttu-id="06df5-153">**_About.cshtml_** &ndash; 페이지 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-153">**_About.cshtml_** &ndash; About page.</span></span>

<span data-ttu-id="06df5-154">브라우저의 주소 표시줄을 사용하여 앱의 기본 문서를 요청하는 경우(예: `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="06df5-154">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="06df5-155">브라우저가 요청을 합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-155">The browser makes a request.</span></span>
1. <span data-ttu-id="06df5-156">기본 페이지(일반적으로 *index.html*)가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-156">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="06df5-157">*index.html* 앱을 부트스트랩으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-157">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="06df5-158">Blazor의 라우터가 로드되고 Razor 기본 페이지(*Main.cshtml*)가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-158">Blazor's router loads, and the Razor Main page (*Main.cshtml*) is displayed.</span></span>

<span data-ttu-id="06df5-159">기본 페이지에서 정보 페이지에 대한 링크를 선택하면 정보 페이지가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-159">On the Main page, selecting the link to the About page loads the About page.</span></span> <span data-ttu-id="06df5-160">Blazor 라우터는 브라우저가 인터넷상에서 `www.contoso.com`에 대해 `About`를 요청하는 것을 중단하므로 정보 페이지에 대한 링크 선택은 클라이언트에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-160">Selecting the link to the About page works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the About page itself.</span></span> <span data-ttu-id="06df5-161">*클라이언트 쪽 앱 내의* 내부 페이지에 대한 모든 요청도 같은 방법으로 작동합니다. 요청은 인터넷상에서 서버가 호스트하는 리소스에 대한 브라우저 기반 요청을 트리거하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-161">All of the requests for internal pages *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="06df5-162">라우터가 내부적으로 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-162">The router handles the requests internally.</span></span>

<span data-ttu-id="06df5-163">브라우저의 주소 표시줄을 사용하여 `www.contoso.com/About`을 요청하면 해당 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-163">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="06df5-164">앱의 인터넷 호스트에 해당 리소스가 없으므로 *404 - 찾을 수 없음* 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-164">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="06df5-165">브라우저는 인터넷 기반 호스트에 대해 클라이언트 쪽 페이지를 요청하므로, 웹 서버 및 호스팅 서비스는 서버에 실제로 존재하지 않는 리소스에 대한 모든 요청을 *index.html* 페이지에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-165">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="06df5-166">*index.html*이 반환되는 경우 앱의 클라이언트 쪽 라우터가 인수하여 올바른 리소스로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-166">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="06df5-167">앱 기본 경로</span><span class="sxs-lookup"><span data-stu-id="06df5-167">App base path</span></span>

<span data-ttu-id="06df5-168">앱 기본 경로는 서버상의 가상 앱 루트 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-168">The *app base path* is the virtual app root path on the server.</span></span> <span data-ttu-id="06df5-169">예를 들어 Contoso 서버의 가상 폴더 `/CoolApp/`에 상주하는 앱은 `https://www.contoso.com/CoolApp`에 도달하며 `/CoolApp/`의 가상 기본 경로를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-169">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="06df5-170">앱 기본 경로를 `CoolApp/`로 설정하면 앱은 서버상에 가상으로 상주하는 위치를 인식하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-170">By setting the app base path to `CoolApp/`, the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="06df5-171">해당 앱은 앱 기본 경로를 사용하여 루트 디렉터리에 없는 구성 요소에서 앱 루트에 대한 상대 URL을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-171">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="06df5-172">이렇게 하면 디렉터리 구조의 다른 수준에 존재하는 구성 요소가 앱 전체의 위치에서 다른 리소스에 대한 링크를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-172">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="06df5-173">또한 링크의 `href` 대상이 앱 기본 경로 내에 있는 경우 &mdash; Blazor 라우터가 내부 탐색을 처리하는 경우 하이퍼링크 클릭을 가로채기 위해서도 앱 기본 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-173">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="06df5-174">많은 호스팅 시나리오에서 앱에 대한 서버의 가상 경로는 앱의 루트입니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-174">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="06df5-175">이러한 경우 앱 기본 경로는 앱에 대한 기본 구성인 슬래시(`<base href="/" />`)입니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-175">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="06df5-176">GitHub 페이지 및 IIS 가상 디렉터리 또는 하위 애플리케이션 등의 다른 호스팅 시나리오에서는 앱 기본 경로를 앱에 대한 서버의 가상 경로로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-176">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="06df5-177">앱의 기본 경로를 설정하려면 `<head>` 태그 요소 내에서 찾은 *index.html*의 `<base>` 태그를 추가하거나 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-177">To set the app's base path, add or update the `<base>` tag in *index.html* found within the `<head>` tag elements.</span></span> <span data-ttu-id="06df5-178">`href` 속성 값을 `virtual-path/`(뒤에 슬래시가 필요함)로 설정하며, 여기서 `virtual-path/`는 앱에 대한 서버의 전체 가상 앱 루트 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-178">Set the `href` attribute value to `virtual-path/` (the trailing slash is required), where `virtual-path/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="06df5-179">앞의 예에서 가상 경로는 `CoolApp/`: `<base href="CoolApp/">`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-179">In the preceding example, the virtual path is set to `CoolApp/`: `<base href="CoolApp/">`.</span></span>

<span data-ttu-id="06df5-180">루트가 아닌 경로가 구성된 앱(예: `<base href="CoolApp/">`)의 경우, 앱은 *로컬로 실행하면* 해당 리소스를 찾지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-180">For an app with a non-root virtual path configured (for example, `<base href="CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="06df5-181">로컬 개발 및 시험 중에 이 문제를 해결하려면 런타임에 `<base>` 태그의 `href` 값과 일치하는 *기본 경로* 인수를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-181">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="06df5-182">앱을 로컬로 실행하는 경우 경로 기본 인수를 루트 경로(`/`)와 함께 전달하려면 앱의 디렉터리에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-182">To pass the path base argument with the root path (`/`) when running the app locally, execute the following command from the app's directory:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="06df5-183">앱이 `http://localhost:port/CoolApp`에서 로컬로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-183">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="06df5-184">자세한 내용은 [경로 기준 호스트 구성 값](#path-base) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06df5-184">For more information, see the section on the [path base host configuration value](#path-base).</span></span>

<span data-ttu-id="06df5-185">앱이 [클라이언트 쪽 호스팅 모델](xref:blazor/hosting-models#client-side-hosting-model)(**Blazor** 프로젝트 템플릿 기반)을 사용하고 ASP.NET Core 앱에서 IIS 하위 애플리케이션으로 호스트되는 경우, 상속된 ASP.NET Core 모듈 핸들러를 사용하지 않도록 설정하거나 *web.config* 파일에서 루트(상위) 앱의 `<handlers>` 섹션을 하위 앱에서 상속하지 않도록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-185">If an app uses the [client-side hosting model](xref:blazor/hosting-models#client-side-hosting-model) (based on the **Blazor** project template) and is hosted as an IIS sub-application in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>

<span data-ttu-id="06df5-186">파일에 `<handlers>` 섹션을 추가하여 앱의 게시된 *web.config* 파일에서 핸들러를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-186">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

<span data-ttu-id="06df5-187">또는 `inheritInChildApplications`를 `false`로 설정한 상태에서 `<location>` 요소를 사용하여 루트(상위) 앱의 `<system.webServer>` 섹션의 상속을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-187">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="06df5-188">핸들러 제거 또는 상속을 사용하지 않도록 설정은 이 섹션에서 설명하는 대로 앱의 기본 경로 구성에 더하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-188">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="06df5-189">IIS에서 하위 앱을 구성할 때
앱의 *index.html* 파일에서 앱 기본 경로를 사용한 IIS 별칭으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-189">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="06df5-190">ASP.NET Core를 사용하여 호스트된 배포</span><span class="sxs-lookup"><span data-stu-id="06df5-190">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="06df5-191">*호스트하는 배포*는 서버상에서 실행하는 [ASP.NET Core 앱](xref:index)에서 클라이언트 쪽 Blazor 앱을 브라우저에 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-191">A *hosted deployment* serves the client-side Blazor app to browsers from an [ASP.NET Core app](xref:index) that runs on a server.</span></span>

<span data-ttu-id="06df5-192">Blazor 앱은 ASP.NET Core 앱과 함께 배포되도록 게시된 출력의 후자 앱과 함께 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-192">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="06df5-193">ASP.NET Core 앱을 호스트할 수 있는 웹 서버가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-193">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="06df5-194">호스트하는 배포의 경우, Visual Studio는 **Blazor(ASP.NET Core가 호스트하는)** 프로젝트 템플릿([dotnet new](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorhosted` 템플릿)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-194">For a hosted deployment, Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template (`blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<span data-ttu-id="06df5-195">ASP.NET Core 앱 호스팅 및 배포에 대한 자세한 내용은 <xref:host-and-deploy/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06df5-195">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="06df5-196">Azure App Service 배포에 대한 자세한 내용은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06df5-196">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="06df5-197">독립 실행형 배포</span><span class="sxs-lookup"><span data-stu-id="06df5-197">Standalone deployment</span></span>

<span data-ttu-id="06df5-198">*독립 실행형 배포*는 클라이언트 쪽 Blazor 앱을 클라이언트가 직접 요청하는 정적 파일 세트로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-198">A *standalone deployment* serves the client-side Blazor app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="06df5-199">Blazor 앱을 제공하기 위해 웹 서버를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-199">A web server isn't used to serve the Blazor app.</span></span>

### <a name="iis"></a><span data-ttu-id="06df5-200">IIS</span><span class="sxs-lookup"><span data-stu-id="06df5-200">IIS</span></span>

<span data-ttu-id="06df5-201">IIS는 Blazor 앱에 사용할 수 있는 정적 파일 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-201">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="06df5-202">Blazor를 호스트하도록 IIS를 구성하려면 [IIS에서 정적 웹 사이트 작성](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06df5-202">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="06df5-203">게시된 자산은 */bin/Release/{TARGET FRAMEWORK}/publish* 폴더에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-203">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="06df5-204">웹 서버 또는 호스팅 서비스에서 *publish* 폴더의 콘텐츠를 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-204">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="06df5-205">web.config</span><span class="sxs-lookup"><span data-stu-id="06df5-205">web.config</span></span>

<span data-ttu-id="06df5-206">Blazor 프로젝트가 게시되면 다음 IIS 구성을 사용하여 *web.config* 파일이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-206">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="06df5-207">다음 파일 확장명에 대해 MIME 형식을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-207">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="06df5-208">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="06df5-208">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="06df5-209">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="06df5-209">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="06df5-210">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="06df5-210">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="06df5-211">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="06df5-211">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="06df5-212">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="06df5-212">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="06df5-213">다음 MIME 형식에 대해 HTTP 압축을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-213">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="06df5-214">URL 재작성 모듈 규칙을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-214">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="06df5-215">앱의 정적 자산이 상주하는 하위 디렉터리(*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-215">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="06df5-216">파일이 아닌 자산에 대한 요청이 정적 자산 폴더에 있는 앱의 기본 문서(*{ASSEMBLY NAME}/dist/index.html*)로 리디렉션되도록 SPA 폴백 라우팅을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-216">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="06df5-217">URL 재작성 모듈 설치</span><span class="sxs-lookup"><span data-stu-id="06df5-217">Install the URL Rewrite Module</span></span>

<span data-ttu-id="06df5-218">URL을 다시 생성하려면 [URL 다시 생성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-218">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="06df5-219">이 모듈은 기본적으로 설치되지 않으며 웹 서버(IIS) 역할 서비스 기능으로 설치하는 데 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-219">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="06df5-220">이 모듈은 IIS 웹 사이트에서 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-220">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="06df5-221">웹 플랫폼 설치 관리자를 사용하여 이 모듈을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-221">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="06df5-222">로컬에서 [URL 다시 생성 모듈 다운로드 페이지](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-222">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="06df5-223">영어 버전의 경우 **WebPI**를 선택하여 WebPI 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-223">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="06df5-224">다른 언어의 경우 서버에 맞는 아키텍처(x86 x64)를 선택하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-224">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="06df5-225">설치 관리자를 서버에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-225">Copy the installer to the server.</span></span> <span data-ttu-id="06df5-226">설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-226">Run the installer.</span></span> <span data-ttu-id="06df5-227">**설치** 버튼을 선택하고 사용 조건에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-227">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="06df5-228">설치가 완료된 후 서버를 다시 시작하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-228">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="06df5-229">웹 사이트 구성</span><span class="sxs-lookup"><span data-stu-id="06df5-229">Configure the website</span></span>

<span data-ttu-id="06df5-230">웹 사이트의 **실제 경로**를 앱의 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-230">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="06df5-231">이 폴더는 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-231">The folder contains:</span></span>

* <span data-ttu-id="06df5-232">필요한 리디렉션 규칙 및 파일 콘텐츠 형식 등 IIS가 웹 사이트를 구성하기 위해 사용하는 *web.config* 파일.</span><span class="sxs-lookup"><span data-stu-id="06df5-232">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="06df5-233">앱의 정적 자산 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-233">The app's static asset folder.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="06df5-234">문제 해결</span><span class="sxs-lookup"><span data-stu-id="06df5-234">Troubleshooting</span></span>

<span data-ttu-id="06df5-235">500 - 내부 서버 오류가 수신되고 웹 사이트의 구성에 액세스를 시도할 때 IIS 관리자가 오류를 표시하면 URL 다시 생성 모듈이 설치되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-235">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="06df5-236">모듈이 설치되지 않은 경우 IIS가 *web.config* 파일을 구문 분석할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-236">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="06df5-237">그러면 IIS 관리자가 웹 사이트의 구성을 로드할 수 없으며 웹 사이트가 Blazor의 정적 파일을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-237">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="06df5-238">IIS 배포 문제 해결에 대한 자세한 내용은 <xref:host-and-deploy/iis/troubleshoot>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06df5-238">For more information on troubleshooting deployments to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>

### <a name="nginx"></a><span data-ttu-id="06df5-239">Nginx</span><span class="sxs-lookup"><span data-stu-id="06df5-239">Nginx</span></span>

<span data-ttu-id="06df5-240">다음 *nginx.conf* 파일은 Nginx가 디스크에서 *index.html* 파일을 찾을 수 없을 때마다 해당 파일을 보내도록 구성하는 방법을 보여 주도록 단순화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-240">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="06df5-241">프로덕션 Nginx 웹 서버 구성에 대한 자세한 내용은 [NGINX Plus 및 NGINX 구성 파일 만들기](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06df5-241">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="06df5-242">Docker의 Nginx</span><span class="sxs-lookup"><span data-stu-id="06df5-242">Nginx in Docker</span></span>

<span data-ttu-id="06df5-243">Docker에서 Nginx를 사용하여 Blazor를 호스트하려면 Dockerfile을 Alpine 기반 Nginx 이미지를 사용하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-243">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="06df5-244">Dockerfile을 업데이트하여 *nginx.config* 파일을 컨테이너에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-244">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="06df5-245">다음 예제와 같이 Dockerfile에 한 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-245">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a><span data-ttu-id="06df5-246">GitHub 페이지</span><span class="sxs-lookup"><span data-stu-id="06df5-246">GitHub Pages</span></span>

<span data-ttu-id="06df5-247">URL 다시 생성을 처리하려면 *index.html* 페이지로 요청 리디렉션을 처리하는 스크립트를 사용하여 *404.html* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-247">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="06df5-248">커뮤니티에서 제공하는 예제 구현은 [GitHub 페이지에 대한 단일 페이지 앱](http://spa-github-pages.rafrex.com/)([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme))을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06df5-248">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="06df5-249">커뮤니티 방식을 사용하는 예는 [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io)([실시간 사이트](https://blazor-demo.github.io/))를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06df5-249">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="06df5-250">조직의 사이트 대신 프로젝트 사이트를 사용하는 경우 *index.html*의 `<base>` 태그를 추가하거나 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="06df5-250">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="06df5-251">`href` 특성 값을 후행 슬래시가 있는 GitHub 리포지토리 이름으로 설정합니다(예: `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="06df5-251">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>
