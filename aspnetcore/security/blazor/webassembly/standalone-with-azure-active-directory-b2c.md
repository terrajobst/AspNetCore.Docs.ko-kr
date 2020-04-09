---
title: Azure Active Blazor Directory B2C를 통해 ASP.NET 핵심 웹어셈블리 독립 실행형 앱 보안
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0734bad2d4281eb856783a362ef8c608a303c17a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977056"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="3daa2-102">Azure Active Blazor Directory B2C를 통해 ASP.NET 핵심 웹어셈블리 독립 실행형 앱 보안</span><span class="sxs-lookup"><span data-stu-id="3daa2-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="3daa2-103">[하비에르 칼바로 넬슨과](https://github.com/javiercn) [루크 라담](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3daa2-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="3daa2-104">인증을 위해 Blazor [AAD(Azure Active Directory) B2C를](/azure/active-directory-b2c/overview) 사용하는 웹 Assembly 독립 실행형 앱을 만들려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="3daa2-105">다음 항목의 지침을 따라 테넌트를 만들고 Azure Portal에서 웹 앱을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="3daa2-106">[AAD B2C 테넌트](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; 만들기 다음 정보를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="3daa2-107">1\.</span><span class="sxs-lookup"><span data-stu-id="3daa2-107">1\.</span></span> <span data-ttu-id="3daa2-108">AAD B2C 인스턴스(예: `https://contoso.b2clogin.com/`후행 슬래시포함)</span><span class="sxs-lookup"><span data-stu-id="3daa2-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="3daa2-109">2\.</span><span class="sxs-lookup"><span data-stu-id="3daa2-109">2\.</span></span> <span data-ttu-id="3daa2-110">AAD B2C 테넌트 `contoso.onmicrosoft.com`도메인(예: )</span><span class="sxs-lookup"><span data-stu-id="3daa2-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="3daa2-111">[웹 응용 프로그램](/azure/active-directory-b2c/tutorial-register-applications) &ndash; 등록 앱 등록 중에 다음 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="3daa2-112">1\.</span><span class="sxs-lookup"><span data-stu-id="3daa2-112">1\.</span></span> <span data-ttu-id="3daa2-113">**웹 앱 / 웹 API를** **예로**설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="3daa2-114">2\.</span><span class="sxs-lookup"><span data-stu-id="3daa2-114">2\.</span></span> <span data-ttu-id="3daa2-115">설정 **암시적 흐름을** 예로 **허용합니다.**</span><span class="sxs-lookup"><span data-stu-id="3daa2-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="3daa2-116">3\.</span><span class="sxs-lookup"><span data-stu-id="3daa2-116">3\.</span></span> <span data-ttu-id="3daa2-117">의 `https://localhost:5001/authentication/login-callback` **회신 URL을** 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="3daa2-118">응용 프로그램 ID(클라이언트 ID)를 `11111111-1111-1111-1111-111111111111`기록합니다(예: ).</span><span class="sxs-lookup"><span data-stu-id="3daa2-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="3daa2-119">[사용자 흐름](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; 만들기 등록 및 로그인 사용자 흐름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="3daa2-120">최소한 `LoginDisplay` 응용 프로그램 **클레임** > **표시 이름** 사용자 속성을 `context.User.Identity.Name` 선택하여 구성 요소(공유/LoginDisplay.razor)에 채워집니다.*Shared/LoginDisplay.razor*</span><span class="sxs-lookup"><span data-stu-id="3daa2-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="3daa2-121">앱에 대해 생성된 등록 및 로그인 사용자 흐름 이름을 기록합니다(예: `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="3daa2-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="3daa2-122">다음 명령의 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="3daa2-123">프로젝트 폴더가 없는 경우 만드는 출력 위치를 지정하려면 경로가 있는 명령에 출력 옵션을 포함합니다(예: `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="3daa2-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="3daa2-124">폴더 이름도 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="3daa2-125">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="3daa2-125">Authentication package</span></span>

<span data-ttu-id="3daa2-126">개별 B2C 계정()을`IndividualB2C`사용하도록 앱을 만들면 앱은 Microsoft 인증 [라이브러리()에](/azure/active-directory/develop/msal-overview) `Microsoft.Authentication.WebAssembly.Msal`대한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="3daa2-127">이 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 토큰을 얻는 데 도움이 되는 기본 요소 집합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="3daa2-128">앱에 인증을 추가하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="3daa2-129">앞의 패키지 참조에서 문서에 표시된 `{VERSION}` `Microsoft.AspNetCore.Blazor.Templates` 패키지 버전으로 <xref:blazor/get-started> 바꿉습니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="3daa2-130">패키지는 `Microsoft.Authentication.WebAssembly.Msal` 앱에 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 전이적으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="3daa2-131">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="3daa2-131">Authentication service support</span></span>

<span data-ttu-id="3daa2-132">사용자 인증에 대한 지원은 패키지에서 제공하는 `AddMsalAuthentication` 확장 방법으로 `Microsoft.Authentication.WebAssembly.Msal` 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="3daa2-133">이 메서드는 앱이 IP(ID 공급자)와 상호 작용하는 데 필요한 모든 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="3daa2-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3daa2-134">*Program.cs*:</span></span>

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

<span data-ttu-id="3daa2-135">메서드는 `AddMsalAuthentication` 응용 프로그램을 인증 하는 데 필요한 매개 변수를 구성 하는 콜백을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="3daa2-136">앱을 구성하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="3daa2-137">토큰 범위에 액세스</span><span class="sxs-lookup"><span data-stu-id="3daa2-137">Access token scopes</span></span>

<span data-ttu-id="3daa2-138">WebAssembly 템플릿은 Blazor 보안 API에 대한 액세스 토큰을 요청하도록 앱을 자동으로 구성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="3daa2-139">로그인 흐름의 일부로 토큰을 프로비전하려면 `MsalProviderOptions`다음의 기본 액세스 토큰 범위에 범위를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-139">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="3daa2-140">Azure 포털이 범위 URI를 제공하고 API에서 *401 무단* 응답을 받을 때 **앱이 처리되지 않은 예외를 throw하는** 경우 구성표 및 호스트를 포함하지 않는 범위 URI를 사용해 보십시오.</span><span class="sxs-lookup"><span data-stu-id="3daa2-140">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="3daa2-141">예를 들어 Azure 포털은 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-141">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="3daa2-142">스키마 및 호스트 없이 범위 URI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3daa2-142">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="3daa2-143">자세한 내용은 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3daa2-143">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="3daa2-144">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="3daa2-144">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="3daa2-145">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="3daa2-145">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="3daa2-146">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3daa2-146">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="3daa2-147">리디렉션토로그인 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3daa2-147">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="3daa2-148">로그인표시 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3daa2-148">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="3daa2-149">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3daa2-149">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="3daa2-150">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="3daa2-150">Additional resources</span></span>

* [<span data-ttu-id="3daa2-151">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="3daa2-151">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="3daa2-152">자습서: Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="3daa2-152">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="3daa2-153">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="3daa2-153">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
