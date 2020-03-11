---
title: ASP.NET Core 보호 된 페이로드의 수명 제한
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api를 사용 하 여 보호 된 페이로드의 수명을 제한 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 8dc3b856ec67477ec8ae777749c9bf3107eb4eda
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651441"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="fba73-103">ASP.NET Core 보호 된 페이로드의 수명 제한</span><span class="sxs-lookup"><span data-stu-id="fba73-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="fba73-104">일부 시나리오에서는 응용 프로그램 개발자가 일정 시간 후에 만료되는 보호된 페이로드를 생성해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="fba73-105">예를 들어, 비밀번호 재설정 토큰으로 한 시간 동안만 유효한 보호된 페이로드 같은 경우를 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="fba73-106">물론 개발자가 내부적으로 만료 일자를 담고 있는 페이로드 형식을 직접 만드는 것도 불가능한 일은 아니고, 오히려 고급 개발자라면 이런 방식을 더 선호할 수도 있겠지만, 만료를 관리하는 이런 작업은 대다수 개발자에게는 번거러운 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="fba73-107">개발자 대상에 게 더 쉽게 사용할 수 있도록 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 패키지에는 설정 된 시간 후 자동으로 만료 되는 페이로드를 만들기 위한 유틸리티 api가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="fba73-108">이러한 Api는 `ITimeLimitedDataProtector` 유형의 작동이 중단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="fba73-109">API 사용 방법</span><span class="sxs-lookup"><span data-stu-id="fba73-109">API usage</span></span>

<span data-ttu-id="fba73-110">`ITimeLimitedDataProtector` 인터페이스는 시간 제한/자체 만료 페이로드를 보호 하 고 보호 해제 하는 핵심 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="fba73-111">`ITimeLimitedDataProtector`인스턴스를 만들려면 먼저 특정 용도를 사용 하 여 생성 된 일반 [IDataProtector](xref:security/data-protection/consumer-apis/overview) 인스턴스가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="fba73-112">`IDataProtector` 인스턴스를 사용할 수 있게 되 면 `IDataProtector.ToTimeLimitedDataProtector` 확장 메서드를 호출 하 여 기본 제공 만료 기능이 포함 된 보호기를 다시 받습니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="fba73-113">`ITimeLimitedDataProtector`는 다음 API 노출 및 확장 메서드를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="fba73-114">CreateProtector (문자열 용도): ITimeLimitedDataProtector-이 API는 루트 시간 제한 보호기에서 [용도 체인](xref:security/data-protection/consumer-apis/purpose-strings) 을 만드는 데 사용할 수 있다는 점에서 기존 `IDataProtectionProvider.CreateProtector`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="fba73-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span><span class="sxs-lookup"><span data-stu-id="fba73-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="fba73-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span><span class="sxs-lookup"><span data-stu-id="fba73-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="fba73-117">Protect(byte[] plaintext) : byte[]</span><span class="sxs-lookup"><span data-stu-id="fba73-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="fba73-118">Protect(string plaintext, DateTimeOffset expiration) : string</span><span class="sxs-lookup"><span data-stu-id="fba73-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="fba73-119">Protect(string plaintext, TimeSpan lifetime) : string</span><span class="sxs-lookup"><span data-stu-id="fba73-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="fba73-120">Protect(string plaintext) : string</span><span class="sxs-lookup"><span data-stu-id="fba73-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="fba73-121">일반 텍스트를 사용 하는 핵심 `Protect` 메서드 외에 페이로드의 만료 날짜를 지정할 수 있는 새로운 오버 로드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="fba73-122">만료 날짜는 절대 날짜 (`DateTimeOffset`을 통해) 또는 상대 시간 (`TimeSpan`를 통해 현재 시스템 시간에서)으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="fba73-123">만료가 지정되지 않은 오버로드 메서드가 호출되면 페이로드가 만료되지 않는 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="fba73-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span><span class="sxs-lookup"><span data-stu-id="fba73-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="fba73-125">Unprotect(byte[] protectedData) : byte[]</span><span class="sxs-lookup"><span data-stu-id="fba73-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="fba73-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span><span class="sxs-lookup"><span data-stu-id="fba73-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="fba73-127">Unprotect(string protectedData) : string</span><span class="sxs-lookup"><span data-stu-id="fba73-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="fba73-128">`Unprotect` 메서드는 보호 되지 않는 원래 데이터를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="fba73-129">페이로드가 아직 만료되지 않았다면 보호되지 않은 원본 데이터와 함께 절대 만료일시가 선택적 out 매개 변수를 통해서 함께 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="fba73-130">반면 이미 페이로드가 만료됐다면 모든 `Unprotect` 오버로드 메서드가 `CryptographicException`을 던집니다</span><span class="sxs-lookup"><span data-stu-id="fba73-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="fba73-131">이러한 Api를 사용 하 여 장기 또는 무한 지 속성을 필요로 하는 페이로드를 보호 하는 것은 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="fba73-132">"보호 된 페이로드가 한 달 후 영구적으로 복구할 수 있습니다."</span><span class="sxs-lookup"><span data-stu-id="fba73-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="fba73-133">좋은 방법으로 사용할 수 있습니다. 대답이 아니요 인 경우 개발자는 대체 Api를 고려해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="fba73-134">아래 샘플에서는 데이터 보호 시스템을 인스턴스화하기 위해 [DI 이외의 코드 경로](xref:security/data-protection/configuration/non-di-scenarios) 를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="fba73-135">이 예제를 실행하려면, 먼저 Microsoft.AspNetCore.DataProtection.Extensions 패키지 참조를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fba73-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
