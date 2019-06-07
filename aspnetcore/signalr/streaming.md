---
title: ASP.NET Core SignalR에서 스트리밍 사용
author: bradygaster
description: 클라이언트와 서버 간에 데이터를 스트림 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/05/2019
uid: signalr/streaming
ms.openlocfilehash: a75156f398e113393ddb891d16eec3f09de80c09
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750196"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="e673b-103">ASP.NET Core SignalR에서 스트리밍 사용</span><span class="sxs-lookup"><span data-stu-id="e673b-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="e673b-104">작성자: [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="e673b-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e673b-105">ASP.NET Core SignalR은 서버에 클라이언트와 서버에서 클라이언트로의 스트리밍 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="e673b-106">시간별 데이터 조각에 도착 하는 위치 하는 시나리오에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="e673b-107">스트리밍을 사용할 때는 각 조각은 클라이언트 또는 서버에 즉시 전송 되기 모든 데이터를 사용할 수 있을 때까지 대기 하는 대신 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e673b-108">ASP.NET Core SignalR은 서버 메서드의 스트리밍 반환 값을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="e673b-109">시간별 데이터 조각에 도착 하는 위치 하는 시나리오에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="e673b-110">클라이언트에 반환 값을 스트리밍할 때 모든 조각이 사용 가능할 때까지 기다리는 대신 사용 가능한 즉시 각 조각을 클라이언트로 전송합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="e673b-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e673b-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="e673b-112">스트리밍에 대 한 허브 설정</span><span class="sxs-lookup"><span data-stu-id="e673b-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e673b-113">반환 될 때 자동으로 허브 메서드를 스트리밍 허브 메서드를 됩니다 <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>를 `Task<IAsyncEnumerable<T>>`, 또는 `Task<ChannelReader<T>>`합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e673b-114"><xref:System.Threading.Channels.ChannelReader%601> 또는 `Task<ChannelReader<T>>`를 반환하는 허브 메서드는 자동으로 스트리밍 허브 메서드로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="e673b-115">서버와 클라이언트 간 스트리밍</span><span class="sxs-lookup"><span data-stu-id="e673b-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e673b-116">스트리밍 허브 메서드를 반환할 수 있습니다 `IAsyncEnumerable<T>` 외에 `ChannelReader<T>`합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="e673b-117">반환 하는 가장 간단한 방법은 `IAsyncEnumerable<T>` 다음 샘플 에서처럼 허브 메서드를 비동기 반복기 메서드 만들기가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="e673b-118">허브 비동기 반복기 메서드에 사용할 수는 `CancellationToken` 스트림에서 클라이언트 등록을 취소 하는 경우 트리거되는 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="e673b-119">반환 하지 않는 같은 채널을 사용 하 여 일반적인 문제를 방지 하는 비동기 반복기 메서드는 `ChannelReader` 충분히 이른 메서드를 완료 하지 않고 종료 또는 <xref:System.Threading.Channels.ChannelWriter`1>합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="e673b-120">다음 샘플에서는 스트리밍 채널을 사용 하 여 클라이언트에는 데이터의 기본 사항을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="e673b-121">개체를 쓸 때마다는 <xref:System.Threading.Channels.ChannelWriter%601>, 개체가 즉시 클라이언트로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="e673b-122">마지막으로 `ChannelWriter`가 완료되어 스트림이 닫혔음을 클라이언트에 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="e673b-123">백그라운드 스레드에서 `ChannelWriter<T>`에 쓰고 최대한 빨리 `ChannelReader`를 반환하십시오.</span><span class="sxs-lookup"><span data-stu-id="e673b-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="e673b-124">다른 허브 호출 될 때까지 차단 되는 `ChannelReader` 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="e673b-125">논리를 래핑하는 `try ... catch`합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="e673b-126">완료 합니다 `Channel` 에 `catch` 및 외부를 `catch` 메서드 호출이 올바르게 완료 되도록 허브입니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

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

<span data-ttu-id="e673b-127">허브 메서드를 스트리밍 서버와 클라이언트 간 허용할 수는 `CancellationToken` 스트림에서 클라이언트 등록을 취소 하는 경우 트리거되는 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="e673b-128">이 토큰을 사용 하 여 서버 작업을 중지 및 스트림 종료 되기 전에 클라이언트 연결이 끊어지면 리소스를 해제 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="e673b-129">클라이언트와 서버-스트리밍</span><span class="sxs-lookup"><span data-stu-id="e673b-129">Client-to-server streaming</span></span>

<span data-ttu-id="e673b-130">형식의 개체 한 개 이상 허용 하는 경우 자동으로 허브 메서드를 클라이언트와 서버 간 스트리밍 허브 메서드를 됩니다 <xref:System.Threading.Channels.ChannelReader%601> 또는 <xref:System.Collections.Generic.IAsyncEnumerable%601>합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="e673b-131">다음 샘플에서는 클라이언트에서 전송 하는 스트리밍 데이터를 읽는의 기본 사항을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="e673b-132">클라이언트에 쓸 때마다 합니다 <xref:System.Threading.Channels.ChannelWriter%601>는 데이터가 기록 되는 `ChannelReader` 허브 메서드를 읽고 서버에.</span><span class="sxs-lookup"><span data-stu-id="e673b-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="e673b-133"><xref:System.Collections.Generic.IAsyncEnumerable%601> 메서드의 버전을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-133">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<Stream> stream) 
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="e673b-134">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="e673b-134">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="e673b-135">서버와 클라이언트 간 스트리밍</span><span class="sxs-lookup"><span data-stu-id="e673b-135">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e673b-136">합니다 `StreamAsync` 하 고 `StreamAsChannelAsync` 메서드를 `HubConnection` 서버와 클라이언트 간 스트리밍 메서드를 호출 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-136">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="e673b-137">허브 메서드 이름 및 허브 메서드를 정의 하는 인수를 전달할 `StreamAsync` 또는 `StreamAsChannelAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-137">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="e673b-138">제네릭 매개 변수를 `StreamAsync<T>` 고 `StreamAsChannelAsync<T>` 스트리밍 메서드에 의해 반환 된 개체의 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-138">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="e673b-139">형식의 개체 `IAsyncEnumerable<T>` 또는 `ChannelReader<T>` stream 호출에서 반환 되 고 클라이언트에서 스트림을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-139">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="e673b-140">A `StreamAsync` 를 반환 하는 예제 `IAsyncEnumerable<int>`:</span><span class="sxs-lookup"><span data-stu-id="e673b-140">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

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

<span data-ttu-id="e673b-141">해당 `StreamAsChannelAsync` 를 반환 하는 예제 `ChannelReader<int>`:</span><span class="sxs-lookup"><span data-stu-id="e673b-141">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

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

<span data-ttu-id="e673b-142">합니다 `StreamAsChannelAsync` 메서드를 `HubConnection` 서버와 클라이언트 간 스트리밍 메서드를 호출 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-142">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="e673b-143">허브 메서드 이름 및 허브 메서드를 정의 하는 인수를 전달 `StreamAsChannelAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-143">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="e673b-144">`StreamAsChannelAsync<T>`의 제네릭 매개 변수는 스트리밍 메서드에서 반환되는 개체의 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-144">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="e673b-145">`ChannelReader<T>` stream 호출에서 반환 되 고 클라이언트에서 스트림을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-145">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

