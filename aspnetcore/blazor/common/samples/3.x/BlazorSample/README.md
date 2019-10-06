# <a name="blazor-webassembly-sample-app"></a>Blazor Weasembomsample 앱

이 샘플에서는 Blazor 설명서에 설명 된 Blazor 시나리오를 사용 하는 방법을 보여 줍니다.

## <a name="call-web-api-example"></a>Web API 호출 예제

Web API 예제에는 Blazor 샘플 앱과 동일한 HTTPS 포트 (5001)를 기본적으로 사용 하는 <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">ASP.NET Core를 사용 하 여 WEB Api 만들기</a> 항목에 대 한 샘플 앱을 기반으로 하는 실행 웹 api가 필요 합니다. 동일한 컴퓨터에서 두 앱을 동시에 사용 하려면 웹 API의 포트를 변경 합니다 (예: 포트 1만 사용). 샘플 앱은 `https://localhost:10000/api/TodoItems`에서 웹 API에 대 한 요청을 만듭니다. 다른 웹 API 주소를 사용 하는 경우 Razor 구성 요소의 `@code` 블록에서 `ServiceEndpoint` 상수 값을 업데이트 합니다.</p>

샘플 앱은 `http://localhost:5000` 또는 `https://localhost:5001`에서 웹 API로 <a href="https://docs.microsoft.com/aspnet/core/security/cors">CORS (크로스-원본 자원 공유)</a> 요청을 만듭니다. 자격 증명 (권한 부여 쿠키/헤더)이 허용 됩니다. Web API의 `Startup.Configure` 메서드에 다음 CORS 미들웨어 구성을 추가 합니다.</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Blazor 앱에 필요에 따라 `WithOrigins`의 도메인 및 포트를 조정 합니다.

웹 API는 CORS에 대해 클라이언트 코드의 권한 부여 쿠키/헤더 및 요청을 허용 하도록 구성 되어 있지만 자습서에서 만든 웹 API는 실제로 요청에 권한을 부여 하지 않습니다. 구현 지침은 <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core 보안 및 id 문서</a> 를 참조 하세요.
