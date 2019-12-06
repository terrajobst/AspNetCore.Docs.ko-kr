---
title: Web Api ASP.NET Core에서 Azure Active Directory B2C를 사용 하 여 인증
author: camsoper
description: ASP.NET Core Web API를 사용 하 여 Azure Active Directory B2C 인증을 설정 하는 방법을 알아봅니다. 인증 된 웹 Postman 사용 하 여 API를 테스트 합니다.
ms.author: casoper
ms.date: 12/05/2019
ms.custom: mvc, seodec18
uid: security/authentication/azure-ad-b2c-webapi
ms.openlocfilehash: 0c7e8a1db924bdedf29468bf6b8cc17f03962c6d
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880735"
---
# <a name="authentication-in-web-apis-with-azure-active-directory-b2c-in-aspnet-core"></a>Web Api ASP.NET Core에서 Azure Active Directory B2C를 사용 하 여 인증

작성자: [Cam Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C)는 웹 및 모바일 앱에 대 한 클라우드 id 관리 솔루션입니다. 서비스는 클라우드 및 온-프레미스에서 호스트 되는 앱에 대 한 인증을 제공 합니다. 인증 유형 포함할 개별 계정, 소셜 네트워크 계정 및 enterprise 계정 페더레이션 합니다. Azure AD B2C는 또한 최소 구성으로 multi-factor authentication 인증을 제공합니다.

