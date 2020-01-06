---
title: ASP.NET Core 데이터 보호 구성
author: rick-anderson
description: ASP.NET Core에서 데이터 보호를 구성 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: cda510d0f8211641e3544b53ded79878d717cc58
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358412"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="e8891-103">ASP.NET Core 데이터 보호 구성</span><span class="sxs-lookup"><span data-stu-id="e8891-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="e8891-104">데이터 보호 시스템이 초기화 될 때, 운영 환경에 따른 [기본 설정](xref:security/data-protection/configuration/default-settings) 이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="e8891-105">대부분 이런 기본 설정은 단일 컴퓨터에서 실행되는 앱에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="e8891-106">개발자가 기본 설정을 변경 해야 할 수 있는 경우는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="e8891-107">앱이 여러 컴퓨터에 걸쳐 분산 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="e8891-108">규정 준수 상의 이유로.</span><span class="sxs-lookup"><span data-stu-id="e8891-108">For compliance reasons.</span></span>

<span data-ttu-id="e8891-109">이러한 시나리오의 경우 데이터 보호 시스템은 다양 한 구성 API를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="e8891-110">구성 파일과 마찬가지로 데이터 보호 키 링은 적절 한 권한을 사용 하 여 보호 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="e8891-111">미사용 키를 암호화 하도록 선택할 수 있지만 공격자가 새 키를 만들 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="e8891-112">따라서 앱의 보안에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="e8891-113">데이터 보호를 사용 하 여 구성 된 저장소 위치는 구성 파일을 보호 하는 방식과 비슷하게 앱 자체에 대 한 액세스를 제한 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="e8891-114">예를 들어 디스크에 키 링을 저장 하도록 선택 하는 경우 파일 시스템 권한을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="e8891-115">웹 앱이 실행 되는 id에만 해당 디렉터리에 대 한 읽기, 쓰기 및 만들기 권한이 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="e8891-116">Azure Blob Storage 사용 하는 경우 웹 앱에만 Blob 저장소에서 새 항목을 읽거나, 쓰고, 만들 수 있는 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="e8891-117">확장 메서드 [Adddataprotection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) 은 [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="e8891-118">`IDataProtectionBuilder` 는 함께 연이어 호출해서 데이터 보호 옵션을 구성할 수 있는 확장 메서드들을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e8891-119">이 문서에서 사용 되는 데이터 보호 확장에는 다음 NuGet 패키지가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-119">The following NuGet packages are required for the Data Protection extensions used in this article:</span></span>

* [<span data-ttu-id="e8891-120">AspNetCore. AzureStorage</span><span class="sxs-lookup"><span data-stu-id="e8891-120">Microsoft.AspNetCore.DataProtection.AzureStorage</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)
* [<span data-ttu-id="e8891-121">AspNetCore. AzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="e8891-121">Microsoft.AspNetCore.DataProtection.AzureKeyVault</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault/)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="e8891-122">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="e8891-122">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="e8891-123">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)에 키를 저장 하려면 `Startup` 클래스에서 [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) 를 사용 하 여 시스템을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-123">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="e8891-124">키 링 저장소 위치 (예: [Persistkeystoazureblobstorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage))를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-124">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="e8891-125">`ProtectKeysWithAzureKeyVault`를 호출 하면 키 링 저장소 위치를 포함 하 여 자동 데이터 보호 설정을 사용 하지 않도록 설정 하는 [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) 을 구현 하기 때문에 위치를 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-125">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="e8891-126">앞의 예제에서는 Azure Blob Storage를 사용 하 여 키 링을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-126">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="e8891-127">자세한 내용은 [키 저장소 공급자: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e8891-127">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="e8891-128">[Persistkeystofilesystem](xref:security/data-protection/implementation/key-storage-providers#file-system)을 사용 하 여 키 링을 로컬로 유지할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-128">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="e8891-129">`keyIdentifier` 키 암호화에 사용 되는 key vault 키 식별자입니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-129">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="e8891-130">예를 들어 `contosokeyvault`에서 `dataprotection` 이라는 키 자격 증명 모음에서 만든 키에는 키 식별자 `https://contosokeyvault.vault.azure.net/keys/dataprotection/`있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-130">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="e8891-131">키 **래핑** 및 키 자격 증명 모음에 대 한 **키 래핑 권한을** 앱에 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-131">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="e8891-132">`ProtectKeysWithAzureKeyVault` 오버 로드:</span><span class="sxs-lookup"><span data-stu-id="e8891-132">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="e8891-133">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) 는 [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) 를 사용 하 여 데이터 보호 시스템에서 키 자격 증명 모음을 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-133">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permits the use of a [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="e8891-134">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, string, string, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) 를 `ClientId` 사용 하 여 데이터 보호 시스템이 키 자격 증명 모음을 사용 [하도록 할 수](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-134">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permits the use of a `ClientId` and [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="e8891-135">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, string, string, string)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) 를 `ClientSecret` `ClientId` 사용 하 여 데이터 보호 시스템이 키 자격 증명 모음을 사용 하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-135">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permits the use of a `ClientId` and `ClientSecret` to enable the data protection system to use the key vault.</span></span>

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="e8891-136">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="e8891-136">PersistKeysToFileSystem</span></span>

<span data-ttu-id="e8891-137">기본 위치인 *%LOCALAPPDATA%* 대신 UNC 공유에 키를 저장하려면 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)을 이용해서 다음과 같이 시스템을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-137">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="e8891-138">키의 저장 위치를 변경하면 데이터 보호 시스템이 더 이상 저장된 비활성 키를 자동으로 암호화하지 않는데, 이는 DPAPI가 적절한 암호화 메커니즘인지 판단하기가 곤란하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-138">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="e8891-139">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="e8891-139">ProtectKeysWith\*</span></span>

<span data-ttu-id="e8891-140">[ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) 구성 API들 중 하나를 호출하면 시스템이 저장된 비활성 키를 보호하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-140">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="e8891-141">다음 예제는 키를 UNC 공유에 저장하고 특정 X.509 인증서를 이용해서 저장된 비활성 키를 암호화합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-141">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e8891-142">ASP.NET Core 2.1 이상에서는 파일에서 로드 한 인증서와 같은 [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)에 [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-142">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

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

<span data-ttu-id="e8891-143">내장 키 암호화 메커니즘에 대한 더 많은 예제와 설명은 [비활성 키 암호화](xref:security/data-protection/implementation/key-encryption-at-rest)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-143">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="e8891-144">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="e8891-144">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="e8891-145">ASP.NET Core 2.1 이상에서는 [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate)를 사용 하 여 [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 인증서 배열을 사용 하 여 rest에서 인증서를 회전 하 고 키를 해독할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-145">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

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

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="e8891-146">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="e8891-146">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="e8891-147">키의 기본 수명인 90일 대신 14일의 키 수명을 사용하도록 시스템을 구성하려면 [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-147">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="e8891-148">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="e8891-148">SetApplicationName</span></span>

<span data-ttu-id="e8891-149">기본적으로 데이터 보호 시스템은 동일한 물리적 키 리포지토리를 공유 하는 경우에도 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 기반으로 하 여 앱을 서로 격리 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-149">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="e8891-150">따라서 앱들은 서로 다른 앱이 보호한 페이로드를 이해할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-150">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="e8891-151">앱 간에 보호 된 페이로드를 공유 하려면:</span><span class="sxs-lookup"><span data-stu-id="e8891-151">To share protected payloads among apps:</span></span>

* <span data-ttu-id="e8891-152">동일한 값을 사용 하 여 각 앱에 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-152">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="e8891-153">앱에서 동일한 버전의 데이터 보호 API 스택을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-153">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="e8891-154">앱의 프로젝트 파일에서 다음 중 **하나** 를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-154">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="e8891-155">[AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 통해 동일한 공유 프레임 워크 버전을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-155">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="e8891-156">동일한 [데이터 보호 패키지](xref:security/data-protection/introduction#package-layout) 버전을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-156">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="e8891-157">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="e8891-157">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="e8891-158">키 만료가 다가오더라도 앱이 자동으로 키를 롤링하지 않도록 (새로운 키를 생성하지 않도록) 구성해야 하는 시나리오도 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-158">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="e8891-159">한 가지 사례는 앱들이 주/보조 관계를 갖도록 설정되어 있어서 주 앱만 키 관리 작업을 수행하고 나머지 모든 보조 앱들은 단순히 키 링의 읽기 전용 뷰만 갖고 있는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-159">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="e8891-160"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>를 사용 하 여 시스템을 구성 하 여 키 링을 읽기 전용으로 처리 하도록 보조 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-160">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="e8891-161">응용 프로그램별 격리</span><span class="sxs-lookup"><span data-stu-id="e8891-161">Per-application isolation</span></span>

<span data-ttu-id="e8891-162">ASP.NET Core 호스트에 의해서 데이터 보호 시스템이 제공되는 경우, 여러 앱이 동일한 작업자 프로세스 계정으로 실행되고 동일한 마스터 키 관련 자료를 사용하더라도 서로 자동으로 격리됩니다</span><span class="sxs-lookup"><span data-stu-id="e8891-162">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="e8891-163">이는 System.web의 `<machineKey>` 요소에 있는 IsolateApps 한정자와 약간 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-163">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="e8891-164">격리 메커니즘은 로컬 컴퓨터의 각 앱을 고유 테 넌 트로 간주 하 여 작동 하므로 지정 된 앱에 대 한 루트 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>에는 자동으로 앱 ID가 판별자로 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-164">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="e8891-165">앱의 고유 ID는 앱의 실제 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-165">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="e8891-166">IIS에서 호스트 되는 앱의 경우 고유 ID는 앱의 IIS 실제 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-166">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="e8891-167">웹 팜 환경에 앱을 배포 하는 경우이 값은 안정적입니다. IIS 환경이 웹 팜의 모든 컴퓨터에서 유사 하 게 구성 되어 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-167">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="e8891-168">[Kestrel 서버](xref:fundamentals/servers/index#kestrel)에서 실행 되는 자체 호스트 된 앱의 경우 고유 ID는 디스크의 앱에 대 한 실제 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-168">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="e8891-169">고유 식별자는 개별 앱과 컴퓨터 자체의&mdash;다시 설정 된 상태로 유지 되도록 설계 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-169">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="e8891-170">이 격리 메커니즘은 응용 프로그램에 악의적인 의사가 없음을 전제로 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-170">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="e8891-171">악의적인 응용 프로그램은 언제든지 동일한 작업자 프로세스 계정으로 동작하는 다른 모든 응용 프로그램에 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-171">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="e8891-172">응용 프로그램들 간에 서로 신뢰할 수 없는 공유 호스팅 환경에서는 호스팅 공급자가 응용 프로그램의 기본 키 저장소를 분리하는 등, OS 수준에서 응용 프로그램 간에 격리를 담보할 수 있는 조치를 취해야만 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-172">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="e8891-173">데이터 보호 시스템이 ASP.NET Core 호스트에 의해 제공되지 않는 경우에는 (가령, 개발자가 직접 구체적인 `DataProtectionProvider` 형식을 이용해서 인스턴스를 생성하는 등),</span><span class="sxs-lookup"><span data-stu-id="e8891-173">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="e8891-174">자동으로 응용 프로그램 격리가 비활성화되며 적절한 [용도](xref:security/data-protection/consumer-apis/purpose-strings)를 제공하기만 하면 동일한 키 관련 자료를 사용하는 모든 응용 프로그램들 간에 페이로드를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-174">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="e8891-175">이런 환경에서 응용 프로그램 간 격리를 제공하려면 앞에서 살펴본 예제처럼 구성 개체의 [SetApplicationName](#setapplicationname) 메서드를 호출하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-175">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="e8891-176">UseCryptographicAlgorithms으로 알고리즘 변경하기</span><span class="sxs-lookup"><span data-stu-id="e8891-176">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="e8891-177">새로 생성된 키에 의해 사용되는 데이터 보호 스택의 기본 알고리즘을 변경할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-177">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="e8891-178">이 작업을 수행하는 가장 간단한 방법은 구성 콜백에서 [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) 을 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-178">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

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

<span data-ttu-id="e8891-179">EncryptionAlgorithm 및 ValidationAlgorithm의 기본값은 각각 AES-256-CBC와 HMACSHA256 입니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-179">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="e8891-180">시스템 관리자가 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy)을 통해서 기본 정책을 설정할 수도 있지만, 명시적으로 `UseCryptographicAlgorithms`를 호출하면 기본 정책이 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-180">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="e8891-181">`UseCryptographicAlgorithms` 을 호출하면 미리 제공되는 목록 중에서 원하는 알고리즘을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-181">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="e8891-182">개발자는 알고리즘 구현을 걱정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-182">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="e8891-183">위의 시나리오에서 데이터 보호 시스템이 Windows에서 실행 중이라면 AES의 CNG 구현을 사용하려고 시도하고,</span><span class="sxs-lookup"><span data-stu-id="e8891-183">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="e8891-184">그렇지 않으면 관리되는 [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) 클래스로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-184">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="e8891-185">[UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)를 호출해서 원하는 구현을 직접 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-185">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="e8891-186">알고리즘을 변경하더라도 키 링에 존재하는 기존 키에는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-186">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="e8891-187">알고리즘 변경은 새로 생성되는 키에만 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-187">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="e8891-188">관리되는 사용자 지정 알고리즘 지정하기</span><span class="sxs-lookup"><span data-stu-id="e8891-188">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e8891-189">사용자 지정 관리 되는 알고리즘을 지정 하려면 구현 유형을 가리키는 [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-189">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="e8891-190">관리되는 사용자 지정 알고리즘을 지정하려면, 구현 형식을 가리키는 [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) 의 인스턴스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-190">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="e8891-191">일반적으로 \*Type 속성들은 구체적이고 인스턴스를 생성할 수 있는 (매개변수가 없는 public 생성자를 통해서) [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) 및 [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)의 구현을 가리켜야 하지만, 시스템 상 특수한 경우에는 편의를 위해 `typeof(Aes)` 같은 특정 값들을 지정하기도 합니다</span><span class="sxs-lookup"><span data-stu-id="e8891-191">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="e8891-192">SymmetricAlgorithm 은 반드시 키 길이가 128 비트 이상이고 블럭 크키가 64 비트 이상이어야 하며, PKCS #7 패딩을 사용하는 CBC 모드 암호화를 지원해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-192">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="e8891-193">KeyedHashAlgorithm 은 다이제스트 크기가 128 비트 이상이어야 하고, 해시 알고리즘의 다이제스트 길이와 동일한 키 길이를 지원해야 합니다</span><span class="sxs-lookup"><span data-stu-id="e8891-193">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="e8891-194">KeyedHashAlgorithm 이 반드시 HMAC여야 할 필요는 없습니다</span><span class="sxs-lookup"><span data-stu-id="e8891-194">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="e8891-195">사용자 지정 Windows CNG 알고리즘 지정하기</span><span class="sxs-lookup"><span data-stu-id="e8891-195">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e8891-196">CBC 모드 암호화 + HMAC 유효성 검사를 사용하는 사용자 지정 Windows CNG 알고리즘을 지정하려면, 알고리즘 정보를 담고 있는 [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration)의 인스턴스를 생성합니다</span><span class="sxs-lookup"><span data-stu-id="e8891-196">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="e8891-197">CBC 모드 암호화 + HMAC 유효성 검사를 사용하는 사용자 지정 Windows CNG 알고리즘을 지정하려면, 알고리즘 정보를 담고 있는 [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) 의 인스턴스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-197">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="e8891-198">대칭형 블럭 암호화 알고리즘(Symmetric Block Cipher Algorithm)은 반드시 키 길이가 128 비트 이상이고 블럭 크키가 64 비트 이상이어야 하며, PKCS #7 패딩을 사용하는 CBC 모드 암호화를 지원해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-198">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="e8891-199">해시 알고리즘은 다이제스트 크기가 128 비트 이상이어야 하고, BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG 플래그로 시작할 수 있도록 지원해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-199">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="e8891-200">\*Provider 속성들을 null로 설정해서 지정된 알고리즘에 대한 기본 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-200">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="e8891-201">보다 자세한 정보는 [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) 를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-201">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e8891-202">갈루아(Galois)/카운터 모드 암호화 + 유효성 검사를 사용하는 사용자 지정 Windows CNG 알고리즘을 지정하려면, 알고리즘 정보를 담고 있는 [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) 의 인스턴스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-202">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="e8891-203">갈루아(Galois)/카운터 모드 암호화 + 유효성 검사를 사용하는 사용자 지정 Windows CNG 알고리즘을 지정하려면, 알고리즘 정보를 담고 있는 [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) 의 인스턴스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-203">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="e8891-204">대칭형 블럭 암호화 알고리즘(Symmetric Block Cipher Algorithm)은 반드시 키 길이가 128 비트 이상이고 블럭 크키가 정확히 128 비트여야 하며, GCM 암호화를 지원해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-204">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="e8891-205">[EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) 속성을 null로 설정해서 지정된 알고리즘에 대한 기본 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-205">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="e8891-206">보다 자세한 정보는 [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) 를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-206">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="e8891-207">다른 사용자 지정 알고리즘 지정하기</span><span class="sxs-lookup"><span data-stu-id="e8891-207">Specifying other custom algorithms</span></span>

<span data-ttu-id="e8891-208">기본적인 API로 제공되지는 않지만 데이터 보호 시스템은 거의 모든 유형의 알고리즘을 지정할 수 있도록 충분한 확장이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-208">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="e8891-209">예를 들어서, 하드웨어 보안 모듈 (HSM) 내에 포함된 모든 키들을 유지하고, 핵심 암호화 및 복호화 루틴의 사용자 지정 구현을 제공하는 것도 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-209">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="e8891-210">보다 자세한 정보는 [핵심 암호화 확장성(Core Cryptography Extensibility)](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor)의 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto) 절을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-210">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="e8891-211">Docker 컨테이너에서 호스팅할 때 키 유지하기</span><span class="sxs-lookup"><span data-stu-id="e8891-211">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="e8891-212">[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) 컨테이너에서 호스팅할 경우, 다음 중 한 곳에 키를 저장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-212">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="e8891-213">공유 볼륨이나 호스트 탑재 볼륨 같이 컨테이너의 수명과 무관하게 유지되는 Docker 볼륨의 폴더</span><span class="sxs-lookup"><span data-stu-id="e8891-213">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="e8891-214">[Azure 키 자격 증명 모음](https://azure.microsoft.com/services/key-vault/) 또는 [Redis](https://redis.io/)와 같은 외부 공급자</span><span class="sxs-lookup"><span data-stu-id="e8891-214">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="e8891-215">Redis를 사용 하 여 키 유지</span><span class="sxs-lookup"><span data-stu-id="e8891-215">Persisting keys with Redis</span></span>

<span data-ttu-id="e8891-216">[Redis 데이터 지 속성](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) 을 지 원하는 Redis 버전만 사용 하 여 키를 저장 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-216">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="e8891-217">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) 는 영구적 이며 키를 저장 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8891-217">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="e8891-218">자세한 내용은 [이 GitHub 이슈](https://github.com/aspnet/AspNetCore/issues/13476)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8891-218">For more information, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8891-219">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e8891-219">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
