---
title: gRPC 서비스와 HTTP API 비교
author: jamesnk
description: 시나리오는 gRPC 비교와 HTTP Api가 무엇을 권장 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 03/31/2019
uid: grpc/comparison
ms.openlocfilehash: 8f4cefe1dedcf4cfd9650e73e6a1ba30dbbfeffa
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087408"
---
# <a name="comparing-grpc-services-with-http-apis"></a>gRPC 서비스와 HTTP API 비교

[James 뉴턴 King](https://twitter.com/jamesnk)

이 문서에서는 설명 하는 방법 [gRPC services](https://grpc.io/docs/guides/) HTTP Api 비교할 (ASP.NET Core를 포함 하 여 [Web Api](xref:web-api/index)). 앱에 대 한 API를 제공 하는 데 기술 중요 한 옵션 및 gRPC HTTP Api에 비해 특유의 이점을 제공 합니다. 이 문서는 gRPC의 장단점을 설명 하 고 다른 기술을 통해 gRPC를 사용 하는 시나리오를 권장 합니다.

#### <a name="overview"></a>개요

|    기능             |    gRPC                                                 |    JSON 사용 하 여 HTTP Api                       |
|------------------------|---------------------------------------------------------|----------------------------------------------|
|    계약            |    필요한 (`*.proto`)                                 |    선택 사항 (OpenAPI)                        |
|    전송           |    HTTP/2                                               |    HTTP                                      |
|    Payload             |    [Protobuf (작은 이진)](#performance)             |    JSON (대형 사람이 읽을 수 있음)              |
|    Prescriptiveness    |    [엄격한 사양](#strict-specification)        |    사용 완화 합니다. 모든 HTTP 유효합니다.                  |
|    스트리밍           |    [클라이언트, 서버, 양방향](#streaming)         |    클라이언트, 서버                            |
|    브라우저 지원     |    [아니요 (grpc 웹 필요)](#limited-browser-support)   |    예                                       |
|    보안            |    전송 (HTTPS)                                    |    전송 (HTTPS)                         |
|    클라이언트 코드 생성     |    [예](#code-generation)                              |    OpenAPI + 타사 도구             |

## <a name="grpc-strengths"></a>gRPC 장점

### <a name="performance"></a>성능

gRPC 메시지를 사용 하 여 serialize 할 [Protobuf](https://developers.google.com/protocol-buffers/docs/overview)는 효율적인 이진 메시지 형식입니다. 서버와 클라이언트에서 Protobuf를 매우 신속 하 게 serialize합니다. Protobuf serialization을 대역폭이 제한 된 시나리오에서 중요 한 작은 메시지 페이로드를 사용 하면 모바일 앱을 선호합니다.

gRPC는 HTTP/2, HTTP를 통해 뛰어난 성능을 제공 하는 HTTP의 주요 수정 버전을 위한 1.x:

* 이진 프레이밍 및 압축 합니다. HTTP/2 프로토콜은 간단 하 고 보내기 및 받기 모두 효율적입니다.
* 여러 HTTP/2 호출의 단일 TCP 연결을 통해 멀티플렉싱 합니다. 제거 멀티플렉싱 [head 아웃오브 라인 차단](https://en.wikipedia.org/wiki/Head-of-line_blocking)합니다.

### <a name="code-generation"></a>코드 생성

모든 gRPC 프레임 워크에는 코드 생성에 대 한 최고 수준의 지원을 제공합니다. GRPC 개발 하는 핵심 파일은는 [ `*.proto` 파일](https://developers.google.com/protocol-buffers/docs/proto3), gRPC 서비스 및 메시지 계약을 정의 하는 합니다. 프레임 워크 코드는이 파일 gRPC에서 서비스 기본 클래스, 메시지 및 전체 클라이언트를 생성 합니다.

공유는 `*.proto` 간 끝에서 서버 및 클라이언트, 메시지 및 클라이언트 코드 간에 파일을 생성할 수 있습니다. 클라이언트의 코드 생성 클라이언트와 서버에서 메시지 중복 제거 및 수에 대 한 강력한 형식의 클라이언트를 만듭니다. 클라이언트를 작성 하지 않아도 많은 서비스를 사용 하 여 응용 프로그램에서 상당한 개발 시간을 저장 합니다.

### <a name="strict-specification"></a>엄격한 사양

JSON 사용 하 여 HTTP API에 대 한 공식 사양 존재 하지 않습니다. 개발자가 Url 중 가장 적절 한 형식을 논의 HTTP 동사와 응답 코드입니다.

합니다 [gRPC 사양](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) gRPC 서비스 따라야 하는 형식에 대 한 지침입니다. gRPC는 논쟁을 제거 하 고 gPRC 플랫폼 구현 간에 일관 된 이므로 개발자 시간을 저장 합니다.

### <a name="streaming"></a>스트리밍

HTTP/2 오랫동안 지속 되는 실시간 통신 스트림을 위한 기초를 제공합니다. gRPC는 HTTP/2를 통해 스트리밍에 대 한 최고 수준의 지원을 제공 합니다.

GRPC 서비스에서는 모든 스트리밍 조합을 지원합니다.

* 단항 (스트리밍 없음)
* 서버에서 클라이언트로 스트리밍
* 스트리밍 서버는 클라이언트
* 양방향 스트리밍

### <a name="deadlinetimeouts-and-cancellation"></a>마감 날짜/시간 제한 및 취소

gRPC 기간는 완료 하는 RPC 대기를 지정 하는 클라이언트를 허용 합니다. 합니다 [최종 기한](https://grpc.io/blog/deadlines) 서버로 보내지는 서버 최종 기한 초과 하는 경우에 수행할 동작을 결정할 수 있습니다. 예를 들어 서버 진행 gRPC/HTTP/데이터베이스 요청 시간 초과 시 취소 될 수 있습니다.

호출을 마감일 및 자식 gRPC 통해 취소 전파 리소스 사용량 제한을 적용할 수 있습니다.

## <a name="grpc-recommended-scenarios"></a>gRPC 시나리오를 권장 합니다.

gRPC는 다음과 같은 시나리오에 적합 합니다.

* **마이크로 서비스** &ndash; gRPC가 설계 된 짧은 대기 시간과 높은 처리량의 통신이 있습니다. gRPC는 간단한 마이크로 서비스에 대 한 훌륭한 효율성 중요 합니다.
* **실시간 간 통신과** &ndash; gRPC가 양방향 스트리밍에 대 한 지원. gRPC 서비스에 푸시할 수 메시지 폴링 없이 실시간.
* **Polygot 환경** &ndash; gRPC 도구에서 만드는 gRPC 다중 언어 환경에 적합 한 모든 주요 개발 언어를 지원 합니다.
* **네트워크 제한 된 환경** &ndash; gRPC 메시지 Protobuf를 간단한 메시지 형식으로 serialize 됩니다. GRPC 메시지에 해당 하는 JSON 메시지 보다 항상 작습니다.

## <a name="grpc-weaknesses"></a>gRPC 단점

### <a name="limited-browser-support"></a>제한 된 브라우저 지원

현재 브라우저에서 gRPC 서비스를 직접 호출 하는 것이 불가능 합니다. gRPC HTTP/2 기능을 많이 사용 하 고 브라우저 없음 수준의 gRPC 클라이언트를 지원 하기 위해 웹 요청 동안 필요한 제어를 제공 합니다. 예를 들어 브라우저 허용 하거나 하지 않으면 호출자는 HTTP/2 사용할 필요에 기본 HTTP/2 프레임에 대 한 액세스를 제공 합니다.

[gRPC 웹](https://grpc.io/docs/tutorials/basic/web.html) 는 브라우저에서 제한 된 gRPC 지원을 제공 하는 gRPC 팀에서 추가 기술 합니다. gRPC 웹 두 부분으로 구성 됩니다: 모든 최신 브라우저 및 gRPC 웹 프록시 서버에서 지 원하는 JavaScript 클라이언트입니다. GRPC 웹 클라이언트 프록시를 호출 하 고 프록시 gRPC 서버로 gRPC 요청에 전달 됩니다.

GRPC의 기능 중 일부만 gRPC 웹에서 지원 됩니다. 클라이언트와 양방향 스트리밍 지원 되지 않습니다 및 스트리밍 서버에 대 한 지원은 제한 됩니다.

### <a name="not-human-readable"></a>하지 사람이 읽을 수 있음

HTTP API 요청 텍스트로 전송 됩니다 및 읽고 사람이 만든 될 수 있습니다.

기본적으로 gRPC 메시지 Protobuf를 사용 하 여 인코딩됩니다. Protobuf를 받고 보내는 데 효율적인 이진 형식으로 아닙니다 사람이 읽을 수 있습니다. Protobuf에 지정 된 메시지의 인터페이스 설명이 필요 합니다 `*.proto` 파일을 제대로 deserialize 합니다. 추가 도구는 통신 중에 Protobuf 페이로드를 분석 하 고 요청을 직접 작성할 필요 합니다.

같은 기능 [server 리플렉션](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) 하며 [gRPC 명령줄 도구](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) 이진 Protobuf 메시지 지원 하기 위해 존재 합니다. 또한 Protobuf 메시지 지원 [JSON 변환을](https://developers.google.com/protocol-buffers/docs/proto3#json)입니다. 기본 제공 JSON 변환을 디버깅할 때와 사람이 읽을 수 있는 형식에서 Protobuf 메시지를 변환 하는 효율적인 방법을 제공 합니다.

## <a name="alternative-framework-scenarios"></a>대체 framework 시나리오

다음 시나리오에서 gRPC를 통해 다른 프레임 워크를 사용 하는 것이 좋습니다.

* **액세스할 Api 브라우저** &ndash; gRPC 브라우저에서 완전히 지원 되지 않습니다. gRPC 웹 브라우저 지원에 제공할 수 있지만 제한이 서버 프록시를 소개 합니다.
* **실시간 통신 브로드캐스트** &ndash; gRPC, 스트리밍을 통해 실시간 통신을 지원 하지만 등록 된 연결에 out 메시지를 브로드캐스팅하 개념이 존재 하지 않습니다. 예를 들어 대화방의 모든 클라이언트에 새 채팅 메시지를 보낼 위치 대화방 시나리오에서는 각 gRPC 호출 해야 클라이언트에 새 채팅 메시지를 개별적으로 스트림 합니다. [SignalR](xref:signalr/introduction) 은이 시나리오에 대 한 유용한 프레임 워크입니다. SignalR 영구 연결 및 브로드캐스트 메시지에 대 한 기본 제공 지원의 개념을 있습니다.
* **프로세스 간 통신** &ndash; 프로세스 들어오는 gRPC 호출을 수락 하기 위해 HTTP/2 서버를 호스트 해야 합니다. Windows에 대 한 프로세스 간 통신 [파이프](/dotnet/standard/io/pipe-operations) 통신의 빠르고 간단한 메서드입니다.

## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
