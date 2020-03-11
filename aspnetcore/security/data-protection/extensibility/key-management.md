---
title: ASP.NET Core에서 키 관리 확장성
author: rick-anderson
description: ASP.NET Core 데이터 보호 키 관리 확장성에 알아봅니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 28932cbef1cc797338980f3e0de8b09caee324c0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654261"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="ef48a-103">ASP.NET Core에서 키 관리 확장성</span><span class="sxs-lookup"><span data-stu-id="ef48a-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="ef48a-104">이러한 Api의 기본 개념 중 일부를 설명 하므로이 섹션을 읽기 전에 [키 관리](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ef48a-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="ef48a-105">다음 인터페이스 중 하나를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="ef48a-106">키</span><span class="sxs-lookup"><span data-stu-id="ef48a-106">Key</span></span>

<span data-ttu-id="ef48a-107">`IKey` 인터페이스는 cryptosystem에서 키의 기본 표현입니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="ef48a-108">용어 키의 "암호화 키 자료" 리터럴 관점에 없는 추상 점에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="ef48a-109">키에 다음 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-109">A key has the following properties:</span></span>

* <span data-ttu-id="ef48a-110">활성화, 생성 및 만료 날짜</span><span class="sxs-lookup"><span data-stu-id="ef48a-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="ef48a-111">해지 상태</span><span class="sxs-lookup"><span data-stu-id="ef48a-111">Revocation status</span></span>

* <span data-ttu-id="ef48a-112">키 식별자 (GUID)</span><span class="sxs-lookup"><span data-stu-id="ef48a-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="ef48a-113">또한 `IKey`는이 키에 연결 된 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스를 만드는 데 사용할 수 있는 `CreateEncryptor` 메서드를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="ef48a-114">또한 `IKey`는이 키에 연결 된 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스를 만드는 데 사용할 수 있는 `CreateEncryptorInstance` 메서드를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="ef48a-115">`IKey` 인스턴스에서 원시 암호화 자료를 검색 하는 API는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="ef48a-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="ef48a-116">IKeyManager</span></span>

<span data-ttu-id="ef48a-117">`IKeyManager` 인터페이스는 일반적인 키 저장, 검색 및 조작을 담당 하는 개체를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="ef48a-118">이 세 가지 개략적인 작업을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="ef48a-119">새 키 만들기 및 저장소에 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="ef48a-120">저장소에서 모든 키를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-120">Get all keys from storage.</span></span>

* <span data-ttu-id="ef48a-121">하나 이상의 키를 취소 하 고 해지 정보 저장소를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="ef48a-122">`IKeyManager` 작성은 매우 고급 작업 이므로 대부분의 개발자는이 작업을 시도해 서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="ef48a-123">대신, 대부분의 개발자는 [Xmlkeymanager](#xmlkeymanager) 클래스에서 제공 하는 기능을 활용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="ef48a-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="ef48a-124">XmlKeyManager</span></span>

<span data-ttu-id="ef48a-125">`XmlKeyManager` 형식은 `IKeyManager`의 기본 구체적 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="ef48a-126">미사용 키 암호화 키 위탁 기능 등 몇 가지 유용한 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="ef48a-127">이 시스템의 키는 XML 요소로 표시 됩니다 (특히 [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="ef48a-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="ef48a-128">`XmlKeyManager`는 작업을 수행 하는 과정에서 여러 다른 구성 요소에 종속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="ef48a-129">새 키에 사용 되는 알고리즘을 지시 하는 `AlgorithmConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="ef48a-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="ef48a-130">`IXmlRepository`는 저장소에서 키가 유지 되는 위치를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="ef48a-131">[옵션]을 `IXmlEncryptor` 하 여 미사용 키를 암호화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="ef48a-132">키 에스크로 서비스를 제공 하는 [옵션]을 `IKeyEscrowSink` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="ef48a-133">`IXmlRepository`는 저장소에서 키가 유지 되는 위치를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="ef48a-134">[옵션]을 `IXmlEncryptor` 하 여 미사용 키를 암호화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="ef48a-135">키 에스크로 서비스를 제공 하는 [옵션]을 `IKeyEscrowSink` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="ef48a-136">다음은 `XmlKeyManager`내에서 이러한 구성 요소를 연결 하는 방법을 나타내는 개략적인 다이어그램입니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![키 만들기](key-management/_static/keycreation2.png)

<span data-ttu-id="ef48a-138">*키 생성/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="ef48a-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="ef48a-139">`CreateNewKey`구현에서는 `AlgorithmConfiguration` 구성 요소를 사용 하 여 고유한 `IAuthenticatedEncryptorDescriptor`를 만든 다음 XML로 serialize 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="ef48a-140">키 에스크로 싱크가 있는 경우 원시 (암호화 되지 않음된) XML 장기 저장소에 대 한 싱크로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="ef48a-141">그런 다음 암호화 되지 않은 XML이 `IXmlEncryptor` (필요한 경우)를 통해 암호화 된 XML 문서를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="ef48a-142">이 암호화 된 문서는 `IXmlRepository`을 통해 장기 저장소에 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="ef48a-143">`IXmlEncryptor` 구성 되지 않은 경우 암호화 되지 않은 문서는 `IXmlRepository`에 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![키 검색](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![키 만들기](key-management/_static/keycreation1.png)

<span data-ttu-id="ef48a-146">*키 생성/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="ef48a-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="ef48a-147">`CreateNewKey`구현에서는 `IAuthenticatedEncryptorConfiguration` 구성 요소를 사용 하 여 고유한 `IAuthenticatedEncryptorDescriptor`를 만든 다음 XML로 serialize 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="ef48a-148">키 에스크로 싱크가 있는 경우 원시 (암호화 되지 않음된) XML 장기 저장소에 대 한 싱크로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="ef48a-149">그런 다음 암호화 되지 않은 XML이 `IXmlEncryptor` (필요한 경우)를 통해 암호화 된 XML 문서를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="ef48a-150">이 암호화 된 문서는 `IXmlRepository`을 통해 장기 저장소에 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="ef48a-151">`IXmlEncryptor` 구성 되지 않은 경우 암호화 되지 않은 문서는 `IXmlRepository`에 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![키 검색](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="ef48a-153">*키 검색/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="ef48a-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="ef48a-154">`GetAllKeys`구현에서는 키와 해지를 나타내는 XML 문서를 기본 `IXmlRepository`에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="ef48a-155">이러한 문서는 암호화 하는 경우 시스템은 자동으로 암호를 해독 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="ef48a-156">`XmlKeyManager`은 적절 한 `IAuthenticatedEncryptorDescriptorDeserializer` 인스턴스를 만들어 문서를 `IAuthenticatedEncryptorDescriptor` 인스턴스로 다시 deserialize 합니다. 그러면 개별 `IKey` 인스턴스에 래핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="ef48a-157">이 `IKey` 인스턴스의 컬렉션은 호출자에 게 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="ef48a-158">특정 XML 요소에 대 한 자세한 내용은 [키 저장소 형식 문서](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="ef48a-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="ef48a-159">IXmlRepository</span></span>

<span data-ttu-id="ef48a-160">`IXmlRepository` 인터페이스는 백업 저장소에서 xml에 xml을 저장 하 고 검색할 수 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="ef48a-161">이 두 개의 Api를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-161">It exposes two APIs:</span></span>

* <span data-ttu-id="ef48a-162">`GetAllElements`:`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="ef48a-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="ef48a-163">`IXmlRepository` 구현에서는 XML을 전달 하는 XML을 구문 분석할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="ef48a-164">불투명으로 XML 문서를 처리 하며 더 높은 계층을 생성 하 고 문서를 구문 분석 하는 방법에 대 한 걱정을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="ef48a-165">`IXmlRepository`를 구현 하는 네 가지 기본 제공 구체적 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="ef48a-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="ef48a-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="ef48a-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="ef48a-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="ef48a-168">AzureStorage AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="ef48a-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="ef48a-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="ef48a-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="ef48a-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="ef48a-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="ef48a-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="ef48a-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="ef48a-172">AzureStorage AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="ef48a-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="ef48a-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="ef48a-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="ef48a-174">자세한 내용은 [키 저장소 공급자 문서](xref:security/data-protection/implementation/key-storage-providers) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ef48a-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="ef48a-175">다른 백업 저장소 (예: Azure Table Storage)를 사용 하는 경우 사용자 지정 `IXmlRepository`를 등록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="ef48a-176">기본 리포지토리 응용 프로그램 전체를 변경 하려면 사용자 지정 `IXmlRepository` 인스턴스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a><span data-ttu-id="ef48a-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="ef48a-177">IXmlEncryptor</span></span>

<span data-ttu-id="ef48a-178">`IXmlEncryptor` 인터페이스는 일반 텍스트 XML 요소를 암호화할 수 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="ef48a-179">단일 API를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-179">It exposes a single API:</span></span>

* <span data-ttu-id="ef48a-180">(XElement plaintextElement) 암호화: EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="ef48a-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="ef48a-181">Serialize 된 `IAuthenticatedEncryptorDescriptor`에 "암호화 필요"로 표시 된 요소가 포함 된 경우 `XmlKeyManager`는 구성 된 `IXmlEncryptor`의 `Encrypt` 메서드를 통해 해당 요소를 실행 하 고 일반 텍스트 요소 대신 암호화 된 읽거나 요소를 `IXmlRepository`에 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="ef48a-182">`Encrypt` 메서드의 출력은 `EncryptedXmlInfo` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="ef48a-183">이 개체는 결과 암호화 된 읽거나 `XElement`와 해당 요소를 해독 하는 데 사용할 수 있는 `IXmlDecryptor`을 나타내는 형식을 모두 포함 하는 래퍼입니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="ef48a-184">`IXmlEncryptor`를 구현 하는 네 가지 기본 제공 구체적 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="ef48a-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="ef48a-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="ef48a-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="ef48a-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="ef48a-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="ef48a-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="ef48a-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="ef48a-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="ef48a-189">자세한 내용은 [휴지 상태의 키 암호화 문서](xref:security/data-protection/implementation/key-encryption-at-rest) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ef48a-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="ef48a-190">기본 키 암호화 메커니즘 응용 프로그램 전체를 변경 하려면 사용자 지정 `IXmlEncryptor` 인스턴스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a><span data-ttu-id="ef48a-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="ef48a-191">IXmlDecryptor</span></span>

<span data-ttu-id="ef48a-192">`IXmlDecryptor` 인터페이스는 `IXmlEncryptor`를 통해 암호화 된 읽거나 된 `XElement`의 암호를 해독 하는 방법을 알고 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="ef48a-193">단일 API를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-193">It exposes a single API:</span></span>

* <span data-ttu-id="ef48a-194">(XElement encryptedElement) 암호 해독: XElement</span><span class="sxs-lookup"><span data-stu-id="ef48a-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="ef48a-195">`Decrypt` 메서드는 `IXmlEncryptor.Encrypt`에서 수행 되는 암호화를 실행 취소 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="ef48a-196">일반적으로 구체적인 `IXmlEncryptor` 구현에는 해당 하는 구체적인 `IXmlDecryptor` 구현이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="ef48a-197">`IXmlDecryptor`를 구현 하는 형식에는 다음 두 개의 공용 생성자 중 하나가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="ef48a-198">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="ef48a-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="ef48a-199">.ctor()</span><span class="sxs-lookup"><span data-stu-id="ef48a-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="ef48a-200">생성자에 전달 된 `IServiceProvider`는 null 일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="ef48a-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="ef48a-201">IKeyEscrowSink</span></span>

<span data-ttu-id="ef48a-202">`IKeyEscrowSink` 인터페이스는 중요 한 정보를 에스크로 할 수 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="ef48a-203">직렬화 된 설명자에는 암호화 자료와 같은 중요 한 정보가 포함 될 수 있으며,이는 첫 번째 위치의 [IXmlEncryptor](#ixmlencryptor) 유형을 도입 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="ef48a-204">그러나 그리고 사고가 발생할 때 키 링을 삭제할 수 있습니다 또는 손상 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="ef48a-205">에스크로 인터페이스는 구성 된 [IXmlEncryptor](#ixmlencryptor)변환 되기 전에 원시 직렬화 된 XML에 대 한 액세스를 허용 하는 비상 이스케이프 해치를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="ef48a-206">인터페이스는 단일 API를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="ef48a-207">저장소 (Guid keyId, XElement 요소)</span><span class="sxs-lookup"><span data-stu-id="ef48a-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="ef48a-208">비즈니스 정책과 일관 된 보안 방식으로 제공 된 요소를 처리 하는 `IKeyEscrowSink` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="ef48a-209">에스크로 싱크에서 인증서의 개인 키가 위탁 된 알려진 회사 x.509 인증서를 사용 하 여 XML 요소를 암호화 하는 것이 가능 합니다. `CertificateXmlEncryptor` 형식은이를 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="ef48a-210">또한 `IKeyEscrowSink` 구현은 제공 된 요소를 적절 하 게 유지 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="ef48a-211">서버 관리자가 [전역적으로 구성할](xref:security/data-protection/configuration/machine-wide-policy)수 있지만 기본적으로는 에스크로 메커니즘이 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="ef48a-212">아래 샘플에 표시 된 것 처럼 `IDataProtectionBuilder.AddKeyEscrowSink` 메서드를 통해 프로그래밍 방식으로 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="ef48a-213">`AddKeyEscrowSink` 메서드 오버 로드는 `IKeyEscrowSink` 인스턴스를 단일 항목 수 있도록 `IServiceCollection.AddSingleton` 및 `IServiceCollection.AddInstance` 오버 로드를 미러링합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="ef48a-214">여러 `IKeyEscrowSink` 인스턴스를 등록 하는 경우 키를 생성 하는 동안 각 인스턴스를 호출 하므로 여러 메커니즘에 동시에 키를 위탁 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="ef48a-215">`IKeyEscrowSink` 인스턴스에서 자료를 읽을 수 있는 API가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="ef48a-216">에스크로 메커니즘의 디자인 이론으로 구성 됩니다: 키 자료를 신뢰할 수 있는 기관에 액세스할 수 있도록 하는 것 자체 escrowed 자료에 대 한 액세스를 다시 없어야 아니므로 응용 프로그램 자체는 신뢰할 수 있는 기관, 및입니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="ef48a-217">다음 샘플 코드는 "CONTOSODomain Admins"의 멤버만 복구할 수 있도록 키가 위탁 `IKeyEscrowSink`를 만들고 등록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="ef48a-218">해야 도메인에 가입 된 Windows 8이이 샘플을 실행 하려면 Windows Server 2012 컴퓨터와 도메인 컨트롤러에는 Windows Server 2012 이상 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef48a-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
