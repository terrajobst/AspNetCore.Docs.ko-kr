---
title: ASP.NET Core에서 azure Key Vault 구성 공급자
author: guardrex
description: Azure 키 자격 증명 모음 구성 공급자를 사용 하 여 런타임에 로드 하는 이름-값 쌍을 사용 하 여 앱을 구성 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/13/2019
uid: security/key-vault-configuration
ms.openlocfilehash: 78c63cf135ca92f0b5f6c6828b2ae34a44a7b36c
ms.sourcegitcommit: 3ee6ee0051c3d2c8d47a58cb17eef1a84a4c46a0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65621026"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>ASP.NET Core에서 azure Key Vault 구성 공급자

하 여 [Luke Latham](https://github.com/guardrex) 고 [Andrew Stanton-nurse](https://github.com/anurse)

이 문서를 사용 하는 방법에 설명 합니다 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 Azure Key Vault 암호에서 앱 구성 값을 로드 합니다. Azure Key Vault는 암호화 키 및 앱 및 서비스에서 사용 하는 비밀을 보호 하는 데 도움이 되는 클라우드 기반 서비스. ASP.NET Core 앱을 사용 하 여 Azure Key Vault를 사용 하는 것에 대 한 일반적인 시나리오에는 다음이 포함 됩니다.

* 중요 한 구성 데이터에 대 한 액세스를 제어 합니다.
* FIPS 요구 사항을 충족 140-2 Level 2 유효성 검사가 하드웨어 보안 모듈 (HSM의) 구성 데이터를 저장할 때.

이 시나리오는 ASP.NET Core 2.1을 대상으로 하는 앱에 사용할 수 있는 이상.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="packages"></a>패키지

Azure 키 자격 증명 모음 구성 공급자를 사용 하려면 패키지 참조를 추가 합니다 [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 패키지 있습니다.

채택 하는 [Azure 리소스에 대 한 id 관리](/azure/active-directory/managed-identities-azure-resources/overview) 시나리오에 대 한 패키지 참조를 추가 합니다 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) 패키지.

> [!NOTE]
> 안정적인 최신 릴리스를 작성할 당시 `Microsoft.Azure.Services.AppAuthentication`, 버전 `1.0.3`에 대 한 지원을 제공 [identities 관리 되는 시스템 할당](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka)합니다. 에 대 한 지원 *identities 관리 되는 사용자 할당* 에서 사용할 수는 `1.2.0-preview2` 패키지 있습니다. 이 항목에서는 시스템 관리 id 사용을 보여 줍니다. 및 버전을 사용 하 여 제공된 된 샘플 앱 `1.0.3` 의 `Microsoft.Azure.Services.AppAuthentication` 패키지 있습니다.

## <a name="sample-app"></a>샘플 앱

샘플 앱에 의해 결정 되는 두 가지 모드 중 하나에서 실행 되는 `#define` 맨 위에 있는 문을 합니다 *Program.cs* 파일:

* `Certificate` &ndash; Azure Key Vault에 저장 된 액세스 비밀을 Azure Key Vault 클라이언트 ID 및 X.509 인증서의 사용을 보여 줍니다. Azure App Service 또는 ASP.NET Core 앱을 처리할 수 있는 모든 호스트에 배포 된 모든 위치에서이 버전의 샘플을 실행할 수 있습니다.
* `Managed` &ndash; 사용 하는 방법을 보여 줍니다 [Azure 리소스에 대 한 id 관리](/azure/active-directory/managed-identities-azure-resources/overview) 앱의 코드 또는 구성에 저장 된 자격 증명 없이 Azure AD 인증을 사용 하 여 Azure Key Vault에 앱을 인증할 수 있습니다. 관리 되는 id를 사용 하 여 인증을 하는 경우 Azure AD 응용 프로그램 ID 및 암호 (클라이언트 암호)를 사용할 필요가 없습니다. `Managed` 버전 샘플의 Azure에 배포 되어야 합니다. 지침에 따라 합니다 [관리 되는 id를 사용 하 여 Azure 리소스에 대 한](#use-managed-identities-for-azure-resources) 섹션입니다.

전처리기 지시문을 사용 하 여 샘플 앱을 구성 하는 방법에 대 한 자세한 내용은 (`#define`)를 참조 하세요 <xref:index#preprocessor-directives-in-sample-code>합니다.

## <a name="secret-storage-in-the-development-environment"></a>개발 환경에서 비밀 저장소

로컬로 사용 하 여 암호를 설정 합니다 [암호 관리자 도구](xref:security/app-secrets)합니다. 암호 로드는 샘플 응용 프로그램 개발 환경에서 로컬 컴퓨터에서 실행 되 면 로컬 관리자 암호 저장소에서.

암호 관리자 도구 필요는 `<UserSecretsId>` 앱의 프로젝트 파일의 속성입니다. 속성 값을 설정 (`{GUID}`) 모든 고유 GUID로:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

비밀 이름-값 쌍으로 생성 됩니다. 계층 값 (구성 섹션)를 사용 하는 `:` (콜론)에서 구분 기호로 [ASP.NET Core 구성](xref:fundamentals/configuration/index) 키 이름을 합니다.

암호 관리자를 열어 프로젝트의 콘텐츠 루트를 명령 셸에서 여기서 `{SECRET NAME}` 이름인 및 `{SECRET VALUE}` 값:

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

샘플 앱에 대 한 암호를 설정 하려면 프로젝트의 콘텐츠 루트에서 명령 셸에서 다음 명령을 실행 합니다.

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

이러한 비밀은 Azure Key Vault에 저장 하는 경우는 [Azure Key Vault를 사용 하 여 프로덕션 환경에서 비밀 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 섹션인 합니다 `_dev` 접미사가으로 변경 되었을 `_prod`합니다. 접미사는 앱의 출력의 구성 값의 원본을 나타내는 시각 표시를 제공 합니다.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault를 사용 하 여 프로덕션 환경에서 비밀 저장소

제공한 지침을 [빠른 시작: 설정 및 Azure CLI를 사용 하 여 Azure Key Vault에서 비밀을 검색](/azure/key-vault/quick-create-cli) 항목은 여기에 요약 된 Azure Key Vault를 만들고 샘플 앱에서 사용 하는 비밀을 저장 합니다. 자세한 내용은 항목을 참조 합니다.

1. 열기 Azure Cloud shell에서 다음 방법 중 하나를 사용 하 여 [Azure portal](https://portal.azure.com/):

   * 선택 **시도** 코드 블록의 오른쪽 위 모퉁이에서. 텍스트 상자에 검색 문자열 "Azure CLI"를 사용 합니다.
   * Cloud Shell을 사용 하 여 브라우저에서 엽니다는 **Cloud Shell 시작** 단추입니다.
   * 선택 된 **Cloud Shell** Azure portal의 오른쪽 위 모서리에 있는 메뉴 단추입니다.

   자세한 내용은 [Azure CLI (명령줄 인터페이스)](/cli/azure/) 하 고 [Azure Cloud Shell 개요](/azure/cloud-shell/overview)합니다.

1. 아직 인증 되지 경우 로그인 하 여 `az login` 명령입니다.

1. 다음 명령을 사용 하 여 리소스 그룹을 만듭니다. 여기서 `{RESOURCE GROUP NAME}` 은 새 리소스 그룹에 대 한 리소스 그룹 이름 및 `{LOCATION}` 은 Azure 지역 (데이터 센터):

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 다음 명령 사용 하 여 리소스 그룹에 key vault 만들기 위치 `{KEY VAULT NAME}` 은 새 키 자격 증명 모음에 대 한 이름 및 `{LOCATION}` 은 Azure 지역 (데이터 센터):

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 이름-값 쌍으로 key vault에서 비밀을 만듭니다.

   Azure Key Vault 비밀 이름은 영숫자와 대시만 제한 됩니다. 계층 값 (구성 섹션) 사용 하 여 `--` (두 개의 대시) 구분 기호로 합니다. 일반적으로 하위 키에의 한 부분을 구분 하는 데 사용 되는 콜론 [ASP.NET Core 구성](xref:fundamentals/configuration/index), 키 자격 증명 모음 비밀 이름에 허용 되지 않습니다. 따라서 두 개의 대시 사용 되며 암호를 앱의 구성에 로드 될 때 콜론으로 교체 됩니다.

   다음 암호를 샘플 앱과 함께 사용 됩니다. 값 포함을 `_prod` 에서 구별 하는 접미사는 `_dev` 접미사 사용자 암호에서 개발 환경에 로드 하는 값입니다. 대체 `{KEY VAULT NAME}` 이전 단계에서 만든 key vault의 이름:

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure 호스트 되는 앱에 대 한 응용 프로그램 ID 및 X.509 인증서를 사용 합니다.

Azure AD를 구성 하 고, Azure Active Directory 응용 프로그램 ID 및 X.509를 사용 하는 Azure Key Vault 및 앱 인증서를 key vault에 인증 **앱 Azure 외부에서 호스트 되는 경우**합니다. 자세한 내용은 [키, 비밀 및 인증서에 대 한](/azure/key-vault/about-keys-secrets-and-certificates)합니다.

> [!NOTE]
> 응용 프로그램 ID 및 X.509 인증서를 사용 하는 Azure에서 호스트 되는 앱에 대 한 지원 되지만 사용 하는 것이 좋습니다 [Azure 리소스에 대 한 id 관리](#use-managed-identities-for-azure-resources) Azure에서 앱을 호스팅하는 경우. 관리 되는 id는 앱에서 또는 개발 환경에서 인증서를 저장할 필요 하지 않습니다.

샘플 앱에서는 경우 응용 프로그램 ID 및 X.509 인증서를 `#define` 맨 위에 있는 문을 합니다 *Program.cs* 파일을 설정 `Certificate`합니다.

1. PKCS #12 파일을 만듭니다 (*.pfx*) 인증서입니다. 인증서를 만들기 위한 옵션에 포함 됩니다 [Windows에서 MakeCert](/windows/desktop/seccrypto/makecert) 하 고 [OpenSSL](https://www.openssl.org/)합니다.
1. 현재 사용자의 개인 인증서 저장소에 인증서를 설치 합니다. 표시 키로 내보낼 수 있는 선택 사항입니다. 이 프로세스에서 나중에 사용 되는 인증서의 지문을 note 합니다.
1. PKCS #12 보관 파일 내보내기 (*.pfx*) DER로 인코딩된 인증서를 인증서 (*.cer*).
1. Azure AD에 앱 등록 (**앱 등록**).
1. DER로 인코딩된 인증서를 업로드 (*.cer*) Azure AD에:
   1. Azure AD에서 앱을 선택 합니다.
   1. 이동할 **인증서 및 비밀**합니다.
   1. 선택 **인증서 업로드** 공개 키를 포함 하는 인증서를 업로드 합니다. A *.cer*를 *.pem*, 또는 *.crt* 인증서 허용 됩니다.
1. 앱의 키 자격 증명 모음 이름, 응용 프로그램 ID 및 인증서 지문을 저장 *appsettings.json* 파일입니다.
1. 이동할 **Key vault** Azure portal에서 합니다.
1. 만든 키 자격 증명 모음을 선택 합니다 [Azure Key Vault를 사용 하 여 프로덕션 환경에서 비밀 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 섹션입니다.
1. 선택 **액세스 정책**합니다.
1. 선택 **새로 추가**합니다.
1. 선택 **보안 주체 선택** 이름으로 등록 된 앱을 선택 합니다. 선택 된 **선택** 단추입니다.
1. 열기 **비밀 권한** 응용 프로그램을 제공 하 고 **가져오기** 하 고 **목록** 권한.
1. **확인**을 선택합니다.
1. **저장**을 선택합니다.
1. 앱을 배포 합니다.

합니다 `Certificate` 샘플 앱에서 해당 구성 값을 가져옵니다 `IConfigurationRoot` 비밀 이름으로 같은 이름의:

* 비 계층 값: 에 대 한 값 `SecretName` 사용 하 여 가져오는 `config["SecretName"]`합니다.
* 계층 값 (섹션): 사용 하 여 `:` (콜론) 표기법 또는 `GetSection` 확장 메서드. 이러한 방법 중 하나를 사용 하 여 구성 값을 가져옵니다.
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 인증서는 OS에 의해 관리 됩니다. 앱 호출 `AddAzureKeyVault` 에서 제공 하는 값을 *appsettings.json* 파일:

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=20-23)]

예제 값:

* 키 자격 증명 모음 이름: `contosovault`
* 응용 프로그램 ID: `627e911e-43cc-61d4-992e-12db9c81b413`
* 인증서 지문: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/sample/appsettings.json)]

앱을 실행 하는 경우 웹 페이지 로드 비밀 값을 보여 줍니다. 개발 환경에서 사용 하 여 비밀 값 로드는 `_dev` 접미사. 프로덕션 환경에서 값을 사용 하 여 로드 된 `_prod` 접미사.

## <a name="use-managed-identities-for-azure-resources"></a>Azure 리소스에 대 한 관리 되는 id를 사용 합니다.

**Azure에 배포 된 앱** 활용할 수 있습니다 [Azure 리소스에 대 한 id 관리](/azure/active-directory/managed-identities-azure-resources/overview), Azure Key Vault를 사용 하 여 인증 하도록 앱을 설정 하는 자격 증명 없이 Azure AD 인증을 사용 하 여이 있어 (응용 프로그램 ID 및 Password/Client 비밀) 앱에 저장 합니다.

Azure 리소스에 대 한 관리 되는 id를 사용 하는 샘플 앱 경우는 `#define` 맨 위에 있는 문을 합니다 *Program.cs* 파일을 설정 `Managed`.

앱의 자격 증명 모음 이름 입력 *appsettings.json* 파일입니다. 샘플 앱은 응용 프로그램 ID 및 암호 (클라이언트 암호)로 설정 된 경우 필요 하지 않습니다는 `Managed` 버전이 없으므로 해당 구성 항목을 무시할 수 있습니다. 앱이 azure에 배포 하 고 Azure 인증 앱이 Azure Key Vault에 저장 된 자격 증명 모음 이름을 통해서만 액세스할 수 합니다 *appsettings.json* 파일입니다.

Azure App Service에 샘플 앱을 배포 합니다.

Azure App Service에 배포 된 앱 서비스를 만들 때 Azure AD를 사용 하 여 자동으로 등록 됩니다. 다음 명령을 사용 하 여 배포에서 개체 ID를 가져옵니다. 개체 ID를 Azure portal에 표시 되는 **Identity** App Service의 패널입니다.

응용 프로그램을 Azure CLI 및 앱의 개체 ID를 사용 하 여 제공할 `list` 고 `get` key vault 액세스 권한:

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

**앱을 다시 시작** Azure CLI, PowerShell 또는 Azure portal을 사용 하 여 합니다.

샘플 앱:

* 인스턴스를 만듭니다는 `AzureServiceTokenProvider` 연결 문자열이 없는 클래스입니다. 연결 문자열을 제공 하지 않으면 공급자는 Azure 리소스에 대 한 관리 되는 id에서 액세스 토큰을 가져올 하려고 합니다.
* 새 `KeyVaultClient` 만들어집니다는 `AzureServiceTokenProvider` 인스턴스 토큰 콜백 합니다.
* 합니다 `KeyVaultClient` 인스턴스의 기본 구현이 사용 됩니다 `IKeyVaultSecretManager` 모든 비밀 값을 로드 하 고 이중 대시를 대체 하는 (`--`) 콜론을 사용 하 여 (`:`) 키 이름에서입니다.

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

앱을 실행 하는 경우 웹 페이지 로드 비밀 값을 보여 줍니다. 비밀 값에 개발 환경에는 `_dev` 사용자 암호에서 제공 하는 접미사입니다. 프로덕션 환경에서 값을 사용 하 여 로드 된 `_prod` Azure Key Vault에서 제공 하는 접미사입니다.

표시 되 면는 `Access denied` 오류를 확인 하는 앱을 Azure AD에 등록 하 고 key vault에 대 한 액세스를 제공 합니다. Azure에서 서비스를 다시 시작 했습니다 하면 확인 합니다.

## <a name="use-a-key-name-prefix"></a>키 이름 접두사를 사용 하 여

`AddAzureKeyVault` 구현의 받아들이는 오버 로드가 제공 `IKeyVaultSecretManager`, 구성 키에 변환 된 키 자격 증명 모음 비밀을 제어할 수 있습니다. 예를 들어, 앱 시작 시 제공한 접두사 값을 기반으로 하는 비밀 값을 로드 하는 인터페이스를 구현할 수 있습니다. 이렇게 하면 앱의 버전을 기반으로 하는 비밀 정보를 로드 하려면 예를 들어 있습니다.

> [!WARNING]
> 동일한 키 자격 증명 모음에 여러 앱에 대 한 암호를 배치 하려면 또는 환경 비밀을 key vault 비밀의 접두사를 사용 하지 마세요 (예를 들어 *개발* 비교 *프로덕션* 비밀)를 동일한 자격 증명 모음입니다. 다른 앱 및 개발/프로덕션 환경 앱 환경을 가장 높은 수준의 보안 격리를 별도 키 자격 증명 모음 사용 하는 것이 좋습니다.

다음 예제에서는 비밀 키에 설정 되며 자격 증명 모음 (및 암호 관리자 도구를 사용 하 여 개발 환경)에 대 한 `5000-AppSecret` (마침표는 키 자격 증명 모음 비밀 이름에 허용 되지 않음). 이 비밀을 5.0.0.0 버전의 앱에 대 한 앱 암호를 나타냅니다. 비밀 키에 추가 됩니다 5.1.0.0, 앱의 다른 버전에 대 한 자격 증명 모음 (및 암호 관리자 도구를 사용 하 여)에 대 한 `5100-AppSecret`합니다. 각 앱 버전으로 해당 구성을 해당 버전의 비밀 값 로드 `AppSecret`, 비밀 로드 된 버전 해제 제거 합니다.

`AddAzureKeyVault` 사용자 지정을 사용 하 여 라고 `IKeyVaultSecretManager`:

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?highlight=30-34)]

