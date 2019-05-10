---
title: 키 불변성 및 ASP.NET Core에서 키 설정
author: rick-anderson
description: ASP.NET Core 데이터 보호 키 불변성 Api 구현 세부 사항에 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 7796cb102c0f6f03809704016fd36b28c7a82438
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892290"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>키 불변성 및 ASP.NET Core에서 키 설정

개체 백업 저장소에 지속 되 면 표현 영구적으로 고정 됩니다. 새 데이터 백업 저장소에 추가할 수 있지만 기존 데이터를 변경할 수 없습니다. 이 동작의 주 목적은 데이터 손상을 방지 하기 위해입니다.

이 동작의 결과는 키를 백업 저장소에 쓴 후 변경할 수 있습니다. 생성, 활성화 및 만료 날짜는 변경할 수 없는 사용 하 여 취소할 수 있지만 `IKeyManager`합니다. 또한 해당 기본 알고리즘 정보, 마스터 키 자료가 및 나머지 속성에 대 한 암호화도 변경할 수 없습니다.

개발자 키 지 속성에 영향을 주는 설정을 변경 하는 경우 해당 변경 내용을 다루지는 효과에 대 한 명시적 호출을 통해 다음에 키가 생성 될 때까지 `IKeyManager.CreateNewKey` 또는 시스템을 통해 데이터 보호의 자체 [자동 키 생성](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 동작 합니다. 키 지 속성에 영향을 주는 설정을 아래와 같습니다.

* [기본 키 수명](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [Rest 메커니즘 키 암호화](xref:security/data-protection/implementation/key-encryption-at-rest)

* [키 알고리즘 정보](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

이러한 설정은 다음 자동 키 롤링 시간 보다 이전 버전 시작에 필요한 경우 만드는 것이 좋습니다에 대 한 명시적 호출 `IKeyManager.CreateNewKey` 새 키를 생성 하도록 합니다. 명시적으로 활성화 날짜를 제공 해야 합니다. ({이제 + 2 일}는 경험에의 한 변경 내용을 전파 하려면 시간을 허용) 및 만료 날짜를 호출 합니다.

>[!TIP]
> 리포지토리를 터치 하는 모든 응용 프로그램에 동일한 설정을 사용 하 여 지정 해야 합니다 `IDataProtectionBuilder` 확장 메서드. 그렇지 않은 경우 유지 되는 키의 속성 키 생성 루틴을 호출 하는 특정 응용 프로그램에 종속 됩니다.