<span data-ttu-id="e673b-146">합니다 `StreamAsChannelAsync` 메서드를 `HubConnection` 서버와 클라이언트 간 스트리밍 메서드를 호출 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-146">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="e673b-147">허브 메서드 이름 및 허브 메서드를 정의 하는 인수를 전달 `StreamAsChannelAsync`합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-147">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="e673b-148">`StreamAsChannelAsync<T>`의 제네릭 매개 변수는 스트리밍 메서드에서 반환되는 개체의 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-148">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="e673b-149">`ChannelReader<T>` stream 호출에서 반환 되 고 클라이언트에서 스트림을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-149">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="e673b-150">클라이언트와 서버-스트리밍</span><span class="sxs-lookup"><span data-stu-id="e673b-150">Client-to-server streaming</span></span>

<span data-ttu-id="e673b-151">.NET 클라이언트에서 클라이언트와 서버 간 스트리밍 허브 메서드를 호출 하는 방법은 두 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-151">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="e673b-152">전달 하거나 수를 `IAsyncEnumerable<T>` 또는 `ChannelReader` 인수로 `SendAsync`, `InvokeAsync`, 또는 `StreamAsChannelAsync`허브 메서드 호출에 따라 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-152">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="e673b-153">데이터를 쓸 때마다 합니다 `IAsyncEnumerable` 또는 `ChannelWriter` 개체를 서버의 허브 메서드를 클라이언트에서 데이터를 사용 하 여 새 항목을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-153">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="e673b-154">사용 하는 경우는 `IAsyncEnumerable` 개체를 스트림에 스트림 항목 종료를 반환 하는 메서드에 한 후 종료 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-154">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

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

<span data-ttu-id="e673b-155">사용 중인 경우 또는 `ChannelWriter`를 사용 하 여 채널 완료 `channel.Writer.Complete()`:</span><span class="sxs-lookup"><span data-stu-id="e673b-155">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="e673b-156">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="e673b-156">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="e673b-157">서버와 클라이언트 간 스트리밍</span><span class="sxs-lookup"><span data-stu-id="e673b-157">Server-to-client streaming</span></span>

