---
title: .NET Core에서 gRPC 클라이언트 팩터리 통합
author: jamesnk
description: 클라이언트 팩터리를 사용 하 여 gRPC 클라이언트를 만드는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/clientfactory
ms.openlocfilehash: 5d719893e96ae017e2de0ee1744003d2d67a49c9
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773673"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="cb729-103">.NET Core에서 gRPC 클라이언트 팩터리 통합</span><span class="sxs-lookup"><span data-stu-id="cb729-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="cb729-104">grpc 통합 `HttpClientFactory` 은 grpc 클라이언트를 중앙 집중식으로 만드는 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="cb729-105">[독립 실행형 gRPC 클라이언트 인스턴스를 구성](xref:grpc/client)하는 대신 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="cb729-106">공장 통합은 [Grpc .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="cb729-107">팩터리는 다음과 같은 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="cb729-108">논리적 gRPC 클라이언트 인스턴스를 구성 하기 위한 중앙 위치를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="cb729-109">기본의 수명을 관리 합니다.`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="cb729-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="cb729-110">ASP.NET Core gRPC 서비스에서 최종 기한 및 취소 자동 전파</span><span class="sxs-lookup"><span data-stu-id="cb729-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="cb729-111">GRPC 클라이언트 등록</span><span class="sxs-lookup"><span data-stu-id="cb729-111">Register gRPC clients</span></span>

<span data-ttu-id="cb729-112">Grpc 클라이언트를 등록 하려면 내에서 `AddGrpcClient` `Startup.ConfigureServices`grpc 형식의 클라이언트 클래스 및 서비스 주소를 지정 하 여 제네릭 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="cb729-113">GRPC 클라이언트 형식은 DI (종속성 주입)를 사용 하 여 일시적으로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="cb729-114">이제 DI를 사용 하 여 만든 형식에서 클라이언트를 직접 삽입 하 고 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="cb729-115">ASP.NET Core MVC 컨트롤러, SignalR hubs 및 gRPC 서비스는 gRPC 클라이언트가 자동으로 삽입 될 수 있는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

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

## <a name="configure-httpclient"></a><span data-ttu-id="cb729-116">HttpClient 구성</span><span class="sxs-lookup"><span data-stu-id="cb729-116">Configure HttpClient</span></span>

<span data-ttu-id="cb729-117">`HttpClientFactory`grpc `HttpClient` 클라이언트에서 사용 하는를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="cb729-118">표준 `HttpClientFactory` 메서드는 나가는 요청 미들웨어를 추가 하거나의 `HttpClient`기본 `HttpClientHandler` 를 구성 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

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

<span data-ttu-id="cb729-119">자세한 내용은 [IHttpClientFactory를 사용 하 여 HTTP 요청 만들기](xref:fundamentals/http-requests)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb729-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="cb729-120">채널 및 인터셉터 구성</span><span class="sxs-lookup"><span data-stu-id="cb729-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="cb729-121">gRPC 관련 메서드는 다음에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="cb729-122">GRPC 클라이언트의 기본 채널을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="cb729-123">클라이언트 `Interceptor` 에서 grpc 호출을 만들 때 사용할 인스턴스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

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

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="cb729-124">최종 기한 및 취소 전파</span><span class="sxs-lookup"><span data-stu-id="cb729-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="cb729-125">grpc 서비스의 팩터리에서 만든 grpc 클라이언트는 최종 기한 및 취소 토큰을 `EnableCallContextPropagation()` 자식 호출에 자동으로 전파 하도록를 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="cb729-126">`EnableCallContextPropagation()` 확장 메서드는 [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="cb729-127">호출 컨텍스트 전파는 현재 gRPC 요청 컨텍스트에서 최종 기한 및 취소 토큰을 읽고 gRPC 클라이언트에서 수행 하는 나가는 호출에 자동으로 전파 하는 방식으로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="cb729-128">호출 컨텍스트 전파는 복잡 한 중첩 된 gRPC 시나리오에서 항상 최종 기한 및 취소를 전파 하는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="cb729-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="cb729-129">마감일 및 RPC 취소에 대 한 자세한 내용은 [rpc 수명 주기](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb729-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cb729-130">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cb729-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
