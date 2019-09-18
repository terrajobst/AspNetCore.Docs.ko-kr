---
title: ASP.NET Core의 계정 확인 및 암호 복구
author: rick-anderson
description: 전자 메일 확인 및 암호 재설정을 사용 하 여 ASP.NET Core 앱을 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 8a515990be584aa1233fc3bf77811ae3784d9b1c
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081555"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="77170-103">ASP.NET Core의 계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="77170-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="77170-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)및 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="77170-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="77170-105">이 자습서에서는 전자 메일 확인 및 암호 재설정을 사용 하 여 ASP.NET Core 앱을 빌드하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="77170-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="77170-106">이 자습서는 시작 항목이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="77170-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="77170-107">에 대해 잘 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-107">You should be familiar with:</span></span>

* [<span data-ttu-id="77170-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="77170-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="77170-109">인증</span><span class="sxs-lookup"><span data-stu-id="77170-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="77170-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="77170-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="77170-111">ASP.NET Core 1.1 버전은 [이 PDF 파일](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="77170-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="77170-112">전제 조건</span><span class="sxs-lookup"><span data-stu-id="77170-112">Prerequisites</span></span>

[<span data-ttu-id="77170-113">.NET Core 3.0 SDK 이상</span><span class="sxs-lookup"><span data-stu-id="77170-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="77170-114">인증을 사용 하 여 웹 앱 만들기 및 테스트</span><span class="sxs-lookup"><span data-stu-id="77170-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="77170-115">다음 명령을 실행 하 여 인증을 사용 하는 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77170-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="77170-116">앱을 실행 하 고, **등록** 링크를 선택 하 고, 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="77170-117">등록 되 면 전자 메일 확인을 시뮬레이트하는 `/Identity/Account/RegisterConfirmation` 링크를 포함 하는 to 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="77170-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="77170-118">링크를 `Click here to confirm your account` 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="77170-119">**로그인** 링크를 선택 하 고 동일한 자격 증명을 사용 하 여 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="77170-120">페이지로리디렉션되`/Identity/Account/Manage/PersonalData` 는 `Hello YourEmail@provider.com!` 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="77170-121">왼쪽에서 **개인 데이터** 탭을 선택 하 고 **삭제**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="77170-122">전자 메일 공급자 구성</span><span class="sxs-lookup"><span data-stu-id="77170-122">Configure an email provider</span></span>

<span data-ttu-id="77170-123">이 자습서에서는 [SendGrid](https://sendgrid.com) 를 사용 하 여 전자 메일을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="77170-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="77170-124">전자 메일을 보내려면 SendGrid 계정 및 키가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="77170-125">다른 전자 메일 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-125">You can use other email providers.</span></span> <span data-ttu-id="77170-126">SendGrid 또는 다른 전자 메일 서비스를 사용 하 여 전자 메일을 보내는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="77170-127">SMTP는 안전 하 게 보호 하 고 올바르게 설정 하기 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="77170-128">보안 전자 메일 키를 인출 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77170-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="77170-129">이 샘플의 경우 *Services/AuthMessageSenderOptions*을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77170-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="77170-130">SendGrid 사용자 비밀 구성</span><span class="sxs-lookup"><span data-stu-id="77170-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="77170-131">`SendGridUser` [암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 및 `SendGridKey` 를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="77170-132">예:</span><span class="sxs-lookup"><span data-stu-id="77170-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="77170-133">Windows에서 Secret Manager는 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 디렉터리에 있는 *비밀. json* 파일의 키/값 쌍을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="77170-134">*비밀. json* 파일의 내용이 암호화 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="77170-135">다음 태그는 *비밀. json* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="77170-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="77170-136">`SendGridKey` 값이 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="77170-137">자세한 내용은 [옵션 패턴](xref:fundamentals/configuration/options) 및 [구성](xref:fundamentals/configuration/index)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="77170-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="77170-138">SendGrid 설치</span><span class="sxs-lookup"><span data-stu-id="77170-138">Install SendGrid</span></span>

<span data-ttu-id="77170-139">이 자습서에서는 [SendGrid](https://sendgrid.com/)를 통해 전자 메일 알림을 추가 하는 방법을 보여 주지만 SMTP 및 기타 메커니즘을 사용 하 여 전자 메일을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="77170-140">NuGet 패키지 `SendGrid` 를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77170-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77170-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="77170-142">패키지 관리자 콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="77170-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="77170-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="77170-144">콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="77170-145">무료 SendGrid 계정에 등록 하려면 [무료로 SendGrid 시작](https://sendgrid.com/free/) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="77170-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="77170-146">IEmailSender 구현</span><span class="sxs-lookup"><span data-stu-id="77170-146">Implement IEmailSender</span></span>

<span data-ttu-id="77170-147">를 구현 `IEmailSender`하려면 다음과 유사한 코드를 사용 하 여 *서비스/emailsender .cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77170-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="77170-148">전자 메일을 지원 하도록 시작 구성</span><span class="sxs-lookup"><span data-stu-id="77170-148">Configure startup to support email</span></span>

<span data-ttu-id="77170-149">`ConfigureServices` *Startup.cs* 파일의 메서드에 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="77170-150">을 `EmailSender` 임시 서비스로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="77170-151">구성 인스턴스 `AuthMessageSenderOptions` 를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="77170-152">등록, 전자 메일 확인 및 암호 재설정</span><span class="sxs-lookup"><span data-stu-id="77170-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="77170-153">웹 앱을 실행 하 고 계정 확인 및 암호 복구 흐름을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="77170-154">앱을 실행 하 고 새 사용자 등록</span><span class="sxs-lookup"><span data-stu-id="77170-154">Run the app and register a new user</span></span>
* <span data-ttu-id="77170-155">계정 확인 링크에 대 한 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="77170-156">전자 메일을 받을 수 없는 경우 [디버그 전자 메일](#debug) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="77170-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="77170-157">링크를 클릭 하 여 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="77170-158">전자 메일 및 암호를 사용 하 여 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="77170-159">로그 아웃 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="77170-160">암호 재설정 테스트</span><span class="sxs-lookup"><span data-stu-id="77170-160">Test password reset</span></span>

* <span data-ttu-id="77170-161">로그인 하는 경우 **로그 아웃**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="77170-162">**로그인** 링크를 선택 하 고 암호를 **잊으셨나요?** 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="77170-163">계정을 등록 하는 데 사용한 전자 메일을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="77170-164">암호를 재설정 하는 링크가 포함 된 전자 메일이 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="77170-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="77170-165">전자 메일을 확인 하 고 링크를 클릭 하 여 암호를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="77170-166">암호를 성공적으로 재설정 한 후에는 전자 메일 및 새 암호로 로그인 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="77170-167">전자 메일 및 작업 시간 제한 변경</span><span class="sxs-lookup"><span data-stu-id="77170-167">Change email and activity timeout</span></span>

<span data-ttu-id="77170-168">기본 비활성 시간 제한은 14 일입니다.</span><span class="sxs-lookup"><span data-stu-id="77170-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="77170-169">다음 코드는 비활성 시간 제한을 5 일로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="77170-170">모든 데이터 보호 토큰 변경 lifespans</span><span class="sxs-lookup"><span data-stu-id="77170-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="77170-171">다음 코드는 모든 데이터 보호 토큰 제한 시간을 3 시간으로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="77170-172">기본 제공 Id 사용자 토큰 ( [AspNetCore/src/identity/extension. Core/src/TokenOptions](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )은 [하루에 한 시간 제한이](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="77170-173">전자 메일 토큰 수명 변경</span><span class="sxs-lookup"><span data-stu-id="77170-173">Change the email token lifespan</span></span>

<span data-ttu-id="77170-174">[Id 사용자 토큰](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 의 기본 토큰 수명은 [1 일](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)입니다.</span><span class="sxs-lookup"><span data-stu-id="77170-174">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="77170-175">이 섹션에서는 전자 메일 토큰 수명을 변경 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="77170-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="77170-176">사용자 지정 [DataProtectorTokenProvider\<tuser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) 을 추가 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>하 고 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="77170-177">서비스 컨테이너에 사용자 지정 공급자를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="77170-178">전자 메일 다시 보내기 확인</span><span class="sxs-lookup"><span data-stu-id="77170-178">Resend email confirmation</span></span>

<span data-ttu-id="77170-179">이 [GitHub 문제](https://github.com/aspnet/AspNetCore/issues/5410)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77170-179">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="77170-180">디버그 전자 메일</span><span class="sxs-lookup"><span data-stu-id="77170-180">Debug email</span></span>

<span data-ttu-id="77170-181">전자 메일을 사용할 수 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="77170-181">If you can't get email working:</span></span>

* <span data-ttu-id="77170-182">가 호출 되었는지 확인 `EmailSender.Execute` `SendGridClient.SendEmailAsync` 하려면에서 중단점을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="77170-183">비슷한 코드를 `EmailSender.Execute`사용 하 여 [전자 메일을 보내는 콘솔 앱](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77170-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="77170-184">[전자 메일 작업](https://sendgrid.com/docs/User_Guide/email_activity.html) 페이지를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="77170-185">스팸 폴더를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-185">Check your spam folder.</span></span>
* <span data-ttu-id="77170-186">다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)에서 다른 전자 메일 별칭을 사용해 보세요.</span><span class="sxs-lookup"><span data-stu-id="77170-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="77170-187">다른 전자 메일 계정으로 전송 해 보세요.</span><span class="sxs-lookup"><span data-stu-id="77170-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="77170-188">**보안 모범 사례** 는 테스트 및 개발에서 프로덕션 암호를 사용 **하지 않는** 것입니다.</span><span class="sxs-lookup"><span data-stu-id="77170-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="77170-189">Azure에 앱을 게시 하는 경우 Azure 웹 앱 포털에서 SendGrid 비밀을 응용 프로그램 설정으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="77170-190">구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="77170-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="77170-191">소셜 및 로컬 로그인 계정 결합</span><span class="sxs-lookup"><span data-stu-id="77170-191">Combine social and local login accounts</span></span>

<span data-ttu-id="77170-192">이 섹션을 완료 하려면 먼저 외부 인증 공급자를 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="77170-193">[Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="77170-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="77170-194">전자 메일 링크를 클릭 하 여 로컬 및 소셜 계정을 결합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="77170-195">다음 시퀀스에서는 ""이RickAndMSFT@gmail.com(가) 로컬 로그인으로 먼저 생성 되지만 계정을 소셜 로그인으로 먼저 만든 다음 로컬 로그인을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![웹 응용 프로그램 RickAndMSFT@gmail.com : 사용자 인증 됨](accconfirm/_static/rick.png)

<span data-ttu-id="77170-197">**관리** 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-197">Click on the **Manage** link.</span></span> <span data-ttu-id="77170-198">이 계정과 연결 된 0 외부 (소셜 로그인)를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-198">Note the 0 external (social logins) associated with this account.</span></span>

![뷰 관리](accconfirm/_static/manage.png)

<span data-ttu-id="77170-200">다른 로그인 서비스에 대 한 링크를 클릭 하 고 앱 요청을 수락 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="77170-201">다음 이미지에서 Facebook은 외부 인증 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="77170-201">In the following image, Facebook is the external authentication provider:</span></span>

![외부 로그인 보기 관리 Facebook 목록](accconfirm/_static/fb.png)

<span data-ttu-id="77170-203">두 계정을 결합 했습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-203">The two accounts have been combined.</span></span> <span data-ttu-id="77170-204">두 계정을 사용 하 여 로그인 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-204">You are able to sign in with either account.</span></span> <span data-ttu-id="77170-205">소셜 로그인 인증 서비스가 다운 되거나 소셜 계정에 대 한 액세스 권한이 손실 될 가능성이 있는 경우 사용자가 로컬 계정을 추가 하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="77170-206">사이트에 사용자가 있는 후 계정 확인 사용</span><span class="sxs-lookup"><span data-stu-id="77170-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="77170-207">사용자가 있는 사이트에서 계정 확인을 사용 하도록 설정 하면 모든 기존 사용자가 잠깁니다.</span><span class="sxs-lookup"><span data-stu-id="77170-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="77170-208">계정이 확인 되지 않았기 때문에 기존 사용자가 잠깁니다.</span><span class="sxs-lookup"><span data-stu-id="77170-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="77170-209">기존 사용자 잠금을 해결 하려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="77170-210">기존 사용자를 모두 확인 된 것으로 표시 하도록 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="77170-211">기존 사용자를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-211">Confirm existing users.</span></span> <span data-ttu-id="77170-212">예를 들어, 확인 링크가 포함 된 메일을 일괄 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="77170-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="77170-213">전제 조건</span><span class="sxs-lookup"><span data-stu-id="77170-213">Prerequisites</span></span>

[<span data-ttu-id="77170-214">.NET Core 2.2 SDK 이상</span><span class="sxs-lookup"><span data-stu-id="77170-214">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="77170-215">웹 앱 및 스 캐 폴드 Id 만들기</span><span class="sxs-lookup"><span data-stu-id="77170-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="77170-216">다음 명령을 실행 하 여 인증을 사용 하는 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77170-216">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="77170-217">새 사용자 등록 테스트</span><span class="sxs-lookup"><span data-stu-id="77170-217">Test new user registration</span></span>

<span data-ttu-id="77170-218">앱을 실행 하 고, **등록** 링크를 선택 하 고, 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="77170-219">이때 전자 메일에 대 한 유일한 유효성 검사는 [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) 특성을 사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="77170-219">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="77170-220">등록을 제출 하면 앱에 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="77170-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="77170-221">자습서의 뒷부분에서 새로운 사용자가 전자 메일의 유효성을 검사할 때까지 로그인 할 수 없도록 코드를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="77170-222">테이블의 `EmailConfirmed` `False`필드는입니다.</span><span class="sxs-lookup"><span data-stu-id="77170-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="77170-223">앱에서 확인 전자 메일을 보낼 때 다음 단계에서이 전자 메일을 다시 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="77170-224">행을 마우스 오른쪽 단추로 클릭 하 고 **삭제**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="77170-225">전자 메일 별칭을 삭제 하면 다음 단계에서 보다 쉽게 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="77170-226">전자 메일 확인 필요</span><span class="sxs-lookup"><span data-stu-id="77170-226">Require email confirmation</span></span>

<span data-ttu-id="77170-227">새 사용자 등록의 전자 메일을 확인 하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="77170-228">전자 메일 확인을 사용 하면 다른 사용자를 가장 하지 않는 것을 확인할 수 있습니다. 즉, 다른 사용자의 전자 메일을 사용 하 여 등록 하지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="77170-229">토론 포럼이 있고 ""을 ""yli@example.comnolivetto@contoso.com로 등록 하지 못하도록 하는 경우를 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="77170-230">전자 메일 확인이 없으면nolivetto@contoso.com""이 (가) 앱에서 원치 않는 전자 메일을 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="77170-231">"ylo@example.com"로 실수로 등록 한 사용자가 "yli"의 맞춤법 오류를 발견 했다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="77170-232">앱에 올바른 전자 메일이 없기 때문에 암호 복구를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="77170-233">전자 메일 확인은 봇에서 제한 된 보호 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="77170-234">전자 메일 확인은 전자 메일 계정이 많은 악의적인 사용자 로부터 보호를 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="77170-235">일반적으로 새 사용자가 확인 된 전자 메일을 보내기 전에 데이터를 웹 사이트에 게시 하는 것을 방지 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="77170-236">확인 `Startup.ConfigureServices` 된 전자 메일을 요구 하도록 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="77170-237">`config.SignIn.RequireConfirmedEmail = true;`등록 된 사용자가 전자 메일을 확인할 때까지 로그인 하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="77170-238">전자 메일 공급자 구성</span><span class="sxs-lookup"><span data-stu-id="77170-238">Configure email provider</span></span>

<span data-ttu-id="77170-239">이 자습서에서는 [SendGrid](https://sendgrid.com) 를 사용 하 여 전자 메일을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="77170-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="77170-240">전자 메일을 보내려면 SendGrid 계정 및 키가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="77170-241">다른 전자 메일 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-241">You can use other email providers.</span></span> <span data-ttu-id="77170-242">ASP.NET Core 2.x에는 앱 `System.Net.Mail`에서 전자 메일을 보낼 수 있는가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="77170-243">SendGrid 또는 다른 전자 메일 서비스를 사용 하 여 전자 메일을 보내는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="77170-244">SMTP는 안전 하 게 보호 하 고 올바르게 설정 하기 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="77170-245">보안 전자 메일 키를 인출 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77170-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="77170-246">이 샘플의 경우 *Services/AuthMessageSenderOptions*을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77170-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="77170-247">SendGrid 사용자 비밀 구성</span><span class="sxs-lookup"><span data-stu-id="77170-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="77170-248">`SendGridUser` [암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 및 `SendGridKey` 를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="77170-249">예:</span><span class="sxs-lookup"><span data-stu-id="77170-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="77170-250">Windows에서 Secret Manager는 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 디렉터리에 있는 *비밀. json* 파일의 키/값 쌍을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="77170-251">*비밀. json* 파일의 내용이 암호화 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="77170-252">다음 태그는 *비밀. json* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="77170-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="77170-253">`SendGridKey` 값이 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="77170-254">자세한 내용은 [옵션 패턴](xref:fundamentals/configuration/options) 및 [구성](xref:fundamentals/configuration/index)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="77170-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="77170-255">SendGrid 설치</span><span class="sxs-lookup"><span data-stu-id="77170-255">Install SendGrid</span></span>

<span data-ttu-id="77170-256">이 자습서에서는 [SendGrid](https://sendgrid.com/)를 통해 전자 메일 알림을 추가 하는 방법을 보여 주지만 SMTP 및 기타 메커니즘을 사용 하 여 전자 메일을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="77170-257">NuGet 패키지 `SendGrid` 를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="77170-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77170-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="77170-259">패키지 관리자 콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-259">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="77170-260">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="77170-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="77170-261">콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-261">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="77170-262">무료 SendGrid 계정에 등록 하려면 [무료로 SendGrid 시작](https://sendgrid.com/free/) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="77170-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="77170-263">IEmailSender 구현</span><span class="sxs-lookup"><span data-stu-id="77170-263">Implement IEmailSender</span></span>

<span data-ttu-id="77170-264">를 구현 `IEmailSender`하려면 다음과 유사한 코드를 사용 하 여 *서비스/emailsender .cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77170-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="77170-265">전자 메일을 지원 하도록 시작 구성</span><span class="sxs-lookup"><span data-stu-id="77170-265">Configure startup to support email</span></span>

<span data-ttu-id="77170-266">`ConfigureServices` *Startup.cs* 파일의 메서드에 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="77170-267">을 `EmailSender` 임시 서비스로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="77170-268">구성 인스턴스 `AuthMessageSenderOptions` 를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="77170-269">계정 확인 및 암호 복구 사용</span><span class="sxs-lookup"><span data-stu-id="77170-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="77170-270">템플릿에는 계정 확인 및 암호 복구를 위한 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="77170-271">`OnPostAsync` *영역/i d/페이지/계정/등록. cshtml*에서 메서드를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="77170-272">다음 줄을 주석으로 처리 하 여 새로 등록 된 사용자가 자동으로 로그인 하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="77170-273">전체 메서드는 변경 된 줄이 강조 표시 된 상태로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="77170-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="77170-274">등록, 전자 메일 확인 및 암호 재설정</span><span class="sxs-lookup"><span data-stu-id="77170-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="77170-275">웹 앱을 실행 하 고 계정 확인 및 암호 복구 흐름을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="77170-276">앱을 실행 하 고 새 사용자 등록</span><span class="sxs-lookup"><span data-stu-id="77170-276">Run the app and register a new user</span></span>
* <span data-ttu-id="77170-277">계정 확인 링크에 대 한 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="77170-278">전자 메일을 받을 수 없는 경우 [디버그 전자 메일](#debug) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="77170-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="77170-279">링크를 클릭 하 여 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="77170-280">전자 메일 및 암호를 사용 하 여 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="77170-281">로그 아웃 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="77170-282">관리 페이지 보기</span><span class="sxs-lookup"><span data-stu-id="77170-282">View the manage page</span></span>

<span data-ttu-id="77170-283">브라우저: 사용자 이름을 사용 하 여 브라우저 ![창에서 사용자 이름을 선택 합니다.](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="77170-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="77170-284">관리 페이지는 **프로필** 탭이 선택 된 상태로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="77170-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="77170-285">전자 **메일** 에는 전자 메일이 확인 되었다는 확인란이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="77170-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="77170-286">암호 재설정 테스트</span><span class="sxs-lookup"><span data-stu-id="77170-286">Test password reset</span></span>

* <span data-ttu-id="77170-287">로그인 하는 경우 **로그 아웃**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="77170-288">**로그인** 링크를 선택 하 고 암호를 **잊으셨나요?** 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="77170-289">계정을 등록 하는 데 사용한 전자 메일을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="77170-290">암호를 재설정 하는 링크가 포함 된 전자 메일이 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="77170-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="77170-291">전자 메일을 확인 하 고 링크를 클릭 하 여 암호를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="77170-292">암호를 성공적으로 재설정 한 후에는 전자 메일 및 새 암호로 로그인 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="77170-293">전자 메일 및 작업 시간 제한 변경</span><span class="sxs-lookup"><span data-stu-id="77170-293">Change email and activity timeout</span></span>

<span data-ttu-id="77170-294">기본 비활성 시간 제한은 14 일입니다.</span><span class="sxs-lookup"><span data-stu-id="77170-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="77170-295">다음 코드는 비활성 시간 제한을 5 일로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="77170-296">모든 데이터 보호 토큰 변경 lifespans</span><span class="sxs-lookup"><span data-stu-id="77170-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="77170-297">다음 코드는 모든 데이터 보호 토큰 제한 시간을 3 시간으로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="77170-298">기본 제공 Id 사용자 토큰 ( [AspNetCore/src/identity/extension. Core/src/TokenOptions](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )은 [하루에 한 시간 제한이](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="77170-299">전자 메일 토큰 수명 변경</span><span class="sxs-lookup"><span data-stu-id="77170-299">Change the email token lifespan</span></span>

<span data-ttu-id="77170-300">[Id 사용자 토큰](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 의 기본 토큰 수명은 [1 일](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)입니다.</span><span class="sxs-lookup"><span data-stu-id="77170-300">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="77170-301">이 섹션에서는 전자 메일 토큰 수명을 변경 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="77170-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="77170-302">사용자 지정 [DataProtectorTokenProvider\<tuser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) 을 추가 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>하 고 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="77170-303">서비스 컨테이너에 사용자 지정 공급자를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="77170-304">전자 메일 다시 보내기 확인</span><span class="sxs-lookup"><span data-stu-id="77170-304">Resend email confirmation</span></span>

<span data-ttu-id="77170-305">이 [GitHub 문제](https://github.com/aspnet/AspNetCore/issues/5410)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77170-305">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="77170-306">디버그 전자 메일</span><span class="sxs-lookup"><span data-stu-id="77170-306">Debug email</span></span>

<span data-ttu-id="77170-307">전자 메일을 사용할 수 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="77170-307">If you can't get email working:</span></span>

* <span data-ttu-id="77170-308">가 호출 되었는지 확인 `EmailSender.Execute` `SendGridClient.SendEmailAsync` 하려면에서 중단점을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="77170-309">비슷한 코드를 `EmailSender.Execute`사용 하 여 [전자 메일을 보내는 콘솔 앱](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77170-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="77170-310">[전자 메일 작업](https://sendgrid.com/docs/User_Guide/email_activity.html) 페이지를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="77170-311">스팸 폴더를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-311">Check your spam folder.</span></span>
* <span data-ttu-id="77170-312">다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)에서 다른 전자 메일 별칭을 사용해 보세요.</span><span class="sxs-lookup"><span data-stu-id="77170-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="77170-313">다른 전자 메일 계정으로 전송 해 보세요.</span><span class="sxs-lookup"><span data-stu-id="77170-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="77170-314">**보안 모범 사례** 는 테스트 및 개발에서 프로덕션 암호를 사용 **하지 않는** 것입니다.</span><span class="sxs-lookup"><span data-stu-id="77170-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="77170-315">Azure에 앱을 게시 하는 경우 Azure 웹 앱 포털에서 응용 프로그램 설정으로 SendGrid 암호를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="77170-316">구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="77170-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="77170-317">소셜 및 로컬 로그인 계정 결합</span><span class="sxs-lookup"><span data-stu-id="77170-317">Combine social and local login accounts</span></span>

<span data-ttu-id="77170-318">이 섹션을 완료 하려면 먼저 외부 인증 공급자를 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="77170-319">[Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="77170-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="77170-320">전자 메일 링크를 클릭 하 여 로컬 및 소셜 계정을 결합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="77170-321">다음 시퀀스에서는 ""이RickAndMSFT@gmail.com(가) 로컬 로그인으로 먼저 생성 되지만 계정을 소셜 로그인으로 먼저 만든 다음 로컬 로그인을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![웹 응용 프로그램 RickAndMSFT@gmail.com : 사용자 인증 됨](accconfirm/_static/rick.png)

<span data-ttu-id="77170-323">**관리** 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-323">Click on the **Manage** link.</span></span> <span data-ttu-id="77170-324">이 계정과 연결 된 0 외부 (소셜 로그인)를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-324">Note the 0 external (social logins) associated with this account.</span></span>

![뷰 관리](accconfirm/_static/manage.png)

<span data-ttu-id="77170-326">다른 로그인 서비스에 대 한 링크를 클릭 하 고 앱 요청을 수락 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="77170-327">다음 이미지에서 Facebook은 외부 인증 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="77170-327">In the following image, Facebook is the external authentication provider:</span></span>

![외부 로그인 보기 관리 Facebook 목록](accconfirm/_static/fb.png)

<span data-ttu-id="77170-329">두 계정을 결합 했습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-329">The two accounts have been combined.</span></span> <span data-ttu-id="77170-330">두 계정을 사용 하 여 로그인 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-330">You are able to sign in with either account.</span></span> <span data-ttu-id="77170-331">소셜 로그인 인증 서비스가 다운 되거나 소셜 계정에 대 한 액세스 권한이 손실 될 가능성이 있는 경우 사용자가 로컬 계정을 추가 하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77170-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="77170-332">사이트에 사용자가 있는 후 계정 확인 사용</span><span class="sxs-lookup"><span data-stu-id="77170-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="77170-333">사용자가 있는 사이트에서 계정 확인을 사용 하도록 설정 하면 모든 기존 사용자가 잠깁니다.</span><span class="sxs-lookup"><span data-stu-id="77170-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="77170-334">계정이 확인 되지 않았기 때문에 기존 사용자가 잠깁니다.</span><span class="sxs-lookup"><span data-stu-id="77170-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="77170-335">기존 사용자 잠금을 해결 하려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="77170-336">기존 사용자를 모두 확인 된 것으로 표시 하도록 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="77170-337">기존 사용자를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="77170-337">Confirm existing users.</span></span> <span data-ttu-id="77170-338">예를 들어, 확인 링크가 포함 된 메일을 일괄 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="77170-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
