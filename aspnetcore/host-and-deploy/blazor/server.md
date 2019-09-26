---
title: ASP.NET Core Blazor 서버 호스트 및 배포
author: guardrex
description: ASP.NET Core를 사용하여 Blazor 서버 앱을 호스트 및 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: host-and-deploy/blazor/server
ms.openlocfilehash: aedef7fe695dd4a0cbf04d3f3e9947f33f7afa40
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211612"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="f4954-103">Blazor 서버 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="f4954-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="f4954-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f4954-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="f4954-105">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="f4954-105">Host configuration values</span></span>

<span data-ttu-id="f4954-106">[Blazor 서버 앱](xref:blazor/hosting-models#blazor-server)은 [일반 호스트 구성 값](xref:fundamentals/host/generic-host#host-configuration)을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="f4954-107">배포</span><span class="sxs-lookup"><span data-stu-id="f4954-107">Deployment</span></span>

<span data-ttu-id="f4954-108">[Blazor 서버 호스팅 모델](xref:blazor/hosting-models#blazor-server)을 사용하면 Blazor가 서버의 ASP.NET Core 앱 내에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="f4954-109">UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="f4954-110">ASP.NET Core 앱을 호스팅할 수 있는 웹 서버가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="f4954-111">Visual Studio에는 **Blazor 서버 앱** 프로젝트 템플릿([dotnet new](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorserverside` 템플릿)이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="f4954-112">확장성</span><span class="sxs-lookup"><span data-stu-id="f4954-112">Scalability</span></span>

<span data-ttu-id="f4954-113">Blazor 서버 앱에 사용 가능한 인프라를 최대한 활용하도록 배포를 계획합니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="f4954-114">Blazor 서버 앱 확장성을 해결하려면 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f4954-114">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="f4954-115">Blazor 서버 앱 기본 사항</span><span class="sxs-lookup"><span data-stu-id="f4954-115">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="f4954-116">배포 서버</span><span class="sxs-lookup"><span data-stu-id="f4954-116">Deployment server</span></span>

<span data-ttu-id="f4954-117">단일 서버의 확장성(규모 확대)을 고려할 때 앱에 사용할 수 있는 메모리는 사용자 요구가 늘어남에 따라 앱이 소진하는 첫 번째 리소스일 가능성이 높습니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="f4954-118">서버에서 사용 가능한 메모리는 다음 사항에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="f4954-119">서버가 지원할 수 있는 활성 회로 수</span><span class="sxs-lookup"><span data-stu-id="f4954-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="f4954-120">클라이언트의 UI 대기 시간</span><span class="sxs-lookup"><span data-stu-id="f4954-120">UI latency on the client.</span></span>

<span data-ttu-id="f4954-121">안전하고 확장 가능한 Blazor 서버 앱을 빌드하는 방법에 대한 지침은 <xref:security/blazor/server>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f4954-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="f4954-122">각 회로는 최소 *Hello World*와 같은 앱에 약 250KB의 메모리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="f4954-123">회로의 크기는 앱의 코드 및 각 구성 요소와 연결된 상태 유지 관리 요구 사항에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="f4954-124">애플리케이션 및 인프라를 개발하는 도중에 리소스 요구를 측정하는 것이 좋지만, 다음 기준은 배포 대상을 계획할 때 출발점으로 삼을 수 있습니다. 앱에서 5,000명의 동시 사용자를 지원할 것으로 예상되는 경우 앱에 최소 1.3GB(또는 사용자당 273KB)의 서버 메모리를 예산하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a><span data-ttu-id="f4954-125">SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="f4954-125">SignalR configuration</span></span>

<span data-ttu-id="f4954-126">Blazor 서버 앱은 ASP.NET Core SignalR을 사용하여 브라우저와 통신합니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="f4954-127">[SignalR의 호스팅 및 크기 조정 조건](xref:signalr/publish-to-azure-web-app)이 Blazor 서버 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="f4954-128">Blazor는 짧은 대기 시간, 안정성 및 [보안](xref:signalr/security) 덕분에 WebSocket을 SignalR 전송으로 사용하는 경우에 가장 효과적입니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-128">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="f4954-129">WebSocket을 사용할 수 없거나 앱이 긴 폴링을 사용하도록 명시적으로 구성된 경우 SignalR에서 긴 폴링을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="f4954-130">Azure App Service에 배포하는 경우 서비스에 대한 Azure Portal 설정에서 WebSocket을 사용하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="f4954-131">Azure App Service에 대해 앱을 구성하는 방법에 대한 자세한 내용은 [SignalR 게시 지침](xref:signalr/publish-to-azure-web-app)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f4954-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

<span data-ttu-id="f4954-132">Blazor 서버 앱에 [Azure SignalR Service](/azure/azure-signalr)를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-132">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="f4954-133">이 서비스를 사용하면 Blazor 서버 앱을 다수의 동시 SignalR 연결로 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-133">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="f4954-134">또한 SignalR 서비스의 글로벌 및 고성능 데이터 센터는 지리적 위치로 인한 대기 시간을 줄이는 데 큰 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-134">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="f4954-135">네트워크 대기 시간 측정</span><span class="sxs-lookup"><span data-stu-id="f4954-135">Measure network latency</span></span>

<span data-ttu-id="f4954-136">[JS interop](xref:blazor/javascript-interop)은 다음 예제와 같이 네트워크 대기 시간을 측정하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-136">[JS interop](xref:blazor/javascript-interop) can be used to measure network latency, as the following example demonstrates:</span></span>

```cshtml
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="f4954-137">적절한 UI 환경을 위해 UI 대기 시간을 250ms 이하로 유지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f4954-137">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
