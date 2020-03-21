---
title: ASP.NET Core에서 Facebook 외부 로그인 설정
author: rick-anderson
description: 기존 ASP.NET Core 앱에 Facebook 계정 사용자 인증을 통합 하는 방법을 보여 주는 코드 예제를 보여 주는 자습서입니다.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: bb26a27f026e744c7d4925aa2281bf0625fff8a2
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989788"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NET Core에서 Facebook 외부 로그인 설정

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서의 코드 예제에서는 [이전 페이지](xref:security/authentication/social/index)에서 만든 샘플 ASP.NET Core 3.0 프로젝트를 사용 하 여 사용자가 Facebook 계정으로 로그인 할 수 있도록 설정 하는 방법을 보여 줍니다. 먼저 [공식 단계](https://developers.facebook.com)를 수행 하 여 FACEBOOK 앱 ID를 만듭니다.

## <a name="create-the-app-in-facebook"></a>Facebook에서 앱 만들기

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet 패키지를 프로젝트에 추가 합니다.

* [Facebook 개발자 앱](https://developers.facebook.com/apps/) 페이지로 이동 하 여 로그인 합니다. Facebook 계정이 아직 없는 경우 로그인 페이지에서 **facebook에 등록** 링크를 사용 하 여 하나를 만듭니다.  Facebook 계정이 있으면 지침에 따라 Facebook 개발자로 등록 합니다.

* **내 앱** 메뉴에서 **앱 만들기** 를 선택 하 여 새 앱 ID를 만듭니다.

   ![Facebook 개발자 포털에 대 한 Microsoft Edge에서 열린](index/_static/FBMyApps.png)

* 양식을 작성 하 고 **앱 ID 만들기** 단추를 탭 합니다.

  ![새 앱 ID 양식 만들기](index/_static/FBNewAppId.png)

* 새 앱 카드에서 **제품 추가**를 선택 합니다.  **Facebook 로그인** 카드에서 **설정** 을 클릭 합니다. 

  ![제품 설치 페이지](index/_static/FBProductSetup.png)

* 첫 번째 페이지로 **플랫폼을 선택** 하 여 **빠른** 시작 마법사가 시작 됩니다. 왼쪽 아래에 있는 메뉴에서 **FaceBook 로그인** **설정** 링크를 클릭 하 여 이제 마법사를 무시 합니다.

  ![Skip 빠른 시작](index/_static/FBSkipQuickStart.png)

* **클라이언트 OAuth 설정** 페이지가 표시 됩니다.

  ![클라이언트 OAuth 설정 페이지](index/_static/FBOAuthSetup.png)

* **유효한 OAuth 리디렉션 uri** 필드에 */signin-facebook* 를 추가 하 여 개발 URI를 입력 합니다 (예: `https://localhost:44320/signin-facebook`). 이 자습서의 뒷부분에서 구성 된 Facebook 인증은 */signin-facebook* 경로에서 요청을 자동으로 처리 하 여 OAuth 흐름을 구현 합니다.

> [!NOTE]
> URI */signin-facebook* 는 facebook 인증 공급자의 기본 콜백으로 설정 됩니다. [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) 클래스의 상속 된 [Remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성을 통해 Facebook 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다.

* **변경 내용 저장**을 클릭합니다.

* 왼쪽 탐색 영역에서 **설정** > **기본** 링크를 클릭 합니다.

  이 페이지에서 `App ID` 및 `App Secret`을 기록해 둡니다. 다음 섹션에서 ASP.NET Core 응용 프로그램에 둘 다를 추가 합니다.

* 사이트를 배포할 때 **Facebook 로그인** 설정 페이지를 다시 방문 하 여 새 공용 URI를 등록 해야 합니다.

## <a name="store-the-facebook-app-id-and-secret"></a>Facebook 앱 ID 및 암호 저장

[비밀 관리자](xref:security/app-secrets)를 사용 하 여 FACEBOOK 앱 ID 및 비밀 값과 같은 중요 한 설정을 저장 합니다. 이 샘플에서는 다음 단계를 사용 합니다.

1. [비밀 저장소 사용](xref:security/app-secrets#enable-secret-storage)의 지침에 따라 비밀 저장소에 대 한 프로젝트를 초기화 합니다.
1. 비밀 키 `Authentication:Facebook:AppId` 및 `Authentication:Facebook:AppSecret`를 사용 하 여 로컬 암호 저장소에 중요 한 설정을 저장 합니다.

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Facebook 인증 구성

*Startup.cs* 파일의 `ConfigureServices` 메서드에 Facebook 서비스를 추가 합니다.

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Facebook 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API 참조를 참조 하세요. 구성 옵션을 사용할 수 있습니다.

* 사용자에 대 한 다른 정보를 요청 합니다.
* 로그인 환경을 사용자 지정 하는 쿼리 문자열 인수를 추가 합니다.

## <a name="sign-in-with-facebook"></a>Facebook으로 로그인

응용 프로그램을 실행 하 고 **로그인**을 클릭 합니다. Facebook으로 로그인 하는 옵션이 표시 됩니다.

![웹 응용 프로그램: 인증 되지 않은 사용자](index/_static/DoneFacebook.png)

**Facebook**을 클릭 하면 인증을 위해 facebook으로 리디렉션됩니다.

![Facebook 인증 페이지](index/_static/FBLogin.png)

Facebook 인증 기본적으로 공용 프로필 및 전자 메일 주소를 요청합니다.

![Facebook 인증 페이지 동의 화면](index/_static/FBLoginDone.png)

Facebook 자격 증명을 입력 한 후 전자 메일을 설정할 수 있는 사이트로 다시 리디렉션됩니다.

이제 Facebook 자격 증명을 사용 하 여 로그인 됩니다.

![웹 응용 프로그램: 사용자 인증](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>문제 해결

* **ASP.NET Core 2.x에만 해당:** `ConfigureServices`에서 `services.AddIdentity`를 호출 하 여 Id가 구성 되지 않은 경우 인증을 시도 하면 ArgumentException이 발생 합니다. *' SignInScheme ' 옵션을 제공 해야*합니다. 이 자습서에 사용 되는 프로젝트 템플릿이이 수행 되도록 보장 합니다.
* 초기 마이그레이션을 적용 하 여 사이트 데이터베이스를 만들지 않은 경우 *요청 오류를 처리 하는 동안 데이터베이스 작업이 실패 했습니다* . **마이그레이션 적용** 을 탭 하 여 데이터베이스를 만들고 새로 고쳐 오류를 계속 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서에서는 Facebook을 사용 하 여 인증 하는 보여 주었습니다. [위의 페이지](xref:security/authentication/social/index)에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다.

* Azure 웹 앱에 웹 사이트를 게시 한 후에는 Facebook 개발자 포털에서 `AppSecret`를 다시 설정 해야 합니다.

* Azure Portal에서 `Authentication:Facebook:AppId` 및 `Authentication:Facebook:AppSecret`를 응용 프로그램 설정으로 설정 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.
