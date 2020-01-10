---
title: ASP.NET Core의 계정 확인 및 암호 복구
author: rick-anderson
description: 전자 메일 확인 및 암호 재설정을 사용 하 여 ASP.NET Core 앱을 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 49d3d214fd64edc5b17df2df929ddc3c2af47ede
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829272"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="ef431-103">ASP.NET Core의 계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="ef431-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="ef431-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)및 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="ef431-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="ef431-105">이 자습서에서는 전자 메일 확인 및 암호 재설정을 사용 하 여 ASP.NET Core 앱을 빌드하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="ef431-106">이 자습서는 시작 항목이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="ef431-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="ef431-107">에 대해 잘 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-107">You should be familiar with:</span></span>

* [<span data-ttu-id="ef431-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef431-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="ef431-109">인증</span><span class="sxs-lookup"><span data-stu-id="ef431-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="ef431-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="ef431-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="ef431-111">ASP.NET Core 1.1 버전은 [이 PDF 파일](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="ef431-112">전제 조건</span><span class="sxs-lookup"><span data-stu-id="ef431-112">Prerequisites</span></span>

[<span data-ttu-id="ef431-113">.NET Core 3.0 SDK 이상</span><span class="sxs-lookup"><span data-stu-id="ef431-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="ef431-114">인증을 사용 하 여 웹 앱 만들기 및 테스트</span><span class="sxs-lookup"><span data-stu-id="ef431-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="ef431-115">다음 명령을 실행 하 여 인증을 사용 하는 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="ef431-116">앱을 실행 하 고, **등록** 링크를 선택 하 고, 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="ef431-117">등록 되 면 전자 메일 확인을 시뮬레이트하는 링크를 포함 하는 `/Identity/Account/RegisterConfirmation` 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="ef431-118">`Click here to confirm your account` 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="ef431-119">**로그인** 링크를 선택 하 고 동일한 자격 증명을 사용 하 여 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="ef431-120">`/Identity/Account/Manage/PersonalData` 페이지로 리디렉션하는 `Hello YourEmail@provider.com!` 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="ef431-121">왼쪽에서 **개인 데이터** 탭을 선택 하 고 **삭제**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="ef431-122">전자 메일 공급자 구성</span><span class="sxs-lookup"><span data-stu-id="ef431-122">Configure an email provider</span></span>

<span data-ttu-id="ef431-123">이 자습서에서는 [SendGrid](https://sendgrid.com) 를 사용 하 여 전자 메일을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="ef431-124">전자 메일을 보내려면 SendGrid 계정 및 키가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="ef431-125">다른 전자 메일 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-125">You can use other email providers.</span></span> <span data-ttu-id="ef431-126">SendGrid 또는 다른 전자 메일 서비스를 사용 하 여 전자 메일을 보내는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="ef431-127">SMTP는 안전 하 게 보호 하 고 올바르게 설정 하기 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="ef431-128">보안 전자 메일 키를 인출 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="ef431-129">이 샘플의 경우 *Services/AuthMessageSenderOptions*을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="ef431-130">SendGrid 사용자 비밀 구성</span><span class="sxs-lookup"><span data-stu-id="ef431-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="ef431-131">`SendGridUser`를 설정 하 고 [암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 `SendGridKey` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="ef431-132">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="ef431-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="ef431-133">Windows에서 Secret Manager는 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 디렉터리에 있는 *비밀.* i n t 파일의 키/값 쌍을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="ef431-134">*비밀. json* 파일의 내용이 암호화 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="ef431-135">다음 태그는 *비밀. json* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="ef431-136">`SendGridKey` 값이 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="ef431-137">자세한 내용은 [옵션 패턴](xref:fundamentals/configuration/options) 및 [구성](xref:fundamentals/configuration/index)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="ef431-138">SendGrid 설치</span><span class="sxs-lookup"><span data-stu-id="ef431-138">Install SendGrid</span></span>

<span data-ttu-id="ef431-139">이 자습서에서는 [SendGrid](https://sendgrid.com/)를 통해 전자 메일 알림을 추가 하는 방법을 보여 주지만 SMTP 및 기타 메커니즘을 사용 하 여 전자 메일을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="ef431-140">`SendGrid` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ef431-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef431-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ef431-142">패키지 관리자 콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ef431-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ef431-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ef431-144">콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="ef431-145">무료 SendGrid 계정에 등록 하려면 [무료로 SendGrid 시작](https://sendgrid.com/free/) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="ef431-146">IEmailSender 구현</span><span class="sxs-lookup"><span data-stu-id="ef431-146">Implement IEmailSender</span></span>

<span data-ttu-id="ef431-147">`IEmailSender`를 구현 하려면 다음과 유사한 코드를 사용 하 여 *서비스/EmailSender. cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="ef431-148">전자 메일을 지원 하도록 시작 구성</span><span class="sxs-lookup"><span data-stu-id="ef431-148">Configure startup to support email</span></span>

<span data-ttu-id="ef431-149">*Startup.cs* 파일의 `ConfigureServices` 메서드에 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="ef431-150">임시 서비스로 `EmailSender`를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="ef431-151">`AuthMessageSenderOptions` 구성 인스턴스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="ef431-152">등록, 전자 메일 확인 및 암호 재설정</span><span class="sxs-lookup"><span data-stu-id="ef431-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="ef431-153">웹 앱을 실행 하 고 계정 확인 및 암호 복구 흐름을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="ef431-154">앱을 실행 하 고 새 사용자 등록</span><span class="sxs-lookup"><span data-stu-id="ef431-154">Run the app and register a new user</span></span>
* <span data-ttu-id="ef431-155">계정 확인 링크에 대 한 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="ef431-156">전자 메일을 받을 수 없는 경우 [디버그 전자 메일](#debug) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="ef431-157">링크를 클릭 하 여 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="ef431-158">전자 메일 및 암호를 사용 하 여 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="ef431-159">로그아웃합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="ef431-160">암호 재설정 테스트</span><span class="sxs-lookup"><span data-stu-id="ef431-160">Test password reset</span></span>

* <span data-ttu-id="ef431-161">로그인 하는 경우 **로그 아웃**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="ef431-162">**로그인** 링크를 선택 하 고 암호를 **잊으셨나요?** 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="ef431-163">계정을 등록 하는 데 사용한 전자 메일을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="ef431-164">암호를 재설정 하는 링크가 포함 된 전자 메일이 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="ef431-165">전자 메일을 확인 하 고 링크를 클릭 하 여 암호를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="ef431-166">암호를 성공적으로 재설정 한 후에는 전자 메일 및 새 암호로 로그인 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="ef431-167">전자 메일 및 작업 시간 제한 변경</span><span class="sxs-lookup"><span data-stu-id="ef431-167">Change email and activity timeout</span></span>

<span data-ttu-id="ef431-168">기본 비활성 시간 제한은 14 일입니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="ef431-169">다음 코드는 비활성 시간 제한을 5 일로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="ef431-170">모든 데이터 보호 토큰 변경 lifespans</span><span class="sxs-lookup"><span data-stu-id="ef431-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="ef431-171">다음 코드는 모든 데이터 보호 토큰 제한 시간을 3 시간으로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="ef431-172">기본 제공 Id 사용자 토큰 ( [AspNetCore/src/identity/extension. Core/src/TokenOptions](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )은 [하루에 한 시간 제한이](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="ef431-173">전자 메일 토큰 수명 변경</span><span class="sxs-lookup"><span data-stu-id="ef431-173">Change the email token lifespan</span></span>

<span data-ttu-id="ef431-174">[Id 사용자 토큰](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 의 기본 토큰 수명은 [1 일](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)입니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-174">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="ef431-175">이 섹션에서는 전자 메일 토큰 수명을 변경 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="ef431-176">사용자 지정 [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) 를 추가 하 고 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="ef431-177">서비스 컨테이너에 사용자 지정 공급자를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="ef431-178">전자 메일 다시 보내기 확인</span><span class="sxs-lookup"><span data-stu-id="ef431-178">Resend email confirmation</span></span>

<span data-ttu-id="ef431-179">이 [GitHub 문제](https://github.com/dotnet/AspNetCore/issues/5410)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-179">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="ef431-180">디버그 전자 메일</span><span class="sxs-lookup"><span data-stu-id="ef431-180">Debug email</span></span>

<span data-ttu-id="ef431-181">전자 메일을 사용할 수 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="ef431-181">If you can't get email working:</span></span>

* <span data-ttu-id="ef431-182">`EmailSender.Execute`의 중단점을 설정 하 여 `SendGridClient.SendEmailAsync` 호출 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="ef431-183">`EmailSender.Execute`하는 비슷한 코드를 사용 하 여 [전자 메일을 보내는 콘솔 앱](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="ef431-184">[전자 메일 작업](https://sendgrid.com/docs/User_Guide/email_activity.html) 페이지를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="ef431-185">스팸 폴더를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-185">Check your spam folder.</span></span>
* <span data-ttu-id="ef431-186">다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)에서 다른 전자 메일 별칭을 사용해 보세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="ef431-187">다른 전자 메일 계정으로 전송 해 보세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="ef431-188">**보안 모범 사례** 는 테스트 및 개발에서 프로덕션 암호를 사용 **하지 않는** 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="ef431-189">Azure에 앱을 게시 하는 경우 Azure 웹 앱 포털에서 SendGrid 비밀을 응용 프로그램 설정으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="ef431-190">구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="ef431-191">소셜 및 로컬 로그인 계정 결합</span><span class="sxs-lookup"><span data-stu-id="ef431-191">Combine social and local login accounts</span></span>

<span data-ttu-id="ef431-192">이 섹션을 완료 하려면 먼저 외부 인증 공급자를 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="ef431-193">[Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="ef431-194">전자 메일 링크를 클릭 하 여 로컬 및 소셜 계정을 결합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="ef431-195">다음 시퀀스에서 "RickAndMSFT@gmail.com"는 먼저 로컬 로그인으로 만들어집니다. 그러나 계정을 소셜 로그인으로 먼저 만든 다음 로컬 로그인을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![웹 응용 프로그램: RickAndMSFT@gmail.com 사용자 인증 됨](accconfirm/_static/rick.png)

<span data-ttu-id="ef431-197">**관리** 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-197">Click on the **Manage** link.</span></span> <span data-ttu-id="ef431-198">이 계정과 연결 된 0 외부 (소셜 로그인)를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-198">Note the 0 external (social logins) associated with this account.</span></span>

![뷰 관리](accconfirm/_static/manage.png)

<span data-ttu-id="ef431-200">다른 로그인 서비스에 대 한 링크를 클릭 하 고 앱 요청을 수락 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="ef431-201">다음 이미지에서 Facebook은 외부 인증 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-201">In the following image, Facebook is the external authentication provider:</span></span>

![외부 로그인 보기 관리 Facebook 목록](accconfirm/_static/fb.png)

<span data-ttu-id="ef431-203">두 계정을 결합 했습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-203">The two accounts have been combined.</span></span> <span data-ttu-id="ef431-204">두 계정을 사용 하 여 로그인 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-204">You are able to sign in with either account.</span></span> <span data-ttu-id="ef431-205">소셜 로그인 인증 서비스가 다운 되거나 소셜 계정에 대 한 액세스 권한이 손실 될 가능성이 있는 경우 사용자가 로컬 계정을 추가 하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="ef431-206">사이트에 사용자가 있는 후 계정 확인 사용</span><span class="sxs-lookup"><span data-stu-id="ef431-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="ef431-207">사용자가 있는 사이트에서 계정 확인을 사용 하도록 설정 하면 모든 기존 사용자가 잠깁니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="ef431-208">계정이 확인 되지 않았기 때문에 기존 사용자가 잠깁니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="ef431-209">기존 사용자 잠금을 해결 하려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="ef431-210">기존 사용자를 모두 확인 된 것으로 표시 하도록 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="ef431-211">기존 사용자를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-211">Confirm existing users.</span></span> <span data-ttu-id="ef431-212">예를 들어, 확인 링크가 포함 된 메일을 일괄 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="ef431-213">전제 조건</span><span class="sxs-lookup"><span data-stu-id="ef431-213">Prerequisites</span></span>

[<span data-ttu-id="ef431-214">.NET Core 2.2 SDK 이상</span><span class="sxs-lookup"><span data-stu-id="ef431-214">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="ef431-215">웹 앱 및 스 캐 폴드 Id 만들기</span><span class="sxs-lookup"><span data-stu-id="ef431-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="ef431-216">다음 명령을 실행 하 여 인증을 사용 하는 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-216">Run the following commands to create a web app with authentication.</span></span>

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

## <a name="test-new-user-registration"></a><span data-ttu-id="ef431-217">새 사용자 등록 테스트</span><span class="sxs-lookup"><span data-stu-id="ef431-217">Test new user registration</span></span>

<span data-ttu-id="ef431-218">앱을 실행 하 고, **등록** 링크를 선택 하 고, 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="ef431-219">이 시점에서 전자 메일에 대 한 유일한 유효성 검사는 [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-219">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="ef431-220">등록을 제출 하면 앱에 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="ef431-221">자습서의 뒷부분에서 새로운 사용자가 전자 메일의 유효성을 검사할 때까지 로그인 할 수 없도록 코드를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="ef431-222">테이블의 `EmailConfirmed` 필드가 `False`됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="ef431-223">앱에서 확인 전자 메일을 보낼 때 다음 단계에서이 전자 메일을 다시 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="ef431-224">행을 마우스 오른쪽 단추로 클릭 하 고 **삭제**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="ef431-225">전자 메일 별칭을 삭제 하면 다음 단계에서 보다 쉽게 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="ef431-226">전자 메일 확인 필요</span><span class="sxs-lookup"><span data-stu-id="ef431-226">Require email confirmation</span></span>

<span data-ttu-id="ef431-227">새 사용자 등록의 전자 메일을 확인 하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="ef431-228">전자 메일 확인을 사용 하면 다른 사용자를 가장 하지 않는 것을 확인할 수 있습니다. 즉, 다른 사용자의 전자 메일을 사용 하 여 등록 하지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="ef431-229">토론 포럼이 있고 "yli@example.com"이 "nolivetto@contoso.com"로 등록 되지 않도록 하려는 경우를 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="ef431-230">전자 메일을 확인 하지 않고 "nolivetto@contoso.com"는 앱에서 원치 않는 전자 메일을 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="ef431-231">사용자가 실수로 "ylo@example.com"로 등록 되어 있고, 사용자가 "yli"의 철자를 발견 했다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="ef431-232">앱에 올바른 전자 메일이 없기 때문에 암호 복구를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="ef431-233">전자 메일 확인은 봇에서 제한 된 보호 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="ef431-234">전자 메일 확인은 전자 메일 계정이 많은 악의적인 사용자 로부터 보호를 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="ef431-235">일반적으로 새 사용자가 확인 된 전자 메일을 보내기 전에 데이터를 웹 사이트에 게시 하는 것을 방지 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="ef431-236">확인 된 전자 메일을 요구 하도록 `Startup.ConfigureServices`를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="ef431-237">`config.SignIn.RequireConfirmedEmail = true;` 등록 된 사용자가 전자 메일을 확인할 때까지 로그인 하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="ef431-238">전자 메일 공급자 구성</span><span class="sxs-lookup"><span data-stu-id="ef431-238">Configure email provider</span></span>

<span data-ttu-id="ef431-239">이 자습서에서는 [SendGrid](https://sendgrid.com) 를 사용 하 여 전자 메일을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="ef431-240">전자 메일을 보내려면 SendGrid 계정 및 키가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="ef431-241">다른 전자 메일 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-241">You can use other email providers.</span></span> <span data-ttu-id="ef431-242">ASP.NET Core 2.x에는 앱에서 전자 메일을 보낼 수 있도록 하는 `System.Net.Mail`포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="ef431-243">SendGrid 또는 다른 전자 메일 서비스를 사용 하 여 전자 메일을 보내는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="ef431-244">SMTP는 안전 하 게 보호 하 고 올바르게 설정 하기 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="ef431-245">보안 전자 메일 키를 인출 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="ef431-246">이 샘플의 경우 *Services/AuthMessageSenderOptions*을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="ef431-247">SendGrid 사용자 비밀 구성</span><span class="sxs-lookup"><span data-stu-id="ef431-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="ef431-248">`SendGridUser`를 설정 하 고 [암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 `SendGridKey` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="ef431-249">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="ef431-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="ef431-250">Windows에서 Secret Manager는 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 디렉터리에 있는 *비밀.* i n t 파일의 키/값 쌍을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="ef431-251">*비밀. json* 파일의 내용이 암호화 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="ef431-252">다음 태그는 *비밀. json* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="ef431-253">`SendGridKey` 값이 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="ef431-254">자세한 내용은 [옵션 패턴](xref:fundamentals/configuration/options) 및 [구성](xref:fundamentals/configuration/index)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="ef431-255">SendGrid 설치</span><span class="sxs-lookup"><span data-stu-id="ef431-255">Install SendGrid</span></span>

<span data-ttu-id="ef431-256">이 자습서에서는 [SendGrid](https://sendgrid.com/)를 통해 전자 메일 알림을 추가 하는 방법을 보여 주지만 SMTP 및 기타 메커니즘을 사용 하 여 전자 메일을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="ef431-257">`SendGrid` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ef431-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef431-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ef431-259">패키지 관리자 콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-259">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ef431-260">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ef431-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ef431-261">콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-261">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="ef431-262">무료 SendGrid 계정에 등록 하려면 [무료로 SendGrid 시작](https://sendgrid.com/free/) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="ef431-263">IEmailSender 구현</span><span class="sxs-lookup"><span data-stu-id="ef431-263">Implement IEmailSender</span></span>

<span data-ttu-id="ef431-264">`IEmailSender`를 구현 하려면 다음과 유사한 코드를 사용 하 여 *서비스/EmailSender. cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="ef431-265">전자 메일을 지원 하도록 시작 구성</span><span class="sxs-lookup"><span data-stu-id="ef431-265">Configure startup to support email</span></span>

<span data-ttu-id="ef431-266">*Startup.cs* 파일의 `ConfigureServices` 메서드에 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="ef431-267">임시 서비스로 `EmailSender`를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="ef431-268">`AuthMessageSenderOptions` 구성 인스턴스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="ef431-269">계정 확인 및 암호 복구 사용</span><span class="sxs-lookup"><span data-stu-id="ef431-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="ef431-270">템플릿에는 계정 확인 및 암호 복구를 위한 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="ef431-271">*영역/i d/페이지/계정/* a s e. s s o. s s o. c o l s. `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="ef431-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="ef431-272">다음 줄을 주석으로 처리 하 여 새로 등록 된 사용자가 자동으로 로그인 하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="ef431-273">전체 메서드는 변경 된 줄이 강조 표시 된 상태로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="ef431-274">등록, 전자 메일 확인 및 암호 재설정</span><span class="sxs-lookup"><span data-stu-id="ef431-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="ef431-275">웹 앱을 실행 하 고 계정 확인 및 암호 복구 흐름을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="ef431-276">앱을 실행 하 고 새 사용자 등록</span><span class="sxs-lookup"><span data-stu-id="ef431-276">Run the app and register a new user</span></span>
* <span data-ttu-id="ef431-277">계정 확인 링크에 대 한 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="ef431-278">전자 메일을 받을 수 없는 경우 [디버그 전자 메일](#debug) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="ef431-279">링크를 클릭 하 여 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="ef431-280">전자 메일 및 암호를 사용 하 여 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="ef431-281">로그아웃합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="ef431-282">관리 페이지 보기</span><span class="sxs-lookup"><span data-stu-id="ef431-282">View the manage page</span></span>

<span data-ttu-id="ef431-283">브라우저: 사용자 이름 ![브라우저 창에서 사용자 이름을 선택](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="ef431-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="ef431-284">관리 페이지는 **프로필** 탭이 선택 된 상태로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="ef431-285">전자 **메일** 에는 전자 메일이 확인 되었다는 확인란이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="ef431-286">암호 재설정 테스트</span><span class="sxs-lookup"><span data-stu-id="ef431-286">Test password reset</span></span>

* <span data-ttu-id="ef431-287">로그인 하는 경우 **로그 아웃**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="ef431-288">**로그인** 링크를 선택 하 고 암호를 **잊으셨나요?** 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="ef431-289">계정을 등록 하는 데 사용한 전자 메일을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="ef431-290">암호를 재설정 하는 링크가 포함 된 전자 메일이 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="ef431-291">전자 메일을 확인 하 고 링크를 클릭 하 여 암호를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="ef431-292">암호를 성공적으로 재설정 한 후에는 전자 메일 및 새 암호로 로그인 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="ef431-293">전자 메일 및 작업 시간 제한 변경</span><span class="sxs-lookup"><span data-stu-id="ef431-293">Change email and activity timeout</span></span>

<span data-ttu-id="ef431-294">기본 비활성 시간 제한은 14 일입니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="ef431-295">다음 코드는 비활성 시간 제한을 5 일로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="ef431-296">모든 데이터 보호 토큰 변경 lifespans</span><span class="sxs-lookup"><span data-stu-id="ef431-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="ef431-297">다음 코드는 모든 데이터 보호 토큰 제한 시간을 3 시간으로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="ef431-298">기본 제공 Id 사용자 토큰 ( [AspNetCore/src/identity/extension. Core/src/TokenOptions](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )은 [하루에 한 시간 제한이](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="ef431-299">전자 메일 토큰 수명 변경</span><span class="sxs-lookup"><span data-stu-id="ef431-299">Change the email token lifespan</span></span>

<span data-ttu-id="ef431-300">[Id 사용자 토큰](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 의 기본 토큰 수명은 [1 일](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)입니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-300">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="ef431-301">이 섹션에서는 전자 메일 토큰 수명을 변경 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="ef431-302">사용자 지정 [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) 를 추가 하 고 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="ef431-303">서비스 컨테이너에 사용자 지정 공급자를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="ef431-304">전자 메일 다시 보내기 확인</span><span class="sxs-lookup"><span data-stu-id="ef431-304">Resend email confirmation</span></span>

<span data-ttu-id="ef431-305">이 [GitHub 문제](https://github.com/dotnet/AspNetCore/issues/5410)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-305">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="ef431-306">디버그 전자 메일</span><span class="sxs-lookup"><span data-stu-id="ef431-306">Debug email</span></span>

<span data-ttu-id="ef431-307">전자 메일을 사용할 수 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="ef431-307">If you can't get email working:</span></span>

* <span data-ttu-id="ef431-308">`EmailSender.Execute`의 중단점을 설정 하 여 `SendGridClient.SendEmailAsync` 호출 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="ef431-309">`EmailSender.Execute`하는 비슷한 코드를 사용 하 여 [전자 메일을 보내는 콘솔 앱](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="ef431-310">[전자 메일 작업](https://sendgrid.com/docs/User_Guide/email_activity.html) 페이지를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="ef431-311">스팸 폴더를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-311">Check your spam folder.</span></span>
* <span data-ttu-id="ef431-312">다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)에서 다른 전자 메일 별칭을 사용해 보세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="ef431-313">다른 전자 메일 계정으로 전송 해 보세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="ef431-314">**보안 모범 사례** 는 테스트 및 개발에서 프로덕션 암호를 사용 **하지 않는** 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="ef431-315">Azure에 앱을 게시 하는 경우 Azure 웹 앱 포털에서 응용 프로그램 설정으로 SendGrid 암호를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="ef431-316">구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="ef431-317">소셜 및 로컬 로그인 계정 결합</span><span class="sxs-lookup"><span data-stu-id="ef431-317">Combine social and local login accounts</span></span>

<span data-ttu-id="ef431-318">이 섹션을 완료 하려면 먼저 외부 인증 공급자를 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="ef431-319">[Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ef431-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="ef431-320">전자 메일 링크를 클릭 하 여 로컬 및 소셜 계정을 결합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="ef431-321">다음 시퀀스에서 "RickAndMSFT@gmail.com"는 먼저 로컬 로그인으로 만들어집니다. 그러나 계정을 소셜 로그인으로 먼저 만든 다음 로컬 로그인을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![웹 응용 프로그램: RickAndMSFT@gmail.com 사용자 인증 됨](accconfirm/_static/rick.png)

<span data-ttu-id="ef431-323">**관리** 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-323">Click on the **Manage** link.</span></span> <span data-ttu-id="ef431-324">이 계정과 연결 된 0 외부 (소셜 로그인)를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-324">Note the 0 external (social logins) associated with this account.</span></span>

![뷰 관리](accconfirm/_static/manage.png)

<span data-ttu-id="ef431-326">다른 로그인 서비스에 대 한 링크를 클릭 하 고 앱 요청을 수락 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="ef431-327">다음 이미지에서 Facebook은 외부 인증 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-327">In the following image, Facebook is the external authentication provider:</span></span>

![외부 로그인 보기 관리 Facebook 목록](accconfirm/_static/fb.png)

<span data-ttu-id="ef431-329">두 계정을 결합 했습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-329">The two accounts have been combined.</span></span> <span data-ttu-id="ef431-330">두 계정을 사용 하 여 로그인 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-330">You are able to sign in with either account.</span></span> <span data-ttu-id="ef431-331">소셜 로그인 인증 서비스가 다운 되거나 소셜 계정에 대 한 액세스 권한이 손실 될 가능성이 있는 경우 사용자가 로컬 계정을 추가 하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="ef431-332">사이트에 사용자가 있는 후 계정 확인 사용</span><span class="sxs-lookup"><span data-stu-id="ef431-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="ef431-333">사용자가 있는 사이트에서 계정 확인을 사용 하도록 설정 하면 모든 기존 사용자가 잠깁니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="ef431-334">계정이 확인 되지 않았기 때문에 기존 사용자가 잠깁니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="ef431-335">기존 사용자 잠금을 해결 하려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="ef431-336">기존 사용자를 모두 확인 된 것으로 표시 하도록 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="ef431-337">기존 사용자를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-337">Confirm existing users.</span></span> <span data-ttu-id="ef431-338">예를 들어, 확인 링크가 포함 된 메일을 일괄 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="ef431-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
