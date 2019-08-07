---
title: ASP.NET Core SignalR 구성
author: bradygaster
description: ASP.NET Core SignalR 앱을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 4706a1e2774fa9f6fb40085da944e8a82476ef05
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820039"
---
# <a name="aspnet-core-signalr-configuration"></a>ASP.NET Core SignalR 구성

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack serialization 옵션

ASP.NET Core SignalR는 메시지 인코딩에 대 한 두 가지 프로토콜을 지원 합니다. [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html). 각 프로토콜에는 serialization 구성 옵션이 있습니다.

JSON serialization을 사용 하 여 서버에 구성할 수 있습니다 합니다 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 후 추가할 수 있는 확장 메서드 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 에서 프로그램 `Startup.ConfigureServices` 메서드. `AddJsonProtocol` 메서드는 `options` 개체를 전달받는 대리자를 받습니다. 이 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 JSON.NET의 `JsonSerializerSettings` 개체로, 인수 및 반환 값의 직렬화를 구성하는 데 사용할 수 있습니다. 더 자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참고하시기 바랍니다.

예를 들어 기본 값인 "camelCase" 속성 이름 대신 "PascalCase" 속성 이름을 사용하도록 직렬화 변환기를 구성하려면 다음 코드를 사용합니다.

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

.NET 클라이언트에서는 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 `AddJsonProtocol` 확장 메서드가 존재합니다. 이 확장 메서드를 확인하려면 `Microsoft.Extensions.DependencyInjection` 네임스페이스를 임포트해야 합니다.

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> 현재 JavaScript 클라이언트에서는 JSON 직렬화를 구성할 수 없습니다.

### <a name="messagepack-serialization-options"></a>MessagePack serialization 옵션

MessagePack 직렬화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공하여 구성할 수 있습니다. 더 자세한 내용은 [SignalR의 MessagePack](xref:signalr/messagepackhubprotocol)을 참고하시기 바랍니다.

> [!NOTE]
> 현재 JavaScript 클라이언트에서는 MessagePack 직렬화를 구성할 수 없습니다.

## <a name="configure-server-options"></a>서버 옵션 구성

다음 표는 SignalR 허브를 구성하기 위한 옵션을 설명합니다.

