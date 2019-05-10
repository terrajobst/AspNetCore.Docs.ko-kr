---
title: ASP.NET Core에서 Google 외부 로그인 설정
author: rick-anderson
description: 이 자습서에서는 기존 ASP.NET Core 앱에 Google 계정 사용자 인증의 통합을 보여 줍니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 1/11/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 44c79b3279db7946b6d89a726bd3f5acfb5f51af
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895540"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>ASP.NET Core에서 Google 외부 로그인 설정

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

[레거시 Google + Api 2019 년 3 월 7 일부 터 종료](https://developers.google.com/+/api-shutdown)합니다. Google +에 로그인 하 고 개발자가 시스템에서 새 Google 로그인을 이동 해야 합니다. Google 인증에 대 한 ASP.NET Core 2.1 및 2.2 패키지 변경 내용에 맞게 업데이트 됩니다. 자세한 정보 및 ASP.NET Core에 대 한 임시 완화 [이 GitHub 문제](https://github.com/aspnet/AspNetCore/issues/6486)합니다. 이 자습서는 새로운 설치 프로세스를 사용 하 여 업데이트 되었습니다.

이 자습서에서 만든 ASP.NET Core 2.2 프로젝트를 사용 하 여 Google 계정으로 로그인 할 수 있도록 하는 방법을 보여 줍니다.는 [이전 페이지](xref:security/authentication/social/index)합니다.

## <a name="create-a-google-api-console-project-and-client-id"></a>Google API 콘솔 프로젝트와 클라이언트 ID 만들기

* 이동할 [통합 Google 로그인에 웹 앱](https://developers.google.com/identity/sign-in/web/devconsole-project) 선택한 **는 프로젝트 구성**합니다.
* 에 **OAuth 클라이언트 구성** 대화 상자에서 **웹 서버**합니다.
* 에 **권한이 부여 된 리디렉션 Uri** 텍스트 입력 상자 리디렉션 URI를 설정 합니다. 예를 들면 `https://localhost:5001/signin-google`과 같습니다.
* 저장 된 **클라이언트 ID** 하 고 **클라이언트 비밀**합니다.
* 사이트를 배포할 때 새 공용 url을 등록 합니다 **Google 콘솔**합니다.

## <a name="store-google-clientid-and-clientsecret"></a>저장소 Google ClientID 및 ClientSecret

Google 같은 중요 한 설정을 저장 `Client ID` 하 고 `Client Secret` 사용 하 여 합니다 [암호 관리자](xref:security/app-secrets)합니다. 이 자습서에서는 이름을 토큰 `Authentication:Google:ClientId` 고 `Authentication:Google:ClientSecret`:

```console
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

API 자격 증명 및에서 사용량을 관리할 수 있습니다 합니다 [API 콘솔](https://console.developers.google.com/apis/dashboard)합니다.

## <a name="configure-google-authentication"></a>Google 인증 구성

Google 서비스를 추가할 `Startup.ConfigureServices`합니다.

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Google로 로그인

* 앱을 실행 하 고 클릭 **로그인**합니다. Google로 로그인 하는 옵션이 표시 됩니다.
* 클릭 합니다 **Google** 단추를 인증에 대 한 Google에 리디렉션합니다.
* Google 자격 증명을 입력 한 후 웹 사이트로 다시 리디렉션됩니다.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

참조 된 [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) Google 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조. 이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.

## <a name="change-the-default-callback-uri"></a>기본 콜백 URI를 변경 합니다.

URI 세그먼트 `/signin-google` Google 인증 공급자의 기본 콜백으로 설정 됩니다. 상속을 통해 Google 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다 [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 의 속성을 [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) 클래스입니다.

## <a name="troubleshooting"></a>문제 해결

* 로그인 작동 하지 않습니다 하 고 오류를 가져오지 않음, 문제를 더 쉽게 디버그 하려면 개발 모드를 전환 합니다.
* 호출 하 여 구성 되어 있지 않으면 Identity `services.AddIdentity` 에 `ConfigureServices`에서 결과 인증 하는 동안, *ArgumentException: 'SignInScheme' 옵션을 제공 해야*합니다. 이 자습서에 사용 되는 프로젝트 템플릿이이 수행 되도록 보장 합니다.
* 사이트 데이터베이스를 초기 마이그레이션을 적용 하 여 만들어지지 않은, 하는 경우 얻게 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다.* 오류입니다. 탭 **마이그레이션 적용** 데이터베이스를 만들고 오류 지 나 새로 고침 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서에서는 Google을 사용 하 여 인증 하는 보여 주었습니다. 에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다 합니다 [이전 페이지](xref:security/authentication/social/index)합니다.
* Azure에 앱을 게시 하면 다시 설정 된 `ClientSecret` Google API 콘솔에서.
* 설정 된 `Authentication:Google:ClientId` 및 `Authentication:Google:ClientSecret` Azure portal에서 응용 프로그램 설정 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.
