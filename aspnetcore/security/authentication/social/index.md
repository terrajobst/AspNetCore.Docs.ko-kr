---
title: ASP.NET Core에서 Facebook, Google 및 외부 공급자 인증
author: rick-anderson
description: 이 자습서에는 외부 인증 공급자에 OAuth 2.0을 사용하여 ASP.NET Core 2.x 앱을 빌드하는 방법을 보여줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 4/19/2019
uid: security/authentication/social/index
ms.openlocfilehash: e2d68ac93bdcfa2fc015e8447ea38626787cdb02
ms.sourcegitcommit: a3926eae3f687013027a2828830c12a89add701f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65451049"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="68ed6-103">ASP.NET Core에서 Facebook, Google 및 외부 공급자 인증</span><span class="sxs-lookup"><span data-stu-id="68ed6-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="68ed6-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="68ed6-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="68ed6-105">이 자습서에서는 사용자가 외부 인증 공급 기업의 자격 증명으로 OAuth 2.0을 사용하여 로그인할 수 있도록 ASP.NET Core 2.2 앱을 빌드하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-105">This tutorial demonstrates how to build an ASP.NET Core 2.2 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="68ed6-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) 및 [Microsoft](xref:security/authentication/microsoft-logins) 공급자는 다음 섹션에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections.</span></span> <span data-ttu-id="68ed6-107">다른 공급자는 [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) 및 [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers)와 같은 타사 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

![Facebook, Twitter, Google Plus 및 Windows용 소셜 미디어 아이콘](index/_static/social.png)

<span data-ttu-id="68ed6-109">사용자가 기존 자격 증명으로 로그인할 수 있도록 설정:</span><span class="sxs-lookup"><span data-stu-id="68ed6-109">Enabling users to sign in with their existing credentials:</span></span>
* <span data-ttu-id="68ed6-110">사용자에게 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-110">Is convenient for the users.</span></span>
* <span data-ttu-id="68ed6-111">로그인 프로세스를 관리하는 많은 복잡한 과정을 타사로 이전합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-111">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span> 

