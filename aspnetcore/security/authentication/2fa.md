---
title: ASP.NET Core에서 SMS 사용한 2 단계 인증
author: rick-anderson
description: ASP.NET Core 앱을 사용 하 여 2 단계 인증 (2FA)을 설정 하는 방법을 알아봅니다.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
uid: security/authentication/2fa
ms.openlocfilehash: 9398cd3bb81eab7b427b19bbd5497630f4dc0838
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165198"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="c2f0b-103">ASP.NET Core에서 SMS 사용한 2 단계 인증</span><span class="sxs-lookup"><span data-stu-id="c2f0b-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="c2f0b-104">하 여 [Rick Anderson](https://twitter.com/RickAndMSFT) 고 [스위스 개발자](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="c2f0b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="c2f0b-105">두 단계 인증 (2FA) authenticator 앱의 경우는 시간 기반 일회용 암호 알고리즘 (TOTP)를 사용 하 여 권장 접근법 2FA 위한 업계 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="c2f0b-106">2FA TOTP를 사용 하 여는 SMS 2FA 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="c2f0b-107">자세한 내용은 [ASP.NET Core에서 TOTP authenticator 앱에 대 한 QR 코드를 사용 하도록 설정 생성](xref:security/authentication/identity-enable-qrcodes) ASP.NET Core 2.0 이상.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="c2f0b-108">이 자습서에는 SMS를 사용 하 여 2 단계 인증 (2FA)을 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="c2f0b-109">에 대 한 지침이 제공 됩니다 [twilio](https://www.twilio.com/) 하 고 [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), 하지만 다른 SMS 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="c2f0b-110">완료 하는 것이 좋습니다 [계정 확인 및 암호 복구](xref:security/authentication/accconfirm) 이 자습서를 시작 하기 전에 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="c2f0b-111">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA)</span><span class="sxs-lookup"><span data-stu-id="c2f0b-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="c2f0b-112">[다운로드 하는 방법을](xref:index#how-to-download-a-sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="c2f0b-113">새 ASP.NET Core 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="c2f0b-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="c2f0b-114">라는 새 ASP.NET Core 웹 앱 만들기 `Web2FA` 개별 사용자 계정을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="c2f0b-115">지침에 따라 <xref:security/enforcing-ssl> 를 설정 하 고 HTTPS가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="c2f0b-116">SMS 계정 만들기</span><span class="sxs-lookup"><span data-stu-id="c2f0b-116">Create an SMS account</span></span>

<span data-ttu-id="c2f0b-117">예를 들어 SMS 계정을 만들 [twilio](https://www.twilio.com/) 하거나 [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="c2f0b-118">인증 자격 증명을 기록 (twilio: accountSid 및 authToken, ASPSMS에 대 한 합니다. 사용자 키 및 암호)입니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="c2f0b-119">SMS 공급자 자격 증명 확인</span><span class="sxs-lookup"><span data-stu-id="c2f0b-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="c2f0b-120">**Twilio:**</span><span class="sxs-lookup"><span data-stu-id="c2f0b-120">**Twilio:**</span></span>

<span data-ttu-id="c2f0b-121">Twilio 계정의 대시보드 탭에서 복사 합니다 **계정 SID** 하 고 **인증 토큰**합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="c2f0b-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="c2f0b-122">**ASPSMS:**</span></span>

<span data-ttu-id="c2f0b-123">계정 설정을에서 이동 **Userkey** 와 함께 복사 및 사용자 **암호**합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="c2f0b-124">키 암호 관리자 도구를 사용 하 여 이러한 값을 나중에 저장 됩니다 것 `SMSAccountIdentification` 고 `SMSAccountPassword`입니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="c2f0b-125">SenderID 지정 / 송신자</span><span class="sxs-lookup"><span data-stu-id="c2f0b-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="c2f0b-126">**Twilio:** 숫자 탭에서 복사 하면 Twilio **전화번호**합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="c2f0b-127">**ASPSMS:** 보낸 사람 잠금 해제 메뉴 내에서 하나 이상의 보낸 사람을 잠금 해제 하거나 (모든 네트워크에서 지원 되지 않음)는 영숫자 보낸 사람을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="c2f0b-128">저장 하겠습니다. 나중에 키에 암호 관리자 도구를 사용 하 여이 값 `SMSAccountFrom`합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="c2f0b-129">SMS 서비스에 대 한 자격 증명을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="c2f0b-130">사용 된 [옵션 패턴](xref:fundamentals/configuration/options) 사용자 계정 및 키 설정에 액세스 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="c2f0b-131">가져올 보안 SMS 키 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="c2f0b-132">이 샘플에서는 합니다 `SMSoptions` 클래스에 생성 됩니다 합니다 *Services/SMSoptions.cs* 파일.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="c2f0b-133">설정 합니다 `SMSAccountIdentification`, `SMSAccountPassword` 하 고 `SMSAccountFrom` 사용 하 여를 [암호 관리자 도구](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="c2f0b-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="c2f0b-134">예를 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="c2f0b-135">SMS 공급자에 대 한 NuGet 패키지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="c2f0b-136">관리자 콘솔 (PMC (패키지)를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="c2f0b-137">**Twilio:**</span><span class="sxs-lookup"><span data-stu-id="c2f0b-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="c2f0b-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="c2f0b-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="c2f0b-139">코드를 추가 합니다 *Services/MessageServices.cs* SMS를 사용 하도록 설정 하려면 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="c2f0b-140">Twilio 또는 ASPSMS 섹션 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="c2f0b-141">**Twilio:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]</span><span class="sxs-lookup"><span data-stu-id="c2f0b-141">**Twilio:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]</span></span>

<span data-ttu-id="c2f0b-142">**ASPSMS:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]</span><span class="sxs-lookup"><span data-stu-id="c2f0b-142">**ASPSMS:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]</span></span>

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="c2f0b-143">사용 하는 시작 구성 `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="c2f0b-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="c2f0b-144">추가 `SMSoptions` 의 서비스 컨테이너에 `ConfigureServices` 에서 메서드는 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="c2f0b-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="c2f0b-145">2 단계 인증을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="c2f0b-145">Enable two-factor authentication</span></span>

<span data-ttu-id="c2f0b-146">엽니다는 *Views/Manage/Index.cshtml* Razor 뷰 파일과 주석 문자 (따라서 없는 태그는 주석) 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="c2f0b-147">2 단계 인증으로 로그인</span><span class="sxs-lookup"><span data-stu-id="c2f0b-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="c2f0b-148">앱을 실행 하 고 새 사용자 등록</span><span class="sxs-lookup"><span data-stu-id="c2f0b-148">Run the app and register a new user</span></span>

![Microsoft Edge에서 열린 웹 응용 프로그램 등록 보기](2fa/_static/login2fa1.png)

* <span data-ttu-id="c2f0b-150">활성화 하는 사용자 이름을 탭 합니다 `Index` 관리 컨트롤러의 동작 메서드.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="c2f0b-151">전화 번호를 누릅니다 **추가** 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-151">Then tap the phone number **Add** link.</span></span>

![보기 관리-"추가" 링크를 탭 합니다.](2fa/_static/login2fa2.png)

* <span data-ttu-id="c2f0b-153">확인 코드를 받고 탭는 전화 번호를 추가할 **확인 코드 보내기**합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![전화 번호 페이지 추가](2fa/_static/login2fa3.png)

* <span data-ttu-id="c2f0b-155">확인 코드로 문자 메시지를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="c2f0b-156">입력 하 고 탭 **제출**</span><span class="sxs-lookup"><span data-stu-id="c2f0b-156">Enter it and tap **Submit**</span></span>

![전화 번호 페이지 확인](2fa/_static/login2fa4.png)

<span data-ttu-id="c2f0b-158">텍스트 메시지를 얻지 못한 경우 twilio 로그 페이지를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="c2f0b-159">관리 보기를 보여 줍니다 전화 번호를 추가 했습니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-159">The Manage view shows your phone number was added successfully.</span></span>

![관리 보기-전화 번호가 추가 했습니다.](2fa/_static/login2fa5.png)

* <span data-ttu-id="c2f0b-161">탭 **사용** 2 단계 인증을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-161">Tap **Enable** to enable two-factor authentication.</span></span>

![보기 관리-2 단계 인증을 사용 하도록 설정](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="c2f0b-163">2 단계 인증 테스트</span><span class="sxs-lookup"><span data-stu-id="c2f0b-163">Test two-factor authentication</span></span>

* <span data-ttu-id="c2f0b-164">로그 오프 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-164">Log off.</span></span>

* <span data-ttu-id="c2f0b-165">로그인</span><span class="sxs-lookup"><span data-stu-id="c2f0b-165">Log in.</span></span>

* <span data-ttu-id="c2f0b-166">사용자 계정 인증의 두번째 단계를 제공 해야 하므로 이중 인증이 있었습니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="c2f0b-167">이 자습서에서는 전화 확인 하도록 설정한 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="c2f0b-168">기본 제공된 템플릿의 허용 하는 두 번째 단계로 전자 메일을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="c2f0b-169">QR 코드와 같은 인증에 대 한 추가 두 번째 요소를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="c2f0b-170">탭 **제출**합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-170">Tap **Submit**.</span></span>

![뷰 확인 코드 보내기](2fa/_static/login2fa7.png)

* <span data-ttu-id="c2f0b-172">SMS 메시지에는 코드를 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="c2f0b-173">클릭 하 여 **이 브라우저 기억** 확인란 있습니다 2FA를 사용 하 여 동일한 장치 및 브라우저를 사용 하는 경우 로그온 할 필요가에서 제외 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="c2f0b-174">2FA를 사용 하도록 설정 하 고 클릭 **이 브라우저 기억** 하면 강력한 2FA 보호를 사용 하 여 악의적인 사용자와 장치에 액세스할 수 없는 계정에 액세스 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="c2f0b-175">정기적으로 사용 하는 모든 개인 장치에서이 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="c2f0b-176">설정 하 여 **이 브라우저 기억**, 정기적으로 사용 하지 않는 장치에서 추가 보안 2FA 얻게 및 고유한 장치의 2FA를 통해 이동 하지 않아도에서 편리 하 게 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![보기를 확인 합니다.](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="c2f0b-178">무차별 대입 공격 으로부터 보호 하기 위한 계정 잠금</span><span class="sxs-lookup"><span data-stu-id="c2f0b-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="c2f0b-179">2FA를 사용 하 여 계정 잠금 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="c2f0b-180">사용자가 로컬 계정 또는 소셜 계정을 통해 로그인 되 면 각 실패 한 시도 2FA에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="c2f0b-181">사용자가 잠겨 실패 한 최대 액세스 시도 횟수에 도달 하는 경우 (기본값: 5 분 잠금 5에 대 한 액세스 시도가 실패 한 후).</span><span class="sxs-lookup"><span data-stu-id="c2f0b-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="c2f0b-182">실패 한 액세스 시도 횟수를 다시 설정 하 고 시계를 다시 설정 하는 성공적으로 인증 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="c2f0b-183">최대 액세스 시도 실패 하 고 사용 하 여 잠금 시간을 설정할 수 있습니다 [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) 하 고 [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="c2f0b-184">다음 10 분 동안 10에 대 한 액세스 시도가 실패 한 후 계정 잠금을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2f0b-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="c2f0b-185">확인 [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) 설정 `lockoutOnFailure` 하려면 `true`:</span><span class="sxs-lookup"><span data-stu-id="c2f0b-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