`IKeyVaultSecretManager` 구현을 구성에 적절 한 암호를 로드 하는 비밀 버전 접두사에 반응 합니다.

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

`Load` 을 찾거나 버전 접두사를 가진 자격 증명 모음 비밀을 통해 반복 하는 알고리즘 공급자에서 호출 됩니다. 버전 접두사를 사용 하 여 검색 되 면 `Load`, 알고리즘을 사용 하는 `GetKey` 비밀 이름의 구성 이름을 반환 하는 방법입니다. 암호의 이름에서 버전 접두사를 제거 하 고 이름-값 쌍에 앱의 구성을 로드에 대 한 비밀 이름의 나머지를 반환 합니다.

경우에이 이렇게 구현 됩니다.

1. 앱의 프로젝트 파일에 지정 된 앱의 버전입니다. 다음 예제에서는 앱의 버전이로 설정 되어 `5.0.0.0`:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. 확인을 `<UserSecretsId>` 속성은 앱의 프로젝트 파일에 있는 위치 `{GUID}` 는 사용자가 제공한 GUID:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   다음 비밀을 사용 하 여 로컬로 저장 합니다 [암호 관리자 도구](xref:security/app-secrets):

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. 암호는 다음 Azure CLI 명령을 사용 하 여 Azure Key Vault에 저장 됩니다.

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. 앱을 실행 하는 경우 key vault 비밀 로드 됩니다. 에 대 한 문자열 암호 `5000-AppSecret` 앱의 프로젝트 파일에 지정 된 앱의 버전 일치 (`5.0.0.0`).

