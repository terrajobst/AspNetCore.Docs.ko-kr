---
title: 백그라운드 서비스에서 ASP.NET Core SignalR 호스트
author: bradygaster
description: .NET Core BackgroundService 클래스에서 SignalR 클라이언트에 메시지를 보내는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: dcd62f0c7056a3f987291b6c8bb8b87f94160865
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087750"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="43098-103">백그라운드 서비스에서 ASP.NET Core SignalR 호스트</span><span class="sxs-lookup"><span data-stu-id="43098-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="43098-104">[Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="43098-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="43098-105">이 문서에서는 대 한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="43098-105">This article provides guidance for:</span></span>

* <span data-ttu-id="43098-106">ASP.NET Core를 사용 하 여 호스트 되는 백그라운드 작업자 프로세스를 사용 하 여 SignalR 허브를 호스팅합니다.</span><span class="sxs-lookup"><span data-stu-id="43098-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="43098-107">.NET Core 내에서 클라이언트 연결에 메시지를 보낼 [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)합니다.</span><span class="sxs-lookup"><span data-stu-id="43098-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="43098-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="43098-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="wire-up-signalr-during-startup"></a><span data-ttu-id="43098-109">시작 하는 동안 SignalR 연결</span><span class="sxs-lookup"><span data-stu-id="43098-109">Wire up SignalR during startup</span></span>

<span data-ttu-id="43098-110">백그라운드 작업자 프로세스의 컨텍스트에서 ASP.NET Core SignalR 허브를 호스팅하는 허브를 ASP.NET Core 웹 앱을 호스트 하는 데는 것과 결과가 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="43098-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="43098-111">에 `Startup.ConfigureServices` 메서드를 호출 `services.AddSignalR` SignalR을 지원 하도록 ASP.NET Core DI (종속성 주입) 계층에 필요한 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="43098-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="43098-112">`Startup.Configure`, `UseSignalR` 메서드는 ASP.NET Core 요청 파이프라인에서 허브 끝점 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="43098-112">In `Startup.Configure`, the `UseSignalR` method is called to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

<span data-ttu-id="43098-113">앞의 예제에는 `ClockHub` 구현 클래스는 `Hub<T>` 강력한 형식의 Hub를 만드는 클래스.</span><span class="sxs-lookup"><span data-stu-id="43098-113">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="43098-114">합니다 `ClockHub` 에서 구성 된 합니다 `Startup` 끝점에 요청에 응답 하는 클래스 `/hubs/clock`합니다.</span><span class="sxs-lookup"><span data-stu-id="43098-114">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="43098-115">강력한 형식의 허브에 대 한 자세한 내용은 참조 하세요. [SignalR에서 허브를 사용 하 여 ASP.NET Core에 대 한](xref:signalr/hubs#strongly-typed-hubs)합니다.</span><span class="sxs-lookup"><span data-stu-id="43098-115">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="43098-116">이 기능에는 제한 되지 않습니다.는 [허브\<T >](xref:Microsoft.AspNetCore.SignalR.Hub`1) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="43098-116">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="43098-117">상속 되는 모든 클래스 [허브](xref:Microsoft.AspNetCore.SignalR.Hub)와 같은 [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), 에서도 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="43098-117">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="43098-118">사용 하는 인터페이스는 강력한 형식의 `ClockHub` 되는 `IClock` 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="43098-118">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="43098-119">백그라운드 서비스에서 SignalR 허브를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="43098-119">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="43098-120">시작 하는 동안 합니다 `Worker` 클래스를 `BackgroundService`를 사용 하 여 연결 하는 `AddHostedService`합니다.</span><span class="sxs-lookup"><span data-stu-id="43098-120">During startup, the `Worker` class, a `BackgroundService`, is wired up using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="43098-121">SignalR 동안 유선 수도 있으므로 합니다 `Startup` 단계를 각 허브에 연결 된 ASP.NET Core의 HTTP 요청 파이프라인에서 개별 끝점을 각 허브 표시 됩니다는 `IHubContext<T>` 서버의.</span><span class="sxs-lookup"><span data-stu-id="43098-121">Since SignalR is also wired up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="43098-122">ASP.NET Core를 사용 하 여 DI 기능을 다른 클래스와 같은 호스팅 계층에서 인스턴스화된 `BackgroundService` 클래스, MVC 컨트롤러 클래스 또는 Razor 페이지 모델의 인스턴스를 그대로 사용 하 여 서버 쪽 허브에 대 한 참조를 가져올 수 있습니다 `IHubContext<ClockHub, IClock>` 생성 중입니다.</span><span class="sxs-lookup"><span data-stu-id="43098-122">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="43098-123">로 `ExecuteAsync` 메서드가 백그라운드 서비스에서 반복적으로 호출 되는 서버의 현재 날짜 및 시간 보내집니다를 사용 하 여 연결 된 클라이언트에는 `ClockHub`합니다.</span><span class="sxs-lookup"><span data-stu-id="43098-123">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="43098-124">백그라운드 서비스를 사용 하 여 SignalR 이벤트에 대응</span><span class="sxs-lookup"><span data-stu-id="43098-124">React to SignalR events with background services</span></span>

<span data-ttu-id="43098-125">사용 하 여 SignalR 또는.NET 데스크톱 앱에서 수행할 수에 대 한 JavaScript 클라이언트를 사용 하 여 단일 페이지 앱 같은 합니다 <xref:signalr/dotnet-client>, `BackgroundService` 또는 `IHostedService` 구현 SignalR 허브에 연결 하 고 이벤트에 응답을 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43098-125">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="43098-126">`ClockHubClient` 클래스 모두 구현 합니다 `IClock` 인터페이스 및 `IHostedService` 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="43098-126">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="43098-127">하는 동안 구성 연결 될 수 있습니다 이러한 방식으로 `Startup` 지속적으로 실행 하 여 서버에서 허브 이벤트에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="43098-127">This way it can be wired up during `Startup` to run continuously and respond to Hub events from the server.</span></span> 

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="43098-128">초기화 하는 동안 합니다 `ClockHubClient` 의 인스턴스를 만듭니다를 `HubConnection` 를 합니다 `IClock.ShowTime` 허브에 대 한 메서드를 처리기로 `ShowTime` 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="43098-128">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and wires up the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="43098-129">에 `IHostedService.StartAsync` 구현을 `HubConnection` 비동기적으로 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43098-129">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="43098-130">중 합니다 `IHostedService.StopAsync` 메서드는 `HubConnection` 비동기적으로 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43098-130">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="43098-131">추가 자료</span><span class="sxs-lookup"><span data-stu-id="43098-131">Additional resources</span></span>

* [<span data-ttu-id="43098-132">시작</span><span class="sxs-lookup"><span data-stu-id="43098-132">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="43098-133">허브</span><span class="sxs-lookup"><span data-stu-id="43098-133">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="43098-134">Azure에 게시하기</span><span class="sxs-lookup"><span data-stu-id="43098-134">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="43098-135">강력한 형식의 허브</span><span class="sxs-lookup"><span data-stu-id="43098-135">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
