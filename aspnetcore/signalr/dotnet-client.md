---
title: ASP.NET Core SignalR .NET 클라이언트
author: bradygaster
description: .NET 클라이언트 SignalR ASP.NET Core에 대 한 정보
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
no-loc:
- SignalR
uid: signalr/dotnet-client
ms.openlocfilehash: a9583c9d6df52ff81a402df03e663ccc3847e51f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652131"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="47ba4-103">ASP.NET Core SignalR .NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="47ba4-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="47ba4-104">ASP.NET Core SignalR .NET 클라이언트 라이브러리를 사용하면 .NET 앱에서 SignalR 허브와 통신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="47ba4-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="47ba4-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="47ba4-106">이 문서의 코드 샘플은 ASP.NET Core SignalR .NET 클라이언트를 사용하는 WPF 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="47ba4-107">SignalR .NET 클라이언트 패키지 설치하기</span><span class="sxs-lookup"><span data-stu-id="47ba4-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="47ba4-108">[AspNetCore SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 패키지는 .Net 클라이언트가 SignalR hubs에 연결 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="47ba4-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47ba4-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="47ba4-110">클라이언트 라이브러리를 설치 하려면 **패키지 관리자 콘솔** 창에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[<span data-ttu-id="47ba4-111">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="47ba4-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="47ba4-112">클라이언트 라이브러리를 설치 하려면 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-112">To install the client library, run the following command in a command shell:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="47ba4-113">허브에 연결하기</span><span class="sxs-lookup"><span data-stu-id="47ba4-113">Connect to a hub</span></span>

<span data-ttu-id="47ba4-114">연결을 설정 하려면 `HubConnectionBuilder` 만들고 `Build`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="47ba4-115">연결을 만드는 동안 허브 URL, 프로토콜, 전송 형태, 로그 수준, 헤더 및 기타 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="47ba4-116">`HubConnectionBuilder` 메서드를 `Build`에 삽입 하 여 필요한 옵션을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="47ba4-117">`StartAsync`와의 연결을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="47ba4-118">연결 해제 처리하기</span><span class="sxs-lookup"><span data-stu-id="47ba4-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="47ba4-119">자동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="47ba4-119">Automatically reconnect</span></span>

<span data-ttu-id="47ba4-120"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>에서 `WithAutomaticReconnect` 메서드를 사용 하 여 자동으로 다시 연결 하도록 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="47ba4-121">기본적으로 자동으로 다시 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="47ba4-122">매개 변수를 사용 하지 않으면 `WithAutomaticReconnect()` 각 다시 연결 시도를 시도 하기 전에 각각 0, 2, 10 및 30 초 동안 대기 하도록 클라이언트를 구성 하 고 실패 한 네 번 시도 후 중지 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="47ba4-123">다시 연결 시도를 시작 하기 전에 `HubConnection` `HubConnectionState.Reconnecting` 상태로 전환 되 고 `Reconnecting` 이벤트를 발생 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="47ba4-124">이렇게 하면 연결이 손실 되었음을 사용자에 게 경고 하 고 UI 요소를 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="47ba4-125">비 대화형 앱은 메시지 큐를 시작 하거나 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="47ba4-126">클라이언트가 처음 네 번의 시도 내에서 다시 연결 하는 경우 `HubConnection`는 다시 `Connected` 상태로 전환 되 고 `Reconnected` 이벤트를 발생 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="47ba4-127">이렇게 하면 연결을 다시 설정 하 고 대기 중인 모든 메시지를 큐에서 제거 하는 사용자에 게 알릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="47ba4-128">연결이 서버를 완전히 처음으로 표시 하기 때문에 `Reconnected` 이벤트 처리기에 새 `ConnectionId` 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="47ba4-129">`HubConnection` [협상을 건너뛰도록](xref:signalr/configuration#configure-client-options)구성 된 경우 `Reconnected` 이벤트 처리기의 `connectionId` 매개 변수는 null입니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="47ba4-130">`WithAutomaticReconnect()` 초기 시작 오류를 다시 시도 하도록 `HubConnection` 구성 하지 않으므로 시작 오류는 수동으로 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="47ba4-131">클라이언트가 처음 네 번의 시도에서 성공적으로 다시 연결 되지 않으면 `HubConnection` `Disconnected` 상태로 전환 되 고 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 이벤트를 발생 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="47ba4-132">이를 통해 연결을 수동으로 다시 시작 하거나 연결이 영구적으로 손실 되었음을 사용자에 게 알릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="47ba4-133">연결을 끊거나 다시 연결 하는 시간을 변경 하기 전에 사용자 지정 횟수의 다시 연결 시도를 구성 하려면 각 다시 연결 시도를 시작 하기 전에 대기할 지연 시간 (밀리초)을 나타내는 숫자 배열을 허용 `WithAutomaticReconnect` 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="47ba4-134">앞의 예제에서는 연결이 끊긴 후 즉시 다시 연결 시도를 시작 하도록 `HubConnection`를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="47ba4-135">기본 구성의 경우에도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="47ba4-136">첫 번째 다시 연결 시도가 실패 하면 두 번째 다시 연결 시도도 기본 구성에서와 같이 2 초 동안 대기 하는 대신 즉시 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="47ba4-137">두 번째 다시 연결 시도가 실패 하면 세 번째 다시 연결 시도가 10 초 후에 다시 시작 됩니다 .이는 기본 구성 처럼 다시 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="47ba4-138">그런 다음 사용자 지정 동작은 세 번째 다시 연결 시도가 실패 한 후 중지 하 여 기본 동작에서 다시 달라 지므로 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="47ba4-139">기본 구성에서는 30 초 내에 다시 한 번 더 다시 연결 하려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="47ba4-140">자동 다시 연결 시도의 타이밍 및 수에 대 한 더 많은 제어를 원하는 경우 `WithAutomaticReconnect`는 `NextRetryDelay`라는 단일 메서드를 포함 하는 `IRetryPolicy` 인터페이스를 구현 하는 개체를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="47ba4-141">`NextRetryDelay`는 형식이 `RetryContext`인 단일 인수를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="47ba4-142">`RetryContext`에는 `PreviousRetryCount`, `ElapsedTime` 및 `RetryReason`의 세 가지 속성이 있습니다 .이 속성은 각각 `long`, `TimeSpan`, `Exception`입니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason`, which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="47ba4-143">첫 번째 다시 연결을 시도 하기 전에 `PreviousRetryCount`와 `ElapsedTime`는 모두 0이 되며, `RetryReason`는 연결이 끊어지는 원인이 되는 예외입니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="47ba4-144">실패 한 각 재시도 후에는 `PreviousRetryCount` 1 씩 증가 하 고, 지금까지 다시 연결 하는 데 걸린 시간을 반영 하 여 `ElapsedTime` 업데이트 되며, `RetryReason`는 마지막 다시 연결 시도가 실패 한 원인이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="47ba4-145">`NextRetryDelay`는 다음 다시 연결 시도 전에 대기할 시간을 나타내는 TimeSpan을 반환 하거나 `HubConnection` 다시 연결을 중지 해야 하는 경우 `null`을 반환 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
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

<span data-ttu-id="47ba4-146">또는 [수동으로 다시 연결](#manually-reconnect)에 설명 된 대로 클라이언트를 수동으로 다시 연결 하는 코드를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="47ba4-147">수동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="47ba4-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="47ba4-148">3\.0 이전 버전의 SignalR에 대 한 .NET 클라이언트는 자동으로 다시 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="47ba4-149">클라이언트에 수동으로 다시 연결 하는 코드를 작성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="47ba4-150"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 이벤트를 사용 하 여 손실 된 연결에 응답할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="47ba4-151">예를 들어 자동으로 재연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="47ba4-152">`Closed` 이벤트에는 `async void`를 사용 하지 않고 비동기 코드를 실행할 수 있도록 하는 `Task`을 반환 하는 대리자가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="47ba4-153">동기적으로 실행 되는 `Closed` 이벤트 처리기에서 대리자 시그니처를 만족 시키려면 `Task.CompletedTask`를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="47ba4-154">비동기를 지원하는 가장 큰 이유는 연결을 다시 시작할 수도 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="47ba4-155">연결 시작은 비동기 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="47ba4-156">연결을 다시 시작 하는 `Closed` 처리기에서 다음 예제와 같이 서버 오버 로드를 방지 하기 위해 임의 지연이 발생할 때까지 대기 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="47ba4-157">클라이언트에서 허브 메서드 호출하기</span><span class="sxs-lookup"><span data-stu-id="47ba4-157">Call hub methods from client</span></span>

<span data-ttu-id="47ba4-158">`InvokeAsync`는 허브에서 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="47ba4-159">허브 메서드 이름 및 허브 메서드에 정의 된 모든 인수를 `InvokeAsync`전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="47ba4-160">비동기 SignalR 이므로 호출을 수행할 때 `async` 및 `await`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-160">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="47ba4-161">`InvokeAsync` 메서드는 서버 메서드가 반환 될 때 완료 되는 `Task` 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="47ba4-162">반환 값 (있는 경우)은 `Task`의 결과로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="47ba4-163">서버에서 메서드에 의해 throw 되는 모든 예외는 오류 `Task`를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="47ba4-164">`await` 구문을 사용 하 여 서버 메서드가 완료 될 때까지 기다리거나 구문을 `try...catch` 하 여 오류를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="47ba4-165">`SendAsync` 메서드는 메시지가 서버로 전송 될 때 완료 되는 `Task` 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="47ba4-166">이 `Task`는 서버 메서드가 완료 될 때까지 기다리지 않으므로 반환 값이 제공 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="47ba4-167">메시지를 보내는 동안 클라이언트에서 throw 되는 모든 예외는 `Task`오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="47ba4-168">`await` 및 `try...catch` 구문을 사용 하 여 보내기 오류를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="47ba4-169">*서버 리스 모드*에서 Azure SignalR 서비스를 사용 하는 경우 클라이언트에서 허브 메서드를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-169">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="47ba4-170">자세한 내용은 [SignalR 서비스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="47ba4-170">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="47ba4-171">허브에서 클라이언트 메서드 호출하기</span><span class="sxs-lookup"><span data-stu-id="47ba4-171">Call client methods from hub</span></span>

<span data-ttu-id="47ba4-172">빌드 후 연결을 시작 하기 전에 `connection.On`를 사용 하 여 허브에서 호출 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="47ba4-173">`connection.On`에서 위의 코드는 서버 쪽 코드가 `SendAsync` 메서드를 사용 하 여이를 호출할 때 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="47ba4-174">오류 처리 및 로깅</span><span class="sxs-lookup"><span data-stu-id="47ba4-174">Error handling and logging</span></span>

<span data-ttu-id="47ba4-175">try-catch 문을 이용해서 오류를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="47ba4-176">`Exception` 개체를 검사 하 여 오류가 발생 한 후 수행할 적절 한 작업을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="47ba4-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="47ba4-177">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="47ba4-177">Additional resources</span></span>

* [<span data-ttu-id="47ba4-178">허브</span><span class="sxs-lookup"><span data-stu-id="47ba4-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="47ba4-179">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="47ba4-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="47ba4-180">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="47ba4-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* <span data-ttu-id="47ba4-181">[Azure SignalR 서비스 서버 리스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="47ba4-181">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
