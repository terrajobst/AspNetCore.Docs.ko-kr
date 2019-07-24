---
title: ASP.NET Core SignalR JavaScript 클라이언트
author: bradygaster
description: ASP.NET Core SignalR JavaScript 클라이언트의 개요입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/28/2019
uid: signalr/javascript-client
ms.openlocfilehash: f314e1fe0ef0ea73a28b332404a09f2956524132
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412388"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.NET Core SignalR JavaScript 클라이언트

작성자: [Rachel Appel](https://twitter.com/rachelappel)

ASP.NET Core SignalR JavaScript 클라이언트 라이브러리를 사용 하면 서버 쪽 허브 코드를 호출할 수 있습니다.

[샘플 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>SignalR 클라이언트 패키지 설치하기

SignalR JavaScript 클라이언트 라이브러리는 [npm](https://www.npmjs.com/) 패키지로 배포됩니다. Visual Studio를 사용하고 있다면 **패키지 관리자 콘솔** 에서 루트 폴더에 있을 때 `npm install` 을 실행합니다. Visual Studio Code에서는 **통합 터미널** 에서 명령을 실행합니다.

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

그러면 npm이 *node_modules\\@microsoft\signalr\dist\browser* 폴더에 패키지 콘텐츠를 설치합니다. *wwwroot\\lib* 폴더 하위에 *signalr* 이라는 새 폴더를 만듭니다 *signalr.js* 파일을 *wwwroot\lib\signalr* 폴더로 복사합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

그러면 npm이 *node_modules\\@aspnet\signalr\dist\browser* 폴더에 패키지 콘텐츠를 설치합니다. *wwwroot\\lib* 폴더 하위에 *signalr* 이라는 새 폴더를 만듭니다 *signalr.js* 파일을 *wwwroot\lib\signalr* 폴더로 복사합니다.

::: moniker-end

## <a name="use-the-signalr-javascript-client"></a>SignalR JavaScript 클라이언트 사용하기

`<script>` 요소를 이용해서 SignalR JavaScript 클라이언트를 참조합니다.

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a>허브에 연결하기

다음 코드는 연결을 만들고 시작합니다. 허브의 이름은 대소문자를 구분하지 않습니다.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>원본 간 연결

일반적으로 브라우저는 요청된 페이지와 동일한 도메인에서 연결을 로드합니다. 그러나 다른 도메인에 연결해야 하는 경우도 있습니다.

악의적인 사이트가 다른 사이트의 민감한 데이터를 읽지 못하게 막기 위해서, 기본적으로 [원본 간 연결](xref:security/cors) 은 비활성화되어 있습니다. 원본 간 요청을 허용하려면 `Startup` 클래스에서 이를 활성화하십시오.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>클라이언트에서 허브 메서드 호출하기

JavaScript 클라이언트는 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)의 [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) 메서드를 통해서 허브의 public 메서드를 호출합니다. 이 `invoke` 메서드는 두 가지 인수를 전달받습니다.

* 허브 메서드의 이름. 다음 예제에서 허브 메서드의 이름은 `SendMessage`입니다.
* 허브 메서드에 정의된 모든 인수. 다음 예제에서 인수의 이름은 `message`입니다. 예제 코드는 Internet Explorer를 제외한 모든 주요 브라우저의 현재 버전에서 지원 되는 화살표 함수 구문을 사용 합니다.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> 서버를 사용 하지 않는 *모드로*Azure SignalR Service를 사용 하는 경우 클라이언트에서 허브 메서드를 호출할 수 없습니다. 자세한 내용은 [SignalR Service 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)를 참조 하세요.

메서드 `invoke` 는 JavaScript [약속](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)을 반환 합니다. 는 `Promise` 서버의 메서드가 반환 될 때 반환 값 (있는 경우)으로 확인 됩니다. 서버에서 메서드가 오류를 `Promise` throw 하는 경우 오류 메시지와 함께이 거부 됩니다. 이러한 경우 또는 `catch` `Promise` `then` 구문을처리하려면자체에서및메서드`await` 를 사용 합니다.

메서드 `send` 는 JavaScript `Promise`를 반환 합니다. 메시지가 서버에 전송 되 면 이확인됩니다.`Promise` 메시지를 보내는 동안 오류가 발생 하는 경우 `Promise` 이 오류 메시지와 함께 거부 됩니다. 이러한 경우 또는 `catch` `Promise` `then` 구문을처리하려면자체에서및메서드`await` 를 사용 합니다.

> [!NOTE]
> 를 `send` 사용 하면 서버에서 메시지를 받을 때까지 기다리지 않습니다. 따라서 서버에서 데이터 또는 오류를 반환할 수 없습니다.

## <a name="call-client-methods-from-hub"></a>허브에서 클라이언트 메서드 호출하기

허브에서 메시지를 수신하려면 `HubConnection`의 [on](/javascript/api/%40aspnet/signalr/hubconnection#on) 메서드를 이용해서 메서드를 정의합니다.

* JavaScript 클라이언트 메서드의 이름. 다음 예제에서 메서드 이름은 `ReceiveMessage`입니다.
* 허브에서 메서드로 전달될 인수. 다음 예제에서 인수의 이름은 `message` 입니다.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

위의 `connection.on` 내부의 코드는 서버 쪽 코드에서 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 메서드를 사용하여 이 코드를 호출할 때 실행됩니다.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR은 `SendAsync`와 `connection.on`에 정의된 메서드 이름과 인수들이 일치하는지를 검사해서 어떤 클라이언트 메서드를 호출할지 결정합니다.

> [!NOTE]
> 권장되는 방식은 `on` 메서드를 호출한 뒤에 `HubConnection`의 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 메서드를 호출하는 것입니다. 이렇게 하면 모든 메시지를 수신하기 전에 처리기가 먼저 등록됩니다.

## <a name="error-handling-and-logging"></a>오류 처리 및 로깅

클라이언트 쪽 오류를 처리하려면 `start` 메서드의 끝에 `catch` 메서드를 연결합니다. 브라우저의 콘솔에 오류를 출력하려면 `console.error`를 사용합니다.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

연결이 만들어지면 로거와 기록할 이벤트 유형을 전달하여 클라이언트 쪽 로그 추적을 설정합니다. 지정한 로그 수준 이상의 메시지가 기록됩니다. 사용 가능한 로그 수준은 다음과 같습니다.

* `signalR.LogLevel.Error` &ndash; 오류 메시지. `Error` 메시지만 기록합니다.
* `signalR.LogLevel.Warning` &ndash; 잠재적 오류에 대한 경고 메시지. `Warning` 및 `Error` 메시지를 기록합니다.
* `signalR.LogLevel.Information` &ndash; 오류가 아닌 상태 메시지. `Information`, `Warning` 및 `Error` 메시지를 기록합니다.
* `signalR.LogLevel.Trace` &ndash; 추적 메시지. 허브와 클라이언트 간에 전송되는 데이터를 포함한 모든 것을 기록합니다.

로그 수준을 구성하려면 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)의 [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) 메서드를 사용합니다. 메시지는 브라우저 콘솔에 기록됩니다.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>클라이언트를 다시 연결

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>자동으로 다시 연결

SignalR에 대 한 JavaScript 클라이언트는 `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)에서 메서드를 사용 하 여 자동으로 다시 연결 되도록 구성할 수 있습니다. 기본적으로 자동으로 다시 연결 되지 않습니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

매개 변수를 `withAutomaticReconnect()` 사용 하지 않으면는 각 다시 연결 시도를 시도 하기 전에 0, 2, 10 및 30 초 동안 대기 하도록 클라이언트를 구성 하 고, 실패 한 네 번 시도 하면 중지 합니다.

다시 연결 `HubConnection` 시도를 시작 하기 전에이 상태로 전환 `HubConnectionState.Reconnecting` 되 고, `Disconnected` 상태를 `onreconnecting` 전환 하 고 해당 `onclose` 콜백을 다음과 같이 `HubConnection`트리거하는대신콜백이발생합니다.자동 다시 연결이 구성 되지 않았습니다. 이렇게 하면 연결이 손실 되었음을 사용자에 게 경고 하 고 UI 요소를 사용 하지 않도록 설정할 수 있습니다.

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

클라이언트에서 처음 네 번의 시도 `HubConnection` 내에 다시 연결 하는 경우이 `Connected` 상태로 다시 전환 되 고 `onreconnected` 콜백이 발생 합니다. 이렇게 하면 연결이 다시 설정 되었음을 사용자에 게 알릴 수 있습니다.

연결은 서버에서 완전히 새로운 것 이므로 `connectionId` `onreconnected` 콜백에 새가 제공 됩니다.

> [!WARNING]
> 가 `onreconnected` 협상을 `connectionId` [건너뛰도록](xref:signalr/configuration#configure-client-options)구성 된 경우 `HubConnection` 콜백의 매개 변수는 정의 되지 않습니다.

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`초기 시작 오류 `HubConnection` 를 다시 시도 하도록를 구성 하지 않으므로 시작 실패를 수동으로 처리 해야 합니다.

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

클라이언트가 처음 네 번의 시도 `HubConnection` 내에서 성공적으로 다시 연결 되지 않으면이 `Disconnected` 상태로 전환 되 고 [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) 콜백이 발생 합니다. 이를 통해 사용자에 게 연결이 영구적으로 손실 되었음을 알리고 페이지 새로 고침을 권장 하는 기회를 제공 합니다.

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

연결을 끊거나 다시 연결 하는 시간 `withAutomaticReconnect` 을 변경 하기 전에 사용자가 다시 연결 시도 횟수를 구성 하기 위해는 각 다시 연결 시도를 시작 하기 전에 대기할 지연 시간 (밀리초)을 나타내는 숫자 배열을 허용 합니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

앞의 예제에서는 연결이 `HubConnection` 끊긴 후 즉시 다시 연결 시도를 시작 하도록를 구성 합니다. 기본 구성의 경우에도 마찬가지입니다.

첫 번째 다시 연결 시도가 실패 하면 두 번째 다시 연결 시도도 기본 구성에서와 같이 2 초 동안 대기 하는 대신 즉시 시작 됩니다.

두 번째 다시 연결 시도가 실패 하면 세 번째 다시 연결 시도가 10 초 후에 다시 시작 됩니다 .이는 기본 구성 처럼 다시 시작 됩니다.

그런 다음 기본 구성에서와 같이 30 초 후에 다시 연결을 다시 시도 하는 대신 세 번째 다시 연결 시도 실패 후 중지 하 여 기본 동작에서 사용자 지정 동작이 다시 달라 지므로.

자동 다시 연결 시도 `withAutomaticReconnect` 의 타이밍과 수를 더 많이 제어 하려는 경우는 라는 `nextRetryDelayInMilliseconds`단일 메서드가 있는 `IRetryPolicy` 인터페이스를 구현 하는 개체를 허용 합니다.

`nextRetryDelayInMilliseconds`는 형식의 `RetryContext`단일 인수를 사용 합니다. `previousRetryCount` 에는`elapsedMilliseconds` , 및 각각,`number`및 인 세 가지 속성이 있습니다. `number` `retryReason` `RetryContext` `Error` 첫 번째 다시 연결을 시도 하기 `previousRetryCount` 전에 `elapsedMilliseconds` 와는 모두 0이 되며 `retryReason` 은 연결이 끊어지는 원인이 되는 오류입니다. 실패 한 각 재시도 `previousRetryCount` 후에는 1 씩 증가 하 고, 지금까지 밀리초 단위로 다시 연결 하는 데 소요 된 시간을 반영 하도록 업데이트 되며, `elapsedMilliseconds` 는 `retryReason` 마지막 다시 연결 시도를 발생 시킨 오류를 발생 시킵니다. 통과.

`nextRetryDelayInMilliseconds`는 다음 다시 연결 시도 `null` 전에 대기 하는 시간 (밀리초)을 나타내는 숫자를 반환 해야 합니다. 그렇지 않으면에서 `HubConnection` 다시 연결을 중지 해야 합니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
          if (retryContext.elapsedMilliseconds < 60000) {
            // If we've been reconnecting for less than 60 seconds so far,
            // wait between 0 and 10 seconds before the next reconnect attempt.
            return Math.random() * 10000;
          } else {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
          }
        })
    .build();
