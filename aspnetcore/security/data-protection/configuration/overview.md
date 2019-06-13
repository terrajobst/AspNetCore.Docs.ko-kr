---
title: ASP.NET Core 데이터 보호를 구성 합니다.
author: rick-anderson
description: ASP.NET core에서 데이터 보호를 구성 하는 방법에 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/11/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 65a927b6288ca6cc41ee1bedd1080e52ffe0d3e1
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034927"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="cd778-103">ASP.NET Core 데이터 보호를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="cd778-104">데이터 보호 시스템이 초기화 될 때, 운영 환경에 따른 [기본 설정](xref:security/data-protection/configuration/default-settings) 이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="cd778-105">대부분 이런 기본 설정은 단일 컴퓨터에서 실행되는 앱에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="cd778-106">가지 경우에 기본 설정을 변경 하려면 개발자 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="cd778-107">앱은 여러 컴퓨터 간에 분산 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="cd778-108">규정 준수 상의 이유로 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-108">For compliance reasons.</span></span>

<span data-ttu-id="cd778-109">이러한 시나리오에 대 한 데이터 보호 시스템은 다양 한 구성 API를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="cd778-110">구성 파일에는 마찬가지로 데이터 보호 키 링을 보호 해야 적절 한 권한을 사용 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="cd778-111">미사용 키를 암호화 하도록 선택할 수 있습니다 하지만이 것을 막기 위해 공격자가 새 키를 만들지 못하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="cd778-112">따라서 앱의 보안 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="cd778-113">데이터 보호를 사용 하 여 구성 저장소 위치에는 자체 구성 파일을 보호 하는 방식과 유사 하 게 앱에 제한 된 액세스가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="cd778-114">예를 들어, 디스크에 키 링을 저장 하려는 경우 파일 시스템 권한을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="cd778-115">확인 id만 웹 앱이 실행 되는 읽기, 쓰기 및 해당 디렉터리에 대 한 액세스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="cd778-116">Azure Blob Storage를 사용 하는 경우 웹 앱에만 읽기, 쓰기 또는 등 blob 저장소에 새 항목을 만들 수가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="cd778-117">확장 메서드 [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) 반환 된 [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="cd778-118">`IDataProtectionBuilder` 는 함께 연이어 호출해서 데이터 보호 옵션을 구성할 수 있는 확장 메서드들을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="cd778-119">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="cd778-119">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="cd778-120">키를 저장할 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용 하 여 시스템을 구성 [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) 에 `Startup` 클래스:</span><span class="sxs-lookup"><span data-stu-id="cd778-120">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="cd778-121">키 링 저장소 위치를 설정 (예를 들어 [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span><span class="sxs-lookup"><span data-stu-id="cd778-121">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="cd778-122">호출 하기 때문에 위치를 설정 해야 합니다 `ProtectKeysWithAzureKeyVault` 를 구현 하는 [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) 키 링 저장소 위치를 포함 하 여 자동으로 데이터 보호 설정을 사용 하지 않도록 설정 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-122">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="cd778-123">키 링을 유지 하기 위해 Azure Blob Storage를 사용 하는 앞의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-123">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="cd778-124">자세한 내용은 참조 하세요. [키 저장소 공급자: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cd778-124">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="cd778-125">키 링을 사용 하 여 로컬로 유지할 수도 있습니다 [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system)합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-125">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="cd778-126">`keyIdentifier` 키 암호화에 사용 되는 키 자격 증명 모음 키 식별자입니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-126">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="cd778-127">예를 들어 라는 key vault에서 만든 키 `dataprotection` 에 `contosokeyvault` 키 식별자를 가진 `https://contosokeyvault.vault.azure.net/keys/dataprotection/`합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-127">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="cd778-128">앱에 부여할 **키 래핑 해제** 및 **래핑할 키** key vault에 대 한 사용 권한.</span><span class="sxs-lookup"><span data-stu-id="cd778-128">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="cd778-129">`ProtectKeysWithAzureKeyVault` 오버 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-129">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="cd778-130">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) 사용할 수는 [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) key vault를 사용 하도록 데이터 보호 시스템을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-130">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permits the use of a [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="cd778-131">[(IDataProtectionBuilder, String, String, X509Certificate2) ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) 사용할 수는 `ClientId` 및 [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) key vault를 사용 하도록 데이터 보호 시스템을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-131">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permits the use of a `ClientId` and [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="cd778-132">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) 사용할 수는 `ClientId` 및 `ClientSecret` key vault를 사용 하도록 데이터 보호 시스템을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-132">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permits the use of a `ClientId` and `ClientSecret` to enable the data protection system to use the key vault.</span></span>

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="cd778-133">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="cd778-133">PersistKeysToFileSystem</span></span>

<span data-ttu-id="cd778-134">기본 위치인 *%LOCALAPPDATA%* 대신 UNC 공유에 키를 저장하려면 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)을 이용해서 다음과 같이 시스템을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-134">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="cd778-135">키의 저장 위치를 변경하면 데이터 보호 시스템이 더 이상 저장된 비활성 키를 자동으로 암호화하지 않는데, 이는 DPAPI가 적절한 암호화 메커니즘인지 판단하기가 곤란하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-135">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="cd778-136">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="cd778-136">ProtectKeysWith\*</span></span>

<span data-ttu-id="cd778-137">[ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) 구성 API들 중 하나를 호출하면 시스템이 저장된 비활성 키를 보호하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-137">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="cd778-138">다음 예제는 키를 UNC 공유에 저장하고 특정 X.509 인증서를 이용해서 저장된 비활성 키를 암호화합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-138">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="cd778-139">ASP.NET Core 2.1 이상 버전을 제공할 수 있습니다는 [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 하 [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)와 같은 파일에서 인증서를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-139">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

<span data-ttu-id="cd778-140">내장 키 암호화 메커니즘에 대한 더 많은 예제와 설명은 [비활성 키 암호화](xref:security/data-protection/implementation/key-encryption-at-rest)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-140">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="cd778-141">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="cd778-141">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="cd778-142">ASP.NET Core 2.1 이상 버전, 인증서 회전 하 고 배열을 사용 하 여 미사용 키를 해독할 수 [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 사용 하 여 인증서 [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span><span class="sxs-lookup"><span data-stu-id="cd778-142">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="cd778-143">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="cd778-143">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="cd778-144">키의 기본 수명인 90일 대신 14일의 키 수명을 사용하도록 시스템을 구성하려면 [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-144">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="cd778-145">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="cd778-145">SetApplicationName</span></span>

<span data-ttu-id="cd778-146">기본적으로 데이터 보호 시스템 같은 물리적 키 저장소를 공유 하는 경우에 앱을 기반으로 콘텐츠 루트 경로 서로 격리 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-146">By default, the Data Protection system isolates apps from one another based on their content root paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="cd778-147">따라서 앱들은 서로 다른 앱이 보호한 페이로드를 이해할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-147">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="cd778-148">앱 간 페이로드 보호 되는 공유:</span><span class="sxs-lookup"><span data-stu-id="cd778-148">To share protected payloads among apps:</span></span>

* <span data-ttu-id="cd778-149">구성 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 동일한 값을 사용 하 여 각 앱에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-149">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="cd778-150">앱에서 데이터 보호 API 스택의 동일한 버전을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-150">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="cd778-151">수행할 **하거나** 앱의 프로젝트 파일에서 다음 중:</span><span class="sxs-lookup"><span data-stu-id="cd778-151">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="cd778-152">통해 동일한 공유 프레임 워크 버전을 참조 합니다 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-152">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="cd778-153">참조 동일 [데이터 보호 패키지](xref:security/data-protection/introduction#package-layout) 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-153">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="cd778-154">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="cd778-154">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="cd778-155">키 만료가 다가오더라도 앱이 자동으로 키를 롤링하지 않도록 (새로운 키를 생성하지 않도록) 구성해야 하는 시나리오도 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-155">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="cd778-156">한 가지 사례는 앱들이 주/보조 관계를 갖도록 설정되어 있어서 주 앱만 키 관리 작업을 수행하고 나머지 모든 보조 앱들은 단순히 키 링의 읽기 전용 뷰만 갖고 있는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-156">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="cd778-157">키 링을 사용 하 여 시스템을 구성 하 여 읽기 전용으로 취급 하도록 보조 앱을 구성할 수 있습니다 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span><span class="sxs-lookup"><span data-stu-id="cd778-157">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="cd778-158">응용 프로그램별 격리</span><span class="sxs-lookup"><span data-stu-id="cd778-158">Per-application isolation</span></span>

<span data-ttu-id="cd778-159">ASP.NET Core 호스트에 의해서 데이터 보호 시스템이 제공되는 경우, 여러 앱이 동일한 작업자 프로세스 계정으로 실행되고 동일한 마스터 키 관련 자료를 사용하더라도 서로 자동으로 격리됩니다</span><span class="sxs-lookup"><span data-stu-id="cd778-159">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="cd778-160">이 다소 비슷합니다 IsolateApps 한정자 System.Web의에서 `<machineKey>` 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-160">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="cd778-161">따라서 고유 테 넌 트를 로컬 컴퓨터의 각 앱을 고려 하 여 격리 메커니즘의 작동을 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 판별자로 앱 ID를 자동으로 포함 하는 지정 된 모든 앱에 대 한 루트입니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-161">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="cd778-162">앱의 고유 ID는 앱의 실제 경로:</span><span class="sxs-lookup"><span data-stu-id="cd778-162">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="cd778-163">IIS에서 호스팅되는 앱에 대 한 고유 ID는 앱의 IIS 실제 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-163">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="cd778-164">웹 팜 환경에서 앱을 배포한 경우이 값은 안정적인 가정할 경우 웹 팜의 모든 컴퓨터에서 IIS 환경 비슷하게 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-164">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="cd778-165">자체 호스팅된 앱 실행에 대 한 합니다 [Kestrel 서버](xref:fundamentals/servers/index#kestrel), 고유 ID가 디스크에 앱의 실제 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-165">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="cd778-166">고유 식별자는 재설정 효력을 유지 하도록 설계 되었습니다&mdash;개별 앱 및 컴퓨터 자체입니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-166">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="cd778-167">이 격리 메커니즘은 응용 프로그램에 악의적인 의사가 없음을 전제로 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-167">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="cd778-168">악의적인 응용 프로그램은 언제든지 동일한 작업자 프로세스 계정으로 동작하는 다른 모든 응용 프로그램에 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-168">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="cd778-169">응용 프로그램들 간에 서로 신뢰할 수 없는 공유 호스팅 환경에서는 호스팅 공급자가 응용 프로그램의 기본 키 저장소를 분리하는 등, OS 수준에서 응용 프로그램 간에 격리를 담보할 수 있는 조치를 취해야만 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-169">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="cd778-170">데이터 보호 시스템이 ASP.NET Core 호스트에 의해 제공되지 않는 경우에는 (가령, 개발자가 직접 구체적인 `DataProtectionProvider` 형식을 이용해서 인스턴스를 생성하는 등),</span><span class="sxs-lookup"><span data-stu-id="cd778-170">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="cd778-171">자동으로 응용 프로그램 격리가 비활성화되며 적절한 [용도](xref:security/data-protection/consumer-apis/purpose-strings)를 제공하기만 하면 동일한 키 관련 자료를 사용하는 모든 응용 프로그램들 간에 페이로드를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-171">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="cd778-172">이런 환경에서 응용 프로그램 간 격리를 제공하려면 앞에서 살펴본 예제처럼 구성 개체의 [SetApplicationName](#setapplicationname) 메서드를 호출하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-172">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="cd778-173">UseCryptographicAlgorithms으로 알고리즘 변경하기</span><span class="sxs-lookup"><span data-stu-id="cd778-173">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="cd778-174">새로 생성된 키에 의해 사용되는 데이터 보호 스택의 기본 알고리즘을 변경할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-174">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="cd778-175">이 작업을 수행하는 가장 간단한 방법은 구성 콜백에서 [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) 을 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-175">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

<span data-ttu-id="cd778-176">EncryptionAlgorithm 및 ValidationAlgorithm의 기본값은 각각 AES-256-CBC와 HMACSHA256 입니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-176">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="cd778-177">시스템 관리자가 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy)을 통해서 기본 정책을 설정할 수도 있지만, 명시적으로 `UseCryptographicAlgorithms`를 호출하면 기본 정책이 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-177">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="cd778-178">`UseCryptographicAlgorithms` 을 호출하면 미리 제공되는 목록 중에서 원하는 알고리즘을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-178">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="cd778-179">개발자는 알고리즘 구현을 걱정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-179">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="cd778-180">위의 시나리오에서 데이터 보호 시스템이 Windows에서 실행 중이라면 AES의 CNG 구현을 사용하려고 시도하고,</span><span class="sxs-lookup"><span data-stu-id="cd778-180">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="cd778-181">그렇지 않으면 관리되는 [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) 클래스로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-181">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="cd778-182">[UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)를 호출해서 원하는 구현을 직접 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-182">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="cd778-183">알고리즘을 변경하더라도 키 링에 존재하는 기존 키에는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-183">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="cd778-184">알고리즘 변경은 새로 생성되는 키에만 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-184">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="cd778-185">관리되는 사용자 지정 알고리즘 지정하기</span><span class="sxs-lookup"><span data-stu-id="cd778-185">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cd778-186">사용자 지정 관리 되는 알고리즘을 지정 하려면 만들기를 [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) 구현 형식을 가리키는 인스턴스:</span><span class="sxs-lookup"><span data-stu-id="cd778-186">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="cd778-187">관리되는 사용자 지정 알고리즘을 지정하려면, 구현 형식을 가리키는 [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) 의 인스턴스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-187">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

<span data-ttu-id="cd778-188">일반적으로 \*Type 속성들은 구체적이고 인스턴스를 생성할 수 있는 (매개변수가 없는 public 생성자를 통해서) [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) 및 [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)의 구현을 가리켜야 하지만, 시스템 상 특수한 경우에는 편의를 위해 `typeof(Aes)` 같은 특정 값들을 지정하기도 합니다</span><span class="sxs-lookup"><span data-stu-id="cd778-188">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="cd778-189">SymmetricAlgorithm 은 반드시 키 길이가 128 비트 이상이고 블럭 크키가 64 비트 이상이어야 하며, PKCS #7 패딩을 사용하는 CBC 모드 암호화를 지원해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-189">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="cd778-190">KeyedHashAlgorithm 은 다이제스트 크기가 128 비트 이상이어야 하고, 해시 알고리즘의 다이제스트 길이와 동일한 키 길이를 지원해야 합니다</span><span class="sxs-lookup"><span data-stu-id="cd778-190">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="cd778-191">KeyedHashAlgorithm 이 반드시 HMAC여야 할 필요는 없습니다</span><span class="sxs-lookup"><span data-stu-id="cd778-191">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="cd778-192">사용자 지정 Windows CNG 알고리즘 지정하기</span><span class="sxs-lookup"><span data-stu-id="cd778-192">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cd778-193">CBC 모드 암호화 + HMAC 유효성 검사를 사용하는 사용자 지정 Windows CNG 알고리즘을 지정하려면, 알고리즘 정보를 담고 있는 [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration)의 인스턴스를 생성합니다</span><span class="sxs-lookup"><span data-stu-id="cd778-193">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="cd778-194">CBC 모드 암호화 + HMAC 유효성 검사를 사용하는 사용자 지정 Windows CNG 알고리즘을 지정하려면, 알고리즘 정보를 담고 있는 [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) 의 인스턴스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-194">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="cd778-195">대칭형 블럭 암호화 알고리즘(Symmetric Block Cipher Algorithm)은 반드시 키 길이가 128 비트 이상이고 블럭 크키가 64 비트 이상이어야 하며, PKCS #7 패딩을 사용하는 CBC 모드 암호화를 지원해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-195">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="cd778-196">해시 알고리즘은 다이제스트 크기가 128 비트 이상이어야 하고, BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG 플래그로 시작할 수 있도록 지원해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-196">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="cd778-197">\*Provider 속성들을 null로 설정해서 지정된 알고리즘에 대한 기본 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-197">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="cd778-198">보다 자세한 정보는 [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) 를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-198">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cd778-199">갈루아(Galois)/카운터 모드 암호화 + 유효성 검사를 사용하는 사용자 지정 Windows CNG 알고리즘을 지정하려면, 알고리즘 정보를 담고 있는 [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) 의 인스턴스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-199">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="cd778-200">갈루아(Galois)/카운터 모드 암호화 + 유효성 검사를 사용하는 사용자 지정 Windows CNG 알고리즘을 지정하려면, 알고리즘 정보를 담고 있는 [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) 의 인스턴스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-200">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="cd778-201">대칭형 블럭 암호화 알고리즘(Symmetric Block Cipher Algorithm)은 반드시 키 길이가 128 비트 이상이고 블럭 크키가 정확히 128 비트여야 하며, GCM 암호화를 지원해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-201">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="cd778-202">[EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) 속성을 null로 설정해서 지정된 알고리즘에 대한 기본 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-202">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="cd778-203">보다 자세한 정보는 [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) 를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-203">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="cd778-204">다른 사용자 지정 알고리즘 지정하기</span><span class="sxs-lookup"><span data-stu-id="cd778-204">Specifying other custom algorithms</span></span>

<span data-ttu-id="cd778-205">기본적인 API로 제공되지는 않지만 데이터 보호 시스템은 거의 모든 유형의 알고리즘을 지정할 수 있도록 충분한 확장이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-205">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="cd778-206">예를 들어서, 하드웨어 보안 모듈 (HSM) 내에 포함된 모든 키들을 유지하고, 핵심 암호화 및 복호화 루틴의 사용자 지정 구현을 제공하는 것도 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-206">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="cd778-207">보다 자세한 정보는 [핵심 암호화 확장성(Core Cryptography Extensibility)](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor)의 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto) 절을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-207">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="cd778-208">Docker 컨테이너에서 호스팅할 때 키 유지하기</span><span class="sxs-lookup"><span data-stu-id="cd778-208">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="cd778-209">[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) 컨테이너에서 호스팅할 경우, 다음 중 한 곳에 키를 저장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd778-209">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="cd778-210">공유 볼륨이나 호스트 탑재 볼륨 같이 컨테이너의 수명과 무관하게 유지되는 Docker 볼륨의 폴더</span><span class="sxs-lookup"><span data-stu-id="cd778-210">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="cd778-211">[Azure 키 자격 증명 모음](https://azure.microsoft.com/services/key-vault/) 또는 [Redis](https://redis.io/)와 같은 외부 공급자</span><span class="sxs-lookup"><span data-stu-id="cd778-211">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd778-212">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cd778-212">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
