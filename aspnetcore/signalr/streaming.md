---
title: ASP.NET Core SignalR에서 스트리밍 사용
author: bradygaster
description: 클라이언트와 서버 간에 데이터를 스트리밍하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/streaming
ms.openlocfilehash: 21dd8180fe168f81ed68b01f02b81a6264d6e5a6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654975"
---
# <a name="use-streaming-in-aspnet-core-opno-locsignalr"></a>ASP.NET Core SignalR에서 스트리밍 사용

만든 사람 [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR는 클라이언트에서 서버로 및 서버에서 클라이언트로의 스트리밍을 지원 합니다. 이는 시간이 지남에 따라 데이터 조각이 도착 하는 시나리오에 유용 합니다. 스트리밍할 때 모든 데이터를 사용할 수 있을 때까지 기다리지 않고 각 조각이 사용할 수 있게 되는 즉시 클라이언트 또는 서버로 전송 됩니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR는 서버 메서드의 스트리밍 반환 값을 지원 합니다. 이는 시간이 지남에 따라 데이터 조각이 도착 하는 시나리오에 유용 합니다. 클라이언트에 반환 값을 스트리밍할 때 모든 조각이 사용 가능할 때까지 기다리는 대신 사용 가능한 즉시 각 조각을 클라이언트로 전송합니다.

::: moniker-end

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>스트리밍을 위한 허브 설정

::: moniker range=">= aspnetcore-3.0"

허브 메서드는 <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`또는 `Task<ChannelReader<T>>`반환 될 때 자동으로 스트리밍 허브 메서드가 됩니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

허브 메서드는 <xref:System.Threading.Channels.ChannelReader%601> 또는 `Task<ChannelReader<T>>`반환 될 때 자동으로 스트리밍 허브 메서드가 됩니다.

::: moniker-end

### <a name="server-to-client-streaming"></a>서버-클라이언트 스트리밍

::: moniker range=">= aspnetcore-3.0"

스트리밍 허브 메서드는 `ChannelReader<T>`외에도 `IAsyncEnumerable<T>`를 반환할 수 있습니다. `IAsyncEnumerable<T>`를 반환 하는 가장 간단한 방법은 다음 샘플에서 보여 주는 것 처럼 허브 메서드를 비동기 반복기 메서드로 만드는 것입니다. 허브 비동기 반복기 메서드는 클라이언트가 스트림에서 구독을 취소 하는 경우 트리거되는 `CancellationToken` 매개 변수를 사용할 수 있습니다. 비동기 반복기 메서드는 <xref:System.Threading.Channels.ChannelWriter`1>를 완료 하지 않고 `ChannelReader`을 신속 하 게 반환 하거나 메서드를 종료 하는 것과 같이 채널에 공통적인 문제를 방지 합니다.

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

다음 샘플에서는 채널을 사용 하 여 클라이언트에 스트리밍 데이터의 기본 사항을 보여 줍니다. 개체가 <xref:System.Threading.Channels.ChannelWriter%601>에 기록 될 때마다 개체가 클라이언트에 즉시 전송 됩니다. 끝에 `ChannelWriter` 완료 되어 클라이언트에 스트림이 닫힙니다.

> [!NOTE]
> 백그라운드 스레드에서 `ChannelWriter<T>`에 쓰고 최대한 빨리 `ChannelReader`을 반환 합니다. 다른 허브 호출은 `ChannelReader`가 반환 될 때까지 차단 됩니다.
>
> `try ... catch`에서 논리를 래핑합니다. `catch`에서 `Channel`를 완료 하 고 `catch` 외부에서 허브 메서드 호출이 제대로 완료 되었는지 확인 합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

서버-클라이언트 스트리밍 허브 메서드는 클라이언트가 스트림에서 구독을 취소 하는 경우 트리거되는 `CancellationToken` 매개 변수를 받아들일 수 있습니다. 이 토큰을 사용 하 여 서버 작업을 중지 하 고 클라이언트에서 스트림의 끝 이전에 연결을 해제 하는 경우 모든 리소스를 해제 합니다.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>클라이언트와 서버 간 스트리밍

허브 메서드는 <xref:System.Threading.Channels.ChannelReader%601> 또는 <xref:System.Collections.Generic.IAsyncEnumerable%601>형식의 개체를 하나 이상 허용 하는 경우 자동으로 클라이언트-서버 스트리밍 허브 메서드가 됩니다. 다음 샘플에서는 클라이언트에서 보낸 스트리밍 데이터를 읽는 방법의 기본 사항을 보여 줍니다. 클라이언트는 <xref:System.Threading.Channels.ChannelWriter%601>에 쓸 때마다 허브 메서드가 읽는 서버의 `ChannelReader`에 데이터를 기록 합니다.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

메서드의 <xref:System.Collections.Generic.IAsyncEnumerable%601> 버전은 다음과 같습니다.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a>.NET 클라이언트

### <a name="server-to-client-streaming"></a>서버-클라이언트 스트리밍


::: moniker range=">= aspnetcore-3.0"

`HubConnection`의 `StreamAsync` 및 `StreamAsChannelAsync` 메서드는 서버-클라이언트 스트리밍 메서드를 호출 하는 데 사용 됩니다. 허브 메서드에 정의 된 허브 메서드 이름 및 인수를 `StreamAsync` 또는 `StreamAsChannelAsync`전달 합니다. `StreamAsync<T>` 및 `StreamAsChannelAsync<T>`의 제네릭 매개 변수는 스트리밍 메서드에서 반환 하는 개체의 형식을 지정 합니다. `IAsyncEnumerable<T>` 또는 `ChannelReader<T>` 형식의 개체는 스트림 호출에서 반환 되 고 클라이언트의 스트림을 나타냅니다.

`IAsyncEnumerable<int>`을 반환 하는 `StreamAsync` 예제는 다음과 같습니다.

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

`ChannelReader<int>`을 반환 하는 해당 `StreamAsChannelAsync` 예제는 다음과 같습니다.

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

`HubConnection`에 대 한 `StreamAsChannelAsync` 메서드는 서버-클라이언트 스트리밍 메서드를 호출 하는 데 사용 됩니다. 허브 메서드에 정의 된 허브 메서드 이름 및 인수를 `StreamAsChannelAsync`에 전달 합니다. `StreamAsChannelAsync<T>`의 제네릭 매개 변수는 스트리밍 메서드에서 반환 하는 개체의 형식을 지정 합니다. `ChannelReader<T>`은 스트림 호출에서 반환 되 고 클라이언트의 스트림을 나타냅니다.

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`HubConnection`에 대 한 `StreamAsChannelAsync` 메서드는 서버-클라이언트 스트리밍 메서드를 호출 하는 데 사용 됩니다. 허브 메서드에 정의 된 허브 메서드 이름 및 인수를 `StreamAsChannelAsync`에 전달 합니다. `StreamAsChannelAsync<T>`의 제네릭 매개 변수는 스트리밍 메서드에서 반환 하는 개체의 형식을 지정 합니다. `ChannelReader<T>`은 스트림 호출에서 반환 되 고 클라이언트의 스트림을 나타냅니다.

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>클라이언트와 서버 간 스트리밍

.NET 클라이언트에서 클라이언트-서버 스트리밍 허브 메서드를 호출 하는 방법에는 두 가지가 있습니다. 호출 된 허브 메서드에 따라 `IAsyncEnumerable<T>` 또는 `ChannelReader`를 `SendAsync`, `InvokeAsync`또는 `StreamAsChannelAsync`에 인수로 전달할 수 있습니다.

`IAsyncEnumerable` 또는 `ChannelWriter` 개체에 데이터를 쓸 때마다 서버의 허브 메서드는 클라이언트의 데이터를 사용 하 여 새 항목을 받습니다.

`IAsyncEnumerable` 개체를 사용 하는 경우 스트림 항목을 반환 하는 메서드는 종료 된 후에 종료 됩니다.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

또는 `ChannelWriter`를 사용 하는 경우 `channel.Writer.Complete()`를 사용 하 여 채널을 완성 합니다.

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>JavaScript 클라이언트

### <a name="server-to-client-streaming"></a>서버-클라이언트 스트리밍

JavaScript 클라이언트는 `connection.stream`을 사용 하 여 허브에서 서버-클라이언트 스트리밍 메서드를 호출 합니다. `stream` 메서드는 두 개의 인수를 허용 합니다.

* 허브 메서드의 이름. 다음 예제에서는 허브 메서드 이름이 `Counter`합니다.
* 허브 메서드에 정의된 인수. 다음 예제에서 인수는 받을 스트림 항목 수와 스트림 항목 간의 지연 개수입니다.

`connection.stream`은 `subscribe` 메서드를 포함 하는 `IStreamResult`을 반환 합니다. `subscribe`에 `IStreamSubscriber`를 전달 하 고 `complete` 호출에서 알림을 받도록 `next`, `error`및 `stream` 콜백을 설정 합니다.

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

클라이언트에서 스트림을 종료 하려면 `subscribe` 메서드에서 반환 된 `ISubscription`에서 `dispose` 메서드를 호출 합니다. 이 메서드를 호출 하면 허브 메서드의 `CancellationToken` 매개 변수 (제공 된 경우)가 취소 됩니다.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

클라이언트에서 스트림을 종료 하려면 `subscribe` 메서드에서 반환 된 `ISubscription`에서 `dispose` 메서드를 호출 합니다.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>클라이언트와 서버 간 스트리밍

JavaScript 클라이언트는 호출 된 허브 메서드에 따라 `Subject`를 `send`, `invoke`또는 `stream`에 인수로 전달 하 여 허브의 클라이언트-서버 스트리밍 메서드를 호출 합니다. `Subject`은 `Subject`와 같은 클래스입니다. 예를 들어 RxJS에서 해당 라이브러리의 [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) 클래스를 사용할 수 있습니다.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

항목을 사용 하 여 `subject.next(item)`를 호출 하면 항목이 스트림에 기록 되 고 허브 메서드가 서버에서 항목을 받습니다.

스트림을 종료 하려면 `subject.complete()`를 호출 합니다.

## <a name="java-client"></a>Java 클라이언트

### <a name="server-to-client-streaming"></a>서버-클라이언트 스트리밍

SignalR Java 클라이언트는 `stream` 메서드를 사용 하 여 스트리밍 메서드를 호출 합니다. `stream`는 세 개 이상의 인수를 허용 합니다.

* 예상 되는 스트림 항목 유형입니다.
* 허브 메서드의 이름.
* 허브 메서드에 정의된 인수.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

`HubConnection`의 `stream` 메서드는 스트림 항목 형식의 관찰 가능 개체를 반환 합니다. 관찰 가능한 형식의 `subscribe` 메서드는 `onNext`, `onError` 및 `onCompleted` 처리기가 정의 됩니다.

::: moniker-end

## <a name="additional-resources"></a>추가 리소스

* [허브](xref:signalr/hubs)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
* [Azure에 게시](xref:signalr/publish-to-azure-web-app)
