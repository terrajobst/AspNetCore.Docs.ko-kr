---
title: ASP.NET SignalR 코어 자바 스크립트 클라이언트
author: bradygaster
description: ASP.NET 핵심 SignalR 자바 스크립트 클라이언트의 개요.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: a99c1dd2aba6ef6ff925783762a98e2c81ed7225
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994577"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET SignalR 코어 자바 스크립트 클라이언트

작성자: [Rachel Appel](https://twitter.com/rachelappel)

ASP.NET 코어 SignalR JavaScript 클라이언트 라이브러리를 사용하면 개발자가 서버 쪽 허브 코드를 호출할 수 있습니다.

[샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) [방법)](xref:index#how-to-download-a-sample)

## <a name="install-the-opno-locsignalr-client-package"></a>SignalR 클라이언트 패키지 설치

SignalR 자바 스크립트 클라이언트 라이브러리는 [npm](https://www.npmjs.com/) 패키지로 제공됩니다. 다음 섹션에서는 클라이언트 라이브러리를 설치하는 여러 가지 방법을 간략하게 설명합니다.

### <a name="install-with-npm"></a>npm으로 설치

Visual Studio를 사용하는 경우 루트 폴더에 있는 동안 **패키지 관리자 콘솔에서** 다음 명령을 실행합니다. Visual Studio 코드의 경우 **통합 터미널에서**다음 명령을 실행합니다.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm은 *node_modules\\ * 폴더에 패키지 내용을 설치합니다. *\\wwwroot lib* 폴더 아래에 *시그널이라는* 새 폴더를 만듭니다. *signalr.js* 파일을 *wwwroot\lib\signalr* 폴더에 복사합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm은 *node_modules\\ * 폴더에 패키지 내용을 설치합니다. *\\wwwroot lib* 폴더 아래에 *시그널이라는* 새 폴더를 만듭니다. *signalr.js* 파일을 *wwwroot\lib\signalr* 폴더에 복사합니다.

::: moniker-end

요소에서 SignalR 자바스크립트 클라이언트를 참조합니다. `<script>` 예를 들어:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>CDN(콘텐츠 전송 네트워크) 사용

npm 필수 구성 조건 없이 클라이언트 라이브러리를 사용하려면 클라이언트 라이브러리의 CDN 호스팅 복사본을 참조하십시오. 예를 들어:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

클라이언트 라이브러리는 다음 CDN에서 사용할 수 있습니다.

::: moniker range=">= aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [언프kg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [언프kg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>LibMan으로 설치

[LibMan은](xref:client-side/libman/index) CDN 호스팅 클라이언트 라이브러리에서 특정 클라이언트 라이브러리 파일을 설치하는 데 사용할 수 있습니다. 예를 들어, 프로젝트에 만 다진 자바 스크립트 파일을 추가합니다. 해당 접근 방식에 대한 자세한 내용은 [클라이언트 라이브러리 추가를 SignalR ](xref:tutorials/signalr#add-the-signalr-client-library)참조하십시오.

## <a name="connect-to-a-hub"></a>허브에 연결

다음 코드는 연결을 만들고 시작합니다. 허브의 이름은 대/소문자를 구분하지 않습니다.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>오리진 간 연결

일반적으로 브라우저는 요청된 페이지와 동일한 도메인에서 연결을 로드합니다. 그러나 다른 도메인에 연결해야 하는 경우가 있습니다.

악의적인 사이트가 다른 사이트에서 중요한 데이터를 읽지 못하도록 하려면 기본적으로 [원본 간 연결이](xref:security/cors) 비활성화됩니다. 원본 간 요청을 허용하려면 클래스에서 `Startup` 사용하도록 설정합니다.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>클라이언트에서 호출 허브 메서드

JavaScript 클라이언트는 [HubConnection의](/javascript/api/%40aspnet/signalr/hubconnection) [호출](/javascript/api/%40aspnet/signalr/hubconnection#invoke) 메서드를 통해 허브에서 공용 메서드를 호출합니다. 메서드는 `invoke` 두 개의 인수를 허용합니다.

* 허브 메서드의 이름입니다. 다음 예제에서는 허브의 메서드 이름은 `SendMessage`입니다.
* 허브 메서드에 정의된 모든 인수입니다. 다음 예제에서 인수 이름은 `message`입니다. 예제 코드는 Internet Explorer를 제외한 모든 주요 브라우저의 현재 버전에서 지원되는 화살표 함수 구문을 사용합니다.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> *서버리스 모드에서*Azure SignalR 서비스를 사용하는 경우 클라이언트에서 허브 메서드를 호출할 수 없습니다. 자세한 내용은 서비스 [ SignalR 설명서를](/azure/azure-signalr/signalr-concept-serverless-development-config)참조하십시오.

이 `invoke` 메서드는 자바 스크립트 [약속을](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)반환합니다. 서버의 `Promise` 메서드가 반환될 때 반환 값(있는 경우)으로 확인됩니다. 서버의 메서드에서 오류가 발생하면 오류 `Promise` 메시지와 함께 거부됩니다. `then` 자체의 `catch` 메서드 및 `Promise` 메서드를 사용하여 이러한 `await` 사례(또는 구문)를 처리합니다.

메서드는 `send` 자바 스크립트를 `Promise`반환합니다. 이 `Promise` 메시지는 서버로 전송될 때 해결됩니다. 메시지를 보내는 오류가 있으면 오류 `Promise` 메시지와 함께 거부됩니다. `then` 자체의 `catch` 메서드 및 `Promise` 메서드를 사용하여 이러한 `await` 사례(또는 구문)를 처리합니다.

> [!NOTE]
> 서버가 메시지를 받을 때까지 사용 `send` 해도 지 않습니다. 따라서 서버에서 데이터 또는 오류를 반환할 수 없습니다.

## <a name="call-client-methods-from-hub"></a>허브에서 클라이언트 메서드 호출

허브에서 메시지를 받으려면 `HubConnection`의 [on](/javascript/api/%40aspnet/signalr/hubconnection#on) 메서드를 사용하여 메서드를 정의합니다.

* 자바 스크립트 클라이언트 메서드의 이름입니다. 다음 예제에서 메서드 이름은 `ReceiveMessage`입니다.
* 인수 허브 메서드에 전달 합니다. 다음 예제에서 인수 값은 `message`입니다.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

위의 코드는 `connection.on` 서버 측 코드가 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 메서드를 사용하여 호출할 때 실행됩니다.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR에서 정의된 `SendAsync` 메서드 이름과 인수를 일치시켜 호출할 클라이언트 `connection.on`메서드를 결정합니다.

> [!NOTE]
> 가장 좋은 방법은 after의 `on` [시작](/javascript/api/%40aspnet/signalr/hubconnection#start) `HubConnection` 메서드를 호출합니다. 이렇게 하면 메시지를 받기 전에 처리기가 등록됩니다.

## <a name="error-handling-and-logging"></a>오류 처리 및 로깅

메서드를 `catch` 메서드 끝에 `start` 연결하여 클라이언트 측 오류를 처리합니다. 브라우저 `console.error` 콘솔에 오류를 출력하는 데 사용합니다.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

연결이 이루어질 때 로거 및 이벤트 유형을 전달하여 클라이언트 측 로그 추적을 설정합니다. 메시지는 지정된 로그 수준 이상으로 기록됩니다. 사용 가능한 로그 수준은 다음과 같습니다.

* `signalR.LogLevel.Error`&ndash; 오류 메시지입니다. 메시지만 `Error` 기록합니다.
* `signalR.LogLevel.Warning`&ndash; 잠재적인 오류에 대한 경고 메시지입니다. `Warning`로그 및 `Error` 메시지입니다.
* `signalR.LogLevel.Information`&ndash; 오류 없이 상태 메시지. `Information`로그 `Warning`및 `Error` 메시지입니다.
* `signalR.LogLevel.Trace`&ndash; 메시지를 추적합니다. 허브와 클라이언트 간에 전송되는 데이터를 포함하여 모든 것을 기록합니다.

[HubConnectionBuilder에서](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) 메서드를 사용하여 로그 수준을 구성합니다. 메시지가 브라우저 콘솔에 기록됩니다.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>클라이언트 다시 연결

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>자동으로 다시 연결

에 대 한 SignalR 자바 스크립트 `withAutomaticReconnect` [클라이언트는 HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). 기본적으로 자동으로 다시 연결되지 않습니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

매개 변수가 `withAutomaticReconnect()` 없으면 클라이언트가 각각 0, 2, 10 및 30초를 기다린 후 각 재연결 시도를 시도하여 4번의 실패한 시도 후에 중지하도록 구성됩니다.

다시 연결 시도를 `HubConnection` 시작하기 전에 `HubConnectionState.Reconnecting` 상태가 상태로 전환되고 `onreconnecting` 자동으로 다시 `Disconnected` `onclose` `HubConnection` 연결되지 않은 것처럼 콜백을 트리거하는 대신 콜백을 발생시면 됩니다. 이렇게 하면 연결이 손실되었음을 사용자에게 경고하고 UI 요소를 사용하지 않도록 설정할 수 있습니다.

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

클라이언트가 처음 네 번의 시도 내에서 `HubConnection` 성공적으로 다시 연결되면 `Connected` 클라이언트가 `onreconnected` 상태로 다시 전환되고 콜백이 발생합니다. 이렇게 하면 연결이 다시 설정되었음을 사용자에게 알릴 수 있습니다.

연결이 서버에 완전히 새 것처럼 보이므로 `connectionId` `onreconnected` 콜백에 새 연결이 제공됩니다.

> [!WARNING]
> 협상을 [건너뛰도록](xref:signalr/configuration#configure-client-options) `connectionId` 구성된 경우 콜백의 `HubConnection` 매개 변수가 정의되지 않습니다. `onreconnected`

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`초기 시작 실패를 `HubConnection` 다시 시도하도록 구성하지 않으므로 시작 실패를 수동으로 처리해야 합니다.

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

클라이언트가 처음 네 번의 시도 내에서 성공적으로 `HubConnection` 다시 연결되지 `Disconnected` 않으면 클라이언트가 상태로 전환되어 [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) 콜백을 발생시면 됩니다. 이렇게 하면 연결이 영구적으로 손실되었음을 사용자에게 알리고 페이지를 새로 고칠 것을 권장합니다.

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

연결을 끊거나 다시 연결 타이밍을 변경하기 전에 사용자 지정 `withAutomaticReconnect` 재연결 시도 수를 구성하려면 각 다시 연결 시도를 시작하기 전에 기다릴 수 있는 밀리초 의 지연을 나타내는 숫자 배열을 수락합니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

앞의 예제는 연결이 `HubConnection` 끊어진 직후다시 연결을 시도할 수 있도록 구성합니다. 기본 구성에도 마찬가지입니다.

첫 번째 다시 연결 시도가 실패하면 기본 구성에서와 마찬가지로 2초를 기다리는 대신 두 번째 다시 연결 시도도 즉시 시작됩니다.

두 번째 다시 연결 시도가 실패하면 세 번째 다시 연결 시도가 기본 구성과 같은 10초 후에 시작됩니다.

그런 다음 사용자 지정 동작은 기본 구성에서와 마찬가지로 다른 30초 동안 다시 연결시도를 한 번 더 시도하는 대신 세 번째 다시 연결 시도 실패 후에 중지하여 기본 동작에서 다시 분기됩니다.

자동 다시 연결 시도의 `withAutomaticReconnect` `IRetryPolicy` 타이밍과 수를 보다 세한 제어를 원하는 `nextRetryDelayInMilliseconds`경우.

`nextRetryDelayInMilliseconds`형식이 `RetryContext`있는 단일 인수를 취합니다. 는 `RetryContext` 세 가지 `previousRetryCount` `elapsedMilliseconds` 속성을 `retryReason` 가지고 `number`있습니다: `number` 및 `Error` 는 각각 . 첫 번째 다시 연결 `previousRetryCount` 시도 `elapsedMilliseconds` 전에 둘 다 `retryReason` 0이 되고 연결이 손실된 오류가 됩니다. 실패한 각 재시도 `previousRetryCount` 후 하나씩 증분되고 지금까지 `elapsedMilliseconds` 다시 연결하는 데 소요된 시간을 밀리초 단위로 반영하도록 `retryReason` 업데이트되며 마지막 다시 연결 시도가 실패하게 한 오류입니다.

`nextRetryDelayInMilliseconds`다음 다시 연결 시도 전에 기다릴 밀리초 수를 나타내는 숫자를 `null` 반환하거나 다시 연결을 중지해야 하는 경우 를 `HubConnection` 반환해야 합니다.

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
        }
    })
    .build();
```

또는 수동으로 다시 연결에서 설명한 대로 클라이언트를 수동으로 다시 연결하는 코드를 [작성할](#manually-reconnect)수 있습니다.

::: moniker-end

### <a name="manually-reconnect"></a>수동으로 다시 연결

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 3.0 이전에는 JavaScript 클라이언트가 자동으로 다시 연결되지 SignalR 않습니다. 클라이언트를 수동으로 다시 연결하는 코드를 작성해야 합니다.

::: moniker-end

다음 코드는 일반적인 수동 재연결 방법을 보여 줍니다.

1. 연결을 시작하는 함수(이 `start` 경우 함수)가 만들어집니다.
1. 연결의 `start` 이벤트 처리기에서 `onclose` 함수를 호출합니다.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

실제 구현은 포기하기 전에 지수 백오프를 사용하거나 지정된 횟수로 다시 시도합니다.

## <a name="additional-resources"></a>추가 리소스

* [JavaScript API 참조](/javascript/api/?view=signalr-js-latest)
* [자바 스크립트 튜토리얼](xref:tutorials/signalr)
* [웹팩 및 타이프스크립트 튜토리얼](xref:tutorials/signalr-typescript-webpack)
* [허브](xref:signalr/hubs)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [Azure에 게시](xref:signalr/publish-to-azure-web-app)
* [교차 원산지 요청(CORS)](xref:security/cors)
* [Azure SignalR 서비스 서버없는 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)
