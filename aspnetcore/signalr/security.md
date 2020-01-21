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
ms.openlocfilehash: 4b27d9abb36938ed8161ff0d3535204e3fa68765
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294714"
---
# <a name="security-considerations-in-aspnet-core-opno-locsignalr"></a>ASP.NET Core SignalR의 보안 고려 사항

작성자: [Andrew Stanton-Nurse](https://twitter.com/anurse)

이 문서에서는 SignalR보안 설정에 대 한 정보를 제공 합니다.

## <a name="cross-origin-resource-sharing"></a>교차 원본 자원 공유

[CORS (크로스-원본 자원 공유)](https://www.w3.org/TR/cors/) 를 사용 하 여 브라우저에서 원본 간 SignalR 연결을 허용할 수 있습니다. JavaScript 코드가 SignalR 앱과 다른 도메인에서 호스트 되는 경우 JavaScript가 SignalR 앱에 연결할 수 있도록 [CORS 미들웨어](xref:security/cors) 를 사용 하도록 설정 해야 합니다. 신뢰하거나 제어할 수 있는 도메인의 교차 원본 요청만 허용해야 합니다. 예를 들면 다음과 같습니다.:

* 사이트가 `http://www.example.com`에 호스팅된 경우
* SignalR 앱이 `http://signalr.example.com`에서 호스팅됩니다.

SignalR 앱에서 원본 `www.example.com`만 허용 하도록 CORS를 구성 해야 합니다.

CORS를 구성하는 방법에 대한 자세한 내용은 [교차 원본 요청(CORS)](xref:security/cors)을 참고하시기 바랍니다. SignalR **하려면** 다음 CORS 정책이 필요 합니다.

* 예상되는 특정 원본을 허용합니다. 모든 원본을 허용할 수는 있지만 안전하지 않거나 권장되지 **않습니다.**
* HTTP 메서드 `GET`과 `POST`를 허용해야 합니다.
* 쿠키 기반 고정 세션이 제대로 작동 하려면 자격 증명을 허용 해야 합니다. 인증을 사용 하지 않는 경우에도 사용 하도록 설정 해야 합니다.

<!--
::: moniker range=">= aspnetcore-5.0"  // Moniker here just to make sure this doesn't get missed in the 5.0 version update.
However, in 5.0 we have provided an option in the TypeScript client to not use credentials.
The not to use credentials option should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers)

For more info, see https://github.com/aspnet/AspNetCore.Docs/issues/16003
.-->

예를 들어 다음 CORS 정책을 사용 하면 `https://example.com`에서 호스팅되는 SignalR browser 클라이언트를 `https://signalr.example.com`에서 호스트 되는 SignalR 앱에 액세스할 수 있습니다.

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
> SignalR은 Azure App Service의 기본 제공 CORS 기능과 호환 되지 않습니다.

## <a name="websocket-origin-restriction"></a>WebSocket 원본 제한

::: moniker range=">= aspnetcore-2.2"

CORS에서 제공하는 보호 기능은 WebSocket에 적용되지 않습니다. Websocket에 대 한 원본 제한의 경우 [websocket 원본 제한](xref:fundamentals/websockets#websocket-origin-restriction)을 참조 하세요.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS에서 제공하는 보호 기능은 WebSocket에 적용되지 않습니다. 브라우저는 다음을 수행하지 **않습니다**.

* CORS pre-flight 요청을 수행합니다.
* WebSocket 요청을 생성할 때 `Access-Control` 헤더에 지정된 제한 사항을 준수합니다.

그러나 브라우저는 WebSocket 요청을 발급할 때 `Origin` 헤더를 보냅니다. 애플리케이션은 예상된 원본에서 제공하는 WebSocket만 허용되도록 이러한 헤더의 유효성을 검사하도록 구성되어야 합니다.

ASP.NET Core 2.1 이상에서는 `Configure`에서 **`UseSignalR` 및 인증 미들웨어를 호출하기 전에** 사용자 지정 미들웨어를 배치해서 헤더 유효성 검사를 수행할 수 있습니다.

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> `Origin` 헤더는 클라이언트에 의해 제어되며 `Referer` 헤더와 마찬가지로 위조될 수 있습니다. 이런 헤더들을 인증 메커니즘으로 사용하면 **안 됩니다.**

::: moniker-end

## <a name="connectionid"></a>ConnectionId

SignalR 서버 또는 클라이언트 버전이 2.2 이전 버전인 경우 `ConnectionId`를 노출 하면 악의적인 가장 ASP.NET Core이 발생할 수 있습니다. SignalR 서버와 클라이언트 버전이 ASP.NET Core 3.0 이상인 경우에는 `ConnectionId` 아닌 `ConnectionToken` 비밀로 유지 되어야 합니다. `ConnectionToken`는 모든 API에서 의도적으로 노출 되지 않습니다.  이전 SignalR 클라이언트가 서버에 연결 되지 않도록 하는 것이 어려울 수 있으므로 SignalR 서버 버전이 3.0 이상 ASP.NET Core 경우에도 `ConnectionId` 노출 되지 않아야 합니다.

## <a name="access-token-logging"></a>액세스 토큰 로깅

WebSocket 또는 서버-전송 이벤트를 사용할 경우 브라우저 클라이언트는 쿼리 문자열을 통해서 액세스 토큰을 전송합니다. 쿼리 문자열을 통해 액세스 토큰을 받는 것은 일반적으로 표준 `Authorization` 헤더를 사용 하는 것 만큼 안전 합니다. 항상 HTTPS를 사용 하 여 클라이언트와 서버 간의 안전한 종단 간 연결을 보장 합니다. 많은 웹 서버는 쿼리 문자열을 포함 하 여 각 요청에 대 한 URL을 기록 합니다. 따라서 URL 로깅이 액세스 토큰까지 기록할 수도 있습니다. ASP.NET Core은 기본적으로 쿼리 문자열을 포함 하는 각 요청에 대 한 URL을 기록 합니다. 예를 들면 다음과 같습니다.:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

서버 로그를 사용 하 여이 데이터를 로깅하는 경우에는 `Microsoft.AspNetCore.Hosting`로 거를 `Warning` 수준 이상으로 구성 하 여이 로깅을 완전히 사용 하지 않도록 설정할 수 있습니다. 이러한 메시지는 `Info` 수준에서 기록 됩니다. 자세한 내용은 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 을 참조 하세요. 여전히 특정 요청 정보를 기록 하려는 경우 [미들웨어를 작성](xref:fundamentals/middleware/write) 하 여 필요한 데이터를 기록 하 고 `access_token` 쿼리 문자열 값 (있는 경우)을 필터링 할 수 있습니다.

## <a name="exceptions"></a>예외

일반적으로 예외 메시지는 민감한 데이터로 간주되어 클라이언트에게 노출되어서는 안 됩니다. 기본적으로 SignalR는 허브 메서드에서 throw 된 예외에 대 한 세부 정보를 클라이언트에 보내지 않습니다. 대신 클라이언트는 오류가 발생했음을 나타내는 일반적인 메시지를 수신합니다. [EnableDetailedErrors](xref:signalr/configuration#configure-server-options)를 사용 하 여 클라이언트에 대 한 예외 메시지 배달 (예: 개발 또는 테스트)을 재정의할 수 있습니다. 운영 앱에서는 예외 메시지가 클라이언트에게 노출되지 않아야 합니다.

## <a name="buffer-management"></a>버퍼 관리

SignalR는 연결당 버퍼를 사용 하 여 들어오고 나가는 메시지를 관리 합니다. 기본적으로 SignalR는 이러한 버퍼를 32 KB로 제한 합니다. 클라이언트나 서버가 전송할 수 있는 최대 메시지는 32KB입니다. 메시지에 대한 연결이 사용하는 최대 메모리는 32KB입니다. 메시지가 항상 32KB보다 작은 경우 이 제한을 줄일 수 있습니다. 그러면 다음과 같이 됩니다.

* 클라이언트가 더 큰 메시지를 전송하는 것을 방지합니다.
* 서버가 메시지를 받기 위해 큰 버퍼를 할당할 필요가 없습니다.

메시지가 32KB보다 크다면 제한을 늘릴 수 있습니다. 이 제한을 늘리는 것은 다음을 의미합니다.

* 클라이언트는 서버가 큰 메모리 버퍼를 할당하게 할 수 있습니다.
* 서버의 큰 버퍼 할당은 동시 연결 수를 줄일 수 있습니다.

들어오는 메시지와 보내는 메시지에 대 한 제한이 있습니다. 둘 다 `MapHub`에서 구성 된 [Httpconnectiondispatcheroptions](xref:signalr/configuration#configure-server-options) 개체에 구성할 수 있습니다.

* `ApplicationMaxBufferSize`는 서버가 버퍼링하는 클라이언트의 최대 바이트 수를 나타냅니다. 클라이언트가 이 제한보다 큰 메시지를 전송하려고 시도하면 연결이 닫힐 수 있습니다.
* `TransportMaxBufferSize`는 서버가 전송할 수 있는 최대 바이트 수를 나타냅니다. 서버가 이 제한보다 큰 메시지(허브 메서드의 반환 값을 포함)를 전송하려고 시도하면 예외가 발생합니다.

제한을 `0`으로 설정하면 제한이 비활성화됩니다. 제한을 제거하면 클라이언트가 모든 크기의 메시지를 전송할 수 있습니다. 악의적인 클라이언트가 큰 메시지를 전송하여 과도한 메모리를 할당하게 만들 수 있습니다. 과도한 메모리의 사용은 동시 연결 수를 크게 감소시킬 수 있습니다.
