---
title: ASP.NET Core에서 Facebook 외부 로그인 설정
author: rick-anderson
description: 기존 ASP.NET Core 앱에 Facebook 계정 사용자 인증의 통합을 보여 주는 코드 예제를 사용 하 여 자습서입니다.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/04/2019
uid: security/authentication/facebook-logins
ms.openlocfilehash: 84b891f6cee71a26726d49a9d42ae45007d2b429
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893590"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="4048c-103">ASP.NET Core에서 Facebook 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="4048c-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="4048c-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4048c-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4048c-105">코드 예제를 사용 하 여이 자습서에서 만든 샘플 ASP.NET Core 2.0 프로젝트를 사용 하 여 Facebook 계정으로 로그인 사용자가 사용 하도록 설정 하는 방법을 보여 줍니다 합니다 [이전 페이지](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="4048c-106">수행 하 여 Facebook 앱 ID를 만드는 것으로 시작 합니다 [공식 단계](https://developers.facebook.com)합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="4048c-107">Facebook에서 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="4048c-107">Create the app in Facebook</span></span>

* <span data-ttu-id="4048c-108">로 이동 합니다 [Facebook 개발자 앱](https://developers.facebook.com/apps/) 페이지 하 고 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-108">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="4048c-109">사용 하 여 Facebook 계정이 아직 없다면 합니다 **Facebook에 대 한 등록** 만들려면 로그인 페이지에 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-109">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>

* <span data-ttu-id="4048c-110">탭의 **새 앱 추가** 단추는 오른쪽 위 모서리에는 새 앱 ID를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-110">Tap the **Add a New App** button in the upper right corner to create a new App ID.</span></span>

   ![Facebook 개발자 포털에 대 한 Microsoft Edge에서 열린](index/_static/FBMyApps.png)

* <span data-ttu-id="4048c-112">양식을 작성 하 고 탭의 **앱 ID 만들기** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-112">Fill out the form and tap the **Create App ID** button.</span></span>

  ![새 앱 ID 양식 만들기](index/_static/FBNewAppId.png)

* <span data-ttu-id="4048c-114">에 **제품을 선택** 페이지에서 클릭 **설정** 에 **Facebook 로그인** 카드.</span><span class="sxs-lookup"><span data-stu-id="4048c-114">On the **Select a product** page, click **Set Up** on the **Facebook Login** card.</span></span>

  ![제품 설치 페이지](index/_static/FBProductSetup.png)

* <span data-ttu-id="4048c-116">합니다 **퀵 스타트** 마법사가 시작 됩니다 **플랫폼을 선택** 의 첫 번째 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-116">The **Quickstart** wizard will launch with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="4048c-117">마법사를 클릭 하 여 지금은 무시 합니다 **설정을** 왼쪽 메뉴에서 링크:</span><span class="sxs-lookup"><span data-stu-id="4048c-117">Bypass the wizard for now by clicking the **Settings** link in the menu on the left:</span></span>

  ![Skip 빠른 시작](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="4048c-119">표시 되는 **클라이언트 OAuth 설정** 페이지:</span><span class="sxs-lookup"><span data-stu-id="4048c-119">You are presented with the **Client OAuth Settings** page:</span></span>

  ![클라이언트 OAuth 설정 페이지](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="4048c-121">개발 URI를 입력으로 */signin-facebook* 에 추가 합니다 **유효한 OAuth 리디렉션 Uri** 필드 (예를 들어: `https://localhost:44320/signin-facebook`).</span><span class="sxs-lookup"><span data-stu-id="4048c-121">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="4048c-122">이 자습서의 뒷부분에서 구성 된 Facebook 인증에는 요청을 자동으로 처리할 */signin-facebook* OAuth 흐름을 구현 하는 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-122">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="4048c-123">URI */signin-facebook* Facebook 인증 공급자의 기본 콜백으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-123">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="4048c-124">상속 된 통해 Facebook 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다 [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 의 속성을 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-124">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="4048c-125">클릭 **변경 내용을 저장**합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-125">Click **Save Changes**.</span></span>

* <span data-ttu-id="4048c-126">클릭 **설정을** > **기본** 왼쪽된 탐색 창에서 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-126">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="4048c-127">이 페이지에서는 기록해 하 `App ID` 고 `App Secret`입니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-127">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="4048c-128">다음 섹션에서 ASP.NET Core 응용 프로그램에 둘 다를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-128">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="4048c-129">다시 방문 해야 하는 사이트를 배포 하는 경우는 **Facebook 로그인** 페이지를 설정 하 고 새 공용 URI를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-129">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-facebook-app-id-and-app-secret"></a><span data-ttu-id="4048c-130">Facebook 앱 ID 및 앱 암호를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-130">Store Facebook App ID and App Secret</span></span>

<span data-ttu-id="4048c-131">Facebook과 같은 중요 한 설정 연결 `App ID` 하 고 `App Secret` 사용 하 여 응용 프로그램 구성에는 [암호 관리자](xref:security/app-secrets)합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-131">Link sensitive settings like Facebook `App ID` and `App Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="4048c-132">이 자습서에서는 이름을 토큰 `Authentication:Facebook:AppId` 고 `Authentication:Facebook:AppSecret`입니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-132">For the purposes of this tutorial, name the tokens `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="4048c-133">안전 하 게 저장 하려면 다음 명령을 실행 `App ID` 고 `App Secret` 암호 관리자를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="4048c-133">Execute the following commands to securely store `App ID` and `App Secret` using Secret Manager:</span></span>

```console
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a><span data-ttu-id="4048c-134">Facebook 인증 구성</span><span class="sxs-lookup"><span data-stu-id="4048c-134">Configure Facebook Authentication</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="4048c-135">Facebook 서비스에 추가 합니다 `ConfigureServices` 의 메서드를 *Startup.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="4048c-135">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="4048c-136">설치 합니다 [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) 패키지 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-136">Install the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) package.</span></span>

* <span data-ttu-id="4048c-137">Visual Studio 2017을 사용 하 여이 패키지를 설치 하려면 마우스 오른쪽 단추로 클릭 프로젝트를 마우스 **NuGet 패키지 관리**합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-137">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="4048c-138">.NET Core CLI를 설치 하려면 프로젝트 디렉터리에서 다음을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-138">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

<span data-ttu-id="4048c-139">Facebook 미들웨어를 추가 합니다 `Configure` 의 메서드 *Startup.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="4048c-139">Add the Facebook middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

::: moniker-end

<span data-ttu-id="4048c-140">참조 된 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) Facebook 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조.</span><span class="sxs-lookup"><span data-stu-id="4048c-140">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="4048c-141">구성 옵션을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-141">Configuration options can be used to:</span></span>

* <span data-ttu-id="4048c-142">사용자에 대 한 다른 정보를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-142">Request different information about the user.</span></span>
* <span data-ttu-id="4048c-143">로그인 환경을 사용자 지정 하는 쿼리 문자열 인수를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-143">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="4048c-144">Facebook으로 로그인</span><span class="sxs-lookup"><span data-stu-id="4048c-144">Sign in with Facebook</span></span>

<span data-ttu-id="4048c-145">응용 프로그램을 실행 하 고 클릭 **로그인**합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-145">Run your application and click **Log in**.</span></span> <span data-ttu-id="4048c-146">Facebook으로 로그인 하는 옵션이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-146">You see an option to sign in with Facebook.</span></span>

![웹 응용 프로그램: 인증 되지 않은 사용자](index/_static/DoneFacebook.png)

<span data-ttu-id="4048c-148">클릭 하면 **Facebook**, 인증에 대 한 Facebook로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-148">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Facebook 인증 페이지](index/_static/FBLogin.png)

<span data-ttu-id="4048c-150">Facebook 인증 기본적으로 공용 프로필 및 전자 메일 주소를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-150">Facebook authentication requests public profile and email address by default:</span></span>

![Facebook 인증 페이지 동의 화면](index/_static/FBLoginDone.png)

<span data-ttu-id="4048c-152">Facebook 자격 증명을 입력 한 후 전자 메일을 설정할 수 있는 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-152">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="4048c-153">이제 Facebook 자격 증명을 사용 하 여 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-153">You are now logged in using your Facebook credentials:</span></span>

![웹 응용 프로그램: 인증 된 사용자](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="4048c-155">문제 해결</span><span class="sxs-lookup"><span data-stu-id="4048c-155">Troubleshooting</span></span>

* <span data-ttu-id="4048c-156">**ASP.NET Core 2.x만:** 호출 하 여 구성 되어 있지 않으면 Identity `services.AddIdentity` 에 `ConfigureServices`를 인증 하려고 하면 *ArgumentException: 'SignInScheme' 옵션을 제공 해야*합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-156">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="4048c-157">이 자습서에 사용 되는 프로젝트 템플릿이이 수행 되도록 보장 합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-157">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="4048c-158">사이트 데이터베이스를 초기 마이그레이션을 적용 하 여 만들어지지 않은, 하는 경우 얻게 *요청을 처리 하는 동안 데이터베이스 작업이 실패 했습니다.* 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-158">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="4048c-159">탭 **마이그레이션 적용** 데이터베이스를 만들고 오류 지 나 새로 고침 합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-159">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4048c-160">다음 단계</span><span class="sxs-lookup"><span data-stu-id="4048c-160">Next steps</span></span>

* <span data-ttu-id="4048c-161">추가 된 [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) 고급 Facebook 인증 시나리오에 대 한 프로젝트에 NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="4048c-161">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to your project for advanced Facebook authentication scenarios.</span></span> <span data-ttu-id="4048c-162">이 패키지에 앱을 사용 하 여 Facebook 외부 로그인 기능을 통합 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-162">This package isn't required to integrate Facebook external login functionality with your app.</span></span> 

* <span data-ttu-id="4048c-163">이 문서에서는 Facebook을 사용 하 여 인증 하는 보여 주었습니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-163">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="4048c-164">에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다 합니다 [이전 페이지](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-164">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="4048c-165">다시 설정 해야 Azure 웹 앱에 웹 사이트를 게시 하면는 `AppSecret` Facebook 개발자 포털에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-165">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="4048c-166">설정 된 `Authentication:Facebook:AppId` 및 `Authentication:Facebook:AppSecret` Azure portal에서 응용 프로그램 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-166">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="4048c-167">구성 시스템 환경 변수에서 키를 읽을 수 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4048c-167">The configuration system is set up to read keys from environment variables.</span></span>
