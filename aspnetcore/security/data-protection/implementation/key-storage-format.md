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
# <a name="key-storage-format-in-aspnet-core"></a>ASP.NET 코어의 주요 스토리지 형식

<a name="data-protection-implementation-key-storage-format"></a>

개체는 XML 표현에 미사용 으로 저장됩니다. 키 저장소의 기본 디렉터리입니다.

* 윈도우: *% 로컬앱데이터%\ASP.NET\데이터 보호-키\*
* 맥 OS / 리눅스 : *$HOME /.aspnet / 데이터 보호 - 키*

## <a name="the-key-element"></a>\<핵심> 요소

키는 키 리포지토리에 최상위 개체로 존재합니다. 규칙 키에는 파일 이름 **key-{guid}.xml이**있으며{guid}가 키의 ID입니다. 이러한 각 파일에는 단일 키가 포함되어 있습니다. 파일의 형식은 다음과 같습니다.

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

키 \<> 요소에는 다음과 같은 특성과 자식 요소가 포함되어 있습니다.

* 키 ID입니다. 이 값은 신뢰할 수 있는 값으로 처리됩니다. 파일 이름은 단순히 사람의 가독성에 대한 좋은 것입니다.

* 현재 1로 \<고정된 키> 요소의 버전입니다.

* 키의 생성, 활성화 및 만료 날짜입니다.

* 이 \<키에 포함된 인증된 암호화 구현에 대한 정보가 포함된 설명자> 요소입니다.

위의 예에서 키의 ID는 {80732141-ec8f-4b80-af9c-c4d2d1ff8901}이며, 2015년 3월 19일에 생성및 활성화되었으며 수명은 90일입니다. (이 예제에서와 같이 활성화 날짜가 생성 날짜 이전일 수도 있습니다. 이는 API가 작동하는 방식에 있어 실제로 무해하기 때문입니다.)

## <a name="the-descriptor-element"></a>\<설명자> 요소

외부 \<설명자> 요소에는 i인증 암호화해독기Deserializer를 구현하는 형식의 어셈블리 자격을 갖춘 이름인 특성 deserializerType이 포함되어 있습니다. 이 형식은 내부 \<설명자> 요소를 읽고 내에 포함된 정보를 구문 분석하는 작업을 담당합니다.

\<설명자> 요소의 특정 형식은 키에 의해 캡슐화된 인증된 암호화기 구현에 따라 달라지며 각 직렬화 유형은 이에 대해 약간 다른 형식을 기대합니다. 그러나 일반적으로 이 요소에는 알고리즘 정보(이름, 형식, ID 또는 이와 유사한) 및 비밀 키 자료가 포함됩니다. 위의 예에서 설명자는 이 키가 AES-256-CBC 암호화 + HMACSHA256 유효성 검사를 래핑한다고 지정합니다.

## <a name="the-encryptedsecret-element"></a>암호화된비밀 \<> 요소

** &lt;암호화된&gt; ** 비밀 키 자료의 암호화된 형식이 포함된 암호화된 비밀 요소가 있을 수 [있습니다.](xref:security/data-protection/implementation/key-encryption-at-rest) 특성은 `decryptorType` [IXmlDecryptor를](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)구현하는 형식의 어셈블리 인증 된 이름입니다. 이 형식은 내부 ** &lt;암호화된Key&gt; ** 요소를 읽고 원래 일반 텍스트를 복구하기 위해 암호를 해독하는 작업을 담당합니다.

마찬가지로 `<descriptor>`요소의 특정 형식은 `<encryptedSecret>` 사용 중미 암호화 메커니즘에 따라 다릅니다. 위의 예에서 마스터 키는 주석당 Windows DPAPI를 사용하여 암호화됩니다.

## <a name="the-revocation-element"></a>\<해지> 요소

해지는 키 리포지토리에 최상위 개체로 존재합니다. 규칙 해지에 따라 파일 이름 **해지-{timestamp}.xml(특정** 날짜 이전에 모든 키를 취소하는 경우) 또는 **해지-{guid}.xml(특정** 키를 해지하는 경우)이 있습니다. 각 파일에는 \<단일 해지> 요소가 포함되어 있습니다.

개별 키의 해지에 대 한 파일 내용은 아래와 같습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

이 경우 지정된 키만 해지됩니다. 그러나 아래 예제와 같이 키 ID가 "*"인 경우 지정된 해지 날짜 이전의 생성 날짜가 지정된 모든 키가 해지됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

> \<요소는 시스템에서 읽지 않는 이유입니다. 그것은 단순히 해지에 대한 인간이 읽을 수있는 이유를 저장하는 편리한 장소입니다.
