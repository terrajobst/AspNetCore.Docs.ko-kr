---
title: ASP.NET Core Blazor 호스팅 모델
author: guardrex
description: Blazor Weasembomand Blazor 서버 호스팅 모델을 이해 합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/03/2019
uid: blazor/hosting-models
ms.openlocfilehash: d1b9e6ab7ba93c00a569be309f2334df9e3f4140
ms.sourcegitcommit: e5d4768aaf85703effb4557a520d681af8284e26
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73616586"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="e2a1f-103">ASP.NET Core Blazor 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="e2a1f-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="e2a1f-104">[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e2a1f-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="e2a1f-105">Blazor는 [WeasembmbBlazor](https://webassembly.org/)(*Blazor server*) ASP.NET Core의 서버 쪽에서 브라우저에서 클라이언트 쪽을 실행 하도록 디자인 된웹 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="e2a1f-106">호스팅 모델에 관계 없이 앱 및 구성 요소 모델은 *동일*합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="e2a1f-107">이 문서에서 설명 하는 호스팅 모델에 대 한 프로젝트를 만들려면 <xref:blazor/get-started>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="e2a1f-108">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e2a1f-108">Blazor WebAssembly</span></span>

<span data-ttu-id="e2a1f-109">Blazor에 대 한 주 호스팅 모델이 브라우저에서 클라이언트 쪽을 제대로 실행 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="e2a1f-110">Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 브라우저에 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="e2a1f-111">해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="e2a1f-112">UI 업데이트 및 이벤트 처리는 동일한 프로세스 내에서 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="e2a1f-113">응용 프로그램의 자산은 정적 콘텐츠를 클라이언트에 제공할 수 있는 웹 서버 또는 서비스에 정적 파일로 배포 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor Weasemboma: Blazor 앱은 브라우저 내부의 UI 스레드에서 실행 됩니다.](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="e2a1f-115">클라이언트 쪽 호스팅 모델을 사용 하 여 Blazor 앱을 만들려면 **Blazor Weasembomapp** 템플릿 ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new))을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="e2a1f-116">**Blazor WebAssembly 앱** 템플릿을 선택한 후에는 **ASP.NET Core hosted** 확인란을 선택 하 여 ASP.NET Core 백 엔드를 사용 하도록 앱을 구성 하는 옵션이 있습니다 ([dotnet new blazorwasm--호스팅](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="e2a1f-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="e2a1f-117">ASP.NET Core 앱은 Blazor 앱을 클라이언트에 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="e2a1f-118">Blazor Weasembomapps는 web API 호출 또는 [SignalR](xref:signalr/introduction)를 사용 하 여 네트워크를 통해 서버와 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="e2a1f-119">템플릿에는를 처리 하는 *blazor* 스크립트가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="e2a1f-120">.NET 런타임, 앱 및 앱의 종속성 다운로드</span><span class="sxs-lookup"><span data-stu-id="e2a1f-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="e2a1f-121">앱을 실행 하기 위한 런타임 초기화</span><span class="sxs-lookup"><span data-stu-id="e2a1f-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="e2a1f-122">Blazor Weasembom호스팅 모델은 몇 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="e2a1f-123">.NET 서버 쪽 종속성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="e2a1f-124">클라이언트에 다운로드 한 후 앱이 완전히 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="e2a1f-125">클라이언트 리소스 및 기능은 완전히 활용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="e2a1f-126">작업은 서버에서 클라이언트로 오프 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="e2a1f-127">ASP.NET Core 웹 서버는 앱을 호스트 하는 데 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="e2a1f-128">서버를 사용 하지 않는 배포 시나리오를 사용할 수 있습니다. 예를 들어 CDN에서 앱을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="e2a1f-129">Blazor Weasembom단점이는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="e2a1f-130">앱은 브라우저의 기능으로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="e2a1f-131">가능 클라이언트 하드웨어 및 소프트웨어 (예: Weasembomsupport)는 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="e2a1f-132">다운로드 크기가 크고 앱을 로드 하는 데 시간이 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="e2a1f-133">.NET 런타임 및 도구 지원의 완성도는 낮아집니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="e2a1f-134">예를 들어 [.NET Standard](/dotnet/standard/net-standard) 지원 및 디버깅에는 제한 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="e2a1f-135">Blazor 서버</span><span class="sxs-lookup"><span data-stu-id="e2a1f-135">Blazor Server</span></span>

<span data-ttu-id="e2a1f-136">Blazor 서버 호스팅 모델을 사용 하면 앱이 ASP.NET Core 앱 내에서 서버에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="e2a1f-137">UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![브라우저는 SignalR 연결을 통해 서버의 앱 (ASP.NET Core 앱 내에서 호스트)과 상호 작용 합니다.](hosting-models/_static/blazor-server.png)

<span data-ttu-id="e2a1f-139">Blazor 서버 호스팅 모델을 사용 하 여 Blazor 앱을 만들려면 ASP.NET Core **Blazor Server 앱** 템플릿 ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new))을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="e2a1f-140">ASP.NET Core 앱은 Blazor Server 앱을 호스팅하고 클라이언트가 연결 하는 SignalR 끝점을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="e2a1f-141">ASP.NET Core 앱은 추가할 앱의 `Startup` 클래스를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="e2a1f-142">서버 쪽 서비스.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-142">Server-side services.</span></span>
* <span data-ttu-id="e2a1f-143">요청 처리 파이프라인에 대 한 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="e2a1f-144">*Blazor* 스크립트&dagger; 클라이언트 연결을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="e2a1f-145">필요에 따라 앱 상태를 유지 하 고 복원 하는 것은 앱의 책임입니다 (예: 네트워크 연결이 끊어진 경우).</span><span class="sxs-lookup"><span data-stu-id="e2a1f-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="e2a1f-146">Blazor 서버 호스팅 모델은 몇 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="e2a1f-147">다운로드 크기는 Blazor Weasembomapp 보다 훨씬 더 작기 때문에 앱이 훨씬 더 빠르게 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="e2a1f-148">앱은 .NET Core와 호환 되는 Api의 사용을 포함 하 여 서버 기능을 최대한 활용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="e2a1f-149">서버의 .NET Core는 앱을 실행 하는 데 사용 되므로 디버깅과 같은 기존 .NET 도구는 정상적으로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="e2a1f-150">씬 클라이언트가 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-150">Thin clients are supported.</span></span> <span data-ttu-id="e2a1f-151">예를 들어 Blazor Server 앱은 리소스 제한 장치에서 Weasembmbmbsemboma를 지원 하지 않는 브라우저에서 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="e2a1f-152">앱의 구성 요소 코드C# 를 포함 하 여 앱의 .net/code 베이스가 클라이언트에 제공 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="e2a1f-153">Blazor 서버 호스팅을 단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="e2a1f-154">일반적으로 더 높은 대기 시간이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-154">Higher latency usually exists.</span></span> <span data-ttu-id="e2a1f-155">모든 사용자 상호 작용에는 네트워크 홉이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="e2a1f-156">오프 라인은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-156">There's no offline support.</span></span> <span data-ttu-id="e2a1f-157">클라이언트 연결에 실패 하면 앱 작동이 중지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="e2a1f-158">여러 사용자가 있는 앱의 경우에는 확장성이 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="e2a1f-159">서버는 여러 클라이언트 연결을 관리 하 고 클라이언트 상태를 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="e2a1f-160">앱을 제공 하려면 ASP.NET Core 서버가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="e2a1f-161">서버를 사용 하지 않는 배포 시나리오 (예: CDN에서 앱 제공)를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="e2a1f-162">&dagger;*blazor* 스크립트는 ASP.NET Core 공유 프레임 워크의 포함 리소스에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="e2a1f-163">서버에서 렌더링 된 UI 비교</span><span class="sxs-lookup"><span data-stu-id="e2a1f-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="e2a1f-164">Blazor 서버 앱을 이해 하는 한 가지 방법은 Razor 뷰나 Razor Pages를 사용 하 여 ASP.NET Core 앱에서 UI를 렌더링 하는 일반적인 모델과의 차이점을 이해 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="e2a1f-165">두 모델 모두 Razor 언어를 사용 하 여 HTML 콘텐츠를 설명 하지만 태그가 렌더링 되는 방식에는 크게 차이가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="e2a1f-166">Razor 페이지 또는 뷰가 렌더링 되 면 Razor 코드의 모든 줄은 텍스트 형식으로 HTML을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="e2a1f-167">렌더링 후 서버는 생성 된 상태를 포함 하 여 페이지 또는 뷰 인스턴스를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="e2a1f-168">서버 유효성 검사에 실패 하 고 유효성 검사 요약이 표시 되는 경우와 같은 페이지에 대 한 다른 요청이 발생 하는 경우</span><span class="sxs-lookup"><span data-stu-id="e2a1f-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="e2a1f-169">전체 페이지는 HTML 텍스트로 다시 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="e2a1f-170">페이지가 클라이언트로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-170">The page is sent to the client.</span></span>

