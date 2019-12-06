---
title: SignalR와 ASP.NET Core의 차이점 SignalR
author: bradygaster
description: SignalR와 ASP.NET Core의 차이점 SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: cca9a0cb0c46fc25eb5d1f7127d31fd3ab92f0b4
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880357"
---
# <a name="differences-between-aspnet-opno-locsignalr-and-aspnet-core-opno-locsignalr"></a>ASP.NET SignalR와 ASP.NET Core 간의 차이점 SignalR

ASP.NET Core SignalR는 ASP.NET SignalR에 대 한 클라이언트 또는 서버와 호환 되지 않습니다. 이 문서에서는 ASP.NET Core SignalR에서 제거 되거나 변경 된 기능에 대해 자세히 설명 합니다.

## <a name="how-to-identify-the-opno-locsignalr-version"></a>SignalR 버전을 확인 하는 방법

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| 서버 NuGet 패키지 | [MicrosoftSignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | 없음. [AspNetCore](xref:fundamentals/metapackage-app) 공유 프레임 워크에 포함 되어 있습니다. |
| 클라이언트 NuGet 패키지 | [SignalR. 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[SignalR. .JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [AspNetCoreSignalR. 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| JavaScript client npm 패키지 | [signalr](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| Java 클라이언트 | [GitHub 리포지토리](https://github.com/SignalR/java-client) (사용 되지 않음)  | Maven package [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| 서버 앱 유형 | ASP.NET (System.web) 또는 OWIN 자체 호스트 | ASP.NET Core |
| 지원 되는 서버 플랫폼 | .NET Framework 4.5 이상 | .NET Core 3.0 이상 |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| 서버 NuGet 패키지 | [MicrosoftSignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)<br>[AspNetCoreSignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| 클라이언트 NuGet 패키지 | [SignalR. 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[SignalR. .JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [AspNetCoreSignalR. 클라이언트로](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| JavaScript client npm 패키지 | [signalr](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| Java 클라이언트 | [GitHub 리포지토리](https://github.com/SignalR/java-client) (사용 되지 않음)  | Maven package [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| 서버 앱 유형 | ASP.NET (System.web) 또는 OWIN 자체 호스트 | ASP.NET Core |
| 지원 되는 서버 플랫폼 | .NET Framework 4.5 이상 | .NET Framework 4.6.1 이상<br>.NET Core 2.1 이상 |

::: moniker-end

## <a name="feature-differences"></a>기능상의 차이점

### <a name="automatic-reconnects"></a>자동 다시 연결

::: moniker range=">= aspnetcore-3.0"

ASP.NET SignalR에서 다음을 수행 합니다.

* 기본적으로 SignalR 연결을 삭제 하면 서버에 다시 연결을 시도 합니다. 

ASP.NET Core SignalR:

* 자동 다시 연결은 [.net 클라이언트](xref:signalr/dotnet-client#automatically-reconnect) 와 [JavaScript 클라이언트](xref:signalr/javascript-client#automatically-reconnect)모두에서 옵트인 (opt in) 됩니다.

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 3.0 이전에는 SignalR 자동 다시 연결을 지원 하지 않습니다. 클라이언트의 연결이 끊어지면 사용자는 새 연결을 명시적으로 시작 하 여 다시 연결 해야 합니다. ASP.NET SignalR에서 SignalR 연결을 삭제 하면 서버에 다시 연결을 시도 합니다.

::: moniker-end

### <a name="protocol-support"></a>프로토콜 지원

ASP.NET Core SignalR는 JSON을 지원 하 고 [MessagePack](xref:signalr/messagepackhubprotocol)을 기반으로 하는 새 이진 프로토콜도 지원 합니다. 또한 사용자 지정 프로토콜을 만들 수도 있습니다.

### <a name="transports"></a>전송

ASP.NET Core SignalR에서는 무한 프레임 전송이 지원 되지 않습니다.

## <a name="differences-on-the-server"></a>서버의 차이점

ASP.NET Core SignalR 서버 쪽 라이브러리는 Razor 및 MVC 프로젝트에 대 한 **ASP.NET Core 웹 응용 프로그램** 템플릿에 사용 되는 [AspNetCore](xref:fundamentals/metapackage-app)에 포함 되어 있습니다.

ASP.NET Core SignalR ASP.NET Core 미들웨어입니다. `Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A>를 호출 하 여 구성 해야 합니다.

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

라우팅을 구성 하려면 `Startup.Configure` 메서드의 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 메서드 호출 내에서 허브에 경로를 매핑합니다.

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

라우팅을 구성 하려면 `Startup.Configure` 메서드의 <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> 메서드 호출 내에서 허브에 경로를 매핑합니다.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>고정 세션

ASP.NET SignalR의 확장 모델을 사용 하면 클라이언트가 팜의 모든 서버에 다시 연결 하 여 메시지를 보낼 수 있습니다. ASP.NET Core SignalR에서 클라이언트는 연결 기간 동안 동일한 서버와 상호 작용 해야 합니다. Redis를 사용 하는 확장의 경우에는 고정 세션이 필요 합니다. [Azure SignalR 서비스](/azure/azure-signalr/)를 사용 하는 확장의 경우 서비스에서 클라이언트에 대 한 연결을 처리 하기 때문에 고정 세션이 필요 하지 않습니다.

### <a name="single-hub-per-connection"></a>연결당 단일 허브

ASP.NET Core SignalR에서는 연결 모델이 간소화 되었습니다. 단일 연결이 여러 허브에 대한 접근을 공유하기 위해 사용되기 보다는 단일 허브에 직접 연결됩니다.

### <a name="streaming"></a>Streaming

이제 ASP.NET Core SignalR 허브에서 클라이언트로의 [스트리밍 데이터](xref:signalr/streaming) 를 지원 합니다.

### <a name="state"></a>State

클라이언트와 허브 (`HubState`라고도 함) 간에 임의의 상태를 전달 하는 기능은 제거 되었으며 진행 메시지를 지원 합니다. 현재 허브 프록시에 해당하는 기능은 존재하지 않습니다.

### <a name="persistentconnection-removal"></a>PersistentConnection 제거

ASP.NET Core SignalR에서는 [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) 클래스가 제거 되었습니다.

### <a name="globalhost"></a>GlobalHost

ASP.NET Core에는 프레임 워크에 기본 제공 되는 DI (종속성 주입)가 있습니다. 서비스는 DI를 사용 하 여 [HubContext](xref:signalr/hubcontext)에 액세스할 수 있습니다. ASP.NET SignalR에서 `HubContext`을 가져오는 데 사용 되는 `GlobalHost` 개체는 ASP.NET Core SignalR에 존재 하지 않습니다.

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR는 `HubPipeline` 모듈을 지원 하지 않습니다.

## <a name="differences-on-the-client"></a>클라이언트의 차이점

### <a name="typescript"></a>TypeScript

ASP.NET Core SignalR 클라이언트는 [TypeScript](https://www.typescriptlang.org/)로 작성 됩니다. [JavaScript 클라이언트](xref:signalr/javascript-client)를 사용할 때 JavaScript 또는 TypeScript로 작성할 수 있습니다.

### <a name="the-javascript-client-is-hosted-at-npm"></a>JavaScript 클라이언트는 npm에서 호스팅됩니다.

::: moniker range=">= aspnetcore-3.0"

ASP.NET 버전에서는 Visual Studio의 NuGet 패키지를 통해 JavaScript 클라이언트를 가져왔습니다. ASP.NET Core 버전의 [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm 패키지에는 JavaScript 라이브러리가 포함 되어 있습니다. 이 패키지는 **ASP.NET Core 웹 응용 프로그램** 템플릿에 포함되어 있지 않습니다. npm을 사용하여 `@microsoft/signalr` npm 패키지를 가져와서 설치합니다.

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

ASP.NET 버전에서는 Visual Studio의 NuGet 패키지를 통해 JavaScript 클라이언트를 가져왔습니다. ASP.NET Core 버전의 [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm 패키지에는 JavaScript 라이브러리가 포함 되어 있습니다. 이 패키지는 **ASP.NET Core 웹 응용 프로그램** 템플릿에 포함되어 있지 않습니다. npm을 사용하여 `@aspnet/signalr` npm 패키지를 가져와서 설치합니다.

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

jQuery에 대한 종속성은 제거되었지만 프로젝트에서 여전히 jQuery를 사용할 수는 있습니다.

### <a name="internet-explorer-support"></a>Internet Explorer 지원

ASP.NET Core SignalR를 사용 하려면 Microsoft internet explorer 11 이상이 필요 합니다 (ASP.NET SignalR Microsoft Internet Explorer 8 이상이 지원 됨).

### <a name="javascript-client-method-syntax"></a>JavaScript 클라이언트 메서드 구문

::: moniker range=">= aspnetcore-3.0"

JavaScript 구문이 SignalRASP.NET 버전에서 변경 되었습니다. `$connection` 개체를 사용하는 대신 [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API를 사용해서 연결을 만듭니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

[on](/javascript/api/@microsoft/signalr/HubConnection#on) 메서드를 사용해서 허브가 호출할 수 있는 클라이언트 메서드를 지정합니다.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

JavaScript 구문이 SignalRASP.NET 버전에서 변경 되었습니다. `$connection` 개체를 사용하는 대신 [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API를 사용해서 연결을 만듭니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

[on](/javascript/api/@aspnet/signalr/HubConnection#on) 메서드를 사용해서 허브가 호출할 수 있는 클라이언트 메서드를 지정합니다.

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

클라이언트 메서드를 생성한 다음, 허브 연결을 시작합니다. 오류를 기록하거나 처리하려면 [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 메서드를 연결합니다.

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>허브 프록시

::: moniker range=">= aspnetcore-3.0"

더 이상 허브 프록시가 자동으로 생성되지 않습니다. 대신 메서드 이름은 [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API에 문자열로 전달됩니다.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

더 이상 허브 프록시가 자동으로 생성되지 않습니다. 대신 메서드 이름은 [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API에 문자열로 전달됩니다.

::: moniker-end

### <a name="net-and-other-clients"></a>.NET 및 기타 클라이언트

[AspNetCoreSignalR입니다. 클라이언트](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet 패키지에는 ASP.NET Core SignalR를 위한 .net 클라이언트 라이브러리가 포함 되어 있습니다.

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>를 사용 하 여 허브에 대 한 연결 인스턴스를 만들고 빌드합니다.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>스케일 아웃 차이점

ASP.NET SignalR는 SQL Server 및 Redis를 지원 합니다. ASP.NET Core SignalR는 Azure SignalR 서비스 및 Redis을 지원 합니다.

### <a name="aspnet"></a>ASP.NET

* [Azure Service Bus를 사용 하 여 확장 SignalR](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [Redis를 사용 하 여 확장 SignalR](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SQL Server를 사용 하 여 확장 SignalR](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Azure SignalR 서비스](/azure/azure-signalr/)
* [Redis 백플레인](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>추가 자료

* [허브](xref:signalr/hubs)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [지원되는 플랫폼](xref:signalr/supported-platforms)
