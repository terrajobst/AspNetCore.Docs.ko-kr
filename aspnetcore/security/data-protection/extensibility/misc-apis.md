---
title: 기타 ASP.NET Core 데이터 보호 Api
author: rick-anderson
description: ASP.NET Core 데이터 보호 ISecret 인터페이스에 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 114cdd6209970e46b827e403fbe79b95692d0242
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896620"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>기타 ASP.NET Core 데이터 보호 Api

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> 다음 인터페이스 중 하나를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자에 대 한 합니다.

## <a name="isecret"></a>ISecret

`ISecret` 인터페이스에는 암호화 키 자료와 같은 비밀 값을 나타냅니다. 다음 API 화면을 포함합니다.

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer` 메서드는 원시 암호 값을 사용 하 여 제공 된 버퍼를 채웁니다. 이 API는 매개 변수 버퍼 이유 반환 하는 대신는 `byte[]` 직접는이 기회를 제공 호출자 개체를 고정 하면 버퍼를 관리 되는 가비지 수집기에 대 한 보안 노출을 제한 됩니다.

합니다 `Secret` 의 구체적 구현 형식은 `ISecret` 경우 비밀 값을 프로세스의 메모리에 저장 됩니다. Windows 플랫폼에서 암호 값을 통해 암호화 됩니다 [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)합니다.
