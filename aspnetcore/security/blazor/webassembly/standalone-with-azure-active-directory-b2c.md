---
title: Azure Active Directory B2C를 사용 하 여 ASP.NET Core Blazor Weasembomoma 독립 실행형 앱을 보호 합니다.
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: b4d32e91b4013cbea37baecb972a535d2874d3d1
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434462"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="7abd2-102">Azure Active Directory B2C를 사용 하 여 ASP.NET Core Blazor Weasembomoma 독립 실행형 앱을 보호 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="7abd2-103">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7abd2-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="7abd2-104">인증을 위해 [AAD (Azure Active Directory) B2C](/azure/active-directory-b2c/overview) 를 사용 하는 Blazor Weasembom독립형 앱을 만들려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="7abd2-105">다음 항목의 지침에 따라 테 넌 트를 만들고 Azure Portal에서 웹 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="7abd2-106">[AAD B2C 테 넌 트를 만들어](/azure/active-directory-b2c/tutorial-create-tenant) 다음 정보를 기록 &ndash; 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="7abd2-107">1 \.</span><span class="sxs-lookup"><span data-stu-id="7abd2-107">1\.</span></span> <span data-ttu-id="7abd2-108">AAD B2C 인스턴스 (예: 후행 슬래시를 포함 하는 `https://contoso.b2clogin.com/`)</span><span class="sxs-lookup"><span data-stu-id="7abd2-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="7abd2-109">2 \.</span><span class="sxs-lookup"><span data-stu-id="7abd2-109">2\.</span></span> <span data-ttu-id="7abd2-110">AAD B2C 테 넌 트 도메인 (예: `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="7abd2-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="7abd2-111">앱을 등록 하는 동안 다음 항목을 선택 하 &ndash; [웹 응용 프로그램을 등록](/azure/active-directory-b2c/tutorial-register-applications) 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="7abd2-112">1 \.</span><span class="sxs-lookup"><span data-stu-id="7abd2-112">1\.</span></span> <span data-ttu-id="7abd2-113">**웹 앱/웹 API** 를 **예**로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="7abd2-114">2 \.</span><span class="sxs-lookup"><span data-stu-id="7abd2-114">2\.</span></span> <span data-ttu-id="7abd2-115">**암시적 흐름 허용** 을 **예**로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="7abd2-116">3\.</span><span class="sxs-lookup"><span data-stu-id="7abd2-116">3\.</span></span> <span data-ttu-id="7abd2-117">`https://localhost:5001/authentication/login-callback`**회신 URL** 을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="7abd2-118">응용 프로그램 ID (클라이언트 ID)를 기록 합니다 (예: `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="7abd2-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="7abd2-119">[사용자](/azure/active-directory-b2c/tutorial-create-user-flows) 흐름 &ndash; 만들어 등록 및 로그인 사용자 흐름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="7abd2-120">최소한 **응용 프로그램 클레임** > **표시 이름** 사용자 특성을 선택 하 여 `LoginDisplay` 구성 요소 (*Shared/LoginDisplay*)에 `context.User.Identity.Name`를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="7abd2-121">앱에 대해 만든 등록 및 로그인 사용자 흐름 이름을 기록 합니다 (예: `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="7abd2-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="7abd2-122">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="7abd2-123">출력 위치를 지정 하려면 프로젝트 폴더를 만드는 경우 (예: `-o BlazorSample`) 경로를 사용 하 여 명령에 출력 옵션을 포함 합니다 (예:).</span><span class="sxs-lookup"><span data-stu-id="7abd2-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="7abd2-124">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="7abd2-125">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="7abd2-125">Authentication package</span></span>

<span data-ttu-id="7abd2-126">`IndividualB2C`(개별 B2C 계정)를 사용 하도록 앱을 만들면 앱은`Microsoft.Authentication.WebAssembly.Msal`( [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) )에 대 한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="7abd2-127">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7abd2-128">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="7abd2-129">이전 패키지 참조의 `{VERSION}`를 <xref:blazor/get-started> 문서에 표시 된 `Microsoft.AspNetCore.Blazor.Templates` 패키지의 버전으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="7abd2-130">`Microsoft.Authentication.WebAssembly.Msal` 패키지는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 앱에 전이적으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="7abd2-131">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="7abd2-131">Authentication service support</span></span>

<span data-ttu-id="7abd2-132">사용자 인증에 대 한 지원은 `Microsoft.Authentication.WebAssembly.Msal` 패키지에서 제공 하는 `AddMsalAuthentication` 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="7abd2-133">이 메서드는 앱이 IP (Id 공급자)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="7abd2-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7abd2-134">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="7abd2-135">`AddMsalAuthentication` 메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="7abd2-136">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="7abd2-137">Blazor Weasembmbomtemplate은 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="7abd2-138">토큰을 로그인 흐름의 일부로 프로 비전 하려면 `MsalProviderOptions`의 기본 액세스 토큰 범위에 범위를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7abd2-138">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{API ID URI}/{SCOPE}");
});
```

## <a name="index-page"></a><span data-ttu-id="7abd2-139">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="7abd2-139">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="7abd2-140">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7abd2-140">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="7abd2-141">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7abd2-141">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="7abd2-142">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7abd2-142">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="7abd2-143">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7abd2-143">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7abd2-144">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="7abd2-144">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="7abd2-145">자습서: Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="7abd2-145">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
