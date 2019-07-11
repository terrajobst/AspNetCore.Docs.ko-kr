---
title: ASP.NET Core에서 응답 압축
author: guardrex
description: ASP.NET Core 앱에서 응답 압축 미들웨어를 사용하는 방법 및 응답 압축에 대해 알아봅니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: performance/response-compression
ms.openlocfilehash: d5d2da3dc0a8a452de97d98161d429389d2f7638
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815611"
---
# <a name="response-compression-in-aspnet-core"></a>ASP.NET Core에서 응답 압축

[Luke Latham](https://github.com/guardrex)으로

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

네트워크 대역폭은 제한 된 리소스입니다. 일반적으로 응답의 크기를 줄이면 앱의 응답성 데이터는 종종 크게 증가 합니다. 페이로드 크기를 줄이는 한 가지 방법은 응용 프로그램의 응답을 압축 하는 경우

## <a name="when-to-use-response-compression-middleware"></a>응답 압축 미들웨어를 사용 하는 경우

IIS, Apache 또는 Nginx에서 서버 기반 응답 압축 기술을 사용 합니다. 미들웨어의 성능이 아마도 일치 하지 않기 때문 서버 모듈입니다. [HTTP.sys 서버](xref:fundamentals/servers/httpsys) 서버와 [Kestrel](xref:fundamentals/servers/kestrel) server 기본 제공 압축 지원 현재 제공 하지 않습니다.

경우에 응답 압축 미들웨어를 사용 합니다.

* 다음 서버 기반 압축 기술을 사용할 수 없습니다.
  * [IIS 동적 압축이 모듈](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate 모듈](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx 압축 및 압축 풀기](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* 직접 호스트:
  * [HTTP.sys 서버](xref:fundamentals/servers/httpsys) (이전의 WebListener)
  * [Kestrel 서버](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>응답 압축

일반적으로 압축 되지 않음 응답에서 응답 압축 이점을 얻을 수 있습니다. 일반적으로 압축 되지 않음 응답은 다음과 같습니다. CSS, JavaScript, HTML, XML 및 JSON입니다. PNG 파일 등 기본적으로 압축 된 자산을 압축 하지 않아야 합니다. 기본적으로 압축 된 응답을 압축 하려는 경우 크기와 전송 시간에 적은 추가 감소는 압축을 처리 하는 데 걸린 시간을 기준으로 늘린다 가능성이 높습니다. (파일의 콘텐츠 및 압축의 효율성)에 따라 약 150 1000 바이트 보다 작은 파일을 압축 하지 마십시오. 작은 파일을 압축 하는 오버 헤드는 압축 되지 않은 파일 보다 큰 압축 된 파일을 생성할 수 있습니다.

클라이언트 전송 하 여 해당 기능의 서버를 알려야 클라이언트 압축 된 콘텐츠를 처리 하는 경우는 `Accept-Encoding` 요청 헤더입니다. 서버에서 압축 된 콘텐츠를 보내는 경우에서 정보를 포함 해야 하는 `Content-Encoding` 압축 된 응답 인코딩 되는 방법에 대 한 헤더입니다. 미들웨어에서 지 원하는 콘텐츠 인코딩 지정은 다음 표에 표시 됩니다.

::: moniker range=">= aspnetcore-2.2"

| `Accept-Encoding` 헤더 값 | 지원 되는 미들웨어 | Description |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | 예 (기본값)        | [Brotli 압축 된 데이터 형식](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | 아니요                   | [DEFLATE 압축 된 데이터 형식](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | 아니요                   | [W3C 효율적인 XML 교환](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | 예                  | [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | 예                  | "인코딩 없이" 식별자: 응답을 인코딩할 수 있어야 합니다. |
| `pack200-gzip`                  | 아니요                   | [네트워크 전송 형식에 Java 아카이브](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | 예                  | 모든 사용 가능한 콘텐츠 인코딩을 명시적으로 요청 하지 |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| `Accept-Encoding` 헤더 값 | 지원 되는 미들웨어 | Description |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | 아니요                   | [Brotli 압축 된 데이터 형식](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | 아니요                   | [DEFLATE 압축 된 데이터 형식](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | 아니요                   | [W3C 효율적인 XML 교환](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | 예 (기본값)        | [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | 예                  | "인코딩 없이" 식별자: 응답을 인코딩할 수 있어야 합니다. |
| `pack200-gzip`                  | 아니요                   | [네트워크 전송 형식에 Java 아카이브](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | 예                  | 모든 사용 가능한 콘텐츠 인코딩을 명시적으로 요청 하지 |

::: moniker-end

자세한 내용은 참조는 [IANA 공식 콘텐츠 코딩 목록](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)합니다.

미들웨어를 사용 하면 사용자 지정에 대 한 추가 압축 공급자를 추가 하려면 `Accept-Encoding` 헤더 값입니다. 자세한 내용은 [사용자 지정 공급자](#custom-providers) 아래.

미들웨어 품질 값에 응답할 수 (qvalue, `q`) 가중치를 보내면 클라이언트에서 압축 체계를 우선 순위를 지정 합니다. 자세한 내용은 참조 하세요. [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).

압축 알고리즘은 압축 속도 압축의 효율성 간의 균형을 유지 될 수 있습니다. *효율성* 이 컨텍스트에서 참조 출력의 크기를 압축 합니다. 최소 크기를 가장 하 여 이루어집니다 *최적의* 압축 합니다.

요청에 관련 된 헤더, 보내기, 캐싱 및 압축 된 내용을 받기는 아래 표에 설명 합니다.

| 헤더             | 역할 |
| ------------------ | ---- |
| `Accept-Encoding`  | 보낼 클라이언트에서 서버로 인코딩 체계를 클라이언트에 허용 되는 콘텐츠를 나타냅니다. |
| `Content-Encoding` | 서버에서 클라이언트로 보낼 페이로드에 콘텐츠 인코딩을 나타낼 수 있습니다. |
| `Content-Length`   | 압축이 발생할 때를 `Content-Length` 응답 압축 하는 경우는 본문 콘텐츠가 변경 이후 헤더를 제거 합니다. |
| `Content-MD5`      | 압축이 발생할 때를 `Content-MD5` 본문 콘텐츠가 변경 된 후 해시 더 이상 유효 헤더를 제거 합니다. |
| `Content-Type`     | 콘텐츠의 MIME 형식을 지정합니다. 모든 응답을 지정 해야 해당 `Content-Type`합니다. 미들웨어는 응답을 압축 해야 하는 경우를 확인 하려면이 값을 확인 합니다. 미들웨어 집합을 지정 [MIME 형식 기본](#mime-types) 인코딩할 수 있지만 바꾸거나 MIME 형식을 추가할 수 있습니다. |
| `Vary`             | 값을 사용 하 여 서버에서 전송 하는 경우 `Accept-Encoding` 클라이언트와 프록시에는 `Vary` 헤더는 클라이언트에 캐시 해야 하는 프록시를 나타냅니다 (다)의 값을 기반으로 하는 응답을 `Accept-Encoding` 요청의 헤더입니다. 사용 하 여 콘텐츠를 반환 하는 결과 `Vary: Accept-Encoding` 헤더는 모두 압축 하 고 압축 되지 않은 응답이 별도로 캐시 됩니다. |

사용 하 여 응답 압축 미들웨어의 기능을 탐색 합니다 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)합니다. 이 샘플에서는 다음을 보여 줍니다.

* Gzip 및 사용자 지정 압축 공급자를 사용 하 여 앱 응답을 압축 합니다.
* 압축에 대 한 MIME 형식 목록을 기본 MIME 형식을 추가 하는 방법.

## <a name="package"></a>패키지

::: moniker range=">= aspnetcore-2.1"

미들웨어를 프로젝트에 포함 하려면에 대 한 참조를 추가 합니다 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)를 포함 하는 합니다 [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 패키지.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

미들웨어를 프로젝트에 포함 하려면에 대 한 참조를 추가 합니다 [Microsoft.AspNetCore.All 메타 패키지](xref:fundamentals/metapackage)를 포함 하는 합니다 [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 패키지.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

미들웨어를 프로젝트에 포함 하려면에 대 한 참조를 추가 합니다 [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 패키지 있습니다.

::: moniker-end

## <a name="configuration"></a>Configuration

::: moniker range=">= aspnetcore-2.2"

다음 코드에 기본 MIME 형식 및 압축 공급자에 대 한 응답 압축 미들웨어를 사용 하도록 설정 하는 방법을 보여 줍니다 ([Brotli](#brotli-compression-provider) 하 고 [Gzip](#gzip-compression-provider)):

::: moniker-end

::: moniker range="< aspnetcore-2.2"

다음 코드에서는 기본 MIME 형식에 대 한 응답 압축 미들웨어를 사용 하도록 설정 하 고 [Gzip 압축 공급자](#gzip-compression-provider):

::: moniker-end

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

참고:

* `app.UseResponseCompression` 먼저 호출 해야 `app.UseMvc`합니다.
* 와 같은 도구를 사용 [Fiddler](https://www.telerik.com/fiddler)를 [Firebug](https://getfirebug.com/), 또는 [Postman](https://www.getpostman.com/) 설정 하는 `Accept-Encoding` 요청 헤더 및 응답 헤더, 크기 및 본문을 연구 합니다.

요청 하지 않고 샘플 앱을 제출 합니다 `Accept-Encoding` 헤더 응답 압축 되지를 확인 합니다. `Content-Encoding` 및 `Vary` 헤더 응답에 없습니다.

![Accept-encoding 헤더 없이 요청의 결과 보여 주는 fiddler 창입니다. 응답 압축 되지 않습니다.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

샘플 응용 프로그램에 요청을 제출 합니다 `Accept-Encoding: br` 헤더 (Brotli 압축) 응답 압축 되어 있는지 확인 합니다. 합니다 `Content-Encoding` 고 `Vary` 헤더가 응답에 있는 합니다.

![Accept-encoding 헤더를 사용 하 여 요청의 결과 및 브라질의 값을 보여 주는 fiddler 창입니다. Vary 및 Content-encoding 헤더를 응답에 추가 됩니다. 응답 압축 됩니다.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

샘플 응용 프로그램에 요청을 제출 합니다 `Accept-Encoding: gzip` 헤더 응답 압축 되어 있는지 확인 합니다. 합니다 `Content-Encoding` 고 `Vary` 헤더가 응답에 있는 합니다.

![Accept-encoding 헤더를 사용 하 여 요청의 결과 및 gzip 값을 보여 주는 fiddler 창입니다. Vary 및 Content-encoding 헤더를 응답에 추가 됩니다. 응답 압축 됩니다.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a>공급자

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a>Brotli 압축 공급자

사용 합니다 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> 사용 하 여 응답을 압축 하는 [Brotli 압축 된 데이터 형식](https://tools.ietf.org/html/rfc7932)합니다.

압축 공급자가 없습니다에 명시적으로 추가 되는 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

* Brotli 압축 공급자가 함께 압축 공급자의 배열에 기본적으로 추가 합니다 [Gzip 압축 공급자](#gzip-compression-provider)합니다.
* Brotli 압축 Brotli 압축 된 데이터 형식을 클라이언트에서 지원 되는 압축 기본값은입니다. 클라이언트에서 지원 되지 않으면 Brotli 압축 기본값은 Gzip 클라이언트에서 Gzip 압축을 지 원하는 경우입니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

모든 압축 공급자 명시적으로 추가 되 면 Brotoli 압축 공급자를 추가 해야 합니다.

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=5)]

압축을 사용 하 여 수준 설정 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>합니다. 가장 빠른 압축 수준을 Brotli 압축 공급자 기본값 ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel))에 가장 효율적인 압축을 생성 하지 않을 수 있습니다. 가장 효율적인 압축을 원하는 경우 최적의 압축에 대 한 미들웨어를 구성 합니다.

| 압축 수준 | 설명 |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | 압축 결과 출력 최적으로 압축 되지 않으며 경우에 가능한 한 빨리 완료 해야 합니다. |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | 압축 하지 않고 수행 되어야 합니다. |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | 응답은 최적으로 압축 되어야 압축을 완료 하는 데 시간이 많이 걸린다는 하는 경우에 합니다. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

::: moniker-end

### <a name="gzip-compression-provider"></a>Gzip 압축 공급자

사용 합니다 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 사용 하 여 응답을 압축 하는 [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952)합니다.

압축 공급자가 없습니다에 명시적으로 추가 되는 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

::: moniker range=">= aspnetcore-2.2"

* Gzip 압축 공급자가 함께 압축 공급자의 배열에 기본적으로 추가 합니다 [Brotli 압축 공급자](#brotli-compression-provider)합니다.
* Brotli 압축 Brotli 압축 된 데이터 형식을 클라이언트에서 지원 되는 압축 기본값은입니다. 클라이언트에서 지원 되지 않으면 Brotli 압축 기본값은 Gzip 클라이언트에서 Gzip 압축을 지 원하는 경우입니다.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Gzip 압축 공급자는 기본적으로 압축 공급자의 배열에 추가 됩니다.
* 클라이언트에서 Gzip 압축을 지 원하는 경우 Gzip 압축 기본값은입니다.

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Gzip 압축 공급자는 모든 압축 공급자 명시적으로 추가 되 면 추가 되어야 합니다.

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5)]

::: moniker-end

압축을 사용 하 여 수준 설정 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>합니다. 가장 빠른 압축 수준을 기본값은 Gzip 압축 공급자 ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel))에 가장 효율적인 압축을 생성 하지 않을 수 있습니다. 가장 효율적인 압축을 원하는 경우 최적의 압축에 대 한 미들웨어를 구성 합니다.

| 압축 수준 | 설명 |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | 압축 결과 출력 최적으로 압축 되지 않으며 경우에 가능한 한 빨리 완료 해야 합니다. |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | 압축 하지 않고 수행 되어야 합니다. |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | 응답은 최적으로 압축 되어야 압축을 완료 하는 데 시간이 많이 걸린다는 하는 경우에 합니다. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a>사용자 지정 공급자

사용 하 여 사용자 지정 압축 구현을 만들 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>합니다. 합니다 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> 이 인코딩 콘텐츠를 나타내는 `ICompressionProvider` 생성 합니다. 에 지정 된 목록에 따라 공급자를 선택 하 여이 정보를 사용 하는 미들웨어를 `Accept-Encoding` 요청의 헤더입니다.

클라이언트를 사용 하 여 요청을 제출 샘플 앱을 사용 하 여 `Accept-Encoding: mycustomcompression` 헤더입니다. 미들웨어는 사용자 지정 압축 구현을 사용 및 사용 하 여 응답을 반환 합니다를 `Content-Encoding: mycustomcompression` 헤더입니다. 클라이언트는 작동 하기 위한 사용자 지정 압축 구현에 대 한 순서에서 사용자 지정 인코딩 압축을 풀 수 있어야 합니다.

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=6)]

[!code-csharp[](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

샘플 응용 프로그램에 요청을 제출 합니다 `Accept-Encoding: mycustomcompression` 헤더 응답 헤더를 확인 합니다. 합니다 `Vary` 고 `Content-Encoding` 헤더가 응답에 있는 합니다. 응답 본문 (표시 되지 않음) 샘플에서 압축 되지 않습니다. 에 압축 구현 하지 않습니다는 `CustomCompressionProvider` 샘플의 클래스입니다. 그러나 샘플 이러한 압축 알고리즘을 구현 하는 위치를 보여줍니다.

![Accept-encoding 헤더를 사용 하 여 요청의 결과 및 mycustomcompression 값을 보여 주는 fiddler 창입니다. Vary 및 Content-encoding 헤더를 응답에 추가 됩니다.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME 형식

미들웨어 압축에 대 한 MIME 형식의 기본 집합을 지정합니다.

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

바꾸거나 응답 압축 미들웨어 옵션을 사용 하 여 MIME 형식을 추가 합니다. 참고 해당 와일드 카드 MIME 형식을 `text/*` 지원 되지 않습니다. 샘플 앱에 대 한 MIME 형식 추가 `image/svg+xml` 압축 및 ASP.NET Core 배너 이미지는 (*banner.svg*).

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7-9)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a>보안 프로토콜을 사용 하 여 압축

보안 연결을 통해 압축 된 응답으로 제어할 수 있습니다는 `EnableForHttps` 옵션을 기본적으로 비활성화 됩니다. 와 같은 보안 문제가 발생할 수 있습니다 동적으로 생성 된 페이지를 사용 하 여 압축을 사용 하는 [범죄](https://wikipedia.org/wiki/CRIME_(security_exploit)) 하 고 [위반](https://wikipedia.org/wiki/BREACH_(security_exploit)) 공격입니다.

## <a name="adding-the-vary-header"></a>Vary 헤더를 추가합니다.

::: moniker range=">= aspnetcore-2.0"

경우에 따라 응답을 압축 된 `Accept-Encoding` 헤더 가지 잠재적으로 여러 개의 압축 된 버전의 응답 및 압축 되지 않은 버전을 합니다. 여러 버전 있으며 저장 하는 클라이언트와 프록시 캐시 지시 하려면 합니다 `Vary` 함께 머리글이 추가 됩니다는 `Accept-Encoding` 값입니다. 미들웨어를 추가 하는 ASP.NET Core 2.0 이상에서는 `Vary` 응답 압축 되어 있을 때 자동으로 헤더입니다.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

경우에 따라 응답을 압축 된 `Accept-Encoding` 헤더 가지 잠재적으로 여러 개의 압축 된 버전의 응답 및 압축 되지 않은 버전을 합니다. 여러 버전 있으며 저장 하는 클라이언트와 프록시 캐시 지시 하려면 합니다 `Vary` 함께 머리글이 추가 됩니다는 `Accept-Encoding` 값입니다. ASP.NET core에서 1.x에서 추가 `Vary` 헤더를 응답은 수동으로 수행:

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Nginx 역방향 프록시 뒤에 있을 때는 미들웨어 문제

Nginx에서 프록시를 요청 하는 경우는 `Accept-Encoding` 헤더를 제거 합니다. 제거 된 `Accept-Encoding` 헤더에서 응답 압축 미들웨어를 방지 합니다. 자세한 내용은 [NGINX: 압축 및 압축 풀기](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)합니다. 이 문제를 추적 하 여 [Nginx에 대 한 통과 압축 파악 (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123)합니다.

## <a name="working-with-iis-dynamic-compression"></a>IIS 동적 압축이 사용

활성 IIS 동적 압축이 모듈 앱에 대해 사용 하지 않도록 설정 하려는 서버 수준에서 구성에 있는 경우에 대 한 추가 사용 하 여 모듈을 사용 하지 않도록 설정 합니다 *web.config* 파일입니다. 보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.

## <a name="troubleshooting"></a>문제 해결

와 같은 도구를 사용 하 여 [Fiddler](https://www.telerik.com/fiddler)를 [Firebug](https://getfirebug.com/), 또는 [Postman](https://www.getpostman.com/)를 설정할 수 있도록는 `Accept-Encoding` 요청 헤더 및 응답 헤더, 크기 및 본문을 연구 합니다. 기본적으로 응답 압축 미들웨어는 다음 조건을 충족 하는 응답을 압축 합니다.

::: moniker range=">= aspnetcore-2.2"

* 합니다 `Accept-Encoding` 헤더 값이 있는지 `br`, `gzip`, `*`를 설정 하는 사용자 지정 압축 공급자와 일치 하는 사용자 지정 인코딩 또는 합니다. 값이 아니어야 `identity` 품질 값 또는 (qvalue, `q`) 0 (영)을 설정 합니다.
* MIME 형식 (`Content-Type`) 설정 해야 하며에 구성 된 MIME 형식과 일치 해야 합니다 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>합니다.
* 요청을 포함 하지 않아야 합니다 `Content-Range` 헤더입니다.
* 요청 응답 압축 미들웨어 옵션에서 보안 프로토콜 (https)를 구성 하지 않으면 안전 하지 않은 프로토콜 (http)를 사용 해야 합니다. *위험 유의 [위에서 설명한](#compression-with-secure-protocol) 보안 콘텐츠 압축을 사용 하도록 설정할 때.*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* `Accept-Encoding` 헤더의 값을 사용 하 여 있는지 `gzip`, `*`를 설정 하는 사용자 지정 압축 공급자와 일치 하는 사용자 지정 인코딩 또는 합니다. 값이 아니어야 `identity` 품질 값 또는 (qvalue, `q`) 0 (영)을 설정 합니다.
* MIME 형식 (`Content-Type`) 설정 해야 하며에 구성 된 MIME 형식과 일치 해야 합니다 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>합니다.
* 요청을 포함 하지 않아야 합니다 `Content-Range` 헤더입니다.
* 요청 응답 압축 미들웨어 옵션에서 보안 프로토콜 (https)를 구성 하지 않으면 안전 하지 않은 프로토콜 (http)를 사용 해야 합니다. *위험 유의 [위에서 설명한](#compression-with-secure-protocol) 보안 콘텐츠 압축을 사용 하도록 설정할 때.*

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla Developer Network: Accept-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 섹션 3.1.2.1: 콘텐츠 구분을 사용 합니다.](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 섹션 4.2.3: Gzip 코딩](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP 파일 형식 사양 버전 4.3](https://www.ietf.org/rfc/rfc1952.txt)
