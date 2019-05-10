---
title: 하위 키 파생 및 ASP.NET Core에서 인증 된 암호화
author: rick-anderson
description: ASP.NET Core 데이터 보호의 구현 세부 정보 파생을 하위 키 및 암호화를 인증에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: 37e7b01700e8a6b755b5ed16a9d7d75a9eeb970e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891840"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>하위 키 파생 및 ASP.NET Core에서 인증 된 암호화

<a name="data-protection-implementation-subkey-derivation"></a>

키 링에서 대부분의 키 엔트로피 형태의 사용 될 및 "CBC 모드 암호화 + HMAC 유효성 검사" 내용의 알고리즘 정보를 갖습니다 또는 "GCM 암호화 + 유효성 검사"입니다. 이러한 경우에이 키에 대 한 마스터 키 관련 자료가 (또는 KM)으로 포함 된 엔트로피를 참조 하 고 실제 암호화 작업에 사용할 키를 파생 하는 키 파생 함수를 수행 했습니다.

> [!NOTE]
> 키 추상화 되어 있고 사용자 지정 구현을 아래와 같이 동작 하지 않을 수 있습니다. 키의 자체 구현을 제공 하는 경우 `IAuthenticatedEncryptor` 는 기본 제공 팩터리 중 하나를 사용 하는 대신이 섹션에서 설명 하는 메커니즘 적용 되지 않습니다.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>추가 인증 된 데이터 및 하위 키 파생

`IAuthenticatedEncryptor` 모든 인증 된 암호화 작업에 대 한 핵심 인터페이스로 사용 되는 인터페이스입니다. 해당 `Encrypt` 메서드는 두 개의 버퍼를 사용 합니다: 일반 텍스트 및 additionalAuthenticatedData (AAD). 일반 텍스트 콘텐츠 흐름에 대 한 호출을 변경 하지 않고 `IDataProtector.Protect`, AAD 시스템에서 생성 되 고 세 가지 구성 요소로 이루어져 있지만:

1. 32 비트 magic 헤더 09 F0 C9 F0이이 버전의 데이터 보호 시스템을 식별 하는입니다.

2. 128 비트 키 id입니다.

3. 만든 용도 체인에서 구성 되는 다양 한 길이의 문자열을 `IDataProtector` 이 작업을 수행 하는 합니다.

AAD가 모든 세 가지 컴포넌트의 튜플에 대 한 고유 하므로 새에서 키를 파생 KM KM 자체에서 모든 암호화 작업을 사용 하는 대신 사용할 수 있습니다. 호출할 때마다 `IAuthenticatedEncryptor.Encrypt`를 다음 키 파생 프로세스가 수행 됩니다.

(K_E, K_H) SP800_108_CTR_HMACSHA512 = (K_M, AAD, contextHeader | | keyModifier)

이때 카운터 모드에서 NIST SP800 108 KDF를 호출 하는 것 (참조 [NIST SP800 108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), 초로 5.1) 다음 매개 변수를 사용 하 여:

* 키 파생 키 (KDK) K_M =

* PRF = HMACSHA512

* 레이블 additionalAuthenticatedData =

* context = contextHeader || keyModifier

컨텍스트 헤더의 가변 길이 이며 기본적으로는 우리는 파생 K_E 및 K_H 알고리즘의 지문을 역할도 합니다. 키 한정자는 각 호출에 대해 임의로 생성 된 128 비트 문자열 `Encrypt` 상수는 다른 모든 KDF 입력 하는 경우에 KE 및 KH이 특정 인증 암호화 작업에 대 한 고유한 지 확률 부담을 보장 하는 데 사용 되 고 있습니다.

CBC 모드 암호화 + HMAC 유효성 검사 작업에 대 한 | K_E | 대칭 블록 암호화 키의 길이 및 | K_H | HMAC 루틴의 다이제스트 크기가입니다. GCM 암호화 + 유효성 검사 작업 | K_H | = 0.

## <a name="cbc-mode-encryption--hmac-validation"></a>CBC 모드 암호화 + HMAC 유효성 검사

K_E 위의 메커니즘을 통해 생성 되 면 임의 초기화 벡터를 생성 하 고 일반 텍스트를 암호화 하는 대칭 블록 암호화 알고리즘을 실행 합니다. 초기화 벡터 및 암호화 텍스트 K_H MAC을 생성 하기 위해 키를 사용 하 여 초기화 하는 HMAC 루틴을 통해 다음 실행 반환 값 및이 프로세스는 아래 그래픽으로 표시 됩니다.

![CBC 모드 프로세스 및 반환](subkeyderivation/_static/cbcprocess.png)

*output:= keyModifier || iv || E_cbc (K_E,iv,data) || HMAC(K_H, iv || E_cbc (K_E,iv,data))*

> [!NOTE]
> `IDataProtector.Protect` 구현 됩니다 [magic 머리글과 키 id 앞](xref:security/data-protection/implementation/authenticated-encryption-details) 호출자에 게 반환 하기 전에 출력 합니다. 매직 머리글과 키 id는 암시적으로 하기 때문에 부분 [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad), 고차원적으로 반환 된 마지막 페이로드는 MAC에서 인증 되는 키 한정자 KDF 입력으로 제공 되므로 매번 이므로 및

## <a name="galoiscounter-mode-encryption--validation"></a>Galois/카운터 모드 암호화 + 유효성 검사

K_E 위의 메커니즘을 통해 생성 되 면 임의의 96 비트 nonce를 생성 하 고 일반 텍스트를 암호화 하 고 128 비트 인증 태그를 생성 하는 대칭 블록 암호화 알고리즘을 실행 합니다.

![GCM 모드 프로세스 및 반환](subkeyderivation/_static/galoisprocess.png)

*output := keyModifier || nonce || E_gcm (K_E,nonce,data) || authTag*

> [!NOTE]
> GCM에서 기본적으로 AAD의 개념을 지 원하는 경우에에서는 여전히 저장 AAD 원래 KDF에만 해당 AAD 매개 변수에 대 한 GCM에 빈 문자열을 전달 하려면 선택 합니다. 그 이유는 두 가지가 있습니다. 먼저 [민첩성을 지원 하기 위해](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) 되지 K_M 암호화 키를 직접 사용 하려고 합니다. 또한 GCM는 해당 입력에 대해 매우 엄격한 고유성 요구 사항을 적용합니다. GCM 암호화 루틴의 두 이전 호출 또는 보다 분명 가능성 (키, nonce) 동일한 입력된 데이터 집합 2 쌍을 초과할 수 없습니다 ^32입니다. 2 개 수행할 수 없습니다. K_E를 해결 했습니다 ^32 암호화 작업의 2의 위반을 실행 했습니다 전에 ^-32를 제한 합니다. 이 작업의 매우 큰 숫자로 처럼 보일 수 있지만 트래픽이 많은 웹 서버 수 십억 4 요청을 통해 이러한 키에 대 한 일반적인 수명 내 에서도 단순한 일. 2의 호환성을 유지 하려면 ^128 비트 키 한정자 및 근본적으로 지정 된 모든 K_M에 대 한 사용 가능한 작업 수를 확장 하는 96 비트 nonce를 사용 하 여 계속-32 확률 제한 합니다. CBC 및 GCM 작업 사이의 KDF 코드 경로 디자인의 단순성에 대 한 공유 되며 AAD KDF에서 이미 간주 되므로 GCM 루틴에 전달할 필요가 없습니다.
