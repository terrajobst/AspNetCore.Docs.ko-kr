---
title: ASP.NET Core의 Facebook 외부 로그인 설정
author: rick-anderson
description: 기존 ASP.NET Core 앱에 Facebook 계정 사용자 인증을 통합 하는 방법을 보여 주는 코드 예제를 보여 주는 자습서입니다.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 12/02/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 2e4cc04c6e7ff8e5f5701cc7f9ede73dbc1b4685
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717126"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="d3963-103">ASP.NET Core의 Facebook 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="d3963-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="d3963-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d3963-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d3963-105">이 자습서의 코드 예제에서는 [이전 페이지](xref:security/authentication/social/index)에서 만든 샘플 ASP.NET Core 3.0 프로젝트를 사용 하 여 사용자가 Facebook 계정으로 로그인 할 수 있도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="d3963-106">먼저 [공식 단계](https://developers.facebook.com)를 수행 하 여 FACEBOOK 앱 ID를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="d3963-107">Facebook에서 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="d3963-107">Create the app in Facebook</span></span>

* <span data-ttu-id="d3963-108">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet 패키지를 프로젝트에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="d3963-109">[Facebook 개발자 앱](https://developers.facebook.com/apps/) 페이지로 이동 하 여 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="d3963-110">Facebook 계정이 아직 없는 경우 로그인 페이지에서 **facebook에 등록** 링크를 사용 하 여 하나를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="d3963-111">Facebook 계정이 있으면 지침에 따라 Facebook 개발자로 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="d3963-112">**내 앱** 메뉴에서 **앱 만들기** 를 선택 하 여 새 앱 ID를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Microsoft Edge에서 오픈 개발자 용 Facebook 포털](index/_static/FBMyApps.png)

* <span data-ttu-id="d3963-114">양식을 작성 하 고 **앱 ID 만들기** 단추를 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![새 앱 ID 양식 만들기](index/_static/FBNewAppId.png)

* <span data-ttu-id="d3963-116">새 앱 카드에서 **제품 추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="d3963-117">**Facebook 로그인** 카드에서 **설정** 을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![제품 설정 페이지](index/_static/FBProductSetup.png)

* <span data-ttu-id="d3963-119">첫 번째 페이지로 **플랫폼을 선택** 하 여 **빠른** 시작 마법사가 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="d3963-120">왼쪽 아래에 있는 메뉴에서 **FaceBook 로그인** **설정** 링크를 클릭 하 여 이제 마법사를 무시 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![빠른 시작 건너뛰기](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="d3963-122">**클라이언트 OAuth 설정** 페이지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![클라이언트 OAuth 설정 페이지](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="d3963-124">**유효한 OAuth 리디렉션 uri** 필드에 */signin-facebook* 를 추가 하 여 개발 URI를 입력 합니다 (예: `https://localhost:44320/signin-facebook`).</span><span class="sxs-lookup"><span data-stu-id="d3963-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="d3963-125">이 자습서의 뒷부분에서 구성 된 Facebook 인증은 */signin-facebook* 경로에서 요청을 자동으로 처리 하 여 OAuth 흐름을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="d3963-126">URI */signin-facebook* 는 facebook 인증 공급자의 기본 콜백으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="d3963-127">[FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) 클래스의 상속 된 [Remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성을 통해 Facebook 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="d3963-128">**변경 내용 저장**을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="d3963-129">왼쪽 탐색 영역에서 **설정** > **기본** 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="d3963-130">이 페이지에서 `App ID` 및 `App Secret`을 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="d3963-131">다음 섹션의 ASP.NET Core 응용 프로그램에 둘 다 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="d3963-132">사이트를 배포할 때 **Facebook 로그인** 설정 페이지를 다시 방문 하 여 새 공용 URI를 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-facebook-app-id-and-app-secret"></a><span data-ttu-id="d3963-133">Facebook 앱 ID 및 앱 암호 저장</span><span class="sxs-lookup"><span data-stu-id="d3963-133">Store Facebook App ID and App Secret</span></span>

<span data-ttu-id="d3963-134">[비밀 관리자](xref:security/app-secrets)를 사용 하 여 Facebook `App ID` 및 `App Secret`와 같은 중요 한 설정을 응용 프로그램 구성에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-134">Link sensitive settings like Facebook `App ID` and `App Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="d3963-135">이 자습서에서는 토큰의 이름을 `Authentication:Facebook:AppId` 하 고 `Authentication:Facebook:AppSecret`합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-135">For the purposes of this tutorial, name the tokens `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d3963-136">다음 명령을 실행 하 여 비밀 Manager를 사용 하 `App ID` 및 `App Secret`를 안전 하 게 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-136">Execute the following commands to securely store `App ID` and `App Secret` using Secret Manager:</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a><span data-ttu-id="d3963-137">Facebook 인증 구성</span><span class="sxs-lookup"><span data-stu-id="d3963-137">Configure Facebook Authentication</span></span>

<span data-ttu-id="d3963-138">*Startup.cs* 파일의 `ConfigureServices` 메서드에 Facebook 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-138">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="d3963-139">Facebook 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API 참조를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d3963-139">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="d3963-140">구성 옵션을 사용 하 여 다음 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-140">Configuration options can be used to:</span></span>

* <span data-ttu-id="d3963-141">사용자에 대 한 다른 정보를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-141">Request different information about the user.</span></span>
* <span data-ttu-id="d3963-142">쿼리 문자열 인수를 추가 하 여 로그인 환경을 사용자 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-142">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="d3963-143">Facebook으로 로그인</span><span class="sxs-lookup"><span data-stu-id="d3963-143">Sign in with Facebook</span></span>

<span data-ttu-id="d3963-144">응용 프로그램을 실행 하 고 **로그인**을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-144">Run your application and click **Log in**.</span></span> <span data-ttu-id="d3963-145">Facebook을 사용 하 여 로그인 하는 옵션이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-145">You see an option to sign in with Facebook.</span></span>

![웹 응용 프로그램: 사용자가 인증 되지 않음](index/_static/DoneFacebook.png)

<span data-ttu-id="d3963-147">**Facebook**을 클릭 하면 인증을 위해 facebook으로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-147">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Facebook 인증 페이지](index/_static/FBLogin.png)

<span data-ttu-id="d3963-149">Facebook 인증 요청 공개 프로필 및 전자 메일 주소 기본적으로:</span><span class="sxs-lookup"><span data-stu-id="d3963-149">Facebook authentication requests public profile and email address by default:</span></span>

![Facebook 인증 페이지 동의 화면](index/_static/FBLoginDone.png)

<span data-ttu-id="d3963-151">Facebook 자격 증명을 입력 하면 전자 메일을 설정할 수 있는 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-151">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="d3963-152">이제 Facebook 자격 증명을 사용 하 여 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-152">You are now logged in using your Facebook credentials:</span></span>

![웹 응용 프로그램: 사용자 인증 됨](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="d3963-154">문제 해결</span><span class="sxs-lookup"><span data-stu-id="d3963-154">Troubleshooting</span></span>

* <span data-ttu-id="d3963-155">**ASP.NET Core 2.x에만 해당:** `ConfigureServices`에서 `services.AddIdentity`를 호출 하 여 Id가 구성 되지 않은 경우 인증을 시도 하면 ArgumentException이 발생 합니다. *' SignInScheme ' 옵션을 제공 해야*합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-155">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="d3963-156">이 자습서에서 사용 되는 프로젝트 템플릿은이 작업이 수행 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-156">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="d3963-157">초기 마이그레이션을 적용 하 여 사이트 데이터베이스를 만들지 않은 경우 *요청 오류를 처리 하는 동안 데이터베이스 작업이 실패 했습니다* .</span><span class="sxs-lookup"><span data-stu-id="d3963-157">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="d3963-158">**마이그레이션 적용** 을 탭 하 여 데이터베이스를 만들고 새로 고쳐 오류를 계속 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-158">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d3963-159">다음 단계</span><span class="sxs-lookup"><span data-stu-id="d3963-159">Next steps</span></span>

* <span data-ttu-id="d3963-160">이 문서에서는 Facebook을 사용 하 여 인증 하는 방법을 살펴보았습니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-160">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="d3963-161">[위의 페이지](xref:security/authentication/social/index)에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-161">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="d3963-162">Azure 웹 앱에 웹 사이트를 게시 한 후에는 Facebook 개발자 포털에서 `AppSecret`를 다시 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-162">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="d3963-163">Azure Portal에서 `Authentication:Facebook:AppId` 및 `Authentication:Facebook:AppSecret`를 응용 프로그램 설정으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-163">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="d3963-164">구성 시스템은 환경 변수에서 키를 읽도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3963-164">The configuration system is set up to read keys from environment variables.</span></span>
