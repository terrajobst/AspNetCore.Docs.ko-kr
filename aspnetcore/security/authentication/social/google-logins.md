---
title: ASP.NET Core에서 Google 외부 로그인 설정
author: rick-anderson
description: 이 자습서에서는 기존 ASP.NET Core 앱에 Google 계정 사용자 인증의 통합을 보여 줍니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 1/11/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 44c79b3279db7946b6d89a726bd3f5acfb5f51af
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705593"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="c2e6f-103">ASP.NET Core에서 Google 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="c2e6f-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="c2e6f-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c2e6f-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c2e6f-105">[레거시 Google + Api 2019 년 3 월 7 일부 터 종료](https://developers.google.com/+/api-shutdown)합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-105">[Legacy Google+ APIs have been shut down as of March 7, 2019](https://developers.google.com/+/api-shutdown).</span></span> <span data-ttu-id="c2e6f-106">Google +에 로그인 하 고 개발자가 시스템에서 새 Google 로그인을 이동 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-106">Google+ sign in and developers must move to a new Google sign in system.</span></span> <span data-ttu-id="c2e6f-107">Google 인증에 대 한 ASP.NET Core 2.1 및 2.2 패키지 변경 내용에 맞게 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-107">The ASP.NET Core 2.1 and 2.2 packages for Google Authentication have be updated to accommodate the changes.</span></span> <span data-ttu-id="c2e6f-108">자세한 정보 및 ASP.NET Core에 대 한 임시 완화 [이 GitHub 문제](https://github.com/aspnet/AspNetCore/issues/6486)합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-108">For more information and temporary mitigations for ASP.NET Core, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/6486).</span></span> <span data-ttu-id="c2e6f-109">이 자습서는 새로운 설치 프로세스를 사용 하 여 업데이트 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-109">This tutorial has been updated with the new setup process.</span></span>

<span data-ttu-id="c2e6f-110">이 자습서에서 만든 ASP.NET Core 2.2 프로젝트를 사용 하 여 Google 계정으로 로그인 할 수 있도록 하는 방법을 보여 줍니다.는 [이전 페이지](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-110">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="c2e6f-111">Google API 콘솔 프로젝트와 클라이언트 ID 만들기</span><span class="sxs-lookup"><span data-stu-id="c2e6f-111">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="c2e6f-112">이동할 [통합 Google 로그인에 웹 앱](https://developers.google.com/identity/sign-in/web/devconsole-project) 선택한 **는 프로젝트 구성**합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-112">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="c2e6f-113">에 **OAuth 클라이언트 구성** 대화 상자에서 **웹 서버**합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-113">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="c2e6f-114">에 **권한이 부여 된 리디렉션 Uri** 텍스트 입력 상자 리디렉션 URI를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-114">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="c2e6f-115">예를 들면 `https://localhost:5001/signin-google`과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-115">For example, `https://localhost:5001/signin-google`</span></span>
* <span data-ttu-id="c2e6f-116">저장 된 **클라이언트 ID** 하 고 **클라이언트 비밀**합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-116">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="c2e6f-117">사이트를 배포할 때 새 공용 url을 등록 합니다 **Google 콘솔**합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-117">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="c2e6f-118">저장소 Google ClientID 및 ClientSecret</span><span class="sxs-lookup"><span data-stu-id="c2e6f-118">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="c2e6f-119">Google 같은 중요 한 설정을 저장 `Client ID` 하 고 `Client Secret` 사용 하 여 합니다 [암호 관리자](xref:security/app-secrets)합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-119">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="c2e6f-120">이 자습서에서는 이름을 토큰 `Authentication:Google:ClientId` 고 `Authentication:Google:ClientSecret`:</span><span class="sxs-lookup"><span data-stu-id="c2e6f-120">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```console
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="c2e6f-121">API 자격 증명 및에서 사용량을 관리할 수 있습니다 합니다 [API 콘솔](https://console.developers.google.com/apis/dashboard)합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-121">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="c2e6f-122">Google 인증 구성</span><span class="sxs-lookup"><span data-stu-id="c2e6f-122">Configure Google authentication</span></span>

<span data-ttu-id="c2e6f-123">Google 서비스를 추가할 `Startup.ConfigureServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-123">Add the Google service to `Startup.ConfigureServices`.</span></span>

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="c2e6f-124">Google로 로그인</span><span class="sxs-lookup"><span data-stu-id="c2e6f-124">Sign in with Google</span></span>

* <span data-ttu-id="c2e6f-125">앱을 실행 하 고 클릭 **로그인**합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-125">Run the app and click **Log in**.</span></span> <span data-ttu-id="c2e6f-126">Google로 로그인 하는 옵션이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-126">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="c2e6f-127">클릭 합니다 **Google** 단추를 인증에 대 한 Google에 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-127">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="c2e6f-128">Google 자격 증명을 입력 한 후 웹 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-128">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="c2e6f-129">참조 된 [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) Google 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-129">See the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="c2e6f-130">이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-130">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="c2e6f-131">기본 콜백 URI를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-131">Change the default callback URI</span></span>

<span data-ttu-id="c2e6f-132">URI 세그먼트 `/signin-google` Google 인증 공급자의 기본 콜백으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-132">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="c2e6f-133">상속을 통해 Google 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다 [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 의 속성을 [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-133">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="c2e6f-134">문제 해결</span><span class="sxs-lookup"><span data-stu-id="c2e6f-134">Troubleshooting</span></span>

* <span data-ttu-id="c2e6f-135">로그인 작동 하지 않습니다 하 고 오류를 가져오지 않음, 문제를 더 쉽게 디버그 하려면 개발 모드를 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-135">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="c2e6f-136">호출 하 여 구성 되어 있지 않으면 Identity `services.AddIdentity` 에 `ConfigureServices`에서 결과 인증 하는 동안, *ArgumentException: 'SignInScheme' 옵션을 제공 해야*합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-136">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="c2e6f-137">이 자습서에 사용 되는 프로젝트 템플릿이이 수행 되도록 보장 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-137">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="c2e6f-138">사이트 데이터베이스를 초기 마이그레이션을 적용 하 여 만들어지지 않은, 하는 경우 얻게 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다.* 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-138">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="c2e6f-139">탭 **마이그레이션 적용** 데이터베이스를 만들고 오류 지 나 새로 고침 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-139">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c2e6f-140">다음 단계</span><span class="sxs-lookup"><span data-stu-id="c2e6f-140">Next steps</span></span>

* <span data-ttu-id="c2e6f-141">이 문서에서는 Google을 사용 하 여 인증 하는 보여 주었습니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-141">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="c2e6f-142">에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다 합니다 [이전 페이지](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-142">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="c2e6f-143">Azure에 앱을 게시 하면 다시 설정 된 `ClientSecret` Google API 콘솔에서.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-143">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="c2e6f-144">설정 된 `Authentication:Google:ClientId` 및 `Authentication:Google:ClientSecret` Azure portal에서 응용 프로그램 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-144">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="c2e6f-145">구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c2e6f-145">The configuration system is set up to read keys from environment variables.</span></span>
