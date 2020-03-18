---
title: Azure Active Directory B2C를 사용 하 여 ASP.NET Core Blazor Weasembomommbommboman 호스팅된 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 12e09cf7e27f85473d84f42564d13e1c0ed5dff1
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434449"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="e74f9-102">Azure Active Directory B2C를 사용 하 여 ASP.NET Core Blazor Weasembomommbommboman 호스팅된 앱 보호</span><span class="sxs-lookup"><span data-stu-id="e74f9-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="e74f9-103">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e74f9-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="e74f9-104">이 문서에서는 인증을 위해 [AAD (Azure Active Directory) B2C](/azure/active-directory-b2c/overview) 를 사용 하는 Blazor Weasembom독립형 앱을 만드는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="e74f9-105">AAD B2C에 앱 등록 및 솔루션 만들기</span><span class="sxs-lookup"><span data-stu-id="e74f9-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="e74f9-106">테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="e74f9-106">Create a tenant</span></span>

<span data-ttu-id="e74f9-107">[자습서: Azure Active Directory B2C 테 넌 트 만들기](/azure/active-directory-b2c/tutorial-create-tenant) 의 지침에 따라 AAD B2C 테 넌 트를 만들고 다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="e74f9-108">AAD B2C 인스턴스 (예: 후행 슬래시를 포함 하는 `https://contoso.b2clogin.com/`)</span><span class="sxs-lookup"><span data-stu-id="e74f9-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="e74f9-109">AAD B2C 테 넌 트 도메인 (예: `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="e74f9-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="e74f9-110">서버 API 앱 등록</span><span class="sxs-lookup"><span data-stu-id="e74f9-110">Register a server API app</span></span>

