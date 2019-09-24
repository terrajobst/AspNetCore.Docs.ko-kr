---
title: .NET의 gRPC에서 로깅 및 진단
author: jamesnk
description: .NET의 gRPC 앱에서 진단을 수집 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: ce6ad96d9e26c9cd3844093536745f8f9bea4a76
ms.sourcegitcommit: 0365af91518004c4a44a30dc3a8ac324558a399b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204326"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="3cc84-103">.NET의 gRPC에서 로깅 및 진단</span><span class="sxs-lookup"><span data-stu-id="3cc84-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="3cc84-104">별 [뉴턴-킹](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="3cc84-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="3cc84-105">이 문서에서는 문제 해결에 도움이 되도록 gRPC 앱에서 진단 정보를 수집 하기 위한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-105">This article provides guidance for gathering diagnostics from your gRPC app to help troubleshoot issues.</span></span>

## <a name="grpc-services-logging"></a><span data-ttu-id="3cc84-106">gRPC 서비스 로깅</span><span class="sxs-lookup"><span data-stu-id="3cc84-106">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="3cc84-107">서버 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="3cc84-108">프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="3cc84-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3cc84-109">GRPC 서비스는 ASP.NET Core에서 호스트 되므로 ASP.NET Core 로깅 시스템을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-109">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="3cc84-110">기본 구성에서 gRPC는 매우 적은 정보를 기록 하지만이를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-110">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="3cc84-111">ASP.NET Core 로깅 구성에 대 한 자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index#configuration) 에 대 한 설명서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="3cc84-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="3cc84-112">grpc는 범주 아래에 `Grpc` 로그를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-112">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="3cc84-113">Grpc에서 자세한 로그를 사용 하도록 설정 하려면 `Grpc` 의 `LogLevel` `Logging`하위 섹션 `Debug` 에 다음 항목을 추가 하 여 *appsettings* 파일의 수준에 대 한 접두사를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-113">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7)]

<span data-ttu-id="3cc84-114">다음을 사용 하 여 `ConfigureLogging` *Startup.cs* 에서 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-114">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5)]

<span data-ttu-id="3cc84-115">JSON 기반 구성을 사용 하지 않는 경우 구성 시스템에서 다음 구성 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-115">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="3cc84-116">구성 시스템에 대 한 설명서를 확인 하 여 중첩 된 구성 값을 지정 하는 방법을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-116">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="3cc84-117">예를 들어 환경 변수를 사용 하는 `_` 경우 대신 `:` 두 개의 문자를 사용 합니다 (예 `Logging__LogLevel__Grpc`:).</span><span class="sxs-lookup"><span data-stu-id="3cc84-117">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="3cc84-118">앱에 대 한 `Debug` 자세한 진단을 수집할 때 수준을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-118">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="3cc84-119">이 `Trace` 수준은 매우 낮은 수준의 진단을 생성 하며 앱에서 문제를 진단 하는 데 거의 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-119">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

### <a name="sample-logging-output"></a><span data-ttu-id="3cc84-120">샘플 로깅 출력</span><span class="sxs-lookup"><span data-stu-id="3cc84-120">Sample logging output</span></span>

<span data-ttu-id="3cc84-121">Grpc 서비스 `Debug` 수준에서 콘솔 출력의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-121">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

## <a name="access-server-side-logs"></a><span data-ttu-id="3cc84-122">서버 쪽 로그 액세스</span><span class="sxs-lookup"><span data-stu-id="3cc84-122">Access server-side logs</span></span>

<span data-ttu-id="3cc84-123">서버 쪽 로그에 액세스 하는 방법은를 실행 하는 환경에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app"></a><span data-ttu-id="3cc84-124">콘솔 앱으로</span><span class="sxs-lookup"><span data-stu-id="3cc84-124">As a console app</span></span>

<span data-ttu-id="3cc84-125">콘솔 응용 프로그램에서 실행 하는 경우 [콘솔로 거](xref:fundamentals/logging/index#console-provider) 를 기본적으로 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="3cc84-126">gRPC 로그가 콘솔에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-126">gRPC logs will appear in the console.</span></span>

### <a name="other-environments"></a><span data-ttu-id="3cc84-127">기타 환경</span><span class="sxs-lookup"><span data-stu-id="3cc84-127">Other environments</span></span>

<span data-ttu-id="3cc84-128">앱을 다른 환경 (예: Docker, Kubernetes 또는 Windows 서비스)에 배포 하는 경우 환경에 적합 <xref:fundamentals/logging/index> 한 로깅 공급자를 구성 하는 방법에 대 한 자세한 내용은을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="3cc84-128">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="grpc-client-logging"></a><span data-ttu-id="3cc84-129">gRPC 클라이언트 로깅</span><span class="sxs-lookup"><span data-stu-id="3cc84-129">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="3cc84-130">클라이언트 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-130">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="3cc84-131">프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="3cc84-131">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3cc84-132">.Net 클라이언트에서 로그를 가져오기 위해 클라이언트의 채널을 만들 `GrpcChannelOptions.LoggerFactory` 때 속성을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-132">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="3cc84-133">ASP.NET Core 앱에서 gRPC 서비스를 호출 하는 경우에는 DI (종속성 주입)에서로 거 팩터리를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-133">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="3cc84-134">클라이언트 로깅을 사용 하도록 설정 하는 다른 방법은 [Grpc 클라이언트 팩터리](xref:grpc/clientfactory) 를 사용 하 여 클라이언트를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-134">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="3cc84-135">클라이언트 팩터리에 등록 되 고 DI에서 확인 된 gRPC 클라이언트는 자동으로 앱의 구성 된 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-135">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="3cc84-136">앱에서 DI를 사용 하지 않는 경우 [server.loggerfactory](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)를 사용 하 `ILoggerFactory` 여 새 인스턴스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-136">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="3cc84-137">이 메서드에 액세스 하려면 앱에 [Microsoft Extensions.](https://www.nuget.org/packages/microsoft.extensions.logging/) i n i 패키지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-137">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/net-client-loggerfactory-create.cs?highlight=1,8)]

### <a name="sample-logging-output"></a><span data-ttu-id="3cc84-138">샘플 로깅 출력</span><span class="sxs-lookup"><span data-stu-id="3cc84-138">Sample logging output</span></span>

<span data-ttu-id="3cc84-139">다음은 grpc 클라이언트 `Debug` 수준에 있는 콘솔 출력의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="3cc84-139">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="additional-resources"></a><span data-ttu-id="3cc84-140">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3cc84-140">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
