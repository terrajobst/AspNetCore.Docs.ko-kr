---
title: C 코어에서 ASP.NET Core로 gRPC 서비스 마이그레이션
author: juntaoluo
description: ASP.NET Core 스택 위에서 실행 되도록 기존 C 코어 기반 gRPC 앱을 이동 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 451171a041f7bbb3711babd73d2fa2e245aadd28
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355133"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="8f982-103">C 코어에서 ASP.NET Core로 gRPC 서비스 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="8f982-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="8f982-104">작성자: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="8f982-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="8f982-105">기본 스택의 구현 때문에 모든 기능이 [C 코어 기반 gRPC](https://grpc.io/blog/grpc-stacks) 앱과 ASP.NET Core 기반 앱 간의 동일한 방식으로 작동 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="8f982-106">이 문서에서는 두 스택 간의 마이그레이션에 대 한 주요 차이점을 강조 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="8f982-107">gRPC 서비스 구현 수명</span><span class="sxs-lookup"><span data-stu-id="8f982-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="8f982-108">ASP.NET Core 스택에서 gRPC 서비스는 기본적으로 범위가 지정 된 [수명](xref:fundamentals/dependency-injection#service-lifetimes)으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="8f982-109">이와 대조적으로 gRPC C-core는 기본적으로 [단일 수명이](xref:fundamentals/dependency-injection#service-lifetimes)포함 된 서비스에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="8f982-110">범위가 지정 된 수명으로 서비스 구현에서 범위가 지정 된 수명으로 다른 서비스를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="8f982-111">예를 들어, 범위 지정 된 수명은 생성자 주입을 통해 DI 컨테이너의 `DbContext` 확인할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="8f982-112">범위가 지정 된 수명 사용:</span><span class="sxs-lookup"><span data-stu-id="8f982-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="8f982-113">서비스 구현의 새 인스턴스는 각 요청에 대해 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="8f982-114">구현 형식에서 인스턴스 멤버를 통해 요청 간에 상태를 공유할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="8f982-115">공유 상태를 DI 컨테이너의 단일 서비스에 저장 하는 것이 예상 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="8f982-116">저장 된 공유 상태는 gRPC 서비스 구현의 생성자에서 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="8f982-117">서비스 수명에 대 한 자세한 내용은 <xref:fundamentals/dependency-injection#service-lifetimes>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f982-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="8f982-118">단일 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="8f982-118">Add a singleton service</span></span>

<span data-ttu-id="8f982-119">GRPC C 코어 구현에서 ASP.NET Core로 전환 하는 것을 용이 하 게 하기 위해 서비스 구현의 서비스 수명 범위를 singleton으로 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="8f982-120">여기에는 서비스 구현 인스턴스를 DI 컨테이너에 추가 하는 작업이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="8f982-121">그러나 singleton 수명이 포함 된 서비스 구현은 더 이상 생성자 주입을 통해 범위 지정 서비스를 확인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="8f982-122">GRPC 서비스 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="8f982-122">Configure gRPC services options</span></span>

<span data-ttu-id="8f982-123">C 코어 기반 앱에서 [서버 인스턴스를 생성할](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)때 `grpc.max_receive_message_length` 및 `grpc.max_send_message_length` 같은 설정이 `ChannelOption` 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="8f982-124">ASP.NET Core에서 gRPC는 `GrpcServiceOptions` 유형을 통해 구성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="8f982-125">예를 들어 gRPC 서비스의 최대 수신 메시지 크기는 `AddGrpc`를 통해 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="8f982-126">다음 예에서는 기본 `MaxReceiveMessageSize` 4mb를 16mb로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="8f982-127">구성에 대 한 자세한 내용은 <xref:grpc/configuration>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f982-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="8f982-128">로깅</span><span class="sxs-lookup"><span data-stu-id="8f982-128">Logging</span></span>

<span data-ttu-id="8f982-129">C 코어 기반 앱은 디버깅을 위해 [로 거를 구성](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) 하는 `GrpcEnvironment`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="8f982-130">ASP.NET Core 스택은 [로깅 API](xref:fundamentals/logging/index)를 통해이 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8f982-131">예를 들어 생성자 주입을 통해 gRPC 서비스에로 거를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="8f982-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8f982-132">HTTPS</span></span>

<span data-ttu-id="8f982-133">C 코어 기반 앱은 [Server. Ports 속성](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)을 통해 HTTPS를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="8f982-134">ASP.NET Core에서 서버를 구성 하는 데 비슷한 개념을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="8f982-135">예를 들어 Kestrel은이 기능에 대해 [끝점 구성을](xref:fundamentals/servers/kestrel#endpoint-configuration) 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="8f982-136">gRPC 인터셉터 vs 미들웨어</span><span class="sxs-lookup"><span data-stu-id="8f982-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="8f982-137">ASP.NET Core [미들웨어](xref:fundamentals/middleware/index) 는 C 코어 기반 grpc 앱의 인터셉터와 비교할 때 비슷한 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="8f982-138">ASP.NET Core 미들웨어 및 인터셉터는 개념적으로 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="8f982-139">모두:</span><span class="sxs-lookup"><span data-stu-id="8f982-139">Both:</span></span>

* <span data-ttu-id="8f982-140">는 gRPC 요청을 처리 하는 파이프라인을 생성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="8f982-141">파이프라인의 다음 구성 요소 전이나 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="8f982-142">`HttpContext`에 대 한 액세스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="8f982-143">미들웨어에서 `HttpContext`는 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="8f982-144">인터셉터에서 `HttpContext`는 `ServerCallContext.GetHttpContext` 확장 메서드와 `ServerCallContext` 매개 변수를 사용 하 여 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="8f982-145">이 기능은 ASP.NET Core에서 실행 되는 인터셉터에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="8f982-146">ASP.NET Core 미들웨어와의 gRPC 인터셉터 차이점:</span><span class="sxs-lookup"><span data-stu-id="8f982-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="8f982-147">인터셉터</span><span class="sxs-lookup"><span data-stu-id="8f982-147">Interceptors:</span></span>
  * <span data-ttu-id="8f982-148">[ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)를 사용 하 여 추상화의 grpc 계층에서 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="8f982-149">다음에 대 한 액세스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-149">Provide access to:</span></span>
    * <span data-ttu-id="8f982-150">호출에 전송 된 deserialize 된 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="8f982-151">메시지를 serialize 하기 전에 호출에서 반환 되는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="8f982-152">GRPC 서비스에서 throw 된 예외를 catch 하 고 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="8f982-153">미들웨어</span><span class="sxs-lookup"><span data-stu-id="8f982-153">Middleware:</span></span>
  * <span data-ttu-id="8f982-154">GRPC 인터셉터 이전에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="8f982-155">기본 HTTP/2 메시지에서 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="8f982-156">는 요청 및 응답 스트림의 바이트에만 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f982-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8f982-157">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8f982-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
