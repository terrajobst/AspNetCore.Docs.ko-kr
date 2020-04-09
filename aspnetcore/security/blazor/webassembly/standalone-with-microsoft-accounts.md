---
title: Microsoft 계정으로 Blazor ASP.NET 핵심 웹어셈블리 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977082"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="bbd18-102">Microsoft 계정으로 Blazor ASP.NET 핵심 웹어셈블리 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="bbd18-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="bbd18-103">[하비에르 칼바로 넬슨과](https://github.com/javiercn) [루크 라담](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bbd18-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="bbd18-104">인증을 위해 Blazor [AAD(Azure Active Directory)를 사용하여 Microsoft 계정을](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) 사용하는 웹 Assembly 독립 실행형 앱을 만들려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="bbd18-105">AAD 테넌트 및 웹 응용 프로그램 만들기</span><span class="sxs-lookup"><span data-stu-id="bbd18-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="bbd18-106">Azure 포털의 **Azure Active Directory** > 앱 등록 영역에 AAD**앱을 등록합니다.**</span><span class="sxs-lookup"><span data-stu-id="bbd18-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="bbd18-107">1\.</span><span class="sxs-lookup"><span data-stu-id="bbd18-107">1\.</span></span> <span data-ttu-id="bbd18-108">앱의 **이름을** 제공합니다(예: \*\* Blazor 클라이언트 AAD).\*\*</span><span class="sxs-lookup"><span data-stu-id="bbd18-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="bbd18-109">2\.</span><span class="sxs-lookup"><span data-stu-id="bbd18-109">2\.</span></span> <span data-ttu-id="bbd18-110">**지원되는 계정 유형에서** **모든 조직 디렉터리에서 계정을**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="bbd18-111">3\.</span><span class="sxs-lookup"><span data-stu-id="bbd18-111">3\.</span></span> <span data-ttu-id="bbd18-112">리디렉션 **URI** 드롭다운을 **웹으로**설정하고 의 `https://localhost:5001/authentication/login-callback`리디렉션 URI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="bbd18-113">4\.</span><span class="sxs-lookup"><span data-stu-id="bbd18-113">4\.</span></span> <span data-ttu-id="bbd18-114">사용 **권한** > 부여 관리자 를 사용 권한 으로 설정 해제 하 고 offline_access 사용 권한 확인란을 사용 하지 않도록 설정**합니다.**</span><span class="sxs-lookup"><span data-stu-id="bbd18-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="bbd18-115">5\.</span><span class="sxs-lookup"><span data-stu-id="bbd18-115">5\.</span></span> <span data-ttu-id="bbd18-116">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-116">Select **Register**.</span></span>

   <span data-ttu-id="bbd18-117">**인증** > **플랫폼 구성** > **웹에서**:</span><span class="sxs-lookup"><span data-stu-id="bbd18-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="bbd18-118">1\.</span><span class="sxs-lookup"><span data-stu-id="bbd18-118">1\.</span></span> <span data-ttu-id="bbd18-119">`https://localhost:5001/authentication/login-callback` 리디렉션 **URI가** 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="bbd18-120">2\.</span><span class="sxs-lookup"><span data-stu-id="bbd18-120">2\.</span></span> <span data-ttu-id="bbd18-121">**암시적 부여의**경우 Access **토큰** 및 ID 토큰에 대한 **확인란을 선택합니다.**</span><span class="sxs-lookup"><span data-stu-id="bbd18-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="bbd18-122">3\.</span><span class="sxs-lookup"><span data-stu-id="bbd18-122">3\.</span></span> <span data-ttu-id="bbd18-123">앱의 나머지 기본값은 이 경험에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="bbd18-124">4\.</span><span class="sxs-lookup"><span data-stu-id="bbd18-124">4\.</span></span> <span data-ttu-id="bbd18-125">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-125">Select the **Save** button.</span></span>

   <span data-ttu-id="bbd18-126">응용 프로그램 ID(클라이언트 ID)를 `11111111-1111-1111-1111-111111111111`기록합니다(예: ).</span><span class="sxs-lookup"><span data-stu-id="bbd18-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="bbd18-127">다음 명령의 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="bbd18-128">프로젝트 폴더가 없는 경우 만드는 출력 위치를 지정하려면 경로가 있는 명령에 출력 옵션을 포함합니다(예: `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="bbd18-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="bbd18-129">폴더 이름도 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="bbd18-130">앱을 만든 후에는 다음을 수행할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="bbd18-131">Microsoft 계정을 사용하여 앱에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="bbd18-132">앱을 올바르게 구성한 경우 제공되는 독립 실행형 Blazor 앱과 동일한 접근 방식을 사용하여 Microsoft API에 대한 액세스 토큰을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="bbd18-133">자세한 내용은 [빠른 시작: 웹 API를 노출하도록 응용 프로그램 구성을](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="bbd18-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="bbd18-134">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="bbd18-134">Authentication package</span></span>

<span data-ttu-id="bbd18-135">직장 또는 학교 계정()을`SingleOrg`사용하도록 앱을 만들면 앱은 Microsoft [인증 라이브러리()에](/azure/active-directory/develop/msal-overview) `Microsoft.Authentication.WebAssembly.Msal`대한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="bbd18-136">이 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 토큰을 얻는 데 도움이 되는 기본 요소 집합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="bbd18-137">앱에 인증을 추가하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="bbd18-138">앞의 패키지 참조에서 문서에 표시된 `{VERSION}` `Microsoft.AspNetCore.Blazor.Templates` 패키지 버전으로 <xref:blazor/get-started> 바꿉습니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="bbd18-139">패키지는 `Microsoft.Authentication.WebAssembly.Msal` 앱에 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 전이적으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="bbd18-140">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="bbd18-140">Authentication service support</span></span>

<span data-ttu-id="bbd18-141">사용자 인증에 대한 지원은 패키지에서 제공하는 `AddMsalAuthentication` 확장 방법으로 `Microsoft.Authentication.WebAssembly.Msal` 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="bbd18-142">이 메서드는 앱이 IP(ID 공급자)와 상호 작용하는 데 필요한 모든 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="bbd18-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="bbd18-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="bbd18-144">메서드는 `AddMsalAuthentication` 응용 프로그램을 인증 하는 데 필요한 매개 변수를 구성 하는 콜백을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="bbd18-145">앱을 구성하는 데 필요한 값은 앱을 등록할 때 Microsoft 계정 구성에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="bbd18-146">토큰 범위에 액세스</span><span class="sxs-lookup"><span data-stu-id="bbd18-146">Access token scopes</span></span>

<span data-ttu-id="bbd18-147">WebAssembly 템플릿은 Blazor 보안 API에 대한 액세스 토큰을 요청하도록 앱을 자동으로 구성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-147">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="bbd18-148">로그인 흐름의 일부로 토큰을 프로비전하려면 `MsalProviderOptions`다음의 기본 액세스 토큰 범위에 범위를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-148">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="bbd18-149">Azure 포털이 범위 URI를 제공하고 API에서 *401 무단* 응답을 받을 때 **앱이 처리되지 않은 예외를 throw하는** 경우 구성표 및 호스트를 포함하지 않는 범위 URI를 사용해 보십시오.</span><span class="sxs-lookup"><span data-stu-id="bbd18-149">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="bbd18-150">예를 들어 Azure 포털은 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-150">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="bbd18-151">스키마 및 호스트 없이 범위 URI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="bbd18-151">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="bbd18-152">자세한 내용은 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bbd18-152">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="bbd18-153">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="bbd18-153">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="bbd18-154">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="bbd18-154">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="bbd18-155">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="bbd18-155">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="bbd18-156">리디렉션토로그인 구성 요소</span><span class="sxs-lookup"><span data-stu-id="bbd18-156">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="bbd18-157">로그인표시 구성 요소</span><span class="sxs-lookup"><span data-stu-id="bbd18-157">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="bbd18-158">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="bbd18-158">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="bbd18-159">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="bbd18-159">Additional resources</span></span>

* [<span data-ttu-id="bbd18-160">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="bbd18-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="bbd18-161">빠른 시작: Microsoft ID 플랫폼에 응용 프로그램 등록</span><span class="sxs-lookup"><span data-stu-id="bbd18-161">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="bbd18-162">빠른 시작: 웹 API를 노출하도록 응용 프로그램 구성</span><span class="sxs-lookup"><span data-stu-id="bbd18-162">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