```

또는 [수동으로 다시 연결](#manually-reconnect)에 설명 된 대로 클라이언트를 수동으로 다시 연결 하는 코드를 작성할 수 있습니다.

::: moniker-end

### <a name="manually-reconnect"></a>수동으로 다시 연결

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 3\.0 이전 버전의 SignalR에 대 한 JavaScript 클라이언트는 자동으로 다시 연결 되지 않습니다. 클라이언트에 수동으로 다시 연결 하는 코드를 작성 해야 합니다.

::: moniker-end

다음 코드에서는 일반적인 수동 다시 연결 방식을 보여 줍니다.

1. 함수 (이 경우에 `start` 함수) 연결을 시작 하기 위해 만들어집니다.
1. 호출 된 `start` 함수에서 연결의 `onclose` 이벤트 처리기입니다.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

실제 구현을는 지 수 백오프를 사용 하거나 포기 하기 전에 지정 된 횟수를 다시 시도 하세요.

## <a name="additional-resources"></a>추가 자료

* [JavaScript API 참조](/javascript/api/?view=signalr-js-latest)
* [JavaScript 자습서](xref:tutorials/signalr)
* [WebPack 및 TypeScript 자습서](xref:tutorials/signalr-typescript-webpack)
* [허브](xref:signalr/hubs)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [Azure에 게시하기](xref:signalr/publish-to-azure-web-app)
* [원본 간 요청 (CORS)](xref:security/cors)
* [Azure SignalR 서비스 서버 리스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)
