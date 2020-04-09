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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET 코어 블레이저 웹어셈블리 추가 보안 시나리오

[하비에르 칼바로 넬슨](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a>추가 액세스 토큰 요청

대부분의 앱은 사용하는 보호된 리소스와 상호 작용하기 위해 액세스 토큰만 필요합니다. 일부 시나리오에서 앱은 둘 이상의 리소스와 상호 작용하기 위해 둘 이상의 토큰이 필요할 수 있습니다.

다음 예제에서는 앱에서 사용자 데이터를 읽고 메일을 보내려면 AAD(Azure Active Directory) Microsoft 그래프 API 범위가 추가로 필요합니다. Azure AAD 포털에서 Microsoft 그래프 API 권한을 추가한 후 추가 범위는 클라이언트`Program.Main`앱 *(Program.cs)* 에서 구성됩니다.

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

이 `IAccessTokenProvider.RequestToken` 메서드는 다음 예제와 같이 앱이 지정된 범위 집합으로 토큰을 프로비전할 수 있도록 하는 오버로드를 제공합니다.

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

`TryGetToken`반환:

* `true`사용할 `token` 수 있습니다.
* `false`토큰이 검색되지 않는 경우.

## <a name="handle-token-request-errors"></a>토큰 요청 오류 처리

단일 페이지 응용 프로그램(SPA)이 개방형 ID 연결(OIDC)을 사용하여 사용자를 인증하면 사용자가 자격 증명을 제공한 결과로 설정된 세션 쿠키 의 형태로 SPA 내 및 ID 공급자(IP) 내에서 인증 상태가 로컬로 유지됩니다.

IP가 사용자에 대해 보내는 토큰은 일반적으로 보통 약 1시간 동안 짧은 기간 동안 유효하므로 클라이언트 앱은 정기적으로 새 토큰을 가져와야 합니다. 그렇지 않으면 부여된 토큰이 만료된 후 사용자가 로그아웃됩니다. 대부분의 경우 OIDC 클라이언트는 IP 내에 보관되는 인증 상태 또는 "세션"으로 인해 사용자가 다시 인증할 필요 없이 새 토큰을 프로비전할 수 있습니다.

예를 들어 사용자가 IP에서 명시적으로 로그아웃하는 경우와 같은 경우와 같은 사용자 상호 작용 없이는 클라이언트가 토큰을 얻을 수 없는 경우가 있습니다. 이 시나리오는 사용자가 `https://login.microsoftonline.com` 방문하여 로그아웃하는 경우에 발생합니다. 이러한 시나리오에서 앱은 사용자가 로그아웃했는지 즉시 알지 못합니다. 클라이언트가 보유하는 모든 토큰은 더 이상 유효하지 않을 수 있습니다. 또한 클라이언트는 현재 토큰이 만료된 후 사용자 상호 작용 없이새 토큰을 프로비전할 수 없습니다.

이러한 시나리오는 토큰 기반 인증에만 국한되지 않습니다. 그들은 SLA의 특성의 일부입니다. 또한 쿠키를 사용하는 SPA는 인증 쿠키가 제거되면 서버 API를 호출하지 못합니다.

앱이 보호된 리소스에 대한 API 호출을 수행하는 경우 다음을 알고 있어야 합니다.

* API를 호출할 새 액세스 토큰을 프로비전하려면 사용자가 다시 인증해야 할 수 있습니다.
* 클라이언트에 유효한 것으로 보이는 토큰이 있더라도 사용자가 토큰을 해지했기 때문에 서버에 대한 호출이 실패할 수 있습니다.

앱에서 토큰을 요청하면 두 가지 가능한 결과가 있습니다.

* 요청이 성공하고 앱에 유효한 토큰이 있습니다.
* 요청이 실패하고 앱이 새 토큰을 얻으려면 사용자를 다시 인증해야 합니다.

토큰 요청이 실패하면 리디렉션을 수행하기 전에 현재 상태를 저장할지 여부를 결정해야 합니다. 복잡성 수준이 증가함에 따라 다음과 같은 몇 가지 접근 방식이 존재합니다.

* 세션 저장소에 현재 페이지 상태를 저장합니다. 중에 `OnInitializeAsync`계속하기 전에 상태를 복원할 수 있는지 확인합니다.
* 쿼리 문자열 매개 변수를 추가하고 이전에 저장된 상태를 다시 하이드레이트해야 한다는 앱을 알리는 방법으로 이 매개 변수를 사용합니다.
* 고유한 식별자가 있는 쿼리 문자열 매개 변수를 추가하여 다른 항목과의 충돌 위험 없이 세션 저장소에 데이터를 저장합니다.

아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.

* 로그인 페이지로 리디렉션하기 전에 상태를 유지합니다.
* 쿼리 문자열 매개 변수를 사용하여 이후 인증을 이전 상태를 복구합니다.

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

## <a name="save-app-state-before-an-authentication-operation"></a>인증 작업 전에 앱 상태 저장

인증 작업 중에 브라우저가 IP로 리디렉션되기 전에 앱 상태를 저장하려는 경우가 있습니다. 상태 컨테이너와 같은 것을 사용하고 인증이 성공한 후 상태를 복원하려는 경우이러한 경우가 있을 수 있습니다. 사용자 지정 인증 상태 개체를 사용하여 앱 별 상태 또는 해당 상태에 대한 참조를 유지하고 인증 작업이 성공적으로 완료되면 해당 상태를 복원할 수 있습니다.

`Authentication`구성*요소(페이지/인증.razor):*

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

## <a name="customize-app-routes"></a>앱 경로 사용자 지정

기본적으로 라이브러리는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 다른 인증 상태를 나타내기 위해 다음 표에 표시된 경로를 사용합니다.

| 라우팅                            | 목적 |
| -------------------------------- | ------- |
| `authentication/login`           | 로그인 작업을 트리거합니다. |
| `authentication/login-callback`  | 로그인 작업의 결과를 처리합니다. |
| `authentication/login-failed`    | 어떤 이유로 로그인 작업이 실패하면 오류 메시지가 표시됩니다. |
| `authentication/logout`          | 로그아웃 작업을 트리거합니다. |
| `authentication/logout-callback` | 로그아웃 작업의 결과를 처리합니다. |
| `authentication/logout-failed`   | 어떤 이유로 로그아웃 작업이 실패하면 오류 메시지가 표시됩니다. |
| `authentication/logged-out`      | 사용자가 로그아웃에 성공했음을 나타냅니다. |
| `authentication/profile`         | 사용자 프로필을 편집하는 작업을 트리거합니다. |
| `authentication/register`        | 새 사용자를 등록하는 작업을 트리거합니다. |

앞의 표에 표시된 경로는 을 `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`통해 구성할 수 있습니다. 사용자 지정 경로를 제공하는 옵션을 설정할 때 앱에 각 경로를 처리하는 경로가 있는지 확인합니다.

다음 예제에서는 모든 경로에 접두사입니다. `/security`

`Authentication`구성*요소(페이지/인증.razor):*

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`*(Program.cs):*

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

요구 사항이 완전히 다른 경로를 요구하는 경우 앞서 설명한 `RemoteAuthenticatorView` 대로 경로를 설정하고 명시적 작업 매개 변수를 사용하여 렌더링합니다.

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

이렇게 선택한 경우 UI를 다른 페이지로 나누도록 허용됩니다.

## <a name="customize-the-authentication-user-interface"></a>인증 사용자 인터페이스 사용자 지정

`RemoteAuthenticatorView`는 각 인증 상태에 대한 기본 UI 조각 집합을 포함합니다. 각 상태는 사용자 지정을 전달하여 사용자 지정할 `RenderFragment`수 있습니다. 초기 로그인 프로세스 중에 표시된 텍스트를 사용자 지정하려면 `RemoteAuthenticatorView` 다음과 같이 변경할 수 있습니다.

`Authentication`구성*요소(페이지/인증.razor):*

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

는 `RemoteAuthenticatorView` 다음 표에 표시된 인증 경로당 사용할 수 있는 하나의 조각이 있습니다.

| 라우팅                            | 조각                |
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
