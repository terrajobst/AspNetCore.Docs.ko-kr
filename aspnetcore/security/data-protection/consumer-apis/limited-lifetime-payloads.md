---
title: ASP.NET Core 보호 된 페이로드의 수명 제한
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api를 사용 하 여 보호 된 페이로드의 수명을 제한 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 8dc3b856ec67477ec8ae777749c9bf3107eb4eda
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651441"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>ASP.NET Core 보호 된 페이로드의 수명 제한

일부 시나리오에서는 응용 프로그램 개발자가 일정 시간 후에 만료되는 보호된 페이로드를 생성해야 할 수도 있습니다. 예를 들어, 비밀번호 재설정 토큰으로 한 시간 동안만 유효한 보호된 페이로드 같은 경우를 들 수 있습니다. 물론 개발자가 내부적으로 만료 일자를 담고 있는 페이로드 형식을 직접 만드는 것도 불가능한 일은 아니고, 오히려 고급 개발자라면 이런 방식을 더 선호할 수도 있겠지만, 만료를 관리하는 이런 작업은 대다수 개발자에게는 번거러운 작업입니다.

개발자 대상에 게 더 쉽게 사용할 수 있도록 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 패키지에는 설정 된 시간 후 자동으로 만료 되는 페이로드를 만들기 위한 유틸리티 api가 포함 되어 있습니다. 이러한 Api는 `ITimeLimitedDataProtector` 유형의 작동이 중단 됩니다.

## <a name="api-usage"></a>API 사용 방법

`ITimeLimitedDataProtector` 인터페이스는 시간 제한/자체 만료 페이로드를 보호 하 고 보호 해제 하는 핵심 인터페이스입니다. `ITimeLimitedDataProtector`인스턴스를 만들려면 먼저 특정 용도를 사용 하 여 생성 된 일반 [IDataProtector](xref:security/data-protection/consumer-apis/overview) 인스턴스가 필요 합니다. `IDataProtector` 인스턴스를 사용할 수 있게 되 면 `IDataProtector.ToTimeLimitedDataProtector` 확장 메서드를 호출 하 여 기본 제공 만료 기능이 포함 된 보호기를 다시 받습니다.

`ITimeLimitedDataProtector`는 다음 API 노출 및 확장 메서드를 노출 합니다.

* CreateProtector (문자열 용도): ITimeLimitedDataProtector-이 API는 루트 시간 제한 보호기에서 [용도 체인](xref:security/data-protection/consumer-apis/purpose-strings) 을 만드는 데 사용할 수 있다는 점에서 기존 `IDataProtectionProvider.CreateProtector`와 비슷합니다.

* Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]

* Protect(byte[] plaintext, TimeSpan lifetime) : byte[]

* Protect(byte[] plaintext) : byte[]

* Protect(string plaintext, DateTimeOffset expiration) : string

* Protect(string plaintext, TimeSpan lifetime) : string

* Protect(string plaintext) : string

일반 텍스트를 사용 하는 핵심 `Protect` 메서드 외에 페이로드의 만료 날짜를 지정할 수 있는 새로운 오버 로드가 있습니다. 만료 날짜는 절대 날짜 (`DateTimeOffset`을 통해) 또는 상대 시간 (`TimeSpan`를 통해 현재 시스템 시간에서)으로 지정할 수 있습니다. 만료가 지정되지 않은 오버로드 메서드가 호출되면 페이로드가 만료되지 않는 것으로 간주됩니다.

* Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]

* Unprotect(byte[] protectedData) : byte[]

* Unprotect(string protectedData, out DateTimeOffset expiration) : string

* Unprotect(string protectedData) : string

`Unprotect` 메서드는 보호 되지 않는 원래 데이터를 반환 합니다. 페이로드가 아직 만료되지 않았다면 보호되지 않은 원본 데이터와 함께 절대 만료일시가 선택적 out 매개 변수를 통해서 함께 반환됩니다. 반면 이미 페이로드가 만료됐다면 모든 `Unprotect` 오버로드 메서드가 `CryptographicException`을 던집니다

>[!WARNING]
> 이러한 Api를 사용 하 여 장기 또는 무한 지 속성을 필요로 하는 페이로드를 보호 하는 것은 권장 되지 않습니다. "보호 된 페이로드가 한 달 후 영구적으로 복구할 수 있습니다." 좋은 방법으로 사용할 수 있습니다. 대답이 아니요 인 경우 개발자는 대체 Api를 고려해 야 합니다.

아래 샘플에서는 데이터 보호 시스템을 인스턴스화하기 위해 [DI 이외의 코드 경로](xref:security/data-protection/configuration/non-di-scenarios) 를 사용 합니다. 이 예제를 실행하려면, 먼저 Microsoft.AspNetCore.DataProtection.Extensions 패키지 참조를 추가해야 합니다.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
