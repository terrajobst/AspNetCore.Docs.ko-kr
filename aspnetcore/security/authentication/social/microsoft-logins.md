---
title: ASP.NET Core를 사용 하 여 Microsoft 계정 외부 로그인 설정
author: rick-anderson
description: 이 샘플에서는 Microsoft 계정 사용자 인증을 기존 ASP.NET Core 앱에 통합 하는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/4/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: ddaae1a25a1dcf167ffae0f24b480e2cde6aca5b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652071"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>ASP.NET Core를 사용 하 여 Microsoft 계정 외부 로그인 설정

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 샘플에서는 사용자가 [이전 페이지](xref:security/authentication/social/index)에서 만든 ASP.NET Core 3.0 프로젝트를 사용 하 여 Microsoft 계정에 로그인 할 수 있도록 하는 방법을 보여 줍니다.

## <a name="create-the-app-in-microsoft-developer-portal"></a>Microsoft 개발자 포털에서 앱 만들기

* 프로젝트에 [AspNetCore. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet 패키지를 추가 합니다.
* [Azure Portal-앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동 하 고 Microsoft 계정를 만들거나 로그인 합니다.

Microsoft 계정 없는 경우 **만들기**를 선택 합니다. 로그인 하면 **앱 등록** 페이지로 리디렉션됩니다.

* **새 등록** 선택
* **이름**을 입력합니다.
* **지원 되는 계정 유형에**대 한 옵션을 선택 합니다.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* **URI 리디렉션**에서 `/signin-microsoft` 추가 된 개발 URL을 입력 합니다. `https://localhost:5001/signin-microsoft`)을 입력합니다. 이 샘플의 뒷부분에서 구성 된 Microsoft 인증 체계는 OAuth 흐름을 구현 하는 `/signin-microsoft` 경로에서 요청을 자동으로 처리 합니다.
* **등록** 선택

### <a name="create-client-secret"></a>클라이언트 암호 만들기

* 왼쪽 창에서 **인증서 & 암호**를 선택 합니다.
* **클라이언트 암호**에서 **새 클라이언트 암호** 를 선택 합니다.

  * 클라이언트 암호에 대 한 설명을 추가 합니다.
  * **추가** 단추를 선택합니다.

* **클라이언트 암호**에서 클라이언트 암호의 값을 복사 합니다.

> [!NOTE]
> `/signin-microsoft` URI 세그먼트가 Microsoft 인증 공급자의 기본 콜백으로 설정 되어 있습니다. [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) 클래스의 상속 된 [Remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성을 통해 Microsoft 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다.

## <a name="store-the-microsoft-client-id-and-client-secret"></a>Microsoft 클라이언트 ID 및 클라이언트 암호 저장

다음 명령을 실행 하 여 [비밀 Manager](xref:security/app-secrets)를 사용 하 `ClientId` 및 `ClientSecret`를 안전 하 게 저장 합니다.

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

[비밀 관리자](xref:security/app-secrets)를 사용 하 여 Microsoft `ClientId` 및 `ClientSecret`와 같은 중요 한 설정을 응용 프로그램 구성에 연결 합니다. 이 샘플의 목적에 맞게 토큰의 이름을 `Authentication:Microsoft:ClientId` 하 고 `Authentication:Microsoft:ClientSecret`합니다.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Microsoft 계정 인증 구성

`Startup.ConfigureServices`에 Microsoft 계정 서비스를 추가 합니다.

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Microsoft 계정 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API 참조를 참조 하세요. 이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.

## <a name="sign-in-with-microsoft-account"></a>Microsoft에 로그인 계정

앱을 실행 하 고 **로그인**을 클릭 합니다. Microsoft에 로그인 할 수 있는 옵션이 나타납니다. Microsoft를 클릭 하면 인증을 위해 Microsoft로 리디렉션됩니다. Microsoft 계정을 사용 하 여 로그인 한 후에 앱에서 사용자의 정보에 액세스 하도록 허용 하 라는 메시지가 표시 됩니다.

**예** 를 탭 하면 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.

이제 Microsoft 자격 증명을 사용 하 여 로그인 됩니다.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>문제 해결

* Microsoft 계정 공급자가 로그인 오류 페이지로 리디렉션되는 경우 Uri에서 `#` (해시 태그) 바로 다음에 나오는 오류 제목 및 설명 쿼리 문자열 매개 변수를 확인 합니다.

  오류 메시지가 Microsoft 인증 문제를 나타내는 것 처럼 보이지만 가장 일반적인 원인은 **웹** 플랫폼에 지정 된 **리디렉션 uri** 와 일치 하지 않는 응용 프로그램 uri입니다.
* `ConfigureServices`에서 `services.AddIdentity`를 호출 하 여 Id가 구성 되지 않은 경우 인증을 시도 하면 ArgumentException이 발생 합니다. *' SignInScheme ' 옵션을 제공 해야*합니다. 이 샘플에서 사용 되는 프로젝트 템플릿은이 작업이 수행 되도록 합니다.
* 초기 마이그레이션을 적용 하 여 사이트 데이터베이스를 만들지 않은 경우 요청 오류를 *처리 하는 동안 데이터베이스 작업이 실패* 하 게 됩니다. **마이그레이션 적용** 을 탭 하 여 데이터베이스를 만들고 새로 고쳐 오류를 계속 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서에서는 Microsoft를 사용 하 여 인증할 수 있는 방법을 살펴보았습니다. [위의 페이지](xref:security/authentication/social/index)에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다.

* Azure 웹 앱에 웹 사이트를 게시 한 후에는 Microsoft 개발자 포털에서 새 클라이언트 암호를 만듭니다.

* Azure Portal에서 `Authentication:Microsoft:ClientId` 및 `Authentication:Microsoft:ClientSecret`를 응용 프로그램 설정으로 설정 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.
