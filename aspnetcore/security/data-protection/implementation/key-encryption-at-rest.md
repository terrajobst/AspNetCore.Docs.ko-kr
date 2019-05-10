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
# <a name="key-encryption-at-rest-in-aspnet-core"></a><span data-ttu-id="1bfc7-103">ASP.NET Core에서 미사용 데이터 암호화 키</span><span class="sxs-lookup"><span data-stu-id="1bfc7-103">Key encryption at rest in ASP.NET Core</span></span>

<span data-ttu-id="1bfc7-104">데이터 보호 시스템 [기본적으로 검색 메커니즘을 사용 하 여](xref:security/data-protection/configuration/default-settings) 어떻게 암호화 키를 확인 하려면 미사용 암호화 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="1bfc7-105">개발자는 검색 메커니즘을 무시 하 고 수동으로 미사용 키 암호화 해야 하는 방법을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="1bfc7-106">명시적 지정 하는 경우 [지 속성 위치 키](xref:security/data-protection/implementation/key-storage-providers), 데이터 보호 시스템 rest 메커니즘에 기본 키 암호화를 등록 취소 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="1bfc7-107">따라서 키 더 이상 미사용 시 암호화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="1bfc7-108">좋습니다 있습니다 [명시적 키 암호화 메커니즘을 지정](xref:security/data-protection/implementation/key-encryption-at-rest) 프로덕션 배포에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="1bfc7-109">미사용 암호화 메커니즘 옵션은이 항목에서 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="1bfc7-110">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="1bfc7-110">Azure Key Vault</span></span>

<span data-ttu-id="1bfc7-111">키를 저장할 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용 하 여 시스템을 구성 [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) 에 `Startup` 클래스:</span><span class="sxs-lookup"><span data-stu-id="1bfc7-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="1bfc7-112">자세한 내용은 참조 하세요. [ASP.NET Core 데이터 보호를 구성 합니다. ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span><span class="sxs-lookup"><span data-stu-id="1bfc7-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="1bfc7-113">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="1bfc7-113">Windows DPAPI</span></span>

<span data-ttu-id="1bfc7-114">**Windows 배포에만 적용 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="1bfc7-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="1bfc7-115">키 자료를 사용 하 여 암호화는 Windows DPAPI를 사용 하면 [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) 저장소에 유지 되 고 하기 전에 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="1bfc7-116">DPAPI는 현재 컴퓨터 외부에서 읽히지는 데이터에 대 한 적절 한 암호화 메커니즘 (Active Directory까지 이러한 키를 백업할 수는;을 참조 하지만 [DPAPI 및 로밍 프로필](https://support.microsoft.com/kb/309408/#6)).</span><span class="sxs-lookup"><span data-stu-id="1bfc7-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="1bfc7-117">DPAPI 키 미사용 암호화를 구성 하려면 중 하나를 호출 합니다 [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) 확장 메서드:</span><span class="sxs-lookup"><span data-stu-id="1bfc7-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="1bfc7-118">경우 `ProtectKeysWithDpapi` 현재 Windows 사용자 계정에는 지속형된 키 링을 해독할 수만 매개 변수 없이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="1bfc7-119">필요에 따라 컴퓨터 (뿐 아니라 현재 사용자 계정)의 모든 사용자 계정 키 링을 해독할 수 되도록 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="1bfc7-120">X.509 certificate(X.509 인증서)</span><span class="sxs-lookup"><span data-stu-id="1bfc7-120">X.509 certificate</span></span>

<span data-ttu-id="1bfc7-121">앱은 여러 컴퓨터 간에 분산 됩니다, 경우에 컴퓨터 간에 공유 하는 X.509 인증서를 배포 하 고 호스팅된 앱이 인증서를 사용 하 여 미사용 키 암호화를 위한 구성 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="1bfc7-122">.NET Framework 제한 때문에 CAPI 개인 키가 있는 인증서만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="1bfc7-123">이러한 제한 사항을 해결할 수 있는 방법은 아래 콘텐츠를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="1bfc7-124">Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="1bfc7-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="1bfc7-125">**이 메커니즘은 Windows 8/windows Server 2012 이상 에서만 사용할 수 있습니다.**</span><span class="sxs-lookup"><span data-stu-id="1bfc7-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="1bfc7-126">Windows 8 부터는 Windows OS DPAPI NG (CNG DPAPI 라고도 함)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="1bfc7-127">자세한 내용은 [CNG DPAPI에 대 한](/windows/desktop/SecCNG/cng-dpapi)합니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="1bfc7-128">보안 주체가 보호 설명자 규칙에 따라 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="1bfc7-129">호출 하는 다음 예와 [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)를 도메인에 가입 된 사용자 지정된 된 SID 사용 하 여 키 링을 해독할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="1bfc7-130">매개 변수가 없는 오버 로드를 이기도 `ProtectKeysWithDpapiNG`합니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="1bfc7-131">이 편의 메서드를 사용 하 여 규칙을 지정 하려면 "SID {CURRENT_ACCOUNT_SID} =", 여기서 *CURRENT_ACCOUNT_SID* 은 현재 Windows 사용자 계정의 SID:</span><span class="sxs-lookup"><span data-stu-id="1bfc7-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="1bfc7-132">이 시나리오에서 AD 도메인 컨트롤러는 DPAPI NG 작업에서 사용 되는 암호화 키를 배포 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="1bfc7-133">대상 사용자 (제공 하는 id 하에서 실행 하는 프로세스는) 모든 도메인에 가입 된 컴퓨터에서 암호화 된 페이로드를 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="1bfc7-134">인증서 기반 암호화와 Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="1bfc7-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="1bfc7-135">Windows 8.1 / windows Server 2012 R2 앱을 실행 하는 경우 인증서 기반 암호화를 수행 하려면 Windows DPAPI NG 나중에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="1bfc7-136">규칙 설명자 문자열을 사용 하 여 "인증서 HashId:THUMBPRINT =", 여기서 *지문을* 는 인증서의 16 진수로 인코딩된 SHA1 지문:</span><span class="sxs-lookup"><span data-stu-id="1bfc7-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="1bfc7-137">이 리포지토리를 가리키는 모든 앱 키 해독 이상 또는 Windows 8.1 / Windows Server 2012 R2에서 실행 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="1bfc7-138">사용자 지정 키 암호화</span><span class="sxs-lookup"><span data-stu-id="1bfc7-138">Custom key encryption</span></span>

<span data-ttu-id="1bfc7-139">개발자가 사용자 지정을 제공 하 여 고유한 키 암호화 메커니즘을 지정할 수 있습니다 기본 메커니즘은 적절 한가 없는 경우 [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)합니다.</span><span class="sxs-lookup"><span data-stu-id="1bfc7-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
