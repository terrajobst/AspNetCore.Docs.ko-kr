<span data-ttu-id="ac147-101">`App` 구성 요소 (*응용 프로그램 razor*)는 Blazor Server 앱에 있는 `App` 구성 요소와 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac147-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="ac147-102">`CascadingAuthenticationState` 구성 요소는 응용 프로그램의 나머지 부분에 대 한 `AuthenticationState` 공개를 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac147-102">The `CascadingAuthenticationState` component manages exposing the `AuthenticationState` to the rest of the app.</span></span>
* <span data-ttu-id="ac147-103">`AuthorizeRouteView` 구성 요소를 사용 하면 현재 사용자에 게 지정 된 페이지에 액세스할 수 있는 권한이 부여 되거나 `RedirectToLogin` 구성 요소가 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac147-103">The `AuthorizeRouteView` component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="ac147-104">`RedirectToLogin` 구성 요소는 로그인 페이지에 대 한 권한 없는 사용자의 리디렉션을 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac147-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <RedirectToLogin />
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
