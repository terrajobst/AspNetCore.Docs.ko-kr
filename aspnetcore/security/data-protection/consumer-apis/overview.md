---
title: ASP.NET core 소비자 Api 개요
author: rick-anderson
description: Api에서 ASP.NET Core 데이터 보호 라이브러리를 사용할 수 있는 다양 한 소비자의 간략 한 개요를 수신 합니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: b0d11d097ee2d448b6781f6fa84445f6400fbc76
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087551"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>ASP.NET core 소비자 Api 개요

`IDataProtectionProvider` 와 `IDataProtector` 는 소비자가 데이터 보호 시스템을 사용할 때 가장 기본이 되는 인터페이스입니다. 이러한 인터페이스는 [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) 패키지에 포함되어 있습니다.

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

공급자 인터페이스는 데이터 보호 시스템의 루트를 나타냅니다. 그러나 이 인터페이스를 데이터 보호나 보호 해제 작업에 직접 사용할 수는 없습니다. 대신 소비자는 `IDataProtectionProvider.CreateProtector(purpose)` 를 호출해서 `IDataProtector`¨의 참조를 가져와야 하며, 여기서 "용도"는 사용 사례에 관한 소비자의 의도를 설명하는 문자열입니다. 이 매개 변수의 의미와 적절한 값을 선택하는 방법에 대한 자세한 내용은 [용도 문자열](xref:security/data-protection/consumer-apis/purpose-strings) 을 참조하세요.

## <a name="idataprotector"></a>IDataProtector

보호자 인터페이스는 `CreateProtector` 호출에 의해 반환되며, 바로 이 인터페이스를 사용하여 소비자는 보호 및 보호 해제 작업을 수행할 수 있습니다.

데이터를 보호하려면 데이터를 `Protect` 메서드에 전달하면 됩니다. 기본 인터페이스는 byte[]를 byte[]로 변환하는 메서드를 정의하지만, 문자열을 문자열로 변환하는 오버로드 메서드도 (확장 메서드의 형태로) 제공됩니다. 두 메서드의 결과는 보안적으로 동일하므로 개발자는 자신의 상황에 가장 적합한 오버로드를 선택하면 됩니다. 어떤 오버로드 메서드를 선택해도 `Protect` 메서드에서 반환된 값은 이제 보호되며 (암호화 및 변조 방지되며), 응용 프로그램은 이 값을 신뢰할 수 없는 클라이언트로 전송할 수 있습니다.

보호된 데이터를 다시 보호 해제하려면 보호된 데이터를 `Unprotect` 메서드에 전달하면 됩니다. (개발자의 편의를 위해서 byte[]를 byte[]로 변환하는 오버로드와 문자열을 문자열로 변환하는 오버로드가 모두 제공됩니다.) 동일한 `IDataProtector` 인스턴스의 `Protect` 메서드를 호출해서 생성된 보호된 페이로드의 경우 `Unprotect` 메서드를 호출하면 보호 해제된 원본 페이로드가 반환됩니다. 그러나 보호된 페이로드가 변조됐거나 다른 `IDataProtector` 인스턴스에 의해 만들어진 페이로드일 경우 `Unprotect` 메서드가 `CryptographicException`을 던집니다.

`IDataProtector` 의 동일 여부를 판단하는 기준은 용도의 개념과 밀접한 관계를 갖고 있습니다. 비록 두 `IDataProtector` 인스턴스가 동일한 루트 `IDataProtectionProvider` 로부터 생성되었더라도, `IDataProtectionProvider.CreateProtector` 를 호출할 때 서로 다른 용도 문자열을 지정했다면, 두 인스턴스는 [별개의 보호자](xref:security/data-protection/consumer-apis/purpose-strings) 로 간주되며, 서로 다른 인스턴스가 보호한 페이로드를 보호 해제할 수 없습니다.

## <a name="consuming-these-interfaces"></a>인터페이스 소비하기

DI-인식 구성 요소의 경우, 구성 요소의 생성자에서 `IDataProtectionProvider` 매개 변수를 전달 받고, 구성 요소의 인스턴스가 만들어질 때 DI 시스템이 자동으로 이 서비스를 제공하는 방식으로 사용하도록 만들어졌습니다.

> [!NOTE]
> 일부 응용 프로그램(예: 콘솔 응용 프로그램 또는 ASP.NET 4.x 응용 프로그램)은 DI를 인식하지 못할 수도 있으므로 본문에서 설명하는 메커니즘을 사용할 수 없습니다. 이러한 시나리오의 경우, DI를 거치지 않고 `IDataProtection` 공급자의 인스턴스를 얻는 방법에 대한 자세한 정보는 [비 DI-인식 시나리오](xref:security/data-protection/configuration/non-di-scenarios) 를 참조하세요.

다음 예제는 세 가지 개념을 보여줍니다.

1. [추가 데이터 보호 시스템](xref:security/data-protection/configuration/overview) 서비스 컨테이너에

2. DI를 통해서 `IDataProtectionProvider` 의 인스턴스 가져오기

3. `IDataProtectionProvider` 로부터 `IDataProtector` 의 인스턴스를 생성하고, 이를 이용해서 데이터를 보호하고 보호 해제하기.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Microsoft.AspNetCore.DataProtection.Abstractions 패키지에는 개발자의 편의를 위한 `IServiceProvider.GetDataProtector` 확장 메서드가 포함되어 있습니다. 이 확장 메서드는 서비스 공급자에서 `IDataProtectionProvider` 를 얻고 `IDataProtectionProvider.CreateProtector` 를 호출하는 과정을 단일 작업으로 축약시켜줍니다. 다음 예제는 이 확장 메서드의 사용 방법을 보여줍니다.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> `IDataProtectionProvider` 및 `IDataProtector` 의 인스턴스는 다중 호출자에 대해 스레드로부터 안전(Thread-Safe)합니다. 구성 요소에서 `CreateProtector` 를 호출해서 `IDataProtector` 의 참조를 얻은 다음, 이 참조를 이용해서 `Protect` 및 `Unprotect` 를 반복적으로 호출할 수 있도록 만들어졌습니다. `Unprotect` 호출 시 보호된 페이로드를 검증하거나 판독할 수 없으면 `CryptographicException`이 던져집니다. 일부 구성 요소는 보호 해제 작업 중 발생하는 오류를 무시해야 하는 경우도 있는데, 가령 인증 쿠키를 읽는 구성 요소는 요청 자체를 실패시키는 대신 이 오류를 처리하여 쿠키가 아예 존재하지 않는 것처럼 동작할 수 있습니다. 이런 동작이 필요한 구성 요소는 모든 예외를 감춰버리는 대신 명확하게 `CryptographicException`만 잡아야 합니다.
