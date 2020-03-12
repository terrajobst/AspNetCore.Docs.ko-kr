---
title: Azure Active Directory를 사용 하 여 ASP.NET Core Blazor Weasembomommbommboman 호스팅된 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 0803e436d66ef7df3c68739e674a8652fde11166
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083664"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="a2691-102">Azure Active Directory를 사용 하 여 ASP.NET Core Blazor Weasembomommbommboman 호스팅된 앱 보호</span><span class="sxs-lookup"><span data-stu-id="a2691-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="a2691-103">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a2691-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



<span data-ttu-id="a2691-104">이 문서에서는 인증을 위해 [AAD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) 를 사용 하는 [Blazor Weasembomhosted 앱](xref:blazor/hosting-models#blazor-webassembly) 을 만드는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="a2691-105">AAD B2C에 앱 등록 및 솔루션 만들기</span><span class="sxs-lookup"><span data-stu-id="a2691-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="a2691-106">테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="a2691-106">Create a tenant</span></span>

<span data-ttu-id="a2691-107">[빠른 시작: 테 넌 트를 설정](/azure/active-directory/develop/quickstart-create-new-tenant) 하 여 AAD에서 테 넌 트 만들기의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="a2691-108">서버 API 앱 등록</span><span class="sxs-lookup"><span data-stu-id="a2691-108">Register a server API app</span></span>

<span data-ttu-id="a2691-109">[빠른 시작: Microsoft id 플랫폼](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD에 응용 프로그램 등록 항목의 지침에 따라 Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 *서버 API 앱* 에 대 한 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="a2691-110">**새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-110">Select **New registration**.</span></span>
1. <span data-ttu-id="a2691-111">앱에 대 한 **이름** (예: **Blazor Server AAD**)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="a2691-112">**지원 되는 계정 유형을**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="a2691-113">이 환경 **에서는이 조직 디렉터리에만 계정** (단일 테 넌 트)을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a2691-114">*서버 API 앱* 은이 시나리오에서 **리디렉션 uri** 를 요구 하지 않으므로 드롭다운을 **웹** 으로 설정 하 고 리디렉션 uri를 입력 하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="a2691-115">**권한을** 사용 하지 않도록 설정 > **관리자 동의 하도록 요구에 게 openid connect 및 offline_access 권한 부여 확인란을** 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a2691-116">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-116">Select **Register**.</span></span>

<span data-ttu-id="a2691-117">**API 권한**에서 사용자 > **Microsoft Graph** 을 제거 **합니다.** 앱에 로그인 또는 Uer 프로필 액세스가 필요 하지 않으므로 읽기 권한이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="a2691-118">**API 노출**:</span><span class="sxs-lookup"><span data-stu-id="a2691-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="a2691-119">**범위 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="a2691-120">**저장 및 계속**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="a2691-121">**범위 이름을** 제공 합니다 (예: `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="a2691-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a2691-122">**관리자 동의 표시 이름** (예: `Access API`)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="a2691-123">**관리자 동의 설명** (예: `Allows the app to access server app API endpoints.`)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="a2691-124">**상태** 가 **사용**으로 설정 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="a2691-125">**범위 추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-125">Select **Add scope**.</span></span>

<span data-ttu-id="a2691-126">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-126">Record the following information:</span></span>

* <span data-ttu-id="a2691-127">*서버 API 앱* 응용 프로그램 ID (클라이언트 ID) (예: `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="a2691-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="a2691-128">디렉터리 ID (테 넌 트 ID) (예: `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="a2691-128">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="a2691-129">AAD 테 넌 트 도메인 (예: `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="a2691-129">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="a2691-130">기본 범위 (예: `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="a2691-130">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="a2691-131">클라이언트 앱 등록</span><span class="sxs-lookup"><span data-stu-id="a2691-131">Register a client app</span></span>

<span data-ttu-id="a2691-132">[빠른 시작: Microsoft id 플랫폼](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD에 응용 프로그램 등록 항목의 지침에 따라 Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 *클라이언트 앱* 에 대 한 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-132">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="a2691-133">**새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-133">Select **New registration**.</span></span>
1. <span data-ttu-id="a2691-134">앱에 대 한 **이름** (예: **Blazor Client AAD**)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-134">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="a2691-135">**지원 되는 계정 유형을**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-135">Choose a **Supported account types**.</span></span> <span data-ttu-id="a2691-136">이 환경 **에서는이 조직 디렉터리에만 계정** (단일 테 넌 트)을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-136">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a2691-137">**리디렉션 uri** 드롭다운을 **웹**으로 설정 된 상태로 두고 `https://localhost:5001/authentication/login-callback`의 리디렉션 uri를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-137">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="a2691-138">**권한을** 사용 하지 않도록 설정 > **관리자 동의 하도록 요구에 게 openid connect 및 offline_access 권한 부여 확인란을** 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-138">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a2691-139">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-139">Select **Register**.</span></span>

<span data-ttu-id="a2691-140">**웹** > **인증** > **플랫폼 구성** 에서 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-140">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a2691-141">`https://localhost:5001/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-141">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a2691-142">**암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-142">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a2691-143">앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-143">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a2691-144">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-144">Select the **Save** button.</span></span>

<span data-ttu-id="a2691-145">**API 사용 권한**:</span><span class="sxs-lookup"><span data-stu-id="a2691-145">In **API permissions**:</span></span>

1. <span data-ttu-id="a2691-146">앱이 > 사용자 **Microsoft Graph** 있는지 확인 **합니다. 읽기** 권한입니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-146">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="a2691-147">**권한 추가를** 선택 하 고 **내 api**를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-147">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="a2691-148">**이름** 열에서 *서버 API 앱* 을 선택 합니다 (예: **Blazor server AAD**).</span><span class="sxs-lookup"><span data-stu-id="a2691-148">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="a2691-149">**API** 목록을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-149">Open the **API** list.</span></span>
1. <span data-ttu-id="a2691-150">API에 대 한 액세스를 사용 하도록 설정 합니다 (예: `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="a2691-150">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a2691-151">**권한 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-151">Select **Add permissions**.</span></span>
1. <span data-ttu-id="a2691-152">**{테 넌 트 이름}에 대 한 관리자 콘텐츠 부여** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-152">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="a2691-153">**예**를 선택하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-153">Select **Yes** to confirm.</span></span>

<span data-ttu-id="a2691-154">*클라이언트 앱* 응용 프로그램 Id (클라이언트 id)를 기록 합니다 (예: `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="a2691-154">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="a2691-155">앱 만들기</span><span class="sxs-lookup"><span data-stu-id="a2691-155">Create the app</span></span>

<span data-ttu-id="a2691-156">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-156">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP CLIENT ID}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="a2691-157">출력 위치를 지정 하려면 프로젝트 폴더를 만드는 경우 (예: `-o BlazorSample`) 경로를 사용 하 여 명령에 출력 옵션을 포함 합니다 (예:).</span><span class="sxs-lookup"><span data-stu-id="a2691-157">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a2691-158">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-158">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="a2691-159">기본 액세스 토큰 범위에 대 한 중요 한 구성 변경은 [인증 서비스 지원](#Authentication service support) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a2691-159">See the [Authentication service support](#Authentication service support) section for an important configuration change to the default access token scope.</span></span> <span data-ttu-id="a2691-160">Blazor Weasembomtemplate에서 제공 하는 값은 *클라이언트 앱* 이 템플릿에서 만들어진 후 수동으로 변경 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-160">The value provided by the Blazor WebAssembly template must be manually changed after the *Client app* is created from the template.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="a2691-161">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="a2691-161">Server app configuration</span></span>

<span data-ttu-id="a2691-162">*이 섹션은 솔루션의 **서버** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="a2691-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a2691-163">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="a2691-163">Authentication package</span></span>

<span data-ttu-id="a2691-164">ASP.NET Core 웹 Api에 대 한 호출을 인증 하 고 권한을 부여 하는 데 대 한 지원은 `Microsoft.AspNetCore.Authentication.AzureAD.UI`제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="a2691-165">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="a2691-165">Authentication service support</span></span>

<span data-ttu-id="a2691-166">`AddAuthentication` 메서드는 앱 내에서 인증 서비스를 설정 하 고 JWT 전달자 처리기를 기본 인증 방법으로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a2691-167">`AddAzureADBearer` 메서드는 Azure Active Directory에서 내보낸 토큰의 유효성을 검사 하는 데 필요한 JWT 전달자 처리기의 특정 매개 변수를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="a2691-168">`UseAuthentication` 하 고 `UseAuthorization` 다음을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="a2691-169">앱에서 들어오는 요청에 대 한 토큰을 구문 분석 하 고 유효성을 검사 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="a2691-170">적절 한 자격 증명 없이 보호 된 리소스에 액세스 하려고 시도 하는 모든 요청은 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="a2691-171">앱 설정</span><span class="sxs-lookup"><span data-stu-id="a2691-171">App settings</span></span>

<span data-ttu-id="a2691-172">*Appsettings* 파일에는 액세스 토큰의 유효성을 검사 하는 데 사용 되는 JWT 전달자 처리기를 구성 하는 옵션이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-172">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="a2691-173">WeatherForecast 컨트롤러</span><span class="sxs-lookup"><span data-stu-id="a2691-173">WeatherForecast controller</span></span>

<span data-ttu-id="a2691-174">WeatherForecast 컨트롤러 (controller */WeatherForecastController*)는 컨트롤러에 적용 된 `[Authorize]` 특성을 사용 하 여 보호 된 API를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-174">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="a2691-175">다음을 이해 하는 것이 **중요** 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-175">It's **important** to understand that:</span></span>

* <span data-ttu-id="a2691-176">이 api 컨트롤러의 `[Authorize]` 특성은 무단 액세스 로부터이 API를 보호 하는 유일한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-176">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="a2691-177">Blazor Weasembomapp에 사용 되는 `[Authorize]` 특성은 사용자가 앱이 올바르게 작동 하도록 권한이 부여 되어야 한다는 것을 앱에 대 한 힌트로만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-177">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="a2691-178">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="a2691-178">Client app configuration</span></span>

<span data-ttu-id="a2691-179">*이 섹션은 솔루션의 **클라이언트** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="a2691-179">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a2691-180">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="a2691-180">Authentication package</span></span>

<span data-ttu-id="a2691-181">회사 또는 학교 계정 (`SingleOrg`)을 사용 하도록 앱을 만들 때 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`)에 대 한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-181">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="a2691-182">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-182">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a2691-183">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-183">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="a2691-184">이전 패키지 참조의 `{VERSION}`를 <xref:blazor/get-started> 문서에 표시 된 `Microsoft.AspNetCore.Blazor.Templates` 패키지의 버전으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-184">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="a2691-185">`Microsoft.Authentication.WebAssembly.Msal` 패키지는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 앱에 전이적으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-185">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="a2691-186">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="a2691-186">Authentication service support</span></span>

<span data-ttu-id="a2691-187">사용자 인증에 대 한 지원은 `Microsoft.Authentication.WebAssembly.Msal` 패키지에서 제공 하는 `AddMsalAuthentication` 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-187">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="a2691-188">이 메서드는 앱이 IP (Id 공급자)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-188">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a2691-189">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2691-189">*Program.cs*:</span></span>

<span data-ttu-id="a2691-190">*클라이언트 앱* 이 생성 될 때 기본 액세스 토큰 범위는 `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`형식입니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-190">When the *Client app* is generated, the default access token scope is of the format `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`.</span></span> <span data-ttu-id="a2691-191">**범위 값의 `api://` 부분을 제거 합니다.**</span><span class="sxs-lookup"><span data-stu-id="a2691-191">**Remove the `api://` portion of the scope value.**</span></span> <span data-ttu-id="a2691-192">이 문제는 이후 미리 보기 릴리스에서 해결 될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-192">This issue will be addressed in a future preview release.</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> <span data-ttu-id="a2691-193">기본 액세스 토큰 범위는 `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` 형식 이어야 합니다 (예: `11111111-1111-1111-1111-111111111111/API.Access`).</span><span class="sxs-lookup"><span data-stu-id="a2691-193">The default access token scope must be in the format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (for example, `11111111-1111-1111-1111-111111111111/API.Access`).</span></span> <span data-ttu-id="a2691-194">Azure Portal에 표시 된 대로 범위 설정에 스키마 또는 스키마 및 호스트를 제공 하는 경우 *클라이언트 앱* 은 *서버 API 앱*에서 *401 권한 없음* 응답을 받을 때 처리 되지 않은 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-194">If a scheme or scheme and host is provided to the scope setting (as shown in the Azure Portal), the *Client app* throws an unhandled exception when it receives a *401 Unauthorized* response from the *Server API app*.</span></span>

<span data-ttu-id="a2691-195">`AddMsalAuthentication` 메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a2691-196">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="a2691-197">기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위의 목록을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-197">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="a2691-198">로그인 요청에 기본적으로 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-198">Included by default in the sign in request.</span></span>
* <span data-ttu-id="a2691-199">인증 후 즉시 액세스 토큰을 프로 비전 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-199">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="a2691-200">모든 범위는 Azure Active Directory 규칙에 따라 동일한 앱에 속해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-200">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="a2691-201">필요에 따라 추가 API 앱에 대 한 추가 범위를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2691-201">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="a2691-202">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="a2691-202">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="a2691-203">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="a2691-203">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a2691-204">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="a2691-204">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a2691-205">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="a2691-205">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="a2691-206">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="a2691-206">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a2691-207">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="a2691-207">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a2691-208">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="a2691-208">Additional resources</span></span>

* <xref:security/authentication/azure-active-directory/index>
