`Authentication` 구성 요소에 의해 생성 된 페이지 (*Pages/Authentication. razor*)는 서로 다른 인증 단계를 처리 하는 데 필요한 경로를 정의 합니다.

`RemoteAuthenticatorView` 구성 요소:

* 는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지에서 제공 됩니다.
* 각 인증 단계에서 적절 한 작업을 수행 하는 작업을 관리 합니다.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
