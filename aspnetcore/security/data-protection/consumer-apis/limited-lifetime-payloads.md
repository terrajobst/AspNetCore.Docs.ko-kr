---
title: ASP.NET Core에서 보호 된 페이로드의 수명 제한
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api를 사용 하 여 보호 된 페이로드의 수명 제한 하는 방법에 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 8dc3b856ec67477ec8ae777749c9bf3107eb4eda
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897120"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="2e100-103">ASP.NET Core에서 보호 된 페이로드의 수명 제한</span><span class="sxs-lookup"><span data-stu-id="2e100-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="2e100-104">일부 시나리오에서는 응용 프로그램 개발자가 일정 시간 후에 만료되는 보호된 페이로드를 생성해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="2e100-105">예를 들어, 비밀번호 재설정 토큰으로 한 시간 동안만 유효한 보호된 페이로드 같은 경우를 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="2e100-106">물론 개발자가 내부적으로 만료 일자를 담고 있는 페이로드 형식을 직접 만드는 것도 불가능한 일은 아니고, 오히려 고급 개발자라면 이런 방식을 더 선호할 수도 있겠지만, 만료를 관리하는 이런 작업은 대다수 개발자에게는 번거러운 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="2e100-107">개발자가 이런 작업을 손쉽게 처리할 수 있도록 [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 패키지에는 일정 시간 뒤에 자동으로 만료되는 페이로드를 생성하는 유틸리티 API들이 포함되어 있습니다</span><span class="sxs-lookup"><span data-stu-id="2e100-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="2e100-108">이 API들은 `ITimeLimitedDataProtector` 형식으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="2e100-109">API 사용 방법</span><span class="sxs-lookup"><span data-stu-id="2e100-109">API usage</span></span>

<span data-ttu-id="2e100-110">`ITimeLimitedDataProtector` 인터페이스는 시간 제한/자체 만료 페이로드를 보호하거나 보호 해제하는 작업을 수행하기 위한 핵심 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="2e100-111">`ITimeLimitedDataProtector`의 인스턴스를 생성하려면, 먼저 특정 용도를 지정해서 생성한 일반적인 [IDataProtector](xref:security/data-protection/consumer-apis/overview)의 인스턴스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="2e100-112">`IDataProtector`의 인스턴스를 얻은 뒤에 `IDataProtector.ToTimeLimitedDataProtector` 확장 메서드를 호출해서 만료 기능이 내장된 보호자를 다시 얻습니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="2e100-113">`ITimeLimitedDataProtector` 는 다음과 같은 API 표면 및 확장 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="2e100-114">CreateProtector(string purpose) : ITimeLimitedDataProtector-이 API는 기존 비슷합니다 `IDataProtectionProvider.CreateProtector` 만들려면 사용할 수 있다는 점에서 [체인 용도의](xref:security/data-protection/consumer-apis/purpose-strings) 루트 시간이 제한 된 보호기를에서.</span><span class="sxs-lookup"><span data-stu-id="2e100-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="2e100-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span><span class="sxs-lookup"><span data-stu-id="2e100-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="2e100-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span><span class="sxs-lookup"><span data-stu-id="2e100-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="2e100-117">Protect(byte[] plaintext) : byte[]</span><span class="sxs-lookup"><span data-stu-id="2e100-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="2e100-118">Protect(string plaintext, DateTimeOffset expiration) : string</span><span class="sxs-lookup"><span data-stu-id="2e100-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="2e100-119">Protect(string plaintext, TimeSpan lifetime) : string</span><span class="sxs-lookup"><span data-stu-id="2e100-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="2e100-120">Protect(string plaintext) : string</span><span class="sxs-lookup"><span data-stu-id="2e100-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="2e100-121">평문만 전달하는 핵심 `Protect` 메서드와 페이로드의 만료 일자를 지정할 수 있는 새로운 오버로드 메서드가 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="2e100-122">만료 일자는 절대 일시 (\`\`DateTimeOffset` 형식으로) 또는 상대적 시간으로 (현재 시스템 시간에 대한 `TimeSpan\` 형식으로) 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="2e100-123">만료가 지정되지 않은 오버로드 메서드가 호출되면 페이로드가 만료되지 않는 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="2e100-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span><span class="sxs-lookup"><span data-stu-id="2e100-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="2e100-125">Unprotect(byte[] protectedData) : byte[]</span><span class="sxs-lookup"><span data-stu-id="2e100-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="2e100-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span><span class="sxs-lookup"><span data-stu-id="2e100-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="2e100-127">Unprotect(string protectedData) : string</span><span class="sxs-lookup"><span data-stu-id="2e100-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="2e100-128">`Unprotect` 메서드는 보호가 해제된 원본 데이터를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="2e100-129">페이로드가 아직 만료되지 않았다면 보호되지 않은 원본 데이터와 함께 절대 만료일시가 선택적 out 매개 변수를 통해서 함께 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="2e100-130">반면 이미 페이로드가 만료됐다면 모든 `Unprotect` 오버로드 메서드가 `CryptographicException`을 던집니다</span><span class="sxs-lookup"><span data-stu-id="2e100-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="2e100-131">장기 또는 무기한 유지 해야 하는 페이로드를 보호 하기 위해 이러한 Api를 사용 하는 것에 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="2e100-132">"I 손실을 수용할 수 있는 한 달 후 영구적으로 복구할 수 없게 하려면 보호 된 페이로드"?</span><span class="sxs-lookup"><span data-stu-id="2e100-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="2e100-133">가장 좋은 방법은 차단한; 역할도 할 수 있습니다. 대답 한 경우 다음 개발자는 대체 Api를 고려해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="2e100-134">다음 예제는 데이터 보호 시스템의 인스턴스를 생성하기 위해서 [비-DI 코드 경로](xref:security/data-protection/configuration/non-di-scenarios)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="2e100-135">이 예제를 실행하려면, 먼저 Microsoft.AspNetCore.DataProtection.Extensions 패키지 참조를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e100-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
