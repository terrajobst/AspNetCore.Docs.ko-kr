---
title: ASP.NET Core SignalR .NET 클라이언트
author: bradygaster
description: ASP.NET Core SignalR .NET 클라이언트에 대한 정보
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/dotnet-client
ms.openlocfilehash: 97c553874cb1e4b678fa0e5cd65074f135193861
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153120"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="1149d-103">ASP.NET Core SignalR .NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="1149d-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="1149d-104">ASP.NET Core SignalR .NET 클라이언트 라이브러리를 사용하면 .NET 앱에서 SignalR 허브와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="1149d-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1149d-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1149d-106">이 문서의 코드 샘플은 ASP.NET Core SignalR .NET 클라이언트를 사용하는 WPF 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="1149d-107">SignalR .NET 클라이언트 패키지 설치하기</span><span class="sxs-lookup"><span data-stu-id="1149d-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="1149d-108">.NET 클라이언트에서 SignalR 허브에 연결하려면 `Microsoft.AspNetCore.SignalR.Client` 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-108">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="1149d-109">클라이언트 라이브러리를 설치하려면 **패키지 관리자 콘솔** 창에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-109">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="1149d-110">허브에 연결하기</span><span class="sxs-lookup"><span data-stu-id="1149d-110">Connect to a hub</span></span>

<span data-ttu-id="1149d-111">연결을 설정하려면 `HubConnectionBuilder`를 생성하고 `Build`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-111">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="1149d-112">연결을 만드는 동안 허브 URL, 프로토콜, 전송 형태, 로그 수준, 헤더 및 기타 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-112">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="1149d-113">`HubConnectionBuilder` 메서드들 중 원하는 메서드를 `Build`에 삽입하여 필요한 모든 옵션을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-113">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="1149d-114">`StartAsync`를 사용하여 연결을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-114">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="1149d-115">연결 해제 처리하기</span><span class="sxs-lookup"><span data-stu-id="1149d-115">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="1149d-116">자동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="1149d-116">Automatically reconnect</span></span>

<span data-ttu-id="1149d-117"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> 자동으로 사용 하 여 다시 연결 하도록 구성할 수 있습니다 합니다 `WithAutomaticReconnect` 메서드는 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-117">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="1149d-118">기본적으로 다시 자동으로 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-118">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="1149d-119">모든 매개 변수 없이 `WithAutomaticReconnect()` 4 실패 한 시도 후 각 다시 연결 시도 전에 각각 0, 2, 10 일 및 30 초를 기다리고 클라이언트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-119">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="1149d-120">다시 연결 시도 시작 하기 전에 `HubConnection` 전환 됩니다 합니다 `HubConnectionState.Reconnecting` 상태 및 실행을 `Reconnecting` 이벤트.</span><span class="sxs-lookup"><span data-stu-id="1149d-120">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="1149d-121">이 연결 손실 되었음을 사용자에 게 경고 하는 데 UI 요소를 사용 하지 않도록 설정할 수 있는 기회를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-121">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="1149d-122">비 대화형 응용 프로그램 큐 또는 메시지 삭제를 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-122">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="1149d-123">클라이언트는 먼저 4 회 성공적으로 다시 연결 하는 경우는 `HubConnection` 으로 다시 전환 됩니다.는 `Connected` 상태 및 실행을 `Reconnected` 이벤트.</span><span class="sxs-lookup"><span data-stu-id="1149d-123">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="1149d-124">이 연결이 다시 설정 하 고 모든 대기 중인된 메시지를 큐에서 제거 하는 사용자에 게 알리기 기회를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-124">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="1149d-125">연결 서버에 완전히 새로운 표시 하므로 새 `ConnectionId` 에 제공 됩니다는 `Reconnected` 이벤트 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-125">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="1149d-126">`Reconnected` 이벤트 처리기 `connectionId` 매개 변수는 null이 됩니다 경우 합니다 `HubConnection` 하도록 구성 된 [협상을 건너뛸](xref:signalr/configuration#configure-client-options)합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-126">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="1149d-127">`WithAutomaticReconnect()` 구성지 않습니다는 `HubConnection` 시작 실패를 수동으로 처리 해야 하므로 초기 시작 실패를 다시 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-127">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

