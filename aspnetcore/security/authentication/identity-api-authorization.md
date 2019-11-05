---
title: ASP.NET Core의 단일 페이지 앱에 대 한 인증 소개
author: javiercn
description: ASP.NET Core 앱 내에서 호스트 되는 단일 페이지 앱과 함께 Id를 사용 합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 5ed5fb61e5989b291523332c6a2ec332f9ca0f6b
ms.sourcegitcommit: e5d4768aaf85703effb4557a520d681af8284e26
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73616615"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="49947-103">SPAs에 대 한 인증 및 권한 부여</span><span class="sxs-lookup"><span data-stu-id="49947-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="49947-104">ASP.NET Core 3.0 이상에서는 API 권한 부여에 대 한 지원을 사용 하 여 SPAs (단일 페이지 앱)의 인증을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="49947-105">사용자를 인증 하 고 저장 하는 ASP.NET Core Id는 Open ID Connect 구현에 대 한 [IdentityServer](https://identityserver.io/) 와 결합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="49947-106">**웹 응용 프로그램 (모델-뷰-컨트롤러)** (MVC) 및 **웹 응용 프로그램** (Razor Pages)의 인증 매개 변수와 비슷한 **각도** 및 **반응** 프로젝트 템플릿에 인증 매개 변수가 추가 되었습니다. 프로젝트 템플릿.</span><span class="sxs-lookup"><span data-stu-id="49947-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="49947-107">허용 되는 매개 변수 값은 **None** 및 **개인용**입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="49947-108">현재 지점 **및 Redux** 프로젝트 템플릿은 인증 매개 변수를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="49947-109">API 권한 부여를 지 원하는 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="49947-109">Create an app with API authorization support</span></span>

<span data-ttu-id="49947-110">사용자 인증 및 권한 부여는 모두와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="49947-111">명령 셸을 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="49947-112">**각도**:</span><span class="sxs-lookup"><span data-stu-id="49947-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="49947-113">**반응**:</span><span class="sxs-lookup"><span data-stu-id="49947-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="49947-114">위의 명령은 SPA를 포함 하는 *ClientApp* 디렉터리를 사용 하 여 ASP.NET Core 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="49947-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="49947-115">앱의 ASP.NET Core 구성 요소에 대 한 일반적인 설명</span><span class="sxs-lookup"><span data-stu-id="49947-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="49947-116">다음 섹션에서는 인증 지원을 포함 하는 경우 프로젝트에 대 한 추가 설명입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="49947-117">Startup 클래스</span><span class="sxs-lookup"><span data-stu-id="49947-117">Startup class</span></span>

<span data-ttu-id="49947-118">`Startup` 클래스에는 다음과 같은 추가 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="49947-119">`Startup.ConfigureServices` 메서드 내에서:</span><span class="sxs-lookup"><span data-stu-id="49947-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="49947-120">기본 UI를 사용 하는 id:</span><span class="sxs-lookup"><span data-stu-id="49947-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="49947-121">IdentityServer IdentityServer 위에 몇 가지 기본 ASP.NET Core 규칙을 설정 하는 추가 `AddApiAuthorization` 도우미 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-121">IdentityServer with an additional `AddApiAuthorization` helper method that setups some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="49947-122">IdentityServer에서 생성 한 JWT 토큰의 유효성을 검사 하도록 앱을 구성 하는 추가 `AddIdentityServerJwt` 도우미 메서드를 사용 하 여 인증 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="49947-123">`Startup.Configure` 메서드 내에서:</span><span class="sxs-lookup"><span data-stu-id="49947-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="49947-124">요청 자격 증명의 유효성을 검사 하 고 요청 컨텍스트에서 사용자를 설정 해야 하는 인증 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="49947-125">Open ID Connect 끝점을 노출 하는 IdentityServer 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="49947-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="49947-126">AddApiAuthorization</span></span>

<span data-ttu-id="49947-127">이 도우미 메서드는 지원 되는 구성을 사용 하도록 IdentityServer를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="49947-128">IdentityServer는 앱 보안 문제를 처리 하기 위한 강력 하 고 확장 가능한 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="49947-129">이와 동시에는 가장 일반적인 시나리오에 대 한 불필요 한 복잡성을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="49947-130">따라서 규칙 집합 및 구성 옵션이 적절 한 시작 지점으로 간주 되는 사용자에 게 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="49947-131">인증을 변경 해야 하는 경우에는 요구 사항에 맞게 인증을 사용자 지정할 수 있는 IdentityServer의 모든 기능을 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="49947-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="49947-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="49947-133">이 도우미 메서드는 앱의 정책 스키마를 기본 인증 처리기로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="49947-134">이 정책은 id URL 공간 "/Identity"의 하위 경로로 라우팅되는 모든 요청을 Id로 처리할 수 있도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="49947-135">`JwtBearerHandler`는 다른 모든 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="49947-136">또한이 메서드는 IdentityServer을 사용 하 여 `<<ApplicationName>>API` API 리소스를 기본 범위 `<<ApplicationName>>API`로 등록 하 고 JWT 전달자 토큰 미들웨어를 구성 하 여 IdentityServer에서 앱에 대해 발급 한 토큰의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="49947-137">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="49947-137">WeatherForecastController</span></span>

<span data-ttu-id="49947-138">*Controllers\WeatherForecastController.cs* 파일에서 리소스에 액세스 하기 위한 기본 정책에 따라 사용자에 게 권한을 부여 해야 함을 나타내는 클래스에 적용 되는 `[Authorize]` 특성을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-138">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="49947-139">기본 권한 부여 정책은 위에서 언급 한 정책 체계에 `AddIdentityServerJwt` 하 여 설정 된 기본 인증 체계를 사용 하도록 구성 되며, 해당 도우미 메서드에서 구성 된 `JwtBearerHandler`를 요청에 대 한 기본 처리기로 설정 합니다. 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="49947-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="49947-140">ApplicationDbContext</span></span>

<span data-ttu-id="49947-141">*Data\ApplicationDbContext.cs* 파일에서 IdentityServer에 대 한 스키마를 포함 하도록 `ApiAuthorizationDbContext` (`IdentityDbContext`에서 더 많이 파생 된 클래스)를 확장 하는 예외와 함께 id에서 동일한 `DbContext` 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="49947-142">데이터베이스 스키마에 대 한 모든 권한을 얻으려면 사용 가능한 Id `DbContext` 클래스 중 하나에서 상속 하 고 `OnModelCreating` 메서드에서 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`를 호출 하 여 Id 스키마를 포함 하도록 컨텍스트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="49947-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="49947-143">OidcConfigurationController</span></span>

<span data-ttu-id="49947-144">*Controllers\OidcConfigurationController.cs* 파일에서 클라이언트가 사용 해야 하는 oidc 매개 변수를 제공 하도록 프로 비전 된 끝점을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="49947-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="49947-145">appsettings.json</span></span>

<span data-ttu-id="49947-146">프로젝트 루트의 *appsettings* 파일에는 구성 된 클라이언트 목록을 설명 하는 새로운 `IdentityServer` 섹션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="49947-147">다음 예제에는 단일 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-147">In the following example, there's a single client.</span></span> <span data-ttu-id="49947-148">클라이언트 이름은 앱 이름에 해당 하며 규칙에 따라 OAuth `ClientId` 매개 변수에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="49947-149">프로필은 구성 중인 앱 유형을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="49947-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="49947-150">서버에 대 한 구성 프로세스를 간소화 하는 규칙을 구동 하기 위해 내부적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="49947-151">[응용 프로그램 프로필](#application-profiles) 섹션에 설명 된 대로 몇 가지 프로필을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="49947-152">appsettings. 개발. json</span><span class="sxs-lookup"><span data-stu-id="49947-152">appsettings.Development.json</span></span>

<span data-ttu-id="49947-153">Appsettings에 *있습니다.* 프로젝트 루트의 Development json 파일에는 토큰에 서명 하는 데 사용 되는 키를 설명 하는 `IdentityServer` 섹션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="49947-154">프로덕션에 배포 하는 경우 [프로덕션에 배포](#deploy-to-production) 섹션에 설명 된 대로 키를 프로 비전 하 고 앱과 함께 배포 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="49947-155">각도 앱에 대 한 일반적인 설명</span><span class="sxs-lookup"><span data-stu-id="49947-155">General description of the Angular app</span></span>

<span data-ttu-id="49947-156">각도 템플릿에서 인증 및 API 권한 부여 지원은 *Clientapp\src\api\ac-authentication* 디렉터리의 자체 각도 모듈에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="49947-157">모듈은 다음 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="49947-158">3 개 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="49947-158">3 components:</span></span>
  * <span data-ttu-id="49947-159">*login. component. ts*: 앱의 로그인 흐름을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="49947-160">*logout*. s a t: 응용 프로그램의 로그 아웃 흐름을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="49947-161">*login-menu. component*: 다음 링크 집합 중 하나를 표시 하는 위젯:</span><span class="sxs-lookup"><span data-stu-id="49947-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="49947-162">사용자가 인증 되 면 사용자 프로필 관리 및 로그 아웃 링크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="49947-163">사용자가 인증 되지 않은 경우 등록 및 로그인 링크.</span><span class="sxs-lookup"><span data-stu-id="49947-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="49947-164">경로에 추가할 수 있는 경로 가드 `AuthorizeGuard` 경로를 방문 하기 전에 사용자를 인증 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="49947-165">사용자가 인증 될 때 API를 대상으로 하는 나가는 HTTP 요청에 액세스 토큰을 연결 하는 HTTP 인터셉터 `AuthorizeInterceptor`입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="49947-166">인증 프로세스의 하위 수준 세부 정보를 처리 하 고, 인증 된 사용자에 대 한 정보를 앱의 나머지 부분에 노출 하는 서비스 `AuthorizeService`입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="49947-167">앱의 인증 부분과 연결 된 경로를 정의 하는 각도 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="49947-168">응용 프로그램의 나머지 부분에서 사용 하기 위해 로그인 메뉴 구성 요소, 인터셉터, 가드 및 서비스를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="49947-169">반응 앱에 대 한 일반적인 설명</span><span class="sxs-lookup"><span data-stu-id="49947-169">General description of the React app</span></span>

<span data-ttu-id="49947-170">반응 템플릿에서 인증 및 API 권한 부여에 대 한 지원은 *ClientApp\src\components\api-authorization* 디렉터리에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="49947-171">다음 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="49947-172">4 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="49947-172">4 components:</span></span>
  * <span data-ttu-id="49947-173">*Login .js*: 앱의 로그인 흐름을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="49947-174">*Logout. js*: 응용 프로그램의 로그 아웃 흐름을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="49947-175">*LoginMenu*: 다음 링크 집합 중 하나를 표시 하는 위젯:</span><span class="sxs-lookup"><span data-stu-id="49947-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="49947-176">사용자가 인증 되 면 사용자 프로필 관리 및 로그 아웃 링크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="49947-177">사용자가 인증 되지 않은 경우 등록 및 로그인 링크.</span><span class="sxs-lookup"><span data-stu-id="49947-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="49947-178">*AuthorizeRoute*: `Component` 매개 변수에 표시 된 구성 요소를 렌더링 하기 전에 사용자를 인증 해야 하는 경로 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="49947-179">인증 프로세스의 하위 수준 세부 정보를 처리 하 고, 인증 된 사용자에 대 한 정보를 사용 하기 위해 나머지 앱에 노출 하는 클래스 `AuthorizeService`의 내보낸 `authService` 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="49947-180">이제 솔루션의 주요 구성 요소를 살펴보았으므로 앱에 대 한 개별 시나리오를 자세히 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="49947-181">새 API에 대 한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="49947-181">Require authorization on a new API</span></span>

<span data-ttu-id="49947-182">기본적으로 시스템은 새 Api에 대 한 권한 부여를 쉽게 요구 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="49947-183">이렇게 하려면 새 컨트롤러를 만들고 컨트롤러 클래스 또는 컨트롤러 내의 모든 작업에 `[Authorize]` 특성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="49947-184">API 인증 처리기 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="49947-184">Customize the API authentication handler</span></span>

<span data-ttu-id="49947-185">API JWT 처리기의 구성을 사용자 지정 하려면 해당 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 인스턴스를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-185">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="49947-186">클라이언트 쪽 경로 (각도) 보호</span><span class="sxs-lookup"><span data-stu-id="49947-186">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="49947-187">클라이언트 쪽 경로 보호는 경로를 구성할 때 실행할 가드 목록에 권한 부여 가드를 추가 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-187">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="49947-188">예를 들어, 기본 앱 각도 모듈 내에서 `fetch-data` 경로를 구성 하는 방법을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-188">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="49947-189">경로를 보호 하는 것은 실제 끝점 (여전히 적용 되는 `[Authorize]` 특성이 필요 함)을 보호 하지는 않지만 사용자가 인증 되지 않은 경우 지정 된 클라이언트 쪽 경로로 이동 하는 것을 방지 하는 것이 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-189">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="49947-190">API 요청 (각도) 인증</span><span class="sxs-lookup"><span data-stu-id="49947-190">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="49947-191">앱과 함께 호스트 되는 Api에 대 한 요청 인증은 앱에서 정의 된 HTTP 클라이언트 인터셉터를 사용 하 여 자동으로 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-191">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="49947-192">클라이언트 쪽 경로 보호 (반응)</span><span class="sxs-lookup"><span data-stu-id="49947-192">Protect a client-side route (React)</span></span>

<span data-ttu-id="49947-193">일반 `Route` 구성 요소 대신 `AuthorizeRoute` 구성 요소를 사용 하 여 클라이언트 쪽 경로를 보호 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-193">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="49947-194">예를 들어 `App` 구성 요소 내에서 `fetch-data` 경로를 구성 하는 방법을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-194">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="49947-195">경로 보호:</span><span class="sxs-lookup"><span data-stu-id="49947-195">Protecting a route:</span></span>

* <span data-ttu-id="49947-196">실제 끝점을 보호 하지 않습니다 (계속 적용 되는 `[Authorize]` 특성이 필요 함).</span><span class="sxs-lookup"><span data-stu-id="49947-196">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="49947-197">사용자가 인증 되지 않은 경우 지정 된 클라이언트 쪽 경로로 이동할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-197">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="49947-198">API 요청 인증 (반응)</span><span class="sxs-lookup"><span data-stu-id="49947-198">Authenticate API requests (React)</span></span>

<span data-ttu-id="49947-199">응답을 사용 하 여 요청을 인증 하려면 먼저 `AuthorizeService`에서 `authService` 인스턴스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-199">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="49947-200">액세스 토큰은 `authService`에서 검색 되 고 아래와 같이 요청에 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-200">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="49947-201">구성 요소에 반응 하는 경우이 작업은 일반적으로 `componentDidMount` 수명 주기 메서드 또는 일부 사용자 상호 작용의 결과로 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-201">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="49947-202">구성 요소로 authService 가져오기</span><span class="sxs-lookup"><span data-stu-id="49947-202">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="49947-203">액세스 토큰을 검색 하 고 응답에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-203">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="49947-204">프로덕션 환경에 배포</span><span class="sxs-lookup"><span data-stu-id="49947-204">Deploy to production</span></span>

<span data-ttu-id="49947-205">프로덕션 환경에 앱을 배포 하려면 다음 리소스를 프로 비전 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-205">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="49947-206">Id 사용자 계정 및 IdentityServer 권한을 저장할 데이터베이스입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-206">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="49947-207">토큰에 서명 하는 데 사용할 프로덕션 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-207">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="49947-208">이 인증서에 대 한 특정 요구 사항은 없습니다. 자체 서명 된 인증서 또는 CA 인증 기관을 통해 프로 비전 된 인증서 일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-208">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="49947-209">PowerShell 또는 OpenSSL 같은 표준 도구를 통해 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-209">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="49947-210">대상 컴퓨터의 인증서 저장소에 설치 하거나 강력한 암호를 사용 하 여 *.pfx* 파일로 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-210">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="49947-211">예: Azure Websites에 배포</span><span class="sxs-lookup"><span data-stu-id="49947-211">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="49947-212">이 섹션에서는 인증서 저장소에 저장 된 인증서를 사용 하 여 Azure websites에 앱을 배포 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-212">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="49947-213">인증서 저장소에서 인증서를 로드 하도록 앱을 수정 하려면 이후 단계에서를 구성할 때 App Service 요금제가 최소한 표준 계층에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-213">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="49947-214">앱의 *appsettings json* 파일에서 키 세부 정보를 포함 하도록 `IdentityServer` 섹션을 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-214">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="49947-215">인증서의 이름 속성은 인증서의 고유 주체에 해당 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-215">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="49947-216">저장소 위치는 인증서를 로드할 위치 (`CurrentUser` 또는 `LocalMachine`)를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="49947-216">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="49947-217">저장소 이름은 인증서가 저장 되는 인증서 저장소의 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="49947-217">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="49947-218">이 경우 개인 사용자 저장소를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="49947-218">In this case, it points to the personal user store.</span></span>

<span data-ttu-id="49947-219">Azure Websites에 배포 하려면 [azure에 앱 배포](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) 의 단계에 따라 앱을 배포 하 여 필요한 azure 리소스를 만들고 앱을 프로덕션에 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-219">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="49947-220">위의 지침을 수행 하면 앱이 Azure에 배포 되지만 아직 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-220">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="49947-221">앱에서 사용 하는 인증서를 아직 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-221">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="49947-222">사용할 인증서의 지문을 찾고 [인증서 로드](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)에 설명 된 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="49947-222">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="49947-223">이러한 단계에서는 SSL을 언급 하지만 포털에는 앱에서 사용할 프로 비전 된 인증서를 업로드할 수 있는 **개인 인증서** 섹션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-223">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="49947-224">이 단계를 수행한 후에는 앱을 다시 시작 하 고 작동 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-224">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="49947-225">기타 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="49947-225">Other configuration options</span></span>

<span data-ttu-id="49947-226">API 권한 부여에 대 한 지원은 규칙, 기본값 및 향상 된 기능 집합을 사용 하 여 IdentityServer를 기반으로 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-226">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="49947-227">물론 ASP.NET Core 통합이 시나리오에 포함 되지 않는 경우 IdentityServer의 전체 기능을 내부적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-227">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="49947-228">ASP.NET Core 지원은 조직에서 모든 앱을 만들고 배포 하는 "자사" 앱에 중점을 두었습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-228">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="49947-229">따라서 동의 또는 페더레이션과 같은 항목에 대 한 지원이 제공 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-229">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="49947-230">이러한 시나리오의 경우 IdentityServer를 사용 하 고 해당 설명서를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="49947-230">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="49947-231">응용 프로그램 프로필</span><span class="sxs-lookup"><span data-stu-id="49947-231">Application profiles</span></span>

<span data-ttu-id="49947-232">응용 프로그램 프로필은 매개 변수를 추가로 정의 하는 앱에 대 한 미리 정의 된 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-232">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="49947-233">이번에는 다음 프로필이 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-233">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="49947-234">`IdentityServerSPA`: 단일 단위로 IdentityServer와 함께 호스트 된 SPA를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="49947-234">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="49947-235">`redirect_uri` 기본값은 `/authentication/login-callback`입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-235">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="49947-236">`post_logout_redirect_uri` 기본값은 `/authentication/logout-callback`입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-236">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="49947-237">범위 집합에는 앱의 Api에 대해 정의 된 `openid`, `profile`및 모든 범위가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-237">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="49947-238">허용 되는 OIDC 응답 유형 집합은 `id_token token` 또는 개별적으로 (`id_token`, `token`)입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-238">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="49947-239">허용 되는 응답 모드가 `fragment`입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-239">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="49947-240">`SPA`: IdentityServer로 호스팅되지 않는 SPA를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="49947-240">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="49947-241">범위 집합에는 앱의 Api에 대해 정의 된 `openid`, `profile`및 모든 범위가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-241">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="49947-242">허용 되는 OIDC 응답 유형 집합은 `id_token token` 또는 개별적으로 (`id_token`, `token`)입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-242">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="49947-243">허용 되는 응답 모드가 `fragment`입니다.</span><span class="sxs-lookup"><span data-stu-id="49947-243">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="49947-244">`IdentityServerJwt`: IdentityServer와 함께 호스트 되는 API를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="49947-244">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="49947-245">앱은 앱 이름으로 기본 설정 된 단일 범위를 갖도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-245">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="49947-246">`API`: IdentityServer를 사용 하 여 호스팅되지 않는 API를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="49947-246">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="49947-247">앱은 앱 이름으로 기본 설정 된 단일 범위를 갖도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="49947-247">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="49947-248">AppSettings를 통한 구성</span><span class="sxs-lookup"><span data-stu-id="49947-248">Configuration through AppSettings</span></span>

<span data-ttu-id="49947-249">구성 시스템을 `Clients` 또는 `Resources`목록에 추가 하 여 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-249">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="49947-250">다음 예제와 같이 각 클라이언트의 `redirect_uri` 및 `post_logout_redirect_uri` 속성을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="49947-250">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="49947-251">리소스를 구성할 때 아래와 같이 리소스에 대 한 범위를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-251">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="49947-252">코드를 통한 구성</span><span class="sxs-lookup"><span data-stu-id="49947-252">Configuration through code</span></span>

<span data-ttu-id="49947-253">옵션을 구성 하는 작업을 수행 하는 `AddApiAuthorization` 오버 로드를 사용 하 여 코드를 통해 클라이언트와 리소스를 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49947-253">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="49947-254">추가 자료</span><span class="sxs-lookup"><span data-stu-id="49947-254">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
