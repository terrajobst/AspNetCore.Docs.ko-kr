---
title: ASP.NET Core에서 인증 된 암호화 세부 정보
author: rick-anderson
description: ASP.NET Core 데이터 보호 인증 된 암호화의 구현 세부 사항에 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 9def03e6b27e19fc34a839e923d6152e086889db
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896630"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>ASP.NET Core에서 인증 된 암호화 세부 정보

<a name="data-protection-implementation-authenticated-encryption-details"></a>

IDataProtector.Protect 호출은 인증 된 암호화 작업입니다. 이 특정 IDataProtector 인스턴스 IDataProtectionProvider 루트에서 파생 하는 데 사용 된 용도 체인으로 연결 됩니다 및 신뢰성과 신뢰성을 보호 메서드를 제공 합니다.

IDataProtector.Protect은 바이트 일반 텍스트 매개 변수를 사용 하 고 바이트 보호 된 페이로드를 해당 형식은 아래에 설명 된를 생성 합니다. (이기도 문자열 일반 텍스트 매개 변수 문자열이 보호 된 페이로드를 반환 하는 확장 메서드 오버 로드 합니다. 이 API를 사용 하는 경우 보호 된 페이로드 형식 에서도 계속 해 서의 구조를 아래 수는 있지만 [base64url로 인코딩된](https://tools.ietf.org/html/rfc4648#section-5).)

## <a name="protected-payload-format"></a>보호 된 페이로드 형식

보호 된 페이로드 형식 세 가지 기본 구성 요소가 구성 됩니다.

* 데이터 보호 시스템의 버전을 식별 하는 32 비트 magic 헤더입니다.

* 이 특정 페이로드를 보호 하기 위해 사용 된 키를 식별 하는 128 비트 키 id입니다.

* 보호 된 페이로드의 나머지가 [이 키에 의해 캡슐화 암호기 관련이](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)합니다. 아래 예제에서는 키는 AES-256-CBC + HMACSHA256 암호기로 나타내고 페이로드는 다음과 같이 더 세분화 합니다.
  * 128 비트 키 한정자입니다.
  * 128 비트 초기화 벡터입니다.
  * AES-256-CBC 출력의 48 바이트입니다.
  * HMACSHA256 인증 태그입니다.

샘플 보호 페이로드는 다음 그림과 같습니다.

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

첫 번째 32 비트 또는 4 바이트 위의 페이로드 형식 (09 F0 C9 F0) 버전을 확인 하는 마법 헤더에는

다음 128 비트 또는 16 바이트는 키 식별자 (80 9 C 81 0 C 19 66 19 40 95 36 53 F8 AA FF EE 57)

나머지 부분에서는 페이로드를 포함 하 고 사용 하는 형식에 따라 다릅니다.

> [!WARNING]
> 지정된 된 키를 보호 하는 모든 페이로드는 동일한 20 바이트 (magic 값, 키 id) 헤더를 사용 하 여 시작 됩니다. 관리자는 페이로드를 생성 되었을 때 근사치 진단 목적을 위해이 팩트를 사용할 수 있습니다. 예를 들어, 위 페이로드의 {0c819c80-6619-4019-9536-53f8aaffee57} 키에 해당합니다. 키 저장소를 확인 한 후는이 특정 키의 정품 인증 날짜가 2015-01-01 된 만료 날짜를 2015-03-01, 고 가정 하는 것이 적합 페이로드 (변조 되지) 하는 경우 제공 해당 창 내에서 생성 된 하거나 작은 수행 어느 쪽에 일종입니다.
