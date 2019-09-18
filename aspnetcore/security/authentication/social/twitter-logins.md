---
title: ASP.NET Core로 Twitter 외부 로그인 설정
author: rick-anderson
description: 이 자습서에서는 Twitter 계정 사용자 인증을 기존 ASP.NET Core 앱에 통합 하는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: 5182f1647acb664bf35f086fcddbe909559a62f7
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082298"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="417d8-103">ASP.NET Core로 Twitter 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="417d8-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="417d8-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="417d8-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="417d8-105">이 샘플에서는 [이전 페이지](xref:security/authentication/social/index)에서 만든 샘플 ASP.NET Core 2.2 프로젝트를 사용 하 여 사용자가 [Twitter 계정으로 로그인](https://dev.twitter.com/web/sign-in/desktop-browser) 할 수 있게 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="417d8-106">Twitter에서 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="417d8-106">Create the app in Twitter</span></span>

* <span data-ttu-id="417d8-107">이동할 [ https://apps.twitter.com/ ](https://apps.twitter.com/) 에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-107">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="417d8-108">Twitter 계정이 아직 없는 경우 **[지금 등록](https://twitter.com/signup)** 링크를 사용 하 여 계정을 만드세요.</span><span class="sxs-lookup"><span data-stu-id="417d8-108">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="417d8-109">**새 앱 만들기** 를 탭 하 고 응용 프로그램 **이름**, **설명** 및 공용 **웹 사이트** URI를 입력 합니다 (도메인 이름을 등록할 때까지 임시 일 수 있음).</span><span class="sxs-lookup"><span data-stu-id="417d8-109">Tap **Create New App** and fill out the application **Name**, **Description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="417d8-110">**유효한 OAuth 리디렉션 uri** 필드 `/signin-twitter` 에 추가 된를 사용 하 여 개발 URI를 입력 합니다 `https://webapp128.azurewebsites.net/signin-twitter`(예:).</span><span class="sxs-lookup"><span data-stu-id="417d8-110">Enter your development URI with `/signin-twitter` appended into the **Valid OAuth Redirect URIs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="417d8-111">이 샘플의 뒷부분에서 구성 된 Twitter 인증 체계는 OAuth 흐름을 `/signin-twitter` 구현 하는 경로에서 요청을 자동으로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-111">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="417d8-112">URI 세그먼트 `/signin-twitter` 는 Twitter 인증 공급자의 기본 콜백으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-112">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="417d8-113">[Twitteroptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) 클래스의 상속 된 [Remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성을 통해 Twitter 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-113">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="417d8-114">양식의 나머지 부분을 작성 하 고 **Twitter 응용 프로그램 만들기**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-114">Fill out the rest of the form and tap **Create your Twitter application**.</span></span> <span data-ttu-id="417d8-115">새 응용 프로그램 세부 정보가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-115">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="417d8-116">Twitter 소비자 API 키 및 암호 저장</span><span class="sxs-lookup"><span data-stu-id="417d8-116">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="417d8-117">다음 명령을 실행 하 여 [비밀 Manager](xref:security/app-secrets)를 `ClientSecret` 안전 하 게 저장 `ClientId` 하 고 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-117">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

<span data-ttu-id="417d8-118">[암호 관리자](xref:security/app-secrets)를 사용 하 `Consumer Key` 여 `Consumer Secret` Twitter 및와 같은 중요 한 설정을 응용 프로그램 구성에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-118">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="417d8-119">이 샘플의 목적을 위해 토큰 `Authentication:Twitter:ConsumerKey` 의 이름을 및 `Authentication:Twitter:ConsumerSecret`으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-119">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="417d8-120">이러한 토큰은 새 Twitter 응용 프로그램을 만든 후에 **키 및 액세스 토큰** 탭에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-120">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="417d8-121">Twitter 인증 구성</span><span class="sxs-lookup"><span data-stu-id="417d8-121">Configure Twitter Authentication</span></span>

<span data-ttu-id="417d8-122">`ConfigureServices` *Startup.cs* 파일의 메서드에 Twitter 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-122">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="417d8-123">Twitter 인증에서 지원 되는 구성 옵션에 대 한 자세한 내용은 [Twitteroptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API 참조를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="417d8-123">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="417d8-124">이 사용 하 여 사용자에 대 한 다른 정보를 요청할 수 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-124">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="417d8-125">Twitter로 로그인</span><span class="sxs-lookup"><span data-stu-id="417d8-125">Sign in with Twitter</span></span>

<span data-ttu-id="417d8-126">앱을 실행 하 고 **로그인**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-126">Run the app and select **Log in**.</span></span> <span data-ttu-id="417d8-127">Twitter를 사용 하 여 로그인 하는 옵션이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-127">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="417d8-128">**Twitter** 로 리디렉션하여 인증을 위해 twitter를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-128">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="417d8-129">Twitter 자격 증명을 입력 하면 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-129">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="417d8-130">이제 Twitter 자격 증명을 사용 하 여 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-130">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="417d8-131">문제 해결</span><span class="sxs-lookup"><span data-stu-id="417d8-131">Troubleshooting</span></span>

* <span data-ttu-id="417d8-132">**ASP.NET Core 2.x에만 해당:** 에서 `services.AddIdentity` *를 호출 하 여 id가 구성 되지 않은 경우 인증을 시도 하면 ArgumentException이 발생 합니다. `ConfigureServices` ' SignInScheme ' 옵션을 제공*해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-132">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="417d8-133">이 샘플에서 사용 되는 프로젝트 템플릿은이 작업이 수행 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-133">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="417d8-134">사이트 데이터베이스를 초기 마이그레이션을 적용 하 여 만들어지지 않은, 경우 받습니다 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다.* 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-134">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="417d8-135">탭 **마이그레이션 적용** 데이터베이스를 만들고 오류 지 나 새로 고침 합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-135">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="417d8-136">다음 단계</span><span class="sxs-lookup"><span data-stu-id="417d8-136">Next steps</span></span>

* <span data-ttu-id="417d8-137">이 문서에서는 Twitter를 사용 하 여 인증할 수 있는 방법을 살펴보았습니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-137">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="417d8-138">에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다 합니다 [이전 페이지](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="417d8-139">Azure 웹 앱에 웹 사이트를 게시 한 후에는 Twitter 개발자 포털 `ConsumerSecret` 에서를 다시 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-139">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="417d8-140">설정 된 `Authentication:Twitter:ConsumerKey` 및 `Authentication:Twitter:ConsumerSecret` Azure portal에서 응용 프로그램 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-140">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="417d8-141">구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="417d8-141">The configuration system is set up to read keys from environment variables.</span></span>
