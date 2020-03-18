---
title: gRPC 서비스와 HTTP API 비교
author: jamesnk
description: gRPC와 HTTP API의 비교 방법과 권장 시나리오를 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- SignalR
uid: grpc/comparison
ms.openlocfilehash: 8935e665dfd5d8f9afa002f475c202ec0f0ee657
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78650793"
---
# <a name="compare-grpc-services-with-http-apis"></a>gRPC 서비스와 HTTP API 비교

작성자: [James Newton-King](https://twitter.com/jamesnk)

이 문서에서는 [gRPC 서비스](https://grpc.io/docs/guides/)와 HTTP API(ASP.NET Core [웹 API](xref:web-api/index) 포함)를 비교하는 방법을 설명합니다. 앱에 API를 제공하는 데 사용되는 기술은 중요한 선택 사항이며, gRPC는 HTTP API에 비해 고유한 혜택을 제공합니다. 이 문서에서는 gRPC의 장점과 단점을 설명하고, 다른 기술보다 gRPC를 사용하는 것이 유용한 시나리오를 추천합니다.

## <a name="high-level-comparison"></a>개괄적인 비교

다음 표에서는 JSON을 사용하는 HTTP API와 gRPC의 기능을 개괄적으로 비교해서 보여 줍니다.

| 기능          | gRPC                                               | JSON을 사용하는 HTTP API           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| 계약         | 필수( *.proto*)                                | 선택 사항(OpenAPI)            |
| 프로토콜         | HTTP/2                                             | HTTP                          |
| Payload          | [Protobuf](#performance)(소형, 이진)           | JSON(대형, 사람이 읽을 수 있음)  |
| 규범 | [엄격한 사양](#strict-specification)      | 느슨함. 모든 HTTP가 유효합니다.     |
| 스트리밍        | [클라이언트, 서버, 양방향](#streaming)       | 클라이언트, 서버                |
| 브라우저 지원  | [아니요](#limited-browser-support)(grpc-web 필요함) | 예                           |
| 보안         | 전송(TLS)                                    | 전송(TLS)               |
| 클라이언트 코드 생성 | [예](#code-generation)                      | OpenAPI + 타사 도구 |

## <a name="grpc-strengths"></a>gRPC 장점

### <a name="performance"></a>성능

gRPC 메시지는 효율적인 이진 메시지 형식인 [Protobuf](https://developers.google.com/protocol-buffers/docs/overview)를 사용하여 직렬화됩니다. Protobuf는 서버와 클라이언트에서 매우 빠르게 직렬화합니다. Protobuf 직렬화는 모바일 앱과 같은 제한된 대역폭 시나리오에서 중요한 작은 메시지 페이로드를 생성합니다.

gRPC는 HTTP 1.x보다 상당한 성능 혜택을 제공하는 주요 HTTP 수정 버전인 HTTP/2용으로 설계되었습니다.

* 이진 프레이밍 및 압축. HTTP/2 프로토콜은 간단하며, 보내고 받을 때 모두 효율적입니다.
* 단일 TCP 연결을 통해 여러 개의 HTTP/2 호출 멀티플렉싱. 멀티플렉싱은 [HOL(Head of Line) 차단](https://en.wikipedia.org/wiki/Head-of-line_blocking)을 제거합니다.

### <a name="code-generation"></a>코드 생성

모든 gRPC 프레임워크는 코드 생성에 대해 최고 수준의 지원을 제공합니다. gRPC 개발의 핵심 파일은 gRPC 서비스 및 메시지 계약을 정의하는 [.proto 파일](https://developers.google.com/protocol-buffers/docs/proto3)입니다. 이 파일에서 gRPC 프레임워크는 서비스 기본 클래스, 메시지 및 전체 클라이언트의 코드를 생성합니다.

서버와 클라이언트 간에 *.proto* 파일을 공유하여 메시지 및 클라이언트 코드를 처음부터 끝까지 생성할 수 있습니다. 클라이언트 코드 생성은 클라이언트와 서버에서 메시지 중복을 제거하고 강력한 형식의 클라이언트를 자동으로 만듭니다. 클라이언트를 작성할 필요가 없으므로 많은 서비스를 포함하는 애플리케이션에서 상당한 개발 시간이 절약됩니다.

### <a name="strict-specification"></a>엄격한 사양

JSON을 사용하는 HTTP API에 대한 공식적인 사양은 없습니다. URL, HTTP 동사 및 응답 코드의 가장 좋은 형식에 대해 개발자의 의견이 분분합니다.

[gRPC 사양](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)은 gRPC 서비스가 따라야 하는 형식에 대한 규범입니다. gRPC는 플랫폼과 구현 간에 일관성이 있으므로 gRPC를 통해 논쟁을 제거하고 개발자 시간을 절약할 수 있습니다.

### <a name="streaming"></a>스트리밍

HTTP/2는 장기 실시간 통신 스트림의 기초를 제공합니다. gRPC는 HTTP/2를 통한 스트리밍에 대해 최고 수준의 지원을 제공합니다.

gRPC 서비스는 모든 스트리밍 조합을 지원합니다.

* 단항(스트리밍 없음)
* 서버-클라이언트 스트리밍
* 클라이언트-서버 스트리밍
* 양방향 스트리밍

### <a name="deadlinetimeouts-and-cancellation"></a>최종 기한/시간 초과 및 취소

gRPC를 사용하면 클라이언트에서 RPC가 완료될 때까지 대기하는 기간을 지정할 수 있습니다. [최종 기한](https://grpc.io/blog/deadlines)은 서버로 전송되며, 서버에서 최종 기한을 넘기면 수행할 작업을 결정할 수 있습니다. 예를 들어 시간 초과 시 서버에서 진행 중인 gRPC/HTTP/데이터베이스 요청을 취소할 수 있습니다.

자식 gRPC 호출을 통해 최종 기한 및 취소를 전파하면 리소스 사용량 제한을 적용하는 데 도움이 됩니다.

## <a name="grpc-recommended-scenarios"></a>gRPC 권장 시나리오

gRPC는 다음과 같은 시나리오에 적합합니다.

* **마이크로 서비스** - gRPC는 대기 시간이 짧고 처리량이 높은 통신용으로 설계되었습니다. gRPC는 효율성이 중요한 경량 마이크로 서비스에 적합합니다.
* **지점 간 실시간 통신** - gRPC는 양방향 스트리밍에 대해 높은 수준의 지원을 제공합니다. gRPC 서비스는 폴링 없이 실시간으로 메시지를 푸시할 수 있습니다.
* **Polyglot 환경** - gRPC 도구는 모든 인기 개발 언어를 지원하므로 다중 언어 환경에서는 gRPC를 선택하는 것이 좋습니다.
* **네트워크 제한 환경** - gRPC 메시지는 경량 메시지 형식인 Protobuf를 사용하여 직렬화됩니다. gRPC 메시지는 해당하는 JSON 메시지보다 항상 작습니다.

## <a name="grpc-weaknesses"></a>gRPC 단점

### <a name="limited-browser-support"></a>제한된 브라우저 지원

현재, 브라우저에서 gRPC 서비스를 직접 호출할 수는 없습니다. gRPC는 HTTP/2 기능을 많이 사용하며, 웹 요청에 대해 gRPC 클라이언트를 지원하는 데 필요한 수준의 제어 기능을 제공하는 브라우저가 없습니다. 예를 들어 브라우저에서 호출자가 HTTP/2를 사용하도록 요구하거나 기본 HTTP/2 프레임에 대한 액세스를 제공할 수 없습니다.

[gRPC-웹](https://grpc.io/docs/tutorials/basic/web.html)은 브라우저에서 제한된 gRPC 지원을 제공하는 gRPC 팀의 추가 기술입니다. gRPC-Web은 모든 최신 브라우저를 지원하는 JavaScript 클라이언트와 서버의 gRPC-Web 프록시라는 두 부분으로 구성됩니다. gRPC-Web 클라이언트는 프록시를 호출하고, 프록시는 gRPC 요청을 gRPC 서버로 전달합니다.

gRPC-Web이 모든 gRPC 기능을 지원하지는 않습니다. 클라이언트 및 양방향 스트리밍은 지원되지 않고, 서버 스트리밍은 제한적으로 지원됩니다.

### <a name="not-human-readable"></a>사람이 읽을 수 없음

HTTP API 요청은 텍스트로 전송되며, 사람이 읽고 수행할 수 있습니다.

gRPC 메시지는 기본적으로 Protobuf로 인코딩됩니다. Protobuf는 효율적으로 주고받을 수 있지만, 해당 이진 형식을 사람이 읽을 수 없습니다. Protobuf가 제대로 역직렬화하려면 *.proto* 파일에 메시지의 인터페이스 설명이 지정되어 있어야 합니다. 네트워크상에서 Protobuf 페이로드를 분석하고 요청을 직접 작성하려면 추가 도구가 필요합니다.

이진 Protobuf 메시지를 지원하기 위해 [서버 리플렉션](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) 및 [gRPC 명령줄 도구](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)와 같은 기능이 있습니다. 또한 Protobuf 메시지는 [JSON으로 변환 및 JSON에서 변환](https://developers.google.com/protocol-buffers/docs/proto3#json)을 지원합니다. 기본 제공 JSON 변환은 디버그할 때 Protobuf 메시지를 사람이 읽을 수 있는 형식으로 변환하거나 그 반대로 변환하는 효율적인 방법을 제공합니다.

## <a name="alternative-framework-scenarios"></a>대체 프레임워크 시나리오

다음과 같은 시나리오에서는 gRPC보다 다른 프레임워크를 사용하는 것이 좋습니다.

* **브라우저에서 액세스 가능한 API** - gRPC는 브라우저에서 완전히 지원되지 않습니다. gRPC-Web은 브라우저 지원을 제공할 수 있지만 제한 사항이 있으며 서버 프록시를 도입합니다.
* **브로드캐스트 실시간 통신** - gRPC는 스트리밍을 통해 실시간 통신을 지원하지만, 메시지를 등록된 연결로 브로드캐스트하는 개념이 없습니다. 예를 들어 새 채팅 메시지를 대화방 모든 클라이언트에 보내야 하는 대화방 시나리오에서는 각 gRPC 호출이 개별적으로 새 채팅 메시지를 클라이언트로 스트리밍해야 합니다. [SignalR](xref:signalr/introduction)는 이 시나리오에서 유용한 프레임워크입니다. SignalR에는 영구 연결 개념과 메시지 브로드캐스트에 대한 기본 제공 지원이 있습니다.
* **프로세스 간 통신** - 프로세스가 들어오는 gRPC 호출을 허용하려면 HTTP/2 서버를 호스트해야 합니다. Windows에서 프로세스 간 통신 [파이프](/dotnet/standard/io/pipe-operations)는 빠르고 간단한 통신 방법입니다.

## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
