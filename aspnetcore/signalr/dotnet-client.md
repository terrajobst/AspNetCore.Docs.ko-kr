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
# <a name="aspnet-core-signalr-net-client"></a>ASP.NET Core SignalR .NET 클라이언트

ASP.NET Core SignalR .NET 클라이언트 라이브러리를 사용하면 .NET 앱에서 SignalR 허브와 통신할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 문서의 코드 샘플은 ASP.NET Core SignalR .NET 클라이언트를 사용하는 WPF 앱입니다.

## <a name="install-the-signalr-net-client-package"></a>SignalR .NET 클라이언트 패키지 설치하기

.NET 클라이언트에서 SignalR 허브에 연결하려면 `Microsoft.AspNetCore.SignalR.Client` 패키지가 필요합니다. 클라이언트 라이브러리를 설치하려면 **패키지 관리자 콘솔** 창에서 다음 명령을 실행합니다.

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a>허브에 연결하기

연결을 설정하려면 `HubConnectionBuilder`를 생성하고 `Build`를 호출합니다. 연결을 만드는 동안 허브 URL, 프로토콜, 전송 형태, 로그 수준, 헤더 및 기타 옵션을 구성할 수 있습니다. `HubConnectionBuilder` 메서드들 중 원하는 메서드를 `Build`에 삽입하여 필요한 모든 옵션을 구성합니다. `StartAsync`를 사용하여 연결을 시작합니다.

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>연결 해제 처리하기

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>자동으로 다시 연결

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> 자동으로 사용 하 여 다시 연결 하도록 구성할 수 있습니다 합니다 `WithAutomaticReconnect` 메서드는 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>합니다. 기본적으로 다시 자동으로 연결 되지 않습니다.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

모든 매개 변수 없이 `WithAutomaticReconnect()` 4 실패 한 시도 후 각 다시 연결 시도 전에 각각 0, 2, 10 일 및 30 초를 기다리고 클라이언트를 구성 합니다.

다시 연결 시도 시작 하기 전에 `HubConnection` 전환 됩니다 합니다 `HubConnectionState.Reconnecting` 상태 및 실행을 `Reconnecting` 이벤트.  이 연결 손실 되었음을 사용자에 게 경고 하는 데 UI 요소를 사용 하지 않도록 설정할 수 있는 기회를 제공 합니다. 비 대화형 응용 프로그램 큐 또는 메시지 삭제를 시작할 수 있습니다.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

클라이언트는 먼저 4 회 성공적으로 다시 연결 하는 경우는 `HubConnection` 으로 다시 전환 됩니다.는 `Connected` 상태 및 실행을 `Reconnected` 이벤트. 이 연결이 다시 설정 하 고 모든 대기 중인된 메시지를 큐에서 제거 하는 사용자에 게 알리기 기회를 제공 합니다.

연결 서버에 완전히 새로운 표시 하므로 새 `ConnectionId` 에 제공 됩니다는 `Reconnected` 이벤트 처리기입니다.

