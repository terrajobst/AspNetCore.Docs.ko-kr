---
title: ASP.NET Core Blazor 인증 및 권한 부여
author: guardrex
description: Blazor 인증 및 권한 부여 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 04bbf20d1d848edfa98e719f316b790c812bfd95
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501319"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a><span data-ttu-id="09975-103">ASP.NET Core Blazor 인증 및 권한 부여</span><span class="sxs-lookup"><span data-stu-id="09975-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="09975-104">작성자: [Steve Sanderson](https://github.com/SteveSandersonMS) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="09975-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> <span data-ttu-id="09975-105">이 문서에서 Blazor WebAssembly에 적용되는 지침에는 ASP.NET Core Blazor WebAssembly 템플릿 버전 3.2 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-105">For the guidance in this article that applies to Blazor WebAssembly, the ASP.NET Core Blazor WebAssembly template version 3.2 or later is required.</span></span> <span data-ttu-id="09975-106">Visual Studio 버전 16.6 미리 보기 2 이상을 사용하지 않는 경우, <xref:blazor/get-started>의 지침에 따라 최신 Blazor WebAssembly 템플릿을 받으세요.</span><span class="sxs-lookup"><span data-stu-id="09975-106">If you aren't using Visual Studio version 16.6 Preview 2 or later, obtain the latest Blazor WebAssembly template by following the guidance in <xref:blazor/get-started>.</span></span>

<span data-ttu-id="09975-107">ASP.NET Core는 Blazor 앱의 보안 구성 및 관리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-107">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="09975-108">Blazor Server 및 Blazor WebAssembly 앱 간의 보안 시나리오는 서로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="09975-108">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="09975-109">Blazor 서버 앱은 서버에서 실행되기 때문에 권한 부여 확인을 통해 다음을 결정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-109">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="09975-110">사용자에게 표시되는 UI 옵션(예: 사용자가 사용할 수 있는 메뉴 항목)</span><span class="sxs-lookup"><span data-stu-id="09975-110">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="09975-111">앱 영역과 구성 요소의 액세스 규칙</span><span class="sxs-lookup"><span data-stu-id="09975-111">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="09975-112"> WebAssembly 앱은 클라이언트에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="09975-112"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="09975-113">권한 부여는 표시할 UI 옵션을 결정하는 ‘용도로만’ 사용됩니다. </span><span class="sxs-lookup"><span data-stu-id="09975-113">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="09975-114">사용자가 클라이언트 쪽 확인을 수정하거나 무시할 수 있기 때문에 Blazor WebAssembly 앱은 권한 부여 액세스 규칙을 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-114">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="09975-115">[Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization)은 라우팅 가능한 Razor 구성 요소에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-115">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="09975-116">라우팅할 수 없는 Razor 구성 요소가 [페이지에 포함](xref:blazor/integrate-components#render-components-from-a-page-or-view)된 경우 페이지의 권한 부여 규칙은 페이지 콘텐츠의 나머지 부분과 함께 Razor 구성 요소에 간접적으로 영향을 미칩니다.</span><span class="sxs-lookup"><span data-stu-id="09975-116">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="09975-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> 및 <xref:Microsoft.AspNetCore.Identity.UserManager%601>는 Razor 구성 요소에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="09975-118">인증</span><span class="sxs-lookup"><span data-stu-id="09975-118">Authentication</span></span>

Blazor<span data-ttu-id="09975-119">는 기존 ASP.NET Core 인증 메커니즘을 사용하여 사용자 ID를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-119"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="09975-120">정확한 메커니즘은 Blazor 앱이 호스트된 방식이 Blazor WebAssembly와 Blazor 서버 중 무엇인지에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="09975-120">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor<span data-ttu-id="09975-121"> WebAssembly 인증</span><span class="sxs-lookup"><span data-stu-id="09975-121"> WebAssembly authentication</span></span>

<span data-ttu-id="09975-122">Blazor WebAssembly 앱에서는 사용자가 클라이언트 쪽 코드를 모두 수정할 수 있기 때문에 인증 확인을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-122">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="09975-123">JavaScript SPA 프레임워크 또는 모든 운영 체제의 네이티브 앱을 포함하여 모든 클라이언트 쪽 앱 기술에는 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="09975-123">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="09975-124">다음을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-124">Add the following:</span></span>

* <span data-ttu-id="09975-125">[Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/)에 대한 패키지 참조를 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-125">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="09975-126">`Microsoft.AspNetCore.Components.Authorization` 네임스페이스를 앱의 *_Imports.razor* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-126">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="09975-127">인증을 처리하려면 이어지는 섹션에서 설명하는 기본 제공 또는 사용자 지정 `AuthenticationStateProvider` 서비스의 구현 방법을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09975-127">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="09975-128">앱 및 구성을 만드는 방법에 대한 자세한 내용은 <xref:security/blazor/webassembly/index> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09975-128">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="opno-locblazor-server-authentication"></a>Blazor<span data-ttu-id="09975-129"> 서버 인증</span><span class="sxs-lookup"><span data-stu-id="09975-129"> Server authentication</span></span>

Blazor<span data-ttu-id="09975-130"> 서버 앱은 SignalR를 사용하여 생성된 실시간 연결을 통해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-130"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="09975-131">[SignalR 기반 앱](xref:signalr/authn-and-authz)의 인증은 연결 시 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="09975-131">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="09975-132">인증은 쿠키 또는 다른 전달자 토큰을 기반으로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-132">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="09975-133">앱 및 구성을 만드는 방법에 대한 자세한 내용은 <xref:security/blazor/server> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09975-133">For more information on creating apps and configuration, see <xref:security/blazor/server>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="09975-134">AuthenticationStateProvider 서비스</span><span class="sxs-lookup"><span data-stu-id="09975-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="09975-135">기본 제공 `AuthenticationStateProvider` 서비스는 ASP.NET Core's `HttpContext.User`에서 인증 상태 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="09975-135">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="09975-136">이것이 바로 인증 상태가 기존의 ASP.NET Core 인증 메커니즘과 통합되는 방식입니다.</span><span class="sxs-lookup"><span data-stu-id="09975-136">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="09975-137">`AuthenticationStateProvider`는 `AuthorizeView` 구성 요소와 `CascadingAuthenticationState` 구성 요소가 인증 상태를 가져오는 데 사용하는 기본 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="09975-137">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="09975-138">일반적으로 `AuthenticationStateProvider`를 직접 사용하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-138">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="09975-139">이 문서의 뒷부분에서 설명하는 [AuthorizeView 구성 요소](#authorizeview-component) 또는 [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) 접근 방식을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="09975-139">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="09975-140">`AuthenticationStateProvider`를 직접 사용하는 경우의 주요 단점은 기본 인증 상태 데이터가 변경될 때 구성 요소가 자동으로 알림을 받지 못하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="09975-140">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="09975-141">`AuthenticationStateProvider` 서비스는 다음 예제와 같이 현재 사용자의 <xref:System.Security.Claims.ClaimsPrincipal> 데이터를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-141">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="09975-142">`user.Identity.IsAuthenticated`가 `true`이면, 사용자가 <xref:System.Security.Claims.ClaimsPrincipal>이므로 클레임을 열거하고 역할의 멤버 자격을 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-142">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="09975-143">DI(종속성 주입) 및 서비스에 대한 자세한 내용은 <xref:blazor/dependency-injection> 및 <xref:fundamentals/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09975-143">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="09975-144">사용자 지정 AuthenticationStateProvider 구현</span><span class="sxs-lookup"><span data-stu-id="09975-144">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="09975-145">앱에 사용자 지정 공급자가 필요한 경우 `AuthenticationStateProvider`를 구현하고 `GetAuthenticationStateAsync`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-145">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

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
```

<span data-ttu-id="09975-146">Blazor WebAssembly 앱에서 `CustomAuthStateProvider` 서비스는 *Program.cs*의 `Main`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="09975-146">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="09975-147">Blazor 서버 앱에서 `CustomAuthStateProvider` 서비스는 `Startup.ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="09975-147">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="09975-148">모든 사용자는 앞에 나온 예제의 `CustomAuthStateProvider`를 사용하여 사용자 이름 `mrfibuli`로 인증되었습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-148">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="09975-149">인증 상태를 연계 매개 변수로 공개</span><span class="sxs-lookup"><span data-stu-id="09975-149">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="09975-150">사용자가 실행한 작업을 수행하는 경우와 같이 절차적 논리에 인증 상태 데이터가 필요한 경우 `Task<AuthenticationState>` 형식의 연계 매개 변수를 정의하여 인증 상태 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="09975-150">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="09975-151">`user.Identity.IsAuthenticated`가 `true`이면, 클레임을 열거하고 역할의 멤버 자격을 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-151">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="09975-152">`App` 구성 요소(*App.razor*)에서 `AuthorizeRouteView` 및 `CascadingAuthenticationState` 구성 요소를 사용하여 `Task<AuthenticationState>` 연계 매개 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-152">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

```razor
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

<span data-ttu-id="09975-153">Blazor WebAssembly 앱에서 `Program.Main`에 옵션 및 인증 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-153">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="09975-154">Blazor 서버 앱에는 옵션 및 인증 서비스가 이미 있으므로 추가 작업이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-154">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="09975-155">권한 부여</span><span class="sxs-lookup"><span data-stu-id="09975-155">Authorization</span></span>

<span data-ttu-id="09975-156">사용자가 인증되면, 사용자가 수행할 수 있는 작업을 제어하기 위해 ‘권한 부여’ 규칙이 적용됩니다. </span><span class="sxs-lookup"><span data-stu-id="09975-156">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="09975-157">일반적으로 다음 여부에 따라 액세스가 허용 또는 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="09975-157">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="09975-158">사용자가 인증(로그인)되었는지 여부</span><span class="sxs-lookup"><span data-stu-id="09975-158">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="09975-159">사용자가 ‘역할’에 속하는지 여부 </span><span class="sxs-lookup"><span data-stu-id="09975-159">A user is in a *role*.</span></span>
* <span data-ttu-id="09975-160">사용자에게 ‘클레임’이 있는지 여부 </span><span class="sxs-lookup"><span data-stu-id="09975-160">A user has a *claim*.</span></span>
* <span data-ttu-id="09975-161">‘정책’이 충족되었는지 여부 </span><span class="sxs-lookup"><span data-stu-id="09975-161">A *policy* is satisfied.</span></span>

<span data-ttu-id="09975-162">이러한 각 개념은 ASP.NET Core MVC 또는 Razor Pages 앱에서와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-162">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="09975-163">ASP.NET Core 보안에 대한 자세한 내용은 [ASP.NET Core 보안 및 ID](xref:security/index)의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09975-163">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="09975-164">AuthorizeView 구성 요소</span><span class="sxs-lookup"><span data-stu-id="09975-164">AuthorizeView component</span></span>

<span data-ttu-id="09975-165">`AuthorizeView` 구성 요소는 사용자에게 볼 수 있는 권한이 있는지 여부에 따라 선택적으로 UI를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-165">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="09975-166">이 접근 방식은 사용자에게 데이터를 ‘표시’하기만 하면 되고 절차적 논리에 사용자 ID를 사용할 필요가 없는 경우에 유용합니다. </span><span class="sxs-lookup"><span data-stu-id="09975-166">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="09975-167">이 구성 요소는 로그인한 사용자 정보에 액세스하는 데 사용할 수 있는 `AuthenticationState` 형식의 `context` 변수를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-167">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="09975-168">사용자가 인증되지 않은 경우에 표시할 다른 콘텐츠를 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-168">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
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

<span data-ttu-id="09975-169">`AuthorizeView` 구성 요소는 `NavMenu` 구성 요소(*Shared/NavMenu.razor*)에서 `NavLink`에 대해 목록 항목(`<li>...</li>`)을 표시하는 데 사용될 수 있지만, 이 방법은 렌더링된 출력에서 목록 항목을 제거할 뿐이고</span><span class="sxs-lookup"><span data-stu-id="09975-169">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="09975-170">사용자가 해당 구성 요소로 이동하는 것을 방지하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-170">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="09975-171">`<Authorized>` 및 `<NotAuthorized>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-171">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="09975-172">UI 옵션이나 액세스를 제어하는 역할 또는 정책과 같은 권한 부여 조건은 [권한 부여](#authorization) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-172">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="09975-173">권한 부여 조건을 지정하지 않으면 `AuthorizeView`는 기본 정책을 사용하고 다음과 같이 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-173">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="09975-174">인증(로그인)된 사용자를 권한 있는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="09975-174">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="09975-175">인증되지 않은(로그아웃된) 사용자를 권한 없는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="09975-175">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="09975-176">역할 기반 및 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="09975-176">Role-based and policy-based authorization</span></span>

<span data-ttu-id="09975-177">`AuthorizeView` 구성 요소는 ‘역할 기반’ 또는 ‘정책 기반’ 권한 부여를 지원합니다.  </span><span class="sxs-lookup"><span data-stu-id="09975-177">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="09975-178">역할 기반 권한 부여의 경우 `Roles` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-178">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="09975-179">자세한 내용은 <xref:security/authorization/roles>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09975-179">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="09975-180">정책 기반 권한 부여의 경우 `Policy` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-180">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="09975-181">클레임 기반 권한 부여는 정책 기반 권한 부여의 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="09975-181">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="09975-182">예를 들어 사용자에게 특정 클레임이 있어야 하는 정책을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-182">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="09975-183">자세한 내용은 <xref:security/authorization/policies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09975-183">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="09975-184">이러한 API는 Blazor 서버나 Blazor WebAssembly 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-184">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="09975-185">`Roles` 또는 `Policy`를 지정하지 않으면 `AuthorizeView`는 기본 정책을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-185">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="09975-186">비동기 인증 중에 표시되는 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="09975-186">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="09975-187">에서는 인증 상태를 *비동기적으로* 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-187"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="09975-188">이 접근 방식의 주요 시나리오는 Blazor WebAssembly 앱이 외부 엔드포인트에 인증 요청을 하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="09975-188">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="09975-189">인증이 진행되는 동안 `AuthorizeView`는 기본적으로 아무 콘텐츠도 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-189">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="09975-190">인증 중에 콘텐츠를 표시하려면 `<Authorizing>` 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-190">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```razor
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

<span data-ttu-id="09975-191">이 접근 방식은 일반적으로 Blazor 서버 앱에 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-191">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="09975-192"> 서버 앱은 상태가 설정되는 즉시 인증 상태를 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-192"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="09975-193">Blazor 서버 앱의 `AuthorizeView` 구성 요소에 `Authorizing` 콘텐츠를 제공할 수는 있지만, 이 콘텐츠는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-193">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="09975-194">[Authorize] 특성</span><span class="sxs-lookup"><span data-stu-id="09975-194">[Authorize] attribute</span></span>

<span data-ttu-id="09975-195">`[Authorize]` 특성은 Razor 구성 요소에서 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-195">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="09975-196">Blazor 라우터를 통해 연결된 `@page` 구성 요소에서만 `[Authorize]`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-196">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="09975-197">권한 부여는 라우팅의 일부로만 수행되고, 페이지에 렌더링된 자식 구성 요소에 대해서는 수행되지 ‘않습니다’. </span><span class="sxs-lookup"><span data-stu-id="09975-197">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="09975-198">페이지 내의 특정 파트 표시 권한을 부여하려면 `AuthorizeView`를 대신 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-198">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="09975-199">`[Authorize]` 특성은 역할 기반 또는 정책 기반 권한 부여도 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-199">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="09975-200">역할 기반 권한 부여의 경우 `Roles` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-200">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="09975-201">정책 기반 권한 부여의 경우 `Policy` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-201">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="09975-202">`Roles` 또는 `Policy`를 지정하지 않으면 `[Authorize]`는 기본적으로 다음과 같이 처리하는 기본 정책을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-202">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="09975-203">인증(로그인)된 사용자를 권한 있는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="09975-203">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="09975-204">인증되지 않은(로그아웃된) 사용자를 권한 없는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="09975-204">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="09975-205">Router 구성 요소를 사용하여 권한 없는 콘텐츠 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="09975-205">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="09975-206">`AuthorizeRouteView` 구성 요소와 함께 `Router` 구성 요소를 사용하면 다음과 같은 경우 앱이 사용자 지정 콘텐츠를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-206">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="09975-207">콘텐츠를 찾을 수 없는 경우</span><span class="sxs-lookup"><span data-stu-id="09975-207">Content isn't found.</span></span>
* <span data-ttu-id="09975-208">사용자가 구성 요소에 적용된 `[Authorize]` 조건을 충족하지 못하는 경우.</span><span class="sxs-lookup"><span data-stu-id="09975-208">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="09975-209">`[Authorize]` 특성은 [`[Authorize]` 속성](#authorize-attribute) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-209">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="09975-210">비동기 인증이 진행 중인 경우</span><span class="sxs-lookup"><span data-stu-id="09975-210">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="09975-211">기본 Blazor 서버 프로젝트 템플릿에서 `App` 구성 요소(*App.razor*)는 사용자 지정 콘텐츠를 설정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="09975-211">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

```razor
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

<span data-ttu-id="09975-212">`<NotFound>`, `<NotAuthorized>` 및 `<Authorizing>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-212">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="09975-213">`<NotAuthorized>` 요소를 지정하지 않으면 `AuthorizeRouteView`는 다음 대체 메시지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-213">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="09975-214">인증 상태 변경 알림</span><span class="sxs-lookup"><span data-stu-id="09975-214">Notification about authentication state changes</span></span>

<span data-ttu-id="09975-215">예를 들어 사용자가 로그아웃했거나 다른 사용자가 해당 역할을 변경하여 기본 인증 상태 데이터가 변경되었음을 앱이 확인하면, [사용자 지정 AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider)는 필요에 따라 `AuthenticationStateProvider` 기본 클래스에서 `NotifyAuthenticationStateChanged` 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-215">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="09975-216">그러면 인증 상태 데이터의 소비자(예: `AuthorizeView`)가 새 데이터를 사용하여 다시 렌더링하라는 알림을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-216">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="09975-217">절차적 논리</span><span class="sxs-lookup"><span data-stu-id="09975-217">Procedural logic</span></span>

<span data-ttu-id="09975-218">앱이 절차적 논리의 일부로 권한 부여 규칙을 확인해야 하는 경우, `Task<AuthenticationState>` 형식의 연계 매개 변수를 사용하여 사용자의 <xref:System.Security.Claims.ClaimsPrincipal>을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="09975-218">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="09975-219">`IAuthorizationService` 등의 다른 서비스와 `Task<AuthenticationState>`를 결합하여 정책을 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-219">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
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
> <span data-ttu-id="09975-220">Blazor WebAssembly 앱 구성 요소에서 `Microsoft.AspNetCore.Authorization` 및 `Microsoft.AspNetCore.Components.Authorization` 네임스페이스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-220">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="09975-221">이러한 네임스페이스는 앱의 *_Imports.razor* 파일에 추가하여 전역적으로 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-221">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a><span data-ttu-id="09975-222">Blazor WebAssembly 앱의 권한 부여</span><span class="sxs-lookup"><span data-stu-id="09975-222">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="09975-223">Blazor WebAssembly 앱에서는 사용자가 클라이언트 쪽 코드를 모두 수정할 수 있기 때문에 권한 부여 확인을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09975-223">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="09975-224">JavaScript SPA 프레임워크 또는 모든 운영 체제의 네이티브 앱을 포함하여 모든 클라이언트 쪽 앱 기술에는 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="09975-224">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="09975-225">**항상 클라이언트 쪽 앱을 통해 액세스한 API 엔드포인트 내에서 서버의 권한 부여 확인을 수행합니다.**</span><span class="sxs-lookup"><span data-stu-id="09975-225">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="09975-226">자세한 내용은 <xref:security/blazor/webassembly/index>의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="09975-226">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="09975-227">오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="09975-227">Troubleshoot errors</span></span>

<span data-ttu-id="09975-228">일반적인 오류:</span><span class="sxs-lookup"><span data-stu-id="09975-228">Common errors:</span></span>

* <span data-ttu-id="09975-229">**권한을 부여하려면 Task\<AuthenticationState> 형식의 연계 매개 변수가 필요합니다. CascadingAuthenticationState를 사용하여 이 매개 변수를 제공하는 것이 좋습니다.**</span><span class="sxs-lookup"><span data-stu-id="09975-229">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="09975-230">**`authenticationStateTask`에 대해 `null` 값을 받았습니다.**</span><span class="sxs-lookup"><span data-stu-id="09975-230">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="09975-231">인증을 사용할 수 있는 Blazor 서버 템플릿으로 프로젝트를 만들지 않았을 가능성이 큽니다.</span><span class="sxs-lookup"><span data-stu-id="09975-231">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="09975-232">UI 트리의 일부를 `<CascadingAuthenticationState>`로 래핑합니다. 다음은 `App` 구성 요소(*App.razor*)를 래핑한 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="09975-232">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="09975-233">`CascadingAuthenticationState`는 기본 `AuthenticationStateProvider` DI 서비스로부터 받은 `Task<AuthenticationState>` 연계 매개 변수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="09975-233">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="09975-234">추가 자료</span><span class="sxs-lookup"><span data-stu-id="09975-234">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="09975-235">[Awesome Blazor: 인증](https://github.com/AdrienTorris/awesome-blazor#authentication) 커뮤니티 샘플 링크</span><span class="sxs-lookup"><span data-stu-id="09975-235">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
