---
title: gRPC 서비스와 HTTP API 비교
author: jamesnk
description: GRPC와 HTTP Api를 비교한 방법과 권장 시나리오를 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/25/2019
uid: grpc/comparison
ms.openlocfilehash: 5c3ea7a78401e6483425fa0774b3051b3d20f516
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2019
ms.locfileid: "72289042"
---
# <a name="compare-grpc-services-with-http-apis"></a>gRPC 서비스와 HTTP API 비교

별 [뉴턴-킹](https://twitter.com/jamesnk)

이 문서에서는 [Grpc 서비스](https://grpc.io/docs/guides/) 와 HTTP api (ASP.NET Core [web api](xref:web-api/index)포함)를 비교 하는 방법을 설명 합니다. 앱에 대 한 API를 제공 하는 데 사용 되는 기술은 중요 한 선택 이며 gRPC는 HTTP Api와 비교해 서 고유한 이점을 제공 합니다. 이 문서에서는 gRPC의 장점과 단점을 설명 하 고 다른 기술에서 gRPC를 사용 하는 시나리오를 권장 합니다.

## <a name="high-level-comparison"></a>상위 수준 비교

다음 표에서는 gRPC와 JSON을 사용 하는 HTTP Api 간의 고급 기능 비교를 제공 합니다.

| 기능          | gRPC                                               | JSON을 사용 하는 HTTP Api           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| 계약         | 필수 (*proto*)                                | 선택 사항 (OpenAPI)            |
| 전송        | HTTP/2                                             | HTTP                          |
| Payload          | [Protobuf (소형, 이진)](#performance)           | JSON (매우 큼, 사람이 읽을 수 있음)  |
| Prescriptiveness | [Strict 사양](#strict-specification)      | 토너가. 모든 HTTP가 유효 합니다.      |
| 스트리밍        | [클라이언트, 서버, 양방향](#streaming)       | 클라이언트, 서버                |
| 브라우저 지원  | [아니요 (grpc-웹 필요)](#limited-browser-support) | 예                           |
| 보안         | 전송 (HTTPS)                                  | 전송 (HTTPS)             |
| 클라이언트 코드 생성 | [예](#code-generation)                      | OpenAPI + 타사 도구 |

## <a name="grpc-strengths"></a>gRPC 장점

### <a name="performance"></a>성능

gRPC 메시지는 효율적인 이진 메시지 형식인 [Protobuf](https://developers.google.com/protocol-buffers/docs/overview)를 사용 하 여 serialize 됩니다. Protobuf는 서버와 클라이언트에서 매우 빠르게 serialize 합니다. Protobuf serialization은 모바일 앱과 같은 제한 된 대역폭 시나리오에서 중요 한 작은 메시지 페이로드를 발생 합니다.

gRPC는 http 1.x에서 상당한 성능 이점을 제공 하는 http의 주요 개정판 인 HTTP/2 용으로 설계 되었습니다.

* 이진 프레이밍 및 압축 HTTP/2 프로토콜은 간단 하 고 보내고 받을 때 모두 효율적입니다.
* 단일 TCP 연결에 대 한 여러 HTTP/2 호출의 멀티플렉싱. 멀티플렉싱은 [줄의 인라인 차단을](https://en.wikipedia.org/wiki/Head-of-line_blocking)제거 합니다.

### <a name="code-generation"></a>코드 생성

모든 gRPC 프레임 워크는 코드 생성에 대 한 최고 수준의 지원을 제공 합니다. GRPC 개발에 대 한 핵심 파일은 gRPC 서비스 및 메시지의 계약을 정의 하는 [ .pfile](https://developers.google.com/protocol-buffers/docs/proto3)입니다. 이 파일 gRPC 프레임 워크에서 코드는 서비스 기본 클래스, 메시지 및 전체 클라이언트를 생성 합니다.

서버와 클라이언트 간에 *proto* 파일을 공유 하 여 메시지와 클라이언트 코드를 종단 간에서 생성할 수 있습니다. 클라이언트의 코드 생성은 클라이언트와 서버에서 메시지의 중복을 제거 하 고 강력한 형식의 클라이언트를 만듭니다. 클라이언트를 작성 하지 않아도 많은 서비스를 갖춘 응용 프로그램에 상당한 개발 시간이 절감 됩니다.

### <a name="strict-specification"></a>Strict 사양

JSON을 사용 하는 HTTP API에 대 한 공식 사양이 없습니다. 개발자는 Url, HTTP 동사 및 응답 코드의 가장 좋은 형식을 논의 합니다.

[Grpc 사양은](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) grpc 서비스에서 따라야 하는 형식에 대 한 지침입니다. grpc는 플랫폼 및 구현에서 일치 하므로 논쟁을 제거 하 고 개발자 시간을 절약 합니다.

### <a name="streaming"></a>스트리밍

H t t p/2는 수명이 긴 실시간 통신 스트림에 대 한 기초를 제공 합니다. gRPC는 HTTP/2를 통한 스트리밍을 위한 최고 수준의 지원을 제공 합니다.

GRPC 서비스는 모든 스트리밍 조합을 지원 합니다.

* 단항 (스트리밍 없음)
* 서버-클라이언트 스트리밍
* 클라이언트에서 서버로 스트리밍
* 양방향 스트리밍

### <a name="deadlinetimeouts-and-cancellation"></a>최종 기한/시간 제한 및 취소

gRPC는 클라이언트가 RPC가 완료 될 때까지 대기 하는 기간을 지정할 수 있습니다. [최종 기한이](https://grpc.io/blog/deadlines) 서버에 전송 되 고 서버에서 최종 기한을 초과 하는 경우 수행할 작업을 결정할 수 있습니다. 예를 들어 서버에서 제한 시간에 진행 중인 gRPC/HTTP/database 요청을 취소할 수 있습니다.

자식 gRPC 호출을 통해 최종 기한 및 취소를 전파 하면 리소스 사용 제한을 강제로 적용할 수 있습니다.

## <a name="grpc-recommended-scenarios"></a>gRPC 권장 시나리오

gRPC는 다음과 같은 시나리오에 적합 합니다.

* **마이크로 서비스** &ndash; grpc는 대기 시간이 짧고 처리량이 높은 통신을 위해 설계 되었습니다. gRPC는 효율성이 중요 한 경량 마이크로 서비스에 적합 합니다.
* **Point to point 실시간 통신** &ndash; grpc는 양방향 스트리밍을 지원 합니다. gRPC 서비스는 폴링을 사용 하지 않고 실시간으로 메시지를 푸시할 수 있습니다.
* **Polyglot environment** &ndash; grpc 도구는 널리 사용 되는 모든 개발 언어를 지원 하 고, grpc를 다중 언어 환경에 적합 하 게 선택할 수 있습니다.
* **네트워크 제한 환경** &ndash; grpc 메시지는 경량 메시지 형식인 Protobuf를 사용 하 여 serialize 됩니다. GRPC 메시지는 항상 해당 하는 JSON 메시지 보다 작습니다.

## <a name="grpc-weaknesses"></a>gRPC 약점

### <a name="limited-browser-support"></a>제한 된 브라우저 지원

현재 브라우저에서 gRPC 서비스를 직접 호출 하는 것은 불가능 합니다. gRPC는 HTTP/2 기능을 많이 사용 하며, 브라우저에서 gRPC 클라이언트를 지원 하기 위해 웹 요청에 필요한 제어 수준을 제공 하지 않습니다. 예를 들어, 브라우저는 호출자가 h t t p/2를 사용 하도록 요구 하거나 기본 HTTP/2 프레임에 대 한 액세스를 제공 하지 않습니다.

[grpc-웹](https://grpc.io/docs/tutorials/basic/web.html) 은 브라우저에서 제한적 grpc 지원을 제공 하는 grpc 팀의 추가 기술입니다. gRPC-웹은 모든 최신 브라우저를 지 원하는 JavaScript 클라이언트와 서버에서 gRPC-웹 프록시의 두 부분으로 구성 됩니다. GRPC-웹 클라이언트는 프록시를 호출 하 고 grpc 서버에 대 한 gRPC 요청에서 프록시를 전달 합니다.

GRPC-웹에서 모든 gRPC 기능을 지원 하지는 않습니다. 클라이언트 및 양방향 스트리밍이 지원 되지 않으며 서버 스트리밍이 제한적으로 지원 됩니다.

### <a name="not-human-readable"></a>사람이 읽을 수 없음

HTTP API 요청은 텍스트로 전송 되며, 사람이 읽고 만들 수 있습니다.

gRPC 메시지는 기본적으로 Protobuf로 인코딩됩니다. Protobuf는 송신 및 수신에 효율적 이지만, 이진 형식은 사람이 읽을 수 없습니다. Protobuf를 사용 하려면 올바른 deserialize를 위해 파일에 지정 된 메시지의 인터페이스 설명이 *필요 합니다.* 네트워크에서 Protobuf 페이로드를 분석 하 고 요청을 직접 작성 하려면 추가 도구가 필요 합니다.

[서버 리플렉션](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) 및 [grpc 명령줄 도구](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) 와 같은 기능은 이진 Protobuf 메시지를 지원 하기 위해 존재 합니다. 또한 Protobuf 메시지는 [JSON으로의 변환을](https://developers.google.com/protocol-buffers/docs/proto3#json)지원 합니다. 기본 제공 JSON 변환은 디버그할 때 Protobuf 메시지를 사람이 읽을 수 있는 형식으로 변환 하는 효율적인 방법을 제공 합니다.

## <a name="alternative-framework-scenarios"></a>대체 프레임 워크 시나리오

다른 프레임 워크는 다음과 같은 시나리오에서 gRPC 보다 권장 됩니다.

* 브라우저에서 **액세스할 수 있는 api** @no__t 1 grpc는 브라우저에서 완전히 지원 되지 않습니다. gRPC-웹은 브라우저 지원을 제공할 수 있지만 제한 사항이 있으며 서버 프록시를 소개 합니다.
* **브로드캐스트 실시간 통신** &ndash; grpc는 스트리밍을 통해 실시간 통신을 지원 하지만 등록 된 연결에 메시지를 브로드캐스트하는 개념이 없습니다. 예를 들어 대화방의 모든 클라이언트에 새 채팅 메시지를 보내야 하는 대화방 시나리오에서 각 gRPC 호출은 클라이언트에 새 채팅 메시지를 개별적으로 스트리밍하는 데 필요 합니다. [SignalR](xref:signalr/introduction) 는이 시나리오에 유용한 프레임 워크입니다. SignalR에는 영구 연결의 개념과 메시지 브로드캐스팅을 위한 기본 제공 지원이 있습니다.
* **프로세스 간 통신** &ndash; 프로세스에서 들어오는 grpc 호출을 허용 하는 HTTP/2 서버를 호스팅해야 합니다. Windows의 경우 프로세스 간 통신 [파이프](/dotnet/standard/io/pipe-operations) 는 빠르고 간단한 통신 방법입니다.

## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
