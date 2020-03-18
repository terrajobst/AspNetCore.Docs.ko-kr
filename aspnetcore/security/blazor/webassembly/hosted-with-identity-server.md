---
title: Id 서버를 사용 하 여 ASP.NET Core Blazor Weasembomommbommboman 호스팅된 앱 보호
author: guardrex
description: '[IdentityServer](https://identityserver.io/) 백 엔드를 사용 하는 Visual Studio 내에서 인증을 사용 하 여 새 Blazor 호스팅된 앱을 만들려면'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: a3993bf635e5a7aae408d72796015f2414e13c14
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434475"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="13ff6-103">Id 서버를 사용 하 여 ASP.NET Core Blazor Weasembomommbommboman 호스팅된 앱 보호</span><span class="sxs-lookup"><span data-stu-id="13ff6-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="13ff6-104">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="13ff6-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="13ff6-105">Visual Studio에서 [IdentityServer](https://identityserver.io/) 를 사용 하 여 사용자 및 API 호출을 인증 하는 새 Blazor 호스팅된 앱을 만들려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="13ff6-106">Visual Studio를 사용 하 여 새 **Blazor Weasembomapp** 을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="13ff6-107">자세한 내용은 <xref:blazor/get-started>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="13ff6-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="13ff6-108">**새 Blazor 앱 만들기** 대화 상자의 **인증** 섹션에서 **변경** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="13ff6-109">**개별 사용자 계정** , **확인을**차례로 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="13ff6-110">**고급** 섹션에서 **호스팅된 ASP.NET Core** 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="13ff6-111">**만들기** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-111">Select the **Create** button.</span></span>

<span data-ttu-id="13ff6-112">명령 셸에서 앱을 만들려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="13ff6-113">출력 위치를 지정 하려면 프로젝트 폴더를 만드는 경우 (예: `-o BlazorSample`) 경로를 사용 하 여 명령에 출력 옵션을 포함 합니다 (예:).</span><span class="sxs-lookup"><span data-stu-id="13ff6-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="13ff6-114">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="13ff6-115">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="13ff6-115">Server app configuration</span></span>

<span data-ttu-id="13ff6-116">다음 섹션에서는 인증 지원을 포함 하는 경우 프로젝트에 대 한 추가 설명입니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="13ff6-117">시작 클래스</span><span class="sxs-lookup"><span data-stu-id="13ff6-117">Startup class</span></span>

<span data-ttu-id="13ff6-118">`Startup` 클래스에는 다음과 같은 추가 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="13ff6-119">`Startup.ConfigureServices`의 경우:</span><span class="sxs-lookup"><span data-stu-id="13ff6-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="13ff6-120">기본 UI를 사용 하는 id:</span><span class="sxs-lookup"><span data-stu-id="13ff6-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="13ff6-121">IdentityServer IdentityServer 위에 몇 가지 기본 ASP.NET Core 규칙을 설정 하는 추가 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 도우미 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="13ff6-122">IdentityServer에서 생성 한 JWT 토큰의 유효성을 검사 하도록 앱을 구성 하는 추가 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 도우미 메서드를 사용 하 여 인증 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="13ff6-123">`Startup.Configure`의 경우:</span><span class="sxs-lookup"><span data-stu-id="13ff6-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="13ff6-124">요청 자격 증명의 유효성을 검사 하 고 요청 컨텍스트에서 사용자를 설정 해야 하는 인증 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="13ff6-125">OIDC (Open ID Connect) 끝점을 노출 하는 IdentityServer 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="13ff6-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="13ff6-126">AddApiAuthorization</span></span>

