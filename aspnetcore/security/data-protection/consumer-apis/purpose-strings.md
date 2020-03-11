---
title: ASP.NET Core의 용도 문자열
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api에서 용도 문자열이 사용 되는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 4c85423f8de7e4b784ae1bb304a884541df251b6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654003"
---
# <a name="purpose-strings-in-aspnet-core"></a>ASP.NET Core의 용도 문자열

<a name="data-protection-consumer-apis-purposes"></a>

`IDataProtectionProvider`를 사용 하는 구성 요소는 `CreateProtector` 메서드에 고유한 *용도* 매개 변수를 전달 해야 합니다. 목적 *매개 변수* 는 루트 암호화 키가 동일한 경우에도 암호화 소비자 간의 격리를 제공 하므로 데이터 보호 시스템의 보안에 내재 되어 있습니다.

소비자가 용도를 지정하면 루트 암호화 키와 용도 문자열이 함께 적용되어 해당 소비자에 고유한 하위 암호화 키가 파생됩니다. 이로 인해서 해당 소비자와 응용 프로그램 내의 모든 다른 암호화 소비자가 서로 격리되며, 해당 소비자의 페이로드를 다른 구성 요소가 읽을 수도, 다른 구성 요소의 페이로드를 해당 소비자가 읽을 수도 없게 됩니다. 또한 이러한 격리는 구성 요소에 대한 전 범주의 공격을 불가능하게 만듭니다.

![목적 다이어그램 예제](purpose-strings/_static/purposes.png)

위의 다이어그램에서 A와 B 인스턴스가 서로의 페이로드를 읽을 **수 없는** 경우에만 `IDataProtector`.

용도 문자열 자체를 암호화할 필요는 없습니다. 정상적으로 동작하는 다른 구성 요소가 사용하는 용도 문자열과 중복되지 않는 유일한 문자열을 지정하기만 하면 됩니다.

>[!TIP]
> 경험적으로 데이터 보호 API를 소비하는 구성 요소의 네임스페이스와 형식 이름을 조합해서 사용하는 것도 좋은 방법입니다. 사실상 이 정보는 중복되지 않기 때문입니다.
>
>예를 들어, 전달자 토큰의 발급을 담당하는 Contoso 프로젝트의 구성 요소는 용도 문자열로 Contoso.Security.BearerToken을 사용할 수 있습니다. 혹은 더 개선된 방법으로 Contoso.Security.BearerToken.v1을 용도 문자열로 사용할 수도 있습니다. 이렇게 버전 번호를 추가해 놓으면 이후 버전에서는 용도 문자열로 Contoso.Security.BearerToken.v2 등과 같이 사용할 수 있으므로, 페이로드에 관한한 버전 간에 서로 완벽한 격리를 제공할 수 있습니다.

`CreateProtector` 하는 데 사용 되는 매개 변수가 문자열 배열 이기 때문에 위의은 `[ "Contoso.Security.BearerToken", "v1" ]`으로 지정 될 수 있습니다. 따라서, 이를 활용하면 용도의 계층적 구조를 구성할 수 있으며 데이터 보호 시스템을 사용하는 다중 테넌트(Multi-Tenancy) 시나리오를 고려해 볼 수 있는 여지를 남겨줍니다.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> 신뢰할 수 없는 사용자의 입력이 구성 요소 용도 체인의 유일한 입력 원본이 될 수 있도록 허용해서는 안됩니다.
>
>가령, 보안 메시지의 저장을 담당하는 Contoso.Messaging.SecureMessage라는 구성 요소가 존재한다고 가정해보겠습니다. 보안 메시징 구성 요소에서 `CreateProtector([ username ])`를 호출 하는 경우 악의적인 사용자가 `CreateProtector([ "Contoso.Security.BearerToken" ])`호출 하는 구성 요소를 가져오기 위해 사용자 이름 "BearerToken"이 포함 된 계정을 만들 수 있습니다. 따라서 보안 메시징 시스템이 인증 토큰으로 인식 될 수 있는 mint 페이로드를 실수로 유발 합니다.
>
>메시징 구성 요소에 대 한 더 나은 용도 체인은 적절 한 격리를 제공 하는 `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`됩니다.

에서 제공 하는 격리와 `IDataProtectionProvider`, `IDataProtector`및 용도의 동작은 다음과 같습니다.

* 지정 된 `IDataProtectionProvider` 개체의 `CreateProtector` 메서드는이 개체를 만든 `IDataProtectionProvider` 개체와 메서드에 전달 된 용도 매개 변수에 모두 고유 하 게 연결 된 `IDataProtector` 개체를 만듭니다.

* 용도 매개 변수에 null을 지정할 수 없습니다. (용도가 배열로 지정 된 경우 배열의 길이가 0이 아니어야 하 고 배열의 모든 요소가 null이 아니어야 함을 의미 합니다.) 빈 문자열 용도는 기술적으로 허용 되지만 권장 되지 않습니다.

* 두 개 요소로 구성된 용도 인자는 동일한 문자열(서수 비교자가 사용됨)이 동일한 순서로 담겨진 경우에만 같은 것으로 간주됩니다. 단일 용도 문자열 인자의 경우, 해당 인자를 담고 있는 단일 요소 용도 배열과 동일합니다.

* 두 개의 `IDataProtector` 개체는 동일한 용도 매개 변수를 사용 하 여 동일한 `IDataProtectionProvider` 개체에서 만들어진 경우에만 동일 합니다.

* 지정 된 `IDataProtector` 개체의 경우 `Unprotect(protectedData)`를 호출 하면 해당 하는 `IDataProtector` 개체에 대해 `protectedData := Protect(unprotectedData)` 경우에만 원래 `unprotectedData` 반환 됩니다.

> [!NOTE]
> 본문에서는 일부 구성 요소가 다른 구성 요소와 동일한 용도 문자열을 의도적으로 선택하는 경우는 감안하지 않습니다. 기본적으로 이런 구성 요소는 악의적인 구성 요소로 간주되며, 데이터 보호 시스템은 이미 악의적인 코드가 작업자 프로세스 내부에서 실행 중인 경우까지 보안을 보장하지는 않습니다.
