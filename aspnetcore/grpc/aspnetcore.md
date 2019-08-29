---
title: ASP.NET Core를 사용하는 gRPC 서비스
author: juntaoluo
description: ASP.NET Core를 사용 하 여 gRPC 서비스를 작성할 때의 기본 개념에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/28/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 128f5b36eac9112460c33693db5537134a077476
ms.sourcegitcommit: 23f79bd71d49c4efddb56377c1f553cc993d781b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70130699"
---
# <a name="grpc-services-with-aspnet-core"></a>ASP.NET Core를 사용하는 gRPC 서비스

이 문서에서는 ASP.NET Core를 사용 하 여 gRPC 서비스를 시작 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

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
* HTTPS를 사용 하 여 보안을 설정 해야 합니다.

#### <a name="http2"></a>HTTP/2

Kestrel은 최신 운영 체제에서 [HTTP/2를 지원](xref:fundamentals/servers/kestrel#http2-support) 합니다. Kestrel 끝점은 기본적으로 HTTP/1.1 및 HTTP/2 연결을 지원 하도록 구성 됩니다.

> [!NOTE]
> macOS는 [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246)가 있는 ASP.NET Core grpc를 지원 하지 않습니다. macOS에서 gRPC 서비스를 성공적으로 실행하려면 추가 구성이 필요합니다. 자세한 내용은 [macOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)을 참조하세요.

#### <a name="https"></a>HTTPS

GRPC에 사용 되는 kestrel 끝점은 HTTPS로 보호 되어야 합니다. 개발 시에는 ASP.NET Core 개발 인증서가 있는 경우 `https://localhost:5001` 에서 HTTPS 끝점이 자동으로 만들어집니다. 구성이 필요하지 않습니다.

프로덕션 내에 HTTPS가 명시적으로 구성되어야 합니다. 다음 *appsettings* 예제에서는 HTTPS를 사용 하 여 보호 되는 HTTP/2 끝점이 제공 됩니다.

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http2"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

또는 *Program.cs*에서 Kestrel endspoints을 구성할 수 있습니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // This endpoint will use HTTP/2 and HTTPS on port 5001.
                options.Listen(IPAddress.Any, 5001, listenOptions =>
                {
                    listenOptions.Protocols = HttpProtocols.Http2;
                    listenOptions.UseHttps("<path to .pfx file>", 
                        "<certificate password>");
                });
            });
            webBuilder.UseStartup<Startup>();
        });
```

Kestrel에서 HTTP/2 및 HTTPS를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [kestrel 끝점 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조 하세요.

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

## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
