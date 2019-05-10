---
title: ASP.NET Core에서 키 관리
author: rick-anderson
description: ASP.NET Core 데이터 보호 키 관리 Api의 구현 세부 사항에 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: 431bdf2d3076c83279b78f327ddb647f69e6e584
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892300"
---
# <a name="key-management-in-aspnet-core"></a>ASP.NET Core에서 키 관리

<a name="data-protection-implementation-key-management"></a>

데이터 보호 시스템에는 자동으로 보호 페이로드 보호 해제 하는 데 사용 되는 마스터 키의 수명을 관리 합니다. 각 키 네 단계 중 하나에 있을 수 있습니다.

* -만든 키는 키 링에 존재 하지만 아직 활성화 되지 않았습니다. 키는 해서는 안 됩니다 사용할 새 보호 작업에 대 한 충분 한 시간이 경과할 때까지 키에 유익한이 키 링을 사용 하는 모든 컴퓨터에 전파 합니다.

* 활성-키를 키 링에 있고 새 모든 보호 작업에 사용 해야 합니다.

* 만료-키 자연 스러운 수명 기간 동안 실행 및 새 보호 작업에 대해 더 이상 사용 해야 합니다.

* 해지-키가 손상 하 고 새 보호 작업에 대해 사용할 수 없습니다.

키 생성, 활성화 및 만료 된 모든 데 사용할 수 있습니다 들어오는 페이로드 보호 해제 합니다. 기본적으로 해지 키 페이로드 보호 해제에 사용할 수 없습니다 하지만 응용 프로그램 개발자 수 [이 동작을 재정의할](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) 필요한 경우.

>[!WARNING]
> 개발자는 (예를 들어 파일 시스템에서 해당 파일을 삭제)에서 키 링에서 키를 삭제 하 고 싶은 생각이 들 수 있습니다. 이 시점에서 키로 보호 되는 모든 데이터를 영구적으로 해독할 수 없는 및 재정의가 없습니다 응급 해지 된 키와 같은 합니다. 실제로 삭제 동작에는 키를 삭제 하 고 데이터 보호 시스템이이 작업을 수행 하는 것에 대 한 최고 수준의 API가 없습니다.를 노출 하는 결과적으로 합니다.

## <a name="default-key-selection"></a>기본 키 선택

데이터 보호 시스템을 키 링이 백업 저장소에서를 읽는 경우 키 링에서 "기본" 키를 찾으려고 시도 합니다. 기본 키를 새 보호 작업에 사용 됩니다.

일반 추론 데이터 보호 시스템을 기본 키로 가장 최근 활성화 날짜를 사용 하 여 키를 선택 하는입니다. (서버-투-서버 클럭 오차를 허용 하는 작은 일종이입니다.) 키가 만료 되거나 해지 하는 경우 응용 프로그램이 비활성화 되지 않은 자동 생성 키를 당 즉시 정품 인증을 사용 하 여 새 키를 생성할 경우 합니다 [키 만료 및 롤링](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) 아래 정책입니다.

데이터 보호 시스템 이유는 새 키를 즉시 생성 하지 않고 다른 키로 대체는 만료 전에 새 키 활성화 되었던 모든 키의 암시적으로 새 키 생성을 처리 해야 함을. 일반 개념은 다른 알고리즘 또는 이전 키 보다 미사용 시 암호화 메커니즘을 사용 하 여 새 키 구성 시스템은 것을 선호할 현재 구성을 대체입니다.

예외가 있습니다. 응용 프로그램 개발자가 있으면 [자동 키 생성을 사용 하지 않도록 설정](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), 데이터 보호 시스템 기본 키로 항목을 선택 해야 합니다. 대체 (fallback)이 시나리오에서는 시스템 우선 클러스터의 다른 컴퓨터에 전파할 시간이 했던 키에 지정 된 가장 최근 활성화 날짜를 사용 하 여 해지 되지 않은 키를 선택 합니다. 대체 (fallback) 시스템은 결과적으로 만료 된 기본 키를 선택 하면 끝날 수도 있습니다. 대체 (fallback) 시스템은 기본 키로 해지 된 키를 선택 하지 않습니다 및 키 링이 비어 있거나 모든 키가 해지 된 경우 다음 시스템에서는 오류가 발생를 초기화 합니다.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>키 만료 및 롤링

