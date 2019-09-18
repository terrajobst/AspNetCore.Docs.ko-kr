---
title: ASP.NET Core에서 Facebook, Google 및 외부 공급자 인증
author: rick-anderson
description: 이 자습서에는 외부 인증 공급자에 OAuth 2.0을 사용하여 ASP.NET Core 2.x 앱을 빌드하는 방법을 보여줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 05/10/2019
uid: security/authentication/social/index
ms.openlocfilehash: edaf9eeaf02879b2f7816bab0eb373a7de640c05
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082513"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="a7f83-103">ASP.NET Core에서 Facebook, Google 및 외부 공급자 인증</span><span class="sxs-lookup"><span data-stu-id="a7f83-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="a7f83-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a7f83-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a7f83-105">이 자습서에서는 사용자가 외부 인증 공급 기업의 자격 증명으로 OAuth 2.0을 사용하여 로그인할 수 있도록 ASP.NET Core 2.2 앱을 빌드하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-105">This tutorial demonstrates how to build an ASP.NET Core 2.2 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="a7f83-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) 및 [Microsoft](xref:security/authentication/microsoft-logins) 공급자는 다음 섹션에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections.</span></span> <span data-ttu-id="a7f83-107">다른 공급자는 [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) 및 [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers)와 같은 타사 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

![Facebook, Twitter, Google Plus 및 Windows용 소셜 미디어 아이콘](index/_static/social.png)

<span data-ttu-id="a7f83-109">사용자가 기존 자격 증명으로 로그인할 수 있도록 설정:</span><span class="sxs-lookup"><span data-stu-id="a7f83-109">Enabling users to sign in with their existing credentials:</span></span>
* <span data-ttu-id="a7f83-110">사용자에게 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-110">Is convenient for the users.</span></span>
* <span data-ttu-id="a7f83-111">로그인 프로세스를 관리하는 많은 복잡한 과정을 타사로 이전합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-111">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span> 

