인덱스 페이지 (*wwwroot/index.html*) 페이지에는 JavaScript에서 `AuthenticationService`를 정의 하는 스크립트가 포함 되어 있습니다. `AuthenticationService`는 OIDC 프로토콜의 하위 수준 세부 정보를 처리 합니다. 앱은 내부적으로 스크립트에 정의 된 메서드를 호출 하 여 인증 작업을 수행 합니다.

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