::: moniker range=">= aspnetcore-3.0"

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30초 | 서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다. 이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다. 권장 값은 `KeepAliveInterval` 값 두 배가 됩니다.|
| `HandshakeTimeout` | 15초 | 클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다. 이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다. |
| `KeepAliveInterval` | 15초 | 서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다. `KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오. 권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.  |
| `SupportedProtocols` | 설치된 모든 프로토콜 | 허브가 지원하는 프로토콜입니다. 기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다. |
| `EnableDetailedErrors` | `false` | 이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다. 예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다. |
| `StreamBufferCapacity` | `10` | 클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다. 이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.|

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30초 | 서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다. 이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다. 권장 값은 `KeepAliveInterval` 값 두 배가 됩니다.|
| `HandshakeTimeout` | 15초 | 클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다. 이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다. |
| `KeepAliveInterval` | 15초 | 서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다. `KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오. 권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.  |
| `SupportedProtocols` | 설치된 모든 프로토콜 | 허브가 지원하는 프로토콜입니다. 기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다. |
| `EnableDetailedErrors` | `false` | 이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다. 예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다. |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15초 | 클라이언트가 이 시간 제한 내에 초기 핸드셰이크 메시지를 전송하지 않으면 연결이 닫힙니다. 이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다. |
| `KeepAliveInterval` | 15초 | 서버가 이 간격 내에 메시지를 전송하지 않으면 자동으로 ping 메시지가 전송되어 연결이 열린 상태로 유지됩니다. `KeepAliveInterval`을 변경할 경우 클라이언트의 `ServerTimeout`/`serverTimeoutInMilliseconds` 설정도 변경하십시오. 권장되는 `ServerTimeout`/`serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값의 두 배입니다.  |
| `SupportedProtocols` | 설치된 모든 프로토콜 | 허브가 지원하는 프로토콜입니다. 기본적으로 서버에 등록된 모든 프로토콜이 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대해 특정 프로토콜을 비활성화시킬 수 있습니다. |
| `EnableDetailedErrors` | `false` | 이 값이 `true`면 Hub 메서드에서 예외가 발생할 경우 자세한 예외 메시지가 클라이언트로 반환됩니다. 예외 메시지에는 민감한 정보가 포함되어 있을 수 있으므로 기본값은 `false`입니다. |

::: moniker-end

`Startup.ConfigureServices`에서 `AddSignalR` 호출에 옵션 대리자를 제공하여 모든 허브에 대한 옵션을 구성할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

단일 허브에 대 한 옵션은에서 `AddSignalR` 제공 되는 전역 옵션을 재정의 하 고 다음을 사용 하 여 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>구성할 수 있습니다.

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>고급 HTTP 구성 옵션

전송 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하려면 `HttpConnectionDispatcherOptions`를 사용합니다. 이러한 옵션은의 `Startup.Configure` [maphub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 합니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | 클라이언트로부터 수신하는 서버 버퍼의 최대 바이트 수입니다. 이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다. |
| `AuthorizationData` | 허브 클래스에 적용된 `Authorize` 특성에서 자동으로 수집된 데이터입니다. | 클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다. |
| `TransportMaxBufferSize` | 32 KB | 앱에서 전송하는 서버 버퍼의 최대 바이트 수입니다. 이 값을 늘리면 서버가 더 큰 메시지를 전송할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다. |
| `Transports` | 모든 전송을 사용할 수 있습니다. | 클라이언트가 연결에 사용할 수 있는 전송을 제한할 수 있는 `HttpTransportType` 값들의 비트 마스크입니다. |
| `LongPolling` | 아래 내용을 참조하세요. | 롱 폴링 전송과 관련된 추가 옵션입니다. |
| `WebSockets` | 아래 내용을 참조하세요. | WebSockets 전송과 관련된 추가 옵션입니다. |

롱 폴링 전송에는 `LongPolling` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90초 | 단일 폴링 요청을 종료하기 전에 서버가 메시지를 클라이언트에 전송할 때까지 대기하는 최대 시간입니다. 이 값을 줄이면 클라이언트는 더 자주 새 폴링 요청을 발행합니다. |

WebSocket 전송에는 `WebSockets` 속성을 이용해서 구성할 수 있는 추가적인 옵션이 존재합니다.

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5초 | 서버가 닫힌 후 클라이언트가 이 시간 제한 내에 닫기에 실패하면 연결이 종료됩니다. |
| `SubProtocolSelector` | `null` | 사용자 지정 값으로 `Sec-WebSocket-Protocol` 헤더를 설정하기 위해 사용할 수 있는 대리자입니다. 이 대리자는 클라이언트에서 요청한 값을 입력으로 받아서 원하는 값을 반환해야 합니다. |

## <a name="configure-client-options"></a>클라이언트 옵션 구성

클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다. Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 `HubConnection` 뿐만 아니라 자체도 포함 됩니다.

### <a name="configure-logging"></a>로깅 구성

.NET 클라이언트에서 로깅은 `ConfigureLogging` 메서드를 이용해서 구성됩니다. 로깅 공급자 및 필터는 서버와 동일한 방법으로 등록할 수 있습니다. 자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index) 설명서를 참고하시기 바랍니다.

> [!NOTE]
> 로깅 공급자를 등록하는 데 필요한 패키지를 설치해야 합니다. 전체 목록은 로깅 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참고하시기 바랍니다.

예를 들어 콘솔 로깅을 사용하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치합니다. `AddConsole` 확장 메서드를 호출합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript 클라이언트에는 유사한 `configureLogging` 메서드가 존재합니다. 기록할 로그 메시지의 최소 수준을 나타내는 `LogLevel` 값을 지정합니다. 로그는 브라우저 콘솔 창에 기록됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

값 대신 로그 수준 이름을 나타내는 값을 `string` 제공할 수도 있습니다. `LogLevel` 이는 `LogLevel` 상수에 대 한 액세스 권한이 없는 환경에서 SignalR 로깅을 구성할 때 유용 합니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

다음 표에서는 사용 가능한 로그 수준을 나열 합니다. 사용자가 지정 `configureLogging` 하는 값은 로깅할 **최소** 로그 수준을 설정 합니다. 이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.

| 문자열                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info` **or** `information` | `LogLevel.Information` |
| `warn` **or** `warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> 로깅을 완전히 비활성화시키려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`을 지정하십시오.

로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.

SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다. 이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다. 다음 코드 조각에서는 SignalR Java 클라이언트와 `java.util.logging` 함께를 사용 하는 방법을 보여 줍니다.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

이는 무시 해도 됩니다.

### <a name="configure-allowed-transports"></a>허용되는 전송 구성

SignalR에서 사용되는 전송은 `WithUrl` 호출에서 (JavaScript에서는 `withUrl` 호출에서) 구성할 수 있습니다. `HttpTransportType` 값들의 비트 OR을 이용해서 클라이언트가 지정한 전송만 사용하도록 제한할 수 있습니다. 기본적으로 모든 전송은 활성화되어 있습니다.

예를 들어 다음 코드는 서버-전송 이벤트 전송을 비활성화시키고 WebSocket 및 롱 폴링 연결만 허용합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript 클라이언트에서 전송은 `withUrl`에 제공되는 옵션 개체의 `transport` 필드를 설정하여 구성합니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Java 클라이언트에서의 `withTransport` 메서드 `HttpHubConnectionBuilder`를 사용 하 여 전송이 선택 됩니다. Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalR Java 클라이언트는 아직 전송 대체를 지원 하지 않습니다.

::: moniker-end

### <a name="configure-bearer-authentication"></a>전달자 인증 구성

SignalR 요청과 함께 인증 데이터를 제공하려면 `AccessTokenProvider` 옵션(JavaScript에서는 `accessTokenFactory`를)을 이용해서 원하는 액세스 토큰을 반환하는 함수를 지정합니다. .NET 클라이언트에서는 액세스 토큰이 HTTP "Bearer Authentication" 토큰으로 전달됩니다(`Bearer` 형식의 `Authorization` 헤더를 통해). JavaScript 클라이언트에서는 브라우저의 API가 헤더 적용 기능을 제한하는 몇 가지 경우를 **제외**하면 (특히 서버-전송 이벤트 및 WebSockets 요청) 액세스 토큰이 전달자 토큰으로 사용됩니다. 이런 경우 액세스 토큰은 `access_token`이라는 쿼리 문자열 값으로 제공됩니다.

