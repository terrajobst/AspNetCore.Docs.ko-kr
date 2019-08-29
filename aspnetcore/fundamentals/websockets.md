---
title: ASP.NET Core에서 WebSocket 지원
author: rick-anderson
description: ASP.NET Core에서 Websocket을 시작하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/10/2019
uid: fundamentals/websockets
ms.openlocfilehash: 5d4d9b02bd45e6650aa56448a3663cad06b3b45e
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975452"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="2621b-103">ASP.NET Core에서 WebSocket 지원</span><span class="sxs-lookup"><span data-stu-id="2621b-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="2621b-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="2621b-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="2621b-105">본문에서는 ASP.NET Core에서 Websocket을 사용하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="2621b-106">[WebSocket](https://wikipedia.org/wiki/WebSocket)([RFC 6455](https://tools.ietf.org/html/rfc6455))은 TCP 연결을 통해 지속적인 양방향 통신 채널을 사용할 수 있도록 해주는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="2621b-107">채팅, 대시보드 및 게임 앱 등 신속한 실시간 통신을 활용하는 앱에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="2621b-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2621b-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="2621b-109">[실행 방법](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="2621b-109">[How to run](#sample-app).</span></span>

## <a name="signalr"></a><span data-ttu-id="2621b-110">SignalR</span><span class="sxs-lookup"><span data-stu-id="2621b-110">SignalR</span></span>

<span data-ttu-id="2621b-111">[ASP.NET Core SignalR](xref:signalr/introduction)은 앱에 실시간 웹 기능을 추가하는 것을 간소화하는 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-111">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="2621b-112">가능하면 Websocket을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-112">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="2621b-113">대부분의 애플리케이션의 경우 원시 WebSockets보다 SignalR을 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-113">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="2621b-114">SignalR은 WebSockets를 사용할 수 없는 환경에 대한 전송 대체(fallback)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-114">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="2621b-115">간단한 원격 프로시저 호출 앱 모델도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-115">It also provides a simple remote procedure call app model.</span></span> <span data-ttu-id="2621b-116">그리고 대부분의 시나리오에서 SignalR은 원시 WebSockets 사용과 비교할 때 큰 성능상의 단점이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-116">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2621b-117">전제 조건</span><span class="sxs-lookup"><span data-stu-id="2621b-117">Prerequisites</span></span>

* <span data-ttu-id="2621b-118">ASP.NET Core 1.1 이상</span><span class="sxs-lookup"><span data-stu-id="2621b-118">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="2621b-119">ASP.NET Core를 지원하는 모든 OS:</span><span class="sxs-lookup"><span data-stu-id="2621b-119">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="2621b-120">Windows 7/Windows Server 2008 이상</span><span class="sxs-lookup"><span data-stu-id="2621b-120">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="2621b-121">Linux</span><span class="sxs-lookup"><span data-stu-id="2621b-121">Linux</span></span>
  * <span data-ttu-id="2621b-122">macOS</span><span class="sxs-lookup"><span data-stu-id="2621b-122">macOS</span></span>
  
* <span data-ttu-id="2621b-123">IIS가 있는 Windows에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="2621b-123">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="2621b-124">Windows 8 / Windows Server 2012 이상</span><span class="sxs-lookup"><span data-stu-id="2621b-124">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="2621b-125">IIS 8 / IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="2621b-125">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="2621b-126">WebSockets를 활성화해야 합니다([IIS/IIS Express 지원](#iisiis-express-support) 섹션 참조).</span><span class="sxs-lookup"><span data-stu-id="2621b-126">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="2621b-127">앱이 [HTTP.sys](xref:fundamentals/servers/httpsys)에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="2621b-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="2621b-128">Windows 8 / Windows Server 2012 이상</span><span class="sxs-lookup"><span data-stu-id="2621b-128">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="2621b-129">지원되는 브라우저는 https://caniuse.com/#feat=websockets 를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2621b-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a><span data-ttu-id="2621b-130">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="2621b-130">NuGet package</span></span>

<span data-ttu-id="2621b-131">[Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-131">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>

::: moniker-end

## <a name="configure-the-middleware"></a><span data-ttu-id="2621b-132">미들웨어 구성하기</span><span class="sxs-lookup"><span data-stu-id="2621b-132">Configure the middleware</span></span>


<span data-ttu-id="2621b-133">`Startup` 클래스의 `Configure` 메서드에 WebSockets 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-133">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2621b-134">이때 다음과 같은 설정을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-134">The following settings can be configured:</span></span>

* <span data-ttu-id="2621b-135">`KeepAliveInterval` - 프록시가 연결을 유지할 수 있도록 클라이언트로 "핑(ping)" 프레임을 전송하는 빈도입니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-135">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="2621b-136">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-136">The default is two minutes.</span></span>
* <span data-ttu-id="2621b-137">`ReceiveBufferSize` - 데이터 수신에 사용되는 버퍼의 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-137">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="2621b-138">고급 사용자는 데이터 크기에 따라 성능 튜닝이 필요할 경우 이 값을 변경해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-138">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="2621b-139">기본값은 4KB입니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-139">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2621b-140">이때 다음과 같은 설정을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-140">The following settings can be configured:</span></span>

* <span data-ttu-id="2621b-141">`KeepAliveInterval` - 프록시가 연결을 유지할 수 있도록 클라이언트로 "핑(ping)" 프레임을 전송하는 빈도입니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-141">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="2621b-142">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-142">The default is two minutes.</span></span>
* <span data-ttu-id="2621b-143">`ReceiveBufferSize` - 데이터 수신에 사용되는 버퍼의 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-143">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="2621b-144">고급 사용자는 데이터 크기에 따라 성능 튜닝이 필요할 경우 이 값을 변경해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-144">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="2621b-145">기본값은 4KB입니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-145">The default is 4 KB.</span></span>
* <span data-ttu-id="2621b-146">`AllowedOrigins` - WebSocket 요청에 허용된 원본 헤더 값의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-146">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="2621b-147">기본적으로 모든 원본이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-147">By default, all origins are allowed.</span></span> <span data-ttu-id="2621b-148">자세한 내용은 아래의 "WebSocket 원본 제한"을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2621b-148">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="2621b-149">WebSocket 요청 수락하기</span><span class="sxs-lookup"><span data-stu-id="2621b-149">Accept WebSocket requests</span></span>

<span data-ttu-id="2621b-150">요청 수명 주기의 뒷부분에서(예를 들어, `Configure` 메서드나 작업 메서드 뒷부분에서) WebSocket 요청 여부를 확인하고 WebSocket 요청을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-150">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="2621b-151">다음 예제는 `Configure` 메서드의 뒷부분에서 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-151">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="2621b-152">WebSocket 요청은 모든 URL을 통해서 전달될 수 있지만, 이 예제 코드에서는 `/ws` 에 대한 요청만 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-152">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="2621b-153">WebSocket을 사용하는 경우 연결 기간 동안 미들웨어 파이프라인이 계속 실행되도록 **해야 합니다**.</span><span class="sxs-lookup"><span data-stu-id="2621b-153">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="2621b-154">미들웨어 파이프라인 종료 후 WebSocket 메시지를 보내거나 받으려고 하면 다음과 같은 예외가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-154">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="2621b-155">백그라운드 서비스를 사용하여 WebSocket에 데이터를 쓰는 경우 미들웨어 파이프라인이 계속 실행되도록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-155">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="2621b-156"><xref:System.Threading.Tasks.TaskCompletionSource%601>을 사용하여 이 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-156">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="2621b-157">`TaskCompletionSource`를 백그라운드 서비스에 전달하고 WebSocket 사용을 완료하면 <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A>를 호출하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-157">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="2621b-158">그런 다음, `await`는 요청 중에 다음 예제와 같이 <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-158">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
<span data-ttu-id="2621b-159">작업 메서드에서 너무 빨리 반환하면 WebSocket 닫힌 예외가 발생할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-159">The WebSocket closed exception can also happen if you return too soon from an action method.</span></span> <span data-ttu-id="2621b-160">작업 메서드에서 소켓을 수락하는 경우, 작업 메서드에서 반환하기 전에 소켓을 사용하는 코드가 완료될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-160">If you accept a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="2621b-161">소켓이 완료될 때까지 대기하려면 `Task.Wait()`, `Task.Result` 또는 유사한 차단 호출을 사용하지 마세요. 심각한 스레드 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-161">Never use `Task.Wait()`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="2621b-162">항상 `await`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-162">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="2621b-163">메시지 보내기 및 받기</span><span class="sxs-lookup"><span data-stu-id="2621b-163">Send and receive messages</span></span>

<span data-ttu-id="2621b-164">`AcceptWebSocketAsync` 메서드는 TCP 연결을 WebSocket 연결로 업그레이드하고 [WebSocket](/dotnet/core/api/system.net.websockets.websocket) 개체를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-164">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="2621b-165">`WebSocket` 개체를 사용하여 메시지를 보내고 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-165">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="2621b-166">앞에서 살펴본 WebSocket 요청을 수락하는 코드는 `WebSocket` 개체를 `Echo` 메서드로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-166">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="2621b-167">코드는 메시지를 수신하고 동일한 메시지를 즉시 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-167">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="2621b-168">메시지는 클라이언트가 연결을 닫을 때까지 루프에서 보내고 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-168">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="2621b-169">루프가 시작되기 전에 WebSocket 연결을 수락하면 미들웨어 파이프라인이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-169">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="2621b-170">그리고 소켓을 닫으면 파이프라인이 풀립니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-170">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="2621b-171">즉, 요청은 WebSocket이 수락될 때 파이프라인에서 앞으로 이동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-171">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="2621b-172">루프가 완료되고 소켓이 닫히면 요청이 파이프라인 백업을 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-172">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="2621b-173">클라이언트 연결 끊김 처리</span><span class="sxs-lookup"><span data-stu-id="2621b-173">Handle client disconnects</span></span>

<span data-ttu-id="2621b-174">연결이 끊어져서 클라이언트 연결이 해제된 경우 서버에 자동으로 알림이 전송되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-174">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="2621b-175">클라이언트에서 연결 끊김 메시지를 보내는 경우에만 서버에서 연결 끊김 메시지를 받습니다. 인터넷 연결이 끊긴 경우 메시지가 전송되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-175">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="2621b-176">그런 경우 조치를 취하려면 특정 시간 내에서 클라이언트에서 아무런 메시지도 받지 않은 후 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-176">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="2621b-177">클라이언트가 항상 메시지를 보내지 않고 연결이 유휴 상태가 되기 때문에 시간 제한을 원하지 않는 경우 클라이언트에서 타이머를 사용하여 X초마다 ping 메시지를 보내도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-177">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="2621b-178">서버에서 메시지가 이전 메시지 후 2\*X초 이내에 도착하지 않는 경우 연결을 종료하고 클라이언트 연결이 끊겼음을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-178">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="2621b-179">ping 메시지를 지연시킬 수 있는 네트워크 지연에 대해 추가 시간을 두려면 예상 시간 간격의 2배 동안 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-179">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="2621b-180">WebSocket 원본 제한</span><span class="sxs-lookup"><span data-stu-id="2621b-180">WebSocket origin restriction</span></span>

<span data-ttu-id="2621b-181">CORS에서 제공하는 보호 기능은 WebSocket에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-181">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="2621b-182">브라우저는 다음을 수행하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="2621b-182">Browsers do **not**:</span></span>

* <span data-ttu-id="2621b-183">CORS pre-flight 요청을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-183">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="2621b-184">WebSocket 요청을 생성할 때 `Access-Control` 헤더에 지정된 제한 사항을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-184">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="2621b-185">그러나 브라우저는 WebSocket 요청을 발급할 때 `Origin` 헤더를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-185">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="2621b-186">애플리케이션은 예상된 원본에서 제공하는 WebSocket만 허용되도록 이러한 헤더의 유효성을 검사하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-186">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="2621b-187">"https://server.com "에서 서버를 호스팅하고 "https://client.com"에서 클라이언트를 호스팅하는 경우 `AllowedOrigins` 목록에 "https://client.com"을 추가하여 WebSocket을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-187">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="2621b-188">`Origin` 헤더는 클라이언트에 의해 제어되며 `Referer` 헤더와 마찬가지로 위조될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-188">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="2621b-189">이러한 헤더를 인증 메커니즘으로 사용하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="2621b-189">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="2621b-190">IIS/IIS Express 지원</span><span class="sxs-lookup"><span data-stu-id="2621b-190">IIS/IIS Express support</span></span>

<span data-ttu-id="2621b-191">IIS/IIS Express 8 이상이 있는 Windows Server 2012 이상 및 Windows 8 이상에서는 WebSocket 프로토콜을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-191">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="2621b-192">IIS Express를 사용할 때 Websocket이 항상 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-192">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="2621b-193">IIS에서 Websocket 사용</span><span class="sxs-lookup"><span data-stu-id="2621b-193">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="2621b-194">Windows Server 2012 이상에서 WebSocket 프로토콜을 지원하려면:</span><span class="sxs-lookup"><span data-stu-id="2621b-194">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="2621b-195">IIS Express를 사용할 때 이러한 단계가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-195">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="2621b-196">**관리** 메뉴 또는 **서버 관리자**의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-196">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="2621b-197">**역할 기반 또는 기능 기반 설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-197">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="2621b-198">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-198">Select **Next**.</span></span>
1. <span data-ttu-id="2621b-199">적절한 서버를 선택합니다(로컬 서버가 기본적으로 선택됨).</span><span class="sxs-lookup"><span data-stu-id="2621b-199">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="2621b-200">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-200">Select **Next**.</span></span>
1. <span data-ttu-id="2621b-201">**역할** 트리에서 **Web Server(IIS)** 를 확장하고 **Web Server**를 확장한 다음, **애플리케이션 개발**을 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-201">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="2621b-202">**WebSocket 프로토콜**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-202">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="2621b-203">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-203">Select **Next**.</span></span>
1. <span data-ttu-id="2621b-204">추가 기능이 필요 없는 경우 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-204">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="2621b-205">**설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-205">Select **Install**.</span></span>
1. <span data-ttu-id="2621b-206">설치가 완료되면 **닫기**를 선택하여 마법사를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-206">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="2621b-207">Windows 8 이상에서 WebSocket 프로토콜을 지원하려면:</span><span class="sxs-lookup"><span data-stu-id="2621b-207">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="2621b-208">IIS Express를 사용할 때 이러한 단계가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-208">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="2621b-209">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-209">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="2621b-210">다음 노드를 엽니다. **인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능**.</span><span class="sxs-lookup"><span data-stu-id="2621b-210">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="2621b-211">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-211">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="2621b-212">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-212">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="2621b-213">Node.js에서 socket.io를 사용할 때 WebSocket 비활성화</span><span class="sxs-lookup"><span data-stu-id="2621b-213">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="2621b-214">[Node.js](https://nodejs.org/)의 [socket.io](https://socket.io/)에서 WebSocket 지원을 사용하는 경우 *web.config* 또는 *applicationHost.config*의 `webSocket` 요소를 사용하여 기본 IIS WebSocket 모듈을 비활성화합니다. 이 단계를 수행하지 않으면 IIS WebSocket 모듈이 Node.js 및 앱이 아닌 WebSocket 통신을 처리하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-214">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="2621b-215">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="2621b-215">Sample app</span></span>

<span data-ttu-id="2621b-216">이 아티클과 함께 제공되는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples)은 에코 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-216">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="2621b-217">WebSocket 연결을 생성하는 웹 페이지가 제공되며 서버는 수신한 메시지를 클라이언트로 재전송합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-217">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="2621b-218">명령 프롬프트에서 앱을 실행하고(IIS Express가 있는 Visual Studio에서 실행되도록 설정되지 않음) http://localhost:5000 으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-218">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="2621b-219">웹 페이지 왼쪽 상단에 연결 상태가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-219">The web page shows the connection status in the upper left:</span></span>

![웹 페이지의 초기 상태](websockets/_static/start.png)

<span data-ttu-id="2621b-221">**Connect** 를 선택해서 지정한 URL로 WebSocket 요청을 전송합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-221">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="2621b-222">그리고 테스트 메시지를 입력한 다음 **Send** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-222">Enter a test message and select **Send**.</span></span> <span data-ttu-id="2621b-223">테스트를 모두 마쳤으면 **Close Socket** 을 선택하십시오.</span><span class="sxs-lookup"><span data-stu-id="2621b-223">When done, select **Close Socket**.</span></span> <span data-ttu-id="2621b-224">**Communication Log** 섹션에는 각각의 열기, 전송 및 닫기 동작이 발생한 순서대로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="2621b-224">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![웹 페이지의 초기 상태](websockets/_static/end.png)

