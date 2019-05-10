---
title: ASP.NET Core로 C 코어에서 gRPC services 마이그레이션
author: juntaoluo
description: ASP.NET Core 스택을 기반으로 실행 하려면 기존 C-core gRPC 기반된 앱을 이동 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/migration
ms.openlocfilehash: 47d74edd821124f0c8390d704ca7931b7eb6c4cd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895240"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>ASP.NET Core로 C 코어에서 gRPC services 마이그레이션

작성자: [John Luo](https://github.com/juntaoluo)

기본 스택 구현으로 인해 일부 기능 간에 동일한 방식으로 작동 [C-core 기반 gRPC](https://grpc.io/blog/grpc-stacks) 앱 및 ASP.NET Core 기반 앱. 이 문서에는 두 스택 간에 마이그레이션에 대 한 주요 차이점을 강조 표시 합니다.

## <a name="grpc-service-implementation-lifetime"></a>gRPC 서비스 구현 수명

ASP.NET Core 스택에서 gRPC 서비스를 기본적으로 사용 하 여 만든를 [scoped 수명](xref:fundamentals/dependency-injection#service-lifetimes)합니다. GRPC C 코어 기본적으로 사용 하 여 서비스 바인딩되는 반면에 [singleton 수명을](xref:fundamentals/dependency-injection#service-lifetimes)합니다.

Scoped 수명 서비스 구현을 수명 범위를 사용 하 여 다른 서비스를 해결할 수 있습니다. 예를 들어, scoped 수명을 해결할 수도 `DBContext` 생성자 주입을 통해 DI 컨테이너에서. Scoped 수명을 사용합니다.

* 서비스 구현의 새 인스턴스는 각 요청에 대해 생성 됩니다.
* 구현 형식에 인스턴스 멤버를 통해 요청 간에 상태를 공유 하는 것이 불가능 합니다.
* 예상 DI 컨테이너의 단일 서비스에서 공유 상태를 저장 하는 것입니다. 공유 상태 저장된 서비스 구현의 gRPC 생성자에서 확인 됩니다.

서비스 수명에 대 한 자세한 내용은 참조 하세요. <xref:fundamentals/dependency-injection#service-lifetimes>합니다.

### <a name="add-a-singleton-service"></a>단일 서비스를 추가 합니다.

ASP.NET Core는 gRPC C 코어 구현에서 전환을 용이 하 게를 서비스 구현에서 서비스 기간을 변경할 수 singleton 범위입니다. 여기에 DI 컨테이너 서비스 구현의 인스턴스를 추가 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

그러나 singleton 수명 사용 하는 서비스 구현 되지 생성자 주입을 통해 범위가 지정 된 서비스를 확인할 수 없습니다.

## <a name="configure-grpc-services-options"></a>GRPC 서비스 옵션 구성

C-core 기반 앱에서와 같이 설정 `grpc.max_receive_message_length` 하 고 `grpc.max_send_message_length` 으로 구성 된 `ChannelOption` 때 [서버 인스턴스를 생성](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)합니다.

ASP.NET Core, gRPC 제공을 통해 구성 된 `GrpcServiceOptions` 형식입니다. 예를 들어 gRPC 서비스의 들어오는 메시지의 최대 크기를 통해 구성할 수 있습니다 `AddGrpc`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 16384; // 16 MB
    });
}
```

구성에 대 한 자세한 내용은 참조 하세요. <xref:grpc/configuration>합니다.

## <a name="logging"></a>로깅

C-core 기반 앱을 사용 합니다 `GrpcEnvironment` 를 [로 거 구성](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) 디버깅 목적으로 합니다. ASP.NET Core 스택을 통해이 기능을 제공 합니다 [로깅 API](xref:fundamentals/logging/index)합니다. 예를 들어로 거는 생성자 주입을 통해 gRPC 서비스에 추가할 수 있습니다.

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

C-core 기반 앱을 통해 HTTPS를 구성 합니다 [Server.Ports 속성](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)합니다. ASP.NET Core에서 서버를 구성 하는 비슷한 개념이 사용 됩니다. Kestrel의 사용 예를 들어 [끝점 구성을](xref:fundamentals/servers/kestrel#endpoint-configuration) 이 기능에 대 한 합니다.

## <a name="interceptors-and-middleware"></a>인터셉터 및 미들웨어

ASP.NET Core [미들웨어](xref:fundamentals/middleware/index) gRPC C-core 기반 앱에서 인터셉터에 비해 비슷한 기능을 제공 합니다. 미들웨어 및 인터셉터와 개념적으로 동일한 gRPC 요청을 처리 하는 파이프라인을 생성 하 되 둘 다. 둘 다 작업 전이나 후 파이프라인의 다음 구성 요소에서 수행할 수 있습니다. 하지만 인터셉터의 추상화를 사용 하 여 gRPC 계층에서 작동 하는 동안 ASP.NET Core 미들웨어 기본 HTTP/2 메시지에서 작동 합니다 [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
