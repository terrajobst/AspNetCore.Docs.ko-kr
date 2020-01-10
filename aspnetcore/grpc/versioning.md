---
title: 버전 관리 gRPC 서비스
author: jamesnk
description: GRPC 서비스의 버전을 확인 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 9bd76009ba28a1abef25a98686afea6753d4a8f4
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828518"
---
# <a name="versioning-grpc-services"></a>버전 관리 gRPC 서비스

별 [뉴턴-킹](https://twitter.com/jamesnk)

앱에 추가 되는 새로운 기능에는 클라이언트에 제공 되는 gRPC 서비스가 필요할 수 있으며, 때로는 예기치 않은 방식으로 변경 될 수 있습니다. GRPC 서비스 변경 시:

* 변경 내용이 클라이언트에 미치는 영향에 대해 고려해 야 합니다.
* 변경을 지원 하기 위한 버전 관리 전략을 구현 해야 합니다.

## <a name="backwards-compatibility"></a>이전 버전과의 호환성

GRPC 프로토콜은 시간이 지남에 따라 변경 되는 서비스를 지원 하도록 설계 되었습니다. 일반적으로 gRPC 서비스 및 메서드에 대 한 추가는 중단 되지 않습니다. 주요 변경 내용으로 기존 클라이언트에서 변경 없이 계속 작업을 수행할 수 있습니다. GRPC 서비스를 변경 하거나 삭제 하는 것은 주요 변경 사항입니다. GRPC 서비스에 주요 변경 사항이 있는 경우 해당 서비스를 사용 하는 클라이언트를 업데이트 하 고 다시 배포 해야 합니다.

서비스에 대 한 주요 변경 내용을 적용 하는 경우 다음과 같은 여러 가지 이점이 있습니다.

* 기존 클라이언트는 계속 실행 됩니다.
* 클라이언트의 주요 변경 사항을 알리고 업데이트 하는 작업과 관련 된 작업을 방지 합니다.
* 서비스의 한 버전을 문서화 하 고 유지 관리 해야 합니다.

### <a name="non-breaking-changes"></a>호환성이 손상되지 않는 변경

이러한 변경은 gRPC 프로토콜 수준 및 .NET 이진 수준에서 분리 되지 않습니다.

* **새 서비스 추가**
* **서비스에 새 메서드 추가**
* **요청 메시지에 필드 추가** -요청 메시지에 추가 된 필드가 설정 되지 않은 경우 서버에서 [기본값으로](https://developers.google.com/protocol-buffers/docs/proto3#default) deserialize 됩니다. 주요 변경 사항이 되려면 이전 클라이언트에서 새 필드를 설정 하지 않은 경우 서비스에 성공 해야 합니다.
* **응답 메시지에 필드 추가** -응답 메시지에 추가 된 필드를 클라이언트의 메시지의 [알 수 없는 필드](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) 컬렉션으로 deserialize 합니다.
* **열거형 열거형에 값을 추가 하** 는 것은 숫자 값으로 serialize 됩니다. 새 열거형 값은 열거형 이름 없이 클라이언트에서 열거형 값으로 deserialize 됩니다. 주요 변경 사항이 되려면 이전 클라이언트는 새 열거형 값을 받을 때 올바르게 실행 되어야 합니다.

### <a name="binary-breaking-changes"></a>이진 주요 변경 내용

다음 변경 내용은 gRPC 프로토콜 수준에서 분리 되지 않지만 최신 *.* p i n 계약 또는 클라이언트 .net 어셈블리로 업그레이드 하는 경우 클라이언트를 업데이트 해야 합니다. GRPC 라이브러리를 NuGet에 게시 하려는 경우 이진 호환성이 중요 합니다.

* 제거 된 필드에서 **필드 값을 제거** 하면 메시지의 [알 수 없는 필드로](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)deserialize 됩니다. 이는 gRPC 프로토콜의 주요 변경 사항이 아니지만 최신 계약으로 업그레이드 하는 경우 클라이언트를 업데이트 해야 합니다. 제거 된 필드 번호는 나중에 실수로 다시 사용 하지 않는 것이 중요 합니다. 이 문제가 발생 하지 않도록 하려면 Protobuf의 [예약](https://developers.google.com/protocol-buffers/docs/proto3#reserved) 키워드를 사용 하 여 메시지에 삭제 된 필드 번호 및 이름을 지정 합니다.
* **메시지 이름 바꾸기** 는 일반적으로 네트워크에서 전송 되지 않으므로이는 grpc 프로토콜의 주요 변경 내용입니다. 최신 계약으로 업그레이드 하는 경우에는 클라이언트를 업데이트 해야 합니다. 메시지 이름이 메시지 유형을 식별 하는 데 사용 되는 경우 네트워크에서 메시지 **이름이 전송 되** 는 한 가지 경우 [는 필드입니다](https://developers.google.com/protocol-buffers/docs/proto3#any) .
* **Csharp_namespace** 변경 `csharp_namespace` 변경 하면 생성 된 .net 형식의 네임 스페이스가 변경 됩니다. 이는 gRPC 프로토콜의 주요 변경 사항이 아니지만 최신 계약으로 업그레이드 하는 경우 클라이언트를 업데이트 해야 합니다.

### <a name="protocol-breaking-changes"></a>프로토콜 주요 변경 내용

다음 항목은 프로토콜 및 이진 주요 변경 사항입니다.

* **필드 이름 바꾸기** -Protobuf 내용, 필드 이름은 생성 된 코드 에서만 사용 됩니다. 필드 번호는 네트워크에서 필드를 식별 하는 데 사용 됩니다. 필드 이름을 바꾸는 것이 Protobuf에 대 한 프로토콜 주요 변경 사항이 아닙니다. 그러나 서버에서 JSON 콘텐츠를 사용 하는 경우 필드의 이름을 바꾸는 것은 주요 변경 사항입니다.
* **필드 데이터 형식을 변경** 하면 필드의 데이터 형식을 [호환 되지 않는 형식](https://developers.google.com/protocol-buffers/docs/proto3#updating) 으로 변경 하면 메시지를 deserialize 할 때 오류가 발생 합니다. 새 데이터 형식이 호환 되는 경우에도 최신 계약으로 업그레이드 하는 경우 새 형식을 지원 하도록 클라이언트를 업데이트 해야 할 수 있습니다.
* Protobuf 페이로드를 사용 하 여 필드 **번호를 변경** 하면 필드 번호가 네트워크에서 필드를 식별 하는 데 사용 됩니다.
* **패키지 이름 바꾸기, 서비스 또는 방법** grpc는 패키지 이름, 서비스 이름 및 메서드 이름을 사용 하 여 URL을 작성 합니다. 클라이언트는 서버에서 *구현* 되지 않은 상태를 가져옵니다.
* **서비스 또는 메서드 제거** -제거 된 메서드를 호출할 때 클라이언트가 서버에서 *구현* 되지 않은 상태를 가져옵니다.

### <a name="behavior-breaking-changes"></a>동작 주요 변경 내용

중요 하지 않은 변경 내용을 적용 하는 경우 이전 클라이언트가 새 서비스 동작으로 계속 작업을 수행할 수 있는지도 고려해 야 합니다. 예를 들어 요청 메시지에 새 필드를 추가 합니다.

* 프로토콜의 주요 변경 사항이 아닙니다.
* 새 필드가 설정 되지 않은 경우 서버에서 오류 상태를 반환 하면 이전 클라이언트에 대 한 주요 변경 내용이 발생 합니다.

동작 호환성은 앱 별 코드에 의해 결정 됩니다.

## <a name="version-number-services"></a>버전 번호 서비스

서비스는 이전 클라이언트와의 호환성을 유지 하기 위해 노력 해야 합니다. 결국 앱을 변경 하면 변경 내용이 크게 필요할 수 있습니다. 이전 클라이언트를 중단 하 고 서비스와 함께 강제로 업데이트 하는 것은 좋은 사용자 환경이 아닙니다. 이전 버전과의 호환성을 유지 하는 동시에 변경 내용을 적용 하는 방법은 여러 버전의 서비스를 게시 하는 것입니다.

gRPC는 .NET 네임 스페이스와 유사 하 게 작동 하는 선택적 [패키지](https://developers.google.com/protocol-buffers/docs/proto3#packages) 지정자를 지원 합니다. 실제로는 `option csharp_namespace`을 (를) *proto* 파일에 설정 하지 않은 경우 생성 된 .NET 형식의 .net 네임 스페이스로 `package` 사용 됩니다. 패키지를 사용 하 여 서비스 및 해당 메시지의 버전 번호를 지정할 수 있습니다.

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

서비스 주소를 식별 하기 위해 패키지 이름이 서비스 이름과 결합 됩니다. 서비스 주소를 사용 하면 여러 버전의 서비스를 함께 호스팅할 수 있습니다.

* `greet.v1.Greeter`
* `greet.v2.Greeter`

버전이 지정 된 서비스의 구현은 *Startup.cs*에 등록 됩니다.

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

패키지 이름에 버전 번호를 포함 하면 *v1* 버전을 호출 하는 이전 클라이언트를 계속 지원 하면서 서비스의 *v2* 버전을 주요 변경 내용으로 게시할 수 있습니다. 클라이언트에서 *v2* 서비스를 사용 하도록 업데이트 한 후에는 이전 버전을 제거 하도록 선택할 수 있습니다. 여러 버전의 서비스를 게시 하도록 계획 하는 경우:

* 합리적인 경우 주요 변경 사항을 방지 합니다.
* 주요 변경 내용을 적용 하지 않는 한 버전 번호를 업데이트 하지 마십시오.
* 주요 변경 내용을 적용할 때 버전 번호를 업데이트 합니다.

여러 버전의 서비스를 게시 하면 중복 됩니다. 중복을 줄이려면 서비스 구현에서 이전 및 새 구현에서 다시 사용할 수 있는 중앙 위치에 비즈니스 논리를 이동 하는 것이 좋습니다.

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

서로 다른 패키지 이름을 사용 하 여 생성 된 서비스와 메시지는 **서로 다른 .net 유형**입니다. 비즈니스 논리를 중앙 위치에 이동 하려면 일반 유형에 메시지를 매핑해야 합니다.
