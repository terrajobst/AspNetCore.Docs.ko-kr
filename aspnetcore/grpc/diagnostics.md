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
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>.NET의 gRPC에서 로깅 및 진단

별 [뉴턴-킹](https://twitter.com/jamesnk)

이 문서에서는 문제 해결을 위해 gRPC 앱에서 진단 정보를 수집 하기 위한 지침을 제공 합니다. 다음 내용을 다룹니다.

* **로깅** 구조적 로그는 [.net Core 로깅](xref:fundamentals/logging/index)에 기록 됩니다. <xref:Microsoft.Extensions.Logging.ILogger>는 앱 프레임 워크에서 로그를 기록 하는 데 사용 되며, 사용자는 앱에서 자체 로깅을 사용 합니다.
* **추적** -`DiaganosticSource` 및 `Activity`를 사용 하 여 작성 된 작업과 관련 된 이벤트입니다. 진단 원본에서의 추적은 일반적으로 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 및 [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)와 같은 라이브러리에서 앱 원격 분석을 수집 하는 데 사용 됩니다.
* **메트릭** -시간 간격에 대 한 데이터 측정값의 표현 (예: 초당 요청 수)입니다. 메트릭은 `EventCounter`를 사용 하 여 내보내지고 [dotnet 카운터](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) 명령줄 도구나 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)를 사용 하 여 관찰할 수 있습니다.

## <a name="logging"></a>로깅

gRPC 서비스 및 gRPC 클라이언트는 [.Net Core 로깅을](xref:fundamentals/logging/index)사용 하 여 로그를 작성 합니다. 응용 프로그램에서 예기치 않은 동작을 디버깅 해야 하는 경우 로그를 시작 하는 것이 좋습니다.

### <a name="grpc-services-logging"></a>gRPC 서비스 로깅

> [!WARNING]
> 서버 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다. 프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .

GRPC 서비스는 ASP.NET Core에서 호스트 되므로 ASP.NET Core 로깅 시스템을 사용 합니다. 기본 구성에서 gRPC는 매우 적은 정보를 기록 하지만이를 구성할 수 있습니다. ASP.NET Core 로깅 구성에 대 한 자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index#configuration) 에 대 한 설명서를 참조 하세요.

gRPC는 `Grpc` 범주에 로그를 추가 합니다. GRPC에서 자세한 로그를 사용 하도록 설정 하려면 `Logging`의 `LogLevel` 하위 섹션에 다음 항목을 추가 하 여 *appsettings* 파일의 `Debug` 수준에 `Grpc` 접두사를 구성 합니다.

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

`ConfigureLogging`를 사용 하 여 *Startup.cs* 에서이를 구성할 수도 있습니다.

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

JSON 기반 구성을 사용 하지 않는 경우 구성 시스템에서 다음 구성 값을 설정 합니다.

* `Logging:LogLevel:Grpc` = `Debug`

구성 시스템에 대 한 설명서를 확인 하 여 중첩 된 구성 값을 지정 하는 방법을 확인 합니다. 예를 들어 환경 변수를 사용 하는 경우 `:` 대신 두 개의 `_` 문자가 사용 됩니다 (예: `Logging__LogLevel__Grpc`).

앱에 대 한 자세한 진단을 수집할 때 `Debug` 수준을 사용 하는 것이 좋습니다. `Trace` 수준은 매우 낮은 수준의 진단을 생성 하며 앱에서 문제를 진단 하는 데 거의 필요 하지 않습니다.

#### <a name="sample-logging-output"></a>샘플 로깅 출력

다음은 gRPC 서비스의 `Debug` 수준에 있는 콘솔 출력의 예입니다.

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

### <a name="access-server-side-logs"></a>서버 쪽 로그 액세스

서버 쪽 로그에 액세스 하는 방법은를 실행 하는 환경에 따라 달라 집니다.

#### <a name="as-a-console-app"></a>콘솔 앱으로

