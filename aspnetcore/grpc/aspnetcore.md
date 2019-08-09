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
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="e1b50-103">ASP.NET Core를 사용하는 gRPC 서비스</span><span class="sxs-lookup"><span data-stu-id="e1b50-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="e1b50-104">이 문서에서는 ASP.NET Core를 사용 하 여 gRPC 서비스를 시작 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e1b50-105">전제 조건</span><span class="sxs-lookup"><span data-stu-id="e1b50-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e1b50-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1b50-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e1b50-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e1b50-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e1b50-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e1b50-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="e1b50-109">ASP.NET Core에서 gRPC 서비스 시작</span><span class="sxs-lookup"><span data-stu-id="e1b50-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="e1b50-110">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1b50-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e1b50-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1b50-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e1b50-112">GRPC 프로젝트를 만드는 방법에 대 한 자세한 지침은 [grpc 서비스 시작](xref:tutorials/grpc/grpc-start) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e1b50-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="e1b50-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e1b50-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e1b50-114">명령줄에서 `dotnet new grpc -o GrpcGreeter`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="e1b50-115">ASP.NET Core 앱에 gRPC 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="e1b50-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="e1b50-116">gRPC에는 [Grpc](https://www.nuget.org/packages/Grpc.AspNetCore) 패키지가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="e1b50-117">GRPC 구성</span><span class="sxs-lookup"><span data-stu-id="e1b50-117">Configure gRPC</span></span>

<span data-ttu-id="e1b50-118">grpc는 다음과 같은 `AddGrpc` 방법으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-118">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

<span data-ttu-id="e1b50-119">각 grpc 서비스는 메서드를 `MapGrpcService` 통해 라우팅 파이프라인에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-119">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

<span data-ttu-id="e1b50-120">ASP.NET Core 미들웨어 및 기능이 라우팅 파이프라인을 공유 하므로 추가 요청 처리기를 제공 하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-120">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="e1b50-121">MVC 컨트롤러와 같은 추가 요청 처리기는 구성 된 gRPC 서비스와 병렬로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-121">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="e1b50-122">ASP.NET Core Api와 통합</span><span class="sxs-lookup"><span data-stu-id="e1b50-122">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="e1b50-123">gRPC 서비스에는 DI ( [종속성 주입](xref:fundamentals/dependency-injection) ) 및 [로깅과](xref:fundamentals/logging/index)같은 ASP.NET Core 기능에 대 한 모든 권한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-123">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e1b50-124">예를 들어 서비스 구현은 생성자를 통해 DI 컨테이너에서로 거 서비스를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-124">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="e1b50-125">기본적으로 gRPC 서비스 구현은 임의의 수명 (Singleton, 범위 또는 임시)으로 다른 DI 서비스를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-125">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="e1b50-126">GRPC 메서드에서 HttpContext를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-126">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="e1b50-127">GRPC API는 메서드, 호스트, 헤더 및 트레일러와 같은 일부 HTTP/2 메시지 데이터에 대 한 액세스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-127">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="e1b50-128">각 grpc 메서드에 `ServerCallContext` 전달 된 인수를 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-128">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="e1b50-129">`ServerCallContext`는 모든 ASP.NET api의에 `HttpContext` 대 한 모든 액세스 권한을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-129">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="e1b50-130">확장 `GetHttpContext` 메서드는 ASP.NET api에서 기본 HTTP `HttpContext` /2 메시지를 나타내는에 대 한 모든 권한을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-130">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="grpc-and-aspnet-core-on-macos"></a><span data-ttu-id="e1b50-131">macOS에서 gRPC 및 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e1b50-131">gRPC and ASP.NET Core on macOS</span></span>

<span data-ttu-id="e1b50-132">Kestrel은 macOS에서 [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) 를 사용 하는 HTTP/2를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-132">Kestrel doesn't support HTTP/2 with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) on macOS.</span></span> <span data-ttu-id="e1b50-133">ASP.NET Core gRPC 템플릿 및 샘플은 기본적으로 TLS를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="e1b50-134">GRPC 서버를 시작 하려고 하면 다음과 같은 오류 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="e1b50-135">IPv4 루프백 인터페이스에서 https://localhost:5001 에 바인딩할 수 없습니다. ' TLS를 통한 HTTP/2 '는 ALPN 지원 누락으로 인해 macOS에서 지원 되지 않습니다. '</span><span class="sxs-lookup"><span data-stu-id="e1b50-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="e1b50-136">이 문제를 해결 하려면 Kestrel 및 gRPC 클라이언트에서 TLS **없이** HTTP/2를 사용 하도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 **without** TLS.</span></span> <span data-ttu-id="e1b50-137">개발 하는 동안에만이 작업을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-137">You should only do this during development.</span></span> <span data-ttu-id="e1b50-138">TLS를 사용 하지 않으면 암호화 하지 않고 gRPC 메시지가 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="e1b50-139">Kestrel은에서 `Program.cs`TLS 없이 HTTP/2 끝점을 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-139">Kestrel must configure a HTTP/2 endpoint without TLS in `Program.cs`:</span></span>

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

<span data-ttu-id="e1b50-140">Grpc 클라이언트는 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` 스위치를로 설정 하 고 서버 `http` 주소에서를 `true` 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-140">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

> [!WARNING]
> <span data-ttu-id="e1b50-141">TLS를 사용 하지 않는 HTTP/2는 앱 개발 중에만 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-141">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="e1b50-142">프로덕션 응용 프로그램은 항상 전송 보안을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e1b50-142">Production applications should always use transport security.</span></span> <span data-ttu-id="e1b50-143">자세한 내용은 [ASP.NET Core에 대 한 gRPC의 보안 고려 사항](xref:grpc/security#transport-security)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e1b50-143">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1b50-144">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e1b50-144">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
