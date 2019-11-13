---
title: ASP.NET Core SignalR의 보안 고려 사항
author: bradygaster
description: ASP.NET Core SignalR에서 인증 및 권한 부여를 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: c5a34ae67bdfb8f7fd92c00f18973b66b685a99c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963907"
---
# <a name="security-considerations-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="7ee68-103">ASP.NET Core SignalR의 보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7ee68-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="7ee68-104">[Andrew Stanton-간호사](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="7ee68-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="7ee68-105">이 문서에서는 SignalR보안 설정에 대 한 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="7ee68-106">원본 간 리소스 공유</span><span class="sxs-lookup"><span data-stu-id="7ee68-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="7ee68-107">[CORS (크로스-원본 자원 공유)](https://www.w3.org/TR/cors/) 를 사용 하 여 브라우저에서 원본 간 SignalR 연결을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="7ee68-108">JavaScript 코드가 SignalR 앱과 다른 도메인에서 호스트 되는 경우 JavaScript가 SignalR 앱에 연결할 수 있도록 [CORS 미들웨어](xref:security/cors) 를 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="7ee68-109">신뢰 하거나 제어 하는 도메인 에서만 원본 간 요청을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="7ee68-110">예를 들어 다음과 같은 가치를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-110">For example:</span></span>

* <span data-ttu-id="7ee68-111">사이트가 `http://www.example.com`에서 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="7ee68-112">SignalR 앱이 `http://signalr.example.com`에서 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="7ee68-113">SignalR 앱에서 원본 `www.example.com`만 허용 하도록 CORS를 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="7ee68-114">CORS를 구성 하는 방법에 대 한 자세한 내용은 [cors (원본 간 요청) 사용](xref:security/cors)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee68-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> SignalR<span data-ttu-id="7ee68-115"> **하려면** 다음 CORS 정책이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-115"> **requires** the following CORS policies:</span></span>

* <span data-ttu-id="7ee68-116">필요한 특정 원본을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-116">Allow the specific expected origins.</span></span> <span data-ttu-id="7ee68-117">모든 원본을 허용 하는 것은 가능 하지만 안전 하거나 권장 **하지** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="7ee68-118">HTTP 메서드 `GET` 및 `POST` 허용 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="7ee68-119">인증을 사용 하지 않는 경우에도 자격 증명을 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-119">Credentials must be enabled, even when authentication is not used.</span></span>

<span data-ttu-id="7ee68-120">예를 들어 다음 CORS 정책을 사용 하면 `https://example.com`에서 호스팅되는 SignalR browser 클라이언트를 `https://signalr.example.com`에서 호스트 되는 SignalR 앱에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-120">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

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
> SignalR<span data-ttu-id="7ee68-121">은 Azure App Service의 기본 제공 CORS 기능과 호환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-121"> is not compatible with the built-in CORS feature in Azure App Service.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="7ee68-122">WebSocket 원본 제한</span><span class="sxs-lookup"><span data-stu-id="7ee68-122">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7ee68-123">CORS에서 제공하는 보호 기능은 WebSocket에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-123">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="7ee68-124">Websocket에 대 한 원본 제한의 경우 [websocket 원본 제한](xref:fundamentals/websockets#websocket-origin-restriction)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee68-124">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7ee68-125">CORS에서 제공하는 보호 기능은 WebSocket에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="7ee68-126">브라우저는 다음을 수행하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="7ee68-126">Browsers do **not**:</span></span>

* <span data-ttu-id="7ee68-127">CORS pre-flight 요청을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-127">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="7ee68-128">WebSocket 요청을 생성할 때 `Access-Control` 헤더에 지정된 제한 사항을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-128">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="7ee68-129">그러나 브라우저는 WebSocket 요청을 발급할 때 `Origin` 헤더를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-129">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="7ee68-130">애플리케이션은 예상된 원본에서 제공하는 WebSocket만 허용되도록 이러한 헤더의 유효성을 검사하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-130">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="7ee68-131">ASP.NET Core 2.1 이상에서 `UseSignalR`전에 배치 된 사용자 지정 미들웨어를 사용 하 여 헤더 유효성 검사를 수행 하 고 `Configure`에서 **인증 미들웨어** 를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-131">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="7ee68-132">`Origin` 헤더는 클라이언트에 의해 제어되며 `Referer` 헤더와 마찬가지로 위조될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-132">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="7ee68-133">이러한 헤더는 인증 메커니즘으로 **사용 하면 안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="7ee68-133">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="access-token-logging"></a><span data-ttu-id="7ee68-134">액세스 토큰 로깅</span><span class="sxs-lookup"><span data-stu-id="7ee68-134">Access token logging</span></span>

