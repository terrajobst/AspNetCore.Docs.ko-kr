---
title: SignalR 클라이언트 기능
author: bradygaster
description: 다양 한 ASP.NET Core SignalR 클라이언트에서 지 원하는 기능에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 2d6759a5484c37aee6db3d22b3127414231605ae
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301184"
---
# <a name="aspnet-core-signalr-client-features"></a>ASP.NET Core SignalR 클라이언트 기능

## <a name="feature-distribution"></a>기능 배포

다음 표에서는 실시간 지원을 제공 하는 클라이언트에 대 한 기능 및 지원을 보여 줍니다.

| 기능 | .NET | JavaScript | Java |
| ---- | :-: | :-: | :-: |
| Azure SignalR 서비스 지원 |✔|✔|✔|
| [서버-클라이언트 스트리밍](xref:signalr/streaming)          |✔|✔|✔|
| [클라이언트와 서버 간 스트리밍](xref:signalr/streaming)          |✔|✔|✔|
| 자동 다시 연결 ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |✔|✔| |
| Websocket 전송 |✔|✔|✔|
| 서버에서 보낸 이벤트 전송 |✔|✔| |
| 긴 폴링 전송 |✔|✔|✔|
| JSON 허브 프로토콜 |✔|✔|✔|
| MessagePack 허브 프로토콜 |✔|✔| |

Java 클라이언트에서 자동 다시 연결에 대 한 지원은 [문제 추적기](https://github.com/aspnet/AspNetCore/issues/8711)에서 추적 됩니다.

## <a name="additional-resources"></a>추가 자료

* [ASP.NET Core SignalR 시작하기](xref:tutorials/signalr)
* [지원되는 플랫폼](xref:signalr/supported-platforms)
* [허브](xref:signalr/hubs)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
