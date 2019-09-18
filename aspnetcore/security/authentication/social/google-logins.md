---
title: ASP.NET Core에서 Google 외부 로그인 설정
author: rick-anderson
description: 이 자습서에서는 기존 ASP.NET Core 앱에 Google 계정 사용자 인증의 통합을 보여 줍니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/19/2019
uid: security/authentication/google-logins
ms.openlocfilehash: e12d831d2e0a5c9acae5ea41fb4187ad4ca6b0ea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082480"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>ASP.NET Core에서 Google 외부 로그인 설정

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

[레거시 Google + api는 2019 년 3 월 7 일에 종료 되었습니다](https://developers.google.com/+/api-shutdown). Google + 로그인 및 개발자는 새 Google 로그인 시스템으로 이동 해야 합니다. Google 인증에 대 한 ASP.NET Core 2.1 및 2.2 패키지가 변경 내용에 맞게 업데이트 되었습니다. ASP.NET Core에 대 한 자세한 내용과 임시 완화 정보는 [이 GitHub 문제](https://github.com/aspnet/AspNetCore/issues/6486)를 참조 하세요. 이 자습서는 새 설치 프로세스를 사용 하 여 업데이트 되었습니다.

이 자습서에서는 사용자가 [이전 페이지](xref:security/authentication/social/index)에서 만든 ASP.NET Core 2.2 프로젝트를 사용 하 여 Google 계정으로 로그인 할 수 있도록 하는 방법을 보여 줍니다.

## <a name="create-a-google-api-console-project-and-client-id"></a>Google API 콘솔 프로젝트 및 클라이언트 ID 만들기

* [Google 로그인을 웹 앱에 통합](https://developers.google.com/identity/sign-in/web/devconsole-project) 으로 이동 하 여 **프로젝트 구성**을 선택 합니다.
* **OAuth 클라이언트 구성** 대화 상자에서 **웹 서버**를 선택 합니다.
* **권한 있는 리디렉션 uri** 텍스트 항목 상자에서 리디렉션 uri를 설정 합니다. 예를 들면 `https://localhost:5001/signin-google`
* **클라이언트 ID** 및 **클라이언트 암호**를 저장 합니다.
* 사이트를 배포할 때 **Google 콘솔**에서 새 공용 url을 등록 합니다.

## <a name="store-google-clientid-and-clientsecret"></a>저장소 Google ClientID 및 ClientSecret

Google `Client ID` 과 같은 중요 한 설정 및 `Client Secret` [비밀 관리자](xref:security/app-secrets)를 저장 합니다. 이 자습서에서는 토큰 `Authentication:Google:ClientId` 의 이름을 및 `Authentication:Google:ClientSecret`으로 합니다.

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Api [콘솔](https://console.developers.google.com/apis/dashboard)에서 api 자격 증명과 사용 현황을 관리할 수 있습니다.

## <a name="configure-google-authentication"></a>Google 인증 구성

다음에 Google 서비스를 `Startup.ConfigureServices`추가 합니다.

[!code-csharp[](~/security/authentication/social/social-code/StartupGoogle.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Google로 로그인

* 앱을 실행 하 고 **로그인**을 클릭 합니다. Google을 사용 하 여 로그인 할 수 있는 옵션이 나타납니다.
* Google 단추를 **클릭 합니다 .이 단추는** 인증을 위해 google으로 리디렉션됩니다.
* Google 자격 증명을 입력 하면 웹 사이트로 다시 리디렉션됩니다.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Google 인증 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> 에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조를 참조 하세요. 이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.

## <a name="change-the-default-callback-uri"></a>기본 콜백 URI 변경

URI 세그먼트 `/signin-google` Google 인증 공급자의 기본 콜백으로 설정 됩니다. 상속을 통해 Google 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다 [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 의 속성을 [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) 클래스입니다.

## <a name="troubleshooting"></a>문제 해결

* 로그인이 작동 하지 않고 오류가 발생 하지 않는 경우 개발 모드로 전환 하 여 문제를 더 쉽게 디버깅할 수 있도록 합니다.
* 에서 `services.AddIdentity` *를 호출 하 여 id를 구성 하지 않은 경우 ArgumentException에서 결과를 인증 하려고 시도 합니다. `ConfigureServices` ' SignInScheme ' 옵션을 제공*해야 합니다. 이 자습서에 사용 되는 프로젝트 템플릿이이 수행 되도록 보장 합니다.
* 사이트 데이터베이스를 초기 마이그레이션을 적용 하 여 만들어지지 않은, 하는 경우 얻게 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다.* 오류입니다. **마이그레이션 적용** 을 선택 하 여 데이터베이스를 만들고 페이지를 새로 고쳐 오류를 계속 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서에서는 Google을 사용 하 여 인증 하는 보여 주었습니다. 에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다 합니다 [이전 페이지](xref:security/authentication/social/index)합니다.
* Azure에 앱을 게시 한 후에는 Google `ClientSecret` API 콘솔에서를 다시 설정 합니다.
* 설정 된 `Authentication:Google:ClientId` 및 `Authentication:Google:ClientSecret` Azure portal에서 응용 프로그램 설정 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.
