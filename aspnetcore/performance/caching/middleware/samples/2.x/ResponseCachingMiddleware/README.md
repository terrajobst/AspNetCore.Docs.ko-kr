# <a name="aspnet-core-response-caching-sample"></a>ASP.NET Core 응답 캐싱 예제

이 샘플에서는 ASP.NET Core의 사용을 보여 줍니다 [응답 캐싱 미들웨어](https://docs.microsoft.com/aspnet/core/performance/caching/middleware)합니다.

해당 인덱스 페이지를 사용 하 여 앱이 응답 등을 `Cache-Control` 캐싱 동작을 구성 하는 헤더입니다. 앱 설정 합니다 `Vary` 경우에만 응답에 맞도록 캐시를 구성 하는 헤더는 `Accept-Encoding` 후속 요청의 헤더는 원래 요청에서 일치 합니다.

이 샘플을 실행 하는 경우 인덱스 페이지가 저장 하 고 최대 10 초 동안 캐시 되 면 캐시에서 제공 됩니다.

캐싱 동작을 테스트 합니다.

* 캐싱 동작을 테스트 하려면 브라우저를 사용 하지 마세요. 브라우저에서 미들웨어가 캐시 된 페이지를 제공 하지 못하도록 하는 다시 로드 캐시 제어 헤더를 추가 하기도 합니다. 예를 들어 한 `Cache-Control` 헤더의 값을 사용 하 여 `max-age=0`) 브라우저에 의해 추가 될 수 있습니다.
* 요청 헤더를 명시적으로 설정을 허용 하는 개발자 도구를 사용 하 여 같은 <a href="https://www.telerik.com/fiddler">Fiddler</a> 하거나 <a href="https://www.getpostman.com/">Postman</a>합니다.
