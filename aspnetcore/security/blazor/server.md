---
title: Blazor Server 앱 보안 ASP.NET Core
author: guardrex
description: Blazor Server 앱에 대 한 보안 위협을 완화 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: 5cf83a4dd255959e8840fca3a8194b5b4e2ad0a8
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963872"
---
# <a name="secure-aspnet-core-opno-locblazor-server-apps"></a>Blazor Server 앱 보안 ASP.NET Core

[Javier Calvarro e](https://github.com/javiercn)

Blazor 서버 앱은 서버와 클라이언트에서 수명이 긴 관계를 유지 하는 *상태 저장* 데이터 처리 모델을 채택 합니다. 영구 상태는 잠재적으로 수명이 긴 연결에 걸쳐 있을 수 있는 [회로](xref:blazor/state-management)에 의해 유지 관리 됩니다.

사용자가 Blazor 서버 사이트를 방문 하면 서버는 서버 메모리에 회로를 만듭니다. 회로는 사용자가 UI에서 단추를 선택 하는 경우와 같이 렌더링할 콘텐츠를 브라우저에 표시 하 고 이벤트에 응답 합니다. 이러한 작업을 수행 하기 위해 회로는 사용자의 브라우저에서 JavaScript 함수를 호출 하 고 서버에서 .NET 메서드를 호출 합니다. 이 양방향 JavaScript 기반 상호 작용을 [javascript interop (JS interop)](xref:blazor/javascript-interop)라고 합니다.

JS interop는 인터넷을 통해 발생 하 고 클라이언트는 원격 브라우저를 사용 하기 때문에 대부분의 웹 앱 보안 문제를 공유 하는 Blazor 서버 응용 프로그램입니다. 이 항목에서는 Blazor Server 앱에 대 한 일반적인 위협에 대해 설명 하 고 인터넷 연결 앱에 초점을 맞춘 위협 완화 지침을 제공 합니다.

회사 네트워크 또는 인트라넷 내부와 같은 제한 된 환경에서 일부 완화 지침은 다음과 같습니다.

* 제약 조건 환경에는 적용 되지 않습니다.
* 제한 된 환경에서는 보안 위험이 낮으므로 구현 비용이 들지 않습니다.

## <a name="resource-exhaustion"></a>리소스 소모

리소스가 서버와 상호 작용 하 고 서버가 과도 한 리소스를 사용 하는 경우 리소스가 고갈 될 수 있습니다. 과도 한 리소스 소비가 주로 영향을 줍니다.

* [CPU](#cpu)
* [메모리](#memory)
* [클라이언트 연결](#client-connections)

DoS (서비스 거부) 공격은 일반적으로 앱 또는 서버 리소스의 고갈를 검색 합니다. 그러나 리소스 소모는 시스템의 공격에 대 한 결과일 수도 있습니다. 예를 들어 높은 사용자 수요로 인해 유한 리소스를 모두 사용할 수 있습니다. DoS는 [dos (서비스 거부) 공격](#denial-of-service-dos-attacks) 섹션에 자세히 설명 되어 있습니다.

데이터베이스 및 파일 핸들 (파일 읽기 및 쓰기에 사용 됨)과 같은 Blazor 프레임 워크 외부 리소스에는 리소스가 고갈 될 수도 있습니다. 자세한 내용은 <xref:performance/performance-best-practices>를 참조하세요.

### <a name="cpu"></a>CPU

하나 이상의 클라이언트에서 서버가 CPU 작업을 많이 수행 하도록 강제 하는 경우 CPU가 고갈 될 수 있습니다.

예를 들어 *Fibonnacci 번호*를 계산 하는 Blazor Server 앱이 있다고 가정 합니다. Fibonnacci number는 Fibonnacci 시퀀스에서 생성 됩니다. 여기서 시퀀스의 각 숫자는 위의 두 숫자의 합계입니다. 응답에 도달 하는 데 필요한 작업량은 시퀀스의 길이와 초기 값의 크기에 따라 달라 집니다. 앱이 클라이언트의 요청에 제한을 두지 않으면 CPU를 많이 사용 하는 계산이 CPU의 시간을 차지 하 고 다른 작업의 성능을 저하 시킬 수 있습니다. 과도 한 리소스 소비는 가용성에 영향을 주는 보안 문제입니다.

CPU 소모는 모든 공용 앱에서 중요 한 문제입니다. 일반 웹 앱에서 요청 및 연결은 보호로 제한 되지만 Blazor Server 앱은 동일한 보호 기능을 제공 하지 않습니다. Blazor 서버 앱은 CPU 사용량이 많은 작업을 수행 하기 전에 적절 한 검사와 제한을 포함 해야 합니다.

### <a name="memory"></a>메모리

하나 이상의 클라이언트가 서버에서 많은 양의 메모리를 강제로 사용 하는 경우 메모리 소모가 발생할 수 있습니다.

예를 들어 항목 목록을 수락 하 고 표시 하는 구성 요소가 포함 된 Blazor서버 쪽 앱을 가정해 보겠습니다. Blazor 앱에서 허용 되는 항목 수 또는 클라이언트로 다시 렌더링 되는 항목 수에 대 한 제한을 설정 하지 않으면 메모리를 많이 사용 하는 처리 및 렌더링이 서버의 메모리를 저하 시킬 수 있습니다. 서버 작동이 중단 된 것으로 표시 되 면 서버 작동이 중단 되거나 속도가 느려질 수 있습니다.

서버의 잠재적 메모리 소모 시나리오와 관련 된 항목의 목록을 유지 관리 하 고 표시 하려면 다음 시나리오를 고려 하십시오.

* `List<MyItem>` 속성 또는 필드의 항목은 서버의 메모리를 사용 합니다. 앱에서 항목 목록을 무제한으로 확장할 수 있는 경우 서버의 메모리가 부족 해질 수 있습니다. 메모리가 부족 하면 현재 세션이 종료 되 고 (충돌) 해당 서버 인스턴스의 모든 동시 세션에서 메모리 부족 예외가 수신 됩니다. 이 시나리오가 발생 하지 않도록 하려면 앱에서 동시 사용자에 게 항목 제한을 적용 하는 데이터 구조를 사용 해야 합니다.
* 렌더링에 페이징 스키마를 사용 하지 않는 경우 서버는 UI에 표시 되지 않는 개체에 추가 메모리를 사용 합니다. 항목 수를 제한 하지 않으면 메모리 요구가 사용 가능한 서버 메모리를 고갈 시킬 수 있습니다. 이 시나리오를 방지 하려면 다음 방법 중 하나를 사용 합니다.
  * 렌더링할 때 페이지가 매겨진 목록을 사용 합니다.
  * 1000 항목에 처음 100 개만 표시 하 고, 표시 된 항목 이외의 항목을 찾기 위해 사용자가 검색 조건을 입력 해야 합니다.
  * 고급 렌더링 시나리오의 경우 *가상화*를 지 원하는 목록 또는 그리드를 구현 합니다. 가상화를 사용 하면 목록에 현재 사용자에 게 표시 되는 항목의 하위 집합만 렌더링 됩니다. 사용자가 UI에서 스크롤 막대와 상호 작용할 때 구성 요소는 표시에 필요한 항목만 렌더링 합니다. 현재 표시 하는 데 필요 하지 않은 항목은 보조 저장소에 보관 될 수 있습니다 .이는 이상적인 방법입니다. 표시 되지 않은 항목은 메모리에 보관 될 수도 있으므로 그다지 유용 하지 않습니다.

Blazor Server 앱은 WPF, Windows Forms, Blazor Weasembmbambmbambambambamboma와 같은 상태 저장 앱의 다른 UI 프레임 워크에 비슷한 프로그래밍 모델 주요 차이점은 몇 가지 UI 프레임 워크에서 응용 프로그램에 사용 되는 메모리는 클라이언트에 속하며 개별 클라이언트에만 영향을 준다는 것입니다. 예를 들어 Blazor Weasembomapp은 전적으로 클라이언트에서 실행 되며 클라이언트 메모리 리소스만 사용 합니다. Blazor Server 시나리오에서 앱이 사용 하는 메모리는 서버에 속하며 서버 인스턴스의 클라이언트 간에 공유 됩니다.

서버 쪽 메모리 요구는 모든 Blazor 서버 앱에 대 한 고려 사항입니다. 그러나 대부분의 웹 앱은 상태 비저장 이며, 요청 처리 중에 사용 되는 메모리는 응답이 반환 될 때 해제 됩니다. 일반적으로 클라이언트 연결을 유지 하는 다른 서버 쪽 앱에서와 같이 클라이언트가 바인딩되지 않은 메모리 양을 할당 하도록 허용 하지 않습니다. Blazor Server 앱에서 사용 하는 메모리는 단일 요청 보다 오랜 시간 동안 지속 됩니다.

> [!NOTE]
> 개발 하는 동안 프로파일러를 사용 하거나 클라이언트의 메모리 수요를 평가 하기 위해 캡처된 추적을 사용할 수 있습니다. 프로파일러 또는 추적은 특정 클라이언트에 할당 된 메모리를 캡처하지 않습니다. 개발 하는 동안 특정 클라이언트의 메모리 사용을 캡처하려면 덤프를 캡처하고 사용자 회로에서 루트 된 모든 개체의 메모리 수요를 검사 합니다.

### <a name="client-connections"></a>클라이언트 연결

하나 이상의 클라이언트에서 서버에 대 한 동시 연결을 너무 많이 열어 다른 클라이언트가 새 연결을 설정 하지 못하게 되 면 연결이 고갈 될 수 있습니다.

Blazor 클라이언트는 세션당 단일 연결을 설정 하 고 브라우저 창이 열려 있는 동안에는 연결을 열어 둡니다. 모든 연결을 유지 관리 하는 서버에 대 한 요청은 Blazor 앱에 국한 되지 않습니다. 연결의 지속적인 특성과 Blazor Server 앱의 상태 저장 특성을 고려 하 여 연결 고갈는 응용 프로그램의 가용성에 더 많은 위험이 있습니다.

기본적으로 Blazor Server 앱에 대 한 사용자 당 연결 수에는 제한이 없습니다. 앱에 연결 제한이 필요한 경우 다음 방법 중 하나 이상을 수행 합니다.

* 인증을 요구 하 여 인증 되지 않은 사용자가 앱에 연결 하는 기능을 자연스럽 게 제한 합니다. 이 시나리오를 적용 하려면 사용자가 새 사용자를 프로 비전 할 수 없도록 합니다.
* 사용자 당 연결 수를 제한 합니다. 다음 방법을 통해 연결 제한을 수행할 수 있습니다. 합법적인 사용자가 응용 프로그램에 액세스할 수 있도록 합니다 (예: 클라이언트의 IP 주소를 기준으로 연결 제한이 설정 된 경우).
  * 앱 수준:
    * 끝점 라우팅 확장성.
    * 앱에 연결 하 고 사용자 당 활성 세션을 추적 하려면 인증이 필요 합니다.
    * 한도에 도달 하면 새 세션을 거부 합니다.
    * 클라이언트에서 앱으로의 연결을 다중 송신 하는 [Azure SignalR 서비스](/azure/azure-signalr/signalr-overview) 와 같은 프록시를 사용 하 여 앱에 대 한 프록시 WebSocket 연결입니다. 이를 통해 단일 클라이언트가 설정할 수 있는 것 보다 더 많은 연결 용량이 있는 앱을 제공 하 여 클라이언트가 서버에 대 한 연결을 고갈 하지 않도록 합니다.
  * 서버 수준: 앱 앞에 프록시/게이트웨이를 사용 합니다. 예를 들어 [Azure Front 도어](/azure/frontdoor/front-door-overview) 를 사용 하면 앱에 대 한 웹 트래픽의 글로벌 라우팅을 정의, 관리 및 모니터링할 수 있습니다.

## <a name="denial-of-service-dos-attacks"></a>DoS (서비스 거부) 공격

DoS (서비스 거부) 공격에는 서버에서 하나 이상의 리소스를 소모 하 여 앱을 사용할 수 없게 만드는 클라이언트가 포함 됩니다. Blazor Server 앱은 몇 가지 기본 제한을 포함 하 고 다른 ASP.NET Core 및 SignalR 제한을 사용 하 여 DoS 공격 으로부터 보호 합니다.

| Blazor Server 앱 제한                            | 설명 | 기본 |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | 지정 된 서버에서 한 번에 메모리에 보유 하는 연결 되지 않은 최대 회로 수입니다. | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | 연결이 끊어지기 전에 연결이 끊어진 회로가 메모리에 보관 되는 최대 시간입니다. | 3 분 |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | 비동기 JavaScript 함수 호출 시간이 초과 될 때까지 서버가 대기 하는 최대 시간입니다. | 1분 |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | 승인 되지 않은 최대 렌더링 일괄 처리 수 서버는 지정 된 시간에 회로 당 메모리에 유지 하 여 강력한 다시 연결을 지원 합니다. 한도에 도달 하면 클라이언트에서 하나 이상의 일괄 처리를 승인할 때까지 서버가 새 렌더링 일괄 처리 생성을 중지 합니다. | 10 |


| SignalR 및 ASP.NET Core 제한             | 설명 | 기본 |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | 개별 메시지의 메시지 크기입니다. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>브라우저와의 상호 작용 (클라이언트)

클라이언트는 JS interop 이벤트 디스패치 및 렌더링 완료를 통해 서버와 상호 작용 합니다. JS interop 통신은 JavaScript와 .NET 사이에 두 가지 방식으로 이루어집니다.

* 브라우저 이벤트는 비동기 방식으로 클라이언트에서 서버로 발송 됩니다.
* 서버는 필요에 따라 UI를 비동기적으로 rerendering 응답 합니다.

### <a name="javascript-functions-invoked-from-net"></a>.NET에서 호출 되는 JavaScript 함수

.NET 메서드에서 JavaScript로의 호출:

* 모든 호출에는 장애 조치 (failover) 후 구성 가능한 시간 제한이 있으므로 호출자에 게 <xref:System.OperationCanceledException>을 반환 합니다.
  * 호출 (`CircuitOptions.JSInteropDefaultCallTimeout`)의 기본 시간 제한은 1 분입니다. 이 제한을 구성 하려면 <xref:blazor/javascript-interop#harden-js-interop-calls>를 참조 하세요.
  * 취소 토큰을 제공 하 여 호출 단위로 취소를 제어할 수 있습니다. 가능 하면 기본 호출 제한 시간을 사용 하 고 취소 토큰이 제공 되는 경우 클라이언트에 대 한 시간 범위를 제한 합니다.
* JavaScript 호출 결과를 신뢰할 수 없습니다. 브라우저에서 실행 중인 Blazor 앱 클라이언트는 호출할 JavaScript 함수를 검색 합니다. 함수가 호출 되 고 결과 또는 오류가 생성 됩니다. 악의적인 클라이언트에서 다음을 시도할 수 있습니다.
  * JavaScript 함수에서 오류를 반환 하 여 앱에서 문제를 발생 시킵니다.
  * JavaScript 함수에서 예기치 않은 결과를 반환 하 여 서버에서 의도 하지 않은 동작을 유도 합니다.

앞의 시나리오를 방지 하기 위해 다음 예방 조치를 취해야 합니다.

* 호출 중에 발생할 수 있는 오류를 고려 하 여 [try-catch 문 내](/dotnet/csharp/language-reference/keywords/try-catch) 에서 JS interop 호출을 래핑합니다. 자세한 내용은 <xref:blazor/handle-errors#javascript-interop>를 참조하세요.
* 작업을 수행 하기 전에 오류 메시지를 포함 하 여 JS interop 호출에서 반환 된 데이터의 유효성을 검사 합니다.

### <a name="net-methods-invoked-from-the-browser"></a>브라우저에서 호출 된 .NET 메서드

JavaScript에서 .NET 메서드로의 호출을 신뢰 하지 않습니다. .NET 메서드가 JavaScript에 노출 되는 경우 .NET 메서드를 호출 하는 방법을 고려 합니다.

* 응용 프로그램에 대 한 공용 끝점과 같이 JavaScript에 노출 된 모든 .NET 메서드를 처리 합니다.
  * 입력의 유효성을 검사 합니다.
    * 값이 예상 범위 내에 있는지 확인 하십시오.
    * 사용자에 게 요청 된 작업을 수행할 수 있는 권한이 있는지 확인 하십시오.
  * .NET 메서드 호출의 일부로 과도 한 리소스를 할당 하지 마세요. 예를 들어 검사를 수행 하 고 CPU 및 메모리 사용에 대 한 제한을 설정 합니다.
  * 정적 및 인스턴스 메서드를 JavaScript 클라이언트에 노출할 수 있는 경우를 고려해 야 합니다. 디자인에서 적절 한 제약 조건을 사용 하 여 상태를 공유 하도록 호출 하지 않는 한 세션 간에 상태를 공유 하지 않습니다.
    * DI (종속성 주입)를 통해 원래 생성 된 `DotNetReference` 개체를 통해 노출 되는 인스턴스 메서드는 개체를 범위 개체로 등록 해야 합니다. 이는 Blazor 서버 앱에서 사용 하는 모든 DI 서비스에 적용 됩니다.
    * 정적 메서드의 경우 응용 프로그램이 서버 인스턴스의 모든 사용자에 게 의도적으로 상태를 명시적으로 공유 하는 경우를 제외 하 고는 클라이언트에 대해 범위를 지정할 수 없는 상태를 설정 하지 마세요.
  * 사용자 제공 데이터를 매개 변수에서 JavaScript 호출로 전달 하지 마십시오. 매개 변수에 데이터를 전달 해야 하는 경우에는 JavaScript 코드가 [XSS (사이트 간 스크립팅)](#cross-site-scripting-xss) 취약성을 일으키지 않고 데이터 전달을 처리 해야 합니다. 예를 들어, 요소의 `innerHTML` 속성을 설정 하 여 사용자 제공 데이터를 DOM (문서 개체 모델)에 쓰지 않습니다. `eval` 및 기타 안전 하지 않은 JavaScript 기본 형식을 사용 하지 않으려면 [CSP (콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 를 사용 하는 것이 좋습니다.
* 프레임 워크의 디스패치 구현 위에 .NET 호출의 사용자 지정 디스패치를 구현 하지 마십시오. .NET 메서드를 브라우저에 노출 하는 것이 고급 시나리오 이며 일반적인 Blazor 개발에는 권장 되지 않습니다.

### <a name="events"></a>이벤트

이벤트는 Blazor 서버 앱에 대 한 진입점을 제공 합니다. 웹 앱에서 끝점을 보호 하는 것과 동일한 규칙이 Blazor Server 앱에서 이벤트 처리에 적용 됩니다. 악의적인 클라이언트는 이벤트에 대 한 페이로드로 전송 하려는 모든 데이터를 보낼 수 있습니다.

예를 들어 다음과 같은 가치를 제공해야 합니다.

* `<select>`에 대 한 변경 이벤트는 앱이 클라이언트에 제공 하는 옵션에 포함 되지 않은 값을 보낼 수 있습니다.
* `<input>`는 클라이언트 쪽 유효성 검사를 무시 하 고 모든 텍스트 데이터를 서버에 보낼 수 있습니다.

앱에서 처리 하는 모든 이벤트에 대 한 데이터의 유효성을 검사 해야 합니다. Blazor framework [forms 구성 요소](xref:blazor/forms-validation) 는 기본 유효성 검사를 수행 합니다. 앱에서 사용자 지정 양식 구성 요소를 사용 하는 경우 적절 한 이벤트 데이터의 유효성을 검사 하기 위해 사용자 지정 코드를 작성 해야 합니다.

Blazor 서버 이벤트는 비동기적 이므로 새 렌더링을 생성 하 여 앱이 반응할 시간이 있기 전에 여러 이벤트를 서버로 디스패치할 수 있습니다. 이는 고려해 야 할 몇 가지 보안 문제를 포함 합니다. 앱에서 클라이언트 작업을 제한 하는 작업은 이벤트 처리기 내에서 수행 해야 하며 현재 렌더링 된 뷰 상태에 종속 되지 않아야 합니다.

사용자가 카운터를 최대 3 번 증가 시킬 수 있도록 하는 카운터 구성 요소를 고려 합니다. 카운터를 증가 시키는 단추는 `count`값에 따라 조건부로 결정 됩니다.

```cshtml
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

클라이언트는 프레임 워크에서이 구성 요소의 새 렌더링을 생성 하기 전에 하나 이상의 증분 이벤트를 디스패치할 수 있습니다. 따라서 단추가 UI에 의해 빠르게 제거 되지 않기 때문에 사용자가 `count`를 *세 번 이상* 증가 시킬 수 있습니다. 다음 예제에서는 3 개의 `count` 증분에 대 한 제한을 얻기 위한 올바른 방법을 보여 줍니다.

```cshtml
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

처리기 내에 `if (count < 3) { ... }` check를 추가 하 여 `count` 증가값은 현재 앱 상태를 기준으로 결정 됩니다. 이러한 결정은 이전 예제에서와 같이 일시적으로 오래 되었을 수 있는 UI의 상태를 기반으로 하지 않습니다.

### <a name="guard-against-multiple-dispatches"></a>여러 디스패치 방지

이벤트 콜백에서 외부 서비스 또는 데이터베이스에서 데이터를 가져오는 것과 같이 장기 실행 작업을 호출 하는 경우 가드를 사용 하는 것이 좋습니다. 가드는 작업이 진행 중인 동안 시각적 피드백을 사용 하 여 사용자가 여러 작업을 큐에 대기 시킬 수 없도록 합니다. 다음 구성 요소 코드는 서버에서 데이터를 가져오는 `GetForecastAsync` 동안 `true` `isLoading` 설정 합니다. `isLoading` `true`되는 동안에는 UI에서 단추가 사용 하지 않도록 설정 됩니다.

```cshtml
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

### <a name="cancel-early-and-avoid-use-after-dispose"></a>조기에 취소 하 고 사용 방지-삭제 후

[여러 디스패치 방지](#guard-against-multiple-dispatches) 섹션에 설명 된 대로 가드를 사용 하는 것 외에도 <xref:System.Threading.CancellationToken> 사용 하 여 구성 요소가 삭제 될 때 장기 실행 작업을 취소 하는 것이 좋습니다. 이 방법에는 구성 요소에서 *삭제 후 사용* 을 방지 하는 추가 혜택이 있습니다.

```cshtml
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        CancellationTokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>많은 양의 데이터를 생성 하는 이벤트 방지

`oninput` 또는 `onscroll`와 같은 일부 DOM 이벤트는 많은 양의 데이터를 생성할 수 있습니다. Blazor server 앱에서는 이러한 이벤트를 사용 하지 마십시오.

## <a name="additional-security-guidance"></a>추가 보안 지침

ASP.NET Core apps를 보호 하기 위한 지침은 Blazor Server 앱에 적용 되며 다음 섹션에서 설명 합니다.

* [로깅 및 중요 한 데이터](#logging-and-sensitive-data)
* [HTTPS를 사용 하 여 전송 중인 정보 보호](#protect-information-in-transit-with-https)
* [XSS (사이트 간 스크립팅](#cross-site-scripting-xss))
* [원본 간 보호](#cross-origin-protection)
* [클릭-킹](#click-jacking)
* [열려 있는 리디렉션](#open-redirects)

### <a name="logging-and-sensitive-data"></a>로깅 및 중요 한 데이터

클라이언트와 서버 간의 JS interop 상호 작용은 <xref:Microsoft.Extensions.Logging.ILogger> 인스턴스를 사용 하 여 서버 로그에 기록 됩니다. Blazor는 실제 이벤트 또는 JS interop 입력 및 출력과 같은 중요 한 정보를 기록 하지 않습니다.

서버에서 오류가 발생 하면 프레임 워크가 클라이언트에 알리고 세션을 중단 합니다. 기본적으로 클라이언트는 브라우저의 개발자 도구에서 볼 수 있는 일반 오류 메시지를 받습니다.

클라이언트 쪽 오류는 호출 스택을 포함 하지 않고 오류의 원인에 대 한 세부 정보를 제공 하지 않지만 서버 로그에는 이러한 정보가 포함 됩니다. 개발을 위해 자세한 오류를 사용 하 여 클라이언트에서 중요 한 오류 정보를 사용할 수 있습니다.

다음을 사용 하 여 자세한 오류 사용:

* `CircuitOptions.DetailedErrors`
* 구성 키를 `DetailedErrors` 합니다. 예를 들어 `ASPNETCORE_DETAILEDERRORS` 환경 변수를 `true`값으로 설정 합니다.

> [!WARNING]
> 인터넷의 클라이언트에 오류 정보를 노출 하는 것은 항상 피해 야 하는 보안 위험입니다.

### <a name="protect-information-in-transit-with-https"></a>HTTPS를 사용 하 여 전송 중인 정보 보호

Blazor Server는 클라이언트와 서버 간의 통신에 SignalR를 사용 합니다. Blazor 서버는 일반적으로 SignalR 협상 하는 전송 (일반적으로 Websocket)을 사용 합니다.

Blazor 서버는 서버와 클라이언트 간에 전송 되는 데이터의 무결성과 기밀성을 보장 하지 않습니다. 항상 HTTPS를 사용 합니다.

### <a name="cross-site-scripting-xss"></a>사이트 간 스크립팅 (XSS)

XSS (교차 사이트 스크립팅)를 사용 하면 권한이 없는 파티가 브라우저의 컨텍스트에서 임의 논리를 실행할 수 있습니다. 손상 된 앱은 잠재적으로 클라이언트에서 임의 코드를 실행할 수 있습니다. 이 취약점을 사용 하 여 서버에 대해 많은 악의적인 작업을 수행할 수 있습니다.

* 서버에 가짜/잘못 된 이벤트를 디스패치합니다.
* 디스패치 실패/잘못 된 렌더링 완료
* 렌더링 완료를 디스패치 하지 않습니다.
* JavaScript에서 .NET으로의 interop 호출을 디스패치합니다.
* .NET에서 JavaScript로의 interop 호출에 대 한 응답을 수정 합니다.
* .NET을 JS interop 결과로 디스패치 하지 않습니다.

Blazor 서버 프레임 워크는 위의 몇 가지 위협 으로부터 보호 하는 단계를 수행 합니다.

* 클라이언트에서 렌더링 일괄 처리를 승인 하지 않으면 새 UI 업데이트 생성을 중지 합니다. `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`으로 구성 됩니다.
* 클라이언트에서 응답을 받지 않고 1 분 후에 .NET에서 JavaScript 호출 시간을 초과 합니다. `CircuitOptions.JSInteropDefaultCallTimeout`으로 구성 됩니다.
* JS interop 중 브라우저에서 들어오는 모든 입력에 대 한 기본 유효성 검사를 수행 합니다.
  * .Net 참조는 유효 하며 .NET 메서드에서 예상 되는 형식입니다.
  * 데이터 형식이 잘못 되었습니다.
  * 메서드에 대 한 올바른 인수 개수가 페이로드에 있습니다.
  * 메서드를 호출 하기 전에 인수나 결과를 올바르게 deserialize 할 수 있습니다.
* 브라우저에서 들어오는 이벤트의 모든 입력에 기본 유효성 검사를 수행 합니다.
  * 이벤트의 형식이 잘못 되었습니다.
  * 이벤트에 대 한 데이터를 deserialize 할 수 있습니다.
  * 이벤트와 연결 된 이벤트 처리기가 있습니다.

프레임 워크에서 구현 하는 보호 기능 외에도 개발자가 앱을 코딩 하 여 위협 으로부터 보호 하 고 적절 한 조치를 취해야 합니다.

* 이벤트를 처리할 때 항상 데이터의 유효성을 검사 합니다.
* 잘못 된 데이터를 받을 때 적절 한 조치를 취합니다.
  * 데이터를 무시 하 고 반환 합니다. 이렇게 하면 앱에서 요청을 계속 처리할 수 있습니다.
  * 앱이 입력을 불법 하 고 합법적인 클라이언트에서 생성할 수 없는 것으로 확인 되 면 예외를 throw 합니다. 예외를 throw 하면 회로가 종료 되 고 세션이 종료 됩니다.
* 로그에 포함 된 렌더 일괄 처리 완료에서 제공 하는 오류 메시지를 신뢰 하지 마세요. 클라이언트는이 오류를 제공 하며 클라이언트를 손상 시킬 수 있기 때문에 일반적으로 신뢰할 수 없습니다.
* JavaScript와 .NET 메서드 사이에서 한 방향으로 JS interop 호출의 입력을 신뢰 하지 마세요.
* 응용 프로그램은 인수나 결과가 올바르게 deserialize 되더라도 인수 및 결과의 내용이 유효한 지 확인 하는 일을 담당 합니다.

XSS 취약성이 있는 경우 앱은 렌더링 된 페이지에 사용자 입력을 포함 해야 합니다. Blazor 서버 구성 요소는 *razor* 파일의 태그가 절차적 C# 논리로 변환 되는 컴파일 시간 단계를 실행 합니다. 런타임에 논리는 C# 요소, 텍스트 및 자식 구성 요소를 설명 하는 *렌더링 트리* 를 작성 합니다. 이는 JavaScript 명령 시퀀스를 통해 브라우저의 DOM에 적용 됩니다. 또는 렌더링 되지 않는 경우 HTML로 serialize 됩니다.

* 텍스트를 쓸 수 있는 명령을 통해 DOM에 Razor 구문를 추가 하기 때문에 일반 Razor 구문 (예: `@someStringValue`)를 통해 렌더링 된 사용자 입력은 XSS 취약성을 노출 하지 않습니다. 값에 HTML 태그가 포함 된 경우에도이 값은 정적 텍스트로 표시 됩니다. 렌더링을 렌더링 하면 출력이 HTML로 인코딩됩니다. 또한 콘텐츠가 정적 텍스트로 표시 됩니다.
* 스크립트 태그는 허용 되지 않으며 앱의 구성 요소 렌더링 트리에 포함 되어서는 안 됩니다. 스크립트 태그가 구성 요소의 태그에 포함 되 면 컴파일 타임 오류가 발생 합니다.
* 구성 요소 작성자는 Razor를 C# 사용 하지 않고에서 구성 요소를 작성할 수 있습니다. 구성 요소 작성자는 출력을 내보낼 때 올바른 Api를 사용 해야 합니다. 예를 들어 `builder.AddContent(0, someUserSuppliedString)`를 사용 합니다. 후자의 경우에는 XSS 취약점을 만들 수 있으므로 `builder.AddMarkupContent(0, someUserSuppliedString)`*하지 않습니다* .

XSS 공격 으로부터 보호 하는 과정에서 [CSP (콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP)와 같은 xss 완화를 구현 하는 것이 좋습니다.

자세한 내용은 <xref:security/cross-site-scripting>를 참조하세요.

### <a name="cross-origin-protection"></a>원본 간 보호

원본 간 공격은 서버에 대해 작업을 수행 하는 다른 원본의 클라이언트와 관련이 있습니다. 악의적인 동작은 일반적으로 GET 요청 또는 양식 게시 (사이트 간 요청 위조, CSRF) 이지만 악의적인 WebSocket을 여는 것도 가능 합니다. Blazor Server 앱 [은 허브 프로토콜을 사용 하는 다른 SignalR 앱과 동일한 보증](xref:signalr/security)을 제공 합니다.

* 이를 방지 하기 위해 추가 조치를 취해야 하는 경우를 제외 하 고는 Blazor 서버 앱에 액세스할 수 있습니다. 크로스-원본 액세스를 사용 하지 않도록 설정 하려면 파이프라인에 CORS 미들웨어를 추가 하 고 Blazor 끝점 메타 데이터에 `DisableCorsAttribute`를 추가 하거나 [크로스-원본 리소스 공유에 대 한 SignalR를 구성](xref:signalr/security#cross-origin-resource-sharing)하 여 허용 되는 원본 집합을 제한 하 여 끝점에서 cors를 사용 하지 않도록 설정 합니다.
* CORS를 사용 하는 경우 CORS 구성에 따라 앱을 보호 하기 위해 추가 단계가 필요할 수 있습니다. CORS를 전역적으로 사용할 수 있는 경우 `hub.MapBlazorHub()`를 호출한 후에 `DisableCorsAttribute` 메타 데이터를 끝점 메타 데이터에 추가 하 여 Blazor 서버 허브에 대해 CORS를 사용 하지 않도록 설정할 수 있습니다.

자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요.

### <a name="click-jacking"></a>클릭-킹

킹를 클릭 하면 사용자가 공격을 받는 사이트에서 작업을 수행 하는 것을 유도할 수 있도록 사이트를 다른 원본의 사이트 내에 `<iframe>` 렌더링 하는 작업이 포함 됩니다.

앱이 `<iframe>`내에서 렌더링 되지 않도록 보호 하려면 [CSP (콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 및 `X-Frame-Options` 헤더를 사용 합니다. 자세한 내용은 [MDN 웹 문서: X 프레임-옵션](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)을 참조 하세요.

### <a name="open-redirects"></a>열려 있는 리디렉션

Blazor Server 앱 세션이 시작 되 면 서버는 세션 시작의 일부로 전송 된 Url의 기본 유효성 검사를 수행 합니다. 프레임 워크는 회로를 설정 하기 전에 기준 URL이 현재 URL의 부모 인지 확인 합니다. 프레임 워크에서 추가 검사를 수행 하지 않습니다.

사용자가 클라이언트에서 링크를 선택 하면 링크에 대 한 URL이 서버로 전송 되어 수행할 작업을 결정 합니다. 예를 들어 응용 프로그램은 클라이언트 쪽 탐색을 수행 하거나 새 위치로 이동 하도록 브라우저에 지정할 수 있습니다.

구성 요소는 `NavigationManager`사용 하 여 프로그래밍 방식으로 탐색 요청을 트리거할 수도 있습니다. 이러한 시나리오에서 응용 프로그램은 클라이언트 쪽 탐색을 수행 하거나 브라우저에 새 위치로 이동 하는 것을 나타낼 수 있습니다.

구성 요소는 다음을 수행 해야 합니다.

* 탐색 호출 인수의 일부로 사용자 입력을 사용 하지 마십시오.
* 응용 프로그램에서 대상이 허용 되는지 확인 하기 위해 인수의 유효성을 검사 합니다.

그렇지 않으면 악의적인 사용자가 브라우저에서 공격자 제어 사이트로 이동 하도록 강제할 수 있습니다. 이 시나리오에서 공격자는 `NavigationManager.Navigate` 메서드 호출의 일부로 사용자 입력을 사용 하 여 앱을 트릭 합니다.

앱의 일부로 링크를 렌더링 하는 경우에도이 조언이 적용 됩니다.

* 가능 하면 상대 링크를 사용 합니다.
* 페이지에 포함 하기 전에 절대 링크 대상이 유효한 지 확인 합니다.

자세한 내용은 <xref:security/preventing-open-redirects>를 참조하세요.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

인증 및 권한 부여에 대 한 지침은 <xref:security/blazor/index>를 참조 하세요.

## <a name="security-checklist"></a>보안 검사 목록

다음 보안 고려 사항 목록은 포괄적이 아닙니다.

* 이벤트의 인수 유효성을 검사 합니다.
* JS interop 호출에서 입력 및 결과의 유효성을 검사 합니다.
* .NET to JS interop 호출에 대 한 사용자 입력 (또는 미리 유효성 검사)을 사용 하지 않습니다.
* 클라이언트에서 바인딩되지 않은 메모리 양을 할당 하지 않도록 합니다.
  * 구성 요소 내의 데이터입니다.
  * `DotNetObject` 참조를 클라이언트에 반환 합니다.
* 여러 디스패치를 방지 합니다.
* 구성 요소가 삭제 될 때 장기 실행 작업을 취소 합니다.
* 많은 양의 데이터를 생성 하는 이벤트를 방지 합니다.
* `NavigationManager.Navigate`에 대 한 호출의 일부로 사용자 입력을 사용 하지 않도록 하 고, 피할 수 없는 경우 허용 되는 원본 집합에 대 한 사용자 입력의 유효성을 검사 합니다.
* UI 상태를 기준으로 권한 부여를 결정 하지 않고 구성 요소 상태 에서만 결정 합니다.
* XSS 공격 으로부터 보호 하기 위해 [CSP (콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 를 사용 하는 것이 좋습니다.
* CSP 및 [X 프레임 옵션](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) 을 사용 하 여 킹를 방지 하는 것이 좋습니다.
* Cors를 사용 하도록 설정 하거나 Blazor 앱에 대해 CORS를 명시적으로 사용 하지 않도록 설정할 때 CORS 설정이 적절 한지 확인
* Blazor 앱에 대 한 서버 쪽 제한이 허용 되지 않는 위험 수준 없이 허용 가능한 사용자 환경을 제공 하는지 테스트 합니다.