<span data-ttu-id="1149d-128">클라이언트 하지 해당 처음 네 개의 시도 내에서 성공적으로 다시 연결 하는 경우는 `HubConnection` 전환 됩니다 합니다 `Disconnected` 상태 및 실행을 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 이벤트.</span><span class="sxs-lookup"><span data-stu-id="1149d-128">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="1149d-129">이 연결을 수동으로 다시 시작 하거나 연결이 영구적으로 손실 된 사용자에 게 알리기 기회를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-129">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="1149d-130">사용자 지정 연결을 끊기 전에 다시 연결 시도 횟수를 구성 하거나 다시 연결 시간을 변경 하려면 `WithAutomaticReconnect` 각 다시 연결 시도 시작 하기 전에 대기할 밀리초의 지연을 나타내는 숫자 배열을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-130">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="1149d-131">앞의 예제 구성는 `HubConnection` 연결이 끊어진 후에 즉시 다시 연결 시도 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-131">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="1149d-132">기본 구성에는이 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-132">This is also true for the default configuration.</span></span>

<span data-ttu-id="1149d-133">첫 번째 다시 연결 시도가 실패 하면 기본 구성이 있는 것 처럼 2 초 대기 하는 대신 두 번째 다시 연결 시도가 즉시 시작도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-133">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="1149d-134">두 번째 다시 연결 시도가 실패 하면 세 번째 다시 연결 시도 기본 구성 등 다시 10 초 후에 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-134">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="1149d-135">사용자 지정 동작은 다음 모델과 다시 기본 동작에서 세 번째 다시 연결 시도 실패 후 중지.</span><span class="sxs-lookup"><span data-stu-id="1149d-135">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="1149d-136">여기에 기본 구성을 수 하나 더 다시 연결 다른 30 초 후에 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-136">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="1149d-137">자동의 수와 타이밍을 보다 잘 제어할 다시 시도 하려는 경우 `WithAutomaticReconnect` 구현 하는 개체를 허용 합니다 `IRetryPolicy` 라는 단일 메서드가 있는 인터페이스 `NextRetryDelay`합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-137">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="1149d-138">`NextRetryDelay` 형식의 단일 인수 `RetryContext`합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-138">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="1149d-139">`RetryContext` 세 가지 속성이 있습니다: `PreviousRetryCount`, `ElapsedTime` 하 고 `RetryReason` 되는 `long`, `TimeSpan` 및 `Exception` 각각.</span><span class="sxs-lookup"><span data-stu-id="1149d-139">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason` which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="1149d-140">첫 번째 다시 연결 시도 하기 전에 둘 다 `PreviousRetryCount` 하 고 `ElapsedTime` 수는 0, 및 `RetryReason` 연결이 손실 되도록 발생 시킨 예외 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-140">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="1149d-141">각 실패 한 시도 후 `PreviousRetryCount` 씩 증가 됩니다 `ElapsedTime` 지금 다시 연결 하는 데 걸린 시간을 반영 하도록 업데이트 됩니다 및 `RetryReason` 마지막 다시 연결 시도 실패를 발생 시킨 예외 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-141">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="1149d-142">`NextRetryDelay` 두 TimeSpan 시간을 나타내는 다음 다시 연결 시도 하기 전에 대기할 반환 해야 합니다 또는 `null` 경우는 `HubConnection` 다시 연결을 중지 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-142">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.Next() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

