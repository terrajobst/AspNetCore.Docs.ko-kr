---
title: ASP.NET Core의 임시 데이터 보호 공급자
author: rick-anderson
description: ASP.NET Core 임시 데이터 보호 공급자의 구현 세부 정보에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: e4b0014ab3bdbf90b91383e8a33102f94faa8153
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654015"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>ASP.NET Core의 임시 데이터 보호 공급자

<a name="data-protection-implementation-key-storage-ephemeral"></a>

응용 프로그램에 throwaway `IDataProtectionProvider`필요한 시나리오가 있습니다. 예를 들어, 개발자는 일회용 콘솔 응용 프로그램에서 실험 하거나 응용 프로그램 자체가 임시 (스크립팅된 또는 단위 테스트 프로젝트) 일 수 있습니다. 이러한 시나리오를 지원 하기 위해 [AspNetCore 보호](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) 패키지에 `EphemeralDataProtectionProvider`형식이 포함 되어 있습니다. 이 형식은 키 리포지토리가 메모리 내에만 저장 되 고 백업 저장소에 기록 되지 않는 `IDataProtectionProvider`의 기본 구현을 제공 합니다.

`EphemeralDataProtectionProvider`의 각 인스턴스는 고유한 마스터 키를 사용 합니다. 따라서 `EphemeralDataProtectionProvider`를 기반으로 하는 `IDataProtector` 보호 된 페이로드를 생성 하는 경우 해당 페이로드는 동일한 `EphemeralDataProtectionProvider` 인스턴스를 기반으로 하는 동일한 `IDataProtector` (동일한 [용도](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) 체인)에 의해서만 보호 되지 않을 수 있습니다.

다음 샘플에서는 `EphemeralDataProtectionProvider`를 인스턴스화하고이를 사용 하 여 데이터를 보호 하 고 보호를 해제 하는 방법을 보여 줍니다.

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