<span data-ttu-id="e673b-158">사용 하 여 허브에서 서버와 클라이언트 간 스트리밍 메서드를 호출 하는 JavaScript 클라이언트 `connection.stream`합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-158">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="e673b-159">이 `stream` 메서드는 두 가지 인수를 전달받습니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-159">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="e673b-160">허브 메서드의 이름.</span><span class="sxs-lookup"><span data-stu-id="e673b-160">The name of the hub method.</span></span> <span data-ttu-id="e673b-161">다음 예제에서 허브 메서드 이름은 `Counter`입니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-161">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="e673b-162">허브 메서드에 정의된 인수.</span><span class="sxs-lookup"><span data-stu-id="e673b-162">Arguments defined in the hub method.</span></span> <span data-ttu-id="e673b-163">다음 예제에서는 인수에는 스트림 항목은 수신 하 고 스트림 항목 사이의 지연 시간 수의 개수입니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-163">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="e673b-164">`connection.stream` 반환 합니다는 `IStreamResult`를 포함 하는 `subscribe` 메서드.</span><span class="sxs-lookup"><span data-stu-id="e673b-164">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="e673b-165">전달는 `IStreamSubscriber` 를 `subscribe` 설정 합니다 `next`, `error`, 및 `complete` 에서 알림을 받을 콜백을 `stream` 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-165">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="e673b-166">클라이언트에서 스트림에 호출 합니다 `dispose` 메서드를 `ISubscription` 에서 반환 되는 `subscribe` 메서드.</span><span class="sxs-lookup"><span data-stu-id="e673b-166">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="e673b-167">이 메서드를 호출 하면 취소 된 `CancellationToken` 허브 메서드를 하나 제공한 경우의 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-167">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="e673b-168">클라이언트에서 스트림에 호출 합니다 `dispose` 메서드를 `ISubscription` 에서 반환 되는 `subscribe` 메서드.</span><span class="sxs-lookup"><span data-stu-id="e673b-168">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="e673b-169">클라이언트와 서버-스트리밍</span><span class="sxs-lookup"><span data-stu-id="e673b-169">Client-to-server streaming</span></span>

<span data-ttu-id="e673b-170">JavaScript 클라이언트 허브에 전달 하 여 클라이언트와 서버 간 스트리밍 메서드를 호출을 `Subject` 인수로 `send`를 `invoke`, 또는 `stream`허브 메서드 호출에 따라 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-170">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="e673b-171">합니다 `Subject` 는 다음과 같은 클래스를 `Subject`입니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-171">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="e673b-172">예를 들어 RxJS를 사용할 수 있습니다 합니다 [주체](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) 해당 라이브러리의에서 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-172">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="e673b-173">호출 `subject.next(item)` 사용 하 여 항목을 스트림에 쓸 항목 및 허브 메서드를 서버에 있는 항목을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-173">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="e673b-174">스트림, 호출 `subject.complete()`합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-174">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="e673b-175">Java 클라이언트</span><span class="sxs-lookup"><span data-stu-id="e673b-175">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="e673b-176">서버와 클라이언트 간 스트리밍</span><span class="sxs-lookup"><span data-stu-id="e673b-176">Server-to-client streaming</span></span>

<span data-ttu-id="e673b-177">SignalR Java 클라이언트를 사용 하 여 `stream` 스트리밍 메서드를 호출 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-177">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="e673b-178">`stream` 3 개 이상의 인수를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-178">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="e673b-179">스트림 아이템의 예상된 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-179">The expected type of the stream items.</span></span>
* <span data-ttu-id="e673b-180">허브 메서드의 이름.</span><span class="sxs-lookup"><span data-stu-id="e673b-180">The name of the hub method.</span></span>
* <span data-ttu-id="e673b-181">허브 메서드에 정의된 인수.</span><span class="sxs-lookup"><span data-stu-id="e673b-181">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="e673b-182">합니다 `stream` 메서드를 `HubConnection` 스트림 항목 형식의 관찰 가능 개체를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-182">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="e673b-183">Observable 형식 `subscribe` 메서드는 `onNext`를 `onError` 고 `onCompleted` 처리기가 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="e673b-183">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e673b-184">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e673b-184">Additional resources</span></span>

* [<span data-ttu-id="e673b-185">허브</span><span class="sxs-lookup"><span data-stu-id="e673b-185">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="e673b-186">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="e673b-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="e673b-187">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="e673b-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="e673b-188">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="e673b-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