<span data-ttu-id="1149d-143">또는 클라이언트에서 설명한 것 처럼 수동으로 다시 연결 하는 코드를 작성할 수 있습니다 [수동으로 다시](#manually-reconnect)입니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-143">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="1149d-144">수동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="1149d-144">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="1149d-145">3\.0 이전 SignalR에 대 한.NET 클라이언트 하지 자동으로 다시 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-145">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="1149d-146">클라이언트에 수동으로 다시 연결 하는 코드를 작성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-146">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="1149d-147"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 이벤트를 이용해서 끊긴 연결에 대응합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-147">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="1149d-148">예를 들어 자동으로 재연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-148">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="1149d-149">`Closed` 이벤트에는 `async void`를 사용하지 않고 비동기 코드를 실행할 수 있는 `Task`를 반환하는 대리자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-149">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="1149d-150">동기적으로 실행되는 `Closed` 이벤트 처리기에서 대리자의 시그니처를 만족시키려면 `Task.CompletedTask`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-150">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="1149d-151">비동기를 지원하는 가장 큰 이유는 연결을 다시 시작할 수도 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-151">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="1149d-152">연결 시작은 비동기 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-152">Starting a connection is an async action.</span></span>

<span data-ttu-id="1149d-153">연결을 재시작하는 `Closed` 처리기에서는 다음 예제에서 볼 수 있는 것처럼 서버의 과부하를 방지할 수 있도록 임의의 지연 시간 동안 대기하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-153">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="1149d-154">클라이언트에서 허브 메서드 호출하기</span><span class="sxs-lookup"><span data-stu-id="1149d-154">Call hub methods from client</span></span>

<span data-ttu-id="1149d-155">`InvokeAsync`는 허브 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-155">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="1149d-156">허브 메서드의 이름과 허브 메서드에 정의된 모든 인수를 `InvokeAsync`에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-156">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="1149d-157">SignalR은 비동기로 동작하므로 호출 시 `async`와 `await`를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-157">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="1149d-158">합니다 `InvokeAsync` 메서드가 반환 되는 `Task` 서버 메서드가 반환 될 때 완료 되는 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-158">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="1149d-159">반환 값에 있는 경우 제공 됩니다 결과로 `Task`합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-159">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="1149d-160">서버에서 메서드에 의해 throw 된 예외 생성 오류가 발생 한 `Task`합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-160">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="1149d-161">사용 하 여 `await` 서버 메서드가 완료 될 때까지 기다리는 구문 및 `try...catch` 구문 오류를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-161">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="1149d-162">`SendAsync` 메서드가 반환 되는 `Task` 메시지 서버에 전송 되었을 때 완료 되는.</span><span class="sxs-lookup"><span data-stu-id="1149d-162">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="1149d-163">반환 값이 제공 되지 아니므로 `Task` 서버 메서드가 완료 될 때까지 대기 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-163">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="1149d-164">메시지를 전송 하는 동안 클라이언트에서 throw 된 예외 생성 오류가 발생 한 `Task`합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-164">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="1149d-165">사용 하 여 `await` 고 `try...catch` 처리 하는 구문 오류를 전송 합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-165">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="1149d-166">Azure SignalR Service를 사용 하는 경우 *서버 리스 모드*, 클라이언트에서 허브 메서드를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-166">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="1149d-167">자세한 내용은 참조는 [SignalR Service 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-167">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="1149d-168">허브에서 클라이언트 메서드 호출하기</span><span class="sxs-lookup"><span data-stu-id="1149d-168">Call client methods from hub</span></span>

<span data-ttu-id="1149d-169">연결을 만든 다음, 그러나 연결을 시작하기 전에 `connection.On`을 이용해서 허브가 호출할 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-169">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="1149d-170">위의 `connection.On` 내부의 코드는 서버 쪽 코드에서 `SendAsync` 메서드를 사용하여 이 코드를 호출할 때 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-170">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="1149d-171">오류 처리 및 로깅</span><span class="sxs-lookup"><span data-stu-id="1149d-171">Error handling and logging</span></span>

<span data-ttu-id="1149d-172">try-catch 문을 이용해서 오류를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-172">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="1149d-173">`Exception` 개체를 검사해서 오류가 발생한 후 수행할 적절한 동작을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="1149d-173">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="1149d-174">추가 자료</span><span class="sxs-lookup"><span data-stu-id="1149d-174">Additional resources</span></span>

* [<span data-ttu-id="1149d-175">허브</span><span class="sxs-lookup"><span data-stu-id="1149d-175">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="1149d-176">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="1149d-176">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="1149d-177">Azure에 게시하기</span><span class="sxs-lookup"><span data-stu-id="1149d-177">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="1149d-178">Azure SignalR Service 서버 리스 설명서</span><span class="sxs-lookup"><span data-stu-id="1149d-178">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
