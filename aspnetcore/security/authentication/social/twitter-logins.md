---
title: ASP.NET Core를 사용 하 여 외부 로그인 설치 twitter
author: rick-anderson
description: 이 자습서에서는 기존 ASP.NET Core 앱에 Twitter 계정 사용자 인증의 통합을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: d816ed27898639b0af6896a51ac035d5526c5d29
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814067"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>ASP.NET Core를 사용 하 여 외부 로그인 설치 twitter

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 샘플에서는 사용자가 사용 하도록 설정 하는 방법을 보여 줍니다 [Twitter 계정으로 로그인](https://dev.twitter.com/web/sign-in/desktop-browser) 에서 만든 샘플 ASP.NET Core 2.2 프로젝트를 사용 하 여 [이전 페이지](xref:security/authentication/social/index)합니다.

## <a name="create-the-app-in-twitter"></a>Twitter에서 앱 만들기

* 이동할 [ https://apps.twitter.com/ ](https://apps.twitter.com/) 에 로그인 합니다. Twitter 계정에 아직 없는 경우 사용 합니다 **[지금 등록](https://twitter.com/signup)** 만들려면 링크 합니다.

* 탭 **Create New App** 응용 프로그램을 작성 하 고 **이름**를 **설명을** 및 공용 **웹 사이트** (두 일 수 있는 때까지 URI 등록 된 도메인 이름):

* 개발 URI를 입력으로 `/signin-twitter` 에 추가 합니다 **유효한 OAuth 리디렉션 Uri** 필드 (예를 들어: `https://webapp128.azurewebsites.net/signin-twitter`). 이 샘플의 뒷부분에 나오는 구성 된 Twitter 인증 체계에서 요청을 자동으로 처리 됩니다 `/signin-twitter` OAuth 흐름을 구현 하는 경로입니다.

  > [!NOTE]
  > URI 세그먼트 `/signin-twitter` Twitter 인증 공급자의 기본 콜백으로 설정 됩니다. 상속을 통해 Twitter 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다 [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 의 속성을 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) 클래스입니다.

* 폼의 나머지 부분을 입력 하 고 탭 **Twitter 응용 프로그램을 만드는**합니다. 새 응용 프로그램 세부 정보가 표시 됩니다.

## <a name="storing-twitter-consumer-api-key-and-secret"></a>Twitter 소비자 API 키 및 암호를 저장합니다.

안전 하 게 저장 하려면 다음 명령을 실행 `ClientId` 하 고 `ClientSecret` 사용 하 여 [암호 관리자](xref:security/app-secrets):

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerAPISecret <Secret>
```

Twitter와 같은 중요 한 설정이 연결 `Consumer Key` 하 고 `Consumer Secret` 사용 하 여 응용 프로그램 구성에는 [암호 관리자](xref:security/app-secrets)합니다. 이 샘플에서는 토큰 이름을 `Authentication:Twitter:ConsumerKey` 고 `Authentication:Twitter:ConsumerSecret`입니다.

이러한 토큰을 찾을 수 있습니다 합니다 **Keys and Access Tokens** 새 Twitter 응용 프로그램을 만든 후 탭:

## <a name="configure-twitter-authentication"></a>Twitter 인증 구성

Twitter 서비스를 추가 합니다 `ConfigureServices` 의 메서드 *Startup.cs* 파일:

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

참조 된 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) Twitter 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조. 이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.

## <a name="sign-in-with-twitter"></a>Sign in with Twitter

앱을 실행 하 고 선택 **로그인**합니다. Twitter 로그인 하는 옵션이 표시 됩니다.

클릭할 **Twitter** 인증에 대 한 Twitter에 리디렉션합니다.

Twitter 자격 증명을 입력 한 후 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.

이제 Twitter 자격 증명을 사용 하 여 로그인 됩니다.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>문제 해결

* **ASP.NET Core 2.x만:** 호출 하 여 구성 되어 있지 않으면 Identity `services.AddIdentity` 에 `ConfigureServices`를 인증 하려고 하면 *ArgumentException: 'SignInScheme' 옵션을 제공 해야*합니다. 이 샘플에 사용 되는 프로젝트 템플릿이이 수행 되도록 보장 합니다.
* 사이트 데이터베이스를 초기 마이그레이션을 적용 하 여 만들어지지 않은, 경우 받습니다 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다.* 오류입니다. 탭 **마이그레이션 적용** 데이터베이스를 만들고 오류 지 나 새로 고침 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서에서는 Twitter를 사용 하 여 인증 하는 방법에 대해 설명 했습니다. 에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다 합니다 [이전 페이지](xref:security/authentication/social/index)합니다.

* 다시 설정 해야 Azure 웹 앱에 웹 사이트를 게시 하면는 `ConsumerSecret` Twitter 개발자 포털에서 합니다.

* 설정 된 `Authentication:Twitter:ConsumerKey` 및 `Authentication:Twitter:ConsumerSecret` Azure portal에서 응용 프로그램 설정 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.