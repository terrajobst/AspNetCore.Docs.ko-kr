---
title: 계정 확인 및 ASP.NET Core에서 암호 복구
author: rick-anderson
description: 전자 메일 확인 및 암호 재설정을 사용 하 여 ASP.NET Core 앱을 빌드하는 방법에 알아봅니다.
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 802ba446af04df6a35ac73187ad693b8ec80c654
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814842"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="b0dc4-103">계정 확인 및 ASP.NET Core에서 암호 복구</span><span class="sxs-lookup"><span data-stu-id="b0dc4-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="b0dc4-104">하 여 [Rick Anderson](https://twitter.com/RickAndMSFT)하십시오 [Ponant](https://github.com/Ponant), 및 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="b0dc4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="b0dc4-105">이 자습서에서는 전자 메일 확인 및 암호 재설정을 사용 하 여 ASP.NET Core 앱을 빌드하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="b0dc4-106">이 자습서는 **되지** 시작 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="b0dc4-107">에 대해 잘 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-107">You should be familiar with:</span></span>

* [<span data-ttu-id="b0dc4-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0dc4-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="b0dc4-109">인증</span><span class="sxs-lookup"><span data-stu-id="b0dc4-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="b0dc4-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b0dc4-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="b0dc4-111">참조 [이 PDF 파일](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) ASP.NET Core 1.1 버전에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="b0dc4-112">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="b0dc4-112">Prerequisites</span></span>

[<span data-ttu-id="b0dc4-113">.NET core 3.0 SDK 이상</span><span class="sxs-lookup"><span data-stu-id="b0dc4-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="b0dc4-114">만들기 및 인증을 사용 하 여 웹 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="b0dc4-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="b0dc4-115">인증을 사용 하 여 웹 앱을 만들려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-115">Run the following commands to create a web app with authentication.</span></span>

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="b0dc4-116">앱 실행을 선택 합니다 **등록** 링크를 선택한 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="b0dc4-117">리디렉션됩니다 등록 되 면를를 `/Identity/Account/RegisterConfirmation` 이메일 확인 메시지를 시뮬레이션에 대 한 링크를 포함 하는 페이지:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="b0dc4-118">선택 된 `Click here to confirm your account` 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="b0dc4-119">선택 된 **로그인** 에 연결 하 고 동일한 자격 증명으로 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="b0dc4-120">선택 합니다 `Hello YourEmail@provider.com!` 할 리디렉션하는 링크는 `/Identity/Account/Manage/PersonalData` 페이지.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="b0dc4-121">선택 된 **개인 데이터** 왼쪽에 탭을 선택 합니다 **삭제**합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="b0dc4-122">전자 메일 공급자로 구성</span><span class="sxs-lookup"><span data-stu-id="b0dc4-122">Configure an email provider</span></span>

<span data-ttu-id="b0dc4-123">이 자습서에서는 [SendGrid](https://sendgrid.com) 전자 메일을 보내는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="b0dc4-124">SendGrid 계정 및 전자 메일을 보내는 키 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="b0dc4-125">다른 이메일 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-125">You can use other email providers.</span></span> <span data-ttu-id="b0dc4-126">전자 메일을 보내려면 SendGrid 또는 다른 전자 메일 서비스를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="b0dc4-127">SMTP를 보호 하 고 올바르게 설정 하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="b0dc4-128">전자 메일 보안 키를 인출 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="b0dc4-129">이 샘플을 만들 *Services/AuthMessageSenderOptions.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="b0dc4-130">SendGrid 사용자 암호를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="b0dc4-131">설정 합니다 `SendGridUser` 하 고 `SendGridKey` 사용 하 여는 [암호 관리자 도구](xref:security/app-secrets)합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="b0dc4-132">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-132">For example:</span></span>

```console
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="b0dc4-133">Windows, 암호 관리자의 키/값 쌍 저장 하는 *secrets.json* 파일을 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="b0dc4-134">콘텐츠를 *secrets.json* 파일 암호화 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="b0dc4-135">에서는 다음 태그를 *secrets.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="b0dc4-136">`SendGridKey` 값이 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="b0dc4-137">자세한 내용은 참조는 [옵션 패턴](xref:fundamentals/configuration/options) 및 [구성](xref:fundamentals/configuration/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="b0dc4-138">SendGrid를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-138">Install SendGrid</span></span>

<span data-ttu-id="b0dc4-139">이 자습서를 통해 전자 메일 알림을 추가 하는 방법을 보여 줍니다 [SendGrid](https://sendgrid.com/), 하지만 SMTP 및 다른 메커니즘을 사용 하 여 메일을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="b0dc4-140">설치는 `SendGrid` NuGet 패키지:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b0dc4-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0dc4-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b0dc4-142">패키지 관리자 콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-142">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b0dc4-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b0dc4-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b0dc4-144">콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-144">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

---

<span data-ttu-id="b0dc4-145">참조 [SendGrid를 사용 하 여 시작 해 보세요](https://sendgrid.com/free/) 무료 SendGrid 계정을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="b0dc4-146">IEmailSender 구현</span><span class="sxs-lookup"><span data-stu-id="b0dc4-146">Implement IEmailSender</span></span>

<span data-ttu-id="b0dc4-147">구현 `IEmailSender`를 만듭니다 *Services/EmailSender.cs* 다음과 비슷한 코드를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="b0dc4-148">시작 전자 메일을 지원 하도록 구성</span><span class="sxs-lookup"><span data-stu-id="b0dc4-148">Configure startup to support email</span></span>

<span data-ttu-id="b0dc4-149">다음 코드를 추가 합니다 `ConfigureServices` 의 메서드를 *Startup.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="b0dc4-150">추가 `EmailSender` 일시적인 서비스로 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="b0dc4-151">등록 된 `AuthMessageSenderOptions` 구성 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="b0dc4-152">등록 하 고, 전자 메일을 확인 하 고, 암호 재설정</span><span class="sxs-lookup"><span data-stu-id="b0dc4-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="b0dc4-153">웹 앱을 실행 하 고 계정 확인 및 암호 복구 흐름을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="b0dc4-154">앱을 실행 하 고 새 사용자 등록</span><span class="sxs-lookup"><span data-stu-id="b0dc4-154">Run the app and register a new user</span></span>
* <span data-ttu-id="b0dc4-155">계정 확인 링크에 대 한 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="b0dc4-156">참조 [전자 메일을 디버그](#debug) 전자 메일을 얻지 못한 경우.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="b0dc4-157">전자 메일 확인 하기 위한 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="b0dc4-158">전자 메일 및 암호를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="b0dc4-159">로그 아웃 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="b0dc4-160">테스트 암호 재설정</span><span class="sxs-lookup"><span data-stu-id="b0dc4-160">Test password reset</span></span>

* <span data-ttu-id="b0dc4-161">로그인 할 경우 선택할 **로그 아웃**합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="b0dc4-162">선택 합니다 **에 로그인** 연결 하 고 선택 합니다 **암호를 잊으셨나요?** 링크.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="b0dc4-163">계정을 등록 하는 데 전자 메일을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="b0dc4-164">암호 재설정에 대 한 링크가 포함 된 전자 메일이 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="b0dc4-165">전자 메일을 확인 하 고 암호 재설정에 대 한 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="b0dc4-166">암호가 성공적으로 다시 설정, 메일 및 새 암호를 사용 하 여 서명할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="b0dc4-167">전자 메일 및 작업 시간 제한 변경</span><span class="sxs-lookup"><span data-stu-id="b0dc4-167">Change email and activity timeout</span></span>

<span data-ttu-id="b0dc4-168">기본 비활성 시간은 14 일입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="b0dc4-169">다음 코드는 5 일에 비활성 시간을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="b0dc4-170">모든 데이터 보호 토큰 lifespans 변경</span><span class="sxs-lookup"><span data-stu-id="b0dc4-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="b0dc4-171">다음 코드는 3 시간으로 모든 데이터 보호 토큰 제한 시간을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="b0dc4-172">기본 제공 Id 사용자 토큰에 (참조 [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )가를 [하루 시간 제한](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="b0dc4-173">전자 메일 토큰 수명을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-173">Change the email token lifespan</span></span>

<span data-ttu-id="b0dc4-174">기본 토큰 수명을 [Id 사용자 토큰](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 됩니다 [하루](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-174">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="b0dc4-175">이 섹션에는 전자 메일 토큰 수명이 변경 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="b0dc4-176">사용자 지정을 추가 [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) 고 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="b0dc4-177">서비스 컨테이너에 사용자 지정 공급자를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="b0dc4-178">전자 메일 확인 다시 전송</span><span class="sxs-lookup"><span data-stu-id="b0dc4-178">Resend email confirmation</span></span>

<span data-ttu-id="b0dc4-179">참조 [이 GitHub 문제](https://github.com/aspnet/AspNetCore/issues/5410)합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-179">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="b0dc4-180">전자 메일을 디버그</span><span class="sxs-lookup"><span data-stu-id="b0dc4-180">Debug email</span></span>

<span data-ttu-id="b0dc4-181">경우 전자 메일 작업을 가져올 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-181">If you can't get email working:</span></span>

* <span data-ttu-id="b0dc4-182">중단점을 설정 `EmailSender.Execute` 확인 하려면 `SendGridClient.SendEmailAsync` 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="b0dc4-183">만들기는 [전자 메일을 보내는 콘솔 앱](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 비슷한 코드를 사용 하 여 `EmailSender.Execute`입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="b0dc4-184">검토 합니다 [전자 메일 작업](https://sendgrid.com/docs/User_Guide/email_activity.html) 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="b0dc4-185">스팸 폴더를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-185">Check your spam folder.</span></span>
* <span data-ttu-id="b0dc4-186">다른 전자 메일 별칭에는 다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)를 시도 하세요.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="b0dc4-187">다른 메일 계정으로 보내기를 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="b0dc4-188">**보안 모범 사례** 하는 것 **하지** 테스트 및 개발에서 프로덕션 비밀을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="b0dc4-189">Azure에 앱을 게시 하는 경우 Azure 웹 앱 포털에서 응용 프로그램 설정으로 SendGrid 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="b0dc4-190">구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="b0dc4-191">로컬 및 소셜 로그인 계정을 병합합니다</span><span class="sxs-lookup"><span data-stu-id="b0dc4-191">Combine social and local login accounts</span></span>

<span data-ttu-id="b0dc4-192">이 섹션을 완료 하려면 먼저 외부 인증 공급자를 활성화 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="b0dc4-193">참조 [Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="b0dc4-194">로컬 및 소셜 계정 전자 메일 링크를 클릭 하 여 결합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="b0dc4-195">그러나 다음 순서로 "RickAndMSFT@gmail.com" 로컬 로그인;으로 처음 만들어질 수 계정으로 소셜 로그인을 먼저 만든 다음 로컬 로그인을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![웹 응용 프로그램: RickAndMSFT@gmail.com 인증 된 사용자](accconfirm/_static/rick.png)

<span data-ttu-id="b0dc4-197">클릭 합니다 **관리** 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-197">Click on the **Manage** link.</span></span> <span data-ttu-id="b0dc4-198">이 계정과 연결 된 참고 0 외부 (소셜 로그인)</span><span class="sxs-lookup"><span data-stu-id="b0dc4-198">Note the 0 external (social logins) associated with this account.</span></span>

![보기 관리](accconfirm/_static/manage.png)

<span data-ttu-id="b0dc4-200">다른 로그인 서비스에 대 한 링크를 클릭 하 고 앱 요청을 수락 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="b0dc4-201">다음 이미지에서는 Facebook는 외부 인증 공급자:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-201">In the following image, Facebook is the external authentication provider:</span></span>

![Facebook을 목록 외부 로그인 보기를 관리 합니다.](accconfirm/_static/fb.png)

<span data-ttu-id="b0dc4-203">두 개의 계정은 결합 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-203">The two accounts have been combined.</span></span> <span data-ttu-id="b0dc4-204">계정이 나 로그인 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-204">You are able to sign in with either account.</span></span> <span data-ttu-id="b0dc4-205">사용자가 자신의 소셜 로그인 인증 서비스가 다운 된 또는 가능성이 소셜 계정에 대 한 액세스를 손실 했습니다 되는 경우 로컬 계정을 추가 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="b0dc4-206">사이트 사용자 후 계정 확인을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="b0dc4-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="b0dc4-207">사용자를 사용 하 여 사이트에서 계정 확인을 사용 하도록 설정 하면 모든 기존 사용자를 잠급니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="b0dc4-208">해당 계정 확인 되지 때문에 기존 사용자가 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="b0dc4-209">기존 사용자 잠금 문제를 해결 하려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="b0dc4-210">로 확인 되 고 모든 기존 사용자를 표시 하도록 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="b0dc4-211">기존 사용자를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-211">Confirm existing users.</span></span> <span data-ttu-id="b0dc4-212">예를 들어 일괄 처리-송신 확인 링크를 사용 하 여 전자 메일입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="b0dc4-213">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="b0dc4-213">Prerequisites</span></span>

[<span data-ttu-id="b0dc4-214">.NET core 2.2 SDK 이상</span><span class="sxs-lookup"><span data-stu-id="b0dc4-214">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="b0dc4-215">웹 앱 만들기 및 Identity를 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="b0dc4-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="b0dc4-216">인증을 사용 하 여 웹 앱을 만들려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-216">Run the following commands to create a web app with authentication.</span></span>

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="b0dc4-217">새 사용자 등록 테스트</span><span class="sxs-lookup"><span data-stu-id="b0dc4-217">Test new user registration</span></span>

<span data-ttu-id="b0dc4-218">앱 실행을 선택 합니다 **등록** 링크를 선택한 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="b0dc4-219">이 시점에서 전자 메일에만 유효성 검사 된 합니다 [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-219">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="b0dc4-220">등록을 제출 후 앱에 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="b0dc4-221">자습서의 뒷부분에 나오는 코드에는 전자 메일의 유효성을 검사할 때까지 새 사용자가 로그인 할 수 없습니다 있도록 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="b0dc4-222">테이블의 유의 `EmailConfirmed` 필드는 `False`합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="b0dc4-223">앱에서 확인 전자 메일을 보낼 때 다음 단계에서는이 전자 메일에 다시 사용 하려는.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="b0dc4-224">선택한 행을 마우스 오른쪽 단추로 클릭 **삭제**합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="b0dc4-225">전자 메일 별칭을 삭제 하면 쉽게 다음 단계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="b0dc4-226">전자 메일 확인이 필요</span><span class="sxs-lookup"><span data-stu-id="b0dc4-226">Require email confirmation</span></span>

<span data-ttu-id="b0dc4-227">새 사용자 등록 전자 메일을 확인 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="b0dc4-228">다른 사람이 가장 하지는 확인 하려면 확인 하면 전자 메일 (즉, 다른 사용자의 전자 메일을 사용 하 여 등록 하지 않은).</span><span class="sxs-lookup"><span data-stu-id="b0dc4-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="b0dc4-229">토론 포럼을 했으며 방지 하려고 한다고 가정해 보겠습니다 "yli@example.com"등록"에서nolivetto@contoso.com"입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="b0dc4-230">전자 메일 확인 하지 않고 "nolivetto@contoso.com" 앱에서 원치 않는 전자 메일을 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="b0dc4-231">사용자는 실수로로 등록 되어 있다고 가정 "ylo@example.com" 및 "yli"의 오타 눈치채 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="b0dc4-232">이러한 앱에 올바른 전자 메일 없기 때문에 암호 복구를 사용 하려면 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="b0dc4-233">전자 메일 확인 봇을에서 제한 된 보호를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="b0dc4-234">전자 메일 확인 전자 메일 계정의 수를 사용 하 여 악의적인 사용자 로부터 보호를 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="b0dc4-235">일반적으로 새 사용자가 전자 메일을 확인된 하기 전에 먼저 웹 사이트에 데이터를 게시 하지 못하도록 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="b0dc4-236">업데이트 `Startup.ConfigureServices` 전자 메일을 확인된 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="b0dc4-237">`config.SignIn.RequireConfirmedEmail = true;` 등록 된 사용자를가 해당 전자 메일이 확인 될 때까지 로그인 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="b0dc4-238">전자 메일 공급자 구성</span><span class="sxs-lookup"><span data-stu-id="b0dc4-238">Configure email provider</span></span>

<span data-ttu-id="b0dc4-239">이 자습서에서는 [SendGrid](https://sendgrid.com) 전자 메일을 보내는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="b0dc4-240">SendGrid 계정 및 전자 메일을 보내는 키 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="b0dc4-241">다른 이메일 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-241">You can use other email providers.</span></span> <span data-ttu-id="b0dc4-242">ASP.NET Core 2.x 포함 `System.Net.Mail`, 앱에서 전자 메일을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="b0dc4-243">전자 메일을 보내려면 SendGrid 또는 다른 전자 메일 서비스를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="b0dc4-244">SMTP를 보호 하 고 올바르게 설정 하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="b0dc4-245">전자 메일 보안 키를 인출 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="b0dc4-246">이 샘플을 만들 *Services/AuthMessageSenderOptions.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="b0dc4-247">SendGrid 사용자 암호를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="b0dc4-248">설정 합니다 `SendGridUser` 하 고 `SendGridKey` 사용 하 여는 [암호 관리자 도구](xref:security/app-secrets)합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="b0dc4-249">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="b0dc4-250">Windows, 암호 관리자의 키/값 쌍 저장 하는 *secrets.json* 파일을 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="b0dc4-251">콘텐츠를 *secrets.json* 파일 암호화 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="b0dc4-252">에서는 다음 태그를 *secrets.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="b0dc4-253">`SendGridKey` 값이 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="b0dc4-254">자세한 내용은 참조는 [옵션 패턴](xref:fundamentals/configuration/options) 및 [구성](xref:fundamentals/configuration/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="b0dc4-255">SendGrid를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-255">Install SendGrid</span></span>

<span data-ttu-id="b0dc4-256">이 자습서를 통해 전자 메일 알림을 추가 하는 방법을 보여 줍니다 [SendGrid](https://sendgrid.com/), 하지만 SMTP 및 다른 메커니즘을 사용 하 여 메일을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="b0dc4-257">설치는 `SendGrid` NuGet 패키지:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b0dc4-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0dc4-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b0dc4-259">패키지 관리자 콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-259">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b0dc4-260">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b0dc4-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b0dc4-261">콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-261">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

---

<span data-ttu-id="b0dc4-262">참조 [SendGrid를 사용 하 여 시작 해 보세요](https://sendgrid.com/free/) 무료 SendGrid 계정을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="b0dc4-263">IEmailSender 구현</span><span class="sxs-lookup"><span data-stu-id="b0dc4-263">Implement IEmailSender</span></span>

<span data-ttu-id="b0dc4-264">구현 `IEmailSender`를 만듭니다 *Services/EmailSender.cs* 다음과 비슷한 코드를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="b0dc4-265">시작 전자 메일을 지원 하도록 구성</span><span class="sxs-lookup"><span data-stu-id="b0dc4-265">Configure startup to support email</span></span>

<span data-ttu-id="b0dc4-266">다음 코드를 추가 합니다 `ConfigureServices` 의 메서드를 *Startup.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="b0dc4-267">추가 `EmailSender` 일시적인 서비스로 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="b0dc4-268">등록 된 `AuthMessageSenderOptions` 구성 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="b0dc4-269">계정 확인 및 암호 복구를 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="b0dc4-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="b0dc4-270">서식 파일에 계정 확인 및 암호 복구를 위한 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="b0dc4-271">찾을 합니다 `OnPostAsync` 의 메서드 *Areas/Identity/Pages/Account/Register.cshtml.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="b0dc4-272">다음 줄을 주석으로 자동으로 로그인 되 새로 등록 된 사용자를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="b0dc4-273">전체 메서드는 강조 표시 된 변경 된 줄으로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="b0dc4-274">등록 하 고, 전자 메일을 확인 하 고, 암호 재설정</span><span class="sxs-lookup"><span data-stu-id="b0dc4-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="b0dc4-275">웹 앱을 실행 하 고 계정 확인 및 암호 복구 흐름을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="b0dc4-276">앱을 실행 하 고 새 사용자 등록</span><span class="sxs-lookup"><span data-stu-id="b0dc4-276">Run the app and register a new user</span></span>
* <span data-ttu-id="b0dc4-277">계정 확인 링크에 대 한 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="b0dc4-278">참조 [전자 메일을 디버그](#debug) 전자 메일을 얻지 못한 경우.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="b0dc4-279">전자 메일 확인 하기 위한 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="b0dc4-280">전자 메일 및 암호를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="b0dc4-281">로그 아웃 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="b0dc4-282">관리 페이지 보기</span><span class="sxs-lookup"><span data-stu-id="b0dc4-282">View the manage page</span></span>

<span data-ttu-id="b0dc4-283">브라우저에서 사용자 이름을 선택 합니다. ![사용자 이름이 있는 브라우저 창](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="b0dc4-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="b0dc4-284">사용 하 여 관리 페이지가 표시 되는 **프로필** 탭이 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="b0dc4-285">합니다 **전자 메일** 확인 된 전자 메일을 나타내는 확인란을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="b0dc4-286">테스트 암호 재설정</span><span class="sxs-lookup"><span data-stu-id="b0dc4-286">Test password reset</span></span>

* <span data-ttu-id="b0dc4-287">로그인 할 경우 선택할 **로그 아웃**합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="b0dc4-288">선택 합니다 **에 로그인** 연결 하 고 선택 합니다 **암호를 잊으셨나요?** 링크.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="b0dc4-289">계정을 등록 하는 데 전자 메일을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="b0dc4-290">암호 재설정에 대 한 링크가 포함 된 전자 메일이 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="b0dc4-291">전자 메일을 확인 하 고 암호 재설정에 대 한 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="b0dc4-292">암호가 성공적으로 다시 설정, 메일 및 새 암호를 사용 하 여 서명할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="b0dc4-293">전자 메일 및 작업 시간 제한 변경</span><span class="sxs-lookup"><span data-stu-id="b0dc4-293">Change email and activity timeout</span></span>

<span data-ttu-id="b0dc4-294">기본 비활성 시간은 14 일입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="b0dc4-295">다음 코드는 5 일에 비활성 시간을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="b0dc4-296">모든 데이터 보호 토큰 lifespans 변경</span><span class="sxs-lookup"><span data-stu-id="b0dc4-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="b0dc4-297">다음 코드는 3 시간으로 모든 데이터 보호 토큰 제한 시간을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="b0dc4-298">기본 제공 Id 사용자 토큰에 (참조 [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )가를 [하루 시간 제한](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="b0dc4-299">전자 메일 토큰 수명을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-299">Change the email token lifespan</span></span>

<span data-ttu-id="b0dc4-300">기본 토큰 수명을 [Id 사용자 토큰](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 됩니다 [하루](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-300">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="b0dc4-301">이 섹션에는 전자 메일 토큰 수명이 변경 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="b0dc4-302">사용자 지정을 추가 [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) 고 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="b0dc4-303">서비스 컨테이너에 사용자 지정 공급자를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="b0dc4-304">전자 메일 확인 다시 전송</span><span class="sxs-lookup"><span data-stu-id="b0dc4-304">Resend email confirmation</span></span>

<span data-ttu-id="b0dc4-305">참조 [이 GitHub 문제](https://github.com/aspnet/AspNetCore/issues/5410)합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-305">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="b0dc4-306">전자 메일을 디버그</span><span class="sxs-lookup"><span data-stu-id="b0dc4-306">Debug email</span></span>

<span data-ttu-id="b0dc4-307">경우 전자 메일 작업을 가져올 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-307">If you can't get email working:</span></span>

* <span data-ttu-id="b0dc4-308">중단점을 설정 `EmailSender.Execute` 확인 하려면 `SendGridClient.SendEmailAsync` 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="b0dc4-309">만들기는 [전자 메일을 보내는 콘솔 앱](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 비슷한 코드를 사용 하 여 `EmailSender.Execute`입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="b0dc4-310">검토 합니다 [전자 메일 작업](https://sendgrid.com/docs/User_Guide/email_activity.html) 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="b0dc4-311">스팸 폴더를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-311">Check your spam folder.</span></span>
* <span data-ttu-id="b0dc4-312">다른 전자 메일 별칭에는 다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)를 시도 하세요.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="b0dc4-313">다른 메일 계정으로 보내기를 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="b0dc4-314">**보안 모범 사례** 하는 것 **하지** 테스트 및 개발에서 프로덕션 비밀을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="b0dc4-315">Azure에 앱을 게시 하는 경우 Azure 웹 앱 포털에서 응용 프로그램 설정으로 SendGrid 비밀을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="b0dc4-316">구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="b0dc4-317">로컬 및 소셜 로그인 계정을 병합합니다</span><span class="sxs-lookup"><span data-stu-id="b0dc4-317">Combine social and local login accounts</span></span>

<span data-ttu-id="b0dc4-318">이 섹션을 완료 하려면 먼저 외부 인증 공급자를 활성화 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="b0dc4-319">참조 [Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="b0dc4-320">로컬 및 소셜 계정 전자 메일 링크를 클릭 하 여 결합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="b0dc4-321">그러나 다음 순서로 "RickAndMSFT@gmail.com" 로컬 로그인;으로 처음 만들어질 수 계정으로 소셜 로그인을 먼저 만든 다음 로컬 로그인을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![웹 응용 프로그램: RickAndMSFT@gmail.com 인증 된 사용자](accconfirm/_static/rick.png)

<span data-ttu-id="b0dc4-323">클릭 합니다 **관리** 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-323">Click on the **Manage** link.</span></span> <span data-ttu-id="b0dc4-324">이 계정과 연결 된 참고 0 외부 (소셜 로그인)</span><span class="sxs-lookup"><span data-stu-id="b0dc4-324">Note the 0 external (social logins) associated with this account.</span></span>

![보기 관리](accconfirm/_static/manage.png)

<span data-ttu-id="b0dc4-326">다른 로그인 서비스에 대 한 링크를 클릭 하 고 앱 요청을 수락 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="b0dc4-327">다음 이미지에서는 Facebook는 외부 인증 공급자:</span><span class="sxs-lookup"><span data-stu-id="b0dc4-327">In the following image, Facebook is the external authentication provider:</span></span>

![Facebook을 목록 외부 로그인 보기를 관리 합니다.](accconfirm/_static/fb.png)

<span data-ttu-id="b0dc4-329">두 개의 계정은 결합 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-329">The two accounts have been combined.</span></span> <span data-ttu-id="b0dc4-330">계정이 나 로그인 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-330">You are able to sign in with either account.</span></span> <span data-ttu-id="b0dc4-331">사용자가 자신의 소셜 로그인 인증 서비스가 다운 된 또는 가능성이 소셜 계정에 대 한 액세스를 손실 했습니다 되는 경우 로컬 계정을 추가 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="b0dc4-332">사이트 사용자 후 계정 확인을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="b0dc4-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="b0dc4-333">사용자를 사용 하 여 사이트에서 계정 확인을 사용 하도록 설정 하면 모든 기존 사용자를 잠급니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="b0dc4-334">해당 계정 확인 되지 때문에 기존 사용자가 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="b0dc4-335">기존 사용자 잠금 문제를 해결 하려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="b0dc4-336">로 확인 되 고 모든 기존 사용자를 표시 하도록 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="b0dc4-337">기존 사용자를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-337">Confirm existing users.</span></span> <span data-ttu-id="b0dc4-338">예를 들어 일괄 처리-송신 확인 링크를 사용 하 여 전자 메일입니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc4-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
