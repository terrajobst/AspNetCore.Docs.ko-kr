---
title: ASP.NET Core를 사용하는 gRPC 서비스
author: juntaoluo
description: ASP.NET Core를 사용 하 여 gRPC 서비스를 작성할 때의 기본 개념에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 26f0d7610151460967b97665ed61deab1ef56d68
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68862935"
---
# <a name="grpc-services-with-aspnet-core"></a>ASP.NET Core를 사용하는 gRPC 서비스

이 문서에서는 ASP.NET Core를 사용 하 여 gRPC 서비스를 시작 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>전제 조건

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

grpc는 다음과 같은 `AddGrpc` 방법으로 사용할 수 있습니다.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

각 grpc 서비스는 메서드를 `MapGrpcService` 통해 라우팅 파이프라인에 추가 됩니다.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

ASP.NET Core 미들웨어 및 기능이 라우팅 파이프라인을 공유 하므로 추가 요청 처리기를 제공 하도록 앱을 구성할 수 있습니다. MVC 컨트롤러와 같은 추가 요청 처리기는 구성 된 gRPC 서비스와 병렬로 작동 합니다.

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

## <a name="grpc-and-aspnet-core-on-macos"></a>macOS에서 gRPC 및 ASP.NET Core

Kestrel은 macOS에서 [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) 를 사용 하는 HTTP/2를 지원 하지 않습니다. ASP.NET Core gRPC 템플릿 및 샘플은 기본적으로 TLS를 사용 합니다. GRPC 서버를 시작 하려고 하면 다음과 같은 오류 메시지가 표시 됩니다.

> IPv4 루프백 인터페이스에서 https://localhost:5001 에 바인딩할 수 없습니다. ' TLS를 통한 HTTP/2 '는 ALPN 지원 누락으로 인해 macOS에서 지원 되지 않습니다. '

이 문제를 해결 하려면 Kestrel 및 gRPC 클라이언트에서 TLS **없이** HTTP/2를 사용 하도록 구성 합니다. 개발 하는 동안에만이 작업을 수행 해야 합니다. TLS를 사용 하지 않으면 암호화 하지 않고 gRPC 메시지가 전송 됩니다.

Kestrel은에서 `Program.cs`TLS 없이 HTTP/2 끝점을 구성 해야 합니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

Grpc 클라이언트는 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` 스위치를로 설정 하 고 서버 `http` 주소에서를 `true` 사용 해야 합니다.

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

> [!WARNING]
> TLS를 사용 하지 않는 HTTP/2는 앱 개발 중에만 사용 해야 합니다. 프로덕션 응용 프로그램은 항상 전송 보안을 사용 해야 합니다. 자세한 내용은 [ASP.NET Core에 대 한 gRPC의 보안 고려 사항](xref:grpc/security#transport-security)을 참조 하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
