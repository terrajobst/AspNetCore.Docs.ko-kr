---
title: ASP.NET Core를 사용 하 여 Microsoft 계정 외부 로그인 설정
author: rick-anderson
description: 이 샘플에서는 기존 ASP.NET Core 앱에 Microsoft 계정 사용자 인증의 통합을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 5/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 1c78cc957b6ff77c91c8ca4aef59a1cacd85a8ca
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517086"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>ASP.NET Core를 사용 하 여 Microsoft 계정 외부 로그인 설정

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 샘플에서 만든 ASP.NET Core 2.2 프로젝트를 사용 하 여 Microsoft 계정으로 로그인 할 수 있도록 하는 방법을 보여 줍니다.는 [이전 페이지](xref:security/authentication/social/index)합니다.

## <a name="create-the-app-in-microsoft-developer-portal"></a>Microsoft 개발자 포털에서 앱 만들기

* 로 이동 합니다 [Azure portal-앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지를 만들거나 Microsoft 계정으로 로그인 합니다.

Microsoft 계정이 없다면 선택 **만드십시오**합니다. 로그인 한 후 리디렉션됩니다 합니다 **앱 등록** 페이지:

* 선택 **새 등록**
* 입력 한 **이름을**입니다.
* 에 대 한 옵션을 선택 **지원 되는 계정 유형**합니다.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* 아래 **리디렉션 URI**를 사용 하 여 개발 URL 입력 `/signin-microsoft` 추가 합니다. 예를 들어, `https://localhost:44389/signin-microsoft`을 입력합니다. 이 샘플의 뒷부분에 나오는 구성 Microsoft 인증 체계에서 요청을 자동으로 처리 됩니다 `/signin-microsoft` OAuth 흐름을 구현 하는 경로입니다.
* 선택 **등록**

### <a name="create-client-secret"></a>클라이언트 비밀 만들기

* 왼쪽된 창에서 선택 **인증서 및 비밀**합니다.
* 아래 **클라이언트 비밀**, 선택 **새 클라이언트 암호**

  * 클라이언트 암호에 대 한 설명을 추가 합니다.
  * 선택 된 **추가** 단추입니다.

* 아래 **클라이언트 비밀**, 클라이언트 암호의 값을 복사 합니다.

> [!NOTE]
> URI 세그먼트 `/signin-microsoft` Microsoft 인증 공급자의 기본 콜백으로 설정 됩니다. 상속을 통해 Microsoft 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다 [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 의 속성을 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) 클래스입니다.

## <a name="store-the-microsoft-client-id-and-client-secret"></a>Microsoft 클라이언트 ID 및 클라이언트 암호를 저장 합니다.

안전 하 게 저장 하려면 다음 명령을 실행 `ClientId` 하 고 `ClientSecret` 사용 하 여 [암호 관리자](xref:security/app-secrets):

```console
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

Microsoft와 같은 중요 한 설정이 연결 `ClientId` 하 고 `ClientSecret` 사용 하 여 응용 프로그램 구성에는 [암호 관리자](xref:security/app-secrets)합니다. 이 샘플에서는 토큰 이름을 `Authentication:Microsoft:ClientId` 고 `Authentication:Microsoft:ClientSecret`입니다.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Microsoft 계정 인증 구성

Microsoft 계정 서비스에 추가 합니다 `ConfigureServices` 의 메서드 *Startup.cs* 파일:

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

참조 된 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) Microsoft 계정 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조. 이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.

## <a name="sign-in-with-microsoft-account"></a>Microsoft 계정으로 로그인

실행을 클릭 하 고 **로그인**합니다. Microsoft에 로그인 하는 옵션이 표시 됩니다. Microsoft를 클릭 하면 리디렉션됩니다 Microsoft 인증에 대 한 합니다. (아직 로그인 하지 않은) 하는 경우 Microsoft 계정으로 로그인 한 후 앱 정보에 액세스할 수 있도록 라는 메시지가 표시 됩니다.

탭 **예** 및 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.

이제 Microsoft 자격 증명을 사용 하 여 로그인 됩니다.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>문제 해결

* Microsoft 계정 공급자는 로그인 오류 페이지로 리디렉션됩니다, 하는 경우 오류 제목과 설명을 쿼리 문자열 매개 변수 바로 다음을 유의 합니다 `#` (해시 태그) Uri에 있습니다.

  가장 일반적인 원인은 응용 프로그램의 모든 일치 하지 않는 Uri 오류 메시지는 Microsoft 인증에 문제가 있음을 나타낼 것 처럼 보이지만, 합니다 **리디렉션 Uri** 에 대해 지정 된 된 **웹** 플랫폼 .
* 호출 하 여 구성 되어 있지 않으면 Identity `services.AddIdentity` 에 `ConfigureServices`를 인증 하려고 하면 *ArgumentException: 'SignInScheme' 옵션을 제공 해야*합니다. 이 샘플에 사용 되는 프로젝트 템플릿이이 수행 되도록 보장 합니다.
* 사이트 데이터베이스를 초기 마이그레이션을 적용 하 여 만들어지지 않은, 경우 받습니다 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다.* 오류입니다. 탭 **마이그레이션 적용** 데이터베이스를 만들고 오류 지 나 새로 고침 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서에서는 Microsoft를 사용 하 여 인증 하는 보여 주었습니다. 에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다 합니다 [이전 페이지](xref:security/authentication/social/index)합니다.

* Azure 웹 앱에 웹 사이트를 게시 하면 새 클라이언트 만들기 암호는 Microsoft 개발자 포털에서.

* 설정 된 `Authentication:Microsoft:ClientId` 및 `Authentication:Microsoft:ClientSecret` Azure portal에서 응용 프로그램 설정 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.