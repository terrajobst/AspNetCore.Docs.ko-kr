---
title: SignalR HubContext
author: bradygaster
description: ASP.NET Core SignalR HubContext 서비스를 사용해서 허브의 외부에서 클라이언트에 알림을 전송하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2018
uid: signalr/hubcontext
ms.openlocfilehash: 7ec52d4711fc191dcb83120cf54b1dc28c41f947
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894480"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="7a31c-103">허브 외부에서 메시지 전송하기</span><span class="sxs-lookup"><span data-stu-id="7a31c-103">Send messages from outside a hub</span></span>

<span data-ttu-id="7a31c-104">작성자: [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="7a31c-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="7a31c-105">SignalR 허브는 SignalR 서버에 연결하는 클라이언트에 메시지를 전송하기 위한 핵심 추상화입니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="7a31c-106">`IHubContext` 서비스를 이용해서 앱의 다른 위치에서 메시지를 전송할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="7a31c-107">이 문서에서는 클라이언트로 알림을 전송하기 위해 허브의 외부에서 SignalR `IHubContext`에 접근하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="7a31c-108">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="7a31c-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="7a31c-109">`IHubContext` 인스턴스 가져오기</span><span class="sxs-lookup"><span data-stu-id="7a31c-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="7a31c-110">ASP.NET Core SignalR에서는 종속성 주입을 통해서 `IHubContext`의 인스턴스에 접근할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="7a31c-111">컨트롤러, 미들웨어 또는 다른 DI 서비스에 `IHubContext`의 인스턴스를 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="7a31c-112">이 인스턴스를 사용해서 클라이언트에 메시지를 전송합니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="7a31c-113">이 점이 `IHubContext`에 대한 접근을 제공하기 위해 GlobalHost를 사용하는 ASP.NET 4.x SignalR과 다른 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="7a31c-114">ASP.NET Core는 이런 전역 싱글톤이 필요 없는 종속성 주입 프레임워크를 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="7a31c-115">컨트롤러에서 `IHubContext` 인스턴스 주입하기</span><span class="sxs-lookup"><span data-stu-id="7a31c-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="7a31c-116">생성자에 `IHubContext`를 추가하여 컨트롤러에 인스턴스를 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="7a31c-117">이제 `IHubContext`의 인스턴스를 사용해서 허브 자체에 위치한 것처럼 허브 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="7a31c-118">미들웨어에서 `IHubContext` 인스턴스 가져오기</span><span class="sxs-lookup"><span data-stu-id="7a31c-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="7a31c-119">미들웨어 파이프라인 내부에서는 다음과 같이 `IHubContext`에 접근합니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<MyHub>>();
    //...
});
```

> [!NOTE]
> <span data-ttu-id="7a31c-120">허브 메서드가 `Hub` 클래스의 외부에서 호출되는 경우에는 해당 호출에 대한 호출자가 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="7a31c-121">따라서 `ConnectionId`, `Caller` 및 `Others` 속성에는 접근할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="7a31c-122">강력한 형식의 HubContext 삽입</span><span class="sxs-lookup"><span data-stu-id="7a31c-122">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="7a31c-123">허브에서 상속 되도록 강력한 HubContext 삽입할 `Hub<T>`합니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-123">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="7a31c-124">사용 하 여 삽입 된 `IHubContext<THub, T>` 인터페이스 대신 `IHubContext<THub>`합니다.</span><span class="sxs-lookup"><span data-stu-id="7a31c-124">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(message);
    }
}
```

## <a name="related-resources"></a><span data-ttu-id="7a31c-125">관련 참고 자료</span><span class="sxs-lookup"><span data-stu-id="7a31c-125">Related resources</span></span>

* [<span data-ttu-id="7a31c-126">시작</span><span class="sxs-lookup"><span data-stu-id="7a31c-126">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="7a31c-127">허브</span><span class="sxs-lookup"><span data-stu-id="7a31c-127">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="7a31c-128">Azure에 게시하기</span><span class="sxs-lookup"><span data-stu-id="7a31c-128">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
