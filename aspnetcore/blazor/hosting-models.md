---
title: ASP.NET Core Blazor 호스팅 모델
author: guardrex
description: Blazor Weasembmbsemboma 및 Blazor Server 호스팅 모델을 이해 합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/31/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 7b4d4aca0bc4650c31bc8e5c4a84ecbad6a49b09
ms.sourcegitcommit: 0e21d4f8111743bcb205a2ae0f8e57910c3e8c25
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77034090"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="c2ae3-103">ASP.NET Core Blazor 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="c2ae3-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="c2ae3-104">[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c2ae3-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="c2ae3-105">Blazor는 [WeasembmbBlazor](https://webassembly.org/)(*Blazor server*) ASP.NET Core의 서버 쪽에서 브라우저에서 클라이언트 쪽을 실행 하도록 디자인 된웹 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="c2ae3-106">호스팅 모델에 관계 없이 앱 및 구성 요소 모델은 *동일*합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="c2ae3-107">이 문서에서 설명 하는 호스팅 모델에 대 한 프로젝트를 만들려면 <xref:blazor/get-started>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="c2ae3-108">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c2ae3-108">Blazor WebAssembly</span></span>

<span data-ttu-id="c2ae3-109">Blazor에 대 한 주 호스팅 모델이 브라우저에서 클라이언트 쪽을 제대로 실행 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="c2ae3-110">Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 브라우저에 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="c2ae3-111">해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="c2ae3-112">UI 업데이트 및 이벤트 처리는 동일한 프로세스 내에서 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="c2ae3-113">응용 프로그램의 자산은 정적 콘텐츠를 클라이언트에 제공할 수 있는 웹 서버 또는 서비스에 정적 파일로 배포 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor Weasemboma: Blazor 앱은 브라우저 내부의 UI 스레드에서 실행 됩니다.](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="c2ae3-115">클라이언트 쪽 호스팅 모델을 사용 하 여 Blazor 앱을 만들려면 **Blazor Weasembomapp** 템플릿 ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new))을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="c2ae3-116">**Blazor WebAssembly 앱** 템플릿을 선택한 후에는 **ASP.NET Core hosted** 확인란을 선택 하 여 ASP.NET Core 백 엔드를 사용 하도록 앱을 구성 하는 옵션이 있습니다 ([dotnet new blazorwasm--호스팅](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="c2ae3-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="c2ae3-117">ASP.NET Core 앱은 Blazor 앱을 클라이언트에 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="c2ae3-118">Blazor Weasembomapps는 web API 호출 또는 [SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>)를 사용 하 여 네트워크를 통해 서버와 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>).</span></span>

<span data-ttu-id="c2ae3-119">템플릿에는를 처리 하는 `blazor.webassembly.js` 스크립트가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-119">The templates include the `blazor.webassembly.js` script that handles:</span></span>

* <span data-ttu-id="c2ae3-120">.NET 런타임, 앱 및 앱의 종속성 다운로드</span><span class="sxs-lookup"><span data-stu-id="c2ae3-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="c2ae3-121">앱을 실행 하기 위한 런타임 초기화</span><span class="sxs-lookup"><span data-stu-id="c2ae3-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="c2ae3-122">Blazor Weasembom호스팅 모델은 몇 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="c2ae3-123">.NET 서버 쪽 종속성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="c2ae3-124">클라이언트에 다운로드 한 후 앱이 완전히 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="c2ae3-125">클라이언트 리소스 및 기능은 완전히 활용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="c2ae3-126">작업은 서버에서 클라이언트로 오프 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="c2ae3-127">ASP.NET Core 웹 서버는 앱을 호스트 하는 데 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="c2ae3-128">서버를 사용 하지 않는 배포 시나리오를 사용할 수 있습니다. 예를 들어 CDN에서 앱을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="c2ae3-129">Blazor Weasembom단점이는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="c2ae3-130">앱은 브라우저의 기능으로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="c2ae3-131">가능 클라이언트 하드웨어 및 소프트웨어 (예: Weasembomsupport)는 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="c2ae3-132">다운로드 크기가 크고 앱을 로드 하는 데 시간이 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="c2ae3-133">.NET 런타임 및 도구 지원의 완성도는 낮아집니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="c2ae3-134">예를 들어 [.NET Standard](/dotnet/standard/net-standard) 지원 및 디버깅에는 제한 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="c2ae3-135">Blazor 서버</span><span class="sxs-lookup"><span data-stu-id="c2ae3-135">Blazor Server</span></span>

<span data-ttu-id="c2ae3-136">Blazor 서버 호스팅 모델을 사용 하면 앱이 ASP.NET Core 앱 내에서 서버에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="c2ae3-137">UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![브라우저는 SignalR 연결을 통해 서버의 앱 (ASP.NET Core 앱 내에서 호스트)과 상호 작용 합니다.](hosting-models/_static/blazor-server.png)

<span data-ttu-id="c2ae3-139">Blazor 서버 호스팅 모델을 사용 하 여 Blazor 앱을 만들려면 ASP.NET Core **Blazor Server 앱** 템플릿 ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new))을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="c2ae3-140">ASP.NET Core 앱은 Blazor Server 앱을 호스팅하고 클라이언트가 연결 하는 SignalR 끝점을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="c2ae3-141">ASP.NET Core 앱은 추가할 앱의 `Startup` 클래스를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="c2ae3-142">서버 쪽 서비스.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-142">Server-side services.</span></span>
* <span data-ttu-id="c2ae3-143">요청 처리 파이프라인에 대 한 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="c2ae3-144">`blazor.server.js` 스크립트&dagger; 클라이언트 연결을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-144">The `blazor.server.js` script&dagger; establishes the client connection.</span></span> <span data-ttu-id="c2ae3-145">필요에 따라 앱 상태를 유지 하 고 복원 하는 것은 앱의 책임입니다 (예: 네트워크 연결이 끊어진 경우).</span><span class="sxs-lookup"><span data-stu-id="c2ae3-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="c2ae3-146">Blazor 서버 호스팅 모델은 몇 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="c2ae3-147">다운로드 크기는 Blazor Weasembomapp 보다 훨씬 더 작기 때문에 앱이 훨씬 더 빠르게 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="c2ae3-148">앱은 .NET Core와 호환 되는 Api의 사용을 포함 하 여 서버 기능을 최대한 활용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="c2ae3-149">서버의 .NET Core는 앱을 실행 하는 데 사용 되므로 디버깅과 같은 기존 .NET 도구는 정상적으로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="c2ae3-150">씬 클라이언트가 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-150">Thin clients are supported.</span></span> <span data-ttu-id="c2ae3-151">예를 들어 Blazor Server 앱은 리소스 제한 장치에서 Weasembmbmbsemboma를 지원 하지 않는 브라우저에서 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="c2ae3-152">앱의 구성 요소 코드C# 를 포함 하 여 앱의 .net/code 베이스가 클라이언트에 제공 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="c2ae3-153">Blazor 서버 호스팅을 단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="c2ae3-154">일반적으로 더 높은 대기 시간이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-154">Higher latency usually exists.</span></span> <span data-ttu-id="c2ae3-155">모든 사용자 상호 작용에는 네트워크 홉이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="c2ae3-156">오프 라인은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-156">There's no offline support.</span></span> <span data-ttu-id="c2ae3-157">클라이언트 연결에 실패 하면 앱 작동이 중지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="c2ae3-158">여러 사용자가 있는 앱의 경우에는 확장성이 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="c2ae3-159">서버는 여러 클라이언트 연결을 관리 하 고 클라이언트 상태를 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="c2ae3-160">앱을 제공 하려면 ASP.NET Core 서버가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="c2ae3-161">서버를 사용 하지 않는 배포 시나리오 (예: CDN에서 앱 제공)를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="c2ae3-162">&dagger;`blazor.server.js` 스크립트는 ASP.NET Core 공유 프레임 워크의 포함 리소스에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-162">&dagger;The `blazor.server.js` script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="c2ae3-163">서버에서 렌더링 된 UI 비교</span><span class="sxs-lookup"><span data-stu-id="c2ae3-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="c2ae3-164">Blazor 서버 앱을 이해 하는 한 가지 방법은 Razor 뷰나 Razor Pages를 사용 하 여 ASP.NET Core 앱에서 UI를 렌더링 하는 일반적인 모델과의 차이점을 이해 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="c2ae3-165">두 모델 모두 Razor 언어를 사용 하 여 HTML 콘텐츠를 설명 하지만 태그가 렌더링 되는 방식에는 크게 차이가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="c2ae3-166">Razor 페이지 또는 뷰가 렌더링 되 면 Razor 코드의 모든 줄은 텍스트 형식으로 HTML을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="c2ae3-167">렌더링 후 서버는 생성 된 상태를 포함 하 여 페이지 또는 뷰 인스턴스를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="c2ae3-168">서버 유효성 검사에 실패 하 고 유효성 검사 요약이 표시 되는 경우와 같은 페이지에 대 한 다른 요청이 발생 하는 경우</span><span class="sxs-lookup"><span data-stu-id="c2ae3-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="c2ae3-169">전체 페이지는 HTML 텍스트로 다시 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="c2ae3-170">페이지가 클라이언트로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-170">The page is sent to the client.</span></span>

