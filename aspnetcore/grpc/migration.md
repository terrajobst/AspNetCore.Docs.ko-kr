---
title: C 코어에서 ASP.NET Core로 gRPC 서비스 마이그레이션
author: juntaoluo
description: ASP.NET Core 스택 위에서 실행 되도록 기존 C 코어 기반 gRPC 앱을 이동 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/migration
ms.openlocfilehash: 39aa711a1a47cf11ec5b08903b4130c7caa1501c
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022292"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="d43fb-103">C 코어에서 ASP.NET Core로 gRPC 서비스 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="d43fb-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="d43fb-104">작성자: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="d43fb-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="d43fb-105">기본 스택의 구현 때문에 모든 기능이 [C 코어 기반 gRPC](https://grpc.io/blog/grpc-stacks) 앱과 ASP.NET Core 기반 앱 간의 동일한 방식으로 작동 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="d43fb-106">이 문서에서는 두 스택 간의 마이그레이션에 대 한 주요 차이점을 강조 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="d43fb-107">gRPC 서비스 구현 수명</span><span class="sxs-lookup"><span data-stu-id="d43fb-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="d43fb-108">ASP.NET Core 스택에서 gRPC 서비스는 기본적으로 범위가 지정 된 [수명](xref:fundamentals/dependency-injection#service-lifetimes)으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="d43fb-109">이와 대조적으로 gRPC C-core는 기본적으로 [단일 수명이](xref:fundamentals/dependency-injection#service-lifetimes)포함 된 서비스에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="d43fb-110">범위가 지정 된 수명으로 서비스 구현에서 범위가 지정 된 수명으로 다른 서비스를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="d43fb-111">예를 들어, 범위 지정 수명은 생성자 주입 `DbContext` 을 통해 DI 컨테이너에서 확인할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="d43fb-112">범위가 지정 된 수명 사용:</span><span class="sxs-lookup"><span data-stu-id="d43fb-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="d43fb-113">서비스 구현의 새 인스턴스는 각 요청에 대해 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="d43fb-114">구현 형식에서 인스턴스 멤버를 통해 요청 간에 상태를 공유할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="d43fb-115">공유 상태를 DI 컨테이너의 단일 서비스에 저장 하는 것이 예상 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="d43fb-116">저장 된 공유 상태는 gRPC 서비스 구현의 생성자에서 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="d43fb-117">서비스 수명에 대 한 자세한 내용은을 <xref:fundamentals/dependency-injection#service-lifetimes>참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="d43fb-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="d43fb-118">단일 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="d43fb-118">Add a singleton service</span></span>

<span data-ttu-id="d43fb-119">GRPC C 코어 구현에서 ASP.NET Core로 전환 하는 것을 용이 하 게 하기 위해 서비스 구현의 서비스 수명 범위를 singleton으로 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="d43fb-120">여기에는 서비스 구현 인스턴스를 DI 컨테이너에 추가 하는 작업이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="d43fb-121">그러나 singleton 수명이 포함 된 서비스 구현은 더 이상 생성자 주입을 통해 범위 지정 서비스를 확인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="d43fb-122">GRPC 서비스 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="d43fb-122">Configure gRPC services options</span></span>

<span data-ttu-id="d43fb-123">C 코어 기반 앱 `grpc.max_receive_message_length` 에서 및 `grpc.max_send_message_length` 와 같은 설정은 [서버 인스턴스를 생성할](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)때로 `ChannelOption` 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="d43fb-124">ASP.NET Core에서 grpc는 `GrpcServiceOptions` 유형을 통해 구성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="d43fb-125">예를 들어 gRPC 서비스의 최대 수신 메시지 크기는 다음을 통해 `AddGrpc`구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 16384; // 16 MB
    });
}
```

<span data-ttu-id="d43fb-126">구성에 대 한 자세한 내용은을 <xref:grpc/configuration>참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="d43fb-126">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="d43fb-127">로깅</span><span class="sxs-lookup"><span data-stu-id="d43fb-127">Logging</span></span>

<span data-ttu-id="d43fb-128">C 코어 기반 앱은를 `GrpcEnvironment` 사용 하 여 디버깅을 위해 [로 거를 구성](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-128">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="d43fb-129">ASP.NET Core 스택은 [로깅 API](xref:fundamentals/logging/index)를 통해이 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-129">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="d43fb-130">예를 들어 생성자 주입을 통해 gRPC 서비스에로 거를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-130">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="d43fb-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d43fb-131">HTTPS</span></span>

<span data-ttu-id="d43fb-132">C 코어 기반 앱은 [Server. Ports 속성](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)을 통해 HTTPS를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-132">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="d43fb-133">ASP.NET Core에서 서버를 구성 하는 데 비슷한 개념을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-133">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="d43fb-134">예를 들어 Kestrel은이 기능에 대해 [끝점 구성을](xref:fundamentals/servers/kestrel#endpoint-configuration) 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-134">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="interceptors-and-middleware"></a><span data-ttu-id="d43fb-135">인터셉터 및 미들웨어</span><span class="sxs-lookup"><span data-stu-id="d43fb-135">Interceptors and Middleware</span></span>

<span data-ttu-id="d43fb-136">ASP.NET Core [미들웨어](xref:fundamentals/middleware/index) 는 C 코어 기반 grpc 앱의 인터셉터와 비교할 때 비슷한 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-136">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="d43fb-137">미들웨어와 인터셉터는 모두 gRPC 요청을 처리 하는 파이프라인을 생성 하는 데 사용 되는 것과 개념적으로 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-137">Middleware and interceptors are conceptually the same as both are used to construct a pipeline that handles a gRPC request.</span></span> <span data-ttu-id="d43fb-138">파이프라인의 다음 구성 요소 전이나 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-138">They both allow work to be performed before or after the next component in the pipeline.</span></span> <span data-ttu-id="d43fb-139">그러나 ASP.NET Core 미들웨어는 기본 HTTP/2 메시지에 대해 작동 하는 반면 인터셉터는 [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)을 사용 하 여 추상화의 grpc 계층에서 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43fb-139">However, ASP.NET Core middleware operates on the underlying HTTP/2 messages, while interceptors operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d43fb-140">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d43fb-140">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
