---
title: ASP.NET Core 데이터 보호의 시스템 수준 정책 지원
author: rick-anderson
description: ASP.NET Core 데이터 보호를 사용하는 모든 응용 프로그램에 적용되는 시스템 수준의 기본 정책 설정에 대한 지원에 관해서 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 70aaca7afcd3df22cebb4466fbd9845a2277688c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655065"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>ASP.NET Core 데이터 보호의 시스템 수준 정책 지원

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

데이터 보호 시스템이 Windows에서 실행되는 경우, ASP.NET Core 데이터 보호를 사용하는 모든 응용 프로그램에 적용되는 시스템 수준의 기본 정책을 제한적으로 설정할 수 있습니다. 기본적인 개념은 시스템에 존재하는 모든 응용 프로그램을 일일이 직접 변경할 필요 없이, 사용되는 알고리즘이나 키 수명 같은 일부 기본 설정을 관리자가 일괄적으로 변경해야 하는 경우도 있을 수 있다는 것입니다.

> [!WARNING]
> 시스템 관리자가 기본 정책을 설정할 수는 있지만 강제로 적용할 수는 없습니다. 응용 프로그램 개발자는 언제든지 모든 값을 자신이 선택한 값 중 하나로 재정의할 수 있습니다. 기본 정책은 개발자가 특정 설정에 대한 값을 명시적으로 지정하지 않은 응용 프로그램에만 영향을 줍니다.

## <a name="setting-default-policy"></a>기본 정책 설정하기

관리자는 시스템 레지스트리의 다음 키 하위에 알려진 값을 설정해서 기본 정책을 설정할 수 있습니다.

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

만약 64 비트 운영 체제를 사용하면서 32 비트 응용 프로그램의 동작에도 영향을 미치려면 이 키에 대응하는 Wow6432Node 하위의 키도 동일하게 설정해줘야 합니다.

지원되는 값은 다음과 같습니다.

| 값              | Type   | Description |
| ------------------ | :----: | ----------- |
| EncryptionType     | 문자열 | 데이터 보호에 사용할 알고리즘을 지정합니다. 값은 CNG-CBC, CNG-GCM 또는 Managed 중 하나여야 하고 아래에서 자세히 살펴봅니다. |
| DefaultKeyLifetime | DWORD  | 새로 생성되는 키의 수명을 지정합니다. 이 값은 일 단위로 지정되며 7보다 크거나 같아야 합니다. |
| KeyEscrowSinks     | 문자열 | 키 에스크로에 사용되는 형식들을 지정합니다. 값은 세미콜론으로 구분 된 주요 에스크로 목록으로, 목록의 각 요소는 [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink)를 구현 하는 형식의 어셈블리로 한정 된 이름입니다. |

## <a name="encryption-types"></a>암호화 형식

EncryptionType이 CNG 인 경우 시스템은 Windows CNG에서 제공 하는 서비스를 사용 하 여 기밀성 및 HMAC에 대해 CBC의 대칭 블록 암호를 사용 하도록 구성 됩니다 (자세한 내용은 [사용자 지정 WINDOWS CNG 알고리즘 지정](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) 참조). 추가적으로 다음과 같은 값들이 지원되며, 이 값들은 각각 CngCbcAuthenticatedEncryptionSettings 형식의 속성에 대응합니다.

| 값                       | Type   | Description |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | 문자열 | CNG가 인식하는 대칭형 블럭 암호화 알고리즘의 이름입니다. 이 알고리즘은 CBC 모드로 열립니다. |
| EncryptionAlgorithmProvider | 문자열 | EncryptionAlgorithm 알고리즘을 생성할 수 있는 CNG 공급자 구현의 이름입니다. |
| EncryptionAlgorithmKeySize  | DWORD  | 대칭형 블럭 암호화 알고리즘에 대해 파생되는 키의 길이(비트)입니다. |
| HashAlgorithm               | 문자열 | CNG가 인식하는 해시 알고리즘의 이름입니다. 이 알고리즘은 HMAC 모드로 열립니다. |
| HashAlgorithmProvider       | 문자열 | 알고리즘 HashAlgorithm을 생성할 수 있는 CNG 공급자 구현의 이름입니다. |

EncryptionType이 CNG 인 경우 시스템은 Windows CNG에서 제공 하는 서비스와의 기밀성 및 신뢰성을 위해 Galois/Counter 모드 대칭 블록 암호를 사용 하도록 구성 됩니다 (자세한 내용은 [사용자 지정 WINDOWS CNG 알고리즘 지정](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) 참조). 추가적으로 다음과 같은 값들이 지원되며, 이 값들은 각각 CngGcmAuthenticatedEncryptionSettings 형식의 속성에 대응합니다.

| 값                       | Type   | Description |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | 문자열 | CNG가 인식하는 대칭형 블럭 암호화 알고리즘의 이름입니다. 이 알고리즘은 갈루아(Galois)/카운터 모드로 열립니다. |
| EncryptionAlgorithmProvider | 문자열 | EncryptionAlgorithm 알고리즘을 생성할 수 있는 CNG 공급자 구현의 이름입니다. |
| EncryptionAlgorithmKeySize  | DWORD  | 대칭형 블럭 암호화 알고리즘에 대해 파생되는 키의 길이(비트)입니다. |

EncryptionType가 관리 되는 경우 시스템은 기밀성 및 KeyedHashAlgorithm에 대 한 관리 되는 System.security.cryptography.symmetricalgorithm를 사용 하도록 구성 됩니다 (자세한 내용은 [사용자 지정 관리 알고리즘 지정](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) 참조). 추가적으로 다음과 같은 값들이 지원되며, 이 값들은 각각 ManagedAuthenticatedEncryptionSettings 형식의 속성에 대응합니다.

| 값                      | Type   | Description |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | 문자열 | SymmetricAlgorithm을 구현하는 형식의 정규화된 어셈블리 이름입니다. |
| EncryptionAlgorithmKeySize | DWORD  | 대칭형 암호화 알고리즘에 대해 파생되는 키의 길이(비트)입니다. |
| ValidationAlgorithmType    | 문자열 | KeyedHashAlgorithm을 구현하는 형식의 정규화된 어셈블리 이름입니다. |

EncryptionType 값에 이 외에 다른 값이 지정되면 (null 값이나 빈 값 이외의), 데이터 보호 시스템이 구동 시 예외를 던집니다.

> [!WARNING]
> 형식 이름(EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks)에 관한 기본 정책 설정을 구성하는 경우, 반드시 응용 프로그램에서 해당 형식을 사용할 수 있어야 합니다. 결론적으로 이 말은 데스크탑 CLR 상에서 실행되는 응용 프로그램의 경우, 해당 형식을 포함한 어셈블리가 GAC에 설치되어 있어야 한다는 뜻입니다. .NET Core에서 실행 되는 ASP.NET Core 앱의 경우 이러한 형식을 포함 하는 패키지를 설치 해야 합니다.
