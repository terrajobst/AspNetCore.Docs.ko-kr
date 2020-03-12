`FetchData` 구성 요소는 다음을 수행 하는 방법을 보여 줍니다.

* 액세스 토큰을 프로 비전 합니다.
* 액세스 토큰을 사용 하 여 *서버* 앱에서 보호 된 리소스 API를 호출 합니다.

`@attribute [Authorize]` 지시어는이 구성 요소를 방문 하기 위해 사용자에 게 권한이 부여 되어야 한다는 Blazor Weasembom권한 부여 시스템을 나타냅니다. *클라이언트* 앱에 특성이 있으면 서버에 있는 API가 적절 한 자격 증명 없이 호출 되는 것을 방지할 수 없습니다. 또한 *서버* 앱은 적절 한 끝점에 대 한 `[Authorize]`를 사용 하 여 올바르게 보호 해야 합니다.

`AuthenticationService.RequestAccessToken();`는 API를 호출 하기 위해 요청에 추가할 수 있는 액세스 토큰을 요청 합니다. 토큰이 캐시 되거나 서비스에서 사용자 상호 작용 없이 새 액세스 토큰을 프로 비전 할 수 있으면 토큰 요청이 성공 합니다. 그렇지 않으면 토큰 요청이 실패 합니다.

요청에 포함할 실제 토큰을 가져오기 위해 앱은 `tokenResult.TryGetToken(out var token)`를 호출 하 여 요청이 성공 했는지 확인 해야 합니다. 

요청에 성공 하면 토큰 변수가 액세스 토큰으로 채워집니다. 토큰의 `Value` 속성은 `Authorization` 요청 헤더에 포함할 리터럴 문자열을 노출 합니다.

사용자 상호 작용 없이 토큰을 프로 비전 할 수 없기 때문에 요청이 실패 한 경우 토큰 결과에 리디렉션 URL이 포함 됩니다. 이 URL로 이동 하면 사용자가 로그인 페이지로 이동 하 여 인증에 성공한 후에 현재 페이지로 돌아갑니다.

```razor
@page "/fetchdata"
...
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

자세한 내용은 [인증 작업 전에 응용 프로그램 상태 저장](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)을 참조 하세요.
