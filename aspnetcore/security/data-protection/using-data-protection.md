---
title: ASP.NET Core에서 데이터 보호 Api 시작
author: rick-anderson
description: 앱에서 데이터를 보호 하 고 보호 해제 하는 데 ASP.NET Core 데이터 보호 Api를 사용 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 8c3f3c7fb21434cf335591c41741f0ce868df33e
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963861"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>ASP.NET Core에서 데이터 보호 Api 시작

<a name="security-data-protection-getting-started"></a>

가장 간단 하 게 데이터를 보호 하는 과정은 다음 단계로 구성 됩니다.

1. 데이터 보호 공급자에서 데이터 보호기를 만듭니다.

2. 보호 하려는 데이터를 사용 하 여 `Protect` 메서드를 호출 합니다.

3. 일반 텍스트로 다시 변환할 데이터를 사용 하 여 `Unprotect` 메서드를 호출 합니다.

ASP.NET Core 또는 SignalR와 같은 대부분의 프레임 워크와 앱 모델은 이미 데이터 보호 시스템을 구성 하 고 종속성 주입을 통해 액세스 하는 서비스 컨테이너에 추가 합니다. 다음 샘플에서는 종속성 주입에 대 한 서비스 컨테이너를 구성 하 고, 데이터 보호 스택을 등록 하 고, DI를 통해 데이터 보호 공급자를 수신 하 고, 보호기를 만들고, 보호 해제 데이터를 보호 하는 방법을 보여 줍니다.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

보호기를 만들 때 하나 이상의 [용도 문자열](xref:security/data-protection/consumer-apis/purpose-strings)을 제공 해야 합니다. 목적 문자열은 소비자 간의 격리를 제공 합니다. 예를 들어 용도 문자열이 "녹색"으로 만들어진 보호기는 "자주색"의 용도를 사용 하 여 보호기에서 제공 되는 데이터의 보호를 해제할 수 없습니다.

>[!TIP]
> `IDataProtectionProvider` 및 `IDataProtector` 인스턴스는 여러 호출자에 대해 스레드로부터 안전 합니다. 구성 요소가 `CreateProtector`호출을 통해 `IDataProtector`에 대 한 참조를 가져온 후에는 `Protect` 및 `Unprotect`에 대 한 여러 호출에 해당 참조를 사용 하 게 됩니다.
>
>보호 된 페이로드를 확인 하거나 해독할 수 없는 경우 `Unprotect`를 호출 하면 System.security.cryptography.cryptographicexception이 throw 됩니다. 일부 구성 요소는 보호 되지 않는 작업 중에 오류를 무시 하려고 할 수 있습니다. 인증 쿠키를 읽는 구성 요소가이 오류를 처리 하 고 요청을 완전히 실패 하는 대신 쿠키가 없는 것 처럼 처리할 수 있습니다. 이 동작을 원하는 구성 요소는 모든 예외를 swallowing 하는 대신 System.security.cryptography.cryptographicexception를 명확 하 게 catch 해야 합니다.