<span data-ttu-id="c2ae3-171">Blazor 앱은 *구성 요소*라는 UI의 재사용 가능한 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="c2ae3-172">구성 요소에 C# 는 코드, 태그 및 기타 구성 요소가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="c2ae3-173">구성 요소가 렌더링 되 면 Blazor은 HTML 또는 XML 문서 개체 모델 (DOM)와 유사한 포함 된 구성 요소의 그래프를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="c2ae3-174">이 그래프에는 속성 및 필드에 유지 되는 구성 요소 상태가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-174">This graph includes component state held in properties and fields.</span></span> <span data-ttu-id="c2ae3-175">Blazor는 구성 요소 그래프를 평가 하 여 태그의 이진 표현을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-175">Blazor evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="c2ae3-176">이진 형식은 다음과 같을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-176">The binary format can be:</span></span>

* <span data-ttu-id="c2ae3-177">렌더링 중에 HTML 텍스트로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="c2ae3-178">정기적으로 렌더링 하는 동안 태그를 효율적으로 업데이트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="c2ae3-179">Blazor의 UI 업데이트는 다음에 의해 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="c2ae3-180">사용자 상호 작용 (예: 단추 선택)</span><span class="sxs-lookup"><span data-stu-id="c2ae3-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="c2ae3-181">타이머와 같은 앱 트리거입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="c2ae3-182">그래프가 계산 되며 UI *diff* (차이)가 계산 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="c2ae3-183">이러한 차이는 클라이언트에서 UI를 업데이트 하는 데 필요한 최소한의 DOM 편집 집합입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="c2ae3-184">Diff는 클라이언트에 이진 형식으로 전송 되 고 브라우저에서 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="c2ae3-185">사용자가 클라이언트에서 다른 구성 요소를 탐색 한 후에 구성 요소가 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="c2ae3-186">사용자가 구성 요소와 상호 작용 하는 동안에는 구성 요소의 상태 (서비스, 리소스)가 서버 메모리에 보관 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="c2ae3-187">서버에서 많은 구성 요소의 상태를 동시에 유지 관리할 수 있기 때문에 메모리 소모는 해결 해야 하는 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="c2ae3-188">서버 메모리를 최적으로 사용 하기 위해 Blazor Server 앱을 제작 하는 방법에 대 한 지침은 <xref:security/blazor/server>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="c2ae3-189">Razor Pages 및 MVC 앱에 Razor 구성 요소 통합</span><span class="sxs-lookup"><span data-stu-id="c2ae3-189">Integrate Razor components into Razor Pages and MVC apps</span></span>

