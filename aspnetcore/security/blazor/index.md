---
title: ASP.NET Core Blazor 인증 및 권한 부여
author: guardrex
description: Blazor 인증 및 권한 부여 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 693ac1a5b5bcaf8a9bbf0ff9ab63fb41764e3888
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880451"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a>ASP.NET Core Blazor 인증 및 권한 부여

작성자: [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

ASP.NET Core는 Blazor 앱의 보안 구성 및 관리를 지원합니다.

Blazor Server 및 Blazor WebAssembly 앱 간의 보안 시나리오는 서로 다릅니다. Blazor 서버 앱은 서버에서 실행되기 때문에 권한 부여 확인을 통해 다음을 결정할 수 있습니다.

* 사용자에게 표시되는 UI 옵션(예: 사용자가 사용할 수 있는 메뉴 항목)
* 앱 영역과 구성 요소의 액세스 규칙

Blazor WebAssembly 앱은 클라이언트에서 실행됩니다. 권한 부여는 표시할 UI 옵션을 결정하는 ‘용도로만’ 사용됩니다.  사용자가 클라이언트 쪽 확인을 수정하거나 무시할 수 있기 때문에 Blazor WebAssembly 앱은 권한 부여 액세스 규칙을 적용할 수 없습니다.

## <a name="authentication"></a>인증

Blazor는 기존 ASP.NET Core 인증 메커니즘을 사용하여 사용자 ID를 설정합니다. 정확한 메커니즘은 Blazor 앱을 호스트하는 방법, 즉 Blazor 서버인지 또는 Blazor WebAssembly인지에 따라 다릅니다.

### <a name="opno-locblazor-server-authentication"></a>Blazor 서버 인증

Blazor 서버 앱은 SignalR를 사용하여 생성된 실시간 연결을 통해 작동합니다. [SignalR 기반 앱](xref:signalr/authn-and-authz)의 인증은 연결 시 처리됩니다. 인증은 쿠키 또는 다른 전달자 토큰을 기반으로 할 수 있습니다.

프로젝트를 만들 때 Blazor 서버 프로젝트 템플릿에서 자동으로 인증을 설정할 수 있습니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<xref:blazor/get-started> 문서의 Visual Studio 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.

**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor 서버 앱** 템플릿을 선택한 후 **인증**에서 **변경**을 선택합니다.

다른 ASP.NET Core 프로젝트에 사용할 수 있는 것과 동일한 인증 메커니즘 세트를 제공하는 대화 상자가 열립니다.

* **인증 없음**
* **개별 사용자 계정** &ndash; 사용자 계정은 다음과 같이 저장할 수 있습니다.
  * ASP.NET Core의 [ID](xref:security/authentication/identity) 시스템을 사용하여 앱에 저장
  * [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 사용
* **회사 또는 학교 계정**
* **Windows 인증**

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<xref:blazor/get-started> 문서의 Visual Studio Code 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.

| 인증 메커니즘                                                                 | `{AUTHENTICATION}` 값 |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| 인증 없음                                                                        | `None`                   |
| 개인<br>ASP.NET Core ID를 사용하여 앱에 저장된 사용자                        | `Individual`             |
| 개인<br>[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자 | `IndividualB2C`          |
| 회사 또는 학교 계정<br>단일 테넌트에 대한 조직 인증            | `SingleOrg`              |
| 회사 또는 학교 계정<br>여러 테넌트에 대한 조직 인증           | `MultiOrg`               |
| Windows 인증                                                                   | `Windows`                |

이 명령은 `{APP NAME}` 자리 표시자에 제공된 값으로 이름을 지정하여 폴더를 만들고, 폴더 이름을 앱 이름으로 사용합니다. 자세한 내용은 .NET Core 가이드의 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.

| Authentication mechanism                                                                 | `{AUTHENTICATION}` value |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| No Authentication                                                                        | `None`                   |
| Individual<br>Users stored in the app with ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Work or School Accounts<br>Organizational authentication for a single tenant.            | `SingleOrg`              |
| Work or School Accounts<br>Organizational authentication for multiple tenants.           | `MultiOrg`               |
| Windows Authentication                                                                   | `Windows`                |

The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name. For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.

-->

---

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor WebAssembly 인증

Blazor WebAssembly 앱에서는 사용자가 클라이언트 쪽 코드를 모두 수정할 수 있기 때문에 인증 확인을 무시할 수 있습니다. JavaScript SPA 프레임워크 또는 모든 운영 체제의 네이티브 앱을 포함하여 모든 클라이언트 쪽 앱 기술에는 동일하게 적용됩니다.

[Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/)에 대한 패키지 참조를 앱의 프로젝트 파일에 추가합니다.

Blazor WebAssembly 앱에 대한 사용자 지정 `AuthenticationStateProvider` 서비스 구현은 다음 섹션에서 설명합니다.

## <a name="authenticationstateprovider-service"></a>AuthenticationStateProvider 서비스

Blazor 서버 앱에는 ASP.NET Core의 `HttpContext.User`에서 인증 상태 데이터를 가져오는 `AuthenticationStateProvider` 서비스가 기본 제공됩니다. 이런 방법으로 인증 상태가 기존의 ASP.NET Core 서버 쪽 인증 메커니즘과 통합됩니다.

`AuthenticationStateProvider`는 `AuthorizeView` 구성 요소와 `CascadingAuthenticationState` 구성 요소가 인증 상태를 가져오는 데 사용하는 기본 서비스입니다.

일반적으로 `AuthenticationStateProvider`를 직접 사용하지는 않습니다. [AuthorizeView 구성 요소](#authorizeview-component) 또는 이 문서의 뒷부분에서 설명하는 [Task<AuthenticationState> ](#expose-the-authentication-state-as-a-cascading-parameter) 접근 방식을 사용합니다. `AuthenticationStateProvider`를 직접 사용하는 경우의 주요 단점은 기본 인증 상태 데이터가 변경될 때 구성 요소가 자동으로 알림을 받지 못하는 것입니다.

`AuthenticationStateProvider` 서비스는 다음 예제와 같이 현재 사용자의 <xref:System.Security.Claims.ClaimsPrincipal> 데이터를 제공할 수 있습니다.

```cshtml
@page "/"
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="@LogUsername">Write user info to console</button>

@code {
    private async Task LogUsername()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

`user.Identity.IsAuthenticated`가 `true`이면, 사용자가 <xref:System.Security.Claims.ClaimsPrincipal>이므로 클레임을 열거하고 역할의 멤버 자격을 평가할 수 있습니다.

DI(종속성 주입) 및 서비스에 대한 자세한 내용은 <xref:blazor/dependency-injection> 및 <xref:fundamentals/dependency-injection>을 참조하세요.

## <a name="implement-a-custom-authenticationstateprovider"></a>사용자 지정 AuthenticationStateProvider 구현

Blazor WebAssembly 앱을 빌드하는 경우 또는 앱의 사양에 사용자 지정 공급자가 필요한 경우, 공급자를 구현하고 `GetAuthenticationStateAsync`를 재정의합니다.

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

namespace BlazorSample.Services
{
    public class CustomAuthStateProvider : AuthenticationStateProvider
    {
        public override Task<AuthenticationState> GetAuthenticationStateAsync()
        {
            var identity = new ClaimsIdentity(new[]
            {
                new Claim(ClaimTypes.Name, "mrfibuli"),
            }, "Fake authentication type");

            var user = new ClaimsPrincipal(identity);

            return Task.FromResult(new AuthenticationState(user));
        }
    }
}
```

`CustomAuthStateProvider` 서비스는 `Startup.ConfigureServices`에 등록됩니다.

```csharp
// using Microsoft.AspNetCore.Components.Authorization;
// using BlazorSample.Services;

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

`CustomAuthStateProvider`를 사용하면 모든 사용자가 사용자 이름 `mrfibuli`로 인증됩니다.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>인증 상태를 연계 매개 변수로 공개

사용자가 실행한 작업을 수행하는 경우와 같이 절차적 논리에 인증 상태 데이터가 필요한 경우 `Task<AuthenticationState>` 형식의 연계 매개 변수를 정의하여 인증 상태 데이터를 가져옵니다.

```cshtml
@page "/"

<button @onclick="@LogUsername">Log username</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

> [!NOTE]
> Blazor WebAssembly 앱 구성 요소에서 `Microsoft.AspNetCore.Components.Authorization` 네임스페이스(`@using Microsoft.AspNetCore.Components.Authorization`)를 추가합니다.

`user.Identity.IsAuthenticated`가 `true`이면, 클레임을 열거하고 역할의 멤버 자격을 평가할 수 있습니다.

`AuthorizeRouteView` 및 `CascadingAuthenticationState` 구성 요소를 사용하여 `Task<AuthenticationState>` 연계 매개 변수를 설정합니다.

```cshtml
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

## <a name="authorization"></a>권한 부여

사용자가 인증되면, 사용자가 수행할 수 있는 작업을 제어하기 위해 ‘권한 부여’ 규칙이 적용됩니다. 

일반적으로 다음 여부에 따라 액세스가 허용 또는 거부됩니다.

* 사용자가 인증(로그인)되었는지 여부
* 사용자가 ‘역할’에 속하는지 여부 
* 사용자에게 ‘클레임’이 있는지 여부 
* ‘정책’이 충족되었는지 여부 

이러한 각 개념은 ASP.NET Core MVC 또는 Razor Pages 앱에서와 동일합니다. ASP.NET Core 보안에 대한 자세한 내용은 [ASP.NET Core 보안 및 ID](xref:security/index)의 문서를 참조하세요.

## <a name="authorizeview-component"></a>AuthorizeView 구성 요소

`AuthorizeView` 구성 요소는 사용자에게 볼 수 있는 권한이 있는지 여부에 따라 선택적으로 UI를 표시합니다. 이 접근 방식은 사용자에게 데이터를 ‘표시’하기만 하면 되고 절차적 논리에 사용자 ID를 사용할 필요가 없는 경우에 유용합니다. 

이 구성 요소는 로그인한 사용자 정보에 액세스하는 데 사용할 수 있는 `AuthenticationState` 형식의 `context` 변수를 공개합니다.

```cshtml
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

사용자가 인증되지 않은 경우에 표시할 다른 콘텐츠를 제공할 수도 있습니다.

```cshtml
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

`<Authorized>` 및 `<NotAuthorized>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목이 포함될 수 있습니다.

UI 옵션이나 액세스를 제어하는 역할 또는 정책과 같은 권한 부여 조건은 [권한 부여](#authorization) 섹션에서 설명합니다.

권한 부여 조건을 지정하지 않으면 `AuthorizeView`는 기본 정책을 사용하고 다음과 같이 처리합니다.

* 인증(로그인)된 사용자를 권한 있는 사용자로 처리
* 인증되지 않은(로그아웃된) 사용자를 권한 없는 사용자로 처리

### <a name="role-based-and-policy-based-authorization"></a>역할 기반 및 정책 기반 권한 부여

`AuthorizeView` 구성 요소는 ‘역할 기반’ 또는 ‘정책 기반’ 권한 부여를 지원합니다.  

역할 기반 권한 부여의 경우 `Roles` 매개 변수를 사용합니다.

```cshtml
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

자세한 내용은 <xref:security/authorization/roles>을 참조하세요.

정책 기반 권한 부여의 경우 `Policy` 매개 변수를 사용합니다.

```cshtml
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

클레임 기반 권한 부여는 정책 기반 권한 부여의 특별한 경우입니다. 예를 들어 사용자에게 특정 클레임이 있어야 하는 정책을 정의할 수 있습니다. 자세한 내용은 <xref:security/authorization/policies>을 참조하세요.

이러한 API는 Blazor 서버나 Blazor WebAssembly 앱에서 사용할 수 있습니다.

`Roles` 또는 `Policy`를 지정하지 않으면 `AuthorizeView`는 기본 정책을 사용합니다.

### <a name="content-displayed-during-asynchronous-authentication"></a>비동기 인증 중에 표시되는 콘텐츠

Blazor에서는 인증 상태를 *비동기적으로* 확인할 수 있습니다. 이 접근 방식의 주요 시나리오는 Blazor WebAssembly 앱이 외부 엔드포인트에 인증 요청을 하는 경우입니다.

인증이 진행되는 동안 `AuthorizeView`는 기본적으로 아무 콘텐츠도 표시하지 않습니다. 인증 중에 콘텐츠를 표시하려면 `<Authorizing>` 요소를 사용합니다.

```cshtml
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

이 접근 방식은 일반적으로 Blazor 서버 앱에 적용할 수 없습니다. Blazor 서버 앱은 상태가 설정되는 즉시 인증 상태를 알 수 있습니다. Blazor 서버 앱의 `AuthorizeView` 구성 요소에 `Authorizing` 콘텐츠를 제공할 수는 있지만, 이 콘텐츠는 표시되지 않습니다.

## <a name="authorize-attribute"></a>[Authorize] 특성

`[Authorize]` 특성은 Razor 구성 요소에서 사용될 수 있습니다.

```cshtml
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!NOTE]
> Blazor WebAssembly 앱 구성 요소에서 `Microsoft.AspNetCore.Authorization` 네임스페이스(`@using Microsoft.AspNetCore.Authorization`)를 이 섹션의 예제에 추가합니다.

> [!IMPORTANT]
> Blazor 라우터를 통해 연결된 `@page` 구성 요소에서만 `[Authorize]`를 사용합니다. 권한 부여는 라우팅의 일부로만 수행되고, 페이지에 렌더링된 자식 구성 요소에 대해서는 수행되지 ‘않습니다’.  페이지 내의 특정 파트 표시 권한을 부여하려면 `AuthorizeView`를 대신 사용합니다.

`[Authorize]` 특성은 역할 기반 또는 정책 기반 권한 부여도 지원합니다. 역할 기반 권한 부여의 경우 `Roles` 매개 변수를 사용합니다.

```cshtml
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

정책 기반 권한 부여의 경우 `Policy` 매개 변수를 사용합니다.

```cshtml
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

`Roles` 또는 `Policy`를 지정하지 않으면 `[Authorize]`는 기본적으로 다음과 같이 처리하는 기본 정책을 사용합니다.

* 인증(로그인)된 사용자를 권한 있는 사용자로 처리
* 인증되지 않은(로그아웃된) 사용자를 권한 없는 사용자로 처리

## <a name="customize-unauthorized-content-with-the-router-component"></a>Router 구성 요소를 사용하여 권한 없는 콘텐츠 사용자 지정

`AuthorizeRouteView` 구성 요소와 함께 `Router` 구성 요소를 사용하면 다음과 같은 경우 앱이 사용자 지정 콘텐츠를 지정할 수 있습니다.

* 콘텐츠를 찾을 수 없는 경우
* 사용자가 구성 요소에 적용된 `[Authorize]` 조건을 충족하지 못하는 경우. `[Authorize]` 특성은 [`[Authorize]` 속성](#authorize-attribute) 섹션에서 설명합니다.
* 비동기 인증이 진행 중인 경우

기본 Blazor 서버 프로젝트 템플릿에서 *App.razor* 파일은 사용자 지정 콘텐츠를 설정하는 방법을 보여 줍니다.

```cshtml
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

`<NotFound>`, `<NotAuthorized>` 및 `<Authorizing>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목이 포함될 수 있습니다.

`<NotAuthorized>` 요소를 지정하지 않으면 `AuthorizeRouteView`는 다음 대체 메시지를 사용합니다.

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>인증 상태 변경 알림

예를 들어 사용자가 로그아웃했거나 다른 사용자가 해당 역할을 변경하여 기본 인증 상태 데이터가 변경되었음을 앱이 확인하면, 사용자 지정 `AuthenticationStateProvider`는 필요에 따라 `AuthenticationStateProvider` 기본 클래스에서 `NotifyAuthenticationStateChanged` 메서드를 호출할 수 있습니다. 그러면 인증 상태 데이터의 소비자(예: `AuthorizeView`)가 새 데이터를 사용하여 다시 렌더링하라는 알림을 받습니다.

## <a name="procedural-logic"></a>절차적 논리

앱이 절차적 논리의 일부로 권한 부여 규칙을 확인해야 하는 경우, `Task<AuthenticationState>` 형식의 연계 매개 변수를 사용하여 사용자의 <xref:System.Security.Claims.ClaimsPrincipal>을 가져옵니다. `IAuthorizationService` 등의 다른 서비스와 `Task<AuthenticationState>`를 결합하여 정책을 평가할 수 있습니다.

```cshtml
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> Blazor WebAssembly 앱 구성 요소에서 `Microsoft.AspNetCore.Authorization` 및 `Microsoft.AspNetCore.Components.Authorization` 네임스페이스를 추가합니다.
>
> ```cshtml
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a>Blazor WebAssembly 앱의 권한 부여

Blazor WebAssembly 앱에서는 사용자가 클라이언트 쪽 코드를 모두 수정할 수 있기 때문에 권한 부여 확인을 무시할 수 있습니다. JavaScript SPA 프레임워크 또는 모든 운영 체제의 네이티브 앱을 포함하여 모든 클라이언트 쪽 앱 기술에는 동일하게 적용됩니다.

**항상 클라이언트 쪽 앱을 통해 액세스한 API 엔드포인트 내에서 서버의 권한 부여 확인을 수행합니다.**

## <a name="troubleshoot-errors"></a>오류 문제 해결

일반적인 오류:

* **권한을 부여하려면 Task\<AuthenticationState> 형식의 연계 매개 변수가 필요합니다. CascadingAuthenticationState를 사용하여 이 매개 변수를 제공하는 것이 좋습니다.**

* **`authenticationStateTask`에 대해 `null` 값을 받았습니다.**

인증을 사용할 수 있는 Blazor 서버 템플릿으로 프로젝트를 만들지 않았을 가능성이 큽니다. *App.razor* 등의 UI 트리 일부를 `<CascadingAuthenticationState>`로 다음과 같이 래핑합니다.

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

`CascadingAuthenticationState`는 기본 `AuthenticationStateProvider` DI 서비스로부터 받은 `Task<AuthenticationState>` 연계 매개 변수를 제공합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