.NET 클라이언트에서 `AccessTokenProvider` 옵션은 `WithUrl`에서 옵션 대리자를 이용해서 지정할 수 있습니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript 클라이언트에서는 `withUrl`에서 옵션 개체의 `accessTokenFactory` 필드를 설정하여 액세스 토큰을 구성합니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다. [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [단일\<문자열 >](https://reactivex.io/documentation/single.html)를 제공 합니다. [단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>시간 제한 및 연결 유지 옵션 구성

시간 제한 및 연결 유지 동작을 구성하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30초(30000밀리초) | 서버 활동 시간 제한입니다. 서버가 이 시간 제한 내에 메시지를 전송하지 않으면 클라이언트는 서버 연결이 끊어졌다고 간주하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를  트리거합니다. 이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다. 권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다. |
| `HandshakeTimeout` | 15초 | 초기 서버 핸드셰이크에 대한 시간 제한입니다. 서버가 이 시간 제한 내에 핸드셰이크 응답을 전송하지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를 트리거합니다. 이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다. |
| `KeepAliveInterval` | 15초 | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정됩니다.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30초(30000밀리초) | 서버 활동 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onclose` 트리거합니다. 이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다. 권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다. |
| `keepAliveIntervalInMilliseconds` | 15 초 (15000 밀리초) | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

# <a name="javatabjava"></a>[Java](#tab/java)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30초(30000밀리초) | 서버 활동 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onClose` 트리거합니다. 이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다. 권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다. |
| `withHandshakeResponseTimeout` | 15초 | 초기 서버 핸드셰이크에 대한 시간 제한입니다. 서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 `onClose` 트리거합니다. 이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다. |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 초 (15000 밀리초) | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30초(30000밀리초) | 서버 활동 시간 제한입니다. 서버가 이 시간 제한 내에 메시지를 전송하지 않으면 클라이언트는 서버 연결이 끊어졌다고 간주하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를  트리거합니다. 이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다. 권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다. |
| `HandshakeTimeout` | 15초 | 초기 서버 핸드셰이크에 대한 시간 제한입니다. 서버가 이 시간 제한 내에 핸드셰이크 응답을 전송하지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` 이벤트(JavaScript에서는 `onclose` 이벤트)를 트리거합니다. 이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다. |

.NET 클라이언트에서 시간 제한 값은 `TimeSpan` 값으로 지정됩니다.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30초(30000밀리초) | 서버 활동 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onclose` 트리거합니다. 이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다. 권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다. |

# <a name="javatabjava"></a>[Java](#tab/java)

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30초(30000밀리초) | 서버 활동 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onClose` 트리거합니다. 이 값이 충분히 커야만 ping 메시지가 서버에서 전송**되고** 시간 제한 내에 클라이언트에 의해 수신될 수 있습니다. 권장되는 값은 ping이 도착할 시간을 확보하기 위해 최소한 서버의 `KeepAliveInterval` 값의 두 배가 되는 숫자여야 합니다. |
| `withHandshakeResponseTimeout` | 15초 | 초기 서버 핸드셰이크에 대한 시간 제한입니다. 서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 `onClose` 트리거합니다. 이 설정은 심각한 네트워크 지연으로 인해 핸드셰이크 시간 제한 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 보다 자세한 내용은 [SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참고하시기 바랍니다. |

::: moniker-end

---

### <a name="configure-additional-options"></a>추가 옵션 구성

Java 클라이언트 `WithUrl` `HttpHubConnectionBuilder` 의에 있는 다양 한 구성`withUrl` api에서 또는의 `HubConnectionBuilder` (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| .NET 옵션 |  기본값 | 설명 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다. |
| `SkipNegotiation` | `false` | 이 값을 `true`로 설정하면 협상 단계를 건너뜁니다. **WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.** Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다. |
| `ClientCertificates` | Empty | 요청을 인증하기 위해 전송할 TLS 인증서의 컬렉션입니다. |
| `Cookies` | Empty | 모든 HTTP 요청과 함께 전송할 HTTP 쿠키의 컬렉션입니다. |
| `Credentials` | Empty | 모든 HTTP 요청과 함께 전송할 자격 증명입니다. |
| `CloseTimeout` | 5초 | Websocket에만 해당됩니다. 클라이언트가 서버를 닫은 후 닫기 요청을 확인하기 위해 대기하는 최대 시간입니다. 서버가 이 시간 내에 종료를 승인하지 않으면 클라이언트가 연결을 끊습니다. |
| `Headers` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |
| `HttpMessageHandlerFactory` | `null` | HTTP 요청을 전송하기 위해 사용되는 `HttpMessageHandler`를 구성하거나 대체하는 데 사용할 수 있는 대리자입니다. WebSocket 연결에는 사용되지 않습니다. 이 대리자는 null이 아닌 값을 반환해야 하며 매개 변수로 기본값을 받습니다. 이 기본값의 설정을 수정하여 반환하거나 새로운 `HttpMessageHandler` 인스턴스를 반환합니다. **처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.** |
| `Proxy` | `null` | HTTP 요청을 전송할 때 사용할 HTTP 프록시입니다. |
| `UseDefaultCredentials` | `false` | HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 전송하려면 이 부울 값을 설정합니다. 그러면 Windows 인증을 사용할 수 있습니다. |
| `WebSocketConfiguration` | `null` | 추가적인 WebSocket 옵션을 구성할 수 있는 대리자입니다. 옵션을 구성에 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)의 인스턴스를 전달받습니다. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| JavaScript 옵션 | 기본값 | 설명 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다. |
| `skipNegotiation` | `false` | 이 값을 `true`로 설정하면 협상 단계를 건너뜁니다. **WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.** Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Java 옵션 | 기본값 | 설명 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공된 문자열을 반환하는 함수입니다. |
| `shouldSkipNegotiate` | `false` | 이 값을 `true`로 설정하면 협상 단계를 건너뜁니다. **WebSockets 전송이 유일하게 활성화된 전송인 경우에만 지원됩니다.** Azure SignalR Service를 사용하는 경우에는 이 설정을 사용할 수 없습니다. |
| `withHeader` `withHeaders` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |

---

.NET 클라이언트에서 이 옵션들은 `WithUrl`에 제공되는 옵션 대리자를 이용해서 수정할 수 있습니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript 클라이언트에서 이 옵션들은 `withUrl`에 제공되는 JavaScript 개체를 통해서 제공될 수 있습니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java 클라이언트에서 다음에서 `HttpHubConnectionBuilder` 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