#### <a name="use-components-in-pages-and-views"></a><span data-ttu-id="c2ae3-190">페이지 및 뷰에서 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="c2ae3-190">Use components in pages and views</span></span>

<span data-ttu-id="c2ae3-191">기존 Razor Pages 또는 MVC 앱은 Razor 구성 요소를 페이지 및 뷰에 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-191">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="c2ae3-192">앱의 레이아웃 파일 ( *_Layout cshtml*)에서:</span><span class="sxs-lookup"><span data-stu-id="c2ae3-192">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="c2ae3-193">`<head>` 요소에 다음 `<base>` 태그를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-193">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="c2ae3-194">앞의 예제에서 `href` 값 ( *앱 기본 경로*)은 앱이 루트 URL 경로 (`/`)에 있는 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-194">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="c2ae3-195">앱이 하위 응용 프로그램 인 경우 <xref:host-and-deploy/blazor/index#app-base-path> 문서의 *앱 기준 경로* 섹션에 있는 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-195">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="c2ae3-196">*_Layout cshtml* 파일은 Razor Pages 앱의 *Pages/SHARED* 폴더 또는 MVC 앱의 *Views/shared* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-196">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="c2ae3-197">닫는 `</body>` 태그 내에 *blazor* 스크립트에 대 한 `<script>` 태그를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-197">Add a `<script>` tag for the *blazor.server.js* script inside of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="c2ae3-198">프레임 워크는 *blazor* 스크립트를 앱에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-198">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="c2ae3-199">앱에 스크립트를 수동으로 추가할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-199">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="c2ae3-200">다음 콘텐츠를 사용 하 여 프로젝트의 루트 폴더에 *_Imports razor* 파일을 추가 합니다 (마지막 네임 스페이스 `MyAppNamespace`를 앱의 네임 스페이스로 변경).</span><span class="sxs-lookup"><span data-stu-id="c2ae3-200">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="c2ae3-201">`Startup.ConfigureServices`에서 Blazor 서버 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-201">In `Startup.ConfigureServices`, add the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="c2ae3-202">`Startup.Configure`에서 `app.UseEndpoints`에 Blazor Hub 끝점을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-202">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="c2ae3-203">모든 페이지 또는 보기에 구성 요소를 통합 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-203">Integrate components into any page or view.</span></span> <span data-ttu-id="c2ae3-204">자세한 내용은 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> 문서의 *Razor Pages 및 MVC 앱에 구성 요소 통합* 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-204">For more information, see the *Integrate components into Razor Pages and MVC apps* section of the <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> article.</span></span>

