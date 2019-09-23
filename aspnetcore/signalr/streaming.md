---
title: ASP.NET Core SignalR에서 스트리밍 사용
author: bradygaster
description: 클라이언트와 서버 간에 데이터를 스트리밍하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/05/2019
uid: signalr/streaming
ms.openlocfilehash: d520c8eec3e777acb9604bdcb5969268deabf8da
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186935"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="22a0c-103">ASP.NET Core SignalR에서 스트리밍 사용</span><span class="sxs-lookup"><span data-stu-id="22a0c-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="22a0c-104">작성자: [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="22a0c-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="22a0c-105">ASP.NET Core SignalR는 클라이언트에서 서버로 및 서버에서 클라이언트로의 스트리밍을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="22a0c-106">이는 시간이 지남에 따라 데이터 조각이 도착 하는 시나리오에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="22a0c-107">스트리밍할 때 모든 데이터를 사용할 수 있을 때까지 기다리지 않고 각 조각이 사용할 수 있게 되는 즉시 클라이언트 또는 서버로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="22a0c-108">ASP.NET Core SignalR은 서버 메서드의 스트리밍 반환 값을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="22a0c-109">이는 시간이 지남에 따라 데이터 조각이 도착 하는 시나리오에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="22a0c-110">클라이언트에 반환 값을 스트리밍할 때 모든 조각이 사용 가능할 때까지 기다리는 대신 사용 가능한 즉시 각 조각을 클라이언트로 전송합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="22a0c-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="22a0c-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="22a0c-112">스트리밍을 위한 허브 설정</span><span class="sxs-lookup"><span data-stu-id="22a0c-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="22a0c-113">허브 <xref:System.Collections.Generic.IAsyncEnumerable`1>메서드는 <xref:System.Threading.Channels.ChannelReader%601> ,,`Task<ChannelReader<T>>`또는를 반환할때자동으로스트리밍허브메서드가됩니다.`Task<IAsyncEnumerable<T>>`</span><span class="sxs-lookup"><span data-stu-id="22a0c-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="22a0c-114"><xref:System.Threading.Channels.ChannelReader%601> 또는 `Task<ChannelReader<T>>`를 반환하는 허브 메서드는 자동으로 스트리밍 허브 메서드로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="22a0c-115">서버-클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="22a0c-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="22a0c-116">스트리밍 허브 메서드는 외 `IAsyncEnumerable<T>` 에도를 `ChannelReader<T>`반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="22a0c-117">을 반환 `IAsyncEnumerable<T>` 하는 가장 간단한 방법은 다음 샘플에서 보여 주는 것 처럼 허브 메서드를 비동기 반복기 메서드로 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="22a0c-118">허브 비동기 반복기 메서드는 클라이언트가 스트림에서 `CancellationToken` 구독을 취소 하는 경우 트리거되는 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="22a0c-119">비동기 반복기 메서드는를 `ChannelReader` <xref:System.Threading.Channels.ChannelWriter`1>완료 하지 않고 가장 일찍 반환 하거나 메서드를 종료 하는 등 채널에 공통적인 문제를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="22a0c-120">다음 샘플에서는 채널을 사용 하 여 클라이언트에 스트리밍 데이터의 기본 사항을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="22a0c-121">개체를에 쓸 <xref:System.Threading.Channels.ChannelWriter%601>때마다 개체가 클라이언트에 즉시 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="22a0c-122">마지막으로 `ChannelWriter`가 완료되어 스트림이 닫혔음을 클라이언트에 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="22a0c-123">백그라운드 스레드에서 `ChannelWriter<T>`에 쓰고 최대한 빨리 `ChannelReader`를 반환하십시오.</span><span class="sxs-lookup"><span data-stu-id="22a0c-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="22a0c-124">다른 허브 호출은 `ChannelReader` 가 반환 될 때까지 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="22a0c-125">에서 논리를 `try ... catch`래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="22a0c-126">허브 메서드 `Channel` 호출이 제대로 `catch` 완료 되었는지 확인 `catch` 하려면 외부 및 외부에서를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

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

<span data-ttu-id="22a0c-127">서버-클라이언트 스트리밍 허브 메서드는 클라이언트가 스트림에서 구독을 `CancellationToken` 취소 하는 경우 트리거되는 매개 변수를 받아들일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="22a0c-128">이 토큰을 사용 하 여 서버 작업을 중지 하 고 클라이언트에서 스트림의 끝 이전에 연결을 해제 하는 경우 모든 리소스를 해제 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="22a0c-129">클라이언트와 서버 간 스트리밍</span><span class="sxs-lookup"><span data-stu-id="22a0c-129">Client-to-server streaming</span></span>

<span data-ttu-id="22a0c-130">허브 메서드는 또는 <xref:System.Threading.Channels.ChannelReader%601> <xref:System.Collections.Generic.IAsyncEnumerable%601>형식의 개체를 하나 이상 허용 하는 경우 자동으로 클라이언트-서버 스트리밍 허브 메서드가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="22a0c-131">다음 샘플에서는 클라이언트에서 보낸 스트리밍 데이터를 읽는 방법의 기본 사항을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="22a0c-132">클라이언트 <xref:System.Threading.Channels.ChannelWriter%601>에서에 쓸 때마다 데이터가 허브 메서드가 읽고 있는 서버의에 기록 `ChannelReader` 됩니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="22a0c-133">메서드 <xref:System.Collections.Generic.IAsyncEnumerable%601> 버전은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-133">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

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

## <a name="net-client"></a><span data-ttu-id="22a0c-134">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="22a0c-134">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="22a0c-135">서버-클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="22a0c-135">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="22a0c-136">의 `StreamAsync` 및`HubConnection` 메서드는 서버-클라이언트 스트리밍 메서드를 호출 하는 데 사용 됩니다. `StreamAsChannelAsync`</span><span class="sxs-lookup"><span data-stu-id="22a0c-136">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="22a0c-137">허브 메서드에 정의 된 허브 메서드 이름 및 인수를 또는 `StreamAsync` `StreamAsChannelAsync`에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-137">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="22a0c-138">`StreamAsync<T>` 및`StreamAsChannelAsync<T>` 의 제네릭 매개 변수는 스트리밍 메서드에서 반환 하는 개체의 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-138">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="22a0c-139">`IAsyncEnumerable<T>` 또는`ChannelReader<T>` 형식의 개체는 스트림 호출에서 반환 되 고 클라이언트의 스트림을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-139">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="22a0c-140">다음 `StreamAsync` 을 반환 `IAsyncEnumerable<int>`하는 예제:</span><span class="sxs-lookup"><span data-stu-id="22a0c-140">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

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

<span data-ttu-id="22a0c-141">다음을 `StreamAsChannelAsync` 반환 `ChannelReader<int>`하는 해당 예제:</span><span class="sxs-lookup"><span data-stu-id="22a0c-141">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

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

<span data-ttu-id="22a0c-142">`StreamAsChannelAsync` 의`HubConnection` 메서드는 서버-클라이언트 스트리밍 메서드를 호출 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-142">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="22a0c-143">허브 메서드에 정의 된 허브 메서드 이름 및 인수를에 `StreamAsChannelAsync`전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-143">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="22a0c-144">`StreamAsChannelAsync<T>`의 제네릭 매개 변수는 스트리밍 메서드에서 반환되는 개체의 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-144">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="22a0c-145">는 `ChannelReader<T>` 스트림 호출에서 반환 되 고 클라이언트의 스트림을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-145">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

<span data-ttu-id="22a0c-146">`StreamAsChannelAsync` 의`HubConnection` 메서드는 서버-클라이언트 스트리밍 메서드를 호출 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-146">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="22a0c-147">허브 메서드에 정의 된 허브 메서드 이름 및 인수를에 `StreamAsChannelAsync`전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-147">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="22a0c-148">`StreamAsChannelAsync<T>`의 제네릭 매개 변수는 스트리밍 메서드에서 반환되는 개체의 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-148">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="22a0c-149">는 `ChannelReader<T>` 스트림 호출에서 반환 되 고 클라이언트의 스트림을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-149">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="22a0c-150">클라이언트와 서버 간 스트리밍</span><span class="sxs-lookup"><span data-stu-id="22a0c-150">Client-to-server streaming</span></span>

<span data-ttu-id="22a0c-151">.NET 클라이언트에서 클라이언트-서버 스트리밍 허브 메서드를 호출 하는 방법에는 두 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-151">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="22a0c-152">호출 된 허브 `IAsyncEnumerable<T>` 메서드에 따라 `ChannelReader` 또는를, `InvokeAsync`또는 `StreamAsChannelAsync`에 `SendAsync`인수로 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-152">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="22a0c-153">`IAsyncEnumerable` 또는`ChannelWriter` 개체에 데이터를 쓸 때마다 서버의 허브 메서드는 클라이언트의 데이터가 포함 된 새 항목을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-153">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="22a0c-154">`IAsyncEnumerable` 개체를 사용 하는 경우 스트림 항목을 반환 하는 메서드는 종료 된 후에 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-154">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

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

<span data-ttu-id="22a0c-155">또는를 사용 하는 `ChannelWriter`경우 `channel.Writer.Complete()`다음을 사용 하 여 채널을 완성 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-155">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="22a0c-156">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="22a0c-156">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="22a0c-157">서버-클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="22a0c-157">Server-to-client streaming</span></span>

<span data-ttu-id="22a0c-158">JavaScript 클라이언트는를 사용 하 여 `connection.stream`허브에서 서버-클라이언트 스트리밍 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-158">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="22a0c-159">이 `stream` 메서드는 두 가지 인수를 전달받습니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-159">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="22a0c-160">허브 메서드의 이름.</span><span class="sxs-lookup"><span data-stu-id="22a0c-160">The name of the hub method.</span></span> <span data-ttu-id="22a0c-161">다음 예제에서 허브 메서드 이름은 `Counter`입니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-161">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="22a0c-162">허브 메서드에 정의된 인수.</span><span class="sxs-lookup"><span data-stu-id="22a0c-162">Arguments defined in the hub method.</span></span> <span data-ttu-id="22a0c-163">다음 예제에서 인수는 받을 스트림 항목 수와 스트림 항목 간의 지연 개수입니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-163">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="22a0c-164">`connection.stream`메서드 를`subscribe` 포함 하는 을반환합니다.`IStreamResult`</span><span class="sxs-lookup"><span data-stu-id="22a0c-164">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="22a0c-165">`IStreamSubscriber` 를에 `subscribe` 전달 하 고 `next`, `error`및 콜백을`complete` 호출에서 알림을 받도록 설정 합니다. `stream`</span><span class="sxs-lookup"><span data-stu-id="22a0c-165">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="22a0c-166">클라이언트에서 스트림을 종료 하려면 `dispose` `subscribe` 메서드에서 반환 된 `ISubscription` 에 대해 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-166">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="22a0c-167">이 메서드를 호출 하면 허브 메서드의 `CancellationToken` 매개 변수를 취소 하 게 됩니다 (제공 된 경우).</span><span class="sxs-lookup"><span data-stu-id="22a0c-167">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="22a0c-168">클라이언트에서 스트림을 종료 하려면 `dispose` `subscribe` 메서드에서 반환 된 `ISubscription` 에 대해 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-168">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="22a0c-169">클라이언트와 서버 간 스트리밍</span><span class="sxs-lookup"><span data-stu-id="22a0c-169">Client-to-server streaming</span></span>

<span data-ttu-id="22a0c-170">JavaScript 클라이언트는 `Subject` 호출 된 허브 메서드에 따라를 `invoke`, 또는 `stream`에 `send`인수로 전달 하 여 허브에서 클라이언트-서버 스트리밍 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-170">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="22a0c-171">는 처럼 보이는 클래스입니다. `Subject` `Subject`</span><span class="sxs-lookup"><span data-stu-id="22a0c-171">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="22a0c-172">예를 들어 RxJS에서 해당 라이브러리의 [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) 클래스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-172">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="22a0c-173">항목 `subject.next(item)` 을 사용 하 여를 호출 하면 항목이 스트림에 기록 되 고 허브 메서드는 서버에서 항목을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-173">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="22a0c-174">스트림을 종료 하려면를 호출 `subject.complete()`합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-174">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="22a0c-175">Java 클라이언트</span><span class="sxs-lookup"><span data-stu-id="22a0c-175">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="22a0c-176">서버-클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="22a0c-176">Server-to-client streaming</span></span>

<span data-ttu-id="22a0c-177">SignalR Java 클라이언트는 `stream` 메서드를 사용 하 여 스트리밍 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-177">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="22a0c-178">`stream`세 개 이상의 인수를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-178">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="22a0c-179">예상 되는 스트림 항목 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-179">The expected type of the stream items.</span></span>
* <span data-ttu-id="22a0c-180">허브 메서드의 이름.</span><span class="sxs-lookup"><span data-stu-id="22a0c-180">The name of the hub method.</span></span>
* <span data-ttu-id="22a0c-181">허브 메서드에 정의된 인수.</span><span class="sxs-lookup"><span data-stu-id="22a0c-181">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="22a0c-182">`stream` 의`HubConnection` 메서드는 스트림 항목 형식의 관찰 가능 개체를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-182">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="22a0c-183">관찰 가능한 형식의 `subscribe` 메서드는 `onNext`, `onError` 및 `onCompleted` 처리기가 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="22a0c-183">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="22a0c-184">추가 자료</span><span class="sxs-lookup"><span data-stu-id="22a0c-184">Additional resources</span></span>

* [<span data-ttu-id="22a0c-185">허브</span><span class="sxs-lookup"><span data-stu-id="22a0c-185">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="22a0c-186">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="22a0c-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="22a0c-187">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="22a0c-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="22a0c-188">Azure에 게시하기</span><span class="sxs-lookup"><span data-stu-id="22a0c-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
