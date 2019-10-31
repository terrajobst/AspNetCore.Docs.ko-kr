---
title: ASP.NET Core의 단일 페이지 앱에 대 한 인증 소개
author: javiercn
description: ASP.NET Core 앱 내에서 호스트 되는 단일 페이지 앱과 함께 Id를 사용 합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 98df1aa1671c22384252676c56e8cb4a3a0a35eb
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190495"
---
# <a name="authentication-and-authorization-for-spas"></a>SPAs에 대 한 인증 및 권한 부여

ASP.NET Core 3.0 이상에서는 API 권한 부여에 대 한 지원을 사용 하 여 SPAs (단일 페이지 앱)의 인증을 제공 합니다. 사용자를 인증 하 고 저장 하는 ASP.NET Core Id는 Open ID Connect 구현에 대 한 [IdentityServer](https://identityserver.io/) 와 결합 됩니다.

**웹 응용 프로그램 (모델-뷰-컨트롤러)** (MVC) 및 **웹 응용 프로그램** (Razor Pages)의 인증 매개 변수와 비슷한 **각도** 및 **반응** 프로젝트 템플릿에 인증 매개 변수가 추가 되었습니다. 프로젝트 템플릿. 허용 되는 매개 변수 값은 **None** 및 **개인용**입니다. 현재 지점 **및 Redux** 프로젝트 템플릿은 인증 매개 변수를 지원 하지 않습니다.

## <a name="create-an-app-with-api-authorization-support"></a>API 권한 부여를 지 원하는 앱 만들기

사용자 인증 및 권한 부여는 모두와 함께 사용할 수 있습니다. 명령 셸을 열고 다음 명령을 실행 합니다.

**각도**:

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**반응**:

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

위의 명령은 SPA를 포함 하는 *ClientApp* 디렉터리를 사용 하 여 ASP.NET Core 앱을 만듭니다.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>앱의 ASP.NET Core 구성 요소에 대 한 일반적인 설명

다음 섹션에서는 인증 지원을 포함 하는 경우 프로젝트에 대 한 추가 설명입니다.

### <a name="startup-class"></a>Startup 클래스

`Startup` 클래스에는 다음과 같은 추가 기능이 있습니다.

* `Startup.ConfigureServices` 메서드 내에서:
  * 기본 UI를 사용 하는 id:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer IdentityServer 위에 몇 가지 기본 ASP.NET Core 규칙을 설정 하는 추가 `AddApiAuthorization` 도우미 메서드를 사용 합니다.

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * IdentityServer에서 생성 한 JWT 토큰의 유효성을 검사 하도록 앱을 구성 하는 추가 `AddIdentityServerJwt` 도우미 메서드를 사용 하 여 인증 합니다.

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure` 메서드 내에서:
  * 요청 자격 증명의 유효성을 검사 하 고 요청 컨텍스트에서 사용자를 설정 해야 하는 인증 미들웨어입니다.

    ```csharp
    app.UseAuthentication();
    ```

  * Open ID Connect 끝점을 노출 하는 IdentityServer 미들웨어입니다.

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

이 도우미 메서드는 지원 되는 구성을 사용 하도록 IdentityServer를 구성 합니다. IdentityServer는 앱 보안 문제를 처리 하기 위한 강력 하 고 확장 가능한 프레임 워크입니다. 이와 동시에는 가장 일반적인 시나리오에 대 한 불필요 한 복잡성을 노출 합니다. 따라서 규칙 집합 및 구성 옵션이 적절 한 시작 지점으로 간주 되는 사용자에 게 제공 됩니다. 인증을 변경 해야 하는 경우에는 요구 사항에 맞게 인증을 사용자 지정할 수 있는 IdentityServer의 모든 기능을 계속 사용할 수 있습니다.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

이 도우미 메서드는 앱의 정책 스키마를 기본 인증 처리기로 구성 합니다. 이 정책은 id URL 공간 "/Identity"의 하위 경로로 라우팅되는 모든 요청을 Id로 처리할 수 있도록 구성 됩니다. `JwtBearerHandler`는 다른 모든 요청을 처리 합니다. 또한이 메서드는 IdentityServer을 사용 하 여 `<<ApplicationName>>API` API 리소스를 기본 범위 `<<ApplicationName>>API`로 등록 하 고 JWT 전달자 토큰 미들웨어를 구성 하 여 IdentityServer에서 앱에 대해 발급 한 토큰의 유효성을 검사 합니다.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

*Controllers\WeatherForecastController.cs* 파일에서 리소스에 액세스 하기 위한 기본 정책에 따라 사용자에 게 권한을 부여 해야 함을 나타내는 클래스에 적용 되는 `[Authorize]` 특성을 확인 합니다. 기본 권한 부여 정책은 위에서 언급 한 정책 체계에 `AddIdentityServerJwt` 하 여 설정 된 기본 인증 체계를 사용 하도록 구성 되며, 해당 도우미 메서드에서 구성 된 `JwtBearerHandler`를 요청에 대 한 기본 처리기로 설정 합니다. 앱입니다.

### <a name="applicationdbcontext"></a>ApplicationDbContext

*Data\ApplicationDbContext.cs* 파일에서 IdentityServer에 대 한 스키마를 포함 하도록 `ApiAuthorizationDbContext` (`IdentityDbContext`에서 더 많이 파생 된 클래스)를 확장 하는 예외와 함께 id에서 동일한 `DbContext` 사용 됩니다.

데이터베이스 스키마에 대 한 모든 권한을 얻으려면 사용 가능한 Id `DbContext` 클래스 중 하나에서 상속 하 고 `OnModelCreating` 메서드에서 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`를 호출 하 여 Id 스키마를 포함 하도록 컨텍스트를 구성 합니다.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

*Controllers\OidcConfigurationController.cs* 파일에서 클라이언트가 사용 해야 하는 oidc 매개 변수를 제공 하도록 프로 비전 된 끝점을 확인 합니다.

### <a name="appsettingsjson"></a>appsettings.json

프로젝트 루트의 *appsettings* 파일에는 구성 된 클라이언트 목록을 설명 하는 새로운 `IdentityServer` 섹션이 있습니다. 다음 예제에는 단일 클라이언트가 있습니다. 클라이언트 이름은 앱 이름에 해당 하며 규칙에 따라 OAuth `ClientId` 매개 변수에 매핑됩니다. 프로필은 구성 중인 앱 유형을 나타냅니다. 서버에 대 한 구성 프로세스를 간소화 하는 규칙을 구동 하기 위해 내부적으로 사용 됩니다. [응용 프로그램 프로필](#application-profiles) 섹션에 설명 된 대로 몇 가지 프로필을 사용할 수 있습니다.

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings. 개발. json

Appsettings에 *있습니다.* 프로젝트 루트의 Development json 파일에는 토큰에 서명 하는 데 사용 되는 키를 설명 하는 `IdentityServer` 섹션이 있습니다. 프로덕션에 배포 하는 경우 [프로덕션에 배포](#deploy-to-production) 섹션에 설명 된 대로 키를 프로 비전 하 고 앱과 함께 배포 해야 합니다.

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>각도 앱에 대 한 일반적인 설명

각도 템플릿에서 인증 및 API 권한 부여 지원은 *Clientapp\src\api\ac-authentication* 디렉터리의 자체 각도 모듈에 있습니다. 모듈은 다음 요소로 구성 됩니다.

* 3 개 구성 요소:
  * *login. component. ts*: 앱의 로그인 흐름을 처리 합니다.
  * *logout*. s a t: 응용 프로그램의 로그 아웃 흐름을 처리 합니다.
  * *login-menu. component*: 다음 링크 집합 중 하나를 표시 하는 위젯:
    * 사용자가 인증 되 면 사용자 프로필 관리 및 로그 아웃 링크를 사용 합니다.
    * 사용자가 인증 되지 않은 경우 등록 및 로그인 링크.
* 경로에 추가할 수 있는 경로 가드 `AuthorizeGuard` 경로를 방문 하기 전에 사용자를 인증 해야 합니다.
* 사용자가 인증 될 때 API를 대상으로 하는 나가는 HTTP 요청에 액세스 토큰을 연결 하는 HTTP 인터셉터 `AuthorizeInterceptor`입니다.
* 인증 프로세스의 하위 수준 세부 정보를 처리 하 고, 인증 된 사용자에 대 한 정보를 앱의 나머지 부분에 노출 하는 서비스 `AuthorizeService`입니다.
* 앱의 인증 부분과 연결 된 경로를 정의 하는 각도 모듈입니다. 응용 프로그램의 나머지 부분에서 사용 하기 위해 로그인 메뉴 구성 요소, 인터셉터, 가드 및 서비스를 노출 합니다.

## <a name="general-description-of-the-react-app"></a>반응 앱에 대 한 일반적인 설명

반응 템플릿에서 인증 및 API 권한 부여에 대 한 지원은 *ClientApp\src\components\api-authorization* 디렉터리에 있습니다. 다음 요소로 구성 됩니다.

* 4 구성 요소:
  * *Login .js*: 앱의 로그인 흐름을 처리 합니다.
  * *Logout. js*: 응용 프로그램의 로그 아웃 흐름을 처리 합니다.
  * *LoginMenu*: 다음 링크 집합 중 하나를 표시 하는 위젯:
    * 사용자가 인증 되 면 사용자 프로필 관리 및 로그 아웃 링크를 사용 합니다.
    * 사용자가 인증 되지 않은 경우 등록 및 로그인 링크.
  * *AuthorizeRoute*: `Component` 매개 변수에 표시 된 구성 요소를 렌더링 하기 전에 사용자를 인증 해야 하는 경로 구성 요소입니다.
* 인증 프로세스의 하위 수준 세부 정보를 처리 하 고, 인증 된 사용자에 대 한 정보를 사용 하기 위해 나머지 앱에 노출 하는 클래스 `AuthorizeService`의 내보낸 `authService` 인스턴스입니다.

이제 솔루션의 주요 구성 요소를 살펴보았으므로 앱에 대 한 개별 시나리오를 자세히 살펴볼 수 있습니다.

## <a name="require-authorization-on-a-new-api"></a>새 API에 대 한 권한 부여 필요

기본적으로 시스템은 새 Api에 대 한 권한 부여를 쉽게 요구 하도록 구성 됩니다. 이렇게 하려면 새 컨트롤러를 만들고 컨트롤러 클래스 또는 컨트롤러 내의 모든 작업에 `[Authorize]` 특성을 추가 합니다.

## <a name="customize-the-api-authentication-handler"></a>API 인증 처리기 사용자 지정

API JWT 처리기의 구성을 사용자 지정 하려면 해당 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 인스턴스를 구성 합니다.

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.ConfigureOptions<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

## <a name="protect-a-client-side-route-angular"></a>클라이언트 쪽 경로 (각도) 보호

클라이언트 쪽 경로 보호는 경로를 구성할 때 실행할 가드 목록에 권한 부여 가드를 추가 하 여 수행 됩니다. 예를 들어, 기본 앱 각도 모듈 내에서 `fetch-data` 경로를 구성 하는 방법을 확인할 수 있습니다.

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

경로를 보호 하는 것은 실제 끝점 (여전히 적용 되는 `[Authorize]` 특성이 필요 함)을 보호 하지는 않지만 사용자가 인증 되지 않은 경우 지정 된 클라이언트 쪽 경로로 이동 하는 것을 방지 하는 것이 중요 합니다.

## <a name="authenticate-api-requests-angular"></a>API 요청 (각도) 인증

앱과 함께 호스트 되는 Api에 대 한 요청 인증은 앱에서 정의 된 HTTP 클라이언트 인터셉터를 사용 하 여 자동으로 수행 됩니다.

## <a name="protect-a-client-side-route-react"></a>클라이언트 쪽 경로 보호 (반응)

일반 `Route` 구성 요소 대신 `AuthorizeRoute` 구성 요소를 사용 하 여 클라이언트 쪽 경로를 보호 합니다. 예를 들어 `App` 구성 요소 내에서 `fetch-data` 경로를 구성 하는 방법을 확인할 수 있습니다.

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

경로 보호:

* 실제 끝점을 보호 하지 않습니다 (계속 적용 되는 `[Authorize]` 특성이 필요 함).
* 사용자가 인증 되지 않은 경우 지정 된 클라이언트 쪽 경로로 이동할 수 없습니다.

## <a name="authenticate-api-requests-react"></a>API 요청 인증 (반응)

응답을 사용 하 여 요청을 인증 하려면 먼저 `AuthorizeService`에서 `authService` 인스턴스를 가져와야 합니다. 액세스 토큰은 `authService`에서 검색 되 고 아래와 같이 요청에 연결 됩니다. 구성 요소에 반응 하는 경우이 작업은 일반적으로 `componentDidMount` 수명 주기 메서드 또는 일부 사용자 상호 작용의 결과로 수행 됩니다.

### <a name="import-the-authservice-into-your-component"></a>구성 요소로 authService 가져오기

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>액세스 토큰을 검색 하 고 응답에 연결 합니다.

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

프로덕션 환경에 앱을 배포 하려면 다음 리소스를 프로 비전 해야 합니다.

* Id 사용자 계정 및 IdentityServer 권한을 저장할 데이터베이스입니다.
* 토큰에 서명 하는 데 사용할 프로덕션 인증서입니다.
  * 이 인증서에 대 한 특정 요구 사항은 없습니다. 자체 서명 된 인증서 또는 CA 인증 기관을 통해 프로 비전 된 인증서 일 수 있습니다.
  * PowerShell 또는 OpenSSL 같은 표준 도구를 통해 생성할 수 있습니다.
  * 대상 컴퓨터의 인증서 저장소에 설치 하거나 강력한 암호를 사용 하 여 *.pfx* 파일로 배포할 수 있습니다.

### <a name="example-deploy-to-azure-websites"></a>예: Azure Websites에 배포

이 섹션에서는 인증서 저장소에 저장 된 인증서를 사용 하 여 Azure websites에 앱을 배포 하는 방법을 설명 합니다. 인증서 저장소에서 인증서를 로드 하도록 앱을 수정 하려면 이후 단계에서를 구성할 때 App Service 요금제가 최소한 표준 계층에 있어야 합니다. 앱의 *appsettings json* 파일에서 키 세부 정보를 포함 하도록 `IdentityServer` 섹션을 수정 합니다.

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

* 인증서의 이름 속성은 인증서의 고유 주체에 해당 합니다.
* 저장소 위치는 인증서를 로드할 위치 (`CurrentUser` 또는 `LocalMachine`)를 나타냅니다.
* 저장소 이름은 인증서가 저장 되는 인증서 저장소의 이름을 나타냅니다. 이 경우 개인 사용자 저장소를 가리킵니다.

Azure Websites에 배포 하려면 [azure에 앱 배포](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) 의 단계에 따라 앱을 배포 하 여 필요한 azure 리소스를 만들고 앱을 프로덕션에 배포 합니다.

위의 지침을 수행 하면 앱이 Azure에 배포 되지만 아직 작동 하지 않습니다. 앱에서 사용 하는 인증서를 아직 설정 해야 합니다. 사용할 인증서의 지문을 찾고 [인증서 로드](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)에 설명 된 단계를 따릅니다.

이러한 단계에서는 SSL을 언급 하지만 포털에는 앱에서 사용할 프로 비전 된 인증서를 업로드할 수 있는 **개인 인증서** 섹션이 있습니다.

이 단계를 수행한 후에는 앱을 다시 시작 하 고 작동 해야 합니다.

## <a name="other-configuration-options"></a>기타 구성 옵션

API 권한 부여에 대 한 지원은 규칙, 기본값 및 향상 된 기능 집합을 사용 하 여 IdentityServer를 기반으로 빌드됩니다. 물론 ASP.NET Core 통합이 시나리오에 포함 되지 않는 경우 IdentityServer의 전체 기능을 내부적으로 사용할 수 있습니다. ASP.NET Core 지원은 조직에서 모든 앱을 만들고 배포 하는 "자사" 앱에 중점을 두었습니다. 따라서 동의 또는 페더레이션과 같은 항목에 대 한 지원이 제공 되지 않습니다. 이러한 시나리오의 경우 IdentityServer를 사용 하 고 해당 설명서를 따릅니다.

### <a name="application-profiles"></a>응용 프로그램 프로필

응용 프로그램 프로필은 매개 변수를 추가로 정의 하는 앱에 대 한 미리 정의 된 구성입니다. 이번에는 다음 프로필이 지원 됩니다.

* `IdentityServerSPA`: 단일 단위로 IdentityServer와 함께 호스트 된 SPA를 나타냅니다.
  * `redirect_uri` 기본값은 `/authentication/login-callback`입니다.
  * `post_logout_redirect_uri` 기본값은 `/authentication/logout-callback`입니다.
  * 범위 집합에는 앱의 Api에 대해 정의 된 `openid`, `profile`및 모든 범위가 포함 됩니다.
  * 허용 되는 OIDC 응답 유형 집합은 `id_token token` 또는 개별적으로 (`id_token`, `token`)입니다.
  * 허용 되는 응답 모드가 `fragment`입니다.
* `SPA`: IdentityServer로 호스팅되지 않는 SPA를 나타냅니다.
  * 범위 집합에는 앱의 Api에 대해 정의 된 `openid`, `profile`및 모든 범위가 포함 됩니다.
  * 허용 되는 OIDC 응답 유형 집합은 `id_token token` 또는 개별적으로 (`id_token`, `token`)입니다.
  * 허용 되는 응답 모드가 `fragment`입니다.
* `IdentityServerJwt`: IdentityServer와 함께 호스트 되는 API를 나타냅니다.
  * 앱은 앱 이름으로 기본 설정 된 단일 범위를 갖도록 구성 됩니다.
* `API`: IdentityServer를 사용 하 여 호스팅되지 않는 API를 나타냅니다.
  * 앱은 앱 이름으로 기본 설정 된 단일 범위를 갖도록 구성 됩니다.

### <a name="configuration-through-appsettings"></a>AppSettings를 통한 구성

구성 시스템을 `Clients` 또는 `Resources`목록에 추가 하 여 앱을 구성 합니다.

다음 예제와 같이 각 클라이언트의 `redirect_uri` 및 `post_logout_redirect_uri` 속성을 구성 합니다.

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

리소스를 구성할 때 아래와 같이 리소스에 대 한 범위를 구성할 수 있습니다.

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

### <a name="configuration-through-code"></a>코드를 통한 구성

옵션을 구성 하는 작업을 수행 하는 `AddApiAuthorization` 오버 로드를 사용 하 여 코드를 통해 클라이언트와 리소스를 구성할 수도 있습니다.

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
* <xref:security/authentication/scaffold-identity>
