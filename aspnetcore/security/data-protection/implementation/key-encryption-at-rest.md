---
title: ASP.NET Core에서 미사용 데이터 암호화 키
author: rick-anderson
description: ASP.NET Core 데이터 보호 미사용 데이터 암호화 키의 구현 세부 사항에 알아봅니다.
ms.author: riande
ms.date: 07/16/2018
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 52c3137dbe467096364b42430c92aecc7c15e313
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892310"
---
# <a name="key-encryption-at-rest-in-aspnet-core"></a>ASP.NET Core에서 미사용 데이터 암호화 키

데이터 보호 시스템 [기본적으로 검색 메커니즘을 사용 하 여](xref:security/data-protection/configuration/default-settings) 어떻게 암호화 키를 확인 하려면 미사용 암호화 해야 합니다. 개발자는 검색 메커니즘을 무시 하 고 수동으로 미사용 키 암호화 해야 하는 방법을 지정할 수 있습니다.

> [!WARNING]
> 명시적 지정 하는 경우 [지 속성 위치 키](xref:security/data-protection/implementation/key-storage-providers), 데이터 보호 시스템 rest 메커니즘에 기본 키 암호화를 등록 취소 합니다. 따라서 키 더 이상 미사용 시 암호화 됩니다. 좋습니다 있습니다 [명시적 키 암호화 메커니즘을 지정](xref:security/data-protection/implementation/key-encryption-at-rest) 프로덕션 배포에 대 한 합니다. 미사용 암호화 메커니즘 옵션은이 항목에서 설명 되어 있습니다.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure Key Vault

키를 저장할 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용 하 여 시스템을 구성 [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) 에 `Startup` 클래스:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

자세한 내용은 참조 하세요. [ASP.NET Core 데이터 보호를 구성 합니다. ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).

::: moniker-end

## <a name="windows-dpapi"></a>Windows DPAPI

**Windows 배포에만 적용 됩니다.**

키 자료를 사용 하 여 암호화는 Windows DPAPI를 사용 하면 [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) 저장소에 유지 되 고 하기 전에 합니다. DPAPI는 현재 컴퓨터 외부에서 읽히지는 데이터에 대 한 적절 한 암호화 메커니즘 (Active Directory까지 이러한 키를 백업할 수는;을 참조 하지만 [DPAPI 및 로밍 프로필](https://support.microsoft.com/kb/309408/#6)). DPAPI 키 미사용 암호화를 구성 하려면 중 하나를 호출 합니다 [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) 확장 메서드:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

경우 `ProtectKeysWithDpapi` 현재 Windows 사용자 계정에는 지속형된 키 링을 해독할 수만 매개 변수 없이 호출 됩니다. 필요에 따라 컴퓨터 (뿐 아니라 현재 사용자 계정)의 모든 사용자 계정 키 링을 해독할 수 되도록 지정할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>X.509 certificate(X.509 인증서)

앱은 여러 컴퓨터 간에 분산 됩니다, 경우에 컴퓨터 간에 공유 하는 X.509 인증서를 배포 하 고 호스팅된 앱이 인증서를 사용 하 여 미사용 키 암호화를 위한 구성 편리할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

.NET Framework 제한 때문에 CAPI 개인 키가 있는 인증서만 지원 됩니다. 이러한 제한 사항을 해결할 수 있는 방법은 아래 콘텐츠를 참조 하세요.

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI-NG

**이 메커니즘은 Windows 8/windows Server 2012 이상 에서만 사용할 수 있습니다.**

Windows 8 부터는 Windows OS DPAPI NG (CNG DPAPI 라고도 함)를 지원 합니다. 자세한 내용은 [CNG DPAPI에 대 한](/windows/desktop/SecCNG/cng-dpapi)합니다.

보안 주체가 보호 설명자 규칙에 따라 인코딩됩니다. 호출 하는 다음 예와 [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)를 도메인에 가입 된 사용자 지정된 된 SID 사용 하 여 키 링을 해독할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

매개 변수가 없는 오버 로드를 이기도 `ProtectKeysWithDpapiNG`합니다. 이 편의 메서드를 사용 하 여 규칙을 지정 하려면 "SID {CURRENT_ACCOUNT_SID} =", 여기서 *CURRENT_ACCOUNT_SID* 은 현재 Windows 사용자 계정의 SID:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

이 시나리오에서 AD 도메인 컨트롤러는 DPAPI NG 작업에서 사용 되는 암호화 키를 배포 하는 일을 담당 합니다. 대상 사용자 (제공 하는 id 하에서 실행 하는 프로세스는) 모든 도메인에 가입 된 컴퓨터에서 암호화 된 페이로드를 읽을 수 있습니다.

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>인증서 기반 암호화와 Windows DPAPI-NG

Windows 8.1 / windows Server 2012 R2 앱을 실행 하는 경우 인증서 기반 암호화를 수행 하려면 Windows DPAPI NG 나중에 사용할 수 있습니다. 규칙 설명자 문자열을 사용 하 여 "인증서 HashId:THUMBPRINT =", 여기서 *지문을* 는 인증서의 16 진수로 인코딩된 SHA1 지문:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

이 리포지토리를 가리키는 모든 앱 키 해독 이상 또는 Windows 8.1 / Windows Server 2012 R2에서 실행 되어야 합니다.

## <a name="custom-key-encryption"></a>사용자 지정 키 암호화

개발자가 사용자 지정을 제공 하 여 고유한 키 암호화 메커니즘을 지정할 수 있습니다 기본 메커니즘은 적절 한가 없는 경우 [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)합니다.
