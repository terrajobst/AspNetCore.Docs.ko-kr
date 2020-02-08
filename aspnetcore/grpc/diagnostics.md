---
title: .NET의 gRPC에서 로깅 및 진단
author: jamesnk
description: .NET의 gRPC 앱에서 진단을 수집 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 17607b734e6d777de9516aa14e81c97f87b61023
ms.sourcegitcommit: 80286715afb93c4d13c931b008016d6086c0312b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77074525"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="81563-103">.NET의 gRPC에서 로깅 및 진단</span><span class="sxs-lookup"><span data-stu-id="81563-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="81563-104">별 [뉴턴-킹](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="81563-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="81563-105">이 문서에서는 문제 해결을 위해 gRPC 앱에서 진단 정보를 수집 하기 위한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="81563-106">다음 내용을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="81563-106">Topics covered include:</span></span>

* <span data-ttu-id="81563-107">**로깅** 구조적 로그는 [.net Core 로깅](xref:fundamentals/logging/index)에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81563-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="81563-108"><xref:Microsoft.Extensions.Logging.ILogger>는 앱 프레임 워크에서 로그를 기록 하는 데 사용 되며, 사용자는 앱에서 자체 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="81563-109">**추적** -`DiaganosticSource` 및 `Activity`를 사용 하 여 작성 된 작업과 관련 된 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="81563-110">진단 원본에서의 추적은 일반적으로 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 및 [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)와 같은 라이브러리에서 앱 원격 분석을 수집 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81563-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="81563-111">**메트릭** -시간 간격에 대 한 데이터 측정값의 표현 (예: 초당 요청 수)입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="81563-112">메트릭은 `EventCounter`를 사용 하 여 내보내지고 [dotnet 카운터](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) 명령줄 도구나 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)를 사용 하 여 관찰할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="81563-113">로깅</span><span class="sxs-lookup"><span data-stu-id="81563-113">Logging</span></span>

<span data-ttu-id="81563-114">gRPC 서비스 및 gRPC 클라이언트는 [.Net Core 로깅을](xref:fundamentals/logging/index)사용 하 여 로그를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="81563-115">응용 프로그램에서 예기치 않은 동작을 디버깅 해야 하는 경우 로그를 시작 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="81563-116">gRPC 서비스 로깅</span><span class="sxs-lookup"><span data-stu-id="81563-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="81563-117">서버 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="81563-118">프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="81563-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="81563-119">GRPC 서비스는 ASP.NET Core에서 호스트 되므로 ASP.NET Core 로깅 시스템을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="81563-120">기본 구성에서 gRPC는 매우 적은 정보를 기록 하지만이를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="81563-121">ASP.NET Core 로깅 구성에 대 한 자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index#configuration) 에 대 한 설명서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="81563-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="81563-122">gRPC는 `Grpc` 범주에 로그를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="81563-123">GRPC에서 자세한 로그를 사용 하도록 설정 하려면 `Logging`의 `LogLevel` 하위 섹션에 다음 항목을 추가 하 여 *appsettings* 파일의 `Debug` 수준에 `Grpc` 접두사를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="81563-124">`ConfigureLogging`를 사용 하 여 *Startup.cs* 에서이를 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="81563-125">JSON 기반 구성을 사용 하지 않는 경우 구성 시스템에서 다음 구성 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="81563-126">구성 시스템에 대 한 설명서를 확인 하 여 중첩 된 구성 값을 지정 하는 방법을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="81563-127">예를 들어 환경 변수를 사용 하는 경우 `:` 대신 두 개의 `_` 문자가 사용 됩니다 (예: `Logging__LogLevel__Grpc`).</span><span class="sxs-lookup"><span data-stu-id="81563-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="81563-128">앱에 대 한 자세한 진단을 수집할 때 `Debug` 수준을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="81563-129">`Trace` 수준은 매우 낮은 수준의 진단을 생성 하며 앱에서 문제를 진단 하는 데 거의 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="81563-130">샘플 로깅 출력</span><span class="sxs-lookup"><span data-stu-id="81563-130">Sample logging output</span></span>

