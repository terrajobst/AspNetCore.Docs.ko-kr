# <a name="blazor-client-side-sample-app"></a>Blazor (클라이언트 쪽) 샘플 앱

이 샘플에서는 Blazor 설명서에 설명 된 Blazor 시나리오를 사용 하는 방법을 보여 줍니다.

## <a name="call-web-api-example"></a>Web API 호출 예제

웹 api 예제를 사용 하려면 <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">자습서 용 샘플 앱을 기반으로 하는 실행 중인 웹 api가 필요 합니다. ASP.NET Core MVC</a> 토픽을 사용 하 여 web API를 만듭니다. 샘플 앱은에서 `https://localhost:10000/api/todo`웹 API에 대 한 요청을 만듭니다. 다른 웹 API 주소를 사용 하는 경우 Razor `ServiceEndpoint` `@functions` 구성 요소의 블록에서 상수 값을 업데이트 합니다.</p>

샘플 앱은 `http://localhost:5000` 또는 `https://localhost:5001` 웹 API에 대 한 <a href="https://docs.microsoft.com/aspnet/core/security/cors">CORS (크로스-원본 자원 공유)</a> 요청을 만듭니다. 자격 증명 (권한 부여 쿠키/헤더)이 허용 됩니다. 를 호출 `Startup.Configure` `UseMvc`하기 전에 다음 CORS 미들웨어 구성을 web API의 메서드에 추가 합니다.</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Blazor 앱에 필요한 만큼의 `WithOrigins` 도메인 및 포트를 조정 합니다.

웹 API는 CORS에 대해 클라이언트 코드의 권한 부여 쿠키/헤더 및 요청을 허용 하도록 구성 되어 있지만 자습서에서 만든 웹 API는 실제로 요청에 권한을 부여 하지 않습니다. 구현 지침은 <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core 보안 및 id 문서</a> 를 참조 하세요.
