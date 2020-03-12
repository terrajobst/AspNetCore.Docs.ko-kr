`LoginDisplay` 구성 요소 (*shared/LoginDisplay*)는 `MainLayout` 구성 요소 (*Shared/mainlayout. razor*)에서 렌더링 되며 다음 동작을 관리 합니다.

* 인증 된 사용자의 경우:
  * 현재 사용자 이름을 표시 합니다.
  * 앱에서 로그 아웃할 수 있는 단추를 제공 합니다.
* 익명 사용자의 경우은 로그인 할 수 있는 옵션을 제공 합니다.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
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
