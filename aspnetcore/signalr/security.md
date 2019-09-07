---
title: ASP.NET Core SignalR의 보안 고려 사항
author: bradygaster
description: ASP.NET Core SignalR에서 인증 및 권한 부여를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/security
ms.openlocfilehash: a52db2ff51c55f7299d63aa3c7398f99727e0694
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746552"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a><span data-ttu-id="be51e-103">ASP.NET Core SignalR의 보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="be51e-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="be51e-104">작성자: [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="be51e-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="be51e-105">이 문서에서는 SignalR의 보안에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="be51e-106">교차 원본 자원 공유</span><span class="sxs-lookup"><span data-stu-id="be51e-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="be51e-107">[교차 원본 자원 공유(CORS)](https://www.w3.org/TR/cors/)를 사용해서 브라우저에서 교차 원본 SignalR 연결을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="be51e-108">JavaScript 코드가 SignalR 앱과 다른 도메인에서 호스팅 될 경우 JavaScript가 SignalR 앱에 연결할 수 있도록 반드시 [CORS 미들웨어](xref:security/cors)를 활성화시켜야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="be51e-109">신뢰하거나 제어할 수 있는 도메인의 교차 원본 요청만 허용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="be51e-110">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="be51e-110">For example:</span></span>

* <span data-ttu-id="be51e-111">사이트가 `http://www.example.com`에 호스팅된 경우</span><span class="sxs-lookup"><span data-stu-id="be51e-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="be51e-112">SignalR 앱이 `http://signalr.example.com`에 호스팅된 경우</span><span class="sxs-lookup"><span data-stu-id="be51e-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="be51e-113">SignalR 앱에서 `www.example.com` 원본만 허용하도록 CORS를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="be51e-114">CORS를 구성하는 방법에 대한 자세한 내용은 [교차 원본 요청(CORS)](xref:security/cors)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> <span data-ttu-id="be51e-115">SignalR은 다음과 같은 CORS 정책을 **필요로 합니다**.</span><span class="sxs-lookup"><span data-stu-id="be51e-115">SignalR **requires** the following CORS policies:</span></span>

* <span data-ttu-id="be51e-116">예상되는 특정 원본을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-116">Allow the specific expected origins.</span></span> <span data-ttu-id="be51e-117">모든 원본을 허용할 수는 있지만 안전하지 않거나 권장되지 **않습니다.**</span><span class="sxs-lookup"><span data-stu-id="be51e-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="be51e-118">HTTP 메서드 `GET`과 `POST`를 허용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="be51e-119">인증이 사용되지 않는 경우에도 자격 증명이 활성화되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-119">Credentials must be enabled, even when authentication is not used.</span></span>

<span data-ttu-id="be51e-120">예를 들어 다음 CORS 정책은 `https://example.com`에서 호스팅되는 SignalR 브라우저 클라이언트가 `https://signalr.example.com`에서 호스팅되는 SignalR 앱에 접근할 수 있도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-120">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

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
> <span data-ttu-id="be51e-121">SignalR은 Azure App Service가 기본 제공하는 CORS 기능과 호환되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-121">SignalR is not compatible with the built-in CORS feature in Azure App Service.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="be51e-122">WebSocket 원본 제한</span><span class="sxs-lookup"><span data-stu-id="be51e-122">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="be51e-123">CORS가 제공하는 보호는 WebSocket에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-123">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="be51e-124">Websocket에 대 한 원본 제한의 경우 [websocket 원본 제한](xref:fundamentals/websockets#websocket-origin-restriction)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="be51e-124">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="be51e-125">CORS가 제공하는 보호는 WebSocket에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="be51e-126">브라우저는 다음을 수행하지 **않습니다.**</span><span class="sxs-lookup"><span data-stu-id="be51e-126">Browsers do **not**:</span></span>

* <span data-ttu-id="be51e-127">CORS 예비 요청을 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-127">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="be51e-128">WebSocket 요청을 생성할 때 `Access-Control` 헤더에 지정된 제한 사항을 준수하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-128">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="be51e-129">그러나 브라우저는 WebSocket 요청을 만들때 `Origin` 헤더를 전송합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-129">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="be51e-130">응용 프로그램은 예상된 원본으로부터 들어오는 WebSocket만 허용하도록 이런 헤더의 유효성을 검사하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-130">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="be51e-131">ASP.NET Core 2.1 이상에서는 `Configure`에서 **`UseSignalR` 및 인증 미들웨어를 호출하기 전에** 사용자 지정 미들웨어를 배치해서 헤더 유효성 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-131">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="be51e-132">`Origin` 헤더는 클라이언트에 의해서 제어되며 `Referer` 헤더처럼 가짜일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-132">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="be51e-133">이런 헤더들을 인증 메커니즘으로 사용하면 **안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="be51e-133">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="access-token-logging"></a><span data-ttu-id="be51e-134">액세스 토큰 로깅</span><span class="sxs-lookup"><span data-stu-id="be51e-134">Access token logging</span></span>

<span data-ttu-id="be51e-135">WebSocket 또는 서버-전송 이벤트를 사용할 경우 브라우저 클라이언트는 쿼리 문자열을 통해서 액세스 토큰을 전송합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-135">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="be51e-136">쿼리 문자열을 통해서 액세스 토큰을 수신하는 방식은 일반적으로 표준 `Authorization` 헤더를 사용하는 방식만큼 안전합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-136">Receiving the access token via query string is generally as secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="be51e-137">클라이언트와 서버 간의 안전한 종단 간 연결을 보장 하려면 항상 HTTPS를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-137">You should always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="be51e-138">많은 웹 서버는 쿼리 문자열을 포함 하 여 각 요청에 대 한 URL을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-138">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="be51e-139">따라서 URL 로깅이 액세스 토큰까지 기록할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-139">Logging the URLs may log the access token.</span></span> <span data-ttu-id="be51e-140">ASP.NET Core은 기본적으로 쿼리 문자열을 포함 하는 각 요청에 대 한 URL을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-140">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="be51e-141">예:</span><span class="sxs-lookup"><span data-stu-id="be51e-141">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="be51e-142">서버 로그를 사용 하 여이 데이터를 기록 하는 데 문제가 있는 경우 `Microsoft.AspNetCore.Hosting` 로 거 `Warning` 를 수준 이상으로 구성 하 여이 로깅을 완전히 사용 하지 않도록 설정할 수 있습니다 `Info` . 이러한 메시지는 수준에서 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-142">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="be51e-143">자세한 내용은 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 에 대 한 설명서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="be51e-143">See the documentation on [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="be51e-144">여전히 특정 요청 정보를 기록 하려는 경우 [미들웨어를 작성](xref:fundamentals/middleware/write) 하 여 필요한 데이터를 기록 하 고 `access_token` 쿼리 문자열 값 (있는 경우)을 필터링 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-144">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="be51e-145">예외</span><span class="sxs-lookup"><span data-stu-id="be51e-145">Exceptions</span></span>

<span data-ttu-id="be51e-146">일반적으로 예외 메시지는 민감한 데이터로 간주되어 클라이언트에게 노출되어서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-146">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="be51e-147">기본적으로 SignalR은 허브 메서드가 던진 예외의 세부 정보를 클라이언트로 전송하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-147">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="be51e-148">대신 클라이언트는 오류가 발생했음을 나타내는 일반적인 메시지를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-148">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="be51e-149">클라이언트에 대한 예외 메시지 전달은 [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options)를 사용하여 재정의할 수 있습니다(예: 개발 또는 테스트 환경에서).</span><span class="sxs-lookup"><span data-stu-id="be51e-149">Exception message delivery to the client can be overridden (for example in development or test) with [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="be51e-150">운영 앱에서는 예외 메시지가 클라이언트에게 노출되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-150">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="be51e-151">버퍼 관리</span><span class="sxs-lookup"><span data-stu-id="be51e-151">Buffer management</span></span>

<span data-ttu-id="be51e-152">SignalR은 연결별 버퍼를 사용해서 들어오고 나가는 메시지를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-152">SignalR uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="be51e-153">기본적으로 SignalR은 이 버퍼를 32KB로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-153">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="be51e-154">클라이언트나 서버가 전송할 수 있는 최대 메시지는 32KB입니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-154">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="be51e-155">메시지에 대한 연결이 사용하는 최대 메모리는 32KB입니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-155">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="be51e-156">메시지가 항상 32KB보다 작은 경우 이 제한을 줄일 수 있습니다. 그러면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-156">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="be51e-157">클라이언트가 더 큰 메시지를 전송하는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-157">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="be51e-158">서버가 메시지를 받기 위해 큰 버퍼를 할당할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-158">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="be51e-159">메시지가 32KB보다 크다면 제한을 늘릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-159">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="be51e-160">이 제한을 늘리는 것은 다음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-160">Increasing this limit means:</span></span>

* <span data-ttu-id="be51e-161">클라이언트는 서버가 큰 메모리 버퍼를 할당하게 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-161">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="be51e-162">서버의 큰 버퍼 할당은 동시 연결 수를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-162">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="be51e-163">들어오는 메시지와 나가는 메시지에 대한 제한이 존재하며, 두 제한 모두 `MapHub`에 구성된 [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) 개체에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-163">There are limits for incoming and outgoing messages, both can be configured on the [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="be51e-164">`ApplicationMaxBufferSize`는 서버가 버퍼링하는 클라이언트의 최대 바이트 수를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-164">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="be51e-165">클라이언트가 이 제한보다 큰 메시지를 전송하려고 시도하면 연결이 닫힐 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-165">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="be51e-166">`TransportMaxBufferSize`는 서버가 전송할 수 있는 최대 바이트 수를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-166">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="be51e-167">서버가 이 제한보다 큰 메시지(허브 메서드의 반환 값을 포함)를 전송하려고 시도하면 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-167">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="be51e-168">제한을 `0`으로 설정하면 제한이 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-168">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="be51e-169">제한을 제거하면 클라이언트가 모든 크기의 메시지를 전송할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-169">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="be51e-170">악의적인 클라이언트가 큰 메시지를 전송하여 과도한 메모리를 할당하게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-170">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="be51e-171">과도한 메모리의 사용은 동시 연결 수를 크게 감소시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be51e-171">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