<span data-ttu-id="e2a1f-171">Blazor 앱은 *구성 요소*라는 UI의 재사용 가능한 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="e2a1f-172">구성 요소에 C# 는 코드, 태그 및 기타 구성 요소가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="e2a1f-173">구성 요소가 렌더링 되 면 Blazor은 HTML 또는 XML 문서 개체 모델 (DOM)와 유사한 포함 된 구성 요소의 그래프를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="e2a1f-174">이 그래프에는 속성 및 필드에 유지 되는 구성 요소 상태가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-174">This graph includes component state held in properties and fields.</span></span> <span data-ttu-id="e2a1f-175">Blazor는 구성 요소 그래프를 평가 하 여 태그의 이진 표현을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-175">Blazor evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="e2a1f-176">이진 형식은 다음과 같을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-176">The binary format can be:</span></span>

* <span data-ttu-id="e2a1f-177">렌더링 중에 HTML 텍스트로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="e2a1f-178">정기적으로 렌더링 하는 동안 태그를 효율적으로 업데이트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="e2a1f-179">Blazor의 UI 업데이트는 다음에 의해 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="e2a1f-180">사용자 상호 작용 (예: 단추 선택)</span><span class="sxs-lookup"><span data-stu-id="e2a1f-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="e2a1f-181">타이머와 같은 앱 트리거입니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="e2a1f-182">그래프가 계산 되며 UI *diff* (차이)가 계산 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="e2a1f-183">이러한 차이는 클라이언트에서 UI를 업데이트 하는 데 필요한 최소한의 DOM 편집 집합입니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="e2a1f-184">Diff는 클라이언트에 이진 형식으로 전송 되 고 브라우저에서 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="e2a1f-185">사용자가 클라이언트에서 다른 구성 요소를 탐색 한 후에 구성 요소가 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="e2a1f-186">사용자가 구성 요소와 상호 작용 하는 동안에는 구성 요소의 상태 (서비스, 리소스)가 서버 메모리에 보관 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="e2a1f-187">서버에서 많은 구성 요소의 상태를 동시에 유지 관리할 수 있기 때문에 메모리 소모는 해결 해야 하는 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="e2a1f-188">서버 메모리를 최적으로 사용 하기 위해 Blazor Server 앱을 제작 하는 방법에 대 한 지침은 <xref:security/blazor/server>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="circuits"></a><span data-ttu-id="e2a1f-189">배선</span><span class="sxs-lookup"><span data-stu-id="e2a1f-189">Circuits</span></span>

