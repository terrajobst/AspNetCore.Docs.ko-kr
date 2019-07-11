---
title: ASP.NET Core를 사용 하 여 Microsoft 계정 외부 로그인 설정
author: rick-anderson
description: 이 샘플에서는 기존 ASP.NET Core 앱에 Microsoft 계정 사용자 인증의 통합을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 2c690e5bd8465806d42091616917cfdd747ef8f0
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815568"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="158e2-103">ASP.NET Core를 사용 하 여 Microsoft 계정 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="158e2-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="158e2-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="158e2-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="158e2-105">이 샘플에서 만든 ASP.NET Core 2.2 프로젝트를 사용 하 여 Microsoft 계정으로 로그인 할 수 있도록 하는 방법을 보여 줍니다.는 [이전 페이지](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="158e2-106">Microsoft 개발자 포털에서 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="158e2-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="158e2-107">로 이동 합니다 [Azure portal-앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지를 만들거나 Microsoft 계정으로 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-107">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="158e2-108">Microsoft 계정이 없다면 선택 **만드십시오**합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-108">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="158e2-109">로그인 한 후 리디렉션됩니다 합니다 **앱 등록** 페이지:</span><span class="sxs-lookup"><span data-stu-id="158e2-109">After signing in you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="158e2-110">선택 **새 등록**</span><span class="sxs-lookup"><span data-stu-id="158e2-110">Select **New registration**</span></span>
* <span data-ttu-id="158e2-111">입력 한 **이름을**입니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-111">Enter a **Name**.</span></span>
* <span data-ttu-id="158e2-112">에 대 한 옵션을 선택 **지원 되는 계정 유형**합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-112">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="158e2-113">아래 **리디렉션 URI**를 사용 하 여 개발 URL 입력 `/signin-microsoft` 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-113">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="158e2-114">`https://localhost:44389/signin-microsoft` )을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-114">For example, `https://localhost:44389/signin-microsoft`.</span></span> <span data-ttu-id="158e2-115">이 샘플의 뒷부분에 나오는 구성 Microsoft 인증 체계에서 요청을 자동으로 처리 됩니다 `/signin-microsoft` OAuth 흐름을 구현 하는 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-115">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="158e2-116">선택 **등록**</span><span class="sxs-lookup"><span data-stu-id="158e2-116">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="158e2-117">클라이언트 비밀 만들기</span><span class="sxs-lookup"><span data-stu-id="158e2-117">Create client secret</span></span>

* <span data-ttu-id="158e2-118">왼쪽된 창에서 선택 **인증서 및 비밀**합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-118">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="158e2-119">아래 **클라이언트 비밀**, 선택 **새 클라이언트 암호**</span><span class="sxs-lookup"><span data-stu-id="158e2-119">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="158e2-120">클라이언트 암호에 대 한 설명을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-120">Add a description for the client secret.</span></span>
  * <span data-ttu-id="158e2-121">선택 된 **추가** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-121">Select the **Add** button.</span></span>

