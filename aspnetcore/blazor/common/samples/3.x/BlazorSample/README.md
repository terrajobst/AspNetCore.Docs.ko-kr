# <a name="blazor-client-side-sample-app"></a><span data-ttu-id="bb43b-101">Blazor (클라이언트 쪽) 샘플 앱</span><span class="sxs-lookup"><span data-stu-id="bb43b-101">Blazor (client-side) Sample App</span></span>

<span data-ttu-id="bb43b-102">이 샘플에서는 Blazor 설명서에 설명 된 Blazor 시나리오를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="bb43b-102">This sample illustrates the use of Blazor scenarios described in the Blazor documentation.</span></span>

## <a name="call-web-api-example"></a><span data-ttu-id="bb43b-103">Web API 호출 예제</span><span class="sxs-lookup"><span data-stu-id="bb43b-103">Call web API example</span></span>

<span data-ttu-id="bb43b-104">웹 api 예제를 사용 하려면 <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">자습서 용 샘플 앱을 기반으로 하는 실행 중인 웹 api가 필요 합니다. ASP.NET Core MVC</a> 토픽을 사용 하 여 web API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bb43b-104">The web API example requires a running web API based on the sample app for the <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">Tutorial: Create a web API with ASP.NET Core MVC</a> topic.</span></span> <span data-ttu-id="bb43b-105">샘플 앱은에서 `https://localhost:10000/api/todo`웹 API에 대 한 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bb43b-105">The sample app makes requests to the web API at `https://localhost:10000/api/todo`.</span></span> <span data-ttu-id="bb43b-106">다른 웹 API 주소를 사용 하는 경우 Razor `ServiceEndpoint` `@functions` 구성 요소의 블록에서 상수 값을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="bb43b-106">If a different web API address is used, update the `ServiceEndpoint` constant value in the Razor component's `@functions` block.</span></span></p>

<span data-ttu-id="bb43b-107">샘플 앱은 `http://localhost:5000` 또는 `https://localhost:5001` 웹 API에 대 한 <a href="https://docs.microsoft.com/aspnet/core/security/cors">CORS (크로스-원본 자원 공유)</a> 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bb43b-107">The sample app makes a <a href="https://docs.microsoft.com/aspnet/core/security/cors">cross-origin resource sharing (CORS)</a> request from `http://localhost:5000` or `https://localhost:5001` to the web API.</span></span> <span data-ttu-id="bb43b-108">자격 증명 (권한 부여 쿠키/헤더)이 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb43b-108">Credentials (authorization cookies/headers) are permitted.</span></span> <span data-ttu-id="bb43b-109">를 호출 `Startup.Configure` `UseMvc`하기 전에 다음 CORS 미들웨어 구성을 web API의 메서드에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="bb43b-109">Add the following CORS middleware configuration to the web API's `Startup.Configure` method before it calls `UseMvc`:</span></span></p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

<span data-ttu-id="bb43b-110">Blazor 앱에 필요한 만큼의 `WithOrigins` 도메인 및 포트를 조정 합니다.</span><span class="sxs-lookup"><span data-stu-id="bb43b-110">Adjust the domains and ports of `WithOrigins` as needed for the Blazor app.</span></span>

<span data-ttu-id="bb43b-111">웹 API는 CORS에 대해 클라이언트 코드의 권한 부여 쿠키/헤더 및 요청을 허용 하도록 구성 되어 있지만 자습서에서 만든 웹 API는 실제로 요청에 권한을 부여 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bb43b-111">The web API is configured for CORS to permit authorization cookies/headers and requests from client code, but the web API as created by the tutorial doesn't actually authorize requests.</span></span> <span data-ttu-id="bb43b-112">구현 지침은 <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core 보안 및 id 문서</a> 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="bb43b-112">See the <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core Security and Identity articles</a> for implementation guidance.</span></span>