키가 만들어지면 {이제 + 2 일}는 활성화 날짜와 만료 날짜가 {이제 + 90 일 동안} 자동으로 지정에 해당 합니다. 활성화 하기 전에 2 일 지연 시스템을 통해 전파 되려면 키 시간을 제공 합니다. 즉, 다른 응용 프로그램을 백업 저장소를 가리키는 경우 키 링 전파 되는 활성 해야 할 수 있는 모든 응용 프로그램 사용 가능성을 극대화 하므로 다음 자동 새로 고침 기간 동안 해당 키를 확인할 수 있습니다.

기본 키 2 일 내에 만료 되 고 키 링 기본 키의 만료 되 면 활성화 됩니다 하는 키가 없으면 데이터 보호 시스템에는 키 링에 새 키를 자동으로 유지 됩니다. 이 새 키에는 활성화 날짜 {0} 기본 키의 만료 날짜} 및 {이제 + 90 일 동안} 만료 날짜를 있습니다. 이 통해 정기적으로 서비스 중단 없이 키를 자동으로 시스템입니다.

있을 경우 즉시 정품 인증 키를 만들 위치입니다. 한 가지 예는 응용 프로그램 시간 동안 실행 되지 않은 경우 키 링에 있는 모든 키 만료 된 것입니다. 이 경우 일반 2 일 활성화 지연 시간 없이 {현재의}는 활성화 날짜 키에 지정 됩니다.

기본 키 수명은 90 일 동안이 옵션은 다음 예제와 같이 구성할 수 있습니다.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

관리자가 시스템 차원 기본값 변경할 수도 있지만 명시적으로 호출 `SetDefaultKeyLifetime` 모든 시스템 수준 정책 재정의 됩니다. 기본 키 수명은 7 일 미만일 경우 수 없습니다.

## <a name="automatic-key-ring-refresh"></a>키 링 자동 새로 고침

데이터 보호 시스템 초기화 될 때 키 링 기본 저장소에서 읽고 메모리에 캐시 합니다. 이 캐시에는 백업 저장소에 도달 하지 않고 계속 하려면 보호 및 Unprotect 작업이 허용 됩니다. 자동으로 시스템은 약 24 시간 마다 또는 현재 기본 키가 만료 되 면 둘 중 먼저 변경 내용에 대 한 백업 저장소를 확인 해야 합니다.

>[!WARNING]
> 개발자는 거의 자제하 키 관리 Api를 직접 사용 해야 합니다. 위에서 설명한 대로 데이터 보호 시스템에서 자동 키 관리를 수행 합니다.

데이터 보호 시스템 인터페이스를 노출 `IKeyManager` 검사 하 고 변경 키 링을 사용할 수 있는 합니다. 인스턴스를 제공 하는 DI 시스템 `IDataProtectionProvider` 의 인스턴스를 제공할 수도 있습니다 `IKeyManager` 사용량에 대 한 합니다. 가져올 수 있습니다 합니다 `IKeyManager` 에서 바로 `IServiceProvider` 아래 예제와 같이 합니다.

키 링 (새 키를 명시적으로 만드는 또는 해지 수행)을 수정 하는 모든 작업에는 메모리 내 캐시를 무효화 됩니다. 다음에 호출할 `Protect` 또는 `Unprotect` 하면 데이터 보호 시스템을 키 링을 다시 읽고 캐시를 다시 만듭니다.

아래 샘플에서는 `IKeyManager` 검사 하 고 취소 키를 기존 및 새 키를 수동으로 생성을 포함 하 여 키 링을 조작 하는 인터페이스입니다.

[!code-csharp[](key-management/samples/key-management.cs)]

## <a name="key-storage"></a>키 저장소

데이터 보호 시스템의 경험적 접근을 적절 한 키 저장소 위치 및 미사용 시 암호화 메커니즘을 자동으로 추론 하려고 가능해 집니다. 키 지 속성 메커니즘은 앱 개발자가 구성 가능한 이기도합니다. 다음 문서는 이러한 메커니즘의 기본 구현을 설명합니다.

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
