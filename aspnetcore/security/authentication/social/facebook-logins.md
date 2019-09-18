---
title: ASP.NET Core에서 Facebook 외부 로그인 설정
author: rick-anderson
description: 기존 ASP.NET Core 앱에 Facebook 계정 사용자 인증을 통합 하는 방법을 보여 주는 코드 예제를 보여 주는 자습서입니다.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/04/2019
uid: security/authentication/facebook-logins
ms.openlocfilehash: f7b21de7e5fe9d77804588280c3d8be9df8afee5
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082543"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NET Core에서 Facebook 외부 로그인 설정

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서의 코드 예제에서는 [이전 페이지](xref:security/authentication/social/index)에서 만든 샘플 ASP.NET Core 2.0 프로젝트를 사용 하 여 사용자가 Facebook 계정으로 로그인 할 수 있도록 설정 하는 방법을 보여 줍니다. 수행 하 여 Facebook 앱 ID를 만드는 것으로 시작 합니다 [공식 단계](https://developers.facebook.com)합니다.

## <a name="create-the-app-in-facebook"></a>Facebook에서 앱 만들기

* 로 이동 합니다 [Facebook 개발자 앱](https://developers.facebook.com/apps/) 페이지 하 고 로그인 합니다. 사용 하 여 Facebook 계정이 아직 없다면 합니다 **Facebook에 대 한 등록** 만들려면 로그인 페이지에 링크 합니다.

* 탭의 **새 앱 추가** 단추는 오른쪽 위 모서리에는 새 앱 ID를 만듭니다.

   ![Facebook 개발자 포털에 대 한 Microsoft Edge에서 열린](index/_static/FBMyApps.png)

* 양식을 작성 하 고 탭의 **앱 ID 만들기** 단추입니다.

  ![새 앱 ID 양식 만들기](index/_static/FBNewAppId.png)

* 에 **제품을 선택** 페이지에서 클릭 **설정** 에 **Facebook 로그인** 카드.

  ![제품 설치 페이지](index/_static/FBProductSetup.png)

* 합니다 **퀵 스타트** 마법사가 시작 됩니다 **플랫폼을 선택** 의 첫 번째 페이지입니다. 마법사를 클릭 하 여 지금은 무시 합니다 **설정을** 왼쪽 메뉴에서 링크:

  ![Skip 빠른 시작](index/_static/FBSkipQuickStart.png)

* 표시 되는 **클라이언트 OAuth 설정** 페이지:

  ![클라이언트 OAuth 설정 페이지](index/_static/FBOAuthSetup.png)

* 개발 URI를 입력으로 */signin-facebook* 에 추가 합니다 **유효한 OAuth 리디렉션 Uri** 필드 (예를 들어: `https://localhost:44320/signin-facebook`). 이 자습서의 뒷부분에서 구성 된 Facebook 인증에는 요청을 자동으로 처리할 */signin-facebook* OAuth 흐름을 구현 하는 경로입니다.

> [!NOTE]
> URI */signin-facebook* Facebook 인증 공급자의 기본 콜백으로 설정 됩니다. 상속 된 통해 Facebook 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다 [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 의 속성을 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) 클래스입니다.

* 클릭 **변경 내용을 저장**합니다.

* 클릭 **설정을** > **기본** 왼쪽된 탐색 창에서 링크 합니다.

  이 페이지에서는 기록해 하 `App ID` 고 `App Secret`입니다. 다음 섹션에서 ASP.NET Core 응용 프로그램에 둘 다를 추가 합니다.

* 다시 방문 해야 하는 사이트를 배포 하는 경우는 **Facebook 로그인** 페이지를 설정 하 고 새 공용 URI를 등록 합니다.

## <a name="store-facebook-app-id-and-app-secret"></a>Facebook 앱 ID 및 앱 암호를 저장 합니다.

Facebook과 같은 중요 한 설정 연결 `App ID` 하 고 `App Secret` 사용 하 여 응용 프로그램 구성에는 [암호 관리자](xref:security/app-secrets)합니다. 이 자습서에서는 이름을 토큰 `Authentication:Facebook:AppId` 고 `Authentication:Facebook:AppSecret`입니다.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

안전 하 게 저장 하려면 다음 명령을 실행 `App ID` 고 `App Secret` 암호 관리자를 사용 하 여:

```dotnetcli
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a>Facebook 인증 구성

::: moniker range=">= aspnetcore-2.0"

Facebook 서비스에 추가 합니다 `ConfigureServices` 의 메서드를 *Startup.cs* 파일:

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

설치 합니다 [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) 패키지 있습니다.

* Visual Studio 2017을 사용 하 여이 패키지를 설치 하려면 마우스 오른쪽 단추로 클릭 프로젝트를 마우스 **NuGet 패키지 관리**합니다.
* .NET Core CLI를 설치 하려면 프로젝트 디렉터리에서 다음을 실행 합니다.

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

Facebook 미들웨어를 추가 합니다 `Configure` 의 메서드 *Startup.cs* 파일:

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

::: moniker-end

참조 된 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) Facebook 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조. 구성 옵션을 사용할 수 있습니다.

* 사용자에 대 한 다른 정보를 요청 합니다.
* 로그인 환경을 사용자 지정 하는 쿼리 문자열 인수를 추가 합니다.

## <a name="sign-in-with-facebook"></a>Facebook으로 로그인

응용 프로그램을 실행 하 고 클릭 **로그인**합니다. Facebook으로 로그인 하는 옵션이 표시 됩니다.

![웹 응용 프로그램: 사용자가 인증 되지 않음](index/_static/DoneFacebook.png)

클릭 하면 **Facebook**, 인증에 대 한 Facebook로 리디렉션됩니다.

![Facebook 인증 페이지](index/_static/FBLogin.png)

Facebook 인증 기본적으로 공용 프로필 및 전자 메일 주소를 요청합니다.

![Facebook 인증 페이지 동의 화면](index/_static/FBLoginDone.png)

Facebook 자격 증명을 입력 한 후 전자 메일을 설정할 수 있는 사이트로 다시 리디렉션됩니다.

이제 Facebook 자격 증명을 사용 하 여 로그인 됩니다.

![웹 응용 프로그램: 사용자 인증 됨](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>문제 해결

* **ASP.NET Core 2.x에만 해당:** 에서 `services.AddIdentity` *를 호출 하 여 id가 구성 되지 않은 경우 인증을 시도 하면 ArgumentException이 발생 합니다. `ConfigureServices` ' SignInScheme ' 옵션을 제공*해야 합니다. 이 자습서에 사용 되는 프로젝트 템플릿이이 수행 되도록 보장 합니다.
* 사이트 데이터베이스를 초기 마이그레이션을 적용 하 여 만들어지지 않은, 하는 경우 얻게 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다.* 오류입니다. 탭 **마이그레이션 적용** 데이터베이스를 만들고 오류 지 나 새로 고침 합니다.

## <a name="next-steps"></a>다음 단계

* 고급 Facebook 인증 시나리오에 대 한 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) 패키지를 프로젝트에 추가 합니다. 이 패키지는 Facebook 외부 로그인 기능을 앱에 통합 하는 데 필요 하지 않습니다. 

* 이 문서에서는 Facebook을 사용 하 여 인증 하는 보여 주었습니다. 에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다 합니다 [이전 페이지](xref:security/authentication/social/index)합니다.

* 다시 설정 해야 Azure 웹 앱에 웹 사이트를 게시 하면는 `AppSecret` Facebook 개발자 포털에서 합니다.

* 설정 된 `Authentication:Facebook:AppId` 및 `Authentication:Facebook:AppSecret` Azure portal에서 응용 프로그램 설정 합니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.
