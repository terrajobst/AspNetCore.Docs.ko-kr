---
title: Azure active Blazor Directory B2C를 통해 ASP.NET 코어 웹어셈블리 호스팅 앱 보안
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 4c79f7530e18b9f70262812a64abb55122701d15
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977160"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="49231-102">Azure active Blazor Directory B2C를 통해 ASP.NET 코어 웹어셈블리 호스팅 앱 보안</span><span class="sxs-lookup"><span data-stu-id="49231-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="49231-103">[하비에르 칼바로 넬슨과](https://github.com/javiercn) [루크 라담](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="49231-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="49231-104">이 문서에서는 인증을 위해 Blazor [AAD(Azure Active Directory) B2C를](/azure/active-directory-b2c/overview) 사용하는 웹 Assembly 독립 실행형 앱을 만드는 방법에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="49231-105">AAD B2C에 앱을 등록하고 솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="49231-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="49231-106">테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="49231-106">Create a tenant</span></span>

<span data-ttu-id="49231-107">[자습서의 지침: Azure Active Directory B2C 테넌트를 만들어](/azure/active-directory-b2c/tutorial-create-tenant) AAD B2C 테넌트를 만들고 다음 정보를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="49231-108">AAD B2C 인스턴스(예: `https://contoso.b2clogin.com/`후행 슬래시포함)</span><span class="sxs-lookup"><span data-stu-id="49231-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="49231-109">AAD B2C 테넌트 `contoso.onmicrosoft.com`도메인(예: )</span><span class="sxs-lookup"><span data-stu-id="49231-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="49231-110">서버 API 앱 등록</span><span class="sxs-lookup"><span data-stu-id="49231-110">Register a server API app</span></span>

<span data-ttu-id="49231-111">[자습서의 지침: Azure Active Directory B2C에 응용 프로그램을 등록하여](/azure/active-directory-b2c/tutorial-register-applications) Azure **Active Directory** > **앱 등록** 영역에서 서버 API *앱에* 대한 AAD 앱을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="49231-112">**새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-112">Select **New registration**.</span></span>
1. <span data-ttu-id="49231-113">앱의 **이름을** 제공합니다(예: \*\* Blazor 서버 AAD B2C).\*\*</span><span class="sxs-lookup"><span data-stu-id="49231-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="49231-114">**지원되는 계정 유형의**경우 **모든 조직 디렉터리 또는 ID 공급자에서 계정을 선택합니다. Azure AD B2C를 통해 사용자를 인증하는 데 적합합니다.**</span><span class="sxs-lookup"><span data-stu-id="49231-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="49231-115">(다중 테넌트).</span><span class="sxs-lookup"><span data-stu-id="49231-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="49231-116">이 시나리오에서는 *Server API 앱에* **리디렉션 URI가** 필요하지 않으므로 드롭다운을 **웹으로** 설정하고 리디렉션 URI를 입력하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49231-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="49231-117">사용 **권한** > **부여 관리자에게 openid 및 offline_access 사용 권한이** 활성화되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="49231-118">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-118">Select **Register**.</span></span>

<span data-ttu-id="49231-119">**API 노출시**:</span><span class="sxs-lookup"><span data-stu-id="49231-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="49231-120">**범위 추가를**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="49231-121">**저장 및 계속**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="49231-122">범위 **이름(예:** )을 `API.Access`제공합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="49231-123">관리자 **동의 표시** 이름(예: `Access API`)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="49231-124">관리자 **동의** 설명(예: `Allows the app to access server app API endpoints.`)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="49231-125">**상태가** **사용 설정으로**설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="49231-126">**범위 추가를**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-126">Select **Add scope**.</span></span>

<span data-ttu-id="49231-127">다음 정보를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-127">Record the following information:</span></span>

* <span data-ttu-id="49231-128">*서버 API 앱* 응용 프로그램 ID(클라이언트 ID) `11111111-1111-1111-1111-111111111111`(예: )</span><span class="sxs-lookup"><span data-stu-id="49231-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="49231-129">앱 ID `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`URI(예: `api://11111111-1111-1111-1111-111111111111`, 또는 제공한 사용자 지정 값)</span><span class="sxs-lookup"><span data-stu-id="49231-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="49231-130">디렉터리 ID(예: `222222222-2222-2222-2222-222222222222`테넌트 ID)</span><span class="sxs-lookup"><span data-stu-id="49231-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="49231-131">*서버 API 앱* 앱 ID URI(예: `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`Azure 포털이 클라이언트 ID로 값을 기본값으로 설정)</span><span class="sxs-lookup"><span data-stu-id="49231-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="49231-132">기본 범위(예: `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="49231-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="49231-133">클라이언트 앱 등록</span><span class="sxs-lookup"><span data-stu-id="49231-133">Register a client app</span></span>

<span data-ttu-id="49231-134">[자습서의 지침: Azure Active Directory B2C에 응용 프로그램을 다시 등록하여](/azure/active-directory-b2c/tutorial-register-applications) Azure Active **Directory** > **앱 등록** 영역에서 클라이언트 *앱에* 대한 AAD 앱을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="49231-135">**새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-135">Select **New registration**.</span></span>
1. <span data-ttu-id="49231-136">앱의 **이름을** 제공합니다(예: \*\* Blazor 클라이언트 AAD B2C).\*\*</span><span class="sxs-lookup"><span data-stu-id="49231-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="49231-137">**지원되는 계정 유형의**경우 **모든 조직 디렉터리 또는 ID 공급자에서 계정을 선택합니다. Azure AD B2C를 통해 사용자를 인증하는 데 적합합니다.**</span><span class="sxs-lookup"><span data-stu-id="49231-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="49231-138">(다중 테넌트).</span><span class="sxs-lookup"><span data-stu-id="49231-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="49231-139">리디렉션 **URI** 드롭다운을 **웹으로**설정하고 의 `https://localhost:5001/authentication/login-callback`리디렉션 URI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="49231-140">사용 **권한** > **부여 관리자에게 openid 및 offline_access 사용 권한이** 활성화되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="49231-141">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-141">Select **Register**.</span></span>

<span data-ttu-id="49231-142">**인증** > **플랫폼 구성** > **웹에서**:</span><span class="sxs-lookup"><span data-stu-id="49231-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="49231-143">`https://localhost:5001/authentication/login-callback` 리디렉션 **URI가** 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="49231-144">**암시적 부여의**경우 Access **토큰** 및 ID 토큰에 대한 **확인란을 선택합니다.**</span><span class="sxs-lookup"><span data-stu-id="49231-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="49231-145">앱의 나머지 기본값은 이 경험에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49231-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="49231-146">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-146">Select the **Save** button.</span></span>

<span data-ttu-id="49231-147">**API 권한:**</span><span class="sxs-lookup"><span data-stu-id="49231-147">In **API permissions**:</span></span>

1. <span data-ttu-id="49231-148">앱에 Microsoft **그래프** > **User.Read** 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="49231-149">**권한 추가** 다음에 **내 API를**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="49231-150">**이름** 열에서 *서버 API 앱을* 선택합니다(예: \*\* Blazor 서버 AAD B2C).\*\*</span><span class="sxs-lookup"><span data-stu-id="49231-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="49231-151">**API** 목록을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="49231-151">Open the **API** list.</span></span>
1. <span data-ttu-id="49231-152">API에 대한 액세스를 활성화합니다(예: `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="49231-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="49231-153">**권한 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="49231-154">**{테넌트 이름}** 단추에 대한 권한 부여 관리자 콘텐츠를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="49231-155">**예**를 선택하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="49231-156">**홈** > **Azure AD B2C** > **사용자 흐름:**</span><span class="sxs-lookup"><span data-stu-id="49231-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="49231-157">가입 및 로그인 사용자 흐름 만들기</span><span class="sxs-lookup"><span data-stu-id="49231-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="49231-158">최소한 `LoginDisplay` 응용 프로그램 **클레임** > **표시 이름** 사용자 속성을 `context.User.Identity.Name` 선택하여 구성 요소(공유/LoginDisplay.razor)에 채워집니다.*Shared/LoginDisplay.razor*</span><span class="sxs-lookup"><span data-stu-id="49231-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="49231-159">다음 정보를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-159">Record the following information:</span></span>

* <span data-ttu-id="49231-160">클라이언트 *앱* 응용 프로그램 ID(예: `33333333-3333-3333-3333-333333333333`클라이언트 ID)를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="49231-161">앱에 대해 생성된 등록 및 로그인 사용자 흐름 이름을 기록합니다(예: `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="49231-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="49231-162">앱 만들기</span><span class="sxs-lookup"><span data-stu-id="49231-162">Create the app</span></span>

<span data-ttu-id="49231-163">다음 명령의 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="49231-164">프로젝트 폴더가 없는 경우 만드는 출력 위치를 지정하려면 경로가 있는 명령에 출력 옵션을 포함합니다(예: `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="49231-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="49231-165">폴더 이름도 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49231-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="49231-166">앱 ID URI를 `app-id-uri` 옵션에 전달하지만 [Access 토큰 범위](#access-token-scopes) 섹션에 설명된 클라이언트 앱에서 구성 변경이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49231-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="49231-167">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="49231-167">Server app configuration</span></span>

<span data-ttu-id="49231-168">*이 섹션에서는 솔루션의 **서버** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="49231-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="49231-169">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="49231-169">Authentication package</span></span>

<span data-ttu-id="49231-170">ASP.NET 코어 웹 API에 대한 호출을 인증하고 권한을 부여하기 위한 지원은 `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`다음에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="49231-171">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="49231-171">Authentication service support</span></span>

<span data-ttu-id="49231-172">메서드는 `AddAuthentication` 앱 내에서 인증 서비스를 설정하고 JWT 베어러 처리기를 기본 인증 방법으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="49231-173">메서드는 `AddAzureADB2CBearer` Azure Active Directory B2C에서 내보낸 토큰의 유효성을 검사하는 데 필요한 JWT 베어러 처리기의 특정 매개 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="49231-174">`UseAuthentication`다음을 수행하십시오. `UseAuthorization`</span><span class="sxs-lookup"><span data-stu-id="49231-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="49231-175">앱은 들어오는 요청에 대해 토큰을 구문 분석하고 유효성을 검사하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="49231-176">적절한 자격 증명 없이 보호된 리소스에 액세스하려는 모든 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="49231-177">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="49231-177">User.Identity.Name</span></span>

<span data-ttu-id="49231-178">기본적으로 는 `User.Identity.Name` 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49231-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="49231-179">`name` 클레임 유형에서 값을 받도록 앱을 구성하려면 [토큰 유효성 검사매개 변수.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 을 구성합니다. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="49231-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="49231-180">앱 설정</span><span class="sxs-lookup"><span data-stu-id="49231-180">App settings</span></span>

<span data-ttu-id="49231-181">*appsettings.json* 파일에는 액세스 토큰의 유효성을 검사하는 데 사용되는 JWT 베어러 처리기를 구성하는 옵션이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49231-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="49231-182">일기 예보 컨트롤러</span><span class="sxs-lookup"><span data-stu-id="49231-182">WeatherForecast controller</span></span>

<span data-ttu-id="49231-183">웨더 예측 컨트롤러(컨트롤러/WeatherForecastController.cs)는 컨트롤러에 적용된 `[Authorize]` 특성으로 보호된 API를*노출합니다.*</span><span class="sxs-lookup"><span data-stu-id="49231-183">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="49231-184">다음을 이해하는 **것이 중요합니다.**</span><span class="sxs-lookup"><span data-stu-id="49231-184">It's **important** to understand that:</span></span>

* <span data-ttu-id="49231-185">이 `[Authorize]` API 컨트롤러의 특성은 이 API를 무단 액세스로부터 보호하는 유일한 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="49231-185">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="49231-186">WebAssembly 앱에 사용되는 특성은 `[Authorize]` 사용자가 앱이 올바르게 작동하도록 승인해야 한다는 힌트일 뿐입니다. Blazor</span><span class="sxs-lookup"><span data-stu-id="49231-186">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="49231-187">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="49231-187">Client app configuration</span></span>

<span data-ttu-id="49231-188">*이 섹션에서는 솔루션의 **클라이언트** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="49231-188">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="49231-189">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="49231-189">Authentication package</span></span>

<span data-ttu-id="49231-190">개별 B2C 계정()을`IndividualB2C`사용하도록 앱을 만들면 앱은 Microsoft 인증 [라이브러리()에](/azure/active-directory/develop/msal-overview) `Microsoft.Authentication.WebAssembly.Msal`대한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="49231-190">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="49231-191">이 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 토큰을 얻는 데 도움이 되는 기본 요소 집합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-191">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="49231-192">앱에 인증을 추가하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-192">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="49231-193">앞의 패키지 참조에서 문서에 표시된 `{VERSION}` `Microsoft.AspNetCore.Blazor.Templates` 패키지 버전으로 <xref:blazor/get-started> 바꿉습니다.</span><span class="sxs-lookup"><span data-stu-id="49231-193">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="49231-194">패키지는 `Microsoft.Authentication.WebAssembly.Msal` 앱에 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 전이적으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-194">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="49231-195">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="49231-195">Authentication service support</span></span>

<span data-ttu-id="49231-196">사용자 인증에 대한 지원은 패키지에서 제공하는 `AddMsalAuthentication` 확장 방법으로 `Microsoft.Authentication.WebAssembly.Msal` 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="49231-196">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="49231-197">이 메서드는 앱이 IP(ID 공급자)와 상호 작용하는 데 필요한 모든 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-197">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="49231-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="49231-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="49231-199">메서드는 `AddMsalAuthentication` 응용 프로그램을 인증 하는 데 필요한 매개 변수를 구성 하는 콜백을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-199">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="49231-200">앱을 구성하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49231-200">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="49231-201">토큰 범위에 액세스</span><span class="sxs-lookup"><span data-stu-id="49231-201">Access token scopes</span></span>

<span data-ttu-id="49231-202">기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위 목록을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="49231-202">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="49231-203">로그인 요청에 기본적으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="49231-203">Included by default in the sign in request.</span></span>
* <span data-ttu-id="49231-204">인증 직후 액세스 토큰을 프로비전하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="49231-204">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="49231-205">모든 범위는 Azure Active Directory 규칙당 동일한 앱에 속해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-205">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="49231-206">필요에 따라 추가 API 앱에 대한 추가 범위를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49231-206">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="49231-207">Azure 포털이 범위 URI를 제공하고 API에서 *401 무단* 응답을 받을 때 **앱이 처리되지 않은 예외를 throw하는** 경우 구성표 및 호스트를 포함하지 않는 범위 URI를 사용해 보십시오.</span><span class="sxs-lookup"><span data-stu-id="49231-207">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="49231-208">예를 들어 Azure 포털은 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49231-208">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="49231-209">스키마 및 호스트 없이 범위 URI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-209">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="49231-210">자세한 내용은 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="49231-210">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="49231-211">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="49231-211">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="49231-212">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="49231-212">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="49231-213">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="49231-213">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="49231-214">리디렉션토로그인 구성 요소</span><span class="sxs-lookup"><span data-stu-id="49231-214">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="49231-215">로그인표시 구성 요소</span><span class="sxs-lookup"><span data-stu-id="49231-215">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="49231-216">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="49231-216">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="49231-217">가져오기데이터 구성 요소</span><span class="sxs-lookup"><span data-stu-id="49231-217">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="49231-218">앱 실행</span><span class="sxs-lookup"><span data-stu-id="49231-218">Run the app</span></span>

<span data-ttu-id="49231-219">서버 프로젝트에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-219">Run the app from the Server project.</span></span> <span data-ttu-id="49231-220">Visual Studio를 사용하는 경우 **솔루션 탐색기에서** 서버 프로젝트를 선택하고 도구 모음에서 **실행** 단추를 선택하거나 **디버그** 메뉴에서 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="49231-220">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->
[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="49231-221">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="49231-221">Additional resources</span></span>

* [<span data-ttu-id="49231-222">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="49231-222">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="49231-223">자습서: Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="49231-223">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="49231-224">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="49231-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
