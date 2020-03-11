# <a name="aspnet-core-response-caching-sample"></a>ASP.NET Core 응답 캐싱 샘플

이 샘플에서는 ASP.NET Core [응답 캐싱 미들웨어](https://docs.microsoft.com/aspnet/core/performance/caching/middleware)를 사용 하는 방법을 보여 줍니다.

앱은 캐싱 동작을 구성 하는 `Cache-Control` 헤더를 포함 하 여 인덱스 페이지에 응답 합니다. 또한 앱은 후속 요청의 `Accept-Encoding` 헤더가 원래 요청에서와 일치 하는 경우에만 응답을 제공 하도록 캐시를 구성 하도록 `Vary` 헤더를 설정 합니다.

샘플을 실행 하는 경우 최대 10 초 동안 인덱스 페이지가 저장 되 고 캐시 될 때 캐시에서 제공 됩니다.

캐싱 동작을 테스트 하려면:

* 브라우저를 사용 하 여 캐싱 동작을 테스트 하지 마세요. 일반적으로 브라우저는 다시 로드 시 캐시 컨트롤 헤더를 추가 하 여 미들웨어가 캐시 된 페이지를 제공할 수 없도록 합니다. 예를 들어 값이 `max-age=0`인 `Cache-Control` 헤더를 브라우저에서 추가할 수 있습니다.
* <a href="https://www.telerik.com/fiddler">Fiddler</a> 또는 <a href="https://www.getpostman.com/">postman</a>과 같은 요청 헤더를 명시적으로 설정할 수 있도록 하는 개발자 도구를 사용 합니다.
