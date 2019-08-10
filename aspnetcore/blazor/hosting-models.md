---
title: ASP.NET Core Blazor 호스팅 모델
author: guardrex
description: Blazor 클라이언트 쪽 및 서버 쪽 호스팅 모델을 이해 합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/hosting-models
ms.openlocfilehash: 9dd96ff6e3539bf1c3e932b33776b16d0fbb2d34
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68948293"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="ac53b-103">ASP.NET Core Blazor 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="ac53b-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="ac53b-104">[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ac53b-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ac53b-105">Blazor는 [Webassembly](https://webassembly.org/)(*Blazor server side*) ASP.NET Core의 서버 쪽 또는 서버 쪽에서 브라우저에서 클라이언트 쪽을 실행하도록 디자인 된 웹 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor client-side*) or server-side in ASP.NET Core (*Blazor server-side*).</span></span> <span data-ttu-id="ac53b-106">호스팅 모델에 관계 없이 앱 및 구성 요소 모델은 *동일*합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="ac53b-107">이 문서에서 설명 하는 호스팅 모델에 대 한 프로젝트를 만들려면 <xref:blazor/get-started>를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="ac53b-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="client-side"></a><span data-ttu-id="ac53b-108">클라이언트 쪽</span><span class="sxs-lookup"><span data-stu-id="ac53b-108">Client-side</span></span>

<span data-ttu-id="ac53b-109">Blazor에 대 한 주 호스팅 모델이 브라우저에서 클라이언트 쪽을 제대로 실행 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="ac53b-110">Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 브라우저에 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="ac53b-111">해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="ac53b-112">UI 업데이트 및 이벤트 처리는 동일한 프로세스 내에서 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="ac53b-113">응용 프로그램의 자산은 정적 콘텐츠를 클라이언트에 제공할 수 있는 웹 서버 또는 서비스에 정적 파일로 배포 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![클라이언트 쪽 Blazor: Blazor 앱은 브라우저 내부의 UI 스레드에서 실행 됩니다.](hosting-models/_static/client-side.png)

<span data-ttu-id="ac53b-115">클라이언트 쪽 호스팅 모델을 사용 하 여 Blazor 앱을 만들려면 다음 템플릿 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-115">To create a Blazor app using the client-side hosting model, use either of the following templates:</span></span>

* <span data-ttu-id="ac53b-116">**Blazor (클라이언트 쪽)** ([dotnet new blazor](/dotnet/core/tools/dotnet-new)) &ndash; 정적 파일 집합으로 배포 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-116">**Blazor (client-side)** ([dotnet new blazor](/dotnet/core/tools/dotnet-new)) &ndash; Deployed as a set of static files.</span></span>
* <span data-ttu-id="ac53b-117">**Blazor (ASP.NET Core 호스팅)** ([dotnet new blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; ASP.NET Core 서버에서 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-117">**Blazor (ASP.NET Core Hosted)** ([dotnet new blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; Hosted by an ASP.NET Core server.</span></span> <span data-ttu-id="ac53b-118">ASP.NET Core 앱은 Blazor 앱을 클라이언트에 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-118">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="ac53b-119">Blazor 클라이언트 쪽 앱은 web API 호출 또는 [SignalR](xref:signalr/introduction)를 사용 하 여 네트워크를 통해 서버와 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-119">The Blazor client-side app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="ac53b-120">템플릿에는를 처리 하는 *blazor* 스크립트가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-120">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="ac53b-121">.NET 런타임, 앱 및 앱의 종속성 다운로드</span><span class="sxs-lookup"><span data-stu-id="ac53b-121">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="ac53b-122">앱을 실행 하기 위한 런타임 초기화</span><span class="sxs-lookup"><span data-stu-id="ac53b-122">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="ac53b-123">클라이언트 쪽 호스팅 모델에서는 다음과 같은 여러 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-123">The client-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="ac53b-124">.NET 서버 쪽 종속성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-124">There's no .NET server-side dependency.</span></span> <span data-ttu-id="ac53b-125">클라이언트에 다운로드 한 후 앱이 완전히 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-125">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="ac53b-126">클라이언트 리소스 및 기능은 완전히 활용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-126">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="ac53b-127">작업은 서버에서 클라이언트로 오프 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-127">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="ac53b-128">ASP.NET Core 웹 서버는 앱을 호스트 하는 데 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-128">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="ac53b-129">서버를 사용 하지 않는 배포 시나리오를 사용할 수 있습니다. 예를 들어 CDN에서 앱을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-129">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="ac53b-130">클라이언트 쪽 호스팅을 단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-130">There are downsides to client-side hosting:</span></span>

* <span data-ttu-id="ac53b-131">앱은 브라우저의 기능으로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-131">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="ac53b-132">가능 클라이언트 하드웨어 및 소프트웨어 (예: Weasembomsupport)는 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-132">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="ac53b-133">다운로드 크기가 크고 앱을 로드 하는 데 시간이 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-133">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="ac53b-134">.NET 런타임 및 도구 지원의 완성도는 낮아집니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-134">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="ac53b-135">예를 들어 [.NET Standard](/dotnet/standard/net-standard) 지원 및 디버깅에는 제한 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-135">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="server-side"></a><span data-ttu-id="ac53b-136">서버 쪽</span><span class="sxs-lookup"><span data-stu-id="ac53b-136">Server-side</span></span>

<span data-ttu-id="ac53b-137">서버 쪽 호스팅 모델을 사용 하면 앱이 ASP.NET Core 앱 내에서 서버에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-137">With the server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="ac53b-138">UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-138">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![브라우저는 SignalR 연결을 통해 서버의 앱 (ASP.NET Core 앱 내에서 호스트)과 상호 작용 합니다.](hosting-models/_static/server-side.png)

<span data-ttu-id="ac53b-140">서버 쪽 호스팅 모델을 사용 하 여 Blazor 앱을 만들려면 ASP.NET Core **Blazor Server 앱** 템플릿 ([dotnet new blazorserverside](/dotnet/core/tools/dotnet-new))을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-140">To create a Blazor app using the server-side hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserverside](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="ac53b-141">ASP.NET Core 앱은 서버 쪽 앱을 호스팅하고 클라이언트에서 연결 하는 SignalR 끝점을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-141">The ASP.NET Core app hosts the server-side app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="ac53b-142">ASP.NET Core 앱은 추가할 앱 `Startup` 클래스를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-142">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="ac53b-143">서버 쪽 서비스.</span><span class="sxs-lookup"><span data-stu-id="ac53b-143">Server-side services.</span></span>
* <span data-ttu-id="ac53b-144">요청 처리 파이프라인에 대 한 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-144">The app to the request handling pipeline.</span></span>

<span data-ttu-id="ac53b-145">*Blazor* 스크립트&dagger; 는 클라이언트 연결을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-145">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="ac53b-146">필요에 따라 앱 상태를 유지 하 고 복원 하는 것은 앱의 책임입니다 (예: 네트워크 연결이 끊어진 경우).</span><span class="sxs-lookup"><span data-stu-id="ac53b-146">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="ac53b-147">서버 쪽 호스팅 모델에서는 다음과 같은 여러 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-147">The server-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="ac53b-148">다운로드 크기는 클라이언트 쪽 앱 보다 훨씬 더 작기 때문에 앱이 훨씬 더 빠르게 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-148">Download size is significantly smaller than a client-side app, and the app loads much faster.</span></span>
* <span data-ttu-id="ac53b-149">앱은 .NET Core와 호환 되는 Api의 사용을 포함 하 여 서버 기능을 최대한 활용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-149">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="ac53b-150">서버의 .NET Core는 앱을 실행 하는 데 사용 되므로 디버깅과 같은 기존 .NET 도구는 정상적으로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-150">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="ac53b-151">씬 클라이언트가 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-151">Thin clients are supported.</span></span> <span data-ttu-id="ac53b-152">예를 들어 서버 쪽 앱은 리소스 제한 장치에서 Weasembmbambmbsemba를 지원 하지 않는 브라우저에서 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-152">For example, server-side apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="ac53b-153">앱의 구성 요소 코드C# 를 포함 하 여 앱의 .net/code 베이스가 클라이언트에 제공 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-153">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="ac53b-154">서버 쪽 호스팅을 단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-154">There are downsides to server-side hosting:</span></span>

* <span data-ttu-id="ac53b-155">일반적으로 더 높은 대기 시간이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-155">Higher latency usually exists.</span></span> <span data-ttu-id="ac53b-156">모든 사용자 상호 작용에는 네트워크 홉이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-156">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="ac53b-157">오프 라인은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-157">There's no offline support.</span></span> <span data-ttu-id="ac53b-158">클라이언트 연결에 실패 하면 앱 작동이 중지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-158">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="ac53b-159">여러 사용자가 있는 앱의 경우에는 확장성이 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-159">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="ac53b-160">서버는 여러 클라이언트 연결을 관리 하 고 클라이언트 상태를 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-160">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="ac53b-161">앱을 제공 하려면 ASP.NET Core 서버가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-161">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="ac53b-162">서버를 사용 하지 않는 배포 시나리오 (예: CDN에서 앱 제공)를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-162">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="ac53b-163">&dagger;*Blazor* 스크립트는 ASP.NET Core 공유 프레임 워크의 포함 리소스에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-163">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="ac53b-164">동일한 서버에 다시 연결</span><span class="sxs-lookup"><span data-stu-id="ac53b-164">Reconnection to the same server</span></span>

<span data-ttu-id="ac53b-165">Blazor 서버 쪽 앱은 서버에 대 한 활성 SignalR 연결이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-165">Blazor server-side apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="ac53b-166">연결이 끊어지면 앱이 서버에 다시 연결을 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-166">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="ac53b-167">클라이언트의 상태가 아직 메모리에 있으면 클라이언트 세션이 상태 손실 없이 다시 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-167">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>
 
<span data-ttu-id="ac53b-168">클라이언트에서 연결이 끊어진 것을 감지 하면 클라이언트에서 다시 연결을 시도 하는 동안 기본 UI가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-168">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="ac53b-169">다시 연결이 실패 하는 경우 사용자에 게 다시 시도 하는 옵션이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-169">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="ac53b-170">UI를 사용자 지정 하려면 `components-reconnect-modal` *_Host* Razor 페이지 `id` 에서로 요소를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-170">To customize the UI, define an element with `components-reconnect-modal` as its `id` in the *_Host.cshtml* Razor page.</span></span> <span data-ttu-id="ac53b-171">클라이언트는 연결 상태에 따라 다음 CSS 클래스 중 하나를 사용 하 여이 요소를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-171">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>
 
* <span data-ttu-id="ac53b-172">`components-reconnect-show`&ndash; 연결이 끊어져서 클라이언트에서 다시 연결을 시도 했음을 나타내는 UI를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-172">`components-reconnect-show` &ndash; Show the UI to indicate the connection was lost and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="ac53b-173">`components-reconnect-hide`&ndash; 클라이언트에 활성 연결이 있으며 UI를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-173">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="ac53b-174">`components-reconnect-failed`&ndash; 다시 연결 하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-174">`components-reconnect-failed` &ndash; Reconnection failed.</span></span> <span data-ttu-id="ac53b-175">다시 연결을 다시 시도 하려면 `window.Blazor.reconnect()`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-175">To attempt reconnection again, call `window.Blazor.reconnect()`.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="ac53b-176">렌더링 후 상태 저장 다시 연결</span><span class="sxs-lookup"><span data-stu-id="ac53b-176">Stateful reconnection after prerendering</span></span>
 
<span data-ttu-id="ac53b-177">서버에 대 한 클라이언트 연결이 설정 되기 전에 서버에서 UI를 미리 렌더링 하도록 Blazor 서버 쪽 앱이 기본적으로 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-177">Blazor server-side apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="ac53b-178">이는 *_Host* Razor 페이지에 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-178">This is set up in the *_Host.cshtml* Razor page:</span></span>
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
<span data-ttu-id="ac53b-179">클라이언트는 앱을 미리 렌더링 하는 데 사용 된 상태와 동일한 상태를 사용 하 여 서버에 다시 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-179">The client reconnects to the server with the same state that was used to prerender the app.</span></span> <span data-ttu-id="ac53b-180">앱의 상태가 아직 메모리에 있는 경우 SignalR 연결이 설정 된 후에 구성 요소 상태가 다시 발생 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-180">If the app's state is still in memory, the component state isn't rerendered after the SignalR connection is established.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="ac53b-181">Razor 페이지 및 뷰에서 상태 저장 대화형 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="ac53b-181">Render stateful interactive components from Razor pages and views</span></span>
 
<span data-ttu-id="ac53b-182">상태 저장 대화형 구성 요소는 Razor 페이지 또는 보기에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-182">Stateful interactive components can be added to a Razor page or view.</span></span> <span data-ttu-id="ac53b-183">페이지 또는 뷰가 렌더링 되 면 구성 요소는 미리 렌더링 된 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-183">When the page or view renders, the component is prerendered with it.</span></span> <span data-ttu-id="ac53b-184">그런 다음 상태가 아직 메모리에 있는 한 클라이언트 연결이 설정 되 면 앱은 구성 요소 상태에 다시 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-184">The app then reconnects to the component state once the client connection is established as long as the state is still in memory.</span></span>
 
<span data-ttu-id="ac53b-185">예를 들어 다음 Razor 페이지는 폼을 `Counter` 사용 하 여 지정 된 초기 카운트를 사용 하 여 구성 요소를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-185">For example, the following Razor page renders a `Counter` component with an initial count that's specified using a form:</span></span>
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialCount" />
    <button type="submit">Set initial count</button>
</form>
 
@(await Html.RenderComponentAsync<Counter>(new { InitialCount = InitialCount }))
 
@code {
    [BindProperty(SupportsGet=true)]
    public int InitialCount { get; set; }
}
```

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="ac53b-186">앱이 사전 렌더링 되는 경우 검색</span><span class="sxs-lookup"><span data-stu-id="ac53b-186">Detect when the app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a><span data-ttu-id="ac53b-187">Blazor 서버 쪽 앱에 대 한 SignalR 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="ac53b-187">Configure the SignalR client for Blazor server-side apps</span></span>
 
<span data-ttu-id="ac53b-188">경우에 따라 Blazor 서버 쪽 앱에서 사용 하는 SignalR 클라이언트를 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-188">Sometimes, you need to configure the SignalR client used by Blazor server-side apps.</span></span> <span data-ttu-id="ac53b-189">예를 들어 SignalR 클라이언트에 대 한 로깅을 구성 하 여 연결 문제를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-189">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>
 
<span data-ttu-id="ac53b-190">*Pages/_Host* 파일에서 SignalR client를 구성 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-190">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="ac53b-191">*Blazor 스크립트* 에 대 한 `<script>` 태그에 특성을추가합니다.`autostart="false"`</span><span class="sxs-lookup"><span data-stu-id="ac53b-191">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="ac53b-192">을 `Blazor.start` 호출 하 고 SignalR builder를 지정 하는 구성 개체를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac53b-192">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>
 
```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="ac53b-193">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ac53b-193">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
