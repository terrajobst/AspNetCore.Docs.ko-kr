---
title: ASP.NET Blazor 코어 웹어셈블리 추가 보안 시나리오
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 516132379ae20bd31c0f3b3261bb09b3f5b218f2
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501120"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="3da75-102">ASP.NET 코어 블레이저 웹어셈블리 추가 보안 시나리오</span><span class="sxs-lookup"><span data-stu-id="3da75-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="3da75-103">[하비에르 칼바로 넬슨](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="3da75-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="3da75-104">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="3da75-104">Request additional access tokens</span></span>

<span data-ttu-id="3da75-105">대부분의 앱은 사용하는 보호된 리소스와 상호 작용하기 위해 액세스 토큰만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="3da75-106">일부 시나리오에서 앱은 둘 이상의 리소스와 상호 작용하기 위해 둘 이상의 토큰이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="3da75-107">다음 예제에서는 앱에서 사용자 데이터를 읽고 메일을 보내려면 AAD(Azure Active Directory) Microsoft 그래프 API 범위가 추가로 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="3da75-108">Azure AAD 포털에서 Microsoft 그래프 API 권한을 추가한 후 추가 범위는 클라이언트`Program.Main`앱 *(Program.cs)* 에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="3da75-109">이 `IAccessTokenProvider.RequestToken` 메서드는 다음 예제와 같이 앱이 지정된 범위 집합으로 토큰을 프로비전할 수 있도록 하는 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="3da75-110">`TryGetToken`반환:</span><span class="sxs-lookup"><span data-stu-id="3da75-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="3da75-111">`true`사용할 `token` 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="3da75-112">`false`토큰이 검색되지 않는 경우.</span><span class="sxs-lookup"><span data-stu-id="3da75-112">`false` if the token isn't retrieved.</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="3da75-113">토큰 요청 오류 처리</span><span class="sxs-lookup"><span data-stu-id="3da75-113">Handle token request errors</span></span>

<span data-ttu-id="3da75-114">단일 페이지 응용 프로그램(SPA)이 개방형 ID 연결(OIDC)을 사용하여 사용자를 인증하면 사용자가 자격 증명을 제공한 결과로 설정된 세션 쿠키 의 형태로 SPA 내 및 ID 공급자(IP) 내에서 인증 상태가 로컬로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-114">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="3da75-115">IP가 사용자에 대해 보내는 토큰은 일반적으로 보통 약 1시간 동안 짧은 기간 동안 유효하므로 클라이언트 앱은 정기적으로 새 토큰을 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-115">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="3da75-116">그렇지 않으면 부여된 토큰이 만료된 후 사용자가 로그아웃됩니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-116">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="3da75-117">대부분의 경우 OIDC 클라이언트는 IP 내에 보관되는 인증 상태 또는 "세션"으로 인해 사용자가 다시 인증할 필요 없이 새 토큰을 프로비전할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-117">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="3da75-118">예를 들어 사용자가 IP에서 명시적으로 로그아웃하는 경우와 같은 경우와 같은 사용자 상호 작용 없이는 클라이언트가 토큰을 얻을 수 없는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-118">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="3da75-119">이 시나리오는 사용자가 `https://login.microsoftonline.com` 방문하여 로그아웃하는 경우에 발생합니다. 이러한 시나리오에서 앱은 사용자가 로그아웃했는지 즉시 알지 못합니다. 클라이언트가 보유하는 모든 토큰은 더 이상 유효하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-119">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="3da75-120">또한 클라이언트는 현재 토큰이 만료된 후 사용자 상호 작용 없이새 토큰을 프로비전할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-120">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="3da75-121">이러한 시나리오는 토큰 기반 인증에만 국한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-121">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="3da75-122">그들은 SLA의 특성의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-122">They are part of the nature of SPAs.</span></span> <span data-ttu-id="3da75-123">또한 쿠키를 사용하는 SPA는 인증 쿠키가 제거되면 서버 API를 호출하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-123">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="3da75-124">앱이 보호된 리소스에 대한 API 호출을 수행하는 경우 다음을 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-124">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="3da75-125">API를 호출할 새 액세스 토큰을 프로비전하려면 사용자가 다시 인증해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-125">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="3da75-126">클라이언트에 유효한 것으로 보이는 토큰이 있더라도 사용자가 토큰을 해지했기 때문에 서버에 대한 호출이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-126">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="3da75-127">앱에서 토큰을 요청하면 두 가지 가능한 결과가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-127">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="3da75-128">요청이 성공하고 앱에 유효한 토큰이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-128">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="3da75-129">요청이 실패하고 앱이 새 토큰을 얻으려면 사용자를 다시 인증해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-129">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="3da75-130">토큰 요청이 실패하면 리디렉션을 수행하기 전에 현재 상태를 저장할지 여부를 결정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-130">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="3da75-131">복잡성 수준이 증가함에 따라 다음과 같은 몇 가지 접근 방식이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-131">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="3da75-132">세션 저장소에 현재 페이지 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-132">Store the current page state in session storage.</span></span> <span data-ttu-id="3da75-133">중에 `OnInitializeAsync`계속하기 전에 상태를 복원할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-133">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="3da75-134">쿼리 문자열 매개 변수를 추가하고 이전에 저장된 상태를 다시 하이드레이트해야 한다는 앱을 알리는 방법으로 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-134">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="3da75-135">고유한 식별자가 있는 쿼리 문자열 매개 변수를 추가하여 다른 항목과의 충돌 위험 없이 세션 저장소에 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-135">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="3da75-136">아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-136">The following example shows how to:</span></span>

* <span data-ttu-id="3da75-137">로그인 페이지로 리디렉션하기 전에 상태를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-137">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="3da75-138">쿼리 문자열 매개 변수를 사용하여 이후 인증을 이전 상태를 복구합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-138">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="3da75-139">인증 작업 전에 앱 상태 저장</span><span class="sxs-lookup"><span data-stu-id="3da75-139">Save app state before an authentication operation</span></span>

<span data-ttu-id="3da75-140">인증 작업 중에 브라우저가 IP로 리디렉션되기 전에 앱 상태를 저장하려는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-140">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="3da75-141">상태 컨테이너와 같은 것을 사용하고 인증이 성공한 후 상태를 복원하려는 경우이러한 경우가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-141">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="3da75-142">사용자 지정 인증 상태 개체를 사용하여 앱 별 상태 또는 해당 상태에 대한 참조를 유지하고 인증 작업이 성공적으로 완료되면 해당 상태를 복원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-142">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="3da75-143">`Authentication`구성*요소(페이지/인증.razor):*</span><span class="sxs-lookup"><span data-stu-id="3da75-143">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a><span data-ttu-id="3da75-144">앱 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="3da75-144">Customize app routes</span></span>

<span data-ttu-id="3da75-145">기본적으로 라이브러리는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 다른 인증 상태를 나타내기 위해 다음 표에 표시된 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-145">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="3da75-146">라우팅</span><span class="sxs-lookup"><span data-stu-id="3da75-146">Route</span></span>                            | <span data-ttu-id="3da75-147">목적</span><span class="sxs-lookup"><span data-stu-id="3da75-147">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="3da75-148">로그인 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-148">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="3da75-149">로그인 작업의 결과를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-149">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="3da75-150">어떤 이유로 로그인 작업이 실패하면 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-150">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="3da75-151">로그아웃 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-151">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="3da75-152">로그아웃 작업의 결과를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-152">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="3da75-153">어떤 이유로 로그아웃 작업이 실패하면 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-153">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="3da75-154">사용자가 로그아웃에 성공했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-154">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="3da75-155">사용자 프로필을 편집하는 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-155">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="3da75-156">새 사용자를 등록하는 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-156">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="3da75-157">앞의 표에 표시된 경로는 을 `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`통해 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-157">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="3da75-158">사용자 지정 경로를 제공하는 옵션을 설정할 때 앱에 각 경로를 처리하는 경로가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-158">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="3da75-159">다음 예제에서는 모든 경로에 접두사입니다. `/security`</span><span class="sxs-lookup"><span data-stu-id="3da75-159">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="3da75-160">`Authentication`구성*요소(페이지/인증.razor):*</span><span class="sxs-lookup"><span data-stu-id="3da75-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="3da75-161">`Program.Main`*(Program.cs):*</span><span class="sxs-lookup"><span data-stu-id="3da75-161">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="3da75-162">요구 사항이 완전히 다른 경로를 요구하는 경우 앞서 설명한 `RemoteAuthenticatorView` 대로 경로를 설정하고 명시적 작업 매개 변수를 사용하여 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-162">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="3da75-163">이렇게 선택한 경우 UI를 다른 페이지로 나누도록 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-163">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="3da75-164">인증 사용자 인터페이스 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="3da75-164">Customize the authentication user interface</span></span>

<span data-ttu-id="3da75-165">`RemoteAuthenticatorView`는 각 인증 상태에 대한 기본 UI 조각 집합을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-165">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="3da75-166">각 상태는 사용자 지정을 전달하여 사용자 지정할 `RenderFragment`수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-166">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="3da75-167">초기 로그인 프로세스 중에 표시된 텍스트를 사용자 지정하려면 `RemoteAuthenticatorView` 다음과 같이 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-167">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="3da75-168">`Authentication`구성*요소(페이지/인증.razor):*</span><span class="sxs-lookup"><span data-stu-id="3da75-168">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="3da75-169">는 `RemoteAuthenticatorView` 다음 표에 표시된 인증 경로당 사용할 수 있는 하나의 조각이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3da75-169">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="3da75-170">라우팅</span><span class="sxs-lookup"><span data-stu-id="3da75-170">Route</span></span>                            | <span data-ttu-id="3da75-171">조각</span><span class="sxs-lookup"><span data-stu-id="3da75-171">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |
