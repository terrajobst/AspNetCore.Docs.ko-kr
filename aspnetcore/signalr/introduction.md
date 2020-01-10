---
title: ASP.NET Core SignalR 소개
author: bradygaster
description: ASP.NET Core SignalR 라이브러리에서 실시간 기능을 앱에 추가 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 635431abf9263c2dff261aea47e6f8324061763f
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829285"
---
# <a name="introduction-to-aspnet-core-opno-locsignalr"></a>ASP.NET Core SignalR 소개

## <a name="what-is-opno-locsignalr"></a>SignalR(이)란 무엇인가요?

ASP.NET Core SignalR는 응용 프로그램에 실시간 웹 기능 추가를 간소화 하는 오픈 소스 라이브러리입니다. 실시간 웹 기능을 사용하면 서버 쪽 코드에서 클라이언트로 콘텐츠를 즉시 푸시할 수 있습니다.

SignalR에 적합 한 후보:

* 서버로부터 빈번한 업데이트가 필요한 앱. 예를 들어 게임, 소셜 네트워크, 투표, 경매, 지도 및 GPS 앱이 있습니다.
* 대시보드 및 모니터링 앱. 예를 들어 기업 대시보드, 즉각적인 매출 업데이트나 여행 경고가 있습니다.
* 공동 작업 앱. 예를 들어 화이트보드 앱 및 팀 회의 소프트웨어가 있습니다.
* 알림이 필요한 앱. 소셜 네트워크, 이메일, 채팅, 게임, 여행 경고 및 다른 많은 앱들이 알림을 사용합니다.

SignalR는 서버-클라이언트 [원격 프로시저 호출 (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)을 만들기 위한 API를 제공 합니다. 이 RPC는 서버 쪽 .NET Core 코드에서 클라이언트 상의 JavaScript 함수를 호출합니다.

ASP.NET Core에 대 한 SignalR 몇 가지 기능은 다음과 같습니다.

* 연결 관리를 자동으로 처리 합니다.
* 연결된 모든 클라이언트에 동시에 메시지를 전송합니다. 그 예로 대화방을 들 수 있습니다.
* 특정 클라이언트나 클라이언트 그룹으로 메시지를 보냅니다.
* 트래픽 증가를 처리할 수 있도록 확장됩니다.

소스는 [GitHub의SignalR 리포지토리에서](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)호스팅됩니다.

## <a name="transports"></a>전송

SignalR는 실시간 통신을 처리 하는 다음과 같은 기술을 지원 합니다 (정상적인 대체 순서 대로).

* [WebSockets](https://tools.ietf.org/html/rfc7118)
* 서버에서 보낸 이벤트
* 긴 폴링

SignalR는 서버 및 클라이언트의 기능 내에서 가장 적합 한 전송 방법을 자동으로 선택 합니다.

## <a name="hubs"></a>허브

SignalR는 *허브* 를 사용 하 여 클라이언트와 서버 간에 통신 합니다.

허브는 클라이언트와 서버가 서로 상대방의 메서드를 호출할 수 있게 해주는 높은 수준의 파이프라인입니다. SignalR은 컴퓨터 경계에서 디스패치를 자동으로 처리 하 여 클라이언트가 서버에서 메서드를 호출할 수 있도록 하 고 그 반대의 경우도 마찬가지입니다. 메서드에 강력한 형식의 매개 변수를 전달할 수 있으므로 모델 바인딩을 사용할 수 있습니다. SignalR는 JSON 기반의 텍스트 프로토콜과 [MessagePack](https://msgpack.org/)기반 이진 프로토콜을 제공 하는 두 가지 기본 제공 허브 프로토콜을 제공 합니다.  일반적으로 MessagePack이 JSON에 비해 작은 크기의 메시지를 만들어냅니다. 구형 브라우저는 MessagePack 프로토콜 기능을 제공하기 위해서 [XHR 수준 2](https://caniuse.com/#feat=xhr2)를 지원해야 합니다.

허브는 클라이언트 쪽 메서드의 이름 및 매개 변수를 담고 있는 메시지를 전송해서 클라이언트 쪽 코드를 호출합니다. 메서드의 매개 변수로 전송된 개체는 구성된 프로토콜을 이용해서 역직렬화됩니다. 클라이언트는 클라이언트 쪽 코드에서 이름이 일치하는 메서드를 찾으려고 시도합니다. 클라이언트가 일치하는 이름을 찾으면 역직렬화된 매개 변수 데이터를 전달하여 메서드를 호출합니다.

## <a name="additional-resources"></a>추가 자료

* [ASP.NET Core에 대 한 SignalR 시작](xref:tutorials/signalr)
* [지원되는 플랫폼](xref:signalr/supported-platforms)
* [허브](xref:signalr/hubs)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