> [!WARNING]
> `Reconnected` 이벤트 처리기 `connectionId` 매개 변수는 null이 됩니다 경우 합니다 `HubConnection` 하도록 구성 된 [협상을 건너뛸](xref:signalr/configuration#configure-client-options)합니다.

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()` 구성지 않습니다는 `HubConnection` 시작 실패를 수동으로 처리 해야 하므로 초기 시작 실패를 다시 시도 합니다.

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

클라이언트 하지 해당 처음 네 개의 시도 내에서 성공적으로 다시 연결 하는 경우는 `HubConnection` 전환 됩니다 합니다 `Disconnected` 상태 및 실행을 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 이벤트. 이 연결을 수동으로 다시 시작 하거나 연결이 영구적으로 손실 된 사용자에 게 알리기 기회를 제공 합니다.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

사용자 지정 연결을 끊기 전에 다시 연결 시도 횟수를 구성 하거나 다시 연결 시간을 변경 하려면 `WithAutomaticReconnect` 각 다시 연결 시도 시작 하기 전에 대기할 밀리초의 지연을 나타내는 숫자 배열을 허용 합니다.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

앞의 예제 구성는 `HubConnection` 연결이 끊어진 후에 즉시 다시 연결 시도 시작 합니다. 기본 구성에는이 마찬가지입니다.

첫 번째 다시 연결 시도가 실패 하면 기본 구성이 있는 것 처럼 2 초 대기 하는 대신 두 번째 다시 연결 시도가 즉시 시작도 됩니다.

두 번째 다시 연결 시도가 실패 하면 세 번째 다시 연결 시도 기본 구성 등 다시 10 초 후에 시작 됩니다.

사용자 지정 동작은 다음 모델과 다시 기본 동작에서 세 번째 다시 연결 시도 실패 후 중지. 여기에 기본 구성을 수 하나 더 다시 연결 다른 30 초 후에 시도 합니다.

자동의 수와 타이밍을 보다 잘 제어할 다시 시도 하려는 경우 `WithAutomaticReconnect` 구현 하는 개체를 허용 합니다 `IRetryPolicy` 라는 단일 메서드가 있는 인터페이스 `NextRetryDelay`합니다.

`NextRetryDelay` 형식의 단일 인수 `RetryContext`합니다. `RetryContext` 세 가지 속성이 있습니다: `PreviousRetryCount`, `ElapsedTime` 하 고 `RetryReason` 되는 `long`, `TimeSpan` 및 `Exception` 각각. 첫 번째 다시 연결 시도 하기 전에 둘 다 `PreviousRetryCount` 하 고 `ElapsedTime` 수는 0, 및 `RetryReason` 연결이 손실 되도록 발생 시킨 예외 됩니다. 각 실패 한 시도 후 `PreviousRetryCount` 씩 증가 됩니다 `ElapsedTime` 지금 다시 연결 하는 데 걸린 시간을 반영 하도록 업데이트 됩니다 및 `RetryReason` 마지막 다시 연결 시도 실패를 발생 시킨 예외 됩니다.

`NextRetryDelay` 두 TimeSpan 시간을 나타내는 다음 다시 연결 시도 하기 전에 대기할 반환 해야 합니다 또는 `null` 경우는 `HubConnection` 다시 연결을 중지 해야 합니다.

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

또는 클라이언트에서 설명한 것 처럼 수동으로 다시 연결 하는 코드를 작성할 수 있습니다 [수동으로 다시](#manually-reconnect)입니다.

::: moniker-end

### <a name="manually-reconnect"></a>수동으로 다시 연결

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 3\.0 이전 SignalR에 대 한.NET 클라이언트 하지 자동으로 다시 연결 합니다. 클라이언트에 수동으로 다시 연결 하는 코드를 작성 해야 합니다.

::: moniker-end

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 이벤트를 이용해서 끊긴 연결에 대응합니다. 예를 들어 자동으로 재연결할 수 있습니다.

`Closed` 이벤트에는 `async void`를 사용하지 않고 비동기 코드를 실행할 수 있는 `Task`를 반환하는 대리자가 필요합니다. 동기적으로 실행되는 `Closed` 이벤트 처리기에서 대리자의 시그니처를 만족시키려면 `Task.CompletedTask`를 반환합니다.

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

비동기를 지원하는 가장 큰 이유는 연결을 다시 시작할 수도 있기 때문입니다. 연결 시작은 비동기 작업입니다.

연결을 재시작하는 `Closed` 처리기에서는 다음 예제에서 볼 수 있는 것처럼 서버의 과부하를 방지할 수 있도록 임의의 지연 시간 동안 대기하는 것이 좋습니다.

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>클라이언트에서 허브 메서드 호출하기

`InvokeAsync`는 허브 메서드를 호출합니다. 허브 메서드의 이름과 허브 메서드에 정의된 모든 인수를 `InvokeAsync`에 전달합니다. SignalR은 비동기로 동작하므로 호출 시 `async`와 `await`를 사용해야 합니다.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

합니다 `InvokeAsync` 메서드가 반환 되는 `Task` 서버 메서드가 반환 될 때 완료 되는 합니다. 반환 값에 있는 경우 제공 됩니다 결과로 `Task`합니다. 서버에서 메서드에 의해 throw 된 예외 생성 오류가 발생 한 `Task`합니다. 사용 하 여 `await` 서버 메서드가 완료 될 때까지 기다리는 구문 및 `try...catch` 구문 오류를 처리 합니다.

`SendAsync` 메서드가 반환 되는 `Task` 메시지 서버에 전송 되었을 때 완료 되는. 반환 값이 제공 되지 아니므로 `Task` 서버 메서드가 완료 될 때까지 대기 하지 않습니다. 메시지를 전송 하는 동안 클라이언트에서 throw 된 예외 생성 오류가 발생 한 `Task`합니다. 사용 하 여 `await` 고 `try...catch` 처리 하는 구문 오류를 전송 합니다.

> [!NOTE]
> Azure SignalR Service를 사용 하는 경우 *서버 리스 모드*, 클라이언트에서 허브 메서드를 호출할 수 없습니다. 자세한 내용은 참조는 [SignalR Service 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)합니다.

## <a name="call-client-methods-from-hub"></a>허브에서 클라이언트 메서드 호출하기

연결을 만든 다음, 그러나 연결을 시작하기 전에 `connection.On`을 이용해서 허브가 호출할 메서드를 정의합니다.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

위의 `connection.On` 내부의 코드는 서버 쪽 코드에서 `SendAsync` 메서드를 사용하여 이 코드를 호출할 때 실행됩니다.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>오류 처리 및 로깅

try-catch 문을 이용해서 오류를 처리합니다. `Exception` 개체를 검사해서 오류가 발생한 후 수행할 적절한 동작을 결정합니다.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>추가 자료

* [허브](xref:signalr/hubs)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
* [Azure에 게시하기](xref:signalr/publish-to-azure-web-app)
* [Azure SignalR Service 서버 리스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)
