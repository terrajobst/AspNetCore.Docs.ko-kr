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
# <a name="authentication-and-authorization-for-spas"></a>인증 및 Spa에 대 한 권한 부여

ASP.NET Core 3.0 이상에서는 API 권한 부여에 대 한 지원을 사용 하 여 단일 페이지 앱 (Spa)에서 인증을 제공 합니다. ASP.NET Core Id를 인증 하 고 사용자가 저장과 함께 [IdentityServer](https://identityserver.io/) Open ID Connect를 구현 합니다.

인증 매개 변수를에 추가 된를 **Angular** 및 **React** 프로젝트 템플릿 유사한 인증 매개 변수는 **웹 응용 프로그램 (모델-뷰-컨트롤러)**  (MVC) 및 **웹 응용 프로그램** (Razor 페이지) 프로젝트 템플릿. 허용 된 매개 변수 값을 **None** 하 고 **개별**합니다. 합니다 **React.js 및 Redux** 프로젝트 템플릿에서 지금은 인증 매개 변수를 지원 하지 않습니다.

## <a name="create-an-app-with-api-authorization-support"></a>API 권한 부여 지원을 통해 앱 만들기

Angular 및 React Spa를 사용 하 여 사용자 인증 및 권한 부여를 사용할 수 있습니다. 명령 셸을 열고 다음 명령을 실행 합니다.

**Angular**:

```console
dotnet new angular -o <output_directory_name> -au Individual
```

**React**:

```console
dotnet new react -o <output_directory_name> -au Individual
```

앞의 명령을 사용 하 여 ASP.NET Core 앱을 만듭니다는 *ClientApp* SPA를 포함 하는 디렉터리입니다.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>앱의 ASP.NET Core 구성 요소에 대 한 일반적인 설명

다음 섹션에서는 인증 지원이 포함 된 경우 프로젝트에 대 한 추가 설명 합니다.

### <a name="startup-class"></a>Startup 클래스

`Startup` 클래스에 다음 내용을 추가 합니다.

* 내부는 `Startup.ConfigureServices` 메서드:
  * 기본 UI 사용 하 여 id:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * 추가 사용 하 여 IdentityServer `AddApiAuthorization` 도우미 메서드 해당 설정 일부 기본 IdentityServer 기반으로 ASP.NET Core 규칙:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * 추가 인증 `AddIdentityServerJwt` IdentityServer에서 생성 된 JWT 토큰의 유효성을 검사 하려면 앱을 구성 하는 도우미 메서드입니다.

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* 내부는 `Startup.Configure` 메서드:
  * 요청 자격 증명 유효성 검사 및 사용자 요청 컨텍스트를 설정 하는 일을 담당 하는 인증 미들웨어:

    ```csharp
    app.UseAuthentication();
    ```

  * Open ID Connect 끝점을 노출 하는 IdentityServer 미들웨어:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

이 도우미 메서드는 지원 되는 구성을 사용 하려면 IdentityServer를 구성 합니다. IdentityServer는 응용 프로그램 보안 문제를 처리 하기 위한 강력 하 고 확장 가능한 프레임 워크입니다. 동시에 가장 일반적인 시나리오에 대 한 불필요 한 복잡성을 표시합니다. 따라서 좋은 시작 지점으로 간주 되는 작업을 할 규칙과 구성 옵션 집합이 제공 됩니다. 인증 요구 변화 되 면 IdentityServer 활용 경우 인증 요구에 맞게 사용자 지정할 수 있습니다.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

이 도우미 메서드는 기본 인증 처리기로 앱에 대 한 정책 구성표를 구성합니다. Identity Id URL 공간에서 모든 하위 라우팅되는 모든 요청을 처리할 수 있도록 정책이 구성 되어 "/ Identity"입니다. `JwtBearerHandler` 다른 모든 요청을 처리 합니다. 또한이 메서드는 등록을 `<<ApplicationName>>API` IdentityServer와의 기본 범위를 사용 하 여 API 리소스 `<<ApplicationName>>API` 하 고 앱에 대 한 IdentityServer에서 발급 된 토큰의 유효성을 검사 하 여 JWT 전달자 토큰 미들웨어를 구성 합니다.

### <a name="sampledatacontroller"></a>SampleDataController

에 *Controllers\SampleDataController.cs* 파일을 확인 합니다 `[Authorize]` 리소스에 액세스 하는 기본 정책을 기반으로 사용자 권한을 부여 해야 함을 나타내는 클래스에 적용 된 특성입니다. 기본 권한 부여 정책을 통해 설정 되는 기본 인증 체계를 사용 하도록 구성 되어야 발생 `AddIdentityServerJwt` 만드는 위에 언급 된 정책 구성표는 `JwtBearerHandler` 이러한 도우미 메서드에서 대 한 기본 처리기 구성 앱에 요청 합니다.

### <a name="applicationdbcontext"></a>ApplicationDbContext

에 *Data\ApplicationDbContext.cs* 파일을 동일한 `DbContext` 확장 하는 예외를 사용 하 여 id에서는 `ApiAuthorizationDbContext` (더 많이 파생 클래스에서 `IdentityDbContext`) IdentityServer에 대 한 스키마를 포함 하도록 합니다.

데이터베이스 스키마의 전체 제어를 사용할 수 있는 Id 중 하나에서 상속 `DbContext` 클래스 및 컨텍스트를 호출 하 여 Id 스키마를 포함 하도록 구성 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` 에 `OnModelCreating` 메서드.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

에 *Controllers\OidcConfigurationController.cs* 파일, 클라이언트를 사용 해야 하는 OIDC 매개 변수를 제공 하도록 프로 비전 되는 끝점을 확인할 수 있습니다.

### <a name="appsettingsjson"></a>appsettings.json

에 *appsettings.json* 파일 프로젝트 루트의 새로운 `IdentityServer` 섹션 목록을 설명 하는 클라이언트를 구성 합니다. 다음 예에는 단일 클라이언트가 있습니다. 클라이언트 앱 이름으로 해당 이름과 OAuth 규칙으로 매핑된 `ClientId` 매개 변수입니다. 프로필에는 구성 중인 앱 유형을 나타냅니다. 서버에 대 한 구성 프로세스를 간소화 하는 드라이브 규칙을 내부적으로 사용 됩니다. 가지 여러 프로필을 사용할 수에 설명 된 대로 합니다 [응용 프로그램 프로필](#application-profiles) 섹션입니다.

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.json

에 *appsettings 합니다. Development.json* 된 프로젝트 루트의 파일 방법이 `IdentityServer` 토큰에 서명 하는 데 키를 설명 하는 섹션입니다. 에 설명 된 대로 프로 비전 하 고 앱을 함께 배포 하는 키 필요를 프로덕션에 배포 하는 경우는 [프로덕션에 배포](#deploy-to-production) 섹션입니다.

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Angular 앱에 대 한 일반적인 설명

Angular에서 지 원하는 인증 및 권한 부여 API 템플릿이 포함에 Angular 모듈 자체에 *ClientApp\src\api에 대 한 권한 부여* 디렉터리입니다. 모듈은 다음 요소로 구성 됩니다.

* 구성 요소 3:
  * *login.component.ts*: 앱의 로그인 흐름을 처리합니다.
  * *logout.component.ts*: 앱의 로그 아웃 흐름을 처리합니다.
  * *login-menu.component.ts*: 다음 링크 중 하나를 표시 하는 위젯:
    * 사용자 프로필 관리 및 로그 아웃 링크를 사용자가 인증 하는 경우.
    * 등록 및 로그인 사용자가 인증 되지 않은 경우 링크 합니다.
* 경로 가드 `AuthorizeGuard` 경로에 추가할 수 있습니다 하 고 사용자 경로 방문 하기 전에 인증 해야 합니다.
* HTTP 인터셉터 `AuthorizeInterceptor` 사용자가 인증 하는 경우에 API를 대상으로 나가는 HTTP 요청에 액세스 토큰을 연결 하는 합니다.
* 서비스 `AuthorizeService` 인증 프로세스의 하위 수준 세부 정보를 처리 하 고 소비에 대 한 앱의 나머지 부분에 인증된 된 사용자에 대 한 정보를 노출 합니다.
* 앱의 인증 부분을 사용 하 여 연결 된 경로 정의 하는 Angular 모듈입니다. 로그인 메뉴 구성 요소, 인터셉터, 가드를 및 앱의 나머지 부분에서 사용 하기 위해 서비스를 노출합니다.

## <a name="general-description-of-the-react-app"></a>React 앱에 대 한 일반적인 설명

인증 및 React 템플릿은 API 권한 부여에 대 한 지원에 상주 합니다 *ClientApp\src\components\api에 대 한 권한 부여* 디렉터리입니다. 다음과 같은 요소로 구성 됩니다.

* 4 개 구성 요소가 있습니다.
  * *Login.js*: 앱의 로그인 흐름을 처리합니다.
  * *Logout.js*: 앱의 로그 아웃 흐름을 처리합니다.
  * *LoginMenu.js*: 다음 링크 중 하나를 표시 하는 위젯:
    * 사용자 프로필 관리 및 로그 아웃 링크를 사용자가 인증 하는 경우.
    * 등록 및 로그인 사용자가 인증 되지 않은 경우 링크 합니다.
  * *AuthorizeRoute.js*: 사용자 구성 요소를 렌더링 하기 전에 인증 해야 하는 경로 구성 요소에 표시 된 `Component` 매개 변수입니다.
* 내보낸 `authService` 클래스의 인스턴스 `AuthorizeService` 인증 프로세스의 하위 수준 세부 정보를 처리 하 고 소비에 대 한 앱의 나머지 부분에 인증된 된 사용자에 대 한 정보를 노출 합니다.

솔루션의 주요 구성 요소를 살펴보았으므로 이제 앱에 대 한 개별 시나리오를 자세히 살펴보겠습니다를 걸릴 수 있습니다.

## <a name="require-authorization-on-a-new-api"></a>새 API에 대 한 권한 부여 필요

기본적으로 시스템은 쉽게 새 Api에 대 한 권한 부여를 요구 하도록 구성 됩니다. 이렇게 하려면 새 컨트롤러를 만들고 추가 `[Authorize]` 특성을 컨트롤러 클래스 또는 컨트롤러 내의 작업 합니다.

## <a name="protect-a-client-side-route-angular"></a>클라이언트 쪽 경로 (Angular) 보호

클라이언트 쪽 경로 보호는 권한 부여 보호에 경로 구성 하는 경우를 실행 하는 가드의 목록에 추가 하 여 수행 됩니다. 예를 들어 볼 수 있습니다 하는 방법을 `fetch-data` 기본 앱 Angular 모듈 내에서 경로 구성 합니다.

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

경로 보호 합니다. 실제 끝점을 보호 하지 않습니다 언급 해야 (여전히 필요는 `[Authorize]` 특성이 적용) 있지만 해당만 사용자에서 인증 되지 않은 경우에 지정된 된 클라이언트 쪽 경로 이동할 수 없습니다.

## <a name="authenticate-api-requests-angular"></a>API 요청 (Angular) 인증

앱에서 정의한 HTTP 클라이언트 인터셉터를 사용 하 여 앱과 함께 호스트 되는 Api에 대 한 요청을 인증 자동으로 수행 됩니다.

## <a name="protect-a-client-side-route-react"></a>클라이언트 쪽 경로 (React) 보호

클라이언트 쪽 경로 사용 하 여 보호 합니다 `AuthorizeRoute` 구성 요소는 일반 대신 `Route` 구성 요소입니다. 예를 들어, 확인 하는 방법을 `fetch-data` 내에서 경로 구성 합니다 `App` 구성 요소:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

경로 보호 합니다.

* 실제 끝점을 보호 하지 않습니다 (여전히 필요는 `[Authorize]` 특성이 적용).
* 인증 되지 않은 경우 지정된 된 클라이언트 쪽 경로 탐색에서 사용자를만 제한 됩니다.

## <a name="authenticate-api-requests-react"></a>API 요청 (React) 인증

먼저 이루어집니다 React 사용 하 여 요청을 인증 합니다 `authService` 에서 인스턴스는 `AuthorizeService`합니다. 액세스 토큰에서 검색 되는 `authService` 아래와 같이 요청에 연결 됩니다. React 구성 요소에서이 작업에서 널리 사용 되는 `componentDidMount` 수명 주기 메서드 또는 일부 사용자 상호 작용의 결과입니다.

### <a name="import-the-authservice-into-your-component"></a>구성 요소에는 authService 가져오기

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>검색 및 액세스 토큰 응답에 연결

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

## <a name="deploy-to-production"></a>프로덕션 환경에 배포

프로덕션 환경에 앱을 배포 하려면 다음 리소스를 프로 비전 할 필요 합니다.

* Identity 사용자 계정과 IdentityServer를 저장할 데이터베이스를 부여 합니다.
* 프로덕션 인증서를 토큰 서명에 사용 합니다.
  * 이 인증서에 대 한 특정 사항은 자체 서명 된 인증서 또는 CA 기관을 통해 프로 비전 인증서를 수 있습니다.
  * PowerShell 또는 OpenSSL과 같은 표준 도구를 통해 생성할 수 있습니다.
  * 대상 컴퓨터의 인증서 저장소에 설치 또는 배포 수는 *.pfx* 강력한 암호를 사용 하 여 파일입니다.

### <a name="example-deploy-to-azure-websites"></a>예제: Azure Websites에 배포

이 섹션에서는 인증서 저장소에 저장 된 인증서를 사용 하 여 Azure 웹 사이트에 앱을 배포를 설명 합니다. 인증서 저장소에서 인증서를 로드 하려면 앱을 수정 하려면 App Service 계획에 있어야 할 이상 표준 계층은 이후 단계에서 구성한 경우. 앱의 *appsettings.json* 파일을 수정 합니다 `IdentityServer` 키 세부 정보를 포함 하는 섹션:

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

* 인증서 이름 속성의 고유 인증서의 주체를 사용 하 여 해당합니다.
* 저장소 위치에서 인증서를 로드할 수 있는 위치를 나타냅니다 (`CurrentUser` 또는 `LocalMachine`).
* 저장소 이름을 인증서 저장 된 인증서 저장소의 이름을 나타냅니다. 이 경우 개인 사용자 저장소를 가리킵니다.

Azure Websites에 배포 하려면의 단계에 따라 앱을 배포할 [Azure에 앱을 배포](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) 필요한 Azure 리소스를 만들고 앱을 프로덕션에 배포 합니다.

앞에서 설명한 절차를 따라 앱을 Azure에 배포 되지만 기능 아직. 앱에서 사용 된 인증서도 설정 해야 합니다. 사용할 인증서의 지문을 찾아에 설명 된 단계를 따릅니다 [인증서 로드](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)합니다.

하지만 이러한 SSL를 언급 하는 단계, 즉를 **개인 인증서** 섹션 포털에서 앱을 사용 하려면 프로 비전된 인증서를 업로드할 수 있습니다.

이 단계를 수행한 후 앱을 다시 시작 하 고 기능 이어야 합니다.

## <a name="other-configuration-options"></a>기타 구성 옵션

규칙, 기본값 및 Spa에 대 한 경험을 간소화 하는 향상 기능 집합과 IdentityServer 기반 API 권한 부여에 대 한 지원. 물론 IdentityServer의 모든 기능을 사용할 수 백그라운드에서 ASP.NET Core 통합 시나리오는 포함 되지 않습니다. ASP.NET Core 지원 "타사" 앱, 모든 앱 생성 되 고 조직에서 배포한에 포커스가 있습니다. 이와 같이 동의 또는 페더레이션과 같은 항목에 대 한 지원은 제공 되지 않습니다. 이러한 시나리오에 대 한 IdentityServer를 사용 하 고 해당 설명서를 따릅니다.

### <a name="application-profiles"></a>응용 프로그램 프로필

응용 프로그램 프로필은 추가 매개 변수를 정의 하는 앱에 대 한 미리 정의 된 구성입니다. 이때 다음 프로필 지원 됩니다.

* `IdentityServerSPA`: SPA를 IdentityServer 단일 단위로 함께 호스트를 나타냅니다.
  * 합니다 `redirect_uri` 기본값으로 `/authentication/login-callback`합니다.
  * 합니다 `post_logout_redirect_uri` 기본값으로 `/authentication/logout-callback`합니다.
  * 범위 집합에 포함 됩니다는 `openid`, `profile`, 및 앱에서 Api에 대해 정의 된 모든 범위입니다.
  * 허용 되는 OIDC 응답 형식 집합이 `id_token token` 또는 각각 개별적으로 (`id_token`, `token`).
  * 허용 되는 응답 모드는 `fragment`합니다.
* `SPA`: IdentityServer와 호스트 되지 SPA를 나타냅니다.
  * 범위 집합에 포함 됩니다는 `openid`, `profile`, 및 앱에서 Api에 대해 정의 된 모든 범위입니다.
  * 허용 되는 OIDC 응답 형식 집합이 `id_token token` 또는 각각 개별적으로 (`id_token`, `token`).
  * 허용 되는 응답 모드는 `fragment`합니다.
* `IdentityServerJwt`: IdentityServer와 함께 호스트 되는 API를 나타냅니다.
  * 앱은 앱 이름으로 설정 되는 범위가 단일 하도록 구성 됩니다.
* `API`: IdentityServer 사용 하 여 호스트 되지 않습니다는 API를 나타냅니다.
  * 앱은 앱 이름으로 설정 되는 범위가 단일 하도록 구성 됩니다.

### <a name="configuration-through-appsettings"></a>AppSettings 통해 구성

목록에 추가 하 여 구성 시스템을 통해 앱을 구성할 `Clients` 또는 `Resources`합니다.

각 클라이언트 구성 `redirect_uri` 고 `post_logout_redirect_uri` 속성을 다음 예와에서 같이:

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

리소스를 구성 하는 경우에 아래와 같이 리소스에 대 한 범위를 구성할 수 있습니다.

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

### <a name="configuration-through-code"></a>코드를 통해 구성

클라이언트 및 오버 로드를 사용 하 여 코드를 통해 리소스를 구성할 수도 있습니다 `AddApiAuthorization` 옵션을 구성 하는 작업을 수행 하는 합니다.

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

## <a name="additional-resources"></a>추가 자료

* <xref:spa/angular>
* <xref:spa/react>
