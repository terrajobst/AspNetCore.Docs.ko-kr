---
title: ASP.NET Core를 사용하는 gRPC 서비스
author: juntaoluo
description: ASP.NET Core를 사용 하 여 gRPC 서비스를 작성할 때의 기본 개념에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 18a6dd2ddd4f3c3c4466e3b96dd1748fd0972e39
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250795"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="c96a7-103">ASP.NET Core를 사용하는 gRPC 서비스</span><span class="sxs-lookup"><span data-stu-id="c96a7-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="c96a7-104">이 문서에서는 ASP.NET Core를 사용 하 여 gRPC 서비스를 시작 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c96a7-105">전제 조건</span><span class="sxs-lookup"><span data-stu-id="c96a7-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c96a7-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c96a7-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c96a7-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c96a7-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c96a7-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c96a7-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="c96a7-109">ASP.NET Core에서 gRPC 서비스 시작</span><span class="sxs-lookup"><span data-stu-id="c96a7-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="c96a7-110">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c96a7-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c96a7-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c96a7-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c96a7-112">GRPC 프로젝트를 만드는 방법에 대 한 자세한 지침은 [grpc 서비스 시작](xref:tutorials/grpc/grpc-start) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c96a7-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c96a7-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c96a7-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c96a7-114">명령줄에서 `dotnet new grpc -o GrpcGreeter`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="c96a7-115">ASP.NET Core 앱에 gRPC 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="c96a7-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="c96a7-116">gRPC에는 [Grpc](https://www.nuget.org/packages/Grpc.AspNetCore) 패키지가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="c96a7-117">GRPC 구성</span><span class="sxs-lookup"><span data-stu-id="c96a7-117">Configure gRPC</span></span>

<span data-ttu-id="c96a7-118">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="c96a7-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="c96a7-119">grpc는 `AddGrpc` 메서드를 사용 하 여 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="c96a7-120">각 grpc 서비스는 메서드를 `MapGrpcService` 통해 라우팅 파이프라인에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]

<span data-ttu-id="c96a7-121">ASP.NET Core 미들웨어 및 기능이 라우팅 파이프라인을 공유 하므로 추가 요청 처리기를 제공 하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="c96a7-122">MVC 컨트롤러와 같은 추가 요청 처리기는 구성 된 gRPC 서비스와 병렬로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="c96a7-123">Kestrel 구성</span><span class="sxs-lookup"><span data-stu-id="c96a7-123">Configure Kestrel</span></span>

<span data-ttu-id="c96a7-124">Kestrel gRPC 끝점:</span><span class="sxs-lookup"><span data-stu-id="c96a7-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="c96a7-125">HTTP/2가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-125">Require HTTP/2.</span></span>
* <span data-ttu-id="c96a7-126">[TLS (전송 계층 보안](https://tools.ietf.org/html/rfc5246))로 보호 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="c96a7-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="c96a7-127">HTTP/2</span></span>

<span data-ttu-id="c96a7-128">gRPC에는 h t t p/2가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="c96a7-129">ASP.NET Core에 대 한 gRPC는 HttpRequest의 `HTTP/2`유효성을 검사 [합니다. 프로토콜](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 은입니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="c96a7-130">Kestrel은 최신 운영 체제에서 [HTTP/2를 지원](xref:fundamentals/servers/kestrel#http2-support) 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="c96a7-131">Kestrel 끝점은 기본적으로 HTTP/1.1 및 HTTP/2 연결을 지원 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="c96a7-132">TLS</span><span class="sxs-lookup"><span data-stu-id="c96a7-132">TLS</span></span>

<span data-ttu-id="c96a7-133">GRPC에 사용 되는 kestrel 끝점은 TLS로 보호 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="c96a7-134">개발에서 TLS로 보안이 설정 된 끝점은 ASP.NET Core 개발 인증서 `https://localhost:5001` 가 있는 경우에서 자동으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="c96a7-135">구성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-135">No configuration is required.</span></span> <span data-ttu-id="c96a7-136">접두사 `https` 는 kestrel 끝점이 TLS를 사용 하 고 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="c96a7-137">프로덕션에서는 TLS를 명시적으로 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="c96a7-138">다음 *appsettings* 예에서는 TLS로 보호 되는 HTTP/2 끝점이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="c96a7-139">또는 *Program.cs*에서 Kestrel 끝점을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="c96a7-140">프로토콜 협상</span><span class="sxs-lookup"><span data-stu-id="c96a7-140">Protocol negotiation</span></span>

<span data-ttu-id="c96a7-141">TLS는 통신 보안을 유지 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="c96a7-142">끝점에서 여러 프로토콜을 지 원하는 경우 클라이언트와 서버 간의 연결 프로토콜을 협상 하는 데 TLS [응용 프로그램 계층 프로토콜 협상 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 핸드셰이크가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="c96a7-143">이 협상은 연결에서 HTTP/1.1 또는 HTTP/2를 사용 하는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="c96a7-144">TLS 없이 HTTP/2 끝점을 구성 하는 경우 끝점의 [ListenOptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) 을로 `HttpProtocols.Http2`설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="c96a7-145">여러 프로토콜 (예: `HttpProtocols.Http1AndHttp2`)이 있는 끝점은 협상이 없으므로 TLS 없이 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="c96a7-146">안전 하지 않은 끝점에 대 한 모든 연결은 기본적으로 HTTP/1.1 및 gRPC 호출에 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="c96a7-147">Kestrel을 사용 하 여 HTTP/2 및 TLS를 사용 하는 방법에 대 한 자세한 내용은 [kestrel 끝점 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c96a7-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="c96a7-148">macOS는 TLS를 사용하는 ASP.NET Core gRPC를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="c96a7-149">macOS에서 gRPC 서비스를 성공적으로 실행하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="c96a7-150">자세한 내용은 [macOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c96a7-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="c96a7-151">ASP.NET Core Api와 통합</span><span class="sxs-lookup"><span data-stu-id="c96a7-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="c96a7-152">gRPC 서비스에는 DI ( [종속성 주입](xref:fundamentals/dependency-injection) ) 및 [로깅과](xref:fundamentals/logging/index)같은 ASP.NET Core 기능에 대 한 모든 권한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="c96a7-153">예를 들어 서비스 구현은 생성자를 통해 DI 컨테이너에서로 거 서비스를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="c96a7-154">기본적으로 gRPC 서비스 구현은 임의의 수명 (Singleton, 범위 또는 임시)으로 다른 DI 서비스를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="c96a7-155">GRPC 메서드에서 HttpContext를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="c96a7-156">GRPC API는 메서드, 호스트, 헤더 및 트레일러와 같은 일부 HTTP/2 메시지 데이터에 대 한 액세스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="c96a7-157">각 grpc 메서드에 `ServerCallContext` 전달 된 인수를 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="c96a7-158">`ServerCallContext`는 모든 ASP.NET api의에 `HttpContext` 대 한 모든 액세스 권한을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="c96a7-159">확장 `GetHttpContext` 메서드는 ASP.NET api에서 기본 HTTP `HttpContext` /2 메시지를 나타내는에 대 한 모든 권한을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c96a7-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="c96a7-160">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c96a7-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
