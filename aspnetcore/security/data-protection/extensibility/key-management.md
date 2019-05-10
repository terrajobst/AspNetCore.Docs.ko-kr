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
# <a name="key-management-extensibility-in-aspnet-core"></a>ASP.NET Core에서 키 관리 확장성

> [!TIP]
> 읽기를 [키 관리](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 섹션에서는 이러한 Api의 기본적인 개념 중 일부 설명 하므로이 섹션을 읽어야 합니다.

> [!WARNING]
> 다음 인터페이스 중 하나를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자에 대 한 합니다.

## <a name="key"></a>Key

`IKey` 인터페이스는 키 암호화 시스템의 기본 표현입니다. 용어 키의 "암호화 키 자료" 리터럴 관점에 없는 추상 점에서 사용 됩니다. 키에 다음 속성이 있습니다.

* 활성화, 생성 및 만료 날짜

* 해지 상태

* 키 식별자 (GUID)

::: moniker range=">= aspnetcore-2.0"

또한 `IKey` 노출를 `CreateEncryptor` 만드는 데 사용할 수 있는 메서드는 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스가이 키에 연결 합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

또한 `IKey` 노출를 `CreateEncryptorInstance` 만드는 데 사용할 수 있는 메서드는 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스가이 키에 연결 합니다.

::: moniker-end

> [!NOTE]
> API는 없습니다에서 원시 암호화 자료를 검색 하는 `IKey` 인스턴스.

## <a name="ikeymanager"></a>IKeyManager

`IKeyManager` 인터페이스 일반 키 저장소, 검색 및 조작 하는 일을 담당 하는 개체를 나타냅니다. 이 세 가지 개략적인 작업을 노출합니다.

* 새 키 만들기 및 저장소에 저장 합니다.

* 저장소에서 모든 키를 가져옵니다.

* 하나 이상의 키를 취소 하 고 해지 정보 저장소를 유지 합니다.

>[!WARNING]
> 쓰기는 `IKeyManager` 는 고급 작업이 며 대부분의 개발자는 시도 하지 않아야 합니다. 대신 대부분의 개발자가 제공 하는 기능 사용 해야 합니다 [XmlKeyManager](#xmlkeymanager) 클래스입니다.

## <a name="xmlkeymanager"></a>XmlKeyManager

합니다 `XmlKeyManager` 형식은 기본 구체적인 구현 `IKeyManager`합니다. 미사용 키 암호화 키 위탁 기능 등 몇 가지 유용한 기능을 제공 합니다. 이 시스템의 키 XML 요소로 표시 됩니다 (특히 [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager` 해당 작업을 수행 하는 과정에서 다른 여러 구성 요소에 따라 달라 집니다.

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`에 새 키로 사용 하는 알고리즘을 지정 합니다.

* `IXmlRepository`를 키 저장소에 유지 되는 위치는 컨트롤입니다.

* `IXmlEncryptor` [선택 사항] 미사용 키를 암호화할 수 있습니다.

* `IKeyEscrowSink` [선택 사항] 키 에스크로 서비스 제공 합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`를 키 저장소에 유지 되는 위치는 컨트롤입니다.

* `IXmlEncryptor` [선택 사항] 미사용 키를 암호화할 수 있습니다.

* `IKeyEscrowSink` [선택 사항] 키 에스크로 서비스 제공 합니다.

::: moniker-end

다음은 이러한 구성 요소는 내에서 함께 연결 하는 방법을 나타내는 개략적인 다이어그램 `XmlKeyManager`합니다.

::: moniker range=">= aspnetcore-2.0"

![키 만들기](key-management/_static/keycreation2.png)

*키 생성 / CreateNewKey*

구현의 `CreateNewKey`는 `AlgorithmConfiguration` 구성 요소는 고유한 만드는 데 `IAuthenticatedEncryptorDescriptor`는 다음 XML로 serialize 됩니다. 키 에스크로 싱크가 있는 경우 원시 (암호화 되지 않음된) XML 장기 저장소에 대 한 싱크로 제공 됩니다. 암호화 되지 않은 XML을 통해 실행 됩니다는 `IXmlEncryptor` (필요한 경우)에 암호화 된 XML 문서를 생성 합니다. 이 암호화 된 문서를 통해 장기 저장소에 유지 되는 `IXmlRepository`합니다. (없으면 `IXmlEncryptor` 는 구성 암호화 되지 않은 문서에 유지 되는 `IXmlRepository`.)

![키 검색](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![키 만들기](key-management/_static/keycreation1.png)

*키 생성 / CreateNewKey*

구현의 `CreateNewKey`는 `IAuthenticatedEncryptorConfiguration` 구성 요소는 고유한 만드는 데 `IAuthenticatedEncryptorDescriptor`는 다음 XML로 serialize 됩니다. 키 에스크로 싱크가 있는 경우 원시 (암호화 되지 않음된) XML 장기 저장소에 대 한 싱크로 제공 됩니다. 암호화 되지 않은 XML을 통해 실행 됩니다는 `IXmlEncryptor` (필요한 경우)에 암호화 된 XML 문서를 생성 합니다. 이 암호화 된 문서를 통해 장기 저장소에 유지 되는 `IXmlRepository`합니다. (없으면 `IXmlEncryptor` 는 구성 암호화 되지 않은 문서에 유지 되는 `IXmlRepository`.)

![키 검색](key-management/_static/keyretrieval1.png)

::: moniker-end

*키 검색 / GetAllKeys*

구현의 `GetAllKeys`, XML 문서 나타내는 키 및 해지 내부에서 읽는 `IXmlRepository`합니다. 이러한 문서는 암호화 하는 경우 시스템은 자동으로 암호를 해독 하 합니다. `XmlKeyManager` 적절 한 만듭니다 `IAuthenticatedEncryptorDescriptorDeserializer` 문서를 deserialize 하는 데는 인스턴스를 다시 `IAuthenticatedEncryptorDescriptor` 인스턴스에 개별 다음 래핑됩니다 `IKey` 인스턴스. 이 컬렉션의 `IKey` 인스턴스 호출자에 게 반환 됩니다.

특정 XML 요소에 대 한 자세한 내용은에서 찾을 수 있습니다 합니다 [키 저장소 형식으로 문서](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)합니다.

## <a name="ixmlrepository"></a>IXmlRepository

`IXmlRepository` 인터페이스는 XML을 유지 하 고 백업 저장소에서 XML을 검색할 수 있는 형식을 나타냅니다. 이 두 개의 Api를 노출합니다.

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

구현의 `IXmlRepository` 통과 하는 XML을 구문 분석할 필요가 없습니다. 불투명으로 XML 문서를 처리 하며 더 높은 계층을 생성 하 고 문서를 구문 분석 하는 방법에 대 한 걱정을 수 있습니다.

네 가지 기본 제공 구체적인 유형 구현 하는 `IXmlRepository`:

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage.AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage.AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

참조 된 [키 저장소 공급자 문서](xref:security/data-protection/implementation/key-storage-providers) 자세한 내용은 합니다.

사용자 지정 등록 `IXmlRepository` 다른 백업 저장소 (예: Azure Table Storage)를 사용 하는 경우 적합 합니다.

응용 프로그램 수준의 기본 리포지토리를 변경 하려면 사용자 지정 등록 `IXmlRepository` 인스턴스:

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

`IXmlEncryptor` 인터페이스에는 일반 텍스트 XML 요소를 암호화할 수 있는 형식을 나타냅니다. 단일 API를 노출 합니다.

* Encrypt(XElement plaintextElement): EncryptedXmlInfo

직렬화 된 경우 `IAuthenticatedEncryptorDescriptor` 다음의 "암호화"가 필요한 것으로 표시 하는 요소가 `XmlKeyManager` 요소를 통해 구성 된 실행 됩니다 `IXmlEncryptor`의 `Encrypt` 메서드를 읽거나 요소가 유지 됩니다 보다는 일반 텍스트 요소를 `IXmlRepository`입니다. 출력을 `Encrypt` 메서드는 `EncryptedXmlInfo` 개체입니다. 이 개체는 모두를 읽거나 결과 포함 하는 래퍼 `XElement` 형식을 나타내는 `IXmlDecryptor` 해당 요소를 해독에 사용할 수 있는 합니다.

네 가지 기본 제공 구체적인 유형 구현 하는 `IXmlEncryptor`:

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

참조 된 [rest 문서에서 키 암호화](xref:security/data-protection/implementation/key-encryption-at-rest) 자세한 내용은 합니다.

응용 프로그램 수준의 기본 미사용 키 암호화 메커니즘을 변경 하려면 사용자 지정 등록 `IXmlEncryptor` 인스턴스:

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

합니다 `IXmlDecryptor` 인터페이스에는 암호를 해독 하는 방법을 알고 있는 유형을 나타냅니다는 `XElement` 는 된 암호화를 통해는 `IXmlEncryptor`합니다. 단일 API를 노출 합니다.

* 암호 해독 (XElement encryptedElement): XElement

합니다 `Decrypt` 메서드를 실행 취소 하 여 수행 된 암호화 `IXmlEncryptor.Encrypt`합니다. 일반적으로 각 구체적 `IXmlEncryptor` 구현에는 해당 구체적인 해야 `IXmlDecryptor` 구현 합니다.

구현 하는 형식 `IXmlDecryptor` 다음 두 명의 public 생성자 중 하나가 있어야 함:

* .ctor(IServiceProvider)
* .ctor()

> [!NOTE]
> `IServiceProvider` 전달할 생성자는 null 일 수 있습니다.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

`IKeyEscrowSink` 인터페이스 에스크로 중요 한 정보를 수행할 수 있는 형식을 나타냅니다. Serialize 된 설명자에는 중요 한 정보 (예: 암호화 자료)가 포함 될 수 있습니다 하 고이 되어 소개를 회수 합니다 [IXmlEncryptor](#ixmlencryptor) 애초에 입력 합니다. 그러나 그리고 사고가 발생할 때 키 링을 삭제할 수 있습니다 또는 손상 되었습니다.

에스크로 인터페이스는 구성으로 변환 되기 전에 원시 serialize 된 XML에 대 한 액세스를 허용 하는 응급 이스케이프 해치를 제공 [IXmlEncryptor](#ixmlencryptor)합니다. 인터페이스는 단일 API를 노출합니다.

* 저장소 (Guid keyId, XElement 요소)

것은 `IKeyEscrowSink` 안전한 방식으로 비즈니스 정책을 사용 하 여 일관 된 지정된 된 요소를 처리 하는 구현 합니다. 알려진된 회사 X.509 인증서를 사용 하 여 XML 요소를 암호화 하는 인증서의 개인 키가 에스크로 되었습니다; 에스크로 싱크에 대 한 한 가지 구현을 수 있습니다. `CertificateXmlEncryptor` 형식이 도움이 될 수 있습니다. `IKeyEscrowSink` 구현에 제공된 된 요소를 적절 하 게 유지 하는 일을 담당 이기도 합니다.

기본적으로 없습니다 에스크로 메커니즘을 사용 하지만 서버 관리자 수 [전역적으로이 구성](xref:security/data-protection/configuration/machine-wide-policy)합니다. 또한 구성할 수 있습니다 프로그래밍 방식으로 통해는 `IDataProtectionBuilder.AddKeyEscrowSink` 아래 예제에 나와 있는 것 처럼 메서드. `AddKeyEscrowSink` 메서드 오버 로드 미러를 `IServiceCollection.AddSingleton` 및 `IServiceCollection.AddInstance` 오버 로드로 `IKeyEscrowSink` 를 단일 항목 인스턴스는 합니다. 여러 개인 경우 `IKeyEscrowSink` 인스턴스를 등록, 각각 여러 메커니즘을 동시에 키를 위탁 수 있도록 키 생성 하는 동안 호출 됩니다.

API에서 자료를 읽을 수는 없습니다를 `IKeyEscrowSink` 인스턴스. 에스크로 메커니즘의 디자인 이론으로 구성 됩니다: 키 자료를 신뢰할 수 있는 기관에 액세스할 수 있도록 하는 것 자체 escrowed 자료에 대 한 액세스를 다시 없어야 아니므로 응용 프로그램 자체는 신뢰할 수 있는 기관, 및입니다.

다음 샘플 코드에서는 만들기 및 등록을 `IKeyEscrowSink` "CONTOSODomain Admins"의 구성원만 복구할 수 있도록 키 위탁 하는 위치입니다.

> [!NOTE]
> 해야 도메인에 가입 된 Windows 8이이 샘플을 실행 하려면 Windows Server 2012 컴퓨터와 도메인 컨트롤러에는 Windows Server 2012 이상 이어야 합니다.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
