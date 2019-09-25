---
title: .NET의 gRPC에서 로깅 및 진단
author: jamesnk
description: .NET의 gRPC 앱에서 진단을 수집 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 7194e91b40a08c4a7ee619b8f207900af2683aa1
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250731"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>.NET의 gRPC에서 로깅 및 진단

별 [뉴턴-킹](https://twitter.com/jamesnk)

이 문서에서는 문제 해결에 도움이 되도록 gRPC 앱에서 진단 정보를 수집 하기 위한 지침을 제공 합니다.

## <a name="grpc-services-logging"></a>gRPC 서비스 로깅

> [!WARNING]
> 서버 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다. 프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .

GRPC 서비스는 ASP.NET Core에서 호스트 되므로 ASP.NET Core 로깅 시스템을 사용 합니다. 기본 구성에서 gRPC는 매우 적은 정보를 기록 하지만이를 구성할 수 있습니다. ASP.NET Core 로깅 구성에 대 한 자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index#configuration) 에 대 한 설명서를 참조 하세요.

grpc는 범주 아래에 `Grpc` 로그를 추가 합니다. Grpc에서 자세한 로그를 사용 하도록 설정 하려면 `Grpc` 의 `LogLevel` `Logging`하위 섹션 `Debug` 에 다음 항목을 추가 하 여 *appsettings* 파일의 수준에 대 한 접두사를 구성 합니다.

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

다음을 사용 하 여 `ConfigureLogging` *Startup.cs* 에서 구성할 수도 있습니다.

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

JSON 기반 구성을 사용 하지 않는 경우 구성 시스템에서 다음 구성 값을 설정 합니다.

* `Logging:LogLevel:Grpc` = `Debug`

구성 시스템에 대 한 설명서를 확인 하 여 중첩 된 구성 값을 지정 하는 방법을 확인 합니다. 예를 들어 환경 변수를 사용 하는 `_` 경우 대신 `:` 두 개의 문자를 사용 합니다 (예 `Logging__LogLevel__Grpc`:).

앱에 대 한 `Debug` 자세한 진단을 수집할 때 수준을 사용 하는 것이 좋습니다. 이 `Trace` 수준은 매우 낮은 수준의 진단을 생성 하며 앱에서 문제를 진단 하는 데 거의 필요 하지 않습니다.

### <a name="sample-logging-output"></a>샘플 로깅 출력

Grpc 서비스 `Debug` 수준에서 콘솔 출력의 예는 다음과 같습니다.

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

## <a name="access-server-side-logs"></a>서버 쪽 로그 액세스

서버 쪽 로그에 액세스 하는 방법은를 실행 하는 환경에 따라 달라 집니다.

### <a name="as-a-console-app"></a>콘솔 앱으로

콘솔 응용 프로그램에서 실행 하는 경우 [콘솔로 거](xref:fundamentals/logging/index#console-provider) 를 기본적으로 사용 하도록 설정 해야 합니다. gRPC 로그가 콘솔에 표시 됩니다.

### <a name="other-environments"></a>기타 환경

앱을 다른 환경 (예: Docker, Kubernetes 또는 Windows 서비스)에 배포 하는 경우 환경에 적합 <xref:fundamentals/logging/index> 한 로깅 공급자를 구성 하는 방법에 대 한 자세한 내용은을 참조 하세요.

## <a name="grpc-client-logging"></a>gRPC 클라이언트 로깅

> [!WARNING]
> 클라이언트 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다. 프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .

.Net 클라이언트에서 로그를 가져오기 위해 클라이언트의 채널을 만들 `GrpcChannelOptions.LoggerFactory` 때 속성을 설정할 수 있습니다. ASP.NET Core 앱에서 gRPC 서비스를 호출 하는 경우에는 DI (종속성 주입)에서로 거 팩터리를 확인할 수 있습니다.

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

클라이언트 로깅을 사용 하도록 설정 하는 다른 방법은 [Grpc 클라이언트 팩터리](xref:grpc/clientfactory) 를 사용 하 여 클라이언트를 만드는 것입니다. 클라이언트 팩터리에 등록 되 고 DI에서 확인 된 gRPC 클라이언트는 자동으로 앱의 구성 된 로깅을 사용 합니다.

앱에서 DI를 사용 하지 않는 경우 [server.loggerfactory](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)를 사용 하 `ILoggerFactory` 여 새 인스턴스를 만들 수 있습니다. 이 메서드에 액세스 하려면 앱에 [Microsoft Extensions.](https://www.nuget.org/packages/microsoft.extensions.logging/) i n i 패키지를 추가 합니다.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

### <a name="sample-logging-output"></a>샘플 로깅 출력

다음은 grpc 클라이언트 `Debug` 수준에 있는 콘솔 출력의 예입니다.

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

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
