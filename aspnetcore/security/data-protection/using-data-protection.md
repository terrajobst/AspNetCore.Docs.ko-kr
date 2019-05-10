---
title: ASP.NET core에서 데이터 보호 Api 시작
author: rick-anderson
description: 보호 하 고 앱에서 데이터를 보호 해제에 대 한 ASP.NET Core 데이터 보호 Api를 사용 하는 방법에 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 25bf099a3d9edd7e6e0872725cbc3707750314e6
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087634"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>ASP.NET core에서 데이터 보호 Api 시작

<a name="security-data-protection-getting-started"></a>

데이터 보호 작업을 간단히 정리하면 다음 단계로 구성됩니다.

1. 데이터 보호 공급자를 이용해서 데이터 보호자를 생성합니다.

2. 보호할 데이터를 대상으로 `Protect` 메서드를 호출합니다.

3. 평문으로 되돌릴 데이터를 대상으로 `Unprotect` 메서드를 호출합니다.

대부분의 프레임 워크 및 ASP.NET Core SignalR 등 앱 모델을 이미 데이터 보호 시스템을 구성 하 고 종속성 주입을 통해 액세스 하는 서비스 컨테이너에 추가 합니다. 다음 샘플 종속성 주입을 위한 서비스 컨테이너를 구성 및 데이터 보호 스택이 등록, DI 통해 데이터 보호 공급자를 받는, 보호기를 만들고 데이터를 보호 한 다음 보호 해제 하는 방법을 보여 줍니다.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

보호자를 생성할 때 반드시 한 개 이상의 [용도 문자열](xref:security/data-protection/consumer-apis/purpose-strings) 을 지정해야 합니다. 용도 문자열은 소비자 간의 격리를 제공해주는 역할을 하는데. 가령 "green"이라는 용도 문자열을 이용해서 생성된 보호자는 "purple"이라는 용도 문자열을 이용해서 생성된 보호자에 의해 만들어진 데이터의 보호를 해제할 수 없습니다.

>[!TIP]
> `IDataProtectionProvider` 및 `IDataProtector` 의 인스턴스는 다중 호출자에 대해 스레드로부터 안전(Thread-Safe)합니다. 구성 요소에서 `CreateProtector` 를 호출해서 `IDataProtector` 의 참조를 얻은 다음, 이 참조를 이용해서 `Protect` 및 `Unprotect` 를 반복적으로 호출할 수 있도록 만들어졌습니다.
>
>`Unprotect` 호출 시 보호된 페이로드를 검증하거나 판독할 수 없으면 `CryptographicException`이 던져집니다. 일부 구성 요소는 보호 해제 작업 중 발생하는 오류를 무시해야 하는 경우도 있는데, 가령 인증 쿠키를 읽는 구성 요소는 요청 자체를 실패시키는 대신 이 오류를 처리하여 쿠키가 아예 존재하지 않는 것처럼 동작할 수 있습니다. 이런 동작이 필요한 구성 요소는 모든 예외를 감춰버리는 대신 명확하게 `CryptographicException`만 잡아야 합니다.
