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
# <a name="aspnet-core-signalr-net-client"></a>ASP.NET Core SignalR .NET 클라이언트

ASP.NET Core SignalR .NET 클라이언트 라이브러리를 사용하면 .NET 앱에서 SignalR 허브와 통신할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 문서의 코드 샘플은 ASP.NET Core SignalR .NET 클라이언트를 사용하는 WPF 앱입니다.

## <a name="install-the-signalr-net-client-package"></a>SignalR .NET 클라이언트 패키지 설치하기

[AspNetCore SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 패키지는 .Net 클라이언트가 SignalR hubs에 연결 하는 데 필요 합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

클라이언트 라이브러리를 설치 하려면 **패키지 관리자 콘솔** 창에서 다음 명령을 실행 합니다.

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

클라이언트 라이브러리를 설치 하려면 명령 셸에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>허브에 연결하기

연결을 설정 하려면 `HubConnectionBuilder` 만들고 `Build`를 호출 합니다. 연결을 만드는 동안 허브 URL, 프로토콜, 전송 형태, 로그 수준, 헤더 및 기타 옵션을 구성할 수 있습니다. `HubConnectionBuilder` 메서드를 `Build`에 삽입 하 여 필요한 옵션을 구성 합니다. `StartAsync`와의 연결을 시작 합니다.

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>연결 해제 처리하기

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>자동으로 다시 연결

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>에서 `WithAutomaticReconnect` 메서드를 사용 하 여 자동으로 다시 연결 하도록 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>를 구성할 수 있습니다. 기본적으로 자동으로 다시 연결 되지 않습니다.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

매개 변수를 사용 하지 않으면 `WithAutomaticReconnect()` 각 다시 연결 시도를 시도 하기 전에 각각 0, 2, 10 및 30 초 동안 대기 하도록 클라이언트를 구성 하 고 실패 한 네 번 시도 후 중지 합니다.

다시 연결 시도를 시작 하기 전에 `HubConnection` `HubConnectionState.Reconnecting` 상태로 전환 되 고 `Reconnecting` 이벤트를 발생 시킵니다.  이렇게 하면 연결이 손실 되었음을 사용자에 게 경고 하 고 UI 요소를 사용 하지 않도록 설정할 수 있습니다. 비 대화형 앱은 메시지 큐를 시작 하거나 삭제할 수 있습니다.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

클라이언트가 처음 네 번의 시도 내에서 다시 연결 하는 경우 `HubConnection`는 다시 `Connected` 상태로 전환 되 고 `Reconnected` 이벤트를 발생 시킵니다. 이렇게 하면 연결을 다시 설정 하 고 대기 중인 모든 메시지를 큐에서 제거 하는 사용자에 게 알릴 수 있습니다.

연결이 서버를 완전히 처음으로 표시 하기 때문에 `Reconnected` 이벤트 처리기에 새 `ConnectionId` 제공 됩니다.

> [!WARNING]
> `HubConnection` [협상을 건너뛰도록](xref:signalr/configuration#configure-client-options)구성 된 경우 `Reconnected` 이벤트 처리기의 `connectionId` 매개 변수는 null입니다.

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()` 초기 시작 오류를 다시 시도 하도록 `HubConnection` 구성 하지 않으므로 시작 오류는 수동으로 처리 해야 합니다.

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

클라이언트가 처음 네 번의 시도에서 성공적으로 다시 연결 되지 않으면 `HubConnection` `Disconnected` 상태로 전환 되 고 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 이벤트를 발생 시킵니다. 이를 통해 연결을 수동으로 다시 시작 하거나 연결이 영구적으로 손실 되었음을 사용자에 게 알릴 수 있습니다.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

연결을 끊거나 다시 연결 하는 시간을 변경 하기 전에 사용자 지정 횟수의 다시 연결 시도를 구성 하려면 각 다시 연결 시도를 시작 하기 전에 대기할 지연 시간 (밀리초)을 나타내는 숫자 배열을 허용 `WithAutomaticReconnect` 합니다.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

앞의 예제에서는 연결이 끊긴 후 즉시 다시 연결 시도를 시작 하도록 `HubConnection`를 구성 합니다. 기본 구성의 경우에도 마찬가지입니다.

첫 번째 다시 연결 시도가 실패 하면 두 번째 다시 연결 시도도 기본 구성에서와 같이 2 초 동안 대기 하는 대신 즉시 시작 됩니다.

두 번째 다시 연결 시도가 실패 하면 세 번째 다시 연결 시도가 10 초 후에 다시 시작 됩니다 .이는 기본 구성 처럼 다시 시작 됩니다.

그런 다음 사용자 지정 동작은 세 번째 다시 연결 시도가 실패 한 후 중지 하 여 기본 동작에서 다시 달라 지므로 합니다. 기본 구성에서는 30 초 내에 다시 한 번 더 다시 연결 하려고 시도 합니다.

자동 다시 연결 시도의 타이밍 및 수에 대 한 더 많은 제어를 원하는 경우 `WithAutomaticReconnect`는 `NextRetryDelay`라는 단일 메서드를 포함 하는 `IRetryPolicy` 인터페이스를 구현 하는 개체를 허용 합니다.

`NextRetryDelay`는 형식이 `RetryContext`인 단일 인수를 사용 합니다. `RetryContext`에는 `PreviousRetryCount`, `ElapsedTime` 및 `RetryReason`의 세 가지 속성이 있습니다 .이 속성은 각각 `long`, `TimeSpan`, `Exception`입니다. 첫 번째 다시 연결을 시도 하기 전에 `PreviousRetryCount`와 `ElapsedTime`는 모두 0이 되며, `RetryReason`는 연결이 끊어지는 원인이 되는 예외입니다. 실패 한 각 재시도 후에는 `PreviousRetryCount` 1 씩 증가 하 고, 지금까지 다시 연결 하는 데 걸린 시간을 반영 하 여 `ElapsedTime` 업데이트 되며, `RetryReason`는 마지막 다시 연결 시도가 실패 한 원인이 됩니다.

`NextRetryDelay`는 다음 다시 연결 시도 전에 대기할 시간을 나타내는 TimeSpan을 반환 하거나 `HubConnection` 다시 연결을 중지 해야 하는 경우 `null`을 반환 해야 합니다.

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

또는 [수동으로 다시 연결](#manually-reconnect)에 설명 된 대로 클라이언트를 수동으로 다시 연결 하는 코드를 작성할 수 있습니다.

::: moniker-end

### <a name="manually-reconnect"></a>수동으로 다시 연결

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 3\.0 이전 버전의 SignalR에 대 한 .NET 클라이언트는 자동으로 다시 연결 되지 않습니다. 클라이언트에 수동으로 다시 연결 하는 코드를 작성 해야 합니다.

::: moniker-end

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 이벤트를 사용 하 여 손실 된 연결에 응답할 수 있습니다. 예를 들어 자동으로 재연결할 수 있습니다.

`Closed` 이벤트에는 `async void`를 사용 하지 않고 비동기 코드를 실행할 수 있도록 하는 `Task`을 반환 하는 대리자가 필요 합니다. 동기적으로 실행 되는 `Closed` 이벤트 처리기에서 대리자 시그니처를 만족 시키려면 `Task.CompletedTask`를 반환 합니다.

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

비동기를 지원하는 가장 큰 이유는 연결을 다시 시작할 수도 있기 때문입니다. 연결 시작은 비동기 작업입니다.

연결을 다시 시작 하는 `Closed` 처리기에서 다음 예제와 같이 서버 오버 로드를 방지 하기 위해 임의 지연이 발생할 때까지 대기 하는 것이 좋습니다.

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>클라이언트에서 허브 메서드 호출하기

`InvokeAsync`는 허브에서 메서드를 호출 합니다. 허브 메서드 이름 및 허브 메서드에 정의 된 모든 인수를 `InvokeAsync`전달 합니다. 비동기 SignalR 이므로 호출을 수행할 때 `async` 및 `await`를 사용 합니다.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

`InvokeAsync` 메서드는 서버 메서드가 반환 될 때 완료 되는 `Task` 반환 합니다. 반환 값 (있는 경우)은 `Task`의 결과로 제공 됩니다. 서버에서 메서드에 의해 throw 되는 모든 예외는 오류 `Task`를 생성 합니다. `await` 구문을 사용 하 여 서버 메서드가 완료 될 때까지 기다리거나 구문을 `try...catch` 하 여 오류를 처리 합니다.

`SendAsync` 메서드는 메시지가 서버로 전송 될 때 완료 되는 `Task` 반환 합니다. 이 `Task`는 서버 메서드가 완료 될 때까지 기다리지 않으므로 반환 값이 제공 되지 않습니다. 메시지를 보내는 동안 클라이언트에서 throw 되는 모든 예외는 `Task`오류가 발생 합니다. `await` 및 `try...catch` 구문을 사용 하 여 보내기 오류를 처리 합니다.

> [!NOTE]
> *서버 리스 모드*에서 Azure SignalR 서비스를 사용 하는 경우 클라이언트에서 허브 메서드를 호출할 수 없습니다. 자세한 내용은 [SignalR 서비스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)를 참조 하세요.

## <a name="call-client-methods-from-hub"></a>허브에서 클라이언트 메서드 호출하기

빌드 후 연결을 시작 하기 전에 `connection.On`를 사용 하 여 허브에서 호출 하는 메서드를 정의 합니다.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

`connection.On`에서 위의 코드는 서버 쪽 코드가 `SendAsync` 메서드를 사용 하 여이를 호출할 때 실행 됩니다.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>오류 처리 및 로깅

try-catch 문을 이용해서 오류를 처리합니다. `Exception` 개체를 검사 하 여 오류가 발생 한 후 수행할 적절 한 작업을 결정 합니다.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>추가 리소스

* [허브](xref:signalr/hubs)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
* [Azure에 게시](xref:signalr/publish-to-azure-web-app)
* [Azure SignalR 서비스 서버 리스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)
