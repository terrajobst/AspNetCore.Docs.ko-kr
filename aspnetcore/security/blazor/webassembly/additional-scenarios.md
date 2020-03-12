---
title: 추가 보안 시나리오를 ASP.NET Core Blazor
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: fe87ce76d8e181de788188b021616f2a09833585
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083694"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor Weasembmbambambambamba 추가 보안 시나리오

[Javier Calvarro e](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="handle-token-request-errors"></a>토큰 요청 오류 처리

SPA (단일 페이지 앱)에서 OIDC (Open ID Connect)를 사용 하 여 사용자를 인증 하는 경우 인증 상태는 해당 자격 증명을 제공 하는 사용자의 결과로 설정 된 세션 쿠키 형식의 SPA 및 IP (Id 공급자) 내에서 로컬로 유지 관리 됩니다.

일반적으로 사용자에 대 한 IP가 내보내는 토큰은 짧은 시간 동안 일반적으로 1 시간 동안 유효 하므로 클라이언트 앱이 정기적으로 새 토큰을 가져와야 합니다. 그렇지 않으면 부여 된 토큰이 만료 된 후 사용자가 로그 아웃 됩니다. 대부분의 경우 OIDC 클라이언트는 사용자가 IP 내에 유지 되는 인증 상태 또는 "세션"으로 인해 다시 인증 하도록 요구 하지 않고 새 토큰을 프로 비전 할 수 있습니다.

사용자가 사용자 개입 없이 토큰을 가져올 수 없는 경우도 있습니다. 예를 들어 사용자가 IP에서 명시적으로 로그 아웃 하는 이유가 있습니다. 이 시나리오는 사용자가 `https://login.microsoftonline.com`를 방문 하 여 로그 아웃 하는 경우에 발생 합니다. 이러한 시나리오에서 앱은 사용자가 로그 아웃 한 즉시 인식 하지 못합니다. 클라이언트에서 보유 하는 모든 토큰은 더 이상 유효 하지 않을 수 있습니다. 또한 클라이언트는 현재 토큰이 만료 된 후 사용자 상호 작용 없이 새 토큰을 프로 비전 할 수 없습니다.

이러한 시나리오는 토큰 기반 인증에 국한 되지 않습니다. 이러한 특성은 SPAs 특성의 일부입니다. 쿠키를 사용 하는 SPA는 인증 쿠키를 제거 하는 경우에도 서버 API를 호출 하지 못합니다.

앱에서 보호 된 리소스에 대 한 API 호출을 수행 하는 경우 다음 사항을 알고 있어야 합니다.

* 새 액세스 토큰을 프로 비전 하 여 API를 호출 하려면 사용자가 다시 인증 해야 할 수 있습니다.
* 클라이언트에 유효한 토큰이 있는 경우에도 토큰을 사용자가 해지 했기 때문에 서버에 대 한 호출이 실패할 수 있습니다.

앱에서 토큰을 요청 하는 경우 다음과 같은 두 가지 결과를 사용할 수 있습니다.

* 요청이 성공 하 고 앱에 유효한 토큰이 있습니다.
* 요청이 실패 하 고 앱이 사용자를 다시 인증 하 여 새 토큰을 가져와야 합니다.

토큰 요청이 실패 하는 경우 리디렉션을 수행 하기 전에 현재 상태를 저장할 것인지 여부를 결정 해야 합니다. 복잡성 수준이 증가 하는 방법에는 여러 가지가 있습니다.

* 세션 저장소에 현재 페이지 상태를 저장 합니다. `OnInitializeAsync`중에는 계속 하기 전에 상태를 복원할 수 있는지 확인 합니다.
* 쿼리 문자열 매개 변수를 추가 하 고이 매개 변수를 사용 하 여 이전에 저장 된 상태를 다시 하이드레이션 하며 나중 하는 데 필요한 것으로 앱에 신호를 보낼 수 있습니다.
* 다른 항목과 충돌을 발생 시 키 지 않고 세션 저장소에 데이터를 저장 하는 고유 식별자를 사용 하 여 쿼리 문자열 매개 변수를 추가 합니다.

아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.

* 로그인 페이지로 리디렉션하기 전에 상태를 유지 합니다.
* 쿼리 문자열 매개 변수를 사용 하 여 인증 이후 이전 상태를 복구 합니다.

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

인증 작업을 수행 하는 동안 브라우저가 IP로 리디렉션되도록 응용 프로그램 상태를 저장 하려는 경우가 있습니다. 상태 컨테이너와 같은 항목을 사용 하 고 인증에 성공 하 고 나면 상태를 복원 하려는 경우를 들 수 있습니다. 사용자 지정 인증 상태 개체를 사용 하 여 앱 특정 상태 또는 참조를 보존 하 고 인증 작업이 성공적으로 완료 되 면 해당 상태를 복원할 수 있습니다.

`Authentication` 구성 요소 (*페이지/인증. razor*):

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

## <a name="request-additional-access-tokens"></a>추가 액세스 토큰 요청

대부분의 앱은 사용 하는 보호 된 리소스와 상호 작용 하는 데 액세스 토큰만 필요 합니다. 일부 시나리오에서는 둘 이상의 리소스와 상호 작용 하기 위해 앱에 둘 이상의 토큰이 필요할 수 있습니다. `IAccessTokenProvider.RequestToken` 메서드는 다음 예제와 같이 앱에서 지정 된 범위 집합을 사용 하 여 토큰을 프로 비전 할 수 있도록 하는 오버 로드를 제공 합니다.

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });
```

## <a name="customize-app-routes"></a>앱 경로 사용자 지정

기본적으로 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 라이브러리는 다음 표에 나와 있는 경로를 사용 하 여 서로 다른 인증 상태를 나타냅니다.

| 라우팅                            | 목적 |
| -------------------------------- | ------- |
| `authentication/login`           | 로그인 작업을 트리거합니다. |
| `authentication/login-callback`  | 로그인 작업의 결과를 처리 합니다. |
| `authentication/login-failed`    | 어떤 이유로 인해 로그인 작업이 실패 하는 경우 오류 메시지를 표시 합니다. |
| `authentication/logout`          | 로그 아웃 작업을 트리거합니다. |
| `authentication/logout-callback` | 로그 아웃 작업의 결과를 처리 합니다. |
| `authentication/logout-failed`   | 어떤 이유로 인해 로그 아웃 작업이 실패 하는 경우 오류 메시지를 표시 합니다. |
| `authentication/logged-out`      | 사용자가 성공적으로 로그 아웃 했음을 나타냅니다. |
| `authentication/profile`         | 사용자 프로필을 편집 하는 작업을 트리거합니다. |
| `authentication/register`        | 새 사용자를 등록 하는 작업을 트리거합니다. |

위의 표에 표시 된 경로는 `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`를 통해 구성할 수 있습니다. 사용자 지정 경로를 제공 하는 옵션을 설정 하는 경우 앱에 각 경로를 처리 하는 경로가 있는지 확인 합니다.

다음 예제에서는 모든 경로에 `/security`접두사가 붙습니다.

`Authentication` 구성 요소 (*페이지/인증. razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (*Program.cs*):

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

요구 사항이 완전히 다른 경로를 호출 하는 경우 앞에서 설명한 대로 경로를 설정 하 고 명시적 작업 매개 변수를 사용 하 여 `RemoteAuthenticatorView`를 렌더링 합니다.

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

이렇게 선택 하면 UI를 다른 페이지로 나눌 수 있습니다.

## <a name="customize-the-authentication-user-interface"></a>인증 사용자 인터페이스 사용자 지정

`RemoteAuthenticatorView`에는 각 인증 상태에 대 한 기본 UI 조각 집합이 포함 되어 있습니다. 사용자 지정 `RenderFragment`를 전달 하 여 각 상태를 사용자 지정할 수 있습니다. 초기 로그인 프로세스 중에 표시 되는 텍스트를 사용자 지정 하기 위해에서 다음과 같이 `RemoteAuthenticatorView`를 변경할 수 있습니다.

`Authentication` 구성 요소 (*페이지/인증. razor*):

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

`RemoteAuthenticatorView`에는 다음 표에 표시 된 인증 경로 별로 사용할 수 있는 조각이 하나 있습니다.

| 라우팅                            | 조각             |
| -------------------------------- | -------------------- |
| `authentication/login`           | `<LoggingIn>`        |
| `authentication/login-callback`  | `<CompletingLogIn>`  |
| `authentication/login-failed`    | `<LogInFailed>`      |
| `authentication/logout`          | `<LoggingOut>`       |
| `authentication/logout-callback` | `<CompletingLogOut>` |
| `authentication/logout-failed`   | `<LogOutFailed>`     |
| `authentication/logged-out`      | `<LogOutSucceeded>`  |
| `authentication/profile`         | `<UserProfile>`      |
| `authentication/register`        | `<Registering>`      |