1. 버전 `5000` (대시로) 키 이름에서 제거 됩니다. 앱 키를 사용 하 여 구성을 읽는 동안 `AppSecret` 비밀 값을 로드 합니다.

1. 프로젝트 파일에서 앱의 버전이 변경 된 경우 `5.1.0.0` 앱이 다시 실행 되 고은 비밀 값을 반환 `5.1.0.0_secret_value_dev` 개발 환경에서 고 `5.1.0.0_secret_value_prod` 프로덕션 환경에서.

> [!NOTE]
> 제공할 수도 있습니다 고유한 `KeyVaultClient` 구현을 `AddAzureKeyVault`합니다. 사용자 지정 클라이언트 앱에 클라이언트의 단일 인스턴스를 공유할 수 있습니다.

## <a name="bind-an-array-to-a-class"></a>클래스에 배열 바인딩

공급자는 POCO 배열에 대 한 바인딩에 배열로 구성 값을 읽을 수 있습니다.

콜론을 포함 하는 키를 허용 하는 구성 소스에서 읽을 때 (`:`) 구분 기호, 숫자 키 세그먼트 배열을 구성 하는 키를 구분 하기 위해 사용 됩니다 (`:0:`, `:1:`,... `:{n}:`). 자세한 내용은 참조 하세요. [구성: 클래스에 배열을 바인딩할](xref:fundamentals/configuration/index#bind-an-array-to-a-class)합니다.

Azure Key Vault 키를 구분 기호로 콜론을 사용할 수 없습니다. 이 항목에서 설명한 접근 방식을 사용 하 여 이중 대시 (`--`) 계층 값 (섹션)에 대 한 구분 기호로 사용 합니다. 배열 키는 이중 대시 및 숫자 키 세그먼트를 사용 하 여 Azure Key Vault에 저장 됩니다 (`--0--`, `--1--`하십시오 &hellip; `--{n}--`).

다음 검사 [Serilog](https://serilog.net/) 로깅 공급자 구성이 JSON 파일에서 제공 합니다. 두 개체에 정의 된 리터럴을 가지는 `WriteTo` 두 Serilog를 반영 하는 배열 *싱크*, 로깅 출력의 대상을 설명 하는:

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

이전 JSON 파일에 표시 된 구성은 이중 대시를 사용 하 여 Azure Key Vault에 저장 됩니다 (`--`) 표기법 및 숫자 세그먼트:

| Key | 값 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>암호를 다시 로드

될 때까지 캐시 된 비밀 `IConfigurationRoot.Reload()` 라고 합니다. 만료를 비활성화 하 고 key vault에 암호를 업데이트 될 때까지 앱에서 준수 하지 않을 `Reload` 실행 됩니다.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>사용 안 함 및 만료 된 암호

사용 안 함 및 만료 된 암호를 throw 한 `KeyVaultClientException`합니다. 앱에서 throw를 방지 하려면 앱 바꾸거나 사용 안 함/만료 된 암호를 업데이트 합니다.

## <a name="troubleshoot"></a>문제 해결

오류 메시지에 기록 됩니다 앱 공급자를 사용 하 여 구성을 로드 하지 못하면 합니다 [ASP.NET Core 로깅 인프라](xref:fundamentals/logging/index)합니다. 다음 조건 하면 구성을 로드에서 하지 것입니다.

* 앱 또는 인증서를 Azure Active Directory에 올바르게 구성 되지 않았습니다.
* Key vault는 Azure Key Vault에 존재 하지 않습니다.
* 앱은 key vault 액세스 권한이 없는 합니다.
* 액세스 정책에 포함 되지 않습니다 `Get` 고 `List` 권한.
* 키 자격 증명 모음에 구성 데이터 (이름-값 쌍)가 이름이, 누락, 비활성화 되었거나 만료 되었습니다.
* 앱에 잘못 된 키 자격 증명 모음 이름 (`KeyVaultName`)를 Azure AD 응용 프로그램 Id (`AzureADApplicationId`), 또는 Azure AD 인증서 지문 (`AzureADCertThumbprint`).
* 구성 키 (이름)를 로드 하려고 하는 값에 대 한 앱에서 올바르지 않습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: 키 자격 증명 모음](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault 설명서](/azure/key-vault/)
* [Azure Key Vault에 대 한 키 생성 및 HSM 보호 된 전송 하는 방법](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient 클래스](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [빠른 시작: 설정 및.NET 웹 앱을 사용 하 여 Azure Key Vault에서 비밀을 검색](/azure/key-vault/quick-create-net)
* [자습서: Azure Key Vault 사용 하 여 Azure Windows 가상 컴퓨터에.NET을 사용 하는 방법](/azure/key-vault/tutorial-net-windows-virtual-machine)
