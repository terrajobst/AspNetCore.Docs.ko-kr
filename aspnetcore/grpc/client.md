---
title: .NET 클라이언트를 사용 하 여 gRPC 서비스 호출
author: jamesnk
description: .NET gRPC 클라이언트를 사용 하 여 gRPC 서비스를 호출 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 27e4b3e7307ae49bacb01a46fbc1b55b6967c7a0
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773687"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="cb97a-103">.NET 클라이언트를 사용 하 여 gRPC 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="cb97a-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="cb97a-104">.NET gRPC 클라이언트 라이브러리는 [grpc .net. 클라이언트](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="cb97a-105">이 문서에서는 다음을 수행 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-105">This document explains how to:</span></span>

* <span data-ttu-id="cb97a-106">Grpc 서비스를 호출 하도록 gRPC 클라이언트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="cb97a-107">단항, 서버 스트리밍, 클라이언트 스트리밍 및 양방향 스트리밍 방법에 대 한 gRPC 호출을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="cb97a-108">GRPC 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="cb97a-108">Configure gRPC client</span></span>

<span data-ttu-id="cb97a-109">grpc 클라이언트는 [.pfiles에서  *\** 생성](xref:grpc/basics#generated-c-assets)되는 구체적인 클라이언트 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="cb97a-110">구체적 grpc 클라이언트에는  *\*.pfile에서* grpc 서비스로 변환 하는 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="cb97a-111">채널에서 gRPC 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="cb97a-112">먼저를 사용 `GrpcChannel.ForAddress` 하 여 채널을 만든 다음 채널을 사용 하 여 grpc 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="cb97a-113">채널은 gRPC 서비스에 대 한 수명이 긴 연결을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="cb97a-114">채널을 만들면 서비스 호출과 관련 된 옵션으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-114">When a channel is created it is configured with options related to calling a service.</span></span> <span data-ttu-id="cb97a-115">예 `HttpClient` 를 들어 호출을 수행 하는 데 사용 되는, 최대 송신 및 수신 메시지 크기 및 로깅을 지정 하 `GrpcChannelOptions` 고에서 사용할 `GrpcChannel.ForAddress`수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="cb97a-116">전체 옵션 목록은 [클라이언트 구성 옵션](xref:grpc/configuration#configure-client-options)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb97a-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

<span data-ttu-id="cb97a-117">채널을 만드는 작업은 비용이 많이 들고, gRPC 호출에 채널을 다시 사용 하면 성능상의 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-117">Creating a channel can be an expensive operation and reusing a channel for gRPC calls offers performance benefits.</span></span> <span data-ttu-id="cb97a-118">여러 가지 유형의 클라이언트를 포함 하 여 채널에서 여러 개의 구체적 gRPC 클라이언트를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-118">Multiple concrete gRPC clients can be created from a channel, including different types of clients.</span></span> <span data-ttu-id="cb97a-119">구체적 gRPC 클라이언트 형식은 경량 개체 이며 필요할 때 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-119">Concrete gRPC client types are lightweight objects and can be created when needed.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="cb97a-120">`GrpcChannel.ForAddress`는 gRPC 클라이언트를 만들기 위한 유일한 옵션은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-120">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="cb97a-121">ASP.NET Core 앱에서 gRPC 서비스를 호출 하는 경우 [grpc 클라이언트 팩터리 통합](xref:grpc/clientfactory)을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-121">If you are calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="cb97a-122">grpc 통합 `HttpClientFactory` 은 grpc 클라이언트를 만드는 중앙의 대안을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-122">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="cb97a-123">GRPC 호출 만들기</span><span class="sxs-lookup"><span data-stu-id="cb97a-123">Make gRPC calls</span></span>

<span data-ttu-id="cb97a-124">GRPC 호출은 클라이언트에서 메서드를 호출 하 여 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-124">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="cb97a-125">GRPC 클라이언트는 메시지 serialization을 처리 하 고 올바른 서비스에 대 한 gRPC 호출의 주소를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-125">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="cb97a-126">gRPC에는 다양 한 유형의 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-126">gRPC has different types of methods.</span></span> <span data-ttu-id="cb97a-127">클라이언트를 사용 하 여 gRPC 호출을 만드는 방법은 호출 하는 메서드의 유형에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-127">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="cb97a-128">GRPC 메서드 형식은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-128">The gRPC method types are:</span></span>

* <span data-ttu-id="cb97a-129">단항</span><span class="sxs-lookup"><span data-stu-id="cb97a-129">Unary</span></span>
* <span data-ttu-id="cb97a-130">서버 스트리밍</span><span class="sxs-lookup"><span data-stu-id="cb97a-130">Server streaming</span></span>
* <span data-ttu-id="cb97a-131">클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="cb97a-131">Client streaming</span></span>
* <span data-ttu-id="cb97a-132">양방향 스트리밍</span><span class="sxs-lookup"><span data-stu-id="cb97a-132">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="cb97a-133">단항 호출</span><span class="sxs-lookup"><span data-stu-id="cb97a-133">Unary call</span></span>

<span data-ttu-id="cb97a-134">단항 호출은 클라이언트에서 요청 메시지를 보내는 것으로 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-134">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="cb97a-135">서비스가 완료 되 면 응답 메시지가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-135">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="cb97a-136">*\*Proto* 파일의 각 단항 서비스 메서드는 메서드를 호출 하기 위한 구체적인 grpc 클라이언트 형식에 대 한 두 가지 .net 메서드인 비동기 메서드와 차단 메서드를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-136">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="cb97a-137">예를 들어에서 `GreeterClient` 를 호출 `SayHello`하는 방법에는 다음 두 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-137">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="cb97a-138">`GreeterClient.SayHelloAsync`-비동기적 `Greeter.SayHello` 으로 서비스를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-138">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="cb97a-139">대기 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-139">Can be awaited.</span></span>
* <span data-ttu-id="cb97a-140">`GreeterClient.SayHello`-서비스 `Greeter.SayHello` 를 호출 하 고 완료 될 때까지 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-140">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="cb97a-141">비동기 코드에서는를 사용 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="cb97a-141">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="cb97a-142">서버 스트리밍 호출</span><span class="sxs-lookup"><span data-stu-id="cb97a-142">Server streaming call</span></span>

<span data-ttu-id="cb97a-143">서버 스트리밍 호출은 요청 메시지를 보내는 클라이언트에서 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-143">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="cb97a-144">`ResponseStream.MoveNext()`서비스에서 스트리밍된 메시지를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-144">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="cb97a-145">서버 스트리밍 호출은를 반환할 `ResponseStream.MoveNext()` `false`때 완료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-145">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="cb97a-146">8`await foreach` 이상을 사용 하 C# 는 경우에는 구문을 사용 하 여 메시지를 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-146">If you are using C# 8 or later then the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="cb97a-147">확장 `IAsyncStreamReader<T>.ReadAllAsync()` 메서드는 응답 스트림에서 모든 메시지를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-147">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="cb97a-148">클라이언트 스트리밍 호출</span><span class="sxs-lookup"><span data-stu-id="cb97a-148">Client streaming call</span></span>

<span data-ttu-id="cb97a-149">클라이언트에서 메시지를 보내지 *않고* 클라이언트 스트리밍 호출이 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-149">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="cb97a-150">클라이언트는를 사용 `RequestStream.WriteAsync`하 여 전송 메시지를 보내도록 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-150">The client can choose to send sends messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="cb97a-151">클라이언트를 완료 한 후에는 `RequestStream.CompleteAsync` 서비스에 알리기 위해 메시지 보내기를 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-151">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="cb97a-152">서비스에서 응답 메시지를 반환 하면 호출이 완료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-152">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="cb97a-153">양방향 스트리밍 호출</span><span class="sxs-lookup"><span data-stu-id="cb97a-153">Bi-directional streaming call</span></span>

<span data-ttu-id="cb97a-154">클라이언트에서 메시지를 보내지 *않고* 양방향 스트리밍 호출이 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-154">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="cb97a-155">클라이언트는를 사용 `RequestStream.WriteAsync`하 여 메시지를 보내도록 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-155">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="cb97a-156">서비스에서 스트리밍된 메시지는 또는 `ResponseStream.MoveNext()` `ResponseStream.ReadAllAsync()`를 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-156">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="cb97a-157">에 `ResponseStream` 더 이상 메시지가 없으면 양방향 스트리밍 호출이 완료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-157">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="cb97a-158">양방향 스트리밍 호출 중에 클라이언트와 서비스는 언제 든 지 서로 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-158">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="cb97a-159">양방향 호출과 상호 작용 하는 데 가장 적합 한 클라이언트 논리는 서비스 논리에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="cb97a-159">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cb97a-160">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cb97a-160">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
