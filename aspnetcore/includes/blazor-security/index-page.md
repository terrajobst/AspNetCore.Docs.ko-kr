<span data-ttu-id="34f88-101">인덱스 페이지 (*wwwroot/index.html*) 페이지에는 JavaScript에서 `AuthenticationService`를 정의 하는 스크립트가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="34f88-101">The Index page (*wwwroot/index.html*) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="34f88-102">`AuthenticationService`는 OIDC 프로토콜의 하위 수준 세부 정보를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="34f88-102">`AuthenticationService` handles the low-level details of the the OIDC protocol.</span></span> <span data-ttu-id="34f88-103">앱은 내부적으로 스크립트에 정의 된 메서드를 호출 하 여 인증 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="34f88-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
