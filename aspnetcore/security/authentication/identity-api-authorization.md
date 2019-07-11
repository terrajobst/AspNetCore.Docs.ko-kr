---
title: ASP.NET Core의 단일 페이지 앱에 대 한 인증 소개
author: javiercn
description: ASP.NET Core 앱 내에서 호스팅되는 단일 페이지 앱을 사용 하 여 사용 하 여 Id입니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 302a5e10a70e40e75ab9fe4b3e5a98c4e847b822
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815228"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="a132f-103">인증 및 Spa에 대 한 권한 부여</span><span class="sxs-lookup"><span data-stu-id="a132f-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="a132f-104">ASP.NET Core 3.0 이상에서는 API 권한 부여에 대 한 지원을 사용 하 여 단일 페이지 앱 (Spa)에서 인증을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="a132f-105">ASP.NET Core Id를 인증 하 고 사용자가 저장과 함께 [IdentityServer](https://identityserver.io/) Open ID Connect를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="a132f-106">인증 매개 변수를에 추가 된를 **Angular** 및 **React** 프로젝트 템플릿 유사한 인증 매개 변수는 **웹 응용 프로그램 (모델-뷰-컨트롤러)**  (MVC) 및 **웹 응용 프로그램** (Razor 페이지) 프로젝트 템플릿.</span><span class="sxs-lookup"><span data-stu-id="a132f-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="a132f-107">허용 된 매개 변수 값을 **None** 하 고 **개별**합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="a132f-108">합니다 **React.js 및 Redux** 프로젝트 템플릿에서 지금은 인증 매개 변수를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="a132f-109">API 권한 부여 지원을 통해 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="a132f-109">Create an app with API authorization support</span></span>

<span data-ttu-id="a132f-110">Angular 및 React Spa를 사용 하 여 사용자 인증 및 권한 부여를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="a132f-111">명령 셸을 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="a132f-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="a132f-112">**Angular**:</span></span>

```console
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="a132f-113">**React**:</span><span class="sxs-lookup"><span data-stu-id="a132f-113">**React**:</span></span>

```console
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="a132f-114">앞의 명령을 사용 하 여 ASP.NET Core 앱을 만듭니다는 *ClientApp* SPA를 포함 하는 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="a132f-115">앱의 ASP.NET Core 구성 요소에 대 한 일반적인 설명</span><span class="sxs-lookup"><span data-stu-id="a132f-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="a132f-116">다음 섹션에서는 인증 지원이 포함 된 경우 프로젝트에 대 한 추가 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="a132f-117">Startup 클래스</span><span class="sxs-lookup"><span data-stu-id="a132f-117">Startup class</span></span>

<span data-ttu-id="a132f-118">`Startup` 클래스에 다음 내용을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="a132f-119">내부는 `Startup.ConfigureServices` 메서드:</span><span class="sxs-lookup"><span data-stu-id="a132f-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="a132f-120">기본 UI 사용 하 여 id:</span><span class="sxs-lookup"><span data-stu-id="a132f-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="a132f-121">추가 사용 하 여 IdentityServer `AddApiAuthorization` 도우미 메서드 해당 설정 일부 기본 IdentityServer 기반으로 ASP.NET Core 규칙:</span><span class="sxs-lookup"><span data-stu-id="a132f-121">IdentityServer with an additional `AddApiAuthorization` helper method that setups some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="a132f-122">추가 인증 `AddIdentityServerJwt` IdentityServer에서 생성 된 JWT 토큰의 유효성을 검사 하려면 앱을 구성 하는 도우미 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="a132f-123">내부는 `Startup.Configure` 메서드:</span><span class="sxs-lookup"><span data-stu-id="a132f-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="a132f-124">요청 자격 증명 유효성 검사 및 사용자 요청 컨텍스트를 설정 하는 일을 담당 하는 인증 미들웨어:</span><span class="sxs-lookup"><span data-stu-id="a132f-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="a132f-125">Open ID Connect 끝점을 노출 하는 IdentityServer 미들웨어:</span><span class="sxs-lookup"><span data-stu-id="a132f-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="a132f-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="a132f-126">AddApiAuthorization</span></span>

<span data-ttu-id="a132f-127">이 도우미 메서드는 지원 되는 구성을 사용 하려면 IdentityServer를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="a132f-128">IdentityServer는 응용 프로그램 보안 문제를 처리 하기 위한 강력 하 고 확장 가능한 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="a132f-129">동시에 가장 일반적인 시나리오에 대 한 불필요 한 복잡성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="a132f-130">따라서 좋은 시작 지점으로 간주 되는 작업을 할 규칙과 구성 옵션 집합이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="a132f-131">인증 요구 변화 되 면 IdentityServer 활용 경우 인증 요구에 맞게 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="a132f-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="a132f-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="a132f-133">이 도우미 메서드는 기본 인증 처리기로 앱에 대 한 정책 구성표를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="a132f-134">Identity Id URL 공간에서 모든 하위 라우팅되는 모든 요청을 처리할 수 있도록 정책이 구성 되어 "/ Identity"입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="a132f-135">`JwtBearerHandler` 다른 모든 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="a132f-136">또한이 메서드는 등록을 `<<ApplicationName>>API` IdentityServer와의 기본 범위를 사용 하 여 API 리소스 `<<ApplicationName>>API` 하 고 앱에 대 한 IdentityServer에서 발급 된 토큰의 유효성을 검사 하 여 JWT 전달자 토큰 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="sampledatacontroller"></a><span data-ttu-id="a132f-137">SampleDataController</span><span class="sxs-lookup"><span data-stu-id="a132f-137">SampleDataController</span></span>

<span data-ttu-id="a132f-138">에 *Controllers\SampleDataController.cs* 파일을 확인 합니다 `[Authorize]` 리소스에 액세스 하는 기본 정책을 기반으로 사용자 권한을 부여 해야 함을 나타내는 클래스에 적용 된 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-138">In the *Controllers\SampleDataController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="a132f-139">기본 권한 부여 정책을 통해 설정 되는 기본 인증 체계를 사용 하도록 구성 되어야 발생 `AddIdentityServerJwt` 만드는 위에 언급 된 정책 구성표는 `JwtBearerHandler` 이러한 도우미 메서드에서 대 한 기본 처리기 구성 앱에 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="a132f-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="a132f-140">ApplicationDbContext</span></span>

<span data-ttu-id="a132f-141">에 *Data\ApplicationDbContext.cs* 파일을 동일한 `DbContext` 확장 하는 예외를 사용 하 여 id에서는 `ApiAuthorizationDbContext` (더 많이 파생 클래스에서 `IdentityDbContext`) IdentityServer에 대 한 스키마를 포함 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="a132f-142">데이터베이스 스키마의 전체 제어를 사용할 수 있는 Id 중 하나에서 상속 `DbContext` 클래스 및 컨텍스트를 호출 하 여 Id 스키마를 포함 하도록 구성 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` 에 `OnModelCreating` 메서드.</span><span class="sxs-lookup"><span data-stu-id="a132f-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="a132f-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="a132f-143">OidcConfigurationController</span></span>

<span data-ttu-id="a132f-144">에 *Controllers\OidcConfigurationController.cs* 파일, 클라이언트를 사용 해야 하는 OIDC 매개 변수를 제공 하도록 프로 비전 되는 끝점을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="a132f-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="a132f-145">appsettings.json</span></span>

<span data-ttu-id="a132f-146">에 *appsettings.json* 파일 프로젝트 루트의 새로운 `IdentityServer` 섹션 목록을 설명 하는 클라이언트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="a132f-147">다음 예에는 단일 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-147">In the following example, there's a single client.</span></span> <span data-ttu-id="a132f-148">클라이언트 앱 이름으로 해당 이름과 OAuth 규칙으로 매핑된 `ClientId` 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="a132f-149">프로필에는 구성 중인 앱 유형을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="a132f-150">서버에 대 한 구성 프로세스를 간소화 하는 드라이브 규칙을 내부적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="a132f-151">가지 여러 프로필을 사용할 수에 설명 된 대로 합니다 [응용 프로그램 프로필](#application-profiles) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="a132f-152">appsettings.Development.json</span><span class="sxs-lookup"><span data-stu-id="a132f-152">appsettings.Development.json</span></span>

<span data-ttu-id="a132f-153">에 *appsettings 합니다. Development.json* 된 프로젝트 루트의 파일 방법이 `IdentityServer` 토큰에 서명 하는 데 키를 설명 하는 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="a132f-154">에 설명 된 대로 프로 비전 하 고 앱을 함께 배포 하는 키 필요를 프로덕션에 배포 하는 경우는 [프로덕션에 배포](#deploy-to-production) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="a132f-155">Angular 앱에 대 한 일반적인 설명</span><span class="sxs-lookup"><span data-stu-id="a132f-155">General description of the Angular app</span></span>

<span data-ttu-id="a132f-156">Angular에서 지 원하는 인증 및 권한 부여 API 템플릿이 포함에 Angular 모듈 자체에 *ClientApp\src\api에 대 한 권한 부여* 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="a132f-157">모듈은 다음 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="a132f-158">구성 요소 3:</span><span class="sxs-lookup"><span data-stu-id="a132f-158">3 components:</span></span>
  * <span data-ttu-id="a132f-159">*login.component.ts*: 앱의 로그인 흐름을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="a132f-160">*logout.component.ts*: 앱의 로그 아웃 흐름을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="a132f-161">*login-menu.component.ts*: 다음 링크 중 하나를 표시 하는 위젯:</span><span class="sxs-lookup"><span data-stu-id="a132f-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="a132f-162">사용자 프로필 관리 및 로그 아웃 링크를 사용자가 인증 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="a132f-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="a132f-163">등록 및 로그인 사용자가 인증 되지 않은 경우 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="a132f-164">경로 가드 `AuthorizeGuard` 경로에 추가할 수 있습니다 하 고 사용자 경로 방문 하기 전에 인증 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="a132f-165">HTTP 인터셉터 `AuthorizeInterceptor` 사용자가 인증 하는 경우에 API를 대상으로 나가는 HTTP 요청에 액세스 토큰을 연결 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="a132f-166">서비스 `AuthorizeService` 인증 프로세스의 하위 수준 세부 정보를 처리 하 고 소비에 대 한 앱의 나머지 부분에 인증된 된 사용자에 대 한 정보를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="a132f-167">앱의 인증 부분을 사용 하 여 연결 된 경로 정의 하는 Angular 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="a132f-168">로그인 메뉴 구성 요소, 인터셉터, 가드를 및 앱의 나머지 부분에서 사용 하기 위해 서비스를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="a132f-169">React 앱에 대 한 일반적인 설명</span><span class="sxs-lookup"><span data-stu-id="a132f-169">General description of the React app</span></span>

<span data-ttu-id="a132f-170">인증 및 React 템플릿은 API 권한 부여에 대 한 지원에 상주 합니다 *ClientApp\src\components\api에 대 한 권한 부여* 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="a132f-171">다음과 같은 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="a132f-172">4 개 구성 요소가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-172">4 components:</span></span>
  * <span data-ttu-id="a132f-173">*Login.js*: 앱의 로그인 흐름을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="a132f-174">*Logout.js*: 앱의 로그 아웃 흐름을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="a132f-175">*LoginMenu.js*: 다음 링크 중 하나를 표시 하는 위젯:</span><span class="sxs-lookup"><span data-stu-id="a132f-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="a132f-176">사용자 프로필 관리 및 로그 아웃 링크를 사용자가 인증 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="a132f-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="a132f-177">등록 및 로그인 사용자가 인증 되지 않은 경우 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="a132f-178">*AuthorizeRoute.js*: 사용자 구성 요소를 렌더링 하기 전에 인증 해야 하는 경로 구성 요소에 표시 된 `Component` 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="a132f-179">내보낸 `authService` 클래스의 인스턴스 `AuthorizeService` 인증 프로세스의 하위 수준 세부 정보를 처리 하 고 소비에 대 한 앱의 나머지 부분에 인증된 된 사용자에 대 한 정보를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="a132f-180">솔루션의 주요 구성 요소를 살펴보았으므로 이제 앱에 대 한 개별 시나리오를 자세히 살펴보겠습니다를 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="a132f-181">새 API에 대 한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="a132f-181">Require authorization on a new API</span></span>

<span data-ttu-id="a132f-182">기본적으로 시스템은 쉽게 새 Api에 대 한 권한 부여를 요구 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="a132f-183">이렇게 하려면 새 컨트롤러를 만들고 추가 `[Authorize]` 특성을 컨트롤러 클래스 또는 컨트롤러 내의 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="a132f-184">클라이언트 쪽 경로 (Angular) 보호</span><span class="sxs-lookup"><span data-stu-id="a132f-184">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="a132f-185">클라이언트 쪽 경로 보호는 권한 부여 보호에 경로 구성 하는 경우를 실행 하는 가드의 목록에 추가 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-185">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="a132f-186">예를 들어 볼 수 있습니다 하는 방법을 `fetch-data` 기본 앱 Angular 모듈 내에서 경로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-186">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="a132f-187">경로 보호 합니다. 실제 끝점을 보호 하지 않습니다 언급 해야 (여전히 필요는 `[Authorize]` 특성이 적용) 있지만 해당만 사용자에서 인증 되지 않은 경우에 지정된 된 클라이언트 쪽 경로 이동할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-187">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="a132f-188">API 요청 (Angular) 인증</span><span class="sxs-lookup"><span data-stu-id="a132f-188">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="a132f-189">앱에서 정의한 HTTP 클라이언트 인터셉터를 사용 하 여 앱과 함께 호스트 되는 Api에 대 한 요청을 인증 자동으로 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-189">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="a132f-190">클라이언트 쪽 경로 (React) 보호</span><span class="sxs-lookup"><span data-stu-id="a132f-190">Protect a client-side route (React)</span></span>

<span data-ttu-id="a132f-191">클라이언트 쪽 경로 사용 하 여 보호 합니다 `AuthorizeRoute` 구성 요소는 일반 대신 `Route` 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-191">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="a132f-192">예를 들어, 확인 하는 방법을 `fetch-data` 내에서 경로 구성 합니다 `App` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="a132f-192">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="a132f-193">경로 보호 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-193">Protecting a route:</span></span>

* <span data-ttu-id="a132f-194">실제 끝점을 보호 하지 않습니다 (여전히 필요는 `[Authorize]` 특성이 적용).</span><span class="sxs-lookup"><span data-stu-id="a132f-194">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="a132f-195">인증 되지 않은 경우 지정된 된 클라이언트 쪽 경로 탐색에서 사용자를만 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-195">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="a132f-196">API 요청 (React) 인증</span><span class="sxs-lookup"><span data-stu-id="a132f-196">Authenticate API requests (React)</span></span>

<span data-ttu-id="a132f-197">먼저 이루어집니다 React 사용 하 여 요청을 인증 합니다 `authService` 에서 인스턴스는 `AuthorizeService`합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-197">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="a132f-198">액세스 토큰에서 검색 되는 `authService` 아래와 같이 요청에 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-198">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="a132f-199">React 구성 요소에서이 작업에서 널리 사용 되는 `componentDidMount` 수명 주기 메서드 또는 일부 사용자 상호 작용의 결과입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-199">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="a132f-200">구성 요소에는 authService 가져오기</span><span class="sxs-lookup"><span data-stu-id="a132f-200">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="a132f-201">검색 및 액세스 토큰 응답에 연결</span><span class="sxs-lookup"><span data-stu-id="a132f-201">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="a132f-202">프로덕션 환경에 배포</span><span class="sxs-lookup"><span data-stu-id="a132f-202">Deploy to production</span></span>

<span data-ttu-id="a132f-203">프로덕션 환경에 앱을 배포 하려면 다음 리소스를 프로 비전 할 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-203">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="a132f-204">Identity 사용자 계정과 IdentityServer를 저장할 데이터베이스를 부여 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-204">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="a132f-205">프로덕션 인증서를 토큰 서명에 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-205">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="a132f-206">이 인증서에 대 한 특정 사항은 자체 서명 된 인증서 또는 CA 기관을 통해 프로 비전 인증서를 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-206">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="a132f-207">PowerShell 또는 OpenSSL과 같은 표준 도구를 통해 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-207">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="a132f-208">대상 컴퓨터의 인증서 저장소에 설치 또는 배포 수는 *.pfx* 강력한 암호를 사용 하 여 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-208">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="a132f-209">예제: Azure Websites에 배포</span><span class="sxs-lookup"><span data-stu-id="a132f-209">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="a132f-210">이 섹션에서는 인증서 저장소에 저장 된 인증서를 사용 하 여 Azure 웹 사이트에 앱을 배포를 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-210">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="a132f-211">인증서 저장소에서 인증서를 로드 하려면 앱을 수정 하려면 App Service 계획에 있어야 할 이상 표준 계층은 이후 단계에서 구성한 경우.</span><span class="sxs-lookup"><span data-stu-id="a132f-211">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="a132f-212">앱의 *appsettings.json* 파일을 수정 합니다 `IdentityServer` 키 세부 정보를 포함 하는 섹션:</span><span class="sxs-lookup"><span data-stu-id="a132f-212">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="a132f-213">인증서 이름 속성의 고유 인증서의 주체를 사용 하 여 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-213">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="a132f-214">저장소 위치에서 인증서를 로드할 수 있는 위치를 나타냅니다 (`CurrentUser` 또는 `LocalMachine`).</span><span class="sxs-lookup"><span data-stu-id="a132f-214">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="a132f-215">저장소 이름을 인증서 저장 된 인증서 저장소의 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-215">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="a132f-216">이 경우 개인 사용자 저장소를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-216">In this case, it points to the personal user store.</span></span>

<span data-ttu-id="a132f-217">Azure Websites에 배포 하려면의 단계에 따라 앱을 배포할 [Azure에 앱을 배포](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) 필요한 Azure 리소스를 만들고 앱을 프로덕션에 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-217">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="a132f-218">앞에서 설명한 절차를 따라 앱을 Azure에 배포 되지만 기능 아직.</span><span class="sxs-lookup"><span data-stu-id="a132f-218">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="a132f-219">앱에서 사용 된 인증서도 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-219">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="a132f-220">사용할 인증서의 지문을 찾아에 설명 된 단계를 따릅니다 [인증서 로드](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-220">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="a132f-221">하지만 이러한 SSL를 언급 하는 단계, 즉를 **개인 인증서** 섹션 포털에서 앱을 사용 하려면 프로 비전된 인증서를 업로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-221">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="a132f-222">이 단계를 수행한 후 앱을 다시 시작 하 고 기능 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-222">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="a132f-223">기타 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="a132f-223">Other configuration options</span></span>

<span data-ttu-id="a132f-224">규칙, 기본값 및 Spa에 대 한 경험을 간소화 하는 향상 기능 집합과 IdentityServer 기반 API 권한 부여에 대 한 지원.</span><span class="sxs-lookup"><span data-stu-id="a132f-224">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="a132f-225">물론 IdentityServer의 모든 기능을 사용할 수 백그라운드에서 ASP.NET Core 통합 시나리오는 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-225">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="a132f-226">ASP.NET Core 지원 "타사" 앱, 모든 앱 생성 되 고 조직에서 배포한에 포커스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-226">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="a132f-227">이와 같이 동의 또는 페더레이션과 같은 항목에 대 한 지원은 제공 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-227">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="a132f-228">이러한 시나리오에 대 한 IdentityServer를 사용 하 고 해당 설명서를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-228">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="a132f-229">응용 프로그램 프로필</span><span class="sxs-lookup"><span data-stu-id="a132f-229">Application profiles</span></span>

<span data-ttu-id="a132f-230">응용 프로그램 프로필은 추가 매개 변수를 정의 하는 앱에 대 한 미리 정의 된 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-230">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="a132f-231">이때 다음 프로필 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-231">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="a132f-232">`IdentityServerSPA`: SPA를 IdentityServer 단일 단위로 함께 호스트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-232">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="a132f-233">합니다 `redirect_uri` 기본값으로 `/authentication/login-callback`합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-233">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="a132f-234">합니다 `post_logout_redirect_uri` 기본값으로 `/authentication/logout-callback`합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-234">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="a132f-235">범위 집합에 포함 됩니다는 `openid`, `profile`, 및 앱에서 Api에 대해 정의 된 모든 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-235">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="a132f-236">허용 되는 OIDC 응답 형식 집합이 `id_token token` 또는 각각 개별적으로 (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="a132f-236">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="a132f-237">허용 되는 응답 모드는 `fragment`합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-237">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="a132f-238">`SPA`: IdentityServer와 호스트 되지 SPA를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-238">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="a132f-239">범위 집합에 포함 됩니다는 `openid`, `profile`, 및 앱에서 Api에 대해 정의 된 모든 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-239">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="a132f-240">허용 되는 OIDC 응답 형식 집합이 `id_token token` 또는 각각 개별적으로 (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="a132f-240">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="a132f-241">허용 되는 응답 모드는 `fragment`합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-241">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="a132f-242">`IdentityServerJwt`: IdentityServer와 함께 호스트 되는 API를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-242">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="a132f-243">앱은 앱 이름으로 설정 되는 범위가 단일 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-243">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="a132f-244">`API`: IdentityServer 사용 하 여 호스트 되지 않습니다는 API를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-244">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="a132f-245">앱은 앱 이름으로 설정 되는 범위가 단일 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-245">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="a132f-246">AppSettings 통해 구성</span><span class="sxs-lookup"><span data-stu-id="a132f-246">Configuration through AppSettings</span></span>

<span data-ttu-id="a132f-247">목록에 추가 하 여 구성 시스템을 통해 앱을 구성할 `Clients` 또는 `Resources`합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-247">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="a132f-248">각 클라이언트 구성 `redirect_uri` 고 `post_logout_redirect_uri` 속성을 다음 예와에서 같이:</span><span class="sxs-lookup"><span data-stu-id="a132f-248">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="a132f-249">리소스를 구성 하는 경우에 아래와 같이 리소스에 대 한 범위를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-249">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="a132f-250">코드를 통해 구성</span><span class="sxs-lookup"><span data-stu-id="a132f-250">Configuration through code</span></span>

<span data-ttu-id="a132f-251">클라이언트 및 오버 로드를 사용 하 여 코드를 통해 리소스를 구성할 수도 있습니다 `AddApiAuthorization` 옵션을 구성 하는 작업을 수행 하는 합니다.</span><span class="sxs-lookup"><span data-stu-id="a132f-251">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="a132f-252">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a132f-252">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
