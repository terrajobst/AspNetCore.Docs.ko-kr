---
title: ASP.NET Core에서 해지 된 키를 포함 하는 페이로드 보호 해제
author: rick-anderson
description: ASP.NET Core 앱에서 해지 된 후의 키로 보호 되는 데이터의 보호를 해제 하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 26061d048dcd9c1e3d8909e9388d8b565376fa2f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654597"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>ASP.NET Core에서 해지 된 키를 포함 하는 페이로드 보호 해제

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

ASP.NET Core 데이터 보호 Api는 주로 기밀 페이로드의 무한 지 속성에 적합 하지 않습니다. [WINDOWS CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) 및 [Azure Rights Management](/rights-management/) 와 같은 기타 기술은 무한 저장소의 시나리오에 보다 적합 하며 강력한 키 관리 기능을 제공 합니다. 즉, 기밀 데이터의 장기 보호를 위해 개발자가 ASP.NET Core 데이터 보호 Api를 사용할 필요가 없습니다. 키가 키 링에서 제거 되지 않으므로 키가 사용 가능 하 고 유효한 경우 항상 기존 페이로드를 복구할 수 `IDataProtector.Unprotect`.

그러나이 경우에는 `IDataProtector.Unprotect`에서 예외를 throw 하므로 취소 된 키로 보호 된 데이터의 보호를 해제 하려고 하면 문제가 발생 합니다. 단기 및 임시 페이로드는 (인증 토큰 같은) 시스템에서 손쉽게 재생성 할 수 있으므로 이런 유형의 페이로드는 크게 문제가 되지 않으며, 최악의 경우가 발생하더라도 사용자가 다시 로그인하면 그만입니다. 그러나 지속형 페이로드의 경우 `Unprotect` throw 하면 데이터 손실이 발생할 수 있습니다.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

해지 된 키가 있는 경우에도 페이로드의 보호를 해제할 수 있도록 하는 시나리오를 지원 하기 위해 데이터 보호 시스템에는 `IPersistedDataProtector` 형식이 포함 되어 있습니다. `IPersistedDataProtector`인스턴스를 가져오려면 일반적인 방법으로 `IDataProtector`의 인스턴스를 가져오고 `IDataProtector`를 `IPersistedDataProtector`로 캐스팅 합니다.

> [!NOTE]
> 모든 `IDataProtector` 인스턴스를 `IPersistedDataProtector`으로 캐스팅할 수 있는 것은 아닙니다. 따라서 개발자는 C#의 as 연산자나 비슷한 다른 기능을 이용해서 유효하지 않은 형변환 시도로부터 발생하는 런타임 예외를 방지하고, 형변환에 실패하는 경우에 대한 적절한 처리를 준비해야 합니다.

`IPersistedDataProtector`는 다음 API 표면을 노출 합니다.

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

이 API는 보호된 페이로드를 전달 받아서 (byte 배열로) 보호 해제된 페이로드를 반환합니다. 이 API에는 문자열 기반의 오버로드가 존재하지 않습니다. 마지막 두 가지 out 매개변수는 다음과 같습니다.

* `requiresMigration`:이 페이로드를 보호 하는 데 사용 되는 키가 더 이상 활성 기본 키가 아닌 경우 true로 설정 됩니다. 예를 들어이 페이로드를 보호 하는 데 사용 되는 키가 오래 되어 키 롤링 작업이 수행 된 후에 발생 합니다. 업무 규칙에 따라서는 호출자가 페이로드를 다시 보호해야 하는 경우도 있습니다.

* `wasRevoked`:이 페이로드를 보호 하는 데 사용 된 키가 해지 된 경우 true로 설정 됩니다.

>[!WARNING]
> `ignoreRevocationErrors: true`를 `DangerousUnprotect` 메서드에 전달할 때는 매우 주의 해야 합니다. 이 메서드를 호출한 후 `wasRevoked` 값이 true 이면이 페이로드를 보호 하는 데 사용 된 키가 해지 되 고 페이로드의 신뢰성은 주의 대상으로 처리 되어야 합니다. 그런 경우, 신뢰할 수 없는 웹 클라이언트에서 전송된 페이로드 등을 제외한, 보안 데이터베이스에서 가져온 페이로드처럼 별도로 신뢰성을 확실하게 보장할 수 있는 경우에만 보호 해제된 페이로드를 이용해서 작업을 수행해야 합니다.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
