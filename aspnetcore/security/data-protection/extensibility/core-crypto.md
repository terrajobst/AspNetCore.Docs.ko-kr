---
title: ASP.NET Core에서 core 암호화 확장성
author: rick-anderson
description: IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer, 및 최상위 factory에 알아봅니다.
ms.author: riande
ms.date: 8/11/2017
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: cf4a142992efe5b00a75285ef9ad9735fe7be411
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896680"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>ASP.NET Core에서 core 암호화 확장성

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> 다음 인터페이스 중 하나를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자에 대 한 합니다.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

합니다 **IAuthenticatedEncryptor** 인터페이스는 암호화 하위 시스템의 기본 빌딩 블록입니다. 일반적으로 키 당 하나의 IAuthenticatedEncryptor 되며 모든 암호화 키 자료 및 암호화 작업을 수행 하는 데 필요한 정보를 알고리즘 IAuthenticatedEncryptor 인스턴스를 래핑합니다.

해당 이름에서 알 수 있듯이, 형식이 암호화 및 암호 해독에 대 한 인증 된 서비스를 제공 하는 일을 담당 합니다. 다음 두 가지 Api를 노출합니다.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

Encrypt 메서드는 일반 텍스트 암호화 및 인증 태그를 포함 하는 blob를 반환 합니다. 인증 태그 자체는 AAD 최종 페이로드에서 복구할 수 없는 필요 하지만 추가 인증된 데이터 (AAD) 포함 되어야 합니다. 암호 해독 메서드 인증 태그의 유효성을 검사 하 고 해독된 페이로드를 반환 합니다. ArgumentNullException 제외한과 유사한 모든 오류는 CryptographicException를 homogenized 해야 합니다.

> [!NOTE]
> IAuthenticatedEncryptor 인스턴스 자체가 키 자료를 포함 하도록 실제로 필요 하지 않습니다. 예를 들어, 구현 모든 작업에 대 한 HSM을 위임할 수 있습니다.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>IAuthenticatedEncryptor를 만드는 방법

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

합니다 **IAuthenticatedEncryptorFactory** 인터페이스를 만드는 방법을 알고 있는 유형을 나타냅니다는 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스. 해당 API는 다음과 같습니다.

* CreateEncryptorInstance (IKey 키): IAuthenticatedEncryptor

지정된 된 IKey 인스턴스에서 대 한 해당 CreateEncryptorInstance 메서드에 의해 만들어진 모든 인증 된 암호기 고려해 야 에서처럼 해당는 아래 코드 샘플입니다.

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

합니다 **IAuthenticatedEncryptorDescriptor** 인터페이스를 만드는 방법을 알고 있는 유형을 나타냅니다는 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스. 해당 API는 다음과 같습니다.

* CreateEncryptorInstance() : IAuthenticatedEncryptor

* ExportToXml() : XmlSerializedDescriptorInfo

IAuthenticatedEncryptor, 같은 IAuthenticatedEncryptorDescriptor 인스턴스에 하나의 특정 키 래핑로 간주 됩니다. 이 지정된 된 IAuthenticatedEncryptorDescriptor 인스턴스에서 대 한 해당 CreateEncryptorInstance 메서드에 의해 만들어진 모든 인증 된 암호기 고려해 야 에서처럼 해당 의미는 아래 코드 샘플입니다.

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x만)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

합니다 **IAuthenticatedEncryptorDescriptor** 인터페이스 자신을 XML로 내보낼 수 있는 형식을 나타냅니다. 해당 API는 다음과 같습니다.

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>XML Serialization

IAuthenticatedEncryptor IAuthenticatedEncryptorDescriptor 사이의 주요 차이점 설명자 암호기를 만들고 올바른 인수를 제공 하는 방법을 알고 있는 경우 구현이 SymmetricAlgorithm 및 KeyedHashAlgorithm 의존 하는 IAuthenticatedEncryptor를 것이 좋습니다. 암호기의 작업이 형식을 사용 하는 것 하지만 반드시 있는지 알 수 없으므로 이러한 형식에서 발생 한 위치 응용 프로그램 다시 시작 하는 경우 자체를 다시 만드는 방법에 대 한 적절 한 설명을 작성 실제로 수 없습니다. 설명자를 기반으로 더 높은 수준으로 작동합니다. 설명자 암호기 인스턴스를 만드는 방법을 알고 있으므로 (예를 들어 알고 필수 알고리즘을 만드는 방법), 응용 프로그램을 다시 설정한 후 암호기 인스턴스를 다시 만들 수 있도록 해당 지식을 XML 형식의 serialize 할 수 있습니다.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

설명자 해당 ExportToXml 루틴을 통해 직렬화 할 수 있습니다. 이 루틴을 XmlSerializedDescriptorInfo 두 속성을 포함 하는 반환: XElement 표현을 나타내는 형식과 설명자를 [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) 일 수 있음 해당 XElement 지정이 설명자 부활 하는 데 사용 합니다.

