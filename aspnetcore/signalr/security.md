---
title: ASP.NET Core SignalR의 보안 고려 사항
author: bradygaster
description: ASP.NET Core SignalR에서 인증 및 권한 부여를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/security
ms.openlocfilehash: 6e9f849ed856cf1cbf989b8b16cab5209c465471
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892050"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a>ASP.NET Core SignalR의 보안 고려 사항

작성자: [Andrew Stanton-Nurse](https://twitter.com/anurse)

이 문서에서는 SignalR의 보안에 대한 정보를 제공합니다.

## <a name="cross-origin-resource-sharing"></a>교차 원본 자원 공유

[교차 원본 자원 공유(CORS)](https://www.w3.org/TR/cors/)를 사용해서 브라우저에서 교차 원본 SignalR 연결을 허용할 수 있습니다. JavaScript 코드가 SignalR 앱과 다른 도메인에서 호스팅 될 경우 JavaScript가 SignalR 앱에 연결할 수 있도록 반드시 [CORS 미들웨어](xref:security/cors)를 활성화시켜야 합니다. 신뢰하거나 제어할 수 있는 도메인의 교차 원본 요청만 허용해야 합니다. 예를 들어:

* 사이트가 `http://www.example.com`에 호스팅된 경우
* SignalR 앱이 `http://signalr.example.com`에 호스팅된 경우

SignalR 앱에서 `www.example.com` 원본만 허용하도록 CORS를 구성해야 합니다.

CORS를 구성하는 방법에 대한 자세한 내용은 [교차 원본 요청(CORS)](xref:security/cors)을 참고하시기 바랍니다. SignalR은 다음과 같은 CORS 정책을 **필요로 합니다**.

* 예상되는 특정 원본을 허용합니다. 모든 원본을 허용할 수는 있지만 안전하지 않거나 권장되지 **않습니다.**
* HTTP 메서드 `GET`과 `POST`를 허용해야 합니다.
* 인증이 사용되지 않는 경우에도 자격 증명이 활성화되어야 합니다.

예를 들어 다음 CORS 정책은 `https://example.com`에서 호스팅되는 SignalR 브라우저 클라이언트가 `https://signalr.example.com`에서 호스팅되는 SignalR 앱에 접근할 수 있도록 허용합니다.

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

> [!NOTE]
> SignalR은 Azure App Service가 기본 제공하는 CORS 기능과 호환되지 않습니다.

## <a name="websocket-origin-restriction"></a>WebSocket 원본 제한

::: moniker range=">= aspnetcore-2.2"

CORS가 제공하는 보호는 WebSocket에는 적용되지 않습니다. Websocket에서 원본 제한에 대 한 읽을 [Websocket 원본 제한은](xref:fundamentals/websockets#websocket-origin-restriction)합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS가 제공하는 보호는 WebSocket에는 적용되지 않습니다. 브라우저는 다음을 수행하지 **않습니다.**

* CORS 예비 요청을 수행하지 않습니다.
* WebSocket 요청을 생성할 때 `Access-Control` 헤더에 지정된 제한 사항을 준수하지 않습니다.

그러나 브라우저는 WebSocket 요청을 만들때 `Origin` 헤더를 전송합니다. 응용 프로그램은 예상된 원본으로부터 들어오는 WebSocket만 허용하도록 이런 헤더의 유효성을 검사하도록 구성되어야 합니다.

ASP.NET Core 2.1 이상에서는 `Configure`에서 **`UseSignalR` 및 인증 미들웨어를 호출하기 전에** 사용자 지정 미들웨어를 배치해서 헤더 유효성 검사를 수행할 수 있습니다.

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> `Origin` 헤더는 클라이언트에 의해서 제어되며 `Referer` 헤더처럼 가짜일 수 있습니다. 이런 헤더들을 인증 메커니즘으로 사용하면 **안 됩니다.**

::: moniker-end

## <a name="access-token-logging"></a>액세스 토큰 로깅

WebSocket 또는 서버-전송 이벤트를 사용할 경우 브라우저 클라이언트는 쿼리 문자열을 통해서 액세스 토큰을 전송합니다. 쿼리 문자열을 통해서 액세스 토큰을 수신하는 방식은 일반적으로 표준 `Authorization` 헤더를 사용하는 방식만큼 안전합니다. 항상 HTTPS를 사용 하 여 클라이언트와 서버 간의 안전한 종단 간 연결을 보장 해야 합니다. 여러 웹 서버 로그 쿼리 문자열을 포함 하 여 각 요청에 대 한 URL입니다. 따라서 URL 로깅이 액세스 토큰까지 기록할 수도 있습니다. ASP.NET Core는 쿼리 문자열을 포함 하는 기본적으로 각 요청에 대 한 URL을 기록 합니다. 예를 들어:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

서버 로그를 사용 하 여이 데이터를 로깅에 대 한 질문이 있으면 완전히 구성 하 여이 로깅을 비활성화할 수 있습니다는 `Microsoft.AspNetCore.Hosting` 로거가 합니다 `Warning` 수준 이상 (이러한 메시지에 기록 됩니다 `Info` 수준). 설명서를 참조 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 자세한 내용은 합니다. 특정 요청 정보를 기록 하려는 경우 [미들웨어를 작성](xref:fundamentals/middleware/write) 필요 하 고 필터링 된 데이터를 기록 하는 `access_token` 쿼리 문자열 값 (있는 경우).

## <a name="exceptions"></a>예외

일반적으로 예외 메시지는 민감한 데이터로 간주되어 클라이언트에게 노출되어서는 안 됩니다. 기본적으로 SignalR은 허브 메서드가 던진 예외의 세부 정보를 클라이언트로 전송하지 않습니다. 대신 클라이언트는 오류가 발생했음을 나타내는 일반적인 메시지를 수신합니다. 클라이언트에 대한 예외 메시지 전달은 [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options)를 사용하여 재정의할 수 있습니다(예: 개발 또는 테스트 환경에서). 운영 앱에서는 예외 메시지가 클라이언트에게 노출되지 않아야 합니다.

## <a name="buffer-management"></a>버퍼 관리

SignalR은 연결별 버퍼를 사용해서 들어오고 나가는 메시지를 관리합니다. 기본적으로 SignalR은 이 버퍼를 32KB로 제한합니다. 클라이언트나 서버가 전송할 수 있는 최대 메시지는 32KB입니다. 메시지에 대한 연결이 사용하는 최대 메모리는 32KB입니다. 메시지가 항상 32KB보다 작은 경우 이 제한을 줄일 수 있습니다. 그러면 다음과 같이 됩니다.

* 클라이언트가 더 큰 메시지를 전송하는 것을 방지합니다.
* 서버가 메시지를 받기 위해 큰 버퍼를 할당할 필요가 없습니다.

메시지가 32KB보다 크다면 제한을 늘릴 수 있습니다. 이 제한을 늘리는 것은 다음을 의미합니다.

* 클라이언트는 서버가 큰 메모리 버퍼를 할당하게 할 수 있습니다.
* 서버의 큰 버퍼 할당은 동시 연결 수를 줄일 수 있습니다.

들어오는 메시지와 나가는 메시지에 대한 제한이 존재하며, 두 제한 모두 `MapHub`에 구성된 [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) 개체에서 구성할 수 있습니다.

* `ApplicationMaxBufferSize`는 서버가 버퍼링하는 클라이언트의 최대 바이트 수를 나타냅니다. 클라이언트가 이 제한보다 큰 메시지를 전송하려고 시도하면 연결이 닫힐 수 있습니다.
* `TransportMaxBufferSize`는 서버가 전송할 수 있는 최대 바이트 수를 나타냅니다. 서버가 이 제한보다 큰 메시지(허브 메서드의 반환 값을 포함)를 전송하려고 시도하면 예외가 발생합니다.

제한을 `0`으로 설정하면 제한이 비활성화됩니다. 제한을 제거하면 클라이언트가 모든 크기의 메시지를 전송할 수 있습니다. 악의적인 클라이언트가 큰 메시지를 전송하여 과도한 메모리를 할당하게 만들 수 있습니다. 과도한 메모리의 사용은 동시 연결 수를 크게 감소시킬 수 있습니다.
