---
title: ASP.NET Core에서 컨텍스트 헤더
author: rick-anderson
description: ASP.NET Core 데이터 보호 상황에 맞는 헤더의 구현 세부 사항에 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 2b8fd594672bf623d38bfae90d05a984f92ce6a3
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087559"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="04457-103">ASP.NET Core에서 컨텍스트 헤더</span><span class="sxs-lookup"><span data-stu-id="04457-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="04457-104">배경 및 이론</span><span class="sxs-lookup"><span data-stu-id="04457-104">Background and theory</span></span>

<span data-ttu-id="04457-105">데이터 보호 시스템 "키"를 제공할 수 있는 개체에는 암호화 서비스 인증을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="04457-106">각 키는 고유 id (GUID)으로 식별 되 고 전달 된 알고리즘 정보와 entropic 자료입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="04457-107">각 키는 고유 entropy를 수행 하지만 시스템을 적용할 수 없습니다 하 키 링에서 기존 키 알고리즘 정보를 수정 하 여 수동으로 키 링을 변경할 수 있습니다 하는 개발자를 위한 계정 해야 것입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="04457-108">이러한 경우를 지정 하는 보안 요구 사항을 달성 하려면 데이터 보호 시스템에는 개념이 [암호화 agility](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), 여러 암호화 알고리즘에서 단일 entropic 값을 사용 하 여 안전 하 게 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04457-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="04457-109">대부분의 시스템 암호화 agility 지 페이로드 내 알고리즘에 대 한 식별 정보를 포함 하 여 이렇게 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="04457-110">알고리즘의 OID는 일반적으로이 대 한 좋은 후보입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="04457-111">그러나 했습니다는 한 가지 문제점은 동일한 알고리즘을 지정 하려면 여러 가지: "AES" (CNG)를 관리 되는, AesManaged, AesCryptoServiceProvider, AesCng, 및 Aes (특정 매개 변수 지정) RijndaelManaged 클래스는 모두 실제로 동일한 작업을 하 고 올바른 OID를 이러한 모든 내용의 매핑을 유지 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="04457-112">개발자는 사용자 지정 알고리즘 (또는 다른 구현의 AES!)를 제공 하고자, 하는 경우 해당 OID를 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="04457-113">이 추가 등록 단계를 수행 하면 시스템 구성 크다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="04457-114">다시 단계별로 실행 하기로 문제를 잘못 된 방향에서 접근 된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="04457-115">OID 알려 알고리즘이 무엇 인지 하지만 실제로 신경을이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04457-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="04457-116">안전 하 게 사용할 단일 entropic 값이 두 가지 알고리즘의 경우 알고리즘은 실제로 알아야 할 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="04457-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="04457-117">새로운 실제로 신경 써야 작동 하는 방식입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="04457-118">훌륭한 대칭 블록 암호화 알고리즘은 또한 강력한 의사 난수 순열 (PRP): 입력 (키, 모드, IV 일반 텍스트로 연결)을 수정 하 고 암호 텍스트 출력을 확률 부담으로 구분 됩니다 다른 대칭 블록 암호화 동일한 입력을 지정 하는 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="04457-119">마찬가지로 모든 훌륭한 키 지정된 해시 함수는 강력한 의사 난수 함수 (PRF) 이기도 하 고 고정 된 입력된 집합을 지정 된 출력 반응은 놀라울 정도로 됩니다 다른 키 지정된 해시 함수에서 고유 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="04457-120">강력한 Prp 및 PRFs이이 개념이 컨텍스트 헤더를 구축 하려면 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="04457-121">이 컨텍스트 헤더 역할을 안정 된 지문을 사용 하 여 지정 된 모든 작업의 경우의 알고리즘을 통해 및 데이터 보호 시스템에 필요한 암호화 민첩성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="04457-122">이 헤더를 재현할 수 고의 일환으로 나중에 사용 되는 [파생 프로세스를 하위 키](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="04457-123">두 가지 다른 기본 알고리즘 작업의 모드에 따라 컨텍스트 헤더를 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04457-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="04457-124">CBC 모드 암호화 + HMAC 인증</span><span class="sxs-lookup"><span data-stu-id="04457-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="04457-125">컨텍스트 헤더 다음 구성 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="04457-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="04457-126">[16 비트] 값 00 마커는 00 "CBC 암호화 + HMAC 인증"을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="04457-127">[32 비트] 대칭 블록 암호화 알고리즘의 키 길이 (바이트, big endian)에서입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="04457-128">[32 비트] 블록 크기 (바이트, big endian) 대칭 블록 암호화 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="04457-129">[32 비트] 키의 길이 (바이트, big endian)는 HMAC 알고리즘.</span><span class="sxs-lookup"><span data-stu-id="04457-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="04457-130">(현재 키 크기는 항상 크기와 일치 다이제스트.)</span><span class="sxs-lookup"><span data-stu-id="04457-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="04457-131">[32 비트] 다이제스트의 크기 (바이트, big endian)는 HMAC 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="04457-132">EncCBC (K_E, IV, ""), 빈 문자열 입력을 지정 된 대칭 블록 암호화 알고리즘의 출력 인 및 IV가 모두 0 인 벡터를 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-132">EncCBC(K_E, IV, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="04457-133">K_E 생성에 대 한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="04457-133">The construction of K_E is described below.</span></span>

* <span data-ttu-id="04457-134">MAC (K_H, ""), 빈 문자열 입력을 지정 하는 HMAC 알고리즘의 출력 인 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-134">MAC(K_H, ""), which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="04457-135">K_H 생성에 대 한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="04457-135">The construction of K_H is described below.</span></span>

<span data-ttu-id="04457-136">이상적으로 모두 0 인 벡터 K_E K_H에 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="04457-136">Ideally, we could pass all-zero vectors for K_E and K_H.</span></span> <span data-ttu-id="04457-137">그러나 기본 알고리즘 (특히 DES 및 3DES) 작업을 수행 하기 전에 약한 키의 벡터를 모두 0과 같은 단순 또는 반복 패턴을 사용 하 여 속하는 있는지를 확인 하는 위치는 상황을 방지 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="04457-138">대신 사용 하 여 NIST SP800 108 KDF 카운터 모드에서 (참조 [NIST SP800 108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), 초로 5.1) 길이가 0 인 키, 레이블 및 컨텍스트 및 기본 PRF로 HMACSHA512를 사용 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="04457-139">파생 | K_E | + | K_H | 출력 바이트 다음 분해 하면 K_E 및 K_H 자체입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-139">We derive | K_E | + | K_H | bytes of output, then decompose the result into K_E and K_H themselves.</span></span> <span data-ttu-id="04457-140">수학적으로 다음과 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="04457-140">Mathematically, this is represented as follows.</span></span>

<span data-ttu-id="04457-141">( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span><span class="sxs-lookup"><span data-stu-id="04457-141">( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="04457-142">예제: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="04457-142">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="04457-143">예를 들어 여기서 대칭 블록 암호화 알고리즘은 AES-192-CBC 하 고 유효성 검사 알고리즘은 HMACSHA256 경우를 생각해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="04457-143">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="04457-144">시스템에는 다음 단계를 사용 하 여 컨텍스트 헤더를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-144">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="04457-145">먼저 (K_E | | K_H) SP800_108_CTR = (prf HMACSHA512를 = 키 = "", 레이블 = "", 상황에 맞는 = ""), 여기서 | K_E | = 192 비트 및 | K_H | = 지정 된 알고리즘 당 256 비트입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-145">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 256 bits per the specified algorithms.</span></span> <span data-ttu-id="04457-146">따라서 K_E 5BB6 =... 21DD 및 K_H A04A =... 아래 예에서 00A9:</span><span class="sxs-lookup"><span data-stu-id="04457-146">This leads to K_E = 5BB6..21DD and K_H = A04A..00A9 in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="04457-147">다음으로, Enc_CBC 계산 (K_E, IV, "") AES-192-CBC IV 지정 = 0 \* 및 위와 K_E.</span><span class="sxs-lookup"><span data-stu-id="04457-147">Next, compute Enc_CBC (K_E, IV, "") for AES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="04457-148">result := F474B1872B3B53E4721DE19C0841DB6F</span><span class="sxs-lookup"><span data-stu-id="04457-148">result := F474B1872B3B53E4721DE19C0841DB6F</span></span>

<span data-ttu-id="04457-149">다음으로 MAC을 계산 (K_H, "") HMACSHA256 위와 K_H 지정에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-149">Next, compute MAC(K_H, "") for HMACSHA256 given K_H as above.</span></span>

<span data-ttu-id="04457-150">result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span><span class="sxs-lookup"><span data-stu-id="04457-150">result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span></span>

<span data-ttu-id="04457-151">이 아래 전체 컨텍스트 헤더를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-151">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="04457-152">이 컨텍스트 헤더는 인증 된 암호화 알고리즘 쌍 (AES-192-CBC 암호화 + HMACSHA256 유효성 검사)의 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-152">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="04457-153">구성 요소에 설명 된 대로 [위에](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) 됩니다.</span><span class="sxs-lookup"><span data-stu-id="04457-153">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="04457-154">표식 (00 00)</span><span class="sxs-lookup"><span data-stu-id="04457-154">the marker (00 00)</span></span>

* <span data-ttu-id="04457-155">블록 암호화 키 길이 (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="04457-155">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="04457-156">블록 암호화 블록 크기 (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="04457-156">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="04457-157">HMAC 키 길이 (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="04457-157">the HMAC key length (00 00 00 20)</span></span>

* <span data-ttu-id="04457-158">HMAC 다이제스트 크기 (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="04457-158">the HMAC digest size (00 00 00 20)</span></span>

* <span data-ttu-id="04457-159">블록 암호화 PRP 출력 (F4 74-DB 6F) 및</span><span class="sxs-lookup"><span data-stu-id="04457-159">the block cipher PRP output (F4 74 - DB 6F) and</span></span>

* <span data-ttu-id="04457-160">HMAC PRF 출력 (D4 79-끝).</span><span class="sxs-lookup"><span data-stu-id="04457-160">the HMAC PRF output (D4 79 - end).</span></span>

> [!NOTE]
> <span data-ttu-id="04457-161">CBC 모드 암호화 + HMAC 인증 컨텍스트 헤더에는 Windows CNG에서 또는 관리 되는 SymmetricAlgorithm 및 KeyedHashAlgorithm 유형에 의해 알고리즘 구현에서 제공 하는 여부에 관계 없이 동일한 방식으로 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="04457-161">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="04457-162">다른 운영 체제에서 실행 하지 않도록 할 수 알고리즘의 구현을 Os 간에 다를 경우에 동일한 컨텍스트 헤더를 안정적으로 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-162">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="04457-163">(실제로 KeyedHashAlgorithm 아니어도 적절 한 HMAC입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-163">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="04457-164">이 모든 형식일 수 있습니다 키 지정된 해시 알고리즘입니다.)</span><span class="sxs-lookup"><span data-stu-id="04457-164">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="04457-165">예제: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="04457-165">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="04457-166">먼저 (K_E | | K_H) SP800_108_CTR = (prf HMACSHA512를 = 키 = "", 레이블 = "", 상황에 맞는 = ""), 여기서 | K_E | = 192 비트 및 | K_H | = 지정 된 알고리즘 당 160 비트입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-166">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 160 bits per the specified algorithms.</span></span> <span data-ttu-id="04457-167">따라서 K_E A219 =... E2BB 및 K_H DC4A =... 아래 예에서 B464:</span><span class="sxs-lookup"><span data-stu-id="04457-167">This leads to K_E = A219..E2BB and K_H = DC4A..B464 in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="04457-168">다음으로, Enc_CBC 계산 (K_E, IV, "") 3DES-192-CBC IV 지정 = 0 \* 및 위와 K_E.</span><span class="sxs-lookup"><span data-stu-id="04457-168">Next, compute Enc_CBC (K_E, IV, "") for 3DES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="04457-169">result := ABB100F81E53E10E</span><span class="sxs-lookup"><span data-stu-id="04457-169">result := ABB100F81E53E10E</span></span>

<span data-ttu-id="04457-170">다음으로 MAC을 계산 (K_H, "") HMACSHA1 위와 K_H 지정에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-170">Next, compute MAC(K_H, "") for HMACSHA1 given K_H as above.</span></span>

<span data-ttu-id="04457-171">result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span><span class="sxs-lookup"><span data-stu-id="04457-171">result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span></span>

<span data-ttu-id="04457-172">전체 컨텍스트 헤더의 인증 된 지문을 생성이 아래에 표시 된 암호화 알고리즘 쌍 (3DES-192-CBC 암호화 + HMACSHA1 유효성 검사):</span><span class="sxs-lookup"><span data-stu-id="04457-172">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="04457-173">구성 요소 같이 세분화 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-173">The components break down as follows:</span></span>

* <span data-ttu-id="04457-174">표식 (00 00)</span><span class="sxs-lookup"><span data-stu-id="04457-174">the marker (00 00)</span></span>

* <span data-ttu-id="04457-175">블록 암호화 키 길이 (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="04457-175">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="04457-176">블록 암호화 블록 크기 (00 00 00 08)</span><span class="sxs-lookup"><span data-stu-id="04457-176">the block cipher block size (00 00 00 08)</span></span>

* <span data-ttu-id="04457-177">HMAC 키 길이 (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="04457-177">the HMAC key length (00 00 00 14)</span></span>

* <span data-ttu-id="04457-178">HMAC 다이제스트 크기 (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="04457-178">the HMAC digest size (00 00 00 14)</span></span>

* <span data-ttu-id="04457-179">블록 암호화 PRP 출력 (AB B1-E1 0E) 및</span><span class="sxs-lookup"><span data-stu-id="04457-179">the block cipher PRP output (AB B1 - E1 0E) and</span></span>

* <span data-ttu-id="04457-180">HMAC PRF 출력 (76 EB-끝).</span><span class="sxs-lookup"><span data-stu-id="04457-180">the HMAC PRF output (76 EB - end).</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="04457-181">Galois/카운터 모드 암호화 + 인증</span><span class="sxs-lookup"><span data-stu-id="04457-181">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="04457-182">컨텍스트 헤더 다음 구성 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="04457-182">The context header consists of the following components:</span></span>

* <span data-ttu-id="04457-183">[16 비트] 값 00 마커는 01 "GCM 암호화 + 인증"을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-183">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="04457-184">[32 비트] 대칭 블록 암호화 알고리즘의 키 길이 (바이트, big endian)에서입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-184">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="04457-185">[32 비트] Nonce 크기 (바이트, big endian) 인증 된 암호화 작업 중에 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-185">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="04457-186">(시스템, nonce 크기로 고정 됩니다이 = 96 비트.)</span><span class="sxs-lookup"><span data-stu-id="04457-186">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="04457-187">[32 비트] 블록 크기 (바이트, big endian) 대칭 블록 암호화 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-187">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="04457-188">(GCM에 대 한 블록 크기로 고정 되어이 128 비트 =.)</span><span class="sxs-lookup"><span data-stu-id="04457-188">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="04457-189">[32 비트] 인증 태그 크기 (바이트, big endian) 인증 된 암호화 함수에 의해 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="04457-189">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="04457-190">(시스템, 태그 크기로 고정 됩니다이 128 비트 =.)</span><span class="sxs-lookup"><span data-stu-id="04457-190">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="04457-191">[128 비트] Enc_GCM의 태그 (K_E, nonce, "")는 지정 된 빈 문자열 입력 대칭 블록 암호화 알고리즘의 출력 이며 여기서 nonce는 96 비트 모두 0 인 벡터입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-191">[128 bits] The tag of Enc_GCM (K_E, nonce, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="04457-192">K_E는 CBC 암호화 + HMAC 인증 시나리오와 같이 동일한 메커니즘을 사용 하 여 파생 됩니다.</span><span class="sxs-lookup"><span data-stu-id="04457-192">K_E is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="04457-193">그러나 여기에 없는 K_H 이므로 기본적으로 있다고 | K_H | = 0, 알고리즘으로 축소 하 고는 양식 아래.</span><span class="sxs-lookup"><span data-stu-id="04457-193">However, since there's no K_H in play here, we essentially have | K_H | = 0, and the algorithm collapses to the below form.</span></span>

<span data-ttu-id="04457-194">K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span><span class="sxs-lookup"><span data-stu-id="04457-194">K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-256-gcm"></a><span data-ttu-id="04457-195">예제: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="04457-195">Example: AES-256-GCM</span></span>

<span data-ttu-id="04457-196">먼저 K_E 수 있도록 SP800_108_CTR = (prf HMACSHA512를 = 키 = "", 레이블 = "", 상황에 맞는 = ""), 여기서 | K_E | = 256 비트입니다.</span><span class="sxs-lookup"><span data-stu-id="04457-196">First, let K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 256 bits.</span></span>

<span data-ttu-id="04457-197">K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span><span class="sxs-lookup"><span data-stu-id="04457-197">K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span></span>

<span data-ttu-id="04457-198">Enc_GCM의 인증 태그를 다음으로, 계산 (K_E, nonce, "") AES-256-GCM nonce 지정 = 096 및 K_E 위와 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-198">Next, compute the authentication tag of Enc_GCM (K_E, nonce, "") for AES-256-GCM given nonce = 096 and K_E as above.</span></span>

<span data-ttu-id="04457-199">result := E7DCCE66DF855A323A6BB7BD7A59BE45</span><span class="sxs-lookup"><span data-stu-id="04457-199">result := E7DCCE66DF855A323A6BB7BD7A59BE45</span></span>

<span data-ttu-id="04457-200">이 아래 전체 컨텍스트 헤더를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-200">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="04457-201">구성 요소 같이 세분화 합니다.</span><span class="sxs-lookup"><span data-stu-id="04457-201">The components break down as follows:</span></span>

* <span data-ttu-id="04457-202">표식 (00 01)</span><span class="sxs-lookup"><span data-stu-id="04457-202">the marker (00 01)</span></span>

* <span data-ttu-id="04457-203">블록 암호화 키 길이 (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="04457-203">the block cipher key length (00 00 00 20)</span></span>

* <span data-ttu-id="04457-204">nonce 크기 (00 00 00 0 C)</span><span class="sxs-lookup"><span data-stu-id="04457-204">the nonce size (00 00 00 0C)</span></span>

* <span data-ttu-id="04457-205">블록 암호화 블록 크기 (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="04457-205">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="04457-206">인증 태그 크기 (00 00 00 10) 및</span><span class="sxs-lookup"><span data-stu-id="04457-206">the authentication tag size (00 00 00 10) and</span></span>

* <span data-ttu-id="04457-207">실행 블록 암호 인증 태그 (E7 DC-끝).</span><span class="sxs-lookup"><span data-stu-id="04457-207">the authentication tag from running the block cipher (E7 DC - end).</span></span>