<span data-ttu-id="13ff6-127"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 도우미 메서드는 ASP.NET Core 시나리오에 대해 [IdentityServer](https://identityserver.io/) 를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="13ff6-128">IdentityServer는 앱 보안 문제를 처리 하기 위한 강력 하 고 확장 가능한 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="13ff6-129">IdentityServer는 가장 일반적인 시나리오에 대 한 불필요 한 복잡성을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="13ff6-130">따라서 유용한 시작 지점을 고려 하는 규칙 집합과 구성 옵션이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="13ff6-131">인증을 변경 해야 하는 경우 앱의 요구 사항에 맞게 인증을 사용자 지정 하기 위해 IdentityServer의 모든 기능을 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="13ff6-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="13ff6-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="13ff6-133"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 도우미 메서드는 앱에 대 한 정책 스키마를 기본 인증 처리기로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="13ff6-134">이 정책은 id URL 공간 `/Identity`의 하위 경로에 라우팅되는 모든 요청을 Id가 처리할 수 있도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="13ff6-135"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>는 다른 모든 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="13ff6-136">또한이 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-136">Additionally, this method:</span></span>

* <span data-ttu-id="13ff6-137">IdentityServer를 사용 하 여 `{APPLICATION NAME}API` API 리소스를 등록 하 고 기본 범위 `{APPLICATION NAME}API`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="13ff6-138">앱에 대해 IdentityServer에서 발급 한 토큰의 유효성을 검사 하도록 JWT 전달자 토큰 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="13ff6-139">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="13ff6-139">WeatherForecastController</span></span>

<span data-ttu-id="13ff6-140">`WeatherForecastController` (*controller/WeatherForecastController*)에서 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 클래스에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="13ff6-141">특성은 리소스에 액세스 하기 위한 기본 정책에 따라 사용자에 게 권한이 부여 되어야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="13ff6-142">기본 권한 부여 정책은 앞에서 언급 한 정책 체계에 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 하 여 설정 되는 기본 인증 체계를 사용 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="13ff6-143">도우미 메서드는 앱에 대 한 요청에 대 한 기본 처리기로 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="13ff6-144">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="13ff6-144">ApplicationDbContext</span></span>

<span data-ttu-id="13ff6-145">`ApplicationDbContext` (*Data/ApplicationDbContext*)에서 IdentityServer에 대 한 스키마를 포함 하도록 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>를 확장 한다는 것을 제외 하 고 id에서 동일한 <xref:Microsoft.EntityFrameworkCore.DbContext> 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="13ff6-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="13ff6-147">데이터베이스 스키마에 대 한 모든 권한을 얻으려면 사용 가능한 Id <xref:Microsoft.EntityFrameworkCore.DbContext> 클래스 중 하나에서 상속 하 고 `OnModelCreating` 메서드에서 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`를 호출 하 여 Id 스키마를 포함 하도록 컨텍스트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="13ff6-148">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="13ff6-148">OidcConfigurationController</span></span>

<span data-ttu-id="13ff6-149">`OidcConfigurationController` (*controller/OidcConfigurationController*)에서 클라이언트 끝점은 oidc 매개 변수를 제공 하도록 프로 비전 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="13ff6-150">앱 설정 파일</span><span class="sxs-lookup"><span data-stu-id="13ff6-150">App settings files</span></span>

<span data-ttu-id="13ff6-151">프로젝트 루트의 앱 설정 파일 (*appsettings*)에서 `IdentityServer` 섹션은 구성 된 클라이언트 목록에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="13ff6-152">다음 예제에는 단일 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-152">In the following example, there's a single client.</span></span> <span data-ttu-id="13ff6-153">클라이언트 이름은 앱 이름에 해당 하며 규칙에 따라 OAuth `ClientId` 매개 변수에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="13ff6-154">프로필은 구성 중인 앱 유형을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="13ff6-155">프로필은 서버에 대 한 구성 프로세스를 간소화 하는 규칙을 구동 하기 위해 내부적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="13ff6-156">개발 환경 앱 설정 파일 (*appsettings. Development. json*) `IdentityServer` 섹션에서는 토큰에 서명 하는 데 사용 되는 키에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="13ff6-157">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="13ff6-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="13ff6-158">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="13ff6-158">Authentication package</span></span>

<span data-ttu-id="13ff6-159">개별 사용자 계정 (`Individual`)을 사용 하도록 앱을 만들면 앱은 앱의 프로젝트 파일에서 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지에 대 한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="13ff6-160">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="13ff6-161">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="13ff6-162">이전 패키지 참조의 `{VERSION}`를 <xref:blazor/get-started> 문서에 표시 된 `Microsoft.AspNetCore.Blazor.Templates` 패키지의 버전으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="13ff6-163">API 권한 부여 지원</span><span class="sxs-lookup"><span data-stu-id="13ff6-163">API authorization support</span></span>

<span data-ttu-id="13ff6-164">사용자 인증에 대 한 지원은 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지 내에서 제공 된 확장 메서드에 의해 서비스 컨테이너에 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="13ff6-165">이 메서드는 앱이 기존 인증 시스템과 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="13ff6-166">기본적으로 `_configuration/{client-id}`에서 규칙에 따라 앱에 대 한 구성을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="13ff6-167">규칙에 따라 클라이언트 ID는 앱의 어셈블리 이름으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="13ff6-168">옵션으로 오버 로드를 호출 하 여 별도의 끝점을 가리키도록이 URL을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="index-page"></a><span data-ttu-id="13ff6-169">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="13ff6-169">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="13ff6-170">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="13ff6-170">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="13ff6-171">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="13ff6-171">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="13ff6-172">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="13ff6-172">LoginDisplay component</span></span>

<span data-ttu-id="13ff6-173">`LoginDisplay` 구성 요소 (*shared/LoginDisplay*)는 `MainLayout` 구성 요소 (*Shared/mainlayout. razor*)에서 렌더링 되며 다음 동작을 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-173">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="13ff6-174">인증 된 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="13ff6-174">For authenticated users:</span></span>
  * <span data-ttu-id="13ff6-175">현재 사용자 이름을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-175">Displays the current user name.</span></span>
  * <span data-ttu-id="13ff6-176">ASP.NET Core Id의 사용자 프로필 페이지에 대 한 링크를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-176">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="13ff6-177">앱에서 로그 아웃할 수 있는 단추를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-177">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="13ff6-178">익명 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="13ff6-178">For anonymous users:</span></span>
  * <span data-ttu-id="13ff6-179">등록할 수 있는 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-179">Offers the option to register.</span></span>
  * <span data-ttu-id="13ff6-180">로그인 할 수 있는 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-180">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="13ff6-181">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="13ff6-181">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="13ff6-182">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="13ff6-182">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="13ff6-183">앱 실행</span><span class="sxs-lookup"><span data-stu-id="13ff6-183">Run the app</span></span>

<span data-ttu-id="13ff6-184">서버 프로젝트에서 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-184">Run the app from the Server project.</span></span> <span data-ttu-id="13ff6-185">Visual Studio를 사용 하는 경우 **솔루션 탐색기** 에서 서버 프로젝트를 선택 하 고 도구 모음에서 **실행** 단추를 선택 하거나 **디버그** 메뉴에서 앱을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="13ff6-185">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