#### <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="c2ae3-205">Razor Pages 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="c2ae3-205">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="c2ae3-206">Razor Pages 앱에서 라우팅할 수 있는 Razor 구성 요소를 지원 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-206">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="c2ae3-207">[페이지 및 뷰에서 구성 요소 사용](#use-components-in-pages-and-views) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-207">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="c2ae3-208">다음 콘텐츠를 사용 하 여 프로젝트의 루트에 *응용 프로그램 razor* 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-208">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="c2ae3-209">다음 내용을 사용 하 여 *Pages* 폴더에 *_Host cshtml* 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-209">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="c2ae3-210">구성 요소는 해당 레이아웃에 대해 공유 *_Layout cshtml* 파일을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-210">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="c2ae3-211">_Host에 대 한 우선 순위가 낮은 경로를 `Startup.Configure`의 끝점 구성에 추가 *합니다* .</span><span class="sxs-lookup"><span data-stu-id="c2ae3-211">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="c2ae3-212">라우팅할 수 있는 구성 요소를 앱에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-212">Add routable components to the app.</span></span> <span data-ttu-id="c2ae3-213">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-213">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="c2ae3-214">사용자 지정 폴더를 사용 하 여 앱의 구성 요소를 저장 하는 경우 폴더를 나타내는 네임 스페이스를 *Pages/_ViewImports cshtml* 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-214">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Pages/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="c2ae3-215">자세한 내용은 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-215">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

#### <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="c2ae3-216">MVC 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="c2ae3-216">Use routable components in an MVC app</span></span>

