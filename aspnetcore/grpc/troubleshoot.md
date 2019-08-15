---
title: .NET Core에서 gRPC 문제 해결
author: jamesnk
description: .NET Core에서 gRPC를 사용할 때 발생 하는 오류 문제를 해결 합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/12/2019
uid: grpc/troubleshoot
ms.openlocfilehash: ad74bfa57d2dde316734d55d86075f463e78ee56
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029036"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="c009b-103">.NET Core에서 gRPC 문제 해결</span><span class="sxs-lookup"><span data-stu-id="c009b-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="c009b-104">별 [뉴턴-킹](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c009b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="c009b-105">클라이언트와 서비스의 SSL/TLS 구성이 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-105">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="c009b-106">GRPC 템플릿 및 샘플에서는 [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) 를 사용 하 여 기본적으로 grpc 서비스를 보호 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-106">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="c009b-107">gRPC 클라이언트는 보안 연결을 사용 하 여 보안 gRPC 서비스를 정상적으로 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-107">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="c009b-108">앱 시작 시 작성 된 로그에서 gRPC 서비스가 TLS를 사용 하 고 ASP.NET Core 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-108">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="c009b-109">서비스는 HTTPS 끝점에서 수신 대기 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-109">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="c009b-110">.Net Core 클라이언트는 서버 주소 `https` 에서를 사용 하 여 보안 연결을 사용 하는 호출을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-110">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
}
```

<span data-ttu-id="c009b-111">모든 gRPC 클라이언트 구현에서는 TLS를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-111">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="c009b-112">다른 언어의 gRPC 클라이언트는 일반적으로로 `SslCredentials`구성 된 채널이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-112">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="c009b-113">`SslCredentials`클라이언트에서 사용 하는 인증서를 지정 합니다 .이 인증서는 안전 하지 않은 자격 증명 대신 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-113">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="c009b-114">TLS를 사용 하도록 여러 gRPC 클라이언트 구현을 구성 하는 예제는 [Grpc 인증](https://www.grpc.io/docs/guides/auth/)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c009b-114">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="c009b-115">.NET Core 클라이언트를 사용 하 여 안전 하지 않은 gRPC 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="c009b-115">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="c009b-116">.NET Core 클라이언트를 사용 하 여 안전 하지 않은 gRPC 서비스를 호출 하려면 추가 구성이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-116">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="c009b-117">Grpc 클라이언트는 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` 스위치를로 설정 하 고 서버 `http` 주소에서를 `true` 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-117">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="c009b-118">MacOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음</span><span class="sxs-lookup"><span data-stu-id="c009b-118">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="c009b-119">Kestrel은 macOS에서 TLS를 사용 하는 HTTP/2 및 Windows 7과 같은 이전 Windows 버전을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-119">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="c009b-120">ASP.NET Core gRPC 템플릿 및 샘플은 기본적으로 TLS를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-120">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="c009b-121">GRPC 서버를 시작 하려고 하면 다음과 같은 오류 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-121">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="c009b-122">IPv4 루프백 인터페이스에서 https://localhost:5001 에 바인딩할 수 없습니다. ' TLS를 통한 HTTP/2 '는 ALPN 지원 누락으로 인해 macOS에서 지원 되지 않습니다. '</span><span class="sxs-lookup"><span data-stu-id="c009b-122">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="c009b-123">이 문제를 해결 하려면 Kestrel 및 gRPC 클라이언트에서 TLS *없이* HTTP/2를 사용 하도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-123">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="c009b-124">개발 하는 동안에만이 작업을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-124">You should only do this during development.</span></span> <span data-ttu-id="c009b-125">TLS를 사용 하지 않으면 암호화 하지 않고 gRPC 메시지가 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-125">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="c009b-126">Kestrel은 *Program.cs*에서 TLS를 사용 하지 않고 HTTP/2 끝점을 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-126">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="c009b-127">또한 gRPC 클라이언트는 TLS를 사용 하지 않도록 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-127">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="c009b-128">자세한 내용은 [.Net Core 클라이언트를 사용 하 여 안전 하지 않은 gRPC 서비스 호출](#call-insecure-grpc-services-with-net-core-client)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c009b-128">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="c009b-129">TLS를 사용 하지 않는 HTTP/2는 앱 개발 중에만 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-129">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="c009b-130">프로덕션 앱은 항상 전송 보안을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-130">Production apps should always use transport security.</span></span> <span data-ttu-id="c009b-131">자세한 내용은 [ASP.NET Core에 대 한 gRPC의 보안 고려 사항](xref:grpc/security#transport-security)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c009b-131">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="c009b-132">grpc C# 자산은 .pfiles에서  *\** 생성 된 코드가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-132">gRPC C# assets are not code generated from *\*.proto* files</span></span>

<span data-ttu-id="c009b-133">구체적 클라이언트 및 서비스 기본 클래스의 gRPC 코드 생성을 위해서는 protobuf 파일 및 도구를 프로젝트에서 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-133">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="c009b-134">다음을 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-134">You must include:</span></span>

* <span data-ttu-id="c009b-135">`<Protobuf>` 항목 그룹에서 사용 하려는 파일 *.*</span><span class="sxs-lookup"><span data-stu-id="c009b-135">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="c009b-136">[가져온 *proto* 파일](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) 은 프로젝트에서 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-136">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="c009b-137">GRPC [도구](https://www.nuget.org/packages/Grpc.Tools/)패키지에 대 한 패키지 참조를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c009b-137">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="c009b-138">GRPC C# 자산을 생성 하는 방법에 대 <xref:grpc/basics>한 자세한 내용은을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c009b-138">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="c009b-139">기본적으로 참조는 `<Protobuf>` 구체적 클라이언트 및 서비스 기본 클래스를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-139">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="c009b-140">Reference 요소의 `GrpcServices` 특성을 사용 하 여 자산 생성을 C# 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-140">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="c009b-141">유효한 `GrpcServices` 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-141">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="c009b-142">`Both`(없는 경우 기본값)</span><span class="sxs-lookup"><span data-stu-id="c009b-142">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="c009b-143">GRPC 서비스를 호스트 하는 ASP.NET Core 웹 앱에는 생성 된 서비스 기본 클래스만 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-143">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="c009b-144">Grpc 클라이언트 앱에서 gRPC 호출에는 구체적 클라이언트만 생성 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c009b-144">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
