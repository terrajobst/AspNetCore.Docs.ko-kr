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
# <a name="key-management-extensibility-in-aspnet-core"></a>ASP.NET Core에서 키 관리 확장성

> [!TIP]
> 이러한 Api의 기본 개념 중 일부를 설명 하므로이 섹션을 읽기 전에 [키 관리](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 섹션을 참조 하세요.

> [!WARNING]
> 다음 인터페이스 중 하나를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자에 대 한 합니다.

## <a name="key"></a>키

`IKey` 인터페이스는 cryptosystem에서 키의 기본 표현입니다. 용어 키의 "암호화 키 자료" 리터럴 관점에 없는 추상 점에서 사용 됩니다. 키에 다음 속성이 있습니다.

* 활성화, 생성 및 만료 날짜

* 해지 상태

* 키 식별자 (GUID)

::: moniker range=">= aspnetcore-2.0"

또한 `IKey`는이 키에 연결 된 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스를 만드는 데 사용할 수 있는 `CreateEncryptor` 메서드를 노출 합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

또한 `IKey`는이 키에 연결 된 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스를 만드는 데 사용할 수 있는 `CreateEncryptorInstance` 메서드를 노출 합니다.

::: moniker-end

> [!NOTE]
> `IKey` 인스턴스에서 원시 암호화 자료를 검색 하는 API는 없습니다.

## <a name="ikeymanager"></a>IKeyManager

`IKeyManager` 인터페이스는 일반적인 키 저장, 검색 및 조작을 담당 하는 개체를 나타냅니다. 이 세 가지 개략적인 작업을 노출합니다.

* 새 키 만들기 및 저장소에 저장 합니다.

* 저장소에서 모든 키를 가져옵니다.

* 하나 이상의 키를 취소 하 고 해지 정보 저장소를 유지 합니다.

>[!WARNING]
> `IKeyManager` 작성은 매우 고급 작업 이므로 대부분의 개발자는이 작업을 시도해 서는 안 됩니다. 대신, 대부분의 개발자는 [Xmlkeymanager](#xmlkeymanager) 클래스에서 제공 하는 기능을 활용 해야 합니다.

## <a name="xmlkeymanager"></a>XmlKeyManager

`XmlKeyManager` 형식은 `IKeyManager`의 기본 구체적 구현입니다. 미사용 키 암호화 키 위탁 기능 등 몇 가지 유용한 기능을 제공 합니다. 이 시스템의 키는 XML 요소로 표시 됩니다 (특히 [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager`는 작업을 수행 하는 과정에서 여러 다른 구성 요소에 종속 됩니다.

::: moniker range=">= aspnetcore-2.0"

* 새 키에 사용 되는 알고리즘을 지시 하는 `AlgorithmConfiguration`.

* `IXmlRepository`는 저장소에서 키가 유지 되는 위치를 제어 합니다.

* [옵션]을 `IXmlEncryptor` 하 여 미사용 키를 암호화할 수 있습니다.

* 키 에스크로 서비스를 제공 하는 [옵션]을 `IKeyEscrowSink` 합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`는 저장소에서 키가 유지 되는 위치를 제어 합니다.

* [옵션]을 `IXmlEncryptor` 하 여 미사용 키를 암호화할 수 있습니다.

* 키 에스크로 서비스를 제공 하는 [옵션]을 `IKeyEscrowSink` 합니다.

::: moniker-end

다음은 `XmlKeyManager`내에서 이러한 구성 요소를 연결 하는 방법을 나타내는 개략적인 다이어그램입니다.

::: moniker range=">= aspnetcore-2.0"

![키 만들기](key-management/_static/keycreation2.png)

*키 생성/CreateNewKey*

`CreateNewKey`구현에서는 `AlgorithmConfiguration` 구성 요소를 사용 하 여 고유한 `IAuthenticatedEncryptorDescriptor`를 만든 다음 XML로 serialize 합니다. 키 에스크로 싱크가 있는 경우 원시 (암호화 되지 않음된) XML 장기 저장소에 대 한 싱크로 제공 됩니다. 그런 다음 암호화 되지 않은 XML이 `IXmlEncryptor` (필요한 경우)를 통해 암호화 된 XML 문서를 생성 합니다. 이 암호화 된 문서는 `IXmlRepository`을 통해 장기 저장소에 유지 됩니다. `IXmlEncryptor` 구성 되지 않은 경우 암호화 되지 않은 문서는 `IXmlRepository`에 유지 됩니다.

![키 검색](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![키 만들기](key-management/_static/keycreation1.png)

*키 생성/CreateNewKey*

`CreateNewKey`구현에서는 `IAuthenticatedEncryptorConfiguration` 구성 요소를 사용 하 여 고유한 `IAuthenticatedEncryptorDescriptor`를 만든 다음 XML로 serialize 합니다. 키 에스크로 싱크가 있는 경우 원시 (암호화 되지 않음된) XML 장기 저장소에 대 한 싱크로 제공 됩니다. 그런 다음 암호화 되지 않은 XML이 `IXmlEncryptor` (필요한 경우)를 통해 암호화 된 XML 문서를 생성 합니다. 이 암호화 된 문서는 `IXmlRepository`을 통해 장기 저장소에 유지 됩니다. `IXmlEncryptor` 구성 되지 않은 경우 암호화 되지 않은 문서는 `IXmlRepository`에 유지 됩니다.

![키 검색](key-management/_static/keyretrieval1.png)

::: moniker-end

*키 검색/GetAllKeys*

`GetAllKeys`구현에서는 키와 해지를 나타내는 XML 문서를 기본 `IXmlRepository`에서 읽습니다. 이러한 문서는 암호화 하는 경우 시스템은 자동으로 암호를 해독 하 합니다. `XmlKeyManager`은 적절 한 `IAuthenticatedEncryptorDescriptorDeserializer` 인스턴스를 만들어 문서를 `IAuthenticatedEncryptorDescriptor` 인스턴스로 다시 deserialize 합니다. 그러면 개별 `IKey` 인스턴스에 래핑됩니다. 이 `IKey` 인스턴스의 컬렉션은 호출자에 게 반환 됩니다.

특정 XML 요소에 대 한 자세한 내용은 [키 저장소 형식 문서](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)에서 찾을 수 있습니다.

## <a name="ixmlrepository"></a>IXmlRepository

`IXmlRepository` 인터페이스는 백업 저장소에서 xml에 xml을 저장 하 고 검색할 수 있는 형식을 나타냅니다. 이 두 개의 Api를 노출합니다.

* `GetAllElements`:`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

`IXmlRepository` 구현에서는 XML을 전달 하는 XML을 구문 분석할 필요가 없습니다. 불투명으로 XML 문서를 처리 하며 더 높은 계층을 생성 하 고 문서를 구문 분석 하는 방법에 대 한 걱정을 수 있습니다.

`IXmlRepository`를 구현 하는 네 가지 기본 제공 구체적 형식이 있습니다.

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

자세한 내용은 [키 저장소 공급자 문서](xref:security/data-protection/implementation/key-storage-providers) 를 참조 하세요.

다른 백업 저장소 (예: Azure Table Storage)를 사용 하는 경우 사용자 지정 `IXmlRepository`를 등록 하는 것이 좋습니다.

기본 리포지토리 응용 프로그램 전체를 변경 하려면 사용자 지정 `IXmlRepository` 인스턴스를 등록 합니다.

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

## <a name="ixmlencryptor"></a>IXmlEncryptor

`IXmlEncryptor` 인터페이스는 일반 텍스트 XML 요소를 암호화할 수 있는 형식을 나타냅니다. 단일 API를 노출 합니다.

* (XElement plaintextElement) 암호화: EncryptedXmlInfo

Serialize 된 `IAuthenticatedEncryptorDescriptor`에 "암호화 필요"로 표시 된 요소가 포함 된 경우 `XmlKeyManager`는 구성 된 `IXmlEncryptor`의 `Encrypt` 메서드를 통해 해당 요소를 실행 하 고 일반 텍스트 요소 대신 암호화 된 읽거나 요소를 `IXmlRepository`에 유지 합니다. `Encrypt` 메서드의 출력은 `EncryptedXmlInfo` 개체입니다. 이 개체는 결과 암호화 된 읽거나 `XElement`와 해당 요소를 해독 하는 데 사용할 수 있는 `IXmlDecryptor`을 나타내는 형식을 모두 포함 하는 래퍼입니다.

`IXmlEncryptor`를 구현 하는 네 가지 기본 제공 구체적 형식이 있습니다.

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

자세한 내용은 [휴지 상태의 키 암호화 문서](xref:security/data-protection/implementation/key-encryption-at-rest) 를 참조 하세요.

기본 키 암호화 메커니즘 응용 프로그램 전체를 변경 하려면 사용자 지정 `IXmlEncryptor` 인스턴스를 등록 합니다.

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

## <a name="ixmldecryptor"></a>IXmlDecryptor

`IXmlDecryptor` 인터페이스는 `IXmlEncryptor`를 통해 암호화 된 읽거나 된 `XElement`의 암호를 해독 하는 방법을 알고 있는 형식을 나타냅니다. 단일 API를 노출 합니다.

* (XElement encryptedElement) 암호 해독: XElement

`Decrypt` 메서드는 `IXmlEncryptor.Encrypt`에서 수행 되는 암호화를 실행 취소 합니다. 일반적으로 구체적인 `IXmlEncryptor` 구현에는 해당 하는 구체적인 `IXmlDecryptor` 구현이 있습니다.

`IXmlDecryptor`를 구현 하는 형식에는 다음 두 개의 공용 생성자 중 하나가 있어야 합니다.

* .ctor(IServiceProvider)
* .ctor()

> [!NOTE]
> 생성자에 전달 된 `IServiceProvider`는 null 일 수 있습니다.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

`IKeyEscrowSink` 인터페이스는 중요 한 정보를 에스크로 할 수 있는 형식을 나타냅니다. 직렬화 된 설명자에는 암호화 자료와 같은 중요 한 정보가 포함 될 수 있으며,이는 첫 번째 위치의 [IXmlEncryptor](#ixmlencryptor) 유형을 도입 하는 것입니다. 그러나 그리고 사고가 발생할 때 키 링을 삭제할 수 있습니다 또는 손상 되었습니다.

에스크로 인터페이스는 구성 된 [IXmlEncryptor](#ixmlencryptor)변환 되기 전에 원시 직렬화 된 XML에 대 한 액세스를 허용 하는 비상 이스케이프 해치를 제공 합니다. 인터페이스는 단일 API를 노출합니다.

* 저장소 (Guid keyId, XElement 요소)

비즈니스 정책과 일관 된 보안 방식으로 제공 된 요소를 처리 하는 `IKeyEscrowSink` 구현입니다. 에스크로 싱크에서 인증서의 개인 키가 위탁 된 알려진 회사 x.509 인증서를 사용 하 여 XML 요소를 암호화 하는 것이 가능 합니다. `CertificateXmlEncryptor` 형식은이를 지원할 수 있습니다. 또한 `IKeyEscrowSink` 구현은 제공 된 요소를 적절 하 게 유지 하는 일을 담당 합니다.

서버 관리자가 [전역적으로 구성할](xref:security/data-protection/configuration/machine-wide-policy)수 있지만 기본적으로는 에스크로 메커니즘이 사용 되지 않습니다. 아래 샘플에 표시 된 것 처럼 `IDataProtectionBuilder.AddKeyEscrowSink` 메서드를 통해 프로그래밍 방식으로 구성할 수도 있습니다. `AddKeyEscrowSink` 메서드 오버 로드는 `IKeyEscrowSink` 인스턴스를 단일 항목 수 있도록 `IServiceCollection.AddSingleton` 및 `IServiceCollection.AddInstance` 오버 로드를 미러링합니다. 여러 `IKeyEscrowSink` 인스턴스를 등록 하는 경우 키를 생성 하는 동안 각 인스턴스를 호출 하므로 여러 메커니즘에 동시에 키를 위탁 수 있습니다.

`IKeyEscrowSink` 인스턴스에서 자료를 읽을 수 있는 API가 없습니다. 에스크로 메커니즘의 디자인 이론으로 구성 됩니다: 키 자료를 신뢰할 수 있는 기관에 액세스할 수 있도록 하는 것 자체 escrowed 자료에 대 한 액세스를 다시 없어야 아니므로 응용 프로그램 자체는 신뢰할 수 있는 기관, 및입니다.

다음 샘플 코드는 "CONTOSODomain Admins"의 멤버만 복구할 수 있도록 키가 위탁 `IKeyEscrowSink`를 만들고 등록 하는 방법을 보여 줍니다.

> [!NOTE]
> 해야 도메인에 가입 된 Windows 8이이 샘플을 실행 하려면 Windows Server 2012 컴퓨터와 도메인 컨트롤러에는 Windows Server 2012 이상 이어야 합니다.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