<span data-ttu-id="c2ae3-217">MVC 앱에서 라우팅할 수 있는 Razor 구성 요소를 지원 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-217">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="c2ae3-218">[페이지 및 뷰에서 구성 요소 사용](#use-components-in-pages-and-views) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-218">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="c2ae3-219">다음 콘텐츠를 사용 하 여 프로젝트의 루트에 *응용 프로그램 razor* 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-219">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="c2ae3-220">다음 내용을 사용 하 여 *Views/Home* 폴더에 _Host을 추가 *합니다* .</span><span class="sxs-lookup"><span data-stu-id="c2ae3-220">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="c2ae3-221">구성 요소는 해당 레이아웃에 대해 공유 *_Layout cshtml* 파일을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-221">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="c2ae3-222">홈 컨트롤러에 작업을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-222">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="c2ae3-223">`Startup.Configure`의 끝점 구성에 *_Host. cshtml* 뷰를 반환 하는 컨트롤러 작업에 우선 순위가 낮은 경로를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-223">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="c2ae3-224">*페이지* 폴더를 만들고 라우팅할 수 있는 구성 요소를 앱에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-224">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="c2ae3-225">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-225">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="c2ae3-226">사용자 지정 폴더를 사용 하 여 앱의 구성 요소를 저장 하는 경우 폴더를 나타내는 네임 스페이스를 *Views/_ViewImports cshtml* 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-226">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="c2ae3-227">자세한 내용은 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-227">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

### <a name="circuits"></a><span data-ttu-id="c2ae3-228">배선</span><span class="sxs-lookup"><span data-stu-id="c2ae3-228">Circuits</span></span>

<span data-ttu-id="c2ae3-229">Blazor 서버 앱은 [ASP.NET Core SignalR](xref:signalr/introduction)위에 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-229">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="c2ae3-230">각 클라이언트는 *회로*라는 하나 이상의 SignalR 연결을 통해 서버와 통신 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-230">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="c2ae3-231">회로는 일시적인 네트워크 중단을 허용할 수 있는 SignalR 연결에 대 한 Blazor의 추상화입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-231">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="c2ae3-232">Blazor client에서 SignalR 연결의 연결이 끊어지면 새 SignalR 연결을 사용 하 여 서버에 다시 연결 하려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-232">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="c2ae3-233">Blazor 서버 앱에 연결 된 각 브라우저 화면 (브라우저 탭 또는 iframe)은 SignalR 연결을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-233">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="c2ae3-234">이는 서버에서 렌더링 되는 일반적인 앱에 비해 또 다른 중요 한 차이점입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-234">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="c2ae3-235">서버에서 렌더링 하는 응용 프로그램에서는 여러 브라우저 화면에서 동일한 앱을 여는 것은 일반적으로 서버에서 추가 리소스 요구 사항으로 변환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-235">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="c2ae3-236">Blazor 서버 앱에서 각 브라우저 화면에는 서버에서 관리 해야 하는 별도의 회로와 개별 구성 요소 상태 인스턴스가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-236">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

