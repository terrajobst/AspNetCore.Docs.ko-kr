---
title: ASP.NET Core에서 Google 외부 로그인 설정
author: rick-anderson
description: 이 자습서에서는 기존 ASP.NET Core 앱에 Google 계정 사용자 인증의 통합을 보여 줍니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/19/2019
uid: security/authentication/google-logins
ms.openlocfilehash: e12d831d2e0a5c9acae5ea41fb4187ad4ca6b0ea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082480"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="4d268-103">ASP.NET Core에서 Google 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="4d268-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="4d268-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4d268-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4d268-105">[레거시 Google + api는 2019 년 3 월 7 일에 종료 되었습니다](https://developers.google.com/+/api-shutdown).</span><span class="sxs-lookup"><span data-stu-id="4d268-105">[Legacy Google+ APIs have been shut down as of March 7, 2019](https://developers.google.com/+/api-shutdown).</span></span> <span data-ttu-id="4d268-106">Google + 로그인 및 개발자는 새 Google 로그인 시스템으로 이동 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-106">Google+ sign in and developers must move to a new Google sign in system.</span></span> <span data-ttu-id="4d268-107">Google 인증에 대 한 ASP.NET Core 2.1 및 2.2 패키지가 변경 내용에 맞게 업데이트 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-107">The ASP.NET Core 2.1 and 2.2 packages for Google Authentication have be updated to accommodate the changes.</span></span> <span data-ttu-id="4d268-108">ASP.NET Core에 대 한 자세한 내용과 임시 완화 정보는 [이 GitHub 문제](https://github.com/aspnet/AspNetCore/issues/6486)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4d268-108">For more information and temporary mitigations for ASP.NET Core, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/6486).</span></span> <span data-ttu-id="4d268-109">이 자습서는 새 설치 프로세스를 사용 하 여 업데이트 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-109">This tutorial has been updated with the new setup process.</span></span>

<span data-ttu-id="4d268-110">이 자습서에서는 사용자가 [이전 페이지](xref:security/authentication/social/index)에서 만든 ASP.NET Core 2.2 프로젝트를 사용 하 여 Google 계정으로 로그인 할 수 있도록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-110">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="4d268-111">Google API 콘솔 프로젝트 및 클라이언트 ID 만들기</span><span class="sxs-lookup"><span data-stu-id="4d268-111">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="4d268-112">[Google 로그인을 웹 앱에 통합](https://developers.google.com/identity/sign-in/web/devconsole-project) 으로 이동 하 여 **프로젝트 구성**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-112">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="4d268-113">**OAuth 클라이언트 구성** 대화 상자에서 **웹 서버**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-113">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="4d268-114">**권한 있는 리디렉션 uri** 텍스트 항목 상자에서 리디렉션 uri를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-114">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="4d268-115">예를 들면 `https://localhost:5001/signin-google`</span><span class="sxs-lookup"><span data-stu-id="4d268-115">For example, `https://localhost:5001/signin-google`</span></span>
* <span data-ttu-id="4d268-116">**클라이언트 ID** 및 **클라이언트 암호**를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-116">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="4d268-117">사이트를 배포할 때 **Google 콘솔**에서 새 공용 url을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-117">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="4d268-118">저장소 Google ClientID 및 ClientSecret</span><span class="sxs-lookup"><span data-stu-id="4d268-118">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="4d268-119">Google `Client ID` 과 같은 중요 한 설정 및 `Client Secret` [비밀 관리자](xref:security/app-secrets)를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-119">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="4d268-120">이 자습서에서는 토큰 `Authentication:Google:ClientId` 의 이름을 및 `Authentication:Google:ClientSecret`으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-120">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="4d268-121">Api [콘솔](https://console.developers.google.com/apis/dashboard)에서 api 자격 증명과 사용 현황을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-121">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="4d268-122">Google 인증 구성</span><span class="sxs-lookup"><span data-stu-id="4d268-122">Configure Google authentication</span></span>

<span data-ttu-id="4d268-123">다음에 Google 서비스를 `Startup.ConfigureServices`추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-123">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupGoogle.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="4d268-124">Google로 로그인</span><span class="sxs-lookup"><span data-stu-id="4d268-124">Sign in with Google</span></span>

* <span data-ttu-id="4d268-125">앱을 실행 하 고 **로그인**을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-125">Run the app and click **Log in**.</span></span> <span data-ttu-id="4d268-126">Google을 사용 하 여 로그인 할 수 있는 옵션이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-126">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="4d268-127">Google 단추를 **클릭 합니다 .이 단추는** 인증을 위해 google으로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-127">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="4d268-128">Google 자격 증명을 입력 하면 웹 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-128">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="4d268-129">Google 인증 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> 에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4d268-129">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="4d268-130">이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-130">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="4d268-131">기본 콜백 URI 변경</span><span class="sxs-lookup"><span data-stu-id="4d268-131">Change the default callback URI</span></span>

<span data-ttu-id="4d268-132">URI 세그먼트 `/signin-google` Google 인증 공급자의 기본 콜백으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-132">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="4d268-133">상속을 통해 Google 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다 [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 의 속성을 [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-133">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="4d268-134">문제 해결</span><span class="sxs-lookup"><span data-stu-id="4d268-134">Troubleshooting</span></span>

* <span data-ttu-id="4d268-135">로그인이 작동 하지 않고 오류가 발생 하지 않는 경우 개발 모드로 전환 하 여 문제를 더 쉽게 디버깅할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-135">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="4d268-136">에서 `services.AddIdentity` *를 호출 하 여 id를 구성 하지 않은 경우 ArgumentException에서 결과를 인증 하려고 시도 합니다. `ConfigureServices` ' SignInScheme ' 옵션을 제공*해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-136">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="4d268-137">이 자습서에 사용 되는 프로젝트 템플릿이이 수행 되도록 보장 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-137">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="4d268-138">사이트 데이터베이스를 초기 마이그레이션을 적용 하 여 만들어지지 않은, 하는 경우 얻게 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다.* 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-138">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="4d268-139">**마이그레이션 적용** 을 선택 하 여 데이터베이스를 만들고 페이지를 새로 고쳐 오류를 계속 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-139">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4d268-140">다음 단계</span><span class="sxs-lookup"><span data-stu-id="4d268-140">Next steps</span></span>

* <span data-ttu-id="4d268-141">이 문서에서는 Google을 사용 하 여 인증 하는 보여 주었습니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-141">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="4d268-142">에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다 합니다 [이전 페이지](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-142">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="4d268-143">Azure에 앱을 게시 한 후에는 Google `ClientSecret` API 콘솔에서를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-143">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="4d268-144">설정 된 `Authentication:Google:ClientId` 및 `Authentication:Google:ClientSecret` Azure portal에서 응용 프로그램 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-144">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="4d268-145">구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d268-145">The configuration system is set up to read keys from environment variables.</span></span>
