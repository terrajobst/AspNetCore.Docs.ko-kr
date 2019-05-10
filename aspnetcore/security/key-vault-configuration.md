---
title: ASP.NET Core에서 azure Key Vault 구성 공급자
author: guardrex
description: Azure 키 자격 증명 모음 구성 공급자를 사용 하 여 런타임에 로드 하는 이름-값 쌍을 사용 하 여 앱을 구성 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: security/key-vault-configuration
ms.openlocfilehash: 45eca05b5eb41815924ca48f60c3b00046c6bdaf
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894990"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="be9ad-103">ASP.NET Core에서 azure Key Vault 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="be9ad-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="be9ad-104">하 여 [Luke Latham](https://github.com/guardrex) 고 [Andrew Stanton-nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="be9ad-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="be9ad-105">이 문서를 사용 하는 방법에 설명 합니다 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 Azure Key Vault 암호에서 앱 구성 값을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="be9ad-106">Azure Key Vault는 암호화 키 및 앱 및 서비스에서 사용 하는 비밀을 보호 하는 데 도움이 되는 클라우드 기반 서비스.</span><span class="sxs-lookup"><span data-stu-id="be9ad-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="be9ad-107">ASP.NET Core 앱을 사용 하 여 Azure Key Vault를 사용 하는 것에 대 한 일반적인 시나리오에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="be9ad-108">중요 한 구성 데이터에 대 한 액세스를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="be9ad-109">FIPS 요구 사항을 충족 140-2 Level 2 유효성 검사가 하드웨어 보안 모듈 (HSM의) 구성 데이터를 저장할 때.</span><span class="sxs-lookup"><span data-stu-id="be9ad-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="be9ad-110">이 시나리오는 ASP.NET Core 2.1을 대상으로 하는 앱에 사용할 수 있는 이상.</span><span class="sxs-lookup"><span data-stu-id="be9ad-110">This scenario is available for apps that target ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="be9ad-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="be9ad-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="be9ad-112">패키지</span><span class="sxs-lookup"><span data-stu-id="be9ad-112">Packages</span></span>

<span data-ttu-id="be9ad-113">Azure 키 자격 증명 모음 구성 공급자를 사용 하려면 패키지 참조를 추가 합니다 [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 패키지 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-113">To use the Azure Key Vault Configuration Provider, add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

<span data-ttu-id="be9ad-114">채택 하는 [Azure 리소스에 대 한 id 관리](/azure/active-directory/managed-identities-azure-resources/overview) 시나리오에 대 한 패키지 참조를 추가 합니다 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) 패키지.</span><span class="sxs-lookup"><span data-stu-id="be9ad-114">To adopt the [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) scenario, add a package reference to the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) package.</span></span>

> [!NOTE]
> <span data-ttu-id="be9ad-115">안정적인 최신 릴리스를 작성할 당시 `Microsoft.Azure.Services.AppAuthentication`, 버전 `1.0.3`에 대 한 지원을 제공 [identities 관리 되는 시스템 할당](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka)합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-115">At the time of writing, the latest stable release of `Microsoft.Azure.Services.AppAuthentication`, version `1.0.3`, provides support for [system-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).</span></span> <span data-ttu-id="be9ad-116">에 대 한 지원 *identities 관리 되는 사용자 할당* 에서 사용할 수는 `1.2.0-preview2` 패키지 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-116">Support for *user-assigned managed identities* is available in the `1.2.0-preview2` package.</span></span> <span data-ttu-id="be9ad-117">이 항목에서는 시스템 관리 id 사용을 보여 줍니다. 및 버전을 사용 하 여 제공된 된 샘플 앱 `1.0.3` 의 `Microsoft.Azure.Services.AppAuthentication` 패키지 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-117">This topic demonstrates the use of system-managed identities, and the provided sample app uses version `1.0.3` of the `Microsoft.Azure.Services.AppAuthentication` package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="be9ad-118">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="be9ad-118">Sample app</span></span>

