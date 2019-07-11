# <a name="response-compression-sample-application-aspnet-core-1x"></a>응답 압축에 대 한 샘플 응용 프로그램 (ASP.NET Core 1.x)

이 샘플에서는 ASP.NET Core 1.x 응답 압축 미들웨어에 대 한 HTTP 응답을 압축 합니다. Gzip 및 텍스트, 이미지 응답에 대 한 사용자 지정 압축 공급자를 보여 줍니다 샘플과 압축에 대 한 MIME 형식을 추가 하는 방법을 보여 줍니다. ASP.NET Core 2.x 샘플을 보려면 [응답 압축에 대 한 샘플 응용 프로그램 (ASP.NET Core 2.x)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x)합니다.

## <a name="examples-in-this-sample"></a>이 샘플의 예제

* `GzipCompressionProvider`
  * `text/plain`
    * **/** -927 바이트를 압축 하는 2,044 바이트로 Lorem ipsum은 1500 년대 텍스트 파일 응답
    * **/testfile1kb.txt** -47 바이트를 압축 하는 1,033 바이트에서 텍스트 파일 응답
    * **trickle/** -1 초 간격을 단일 문자로 발행 된 응답
  * `image/svg+xml`
    * **/banner.svg** -4,459 바이트를 압축 하는 9,707 바이트로 확장 가능한 SVG (벡터 그래픽) 이미지 응답
* `CustomCompressionProvider`<br>미들웨어를 사용 하 여 사용에 대 한 사용자 지정 압축 공급자를 구현 하는 방법을 보여 줍니다.

요청에 포함 하는 경우는 `Accept-Encoding` 샘플을 추가 하는 헤더를 `Vary: Accept-Encoding` 헤더를 응답 합니다. 합니다 `Vary` 헤더는 캐시의 대체 값을 기반으로 응답의 여러 복사본을 유지 하려면 `Accept-Encoding`이므로 시스템 중 하나를 수행할 수 있는 압축 된 허용에 대 한 압축 된 (gzip) 및 압축 되지 않은 버전을 모두 캐시에 저장 됩니다 또는 압축 되지 않은 응답입니다.

## <a name="using-the-sample"></a>샘플 사용

1. 사용 하 여 요청 [Fiddler](https://www.telerik.com/fiddler)를 [Firebug](https://getfirebug.com/), 또는 [Postman](https://www.getpostman.com/) 없이 응용 프로그램에는 `Accept-Encoding` 헤더 및 응답 크기는 응답 페이로드를 참고 하 고 응답 헤더입니다.
1. 추가 `Accept-Encoding: gzip` 헤더 압축 된 응답 크기 및 응답 헤더를 확인 합니다. 삭제 응답 크기를 표시 하며 `Content-Encoding: gzip` 응답 헤더 샘플 앱으로 포함 됩니다. Lorem Ipsum에 대 한 응답 본문에서 볼 때 또는 **testfile1kb.txt** 응답을 표시의 텍스트를 압축 하 고 읽을 수 없습니다.
1. 추가 `Accept-Encoding: mycustomcompression` 헤더 응답 헤더를 확인 합니다. 합니다 `CustomCompressionProvider` 응답을 압축 실제로 되지 않는 빈 구현을 이지만 사용자 지정 압축 스트림을 래퍼를 만들 수 있습니다는 `CreateStream()` 메서드.
