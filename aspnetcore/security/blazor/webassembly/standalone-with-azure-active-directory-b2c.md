---
title: Azure Active Directory B2C를 사용 하 여 ASP.NET Core Blazor Weasembomoma 독립 실행형 앱을 보호 합니다.
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0ea42943c908d8cf9d083c1cfc568c1835588ce9
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083658"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="39068-102">Azure Active Directory B2C를 사용 하 여 ASP.NET Core Blazor Weasembomoma 독립 실행형 앱을 보호 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="39068-103">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="39068-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="39068-104">인증을 위해 [AAD (Azure Active Directory) B2C](/azure/active-directory-b2c/overview) 를 사용 하는 Blazor Weasembom독립형 앱을 만들려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="39068-105">다음 항목의 지침에 따라 테 넌 트를 만들고 Azure Portal에서 웹 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="39068-106">[AAD B2C 테 넌 트를 만들어](/azure/active-directory-b2c/tutorial-create-tenant) 다음 정보를 기록 &ndash; 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="39068-107">1 \.</span><span class="sxs-lookup"><span data-stu-id="39068-107">1\.</span></span> <span data-ttu-id="39068-108">AAD B2C 인스턴스 (예: 후행 슬래시를 포함 하는 `https://contoso.b2clogin.com/`)</span><span class="sxs-lookup"><span data-stu-id="39068-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="39068-109">2 \.</span><span class="sxs-lookup"><span data-stu-id="39068-109">2\.</span></span> <span data-ttu-id="39068-110">AAD B2C 테 넌 트 도메인 (예: `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="39068-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="39068-111">앱을 등록 하는 동안 다음 항목을 선택 하 &ndash; [웹 응용 프로그램을 등록](/azure/active-directory-b2c/tutorial-register-applications) 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="39068-112">1 \.</span><span class="sxs-lookup"><span data-stu-id="39068-112">1\.</span></span> <span data-ttu-id="39068-113">**웹 앱/웹 API** 를 **예**로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="39068-114">2 \.</span><span class="sxs-lookup"><span data-stu-id="39068-114">2\.</span></span> <span data-ttu-id="39068-115">**암시적 흐름 허용** 을 **예**로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="39068-116">3\.</span><span class="sxs-lookup"><span data-stu-id="39068-116">3\.</span></span> <span data-ttu-id="39068-117">`https://localhost:5001/authentication/login-callback`**회신 URL** 을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="39068-118">응용 프로그램 ID (클라이언트 ID)를 기록 합니다 (예: `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="39068-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="39068-119">H & [사용자 흐름 만들기](/azure/active-directory-b2c/tutorial-create-user-flows) 등록 및 로그인 사용자 흐름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="39068-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) & ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="39068-120">앱에 대해 만든 등록 및 로그인 사용자 흐름 이름을 기록 합니다 (예: `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="39068-120">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="39068-121">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="39068-122">출력 위치를 지정 하려면 프로젝트 폴더를 만드는 경우 (예: `-o BlazorSample`) 경로를 사용 하 여 명령에 출력 옵션을 포함 합니다 (예:).</span><span class="sxs-lookup"><span data-stu-id="39068-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="39068-123">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39068-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="39068-124">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="39068-124">Authentication package</span></span>

<span data-ttu-id="39068-125">`IndividualB2C`(개별 B2C 계정)를 사용 하도록 앱을 만들면 앱은`Microsoft.Authentication.WebAssembly.Msal`( [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) )에 대 한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="39068-125">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="39068-126">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="39068-127">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="39068-128">이전 패키지 참조의 `{VERSION}`를 <xref:blazor/get-started> 문서에 표시 된 `Microsoft.AspNetCore.Blazor.Templates` 패키지의 버전으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="39068-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="39068-129">`Microsoft.Authentication.WebAssembly.Msal` 패키지는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 앱에 전이적으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="39068-130">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="39068-130">Authentication service support</span></span>

<span data-ttu-id="39068-131">사용자 인증에 대 한 지원은 `Microsoft.Authentication.WebAssembly.Msal` 패키지에서 제공 하는 `AddMsalAuthentication` 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39068-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="39068-132">이 메서드는 앱이 IP (Id 공급자)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="39068-133">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="39068-133">*Program.cs*:</span></span>

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

<span data-ttu-id="39068-134">`AddMsalAuthentication` 메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="39068-135">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39068-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="39068-136">Blazor Weasembmbomtemplate은 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="39068-136">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="39068-137">토큰을 로그인 흐름의 일부로 프로 비전 하려면 `MsalProviderOptions`의 기본 액세스 토큰 범위에 범위를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="39068-137">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{API ID URI}/{SCOPE}");
});
```

## <a name="index-page"></a><span data-ttu-id="39068-138">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="39068-138">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="39068-139">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="39068-139">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="39068-140">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="39068-140">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="39068-141">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="39068-141">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="39068-142">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="39068-142">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="39068-143">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="39068-143">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="39068-144">자습서: Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="39068-144">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
