`App` 구성 요소 (*응용 프로그램 razor*)는 Blazor Server 앱에 있는 `App` 구성 요소와 유사 합니다.

* `CascadingAuthenticationState` 구성 요소는 응용 프로그램의 나머지 부분에 대 한 `AuthenticationState` 공개를 관리 합니다.
* `AuthorizeRouteView` 구성 요소를 사용 하면 현재 사용자에 게 지정 된 페이지에 액세스할 수 있는 권한이 부여 되거나 `RedirectToLogin` 구성 요소가 렌더링 됩니다.
* `RedirectToLogin` 구성 요소는 로그인 페이지에 대 한 권한 없는 사용자의 리디렉션을 관리 합니다.

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
