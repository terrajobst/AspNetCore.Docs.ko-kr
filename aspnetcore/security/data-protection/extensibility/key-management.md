---
title: ASP.NET Core에서 키 관리 확장성
author: rick-anderson
description: ASP.NET Core 데이터 보호 키 관리 확장성에 알아봅니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 28932cbef1cc797338980f3e0de8b09caee324c0
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896910"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="21762-103">ASP.NET Core에서 키 관리 확장성</span><span class="sxs-lookup"><span data-stu-id="21762-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="21762-104">읽기를 [키 관리](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 섹션에서는 이러한 Api의 기본적인 개념 중 일부 설명 하므로이 섹션을 읽어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="21762-105">다음 인터페이스 중 하나를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="21762-106">Key</span><span class="sxs-lookup"><span data-stu-id="21762-106">Key</span></span>

<span data-ttu-id="21762-107">`IKey` 인터페이스는 키 암호화 시스템의 기본 표현입니다.</span><span class="sxs-lookup"><span data-stu-id="21762-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="21762-108">용어 키의 "암호화 키 자료" 리터럴 관점에 없는 추상 점에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21762-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="21762-109">키에 다음 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21762-109">A key has the following properties:</span></span>

* <span data-ttu-id="21762-110">활성화, 생성 및 만료 날짜</span><span class="sxs-lookup"><span data-stu-id="21762-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="21762-111">해지 상태</span><span class="sxs-lookup"><span data-stu-id="21762-111">Revocation status</span></span>

* <span data-ttu-id="21762-112">키 식별자 (GUID)</span><span class="sxs-lookup"><span data-stu-id="21762-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="21762-113">또한 `IKey` 노출를 `CreateEncryptor` 만드는 데 사용할 수 있는 메서드는 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스가이 키에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="21762-114">또한 `IKey` 노출를 `CreateEncryptorInstance` 만드는 데 사용할 수 있는 메서드는 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스가이 키에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="21762-115">API는 없습니다에서 원시 암호화 자료를 검색 하는 `IKey` 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="21762-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="21762-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="21762-116">IKeyManager</span></span>

<span data-ttu-id="21762-117">`IKeyManager` 인터페이스 일반 키 저장소, 검색 및 조작 하는 일을 담당 하는 개체를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="21762-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="21762-118">이 세 가지 개략적인 작업을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="21762-119">새 키 만들기 및 저장소에 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="21762-120">저장소에서 모든 키를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="21762-120">Get all keys from storage.</span></span>

* <span data-ttu-id="21762-121">하나 이상의 키를 취소 하 고 해지 정보 저장소를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="21762-122">쓰기는 `IKeyManager` 는 고급 작업이 며 대부분의 개발자는 시도 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="21762-123">대신 대부분의 개발자가 제공 하는 기능 사용 해야 합니다 [XmlKeyManager](#xmlkeymanager) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="21762-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="21762-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="21762-124">XmlKeyManager</span></span>

<span data-ttu-id="21762-125">합니다 `XmlKeyManager` 형식은 기본 구체적인 구현 `IKeyManager`합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="21762-126">미사용 키 암호화 키 위탁 기능 등 몇 가지 유용한 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="21762-127">이 시스템의 키 XML 요소로 표시 됩니다 (특히 [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="21762-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="21762-128">`XmlKeyManager` 해당 작업을 수행 하는 과정에서 다른 여러 구성 요소에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="21762-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="21762-129">`AlgorithmConfiguration`에 새 키로 사용 하는 알고리즘을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="21762-130">`IXmlRepository`를 키 저장소에 유지 되는 위치는 컨트롤입니다.</span><span class="sxs-lookup"><span data-stu-id="21762-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="21762-131">`IXmlEncryptor` [선택 사항] 미사용 키를 암호화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21762-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="21762-132">`IKeyEscrowSink` [선택 사항] 키 에스크로 서비스 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="21762-133">`IXmlRepository`를 키 저장소에 유지 되는 위치는 컨트롤입니다.</span><span class="sxs-lookup"><span data-stu-id="21762-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="21762-134">`IXmlEncryptor` [선택 사항] 미사용 키를 암호화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21762-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="21762-135">`IKeyEscrowSink` [선택 사항] 키 에스크로 서비스 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="21762-136">다음은 이러한 구성 요소는 내에서 함께 연결 하는 방법을 나타내는 개략적인 다이어그램 `XmlKeyManager`합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![키 만들기](key-management/_static/keycreation2.png)

<span data-ttu-id="21762-138">*키 생성 / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="21762-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="21762-139">구현의 `CreateNewKey`는 `AlgorithmConfiguration` 구성 요소는 고유한 만드는 데 `IAuthenticatedEncryptorDescriptor`는 다음 XML로 serialize 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21762-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="21762-140">키 에스크로 싱크가 있는 경우 원시 (암호화 되지 않음된) XML 장기 저장소에 대 한 싱크로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21762-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="21762-141">암호화 되지 않은 XML을 통해 실행 됩니다는 `IXmlEncryptor` (필요한 경우)에 암호화 된 XML 문서를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="21762-142">이 암호화 된 문서를 통해 장기 저장소에 유지 되는 `IXmlRepository`합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="21762-143">(없으면 `IXmlEncryptor` 는 구성 암호화 되지 않은 문서에 유지 되는 `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="21762-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![키 검색](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![키 만들기](key-management/_static/keycreation1.png)

<span data-ttu-id="21762-146">*키 생성 / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="21762-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="21762-147">구현의 `CreateNewKey`는 `IAuthenticatedEncryptorConfiguration` 구성 요소는 고유한 만드는 데 `IAuthenticatedEncryptorDescriptor`는 다음 XML로 serialize 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21762-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="21762-148">키 에스크로 싱크가 있는 경우 원시 (암호화 되지 않음된) XML 장기 저장소에 대 한 싱크로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21762-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="21762-149">암호화 되지 않은 XML을 통해 실행 됩니다는 `IXmlEncryptor` (필요한 경우)에 암호화 된 XML 문서를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="21762-150">이 암호화 된 문서를 통해 장기 저장소에 유지 되는 `IXmlRepository`합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="21762-151">(없으면 `IXmlEncryptor` 는 구성 암호화 되지 않은 문서에 유지 되는 `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="21762-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![키 검색](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="21762-153">*키 검색 / GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="21762-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="21762-154">구현의 `GetAllKeys`, XML 문서 나타내는 키 및 해지 내부에서 읽는 `IXmlRepository`합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="21762-155">이러한 문서는 암호화 하는 경우 시스템은 자동으로 암호를 해독 하 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="21762-156">`XmlKeyManager` 적절 한 만듭니다 `IAuthenticatedEncryptorDescriptorDeserializer` 문서를 deserialize 하는 데는 인스턴스를 다시 `IAuthenticatedEncryptorDescriptor` 인스턴스에 개별 다음 래핑됩니다 `IKey` 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="21762-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="21762-157">이 컬렉션의 `IKey` 인스턴스 호출자에 게 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21762-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="21762-158">특정 XML 요소에 대 한 자세한 내용은에서 찾을 수 있습니다 합니다 [키 저장소 형식으로 문서](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="21762-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="21762-159">IXmlRepository</span></span>

<span data-ttu-id="21762-160">`IXmlRepository` 인터페이스는 XML을 유지 하 고 백업 저장소에서 XML을 검색할 수 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="21762-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="21762-161">이 두 개의 Api를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-161">It exposes two APIs:</span></span>

* <span data-ttu-id="21762-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="21762-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="21762-163">구현의 `IXmlRepository` 통과 하는 XML을 구문 분석할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="21762-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="21762-164">불투명으로 XML 문서를 처리 하며 더 높은 계층을 생성 하 고 문서를 구문 분석 하는 방법에 대 한 걱정을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21762-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="21762-165">네 가지 기본 제공 구체적인 유형 구현 하는 `IXmlRepository`:</span><span class="sxs-lookup"><span data-stu-id="21762-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="21762-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="21762-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="21762-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="21762-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="21762-168">AzureStorage.AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="21762-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="21762-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="21762-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="21762-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="21762-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="21762-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="21762-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="21762-172">AzureStorage.AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="21762-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="21762-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="21762-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="21762-174">참조 된 [키 저장소 공급자 문서](xref:security/data-protection/implementation/key-storage-providers) 자세한 내용은 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="21762-175">사용자 지정 등록 `IXmlRepository` 다른 백업 저장소 (예: Azure Table Storage)를 사용 하는 경우 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="21762-176">응용 프로그램 수준의 기본 리포지토리를 변경 하려면 사용자 지정 등록 `IXmlRepository` 인스턴스:</span><span class="sxs-lookup"><span data-stu-id="21762-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

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

## <a name="ixmlencryptor"></a><span data-ttu-id="21762-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="21762-177">IXmlEncryptor</span></span>

<span data-ttu-id="21762-178">`IXmlEncryptor` 인터페이스에는 일반 텍스트 XML 요소를 암호화할 수 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="21762-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="21762-179">단일 API를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-179">It exposes a single API:</span></span>

* <span data-ttu-id="21762-180">Encrypt(XElement plaintextElement): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="21762-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="21762-181">직렬화 된 경우 `IAuthenticatedEncryptorDescriptor` 다음의 "암호화"가 필요한 것으로 표시 하는 요소가 `XmlKeyManager` 요소를 통해 구성 된 실행 됩니다 `IXmlEncryptor`의 `Encrypt` 메서드를 읽거나 요소가 유지 됩니다 보다는 일반 텍스트 요소를 `IXmlRepository`입니다.</span><span class="sxs-lookup"><span data-stu-id="21762-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="21762-182">출력을 `Encrypt` 메서드는 `EncryptedXmlInfo` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="21762-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="21762-183">이 개체는 모두를 읽거나 결과 포함 하는 래퍼 `XElement` 형식을 나타내는 `IXmlDecryptor` 해당 요소를 해독에 사용할 수 있는 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="21762-184">네 가지 기본 제공 구체적인 유형 구현 하는 `IXmlEncryptor`:</span><span class="sxs-lookup"><span data-stu-id="21762-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="21762-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="21762-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="21762-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="21762-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="21762-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="21762-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="21762-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="21762-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="21762-189">참조 된 [rest 문서에서 키 암호화](xref:security/data-protection/implementation/key-encryption-at-rest) 자세한 내용은 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="21762-190">응용 프로그램 수준의 기본 미사용 키 암호화 메커니즘을 변경 하려면 사용자 지정 등록 `IXmlEncryptor` 인스턴스:</span><span class="sxs-lookup"><span data-stu-id="21762-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

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

## <a name="ixmldecryptor"></a><span data-ttu-id="21762-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="21762-191">IXmlDecryptor</span></span>

<span data-ttu-id="21762-192">합니다 `IXmlDecryptor` 인터페이스에는 암호를 해독 하는 방법을 알고 있는 유형을 나타냅니다는 `XElement` 는 된 암호화를 통해는 `IXmlEncryptor`합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="21762-193">단일 API를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-193">It exposes a single API:</span></span>

* <span data-ttu-id="21762-194">암호 해독 (XElement encryptedElement): XElement</span><span class="sxs-lookup"><span data-stu-id="21762-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="21762-195">합니다 `Decrypt` 메서드를 실행 취소 하 여 수행 된 암호화 `IXmlEncryptor.Encrypt`합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="21762-196">일반적으로 각 구체적 `IXmlEncryptor` 구현에는 해당 구체적인 해야 `IXmlDecryptor` 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="21762-197">구현 하는 형식 `IXmlDecryptor` 다음 두 명의 public 생성자 중 하나가 있어야 함:</span><span class="sxs-lookup"><span data-stu-id="21762-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="21762-198">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="21762-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="21762-199">.ctor()</span><span class="sxs-lookup"><span data-stu-id="21762-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="21762-200">`IServiceProvider` 전달할 생성자는 null 일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21762-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="21762-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="21762-201">IKeyEscrowSink</span></span>

<span data-ttu-id="21762-202">`IKeyEscrowSink` 인터페이스 에스크로 중요 한 정보를 수행할 수 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="21762-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="21762-203">Serialize 된 설명자에는 중요 한 정보 (예: 암호화 자료)가 포함 될 수 있습니다 하 고이 되어 소개를 회수 합니다 [IXmlEncryptor](#ixmlencryptor) 애초에 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="21762-204">그러나 그리고 사고가 발생할 때 키 링을 삭제할 수 있습니다 또는 손상 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="21762-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="21762-205">에스크로 인터페이스는 구성으로 변환 되기 전에 원시 serialize 된 XML에 대 한 액세스를 허용 하는 응급 이스케이프 해치를 제공 [IXmlEncryptor](#ixmlencryptor)합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="21762-206">인터페이스는 단일 API를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="21762-207">저장소 (Guid keyId, XElement 요소)</span><span class="sxs-lookup"><span data-stu-id="21762-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="21762-208">것은 `IKeyEscrowSink` 안전한 방식으로 비즈니스 정책을 사용 하 여 일관 된 지정된 된 요소를 처리 하는 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="21762-209">알려진된 회사 X.509 인증서를 사용 하 여 XML 요소를 암호화 하는 인증서의 개인 키가 에스크로 되었습니다; 에스크로 싱크에 대 한 한 가지 구현을 수 있습니다. `CertificateXmlEncryptor` 형식이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21762-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="21762-210">`IKeyEscrowSink` 구현에 제공된 된 요소를 적절 하 게 유지 하는 일을 담당 이기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="21762-211">기본적으로 없습니다 에스크로 메커니즘을 사용 하지만 서버 관리자 수 [전역적으로이 구성](xref:security/data-protection/configuration/machine-wide-policy)합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="21762-212">또한 구성할 수 있습니다 프로그래밍 방식으로 통해는 `IDataProtectionBuilder.AddKeyEscrowSink` 아래 예제에 나와 있는 것 처럼 메서드.</span><span class="sxs-lookup"><span data-stu-id="21762-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="21762-213">`AddKeyEscrowSink` 메서드 오버 로드 미러를 `IServiceCollection.AddSingleton` 및 `IServiceCollection.AddInstance` 오버 로드로 `IKeyEscrowSink` 를 단일 항목 인스턴스는 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="21762-214">여러 개인 경우 `IKeyEscrowSink` 인스턴스를 등록, 각각 여러 메커니즘을 동시에 키를 위탁 수 있도록 키 생성 하는 동안 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21762-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="21762-215">API에서 자료를 읽을 수는 없습니다를 `IKeyEscrowSink` 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="21762-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="21762-216">에스크로 메커니즘의 디자인 이론으로 구성 됩니다: 키 자료를 신뢰할 수 있는 기관에 액세스할 수 있도록 하는 것 자체 escrowed 자료에 대 한 액세스를 다시 없어야 아니므로 응용 프로그램 자체는 신뢰할 수 있는 기관, 및입니다.</span><span class="sxs-lookup"><span data-stu-id="21762-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="21762-217">다음 샘플 코드에서는 만들기 및 등록을 `IKeyEscrowSink` "CONTOSODomain Admins"의 구성원만 복구할 수 있도록 키 위탁 하는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="21762-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="21762-218">해야 도메인에 가입 된 Windows 8이이 샘플을 실행 하려면 Windows Server 2012 컴퓨터와 도메인 컨트롤러에는 Windows Server 2012 이상 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="21762-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