<span data-ttu-id="e74f9-111">[자습서: Azure Active Directory B2C에 응용 프로그램 등록](/azure/active-directory-b2c/tutorial-register-applications) 의 지침에 따라 Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 *서버 API 앱* 에 대 한 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e74f9-112">**새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-112">Select **New registration**.</span></span>
1. <span data-ttu-id="e74f9-113">앱의 **이름을** 제공 합니다 (예: **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="e74f9-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="e74f9-114">**지원 되는 계정 유형에**대해 **모든 조직 디렉터리 또는 모든 Id 공급자의 계정을 선택 합니다. Azure AD B2C를 사용 하 여 사용자를 인증 합니다.**</span><span class="sxs-lookup"><span data-stu-id="e74f9-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="e74f9-115">(다중 테 넌 트)이 환경에 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="e74f9-116">*서버 API 앱* 은이 시나리오에서 **리디렉션 uri** 를 요구 하지 않으므로 드롭다운을 **웹** 으로 설정 하 고 리디렉션 uri를 입력 하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="e74f9-117">**권한** **부여 > admin 동의 하도록 요구 to openid connect and offline_access** 사용 권한이 설정 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="e74f9-118">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-118">Select **Register**.</span></span>

<span data-ttu-id="e74f9-119">**API 노출**:</span><span class="sxs-lookup"><span data-stu-id="e74f9-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="e74f9-120">**범위 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="e74f9-121">**저장 및 계속**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="e74f9-122">**범위 이름을** 제공 합니다 (예: `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="e74f9-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e74f9-123">**관리자 동의 표시 이름** (예: `Access API`)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="e74f9-124">**관리자 동의 설명** (예: `Allows the app to access server app API endpoints.`)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="e74f9-125">**상태** 가 **사용**으로 설정 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="e74f9-126">**범위 추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-126">Select **Add scope**.</span></span>

<span data-ttu-id="e74f9-127">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-127">Record the following information:</span></span>

* <span data-ttu-id="e74f9-128">*서버 API 앱* 응용 프로그램 ID (클라이언트 ID) (예: `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="e74f9-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="e74f9-129">디렉터리 ID (테 넌 트 ID) (예: `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="e74f9-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="e74f9-130">*서버 API 앱* 앱 ID URI (예: `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, Azure Portal는 클라이언트 ID에 대 한 값을 기본값으로 사용할 수 있음)</span><span class="sxs-lookup"><span data-stu-id="e74f9-130">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="e74f9-131">기본 범위 (예: `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="e74f9-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="e74f9-132">클라이언트 앱 등록</span><span class="sxs-lookup"><span data-stu-id="e74f9-132">Register a client app</span></span>

<span data-ttu-id="e74f9-133">[자습서: Azure Active Directory B2C에 응용 프로그램 등록](/azure/active-directory-b2c/tutorial-register-applications) 의 지침에 따라 Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 *클라이언트 앱* 에 대 한 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-133">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e74f9-134">**새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-134">Select **New registration**.</span></span>
1. <span data-ttu-id="e74f9-135">앱에 대 한 **이름** (예: **클라이언트 AAD B2CBlazor** )을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-135">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="e74f9-136">**지원 되는 계정 유형에**대해 **모든 조직 디렉터리 또는 모든 Id 공급자의 계정을 선택 합니다. Azure AD B2C를 사용 하 여 사용자를 인증 합니다.**</span><span class="sxs-lookup"><span data-stu-id="e74f9-136">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="e74f9-137">(다중 테 넌 트)이 환경에 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-137">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="e74f9-138">**리디렉션 uri** 드롭다운을 **웹**으로 설정 된 상태로 두고 `https://localhost:5001/authentication/login-callback`의 리디렉션 uri를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="e74f9-139">**권한** **부여 > admin 동의 하도록 요구 to openid connect and offline_access** 사용 권한이 설정 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-139">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="e74f9-140">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-140">Select **Register**.</span></span>

<span data-ttu-id="e74f9-141">**웹** > **인증** > **플랫폼 구성** 에서 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="e74f9-142">`https://localhost:5001/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e74f9-143">**암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e74f9-144">앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e74f9-145">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-145">Select the **Save** button.</span></span>

<span data-ttu-id="e74f9-146">**API 사용 권한**:</span><span class="sxs-lookup"><span data-stu-id="e74f9-146">In **API permissions**:</span></span>

1. <span data-ttu-id="e74f9-147">앱이 > 사용자 **Microsoft Graph** 있는지 확인 **합니다. 읽기** 권한입니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="e74f9-148">**권한 추가를** 선택 하 고 **내 api**를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="e74f9-149">**이름** 열에서 *서버 API 앱* 을 선택 합니다 (예: **Blazor server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="e74f9-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="e74f9-150">**API** 목록을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-150">Open the **API** list.</span></span>
1. <span data-ttu-id="e74f9-151">API에 대 한 액세스를 사용 하도록 설정 합니다 (예: `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="e74f9-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e74f9-152">**권한 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="e74f9-153">**{테 넌 트 이름}에 대 한 관리자 콘텐츠 부여** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="e74f9-154">**예**를 선택하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="e74f9-155">**홈** > **Azure AD B2C** > **사용자 흐름**:</span><span class="sxs-lookup"><span data-stu-id="e74f9-155">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="e74f9-156">등록 및 로그인 사용자 흐름 만들기</span><span class="sxs-lookup"><span data-stu-id="e74f9-156">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="e74f9-157">최소한 **응용 프로그램 클레임** > **표시 이름** 사용자 특성을 선택 하 여 `LoginDisplay` 구성 요소 (*Shared/LoginDisplay*)에 `context.User.Identity.Name`를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-157">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="e74f9-158">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-158">Record the following information:</span></span>

* <span data-ttu-id="e74f9-159">*클라이언트 앱* 응용 프로그램 Id (클라이언트 id)를 기록 합니다 (예: `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="e74f9-159">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="e74f9-160">앱에 대해 만든 등록 및 로그인 사용자 흐름 이름을 기록 합니다 (예: `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="e74f9-160">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="e74f9-161">앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e74f9-161">Create the app</span></span>

<span data-ttu-id="e74f9-162">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-162">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="e74f9-163">출력 위치를 지정 하려면 프로젝트 폴더를 만드는 경우 (예: `-o BlazorSample`) 경로를 사용 하 여 명령에 출력 옵션을 포함 합니다 (예:).</span><span class="sxs-lookup"><span data-stu-id="e74f9-163">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="e74f9-164">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-164">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="e74f9-165">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="e74f9-165">Server app configuration</span></span>

<span data-ttu-id="e74f9-166">*이 섹션은 솔루션의 **서버** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="e74f9-166">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e74f9-167">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="e74f9-167">Authentication package</span></span>

<span data-ttu-id="e74f9-168">ASP.NET Core 웹 Api에 대 한 호출을 인증 하 고 권한을 부여 하는 데 대 한 지원은 `Microsoft.AspNetCore.Authentication.AzureAD.UI`제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-168">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="e74f9-169">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="e74f9-169">Authentication service support</span></span>

<span data-ttu-id="e74f9-170">`AddAuthentication` 메서드는 앱 내에서 인증 서비스를 설정 하 고 JWT 전달자 처리기를 기본 인증 방법으로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-170">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="e74f9-171">`AddAzureADBearer` 메서드는 Azure Active Directory에서 내보낸 토큰의 유효성을 검사 하는 데 필요한 JWT 전달자 처리기의 특정 매개 변수를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-171">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="e74f9-172">`UseAuthentication` 하 고 `UseAuthorization` 다음을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-172">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="e74f9-173">앱에서 들어오는 요청에 대 한 토큰을 구문 분석 하 고 유효성을 검사 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-173">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="e74f9-174">적절 한 자격 증명 없이 보호 된 리소스에 액세스 하려고 시도 하는 모든 요청은 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-174">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="e74f9-175">앱 설정</span><span class="sxs-lookup"><span data-stu-id="e74f9-175">App settings</span></span>

<span data-ttu-id="e74f9-176">*Appsettings* 파일에는 액세스 토큰의 유효성을 검사 하는 데 사용 되는 JWT 전달자 처리기를 구성 하는 옵션이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-176">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="e74f9-177">WeatherForecast 컨트롤러</span><span class="sxs-lookup"><span data-stu-id="e74f9-177">WeatherForecast controller</span></span>

<span data-ttu-id="e74f9-178">WeatherForecast 컨트롤러 (controller */WeatherForecastController*)는 컨트롤러에 적용 된 `[Authorize]` 특성을 사용 하 여 보호 된 API를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="e74f9-179">다음을 이해 하는 것이 **중요** 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="e74f9-180">이 api 컨트롤러의 `[Authorize]` 특성은 무단 액세스 로부터이 API를 보호 하는 유일한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="e74f9-181">Blazor Weasembomapp에 사용 되는 `[Authorize]` 특성은 사용자가 앱이 올바르게 작동 하도록 권한이 부여 되어야 한다는 것을 앱에 대 한 힌트로만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="e74f9-182">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="e74f9-182">Client app configuration</span></span>

<span data-ttu-id="e74f9-183">*이 섹션은 솔루션의 **클라이언트** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="e74f9-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e74f9-184">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="e74f9-184">Authentication package</span></span>

<span data-ttu-id="e74f9-185">`IndividualB2C`(개별 B2C 계정)를 사용 하도록 앱을 만들면 앱은`Microsoft.Authentication.WebAssembly.Msal`( [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) )에 대 한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-185">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="e74f9-186">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e74f9-187">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="e74f9-188">이전 패키지 참조의 `{VERSION}`를 <xref:blazor/get-started> 문서에 표시 된 `Microsoft.AspNetCore.Blazor.Templates` 패키지의 버전으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="e74f9-189">`Microsoft.Authentication.WebAssembly.Msal` 패키지는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 앱에 전이적으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="e74f9-190">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="e74f9-190">Authentication service support</span></span>

<span data-ttu-id="e74f9-191">사용자 인증에 대 한 지원은 `Microsoft.Authentication.WebAssembly.Msal` 패키지에서 제공 하는 `AddMsalAuthentication` 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-191">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="e74f9-192">이 메서드는 앱이 IP (Id 공급자)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-192">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e74f9-193">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e74f9-193">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{DEFAULT SCOPE}");
});
```

<span data-ttu-id="e74f9-194">`AddMsalAuthentication` 메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-194">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e74f9-195">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-195">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="e74f9-196">Blazor WebAssembly 템플릿은 `dotnet new` 명령 (`{APP ID URI}/{DEFAULT SCOPE}`)에 제공 된 기본 범위에 대해 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-196">The Blazor WebAssembly template automatically configures the app to request an access token for a secure API for the default scope provided to the `dotnet new` command (`{APP ID URI}/{DEFAULT SCOPE}`).</span></span>

<span data-ttu-id="e74f9-197">기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위의 목록을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-197">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="e74f9-198">로그인 요청에 기본적으로 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-198">Included by default in the sign in request.</span></span>
* <span data-ttu-id="e74f9-199">인증 후 즉시 액세스 토큰을 프로 비전 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-199">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="e74f9-200">모든 범위는 Azure Active Directory 규칙에 따라 동일한 앱에 속해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-200">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="e74f9-201">필요에 따라 추가 API 앱에 대 한 추가 범위를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-201">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="e74f9-202">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="e74f9-202">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="e74f9-203">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="e74f9-203">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="e74f9-204">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="e74f9-204">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="e74f9-205">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="e74f9-205">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="e74f9-206">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="e74f9-206">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="e74f9-207">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="e74f9-207">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="e74f9-208">앱 실행</span><span class="sxs-lookup"><span data-stu-id="e74f9-208">Run the app</span></span>

<span data-ttu-id="e74f9-209">서버 프로젝트에서 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-209">Run the app from the Server project.</span></span> <span data-ttu-id="e74f9-210">Visual Studio를 사용 하는 경우 **솔루션 탐색기** 에서 서버 프로젝트를 선택 하 고 도구 모음에서 **실행** 단추를 선택 하거나 **디버그** 메뉴에서 앱을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="e74f9-210">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e74f9-211">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="e74f9-211">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="e74f9-212">자습서: Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="e74f9-212">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
