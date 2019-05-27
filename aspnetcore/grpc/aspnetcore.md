---
title: ASP.NET Core를 사용하는 gRPC 서비스
author: juntaoluo
description: ASP.NET Core를 사용 하 여 gRPC 서비스를 작성 하는 경우에 기본 개념에 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 1f019fac23982a95fa37d43099522f4b3e9d107a
ms.sourcegitcommit: 5d384db2fa9373a93b5d15e985fb34430e49ad7a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66039277"
---
# <a name="grpc-services-with-aspnet-core"></a>ASP.NET Core를 사용하는 gRPC 서비스

이 문서에는 ASP.NET Core를 사용 하 여 gRPC 서비스를 사용 하 여 시작 하는 방법을 보여 줍니다.

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>ASP.NET Core에서 gRPC 서비스 시작

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

참조 [gRPC services 시작](xref:tutorials/grpc/grpc-start) gRPC 프로젝트를 만드는 방법에 대 한 자세한 지침에 대 한 합니다.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

명령줄에서 `dotnet new grpc -o GrpcGreeter`을 실행합니다.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>ASP.NET Core 앱에 gRPC 서비스 추가

gRPC는 다음 패키지가 필요 합니다.

* [Grpc.AspNetCore.Server](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) protobuf 메시지 Api.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a>GRPC를 구성 합니다.

gRPC 사용 하도록 설정 되었는지는 `AddGrpc` 메서드:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Startup.cs?name=snippet&highlight=5)]

각 gRPC 서비스를 통해 라우팅 파이프라인에 추가 되는 `MapGrpcService` 메서드:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Startup.cs?name=snippet&highlight=21)]

ASP.NET Core 미들웨어 및 기능 공유 라우팅 파이프라인, 따라서 추가 요청 처리기를 제공 하는 앱을 구성할 수 있습니다. 예: MVC 컨트롤러 추가 요청 처리기 구성된 gRPC 서비스를 사용 하 여 병렬로 작동합니다.

## <a name="integration-with-aspnet-core-apis"></a>ASP.NET Core Api와 통합

gRPC 서비스 전체 기능에 액세스할 수는 ASP.NET Core와 같은 [종속성 주입](xref:fundamentals/dependency-injection) (DI) 및 [로깅](xref:fundamentals/logging/index)합니다. 예를 들어, 서비스 구현에는 생성자를 통해 DI 컨테이너에서로 거 서비스를 해결할 수 있습니다.

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

기본적으로 gRPC 서비스 구현의 모든 수명 (단일, Scoped, 또는 일시적)를 사용 하 여 다른 di를 해결할 수 있습니다.

### <a name="resolve-httpcontext-in-grpc-methods"></a>GRPC 메서드에서 HttpContext 해결

GRPC API 메서드, 호스트, 헤더 및 트레일러와 같은 일부 HTTP/2 메시지 데이터에 액세스할 수 있습니다. 통해 액세스 된 `ServerCallContext` 각 gRPC 메서드에 전달 된 인수:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext` 에 대 한 전체 액세스를 제공 하지 않습니다 `HttpContext` 모든 ASP.NET api에서. 합니다 `GetHttpContext` 에 대 한 전체 액세스를 제공 하는 확장 메서드는 `HttpContext` ASP.NET Api에서 기본 HTTP/2 메시지를 표시 합니다.

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?name=snippet1)]

## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
