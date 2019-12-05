---
title: ASP.NET Core를 사용 하 여 Microsoft 계정 외부 로그인 설정
author: rick-anderson
description: 이 샘플에서는 Microsoft 계정 사용자 인증을 기존 ASP.NET Core 앱에 통합 하는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/4/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: ddaae1a25a1dcf167ffae0f24b480e2cde6aca5b
ms.sourcegitcommit: f4cd3828e26e6d549ba8d0c36a17be35ad9e5a51
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74825459"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="f124c-103">ASP.NET Core를 사용 하 여 Microsoft 계정 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="f124c-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="f124c-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f124c-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f124c-105">이 샘플에서는 사용자가 [이전 페이지](xref:security/authentication/social/index)에서 만든 ASP.NET Core 3.0 프로젝트를 사용 하 여 Microsoft 계정에 로그인 할 수 있도록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="f124c-106">Microsoft 개발자 포털에서 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="f124c-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="f124c-107">프로젝트에 [AspNetCore. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet 패키지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="f124c-108">[Azure Portal-앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동 하 고 Microsoft 계정를 만들거나 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="f124c-109">Microsoft 계정 없는 경우 **만들기**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="f124c-110">로그인 하면 **앱 등록** 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="f124c-111">**새 등록** 선택</span><span class="sxs-lookup"><span data-stu-id="f124c-111">Select **New registration**</span></span>
* <span data-ttu-id="f124c-112">**이름**을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-112">Enter a **Name**.</span></span>
* <span data-ttu-id="f124c-113">**지원 되는 계정 유형에**대 한 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="f124c-114">**URI 리디렉션**에서 `/signin-microsoft` 추가 된 개발 URL을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="f124c-115">예를 들어 `https://localhost:5001/signin-microsoft`과 같은 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="f124c-116">이 샘플의 뒷부분에서 구성 된 Microsoft 인증 체계는 OAuth 흐름을 구현 하는 `/signin-microsoft` 경로에서 요청을 자동으로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="f124c-117">**등록** 선택</span><span class="sxs-lookup"><span data-stu-id="f124c-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="f124c-118">클라이언트 암호 만들기</span><span class="sxs-lookup"><span data-stu-id="f124c-118">Create client secret</span></span>

* <span data-ttu-id="f124c-119">왼쪽 창에서 **인증서 & 암호**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="f124c-120">**클라이언트 암호**에서 **새 클라이언트 암호** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="f124c-121">클라이언트 암호에 대 한 설명을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="f124c-122">**추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-122">Select the **Add** button.</span></span>

* <span data-ttu-id="f124c-123">**클라이언트 암호**에서 클라이언트 암호의 값을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-123">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="f124c-124">`/signin-microsoft` URI 세그먼트가 Microsoft 인증 공급자의 기본 콜백으로 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="f124c-125">[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) 클래스의 상속 된 [Remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성을 통해 Microsoft 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-client-secret"></a><span data-ttu-id="f124c-126">Microsoft 클라이언트 ID 및 클라이언트 암호 저장</span><span class="sxs-lookup"><span data-stu-id="f124c-126">Store the Microsoft client ID and client secret</span></span>

<span data-ttu-id="f124c-127">다음 명령을 실행 하 여 [비밀 Manager](xref:security/app-secrets)를 사용 하 `ClientId` 및 `ClientSecret`를 안전 하 게 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-127">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

<span data-ttu-id="f124c-128">[비밀 관리자](xref:security/app-secrets)를 사용 하 여 Microsoft `ClientId` 및 `ClientSecret`와 같은 중요 한 설정을 응용 프로그램 구성에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-128">Link sensitive settings like Microsoft `ClientId` and `ClientSecret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="f124c-129">이 샘플의 목적에 맞게 토큰의 이름을 `Authentication:Microsoft:ClientId` 하 고 `Authentication:Microsoft:ClientSecret`합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-129">For the purposes of this sample, name the tokens `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="f124c-130">Microsoft 계정 인증 구성</span><span class="sxs-lookup"><span data-stu-id="f124c-130">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="f124c-131">`Startup.ConfigureServices`에 Microsoft 계정 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-131">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="f124c-132">Microsoft 계정 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API 참조를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f124c-132">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="f124c-133">이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-133">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="f124c-134">Microsoft에 로그인 계정</span><span class="sxs-lookup"><span data-stu-id="f124c-134">Sign in with Microsoft Account</span></span>

<span data-ttu-id="f124c-135">앱을 실행 하 고 **로그인**을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-135">Run the app and click **Log in**.</span></span> <span data-ttu-id="f124c-136">Microsoft에 로그인 할 수 있는 옵션이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-136">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="f124c-137">Microsoft를 클릭 하면 인증을 위해 Microsoft로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-137">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="f124c-138">Microsoft 계정을 사용 하 여 로그인 한 후에 앱에서 사용자의 정보에 액세스 하도록 허용 하 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-138">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="f124c-139">**예** 를 탭 하면 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-139">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="f124c-140">이제 Microsoft 자격 증명을 사용 하 여 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-140">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="f124c-141">문제 해결</span><span class="sxs-lookup"><span data-stu-id="f124c-141">Troubleshooting</span></span>

* <span data-ttu-id="f124c-142">Microsoft 계정 공급자가 로그인 오류 페이지로 리디렉션되는 경우 Uri에서 `#` (해시 태그) 바로 다음에 나오는 오류 제목 및 설명 쿼리 문자열 매개 변수를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-142">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="f124c-143">오류 메시지가 Microsoft 인증 문제를 나타내는 것 처럼 보이지만 가장 일반적인 원인은 **웹** 플랫폼에 지정 된 **리디렉션 uri** 와 일치 하지 않는 응용 프로그램 uri입니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-143">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="f124c-144">`ConfigureServices`에서 `services.AddIdentity`를 호출 하 여 Id가 구성 되지 않은 경우 인증을 시도 하면 ArgumentException이 발생 합니다. *' SignInScheme ' 옵션을 제공 해야*합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-144">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="f124c-145">이 샘플에서 사용 되는 프로젝트 템플릿은이 작업이 수행 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-145">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="f124c-146">사이트 데이터베이스를 초기 마이그레이션을 적용 하 여 만들어지지 않은, 경우 받습니다 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다.* 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-146">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="f124c-147">탭 **마이그레이션 적용** 데이터베이스를 만들고 오류 지 나 새로 고침 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-147">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f124c-148">다음 단계</span><span class="sxs-lookup"><span data-stu-id="f124c-148">Next steps</span></span>

* <span data-ttu-id="f124c-149">이 문서에서는 Microsoft를 사용 하 여 인증할 수 있는 방법을 살펴보았습니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-149">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="f124c-150">에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다 합니다 [이전 페이지](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-150">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="f124c-151">Azure 웹 앱에 웹 사이트를 게시 한 후에는 Microsoft 개발자 포털에서 새 클라이언트 암호를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-151">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="f124c-152">설정 된 `Authentication:Microsoft:ClientId` 및 `Authentication:Microsoft:ClientSecret` Azure portal에서 응용 프로그램 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-152">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="f124c-153">구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f124c-153">The configuration system is set up to read keys from environment variables.</span></span>
