---
title: ASP.NET Core의 데이터 보호 키 관리 및 수명
author: rick-anderson
description: ASP.NET Core의 데이터 보호 키 관리 및 수명에 관해서 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 2f022a4c7519485fe629ce47c27d214c8c27d5bc
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897280"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>ASP.NET Core의 데이터 보호 키 관리 및 수명

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>키 관리

응용 프로그램은 자신이 실행되는 운영 환경을 감지해서 자체적으로 키 구성을 처리하려고 시도합니다.

1. 앱에서 호스팅되는 경우 [Azure 앱](https://azure.microsoft.com/services/app-service/), 키에 유지 되는 *%HOME%\ASP.NET\DataProtection-Keys* 폴더입니다. 이 폴더는 네트워크 스토리지에서 지원하고, 앱을 호스트하는 모든 머신에서 동기화됩니다.
   * 저장된 키는 보호되지 않습니다.
   * *DataProtection-Keys* 폴더는 단일 배포 슬롯에 위치한 응용 프로그램의 모든 인스턴스에 키 링을 제공합니다.
   * 준비 및 프로덕션과 같은 별도의 배포 슬롯은 키 링을 공유하지 않습니다. 예를 들어 스테이징 및 프로덕션을 교환 또는 A를 사용 하 여 배포 슬롯 간에 교환할 때 / B 테스트, 데이터 보호를 사용 하 여 모든 앱은 이전 슬롯 내 키 링을 사용 하 여 저장 된 데이터를 해독할 수 없습니다. 이렇게 하면 사용자 데이터 보호를 사용 하 여 쿠키를 보호 하기 위해 표준 Asp.net 쿠키 인증을 사용 하는 앱에서 기록 합니다. 슬롯에 관계 없는 키 링을 설치 하려는 경우 Azure Blob Storage, Azure Key Vault를 SQL 저장소 등의 외부 키 링 공급자를 사용 하거나 Redis 캐시 합니다.

1. 사용자 프로필이 사용 가능한 경우에는 *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* 폴더에 키가 저장됩니다. 운영 체제가 Windows DPAPI를 사용 하 여 미사용 키 암호화 됩니다.

   앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다. `setProfileEnvironment` 의 기본값은 `true`입니다. Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다. 키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.

   1. *%windir%/system32/inetsrv/config* 폴더로 이동합니다.
   1. *applicationHost.config* 파일을 엽니다.
   1. `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.
   1. `setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.

1. 응용 프로그램이 IIS에서 호스트되는 경우에는 작업자 프로세스 계정에만 ACL로 허용된 HKLM 레지스트리 하위의 특수한 레지스트리 키에 키가 저장됩니다. 저장된 비활성 키는 DPAPI를 통해서 암호화됩니다.

1. 지금까지 살펴본 모든 조건과 일치하지 않으면 현재 프로세스 외부에서는 키가 유지되지 않습니다. 따라서 프로세스가 종료되면 생성된 모든 키가 사라집니다.

개발자는 언제나 모든 제어 권한을 갖고 있으며 키가 저장되는 방식과 위치를 재정의할 수 있습니다. 위의 처음 세 가지 옵션은 대부분의 앱과 유사한 방법에 대 한 적절 한 기본값을 제공 해야 ASP.NET  **\<machineKey >** 자동 생성 루틴 이전에 작동 합니다. 유일하게 마지막 대체 옵션만 키 지속성을 원할 경우 개발자가 사전 [구성](xref:security/data-protection/configuration/overview) 작업을 수행해야 하는 시나리오지만, 이런 대체 시나리오는 매우 드물게 발생합니다.

Docker 볼륨 (공유 볼륨 또는 컨테이너의 수명을 넘어 지속 되는 호스트에 탑재 된 볼륨)가 있는 폴더에 키를 유지 해야 경우 Docker 컨테이너에서 호스팅, 또는 외부 공급자와 같은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 또는 [Redis](https://redis.io/)합니다. 앱 공유 네트워크 볼륨에 액세스할 수 없는 경우 외부 공급자로 웹 팜 시나리오에 유용한 이기도 (참조 [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) 자세한).

> [!WARNING]
> 개발자가 기본 구성을 재정의해서 데이터 보호 시스템이 특정 키 저장소를 바라보도록 지정하면, 저장된 비활성 키의 자동 암호화가 비활성화됩니다. 저장된 비활성 키의 보호 기능은 [구성](xref:security/data-protection/configuration/overview)을 통해서 다시 활성화시킬 수 있습니다.

## <a name="key-lifetime"></a>키 수명

기본적으로 90 일 수명 동안을 보유 하는 키입니다. 키 만료 되 면 앱 자동으로 새 키를 생성 하 고 새 키를 활성 키로 설정 합니다. 만료 된 키 시스템에 남아를 앱으로 보호 되는 모든 데이터를 해독할 수 있습니다. 참조 [키 관리](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) 자세한 내용은 합니다.

## <a name="default-algorithms"></a>기본 알고리즘

기본 페이로드 보호 알고리즘 사용은 AES-256-CBC 기밀성 및 HMACSHA256에 대 한 신뢰성에 대 한 합니다. 90 일 마다 변경 512 비트 마스터 키로 페이로드 당 단위로 이러한 알고리즘에 사용 되는 두 개의 하위 키 파생에 사용 됩니다. 참조 [파생 하위 키](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) 자세한 내용은 합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
