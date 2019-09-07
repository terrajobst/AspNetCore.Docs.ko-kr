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
# <a name="call-grpc-services-with-the-net-client"></a>.NET 클라이언트를 사용 하 여 gRPC 서비스 호출

.NET gRPC 클라이언트 라이브러리는 [grpc .net. 클라이언트](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 패키지에서 사용할 수 있습니다. 이 문서에서는 다음을 수행 하는 방법을 설명 합니다.

* Grpc 서비스를 호출 하도록 gRPC 클라이언트를 구성 합니다.
* 단항, 서버 스트리밍, 클라이언트 스트리밍 및 양방향 스트리밍 방법에 대 한 gRPC 호출을 수행 합니다.

## <a name="configure-grpc-client"></a>GRPC 클라이언트 구성

grpc 클라이언트는 [.pfiles에서  *\** 생성](xref:grpc/basics#generated-c-assets)되는 구체적인 클라이언트 유형입니다. 구체적 grpc 클라이언트에는  *\*.pfile에서* grpc 서비스로 변환 하는 메서드가 있습니다.

채널에서 gRPC 클라이언트를 만듭니다. 먼저를 사용 `GrpcChannel.ForAddress` 하 여 채널을 만든 다음 채널을 사용 하 여 grpc 클라이언트를 만듭니다.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

채널은 gRPC 서비스에 대 한 수명이 긴 연결을 나타냅니다. 채널을 만들면 서비스 호출과 관련 된 옵션으로 구성 됩니다. 예 `HttpClient` 를 들어 호출을 수행 하는 데 사용 되는, 최대 송신 및 수신 메시지 크기 및 로깅을 지정 하 `GrpcChannelOptions` 고에서 사용할 `GrpcChannel.ForAddress`수 있습니다. 전체 옵션 목록은 [클라이언트 구성 옵션](xref:grpc/configuration#configure-client-options)을 참조 하세요.

채널을 만드는 작업은 비용이 많이 들고, gRPC 호출에 채널을 다시 사용 하면 성능상의 이점이 있습니다. 여러 가지 유형의 클라이언트를 포함 하 여 채널에서 여러 개의 구체적 gRPC 클라이언트를 만들 수 있습니다. 구체적 gRPC 클라이언트 형식은 경량 개체 이며 필요할 때 만들 수 있습니다.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

`GrpcChannel.ForAddress`는 gRPC 클라이언트를 만들기 위한 유일한 옵션은 아닙니다. ASP.NET Core 앱에서 gRPC 서비스를 호출 하는 경우 [grpc 클라이언트 팩터리 통합](xref:grpc/clientfactory)을 고려 합니다. grpc 통합 `HttpClientFactory` 은 grpc 클라이언트를 만드는 중앙의 대안을 제공 합니다.

## <a name="make-grpc-calls"></a>GRPC 호출 만들기

GRPC 호출은 클라이언트에서 메서드를 호출 하 여 시작 됩니다. GRPC 클라이언트는 메시지 serialization을 처리 하 고 올바른 서비스에 대 한 gRPC 호출의 주소를 지정 합니다.

gRPC에는 다양 한 유형의 메서드가 있습니다. 클라이언트를 사용 하 여 gRPC 호출을 만드는 방법은 호출 하는 메서드의 유형에 따라 달라 집니다. GRPC 메서드 형식은 다음과 같습니다.

* 단항
* 서버 스트리밍
* 클라이언트 스트리밍
* 양방향 스트리밍

### <a name="unary-call"></a>단항 호출

단항 호출은 클라이언트에서 요청 메시지를 보내는 것으로 시작 합니다. 서비스가 완료 되 면 응답 메시지가 반환 됩니다.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

*\*Proto* 파일의 각 단항 서비스 메서드는 메서드를 호출 하기 위한 구체적인 grpc 클라이언트 형식에 대 한 두 가지 .net 메서드인 비동기 메서드와 차단 메서드를 생성 합니다. 예를 들어에서 `GreeterClient` 를 호출 `SayHello`하는 방법에는 다음 두 가지가 있습니다.

* `GreeterClient.SayHelloAsync`-비동기적 `Greeter.SayHello` 으로 서비스를 호출 합니다. 대기 수 있습니다.
* `GreeterClient.SayHello`-서비스 `Greeter.SayHello` 를 호출 하 고 완료 될 때까지 차단 합니다. 비동기 코드에서는를 사용 하지 마세요.

### <a name="server-streaming-call"></a>서버 스트리밍 호출

서버 스트리밍 호출은 요청 메시지를 보내는 클라이언트에서 시작 됩니다. `ResponseStream.MoveNext()`서비스에서 스트리밍된 메시지를 읽습니다. 서버 스트리밍 호출은를 반환할 `ResponseStream.MoveNext()` `false`때 완료 됩니다.

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

8`await foreach` 이상을 사용 하 C# 는 경우에는 구문을 사용 하 여 메시지를 읽을 수 있습니다. 확장 `IAsyncStreamReader<T>.ReadAllAsync()` 메서드는 응답 스트림에서 모든 메시지를 읽습니다.

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

### <a name="client-streaming-call"></a>클라이언트 스트리밍 호출

클라이언트에서 메시지를 보내지 *않고* 클라이언트 스트리밍 호출이 시작 됩니다. 클라이언트는를 사용 `RequestStream.WriteAsync`하 여 전송 메시지를 보내도록 선택할 수 있습니다. 클라이언트를 완료 한 후에는 `RequestStream.CompleteAsync` 서비스에 알리기 위해 메시지 보내기를 호출 해야 합니다. 서비스에서 응답 메시지를 반환 하면 호출이 완료 됩니다.

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

### <a name="bi-directional-streaming-call"></a>양방향 스트리밍 호출

클라이언트에서 메시지를 보내지 *않고* 양방향 스트리밍 호출이 시작 됩니다. 클라이언트는를 사용 `RequestStream.WriteAsync`하 여 메시지를 보내도록 선택할 수 있습니다. 서비스에서 스트리밍된 메시지는 또는 `ResponseStream.MoveNext()` `ResponseStream.ReadAllAsync()`를 통해 액세스할 수 있습니다. 에 `ResponseStream` 더 이상 메시지가 없으면 양방향 스트리밍 호출이 완료 됩니다.

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

양방향 스트리밍 호출 중에 클라이언트와 서비스는 언제 든 지 서로 메시지를 보낼 수 있습니다. 양방향 호출과 상호 작용 하는 데 가장 적합 한 클라이언트 논리는 서비스 논리에 따라 달라 집니다.

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
