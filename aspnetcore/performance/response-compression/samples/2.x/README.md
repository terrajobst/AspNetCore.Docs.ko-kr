# <a name="response-compression-sample-application-aspnet-core-2x"></a>응답 압축에 대 한 샘플 응용 프로그램 (ASP.NET Core 2.x)

이 샘플에서는 ASP.NET Core 2.x 응답 압축 미들웨어 HTTP 응답을 압축 합니다. 샘플 Gzip, Brotli을 및 텍스트, 이미지 응답에 대 한 사용자 지정 압축 공급자를 보여 줍니다 및 압축을 위한 MIME 형식을 추가 하는 방법을 보여 줍니다. ASP.NET Core 1.x 샘플을 보려면 [응답 압축에 대 한 샘플 응용 프로그램 (ASP.NET Core 1.x)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/1.x)합니다.

## <a name="examples-in-this-sample"></a>이 샘플의 예제

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -Lorem ipsum은 1500 년대 텍스트 파일 응답 2,044 바이트로 ~ 979 바이트를 압축 합니다.
    * **/testfile1kb.txt** -~ 36 바이트가 압축 1,033 바이트에서 텍스트 파일 응답 합니다.
    * **trickle/** -1 초 간격을 단일 문자로 발행 된 응답입니다.
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Lorem ipsum은 1500 년대 텍스트 파일 응답 2,044 바이트로 ~ 927 바이트를 압축 합니다.
    * **/testfile1kb.txt** -~ 47 바이트가 압축 1,033 바이트에서 텍스트 파일 응답 합니다.
    * **trickle/** -1 초 간격을 단일 문자로 발행 된 응답입니다.
  * `image/svg+xml`
    * **/banner.svg** -~ 4,459 바이트가 압축 9,707 바이트에는 확장 가능한 SVG (벡터 그래픽) 이미지 응답 합니다.
* `CustomCompressionProvider`<br>미들웨어를 사용 하 여 사용에 대 한 사용자 지정 압축 공급자를 구현 하는 방법을 보여 줍니다.

요청에 포함 하는 경우는 `Accept-Encoding` 성공 하면 헤더 및 응답 압축 미들웨어를 자동으로 추가 `Vary: Accept-Encoding` 헤더를 응답 합니다. `Vary` 헤더는 캐시의 대체 값을 기반으로 응답의 여러 복사본을 유지 하려면 `Accept-Encoding`, 모두는 압축 된 (Gzip 또는 Brotli) 및 압축 되지 않은 버전 중 하나를 수행할 수 있는 시스템에서 허용에 대 한 캐시에 저장 됩니다는 압축 또는 압축 되지 않은 응답 합니다.

## <a name="use-the-sample"></a>샘플 사용

1. 사용 하 여 요청 [Fiddler](http://www.telerik.com/fiddler)를 [Firebug](http://getfirebug.com/), 또는 [Postman](https://www.getpostman.com/) 없이 응용 프로그램에는 `Accept-Encoding` 헤더 및 응답 크기는 응답 페이로드를 참고 하 고 응답 헤더입니다.
1. 추가 된 `Accept-Encoding: br` 또는 `Accept-Encoding: gzip` 헤더 압축 된 응답 크기 및 응답 헤더를 확인 합니다. 응답 크기, 삭제 및 `Content-Encoding` 나타내는 하거나 Gzip으로 압축 하는 미들웨어에서 응답 헤더 포함 되어 있거나 Brotli 발생 했습니다. Lorem Ipsum에 대 한 응답 본문에서 볼 때 또는 **testfile1kb.txt** 응답을 표시의 텍스트를 압축 하 고 읽을 수 없습니다.
1. 추가 `Accept-Encoding: mycustomcompression` 헤더 응답 헤더를 확인 합니다. 합니다 `CustomCompressionProvider` 응답을 압축 실제로 되지 않는 빈 구현을 이지만 사용자 지정 압축 스트림을 래퍼를 만들 수 있습니다는 `CreateStream()` 메서드.
