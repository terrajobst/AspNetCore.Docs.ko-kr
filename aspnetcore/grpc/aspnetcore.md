---
title: ASP.NET Core를 사용하는 gRPC 서비스
author: juntaoluo
description: ASP.NET Core를 사용 하 여 gRPC 서비스를 작성할 때의 기본 개념에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 2507ce6df05403cb19e8bfa2565d410d6140b144
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925088"
---
# <a name="grpc-services-with-aspnet-core"></a>ASP.NET Core를 사용하는 gRPC 서비스

이 문서에서는 ASP.NET Core를 사용 하 여 gRPC 서비스를 시작 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>ASP.NET Core에서 gRPC 서비스 시작

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

GRPC 프로젝트를 만드는 방법에 대 한 자세한 지침은 [grpc 서비스 시작](xref:tutorials/grpc/grpc-start) 을 참조 하세요.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

명령줄에서 `dotnet new grpc -o GrpcGreeter`을 실행합니다.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>ASP.NET Core 앱에 gRPC 서비스 추가

gRPC에는 [Grpc](https://www.nuget.org/packages/Grpc.AspNetCore) 패키지가 필요 합니다.

### <a name="configure-grpc"></a>GRPC 구성

*Startup.cs*:

* grpc는 `AddGrpc` 메서드를 사용 하 여 사용 하도록 설정 됩니다.
* 각 grpc 서비스는 메서드를 `MapGrpcService` 통해 라우팅 파이프라인에 추가 됩니다.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]

ASP.NET Core 미들웨어 및 기능이 라우팅 파이프라인을 공유 하므로 추가 요청 처리기를 제공 하도록 앱을 구성할 수 있습니다. MVC 컨트롤러와 같은 추가 요청 처리기는 구성 된 gRPC 서비스와 병렬로 작동 합니다.

### <a name="configure-kestrel"></a>Kestrel 구성

Kestrel gRPC 끝점:

* HTTP/2가 필요 합니다.
* [TLS (전송 계층 보안](https://tools.ietf.org/html/rfc5246))로 보호 되어야 합니다.

#### <a name="http2"></a>HTTP/2

gRPC에는 h t t p/2가 필요 합니다. ASP.NET Core에 대 한 gRPC는 HttpRequest의 `HTTP/2`유효성을 검사 [합니다. 프로토콜](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 은입니다.

Kestrel은 최신 운영 체제에서 [HTTP/2를 지원](xref:fundamentals/servers/kestrel#http2-support) 합니다. Kestrel 끝점은 기본적으로 HTTP/1.1 및 HTTP/2 연결을 지원 하도록 구성 됩니다.

#### <a name="tls"></a>TLS

GRPC에 사용 되는 kestrel 끝점은 TLS로 보호 되어야 합니다. 개발에서 TLS로 보안이 설정 된 끝점은 ASP.NET Core 개발 인증서 `https://localhost:5001` 가 있는 경우에서 자동으로 만들어집니다. 구성이 필요하지 않습니다. 접두사 `https` 는 kestrel 끝점이 TLS를 사용 하 고 있는지 확인 합니다.

프로덕션에서는 TLS를 명시적으로 구성 해야 합니다. 다음 *appsettings* 예에서는 TLS로 보호 되는 HTTP/2 끝점이 제공 됩니다.

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

또는 *Program.cs*에서 Kestrel 끝점을 구성할 수 있습니다.

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a>프로토콜 협상

TLS는 통신 보안을 유지 하는 데 사용 됩니다. 끝점에서 여러 프로토콜을 지 원하는 경우 클라이언트와 서버 간의 연결 프로토콜을 협상 하는 데 TLS [응용 프로그램 계층 프로토콜 협상 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 핸드셰이크가 사용 됩니다. 이 협상은 연결에서 HTTP/1.1 또는 HTTP/2를 사용 하는지 여부를 결정 합니다.

TLS 없이 HTTP/2 끝점을 구성 하는 경우 끝점의 [ListenOptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) 을로 `HttpProtocols.Http2`설정 해야 합니다. 여러 프로토콜 (예: `HttpProtocols.Http1AndHttp2`)이 있는 끝점은 협상이 없으므로 TLS 없이 사용할 수 없습니다. 안전 하지 않은 끝점에 대 한 모든 연결은 기본적으로 HTTP/1.1 및 gRPC 호출에 실패 합니다.

Kestrel을 사용 하 여 HTTP/2 및 TLS를 사용 하는 방법에 대 한 자세한 내용은 [kestrel 끝점 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조 하세요.

> [!NOTE]
> macOS는 TLS를 사용하는 ASP.NET Core gRPC를 지원하지 않습니다. macOS에서 gRPC 서비스를 성공적으로 실행하려면 추가 구성이 필요합니다. 자세한 내용은 [macOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)을 참조하세요.

## <a name="integration-with-aspnet-core-apis"></a>ASP.NET Core Api와 통합

gRPC 서비스에는 DI ( [종속성 주입](xref:fundamentals/dependency-injection) ) 및 [로깅과](xref:fundamentals/logging/index)같은 ASP.NET Core 기능에 대 한 모든 권한이 있습니다. 예를 들어 서비스 구현은 생성자를 통해 DI 컨테이너에서로 거 서비스를 확인할 수 있습니다.

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

기본적으로 gRPC 서비스 구현은 임의의 수명 (Singleton, 범위 또는 임시)으로 다른 DI 서비스를 확인할 수 있습니다.

### <a name="resolve-httpcontext-in-grpc-methods"></a>GRPC 메서드에서 HttpContext를 확인 합니다.

GRPC API는 메서드, 호스트, 헤더 및 트레일러와 같은 일부 HTTP/2 메시지 데이터에 대 한 액세스를 제공 합니다. 각 grpc 메서드에 `ServerCallContext` 전달 된 인수를 통해 액세스할 수 있습니다.

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`는 모든 ASP.NET api의에 `HttpContext` 대 한 모든 액세스 권한을 제공 하지 않습니다. 확장 `GetHttpContext` 메서드는 ASP.NET api에서 기본 HTTP `HttpContext` /2 메시지를 나타내는에 대 한 모든 권한을 제공 합니다.

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