<span data-ttu-id="be9ad-119">샘플 앱에 의해 결정 되는 두 가지 모드 중 하나에서 실행 되는 `#define` 맨 위에 있는 문을 합니다 *Program.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="be9ad-119">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="be9ad-120">`Certificate` &ndash; Azure Key Vault에 저장 된 액세스 비밀을 Azure Key Vault 클라이언트 ID 및 X.509 인증서의 사용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-120">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="be9ad-121">Azure App Service 또는 ASP.NET Core 앱을 처리할 수 있는 모든 호스트에 배포 된 모든 위치에서이 버전의 샘플을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-121">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="be9ad-122">`Managed` &ndash; 사용 하는 방법을 보여 줍니다 [Azure 리소스에 대 한 id 관리](/azure/active-directory/managed-identities-azure-resources/overview) 앱의 코드 또는 구성에 저장 된 자격 증명 없이 Azure AD 인증을 사용 하 여 Azure Key Vault에 앱을 인증할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-122">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="be9ad-123">관리 되는 id를 사용 하 여 인증을 하는 경우 Azure AD 응용 프로그램 ID 및 암호 (클라이언트 암호)를 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-123">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="be9ad-124">`Managed` 버전 샘플의 Azure에 배포 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-124">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="be9ad-125">지침에 따라 합니다 [관리 되는 id를 사용 하 여 Azure 리소스에 대 한](#use-managed-identities-for-azure-resources) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-125">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="be9ad-126">전처리기 지시문을 사용 하 여 샘플 앱을 구성 하는 방법에 대 한 자세한 내용은 (`#define`)를 참조 하세요 <xref:index#preprocessor-directives-in-sample-code>합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-126">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="be9ad-127">개발 환경에서 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="be9ad-127">Secret storage in the Development environment</span></span>

