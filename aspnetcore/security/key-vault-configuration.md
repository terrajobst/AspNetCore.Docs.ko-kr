---
title: ASP.NET Core의 Azure Key Vault 구성 공급자
author: guardrex
description: Azure Key Vault 구성 공급자를 사용 하 여 런타임에 로드 된 이름-값 쌍을 사용 하 여 앱을 구성 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2019
uid: security/key-vault-configuration
ms.openlocfilehash: acc3a77cdeb3ba73d8467d465128106e461efa7c
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034325"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="cfb75-103">ASP.NET Core의 Azure Key Vault 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="cfb75-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="cfb75-104">By [Luke Latham](https://github.com/guardrex) 및 [Andrew Stanton-간호사](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="cfb75-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="cfb75-105">이 문서에서는 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 사용 하 여 Azure Key Vault 암호에서 앱 구성 값을 로드 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="cfb75-106">Azure Key Vault는 앱 및 서비스에서 사용 하는 암호화 키 및 암호를 보호 하는 데 도움이 되는 클라우드 기반 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="cfb75-107">ASP.NET Core 앱과 함께 Azure Key Vault를 사용 하는 일반적인 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="cfb75-108">중요 한 구성 데이터에 대 한 액세스 제어</span><span class="sxs-lookup"><span data-stu-id="cfb75-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="cfb75-109">구성 데이터를 저장할 때 FIPS 140-2 수준 2 유효성 검사 된 하드웨어 보안 모듈 (HSM)에 대 한 요구 사항을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="cfb75-110">이 시나리오는 ASP.NET Core 2.1 이상을 대상으로 하는 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-110">This scenario is available for apps that target ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="cfb75-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cfb75-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="cfb75-112">패키지</span><span class="sxs-lookup"><span data-stu-id="cfb75-112">Packages</span></span>

<span data-ttu-id="cfb75-113">Azure Key Vault 구성 공급자를 사용 하려면 패키지 참조를 [Microsoft 확장명이. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 패키지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-113">To use the Azure Key Vault Configuration Provider, add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

<span data-ttu-id="cfb75-114">[Azure 리소스에 대 한 관리 되는 id](/azure/active-directory/managed-identities-azure-resources/overview) 시나리오를 채택 하려면 패키지 참조를 [Microsoft. Azure. appauthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) 패키지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-114">To adopt the [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) scenario, add a package reference to the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) package.</span></span>

> [!NOTE]
> <span data-ttu-id="cfb75-115">이 문서를 작성할 당시에는 안정적인 최신 릴리스의 `Microsoft.Azure.Services.AppAuthentication` 버전 `1.0.3`에서 [시스템 할당 관리 id](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-115">At the time of writing, the latest stable release of `Microsoft.Azure.Services.AppAuthentication`, version `1.0.3`, provides support for [system-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work).</span></span> <span data-ttu-id="cfb75-116">*사용자 할당 관리 id* 에 대 한 지원은 `1.2.0-preview2` 패키지에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-116">Support for *user-assigned managed identities* is available in the `1.2.0-preview2` package.</span></span> <span data-ttu-id="cfb75-117">이 항목에서는 시스템 관리 id를 사용 하는 방법을 보여 줍니다. 제공 된 샘플 앱은 `Microsoft.Azure.Services.AppAuthentication` 패키지의 버전 `1.0.3`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-117">This topic demonstrates the use of system-managed identities, and the provided sample app uses version `1.0.3` of the `Microsoft.Azure.Services.AppAuthentication` package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="cfb75-118">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="cfb75-118">Sample app</span></span>

<span data-ttu-id="cfb75-119">샘플 앱은 *Program.cs* 파일의 맨 위에 있는 `#define` 문에 의해 결정 된 두 가지 모드 중 하나에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-119">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="cfb75-120">`Certificate` &ndash;에서는 Azure Key Vault 클라이언트 ID 및 x.509 인증서를 사용 하 여 Azure Key Vault에 저장 된 암호에 액세스 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-120">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="cfb75-121">이 버전의 샘플은 Azure App Service 또는 ASP.NET Core 앱을 제공할 수 있는 호스트에 배포 된 모든 위치에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-121">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="cfb75-122">`Managed` &ndash; [azure 리소스에 관리 되는 id](/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하 여 앱의 코드 또는 구성에 저장 된 자격 증명 없이 azure AD 인증을 사용 하 여 Azure Key Vault 하도록 앱을 인증 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-122">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="cfb75-123">관리 id를 사용 하 여 인증 하는 경우 Azure AD 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-123">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="cfb75-124">샘플의 `Managed` 버전은 Azure에 배포 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-124">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="cfb75-125">[Azure 리소스에 대 한 관리 되는 Id 사용](#use-managed-identities-for-azure-resources) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-125">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="cfb75-126">전처리기 지시문 (`#define`)을 사용 하 여 샘플 앱을 구성 하는 방법에 대 한 자세한 내용은 <xref:index#preprocessor-directives-in-sample-code>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cfb75-126">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="cfb75-127">개발 환경의 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="cfb75-127">Secret storage in the Development environment</span></span>

<span data-ttu-id="cfb75-128">[암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 암호를 로컬로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-128">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="cfb75-129">개발 환경에서 샘플 앱이 로컬 컴퓨터에서 실행 되 면 비밀이 로컬 암호 관리자 저장소에서 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-129">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="cfb75-130">암호 관리자 도구를 사용 하려면 앱의 프로젝트 파일에 `<UserSecretsId>` 속성이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-130">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="cfb75-131">속성 값 (`{GUID}`)을 고유한 GUID로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-131">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="cfb75-132">비밀은 이름-값 쌍으로 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-132">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="cfb75-133">계층 값 (구성 섹션) [ASP.NET Core 구성](xref:fundamentals/configuration/index) 키 이름에서 구분 기호로 `:` (콜론)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-133">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="cfb75-134">암호 관리자는 프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root)에 열린 명령 셸에서 사용 됩니다. 여기서 `{SECRET NAME}`는 이름이 고 `{SECRET VALUE}`는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-134">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="cfb75-135">프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root) 에서 명령 셸에서 다음 명령을 실행 하 여 샘플 앱에 대 한 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-135">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="cfb75-136">이러한 암호가 Azure Key Vault 섹션을 사용 하 여 [프로덕션 환경의 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 Azure Key Vault에 저장 되는 경우 `_dev` 접미사가 `_prod`로 변경 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-136">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="cfb75-137">접미사는 구성 값의 원본을 나타내는 응용 프로그램의 출력에 표시 되는 시각적 표시를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-137">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="cfb75-138">Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장</span><span class="sxs-lookup"><span data-stu-id="cfb75-138">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="cfb75-139">[빠른 시작: Azure CLI를 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](/azure/key-vault/quick-create-cli) 항목은 샘플 앱에서 사용 하는 Azure Key Vault를 만들고 암호를 저장 하기 위해 여기에 요약 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-139">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="cfb75-140">자세한 내용은 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cfb75-140">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="cfb75-141">[Azure Portal](https://portal.azure.com/)에서 다음 방법 중 하나를 사용 하 여 Azure Cloud shell을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-141">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="cfb75-142">코드 블록의 오른쪽 위 모퉁이에서 **시도** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-142">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="cfb75-143">텍스트 상자에 "Azure CLI" 검색 문자열을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-143">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="cfb75-144">**Cloud Shell 시작** 단추를 사용 하 여 브라우저에서 Cloud Shell를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-144">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="cfb75-145">Azure Portal의 오른쪽 위 모퉁이에 있는 메뉴에서 **Cloud Shell** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-145">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="cfb75-146">자세한 내용은 [Azure 명령줄 인터페이스 (CLI)](/cli/azure/) 및 [Azure Cloud Shell 개요](/azure/cloud-shell/overview)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cfb75-146">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="cfb75-147">아직 인증 하지 않은 경우 `az login` 명령을 사용 하 여 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-147">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="cfb75-148">다음 명령을 사용 하 여 리소스 그룹을 만듭니다. 여기서 `{RESOURCE GROUP NAME}`는 새 리소스 그룹의 리소스 그룹 이름이 고 `{LOCATION}`은 Azure 지역 (데이터 센터)입니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-148">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="cfb75-149">다음 명령을 사용 하 여 리소스 그룹에 키 자격 증명 모음을 만듭니다. 여기서 `{KEY VAULT NAME}`는 새 키 자격 증명 모음에 대 한 이름이 고 `{LOCATION}`은 Azure 지역 (데이터 센터)입니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-149">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="cfb75-150">키 자격 증명 모음에서 암호를 이름-값 쌍으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-150">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="cfb75-151">Azure Key Vault 비밀 이름은 영숫자 문자와 대시로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-151">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="cfb75-152">계층 값 (구성 섹션)은 `--` (대시 두 개)를 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-152">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="cfb75-153">일반적으로 [ASP.NET Core 구성](xref:fundamentals/configuration/index)의 하위 키에서 섹션을 구분 하는 데 사용 되는 콜론은 key vault 암호 이름에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-153">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="cfb75-154">따라서 비밀을 앱의 구성으로 로드할 때 콜론에 대해 두 개의 대시를 사용 하 고 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-154">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="cfb75-155">다음 암호는 샘플 앱에서 사용 하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-155">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="cfb75-156">값에는 사용자 암호에서 개발 환경에 로드 된 `_dev` 접미사 값과 구분 하는 `_prod` 접미사가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-156">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="cfb75-157">`{KEY VAULT NAME}`을 이전 단계에서 만든 key vault의 이름으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-157">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="cfb75-158">Azure에서 호스트 되지 않는 앱에 응용 프로그램 ID 및 x.509 인증서 사용</span><span class="sxs-lookup"><span data-stu-id="cfb75-158">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="cfb75-159">**앱이 azure 외부에서 호스팅될 때**Azure Active Directory 응용 프로그램 ID 및 x.509 인증서를 사용 하 여 Key Vault에 인증 하도록 azure AD, Azure Key Vault 및 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-159">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="cfb75-160">자세한 내용은 [키, 암호 및 인증서 정보](/azure/key-vault/about-keys-secrets-and-certificates)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cfb75-160">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="cfb75-161">Azure에서 호스트 되는 앱에는 응용 프로그램 ID 및 x.509 인증서를 사용할 수 있지만 azure에서 앱을 호스팅할 때 [azure 리소스에 관리 되는 id](#use-managed-identities-for-azure-resources) 를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-161">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="cfb75-162">관리 되는 id는 응용 프로그램 또는 개발 환경에서 인증서를 저장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-162">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="cfb75-163">*Program.cs* 파일의 맨 위에 있는 `#define` 문이 `Certificate`로 설정 된 경우 샘플 앱은 응용 프로그램 ID 및 x.509 인증서를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-163">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="cfb75-164">PKCS # 12 archive ( *.pfx*) 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-164">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="cfb75-165">인증서를 만들기 위한 옵션에는 Windows 및 [OpenSSL](https://www.openssl.org/) [의 makecert.exe](/windows/desktop/seccrypto/makecert) 가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-165">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="cfb75-166">현재 사용자의 개인 인증서 저장소에 인증서를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-166">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="cfb75-167">키를 내보낼 수 있는 것으로 표시는 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-167">Marking the key as exportable is optional.</span></span> <span data-ttu-id="cfb75-168">이 프로세스의 뒷부분에서 사용 되는 인증서의 지문을 적어둡니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-168">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="cfb75-169">PKCS # 12 archive ( *.pfx*) 인증서를 DER로 인코딩된 인증서 ( *.cer*)로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-169">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="cfb75-170">Azure AD (**앱 등록**)에 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-170">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="cfb75-171">DER로 인코딩된 인증서 ( *.cer*)를 Azure AD에 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-171">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="cfb75-172">Azure AD에서 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-172">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="cfb75-173">**인증서 & 암호**로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-173">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="cfb75-174">**인증서 업로드** 를 선택 하 여 공개 키가 포함 된 인증서를 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-174">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="cfb75-175">*.Cer*, *pem*또는 *.crt* 인증서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-175">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="cfb75-176">앱의 *appsettings* 파일에 키 자격 증명 모음 이름, 응용 프로그램 ID 및 인증서 지문을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-176">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="cfb75-177">Azure Portal에서 **키 자격 증명 모음** 으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-177">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="cfb75-178">[Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 만든 key vault를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-178">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="cfb75-179">**액세스 정책**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-179">Select **Access policies**.</span></span>
1. <span data-ttu-id="cfb75-180">**새로 추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-180">Select **Add new**.</span></span>
1. <span data-ttu-id="cfb75-181">**보안 주체 선택** 을 선택 하 고 이름으로 등록 된 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="cfb75-182">**선택** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="cfb75-183">**비밀 권한을** 열고 **Get** 및 **List** 권한을 사용 하 여 앱을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-183">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="cfb75-184">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-184">Select **OK**.</span></span>
1. <span data-ttu-id="cfb75-185">**저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-185">Select **Save**.</span></span>
1. <span data-ttu-id="cfb75-186">앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-186">Deploy the app.</span></span>

<span data-ttu-id="cfb75-187">`Certificate` 샘플 앱은 암호 이름과 같은 이름으로 `IConfigurationRoot`에서 구성 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-187">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="cfb75-188">비 계층 구조 값: `SecretName`의 값은 `config["SecretName"]`를 사용 하 여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-188">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="cfb75-189">계층적 값 (섹션): `:` (콜론) 표기법 또는 `GetSection` 확장 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-189">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="cfb75-190">다음 방법 중 하나를 사용 하 여 구성 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-190">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="cfb75-191">X.509 인증서는 OS를 통해 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-191">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="cfb75-192">응용 프로그램은 *appsettings* 파일에서 제공 하는 값을 사용 하 여 `AddAzureKeyVault`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-192">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="cfb75-193">예제 값:</span><span class="sxs-lookup"><span data-stu-id="cfb75-193">Example values:</span></span>

* <span data-ttu-id="cfb75-194">키 자격 증명 모음 이름: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="cfb75-194">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="cfb75-195">응용 프로그램 ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="cfb75-195">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="cfb75-196">인증서 지문: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="cfb75-196">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="cfb75-197">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cfb75-197">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="cfb75-198">앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-198">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="cfb75-199">개발 환경에서 비밀 값은 `_dev` 접미사로 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-199">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="cfb75-200">프로덕션 환경에서 값은 `_prod` 접미사로 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-200">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="cfb75-201">Azure 리소스에 관리 되는 id 사용</span><span class="sxs-lookup"><span data-stu-id="cfb75-201">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="cfb75-202">**Azure에 배포 된 앱** 은 azure [리소스에 대 한 관리 id](/azure/active-directory/managed-identities-azure-resources/overview)를 활용할 수 있습니다 .이를 통해 앱은 자격 증명 없이 azure AD 인증을 사용 하 여 Azure Key Vault 인증할 수 있습니다 (응용 프로그램 ID 및 암호/클라이언트 암호). 앱에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-202">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="cfb75-203">*Program.cs* 파일의 맨 위에 있는 `#define` 문이 `Managed`으로 설정 된 경우 샘플 앱은 Azure 리소스에 관리 되는 id를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-203">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="cfb75-204">앱의 *appsettings json* 파일에 자격 증명 모음 이름을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-204">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="cfb75-205">샘플 앱에는 `Managed` 버전으로 설정 된 경우 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 않으므로 이러한 구성 항목을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-205">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="cfb75-206">앱이 Azure에 배포 되 고 Azure는 *appsettings* 파일에 저장 된 자격 증명 모음 이름을 사용 하 여 Azure Key Vault에만 액세스 하도록 앱을 인증 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-206">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="cfb75-207">Azure App Service에 샘플 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-207">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="cfb75-208">Azure App Service에 배포 된 앱은 서비스를 만들 때 Azure AD에 자동으로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-208">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="cfb75-209">다음 명령에서 사용할 개체 ID를 배포에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-209">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="cfb75-210">개체 ID는 App Service의 **id** 패널에 Azure Portal 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-210">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="cfb75-211">Azure CLI와 앱의 개체 ID를 사용 하 여 앱에 `list` 및 `get` 키 자격 증명 모음에 액세스할 수 있는 권한을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-211">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azure-cli
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="cfb75-212">Azure CLI, PowerShell 또는 Azure Portal를 사용 하 여 **앱을 다시 시작** 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-212">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="cfb75-213">샘플 앱:</span><span class="sxs-lookup"><span data-stu-id="cfb75-213">The sample app:</span></span>

* <span data-ttu-id="cfb75-214">연결 문자열 없이 `AzureServiceTokenProvider` 클래스의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-214">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="cfb75-215">연결 문자열이 제공 되지 않으면 공급자는 Azure 리소스에 대 한 관리 되는 id에서 액세스 토큰을 가져오려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-215">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="cfb75-216">새 `KeyVaultClient` `AzureServiceTokenProvider` 인스턴스 토큰 콜백을 사용 하 여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-216">A new `KeyVaultClient` is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="cfb75-217">`KeyVaultClient` 인스턴스는 모든 암호 값을 로드 하 고 키 이름에서 이중 대시 (`--`)를 콜론 (`:`)으로 바꾸는 `IKeyVaultSecretManager`의 기본 구현과 함께 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-217">The `KeyVaultClient` instance is used with a default implementation of `IKeyVaultSecretManager` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="cfb75-218">키 자격 증명 모음 이름 예제 값: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="cfb75-218">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="cfb75-219">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cfb75-219">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="cfb75-220">앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-220">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="cfb75-221">개발 환경에서 비밀 값은 사용자 비밀에서 제공 되기 때문에 `_dev` 접미사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-221">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="cfb75-222">프로덕션 환경에서 값은 Azure Key Vault에서 제공 되기 때문에 `_prod` 접미사로 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-222">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="cfb75-223">`Access denied` 오류가 표시 되 면 앱이 Azure AD에 등록 되 고 키 자격 증명 모음에 대 한 액세스 권한을 제공 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-223">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="cfb75-224">Azure에서 서비스를 다시 시작 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-224">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="cfb75-225">관리 되는 id 및 Azure DevOps 파이프라인에서 공급자를 사용 하는 방법에 대 한 자세한 내용은 [관리 서비스 id를 사용 하 여 VM에 대 한 Azure Resource Manager 서비스 연결 만들기](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cfb75-225">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="cfb75-226">키 이름 접두사 사용</span><span class="sxs-lookup"><span data-stu-id="cfb75-226">Use a key name prefix</span></span>

<span data-ttu-id="cfb75-227">`AddAzureKeyVault`은 `IKeyVaultSecretManager`의 구현을 허용 하는 오버 로드를 제공 하며,이를 통해 키 자격 증명 모음 암호를 구성 키로 변환 하는 방법을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-227">`AddAzureKeyVault` provides an overload that accepts an implementation of `IKeyVaultSecretManager`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="cfb75-228">예를 들어, 인터페이스를 구현 하 여 앱 시작 시 제공 하는 접두사 값에 따라 비밀 값을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-228">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="cfb75-229">예를 들어 앱의 버전에 따라 비밀을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-229">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="cfb75-230">키 자격 증명 모음 비밀에 대 한 접두사를 사용 하 여 여러 앱에 대 한 비밀을 동일한 주요 자격 증명 모음에 저장 하거나 환경 비밀 (예: *개발* 및 *프로덕션* 비밀)을 동일한 자격 증명 모음에 저장 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="cfb75-230">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="cfb75-231">앱과 개발/프로덕션 환경 마다 별도의 키 자격 증명 모음을 사용 하 여 가장 높은 수준의 보안을 위해 앱 환경을 격리 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-231">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="cfb75-232">다음 예제에서는 키 자격 증명 모음 (및 개발 환경에 대 한 암호 관리자 도구 사용)에서 `5000-AppSecret`에 대 한 암호를 설정 합니다 (기간은 key vault 암호 이름에 사용할 수 없음).</span><span class="sxs-lookup"><span data-stu-id="cfb75-232">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="cfb75-233">이 암호는 앱의 버전 5.0.0.0 앱 암호를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-233">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="cfb75-234">5\.1.0.0 앱의 다른 버전에 대해 암호는 키 자격 증명 모음에 추가 되 고 (암호 관리자 도구 사용) `5100-AppSecret`에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-234">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="cfb75-235">각 응용 프로그램 버전은 해당 버전의 보안 값을 `AppSecret` 구성으로 로드 하 고, 암호를 로드할 때 버전을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-235">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="cfb75-236">`AddAzureKeyVault`는 사용자 지정 `IKeyVaultSecretManager`를 사용 하 여 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-236">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager`:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?highlight=30-34)]

<span data-ttu-id="cfb75-237">`IKeyVaultSecretManager` 구현은 비밀의 버전 접두사에 반응 하 여 구성에 적절 한 비밀을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-237">The `IKeyVaultSecretManager` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

<span data-ttu-id="cfb75-238">`Load` 메서드는 버전 접두사가 있는 공급자를 찾기 위해 자격 증명 모음 암호를 반복 하는 공급자 알고리즘에 의해 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-238">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="cfb75-239">`Load`를 사용 하 여 버전 접두사를 발견 하면 알고리즘은 `GetKey` 메서드를 사용 하 여 비밀 이름의 구성 이름을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-239">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="cfb75-240">암호 이름에서 버전 접두사를 제거 하 고 앱의 구성 이름-값 쌍으로 로드 하기 위한 비밀 이름의 나머지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-240">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="cfb75-241">이 방법이 구현 되는 경우:</span><span class="sxs-lookup"><span data-stu-id="cfb75-241">When this approach is implemented:</span></span>

1. <span data-ttu-id="cfb75-242">앱의 프로젝트 파일에 지정 된 앱 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-242">The app's version specified in the app's project file.</span></span> <span data-ttu-id="cfb75-243">다음 예제에서는 앱의 버전이 `5.0.0.0`로 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-243">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="cfb75-244">응용 프로그램의 프로젝트 파일에 `<UserSecretsId>` 속성이 있는지 확인 합니다. 여기서 `{GUID}`은 사용자가 제공한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-244">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="cfb75-245">[암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 다음 암호를 로컬로 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-245">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="cfb75-246">비밀은 다음 Azure CLI 명령을 사용 하 여 Azure Key Vault에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-246">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="cfb75-247">앱이 실행 되 면 키 자격 증명 모음 비밀이 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-247">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="cfb75-248">`5000-AppSecret`에 대 한 문자열 비밀이 응용 프로그램의 프로젝트 파일 (`5.0.0.0`)에 지정 된 앱 버전과 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-248">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="cfb75-249">`5000` (대시 포함) 버전이 키 이름에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-249">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="cfb75-250">앱 전체에서 키 `AppSecret` 사용 하 여 구성을 읽으면 비밀 값이 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-250">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="cfb75-251">응용 프로그램의 버전이 프로젝트 파일에서 `5.1.0.0` 변경 되 고 앱이 다시 실행 되는 경우 반환 되는 암호 값은 개발 환경에서 `5.1.0.0_secret_value_dev` 되 고 프로덕션 환경에서 `5.1.0.0_secret_value_prod` 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-251">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="cfb75-252">사용자 고유의 `KeyVaultClient` 구현을 제공 하 여 `AddAzureKeyVault` 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-252">You can also provide your own `KeyVaultClient` implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="cfb75-253">사용자 지정 클라이언트는 앱에서 클라이언트의 단일 인스턴스를 공유 하도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-253">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="cfb75-254">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="cfb75-254">Bind an array to a class</span></span>

<span data-ttu-id="cfb75-255">공급자는 POCO 배열에 바인딩하기 위해 구성 값을 배열로 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-255">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="cfb75-256">키에 콜론 (`:`) 구분 기호가 포함 될 수 있도록 하는 구성 소스에서 읽을 때 숫자 키 세그먼트를 사용 하 여 배열을 구성 하는 키를 구분 합니다 (`:0:`, `:1:`, ...</span><span class="sxs-lookup"><span data-stu-id="cfb75-256">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="cfb75-257">`:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="cfb75-257">`:{n}:`).</span></span> <span data-ttu-id="cfb75-258">자세한 내용은 [구성: 클래스에 배열 바인딩](xref:fundamentals/configuration/index#bind-an-array-to-a-class)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cfb75-258">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="cfb75-259">Azure Key Vault 키는 콜론을 구분 기호로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-259">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="cfb75-260">이 항목에서 설명 하는 접근 방식에서는 이중 대시 (`--`)를 계층적 값 (섹션) 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-260">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="cfb75-261">배열 키는 이중 대시 및 숫자 키 세그먼트 (`--0--`, `--1--`, &hellip; `--{n}--`)를 사용 하 여 Azure Key Vault에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-261">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="cfb75-262">JSON 파일에서 제공 하는 다음과 같은 [Serilog](https://serilog.net/) 로깅 공급자 구성을 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-262">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="cfb75-263">로깅 출력의 대상을 설명 하는 두 개의 Serilog *싱크*를 반영 하는 두 개의 개체 리터럴이 `WriteTo` 배열에 정의 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-263">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="cfb75-264">위의 JSON 파일에 표시 된 구성은 이중 대시 (`--`) 표기법 및 숫자 세그먼트를 사용 하 Azure Key Vault에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-264">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="cfb75-265">Key</span><span class="sxs-lookup"><span data-stu-id="cfb75-265">Key</span></span> | <span data-ttu-id="cfb75-266">값</span><span class="sxs-lookup"><span data-stu-id="cfb75-266">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="cfb75-267">비밀 다시 로드</span><span class="sxs-lookup"><span data-stu-id="cfb75-267">Reload secrets</span></span>

<span data-ttu-id="cfb75-268">암호는 `IConfigurationRoot.Reload()`가 호출 될 때까지 캐시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-268">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="cfb75-269">키 자격 증명 모음에서 만료, 사용 안 함 및 업데이트 된 비밀은 `Reload` 실행 될 때까지 앱에서 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-269">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="cfb75-270">비활성화 및 만료 된 비밀</span><span class="sxs-lookup"><span data-stu-id="cfb75-270">Disabled and expired secrets</span></span>

<span data-ttu-id="cfb75-271">비활성화 및 만료 된 비밀은 런타임에 `KeyVaultClientException`을 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-271">Disabled and expired secrets throw a `KeyVaultClientException` at runtime.</span></span> <span data-ttu-id="cfb75-272">앱이 throw 되지 않도록 하려면 다른 구성 공급자를 사용 하 여 구성을 제공 하거나 사용 하지 않거나 만료 된 암호를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-272">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="cfb75-273">문제 해결</span><span class="sxs-lookup"><span data-stu-id="cfb75-273">Troubleshoot</span></span>

<span data-ttu-id="cfb75-274">앱이 공급자를 사용 하 여 구성을 로드 하지 못하면 [ASP.NET Core 로깅 인프라](xref:fundamentals/logging/index)에 오류 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-274">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="cfb75-275">다음 조건에서는 구성을 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-275">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="cfb75-276">앱 또는 인증서가 Azure Active Directory에서 올바르게 구성 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-276">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="cfb75-277">키 자격 증명 모음이 Azure Key Vault에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-277">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="cfb75-278">앱에 키 자격 증명 모음에 액세스할 수 있는 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-278">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="cfb75-279">액세스 정책에는 `Get` 및 `List` 권한이 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-279">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="cfb75-280">키 자격 증명 모음에서 구성 데이터 (이름-값 쌍)의 이름을 잘못 지정 했거나, 누락 되었거나, 사용 하지 않거나, 만료 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-280">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="cfb75-281">앱에 잘못 된 키 자격 증명 모음 이름 (`KeyVaultName`), Azure AD 응용 프로그램 Id (`AzureADApplicationId`) 또는 Azure AD 인증서 지문 (`AzureADCertThumbprint`)이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-281">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="cfb75-282">로드 하려는 값에 대 한 앱의 구성 키 (이름)가 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="cfb75-282">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cfb75-283">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cfb75-283">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="cfb75-284">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="cfb75-284">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="cfb75-285">Microsoft Azure: Key Vault 설명서</span><span class="sxs-lookup"><span data-stu-id="cfb75-285">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="cfb75-286">Azure Key Vault 용 HSM 보호 키를 생성 하 고 전송 하는 방법</span><span class="sxs-lookup"><span data-stu-id="cfb75-286">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="cfb75-287">KeyVaultClient 클래스</span><span class="sxs-lookup"><span data-stu-id="cfb75-287">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="cfb75-288">빠른 시작: .NET 웹 앱을 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색</span><span class="sxs-lookup"><span data-stu-id="cfb75-288">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="cfb75-289">자습서: .NET에서 Azure Windows 가상 머신과 Azure Key Vault를 사용 하는 방법</span><span class="sxs-lookup"><span data-stu-id="cfb75-289">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
