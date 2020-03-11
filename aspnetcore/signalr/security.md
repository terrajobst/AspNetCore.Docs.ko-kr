---
title: ASP.NET Core SignalR의 보안 고려 사항
author: bradygaster
description: ASP.NET Core SignalR에서 인증 및 권한 부여를 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: f92b56132d0fa55665568416d0760430cb698f8b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655155"
---
# <a name="security-considerations-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="7addc-103">ASP.NET Core SignalR의 보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7addc-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="7addc-104">[Andrew Stanton-간호사](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="7addc-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="7addc-105">이 문서에서는 SignalR보안 설정에 대 한 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="7addc-106">크로스-원본 자원 공유</span><span class="sxs-lookup"><span data-stu-id="7addc-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="7addc-107">[CORS (크로스-원본 자원 공유)](https://www.w3.org/TR/cors/) 를 사용 하 여 브라우저에서 원본 간 SignalR 연결을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="7addc-108">JavaScript 코드가 SignalR 앱과 다른 도메인에서 호스트 되는 경우 JavaScript가 SignalR 앱에 연결할 수 있도록 [CORS 미들웨어](xref:security/cors) 를 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="7addc-109">신뢰하거나 제어할 수 있는 도메인의 교차 원본 요청만 허용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="7addc-110">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-110">For example:</span></span>

* <span data-ttu-id="7addc-111">사이트가 `http://www.example.com`에서 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="7addc-112">SignalR 앱이 `http://signalr.example.com`에서 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="7addc-113">SignalR 앱에서 원본 `www.example.com`만 허용 하도록 CORS를 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="7addc-114">CORS를 구성 하는 방법에 대 한 자세한 내용은 [cors (원본 간 요청) 사용](xref:security/cors)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7addc-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> SignalR<span data-ttu-id="7addc-115"> **하려면** 다음 CORS 정책이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-115"> **requires** the following CORS policies:</span></span>

* <span data-ttu-id="7addc-116">예상되는 특정 원본을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-116">Allow the specific expected origins.</span></span> <span data-ttu-id="7addc-117">모든 원본을 허용 하는 것은 가능 하지만 안전 하거나 권장 **하지** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="7addc-118">HTTP 메서드 `GET` 및 `POST` 허용 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="7addc-119">쿠키 기반 고정 세션이 제대로 작동 하려면 자격 증명을 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-119">Credentials must be allowed in order for cookie-based sticky sessions to work correctly.</span></span> <span data-ttu-id="7addc-120">인증을 사용 하지 않는 경우에도 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-120">They must be enabled even when authentication isn't used.</span></span>

<!--
::: moniker range=">= aspnetcore-5.0"  // Moniker here just to make sure this doesn't get missed in the 5.0 version update.
However, in 5.0 we have provided an option in the TypeScript client to not use credentials.
The not to use credentials option should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers)

For more info, see https://github.com/dotnet/AspNetCore.Docs/issues/16003
.-->

<span data-ttu-id="7addc-121">예를 들어 다음 CORS 정책을 사용 하면 `https://example.com`에서 호스팅되는 SignalR browser 클라이언트를 `https://signalr.example.com`에서 호스트 되는 SignalR 앱에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-121">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

> [!NOTE]
> SignalR<span data-ttu-id="7addc-122">은 Azure App Service의 기본 제공 CORS 기능과 호환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-122"> is not compatible with the built-in CORS feature in Azure App Service.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="7addc-123">WebSocket 원본 제한</span><span class="sxs-lookup"><span data-stu-id="7addc-123">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7addc-124">CORS에서 제공하는 보호 기능은 WebSocket에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-124">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="7addc-125">Websocket에 대 한 원본 제한의 경우 [websocket 원본 제한](xref:fundamentals/websockets#websocket-origin-restriction)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7addc-125">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7addc-126">CORS에서 제공하는 보호 기능은 WebSocket에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-126">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="7addc-127">브라우저는 다음을 수행하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="7addc-127">Browsers do **not**:</span></span>

* <span data-ttu-id="7addc-128">CORS pre-flight 요청을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-128">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="7addc-129">WebSocket 요청을 생성할 때 `Access-Control` 헤더에 지정된 제한 사항을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-129">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="7addc-130">그러나 브라우저는 WebSocket 요청을 발급할 때 `Origin` 헤더를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-130">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="7addc-131">애플리케이션은 예상된 원본에서 제공하는 WebSocket만 허용되도록 이러한 헤더의 유효성을 검사하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-131">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="7addc-132">ASP.NET Core 2.1 이상에서 `UseSignalR`전에 배치 된 사용자 지정 미들웨어를 사용 하 여 헤더 유효성 검사를 수행 하 고 `Configure`에서 **인증 미들웨어** 를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-132">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="7addc-133">`Origin` 헤더는 클라이언트에 의해 제어되며 `Referer` 헤더와 마찬가지로 위조될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-133">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="7addc-134">이러한 헤더는 인증 메커니즘으로 **사용 하면 안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="7addc-134">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="7addc-135">ConnectionId</span><span class="sxs-lookup"><span data-stu-id="7addc-135">ConnectionId</span></span>

<span data-ttu-id="7addc-136">SignalR 서버 또는 클라이언트 버전이 2.2 이전 버전인 경우 `ConnectionId`를 노출 하면 악의적인 가장 ASP.NET Core이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-136">Exposing `ConnectionId` can lead to malicious impersonation if the SignalR server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="7addc-137">SignalR 서버와 클라이언트 버전이 ASP.NET Core 3.0 이상인 경우에는 `ConnectionId` 아닌 `ConnectionToken` 비밀로 유지 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-137">If the SignalR server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="7addc-138">`ConnectionToken`는 모든 API에서 의도적으로 노출 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-138">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="7addc-139">이전 SignalR 클라이언트가 서버에 연결 되지 않도록 하는 것이 어려울 수 있으므로 SignalR 서버 버전이 3.0 이상 ASP.NET Core 경우에도 `ConnectionId` 노출 되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-139">It can be difficult to ensure that older SignalR clients aren't connecting to the server, so even if your SignalR server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="7addc-140">액세스 토큰 로깅</span><span class="sxs-lookup"><span data-stu-id="7addc-140">Access token logging</span></span>