<span data-ttu-id="be9ad-128">로컬로 사용 하 여 암호를 설정 합니다 [암호 관리자 도구](xref:security/app-secrets)합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-128">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="be9ad-129">암호 로드는 샘플 응용 프로그램 개발 환경에서 로컬 컴퓨터에서 실행 되 면 로컬 관리자 암호 저장소에서.</span><span class="sxs-lookup"><span data-stu-id="be9ad-129">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="be9ad-130">암호 관리자 도구 필요는 `<UserSecretsId>` 앱의 프로젝트 파일의 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-130">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="be9ad-131">속성 값을 설정 (`{GUID}`) 모든 고유 GUID로:</span><span class="sxs-lookup"><span data-stu-id="be9ad-131">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="be9ad-132">비밀 이름-값 쌍으로 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-132">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="be9ad-133">계층 값 (구성 섹션)를 사용 하는 `:` (콜론)에서 구분 기호로 [ASP.NET Core 구성](xref:fundamentals/configuration/index) 키 이름을 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-133">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="be9ad-134">암호 관리자를 열어 프로젝트의 콘텐츠 루트를 명령 셸에서 여기서 `{SECRET NAME}` 이름인 및 `{SECRET VALUE}` 값:</span><span class="sxs-lookup"><span data-stu-id="be9ad-134">The Secret Manager is used from a command shell opened to the project's content root, where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="be9ad-135">샘플 앱에 대 한 암호를 설정 하려면 프로젝트의 콘텐츠 루트에서 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-135">Execute the following commands in a command shell from the project's content root to set the secrets for the sample app:</span></span>

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="be9ad-136">이러한 비밀은 Azure Key Vault에 저장 하는 경우는 [Azure Key Vault를 사용 하 여 프로덕션 환경에서 비밀 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 섹션인 합니다 `_dev` 접미사가으로 변경 되었을 `_prod`합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-136">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="be9ad-137">접미사는 앱의 출력의 구성 값의 원본을 나타내는 시각 표시를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-137">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="be9ad-138">Azure Key Vault를 사용 하 여 프로덕션 환경에서 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="be9ad-138">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="be9ad-139">제공한 지침을 [빠른 시작: 설정 및 Azure CLI를 사용 하 여 Azure Key Vault에서 비밀을 검색](/azure/key-vault/quick-create-cli) 항목은 여기에 요약 된 Azure Key Vault를 만들고 샘플 앱에서 사용 하는 비밀을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-139">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="be9ad-140">자세한 내용은 항목을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-140">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="be9ad-141">열기 Azure Cloud shell에서 다음 방법 중 하나를 사용 하 여 [Azure portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="be9ad-141">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="be9ad-142">선택 **시도** 코드 블록의 오른쪽 위 모퉁이에서.</span><span class="sxs-lookup"><span data-stu-id="be9ad-142">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="be9ad-143">텍스트 상자에 검색 문자열 "Azure CLI"를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-143">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="be9ad-144">Cloud Shell을 사용 하 여 브라우저에서 엽니다는 **Cloud Shell 시작** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-144">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="be9ad-145">선택 된 **Cloud Shell** Azure portal의 오른쪽 위 모서리에 있는 메뉴 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-145">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="be9ad-146">자세한 내용은 [Azure CLI (명령줄 인터페이스)](/cli/azure/) 하 고 [Azure Cloud Shell 개요](/azure/cloud-shell/overview)합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-146">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="be9ad-147">아직 인증 되지 경우 로그인 하 여 `az login` 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-147">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="be9ad-148">다음 명령을 사용 하 여 리소스 그룹을 만듭니다. 여기서 `{RESOURCE GROUP NAME}` 은 새 리소스 그룹에 대 한 리소스 그룹 이름 및 `{LOCATION}` 은 Azure 지역 (데이터 센터):</span><span class="sxs-lookup"><span data-stu-id="be9ad-148">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="be9ad-149">다음 명령 사용 하 여 리소스 그룹에 key vault 만들기 위치 `{KEY VAULT NAME}` 은 새 키 자격 증명 모음에 대 한 이름 및 `{LOCATION}` 은 Azure 지역 (데이터 센터):</span><span class="sxs-lookup"><span data-stu-id="be9ad-149">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="be9ad-150">이름-값 쌍으로 key vault에서 비밀을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-150">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="be9ad-151">Azure Key Vault 비밀 이름은 영숫자와 대시만 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-151">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="be9ad-152">계층 값 (구성 섹션) 사용 하 여 `--` (두 개의 대시) 구분 기호로 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-152">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="be9ad-153">일반적으로 하위 키에의 한 부분을 구분 하는 데 사용 되는 콜론 [ASP.NET Core 구성](xref:fundamentals/configuration/index), 키 자격 증명 모음 비밀 이름에 허용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-153">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="be9ad-154">따라서 두 개의 대시 사용 되며 암호를 앱의 구성에 로드 될 때 콜론으로 교체 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-154">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="be9ad-155">다음 암호를 샘플 앱과 함께 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-155">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="be9ad-156">값 포함을 `_prod` 에서 구별 하는 접미사는 `_dev` 접미사 사용자 암호에서 개발 환경에 로드 하는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-156">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="be9ad-157">대체 `{KEY VAULT NAME}` 이전 단계에서 만든 key vault의 이름:</span><span class="sxs-lookup"><span data-stu-id="be9ad-157">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-client-secret-for-non-azure-hosted-apps"></a><span data-ttu-id="be9ad-158">응용 프로그램 ID 및 클라이언트 암호를 사용 하 여 Azure 호스트 되는 앱에 대 한</span><span class="sxs-lookup"><span data-stu-id="be9ad-158">Use Application ID and Client Secret for non-Azure-hosted apps</span></span>

<span data-ttu-id="be9ad-159">Azure AD를 구성 하 고, Azure Active Directory 응용 프로그램 ID 및 X.509를 사용 하는 Azure Key Vault 및 앱 인증서를 key vault에 인증 **앱 Azure 외부에서 호스트 되는 경우**합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-159">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="be9ad-160">자세한 내용은 [키, 비밀 및 인증서에 대 한](/azure/key-vault/about-keys-secrets-and-certificates)합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-160">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="be9ad-161">응용 프로그램 ID 및 X.509 인증서를 사용 하는 Azure에서 호스트 되는 앱에 대 한 지원 되지만 사용 하는 것이 좋습니다 [Azure 리소스에 대 한 id 관리](#use-managed-identities-for-azure-resources) Azure에서 앱을 호스팅하는 경우.</span><span class="sxs-lookup"><span data-stu-id="be9ad-161">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="be9ad-162">관리 되는 id는 앱에서 또는 개발 환경에서 인증서를 저장할 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-162">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="be9ad-163">샘플 앱에서는 경우 응용 프로그램 ID 및 X.509 인증서를 `#define` 맨 위에 있는 문을 합니다 *Program.cs* 파일을 설정 `Certificate`합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-163">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="be9ad-164">Azure AD에 앱 등록 (**앱 등록**).</span><span class="sxs-lookup"><span data-stu-id="be9ad-164">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="be9ad-165">공개 키를 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-165">Upload the public key:</span></span>
   1. <span data-ttu-id="be9ad-166">Azure AD에서 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-166">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="be9ad-167">이동할 **설정을** > **키**합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-167">Navigate to **Settings** > **Keys**.</span></span>
   1. <span data-ttu-id="be9ad-168">선택 **공개 키 업로드** 공개 키를 포함 하는 인증서를 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-168">Select **Upload Public Key** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="be9ad-169">사용 하는 것 외에도 *.cer*를 *.pem*, 또는 *.crt* 인증서를 *.pfx* 인증서를 업로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-169">In addition to using a *.cer*, *.pem*, or *.crt* certificate, a *.pfx* certificate can be uploaded.</span></span>
1. <span data-ttu-id="be9ad-170">앱의 키 자격 증명 모음 이름 및 응용 프로그램 ID를 저장할 *appsettings.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-170">Store the key vault name and Application ID in the app's *appsettings.json* file.</span></span> <span data-ttu-id="be9ad-171">앱 또는 앱의 인증서 저장소에 루트 인증서를 배치&dagger;합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-171">Place the certificate at the root of the app or in the app's certificate store&dagger;.</span></span>
1. <span data-ttu-id="be9ad-172">이동할 **Key vault** Azure portal에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="be9ad-173">만든 키 자격 증명 모음을 선택 합니다 [Azure Key Vault를 사용 하 여 프로덕션 환경에서 비밀 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="be9ad-174">선택 **액세스 정책**합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="be9ad-175">선택 **새로 추가**합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-175">Select **Add new**.</span></span>
1. <span data-ttu-id="be9ad-176">선택 **보안 주체 선택** 이름으로 등록 된 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-176">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="be9ad-177">선택 된 **선택** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-177">Select the **Select** button.</span></span>
1. <span data-ttu-id="be9ad-178">열기 **비밀 권한** 응용 프로그램을 제공 하 고 **가져오기** 하 고 **목록** 권한.</span><span class="sxs-lookup"><span data-stu-id="be9ad-178">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="be9ad-179">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-179">Select **OK**.</span></span>
1. <span data-ttu-id="be9ad-180">**저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-180">Select **Save**.</span></span>
1. <span data-ttu-id="be9ad-181">앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-181">Deploy the app.</span></span>

<span data-ttu-id="be9ad-182">&dagger;샘플 앱에서 인증서 사용 되는 앱의 루트에서 실제 인증서 파일에서 직접 새 `X509Certificate2` 호출할 때 `AddAzureKeyVault`합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-182">&dagger;In the sample app, the certificate is consumed directly from the physical certificate file in the root of the app by creating a new `X509Certificate2` when calling `AddAzureKeyVault`.</span></span> <span data-ttu-id="be9ad-183">또 다른 방법은 OS가 인증서를 관리할 수 있도록 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-183">An alternative approach is to allow the OS to manage the certificate.</span></span> <span data-ttu-id="be9ad-184">자세한 내용은 참조는 [OS가 X.509 인증서를 관리할 수 있도록](#allow-the-os-to-manage-the-x509-certificate) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-184">For more information, see the [Allow the OS to manage the X.509 certificate](#allow-the-os-to-manage-the-x509-certificate) section.</span></span>

<span data-ttu-id="be9ad-185">합니다 `Certificate` 샘플 앱에서 해당 구성 값을 가져옵니다 `IConfigurationRoot` 비밀 이름으로 같은 이름의:</span><span class="sxs-lookup"><span data-stu-id="be9ad-185">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="be9ad-186">비 계층 값: 에 대 한 값 `SecretName` 사용 하 여 가져오는 `config["SecretName"]`합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-186">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="be9ad-187">계층 값 (섹션): 사용 하 여 `:` (콜론) 표기법 또는 `GetSection` 확장 메서드.</span><span class="sxs-lookup"><span data-stu-id="be9ad-187">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="be9ad-188">이러한 방법 중 하나를 사용 하 여 구성 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-188">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="be9ad-189">앱 호출 `AddAzureKeyVault` 에서 제공 하는 값을 *appsettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="be9ad-189">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=12-15)]

<span data-ttu-id="be9ad-190">예제 값:</span><span class="sxs-lookup"><span data-stu-id="be9ad-190">Example values:</span></span>

* <span data-ttu-id="be9ad-191">키 자격 증명 모음 이름: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="be9ad-191">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="be9ad-192">응용 프로그램 ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="be9ad-192">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>

<span data-ttu-id="be9ad-193">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="be9ad-193">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="be9ad-194">앱을 실행 하는 경우 웹 페이지 로드 비밀 값을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-194">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="be9ad-195">개발 환경에서 사용 하 여 비밀 값 로드는 `_dev` 접미사.</span><span class="sxs-lookup"><span data-stu-id="be9ad-195">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="be9ad-196">프로덕션 환경에서 값을 사용 하 여 로드 된 `_prod` 접미사.</span><span class="sxs-lookup"><span data-stu-id="be9ad-196">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="be9ad-197">Azure 리소스에 대 한 관리 되는 id를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-197">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="be9ad-198">**Azure에 배포 된 앱** 활용할 수 있습니다 [Azure 리소스에 대 한 id 관리](/azure/active-directory/managed-identities-azure-resources/overview), Azure Key Vault를 사용 하 여 인증 하도록 앱을 설정 하는 자격 증명 없이 Azure AD 인증을 사용 하 여이 있어 (응용 프로그램 ID 및 Password/Client 비밀) 앱에 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-198">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="be9ad-199">Azure 리소스에 대 한 관리 되는 id를 사용 하는 샘플 앱 경우는 `#define` 맨 위에 있는 문을 합니다 *Program.cs* 파일을 설정 `Managed`.</span><span class="sxs-lookup"><span data-stu-id="be9ad-199">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="be9ad-200">앱의 자격 증명 모음 이름 입력 *appsettings.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-200">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="be9ad-201">샘플 앱은 응용 프로그램 ID 및 암호 (클라이언트 암호)로 설정 된 경우 필요 하지 않습니다는 `Managed` 버전이 없으므로 해당 구성 항목을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-201">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="be9ad-202">앱이 azure에 배포 하 고 Azure 인증 앱이 Azure Key Vault에 저장 된 자격 증명 모음 이름을 통해서만 액세스할 수 합니다 *appsettings.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-202">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="be9ad-203">Azure App Service에 샘플 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-203">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="be9ad-204">Azure App Service에 배포 된 앱 서비스를 만들 때 Azure AD를 사용 하 여 자동으로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-204">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="be9ad-205">다음 명령을 사용 하 여 배포에서 개체 ID를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-205">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="be9ad-206">개체 ID를 Azure portal에 표시 되는 **Identity** App Service의 패널입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-206">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="be9ad-207">응용 프로그램을 Azure CLI 및 앱의 개체 ID를 사용 하 여 제공할 `list` 고 `get` key vault 액세스 권한:</span><span class="sxs-lookup"><span data-stu-id="be9ad-207">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="be9ad-208">**앱을 다시 시작** Azure CLI, PowerShell 또는 Azure portal을 사용 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-208">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="be9ad-209">샘플 앱:</span><span class="sxs-lookup"><span data-stu-id="be9ad-209">The sample app:</span></span>

* <span data-ttu-id="be9ad-210">인스턴스를 만듭니다는 `AzureServiceTokenProvider` 연결 문자열이 없는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-210">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="be9ad-211">연결 문자열을 제공 하지 않으면 공급자는 Azure 리소스에 대 한 관리 되는 id에서 액세스 토큰을 가져올 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-211">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="be9ad-212">새 `KeyVaultClient` 만들어집니다는 `AzureServiceTokenProvider` 인스턴스 토큰 콜백 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-212">A new `KeyVaultClient` is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="be9ad-213">합니다 `KeyVaultClient` 인스턴스의 기본 구현이 사용 됩니다 `IKeyVaultSecretManager` 모든 비밀 값을 로드 하 고 이중 대시를 대체 하는 (`--`) 콜론을 사용 하 여 (`:`) 키 이름에서입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-213">The `KeyVaultClient` instance is used with a default implementation of `IKeyVaultSecretManager` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="be9ad-214">앱을 실행 하는 경우 웹 페이지 로드 비밀 값을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-214">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="be9ad-215">비밀 값에 개발 환경에는 `_dev` 사용자 암호에서 제공 하는 접미사입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-215">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="be9ad-216">프로덕션 환경에서 값을 사용 하 여 로드 된 `_prod` Azure Key Vault에서 제공 하는 접미사입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-216">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="be9ad-217">표시 되 면는 `Access denied` 오류를 확인 하는 앱을 Azure AD에 등록 하 고 key vault에 대 한 액세스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-217">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="be9ad-218">Azure에서 서비스를 다시 시작 했습니다 하면 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-218">Confirm that you've restarted the service in Azure.</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="be9ad-219">키 이름 접두사를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="be9ad-219">Use a key name prefix</span></span>

<span data-ttu-id="be9ad-220">`AddAzureKeyVault` 구현의 받아들이는 오버 로드가 제공 `IKeyVaultSecretManager`, 구성 키에 변환 된 키 자격 증명 모음 비밀을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-220">`AddAzureKeyVault` provides an overload that accepts an implementation of `IKeyVaultSecretManager`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="be9ad-221">예를 들어, 앱 시작 시 제공한 접두사 값을 기반으로 하는 비밀 값을 로드 하는 인터페이스를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-221">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="be9ad-222">이렇게 하면 앱의 버전을 기반으로 하는 비밀 정보를 로드 하려면 예를 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-222">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="be9ad-223">동일한 키 자격 증명 모음에 여러 앱에 대 한 암호를 배치 하려면 또는 환경 비밀을 key vault 비밀의 접두사를 사용 하지 마세요 (예를 들어 *개발* 비교 *프로덕션* 비밀)를 동일한 자격 증명 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-223">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="be9ad-224">다른 앱 및 개발/프로덕션 환경 앱 환경을 가장 높은 수준의 보안 격리를 별도 키 자격 증명 모음 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-224">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="be9ad-225">다음 예제에서는 비밀 키에 설정 되며 자격 증명 모음 (및 암호 관리자 도구를 사용 하 여 개발 환경)에 대 한 `5000-AppSecret` (마침표는 키 자격 증명 모음 비밀 이름에 허용 되지 않음).</span><span class="sxs-lookup"><span data-stu-id="be9ad-225">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="be9ad-226">이 비밀을 5.0.0.0 버전의 앱에 대 한 앱 암호를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-226">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="be9ad-227">비밀 키에 추가 됩니다 5.1.0.0, 앱의 다른 버전에 대 한 자격 증명 모음 (및 암호 관리자 도구를 사용 하 여)에 대 한 `5100-AppSecret`합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-227">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="be9ad-228">각 앱 버전으로 해당 구성을 해당 버전의 비밀 값 로드 `AppSecret`, 비밀 로드 된 버전 해제 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-228">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="be9ad-229">`AddAzureKeyVault` 사용자 지정을 사용 하 여 라고 `IKeyVaultSecretManager`:</span><span class="sxs-lookup"><span data-stu-id="be9ad-229">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager`:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?name=snippet1&highlight=22)]

<span data-ttu-id="be9ad-230">키 자격 증명 모음 이름, 응용 프로그램 ID 및 암호 (클라이언트 암호)에 대 한 값을 제공 합니다 *appsettings.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="be9ad-230">The values for key vault name, Application ID, and Password (Client Secret) are provided by the *appsettings.json* file:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="be9ad-231">예제 값:</span><span class="sxs-lookup"><span data-stu-id="be9ad-231">Example values:</span></span>

* <span data-ttu-id="be9ad-232">키 자격 증명 모음 이름: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="be9ad-232">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="be9ad-233">응용 프로그램 ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="be9ad-233">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="be9ad-234">암호: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span><span class="sxs-lookup"><span data-stu-id="be9ad-234">Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span></span>

<span data-ttu-id="be9ad-235">`IKeyVaultSecretManager` 구현을 구성에 적절 한 암호를 로드 하는 비밀 버전 접두사에 반응 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-235">The `IKeyVaultSecretManager` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

<span data-ttu-id="be9ad-236">`Load` 을 찾거나 버전 접두사를 가진 자격 증명 모음 비밀을 통해 반복 하는 알고리즘 공급자에서 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-236">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="be9ad-237">버전 접두사를 사용 하 여 검색 되 면 `Load`, 알고리즘을 사용 하는 `GetKey` 비밀 이름의 구성 이름을 반환 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-237">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="be9ad-238">암호의 이름에서 버전 접두사를 제거 하 고 이름-값 쌍에 앱의 구성을 로드에 대 한 비밀 이름의 나머지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-238">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="be9ad-239">경우에이 이렇게 구현 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-239">When this approach is implemented:</span></span>

1. <span data-ttu-id="be9ad-240">앱의 프로젝트 파일에 지정 된 앱의 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-240">The app's version specified in the app's project file.</span></span> <span data-ttu-id="be9ad-241">다음 예제에서는 앱의 버전이로 설정 되어 `5.0.0.0`:</span><span class="sxs-lookup"><span data-stu-id="be9ad-241">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="be9ad-242">확인을 `<UserSecretsId>` 속성은 앱의 프로젝트 파일에 있는 위치 `{GUID}` 는 사용자가 제공한 GUID:</span><span class="sxs-lookup"><span data-stu-id="be9ad-242">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="be9ad-243">다음 비밀을 사용 하 여 로컬로 저장 합니다 [암호 관리자 도구](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="be9ad-243">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="be9ad-244">암호는 다음 Azure CLI 명령을 사용 하 여 Azure Key Vault에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-244">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="be9ad-245">앱을 실행 하는 경우 key vault 비밀 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-245">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="be9ad-246">에 대 한 문자열 암호 `5000-AppSecret` 앱의 프로젝트 파일에 지정 된 앱의 버전 일치 (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="be9ad-246">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="be9ad-247">버전 `5000` (대시로) 키 이름에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-247">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="be9ad-248">앱 키를 사용 하 여 구성을 읽는 동안 `AppSecret` 비밀 값을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-248">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="be9ad-249">프로젝트 파일에서 앱의 버전이 변경 된 경우 `5.1.0.0` 앱이 다시 실행 되 고은 비밀 값을 반환 `5.1.0.0_secret_value_dev` 개발 환경에서 고 `5.1.0.0_secret_value_prod` 프로덕션 환경에서.</span><span class="sxs-lookup"><span data-stu-id="be9ad-249">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="be9ad-250">제공할 수도 있습니다 고유한 `KeyVaultClient` 구현을 `AddAzureKeyVault`합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-250">You can also provide your own `KeyVaultClient` implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="be9ad-251">사용자 지정 클라이언트 앱에 클라이언트의 단일 인스턴스를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-251">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="allow-the-os-to-manage-the-x509-certificate"></a><span data-ttu-id="be9ad-252">X.509 인증서를 관리 하는 OS를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-252">Allow the OS to manage the X.509 certificate</span></span>

<span data-ttu-id="be9ad-253">OS에서 X.509 인증서를 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-253">The X.509 certificate can be managed by the OS.</span></span> <span data-ttu-id="be9ad-254">다음 예제에서는 합니다 `AddAzureKeyVault` 받아들이는 오버 로드는 `X509Certificate2` 컴퓨터의 현재 사용자 인증서 저장소를 구성 하 여 제공 된 인증서 지문:</span><span class="sxs-lookup"><span data-stu-id="be9ad-254">The following example uses the `AddAzureKeyVault` overload that accepts an `X509Certificate2` from the machine's current user certificate store and a certificate thumbprint supplied by configuration:</span></span>

```csharp
// using System.Linq;
// using System.Security.Cryptography.X509Certificates;
// using Microsoft.Extensions.Configuration;

WebHost.CreateDefaultBuilder(args)
    .ConfigureAppConfiguration((context, config) =>
    {
        if (context.HostingEnvironment.IsProduction())
        {
            var builtConfig = config.Build();

            using (var store = new X509Store(StoreName.My, 
                StoreLocation.CurrentUser))
            {
                store.Open(OpenFlags.ReadOnly);
                var certs = store.Certificates
                    .Find(X509FindType.FindByThumbprint, 
                        builtConfig["CertificateThumbprint"], false);

                config.AddAzureKeyVault(
                    builtConfig["KeyVaultName"], 
                    builtConfig["AzureADApplicationId"], 
                    certs.OfType<X509Certificate2>().Single());

                store.Close();
            }
        }
    })
    .UseStartup<Startup>();
```

<span data-ttu-id="be9ad-255">자세한 내용은 [클라이언트 암호 대신 인증서를 사용 하 여 인증](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret)합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-255">For more information, see [Authenticate with a Certificate instead of a Client Secret](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret).</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="be9ad-256">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="be9ad-256">Bind an array to a class</span></span>

<span data-ttu-id="be9ad-257">공급자는 POCO 배열에 대 한 바인딩에 배열로 구성 값을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-257">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="be9ad-258">콜론을 포함 하는 키를 허용 하는 구성 소스에서 읽을 때 (`:`) 구분 기호, 숫자 키 세그먼트 배열을 구성 하는 키를 구분 하기 위해 사용 됩니다 (`:0:`, `:1:`,...</span><span class="sxs-lookup"><span data-stu-id="be9ad-258">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="be9ad-259">`:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="be9ad-259">`:{n}:`).</span></span> <span data-ttu-id="be9ad-260">자세한 내용은 참조 하세요. [구성: 클래스에 배열을 바인딩할](xref:fundamentals/configuration/index#bind-an-array-to-a-class)합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-260">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="be9ad-261">Azure Key Vault 키를 구분 기호로 콜론을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-261">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="be9ad-262">이 항목에서 설명한 접근 방식을 사용 하 여 이중 대시 (`--`) 계층 값 (섹션)에 대 한 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-262">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="be9ad-263">배열 키는 이중 대시 및 숫자 키 세그먼트를 사용 하 여 Azure Key Vault에 저장 됩니다 (`--0--`, `--1--`하십시오 &hellip; `--{n}--`).</span><span class="sxs-lookup"><span data-stu-id="be9ad-263">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="be9ad-264">다음 검사 [Serilog](https://serilog.net/) 로깅 공급자 구성이 JSON 파일에서 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-264">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="be9ad-265">두 개체에 정의 된 리터럴을 가지는 `WriteTo` 두 Serilog를 반영 하는 배열 *싱크*, 로깅 출력의 대상을 설명 하는:</span><span class="sxs-lookup"><span data-stu-id="be9ad-265">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="be9ad-266">이전 JSON 파일에 표시 된 구성은 이중 대시를 사용 하 여 Azure Key Vault에 저장 됩니다 (`--`) 표기법 및 숫자 세그먼트:</span><span class="sxs-lookup"><span data-stu-id="be9ad-266">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="be9ad-267">Key</span><span class="sxs-lookup"><span data-stu-id="be9ad-267">Key</span></span> | <span data-ttu-id="be9ad-268">값</span><span class="sxs-lookup"><span data-stu-id="be9ad-268">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="be9ad-269">암호를 다시 로드</span><span class="sxs-lookup"><span data-stu-id="be9ad-269">Reload secrets</span></span>

<span data-ttu-id="be9ad-270">될 때까지 캐시 된 비밀 `IConfigurationRoot.Reload()` 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-270">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="be9ad-271">만료를 비활성화 하 고 key vault에 암호를 업데이트 될 때까지 앱에서 준수 하지 않을 `Reload` 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-271">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="be9ad-272">사용 안 함 및 만료 된 암호</span><span class="sxs-lookup"><span data-stu-id="be9ad-272">Disabled and expired secrets</span></span>

<span data-ttu-id="be9ad-273">사용 안 함 및 만료 된 암호를 throw 한 `KeyVaultClientException`합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-273">Disabled and expired secrets throw a `KeyVaultClientException`.</span></span> <span data-ttu-id="be9ad-274">앱에서 throw를 방지 하려면 앱 바꾸거나 사용 안 함/만료 된 암호를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-274">To prevent your app from throwing, replace your app or update the disabled/expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="be9ad-275">문제 해결</span><span class="sxs-lookup"><span data-stu-id="be9ad-275">Troubleshoot</span></span>

<span data-ttu-id="be9ad-276">오류 메시지에 기록 됩니다 앱 공급자를 사용 하 여 구성을 로드 하지 못하면 합니다 [ASP.NET Core 로깅 인프라](xref:fundamentals/logging/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-276">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="be9ad-277">다음 조건 하면 구성을 로드에서 하지 것입니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-277">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="be9ad-278">앱은 Azure Active Directory에 올바르게 구성 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-278">The app isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="be9ad-279">Key vault는 Azure Key Vault에 존재 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-279">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="be9ad-280">앱은 key vault 액세스 권한이 없는 합니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-280">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="be9ad-281">액세스 정책에 포함 되지 않습니다 `Get` 고 `List` 권한.</span><span class="sxs-lookup"><span data-stu-id="be9ad-281">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="be9ad-282">키 자격 증명 모음에 구성 데이터 (이름-값 쌍)가 이름이, 누락, 비활성화 되었거나 만료 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-282">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="be9ad-283">앱에 잘못 된 키 자격 증명 모음 이름 (`KeyVaultName`)를 Azure AD 응용 프로그램 Id (`AzureADApplicationId`), 또는 Azure AD 암호 (클라이언트 암호) (`AzureADPassword`).</span><span class="sxs-lookup"><span data-stu-id="be9ad-283">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD Password (Client Secret) (`AzureADPassword`).</span></span>
* <span data-ttu-id="be9ad-284">Azure AD 암호 (클라이언트 암호) (`AzureADPassword`) 만료 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-284">The Azure AD Password (Client Secret) (`AzureADPassword`) is expired.</span></span>
* <span data-ttu-id="be9ad-285">구성 키 (이름)를 로드 하려고 하는 값에 대 한 앱에서 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be9ad-285">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be9ad-286">추가 자료</span><span class="sxs-lookup"><span data-stu-id="be9ad-286">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="be9ad-287">Microsoft Azure: 키 자격 증명 모음</span><span class="sxs-lookup"><span data-stu-id="be9ad-287">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="be9ad-288">Microsoft Azure: Key Vault 설명서</span><span class="sxs-lookup"><span data-stu-id="be9ad-288">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="be9ad-289">Azure Key Vault에 대 한 키 생성 및 HSM 보호 된 전송 하는 방법</span><span class="sxs-lookup"><span data-stu-id="be9ad-289">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="be9ad-290">KeyVaultClient 클래스</span><span class="sxs-lookup"><span data-stu-id="be9ad-290">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="be9ad-291">빠른 시작: 설정 및.NET 웹 앱을 사용 하 여 Azure Key Vault에서 비밀을 검색</span><span class="sxs-lookup"><span data-stu-id="be9ad-291">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="be9ad-292">자습서: Azure Key Vault 사용 하 여 Azure Windows 가상 컴퓨터에.NET을 사용 하는 방법</span><span class="sxs-lookup"><span data-stu-id="be9ad-292">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
