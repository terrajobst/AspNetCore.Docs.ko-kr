---
title: SignalR과 ASP.NET Core SignalR의 차이점
author: bradygaster
description: SignalR과 ASP.NET Core SignalR의 차이점
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/14/2018
uid: signalr/version-differences
ms.openlocfilehash: 70b09493d9b4c96c897465d60e53e93a793c42f9
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746545"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>ASP.NET SignalR과 ASP.NET Core SignalR의 차이점

ASP.NET Core SignalR은 ASP.NET SignalR용 클라이언트 또는 서버와 호환되지 않습니다. 이 문서에서는 ASP.NET Core SignalR에서 제거되거나 변경된 기능을 자세히 설명합니다.

## <a name="how-to-identify-the-signalr-version"></a>SignalR 버전 식별 방법

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| 서버 NuGet 패키지 | [Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)<br>[AspNetCore. SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| 클라이언트 NuGet 패키지 | [Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| 클라이언트 npm 패키지 | [signalr](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| Java 클라이언트 | [GitHub 리포지토리](https://github.com/SignalR/java-client) mapi  | Maven package [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| 서버 앱 유형 | ASP.NET (System.web) 또는 OWIN 자체 호스트 | ASP.NET Core |
| 지원되는 서버 플랫폼 | .NET Framework 4.5 이상 | .NET Framework 4.6.1 이상<br>.NET Core 2.1 이상 |

## <a name="feature-differences"></a>기능상의 차이점

### <a name="automatic-reconnects"></a>자동 다시 연결

ASP.NET Core SignalR은 자동 재연결을 지원하지 않습니다. 클라이언트의 연결이 끊어진 경우 재연결하려면 사용자가 명시적으로 새로운 연결을 시작해야 합니다. ASP.NET SignalR은 연결이 끊어지면 SignalR이 서버에 재연결하려고 시도합니다.

### <a name="protocol-support"></a>프로토콜 지원

ASP.NET Core SignalR은 JSON뿐만 아니라 [MessagePack](xref:signalr/messagepackhubprotocol) 기반의 새로운 이진 프로토콜을 지원합니다. 또한 사용자 지정 프로토콜을 만들 수도 있습니다.

### <a name="transports"></a>전송

ASP.NET Core SignalR에서는 무한 프레임 전송이 지원 되지 않습니다.

## <a name="differences-on-the-server"></a>서버의 차이점

ASP.NET Core SignalR의 서버 쪽 라이브러리는 Razor 및 MVC 프로젝트를 위한 **ASP.NET Core 웹 응용 프로그램** 템플릿의 일부인 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app) 패키지에 포함되어 있습니다.

ASP.NET Core SignalR은 ASP.NET Core 미들웨어이므로 `Startup.ConfigureServices`에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)을 호출하여 구성해야 합니다.

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

라우팅을 구성 하려면 `Startup.Configure` 메서드의 [useendpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) 메서드 호출 내에서 허브에 경로를 매핑합니다.


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

라우팅을 구성하려면 `Startup.Configure` 메서드에서 [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) 메서드 호출 내부에서 허브에 경로를 매핑합니다.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>고정 세션

ASP.NET SignalR에 대 한 확장 모델을 사용 하면 클라이언트가 팜의 모든 서버에 다시 연결 하 여 메시지를 보낼 수 있습니다. ASP.NET Core SignalR에서 클라이언트는 연결 기간 동안 동일한 서버와 상호 작용 해야 합니다. Redis를 사용 하는 확장의 경우에는 고정 세션이 필요 합니다. [Azure SignalR service](/azure/azure-signalr/)를 사용 하는 확장의 경우 서비스에서 클라이언트에 대 한 연결을 처리 하기 때문에 고정 세션이 필요 하지 않습니다.

### <a name="single-hub-per-connection"></a>연결당 단일 허브

ASP.NET Core SignalR에서는 연결 모델이 단순화되었습니다. 단일 연결이 여러 허브에 대한 접근을 공유하기 위해 사용되기 보다는 단일 허브에 직접 연결됩니다.

### <a name="streaming"></a>스트리밍

이제 ASP.NET Core SignalR은 허브에서 클라이언트로의 [스트리밍 데이터](xref:signalr/streaming)를 지원합니다.

### <a name="state"></a>상태

진행 메시지 관련 기능뿐만 아니라 클라이언트와 허브 간에 임의의 상태를 전달할 수 있는 기능(HubState라고도 함)이 제거되었습니다. 현재 허브 프록시에 해당하는 기능은 존재하지 않습니다.

### <a name="persistentconnection-removal"></a>PersistentConnection 제거

ASP.NET Core SignalR에서 [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) 클래스가 제거 되었습니다.

### <a name="globalhost"></a>GlobalHost

ASP.NET Core에는 프레임 워크에 기본 제공 되는 DI (종속성 주입)가 있습니다. 서비스는 DI를 사용 하 여 [HubContext](xref:signalr/hubcontext)에 액세스할 수 있습니다. ASP.NET `GlobalHost` SignalR`HubContext` 에 사용 되는 개체는 ASP.NET Core SignalR에 존재 하지 않습니다.

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR는 모듈을 `HubPipeline` 지원 하지 않습니다.

## <a name="differences-on-the-client"></a>클라이언트의 차이점

### <a name="typescript"></a>TypeScript

ASP.NET Core SignalR 클라이언트는 [TypeScript](https://www.typescriptlang.org/)로 작성되었습니다. [JavaScript 클라이언트](xref:signalr/javascript-client)를 사용할 때 JavaScript 또는 TypeScript로 작성할 수 있습니다.

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a>JavaScript 클라이언트가 [npm](https://www.npmjs.com/)에서 호스팅됩니다.

이전 버전에서는 Visual Studio의 NuGet 패키지를 통해서 JavaScript 클라이언트를 가져왔습니다. Core 버전에서는 [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm 패키지에 JavaScript 라이브러리가 포함되어 있습니다. 이 패키지는 **ASP.NET Core 웹 응용 프로그램** 템플릿에 포함되어 있지 않습니다. npm을 사용하여 `@aspnet/signalr` npm 패키지를 가져와서 설치합니다.

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a>jQuery

jQuery에 대한 종속성은 제거되었지만 프로젝트에서 여전히 jQuery를 사용할 수는 있습니다.

### <a name="internet-explorer-support"></a>Internet Explorer 지원

ASP.NET Core SignalR에는 Microsoft Internet explorer 11 이상이 필요 합니다 (ASP.NET SignalR에서 지원 되는 Microsoft Internet Explorer 8 이상).

### <a name="javascript-client-method-syntax"></a>JavaScript 클라이언트 메서드 구문

JavaScript 구문이 이전 버전의 SignalR과 달라졌습니다. `$connection` 개체를 사용하는 대신 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API를 사용해서 연결을 만듭니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

[on](/javascript/api/@aspnet/signalr/HubConnection#on) 메서드를 사용해서 허브가 호출할 수 있는 클라이언트 메서드를 지정합니다.

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

클라이언트 메서드를 생성한 다음, 허브 연결을 시작합니다. 오류를 기록하거나 처리하려면 [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 메서드를 연결합니다.

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a>허브 프록시

더 이상 허브 프록시가 자동으로 생성되지 않습니다. 대신 메서드 이름은 [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API에 문자열로 전달됩니다.

### <a name="net-and-other-clients"></a>.NET 및 기타 클라이언트

`Microsoft.AspNetCore.SignalR.Client` NuGet 패키지에는 ASP.NET Core SignalR용 .NET 클라이언트 라이브러리가 포함되어 있습니다.

[HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)를 사용해서 허브에 대한 연결의 인스턴스를 생성하고 빌드합니다.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>스케일 아웃 차이점

ASP.NET SignalR은 SQL Server 및 Redis를 지원합니다. ASP.NET Core SignalR은 Azure SignalR Service 및 Redis를 지원합니다.

### <a name="aspnet"></a>ASP.NET

* [Azure Service Bus를 이용한 SignalR 스케일 아웃](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [Redis를 이용한 SignalR 스케일 아웃](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SQL Server를 이용한 SignalR 스케일 아웃](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Azure SignalR Service](/azure/azure-signalr/)
* [Redis 백플레인](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>추가 자료

* [허브](xref:signalr/hubs)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [지원되는 플랫폼](xref:signalr/supported-platforms)
