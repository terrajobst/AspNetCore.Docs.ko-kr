---
title: ASP.NET Core SignalR JavaScript 클라이언트
author: bradygaster
description: ASP.NET Core SignalR JavaScript 클라이언트의 개요입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/javascript-client
ms.openlocfilehash: 8b645304b597db0c37fb9cd8814c493ca1c6ee62
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814956"
---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="28291-103">ASP.NET Core SignalR JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="28291-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="28291-104">작성자: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="28291-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="28291-105">ASP.NET Core SignalR JavaScript 클라이언트 라이브러리를 사용 하면 서버 쪽 허브 코드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28291-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="28291-106">[샘플 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="28291-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="28291-107">SignalR 클라이언트 패키지 설치하기</span><span class="sxs-lookup"><span data-stu-id="28291-107">Install the SignalR client package</span></span>

<span data-ttu-id="28291-108">SignalR JavaScript 클라이언트 라이브러리는 [npm](https://www.npmjs.com/) 패키지로 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="28291-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="28291-109">Visual Studio를 사용하고 있다면 **패키지 관리자 콘솔** 에서 루트 폴더에 있을 때 `npm install` 을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="28291-110">Visual Studio Code에서는 **통합 터미널** 에서 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="28291-111">그러면 npm이 *node_modules\\@aspnet\signalr\dist\browser* 폴더에 패키지 콘텐츠를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-111">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="28291-112">*wwwroot\\lib* 폴더 하위에 *signalr* 이라는 새 폴더를 만듭니다</span><span class="sxs-lookup"><span data-stu-id="28291-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="28291-113">*signalr.js* 파일을 *wwwroot\lib\signalr* 폴더로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

## <a name="use-the-signalr-javascript-client"></a><span data-ttu-id="28291-114">SignalR JavaScript 클라이언트 사용하기</span><span class="sxs-lookup"><span data-stu-id="28291-114">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="28291-115">`<script>` 요소를 이용해서 SignalR JavaScript 클라이언트를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-115">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="28291-116">허브에 연결하기</span><span class="sxs-lookup"><span data-stu-id="28291-116">Connect to a hub</span></span>

<span data-ttu-id="28291-117">다음 코드는 연결을 만들고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-117">The following code creates and starts a connection.</span></span> <span data-ttu-id="28291-118">허브의 이름은 대소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28291-118">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="28291-119">원본 간 연결</span><span class="sxs-lookup"><span data-stu-id="28291-119">Cross-origin connections</span></span>

<span data-ttu-id="28291-120">일반적으로 브라우저는 요청된 페이지와 동일한 도메인에서 연결을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-120">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="28291-121">그러나 다른 도메인에 연결해야 하는 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28291-121">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="28291-122">악의적인 사이트가 다른 사이트의 민감한 데이터를 읽지 못하게 막기 위해서, 기본적으로 [원본 간 연결](xref:security/cors) 은 비활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28291-122">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="28291-123">원본 간 요청을 허용하려면 `Startup` 클래스에서 이를 활성화하십시오.</span><span class="sxs-lookup"><span data-stu-id="28291-123">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="28291-124">클라이언트에서 허브 메서드 호출하기</span><span class="sxs-lookup"><span data-stu-id="28291-124">Call hub methods from client</span></span>

<span data-ttu-id="28291-125">JavaScript 클라이언트는 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)의 [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) 메서드를 통해서 허브의 public 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-125">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="28291-126">이 `invoke` 메서드는 두 가지 인수를 전달받습니다.</span><span class="sxs-lookup"><span data-stu-id="28291-126">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="28291-127">허브 메서드의 이름.</span><span class="sxs-lookup"><span data-stu-id="28291-127">The name of the hub method.</span></span> <span data-ttu-id="28291-128">다음 예제에서 허브 메서드의 이름은 `SendMessage`입니다.</span><span class="sxs-lookup"><span data-stu-id="28291-128">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="28291-129">허브 메서드에 정의된 모든 인수.</span><span class="sxs-lookup"><span data-stu-id="28291-129">Any arguments defined in the hub method.</span></span> <span data-ttu-id="28291-130">다음 예제에서 인수의 이름은 `message`입니다.</span><span class="sxs-lookup"><span data-stu-id="28291-130">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="28291-131">이 예제 코드에서는 최신 버전의 Internet Explorer를 제외 하 고 모든 주요 브라우저에서 지원 되는 화살표 함수 구문입니다.</span><span class="sxs-lookup"><span data-stu-id="28291-131">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="28291-132">Azure SignalR Service를 사용 하는 경우 *서버 리스 모드*, 클라이언트에서 허브 메서드를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="28291-132">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="28291-133">자세한 내용은 참조는 [SignalR Service 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-133">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="28291-134">합니다 `invoke` 메서드는 JavaScript [약속](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-134">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="28291-135">`Promise` 해결 될 반환 값 (있는 경우) 서버에 대 한 메서드가 반환 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="28291-135">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="28291-136">서버에 대 한 메서드가 오류를 throw 하는 경우는 `Promise` 오류 메시지와 함께 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="28291-136">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="28291-137">사용 합니다 `then` 및 `catch` 메서드는 `Promise` 이러한 자체 (또는 `await` 구문).</span><span class="sxs-lookup"><span data-stu-id="28291-137">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="28291-138">합니다 `send` 메서드는 JavaScript `Promise`합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-138">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="28291-139">`Promise` 서버로 메시지가 전송 된 때 해결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="28291-139">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="28291-140">메시지를 보내는 오류가 발생 하는 경우는 `Promise` 오류 메시지와 함께 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="28291-140">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="28291-141">사용 합니다 `then` 및 `catch` 메서드는 `Promise` 이러한 자체 (또는 `await` 구문).</span><span class="sxs-lookup"><span data-stu-id="28291-141">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="28291-142">사용 하 여 `send` 서버에서 메시지를 수신 될 때까지 대기 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28291-142">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="28291-143">따라서 서버에서 데이터 또는 오류를 반환할 수 없는 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-143">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="28291-144">허브에서 클라이언트 메서드 호출하기</span><span class="sxs-lookup"><span data-stu-id="28291-144">Call client methods from hub</span></span>

<span data-ttu-id="28291-145">허브에서 메시지를 수신하려면 `HubConnection`의 [on](/javascript/api/%40aspnet/signalr/hubconnection#on) 메서드를 이용해서 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-145">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="28291-146">JavaScript 클라이언트 메서드의 이름.</span><span class="sxs-lookup"><span data-stu-id="28291-146">The name of the JavaScript client method.</span></span> <span data-ttu-id="28291-147">다음 예제에서 메서드 이름은 `ReceiveMessage`입니다.</span><span class="sxs-lookup"><span data-stu-id="28291-147">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="28291-148">허브에서 메서드로 전달될 인수.</span><span class="sxs-lookup"><span data-stu-id="28291-148">Arguments the hub passes to the method.</span></span> <span data-ttu-id="28291-149">다음 예제에서 인수의 이름은 `message` 입니다.</span><span class="sxs-lookup"><span data-stu-id="28291-149">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="28291-150">위의 `connection.on` 내부의 코드는 서버 쪽 코드에서 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 메서드를 사용하여 이 코드를 호출할 때 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="28291-150">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="28291-151">SignalR은 `SendAsync`와 `connection.on`에 정의된 메서드 이름과 인수들이 일치하는지를 검사해서 어떤 클라이언트 메서드를 호출할지 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-151">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="28291-152">권장되는 방식은 `on` 메서드를 호출한 뒤에 `HubConnection`의 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 메서드를 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="28291-152">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="28291-153">이렇게 하면 모든 메시지를 수신하기 전에 처리기가 먼저 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="28291-153">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="28291-154">오류 처리 및 로깅</span><span class="sxs-lookup"><span data-stu-id="28291-154">Error handling and logging</span></span>

<span data-ttu-id="28291-155">클라이언트 쪽 오류를 처리하려면 `start` 메서드의 끝에 `catch` 메서드를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-155">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="28291-156">브라우저의 콘솔에 오류를 출력하려면 `console.error`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-156">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="28291-157">연결이 만들어지면 로거와 기록할 이벤트 유형을 전달하여 클라이언트 쪽 로그 추적을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-157">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="28291-158">지정한 로그 수준 이상의 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="28291-158">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="28291-159">사용 가능한 로그 수준은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="28291-159">Available log levels are as follows:</span></span>

* <span data-ttu-id="28291-160">`signalR.LogLevel.Error` &ndash; 오류 메시지.</span><span class="sxs-lookup"><span data-stu-id="28291-160">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="28291-161">`Error` 메시지만 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-161">Logs `Error` messages only.</span></span>
* <span data-ttu-id="28291-162">`signalR.LogLevel.Warning` &ndash; 잠재적 오류에 대한 경고 메시지.</span><span class="sxs-lookup"><span data-stu-id="28291-162">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="28291-163">`Warning` 및 `Error` 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-163">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="28291-164">`signalR.LogLevel.Information` &ndash; 오류가 아닌 상태 메시지.</span><span class="sxs-lookup"><span data-stu-id="28291-164">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="28291-165">`Information`, `Warning` 및 `Error` 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-165">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="28291-166">`signalR.LogLevel.Trace` &ndash; 추적 메시지.</span><span class="sxs-lookup"><span data-stu-id="28291-166">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="28291-167">허브와 클라이언트 간에 전송되는 데이터를 포함한 모든 것을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-167">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="28291-168">로그 수준을 구성하려면 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)의 [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-168">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="28291-169">메시지는 브라우저 콘솔에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="28291-169">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="28291-170">클라이언트를 다시 연결</span><span class="sxs-lookup"><span data-stu-id="28291-170">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="28291-171">자동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="28291-171">Automatically reconnect</span></span>

<span data-ttu-id="28291-172">SignalR에 대 한 JavaScript 클라이언트를 자동으로 사용 하 여 다시 연결을 구성할 수 있습니다 합니다 `withAutomaticReconnect` 메서드를 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-172">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="28291-173">기본적으로 다시 자동으로 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="28291-173">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="28291-174">모든 매개 변수 없이 `withAutomaticReconnect()` 4 실패 한 시도 후 각 다시 연결 시도 전에 각각 0, 2, 10 일 및 30 초를 기다리고 클라이언트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-174">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="28291-175">다시 연결 시도 시작 하기 전에 `HubConnection` 전환 됩니다 합니다 `HubConnectionState.Reconnecting` 상태 및 실행 해당 `onreconnecting` 전환 하는 대신 콜백을 `Disconnected` 상태 및 트리거 해당 `onclose` 콜백을 등을 `HubConnection`자동 다시 연결 하지 않고 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-175">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="28291-176">이 연결 손실 되었음을 사용자에 게 경고 하는 데 UI 요소를 사용 하지 않도록 설정할 수 있는 기회를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-176">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="28291-177">클라이언트는 먼저 4 회 성공적으로 다시 연결 하는 경우는 `HubConnection` 으로 다시 전환 됩니다.는 `Connected` 상태 및 실행 해당 `onreconnected` 콜백 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-177">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="28291-178">이 연결이 다시 설정 하는 사용자에 게 알리기 기회를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-178">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="28291-179">연결 서버에 완전히 새로운 표시 하므로 새 `connectionId` 에 제공 됩니다는 `onreconnected` 콜백 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-179">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="28291-180">`onreconnected` 콜백의 `connectionId` 매개 변수는 정의 되지 경우 합니다 `HubConnection` 하도록 구성 된 [협상 건너뛸](xref:signalr/configuration#configure-client-options)합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-180">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="28291-181">`withAutomaticReconnect()` 구성지 않습니다는 `HubConnection` 시작 실패를 수동으로 처리 해야 하므로 초기 시작 실패를 다시 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-181">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="28291-182">클라이언트 하지 해당 처음 네 개의 시도 내에서 성공적으로 다시 연결 하는 경우는 `HubConnection` 전환 됩니다 합니다 `Disconnected` 상태 및 실행 해당 [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) 콜백 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-182">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="28291-183">이 연결이 영구적으로 손실 되었으며 페이지를 새로 고치는 것이 좋습니다 사용자에 게 알리기 기회를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-183">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="28291-184">사용자 지정 연결을 끊기 전에 다시 연결 시도 횟수를 구성 하거나 다시 연결 시간을 변경 하려면 `withAutomaticReconnect` 각 다시 연결 시도 시작 하기 전에 대기할 밀리초의 지연을 나타내는 숫자 배열을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-184">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="28291-185">앞의 예제 구성는 `HubConnection` 연결이 끊어진 후에 즉시 다시 연결 시도 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-185">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="28291-186">기본 구성에는이 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="28291-186">This is also true for the default configuration.</span></span>

<span data-ttu-id="28291-187">첫 번째 다시 연결 시도가 실패 하면 기본 구성이 있는 것 처럼 2 초 대기 하는 대신 두 번째 다시 연결 시도가 즉시 시작도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="28291-187">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="28291-188">두 번째 다시 연결 시도가 실패 하면 세 번째 다시 연결 시도 기본 구성 등 다시 10 초 후에 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="28291-188">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="28291-189">사용자 지정 동작은 다음 달라 지므로 다시 기본 동작에서 중지 하 여 세 번째 다시 연결 시도 하나를 사용 하는 대신 오류 후 더 시도 기본 구성이 있는 것 처럼 다른 30 초 후에 다시 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-189">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="28291-190">자동의 수와 타이밍을 보다 잘 제어할 다시 시도 하려는 경우 `withAutomaticReconnect` 구현 하는 개체를 허용 합니다 `IReconnectPolicy` 라는 단일 메서드가 있는 인터페이스 `nextRetryDelayInMilliseconds`합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-190">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IReconnectPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="28291-191">`nextRetryDelayInMilliseconds` 두 개의 인수로 `previousRetryCount` 및 `elapsedMilliseconds`, 두 번호는 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-191">`nextRetryDelayInMilliseconds` takes two arguments, `previousRetryCount` and `elapsedMilliseconds`, which are both numbers.</span></span> <span data-ttu-id="28291-192">첫 번째 다시 연결 시도 하기 전에 둘 다 `previousRetryCount` 고 `elapsedMilliseconds` 0이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="28291-192">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero.</span></span> <span data-ttu-id="28291-193">각 실패 한 시도 후 `previousRetryCount` 1 씩 증가 하 고 `elapsedMilliseconds` 밀리초에서 지금 다시 연결 하는 데 걸린 시간을 반영 하도록 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="28291-193">After each failed retry attempt, `previousRetryCount` will be incremented by one and `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds.</span></span>

<span data-ttu-id="28291-194">`nextRetryDelayInMilliseconds` 두 숫자 밀리초 수를 나타내는 다음 다시 연결 시도 하기 전에 대기할 반환 해야 합니다 또는 `null` 경우는 `HubConnection` 다시 연결을 중지 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-194">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: (previousRetryCount, elapsedMilliseconds) => {
            if (elapsedMilliseconds < 60000) {
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

<span data-ttu-id="28291-195">또는 클라이언트에서 설명한 것 처럼 수동으로 다시 연결 하는 코드를 작성할 수 있습니다 [수동으로 다시](#manually-reconnect)입니다.</span><span class="sxs-lookup"><span data-stu-id="28291-195">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="28291-196">수동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="28291-196">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="28291-197">3\.0 이전 SignalR에 대 한 JavaScript 클라이언트 하지 자동으로 다시 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-197">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="28291-198">클라이언트에 수동으로 다시 연결 하는 코드를 작성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="28291-198">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="28291-199">다음 코드는 일반적인 수동 다시 연결 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="28291-199">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="28291-200">함수 (이 경우에 `start` 함수) 연결을 시작 하기 위해 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="28291-200">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="28291-201">호출 된 `start` 함수에서 연결의 `onclose` 이벤트 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="28291-201">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="28291-202">실제 구현을는 지 수 백오프를 사용 하거나 포기 하기 전에 지정 된 횟수를 다시 시도 하세요.</span><span class="sxs-lookup"><span data-stu-id="28291-202">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28291-203">추가 자료</span><span class="sxs-lookup"><span data-stu-id="28291-203">Additional resources</span></span>

* [<span data-ttu-id="28291-204">JavaScript API 참조</span><span class="sxs-lookup"><span data-stu-id="28291-204">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="28291-205">JavaScript 자습서</span><span class="sxs-lookup"><span data-stu-id="28291-205">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="28291-206">WebPack 및 TypeScript 자습서</span><span class="sxs-lookup"><span data-stu-id="28291-206">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="28291-207">허브</span><span class="sxs-lookup"><span data-stu-id="28291-207">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="28291-208">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="28291-208">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="28291-209">Azure에 게시하기</span><span class="sxs-lookup"><span data-stu-id="28291-209">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="28291-210">원본 간 요청 (CORS)</span><span class="sxs-lookup"><span data-stu-id="28291-210">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="28291-211">Azure SignalR Service 서버 리스 설명서</span><span class="sxs-lookup"><span data-stu-id="28291-211">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
