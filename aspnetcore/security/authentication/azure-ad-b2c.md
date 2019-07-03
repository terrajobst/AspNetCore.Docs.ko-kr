---
title: ASP.NET Core에서 Azure Active Directory B2C를 사용 하 여 클라우드 인증
author: camsoper
description: ASP.NET Core를 사용 하 여 Azure Active Directory B2C 인증을 설정 하는 방법을 알아봅니다.
ms.author: casoper
ms.custom: mvc
ms.date: 02/27/2019
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 3cb878aff7bf0c6c8efe7f3f0c0f06c74acef477
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538739"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a>ASP.NET Core에서 Azure Active Directory B2C를 사용 하 여 클라우드 인증

작성자: [Cam Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C)는 웹 및 모바일 앱에 대 한 클라우드 id 관리 솔루션입니다. 서비스는 클라우드 및 온-프레미스에서 호스트 되는 앱에 대 한 인증을 제공 합니다. 인증 유형 포함할 개별 계정, 소셜 네트워크 계정 및 enterprise 계정 페더레이션 합니다. 또한 Azure AD B2C는 최소 구성으로 multi-factor authentication 인증을 제공할 수 있습니다.

> [!TIP]
> Azure Active Directory (Azure AD) 및 Azure AD B2C는 별개 제품입니다. Azure AD 테 넌 트 조직을 나타내고 Azure AD B2C 테 넌 트를 신뢰 당사자 응용 프로그램을 사용 하 여 사용할 id의 컬렉션을 나타냅니다. 자세한 내용은를 참조 하세요. [Azure AD B2C: 질문과 대답 (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs)합니다.

이 자습서에 설명 하는 방법.

> [!div class="checklist"]
> * Azure Active Directory B2C 테 넌 트 만들기
> * Azure AD B2C에서 앱을 등록 합니다.
> * Visual Studio를 사용 하 여 인증을 위해 Azure AD B2C 테 넌 트를 사용 하도록 구성 하는 ASP.NET Core 웹 앱을 만들려면
> * Azure AD B2C 테 넌 트의 동작을 제어 하는 정책 구성

## <a name="prerequisites"></a>전제 조건

다음은이 연습에 필요한:

* [Microsoft Azure 구독](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C 테 넌 트 만들기

Azure Active Directory B2C 테 넌 트 만들기 [설명서에 설명 된 대로](/azure/active-directory-b2c/active-directory-b2c-get-started)입니다. 메시지가 표시 되 면 Azure 구독을 사용 하 여 테 넌 트 연결는 선택 사항이 자습서에 대 한 합니다.

## <a name="register-the-app-in-azure-ad-b2c"></a>Azure AD B2C에서 앱을 등록 합니다.

새로 만든 Azure AD B2C 테 넌 트를 사용 하 여 앱을 등록 [설명서에 나와 있는 단계](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) 아래 합니다 **웹 앱을 등록** 섹션입니다. 중지 된 **웹 앱 클라이언트 비밀 만들기** 섹션입니다. 클라이언트 암호를이 자습서에 필요 하지 않습니다. 

다음 값을 사용 합니다.

| 설정                       | 값                     | 노트                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **이름**                      | *&lt;앱 이름&gt;*        | 입력 한 **이름을** 소비자에 게 앱을 설명 하는 앱에 대 한 합니다.                                                                                                                                 |
| **웹 앱/웹 API 포함** | 예                       |                                                                                                                                                                                                    |
| **암시적 흐름 허용**       | 예                       |                                                                                                                                                                                                    |
| **회신 URL**                 | `https://localhost:44300/signin-oidc` | 회신 Url은 Azure AD B2C 앱이 요청한 토큰을 반환 하는 위치 끝점입니다. Visual Studio를 사용 하 여 회신 URL을 제공 합니다. 이제 입력 `https://localhost:44300/signin-oidc` 양식을 완성 하 합니다. |
| **앱 ID URI**                | 비워 둡니다               | 이 자습서에 필요 하지 않습니다.                                                                                                                                                                    |
| **네이티브 클라이언트 포함**     | 아니요                        |                                                                                                                                                                                                    |

> [!WARNING]
> 경우 유의 해야 localhost가 아닌 회신 URL을 설정 합니다 [회신 URL 목록에 허용 되는 사항에 대 한 제약 조건을](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url)합니다. 

앱을 등록 한 후에 테 넌 트에서 앱의 목록이 표시 됩니다. 방금 등록 된 앱을 선택 합니다. 선택 합니다 **복사** 아이콘의 오른쪽에는 **응용 프로그램 ID** 클립보드에 복사 하는 필드입니다.

Nothing 자세히 이때 Azure AD B2C 테 넌 트에서 구성할 수 있지만 브라우저 창을 그대로 열어 둡니다. ASP.NET Core 앱을 만든 후 더 많은 구성이 있습니다.

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Visual Studio에서 ASP.NET Core 앱 만들기

Visual Studio 웹 응용 프로그램 템플릿은 인증에 Azure AD B2C 테 넌 트를 사용 하도록 구성할 수 있습니다.

Visual Studio에서 다음을 수행합니다.

1. 새 ASP.NET Core 웹 애플리케이션을 만듭니다. 
2. 선택 **웹 응용 프로그램** 템플릿 목록에서.
3. 선택 된 **인증 변경** 단추입니다.
    
    ![인증 변경 단추](./azure-ad-b2c/_static/changeauth.png)

4. 에 **인증 변경** 대화 상자에서 **개별 사용자 계정**를 선택한 후 **클라우드에서 기존 사용자 저장소에 연결** 드롭다운 목록에서. 
    
    ![인증 대화 상자 변경](./azure-ad-b2c/_static/changeauthdialog.png)

5. 다음 값으로 양식을 완성 합니다.
    
    | 설정                       | 값                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **도메인 이름**               | *&lt;B2C 테 넌 트의 도메인 이름&gt;*          |
    | **응용 프로그램 ID**            | *&lt;클립보드의 응용 프로그램 ID를 붙여 넣습니다.&gt;* |
    | **콜백 경로**             | *&lt;기본값 사용&gt;*                       |
    | **등록 또는 로그인 정책** | `B2C_1_SiUpIn`                                        |
    | **암호 재설정 정책**     | `B2C_1_SSPR`                                          |
    | **프로필 정책 편집**       | *&lt;비워 둡니다&gt;*                                 |
    
    선택 합니다 **복사본** 링크 옆에 **회신 URI** 회신 URI를 클립보드에 복사 합니다. 선택 **확인** 닫으려면 합니다 **인증 변경** 대화 합니다. 선택 **확인** 웹 앱을 만듭니다.

## <a name="finish-the-b2c-app-registration"></a>B2C 앱 등록을 완료

아직 열려 B2C 앱 속성을 사용 하 여 브라우저 창으로 돌아갑니다. 임시 변경 **회신 URL** Visual Studio에서 복사한 이전 값으로 지정 합니다. 선택 **저장할** 창의 맨 위에 있는 합니다.

> [!TIP]
> 회신 URL을 복사 하지 않은 경우 웹 프로젝트 속성에서 디버그 탭에서 HTTPS 주소를 사용 하 고 추가 합니다 **CallbackPath** 에서 값 *appsettings.json*합니다.

## <a name="configure-policies"></a>정책 구성

Azure AD B2C 설명서의 단계를 사용 [등록 또는 로그인 정책 만들기](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy)를 차례로 [암호 재설정 정책 만들기](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy)합니다. 에 대 한 설명서에 제공 된 예제 값을 사용할 **Id 공급자**를 **등록 특성**, 및 **응용 프로그램 클레임**합니다. 사용 하는 **지금 실행** 설명서에 설명 된 대로 정책을 테스트 하려면 단추는 선택 사항입니다.

> [!WARNING]
> 정책 이름이 해당 정책에서 사용 된 설명서에 설명 된 대로 정확 하 게 되어 확인 합니다 **인증 변경** Visual Studio에서 대화 합니다. 정책 이름을 확인할 수 있습니다 *appsettings.json*합니다.

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a>기본 쿠키/JwtBearer/OpenIdConnectOptions 옵션 구성

기본 옵션을 직접 구성 하려면에서 적절 한 체계 상수를 사용 하 여 `Startup.ConfigureServices`:

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a>앱 실행

Visual Studio에서 눌러 **F5** 를 빌드하고 앱을 실행 합니다. 웹 앱이 시작 되 면 선택 **Accept** (메시지 표시) 하는 경우 쿠키 사용을 받아들이고 선택한 **로그인**합니다.

![앱에 로그인](./azure-ad-b2c/_static/signin.png)

브라우저에서 Azure AD B2C 테 넌 트 이동 합니다. (정책 테스트를 만든) 하는 경우 기존 계정으로 로그인 하거나 선택 **지금 등록** 새 계정을 만들 수 있습니다. 합니다 **암호를 잊으셨나요?** 잊어버린된 암호 재설정 링크를 사용 합니다.

![Azure AD B2C 로그인](./azure-ad-b2c/_static/b2csts.png)

성공적으로 로그인 한 후 브라우저에서 웹 앱에 이동 합니다.

![성공](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법을 학습했습니다.

> [!div class="checklist"]
> * Azure Active Directory B2C 테 넌 트 만들기
> * Azure AD B2C에서 앱을 등록 합니다.
> * Visual Studio를 사용 하 여 인증을 위해 Azure AD B2C 테 넌 트를 사용 하도록 구성 된 ASP.NET Core 웹 응용 프로그램을 만들려면
> * Azure AD B2C 테 넌 트의 동작을 제어 하는 정책 구성

ASP.NET Core 앱은 인증을 위해 Azure AD B2C를 사용 하도록 구성 했으므로 합니다 [Authorize 특성](xref:security/authorization/simple) 앱 보안을 사용할 수 있습니다. 응용 프로그램을 학습 하 여 개발을 계속 합니다.

* [Azure AD B2C 사용자 인터페이스](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)합니다.
* [암호 복잡성 요구 사항을 구성](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)합니다.
* [Multi-factor authentication 사용](/azure/active-directory-b2c/active-directory-b2c-reference-mfa)합니다.
* 와 같은 추가 id 공급자를 구성 [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app)합니다 [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app)를 [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), 등입니다.
* [Azure AD Graph API를 사용 하 여](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) 를 Azure AD B2C 테 넌 트에서 그룹 멤버 자격 등의 추가 사용자 정보를 검색 합니다.
* [ASP.NET Core web API를 Azure AD B2C를 사용 하 여 보안](xref:security/authentication/azure-ad-b2c-webapi)합니다.
* [Azure AD B2C를 사용 하 여.NET 웹 앱에서.NET web API 호출](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet)합니다.
