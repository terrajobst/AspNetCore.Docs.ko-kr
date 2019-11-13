---
title: JavaScript 클라이언트 ASP.NET Core SignalR
author: bradygaster
description: JavaScript 클라이언트 SignalR ASP.NET Core에 대 한 개요입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 926160a41c82853d83890f0d52b14d7d5561a990
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963779"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>JavaScript 클라이언트 ASP.NET Core SignalR

작성자: [Rachel Appel](https://twitter.com/rachelappel)

ASP.NET Core SignalR JavaScript 클라이언트 라이브러리를 사용 하면 개발자가 서버 쪽 허브 코드를 호출할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-client-package"></a>SignalR 클라이언트 패키지를 설치 합니다.

SignalR JavaScript 클라이언트 라이브러리는 [npm](https://www.npmjs.com/) 패키지로 제공 됩니다. Visual Studio를 사용 하는 경우 루트 폴더에 있는 동안 **패키지 관리자 콘솔** 에서 `npm install`를 실행 합니다. Visual Studio Code의 경우 **통합 터미널**에서 명령을 실행 합니다.

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

npm *node_modules\\@microsoft\signalr\dist\browser* 폴더에 패키지 콘텐츠를 설치 합니다. *Wwwroot\\lib* 폴더 아래에 *signalr* 라는 새 폴더를 만듭니다. *Signalr* 파일을 *wwwroot\lib\signalr* 폴더에 복사 합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

npm *node_modules\\@aspnet\signalr\dist\browser* 폴더에 패키지 콘텐츠를 설치 합니다. *Wwwroot\\lib* 폴더 아래에 *signalr* 라는 새 폴더를 만듭니다. *Signalr* 파일을 *wwwroot\lib\signalr* 폴더에 복사 합니다.

::: moniker-end

## <a name="use-the-opno-locsignalr-javascript-client"></a>SignalR JavaScript 클라이언트 사용

`<script>` 요소에서 SignalR JavaScript 클라이언트를 참조 합니다.

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a>허브에 연결

다음 코드에서는 연결을 만들고 시작 합니다. 허브 이름은 대/소문자를 구분 하지 않습니다.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>크로스-원본 연결

일반적으로 브라우저는 요청 된 페이지와 동일한 도메인의 연결을 로드 합니다. 그러나 다른 도메인에 연결 해야 하는 경우도 있습니다.

악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 하기 위해 [교차 원본 연결은](xref:security/cors) 기본적으로 사용 하지 않도록 설정 됩니다. 원본 간 요청을 허용 하려면 `Startup` 클래스에서 사용 하도록 설정 합니다.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>클라이언트에서 허브 메서드 호출

JavaScript 클라이언트는 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)의 [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) 메서드를 통해 허브에서 공용 메서드를 호출 합니다. `invoke` 메서드는 두 개의 인수를 허용 합니다.

* 허브 메서드의 이름입니다. 다음 예제에서는 허브의 메서드 이름이 `SendMessage`됩니다.
* 허브 메서드에 정의 된 모든 인수입니다. 다음 예에서는 인수 이름을 `message`합니다. 예제 코드는 Internet Explorer를 제외한 모든 주요 브라우저의 현재 버전에서 지원 되는 화살표 함수 구문을 사용 합니다.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> *서버 리스 모드*에서 Azure SignalR 서비스를 사용 하는 경우 클라이언트에서 허브 메서드를 호출할 수 없습니다. 자세한 내용은 [SignalR 서비스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)를 참조 하세요.

`invoke` 메서드는 JavaScript [약속](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)을 반환 합니다. 서버에서 메서드가 반환 될 때 `Promise` 반환 값 (있는 경우)으로 확인 됩니다. 서버에서 메서드가 오류를 throw 하는 경우 오류 메시지와 함께 `Promise` 거부 됩니다. `Promise` 자체에서 `then` 및 `catch` 메서드를 사용 하 여 이러한 경우 또는 `await` 구문을 처리 합니다.

`send` 메서드는 JavaScript `Promise`를 반환 합니다. 메시지가 서버에 전송 되 면 `Promise` 확인 됩니다. 메시지를 보내는 동안 오류가 발생 하면 오류 메시지와 함께 `Promise` 거부 됩니다. `Promise` 자체에서 `then` 및 `catch` 메서드를 사용 하 여 이러한 경우 또는 `await` 구문을 처리 합니다.

> [!NOTE]
> `send` 사용 하면 서버에서 메시지를 받을 때까지 기다리지 않습니다. 따라서 서버에서 데이터 또는 오류를 반환할 수 없습니다.

## <a name="call-client-methods-from-hub"></a>허브에서 클라이언트 메서드 호출

허브에서 메시지를 수신 하려면 `HubConnection`의 [on](/javascript/api/%40aspnet/signalr/hubconnection#on) 메서드를 사용 하 여 메서드를 정의 합니다.

* JavaScript 클라이언트 메서드의 이름입니다. 다음 예제에서는 메서드 이름이 `ReceiveMessage`입니다.
* 허브가 메서드에 전달 하는 인수입니다. 다음 예에서는 인수 값이 `message`됩니다.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

`connection.on`에서 위의 코드는 서버 쪽 코드가 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 메서드를 사용 하 여이를 호출할 때 실행 됩니다.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR은 `SendAsync` 및 `connection.on`에 정의 된 메서드 이름과 인수를 일치 시켜 호출할 클라이언트 메서드를 결정 합니다.

> [!NOTE]
> 모범 사례로, `on`후 `HubConnection`에서 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 메서드를 호출 합니다. 이렇게 하면 메시지를 받기 전에 처리기가 등록 됩니다.

## <a name="error-handling-and-logging"></a>오류 처리 및 로깅

`catch` 메서드를 `start` 메서드의 끝에 연결 하 여 클라이언트 쪽 오류를 처리 합니다. `console.error`를 사용 하 여 브라우저의 콘솔에 오류를 출력 합니다.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

연결이 설정 될 때 기록할 이벤트의 형식 및 형식을 전달 하 여 클라이언트 쪽 로그 추적을 설정 합니다. 메시지는 지정 된 로그 수준 이상으로 기록 됩니다. 사용 가능한 로그 수준은 다음과 같습니다.

* &ndash; 오류 메시지를 `signalR.LogLevel.Error` 합니다. `Error` 메시지만 로깅합니다.
* 잠재적 오류에 대 한 경고 메시지를 &ndash; `signalR.LogLevel.Warning` 합니다. `Warning`및 `Error` 메시지를 로깅합니다.
* 오류 없이 &ndash; 상태 메시지를 `signalR.LogLevel.Information` 합니다. `Information`, `Warning`및 `Error` 메시지를 기록 합니다.
* 추적 메시지를 &ndash; `signalR.LogLevel.Trace`. 허브와 클라이언트 간에 전송 되는 데이터를 포함 하 여 모든 것을 기록 합니다.

[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) 에서 [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) 메서드를 사용 하 여 로그 수준을 구성 합니다. 메시지는 브라우저 콘솔에 기록 됩니다.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>클라이언트 다시 연결

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>자동으로 다시 연결

[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)의 `withAutomaticReconnect` 메서드를 사용 하 여 SignalR에 대 한 JavaScript 클라이언트를 자동으로 다시 연결 하도록 구성할 수 있습니다. 기본적으로 자동으로 다시 연결 되지 않습니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

매개 변수를 사용 하지 않으면 `withAutomaticReconnect()` 각 다시 연결 시도를 시도 하기 전에 각각 0, 2, 10 및 30 초 동안 대기 하도록 클라이언트를 구성 하 고 실패 한 네 번 시도 후 중지 합니다.

다시 연결 시도를 시작 하기 전에 `HubConnection`는 `Disconnected` 상태로 전환 하 고 자동 다시 연결이 구성 되지 않은 `onclose` 처럼 `HubConnection` 콜백을 트리거하는 대신 `HubConnectionState.Reconnecting` 상태로 전환 하 고 `onreconnecting` 콜백을 발생 시킵니다. 이렇게 하면 연결이 손실 되었음을 사용자에 게 경고 하 고 UI 요소를 사용 하지 않도록 설정할 수 있습니다.

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

클라이언트가 처음 네 번의 시도 내에서 다시 연결 하는 경우 `HubConnection`는 다시 `Connected` 상태로 전환 되 고 `onreconnected` 콜백을 발생 시킵니다. 이렇게 하면 연결이 다시 설정 되었음을 사용자에 게 알릴 수 있습니다.

연결이 서버를 완전히 처음으로 표시 하기 때문에 `onreconnected` 콜백에 새 `connectionId` 제공 됩니다.

> [!WARNING]
> `HubConnection` [협상을 건너뛰도록](xref:signalr/configuration#configure-client-options)구성 된 경우 `onreconnected` 콜백의 `connectionId` 매개 변수가 정의 되지 않습니다.

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` 초기 시작 오류를 다시 시도 하도록 `HubConnection` 구성 하지 않으므로 시작 오류는 수동으로 처리 해야 합니다.

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

클라이언트가 처음 네 번의 시도 내에서 성공적으로 다시 연결 되지 않으면 `HubConnection` `Disconnected` 상태로 전환 되 고 [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) 콜백이 발생 합니다. 이를 통해 사용자에 게 연결이 영구적으로 손실 되었음을 알리고 페이지 새로 고침을 권장 하는 기회를 제공 합니다.

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

연결을 끊거나 다시 연결 하는 시간을 변경 하기 전에 사용자 지정 횟수의 다시 연결 시도를 구성 하려면 각 다시 연결 시도를 시작 하기 전에 대기할 지연 시간 (밀리초)을 나타내는 숫자 배열을 허용 `withAutomaticReconnect` 합니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

앞의 예제에서는 연결이 끊긴 후 즉시 다시 연결 시도를 시작 하도록 `HubConnection`를 구성 합니다. 기본 구성의 경우에도 마찬가지입니다.

첫 번째 다시 연결 시도가 실패 하면 두 번째 다시 연결 시도도 기본 구성에서와 같이 2 초 동안 대기 하는 대신 즉시 시작 됩니다.

두 번째 다시 연결 시도가 실패 하면 세 번째 다시 연결 시도가 10 초 후에 다시 시작 됩니다 .이는 기본 구성 처럼 다시 시작 됩니다.

그런 다음 기본 구성에서와 같이 30 초 후에 다시 연결을 다시 시도 하는 대신 세 번째 다시 연결 시도 실패 후 중지 하 여 기본 동작에서 사용자 지정 동작이 다시 달라 지므로.

자동 다시 연결 시도의 타이밍 및 수에 대 한 더 많은 제어를 원하는 경우 `withAutomaticReconnect`는 `nextRetryDelayInMilliseconds`라는 단일 메서드를 포함 하는 `IRetryPolicy` 인터페이스를 구현 하는 개체를 허용 합니다.

`nextRetryDelayInMilliseconds`는 형식이 `RetryContext`인 단일 인수를 사용 합니다. `RetryContext`에는 `previousRetryCount`, `elapsedMilliseconds` 및 `retryReason` 각각 `number`, `number` 및 `Error` 인 세 가지 속성이 있습니다. 첫 번째 다시 연결을 시도 하기 전에 `previousRetryCount`와 `elapsedMilliseconds`는 모두 0이 되며 `retryReason`는 연결이 끊어지는 원인이 되는 오류입니다. 실패 한 각 재시도 후에는 `previousRetryCount` 1 씩 증가 하 고, 지금까지 밀리초 단위로 다시 연결 하는 데 걸린 시간을 반영 하 여 `elapsedMilliseconds` 업데이트 되며, `retryReason`는 마지막 다시 연결 시도 실패를 일으킨 오류로 나타납니다.

`nextRetryDelayInMilliseconds`는 다음 다시 연결 시도 전에 대기 하는 시간 (밀리초)을 나타내는 숫자를 반환 하거나 `HubConnection` 다시 연결을 중지 해야 하는 경우 `null`을 반환 해야 합니다.

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
> 3\.0 이전 버전의 SignalR에 대 한 JavaScript 클라이언트는 자동으로 다시 연결 되지 않습니다. 클라이언트를 수동으로 다시 연결 하는 코드를 작성 해야 합니다.

::: moniker-end

다음 코드에서는 일반적인 수동 다시 연결 방식을 보여 줍니다.

1. 연결을 시작 하기 위해 함수 (이 경우 `start` 함수)가 만들어집니다.
1. 연결의 `onclose` 이벤트 처리기에서 `start` 함수를 호출 합니다.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

실제 구현에서는 지 수 백오프를 사용 하거나 지정 된 횟수 만큼 다시 시도 하 여 포기 합니다.

## <a name="additional-resources"></a>추가 자료

* [JavaScript API 참조](/javascript/api/?view=signalr-js-latest)
* [JavaScript 자습서](xref:tutorials/signalr)
* [WebPack 및 TypeScript 자습서](xref:tutorials/signalr-typescript-webpack)
* [허브](xref:signalr/hubs)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [Azure에 게시](xref:signalr/publish-to-azure-web-app)
* [원본 간 요청 (CORS)](xref:security/cors)
* [Azure SignalR 서비스 서버 리스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)
