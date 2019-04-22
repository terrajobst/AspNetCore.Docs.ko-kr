---
ms.openlocfilehash: 209b5c41e17897693962954b1e795bdbb41f9384
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59012736"
---
# <a name="key-vault-configuration-provider-sample-app"></a>키 자격 증명 모음 구성 공급자 샘플 응용 프로그램

이 샘플에서는 Azure 키 자격 증명 모음 구성 공급자를 사용 합니다.

샘플에 의해 결정 되는 두 가지 모드 중 하나에서 실행 되는 `#define` 맨 위에 있는 문을 *Program.cs* 파일입니다. 자세한 내용은 [샘플 코드에서 전처리기 지시문](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code):

* `Certificate` &ndash; Azure Key Vault에 저장 된 액세스 비밀을 Azure Key Vault 클라이언트 ID 및 X.509 인증서의 사용을 보여 줍니다. Azure App Service 또는 ASP.NET Core 앱을 처리할 수 있는 모든 호스트에 배포 된 모든 위치에서이 버전의 샘플을 실행할 수 있습니다.
* `Managed` &ndash; Azure를 사용 하는 방법을 보여 줍니다 [관리 서비스 Id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 앱의 코드 또는 구성에 대 한 자격 증명 없이 Azure AD 인증을 사용 하 여 Azure Key Vault에 앱을 인증 합니다. Azure AD 클라이언트 ID 및 암호를 앱이 Azure Key Vault를 사용 하 여 인증 하는 데 필요 하지 않습니다. 이 샘플은 Azure App service 관리 Id scearnio 탐색에 배포 되어야 합니다.

자세한 내용은 [Azure 키 자격 증명 모음 구성 공급자](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration)합니다.
