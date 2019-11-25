# <a name="key-vault-configuration-provider-sample-app"></a>Key Vault 구성 공급자 샘플 앱

이 샘플에서는 Azure Key Vault 구성 공급자를 사용 하는 방법을 보여 줍니다.

이 샘플은 *Program.cs* 파일의 맨 위에 있는 `#define` 문에 의해 결정 된 두 가지 모드 중 하나로 실행 됩니다. 자세한 지침은 [샘플 코드의 전처리기 지시문](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code)을 참조 하세요.

* `Certificate` &ndash;에서는 Azure Key Vault 클라이언트 ID 및 x.509 인증서를 사용 하 여 Azure Key Vault에 저장 된 암호에 액세스 하는 방법을 보여 줍니다. 이 버전의 샘플은 Azure App Service 또는 ASP.NET Core 앱을 제공할 수 있는 호스트에 배포 된 모든 위치에서 실행할 수 있습니다.
* `Managed` &ndash; Azure의 [관리 서비스 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 사용 하 여 앱의 코드 또는 구성에서 자격 증명 없이 azure AD 인증을 사용 하 여 Azure Key Vault 하도록 앱을 인증 하는 방법을 보여 줍니다. 앱이 Azure Key Vault으로 인증 하는 데 Azure AD 클라이언트 ID 및 비밀이 필요 하지 않습니다. 이 샘플은 관리 되는 Id scearnio를 탐색 하기 위해 Azure App Service에 배포 되어야 합니다.

자세한 내용은 [Azure Key Vault 구성 공급자](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration)를 참조 하세요.