<span data-ttu-id="68ed6-112">소셜 로그인이 트래픽 및 고객 변환을 제공할 수 있는 방법에 대한 예제는 [Facebook](https://www.facebook.com/unsupportedbrowser) 및 [Twitter](https://dev.twitter.com/resources/case-studies)의 사례 연구를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="68ed6-112">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="68ed6-113">새 ASP.NET Core 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="68ed6-113">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68ed6-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68ed6-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="68ed6-115">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="68ed6-116">새 ASP.NET Core 웹 애플리케이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-116">Create a new ASP.NET Core Web Application.</span></span>
* <span data-ttu-id="68ed6-117">드롭다운에서 **ASP.NET Core 2.2**를 선택한 다음, **웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-117">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>
* <span data-ttu-id="68ed6-118">**인증 변경**을 선택하고 인증을 **개별 사용자 계정**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-118">Select **Change Authentication** and set authentication to **Individual User Accounts**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="68ed6-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="68ed6-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="68ed6-120">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-120">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="68ed6-121">디렉터리(`cd`)를 프로젝트를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-121">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="68ed6-122">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-122">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o WebApp1 -au Individual -uld
  code -r WebApp1
  ```

  * <span data-ttu-id="68ed6-123">`dotnet new` 명령은 *WebApp1* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-123">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="68ed6-124">`-uld`는 SQLite 대신 LocalDB를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-124">`-uld` uses LocalDB instead of SQLite.</span></span> <span data-ttu-id="68ed6-125">SQLite를 사용하려면 `-uld`를 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-125">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="68ed6-126">`-au Individual`은 개별 인증을 위한 코드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-126">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="68ed6-127">`code` 명령은 Visual Studio Code의 새 인스턴스에서 *WebApp1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-127">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="68ed6-128">**가 있는 대화 상자가 나타나고 'WebApp1'에서 빌드 및 디버그에 필요한 자산이 누락되었습니다. 추가할까요?**</span><span class="sxs-lookup"><span data-stu-id="68ed6-128">A dialog box appears with **Required assets to build and debug are missing from 'WebApp1'. Add them?**</span></span>

* <span data-ttu-id="68ed6-129">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-129">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="68ed6-130">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="68ed6-130">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="68ed6-131">터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-131">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o WebApp1 -au Individual
```

<span data-ttu-id="68ed6-132">이전 명령은 [.NET Core CLI](/dotnet/core/tools/dotnet)를 사용하여 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-132">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="68ed6-133">프로젝트 열기</span><span class="sxs-lookup"><span data-stu-id="68ed6-133">Open the project</span></span>

<span data-ttu-id="68ed6-134">Visual Studio에서 **파일 > 열기**를 선택한 다음, *WebApp1.csproj* 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-134">From Visual Studio, select **File > Open**, and then select the *WebApp1.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="apply-migrations"></a><span data-ttu-id="68ed6-135">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="68ed6-135">Apply migrations</span></span>

* <span data-ttu-id="68ed6-136">앱을 실행하고 **레지스터** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-136">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="68ed6-137">새 계정의 메일과 암호를 입력한 다음 **등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-137">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="68ed6-138">지침에 따라 마이그레이션을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-138">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="68ed6-139">SecretManager를 사용하여 로그인 공급자에 의해 할당된 토큰 저장</span><span class="sxs-lookup"><span data-stu-id="68ed6-139">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="68ed6-140">소셜 로그인 공급자는 등록 프로세스 중에 **애플리케이션 ID** 및 **애플리케이션 암호** 토큰을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-140">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="68ed6-141">정확한 토큰 이름은 공급자에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-141">The exact token names vary by provider.</span></span> <span data-ttu-id="68ed6-142">이러한 토큰은 앱이 API에 액세스하는 데 사용하는 자격 증명을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-142">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="68ed6-143">토큰은 [Secret Manager](xref:security/app-secrets#secret-manager)의 도움으로 앱 구성에 연결할 수 있는 "암호"를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-143">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="68ed6-144">Secret Manager는 *appsettings.json*과 같은 구성 파일에 토큰을 저장하는 보다 안전한 대안입니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-144">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="68ed6-145">Secret Manager는 개발 목적으로만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-145">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="68ed6-146">[Azure Key Vault 구성 제공자](xref:security/key-vault-configuration)로 Azure 테스트 및 프로덕션 암호를 저장하고 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-146">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="68ed6-147">[ASP.NET Core에서 개발 중인 앱 암호의 안전한 스토리지](xref:security/app-secrets) 항목의 단계에 따라 아래의 각 로그인 공급자에 의해 할당된 토큰을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-147">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="68ed6-148">애플리케이션에 필요한 로그인 공급자 설정</span><span class="sxs-lookup"><span data-stu-id="68ed6-148">Setup login providers required by your application</span></span>

<span data-ttu-id="68ed6-149">다음 항목을 사용하여 해당 공급자를 사용하도록 애플리케이션을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-149">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="68ed6-150">[Facebook](xref:security/authentication/facebook-logins) 지침</span><span class="sxs-lookup"><span data-stu-id="68ed6-150">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="68ed6-151">[Twitter](xref:security/authentication/twitter-logins) 지침</span><span class="sxs-lookup"><span data-stu-id="68ed6-151">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="68ed6-152">[Google](xref:security/authentication/google-logins) 지침</span><span class="sxs-lookup"><span data-stu-id="68ed6-152">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="68ed6-153">[Microsoft](xref:security/authentication/microsoft-logins) 지침</span><span class="sxs-lookup"><span data-stu-id="68ed6-153">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="68ed6-154">[다른 공급자](xref:security/authentication/otherlogins) 지침</span><span class="sxs-lookup"><span data-stu-id="68ed6-154">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="68ed6-155">필요에 따라 암호 설정</span><span class="sxs-lookup"><span data-stu-id="68ed6-155">Optionally set password</span></span>

<span data-ttu-id="68ed6-156">외부 로그인 공급자를 등록하는 경우 앱에 암호를 등록하지 않은 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-156">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="68ed6-157">그러면 사이트에 암호를 만들고 기억하지 않아도 되지만 외부 로그인 공급자에 따라 다르게 적용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-157">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="68ed6-158">외부 로그인 공급자를 사용할 수 없는 경우 웹 사이트에 로그인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-158">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="68ed6-159">외부 공급자로 로그인하는 프로세스 중에 설정한 전자 메일을 사용하여 암호를 만들고 로그인하려면:</span><span class="sxs-lookup"><span data-stu-id="68ed6-159">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="68ed6-160">오른쪽 위 모서리에 있는 **Hello &lt;이메일 별칭&gt;** 링크를 선택하여 **관리** 보기로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-160">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![웹 애플리케이션 관리 뷰](index/_static/pass1a.png)

* <span data-ttu-id="68ed6-162">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-162">Select **Create**</span></span>

![암호 페이지 설정](index/_static/pass2a.png)

* <span data-ttu-id="68ed6-164">올바른 암호를 설정하고 사용자의 전자 메일을 사용하여 로그인하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-164">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="68ed6-165">다음 단계</span><span class="sxs-lookup"><span data-stu-id="68ed6-165">Next steps</span></span>

* <span data-ttu-id="68ed6-166">이 문서는 외부 인증을 고새하고 ASP.NET Core 앱에 외부 로그인을 추가하는 데 필요한 필수 구성 요소를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-166">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>

* <span data-ttu-id="68ed6-167">앱에 필요한 공급자에 대한 로그인을 구성하기 위해 공급자 관련 페이지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-167">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>

* <span data-ttu-id="68ed6-168">사용자와 해당 액세스 및 새로 고침 토큰에 대한 추가 데이터를 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68ed6-168">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="68ed6-169">자세한 내용은 <xref:security/authentication/social/additional-claims>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="68ed6-169">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
