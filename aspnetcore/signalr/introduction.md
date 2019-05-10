---
title: ASP.NET Core SignalR 소개
author: bradygaster
description: ASP.NET Core SignalR 라이브러리로 앱에 실시간 기능을 추가하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/25/2018
uid: signalr/introduction
ms.openlocfilehash: 673efafce60dfa46cb99f9537fda2bca42bf9822
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892250"
---
# <a name="introduction-to-aspnet-core-signalr"></a>ASP.NET Core SignalR 소개

## <a name="what-is-signalr"></a>SignalR이란?

ASP.NET Core SignalR은 앱에 실시간 웹 기능을 손쉽게 추가할 수 있는 오픈 소스 라이브러리입니다. 실시간 웹 기능을 사용하면 서버 쪽 코드에서 클라이언트로 콘텐츠를 즉시 푸시할 수 있습니다.

SignalR은 다음과 같은 앱에 적합합니다.

* 서버로부터 빈번한 업데이트가 필요한 앱. 게임, 소셜 네트워크, 투표, 경매, 지도 및 GPS 앱을 예로 들 수 있습니다.
* 대시보드 및 모니터링 앱. 예를 들어 기업 대시보드, 즉각적인 매출 업데이트나 여행 경고가 있습니다.
* 공동 작업 앱. 화이트 보드 앱 및 팀 회의 소프트웨어가 공동 작업 앱의 예입니다.
* 알림이 필요한 앱. 소셜 네트워크, 이메일, 채팅, 게임, 여행 경고 및 다른 많은 앱들이 알림을 사용합니다.

SignalR은 서버에서 클라이언트로 [원격 프로시저 호출(RPC)](https://wikipedia.org/wiki/Remote_procedure_call)을 생성하는 API를 제공합니다. 이 RPC는 서버 쪽 .NET Core 코드에서 클라이언트 상의 JavaScript 함수를 호출합니다.

다음은 ASP.NET Core SignalR의 일부 기능입니다.

* 연결 관리를 자동으로 처리합니다.
* 연결된 모든 클라이언트에 동시에 메시지를 전송합니다. 그 예로 대화방을 들 수 있습니다.
* 특정 클라이언트 또는 클라이언트 그룹에 메시지를 보냅니다.
* 트래픽 증가를 처리할 수 있도록 확장됩니다.

소스는 [GitHub의 SignalR 리포지토리](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR)에서 호스트됩니다.

## <a name="transports"></a>전송

SignalR은 실시간 통신을 처리하기 위한 몇 가지 기법을 지원합니다.

* [WebSockets](https://tools.ietf.org/html/rfc7118)
* 서버에서 전송 이벤트
* 긴 폴링

SignalR은 서버와 클라이언트의 기능을 감안하여 자동으로 가장 적합한 전송 방식을 선택합니다.

## <a name="hubs"></a>허브

SignalR은 *허브*를 통해서 클라이언트와 서버 간의 통신을 수행합니다.

허브는 클라이언트와 서버가 서로 상대방의 메서드를 호출할 수 있게 해주는 높은 수준의 파이프라인입니다. SignalR은 클라이언트에서 서버의 메서드를 호출하거나 그 반대로 호출이 가능하도록, 컴퓨터의 경계를 넘어서는 디스패치 처리를 자동으로 수행합니다. 메서드에 강력한 형식의 매개 변수를 전달할 수 있으므로 모델 바인딩을 사용할 수 있습니다. SignalR은 JSON 기반의 텍스트 프로토콜 및 [MessagePack](https://msgpack.org/) 기반의 바이너리 프로토콜의 두 가지 기본 제공 허브 프로토콜을 제공합니다.  일반적으로 MessagePack이 JSON에 비해 작은 크기의 메시지를 만들어냅니다. 구형 브라우저는 MessagePack 프로토콜 기능을 제공하기 위해서 [XHR 수준 2](https://caniuse.com/#feat=xhr2)를 지원해야 합니다.

허브는 클라이언트 쪽 메서드의 이름 및 매개 변수를 담고 있는 메시지를 전송해서 클라이언트 쪽 코드를 호출합니다. 메서드의 매개 변수로 전송된 개체는 구성된 프로토콜을 이용해서 역직렬화됩니다. 클라이언트는 클라이언트 쪽 코드에서 이름이 일치하는 메서드를 찾으려고 시도합니다. 클라이언트가 일치하는 이름을 찾으면 역직렬화된 매개 변수 데이터를 전달하여 메서드를 호출합니다.

## <a name="additional-resources"></a>추가 자료

* [ASP.NET Core SignalR 시작하기](xref:tutorials/signalr)
* [지원되는 플랫폼](xref:signalr/supported-platforms)
* [허브](xref:signalr/hubs)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