콘솔 응용 프로그램에서 실행 하는 경우 [콘솔로 거](xref:fundamentals/logging/index#console-provider) 를 기본적으로 사용 하도록 설정 해야 합니다. gRPC 로그가 콘솔에 표시 됩니다.

#### <a name="other-environments"></a>기타 환경

앱을 다른 환경 (예: Docker, Kubernetes 또는 Windows 서비스)에 배포 하는 경우 환경에 적합 한 로깅 공급자를 구성 하는 방법에 대 한 자세한 내용은 <xref:fundamentals/logging/index>를 참조 하세요.

### <a name="grpc-client-logging"></a>gRPC 클라이언트 로깅

> [!WARNING]
> 클라이언트 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다. 프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .

.NET 클라이언트에서 로그를 가져오기 위해 클라이언트의 채널을 만들 때 `GrpcChannelOptions.LoggerFactory` 속성을 설정할 수 있습니다. ASP.NET Core 앱에서 gRPC 서비스를 호출 하는 경우에는 DI (종속성 주입)에서로 거 팩터리를 확인할 수 있습니다.

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

클라이언트 로깅을 사용 하도록 설정 하는 다른 방법은 [Grpc 클라이언트 팩터리](xref:grpc/clientfactory) 를 사용 하 여 클라이언트를 만드는 것입니다. 클라이언트 팩터리에 등록 되 고 DI에서 확인 된 gRPC 클라이언트는 자동으로 앱의 구성 된 로깅을 사용 합니다.

앱에서 DI를 사용 하지 않는 경우 [server.loggerfactory](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)를 사용 하 여 새 `ILoggerFactory` 인스턴스를 만들 수 있습니다. 이 메서드에 액세스 하려면 앱에 [Microsoft Extensions.](https://www.nuget.org/packages/microsoft.extensions.logging/) i n i 패키지를 추가 합니다.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>gRPC 클라이언트 로그 범위

GRPC 클라이언트는 gRPC 호출 중에 생성 된 로그에 [로깅 범위](https://docs.microsoft.com/aspnet/core/fundamentals/logginglog-scopes) 를 추가 합니다. 범위에는 gRPC 호출과 관련 된 메타 데이터가 있습니다.

* **Grpcmethodtype** -grpc 메서드 형식입니다. 가능한 값은 `Grpc.Core.MethodType` 열거형의 이름 (예: 단항)입니다.
* **GrpcUri** -grpc 메서드의 상대 URI (예:/greet.) Greeter/SayHellos

#### <a name="sample-logging-output"></a>샘플 로깅 출력

다음은 gRPC 클라이언트의 `Debug` 수준에 있는 콘솔 출력의 예입니다.

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

## <a name="tracing"></a>추적

gRPC 서비스 및 gRPC 클라이언트는 [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) 및 [활동](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity)을 사용 하 여 grpc 호출에 대 한 정보를 제공 합니다.

* .NET gRPC는 활동을 사용 하 여 gRPC 통화를 나타냅니다.
* 추적 이벤트는 gRPC 통화 활동의 시작 및 중지 시 진단 원본에 기록 됩니다.
* 추적은 gRPC 스트리밍 호출의 수명 동안 메시지가 전송 되는 경우에 대 한 정보를 캡처하지 않습니다.

### <a name="grpc-service-tracing"></a>gRPC 서비스 추적

gRPC 서비스는 들어오는 HTTP 요청에 대 한 이벤트를 보고 하는 ASP.NET Core에서 호스팅됩니다. gRPC 특정 메타 데이터는 ASP.NET Core에서 제공 하는 기존 HTTP 요청 진단에 추가 됩니다.

* 진단 소스 이름이 `Microsoft.AspNetCore`되었습니다.
* 작업 이름이 `Microsoft.AspNetCore.Hosting.HttpRequestIn`입니다.
  * GRPC 호출에 의해 호출 되는 gRPC 메서드의 이름은 `grpc.method`이름의 태그로 추가 됩니다.
  * 완료 될 때 gRPC 호출의 상태 코드가 `grpc.status_code`이름의 태그로 추가 됩니다.

### <a name="grpc-client-tracing"></a>gRPC 클라이언트 추적

.NET gRPC 클라이언트는 `HttpClient`을 사용 하 여 gRPC 호출을 수행 합니다. `HttpClient`는 진단 이벤트를 작성 하지만 .NET gRPC 클라이언트는 gRPC 호출에 대 한 전체 정보를 수집할 수 있도록 사용자 지정 진단 원본, 활동 및 이벤트를 제공 합니다.

* 진단 소스 이름이 `Grpc.Net.Client`되었습니다.
* 작업 이름이 `Grpc.Net.Client.GrpcOut`입니다.
  * GRPC 호출에 의해 호출 되는 gRPC 메서드의 이름은 `grpc.method`이름의 태그로 추가 됩니다.
  * 완료 될 때 gRPC 호출의 상태 코드가 `grpc.status_code`이름의 태그로 추가 됩니다.

### <a name="collecting-tracing"></a>추적 수집

`DiagnosticSource`를 사용 하는 가장 쉬운 방법은 앱에서 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 또는 [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) 와 같은 원격 분석 라이브러리를 구성 하는 것입니다. 라이브러리는 다른 앱 원격 분석과 함께 gRPC 호출에 대 한 정보를 처리 합니다.

추적은 Application Insights와 같은 관리 되는 서비스에서 볼 수 있으며, 사용자가 직접 분산 추적 시스템을 실행 하도록 선택할 수도 있습니다. OpenTelemetry는 추적 데이터를 [Jaeger](https://www.jaegertracing.io/) 및 [Zipkin](https://zipkin.io/)로 내보내는 기능을 지원 합니다.

`DiagnosticSource` `DiagnosticListener`를 사용 하 여 코드에서 추적 이벤트를 사용할 수 있습니다. 코드를 사용 하 여 진단 소스를 수신 하는 방법에 대 한 자세한 내용은 [DiagnosticSource 사용자 가이드](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener)를 참조 하세요.

> [!NOTE]
> 원격 분석 라이브러리는 현재 gRPC 관련 `Grpc.Net.Client.GrpcOut` 원격 분석을 캡처하지 않습니다. 이 추적을 캡처하는 원격 분석 라이브러리를 개선 하는 작업은 진행 중입니다.

## <a name="metrics"></a>메트릭

메트릭은 시간 간격 (예: 초당 요청 수)에 대 한 데이터 측정값의 표현입니다. 메트릭 데이터를 사용 하면 상위 수준에서 앱의 상태를 확인할 수 있습니다. .NET gRPC 메트릭은 `EventCounter`를 사용 하 여 내보내집니다.

### <a name="grpc-service-metrics"></a>gRPC 서비스 메트릭

gRPC 서버 메트릭은 `Grpc.AspNetCore.Server` 이벤트 원본에 보고 됩니다.

| 속성                      | Description                   |
| --------------------------|-------------------------------|
| `total-calls`             | 총 호출                   |
| `current-calls`           | 현재 호출                 |
| `calls-failed`            | 총 호출 실패            |
| `calls-deadline-exceeded` | 총 통화 최종 기한 초과 |
| `messages-sent`           | 총 보낸 메시지           |
| `messages-received`       | 받은 총 메시지 수       |
| `calls-unimplemented`     | 구현 되지 않은 총 호출     |

또한 ASP.NET Core `Microsoft.AspNetCore.Hosting` 이벤트 원본에 대 한 고유한 메트릭을 제공 합니다.

### <a name="grpc-client-metrics"></a>gRPC 클라이언트 메트릭

gRPC 클라이언트 메트릭은 `Grpc.Net.Client` 이벤트 원본에 보고 됩니다.

| 속성                      | Description                   |
| --------------------------|-------------------------------|
| `total-calls`             | 총 호출                   |
| `current-calls`           | 현재 호출                 |
| `calls-failed`            | 총 호출 실패            |
| `calls-deadline-exceeded` | 총 통화 최종 기한 초과 |
| `messages-sent`           | 총 보낸 메시지           |
| `messages-received`       | 받은 총 메시지 수       |

### <a name="observe-metrics"></a>메트릭 관찰

[dotnet-카운터](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) 는 임시 상태 모니터링 및 첫 번째 수준의 성능 조사를 위한 성능 모니터링 도구입니다. 공급자 이름으로 `Grpc.AspNetCore.Server` 또는 `Grpc.Net.Client`를 사용 하 여 .NET 앱을 모니터링 합니다.

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

GRPC 메트릭을 관찰 하는 또 다른 방법은 Application Insights의 [Microsoft ApplicationInsights. EventCounterCollector 패키지](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)를 사용 하 여 카운터 데이터를 캡처하는 것입니다. 설치가 완료 되 면 Application Insights는 런타임에 일반적인 .NET 카운터를 수집 합니다. gRPC의 카운터는 기본적으로 수집 되지 않지만 [추가 카운터를 포함 하도록](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)앱 정보를 사용자 지정할 수 있습니다.

*Startup.cs*에서 수집할 응용 프로그램 정보에 대 한 grpc 카운터를 지정 합니다.

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

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
