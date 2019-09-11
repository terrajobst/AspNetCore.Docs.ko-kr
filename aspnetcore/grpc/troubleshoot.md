---
title: .NET Core에서 gRPC 문제 해결
author: jamesnk
description: .NET Core에서 gRPC를 사용할 때 발생 하는 오류 문제를 해결 합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/troubleshoot
ms.openlocfilehash: 33864ceb18304eb1d3413bcc9aebacd6eaffdbc6
ms.sourcegitcommit: e7c56e8da5419bbc20b437c2dd531dedf9b0dc6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70878494"
---
# <a name="troubleshoot-grpc-on-net-core"></a>.NET Core에서 gRPC 문제 해결

별 [뉴턴-킹](https://twitter.com/jamesnk)

이 문서에서는 .NET에서 gRPC 앱을 개발할 때 발생 하는 일반적인 문제에 대해 설명 합니다.

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>클라이언트와 서비스의 SSL/TLS 구성이 일치 하지 않습니다.

GRPC 템플릿 및 샘플에서는 [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) 를 사용 하 여 기본적으로 grpc 서비스를 보호 합니다. gRPC 클라이언트는 보안 연결을 사용 하 여 보안 gRPC 서비스를 정상적으로 호출 해야 합니다.

앱 시작 시 작성 된 로그에서 gRPC 서비스가 TLS를 사용 하 고 ASP.NET Core 확인할 수 있습니다. 서비스는 HTTPS 끝점에서 수신 대기 합니다.

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

.Net Core 클라이언트는 서버 주소 `https` 에서를 사용 하 여 보안 연결을 사용 하는 호출을 수행 해야 합니다.

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

모든 gRPC 클라이언트 구현에서는 TLS를 지원 합니다. 다른 언어의 gRPC 클라이언트는 일반적으로로 `SslCredentials`구성 된 채널이 필요 합니다. `SslCredentials`클라이언트에서 사용 하는 인증서를 지정 합니다 .이 인증서는 안전 하지 않은 자격 증명 대신 사용 해야 합니다. TLS를 사용 하도록 여러 gRPC 클라이언트 구현을 구성 하는 예제는 [Grpc 인증](https://www.grpc.io/docs/guides/auth/)을 참조 하세요.

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>신뢰할 수 없는/잘못 된 인증서를 사용 하 여 gRPC 서비스 호출

.NET gRPC 클라이언트를 사용 하려면 서비스에 신뢰할 수 있는 인증서가 있어야 합니다. 다음 오류 메시지는 신뢰할 수 있는 인증서가 없는 gRPC 서비스를 호출할 때 반환 됩니다.

> 처리되지 않은 예외가 발생했습니다. System.Net.Http.HttpRequestException: SSL 연결을 설정할 수 없습니다. 내부 예외를 참조 하십시오.
> ---> 합니다. AuthenticationException: 유효성 검사 절차에 따르면 원격 인증서가 잘못 되었습니다.

응용 프로그램을 로컬로 테스트 하 고 ASP.NET Core HTTPS 개발 인증서를 신뢰할 수 없는 경우이 오류가 표시 될 수 있습니다. 이 문제의 해결 지침은 [Windows 및 macOS에서의 ASP.NET Core HTTPS 개발 인증서 신뢰](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)를 참조하세요.

다른 컴퓨터에서 gRPC 서비스를 호출 하 고 인증서를 신뢰할 수 없는 경우 gRPC 클라이언트에서 잘못 된 인증서를 무시 하도록 구성할 수 있습니다. 다음 코드는 [Httpclienthandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) 를 사용 하 여 신뢰할 수 있는 인증서가 없는 호출을 허용 합니다.

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = (message, cert, chain, errors) => true;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> 신뢰할 수 없는 인증서는 앱을 개발 하는 동안에만 사용 해야 합니다. 프로덕션 앱은 항상 유효한 인증서를 사용 해야 합니다.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>.NET Core 클라이언트를 사용 하 여 안전 하지 않은 gRPC 서비스 호출

.NET Core 클라이언트를 사용 하 여 안전 하지 않은 gRPC 서비스를 호출 하려면 추가 구성이 필요 합니다. Grpc 클라이언트는 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` 스위치를로 설정 하 고 서버 `http` 주소에서를 `true` 사용 해야 합니다.

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>MacOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음

Kestrel은 macOS에서 TLS를 사용 하는 HTTP/2 및 Windows 7과 같은 이전 Windows 버전을 지원 하지 않습니다. ASP.NET Core gRPC 템플릿 및 샘플은 기본적으로 TLS를 사용 합니다. GRPC 서버를 시작 하려고 하면 다음과 같은 오류 메시지가 표시 됩니다.

> IPv4 루프백 인터페이스에서 https://localhost:5001 에 바인딩할 수 없습니다. ' TLS를 통한 HTTP/2 '는 ALPN 지원 누락으로 인해 macOS에서 지원 되지 않습니다. '

이 문제를 해결 하려면 Kestrel 및 gRPC 클라이언트에서 TLS *없이* HTTP/2를 사용 하도록 구성 합니다. 개발 하는 동안에만이 작업을 수행 해야 합니다. TLS를 사용 하지 않으면 암호화 하지 않고 gRPC 메시지가 전송 됩니다.

Kestrel은 *Program.cs*에서 TLS를 사용 하지 않고 HTTP/2 끝점을 구성 해야 합니다.

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

TLS 없이 HTTP/2 끝점을 구성 하는 경우 끝점의 [ListenOptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) 을로 `HttpProtocols.Http2`설정 해야 합니다. `HttpProtocols.Http1AndHttp2`TLS가 HTTP/2를 협상 하는 데 필요 하기 때문에를 사용할 수 없습니다. TLS를 사용 하지 않으면 끝점에 대 한 모든 연결의 기본값은 HTTP/1.1 이며 gRPC 호출은 실패 합니다.

또한 gRPC 클라이언트는 TLS를 사용 하지 않도록 구성 해야 합니다. 자세한 내용은 [.Net Core 클라이언트를 사용 하 여 안전 하지 않은 gRPC 서비스 호출](#call-insecure-grpc-services-with-net-core-client)을 참조 하세요.

> [!WARNING]
> TLS를 사용 하지 않는 HTTP/2는 앱 개발 중에만 사용 해야 합니다. 프로덕션 앱은 항상 전송 보안을 사용 해야 합니다. 자세한 내용은 [ASP.NET Core에 대 한 gRPC의 보안 고려 사항](xref:grpc/security#transport-security)을 참조 하세요.

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>grpc C# 자산은 .pfiles에서  *\** 생성 된 코드가 아닙니다.

구체적 클라이언트 및 서비스 기본 클래스의 gRPC 코드 생성을 위해서는 protobuf 파일 및 도구를 프로젝트에서 참조 해야 합니다. 다음을 포함 해야 합니다.

* `<Protobuf>` 항목 그룹에서 사용 하려는 파일 *.* [가져온 *proto* 파일](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) 은 프로젝트에서 참조 해야 합니다.
* GRPC [도구](https://www.nuget.org/packages/Grpc.Tools/)패키지에 대 한 패키지 참조를 참조 하세요.

GRPC C# 자산을 생성 하는 방법에 대 <xref:grpc/basics>한 자세한 내용은을 참조 하세요.

기본적으로 참조는 `<Protobuf>` 구체적 클라이언트 및 서비스 기본 클래스를 생성 합니다. Reference 요소의 `GrpcServices` 특성을 사용 하 여 자산 생성을 C# 제한할 수 있습니다. 유효한 `GrpcServices` 옵션은 다음과 같습니다.

* `Both`(없는 경우 기본값)
* `Server`
* `Client`
* `None`

GRPC 서비스를 호스트 하는 ASP.NET Core 웹 앱에는 생성 된 서비스 기본 클래스만 필요 합니다.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Grpc 클라이언트 앱에서 gRPC 호출에는 구체적 클라이언트만 생성 되어야 합니다.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