Serialize 된 설명자는 암호화 키 자료와 같은 중요 한 정보를 포함할 수 있습니다. 데이터 보호 시스템은 기본적으로 지원 저장소에 유지에 전에 정보를 암호화 합니다. 설명자를 이용 하려면이 특성 이름 "requiresEncryption"를 사용 하 여 중요 한 정보를 포함 하는 요소를 표시 합니다 (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), "true" 값입니다.

>[!TIP]
> 이 특성을 설정 하기 위한 도우미 API 있습니다. XElement.MarkAsRequiresEncryption() Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel 네임 스페이스에 있는 확장 메서드를 호출 합니다.

Serialize 된 설명자를 중요 한 정보가 포함 되지 않습니다 하는 경우 수도 있습니다. HSM에 저장 된 암호화 키의 경우 다시 생각해 보세요. HSM의 일반 텍스트 형태로 자료를 노출 하지 않습니다 하므로 자체를 직렬화 할 때 키 자료 아웃 설명자를 쓸 수 없습니다. 대신 설명자 (HSM이 방식으로 내보내기 허용) 하는 경우 키 또는 키의 HSM의 고유 식별자 키 래핑 버전 작성 될 수 있습니다.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

합니다 **IAuthenticatedEncryptorDescriptorDeserializer** 인터페이스 XElement에서 IAuthenticatedEncryptorDescriptor 인스턴스를 deserialize 하는 방법을 아는 형식을 나타냅니다. 이 단일 메서드를 노출합니다.

* ImportFromXml (XElement 요소). IAuthenticatedEncryptorDescriptor

ImportFromXml 메서드에서 사용 하 여 반환 된 XElement [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) 원래 IAuthenticatedEncryptorDescriptor 해당을 만듭니다.

IAuthenticatedEncryptorDescriptorDeserializer를 구현 하는 형식에는 다음 두 명의 public 생성자 중 하나 있어야 합니다.

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> 생성자에 전달 하는 IServiceProvider는 null 일 수 있습니다.

## <a name="the-top-level-factory"></a>최상위 팩터리

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

합니다 **AlgorithmConfiguration** 클래스를 만드는 방법을 알고 형식을 나타냅니다 [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) 인스턴스. 단일 API를 노출합니다.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

최상위 팩터리에서 AlgorithmConfiguration 생각 합니다. 구성 템플릿으로 사용 됩니다. 알고리즘 정보를 래핑합니다 (예를 들어,이 구성은 설명자는 AES-128-GCM 마스터 키를 사용 하 여 생성) 되었지만 특정 키와 연관 된 되지 않았습니다.

CreateNewDescriptor 라고, 새 키 자료만이 호출에 대해 생성 되 고 새 IAuthenticatedEncryptorDescriptor 생성 되는 경우이 키 자료 및 자료를 사용 하는 데 필요한 알고리즘 정보를 래핑하는 합니다. 키 자료 없습니다 수 소프트웨어에서 만든 (메모리에 보관)를 만들고는 HSM 및 기타 등등 내에 보관 될 수 없습니다. 중요 한 점은 두 CreateNewDescriptor 호출 해당 IAuthenticatedEncryptorDescriptor 인스턴스를 만들지 마십시오입니다.

AlgorithmConfiguration 형식와 같은 키를 만드는 루틴에 대 한 진입점으로 사용 됩니다 [자동 키 롤링](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)합니다. 이후 모든 키에 대 한 구현을 변경 하려면 KeyManagementOptions에서 AuthenticatedEncryptorConfiguration 속성을 설정 합니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

합니다 **IAuthenticatedEncryptorConfiguration** 인터페이스를 만드는 방법을 알고 형식을 나타냅니다 [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) 인스턴스. 단일 API를 노출합니다.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

최상위 팩터리에서 IAuthenticatedEncryptorConfiguration 생각 합니다. 구성 템플릿으로 사용 됩니다. 알고리즘 정보를 래핑합니다 (예를 들어,이 구성은 설명자는 AES-128-GCM 마스터 키를 사용 하 여 생성) 되었지만 특정 키와 연관 된 되지 않았습니다.

CreateNewDescriptor 라고, 새 키 자료만이 호출에 대해 생성 되 고 새 IAuthenticatedEncryptorDescriptor 생성 되는 경우이 키 자료 및 자료를 사용 하는 데 필요한 알고리즘 정보를 래핑하는 합니다. 키 자료 없습니다 수 소프트웨어에서 만든 (메모리에 보관)를 만들고는 HSM 및 기타 등등 내에 보관 될 수 없습니다. 중요 한 점은 두 CreateNewDescriptor 호출 해당 IAuthenticatedEncryptorDescriptor 인스턴스를 만들지 마십시오입니다.

IAuthenticatedEncryptorConfiguration 형식와 같은 키를 만드는 루틴에 대 한 진입점으로 사용 됩니다 [자동 키 롤링](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)합니다. 이후 모든 키에 대 한 구현을 변경 하려면 단일 IAuthenticatedEncryptorConfiguration 서비스 컨테이너에 등록 합니다.

---