<span data-ttu-id="7ee68-135">Websocket 또는 서버에서 보낸 이벤트를 사용 하는 경우 browser 클라이언트는 쿼리 문자열에 액세스 토큰을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-135">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="7ee68-136">쿼리 문자열을 통해 액세스 토큰을 받는 것은 일반적으로 표준 `Authorization` 헤더를 사용 하는 것 만큼 안전 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-136">Receiving the access token via query string is generally as secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="7ee68-137">클라이언트와 서버 간의 안전한 종단 간 연결을 보장 하려면 항상 HTTPS를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-137">You should always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="7ee68-138">많은 웹 서버는 쿼리 문자열을 포함 하 여 각 요청에 대 한 URL을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-138">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="7ee68-139">Url을 기록 하면 액세스 토큰이 기록 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-139">Logging the URLs may log the access token.</span></span> <span data-ttu-id="7ee68-140">ASP.NET Core은 기본적으로 쿼리 문자열을 포함 하는 각 요청에 대 한 URL을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-140">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="7ee68-141">예를 들어 다음과 같은 가치를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-141">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="7ee68-142">서버 로그를 사용 하 여이 데이터를 로깅하는 경우에는 `Microsoft.AspNetCore.Hosting`로 거를 `Warning` 수준 이상으로 구성 하 여이 로깅을 완전히 사용 하지 않도록 설정할 수 있습니다. 이러한 메시지는 `Info` 수준에서 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-142">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="7ee68-143">자세한 내용은 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 에 대 한 설명서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee68-143">See the documentation on [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="7ee68-144">여전히 특정 요청 정보를 기록 하려는 경우 [미들웨어를 작성](xref:fundamentals/middleware/write) 하 여 필요한 데이터를 기록 하 고 `access_token` 쿼리 문자열 값 (있는 경우)을 필터링 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-144">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="7ee68-145">예외</span><span class="sxs-lookup"><span data-stu-id="7ee68-145">Exceptions</span></span>

<span data-ttu-id="7ee68-146">일반적으로 예외 메시지는 클라이언트에 노출 되지 않아야 하는 중요 한 데이터로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-146">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="7ee68-147">기본적으로 SignalR는 허브 메서드에서 throw 된 예외에 대 한 세부 정보를 클라이언트에 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-147">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="7ee68-148">대신, 클라이언트는 오류가 발생 했음을 나타내는 일반 메시지를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-148">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="7ee68-149">[`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options)를 사용 하 여 클라이언트에 대 한 예외 메시지 배달 (예: 개발 또는 테스트)을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-149">Exception message delivery to the client can be overridden (for example in development or test) with [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="7ee68-150">예외 메시지는 프로덕션 앱에서 클라이언트에 노출 되 면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-150">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="7ee68-151">버퍼 관리</span><span class="sxs-lookup"><span data-stu-id="7ee68-151">Buffer management</span></span>

SignalR<span data-ttu-id="7ee68-152">는 연결당 버퍼를 사용 하 여 들어오고 나가는 메시지를 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-152"> uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="7ee68-153">기본적으로 SignalR는 이러한 버퍼를 32 KB로 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-153">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="7ee68-154">클라이언트 또는 서버에서 보낼 수 있는 최대 메시지는 32 KB입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-154">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="7ee68-155">메시지에 대 한 연결에서 사용 되는 최대 메모리는 32 KB입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-155">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="7ee68-156">메시지가 항상 32 KB 보다 작은 경우 다음 제한을 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-156">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="7ee68-157">클라이언트에서 더 큰 메시지를 보낼 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-157">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="7ee68-158">서버는 메시지를 수락 하기 위해 대량 버퍼를 할당할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-158">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="7ee68-159">메시지가 32 KB 보다 큰 경우 제한을 늘릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-159">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="7ee68-160">이 제한을 늘리려면 다음을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-160">Increasing this limit means:</span></span>

* <span data-ttu-id="7ee68-161">클라이언트는 서버에서 많은 메모리 버퍼를 할당 하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-161">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="7ee68-162">대량 버퍼를 서버 할당 하면 동시 연결 수를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-162">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="7ee68-163">들어오는 메시지와 보내는 메시지에 대 한 제한이 있습니다. 둘 다 `MapHub`에서 구성 된 [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) 개체에 대해 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-163">There are limits for incoming and outgoing messages, both can be configured on the [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="7ee68-164">`ApplicationMaxBufferSize`는 서버에서 버퍼링 하는 클라이언트의 최대 바이트 수를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-164">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="7ee68-165">클라이언트에서이 한도 보다 큰 메시지를 보내려고 시도 하면 연결이 닫힐 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-165">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="7ee68-166">`TransportMaxBufferSize`는 서버에서 보낼 수 있는 최대 바이트 수를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-166">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="7ee68-167">서버에서이 한도 보다 큰 메시지 (허브 메서드에서 반환 값 포함)를 보내려고 시도 하면 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-167">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="7ee68-168">제한을 `0` 설정 하면 제한이 해제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-168">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="7ee68-169">제한을 제거 하면 클라이언트가 모든 크기의 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-169">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="7ee68-170">큰 메시지를 보내는 악의적인 클라이언트는 과도 한 메모리를 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-170">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="7ee68-171">메모리를 과도 하 게 사용 하면 동시 연결 수를 상당히 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee68-171">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
