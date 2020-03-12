<span data-ttu-id="2a96c-101">`Authentication` 구성 요소에 의해 생성 된 페이지 (*Pages/Authentication. razor*)는 서로 다른 인증 단계를 처리 하는 데 필요한 경로를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="2a96c-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="2a96c-102">`RemoteAuthenticatorView` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="2a96c-102">The `RemoteAuthenticatorView` component:</span></span>

* <span data-ttu-id="2a96c-103">는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2a96c-103">Is provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span>
* <span data-ttu-id="2a96c-104">각 인증 단계에서 적절 한 작업을 수행 하는 작업을 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2a96c-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
