---
title: ASP.NET 코어의 주요 스토리지 형식
author: rick-anderson
description: ASP.NET 핵심 데이터 보호 키 저장소 형식의 구현 세부 정보를 알아봅니다.
ms.author: riande
ms.date: 04/08/2020
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 3072c673791b589027a910b80eaba52052eb9311
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976939"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="2bee7-103">ASP.NET 코어의 주요 스토리지 형식</span><span class="sxs-lookup"><span data-stu-id="2bee7-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="2bee7-104">개체는 XML 표현에 미사용 으로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="2bee7-105">키 저장소의 기본 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="2bee7-106">윈도우: \*% 로컬앱데이터%\ASP.NET\데이터 보호-키\*</span><span class="sxs-lookup"><span data-stu-id="2bee7-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="2bee7-107">맥 OS / 리눅스 : *$HOME /.aspnet / 데이터 보호 - 키*</span><span class="sxs-lookup"><span data-stu-id="2bee7-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="2bee7-108">\<핵심> 요소</span><span class="sxs-lookup"><span data-stu-id="2bee7-108">The \<key> element</span></span>

<span data-ttu-id="2bee7-109">키는 키 리포지토리에 최상위 개체로 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="2bee7-110">규칙 키에는 파일 이름 **key-{guid}.xml이**있으며{guid}가 키의 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-110">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="2bee7-111">이러한 각 파일에는 단일 키가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-111">Each such file contains a single key.</span></span> <span data-ttu-id="2bee7-112">파일의 형식은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-112">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="2bee7-113">키 \<> 요소에는 다음과 같은 특성과 자식 요소가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="2bee7-114">키 ID입니다. 이 값은 신뢰할 수 있는 값으로 처리됩니다. 파일 이름은 단순히 사람의 가독성에 대한 좋은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="2bee7-115">현재 1로 \<고정된 키> 요소의 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="2bee7-116">키의 생성, 활성화 및 만료 날짜입니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="2bee7-117">이 \<키에 포함된 인증된 암호화 구현에 대한 정보가 포함된 설명자> 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="2bee7-118">위의 예에서 키의 ID는 {80732141-ec8f-4b80-af9c-c4d2d1ff8901}이며, 2015년 3월 19일에 생성및 활성화되었으며 수명은 90일입니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="2bee7-119">(이 예제에서와 같이 활성화 날짜가 생성 날짜 이전일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="2bee7-120">이는 API가 작동하는 방식에 있어 실제로 무해하기 때문입니다.)</span><span class="sxs-lookup"><span data-stu-id="2bee7-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="2bee7-121">\<설명자> 요소</span><span class="sxs-lookup"><span data-stu-id="2bee7-121">The \<descriptor> element</span></span>

<span data-ttu-id="2bee7-122">외부 \<설명자> 요소에는 i인증 암호화해독기Deserializer를 구현하는 형식의 어셈블리 자격을 갖춘 이름인 특성 deserializerType이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="2bee7-123">이 형식은 내부 \<설명자> 요소를 읽고 내에 포함된 정보를 구문 분석하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="2bee7-124">\<설명자> 요소의 특정 형식은 키에 의해 캡슐화된 인증된 암호화기 구현에 따라 달라지며 각 직렬화 유형은 이에 대해 약간 다른 형식을 기대합니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="2bee7-125">그러나 일반적으로 이 요소에는 알고리즘 정보(이름, 형식, ID 또는 이와 유사한) 및 비밀 키 자료가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="2bee7-126">위의 예에서 설명자는 이 키가 AES-256-CBC 암호화 + HMACSHA256 유효성 검사를 래핑한다고 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="2bee7-127">암호화된비밀 \<> 요소</span><span class="sxs-lookup"><span data-stu-id="2bee7-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="2bee7-128">\*\* &lt;암호화된&gt; \*\* 비밀 키 자료의 암호화된 형식이 포함된 암호화된 비밀 요소가 있을 수 [있습니다.](xref:security/data-protection/implementation/key-encryption-at-rest)</span><span class="sxs-lookup"><span data-stu-id="2bee7-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="2bee7-129">특성은 `decryptorType` [IXmlDecryptor를](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)구현하는 형식의 어셈블리 인증 된 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="2bee7-130">이 형식은 내부 \*\* &lt;암호화된Key&gt; \*\* 요소를 읽고 원래 일반 텍스트를 복구하기 위해 암호를 해독하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="2bee7-131">마찬가지로 `<descriptor>`요소의 특정 형식은 `<encryptedSecret>` 사용 중미 암호화 메커니즘에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="2bee7-132">위의 예에서 마스터 키는 주석당 Windows DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="2bee7-133">\<해지> 요소</span><span class="sxs-lookup"><span data-stu-id="2bee7-133">The \<revocation> element</span></span>

<span data-ttu-id="2bee7-134">해지는 키 리포지토리에 최상위 개체로 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="2bee7-135">규칙 해지에 따라 파일 이름 **해지-{timestamp}.xml(특정** 날짜 이전에 모든 키를 취소하는 경우) 또는 **해지-{guid}.xml(특정** 키를 해지하는 경우)이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="2bee7-136">각 파일에는 \<단일 해지> 요소가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="2bee7-137">개별 키의 해지에 대 한 파일 내용은 아래와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="2bee7-138">이 경우 지정된 키만 해지됩니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="2bee7-139">그러나 아래 예제와 같이 키 ID가 "\*"인 경우 지정된 해지 날짜 이전의 생성 날짜가 지정된 모든 키가 해지됩니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="2bee7-140">> \<요소는 시스템에서 읽지 않는 이유입니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="2bee7-141">그것은 단순히 해지에 대한 인간이 읽을 수있는 이유를 저장하는 편리한 장소입니다.</span><span class="sxs-lookup"><span data-stu-id="2bee7-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
