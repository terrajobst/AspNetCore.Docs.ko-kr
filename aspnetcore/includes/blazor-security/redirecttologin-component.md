`RedirectToLogin` 구성 요소 (*Shared/RedirectToLogin. razor*):

* 로그인 페이지에 대 한 권한 없는 사용자의 리디렉션을 관리 합니다.
* 인증이 성공 하는 경우 해당 페이지로 반환 될 수 있도록 사용자가 액세스 하려고 하는 현재 URL을 유지 합니다.

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo($"authentication/login?returnUrl={Navigation.Uri}");
    }
}
```