<span data-ttu-id="7addc-141">WebSocket 또는 서버-전송 이벤트를 사용할 경우 브라우저 클라이언트는 쿼리 문자열을 통해서 액세스 토큰을 전송합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-141">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="7addc-142">쿼리 문자열을 통해 액세스 토큰을 받는 것은 일반적으로 표준 `Authorization` 헤더를 사용 하는 것 만큼 안전 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-142">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="7addc-143">항상 HTTPS를 사용 하 여 클라이언트와 서버 간의 안전한 종단 간 연결을 보장 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-143">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="7addc-144">많은 웹 서버는 쿼리 문자열을 포함 하 여 각 요청에 대 한 URL을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-144">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="7addc-145">따라서 URL 로깅이 액세스 토큰까지 기록할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-145">Logging the URLs may log the access token.</span></span> <span data-ttu-id="7addc-146">ASP.NET Core은 기본적으로 쿼리 문자열을 포함 하는 각 요청에 대 한 URL을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-146">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="7addc-147">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-147">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="7addc-148">서버 로그를 사용 하 여이 데이터를 로깅하는 경우에는 `Microsoft.AspNetCore.Hosting`로 거를 `Warning` 수준 이상으로 구성 하 여이 로깅을 완전히 사용 하지 않도록 설정할 수 있습니다. 이러한 메시지는 `Info` 수준에서 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-148">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="7addc-149">자세한 내용은 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7addc-149">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="7addc-150">여전히 특정 요청 정보를 기록 하려는 경우 [미들웨어를 작성](xref:fundamentals/middleware/write) 하 여 필요한 데이터를 기록 하 고 `access_token` 쿼리 문자열 값 (있는 경우)을 필터링 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-150">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="7addc-151">예외</span><span class="sxs-lookup"><span data-stu-id="7addc-151">Exceptions</span></span>

<span data-ttu-id="7addc-152">일반적으로 예외 메시지는 민감한 데이터로 간주되어 클라이언트에게 노출되어서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-152">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="7addc-153">기본적으로 SignalR는 허브 메서드에서 throw 된 예외에 대 한 세부 정보를 클라이언트에 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-153">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="7addc-154">대신 클라이언트는 오류가 발생했음을 나타내는 일반적인 메시지를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-154">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="7addc-155">[EnableDetailedErrors](xref:signalr/configuration#configure-server-options)를 사용 하 여 클라이언트에 대 한 예외 메시지 배달 (예: 개발 또는 테스트)을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-155">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="7addc-156">운영 앱에서는 예외 메시지가 클라이언트에게 노출되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-156">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="7addc-157">버퍼 관리</span><span class="sxs-lookup"><span data-stu-id="7addc-157">Buffer management</span></span>

SignalR<span data-ttu-id="7addc-158">는 연결당 버퍼를 사용 하 여 들어오고 나가는 메시지를 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-158"> uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="7addc-159">기본적으로 SignalR는 이러한 버퍼를 32 KB로 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-159">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="7addc-160">클라이언트나 서버가 전송할 수 있는 최대 메시지는 32KB입니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-160">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="7addc-161">메시지에 대한 연결이 사용하는 최대 메모리는 32KB입니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-161">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="7addc-162">메시지가 항상 32KB보다 작은 경우 이 제한을 줄일 수 있습니다. 그러면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-162">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="7addc-163">클라이언트가 더 큰 메시지를 전송하는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-163">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="7addc-164">서버가 메시지를 받기 위해 큰 버퍼를 할당할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-164">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="7addc-165">메시지가 32KB보다 크다면 제한을 늘릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-165">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="7addc-166">이 제한을 늘리는 것은 다음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-166">Increasing this limit means:</span></span>

* <span data-ttu-id="7addc-167">클라이언트는 서버가 큰 메모리 버퍼를 할당하게 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-167">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="7addc-168">서버의 큰 버퍼 할당은 동시 연결 수를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-168">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="7addc-169">들어오는 메시지와 보내는 메시지에 대 한 제한이 있습니다. 둘 다 `MapHub`에서 구성 된 [Httpconnectiondispatcheroptions](xref:signalr/configuration#configure-server-options) 개체에 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-169">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="7addc-170">`ApplicationMaxBufferSize`는 서버에서 버퍼링 하는 클라이언트의 최대 바이트 수를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-170">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="7addc-171">클라이언트가 이 제한보다 큰 메시지를 전송하려고 시도하면 연결이 닫힐 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-171">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="7addc-172">`TransportMaxBufferSize`는 서버에서 보낼 수 있는 최대 바이트 수를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-172">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="7addc-173">서버가 이 제한보다 큰 메시지(허브 메서드의 반환 값을 포함)를 전송하려고 시도하면 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-173">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="7addc-174">제한을 `0` 설정 하면 제한이 해제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-174">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="7addc-175">제한을 제거하면 클라이언트가 모든 크기의 메시지를 전송할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-175">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="7addc-176">악의적인 클라이언트가 큰 메시지를 전송하여 과도한 메모리를 할당하게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-176">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="7addc-177">과도한 메모리의 사용은 동시 연결 수를 크게 감소시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7addc-177">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