Azure Active Directory (Azure AD) 및 Azure AD B2C는 별개 제품입니다. Azure AD 테 넌 트 조직을 나타내고 Azure AD B2C 테 넌 트를 신뢰 당사자 응용 프로그램을 사용 하 여 사용할 id의 컬렉션을 나타냅니다. 자세한 내용은 참조 하세요 [Azure AD B2C: 질문과 대답 (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs)합니다.

웹 Api에는 사용자 인터페이스가 없습니다, 이후 이러한 사항을 Azure AD B2C와 같은 보안 토큰 서비스에 사용자를 리디렉션할 수 없습니다. 대신 API 호출에서 이미 Azure AD B2C를 사용 하 여 사용자를 인증 하는 앱에서 전달자 토큰을 전달 됩니다. 다음 API는 직접적인 사용자 개입 없이 토큰 유효성을 검사 합니다.

이 자습서에 설명 하는 방법.

> [!div class="checklist"]
> * Azure Active Directory B2C 테 넌 트를 만듭니다.
> * Azure AD B2C에서 웹 API를 등록 합니다.
> * 인증에 대 한 Azure AD B2C 테 넌 트를 사용 하도록 구성 된 Web API를 만들려면 Visual Studio를 사용 합니다.
> * Azure AD B2C 테 넌 트의 동작을 제어 하는 정책을 구성 합니다.
> * 토큰을 검색 하 고 웹 API에 대 한 요청을 수행 하는 데 사용 하는 로그인 대화 상자를 표시 하는 웹 앱을 시뮬레이션 하기 위해 Postman 사용.

## <a name="prerequisites"></a>Prerequisites

다음은이 연습에 필요한:

* [Microsoft Azure 구독](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [Postman](https://www.getpostman.com/postman)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C 테 넌 트 만들기

Azure AD B2C 테 넌 트 만들기 [설명서에 설명 된 대로](/azure/active-directory-b2c/active-directory-b2c-get-started)입니다. 메시지가 표시 되 면 Azure 구독을 사용 하 여 테 넌 트 연결는 선택 사항이 자습서에 대 한 합니다.

## <a name="configure-a-sign-up-or-sign-in-policy"></a>등록 또는 로그인 정책 구성

Azure AD B2C 설명서에서 절차 [등록 또는 로그인 정책 만들기](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)합니다. 정책 이름을 **SiUpIn**합니다.  에 대 한 설명서에 제공 된 예제 값을 사용할 **Id 공급자**를 **등록 특성**, 및 **응용 프로그램 클레임**합니다. 사용 하는 **지금 실행** 설명서에 설명 된 대로 정책을 테스트 하려면 단추는 선택 사항입니다.

## <a name="register-the-api-in-azure-ad-b2c"></a>Azure AD B2C에서 API 등록

새로 만든 Azure AD B2C 테 넌 트를 사용 하 여 API를 등록 [설명서에 나와 있는 단계](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) 아래 합니다 **웹 API 등록** 섹션입니다.

다음 값을 사용합니다.

| 설정                       | 값               | 참고                                                                                  |
|-------------------------------|---------------------|----------------------------------------------------------------------------------------|
| **이름**                      | *{0} API 이름}*        | 입력 한 **이름을** 소비자에 게 앱을 설명 하는 앱에 대 한 합니다.                     |
| **웹 앱/웹 API 포함** | 예                 |                                                                                        |
| **암시적 흐름 허용**       | 예                 |                                                                                        |
| **회신 URL**                 | `https://localhost` | 회신 Url은 Azure AD B2C 앱이 요청한 토큰을 반환 하는 위치 끝점입니다. |
| **앱 ID URI**                | *api*               | URI는 실제 주소를 확인할 필요가 없습니다. 만 고유 해야 합니다.     |
| **네이티브 클라이언트 포함**     | 아니요                  |                                                                                        |

API를 등록 한 후에 테 넌 트에서 앱 및 Api의 목록이 표시 됩니다. 이전에 등록 된 API를 선택 합니다. 선택 합니다 **복사** 아이콘의 오른쪽에는 **응용 프로그램 ID** 클립보드에 복사 하는 필드입니다. 선택 **게시 된 범위** 기본값을 확인 하 고 *user_impersonation* 범위가 존재 합니다.

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Visual Studio에서 ASP.NET Core 앱 만들기

Visual Studio 웹 응용 프로그램 템플릿은 인증에 Azure AD B2C 테 넌 트를 사용 하도록 구성할 수 있습니다.

Visual Studio에서 다음을 수행합니다.

1. 새 ASP.NET Core 웹 애플리케이션을 만듭니다. 
2. 선택 **Web API** 템플릿 목록에서.
3. 선택 된 **인증 변경** 단추입니다.

    ![인증 변경 단추](./azure-ad-b2c-webapi/change-auth-button.png)

4. 에 **인증 변경** 대화 상자에서 **개별 사용자 계정** > **클라우드에서 기존 사용자 저장소에 연결**합니다.

    ![인증 대화 상자 변경](./azure-ad-b2c-webapi/change-auth-dialog.png)

5. 다음 값으로 양식을 완성 합니다.

    | 설정                       | 값                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **도메인 이름**               | *{B2C 테 넌 트의 도메인 이름}*                |
    | **응용 프로그램 ID**            | *{응용 프로그램 ID 클립보드에서 붙여넣을}*       |
    | **등록 또는 로그인 정책** | B2C_1_SiUpIn                                          |

    선택 **확인** 닫으려면 합니다 **인증 변경** 대화 합니다. 선택 **확인** 웹 앱을 만듭니다.

Visual Studio 라는 컨트롤러를 사용 하 여 web API를 만듭니다 *ValuesController.cs* 는 GET 요청에 대 한 하드 코드 된 값을 반환 합니다. 클래스는 [권한 부여](xref:security/authorization/simple) 특성으로 표시 되므로 모든 요청에 인증이 필요 합니다.

## <a name="run-the-web-api"></a>Web API 실행

Visual Studio에서 API를 실행 합니다. Visual Studio는 API의 루트 URL을 가리키는 브라우저를 시작 합니다. 주소 표시줄에서 URL을 확인 하 고 백그라운드에서 실행 되는 API를 그대로 둡니다.

> [!NOTE]
> 컨트롤러의 루트 URL에 대해 정의 된 없음 이므로 브라우저 404 (찾을 수 없음 페이지) 오류를 표시할 수 있습니다. 이는 예상된 동작입니다.

## <a name="use-postman-to-get-a-token-and-test-the-api"></a>Postman을 사용 하 여 토큰 가져오기 및 API 테스트

[Postman](https://getpostman.com/postman) Api 웹 테스트에 대 한 도구입니다. 이 자습서에서는 Postman 사용자 대신 웹 API에 액세스 하는 웹 앱을 시뮬레이션 합니다.

### <a name="register-postman-as-a-web-app"></a>Postman에 웹 앱으로 등록

Azure AD B2C 테 넌 트에서 토큰을 가져옵니다는 웹 앱을 시뮬레이션 하는 Postman, 때문에 웹 앱으로 테 넌 트에 등록 되어야 합니다. Postman을 사용 하 여 등록 [설명서에 나와 있는 단계](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) 아래 합니다 **웹 앱을 등록** 섹션입니다. 중지 된 **웹 앱 클라이언트 비밀 만들기** 섹션입니다. 클라이언트 암호를이 자습서에 필요 하지 않습니다. 

다음 값을 사용합니다.

| 설정                       | 값                            | 참고                           |
|-------------------------------|----------------------------------|---------------------------------|
| **이름**                      | Postman                          |                                 |
| **웹 앱/웹 API 포함** | 예                              |                                 |
| **암시적 흐름 허용**       | 예                              |                                 |
| **회신 URL**                 | `https://getpostman.com/postman` |                                 |
| **앱 ID URI**                | *{비워}*                  | 이 자습서에 필요 하지 않습니다. |
| **네이티브 클라이언트 포함**     | 아니요                               |                                 |

새로 등록 된 웹 앱 사용자를 대신 하 여 웹 API에 액세스 권한이 필요 합니다.  

1. 선택 **Postman** 앱과 선택 목록의 **API 액세스** 왼쪽 메뉴에서.
1. 선택 **+ 추가**합니다.
1. 에 **API 선택** 드롭다운에서 웹 API의 이름을 선택 합니다.
1. 에 **범위 선택** 드롭다운에서 모든 범위 선택 되어 있는지 확인 합니다.
1. 선택 **확인**합니다.

전달자 토큰을 얻어야 하는 데 필요한 것 처럼 Postman 앱의 응용 프로그램 ID를 note 합니다.

### <a name="create-a-postman-request"></a>Postman 요청 만들기

Postman을 시작 합니다. Postman 기본적으로 표시 합니다 **새로 만들기** 대화 시작 시. 대화 상자가 표시 되어 있지 않으면 선택 합니다 **+ 새로 만들기** 왼쪽 위에 있는 단추입니다.

**새로 만들기** 대화 상자:

1. 선택 **요청**합니다.

    ![요청 단추](./azure-ad-b2c-webapi/postman-create-new.png)

2. 입력 *값 가져오기* 에 **요청 이름** 상자입니다.
3. 선택 **+ 컬렉션 만들기** 요청을 저장 하는 것에 대 한 새 컬렉션을 만들려고 합니다. 컬렉션의 이름을 *ASP.NET Core 자습서* 한 다음 확인 표시를 선택 합니다.

    ![새 컬렉션 만들기](./azure-ad-b2c-webapi/postman-create-collection.png)

4. 선택 된 **ASP.NET Core 자습서로 저장** 단추입니다.

### <a name="test-the-web-api-without-authentication"></a>인증 없이 web API 테스트

된 web API에 인증이 필요한 경우를 확인 하려면 먼저 인증 없이 요청을 확인 합니다.

1. 에 **요청 URL 입력** 상자에 대 한 URL을 입력 `ValuesController`합니다. URL이 사용 하 여 브라우저에 표시 된 것과 동일 **api/값** 추가 합니다. 예를 들어 `https://localhost:44375/api/values`과 같은 형식입니다.
2. 선택 된 **보낼** 단추입니다.
3. 응답의 상태가 *401 권한 없음*합니다.

    ![401 권한 없음된 응답](./azure-ad-b2c-webapi/postman-401-status.png)

> [!IMPORTANT]
> "응답을 받을 수 없습니다." 오류가 표시 되 면 [Postman 설정](https://learning.getpostman.com/docs/postman/launching_postman/settings)에서 SSL 인증서 확인을 사용 하지 않도록 설정 해야 할 수 있습니다.

### <a name="obtain-a-bearer-token"></a>전달자 토큰을 가져옵니다

Web API에 요청을 인증된 하는 전달자 토큰을 필요 합니다. Postman을 사용 하면 쉽게 Azure AD B2C 테 넌 트에 로그인 하 고 토큰을 가져옵니다.

1. 에 **권한 부여** 탭을 **형식** 드롭다운 **OAuth 2.0**합니다. 에 **권한 부여 데이터를 추가할** 드롭다운 **요청 헤더**합니다. 선택 **새 액세스 토큰 가져오기**합니다.

    ![설정 사용 하 여 권한 부여 탭](./azure-ad-b2c-webapi/postman-auth-tab.png)

2. 완료 합니다 **새 액세스 토큰 가져오기** 같이 대화 상자:

   |                설정                 |                                             값                                             |                                                                                                                                    참고                                                                                                                                     |
   |----------------------------------------|-----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |      **토큰 이름**       |                                          *{0} 토큰 이름}*                                       |                                                                                                                   토큰에 대 한 설명이 포함 된 이름을 입력 합니다.                                                                                                                    |
   |      **권한 부여 유형**       |                                           암시적 방법                                            |                                                                                                                                                                                                                                                                              |
   |     **콜백 URL**      |                                 `https://getpostman.com/postman`                              |                                                                                                                                                                                                                                                                              |
   |       **인증 URL**        | `https://login.microsoftonline.com/{tenant domain name}/oauth2/v2.0/authorize?p=B2C_1_SiUpIn` |  바꿉니다 *{테 넌 트 도메인 이름}* 테 넌 트의 도메인 이름입니다. **중요**:이 URL에 있는 내용으로 동일한 도메인 이름이 있어야 `AzureAdB2C.Instance` 웹 API의 *appsettings.json* 파일입니다. 참고&dagger;합니다.                                                  |
   |       **클라이언트 ID**       |                *{Postman 앱의 입력 **응용 프로그램 ID**}*                              |                                                                                                                                                                                                                                                                              |
   |         **범위**         |         `https://{tenant domain name}/{api}/user_impersonation openid offline_access`       | 바꿉니다 *{테 넌 트 도메인 이름}* 테 넌 트의 도메인 이름입니다. 바꿉니다 *{api}* 앱 ID URI를 사용 하 여 지정한 웹 API 처음 등록할 때 (이때 `api`). URL 패턴은: `https://{tenant}.onmicrosoft.com/{api-id-uri}/{scope name}`합니다.         |
   |         **상태**         |                                      *{비워}*                                          |                                                                                                                                                                                                                                                                              |
   | **클라이언트 인증** |                                본문에 클라이언트 자격 증명 보내기                                |                                                                                                                                                                                                                                                                              |

    > [!NOTE]
    > &dagger; 두 가지 가능한 Url을 표시 하는 Azure Active Directory B2C 포털에서 정책 설정 대화 상자: 형식으로 하나 `https://login.microsoftonline.com/`{테 넌 트 도메인 이름} / {추가 경로 정보} 및 형식으로 기타 `https://{tenant name}.b2clogin.com/`{테 넌 트 도메인 이름} / {추가 경로 정보}입니다. 있기 **중요 한** 도메인에서 발견 되는 `AzureAdB2C.Instance` web API에서에서 *appsettings.json* 파일이 웹 앱에서 사용 하는 것을 일치 *appsettings.json* 파일입니다. Postman에서 인증 URL 필드에 사용 되는 동일한 도메인입니다. Visual Studio 포털에서 표시 되는 항목 보다 약간 다른 URL 형식을 사용 하는 note 합니다. 일치 하는 도메인으로 URL을 사용할 수 있습니다.

3. 선택 된 **토큰 요청** 단추입니다.

4. Postman에서 Azure AD B2C 테 넌 트가 로그인 대화 상자를 포함 하는 새 창을 엽니다. (정책 테스트를 만든) 하는 경우 기존 계정으로 로그인 하거나 선택 **지금 등록** 새 계정을 만들 수 있습니다. 합니다 **암호를 잊으셨나요?** 잊어버린된 암호 재설정 링크를 사용 합니다.

5. 창이 닫힌 성공적으로 로그인 한 후 및 **액세스 토큰 관리** 대화 상자가 나타납니다. 선택한 아래쪽까지 아래로 스크롤합니다 합니다 **사용 하 여 토큰** 단추입니다.

    !["토큰 사용" 단추를 찾을 수 있는 위치](./azure-ad-b2c-webapi/postman-access-token.png)

### <a name="test-the-web-api-with-authentication"></a>인증을 사용 하 여 web API 테스트

선택 합니다 **보낼** 단추 요청을 다시 보내도록 합니다. 이 경우 응답 상태는 *200 확인* JSON 페이로드는 응답에 표시 됩니다 **본문** 탭 합니다.

![페이로드 및 성공 상태](./azure-ad-b2c-webapi/postman-success.png)

## <a name="next-steps"></a>다음 단계

본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.

> [!div class="checklist"]
> * Azure Active Directory B2C 테 넌 트를 만듭니다.
> * Azure AD B2C에서 웹 API를 등록 합니다.
> * 인증에 대 한 Azure AD B2C 테 넌 트를 사용 하도록 구성 된 Web API를 만들려면 Visual Studio를 사용 합니다.
> * Azure AD B2C 테 넌 트의 동작을 제어 하는 정책을 구성 합니다.
> * 토큰을 검색 하 고 웹 API에 대 한 요청을 수행 하는 데 사용 하는 로그인 대화 상자를 표시 하는 웹 앱을 시뮬레이션 하기 위해 Postman 사용.

API를 학습 하 여 개발을 계속 합니다.

* [ASP.NET Core 웹 앱을 Azure AD B2C를 사용 하 여 보안](xref:security/authentication/azure-ad-b2c)합니다.
* [Azure AD B2C를 사용 하 여.NET 웹 앱에서.NET web API 호출](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet)합니다.
* [Azure AD B2C 사용자 인터페이스](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)합니다.
* [암호 복잡성 요구 사항을 구성](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)합니다.
* [Multi-factor authentication 사용](/azure/active-directory-b2c/active-directory-b2c-reference-mfa)합니다.
* 와 같은 추가 id 공급자를 구성 [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app)합니다 [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app)를 [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), 등입니다.
* [Azure AD Graph API를 사용 하 여](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) 를 Azure AD B2C 테 넌 트에서 그룹 멤버 자격 등의 추가 사용자 정보를 검색 합니다.
