---
title: ASP.NET Core에서 임시 데이터 보호 공급자
author: rick-anderson
description: ASP.NET Core 삭제 되는 데이터 보호 공급자의 구현 세부 사항에 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: e4b0014ab3bdbf90b91383e8a33102f94faa8153
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895180"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="33519-103">ASP.NET Core에서 임시 데이터 보호 공급자</span><span class="sxs-lookup"><span data-stu-id="33519-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="33519-104">응용 프로그램을 throwaway 해야 하는 시나리오가 있습니다 `IDataProtectionProvider`합니다.</span><span class="sxs-lookup"><span data-stu-id="33519-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="33519-105">예를 들어, 개발자 일회용 콘솔 응용 프로그램에서 실험만 될 수 있습니다 또는 응용 프로그램 자체는 일시적 (스크립팅된은 또는 단위 테스트 프로젝트).</span><span class="sxs-lookup"><span data-stu-id="33519-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="33519-106">이러한 시나리오를 지원 합니다 [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) 패키지 형식을 포함 `EphemeralDataProtectionProvider`합니다.</span><span class="sxs-lookup"><span data-stu-id="33519-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="33519-107">이 형식은의 기본 구현을 제공 `IDataProtectionProvider` 인 키 저장소만 메모리를 보유 하 고 백업 저장소에 기록 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="33519-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="33519-108">각 인스턴스의 `EphemeralDataProtectionProvider` 자체 고유 마스터 키를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="33519-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="33519-109">따라서 경우는 `IDataProtector` 에서 시작 하는 `EphemeralDataProtectionProvider` 보호 된 페이로드를 생성 해당에 의해 해당 페이로드를 보호만 있습니다 `IDataProtector` (동일한 [용도](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) 체인) 동시 루 팅 `EphemeralDataProtectionProvider` 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="33519-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="33519-110">다음 샘플을 인스턴스화하는 `EphemeralDataProtectionProvider` 보호 및 데이터를 보호 해제 하는 데 사용 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="33519-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

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
