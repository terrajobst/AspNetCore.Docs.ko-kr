<span data-ttu-id="e898f-101">`RedirectToLogin` 구성 요소 (*Shared/RedirectToLogin. razor*):</span><span class="sxs-lookup"><span data-stu-id="e898f-101">The `RedirectToLogin` component (*Shared/RedirectToLogin.razor*):</span></span>

* <span data-ttu-id="e898f-102">로그인 페이지에 대 한 권한 없는 사용자의 리디렉션을 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="e898f-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="e898f-103">인증이 성공 하는 경우 해당 페이지로 반환 될 수 있도록 사용자가 액세스 하려고 하는 현재 URL을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="e898f-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

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
