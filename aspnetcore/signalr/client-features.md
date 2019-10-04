---
title: SignalR 클라이언트 기능
author: bradygaster
description: 다양 한 ASP.NET Core SignalR 클라이언트에서 지 원하는 기능에 대해 알아봅니다.
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 6718722cdbcfae500026fcd429ca6b9de8f0f103
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925348"
---
# <a name="aspnet-core-signalr-client-features"></a>ASP.NET Core SignalR 클라이언트 기능

## <a name="feature-distribution"></a>기능 배포

다음 표에서는 실시간 지원을 제공 하는 클라이언트에 대 한 기능 및 지원을 보여 줍니다. 각 기능에 대해이 기능을 지 원하는 *최소* 버전이 나열 됩니다. 버전이 나열 되지 않은 경우에는 기능이 지원 되지 않습니다.

| 기능 | .NET | JavaScript | Java |
| ---- | :-: | :-: | :-: |
| Azure SignalR 서비스 지원 |1.0.0|1.0.0|1.0.0|
| [서버-클라이언트 스트리밍](xref:signalr/streaming)          |1.0.0|1.0.0|1.0.0|
| [클라이언트와 서버 간 스트리밍](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|
| 자동 다시 연결 ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|❌|
| Websocket 전송 |1.0.0|1.0.0|1.0.0|
| 서버에서 보낸 이벤트 전송 |1.0.0|1.0.0|❌|
| 긴 폴링 전송 |1.0.0|1.0.0|3.0.0|
| JSON 허브 프로토콜 |1.0.0|1.0.0|1.0.0|
| MessagePack 허브 프로토콜 |1.0.0|1.0.0|❌|

Java 클라이언트에서 자동 다시 연결에 대 한 지원은 [문제 추적기](https://github.com/aspnet/AspNetCore/issues/8711)에서 추적 됩니다.

## <a name="additional-resources"></a>추가 자료

* [ASP.NET Core SignalR 시작하기](xref:tutorials/signalr)
* [지원되는 플랫폼](xref:signalr/supported-platforms)
* [허브](xref:signalr/hubs)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
