---
title: ID 서버를 Blazor 통해 ASP.NET 코어 웹어셈블리 호스팅 앱 보안
author: guardrex
description: '[IdentityServer](https://identityserver.io/) 백 Blazor 엔드를 사용하는 Visual Studio 내에서 인증을 사용하여 새 호스팅 앱을 만들려면'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501277"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="6577b-103">ID 서버를 Blazor 통해 ASP.NET 코어 웹어셈블리 호스팅 앱 보안</span><span class="sxs-lookup"><span data-stu-id="6577b-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="6577b-104">[하비에르 칼바로 넬슨과](https://github.com/javiercn) [루크 라담](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6577b-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="6577b-105">[IdServer를](https://identityserver.io/) Blazor 사용하여 사용자 및 API 호출을 인증하는 Visual Studio에서 새 호스팅 앱을 만들려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="6577b-106">Visual Studio를 사용하여 \*\* Blazor \*\* 새 웹 Assembly 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="6577b-107">자세한 내용은 <xref:blazor/get-started>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6577b-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="6577b-108">**새 Blazor 앱 만들기** 대화 상자에서 **인증** 섹션에서 **변경을** 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="6577b-109">**개인 사용자 계정을** 선택한 다음 **확인을**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="6577b-110">**고급** 섹션에서 **ASP.NET 코어 호스팅** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="6577b-111">**만들기** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-111">Select the **Create** button.</span></span>

<span data-ttu-id="6577b-112">명령 셸에서 앱을 만들려면 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="6577b-113">프로젝트 폴더가 없는 경우 만드는 출력 위치를 지정하려면 경로가 있는 명령에 출력 옵션을 포함합니다(예: `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="6577b-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6577b-114">폴더 이름도 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="6577b-115">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="6577b-115">Server app configuration</span></span>

<span data-ttu-id="6577b-116">다음 섹션에서는 인증 지원이 포함된 경우 프로젝트에 대한 추가 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="6577b-117">시작 클래스</span><span class="sxs-lookup"><span data-stu-id="6577b-117">Startup class</span></span>

<span data-ttu-id="6577b-118">클래스에는 `Startup` 다음과 같은 추가 가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="6577b-119">`Startup.ConfigureServices`의 경우:</span><span class="sxs-lookup"><span data-stu-id="6577b-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="6577b-120">기본 UI가 있는 ID:</span><span class="sxs-lookup"><span data-stu-id="6577b-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="6577b-121">IDServer 위에 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 몇 가지 기본 ASP.NET 핵심 규칙을 설정 하는 추가 도우미 메서드를 사용 하 여 IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="6577b-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="6577b-122">IdentityServer에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 생성한 JWT 토큰의 유효성을 검사하기 위해 앱을 구성하는 추가 도우미 메서드를 사용한 인증:</span><span class="sxs-lookup"><span data-stu-id="6577b-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="6577b-123">`Startup.Configure`의 경우:</span><span class="sxs-lookup"><span data-stu-id="6577b-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="6577b-124">요청 자격 증명의 유효성을 검사하고 요청 컨텍스트에서 사용자를 설정하는 인증 미들웨어:</span><span class="sxs-lookup"><span data-stu-id="6577b-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="6577b-125">개방형 ID 연결(OIDC) 끝점을 노출하는 IdentityServer 미들웨어:</span><span class="sxs-lookup"><span data-stu-id="6577b-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="6577b-126">추가 Api 권한 부여</span><span class="sxs-lookup"><span data-stu-id="6577b-126">AddApiAuthorization</span></span>

<span data-ttu-id="6577b-127">도우미 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 메서드는 ASP.NET 코어 시나리오에 대 한 [IdentityServer를](https://identityserver.io/) 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="6577b-128">IdentityServer는 앱 보안 문제를 처리하기 위한 강력하고 확장 가능한 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="6577b-129">IdentityServer는 가장 일반적인 시나리오에 대해 불필요한 복잡성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="6577b-130">따라서 좋은 출발점으로 간주하는 일련의 규칙 및 구성 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="6577b-131">인증이 변경되면 앱의 요구 사항에 맞게 인증을 사용자 지정할 수 있는 IdentityServer의 모든 기능을 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="6577b-132">애드아이덴티티서버</span><span class="sxs-lookup"><span data-stu-id="6577b-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="6577b-133">도우미 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 메서드는 기본 인증 처리기로 앱에 대 한 정책 체계를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="6577b-134">이 정책은 ID가 ID URL 공간의 `/Identity`모든 하위 경로로 라우팅된 모든 요청을 처리할 수 있도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="6577b-135">는 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 다른 모든 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="6577b-136">또한 이 메서드는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-136">Additionally, this method:</span></span>

* <span data-ttu-id="6577b-137">`{APPLICATION NAME}API` 의 기본 범위로 IDServer에 API `{APPLICATION NAME}API`리소스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="6577b-138">앱에 대해 IdentityServer에서 발급한 토큰의 유효성을 검사하도록 JWT 베어러 토큰 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="6577b-139">일기 예보컨트롤러</span><span class="sxs-lookup"><span data-stu-id="6577b-139">WeatherForecastController</span></span>

<span data-ttu-id="6577b-140">`WeatherForecastController` *(컨트롤러/WeatherForecastController.cs)에서* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성이 클래스에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="6577b-141">이 특성은 리소스에 액세스하려면 기본 정책에 따라 사용자가 권한이 있어야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="6577b-142">기본 권한 부여 정책은 앞에서 언급한 정책 체계에 의해 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 설정된 기본 인증 체계를 사용하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="6577b-143">도우미 메서드는 앱에 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 대한 요청에 대한 기본 처리기로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="6577b-144">응용 프로그램Db컨텍스트</span><span class="sxs-lookup"><span data-stu-id="6577b-144">ApplicationDbContext</span></span>

<span data-ttu-id="6577b-145">`ApplicationDbContext` (Data/ApplicationDbContext.cs)에서 IdServer에 대한 스키마를 포함하도록 확장되는*Data/ApplicationDbContext.cs* <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 경우를 제외하고는 ID에서 동일이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="6577b-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="6577b-147">데이터베이스 스키마를 완전히 제어하려면 사용 가능한 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 클래스 중 하나에서 상속하고 메서드를 호출하여 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` Identity `OnModelCreating` 스키마를 포함하도록 컨텍스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="6577b-148">Oidc구성컨트롤러</span><span class="sxs-lookup"><span data-stu-id="6577b-148">OidcConfigurationController</span></span>

<span data-ttu-id="6577b-149">`OidcConfigurationController` *(컨트롤러/OidcConfigurationController.cs)에서*클라이언트 끝점은 OIDC 매개 변수를 제공 하도록 프로비전 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="6577b-150">앱 설정 파일</span><span class="sxs-lookup"><span data-stu-id="6577b-150">App settings files</span></span>

<span data-ttu-id="6577b-151">프로젝트 루트의 앱 설정 파일(appsettings.json)에서 `IdentityServer` 이 섹션에서는 구성된 클라이언트 목록을 설명합니다.*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="6577b-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="6577b-152">다음 예제에서는 단일 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-152">In the following example, there's a single client.</span></span> <span data-ttu-id="6577b-153">클라이언트 이름은 앱 이름에 해당하며 OAuth `ClientId` 매개 변수에 규칙에 따라 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="6577b-154">프로필은 구성 중인 앱 유형을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="6577b-155">이 프로필은 내부적으로 서버의 구성 프로세스를 단순화하는 규칙을 구동하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="6577b-156">개발 환경 앱 설정*파일(appsettings)에서. Development.json)* 프로젝트 루트에서 `IdentityServer` 이 섹션에서는 토큰에 서명하는 데 사용되는 키에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="6577b-157">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="6577b-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6577b-158">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="6577b-158">Authentication package</span></span>

<span data-ttu-id="6577b-159">개별 사용자 계정()을`Individual`사용하도록 앱을 만들면 앱은 앱의 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 프로젝트 파일에서 패키지에 대한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="6577b-160">이 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 토큰을 얻는 데 도움이 되는 기본 요소 집합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6577b-161">앱에 인증을 추가하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="6577b-162">앞의 패키지 참조에서 문서에 표시된 `{VERSION}` `Microsoft.AspNetCore.Blazor.Templates` 패키지 버전으로 <xref:blazor/get-started> 바꿉습니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="6577b-163">API 권한 부여 지원</span><span class="sxs-lookup"><span data-stu-id="6577b-163">API authorization support</span></span>

<span data-ttu-id="6577b-164">사용자 인증에 대한 지원은 패키지 내에 제공된 확장 방법에 의해 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 서비스 컨테이너에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="6577b-165">이 메서드는 앱이 기존 권한 부여 시스템과 상호 작용하는 데 필요한 모든 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="6577b-166">기본적으로 `_configuration/{client-id}`에서 규칙에 따라 앱에 대한 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="6577b-167">규칙에 따라 클라이언트 ID는 앱의 어셈블리 이름으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="6577b-168">이 URL은 옵션과 함께 오버로드를 호출하여 별도의 끝점을 가리키도록 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="6577b-169">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="6577b-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="6577b-170">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="6577b-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="6577b-171">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6577b-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="6577b-172">리디렉션토로그인 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6577b-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="6577b-173">로그인표시 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6577b-173">LoginDisplay component</span></span>

<span data-ttu-id="6577b-174">구성*요소(공유/LoginDisplay.razor)는* `MainLayout` 구성 요소(공유/MainLayout.razor)에서 렌더링되고 다음 동작을 관리합니다.*Shared/MainLayout.razor* `LoginDisplay`</span><span class="sxs-lookup"><span data-stu-id="6577b-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="6577b-175">인증된 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="6577b-175">For authenticated users:</span></span>
  * <span data-ttu-id="6577b-176">현재 사용자 이름을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-176">Displays the current user name.</span></span>
  * <span data-ttu-id="6577b-177">ASP.NET 코어 ID의 사용자 프로필 페이지에 대한 링크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="6577b-178">앱에서 로그아웃할 수 있는 버튼을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="6577b-179">익명 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="6577b-179">For anonymous users:</span></span>
  * <span data-ttu-id="6577b-180">등록 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-180">Offers the option to register.</span></span>
  * <span data-ttu-id="6577b-181">로그인 할 수있는 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="6577b-182">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6577b-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="6577b-183">가져오기데이터 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6577b-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="6577b-184">앱 실행</span><span class="sxs-lookup"><span data-stu-id="6577b-184">Run the app</span></span>

<span data-ttu-id="6577b-185">서버 프로젝트에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-185">Run the app from the Server project.</span></span> <span data-ttu-id="6577b-186">Visual Studio를 사용하는 경우 **솔루션 탐색기에서** 서버 프로젝트를 선택하고 도구 모음에서 **실행** 단추를 선택하거나 **디버그** 메뉴에서 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="6577b-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6577b-187">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="6577b-187">Additional resources</span></span>

* [<span data-ttu-id="6577b-188">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="6577b-188">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