<span data-ttu-id="81563-131">다음은 gRPC 서비스의 `Debug` 수준에 있는 콘솔 출력의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```console
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

### <a name="access-server-side-logs"></a><span data-ttu-id="81563-132">서버 쪽 로그 액세스</span><span class="sxs-lookup"><span data-stu-id="81563-132">Access server-side logs</span></span>

<span data-ttu-id="81563-133">서버 쪽 로그에 액세스 하는 방법은를 실행 하는 환경에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="81563-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="81563-134">콘솔 앱으로</span><span class="sxs-lookup"><span data-stu-id="81563-134">As a console app</span></span>

<span data-ttu-id="81563-135">콘솔 응용 프로그램에서 실행 하는 경우 [콘솔로 거](xref:fundamentals/logging/index#console-provider) 를 기본적으로 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="81563-136">gRPC 로그가 콘솔에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81563-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="81563-137">기타 환경</span><span class="sxs-lookup"><span data-stu-id="81563-137">Other environments</span></span>

<span data-ttu-id="81563-138">앱을 다른 환경 (예: Docker, Kubernetes 또는 Windows 서비스)에 배포 하는 경우 환경에 적합 한 로깅 공급자를 구성 하는 방법에 대 한 자세한 내용은 <xref:fundamentals/logging/index>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="81563-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="81563-139">gRPC 클라이언트 로깅</span><span class="sxs-lookup"><span data-stu-id="81563-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="81563-140">클라이언트 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="81563-141">프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="81563-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="81563-142">.NET 클라이언트에서 로그를 가져오기 위해 클라이언트의 채널을 만들 때 `GrpcChannelOptions.LoggerFactory` 속성을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="81563-143">ASP.NET Core 앱에서 gRPC 서비스를 호출 하는 경우에는 DI (종속성 주입)에서로 거 팩터리를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="81563-144">클라이언트 로깅을 사용 하도록 설정 하는 다른 방법은 [Grpc 클라이언트 팩터리](xref:grpc/clientfactory) 를 사용 하 여 클라이언트를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="81563-145">클라이언트 팩터리에 등록 되 고 DI에서 확인 된 gRPC 클라이언트는 자동으로 앱의 구성 된 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="81563-146">앱에서 DI를 사용 하지 않는 경우 [server.loggerfactory](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)를 사용 하 여 새 `ILoggerFactory` 인스턴스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="81563-147">이 메서드에 액세스 하려면 앱에 [Microsoft Extensions.](https://www.nuget.org/packages/microsoft.extensions.logging/) i n i 패키지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="81563-148">gRPC 클라이언트 로그 범위</span><span class="sxs-lookup"><span data-stu-id="81563-148">gRPC client log scopes</span></span>

<span data-ttu-id="81563-149">GRPC 클라이언트는 gRPC 호출 중에 생성 된 로그에 [로깅 범위](https://docs.microsoft.com/aspnet/core/fundamentals/logginglog-scopes) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-149">The gRPC client adds a [logging scope](https://docs.microsoft.com/aspnet/core/fundamentals/logginglog-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="81563-150">범위에는 gRPC 호출과 관련 된 메타 데이터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="81563-151">**Grpcmethodtype** -grpc 메서드 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="81563-152">가능한 값은 `Grpc.Core.MethodType` 열거형의 이름 (예: 단항)입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="81563-153">**GrpcUri** -grpc 메서드의 상대 URI (예:/greet.) Greeter/SayHellos</span><span class="sxs-lookup"><span data-stu-id="81563-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="81563-154">샘플 로깅 출력</span><span class="sxs-lookup"><span data-stu-id="81563-154">Sample logging output</span></span>

<span data-ttu-id="81563-155">다음은 gRPC 클라이언트의 `Debug` 수준에 있는 콘솔 출력의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a><span data-ttu-id="81563-156">추적</span><span class="sxs-lookup"><span data-stu-id="81563-156">Tracing</span></span>

<span data-ttu-id="81563-157">gRPC 서비스 및 gRPC 클라이언트는 [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) 및 [활동](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity)을 사용 하 여 grpc 호출에 대 한 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="81563-158">.NET gRPC는 활동을 사용 하 여 gRPC 통화를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="81563-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="81563-159">추적 이벤트는 gRPC 통화 활동의 시작 및 중지 시 진단 원본에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81563-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="81563-160">추적은 gRPC 스트리밍 호출의 수명 동안 메시지가 전송 되는 경우에 대 한 정보를 캡처하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="81563-161">gRPC 서비스 추적</span><span class="sxs-lookup"><span data-stu-id="81563-161">gRPC service tracing</span></span>

<span data-ttu-id="81563-162">gRPC 서비스는 들어오는 HTTP 요청에 대 한 이벤트를 보고 하는 ASP.NET Core에서 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="81563-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="81563-163">gRPC 특정 메타 데이터는 ASP.NET Core에서 제공 하는 기존 HTTP 요청 진단에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81563-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="81563-164">진단 소스 이름이 `Microsoft.AspNetCore`되었습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="81563-165">작업 이름이 `Microsoft.AspNetCore.Hosting.HttpRequestIn`입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="81563-166">GRPC 호출에 의해 호출 되는 gRPC 메서드의 이름은 `grpc.method`이름의 태그로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81563-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="81563-167">완료 될 때 gRPC 호출의 상태 코드가 `grpc.status_code`이름의 태그로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81563-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="81563-168">gRPC 클라이언트 추적</span><span class="sxs-lookup"><span data-stu-id="81563-168">gRPC client tracing</span></span>

<span data-ttu-id="81563-169">.NET gRPC 클라이언트는 `HttpClient`을 사용 하 여 gRPC 호출을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="81563-170">`HttpClient`는 진단 이벤트를 작성 하지만 .NET gRPC 클라이언트는 gRPC 호출에 대 한 전체 정보를 수집할 수 있도록 사용자 지정 진단 원본, 활동 및 이벤트를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="81563-171">진단 소스 이름이 `Grpc.Net.Client`되었습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="81563-172">작업 이름이 `Grpc.Net.Client.GrpcOut`입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="81563-173">GRPC 호출에 의해 호출 되는 gRPC 메서드의 이름은 `grpc.method`이름의 태그로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81563-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="81563-174">완료 될 때 gRPC 호출의 상태 코드가 `grpc.status_code`이름의 태그로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81563-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="81563-175">추적 수집</span><span class="sxs-lookup"><span data-stu-id="81563-175">Collecting tracing</span></span>

<span data-ttu-id="81563-176">`DiagnosticSource`를 사용 하는 가장 쉬운 방법은 앱에서 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 또는 [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) 와 같은 원격 분석 라이브러리를 구성 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="81563-177">라이브러리는 다른 앱 원격 분석과 함께 gRPC 호출에 대 한 정보를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="81563-178">추적은 Application Insights와 같은 관리 되는 서비스에서 볼 수 있으며, 사용자가 직접 분산 추적 시스템을 실행 하도록 선택할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="81563-179">OpenTelemetry는 추적 데이터를 [Jaeger](https://www.jaegertracing.io/) 및 [Zipkin](https://zipkin.io/)로 내보내는 기능을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="81563-180">`DiagnosticSource` `DiagnosticListener`를 사용 하 여 코드에서 추적 이벤트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="81563-181">코드를 사용 하 여 진단 소스를 수신 하는 방법에 대 한 자세한 내용은 [DiagnosticSource 사용자 가이드](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="81563-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="81563-182">원격 분석 라이브러리는 현재 gRPC 관련 `Grpc.Net.Client.GrpcOut` 원격 분석을 캡처하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="81563-183">이 추적을 캡처하는 원격 분석 라이브러리를 개선 하는 작업은 진행 중입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="81563-184">메트릭</span><span class="sxs-lookup"><span data-stu-id="81563-184">Metrics</span></span>

<span data-ttu-id="81563-185">메트릭은 시간 간격 (예: 초당 요청 수)에 대 한 데이터 측정값의 표현입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="81563-186">메트릭 데이터를 사용 하면 상위 수준에서 앱의 상태를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="81563-187">.NET gRPC 메트릭은 `EventCounter`를 사용 하 여 내보내집니다.</span><span class="sxs-lookup"><span data-stu-id="81563-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="81563-188">gRPC 서비스 메트릭</span><span class="sxs-lookup"><span data-stu-id="81563-188">gRPC service metrics</span></span>

<span data-ttu-id="81563-189">gRPC 서버 메트릭은 `Grpc.AspNetCore.Server` 이벤트 원본에 보고 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81563-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="81563-190">속성</span><span class="sxs-lookup"><span data-stu-id="81563-190">Name</span></span>                      | <span data-ttu-id="81563-191">Description</span><span class="sxs-lookup"><span data-stu-id="81563-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="81563-192">총 호출</span><span class="sxs-lookup"><span data-stu-id="81563-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="81563-193">현재 호출</span><span class="sxs-lookup"><span data-stu-id="81563-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="81563-194">총 호출 실패</span><span class="sxs-lookup"><span data-stu-id="81563-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="81563-195">총 통화 최종 기한 초과</span><span class="sxs-lookup"><span data-stu-id="81563-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="81563-196">총 보낸 메시지</span><span class="sxs-lookup"><span data-stu-id="81563-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="81563-197">받은 총 메시지 수</span><span class="sxs-lookup"><span data-stu-id="81563-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="81563-198">구현 되지 않은 총 호출</span><span class="sxs-lookup"><span data-stu-id="81563-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="81563-199">또한 ASP.NET Core `Microsoft.AspNetCore.Hosting` 이벤트 원본에 대 한 고유한 메트릭을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="81563-200">gRPC 클라이언트 메트릭</span><span class="sxs-lookup"><span data-stu-id="81563-200">gRPC client metrics</span></span>

<span data-ttu-id="81563-201">gRPC 클라이언트 메트릭은 `Grpc.Net.Client` 이벤트 원본에 보고 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81563-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="81563-202">속성</span><span class="sxs-lookup"><span data-stu-id="81563-202">Name</span></span>                      | <span data-ttu-id="81563-203">Description</span><span class="sxs-lookup"><span data-stu-id="81563-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="81563-204">총 호출</span><span class="sxs-lookup"><span data-stu-id="81563-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="81563-205">현재 호출</span><span class="sxs-lookup"><span data-stu-id="81563-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="81563-206">총 호출 실패</span><span class="sxs-lookup"><span data-stu-id="81563-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="81563-207">총 통화 최종 기한 초과</span><span class="sxs-lookup"><span data-stu-id="81563-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="81563-208">총 보낸 메시지</span><span class="sxs-lookup"><span data-stu-id="81563-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="81563-209">받은 총 메시지 수</span><span class="sxs-lookup"><span data-stu-id="81563-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="81563-210">메트릭 관찰</span><span class="sxs-lookup"><span data-stu-id="81563-210">Observe metrics</span></span>

<span data-ttu-id="81563-211">[dotnet-카운터](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) 는 임시 상태 모니터링 및 첫 번째 수준의 성능 조사를 위한 성능 모니터링 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="81563-212">공급자 이름으로 `Grpc.AspNetCore.Server` 또는 `Grpc.Net.Client`를 사용 하 여 .NET 앱을 모니터링 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

<span data-ttu-id="81563-213">GRPC 메트릭을 관찰 하는 또 다른 방법은 Application Insights의 [Microsoft ApplicationInsights. EventCounterCollector 패키지](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)를 사용 하 여 카운터 데이터를 캡처하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="81563-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="81563-214">설치가 완료 되 면 Application Insights는 런타임에 일반적인 .NET 카운터를 수집 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="81563-215">gRPC의 카운터는 기본적으로 수집 되지 않지만 [추가 카운터를 포함 하도록](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)앱 정보를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81563-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="81563-216">*Startup.cs*에서 수집할 응용 프로그램 정보에 대 한 grpc 카운터를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="81563-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a><span data-ttu-id="81563-217">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="81563-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