<span data-ttu-id="e2a1f-190">Blazor 서버 앱은 [ASP.NET Core SignalR](xref:signalr/introduction)위에 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-190">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="e2a1f-191">각 클라이언트는 *회로*라는 하나 이상의 SignalR 연결을 통해 서버와 통신 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-191">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="e2a1f-192">회로는 일시적인 네트워크 중단을 허용할 수 있는 SignalR 연결에 대 한 Blazor의 추상화입니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-192">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="e2a1f-193">Blazor client에서 SignalR 연결의 연결이 끊어지면 새 SignalR 연결을 사용 하 여 서버에 다시 연결 하려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-193">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="e2a1f-194">Blazor 서버 앱에 연결 된 각 브라우저 화면 (브라우저 탭 또는 iframe)은 SignalR 연결을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-194">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="e2a1f-195">이는 서버에서 렌더링 되는 일반적인 앱에 비해 또 다른 중요 한 차이점입니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-195">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="e2a1f-196">서버에서 렌더링 하는 응용 프로그램에서는 여러 브라우저 화면에서 동일한 앱을 여는 것은 일반적으로 서버에서 추가 리소스 요구 사항으로 변환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-196">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="e2a1f-197">Blazor 서버 앱에서 각 브라우저 화면에는 서버에서 관리 해야 하는 별도의 회로와 개별 구성 요소 상태 인스턴스가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-197">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

