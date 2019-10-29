---
title: ASP.NET Core의 Google 외부 로그인 설정
author: rick-anderson
description: 이 자습서에서는 Google 계정 사용자 인증을 기존 ASP.NET Core 앱에 통합 하는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/28/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 663029ecab99efd4f63f8deca026957c19c64710
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034322"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="8f667-103">ASP.NET Core의 Google 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="8f667-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="8f667-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8f667-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8f667-105">이 자습서에서는 사용자가 [이전 페이지](xref:security/authentication/social/index)에서 만든 ASP.NET Core 3.0 프로젝트를 사용 하 여 Google 계정으로 로그인 할 수 있도록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="8f667-106">Google API 콘솔 프로젝트 및 클라이언트 ID 만들기</span><span class="sxs-lookup"><span data-stu-id="8f667-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="8f667-107">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-107">Install [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span></span>
* <span data-ttu-id="8f667-108">[Google 로그인을 웹 앱에 통합](https://developers.google.com/identity/sign-in/web/devconsole-project) 으로 이동 하 여 **프로젝트 구성**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-108">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="8f667-109">**OAuth 클라이언트 구성** 대화 상자에서 **웹 서버**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-109">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="8f667-110">**권한 있는 리디렉션 uri** 텍스트 항목 상자에서 리디렉션 uri를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-110">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="8f667-111">예를 들면 `https://localhost:44312/signin-google`과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-111">For example, `https://localhost:44312/signin-google`</span></span>
* <span data-ttu-id="8f667-112">**클라이언트 ID** 및 **클라이언트 암호**를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-112">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="8f667-113">사이트를 배포할 때 **Google 콘솔**에서 새 공용 url을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-113">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="8f667-114">Google ClientID 및 ClientSecret 저장</span><span class="sxs-lookup"><span data-stu-id="8f667-114">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="8f667-115">Google `Client ID`와 같은 중요 한 설정 및 [비밀 관리자](xref:security/app-secrets)와 `Client Secret`를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-115">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="8f667-116">이 자습서에서는 토큰의 이름을 `Authentication:Google:ClientId` 하 고 `Authentication:Google:ClientSecret`합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-116">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "<client id>"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8f667-117">Api [콘솔](https://console.developers.google.com/apis/dashboard)에서 api 자격 증명과 사용 현황을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-117">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="8f667-118">Google 인증 구성</span><span class="sxs-lookup"><span data-stu-id="8f667-118">Configure Google authentication</span></span>

<span data-ttu-id="8f667-119">`Startup.ConfigureServices`에 Google 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-119">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="8f667-120">Google을 사용 하 여 로그인</span><span class="sxs-lookup"><span data-stu-id="8f667-120">Sign in with Google</span></span>

* <span data-ttu-id="8f667-121">앱을 실행 하 고 **로그인**을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-121">Run the app and click **Log in**.</span></span> <span data-ttu-id="8f667-122">Google을 사용 하 여 로그인 할 수 있는 옵션이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-122">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="8f667-123">Google 단추를 **클릭 합니다 .이 단추는** 인증을 위해 google으로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-123">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="8f667-124">Google 자격 증명을 입력 하면 웹 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-124">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="8f667-125">Google 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API 참조를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8f667-125">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="8f667-126">사용자에 대 한 다른 정보를 요청 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-126">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="8f667-127">기본 콜백 URI 변경</span><span class="sxs-lookup"><span data-stu-id="8f667-127">Change the default callback URI</span></span>

<span data-ttu-id="8f667-128">`/signin-google` URI 세그먼트는 Google 인증 공급자의 기본 콜백으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-128">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="8f667-129">[GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) 클래스의 상속 된 [Remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성을 통해 Google 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-129">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="8f667-130">문제 해결</span><span class="sxs-lookup"><span data-stu-id="8f667-130">Troubleshooting</span></span>

* <span data-ttu-id="8f667-131">로그인이 작동 하지 않고 오류가 발생 하지 않는 경우 개발 모드로 전환 하 여 문제를 더 쉽게 디버깅할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-131">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="8f667-132">`ConfigureServices`에서 `services.AddIdentity`를 호출 하 여 Id를 구성 하지 않은 경우 ArgumentException에서 결과를 인증 하려고 하면 *' SignInScheme ' 옵션을 제공 해야*합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-132">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="8f667-133">이 자습서에서 사용 되는 프로젝트 템플릿은이 작업이 수행 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-133">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="8f667-134">초기 마이그레이션을 적용 하 여 사이트 데이터베이스를 만들지 않은 경우 *요청 오류를 처리 하는 동안 데이터베이스 작업이 실패 했습니다* .</span><span class="sxs-lookup"><span data-stu-id="8f667-134">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="8f667-135">**마이그레이션 적용** 을 선택 하 여 데이터베이스를 만들고 페이지를 새로 고쳐 오류를 계속 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-135">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8f667-136">다음 단계</span><span class="sxs-lookup"><span data-stu-id="8f667-136">Next steps</span></span>

* <span data-ttu-id="8f667-137">이 문서에서는 Google을 사용 하 여 인증할 수 있는 방법을 살펴보았습니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-137">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="8f667-138">[위의 페이지](xref:security/authentication/social/index)에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="8f667-139">Azure에 앱을 게시 한 후 Google API 콘솔에서 `ClientSecret`를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-139">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="8f667-140">Azure Portal에서 `Authentication:Google:ClientId` 및 `Authentication:Google:ClientSecret`를 응용 프로그램 설정으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-140">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="8f667-141">구성 시스템은 환경 변수에서 키를 읽도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f667-141">The configuration system is set up to read keys from environment variables.</span></span>
