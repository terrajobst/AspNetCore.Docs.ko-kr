---
title: .NET Core에서 gRPC 문제 해결
author: jamesnk
description: .NET Core에서 gRPC를 사용할 때 발생 하는 오류 문제를 해결 합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519046"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="2fa59-103">.NET Core에서 gRPC 문제 해결</span><span class="sxs-lookup"><span data-stu-id="2fa59-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="2fa59-104">별 [뉴턴-킹](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="2fa59-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="2fa59-105">이 문서에서는 .NET에서 gRPC 앱을 개발할 때 발생 하는 일반적인 문제에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="2fa59-106">클라이언트와 서비스의 SSL/TLS 구성이 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="2fa59-107">GRPC 템플릿 및 샘플에서는 [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) 를 사용 하 여 기본적으로 grpc 서비스를 보호 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="2fa59-108">gRPC 클라이언트는 보안 연결을 사용 하 여 보안 gRPC 서비스를 정상적으로 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="2fa59-109">앱 시작 시 작성 된 로그에서 gRPC 서비스가 TLS를 사용 하 고 ASP.NET Core 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="2fa59-110">서비스는 HTTPS 끝점에서 수신 대기 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="2fa59-111">.NET Core 클라이언트는 서버 주소에 `https`를 사용 하 여 보안 연결을 통해 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="2fa59-112">모든 gRPC 클라이언트 구현에서는 TLS를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="2fa59-113">다른 언어의 gRPC 클라이언트는 일반적으로 `SslCredentials`로 구성 된 채널이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="2fa59-114">`SslCredentials` 클라이언트에서 사용할 인증서를 지정 합니다 .이 인증서는 보안 되지 않은 자격 증명 대신 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="2fa59-115">TLS를 사용 하도록 여러 gRPC 클라이언트 구현을 구성 하는 예제는 [Grpc 인증](https://www.grpc.io/docs/guides/auth/)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fa59-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="2fa59-116">신뢰할 수 없는/잘못 된 인증서를 사용 하 여 gRPC 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="2fa59-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="2fa59-117">.NET gRPC 클라이언트를 사용 하려면 서비스에 신뢰할 수 있는 인증서가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="2fa59-118">다음 오류 메시지는 신뢰할 수 있는 인증서가 없는 gRPC 서비스를 호출할 때 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="2fa59-119">처리되지 않은 예외가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-119">Unhandled exception.</span></span> <span data-ttu-id="2fa59-120">시스템 .Net. Http. HttpRequestException: SSL 연결을 설정할 수 없습니다. 내부 예외를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="2fa59-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="2fa59-121">---> 합니다. 인증: 유효성 검사 절차에 따르면 원격 인증서가 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="2fa59-122">응용 프로그램을 로컬로 테스트 하 고 ASP.NET Core HTTPS 개발 인증서를 신뢰할 수 없는 경우이 오류가 표시 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="2fa59-123">이 문제의 해결 지침은 [Windows 및 macOS에서 ASP.NET Core HTTPS 개발 인증서 신뢰](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2fa59-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="2fa59-124">다른 컴퓨터에서 gRPC 서비스를 호출 하 고 인증서를 신뢰할 수 없는 경우 gRPC 클라이언트에서 잘못 된 인증서를 무시 하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="2fa59-125">다음 코드는 [Httpclienthandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) 를 사용 하 여 신뢰할 수 있는 인증서가 없는 호출을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="2fa59-126">신뢰할 수 없는 인증서는 앱을 개발 하는 동안에만 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="2fa59-127">프로덕션 앱은 항상 유효한 인증서를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="2fa59-128">.NET Core 클라이언트를 사용 하 여 안전 하지 않은 gRPC 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="2fa59-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="2fa59-129">.NET Core 클라이언트를 사용 하 여 안전 하지 않은 gRPC 서비스를 호출 하려면 추가 구성이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="2fa59-130">GRPC 클라이언트는 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` 스위치를 `true`으로 설정 하 고 서버 주소에서 `http`를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="2fa59-131">MacOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음</span><span class="sxs-lookup"><span data-stu-id="2fa59-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="2fa59-132">Kestrel은 macOS에서 TLS를 사용 하는 HTTP/2 및 Windows 7과 같은 이전 Windows 버전을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="2fa59-133">ASP.NET Core gRPC 템플릿 및 샘플은 기본적으로 TLS를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="2fa59-134">GRPC 서버를 시작 하려고 하면 다음과 같은 오류 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="2fa59-135">IPv4 루프백 인터페이스의 https://localhost:5001에 바인딩할 수 없습니다. ' TLS를 통한 HTTP/2는 ALPN 지원 누락으로 인해 macOS에서 지원 되지 않습니다. '.</span><span class="sxs-lookup"><span data-stu-id="2fa59-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="2fa59-136">이 문제를 해결 하려면 Kestrel 및 gRPC 클라이언트에서 TLS *없이* HTTP/2를 사용 하도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="2fa59-137">개발 하는 동안에만이 작업을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-137">You should only do this during development.</span></span> <span data-ttu-id="2fa59-138">TLS를 사용 하지 않으면 암호화 하지 않고 gRPC 메시지가 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="2fa59-139">Kestrel은 *Program.cs*에서 TLS를 사용 하지 않고 HTTP/2 끝점을 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="2fa59-140">TLS 없이 HTTP/2 끝점을 구성 하는 경우 끝점의 [ListenOptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) 을 `HttpProtocols.Http2`설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="2fa59-141">TLS가 HTTP/2를 협상 하는 데 필요 하기 때문에 `HttpProtocols.Http1AndHttp2`를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="2fa59-142">TLS를 사용 하지 않으면 끝점에 대 한 모든 연결의 기본값은 HTTP/1.1 이며 gRPC 호출은 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="2fa59-143">또한 gRPC 클라이언트는 TLS를 사용 하지 않도록 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="2fa59-144">자세한 내용은 [.Net Core 클라이언트를 사용 하 여 안전 하지 않은 gRPC 서비스 호출](#call-insecure-grpc-services-with-net-core-client)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fa59-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="2fa59-145">TLS를 사용 하지 않는 HTTP/2는 앱 개발 중에만 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="2fa59-146">프로덕션 앱은 항상 전송 보안을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-146">Production apps should always use transport security.</span></span> <span data-ttu-id="2fa59-147">자세한 내용은 [ASP.NET Core에 대 한 gRPC의 보안 고려 사항](xref:grpc/security#transport-security)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fa59-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="2fa59-148">gRPC C# 자산은 .pfiles에서 생성 된 코드가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="2fa59-149">구체적 클라이언트 및 서비스 기본 클래스의 gRPC 코드 생성을 위해서는 protobuf 파일 및 도구를 프로젝트에서 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="2fa59-150">다음을 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-150">You must include:</span></span>

* <span data-ttu-id="2fa59-151">`<Protobuf>` 항목 그룹에서 사용 하려는 파일 *.*</span><span class="sxs-lookup"><span data-stu-id="2fa59-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="2fa59-152">[가져온 *proto* 파일](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) 은 프로젝트에서 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="2fa59-153">GRPC [도구](https://www.nuget.org/packages/Grpc.Tools/)패키지에 대 한 패키지 참조를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fa59-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="2fa59-154">GRPC C# 자산을 생성 하는 방법에 대 한 자세한 내용은 <xref:grpc/basics>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fa59-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="2fa59-155">기본적으로 `<Protobuf>` 참조는 구체적 클라이언트 및 서비스 기본 클래스를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="2fa59-156">Reference 요소의 `GrpcServices` 특성을 사용 하 여 자산 생성을 C# 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="2fa59-157">유효한 `GrpcServices` 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="2fa59-158">`Both` (표시 되지 않는 경우 기본값)</span><span class="sxs-lookup"><span data-stu-id="2fa59-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="2fa59-159">GRPC 서비스를 호스트 하는 ASP.NET Core 웹 앱에는 생성 된 서비스 기본 클래스만 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="2fa59-160">Grpc 클라이언트 앱에서 gRPC 호출에는 구체적 클라이언트만 생성 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="2fa59-161">WPF 프로젝트는 .pfiles에서 gRPC C# 자산을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="2fa59-162">WPF 프로젝트에는 gRPC 코드 생성이 올바르게 작동 하지 않도록 하는 [알려진 문제가](https://github.com/dotnet/wpf/issues/810) 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="2fa59-163">`Grpc.Tools` *및 .pfiles를 참조* 하 여 WPF 프로젝트에서 생성 된 모든 grpc 형식은 사용 시 컴파일 오류를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="2fa59-164">오류 CS0246: ' MyGrpcServices ' 형식 또는 네임 스페이스 이름을 찾을 수 없습니다. using 지시문 또는 어셈블리 참조가 있는지 확인 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fa59-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="2fa59-165">이 문제는 다음을 통해 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="2fa59-166">새 .NET Core 클래스 라이브러리 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="2fa59-167">새 프로젝트에서 참조를 추가 하 여 [ C#\*파일에서 코드 생성](xref:grpc/basics#generated-c-assets)을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="2fa59-168">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="2fa59-169">*항목 그룹에 \** .proto`<Protobuf>` 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="2fa59-170">WPF 응용 프로그램에서 새 프로젝트에 대 한 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="2fa59-171">WPF 응용 프로그램은 새 클래스 라이브러리 프로젝트에서 gRPC 생성 형식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fa59-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
