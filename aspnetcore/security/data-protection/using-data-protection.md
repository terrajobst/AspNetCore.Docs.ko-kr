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
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653835"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>ASP.NET Core에서 데이터 보호 Api 시작

<a name="security-data-protection-getting-started"></a>

데이터 보호 작업을 간단히 정리하면 다음 단계로 구성됩니다.

1. 데이터 보호 공급자를 이용해서 데이터 보호자를 생성합니다.

2. 보호 하려는 데이터를 사용 하 여 `Protect` 메서드를 호출 합니다.

3. 일반 텍스트로 다시 변환할 데이터를 사용 하 여 `Unprotect` 메서드를 호출 합니다.

ASP.NET Core 또는 SignalR와 같은 대부분의 프레임 워크와 앱 모델은 이미 데이터 보호 시스템을 구성 하 고 종속성 주입을 통해 액세스 하는 서비스 컨테이너에 추가 합니다. 다음 샘플에서는 종속성 주입에 대 한 서비스 컨테이너를 구성 하 고, 데이터 보호 스택을 등록 하 고, DI를 통해 데이터 보호 공급자를 수신 하 고, 보호기를 만들고, 보호 해제 데이터를 보호 하는 방법을 보여 줍니다.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

보호기를 만들 때 하나 이상의 [용도 문자열](xref:security/data-protection/consumer-apis/purpose-strings)을 제공 해야 합니다. 용도 문자열은 소비자 간의 격리를 제공해주는 역할을 하는데. 가령 "green"이라는 용도 문자열을 이용해서 생성된 보호자는 "purple"이라는 용도 문자열을 이용해서 생성된 보호자에 의해 만들어진 데이터의 보호를 해제할 수 없습니다.

>[!TIP]
> `IDataProtectionProvider` 및 `IDataProtector` 인스턴스는 여러 호출자에 대해 스레드로부터 안전 합니다. 구성 요소가 `CreateProtector`호출을 통해 `IDataProtector`에 대 한 참조를 가져온 후에는 `Protect` 및 `Unprotect`에 대 한 여러 호출에 해당 참조를 사용 하 게 됩니다.
>
>보호 된 페이로드를 확인 하거나 해독할 수 없는 경우 `Unprotect`를 호출 하면 System.security.cryptography.cryptographicexception이 throw 됩니다. 일부 구성 요소는 보호 해제 작업 중 발생하는 오류를 무시해야 하는 경우도 있는데, 가령 인증 쿠키를 읽는 구성 요소는 요청 자체를 실패시키는 대신 이 오류를 처리하여 쿠키가 아예 존재하지 않는 것처럼 동작할 수 있습니다. 이런 동작이 필요한 구성 요소는 모든 예외를 감춰버리는 대신 명확하게 `CryptographicException`만 잡아야 합니다.
