---
title: 계정 확인 및 ASP.NET Core에서 암호 복구
author: rick-anderson
description: 전자 메일 확인 및 암호 재설정을 사용 하 여 ASP.NET Core 앱을 빌드하는 방법에 알아봅니다.
ms.author: riande
ms.date: 3/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 59041bcf11f7deb351a2f0bb075ed80c8af5e12b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891680"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>계정 확인 및 ASP.NET Core에서 암호 복구

::: moniker range="<= aspnetcore-2.0"

참조 [이 PDF 파일](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) ASP.NET Core 1.1 및 2.1 버전에 대 한 합니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

하 여 [Rick Anderson](https://twitter.com/RickAndMSFT)하십시오 [Ponant](https://github.com/Ponant), 및 [Joe Audette](https://twitter.com/joeaudette)

이 자습서에서는 전자 메일 확인 및 암호 재설정을 사용 하 여 ASP.NET Core 앱을 빌드하는 방법을 보여 줍니다. 이 자습서는 **되지** 시작 항목입니다. 에 대해 잘 알고 있어야 합니다.

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [인증](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

## <a name="prerequisites"></a>전제 조건

[.NET core 2.2 SDK 이상](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a>웹 앱 만들기 및 Identity를 스 캐 폴드

인증을 사용 하 여 웹 앱을 만들려면 다음 명령을 실행 합니다.

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a>새 사용자 등록 테스트

앱 실행을 선택 합니다 **등록** 링크를 선택한 사용자를 등록 합니다. 이 시점에서 전자 메일에만 유효성 검사 된 합니다 [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) 특성입니다. 등록을 제출 후 앱에 로그인 됩니다. 자습서의 뒷부분에 나오는 코드에는 전자 메일의 유효성을 검사할 때까지 새 사용자가 로그인 할 수 없습니다 있도록 업데이트 됩니다.

[!INCLUDE[](~/includes/view-identity-db.md)]

테이블의 유의 `EmailConfirmed` 필드는 `False`합니다.

앱에서 확인 전자 메일을 보낼 때 다음 단계에서는이 전자 메일에 다시 사용 하려는. 선택한 행을 마우스 오른쪽 단추로 클릭 **삭제**합니다. 전자 메일 별칭을 삭제 하면 쉽게 다음 단계에 있습니다.

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>전자 메일 확인이 필요

새 사용자 등록 전자 메일을 확인 하는 것이 좋습니다. 다른 사람이 가장 하지는 확인 하려면 확인 하면 전자 메일 (즉, 다른 사용자의 전자 메일을 사용 하 여 등록 하지 않은). 토론 포럼을 했으며 방지 하려고 한다고 가정해 보겠습니다 "yli@example.com"등록"에서nolivetto@contoso.com"입니다. 전자 메일 확인 하지 않고 "nolivetto@contoso.com" 앱에서 원치 않는 전자 메일을 받을 수 있습니다. 사용자는 실수로로 등록 되어 있다고 가정 "ylo@example.com" 및 "yli"의 오타 눈치채 합니다. 이러한 앱에 올바른 전자 메일 없기 때문에 암호 복구를 사용 하려면 없게 됩니다. 전자 메일 확인 봇을에서 제한 된 보호를 제공합니다. 전자 메일 확인 전자 메일 계정의 수를 사용 하 여 악의적인 사용자 로부터 보호를 제공 하지 않습니다.

일반적으로 새 사용자가 전자 메일을 확인된 하기 전에 먼저 웹 사이트에 데이터를 게시 하지 못하도록 좋습니다.

업데이트 `Startup.ConfigureServices` 전자 메일을 확인된 해야 합니다.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;` 등록 된 사용자를가 해당 전자 메일이 확인 될 때까지 로그인 수 없습니다.

### <a name="configure-email-provider"></a>전자 메일 공급자 구성

이 자습서에서는 [SendGrid](https://sendgrid.com) 전자 메일을 보내는 데 사용 됩니다. SendGrid 계정 및 전자 메일을 보내는 키 필요 합니다. 다른 이메일 공급자를 사용할 수 있습니다. ASP.NET Core 2.x 포함 `System.Net.Mail`, 앱에서 전자 메일을 보낼 수 있습니다. 전자 메일을 보내려면 SendGrid 또는 다른 전자 메일 서비스를 사용 하는 것이 좋습니다. SMTP를 보호 하 고 올바르게 설정 하기가 어렵습니다.

전자 메일 보안 키를 인출 하는 클래스를 만듭니다. 이 샘플을 만들 *Services/AuthMessageSenderOptions.cs*:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>SendGrid 사용자 암호를 구성 합니다.

설정 합니다 `SendGridUser` 하 고 `SendGridKey` 사용 하 여는 [암호 관리자 도구](xref:security/app-secrets)합니다. 예를 들어:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Windows, 암호 관리자의 키/값 쌍 저장 하는 *secrets.json* 파일을 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 디렉터리입니다.

콘텐츠를 *secrets.json* 파일 암호화 되지 않습니다. 에서는 다음 태그를 *secrets.json* 파일입니다. `SendGridKey` 값이 제거 되었습니다.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

자세한 내용은 참조는 [옵션 패턴](xref:fundamentals/configuration/options) 및 [구성](xref:fundamentals/configuration/index)합니다.

### <a name="install-sendgrid"></a>SendGrid를 설치 합니다.

이 자습서를 통해 전자 메일 알림을 추가 하는 방법을 보여 줍니다 [SendGrid](https://sendgrid.com/), 하지만 SMTP 및 다른 메커니즘을 사용 하 여 메일을 보낼 수 있습니다.

설치는 `SendGrid` NuGet 패키지:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

패키지 관리자 콘솔에서 다음 명령을 입력 합니다.

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

콘솔에서 다음 명령을 입력 합니다.

```cli
dotnet add package SendGrid
```

---

참조 [SendGrid를 사용 하 여 시작 해 보세요](https://sendgrid.com/free/) 무료 SendGrid 계정을 등록 합니다.

### <a name="implement-iemailsender"></a>IEmailSender 구현

구현 `IEmailSender`를 만듭니다 *Services/EmailSender.cs* 다음과 비슷한 코드를 사용 하 여:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>시작 전자 메일을 지원 하도록 구성

다음 코드를 추가 합니다 `ConfigureServices` 의 메서드를 *Startup.cs* 파일:

* 추가 `EmailSender` 일시적인 서비스로 합니다.
* 등록 된 `AuthMessageSenderOptions` 구성 인스턴스.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>계정 확인 및 암호 복구를 사용 하도록 설정

서식 파일에 계정 확인 및 암호 복구를 위한 코드가 있습니다. 찾을 합니다 `OnPostAsync` 의 메서드 *Areas/Identity/Pages/Account/Register.cshtml.cs*합니다.

다음 줄을 주석으로 자동으로 로그인 되 새로 등록 된 사용자를 방지 합니다.

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

전체 메서드는 강조 표시 된 변경 된 줄으로 표시 됩니다.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>등록 하 고, 전자 메일을 확인 하 고, 암호 재설정

웹 앱을 실행 하 고 계정 확인 및 암호 복구 흐름을 테스트 합니다.

* 앱을 실행 하 고 새 사용자 등록
* 계정 확인 링크에 대 한 전자 메일을 확인 합니다. 참조 [전자 메일을 디버그](#debug) 전자 메일을 얻지 못한 경우.
* 전자 메일 확인 하기 위한 링크를 클릭 합니다.
* 전자 메일 및 암호를 로그인 합니다.
* 로그 아웃 합니다.

### <a name="view-the-manage-page"></a>관리 페이지 보기

브라우저에서 사용자 이름을 선택 합니다. ![사용자 이름이 있는 브라우저 창](accconfirm/_static/un.png)

사용 하 여 관리 페이지가 표시 되는 **프로필** 탭이 선택 합니다. 합니다 **전자 메일** 확인 된 전자 메일을 나타내는 확인란을 보여 줍니다.

### <a name="test-password-reset"></a>테스트 암호 재설정

* 로그인 할 경우 선택할 **로그 아웃**합니다.
* 선택 합니다 **에 로그인** 연결 하 고 선택 합니다 **암호를 잊으셨나요?** 링크.
* 계정을 등록 하는 데 전자 메일을 입력 합니다.
* 암호 재설정에 대 한 링크가 포함 된 전자 메일이 전송 됩니다. 전자 메일을 확인 하 고 암호 재설정에 대 한 링크를 클릭 합니다. 암호가 성공적으로 다시 설정, 메일 및 새 암호를 사용 하 여 서명할 수 있습니다.

## <a name="change-email-and-activity-timeout"></a>전자 메일 및 작업 시간 제한 변경

기본 비활성 시간은 14 일입니다. 다음 코드는 5 일에 비활성 시간을 설정합니다.

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>모든 데이터 보호 토큰 lifespans 변경

다음 코드는 3 시간으로 모든 데이터 보호 토큰 제한 시간을 변경합니다.

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

기본 제공 Id 사용자 토큰에 (참조 [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )가를 [하루 시간 제한](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)합니다.

### <a name="change-the-email-token-lifespan"></a>전자 메일 토큰 수명을 변경 합니다.

기본 토큰 수명을 [Id 사용자 토큰](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 됩니다 [하루](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)합니다. 이 섹션에는 전자 메일 토큰 수명이 변경 하는 방법을 보여 줍니다.

사용자 지정을 추가 [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) 고 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

서비스 컨테이너에 사용자 지정 공급자를 추가 합니다.

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>전자 메일 확인 다시 전송

참조 [이 GitHub 문제](https://github.com/aspnet/AspNetCore/issues/5410)합니다.

<a name="debug"></a>

### <a name="debug-email"></a>전자 메일을 디버그

경우 전자 메일 작업을 가져올 수 없습니다.

* 중단점을 설정 `EmailSender.Execute` 확인 하려면 `SendGridClient.SendEmailAsync` 라고 합니다.
* 만들기는 [전자 메일을 보내는 콘솔 앱](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 비슷한 코드를 사용 하 여 `EmailSender.Execute`입니다.
* 검토 합니다 [전자 메일 작업](https://sendgrid.com/docs/User_Guide/email_activity.html) 페이지입니다.
* 스팸 폴더를 확인 합니다.
* 다른 전자 메일 별칭에는 다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)를 시도 하세요.
* 다른 메일 계정으로 보내기를 시도 합니다.

**보안 모범 사례** 하는 것 **하지** 테스트 및 개발에서 프로덕션 비밀을 사용 합니다. Azure에 앱을 게시 하는 경우 Azure 웹 앱 포털에서 응용 프로그램 설정으로 SendGrid 비밀을 설정할 수 있습니다. 구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.

## <a name="combine-social-and-local-login-accounts"></a>로컬 및 소셜 로그인 계정을 병합합니다

이 섹션을 완료 하려면 먼저 외부 인증 공급자를 활성화 해야 합니다. 참조 [Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)합니다.

로컬 및 소셜 계정 전자 메일 링크를 클릭 하 여 결합할 수 있습니다. 그러나 다음 순서로 "RickAndMSFT@gmail.com" 로컬 로그인;으로 처음 만들어질 수 계정으로 소셜 로그인을 먼저 만든 다음 로컬 로그인을 추가 합니다.

![웹 응용 프로그램: RickAndMSFT@gmail.com 인증 된 사용자](accconfirm/_static/rick.png)

클릭 합니다 **관리** 링크 합니다. 이 계정과 연결 된 참고 0 외부 (소셜 로그인)

![보기 관리](accconfirm/_static/manage.png)

다른 로그인 서비스에 대 한 링크를 클릭 하 고 앱 요청을 수락 합니다. 다음 이미지에서는 Facebook는 외부 인증 공급자:

![Facebook을 목록 외부 로그인 보기를 관리 합니다.](accconfirm/_static/fb.png)

두 개의 계정은 결합 되었습니다. 계정이 나 로그인 수 있습니다. 사용자가 자신의 소셜 로그인 인증 서비스가 다운 된 또는 가능성이 소셜 계정에 대 한 액세스를 손실 했습니다 되는 경우 로컬 계정을 추가 수 있습니다.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>사이트 사용자 후 계정 확인을 사용 하도록 설정

사용자를 사용 하 여 사이트에서 계정 확인을 사용 하도록 설정 하면 모든 기존 사용자를 잠급니다. 해당 계정 확인 되지 때문에 기존 사용자가 잠겨 있습니다. 기존 사용자 잠금 문제를 해결 하려면 다음 방법 중 하나를 사용 합니다.

* 로 확인 되 고 모든 기존 사용자를 표시 하도록 데이터베이스를 업데이트 합니다.
* 기존 사용자를 확인 합니다. 예를 들어 일괄 처리-송신 확인 링크를 사용 하 여 전자 메일입니다.

::: moniker-end