<span data-ttu-id="a7f83-112">소셜 로그인이 트래픽 및 고객 변환을 제공할 수 있는 방법에 대한 예제는 [Facebook](https://www.facebook.com/unsupportedbrowser) 및 [Twitter](https://dev.twitter.com/resources/case-studies)의 사례 연구를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a7f83-112">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="a7f83-113">새 ASP.NET Core 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="a7f83-113">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a7f83-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a7f83-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a7f83-115">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-115">Create a new project.</span></span>
* <span data-ttu-id="a7f83-116">**ASP.NET Core 웹 애플리케이션** 및 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-116">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="a7f83-117">**프로젝트 이름**을 제공하고 **위치**를 확인하거나 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-117">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="a7f83-118">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-118">Select **Create**.</span></span>
* <span data-ttu-id="a7f83-119">드롭다운에서 **ASP.NET Core 2.2**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-119">Select **ASP.NET Core 2.2** in the drop down.</span></span> <span data-ttu-id="a7f83-120">템플릿 목록에서 **웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-120">Select **Web Application** in the template list.</span></span>
* <span data-ttu-id="a7f83-121">**인증**에서 **변경**을 선택하고 인증을 **개별 사용자 계정**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-121">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="a7f83-122">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-122">Select **OK**.</span></span>
* <span data-ttu-id="a7f83-123">**새 ASP.NET Core 웹 애플리케이션 만들기** 창에서 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-123">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a7f83-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a7f83-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a7f83-125">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="a7f83-126">디렉터리(`cd`)를 프로젝트를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-126">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="a7f83-127">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-127">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  code -r WebApp1
  ```

  * <span data-ttu-id="a7f83-128">`dotnet new` 명령은 *WebApp1* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="a7f83-129">`-uld`는 SQLite 대신 LocalDB를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-129">`-uld` uses LocalDB instead of SQLite.</span></span> <span data-ttu-id="a7f83-130">SQLite를 사용하려면 `-uld`를 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-130">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="a7f83-131">`-au Individual`은 개별 인증을 위한 코드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-131">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="a7f83-132">`code` 명령은 Visual Studio Code의 새 인스턴스에서 *WebApp1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

* <span data-ttu-id="a7f83-133">**가 있는 대화 상자가 나타나고 'WebApp1'에서 빌드 및 디버그에 필요한 자산이 누락되었습니다. 추가할까요?**</span><span class="sxs-lookup"><span data-stu-id="a7f83-133">A dialog box appears with **Required assets to build and debug are missing from 'WebApp1'. Add them?**</span></span> <span data-ttu-id="a7f83-134">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-134">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a7f83-135">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a7f83-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a7f83-136">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-136">Select **File** > **New Solution**.</span></span>
* <span data-ttu-id="a7f83-137">사이드바에서 **.NET Core** > **앱**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-137">Select **.NET Core** > **App** in the sidebar.</span></span> <span data-ttu-id="a7f83-138">**웹 애플리케이션** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-138">Select the **Web Application** template.</span></span> <span data-ttu-id="a7f83-139">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-139">Select **Next**.</span></span>
* <span data-ttu-id="a7f83-140">**대상프레임 워크** 드롭다운을 **.NET Core 2.2**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-140">Set the **Target Framework** drop down to **.NET Core 2.2**.</span></span> <span data-ttu-id="a7f83-141">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-141">Select **Next**.</span></span>
* <span data-ttu-id="a7f83-142">**프로젝트 이름**을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="a7f83-142">Provide a **Project Name**.</span></span> <span data-ttu-id="a7f83-143">**위치**를 확인하거나 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-143">Confirm or change the **Location**.</span></span> <span data-ttu-id="a7f83-144">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-144">Select **Create**.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="a7f83-145">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="a7f83-145">Apply migrations</span></span>

* <span data-ttu-id="a7f83-146">앱을 실행하고 **레지스터** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-146">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="a7f83-147">새 계정의 메일과 암호를 입력한 다음 **등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-147">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="a7f83-148">지침에 따라 마이그레이션을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-148">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="a7f83-149">SecretManager를 사용하여 로그인 공급자에 의해 할당된 토큰 저장</span><span class="sxs-lookup"><span data-stu-id="a7f83-149">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="a7f83-150">소셜 로그인 공급자는 등록 프로세스 중에 **애플리케이션 ID** 및 **애플리케이션 암호** 토큰을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-150">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="a7f83-151">정확한 토큰 이름은 공급자에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-151">The exact token names vary by provider.</span></span> <span data-ttu-id="a7f83-152">이러한 토큰은 앱이 API에 액세스하는 데 사용하는 자격 증명을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-152">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="a7f83-153">토큰은 [Secret Manager](xref:security/app-secrets#secret-manager)의 도움으로 앱 구성에 연결할 수 있는 "암호"를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-153">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="a7f83-154">Secret Manager는 *appsettings.json*과 같은 구성 파일에 토큰을 저장하는 보다 안전한 대안입니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-154">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a7f83-155">Secret Manager는 개발 목적으로만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-155">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="a7f83-156">[Azure Key Vault 구성 제공자](xref:security/key-vault-configuration)로 Azure 테스트 및 프로덕션 암호를 저장하고 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-156">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="a7f83-157">[ASP.NET Core에서 개발 중인 앱 암호의 안전한 스토리지](xref:security/app-secrets) 항목의 단계에 따라 아래의 각 로그인 공급자에 의해 할당된 토큰을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-157">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="a7f83-158">애플리케이션에 필요한 로그인 공급자 설정</span><span class="sxs-lookup"><span data-stu-id="a7f83-158">Setup login providers required by your application</span></span>

<span data-ttu-id="a7f83-159">다음 항목을 사용하여 해당 공급자를 사용하도록 애플리케이션을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-159">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="a7f83-160">[Facebook](xref:security/authentication/facebook-logins) 지침</span><span class="sxs-lookup"><span data-stu-id="a7f83-160">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="a7f83-161">[Twitter](xref:security/authentication/twitter-logins) 지침</span><span class="sxs-lookup"><span data-stu-id="a7f83-161">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="a7f83-162">[Google](xref:security/authentication/google-logins) 지침</span><span class="sxs-lookup"><span data-stu-id="a7f83-162">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="a7f83-163">[Microsoft](xref:security/authentication/microsoft-logins) 지침</span><span class="sxs-lookup"><span data-stu-id="a7f83-163">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="a7f83-164">[다른 공급자](xref:security/authentication/otherlogins) 지침</span><span class="sxs-lookup"><span data-stu-id="a7f83-164">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="a7f83-165">필요에 따라 암호 설정</span><span class="sxs-lookup"><span data-stu-id="a7f83-165">Optionally set password</span></span>

<span data-ttu-id="a7f83-166">외부 로그인 공급자를 등록하는 경우 앱에 암호를 등록하지 않은 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-166">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="a7f83-167">그러면 사이트에 암호를 만들고 기억하지 않아도 되지만 외부 로그인 공급자에 따라 다르게 적용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-167">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="a7f83-168">외부 로그인 공급자를 사용할 수 없는 경우 웹 사이트에 로그인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-168">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="a7f83-169">외부 공급자로 로그인하는 프로세스 중에 설정한 전자 메일을 사용하여 암호를 만들고 로그인하려면:</span><span class="sxs-lookup"><span data-stu-id="a7f83-169">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="a7f83-170">오른쪽 위 모서리에 있는 **Hello &lt;이메일 별칭&gt;** 링크를 선택하여 **관리** 보기로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-170">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![웹 애플리케이션 관리 뷰](index/_static/pass1a.png)

* <span data-ttu-id="a7f83-172">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-172">Select **Create**</span></span>

![암호 페이지 설정](index/_static/pass2a.png)

* <span data-ttu-id="a7f83-174">올바른 암호를 설정하고 사용자의 전자 메일을 사용하여 로그인하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-174">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a7f83-175">다음 단계</span><span class="sxs-lookup"><span data-stu-id="a7f83-175">Next steps</span></span>

* <span data-ttu-id="a7f83-176">이 문서는 외부 인증을 고새하고 ASP.NET Core 앱에 외부 로그인을 추가하는 데 필요한 필수 구성 요소를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-176">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>

* <span data-ttu-id="a7f83-177">앱에 필요한 공급자에 대한 로그인을 구성하기 위해 공급자 관련 페이지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-177">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>

* <span data-ttu-id="a7f83-178">사용자와 해당 액세스 및 새로 고침 토큰에 대한 추가 데이터를 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7f83-178">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="a7f83-179">자세한 내용은 <xref:security/authentication/social/additional-claims>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a7f83-179">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
