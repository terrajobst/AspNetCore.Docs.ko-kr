---
title: ASP.NET Core에 대 한 소비자 Api 개요
author: rick-anderson
description: ASP.NET Core 데이터 보호 라이브러리에서 사용할 수 있는 다양 한 소비자 Api에 대 한 간략 한 개요를 수신 합니다.
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: ff9badb55813cae0aa72d3a95dc53792332f109b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654585"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>ASP.NET Core에 대 한 소비자 Api 개요

`IDataProtectionProvider` 및 `IDataProtector` 인터페이스는 소비자가 데이터 보호 시스템을 사용 하는 기본 인터페이스입니다. 이러한 데이터는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) 패키지에 있습니다.

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

공급자 인터페이스는 데이터 보호 시스템의 루트를 나타냅니다. 그러나 이 인터페이스를 데이터 보호나 보호 해제 작업에 직접 사용할 수는 없습니다. 대신 소비자는 `IDataProtectionProvider.CreateProtector(purpose)`를 호출 하 여 `IDataProtector`에 대 한 참조를 가져와야 합니다. 여기서 용도는 의도 된 소비자 사용 사례를 설명 하는 문자열입니다. 이 매개 변수의 의도 및 적절 한 값을 선택 하는 방법에 대 한 자세한 내용은 [용도 문자열](xref:security/data-protection/consumer-apis/purpose-strings) 을 참조 하세요.

## <a name="idataprotector"></a>IDataProtector

보호기 인터페이스는 `CreateProtector`에 대 한 호출에 의해 반환 되며, 소비자는이 인터페이스를 사용 하 여 보호 및 보호 해제 작업을 수행할 수 있습니다.

데이터를 보호 하려면 `Protect` 메서드에 데이터를 전달 합니다. 기본 인터페이스는 byte[]를 byte[]로 변환하는 메서드를 정의하지만, 문자열을 문자열로 변환하는 오버로드 메서드도 (확장 메서드의 형태로) 제공됩니다. 두 메서드의 결과는 보안적으로 동일하므로 개발자는 자신의 상황에 가장 적합한 오버로드를 선택하면 됩니다. 어떤 오버로드 메서드를 선택해도 `Protect` 메서드에서 반환된 값은 이제 보호되며 (암호화 및 변조 방지되며), 응용 프로그램은 이 값을 신뢰할 수 없는 클라이언트로 전송할 수 있습니다.

이전에 보호 된 데이터 부분의 보호를 해제 하려면 보호 된 데이터를 `Unprotect` 메서드로 전달 합니다. 개발자 편의를 위해 바이트 [] 기반 및 문자열 기반 오버 로드가 있습니다. 이 동일한 `IDataProtector`에서 `Protect`에 대 한 이전 호출로 보호 된 페이로드가 생성 된 경우에는 `Unprotect` 메서드가 원래 보호 되지 않는 페이로드를 반환 합니다. 보호 된 페이로드가 변조 되었거나 다른 `IDataProtector`에 의해 생성 된 경우 `Unprotect` 메서드는 System.security.cryptography.cryptographicexception을 throw 합니다.

동일 하 고 다른 `IDataProtector` 개념은 목적 개념에 다시 연결 됩니다. 두 개의 `IDataProtector` 인스턴스가 동일한 루트 `IDataProtectionProvider`에서 생성 되었지만 `IDataProtectionProvider.CreateProtector`에 대 한 호출에서 다른 용도의 문자열을 통해 생성 되는 경우 [다른 보호기](xref:security/data-protection/consumer-apis/purpose-strings)로 간주 되 고 다른 하나는 생성 된 페이로드의 보호를 해제할 수 없습니다.

## <a name="consuming-these-interfaces"></a>인터페이스 소비하기

DI 인식 구성 요소의 경우 구성 요소가 생성자에서 `IDataProtectionProvider` 매개 변수를 사용 하 고 구성 요소가 인스턴스화될 때 DI 시스템이이 서비스를 자동으로 제공 한다는 것입니다.

> [!NOTE]
> 일부 응용 프로그램(예: 콘솔 응용 프로그램 또는 ASP.NET 4.x 응용 프로그램)은 DI를 인식하지 못할 수도 있으므로 본문에서 설명하는 메커니즘을 사용할 수 없습니다. 이러한 시나리오에서는 DI를 거치지 않고 `IDataProtection` 공급자 인스턴스를 가져오는 방법에 대 한 자세한 내용은 [비 Di 인식 시나리오](xref:security/data-protection/configuration/non-di-scenarios) 문서를 참조 하세요.

다음 예제는 세 가지 개념을 보여줍니다.

1. 서비스 컨테이너에 [데이터 보호 시스템 추가](xref:security/data-protection/configuration/overview)

2. DI를 사용 하 여 `IDataProtectionProvider`인스턴스 받기 및

3. `IDataProtectionProvider`에서 `IDataProtector`를 만들고이를 사용 하 여 데이터 보호 및 보호 해제

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

AspNetCore 패키지에는 개발자 편의를 위해 `IServiceProvider.GetDataProtector` 확장 메서드가 포함 되어 있습니다. 서비스 공급자에서 `IDataProtectionProvider`를 검색 하 고 `IDataProtectionProvider.CreateProtector`를 호출 하는 단일 작업으로 캡슐화 합니다. 다음 예제는 이 확장 메서드의 사용 방법을 보여줍니다.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> `IDataProtectionProvider` 및 `IDataProtector` 인스턴스는 여러 호출자에 대해 스레드로부터 안전 합니다. 구성 요소가 `CreateProtector`호출을 통해 `IDataProtector`에 대 한 참조를 가져온 후에는 `Protect` 및 `Unprotect`에 대 한 여러 호출에 해당 참조를 사용 하 게 됩니다. 보호 된 페이로드를 확인 하거나 해독할 수 없는 경우 `Unprotect`를 호출 하면 System.security.cryptography.cryptographicexception이 throw 됩니다. 일부 구성 요소는 보호 해제 작업 중 발생하는 오류를 무시해야 하는 경우도 있는데, 가령 인증 쿠키를 읽는 구성 요소는 요청 자체를 실패시키는 대신 이 오류를 처리하여 쿠키가 아예 존재하지 않는 것처럼 동작할 수 있습니다. 이런 동작이 필요한 구성 요소는 모든 예외를 감춰버리는 대신 명확하게 `CryptographicException`만 잡아야 합니다.
