---
title: ASP.NET Core에서 해시 암호
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api를 사용 하 여 암호를 해시 하는 방법에 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 70301ffffbaaf3c5ff0642b19b80e40be83aa438
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896650"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="d3878-103">ASP.NET Core에서 해시 암호</span><span class="sxs-lookup"><span data-stu-id="d3878-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="d3878-104">데이터 보호 코드 베이스에는 암호화 키 파생 함수를 제공해주는 *Microsoft.AspNetCore.Cryptography.KeyDerivation* 패키지가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3878-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="d3878-105">이 패키지는 독립적인 구성 요소로서 데이터 보호 시스템의 나머지 다른 부분에 의존하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3878-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="d3878-106">이 패키지는 완벽히 독립적으로 사용이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="d3878-106">It can be used completely independently.</span></span> <span data-ttu-id="d3878-107">다만 편의상 소스가 데이터 보호 코드 베이스와 함께 위치해 있을 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="d3878-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="d3878-108">이 패키지는 [PBKDF2 알고리즘](https://tools.ietf.org/html/rfc2898#section-5.2)을 이용해서 비밀번호 해싱을 수행하는 `KeyDerivation.Pbkdf2` 메서드를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d3878-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="d3878-109">이 API는 .NET 프레임워크의 기존 [Rfc2898DeriveBytes 형식](/dotnet/api/system.security.cryptography.rfc2898derivebytes)과 매우 유사하지만, 세 가지 중요한 차이점이 존재합니다:</span><span class="sxs-lookup"><span data-stu-id="d3878-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="d3878-110">`KeyDerivation.Pbkdf2` 메서드는 다양한 PRF를 사용할 수 있는 반면 (현재 `HMACSHA1`, `HMACSHA256`, 그리고 `HMACSHA512`를 지원), `Rfc2898DeriveBytes` 형식은 `HMACSHA1`만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d3878-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="d3878-111">`KeyDerivation.Pbkdf2` 메서드는 현재 운영 체제를 감지해서 가장 최적화된 구현 루틴을 선택하기 때문에 상황에 따라 훨씬 향상된 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d3878-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="d3878-112">(Windows 8에서 `Rfc2898DeriveBytes`보다 약 10배에 가까운 성능을 보여줍니다.)</span><span class="sxs-lookup"><span data-stu-id="d3878-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="d3878-113">`KeyDerivation.Pbkdf2` 메서드는 호출자가 모든 매개 변수를 지정해야 합니다 (솔트, PRF, 그리고 반복 횟수까지).</span><span class="sxs-lookup"><span data-stu-id="d3878-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="d3878-114">반면 `Rfc2898DeriveBytes` 형식은 이에 대한 기본값들을 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="d3878-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="d3878-115">참조 된 [소스 코드](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs) ASP.NET Core Id에 대 한 `PasswordHasher` 실제 유형을 사용 사례입니다.</span><span class="sxs-lookup"><span data-stu-id="d3878-115">See the [source code](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
