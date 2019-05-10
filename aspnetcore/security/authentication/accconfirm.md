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
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="f0da5-103">계정 확인 및 ASP.NET Core에서 암호 복구</span><span class="sxs-lookup"><span data-stu-id="f0da5-103">Account confirmation and password recovery in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="f0da5-104">참조 [이 PDF 파일](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) ASP.NET Core 1.1 및 2.1 버전에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-104">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 and 2.1 version.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f0da5-105">하 여 [Rick Anderson](https://twitter.com/RickAndMSFT)하십시오 [Ponant](https://github.com/Ponant), 및 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="f0da5-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="f0da5-106">이 자습서에서는 전자 메일 확인 및 암호 재설정을 사용 하 여 ASP.NET Core 앱을 빌드하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-106">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="f0da5-107">이 자습서는 **되지** 시작 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-107">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="f0da5-108">에 대해 잘 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-108">You should be familiar with:</span></span>

* [<span data-ttu-id="f0da5-109">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f0da5-109">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="f0da5-110">인증</span><span class="sxs-lookup"><span data-stu-id="f0da5-110">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="f0da5-111">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f0da5-111">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

## <a name="prerequisites"></a><span data-ttu-id="f0da5-112">전제 조건</span><span class="sxs-lookup"><span data-stu-id="f0da5-112">Prerequisites</span></span>

[<span data-ttu-id="f0da5-113">.NET core 2.2 SDK 이상</span><span class="sxs-lookup"><span data-stu-id="f0da5-113">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="f0da5-114">웹 앱 만들기 및 Identity를 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="f0da5-114">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="f0da5-115">인증을 사용 하 여 웹 앱을 만들려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-115">Run the following commands to create a web app with authentication.</span></span>

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

## <a name="test-new-user-registration"></a><span data-ttu-id="f0da5-116">새 사용자 등록 테스트</span><span class="sxs-lookup"><span data-stu-id="f0da5-116">Test new user registration</span></span>

<span data-ttu-id="f0da5-117">앱 실행을 선택 합니다 **등록** 링크를 선택한 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-117">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="f0da5-118">이 시점에서 전자 메일에만 유효성 검사 된 합니다 [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-118">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="f0da5-119">등록을 제출 후 앱에 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-119">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="f0da5-120">자습서의 뒷부분에 나오는 코드에는 전자 메일의 유효성을 검사할 때까지 새 사용자가 로그인 할 수 없습니다 있도록 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-120">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="f0da5-121">테이블의 유의 `EmailConfirmed` 필드는 `False`합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-121">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="f0da5-122">앱에서 확인 전자 메일을 보낼 때 다음 단계에서는이 전자 메일에 다시 사용 하려는.</span><span class="sxs-lookup"><span data-stu-id="f0da5-122">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="f0da5-123">선택한 행을 마우스 오른쪽 단추로 클릭 **삭제**합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-123">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="f0da5-124">전자 메일 별칭을 삭제 하면 쉽게 다음 단계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-124">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="f0da5-125">전자 메일 확인이 필요</span><span class="sxs-lookup"><span data-stu-id="f0da5-125">Require email confirmation</span></span>

<span data-ttu-id="f0da5-126">새 사용자 등록 전자 메일을 확인 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-126">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="f0da5-127">다른 사람이 가장 하지는 확인 하려면 확인 하면 전자 메일 (즉, 다른 사용자의 전자 메일을 사용 하 여 등록 하지 않은).</span><span class="sxs-lookup"><span data-stu-id="f0da5-127">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="f0da5-128">토론 포럼을 했으며 방지 하려고 한다고 가정해 보겠습니다 "yli@example.com"등록"에서nolivetto@contoso.com"입니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-128">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="f0da5-129">전자 메일 확인 하지 않고 "nolivetto@contoso.com" 앱에서 원치 않는 전자 메일을 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-129">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="f0da5-130">사용자는 실수로로 등록 되어 있다고 가정 "ylo@example.com" 및 "yli"의 오타 눈치채 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-130">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="f0da5-131">이러한 앱에 올바른 전자 메일 없기 때문에 암호 복구를 사용 하려면 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-131">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="f0da5-132">전자 메일 확인 봇을에서 제한 된 보호를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-132">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="f0da5-133">전자 메일 확인 전자 메일 계정의 수를 사용 하 여 악의적인 사용자 로부터 보호를 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-133">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="f0da5-134">일반적으로 새 사용자가 전자 메일을 확인된 하기 전에 먼저 웹 사이트에 데이터를 게시 하지 못하도록 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-134">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="f0da5-135">업데이트 `Startup.ConfigureServices` 전자 메일을 확인된 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-135">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="f0da5-136">`config.SignIn.RequireConfirmedEmail = true;` 등록 된 사용자를가 해당 전자 메일이 확인 될 때까지 로그인 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-136">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="f0da5-137">전자 메일 공급자 구성</span><span class="sxs-lookup"><span data-stu-id="f0da5-137">Configure email provider</span></span>

<span data-ttu-id="f0da5-138">이 자습서에서는 [SendGrid](https://sendgrid.com) 전자 메일을 보내는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-138">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="f0da5-139">SendGrid 계정 및 전자 메일을 보내는 키 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-139">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="f0da5-140">다른 이메일 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-140">You can use other email providers.</span></span> <span data-ttu-id="f0da5-141">ASP.NET Core 2.x 포함 `System.Net.Mail`, 앱에서 전자 메일을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-141">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="f0da5-142">전자 메일을 보내려면 SendGrid 또는 다른 전자 메일 서비스를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-142">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="f0da5-143">SMTP를 보호 하 고 올바르게 설정 하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-143">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="f0da5-144">전자 메일 보안 키를 인출 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-144">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="f0da5-145">이 샘플을 만들 *Services/AuthMessageSenderOptions.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0da5-145">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="f0da5-146">SendGrid 사용자 암호를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-146">Configure SendGrid user secrets</span></span>

<span data-ttu-id="f0da5-147">설정 합니다 `SendGridUser` 하 고 `SendGridKey` 사용 하 여는 [암호 관리자 도구](xref:security/app-secrets)합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-147">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="f0da5-148">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="f0da5-148">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="f0da5-149">Windows, 암호 관리자의 키/값 쌍 저장 하는 *secrets.json* 파일을 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-149">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="f0da5-150">콘텐츠를 *secrets.json* 파일 암호화 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-150">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="f0da5-151">에서는 다음 태그를 *secrets.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-151">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="f0da5-152">`SendGridKey` 값이 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-152">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="f0da5-153">자세한 내용은 참조는 [옵션 패턴](xref:fundamentals/configuration/options) 및 [구성](xref:fundamentals/configuration/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-153">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="f0da5-154">SendGrid를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-154">Install SendGrid</span></span>

<span data-ttu-id="f0da5-155">이 자습서를 통해 전자 메일 알림을 추가 하는 방법을 보여 줍니다 [SendGrid](https://sendgrid.com/), 하지만 SMTP 및 다른 메커니즘을 사용 하 여 메일을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-155">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="f0da5-156">설치는 `SendGrid` NuGet 패키지:</span><span class="sxs-lookup"><span data-stu-id="f0da5-156">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f0da5-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0da5-157">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f0da5-158">패키지 관리자 콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-158">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f0da5-159">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f0da5-159">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f0da5-160">콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-160">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

---

<span data-ttu-id="f0da5-161">참조 [SendGrid를 사용 하 여 시작 해 보세요](https://sendgrid.com/free/) 무료 SendGrid 계정을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-161">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="f0da5-162">IEmailSender 구현</span><span class="sxs-lookup"><span data-stu-id="f0da5-162">Implement IEmailSender</span></span>

<span data-ttu-id="f0da5-163">구현 `IEmailSender`를 만듭니다 *Services/EmailSender.cs* 다음과 비슷한 코드를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="f0da5-163">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="f0da5-164">시작 전자 메일을 지원 하도록 구성</span><span class="sxs-lookup"><span data-stu-id="f0da5-164">Configure startup to support email</span></span>

<span data-ttu-id="f0da5-165">다음 코드를 추가 합니다 `ConfigureServices` 의 메서드를 *Startup.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="f0da5-165">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="f0da5-166">추가 `EmailSender` 일시적인 서비스로 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-166">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="f0da5-167">등록 된 `AuthMessageSenderOptions` 구성 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="f0da5-167">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="f0da5-168">계정 확인 및 암호 복구를 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="f0da5-168">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="f0da5-169">서식 파일에 계정 확인 및 암호 복구를 위한 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-169">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="f0da5-170">찾을 합니다 `OnPostAsync` 의 메서드 *Areas/Identity/Pages/Account/Register.cshtml.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-170">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="f0da5-171">다음 줄을 주석으로 자동으로 로그인 되 새로 등록 된 사용자를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-171">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="f0da5-172">전체 메서드는 강조 표시 된 변경 된 줄으로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-172">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="f0da5-173">등록 하 고, 전자 메일을 확인 하 고, 암호 재설정</span><span class="sxs-lookup"><span data-stu-id="f0da5-173">Register, confirm email, and reset password</span></span>

<span data-ttu-id="f0da5-174">웹 앱을 실행 하 고 계정 확인 및 암호 복구 흐름을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-174">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="f0da5-175">앱을 실행 하 고 새 사용자 등록</span><span class="sxs-lookup"><span data-stu-id="f0da5-175">Run the app and register a new user</span></span>
* <span data-ttu-id="f0da5-176">계정 확인 링크에 대 한 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-176">Check your email for the account confirmation link.</span></span> <span data-ttu-id="f0da5-177">참조 [전자 메일을 디버그](#debug) 전자 메일을 얻지 못한 경우.</span><span class="sxs-lookup"><span data-stu-id="f0da5-177">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="f0da5-178">전자 메일 확인 하기 위한 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-178">Click the link to confirm your email.</span></span>
* <span data-ttu-id="f0da5-179">전자 메일 및 암호를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-179">Sign in with your email and password.</span></span>
* <span data-ttu-id="f0da5-180">로그 아웃 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-180">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="f0da5-181">관리 페이지 보기</span><span class="sxs-lookup"><span data-stu-id="f0da5-181">View the manage page</span></span>

<span data-ttu-id="f0da5-182">브라우저에서 사용자 이름을 선택 합니다. ![사용자 이름이 있는 브라우저 창](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="f0da5-182">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="f0da5-183">사용 하 여 관리 페이지가 표시 되는 **프로필** 탭이 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-183">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="f0da5-184">합니다 **전자 메일** 확인 된 전자 메일을 나타내는 확인란을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-184">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="f0da5-185">테스트 암호 재설정</span><span class="sxs-lookup"><span data-stu-id="f0da5-185">Test password reset</span></span>

* <span data-ttu-id="f0da5-186">로그인 할 경우 선택할 **로그 아웃**합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-186">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="f0da5-187">선택 합니다 **에 로그인** 연결 하 고 선택 합니다 **암호를 잊으셨나요?** 링크.</span><span class="sxs-lookup"><span data-stu-id="f0da5-187">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="f0da5-188">계정을 등록 하는 데 전자 메일을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-188">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="f0da5-189">암호 재설정에 대 한 링크가 포함 된 전자 메일이 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-189">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="f0da5-190">전자 메일을 확인 하 고 암호 재설정에 대 한 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-190">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="f0da5-191">암호가 성공적으로 다시 설정, 메일 및 새 암호를 사용 하 여 서명할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-191">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="f0da5-192">전자 메일 및 작업 시간 제한 변경</span><span class="sxs-lookup"><span data-stu-id="f0da5-192">Change email and activity timeout</span></span>

<span data-ttu-id="f0da5-193">기본 비활성 시간은 14 일입니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-193">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="f0da5-194">다음 코드는 5 일에 비활성 시간을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-194">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="f0da5-195">모든 데이터 보호 토큰 lifespans 변경</span><span class="sxs-lookup"><span data-stu-id="f0da5-195">Change all data protection token lifespans</span></span>

<span data-ttu-id="f0da5-196">다음 코드는 3 시간으로 모든 데이터 보호 토큰 제한 시간을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-196">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="f0da5-197">기본 제공 Id 사용자 토큰에 (참조 [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )가를 [하루 시간 제한](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-197">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="f0da5-198">전자 메일 토큰 수명을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-198">Change the email token lifespan</span></span>

<span data-ttu-id="f0da5-199">기본 토큰 수명을 [Id 사용자 토큰](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 됩니다 [하루](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-199">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="f0da5-200">이 섹션에는 전자 메일 토큰 수명이 변경 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-200">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="f0da5-201">사용자 지정을 추가 [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) 고 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="f0da5-201">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="f0da5-202">서비스 컨테이너에 사용자 지정 공급자를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-202">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="f0da5-203">전자 메일 확인 다시 전송</span><span class="sxs-lookup"><span data-stu-id="f0da5-203">Resend email confirmation</span></span>

<span data-ttu-id="f0da5-204">참조 [이 GitHub 문제](https://github.com/aspnet/AspNetCore/issues/5410)합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-204">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="f0da5-205">전자 메일을 디버그</span><span class="sxs-lookup"><span data-stu-id="f0da5-205">Debug email</span></span>

<span data-ttu-id="f0da5-206">경우 전자 메일 작업을 가져올 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-206">If you can't get email working:</span></span>

* <span data-ttu-id="f0da5-207">중단점을 설정 `EmailSender.Execute` 확인 하려면 `SendGridClient.SendEmailAsync` 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-207">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="f0da5-208">만들기는 [전자 메일을 보내는 콘솔 앱](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 비슷한 코드를 사용 하 여 `EmailSender.Execute`입니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-208">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="f0da5-209">검토 합니다 [전자 메일 작업](https://sendgrid.com/docs/User_Guide/email_activity.html) 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-209">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="f0da5-210">스팸 폴더를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-210">Check your spam folder.</span></span>
* <span data-ttu-id="f0da5-211">다른 전자 메일 별칭에는 다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)를 시도 하세요.</span><span class="sxs-lookup"><span data-stu-id="f0da5-211">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="f0da5-212">다른 메일 계정으로 보내기를 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-212">Try sending to different email accounts.</span></span>

<span data-ttu-id="f0da5-213">**보안 모범 사례** 하는 것 **하지** 테스트 및 개발에서 프로덕션 비밀을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-213">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="f0da5-214">Azure에 앱을 게시 하는 경우 Azure 웹 앱 포털에서 응용 프로그램 설정으로 SendGrid 비밀을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-214">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="f0da5-215">구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-215">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="f0da5-216">로컬 및 소셜 로그인 계정을 병합합니다</span><span class="sxs-lookup"><span data-stu-id="f0da5-216">Combine social and local login accounts</span></span>

<span data-ttu-id="f0da5-217">이 섹션을 완료 하려면 먼저 외부 인증 공급자를 활성화 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-217">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="f0da5-218">참조 [Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-218">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="f0da5-219">로컬 및 소셜 계정 전자 메일 링크를 클릭 하 여 결합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-219">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="f0da5-220">그러나 다음 순서로 "RickAndMSFT@gmail.com" 로컬 로그인;으로 처음 만들어질 수 계정으로 소셜 로그인을 먼저 만든 다음 로컬 로그인을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-220">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![웹 응용 프로그램: RickAndMSFT@gmail.com 인증 된 사용자](accconfirm/_static/rick.png)

<span data-ttu-id="f0da5-222">클릭 합니다 **관리** 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-222">Click on the **Manage** link.</span></span> <span data-ttu-id="f0da5-223">이 계정과 연결 된 참고 0 외부 (소셜 로그인)</span><span class="sxs-lookup"><span data-stu-id="f0da5-223">Note the 0 external (social logins) associated with this account.</span></span>

![보기 관리](accconfirm/_static/manage.png)

<span data-ttu-id="f0da5-225">다른 로그인 서비스에 대 한 링크를 클릭 하 고 앱 요청을 수락 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-225">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="f0da5-226">다음 이미지에서는 Facebook는 외부 인증 공급자:</span><span class="sxs-lookup"><span data-stu-id="f0da5-226">In the following image, Facebook is the external authentication provider:</span></span>

![Facebook을 목록 외부 로그인 보기를 관리 합니다.](accconfirm/_static/fb.png)

<span data-ttu-id="f0da5-228">두 개의 계정은 결합 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-228">The two accounts have been combined.</span></span> <span data-ttu-id="f0da5-229">계정이 나 로그인 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-229">You are able to sign in with either account.</span></span> <span data-ttu-id="f0da5-230">사용자가 자신의 소셜 로그인 인증 서비스가 다운 된 또는 가능성이 소셜 계정에 대 한 액세스를 손실 했습니다 되는 경우 로컬 계정을 추가 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-230">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="f0da5-231">사이트 사용자 후 계정 확인을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="f0da5-231">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="f0da5-232">사용자를 사용 하 여 사이트에서 계정 확인을 사용 하도록 설정 하면 모든 기존 사용자를 잠급니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-232">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="f0da5-233">해당 계정 확인 되지 때문에 기존 사용자가 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-233">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="f0da5-234">기존 사용자 잠금 문제를 해결 하려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-234">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="f0da5-235">로 확인 되 고 모든 기존 사용자를 표시 하도록 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-235">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="f0da5-236">기존 사용자를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-236">Confirm existing users.</span></span> <span data-ttu-id="f0da5-237">예를 들어 일괄 처리-송신 확인 링크를 사용 하 여 전자 메일입니다.</span><span class="sxs-lookup"><span data-stu-id="f0da5-237">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