<span data-ttu-id="c2ae3-237">Blazor는 브라우저 탭을 닫거나 외부 URL로 이동 하는 *정상적인* 종료를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-237">Blazor considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="c2ae3-238">정상적인 종료 시 회로 및 연결 된 리소스가 즉시 해제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-238">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="c2ae3-239">클라이언트는 네트워크 중단으로 인해 안정적이 지 않은 방식으로 연결을 끊을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-239">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> <span data-ttu-id="c2ae3-240">Blazor 서버는 클라이언트가 다시 연결할 수 있도록 구성 가능한 간격 동안 연결이 끊어진 회로를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-240">Blazor Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="c2ae3-241">자세한 내용은 [동일한 서버에](#reconnection-to-the-same-server) 다시 연결 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-241">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="c2ae3-242">UI 대기 시간</span><span class="sxs-lookup"><span data-stu-id="c2ae3-242">UI Latency</span></span>

<span data-ttu-id="c2ae3-243">UI 대기 시간은 시작 된 작업에서 UI를 업데이트 하는 데 걸리는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-243">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="c2ae3-244">UI 대기 시간에 대 한 값이 작을수록 앱이 사용자에 게 반응 하는 데 필수적입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-244">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="c2ae3-245">Blazor 서버 앱에서 각 작업은 서버로 전송 되 고 처리 되며 UI diff가 다시 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-245">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="c2ae3-246">따라서 UI 대기 시간은 네트워크 대기 시간 및 작업 처리의 서버 대기 시간 합계입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-246">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="c2ae3-247">개인 회사 네트워크로 제한 된 lob (기간 업무) 앱의 경우 네트워크 대기 시간으로 인 한 대기 시간에 대 한 사용자의 영향은 일반적으로 imperceptible입니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-247">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="c2ae3-248">인터넷을 통해 배포 된 앱의 경우 특히 사용자가 지리적으로 광범위 하 게 분산 된 경우 대기 시간이 달라질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-248">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="c2ae3-249">메모리 사용은 앱 대기 시간에도 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-249">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="c2ae3-250">메모리 사용이 증가 하면 가비지 수집 또는 페이징 메모리가 디스크에 자주 발생 하므로 앱 성능이 저하 되 고 결과적으로 UI 대기 시간이 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-250">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="c2ae3-251">자세한 내용은 <xref:security/blazor/server>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-251">For more information, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="c2ae3-252">Blazor 서버 앱은 네트워크 대기 시간 및 메모리 사용을 줄여 UI 대기 시간을 최소화 하도록 최적화 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-252">Blazor Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="c2ae3-253">네트워크 대기 시간을 측정 하는 방법에 대 한 자세한 내용은 <xref:host-and-deploy/blazor/server#measure-network-latency>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-253">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="c2ae3-254">SignalR 및 Blazor에 대 한 자세한 내용은 다음을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-254">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a><span data-ttu-id="c2ae3-255">서버에 대 한 연결</span><span class="sxs-lookup"><span data-stu-id="c2ae3-255">Connection to the server</span></span>

<span data-ttu-id="c2ae3-256">Blazor 서버 앱은 서버에 대 한 활성 SignalR 연결이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-256">Blazor Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="c2ae3-257">연결이 끊어지면 앱이 서버에 다시 연결을 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-257">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="c2ae3-258">클라이언트의 상태가 아직 메모리에 있으면 클라이언트 세션이 상태 손실 없이 다시 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-258">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

#### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="c2ae3-259">동일한 서버에 다시 연결</span><span class="sxs-lookup"><span data-stu-id="c2ae3-259">Reconnection to the same server</span></span>

<span data-ttu-id="c2ae3-260">Blazor 서버 앱은 서버에서 UI 상태를 설정 하는 첫 번째 클라이언트 요청에 대 한 응답으로 prerenders.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-260">A Blazor Server app prerenders in response to the first client request, which sets up the UI state on the server.</span></span> <span data-ttu-id="c2ae3-261">클라이언트에서 SignalR 연결을 만들려고 하면 클라이언트는 동일한 서버에 다시 연결 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-261">When the client attempts to create a SignalR connection, the client must reconnect to the same server.</span></span> <span data-ttu-id="c2ae3-262">두 개 이상의 백 엔드 서버를 사용 하는 Blazor Server 앱은 SignalR 연결에 대 한 *고정 세션* 을 구현 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-262">Blazor Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span>

<span data-ttu-id="c2ae3-263">Blazor 서버 앱에 [Azure SignalR Service](/azure/azure-signalr)를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-263">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="c2ae3-264">이 서비스를 사용하면 Blazor 서버 앱을 다수의 동시 SignalR 연결로 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-264">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="c2ae3-265">고정 세션은 서비스의 `ServerStickyMode` 옵션 또는 구성 값을 `Required`로 설정 하 여 Azure SignalR 서비스에 대해 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-265">Sticky sessions are enabled for the Azure SignalR Service by setting the service's `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="c2ae3-266">자세한 내용은 <xref:host-and-deploy/blazor/server#signalr-configuration>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-266">For more information, see <xref:host-and-deploy/blazor/server#signalr-configuration>.</span></span>

<span data-ttu-id="c2ae3-267">IIS를 사용하는 경우 애플리케이션 요청 라우팅을 사용하여 고정 세션을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-267">When using IIS, sticky sessions are enabled with Application Request Routing.</span></span> <span data-ttu-id="c2ae3-268">자세한 내용은 [애플리케이션 요청 라우팅을 사용하여 HTTP 부하 분산](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-268">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="c2ae3-269">UI의 연결 상태를 반영 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-269">Reflect the connection state in the UI</span></span>

<span data-ttu-id="c2ae3-270">클라이언트에서 연결이 끊어진 것을 감지 하면 클라이언트에서 다시 연결을 시도 하는 동안 기본 UI가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-270">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="c2ae3-271">다시 연결이 실패 하는 경우 사용자에 게 다시 시도 하는 옵션이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-271">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="c2ae3-272">UI를 사용자 지정 하려면 _Host의 `<body>`에 `components-reconnect-modal` `id`를 사용 하 여 요소를 정의 *합니다. cshtml* Razor 페이지:</span><span class="sxs-lookup"><span data-stu-id="c2ae3-272">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="c2ae3-273">다음 표에서는 `components-reconnect-modal` 요소에 적용 된 CSS 클래스에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-273">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="c2ae3-274">CSS 클래스</span><span class="sxs-lookup"><span data-stu-id="c2ae3-274">CSS class</span></span>                       | <span data-ttu-id="c2ae3-275">&hellip;를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-275">Indicates&hellip;</span></span> |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | <span data-ttu-id="c2ae3-276">연결이 끊어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-276">A lost connection.</span></span> <span data-ttu-id="c2ae3-277">클라이언트에서 다시 연결 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-277">The client is attempting to reconnect.</span></span> <span data-ttu-id="c2ae3-278">모달을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-278">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="c2ae3-279">서버에 대 한 활성 연결이 다시 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-279">An active connection is re-established to the server.</span></span> <span data-ttu-id="c2ae3-280">모달을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-280">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="c2ae3-281">네트워크 오류로 인해 다시 연결 하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-281">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="c2ae3-282">다시 연결을 시도 하려면 `window.Blazor.reconnect()`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-282">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="c2ae3-283">다시 연결이 거부 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-283">Reconnection rejected.</span></span> <span data-ttu-id="c2ae3-284">서버에 도달 했지만 연결이 거부 되었으며 서버에서 사용자의 상태가 손실 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-284">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="c2ae3-285">앱을 다시 로드 하려면 `location.reload()`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-285">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="c2ae3-286">이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-286">This connection state may result when:</span></span><ul><li><span data-ttu-id="c2ae3-287">서버 쪽 회로의 작동이 중단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-287">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="c2ae3-288">서버에서 사용자의 상태를 삭제 하기에 충분 한 길이의 클라이언트 연결이 끊겼습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-288">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="c2ae3-289">사용자가 상호 작용 하는 구성 요소의 인스턴스가 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-289">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="c2ae3-290">서버가 다시 시작 되거나 앱의 작업자 프로세스가 재활용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-290">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="c2ae3-291">렌더링 후 상태 저장 다시 연결</span><span class="sxs-lookup"><span data-stu-id="c2ae3-291">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="c2ae3-292">서버에 대 한 클라이언트 연결이 설정 되기 전에 서버에서 UI를 미리 렌더링 하도록 Blazor 서버 앱이 기본적으로 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-292">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="c2ae3-293">이는 _Host에 설정 되어 *있습니다. cshtml* Razor 페이지:</span><span class="sxs-lookup"><span data-stu-id="c2ae3-293">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="c2ae3-294">구성 요소가 있는지 여부를 구성 하 `RenderMode` 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-294">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="c2ae3-295">는 페이지에 미리 렌더링 된 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-295">Is prerendered into the page.</span></span>
* <span data-ttu-id="c2ae3-296">는 페이지에서 정적 HTML로 렌더링 되거나 사용자 에이전트에서 Blazor 앱을 부트스트랩 하는 데 필요한 정보가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-296">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="c2ae3-297">Description</span><span class="sxs-lookup"><span data-stu-id="c2ae3-297">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="c2ae3-298">구성 요소를 정적 HTML로 렌더링 하 고 Blazor Server 앱에 대 한 마커를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-298">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="c2ae3-299">사용자 에이전트가 시작 되 면이 마커는 Blazor 앱을 부트스트랩 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-299">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="c2ae3-300">Blazor Server 앱에 대 한 마커를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-300">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="c2ae3-301">구성 요소의 출력은 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-301">Output from the component isn't included.</span></span> <span data-ttu-id="c2ae3-302">사용자 에이전트가 시작 되 면이 마커는 Blazor 앱을 부트스트랩 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-302">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="c2ae3-303">구성 요소를 정적 HTML로 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-303">Renders the component into static HTML.</span></span> |

<span data-ttu-id="c2ae3-304">정적 HTML 페이지에서 서버 구성 요소를 렌더링 하는 것은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-304">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="c2ae3-305">`RenderMode` `ServerPrerendered`되 면 구성 요소는 초기에 페이지의 일부로 정적으로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-305">When `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="c2ae3-306">브라우저에서 서버로 다시 연결 하면 구성 요소가 *다시*렌더링 되 고 구성 요소가 대화형으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-306">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="c2ae3-307">구성 요소를 초기화 하는 [Oninitialized {Async}](xref:blazor/lifecycle#component-initialization-methods) 주기 방법이 있는 경우 메서드는 *두 번*실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-307">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="c2ae3-308">구성 요소가 정적으로 미리 렌더링 된 된 경우</span><span class="sxs-lookup"><span data-stu-id="c2ae3-308">When the component is prerendered statically.</span></span>
* <span data-ttu-id="c2ae3-309">서버 연결이 설정 된 후</span><span class="sxs-lookup"><span data-stu-id="c2ae3-309">After the server connection has been established.</span></span>

<span data-ttu-id="c2ae3-310">이로 인해 구성 요소가 최종적으로 렌더링 될 때 UI에 표시 되는 데이터가 크게 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-310">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="c2ae3-311">Blazor Server 앱에서 이중 렌더링 시나리오를 방지 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-311">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="c2ae3-312">렌더링 중에 상태를 캐시 하 고 앱이 다시 시작 된 후 상태를 검색 하는 데 사용할 수 있는 식별자를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-312">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="c2ae3-313">렌더링 중에 식별자를 사용 하 여 구성 요소 상태를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-313">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="c2ae3-314">렌더링 후 식별자를 사용 하 여 캐시 된 상태를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-314">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="c2ae3-315">다음 코드는 이중 렌더링을 방지 하는 템플릿 기반 Blazor 서버 앱에서 업데이트 된 `WeatherForecastService`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-315">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
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
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="c2ae3-316">Razor 페이지 및 뷰에서 상태 저장 대화형 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="c2ae3-316">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="c2ae3-317">상태 저장 대화형 구성 요소는 Razor 페이지 또는 보기에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-317">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="c2ae3-318">페이지 또는 뷰가 렌더링 되는 경우:</span><span class="sxs-lookup"><span data-stu-id="c2ae3-318">When the page or view renders:</span></span>

* <span data-ttu-id="c2ae3-319">구성 요소가 페이지 또는 뷰와 미리 렌더링 된 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-319">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="c2ae3-320">렌더링에 사용 되는 초기 구성 요소 상태가 손실 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-320">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="c2ae3-321">새 구성 요소 상태는 SignalR 연결이 설정 될 때 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-321">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="c2ae3-322">다음 Razor 페이지는 `Counter` 구성 요소를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-322">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="c2ae3-323">Razor 페이지 및 뷰에서 비 대화형 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="c2ae3-323">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="c2ae3-324">다음 Razor 페이지에서 `Counter` 구성 요소는 폼을 사용 하 여 지정 된 초기 값을 사용 하 여 정적으로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-324">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="c2ae3-325">`MyComponent` 정적으로 렌더링 되므로 구성 요소는 대화형이 될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-325">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="c2ae3-326">앱이 사전 렌더링 되는 경우 검색</span><span class="sxs-lookup"><span data-stu-id="c2ae3-326">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="c2ae3-327">Blazor Server 앱에 대 한 SignalR 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="c2ae3-327">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="c2ae3-328">경우에 따라 Blazor Server 앱에서 사용 하는 SignalR 클라이언트를 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-328">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="c2ae3-329">예를 들어 SignalR 클라이언트에 대 한 로깅을 구성 하 여 연결 문제를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-329">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="c2ae3-330">*Pages/_Host* 파일에서 SignalR 클라이언트를 구성 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-330">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="c2ae3-331">`blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-331">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="c2ae3-332">`Blazor.start`를 호출 하 고 SignalR 빌더를 지정 하는 구성 개체를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2ae3-332">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="c2ae3-333">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="c2ae3-333">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:tutorials/signalr-blazor-webassembly>