* <span data-ttu-id="158e2-122">아래 **클라이언트 비밀**, 클라이언트 암호의 값을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-122">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="158e2-123">URI 세그먼트 `/signin-microsoft` Microsoft 인증 공급자의 기본 콜백으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-123">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="158e2-124">상속을 통해 Microsoft 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다 [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 의 속성을 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-124">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-client-secret"></a><span data-ttu-id="158e2-125">Microsoft 클라이언트 ID 및 클라이언트 암호를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-125">Store the Microsoft client ID and client secret</span></span>

<span data-ttu-id="158e2-126">안전 하 게 저장 하려면 다음 명령을 실행 `ClientId` 하 고 `ClientSecret` 사용 하 여 [암호 관리자](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="158e2-126">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```console
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

<span data-ttu-id="158e2-127">Microsoft와 같은 중요 한 설정이 연결 `ClientId` 하 고 `ClientSecret` 사용 하 여 응용 프로그램 구성에는 [암호 관리자](xref:security/app-secrets)합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-127">Link sensitive settings like Microsoft `ClientId` and `ClientSecret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="158e2-128">이 샘플에서는 토큰 이름을 `Authentication:Microsoft:ClientId` 고 `Authentication:Microsoft:ClientSecret`입니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-128">For the purposes of this sample, name the tokens `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="158e2-129">Microsoft 계정 인증 구성</span><span class="sxs-lookup"><span data-stu-id="158e2-129">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="158e2-130">Microsoft 계정 서비스를 추가할 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="158e2-130">Add the Microsoft Account service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="158e2-131">참조 된 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) Microsoft 계정 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조.</span><span class="sxs-lookup"><span data-stu-id="158e2-131">See the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="158e2-132">이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-132">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="158e2-133">Microsoft 계정으로 로그인</span><span class="sxs-lookup"><span data-stu-id="158e2-133">Sign in with Microsoft Account</span></span>

<span data-ttu-id="158e2-134">실행을 클릭 하 고 **로그인**합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-134">Run the and click **Log in**.</span></span> <span data-ttu-id="158e2-135">Microsoft에 로그인 하는 옵션이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-135">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="158e2-136">Microsoft를 클릭 하면 리디렉션됩니다 Microsoft 인증에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-136">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="158e2-137">(아직 로그인 하지 않은) 하는 경우 Microsoft 계정으로 로그인 한 후 앱 정보에 액세스할 수 있도록 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-137">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="158e2-138">탭 **예** 및 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-138">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="158e2-139">이제 Microsoft 자격 증명을 사용 하 여 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-139">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="158e2-140">문제 해결</span><span class="sxs-lookup"><span data-stu-id="158e2-140">Troubleshooting</span></span>

* <span data-ttu-id="158e2-141">Microsoft 계정 공급자는 로그인 오류 페이지로 리디렉션됩니다, 하는 경우 오류 제목과 설명을 쿼리 문자열 매개 변수 바로 다음을 유의 합니다 `#` (해시 태그) Uri에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-141">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="158e2-142">가장 일반적인 원인은 응용 프로그램의 모든 일치 하지 않는 Uri 오류 메시지는 Microsoft 인증에 문제가 있음을 나타낼 것 처럼 보이지만, 합니다 **리디렉션 Uri** 에 대해 지정 된 된 **웹** 플랫폼 .</span><span class="sxs-lookup"><span data-stu-id="158e2-142">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="158e2-143">호출 하 여 구성 되어 있지 않으면 Identity `services.AddIdentity` 에 `ConfigureServices`를 인증 하려고 하면 *ArgumentException: 'SignInScheme' 옵션을 제공 해야*합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-143">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="158e2-144">이 샘플에 사용 되는 프로젝트 템플릿이이 수행 되도록 보장 합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-144">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="158e2-145">사이트 데이터베이스를 초기 마이그레이션을 적용 하 여 만들어지지 않은, 경우 받습니다 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다.* 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-145">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="158e2-146">탭 **마이그레이션 적용** 데이터베이스를 만들고 오류 지 나 새로 고침 합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-146">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="158e2-147">다음 단계</span><span class="sxs-lookup"><span data-stu-id="158e2-147">Next steps</span></span>

* <span data-ttu-id="158e2-148">이 문서에서는 Microsoft를 사용 하 여 인증 하는 보여 주었습니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-148">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="158e2-149">에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다 합니다 [이전 페이지](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-149">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="158e2-150">Azure 웹 앱에 웹 사이트를 게시 하면 새 클라이언트 만들기 암호는 Microsoft 개발자 포털에서.</span><span class="sxs-lookup"><span data-stu-id="158e2-150">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="158e2-151">설정 된 `Authentication:Microsoft:ClientId` 및 `Authentication:Microsoft:ClientSecret` Azure portal에서 응용 프로그램 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-151">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="158e2-152">구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="158e2-152">The configuration system is set up to read keys from environment variables.</span></span>