<span data-ttu-id="e2a1f-198">Blazor는 브라우저 탭을 닫거나 외부 URL로 이동 하는 *정상적인* 종료를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-198">Blazor considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="e2a1f-199">정상적인 종료 시 회로 및 연결 된 리소스가 즉시 해제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-199">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="e2a1f-200">클라이언트는 네트워크 중단으로 인해 안정적이 지 않은 방식으로 연결을 끊을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-200">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> <span data-ttu-id="e2a1f-201">Blazor 서버는 클라이언트가 다시 연결할 수 있도록 구성 가능한 간격 동안 연결이 끊어진 회로를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-201">Blazor Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="e2a1f-202">자세한 내용은 [동일한 서버에](#reconnection-to-the-same-server) 다시 연결 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-202">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="e2a1f-203">UI 대기 시간</span><span class="sxs-lookup"><span data-stu-id="e2a1f-203">UI Latency</span></span>

<span data-ttu-id="e2a1f-204">UI 대기 시간은 시작 된 작업에서 UI를 업데이트 하는 데 걸리는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-204">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="e2a1f-205">UI 대기 시간에 대 한 값이 작을수록 앱이 사용자에 게 반응 하는 데 필수적입니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-205">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="e2a1f-206">Blazor 서버 앱에서 각 작업은 서버로 전송 되 고 처리 되며 UI diff가 다시 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-206">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="e2a1f-207">따라서 UI 대기 시간은 네트워크 대기 시간 및 작업 처리의 서버 대기 시간 합계입니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-207">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="e2a1f-208">개인 회사 네트워크로 제한 된 lob (기간 업무) 앱의 경우 네트워크 대기 시간으로 인 한 대기 시간에 대 한 사용자의 영향은 일반적으로 imperceptible입니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-208">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="e2a1f-209">인터넷을 통해 배포 된 앱의 경우 특히 사용자가 지리적으로 광범위 하 게 분산 된 경우 대기 시간이 달라질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-209">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="e2a1f-210">메모리 사용은 앱 대기 시간에도 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-210">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="e2a1f-211">메모리 사용이 증가 하면 가비지 수집 또는 페이징 메모리가 디스크에 자주 발생 하므로 앱 성능이 저하 되 고 결과적으로 UI 대기 시간이 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-211">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="e2a1f-212">자세한 내용은 <xref:security/blazor/server>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-212">For more information, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="e2a1f-213">Blazor 서버 앱은 네트워크 대기 시간 및 메모리 사용을 줄여 UI 대기 시간을 최소화 하도록 최적화 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-213">Blazor Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="e2a1f-214">네트워크 대기 시간을 측정 하는 방법은 <xref:host-and-deploy/blazor/server#measure-network-latency>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-214">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="e2a1f-215">SignalR 및 Blazor에 대 한 자세한 내용은 다음을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-215">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="e2a1f-216">동일한 서버에 다시 연결</span><span class="sxs-lookup"><span data-stu-id="e2a1f-216">Reconnection to the same server</span></span>

<span data-ttu-id="e2a1f-217">Blazor 서버 앱은 서버에 대 한 활성 SignalR 연결이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-217">Blazor Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="e2a1f-218">연결이 끊어지면 앱이 서버에 다시 연결을 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-218">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="e2a1f-219">클라이언트의 상태가 아직 메모리에 있으면 클라이언트 세션이 상태 손실 없이 다시 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-219">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

<span data-ttu-id="e2a1f-220">클라이언트에서 연결이 끊어진 것을 감지 하면 클라이언트에서 다시 연결을 시도 하는 동안 기본 UI가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-220">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="e2a1f-221">다시 연결이 실패 하는 경우 사용자에 게 다시 시도 하는 옵션이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-221">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="e2a1f-222">UI를 사용자 지정 하려면 *_Host* Razor 페이지에서 `components-reconnect-modal` `id`로 요소를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-222">To customize the UI, define an element with `components-reconnect-modal` as its `id` in the *_Host.cshtml* Razor page.</span></span> <span data-ttu-id="e2a1f-223">클라이언트는 연결 상태에 따라 다음 CSS 클래스 중 하나를 사용 하 여이 요소를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-223">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>

* <span data-ttu-id="e2a1f-224">연결 끊김을 나타내는 UI를 표시 하 고 클라이언트를 다시 연결 하려고 하는 `components-reconnect-show` &ndash; 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-224">`components-reconnect-show` &ndash; Show the UI to indicate a lost connection and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="e2a1f-225">클라이언트에 활성 연결이 &ndash; `components-reconnect-hide` UI를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-225">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="e2a1f-226">네트워크 오류로 인해 `components-reconnect-failed` &ndash; 다시 연결 하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-226">`components-reconnect-failed` &ndash; Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="e2a1f-227">다시 연결을 시도 하려면 `window.Blazor.reconnect()`을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-227">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span>
* <span data-ttu-id="e2a1f-228">`components-reconnect-rejected` &ndash; 다시 연결이 거부 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-228">`components-reconnect-rejected` &ndash; Reconnection rejected.</span></span> <span data-ttu-id="e2a1f-229">서버에 도달 했지만 연결이 거부 되었으며 서버의 사용자 상태가 사라졌습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-229">The server was reached but refused the connection, and the user's state on the server is gone.</span></span> <span data-ttu-id="e2a1f-230">앱을 다시 로드 하려면 `location.reload()`을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-230">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="e2a1f-231">이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-231">This connection state may result when:</span></span>
  * <span data-ttu-id="e2a1f-232">회로 (서버 쪽 코드)의 작동이 중단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-232">A crash in the circuit (server-side code) occurs.</span></span>
  * <span data-ttu-id="e2a1f-233">서버에서 사용자의 상태를 삭제 하기에 충분 한 길이의 클라이언트 연결이 끊겼습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-233">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="e2a1f-234">사용자가 상호 작용 하는 구성 요소의 인스턴스가 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-234">Instances of components that the user was interacting with are disposed.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="e2a1f-235">렌더링 후 상태 저장 다시 연결</span><span class="sxs-lookup"><span data-stu-id="e2a1f-235">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="e2a1f-236">서버에 대 한 클라이언트 연결이 설정 되기 전에 서버에서 UI를 미리 렌더링 하도록 Blazor 서버 앱이 기본적으로 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-236">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="e2a1f-237">이는 *_Host* Razor 페이지에 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-237">This is set up in the *_Host.cshtml* Razor page:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

<span data-ttu-id="e2a1f-238">`RenderMode`은 구성 요소가 있는지 여부를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-238">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="e2a1f-239">는 페이지에 미리 렌더링 된 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-239">Is prerendered into the page.</span></span>
* <span data-ttu-id="e2a1f-240">는 페이지에서 정적 HTML로 렌더링 되거나 사용자 에이전트에서 Blazor 앱을 부트스트랩 하는 데 필요한 정보가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-240">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="e2a1f-241">설명</span><span class="sxs-lookup"><span data-stu-id="e2a1f-241">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="e2a1f-242">구성 요소를 정적 HTML로 렌더링 하 고 Blazor 서버 앱에 대 한 마커를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-242">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e2a1f-243">사용자 에이전트가 시작 되 면이 표식은 Blazor 앱을 부트스트랩 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-243">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="e2a1f-244">Blazor 서버 앱에 대 한 마커를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-244">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e2a1f-245">구성 요소의 출력은 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-245">Output from the component isn't included.</span></span> <span data-ttu-id="e2a1f-246">사용자 에이전트가 시작 되 면이 표식은 Blazor 앱을 부트스트랩 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-246">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="e2a1f-247">구성 요소를 정적 HTML로 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-247">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="e2a1f-248">설명</span><span class="sxs-lookup"><span data-stu-id="e2a1f-248">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="e2a1f-249">구성 요소를 정적 HTML로 렌더링 하 고 Blazor 서버 앱에 대 한 마커를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-249">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e2a1f-250">사용자 에이전트가 시작 되 면이 표식은 Blazor 앱을 부트스트랩 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-250">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="e2a1f-251">매개 변수는 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-251">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="e2a1f-252">Blazor 서버 앱에 대 한 마커를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-252">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e2a1f-253">구성 요소의 출력은 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-253">Output from the component isn't included.</span></span> <span data-ttu-id="e2a1f-254">사용자 에이전트가 시작 되 면이 표식은 Blazor 앱을 부트스트랩 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-254">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="e2a1f-255">매개 변수는 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-255">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="e2a1f-256">구성 요소를 정적 HTML로 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-256">Renders the component into static HTML.</span></span> <span data-ttu-id="e2a1f-257">매개 변수가 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-257">Parameters are supported.</span></span> |

::: moniker-end

<span data-ttu-id="e2a1f-258">정적 HTML 페이지에서 서버 구성 요소를 렌더링 하는 것은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-258">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="e2a1f-259">`RenderMode` `ServerPrerendered`되 면 구성 요소는 초기에 페이지의 일부로 정적으로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-259">When `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="e2a1f-260">브라우저에서 서버로 다시 연결 하면 구성 요소가 *다시*렌더링 되 고 구성 요소가 대화형으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-260">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="e2a1f-261">구성 요소를 초기화 하는 [수명 주기 메서드](xref:blazor/components#lifecycle-methods) (`OnInitialized{Async}`)가 있는 경우 메서드는 *두 번*실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-261">If a [lifecycle method](xref:blazor/components#lifecycle-methods) for initializing the component (`OnInitialized{Async}`) is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="e2a1f-262">구성 요소가 정적으로 미리 렌더링 된 된 경우</span><span class="sxs-lookup"><span data-stu-id="e2a1f-262">When the component is prerendered statically.</span></span>
* <span data-ttu-id="e2a1f-263">서버 연결이 설정 된 후</span><span class="sxs-lookup"><span data-stu-id="e2a1f-263">After the server connection has been established.</span></span>

<span data-ttu-id="e2a1f-264">이로 인해 구성 요소가 최종적으로 렌더링 될 때 UI에 표시 되는 데이터가 크게 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-264">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="e2a1f-265">Blazor 서버 앱에서 이중 렌더링 시나리오를 방지 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-265">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="e2a1f-266">렌더링 중에 상태를 캐시 하 고 앱이 다시 시작 된 후 상태를 검색 하는 데 사용할 수 있는 식별자를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-266">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="e2a1f-267">렌더링 중에 식별자를 사용 하 여 구성 요소 상태를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-267">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="e2a1f-268">렌더링 후 식별자를 사용 하 여 캐시 된 상태를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-268">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="e2a1f-269">다음 코드는 이중 렌더링을 방지 하는 템플릿 기반 Blazor 서버 앱에서 업데이트 된 `WeatherForecastService`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-269">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] Summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            }).ToArray();
        });
    }
}
```

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="e2a1f-270">Razor 페이지 및 뷰에서 상태 저장 대화형 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="e2a1f-270">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="e2a1f-271">상태 저장 대화형 구성 요소는 Razor 페이지 또는 보기에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-271">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="e2a1f-272">페이지 또는 뷰가 렌더링 되는 경우:</span><span class="sxs-lookup"><span data-stu-id="e2a1f-272">When the page or view renders:</span></span>

* <span data-ttu-id="e2a1f-273">구성 요소가 페이지 또는 뷰와 미리 렌더링 된 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-273">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="e2a1f-274">렌더링에 사용 되는 초기 구성 요소 상태가 손실 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-274">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="e2a1f-275">새 구성 요소 상태는 SignalR 연결이 설정 될 때 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-275">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="e2a1f-276">다음 Razor 페이지는 `Counter` 구성 요소를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-276">The following Razor page renders a `Counter` component:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="e2a1f-277">Razor 페이지 및 뷰에서 비 대화형 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="e2a1f-277">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="e2a1f-278">다음 Razor 페이지에서 `MyComponent` 구성 요소는 폼을 사용 하 여 지정 된 초기 값을 사용 하 여 정적으로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-278">In the following Razor page, the `MyComponent` component is statically rendered with an initial value that's specified using a form:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<MyComponent>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

<span data-ttu-id="e2a1f-279">`MyComponent` 정적으로 렌더링 되므로 구성 요소는 대화형이 될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-279">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="e2a1f-280">앱이 사전 렌더링 되는 경우 검색</span><span class="sxs-lookup"><span data-stu-id="e2a1f-280">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="e2a1f-281">Blazor 서버 앱에 대 한 SignalR 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="e2a1f-281">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="e2a1f-282">경우에 따라 Blazor Server 앱에서 사용 하는 SignalR 클라이언트를 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-282">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="e2a1f-283">예를 들어 SignalR 클라이언트에 대 한 로깅을 구성 하 여 연결 문제를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-283">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="e2a1f-284">*Pages/_Host* 파일에서 SignalR client를 구성 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-284">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="e2a1f-285">*Blazor* 스크립트에 대 한 `<script>` 태그에 `autostart="false"` 특성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-285">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="e2a1f-286">`Blazor.start`를 호출 하 고 SignalR builder를 지정 하는 구성 개체를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2a1f-286">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="e2a1f-287">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e2a1f-287">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
