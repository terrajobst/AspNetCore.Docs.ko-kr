---
title: .NET Core에서 gRPC 클라이언트 팩터리 통합
author: jamesnk
description: 클라이언트 팩터리를 사용 하 여 gRPC 클라이언트를 만드는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 3042bb61367f8b9a9f3142217ad329270ab2cca5
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963677"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>.NET Core에서 gRPC 클라이언트 팩터리 통합

`HttpClientFactory`와 gRPC 통합은 gRPC 클라이언트를 중앙 집중식으로 만드는 방법을 제공 합니다. [독립 실행형 gRPC 클라이언트 인스턴스를 구성](xref:grpc/client)하는 대신 사용할 수 있습니다. 공장 통합은 [Grpc .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet 패키지에서 사용할 수 있습니다.

팩터리는 다음과 같은 이점을 제공 합니다.

* 논리적 gRPC 클라이언트 인스턴스를 구성 하기 위한 중앙 위치를 제공 합니다.
* 기본 `HttpClientMessageHandler`의 수명을 관리 합니다.
* ASP.NET Core gRPC 서비스에서 최종 기한 및 취소 자동 전파

## <a name="register-grpc-clients"></a>GRPC 클라이언트 등록

GRPC 클라이언트를 등록 하기 위해 `Startup.ConfigureServices`내에서 일반 `AddGrpcClient` 확장 메서드를 사용 하 여 gRPC 형식의 클라이언트 클래스 및 서비스 주소를 지정할 수 있습니다.

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

GRPC 클라이언트 형식은 DI (종속성 주입)를 사용 하 여 일시적으로 등록 됩니다. 이제 DI를 사용 하 여 만든 형식에서 클라이언트를 직접 삽입 하 고 사용할 수 있습니다. ASP.NET Core MVC 컨트롤러, SignalR 허브 및 gRPC 서비스는 gRPC 클라이언트가 자동으로 삽입 될 수 있는 위치입니다.

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>HttpClient 구성

`HttpClientFactory` gRPC 클라이언트에서 사용 하는 `HttpClient`을 만듭니다. 표준 `HttpClientFactory` 메서드는 나가는 요청 미들웨어를 추가 하거나 `HttpClient`의 기본 `HttpClientHandler`를 구성 하는 데 사용할 수 있습니다.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

자세한 내용은 [IHttpClientFactory를 사용 하 여 HTTP 요청 만들기](xref:fundamentals/http-requests)를 참조 하세요.

## <a name="configure-channel-and-interceptors"></a>채널 및 인터셉터 구성

gRPC 관련 메서드는 다음에 사용할 수 있습니다.

* GRPC 클라이언트의 기본 채널을 구성 합니다.
* 클라이언트에서 gRPC 호출을 만들 때 사용할 `Interceptor` 인스턴스를 추가 합니다.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>최종 기한 및 취소 전파

gRPC 서비스의 팩터리에서 만든 gRPC 클라이언트는 최종 기한 및 취소 토큰을 자식 호출에 자동으로 전파 하도록 `EnableCallContextPropagation()`를 사용 하 여 구성할 수 있습니다. `EnableCallContextPropagation()` 확장 메서드는 [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet 패키지에서 사용할 수 있습니다.

호출 컨텍스트 전파는 현재 gRPC 요청 컨텍스트에서 최종 기한 및 취소 토큰을 읽고 gRPC 클라이언트에서 수행 하는 나가는 호출에 자동으로 전파 하는 방식으로 작동 합니다. 호출 컨텍스트 전파는 복잡 한 중첩 된 gRPC 시나리오에서 항상 최종 기한 및 취소를 전파 하는 좋은 방법입니다.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

마감일 및 RPC 취소에 대 한 자세한 내용은 [rpc 수명 주기](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle)를 참조 하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
