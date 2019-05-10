---
title: ASP.NET Core에서 키 해지 된 페이로드 보호 해제
author: rick-anderson
description: 이후로 해지 된, ASP.NET Core 앱에서 키로 보호 된 데이터를 보호 해제 하는 방법에 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 26061d048dcd9c1e3d8909e9388d8b565376fa2f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897110"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="680d4-103">ASP.NET Core에서 키 해지 된 페이로드 보호 해제</span><span class="sxs-lookup"><span data-stu-id="680d4-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="680d4-104">ASP.NET Core 데이터 보호 Api는 하지 주로 기밀 페이로드 무기한 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="680d4-105">와 같은 다른 기술이 [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) 하 고 [Azure Rights Management](/rights-management/) 무기한 저장 하는 시나리오에 보다 적합 한 마찬가지로 강력한 키 관리 기능을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-105">Other technologies like [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="680d4-106">즉, ASP.NET Core 데이터 보호 Api를 사용 하 여 기밀 데이터의 장기 보호에 대 한 개발자를 금지 하는 항목이 없는 합니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="680d4-107">키가 키 링에서 하므로 제거 하지 `IDataProtector.Unprotect` 으로 키가 사용 가능 하며 올바른지에 항상 기존 페이로드를 복구할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="680d4-108">그러나 개발자가 폐기된 키를 이용해서 보호된 데이터를 보호 해제하려고 시도할 경우 문제가 발생하는데, `IDataProtector.Unprotect`가 예외를 던지기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="680d4-109">단기 및 임시 페이로드는 (인증 토큰 같은) 시스템에서 손쉽게 재생성 할 수 있으므로 이런 유형의 페이로드는 크게 문제가 되지 않으며, 최악의 경우가 발생하더라도 사용자가 다시 로그인하면 그만입니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="680d4-110">그러나 영속화된 페이로드의 경우에는 `Unprotect` 메서드가 예외를 던지는 상황이 발생하면 간과할 수 없는 데이터 유실이 발생하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="680d4-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="680d4-111">IPersistedDataProtector</span></span>

<span data-ttu-id="680d4-112">키가 폐기된 경우에도 페이로드의 보호를 해제할 수 있도록 허용해야 하는 시나리오를 지원하기 위해서 데이터 보호 시스템에는 `IPersistedDataProtector` 형식이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="680d4-113">인스턴스를 가져옵니다 `IPersistedDataProtector`를 단순히 인스턴스를 가져올 `IDataProtector` 시도 캐스팅 고 일반적인 방식으로 `IDataProtector` 에 `IPersistedDataProtector`합니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="680d4-114">모든 `IDataProtector` 인스턴스를 `IPersistedDataProtector`형식으로 형변환 할 수 있는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="680d4-115">따라서 개발자는 C#의 as 연산자나 비슷한 다른 기능을 이용해서 유효하지 않은 형변환 시도로부터 발생하는 런타임 예외를 방지하고, 형변환에 실패하는 경우에 대한 적절한 처리를 준비해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="680d4-116">`IPersistedDataProtector` 는 다음과 같은 API 표면을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="680d4-117">이 API는 보호된 페이로드를 전달 받아서 (byte 배열로) 보호 해제된 페이로드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="680d4-118">이 API에는 문자열 기반의 오버로드가 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-118">There's no string-based overload.</span></span> <span data-ttu-id="680d4-119">마지막 두 가지 out 매개변수는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="680d4-120">`requiresMigration`:페이로드를 보호하는데 사용된 키가 더 이상 활성화 된 기본 키가 아닌 경우 true로 설정됩니다 (페이로드를 보호하는데 사용된 키가 오래되어 키 롤링 작업이 발생한 경우 등).</span><span class="sxs-lookup"><span data-stu-id="680d4-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="680d4-121">업무 규칙에 따라서는 호출자가 페이로드를 다시 보호해야 하는 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="680d4-122">`wasRevoked`:페이로드를 보호하는 데 사용된 키가 폐기된 경우 true로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="680d4-123">`DangerousUnprotect` 메서드를 호출할 때 `ignoreRevocationErrors: true` 매개 변수를 true로 전달하는 경우에는 특히 주의하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="680d4-124">만약 이 메서드를 호출한 후 `wasRevoked` 값으로 true가 반환된다면, 페이로드 보호에 사용된 키가 폐기되었으며 페이로드의 신뢰성이 의심스러운 것으로 간주되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="680d4-125">그런 경우, 신뢰할 수 없는 웹 클라이언트에서 전송된 페이로드 등을 제외한, 보안 데이터베이스에서 가져온 페이로드처럼 별도로 신뢰성을 확실하게 보장할 수 있는 경우에만 보호 해제된 페이로드를 이용해서 작업을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="680d4-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
