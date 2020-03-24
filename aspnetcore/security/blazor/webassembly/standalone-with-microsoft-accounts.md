---
title: Microsoft 계정을 사용 하 여 ASP.NET Core Blazor Weasembomamboma 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: be73bec971f96bd64afc735a1ea750d47c7bc383
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219261"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="fc929-102">Microsoft 계정을 사용 하 여 ASP.NET Core Blazor Weasembomamboma 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="fc929-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="fc929-103">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fc929-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="fc929-104">인증을 위해 [AAD (Azure Active Directory)에서 Microsoft 계정을](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) 사용 하는 Blazor Weasembom독립형 앱을 만들려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="fc929-105">AAD 테 넌 트 및 웹 응용 프로그램 만들기</span><span class="sxs-lookup"><span data-stu-id="fc929-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="fc929-106">Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="fc929-107">1\.</span><span class="sxs-lookup"><span data-stu-id="fc929-107">1\.</span></span> <span data-ttu-id="fc929-108">앱에 대 한 **이름** (예: **Blazor Client AAD**)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="fc929-109">2\.</span><span class="sxs-lookup"><span data-stu-id="fc929-109">2\.</span></span> <span data-ttu-id="fc929-110">**지원 되는 계정 유형**에서 **조직 디렉터리의 계정**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="fc929-111">3\.</span><span class="sxs-lookup"><span data-stu-id="fc929-111">3\.</span></span> <span data-ttu-id="fc929-112">**리디렉션 uri** 드롭다운을 **웹**으로 설정 된 상태로 두고 `https://localhost:5001/authentication/login-callback`의 리디렉션 uri를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="fc929-113">4\.</span><span class="sxs-lookup"><span data-stu-id="fc929-113">4\.</span></span> <span data-ttu-id="fc929-114">**권한을** 사용 하지 않도록 설정 > **관리자 동의 하도록 요구에 게 openid connect 및 offline_access 권한 부여 확인란을** 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="fc929-115">5\.</span><span class="sxs-lookup"><span data-stu-id="fc929-115">5\.</span></span> <span data-ttu-id="fc929-116">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-116">Select **Register**.</span></span>

   <span data-ttu-id="fc929-117">**웹** > **인증** > **플랫폼 구성** 에서 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="fc929-118">1\.</span><span class="sxs-lookup"><span data-stu-id="fc929-118">1\.</span></span> <span data-ttu-id="fc929-119">`https://localhost:5001/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="fc929-120">2\.</span><span class="sxs-lookup"><span data-stu-id="fc929-120">2\.</span></span> <span data-ttu-id="fc929-121">**암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="fc929-122">3\.</span><span class="sxs-lookup"><span data-stu-id="fc929-122">3\.</span></span> <span data-ttu-id="fc929-123">앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="fc929-124">4\.</span><span class="sxs-lookup"><span data-stu-id="fc929-124">4\.</span></span> <span data-ttu-id="fc929-125">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-125">Select the **Save** button.</span></span>

   <span data-ttu-id="fc929-126">응용 프로그램 ID (클라이언트 ID)를 기록 합니다 (예: `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="fc929-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="fc929-127">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="fc929-128">출력 위치를 지정 하려면 프로젝트 폴더를 만드는 경우 (예: `-o BlazorSample`) 경로를 사용 하 여 명령에 출력 옵션을 포함 합니다 (예:).</span><span class="sxs-lookup"><span data-stu-id="fc929-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="fc929-129">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="fc929-130">앱을 만든 후 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="fc929-131">Microsoft 계정을 사용 하 여 앱에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="fc929-132">앱을 올바르게 구성 하는 경우 독립 실행형 Blazor 앱과 동일한 방식으로 Microsoft Api에 대 한 액세스 토큰을 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="fc929-133">자세한 내용은 [빠른 시작: 웹 api를 노출 하도록 응용 프로그램 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fc929-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="fc929-134">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="fc929-134">Authentication package</span></span>

<span data-ttu-id="fc929-135">회사 또는 학교 계정 (`SingleOrg`)을 사용 하도록 앱을 만들 때 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`)에 대 한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="fc929-136">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="fc929-137">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="fc929-138">이전 패키지 참조의 `{VERSION}`를 <xref:blazor/get-started> 문서에 표시 된 `Microsoft.AspNetCore.Blazor.Templates` 패키지의 버전으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="fc929-139">`Microsoft.Authentication.WebAssembly.Msal` 패키지는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 앱에 전이적으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="fc929-140">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="fc929-140">Authentication service support</span></span>

<span data-ttu-id="fc929-141">사용자 인증에 대 한 지원은 `Microsoft.Authentication.WebAssembly.Msal` 패키지에서 제공 하는 `AddMsalAuthentication` 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="fc929-142">이 메서드는 앱이 IP (Id 공급자)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="fc929-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fc929-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="fc929-144">`AddMsalAuthentication` 메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="fc929-145">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Microsoft 계정 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc929-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="index-page"></a><span data-ttu-id="fc929-146">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="fc929-146">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="fc929-147">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="fc929-147">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="fc929-148">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="fc929-148">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="fc929-149">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="fc929-149">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="fc929-150">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="fc929-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="fc929-151">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="fc929-151">Additional resources</span></span>

* [<span data-ttu-id="fc929-152">빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록</span><span class="sxs-lookup"><span data-stu-id="fc929-152">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="fc929-153">빠른 시작: 웹 Api를 노출 하도록 응용 프로그램 구성</span><span class="sxs-lookup"><span data-stu-id="fc929-153">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
