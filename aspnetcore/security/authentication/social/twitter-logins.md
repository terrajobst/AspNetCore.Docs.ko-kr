---
title: ASP.NET Core로 Twitter 외부 로그인 설정
author: rick-anderson
description: 이 자습서에서는 Twitter 계정 사용자 인증을 기존 ASP.NET Core 앱에 통합 하는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 4710c033018710ce3620f8d7221ae2253b2c0b69
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172519"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>ASP.NET Core로 Twitter 외부 로그인 설정

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 샘플에서는 [이전 페이지](xref:security/authentication/social/index)에서 만든 샘플 ASP.NET Core 3.0 프로젝트를 사용 하 여 사용자가 [Twitter 계정으로 로그인](https://dev.twitter.com/web/sign-in/desktop-browser) 할 수 있게 하는 방법을 보여 줍니다.

## <a name="create-the-app-in-twitter"></a>Twitter에서 앱 만들기

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet 패키지를 프로젝트에 추가 합니다.

* [https://apps.twitter.com/](https://apps.twitter.com/) 로 이동 하 여 로그인 합니다. Twitter 계정이 아직 없는 경우 **[지금 등록](https://twitter.com/signup)** 링크를 사용 하 여 계정을 만드세요.

* **앱 만들기**를 선택합니다. **앱 이름**, **응용 프로그램 설명** 및 공개 **웹 사이트** URI를 입력 합니다 (도메인 이름을 등록할 때까지 임시 일 수 있음).

* **Twitter로 로그인 사용** 옆의 확인란을 선택 합니다.

* AspNetCore에는 기본적으로 사용자에 게 전자 메일 주소가 있어야 합니다. **권한** 탭으로 이동 하 고 **편집** 단추를 클릭 한 다음 **사용자에 게 전자 메일 주소 요청**옆의 확인란을 선택 합니다.

* **콜백 url** 필드에 추가 된 `/signin-twitter`를 사용 하 여 개발 URI를 입력 합니다 (예: `https://webapp128.azurewebsites.net/signin-twitter`). 이 샘플의 뒷부분에서 구성 된 Twitter 인증 스키마는 `/signin-twitter` 경로에서 요청을 자동으로 처리 하 여 OAuth 흐름을 구현 합니다.

  > [!NOTE]
  > `/signin-twitter` URI 세그먼트는 Twitter 인증 공급자의 기본 콜백으로 설정 됩니다. [Twitteroptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) 클래스의 상속 된 [Remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성을 통해 Twitter 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다.

* 양식의 나머지를 입력 하 고 **만들기**를 선택 합니다. 새 응용 프로그램 세부 정보가 표시 됩니다.

## <a name="storing-twitter-consumer-api-key-and-secret"></a>Twitter 소비자 API 키 및 암호 저장

다음 명령을 실행 하 여 [비밀 Manager](xref:security/app-secrets)를 사용 하 `ClientId` 및 `ClientSecret`를 안전 하 게 저장 합니다.

```dotnetcli
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

[암호 관리자](xref:security/app-secrets)를 사용 하 여 Twitter `Consumer Key` 및 `Consumer Secret`와 같은 중요 한 설정을 응용 프로그램 구성에 연결 합니다. 이 샘플의 목적에 맞게 토큰의 이름을 `Authentication:Twitter:ConsumerKey` 하 고 `Authentication:Twitter:ConsumerSecret`합니다.

이러한 토큰은 새 Twitter 응용 프로그램을 만든 후에 **키 및 액세스 토큰** 탭에서 찾을 수 있습니다.

## <a name="configure-twitter-authentication"></a>Twitter 인증 구성

*Startup.cs* 파일의 `ConfigureServices` 메서드에 Twitter 서비스를 추가 합니다.

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Twitter 인증에서 지원 되는 구성 옵션에 대 한 자세한 내용은 [Twitteroptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API 참조를 참조 하세요. 이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.

## <a name="sign-in-with-twitter"></a>Twitter로 로그인

앱을 실행 하 고 **로그인**을 선택 합니다. Twitter를 사용 하 여 로그인 하는 옵션이 표시 됩니다.

**Twitter** 로 리디렉션하여 인증을 위해 twitter를 클릭 합니다.

Twitter 자격 증명을 입력 하면 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.

이제 Twitter 자격 증명을 사용 하 여 로그인 됩니다.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>문제 해결

* **ASP.NET Core 2.x에만 해당:** `ConfigureServices`에서 `services.AddIdentity`를 호출 하 여 Id가 구성 되지 않은 경우 인증을 시도 하면 ArgumentException이 발생 합니다. *' SignInScheme ' 옵션을 제공 해야*합니다. 이 샘플에서 사용 되는 프로젝트 템플릿은이 작업이 수행 되도록 합니다.
* 초기 마이그레이션을 적용 하 여 사이트 데이터베이스를 만들지 않은 경우 요청 오류를 *처리 하는 동안 데이터베이스 작업이 실패* 하 게 됩니다. **마이그레이션 적용** 을 탭 하 여 데이터베이스를 만들고 새로 고쳐 오류를 계속 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서에서는 Twitter를 사용 하 여 인증할 수 있는 방법을 살펴보았습니다. [위의 페이지](xref:security/authentication/social/index)에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다.

* Azure 웹 앱에 웹 사이트를 게시 한 후 Twitter 개발자 포털에서 `ConsumerSecret`를 다시 설정 해야 합니다.

* Azure Portal에서 `Authentication:Twitter:ConsumerKey` 및 `Authentication:Twitter:ConsumerSecret`를 응용 프로그램 설정으로 설정 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.
