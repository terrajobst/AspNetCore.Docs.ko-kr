---
title: ASP.NET Core에서 WebSocket 지원
author: rick-anderson
description: ASP.NET Core에서 Websocket을 시작하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/17/2019
uid: fundamentals/websockets
ms.openlocfilehash: 1b62dc91453437518e4b8f6f8dd0915977130766
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64888248"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="b4895-103">ASP.NET Core에서 WebSocket 지원</span><span class="sxs-lookup"><span data-stu-id="b4895-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="b4895-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="b4895-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="b4895-105">본문에서는 ASP.NET Core에서 Websocket을 사용하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="b4895-106">[WebSocket](https://wikipedia.org/wiki/WebSocket)([RFC 6455](https://tools.ietf.org/html/rfc6455))은 TCP 연결을 통해 지속적인 양방향 통신 채널을 사용할 수 있도록 해주는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="b4895-107">채팅, 대시보드 및 게임 앱 등 신속한 실시간 통신을 활용하는 앱에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="b4895-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b4895-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="b4895-109">자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b4895-109">See the [Next steps](#next-steps) section for more information.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b4895-110">전제 조건</span><span class="sxs-lookup"><span data-stu-id="b4895-110">Prerequisites</span></span>

* <span data-ttu-id="b4895-111">ASP.NET Core 1.1 이상</span><span class="sxs-lookup"><span data-stu-id="b4895-111">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="b4895-112">ASP.NET Core를 지원하는 모든 OS:</span><span class="sxs-lookup"><span data-stu-id="b4895-112">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="b4895-113">Windows 7/Windows Server 2008 이상</span><span class="sxs-lookup"><span data-stu-id="b4895-113">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="b4895-114">Linux</span><span class="sxs-lookup"><span data-stu-id="b4895-114">Linux</span></span>
  * <span data-ttu-id="b4895-115">macOS</span><span class="sxs-lookup"><span data-stu-id="b4895-115">macOS</span></span>
  
* <span data-ttu-id="b4895-116">IIS가 있는 Windows에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="b4895-116">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="b4895-117">Windows 8 / Windows Server 2012 이상</span><span class="sxs-lookup"><span data-stu-id="b4895-117">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="b4895-118">IIS 8 / IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="b4895-118">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="b4895-119">WebSockets를 활성화해야 합니다([IIS/IIS Express 지원](#iisiis-express-support) 섹션 참조).</span><span class="sxs-lookup"><span data-stu-id="b4895-119">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="b4895-120">앱이 [HTTP.sys](xref:fundamentals/servers/httpsys)에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="b4895-120">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="b4895-121">Windows 8 / Windows Server 2012 이상</span><span class="sxs-lookup"><span data-stu-id="b4895-121">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="b4895-122">지원되는 브라우저는 https://caniuse.com/#feat=websockets를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b4895-122">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="when-to-use-websockets"></a><span data-ttu-id="b4895-123">WebSockets를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="b4895-123">When to use WebSockets</span></span>

<span data-ttu-id="b4895-124">WebSockets를 사용하여 소켓 연결로 직접 작업하세요.</span><span class="sxs-lookup"><span data-stu-id="b4895-124">Use WebSockets to work directly with a socket connection.</span></span> <span data-ttu-id="b4895-125">예를 들어, 실시간 게임에서 가능한 최상의 성능을 얻으려면 WebSockets를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="b4895-125">For example, use WebSockets for the best possible performance with a real-time game.</span></span>

<span data-ttu-id="b4895-126">[ASP.NET Core SignalR](xref:signalr/introduction)은 앱에 실시간 웹 기능을 추가하는 것을 간소화하는 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-126">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="b4895-127">가능하면 Websocket을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-127">It uses WebSockets whenever possible.</span></span>

## <a name="how-to-use-websockets"></a><span data-ttu-id="b4895-128">WebSocket 사용 방법</span><span class="sxs-lookup"><span data-stu-id="b4895-128">How to use WebSockets</span></span>

* <span data-ttu-id="b4895-129">[Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-129">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>
* <span data-ttu-id="b4895-130">미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-130">Configure the middleware.</span></span>
* <span data-ttu-id="b4895-131">WebSocket 요청을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-131">Accept WebSocket requests.</span></span>
* <span data-ttu-id="b4895-132">메시지를 전송 및 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-132">Send and receive messages.</span></span>

### <a name="configure-the-middleware"></a><span data-ttu-id="b4895-133">미들웨어 구성하기</span><span class="sxs-lookup"><span data-stu-id="b4895-133">Configure the middleware</span></span>

<span data-ttu-id="b4895-134">`Startup` 클래스의 `Configure` 메서드에 WebSockets 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-134">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b4895-135">이때 다음과 같은 설정을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-135">The following settings can be configured:</span></span>

* <span data-ttu-id="b4895-136">`KeepAliveInterval` - 프록시가 연결을 유지할 수 있도록 클라이언트로 "핑(ping)" 프레임을 전송하는 빈도입니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-136">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="b4895-137">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-137">The default is two minutes.</span></span>
* <span data-ttu-id="b4895-138">`ReceiveBufferSize` - 데이터 수신에 사용되는 버퍼의 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-138">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="b4895-139">고급 사용자는 데이터 크기에 따라 성능 튜닝이 필요할 경우 이 값을 변경해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-139">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="b4895-140">기본값은 4KB입니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-140">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b4895-141">이때 다음과 같은 설정을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-141">The following settings can be configured:</span></span>

* <span data-ttu-id="b4895-142">`KeepAliveInterval` - 프록시가 연결을 유지할 수 있도록 클라이언트로 "핑(ping)" 프레임을 전송하는 빈도입니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-142">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="b4895-143">기본값은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-143">The default is two minutes.</span></span>
* <span data-ttu-id="b4895-144">`ReceiveBufferSize` - 데이터 수신에 사용되는 버퍼의 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-144">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="b4895-145">고급 사용자는 데이터 크기에 따라 성능 튜닝이 필요할 경우 이 값을 변경해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-145">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="b4895-146">기본값은 4KB입니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-146">The default is 4 KB.</span></span>
* <span data-ttu-id="b4895-147">`AllowedOrigins` - WebSocket 요청에 허용된 원본 헤더 값의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-147">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="b4895-148">기본적으로 모든 원본이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-148">By default, all origins are allowed.</span></span> <span data-ttu-id="b4895-149">자세한 내용은 아래의 "WebSocket 원본 제한"을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b4895-149">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

::: moniker-end

### <a name="accept-websocket-requests"></a><span data-ttu-id="b4895-150">WebSocket 요청 수락하기</span><span class="sxs-lookup"><span data-stu-id="b4895-150">Accept WebSocket requests</span></span>

<span data-ttu-id="b4895-151">요청 수명 주기의 뒷부분에서 (예를 들어, `Configure` 메서드나 MVC 액션의 뒷부분에서) WebSocket 요청 여부를 확인하고 WebSocket 요청을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-151">Somewhere later in the request life cycle (later in the `Configure` method or in an MVC action, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="b4895-152">다음 예제는 `Configure` 메서드의 뒷부분에서 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-152">The following example is from later in the `Configure` method:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

::: moniker-end

<span data-ttu-id="b4895-153">WebSocket 요청은 모든 URL을 통해서 전달될 수 있지만, 이 예제 코드에서는 `/ws` 에 대한 요청만 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-153">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

### <a name="send-and-receive-messages"></a><span data-ttu-id="b4895-154">메시지 보내기 및 받기</span><span class="sxs-lookup"><span data-stu-id="b4895-154">Send and receive messages</span></span>

<span data-ttu-id="b4895-155">`AcceptWebSocketAsync` 메서드는 TCP 연결을 WebSocket 연결로 업그레이드하고 [WebSocket](/dotnet/core/api/system.net.websockets.websocket) 개체를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-155">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="b4895-156">`WebSocket` 개체를 사용하여 메시지를 보내고 받습니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-156">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="b4895-157">앞에서 살펴본 WebSocket 요청을 수락하는 코드는 `WebSocket` 개체를 `Echo` 메서드로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-157">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="b4895-158">코드는 메시지를 수신하고 동일한 메시지를 즉시 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-158">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="b4895-159">메시지는 클라이언트가 연결을 닫을 때까지 루프에서 보내고 받습니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-159">Messages are sent and received in a loop until the client closes the connection:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=Echo)]

::: moniker-end

<span data-ttu-id="b4895-160">루프가 시작되기 전에 WebSocket 연결을 수락하면 미들웨어 파이프라인이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-160">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="b4895-161">그리고 소켓을 닫으면 파이프라인이 풀립니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-161">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="b4895-162">즉, 요청은 WebSocket이 수락될 때 파이프라인에서 앞으로 이동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-162">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="b4895-163">루프가 완료되고 소켓이 닫히면 요청이 파이프라인 백업을 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-163">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="handle-client-disconnects"></a><span data-ttu-id="b4895-164">클라이언트 연결 끊김 처리</span><span class="sxs-lookup"><span data-stu-id="b4895-164">Handle client disconnects</span></span>

<span data-ttu-id="b4895-165">연결이 끊어져서 클라이언트 연결이 해제된 경우 서버에 자동으로 알림이 전송되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-165">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="b4895-166">클라이언트에서 연결 끊김 메시지를 보내는 경우에만 서버에서 연결 끊김 메시지를 받습니다. 인터넷 연결이 끊긴 경우 메시지가 전송되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-166">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="b4895-167">그런 경우 조치를 취하려면 특정 시간 내에서 클라이언트에서 아무런 메시지도 받지 않은 후 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-167">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="b4895-168">클라이언트가 항상 메시지를 보내지 않고 연결이 유휴 상태가 되기 때문에 시간 제한을 원하지 않는 경우 클라이언트에서 타이머를 사용하여 X초마다 ping 메시지를 보내도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-168">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="b4895-169">서버에서 메시지가 이전 메시지 후 2\*X초 이내에 도착하지 않는 경우 연결을 종료하고 클라이언트 연결이 끊겼음을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-169">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="b4895-170">ping 메시지를 지연시킬 수 있는 네트워크 지연에 대해 추가 시간을 두려면 예상 시간 간격의 2배 동안 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-170">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

### <a name="websocket-origin-restriction"></a><span data-ttu-id="b4895-171">WebSocket 원본 제한</span><span class="sxs-lookup"><span data-stu-id="b4895-171">WebSocket origin restriction</span></span>

<span data-ttu-id="b4895-172">CORS에서 제공하는 보호 기능은 WebSocket에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-172">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="b4895-173">브라우저는 다음을 수행하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b4895-173">Browsers do **not**:</span></span>

* <span data-ttu-id="b4895-174">CORS pre-flight 요청을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-174">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="b4895-175">WebSocket 요청을 생성할 때 `Access-Control` 헤더에 지정된 제한 사항을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-175">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="b4895-176">그러나 브라우저는 WebSocket 요청을 발급할 때 `Origin` 헤더를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-176">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="b4895-177">애플리케이션은 예상된 원본에서 제공하는 WebSocket만 허용되도록 이러한 헤더의 유효성을 검사하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-177">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="b4895-178">"https://server.com"에서 서버를 호스팅하고 "https://client.com"에서 클라이언트를 호스팅하는 경우 `AllowedOrigins` 목록에 "https://client.com"을 추가하여 WebSocket을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-178">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="b4895-179">`Origin` 헤더는 클라이언트에 의해 제어되며 `Referer` 헤더와 마찬가지로 위조될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-179">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="b4895-180">이러한 헤더를 인증 메커니즘으로 사용하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="b4895-180">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="b4895-181">IIS/IIS Express 지원</span><span class="sxs-lookup"><span data-stu-id="b4895-181">IIS/IIS Express support</span></span>

<span data-ttu-id="b4895-182">IIS/IIS Express 8 이상이 있는 Windows Server 2012 이상 및 Windows 8 이상에서는 WebSocket 프로토콜을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-182">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="b4895-183">IIS Express를 사용할 때 Websocket이 항상 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-183">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="b4895-184">IIS에서 Websocket 사용</span><span class="sxs-lookup"><span data-stu-id="b4895-184">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="b4895-185">Windows Server 2012 이상에서 WebSocket 프로토콜을 지원하려면:</span><span class="sxs-lookup"><span data-stu-id="b4895-185">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="b4895-186">IIS Express를 사용할 때 이러한 단계가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-186">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="b4895-187">**관리** 메뉴 또는 **서버 관리자**의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-187">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="b4895-188">**역할 기반 또는 기능 기반 설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-188">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="b4895-189">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-189">Select **Next**.</span></span>
1. <span data-ttu-id="b4895-190">적절한 서버를 선택합니다(로컬 서버가 기본적으로 선택됨).</span><span class="sxs-lookup"><span data-stu-id="b4895-190">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="b4895-191">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-191">Select **Next**.</span></span>
1. <span data-ttu-id="b4895-192">**역할** 트리에서 **Web Server(IIS)** 를 확장하고 **Web Server**를 확장한 다음, **애플리케이션 개발**을 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-192">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="b4895-193">**WebSocket 프로토콜**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-193">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="b4895-194">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-194">Select **Next**.</span></span>
1. <span data-ttu-id="b4895-195">추가 기능이 필요 없는 경우 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-195">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="b4895-196">**설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-196">Select **Install**.</span></span>
1. <span data-ttu-id="b4895-197">설치가 완료되면 **닫기**를 선택하여 마법사를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-197">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="b4895-198">Windows 8 이상에서 WebSocket 프로토콜을 지원하려면:</span><span class="sxs-lookup"><span data-stu-id="b4895-198">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="b4895-199">IIS Express를 사용할 때 이러한 단계가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-199">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="b4895-200">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-200">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="b4895-201">다음 노드를 엽니다. **인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능**.</span><span class="sxs-lookup"><span data-stu-id="b4895-201">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="b4895-202">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-202">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="b4895-203">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-203">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="b4895-204">Node.js에서 socket.io를 사용할 때 WebSocket 비활성화</span><span class="sxs-lookup"><span data-stu-id="b4895-204">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="b4895-205">[Node.js](https://nodejs.org/)의 [socket.io](https://socket.io/)에서 WebSocket 지원을 사용하는 경우 *web.config* 또는 *applicationHost.config*의 `webSocket` 요소를 사용하여 기본 IIS WebSocket 모듈을 비활성화합니다. 이 단계를 수행하지 않으면 IIS WebSocket 모듈이 Node.js 및 앱이 아닌 WebSocket 통신을 처리하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-205">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="next-steps"></a><span data-ttu-id="b4895-206">다음 단계</span><span class="sxs-lookup"><span data-stu-id="b4895-206">Next steps</span></span>

<span data-ttu-id="b4895-207">이 아티클과 함께 제공되는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples)은 에코 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-207">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="b4895-208">WebSocket 연결을 생성하는 웹 페이지가 제공되며 서버는 수신한 메시지를 클라이언트로 재전송합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-208">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="b4895-209">명령 프롬프트에서 앱을 실행하고(IIS Express가 있는 Visual Studio에서 실행되도록 설정되지 않음) http://localhost:5000으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-209">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="b4895-210">웹 페이지 왼쪽 상단에 연결 상태가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-210">The web page shows the connection status in the upper left:</span></span>

![웹 페이지의 초기 상태](websockets/_static/start.png)

<span data-ttu-id="b4895-212">**Connect** 를 선택해서 지정한 URL로 WebSocket 요청을 전송합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-212">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="b4895-213">그리고 테스트 메시지를 입력한 다음 **Send** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-213">Enter a test message and select **Send**.</span></span> <span data-ttu-id="b4895-214">테스트를 모두 마쳤으면 **Close Socket** 을 선택하십시오.</span><span class="sxs-lookup"><span data-stu-id="b4895-214">When done, select **Close Socket**.</span></span> <span data-ttu-id="b4895-215">**Communication Log** 섹션에는 각각의 열기, 전송 및 닫기 동작이 발생한 순서대로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="b4895-215">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![웹 페이지의 초기 상태](websockets/_static/end.png)
