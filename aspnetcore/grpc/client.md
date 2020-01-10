---
title: .NET 클라이언트를 사용 하 여 gRPC 서비스 호출
author: jamesnk
description: .NET gRPC 클라이언트를 사용 하 여 gRPC 서비스를 호출 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 1e7887388a752fb35d00e65db210c3924c6ab192
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829103"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="2f5b6-103">.NET 클라이언트를 사용 하 여 gRPC 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="2f5b6-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="2f5b6-104">.NET gRPC 클라이언트 라이브러리는 [grpc .net. 클라이언트](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="2f5b6-105">이 문서에서는 다음을 수행 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-105">This document explains how to:</span></span>

* <span data-ttu-id="2f5b6-106">Grpc 서비스를 호출 하도록 gRPC 클라이언트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="2f5b6-107">단항, 서버 스트리밍, 클라이언트 스트리밍 및 양방향 스트리밍 방법에 대 한 gRPC 호출을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="2f5b6-108">GRPC 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="2f5b6-108">Configure gRPC client</span></span>

<span data-ttu-id="2f5b6-109">gRPC 클라이언트는 [ *\*.proto* 파일에서 생성](xref:grpc/basics#generated-c-assets)되는 구체적인 클라이언트 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="2f5b6-110">구체적인 gRPC 클라이언트에는 *\*.proto* 파일에서 gRPC 서비스로 변환되는 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="2f5b6-111">채널에서 gRPC 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="2f5b6-112">먼저 `GrpcChannel.ForAddress`를 사용 하 여 채널을 만든 다음 채널을 사용 하 여 gRPC 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="2f5b6-113">채널은 gRPC 서비스에 대 한 수명이 긴 연결을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="2f5b6-114">채널을 만들면 서비스 호출과 관련 된 옵션으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="2f5b6-115">예를 들어 호출을 수행 하는 데 사용 되는 `HttpClient` 최대 전송 및 수신 메시지 크기 및 로깅을 `GrpcChannelOptions`에 지정 하 고 `GrpcChannel.ForAddress`와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="2f5b6-116">전체 옵션 목록은 [클라이언트 구성 옵션](xref:grpc/configuration#configure-client-options)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="2f5b6-117">채널 및 클라이언트 성능 및 사용:</span><span class="sxs-lookup"><span data-stu-id="2f5b6-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="2f5b6-118">채널을 만드는 작업은 비용이 많이 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="2f5b6-119">GRPC 호출에 채널을 재사용 하면 성능상의 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="2f5b6-120">gRPC 클라이언트는 채널을 사용 하 여 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="2f5b6-121">gRPC 클라이언트는 경량 개체 이며 캐시 하거나 다시 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="2f5b6-122">여러 gRPC 클라이언트를 다양 한 유형의 클라이언트를 포함 하는 채널에서 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="2f5b6-123">채널 및 채널에서 만든 클라이언트는 여러 스레드에서 안전 하 게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="2f5b6-124">채널에서 만든 클라이언트는 여러 동시 호출을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="2f5b6-125">`GrpcChannel.ForAddress`는 gRPC 클라이언트를 만들기 위한 유일한 옵션은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="2f5b6-126">ASP.NET Core 앱에서 gRPC 서비스를 호출 하는 경우 [grpc 클라이언트 팩터리 통합](xref:grpc/clientfactory)을 고려 하세요.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-126">If you're calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="2f5b6-127">`HttpClientFactory`와 gRPC 통합은 gRPC 클라이언트를 만드는 중앙의 대안을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="2f5b6-128">[.Net 클라이언트를 사용 하 여 안전 하지 않은 gRPC 서비스를 호출](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)하려면 추가 구성이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="2f5b6-129">GRPC 호출 만들기</span><span class="sxs-lookup"><span data-stu-id="2f5b6-129">Make gRPC calls</span></span>

<span data-ttu-id="2f5b6-130">GRPC 호출은 클라이언트에서 메서드를 호출 하 여 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-130">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="2f5b6-131">GRPC 클라이언트는 메시지 serialization을 처리 하 고 올바른 서비스에 대 한 gRPC 호출의 주소를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-131">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="2f5b6-132">gRPC에는 다양 한 유형의 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-132">gRPC has different types of methods.</span></span> <span data-ttu-id="2f5b6-133">클라이언트를 사용 하 여 gRPC 호출을 만드는 방법은 호출 하는 메서드의 유형에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-133">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="2f5b6-134">GRPC 메서드 형식은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-134">The gRPC method types are:</span></span>

* <span data-ttu-id="2f5b6-135">단항</span><span class="sxs-lookup"><span data-stu-id="2f5b6-135">Unary</span></span>
* <span data-ttu-id="2f5b6-136">서버 스트리밍</span><span class="sxs-lookup"><span data-stu-id="2f5b6-136">Server streaming</span></span>
* <span data-ttu-id="2f5b6-137">클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="2f5b6-137">Client streaming</span></span>
* <span data-ttu-id="2f5b6-138">양방향 스트리밍</span><span class="sxs-lookup"><span data-stu-id="2f5b6-138">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="2f5b6-139">단항 호출</span><span class="sxs-lookup"><span data-stu-id="2f5b6-139">Unary call</span></span>

<span data-ttu-id="2f5b6-140">단항 호출은 클라이언트에서 요청 메시지를 보내는 것으로 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-140">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="2f5b6-141">서비스가 완료 되 면 응답 메시지가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-141">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="2f5b6-142">*\*proto* 파일의 각 단항 서비스 메서드는 메서드를 호출 하기 위한 구체적인 grpc 클라이언트 형식에 대 한 두 가지 .net 메서드인 비동기 메서드와 블로킹 메서드를 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-142">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="2f5b6-143">예를 들어 `GreeterClient`에서 `SayHello`를 호출 하는 방법에는 두 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-143">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="2f5b6-144">`GreeterClient.SayHelloAsync` `Greeter.SayHello` 서비스를 비동기적으로 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-144">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="2f5b6-145">대기 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-145">Can be awaited.</span></span>
* <span data-ttu-id="2f5b6-146">`GreeterClient.SayHello` `Greeter.SayHello` 서비스를 호출 하 고 완료 될 때까지 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-146">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="2f5b6-147">비동기 코드에서는를 사용 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-147">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="2f5b6-148">서버 스트리밍 호출</span><span class="sxs-lookup"><span data-stu-id="2f5b6-148">Server streaming call</span></span>

<span data-ttu-id="2f5b6-149">서버 스트리밍 호출은 요청 메시지를 보내는 클라이언트에서 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-149">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="2f5b6-150">`ResponseStream.MoveNext()` 서비스에서 스트리밍된 메시지를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-150">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="2f5b6-151">`ResponseStream.MoveNext()`에서 `false`를 반환할 때 서버 스트리밍 호출이 완료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-151">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

<span data-ttu-id="2f5b6-152">8 이상을 사용 하 C# 는 경우 `await foreach` 구문을 사용 하 여 메시지를 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-152">If you are using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="2f5b6-153">`IAsyncStreamReader<T>.ReadAllAsync()` 확장 메서드는 응답 스트림에서 모든 메시지를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-153">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="2f5b6-154">클라이언트 스트리밍 호출</span><span class="sxs-lookup"><span data-stu-id="2f5b6-154">Client streaming call</span></span>

<span data-ttu-id="2f5b6-155">클라이언트에서 메시지를 보내지 *않고* 클라이언트 스트리밍 호출이 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-155">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="2f5b6-156">클라이언트는 `RequestStream.WriteAsync`로 메시지를 보내도록 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-156">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="2f5b6-157">클라이언트가 메시지 전송을 완료 하면 서비스에 알리기 위해 `RequestStream.CompleteAsync`를 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-157">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="2f5b6-158">서비스에서 응답 메시지를 반환 하면 호출이 완료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-158">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using (var call = client.AccumulateCount())
{
    for (var i = 0; i < 3; i++)
    {
        await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
    }
    await call.RequestStream.CompleteAsync();

    var response = await call;
    Console.WriteLine($"Count: {response.Count}");
    // Count: 3
}
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="2f5b6-159">양방향 스트리밍 호출</span><span class="sxs-lookup"><span data-stu-id="2f5b6-159">Bi-directional streaming call</span></span>

<span data-ttu-id="2f5b6-160">클라이언트에서 메시지를 보내지 *않고* 양방향 스트리밍 호출이 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-160">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="2f5b6-161">클라이언트는 `RequestStream.WriteAsync`로 메시지를 보내도록 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-161">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="2f5b6-162">서비스에서 스트리밍된 메시지는 `ResponseStream.MoveNext()` 또는 `ResponseStream.ReadAllAsync()`를 사용 하 여 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-162">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="2f5b6-163">`ResponseStream`에 더 이상 메시지가 없으면 양방향 스트리밍 호출이 완료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-163">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
using (var call = client.Echo())
{
    Console.WriteLine("Starting background task to receive messages");
    var readTask = Task.Run(async () =>
    {
        await foreach (var response in call.ResponseStream.ReadAllAsync())
        {
            Console.WriteLine(response.Message);
            // Echo messages sent to the service
        }
    });

    Console.WriteLine("Starting to send messages");
    Console.WriteLine("Type a message to echo then press enter.");
    while (true)
    {
        var result = Console.ReadLine();
        if (string.IsNullOrEmpty(result))
        {
            break;
        }

        await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
    }

    Console.WriteLine("Disconnecting");
    await call.RequestStream.CompleteAsync();
    await readTask;
}
```

<span data-ttu-id="2f5b6-164">양방향 스트리밍 호출 중에 클라이언트와 서비스는 언제 든 지 서로 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-164">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="2f5b6-165">양방향 호출과 상호 작용 하는 데 가장 적합 한 클라이언트 논리는 서비스 논리에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="2f5b6-165">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2f5b6-166">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2f5b6-166">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
