---
title: ASP.NET Core 응답 압축
author: guardrex
description: ASP.NET Core 앱에서 응답 압축 미들웨어를 사용하는 방법 및 응답 압축에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: performance/response-compression
ms.openlocfilehash: d37b05edd55ac0d3910855563b819114cf815b43
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114811"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="ab75a-103">ASP.NET Core 응답 압축</span><span class="sxs-lookup"><span data-stu-id="ab75a-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="ab75a-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="ab75a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ab75a-105">네트워크 대역폭은 제한 된 리소스입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-105">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="ab75a-106">응답 크기를 줄이면 일반적으로 앱의 응답성이 크게 향상 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-106">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="ab75a-107">페이로드 크기를 줄이는 한 가지 방법은 앱의 응답을 압축 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-107">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="ab75a-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ab75a-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="ab75a-109">응답 압축 미들웨어를 사용 하는 경우</span><span class="sxs-lookup"><span data-stu-id="ab75a-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="ab75a-110">IIS, Apache 또는 Nginx에서 서버 기반 응답 압축 기술을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="ab75a-111">미들웨어의 성능은 서버 모듈의 성능과 일치 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="ab75a-112">[Http.sys 서버](xref:fundamentals/servers/httpsys) 서버와 [kestrel](xref:fundamentals/servers/kestrel) 서버는 현재 기본 제공 압축 지원을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="ab75a-113">응답 압축 미들웨어를 사용 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="ab75a-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="ab75a-114">다음 서버 기반 압축 기술을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="ab75a-115">IIS 동적 압축 모듈</span><span class="sxs-lookup"><span data-stu-id="ab75a-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="ab75a-116">Apache mod_deflate 모듈</span><span class="sxs-lookup"><span data-stu-id="ab75a-116">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="ab75a-117">Nginx 압축 및 압축 풀기</span><span class="sxs-lookup"><span data-stu-id="ab75a-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="ab75a-118">에서 직접 호스팅:</span><span class="sxs-lookup"><span data-stu-id="ab75a-118">Hosting directly on:</span></span>
  * <span data-ttu-id="ab75a-119">[Http.sys 서버](xref:fundamentals/servers/httpsys) (이전에는 weblistener 라고 함)</span><span class="sxs-lookup"><span data-stu-id="ab75a-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="ab75a-120">Kestrel 서버</span><span class="sxs-lookup"><span data-stu-id="ab75a-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="ab75a-121">응답 압축</span><span class="sxs-lookup"><span data-stu-id="ab75a-121">Response compression</span></span>

<span data-ttu-id="ab75a-122">일반적으로 기본적으로 압축 되지 않은 모든 응답은 응답 압축의 이점을 누릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="ab75a-123">기본적으로 압축 되지 않은 응답은 일반적으로 CSS, JavaScript, HTML, XML 및 JSON을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="ab75a-124">PNG 파일과 같이 기본적으로 압축 된 자산은 압축 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="ab75a-125">고유 하 게 압축 된 응답을 추가로 압축 하려는 경우 크기 및 전송 시간의 작은 추가 감소는 압축을 처리 하는 데 걸린 시간을 overshadowed 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="ab75a-126">파일의 내용과 압축 효율성에 따라 약 150-1000 바이트 보다 작은 파일은 압축 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="ab75a-127">작은 파일을 압축 하는 오버 헤드로 인해 압축 되지 않은 파일 보다 큰 압축 된 파일이 생성 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="ab75a-128">클라이언트가 압축 된 콘텐츠를 처리할 수 있는 경우 클라이언트는 요청과 함께 `Accept-Encoding` 헤더를 전송 하 여 해당 기능을 서버에 알려야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="ab75a-129">서버는 압축 된 콘텐츠를 보낼 때 압축 된 응답을 인코딩하는 방법에 대 한 정보를 `Content-Encoding` 헤더에 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="ab75a-130">미들웨어에서 지 원하는 콘텐츠 인코딩 명칭은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="ab75a-131">`Accept-Encoding` 헤더 값</span><span class="sxs-lookup"><span data-stu-id="ab75a-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="ab75a-132">미들웨어 지원</span><span class="sxs-lookup"><span data-stu-id="ab75a-132">Middleware Supported</span></span> | <span data-ttu-id="ab75a-133">설명</span><span class="sxs-lookup"><span data-stu-id="ab75a-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="ab75a-134">예 (기본값)</span><span class="sxs-lookup"><span data-stu-id="ab75a-134">Yes (default)</span></span>        | [<span data-ttu-id="ab75a-135">Brotli 압축 된 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="ab75a-136">아니요</span><span class="sxs-lookup"><span data-stu-id="ab75a-136">No</span></span>                   | [<span data-ttu-id="ab75a-137">DEFLATE 압축 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="ab75a-138">아니요</span><span class="sxs-lookup"><span data-stu-id="ab75a-138">No</span></span>                   | [<span data-ttu-id="ab75a-139">W3C 효율적인 XML 교환</span><span class="sxs-lookup"><span data-stu-id="ab75a-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="ab75a-140">예</span><span class="sxs-lookup"><span data-stu-id="ab75a-140">Yes</span></span>                  | [<span data-ttu-id="ab75a-141">Gzip 파일 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="ab75a-142">예</span><span class="sxs-lookup"><span data-stu-id="ab75a-142">Yes</span></span>                  | <span data-ttu-id="ab75a-143">"인코딩 안 함" 식별자: 응답은 인코딩되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="ab75a-144">아니요</span><span class="sxs-lookup"><span data-stu-id="ab75a-144">No</span></span>                   | [<span data-ttu-id="ab75a-145">Java 보관을 위한 네트워크 전송 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="ab75a-146">예</span><span class="sxs-lookup"><span data-stu-id="ab75a-146">Yes</span></span>                  | <span data-ttu-id="ab75a-147">명시적으로 요청 되지 않은 모든 사용 가능한 콘텐츠 인코딩입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-147">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="ab75a-148">자세한 내용은 [IANA 공식 콘텐츠 코딩 목록](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-148">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="ab75a-149">미들웨어를 사용 하면 사용자 지정 `Accept-Encoding` 헤더 값에 대 한 추가 압축 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-149">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="ab75a-150">자세한 내용은 아래의 [사용자 지정 공급자](#custom-providers) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-150">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="ab75a-151">미들웨어는 압축 체계의 우선 순위를 지정 하기 위해 클라이언트에서 보낼 때 품질 값 (qvalue, `q`) 가중치에 대응 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-151">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="ab75a-152">자세한 내용은 [RFC 7231: 수락-인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-152">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="ab75a-153">압축 알고리즘은 압축 속도와 압축의 효율성 사이의 균형을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-153">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="ab75a-154">이 컨텍스트의 *효과* 는 압축 후 출력의 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-154">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="ab75a-155">가장 작은 크기는 가장 *최적* 압축을 통해 달성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-155">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="ab75a-156">다음 표에서는 압축 된 콘텐츠 요청, 송신, 캐싱 및 수신에 관련 된 헤더에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-156">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="ab75a-157">헤더</span><span class="sxs-lookup"><span data-stu-id="ab75a-157">Header</span></span>             | <span data-ttu-id="ab75a-158">Role</span><span class="sxs-lookup"><span data-stu-id="ab75a-158">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="ab75a-159">클라이언트에서 서버로 전송 되어 클라이언트에 허용 되는 콘텐츠 인코딩 스키마를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-159">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="ab75a-160">페이로드에 있는 콘텐츠의 인코딩을 나타내기 위해 서버에서 클라이언트로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-160">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="ab75a-161">압축이 발생 하면 응답이 압축 될 때 본문 내용이 변경 되기 때문에 `Content-Length` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-161">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="ab75a-162">압축이 발생 하면 본문 내용이 변경 되 고 해시가 더 이상 유효 하지 않기 때문에 `Content-MD5` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-162">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="ab75a-163">콘텐츠의 MIME 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-163">Specifies the MIME type of the content.</span></span> <span data-ttu-id="ab75a-164">모든 응답은 해당 `Content-Type`을 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-164">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="ab75a-165">미들웨어는이 값을 확인 하 여 응답이 압축 되어야 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-165">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="ab75a-166">미들웨어는 인코딩할 수 있는 [기본 MIME 형식의](#mime-types) 집합을 지정 하지만 mime 형식을 바꾸거나 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-166">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="ab75a-167">클라이언트 및 프록시에 `Accept-Encoding` 값을 사용 하 여 서버에서 보낸 경우 `Vary` 헤더는 요청의 `Accept-Encoding` 헤더 값을 기준으로 응답을 캐시 (vary) 해야 함을 클라이언트 또는 프록시에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-167">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="ab75a-168">`Vary: Accept-Encoding` 헤더를 사용 하 여 콘텐츠를 반환한 결과는 압축 된 응답과 압축 되지 않은 응답이 모두 개별적으로 캐시 된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-168">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="ab75a-169">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)을 사용 하 여 응답 압축 미들웨어의 기능을 탐색 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-169">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="ab75a-170">샘플은 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-170">The sample illustrates:</span></span>

* <span data-ttu-id="ab75a-171">Gzip 및 사용자 지정 압축 공급자를 사용 하 여 앱 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-171">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="ab75a-172">Mime 형식을 압축을 위한 MIME 형식의 기본 목록에 추가 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-172">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="ab75a-173">패키지</span><span class="sxs-lookup"><span data-stu-id="ab75a-173">Package</span></span>

<span data-ttu-id="ab75a-174">응답 압축 미들웨어는 ASP.NET Core 앱에 암시적으로 포함 되는 [ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 패키지에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-174">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="configuration"></a><span data-ttu-id="ab75a-175">구성</span><span class="sxs-lookup"><span data-stu-id="ab75a-175">Configuration</span></span>

<span data-ttu-id="ab75a-176">다음 코드에서는 기본 MIME 형식 및 압축 공급자 ([Brotli](#brotli-compression-provider) 및 [Gzip](#gzip-compression-provider))에 대 한 응답 압축 미들웨어를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-176">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="ab75a-177">참고:</span><span class="sxs-lookup"><span data-stu-id="ab75a-177">Notes:</span></span>

* <span data-ttu-id="ab75a-178">`app.UseResponseCompression`는 응답을 압축 하는 미들웨어 보다 먼저 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-178">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="ab75a-179">자세한 내용은 <xref:fundamentals/middleware/index#middleware-order>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-179">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="ab75a-180">[Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/) 과 같은 도구를 사용 하 여 `Accept-Encoding` 요청 헤더를 설정 하 고 응답 헤더, 크기 및 본문을 연구 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-180">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="ab75a-181">`Accept-Encoding` 헤더를 제외 하 고 샘플 앱에 요청을 제출 하 고 응답이 압축 되지 않은 상태 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-181">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="ab75a-182">`Content-Encoding` 및 `Vary` 헤더가 응답에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-182">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![수락 인코딩 헤더가 없는 요청 결과를 보여 주는 Fiddler 창](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="ab75a-185">`Accept-Encoding: br` 헤더 (Brotli 압축)를 사용 하 여 샘플 앱에 요청을 제출 하 고 응답이 압축 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-185">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="ab75a-186">`Content-Encoding` 및 `Vary` 헤더가 응답에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-186">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![허용 인코딩 헤더와 값이 br 인 요청 결과를 보여 주는 Fiddler 창입니다.](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="ab75a-190">공급자</span><span class="sxs-lookup"><span data-stu-id="ab75a-190">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="ab75a-191">Brotli 압축 공급자</span><span class="sxs-lookup"><span data-stu-id="ab75a-191">Brotli Compression Provider</span></span>

<span data-ttu-id="ab75a-192"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>를 사용 하 여 [Brotli 압축 된 데이터 형식의](https://tools.ietf.org/html/rfc7932)응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-192">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="ab75a-193"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>에 명시적으로 추가 된 압축 공급자가 없으면:</span><span class="sxs-lookup"><span data-stu-id="ab75a-193">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="ab75a-194">Brotli 압축 공급자는 기본적으로 [Gzip 압축 공급자](#gzip-compression-provider)와 함께 압축 공급자 배열에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-194">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="ab75a-195">클라이언트에서 Brotli 압축 데이터 형식이 지원 되는 경우 압축의 기본값은 Brotli 압축입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-195">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="ab75a-196">클라이언트에서 Brotli을 지원 하지 않는 경우 클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-196">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="ab75a-197">압축 공급자가 명시적으로 추가 되 면 Brotoli 압축 공급자를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-197">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="ab75a-198"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>압축 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-198">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="ab75a-199">Brotli 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며,이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-199">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="ab75a-200">가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-200">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="ab75a-201">압축 수준</span><span class="sxs-lookup"><span data-stu-id="ab75a-201">Compression Level</span></span> | <span data-ttu-id="ab75a-202">설명</span><span class="sxs-lookup"><span data-stu-id="ab75a-202">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="ab75a-203">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="ab75a-203">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-204">결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-204">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="ab75a-205">CompressionLevel 압축</span><span class="sxs-lookup"><span data-stu-id="ab75a-205">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-206">압축을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-206">No compression should be performed.</span></span> |
| [<span data-ttu-id="ab75a-207">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="ab75a-207">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-208">압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-208">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="ab75a-209">Gzip 압축 공급자</span><span class="sxs-lookup"><span data-stu-id="ab75a-209">Gzip Compression Provider</span></span>

<span data-ttu-id="ab75a-210"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>를 사용 하 여 [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952)으로 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-210">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="ab75a-211"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>에 명시적으로 추가 된 압축 공급자가 없으면:</span><span class="sxs-lookup"><span data-stu-id="ab75a-211">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="ab75a-212">Gzip 압축 공급자는 기본적으로 [Brotli 압축](#brotli-compression-provider)공급자와 함께 압축 공급자 배열에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-212">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="ab75a-213">클라이언트에서 Brotli 압축 데이터 형식이 지원 되는 경우 압축의 기본값은 Brotli 압축입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-213">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="ab75a-214">클라이언트에서 Brotli을 지원 하지 않는 경우 클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-214">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="ab75a-215">모든 압축 공급자가 명시적으로 추가 되 면 Gzip 압축 공급자를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-215">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="ab75a-216"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>압축 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-216">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="ab75a-217">Gzip 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-217">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="ab75a-218">가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-218">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="ab75a-219">압축 수준</span><span class="sxs-lookup"><span data-stu-id="ab75a-219">Compression Level</span></span> | <span data-ttu-id="ab75a-220">설명</span><span class="sxs-lookup"><span data-stu-id="ab75a-220">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="ab75a-221">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="ab75a-221">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-222">결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-222">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="ab75a-223">CompressionLevel 압축</span><span class="sxs-lookup"><span data-stu-id="ab75a-223">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-224">압축을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-224">No compression should be performed.</span></span> |
| [<span data-ttu-id="ab75a-225">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="ab75a-225">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-226">압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-226">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="ab75a-227">사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="ab75a-227">Custom providers</span></span>

<span data-ttu-id="ab75a-228"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>를 사용 하 여 사용자 지정 압축 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-228">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="ab75a-229"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>는이 `ICompressionProvider` 생성 하는 콘텐츠 인코딩을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-229">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="ab75a-230">미들웨어는이 정보를 사용 하 여 요청의 `Accept-Encoding` 헤더에 지정 된 목록에 따라 공급자를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-230">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="ab75a-231">클라이언트는 샘플 앱을 사용 하 여 `Accept-Encoding: mycustomcompression` 헤더를 사용 하 여 요청을 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-231">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="ab75a-232">미들웨어는 사용자 지정 압축 구현을 사용 하 고 `Content-Encoding: mycustomcompression` 헤더가 포함 된 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-232">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="ab75a-233">클라이언트는 사용자 지정 압축 구현이 작동 하기 위해 사용자 지정 인코딩의 압축을 해제할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-233">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


<span data-ttu-id="ab75a-234">`Accept-Encoding: mycustomcompression` 헤더를 사용 하 여 샘플 앱에 요청을 제출 하 고 응답 헤더를 관찰 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-234">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="ab75a-235">`Vary` 및 `Content-Encoding` 헤더가 응답에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-235">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="ab75a-236">응답 본문 (표시 되지 않음)은 샘플에 의해 압축 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-236">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="ab75a-237">샘플의 `CustomCompressionProvider` 클래스에는 압축 구현이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-237">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="ab75a-238">그러나이 샘플에서는 이러한 압축 알고리즘을 구현 하는 위치를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-238">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 헤더를 포함 하는 요청 결과와 mycustomcompression 값을 보여 주는 창](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="ab75a-241">MIME 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-241">MIME types</span></span>

<span data-ttu-id="ab75a-242">미들웨어는 압축을 위한 기본 MIME 형식 집합을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-242">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="ab75a-243">MIME 형식을 응답 압축 미들웨어 옵션으로 바꾸거나 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-243">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="ab75a-244">`text/*`와 같은 와일드 카드 MIME 형식은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-244">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="ab75a-245">샘플 앱은 `image/svg+xml`에 대 한 MIME 형식을 추가 하 고 압축 하 여 ASP.NET Core 배너 이미지 (*배너나*)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-245">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="ab75a-246">보안 프로토콜을 사용한 압축</span><span class="sxs-lookup"><span data-stu-id="ab75a-246">Compression with secure protocol</span></span>

<span data-ttu-id="ab75a-247">보안 연결을 통한 압축 된 응답은 기본적으로 사용 하지 않도록 설정 된 `EnableForHttps` 옵션을 사용 하 여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-247">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="ab75a-248">동적으로 생성 된 페이지에서 압축을 사용 하면 [범죄](https://wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://wikipedia.org/wiki/BREACH_(security_exploit)) 공격과 같은 보안 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-248">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="ab75a-249">Vary 헤더 추가</span><span class="sxs-lookup"><span data-stu-id="ab75a-249">Adding the Vary header</span></span>

<span data-ttu-id="ab75a-250">`Accept-Encoding` 헤더를 기반으로 응답을 압축 하는 경우 여러 압축 버전의 응답 및 압축 되지 않은 버전이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-250">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="ab75a-251">여러 버전이 존재 하 고 저장 해야 함을 클라이언트 및 프록시 캐시에 지시 하기 위해 `Vary` 헤더가 `Accept-Encoding` 값으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-251">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="ab75a-252">ASP.NET Core 2.0 이상에서 미들웨어는 응답이 압축 될 때 `Vary` 헤더를 자동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-252">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="ab75a-253">Nginx 역방향 프록시 뒤에 있는 미들웨어 문제</span><span class="sxs-lookup"><span data-stu-id="ab75a-253">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="ab75a-254">Nginx에서 요청을 프록시 하는 경우에는 `Accept-Encoding` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-254">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="ab75a-255">`Accept-Encoding` 헤더를 제거 하면 미들웨어가 응답을 압축 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-255">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="ab75a-256">자세한 내용은 [NGINX: 압축 및](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)압축 해제를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-256">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="ab75a-257">이 문제는 [Nginx (aspnet/BasicMiddleware #123)에 대 한 통과 압축](https://github.com/aspnet/BasicMiddleware/issues/123)을 통해 추적 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-257">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="ab75a-258">IIS 동적 압축 사용</span><span class="sxs-lookup"><span data-stu-id="ab75a-258">Working with IIS dynamic compression</span></span>

<span data-ttu-id="ab75a-259">앱에 대해 사용 하지 않도록 설정할 서버 수준에서 활성 IIS 동적 압축 모듈이 구성 된 경우 *web.config* 파일에 추가 하 여 모듈을 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-259">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="ab75a-260">보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-260">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ab75a-261">문제 해결</span><span class="sxs-lookup"><span data-stu-id="ab75a-261">Troubleshooting</span></span>

<span data-ttu-id="ab75a-262">`Accept-Encoding` 요청 헤더를 설정 하 고 응답 헤더, 크기 및 본문을 학습할 수 있는 [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-262">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="ab75a-263">기본적으로 응답 압축 미들웨어는 다음 조건을 충족 하는 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-263">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="ab75a-264">`Accept-Encoding` 헤더는 설정 된 사용자 지정 압축 공급자와 일치 하는 `br`, `gzip`, `*`또는 사용자 지정 인코딩의 값과 함께 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-264">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="ab75a-265">값을 `identity` 하거나 품질 값 (qvalue, `q`)을 0 (영)으로 설정 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-265">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="ab75a-266">MIME 형식 (`Content-Type`)을 설정 하 고 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>에 구성 된 MIME 형식과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-266">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="ab75a-267">요청은 `Content-Range` 헤더를 포함 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-267">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="ab75a-268">응답 압축 미들웨어 옵션에 보안 프로토콜 (https)이 구성 되지 않은 경우 요청은 안전 하지 않은 프로토콜 (http)을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-268">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="ab75a-269">*보안 콘텐츠 압축을 사용 하도록 설정할 때 [위에서 설명한](#compression-with-secure-protocol) 위험에 유의 하십시오.*</span><span class="sxs-lookup"><span data-stu-id="ab75a-269">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ab75a-270">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ab75a-270">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="ab75a-271">Mozilla 개발자 네트워크: 수락-인코딩</span><span class="sxs-lookup"><span data-stu-id="ab75a-271">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="ab75a-272">RFC 7231 섹션 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="ab75a-272">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="ab75a-273">RFC 7230 섹션 4.2.3: Gzip 코딩</span><span class="sxs-lookup"><span data-stu-id="ab75a-273">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="ab75a-274">GZIP 파일 형식 사양 버전 4.3</span><span class="sxs-lookup"><span data-stu-id="ab75a-274">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ab75a-275">네트워크 대역폭은 제한 된 리소스입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-275">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="ab75a-276">응답 크기를 줄이면 일반적으로 앱의 응답성이 크게 향상 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-276">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="ab75a-277">페이로드 크기를 줄이는 한 가지 방법은 앱의 응답을 압축 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-277">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="ab75a-278">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ab75a-278">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="ab75a-279">응답 압축 미들웨어를 사용 하는 경우</span><span class="sxs-lookup"><span data-stu-id="ab75a-279">When to use Response Compression Middleware</span></span>

<span data-ttu-id="ab75a-280">IIS, Apache 또는 Nginx에서 서버 기반 응답 압축 기술을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-280">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="ab75a-281">미들웨어의 성능은 서버 모듈의 성능과 일치 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-281">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="ab75a-282">[Http.sys 서버](xref:fundamentals/servers/httpsys) 서버와 [kestrel](xref:fundamentals/servers/kestrel) 서버는 현재 기본 제공 압축 지원을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-282">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="ab75a-283">응답 압축 미들웨어를 사용 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="ab75a-283">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="ab75a-284">다음 서버 기반 압축 기술을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-284">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="ab75a-285">IIS 동적 압축 모듈</span><span class="sxs-lookup"><span data-stu-id="ab75a-285">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="ab75a-286">Apache mod_deflate 모듈</span><span class="sxs-lookup"><span data-stu-id="ab75a-286">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="ab75a-287">Nginx 압축 및 압축 풀기</span><span class="sxs-lookup"><span data-stu-id="ab75a-287">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="ab75a-288">에서 직접 호스팅:</span><span class="sxs-lookup"><span data-stu-id="ab75a-288">Hosting directly on:</span></span>
  * <span data-ttu-id="ab75a-289">[Http.sys 서버](xref:fundamentals/servers/httpsys) (이전에는 weblistener 라고 함)</span><span class="sxs-lookup"><span data-stu-id="ab75a-289">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="ab75a-290">Kestrel 서버</span><span class="sxs-lookup"><span data-stu-id="ab75a-290">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="ab75a-291">응답 압축</span><span class="sxs-lookup"><span data-stu-id="ab75a-291">Response compression</span></span>

<span data-ttu-id="ab75a-292">일반적으로 기본적으로 압축 되지 않은 모든 응답은 응답 압축의 이점을 누릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-292">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="ab75a-293">기본적으로 압축 되지 않은 응답은 일반적으로 CSS, JavaScript, HTML, XML 및 JSON을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-293">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="ab75a-294">PNG 파일과 같이 기본적으로 압축 된 자산은 압축 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-294">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="ab75a-295">고유 하 게 압축 된 응답을 추가로 압축 하려는 경우 크기 및 전송 시간의 작은 추가 감소는 압축을 처리 하는 데 걸린 시간을 overshadowed 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-295">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="ab75a-296">파일의 내용과 압축 효율성에 따라 약 150-1000 바이트 보다 작은 파일은 압축 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-296">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="ab75a-297">작은 파일을 압축 하는 오버 헤드로 인해 압축 되지 않은 파일 보다 큰 압축 된 파일이 생성 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-297">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="ab75a-298">클라이언트가 압축 된 콘텐츠를 처리할 수 있는 경우 클라이언트는 요청과 함께 `Accept-Encoding` 헤더를 전송 하 여 해당 기능을 서버에 알려야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-298">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="ab75a-299">서버는 압축 된 콘텐츠를 보낼 때 압축 된 응답을 인코딩하는 방법에 대 한 정보를 `Content-Encoding` 헤더에 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-299">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="ab75a-300">미들웨어에서 지 원하는 콘텐츠 인코딩 명칭은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-300">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="ab75a-301">`Accept-Encoding` 헤더 값</span><span class="sxs-lookup"><span data-stu-id="ab75a-301">`Accept-Encoding` header values</span></span> | <span data-ttu-id="ab75a-302">미들웨어 지원</span><span class="sxs-lookup"><span data-stu-id="ab75a-302">Middleware Supported</span></span> | <span data-ttu-id="ab75a-303">설명</span><span class="sxs-lookup"><span data-stu-id="ab75a-303">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="ab75a-304">예 (기본값)</span><span class="sxs-lookup"><span data-stu-id="ab75a-304">Yes (default)</span></span>        | [<span data-ttu-id="ab75a-305">Brotli 압축 된 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-305">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="ab75a-306">아니요</span><span class="sxs-lookup"><span data-stu-id="ab75a-306">No</span></span>                   | [<span data-ttu-id="ab75a-307">DEFLATE 압축 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-307">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="ab75a-308">아니요</span><span class="sxs-lookup"><span data-stu-id="ab75a-308">No</span></span>                   | [<span data-ttu-id="ab75a-309">W3C 효율적인 XML 교환</span><span class="sxs-lookup"><span data-stu-id="ab75a-309">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="ab75a-310">예</span><span class="sxs-lookup"><span data-stu-id="ab75a-310">Yes</span></span>                  | [<span data-ttu-id="ab75a-311">Gzip 파일 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-311">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="ab75a-312">예</span><span class="sxs-lookup"><span data-stu-id="ab75a-312">Yes</span></span>                  | <span data-ttu-id="ab75a-313">"인코딩 안 함" 식별자: 응답은 인코딩되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-313">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="ab75a-314">아니요</span><span class="sxs-lookup"><span data-stu-id="ab75a-314">No</span></span>                   | [<span data-ttu-id="ab75a-315">Java 보관을 위한 네트워크 전송 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-315">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="ab75a-316">예</span><span class="sxs-lookup"><span data-stu-id="ab75a-316">Yes</span></span>                  | <span data-ttu-id="ab75a-317">명시적으로 요청 되지 않은 모든 사용 가능한 콘텐츠 인코딩입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-317">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="ab75a-318">자세한 내용은 [IANA 공식 콘텐츠 코딩 목록](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-318">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="ab75a-319">미들웨어를 사용 하면 사용자 지정 `Accept-Encoding` 헤더 값에 대 한 추가 압축 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-319">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="ab75a-320">자세한 내용은 아래의 [사용자 지정 공급자](#custom-providers) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-320">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="ab75a-321">미들웨어는 압축 체계의 우선 순위를 지정 하기 위해 클라이언트에서 보낼 때 품질 값 (qvalue, `q`) 가중치에 대응 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-321">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="ab75a-322">자세한 내용은 [RFC 7231: 수락-인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-322">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="ab75a-323">압축 알고리즘은 압축 속도와 압축의 효율성 사이의 균형을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-323">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="ab75a-324">이 컨텍스트의 *효과* 는 압축 후 출력의 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-324">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="ab75a-325">가장 작은 크기는 가장 *최적* 압축을 통해 달성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-325">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="ab75a-326">다음 표에서는 압축 된 콘텐츠 요청, 송신, 캐싱 및 수신에 관련 된 헤더에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-326">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="ab75a-327">헤더</span><span class="sxs-lookup"><span data-stu-id="ab75a-327">Header</span></span>             | <span data-ttu-id="ab75a-328">Role</span><span class="sxs-lookup"><span data-stu-id="ab75a-328">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="ab75a-329">클라이언트에서 서버로 전송 되어 클라이언트에 허용 되는 콘텐츠 인코딩 스키마를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-329">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="ab75a-330">페이로드에 있는 콘텐츠의 인코딩을 나타내기 위해 서버에서 클라이언트로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-330">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="ab75a-331">압축이 발생 하면 응답이 압축 될 때 본문 내용이 변경 되기 때문에 `Content-Length` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-331">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="ab75a-332">압축이 발생 하면 본문 내용이 변경 되 고 해시가 더 이상 유효 하지 않기 때문에 `Content-MD5` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-332">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="ab75a-333">콘텐츠의 MIME 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-333">Specifies the MIME type of the content.</span></span> <span data-ttu-id="ab75a-334">모든 응답은 해당 `Content-Type`을 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-334">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="ab75a-335">미들웨어는이 값을 확인 하 여 응답이 압축 되어야 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-335">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="ab75a-336">미들웨어는 인코딩할 수 있는 [기본 MIME 형식의](#mime-types) 집합을 지정 하지만 mime 형식을 바꾸거나 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-336">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="ab75a-337">클라이언트 및 프록시에 `Accept-Encoding` 값을 사용 하 여 서버에서 보낸 경우 `Vary` 헤더는 요청의 `Accept-Encoding` 헤더 값을 기준으로 응답을 캐시 (vary) 해야 함을 클라이언트 또는 프록시에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-337">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="ab75a-338">`Vary: Accept-Encoding` 헤더를 사용 하 여 콘텐츠를 반환한 결과는 압축 된 응답과 압축 되지 않은 응답이 모두 개별적으로 캐시 된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-338">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="ab75a-339">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)을 사용 하 여 응답 압축 미들웨어의 기능을 탐색 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-339">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="ab75a-340">샘플은 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-340">The sample illustrates:</span></span>

* <span data-ttu-id="ab75a-341">Gzip 및 사용자 지정 압축 공급자를 사용 하 여 앱 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-341">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="ab75a-342">Mime 형식을 압축을 위한 MIME 형식의 기본 목록에 추가 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-342">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="ab75a-343">패키지</span><span class="sxs-lookup"><span data-stu-id="ab75a-343">Package</span></span>

<span data-ttu-id="ab75a-344">프로젝트에 미들웨어를 포함 하려면 [ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 패키지를 포함 하는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에 대 한 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-344">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="ab75a-345">구성</span><span class="sxs-lookup"><span data-stu-id="ab75a-345">Configuration</span></span>

<span data-ttu-id="ab75a-346">다음 코드에서는 기본 MIME 형식 및 압축 공급자 ([Brotli](#brotli-compression-provider) 및 [Gzip](#gzip-compression-provider))에 대 한 응답 압축 미들웨어를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-346">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="ab75a-347">참고:</span><span class="sxs-lookup"><span data-stu-id="ab75a-347">Notes:</span></span>

* <span data-ttu-id="ab75a-348">`app.UseResponseCompression`는 응답을 압축 하는 미들웨어 보다 먼저 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-348">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="ab75a-349">자세한 내용은 <xref:fundamentals/middleware/index#middleware-order>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-349">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="ab75a-350">[Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/) 과 같은 도구를 사용 하 여 `Accept-Encoding` 요청 헤더를 설정 하 고 응답 헤더, 크기 및 본문을 연구 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-350">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="ab75a-351">`Accept-Encoding` 헤더를 제외 하 고 샘플 앱에 요청을 제출 하 고 응답이 압축 되지 않은 상태 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-351">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="ab75a-352">`Content-Encoding` 및 `Vary` 헤더가 응답에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-352">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![수락 인코딩 헤더가 없는 요청 결과를 보여 주는 Fiddler 창](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="ab75a-355">`Accept-Encoding: br` 헤더 (Brotli 압축)를 사용 하 여 샘플 앱에 요청을 제출 하 고 응답이 압축 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-355">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="ab75a-356">`Content-Encoding` 및 `Vary` 헤더가 응답에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-356">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![허용 인코딩 헤더와 값이 br 인 요청 결과를 보여 주는 Fiddler 창입니다.](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="ab75a-360">공급자</span><span class="sxs-lookup"><span data-stu-id="ab75a-360">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="ab75a-361">Brotli 압축 공급자</span><span class="sxs-lookup"><span data-stu-id="ab75a-361">Brotli Compression Provider</span></span>

<span data-ttu-id="ab75a-362"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>를 사용 하 여 [Brotli 압축 된 데이터 형식의](https://tools.ietf.org/html/rfc7932)응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-362">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="ab75a-363"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>에 명시적으로 추가 된 압축 공급자가 없으면:</span><span class="sxs-lookup"><span data-stu-id="ab75a-363">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="ab75a-364">Brotli 압축 공급자는 기본적으로 [Gzip 압축 공급자](#gzip-compression-provider)와 함께 압축 공급자 배열에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-364">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="ab75a-365">클라이언트에서 Brotli 압축 데이터 형식이 지원 되는 경우 압축의 기본값은 Brotli 압축입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-365">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="ab75a-366">클라이언트에서 Brotli을 지원 하지 않는 경우 클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-366">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="ab75a-367">압축 공급자가 명시적으로 추가 되 면 Brotoli 압축 공급자를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-367">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="ab75a-368"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>압축 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-368">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="ab75a-369">Brotli 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며,이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-369">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="ab75a-370">가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-370">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="ab75a-371">압축 수준</span><span class="sxs-lookup"><span data-stu-id="ab75a-371">Compression Level</span></span> | <span data-ttu-id="ab75a-372">설명</span><span class="sxs-lookup"><span data-stu-id="ab75a-372">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="ab75a-373">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="ab75a-373">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-374">결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-374">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="ab75a-375">CompressionLevel 압축</span><span class="sxs-lookup"><span data-stu-id="ab75a-375">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-376">압축을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-376">No compression should be performed.</span></span> |
| [<span data-ttu-id="ab75a-377">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="ab75a-377">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-378">압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-378">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="ab75a-379">Gzip 압축 공급자</span><span class="sxs-lookup"><span data-stu-id="ab75a-379">Gzip Compression Provider</span></span>

<span data-ttu-id="ab75a-380"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>를 사용 하 여 [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952)으로 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-380">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="ab75a-381"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>에 명시적으로 추가 된 압축 공급자가 없으면:</span><span class="sxs-lookup"><span data-stu-id="ab75a-381">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="ab75a-382">Gzip 압축 공급자는 기본적으로 [Brotli 압축](#brotli-compression-provider)공급자와 함께 압축 공급자 배열에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-382">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="ab75a-383">클라이언트에서 Brotli 압축 데이터 형식이 지원 되는 경우 압축의 기본값은 Brotli 압축입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-383">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="ab75a-384">클라이언트에서 Brotli을 지원 하지 않는 경우 클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-384">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="ab75a-385">모든 압축 공급자가 명시적으로 추가 되 면 Gzip 압축 공급자를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-385">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="ab75a-386"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>압축 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-386">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="ab75a-387">Gzip 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-387">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="ab75a-388">가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-388">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="ab75a-389">압축 수준</span><span class="sxs-lookup"><span data-stu-id="ab75a-389">Compression Level</span></span> | <span data-ttu-id="ab75a-390">설명</span><span class="sxs-lookup"><span data-stu-id="ab75a-390">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="ab75a-391">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="ab75a-391">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-392">결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-392">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="ab75a-393">CompressionLevel 압축</span><span class="sxs-lookup"><span data-stu-id="ab75a-393">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-394">압축을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-394">No compression should be performed.</span></span> |
| [<span data-ttu-id="ab75a-395">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="ab75a-395">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-396">압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-396">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="ab75a-397">사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="ab75a-397">Custom providers</span></span>

<span data-ttu-id="ab75a-398"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>를 사용 하 여 사용자 지정 압축 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-398">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="ab75a-399"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>는이 `ICompressionProvider` 생성 하는 콘텐츠 인코딩을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-399">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="ab75a-400">미들웨어는이 정보를 사용 하 여 요청의 `Accept-Encoding` 헤더에 지정 된 목록에 따라 공급자를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-400">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="ab75a-401">클라이언트는 샘플 앱을 사용 하 여 `Accept-Encoding: mycustomcompression` 헤더를 사용 하 여 요청을 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-401">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="ab75a-402">미들웨어는 사용자 지정 압축 구현을 사용 하 고 `Content-Encoding: mycustomcompression` 헤더가 포함 된 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-402">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="ab75a-403">클라이언트는 사용자 지정 압축 구현이 작동 하기 위해 사용자 지정 인코딩의 압축을 해제할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-403">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="ab75a-404">`Accept-Encoding: mycustomcompression` 헤더를 사용 하 여 샘플 앱에 요청을 제출 하 고 응답 헤더를 관찰 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-404">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="ab75a-405">`Vary` 및 `Content-Encoding` 헤더가 응답에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-405">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="ab75a-406">응답 본문 (표시 되지 않음)은 샘플에 의해 압축 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-406">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="ab75a-407">샘플의 `CustomCompressionProvider` 클래스에는 압축 구현이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-407">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="ab75a-408">그러나이 샘플에서는 이러한 압축 알고리즘을 구현 하는 위치를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-408">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 헤더를 포함 하는 요청 결과와 mycustomcompression 값을 보여 주는 창](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="ab75a-411">MIME 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-411">MIME types</span></span>

<span data-ttu-id="ab75a-412">미들웨어는 압축을 위한 기본 MIME 형식 집합을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-412">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="ab75a-413">MIME 형식을 응답 압축 미들웨어 옵션으로 바꾸거나 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-413">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="ab75a-414">`text/*`와 같은 와일드 카드 MIME 형식은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-414">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="ab75a-415">샘플 앱은 `image/svg+xml`에 대 한 MIME 형식을 추가 하 고 압축 하 여 ASP.NET Core 배너 이미지 (*배너나*)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-415">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="ab75a-416">보안 프로토콜을 사용한 압축</span><span class="sxs-lookup"><span data-stu-id="ab75a-416">Compression with secure protocol</span></span>

<span data-ttu-id="ab75a-417">보안 연결을 통한 압축 된 응답은 기본적으로 사용 하지 않도록 설정 된 `EnableForHttps` 옵션을 사용 하 여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-417">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="ab75a-418">동적으로 생성 된 페이지에서 압축을 사용 하면 [범죄](https://wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://wikipedia.org/wiki/BREACH_(security_exploit)) 공격과 같은 보안 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-418">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="ab75a-419">Vary 헤더 추가</span><span class="sxs-lookup"><span data-stu-id="ab75a-419">Adding the Vary header</span></span>

<span data-ttu-id="ab75a-420">`Accept-Encoding` 헤더를 기반으로 응답을 압축 하는 경우 여러 압축 버전의 응답 및 압축 되지 않은 버전이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-420">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="ab75a-421">여러 버전이 존재 하 고 저장 해야 함을 클라이언트 및 프록시 캐시에 지시 하기 위해 `Vary` 헤더가 `Accept-Encoding` 값으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-421">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="ab75a-422">ASP.NET Core 2.0 이상에서 미들웨어는 응답이 압축 될 때 `Vary` 헤더를 자동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-422">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="ab75a-423">Nginx 역방향 프록시 뒤에 있는 미들웨어 문제</span><span class="sxs-lookup"><span data-stu-id="ab75a-423">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="ab75a-424">Nginx에서 요청을 프록시 하는 경우에는 `Accept-Encoding` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-424">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="ab75a-425">`Accept-Encoding` 헤더를 제거 하면 미들웨어가 응답을 압축 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-425">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="ab75a-426">자세한 내용은 [NGINX: 압축 및](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)압축 해제를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-426">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="ab75a-427">이 문제는 [Nginx (aspnet/BasicMiddleware #123)에 대 한 통과 압축](https://github.com/aspnet/BasicMiddleware/issues/123)을 통해 추적 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-427">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="ab75a-428">IIS 동적 압축 사용</span><span class="sxs-lookup"><span data-stu-id="ab75a-428">Working with IIS dynamic compression</span></span>

<span data-ttu-id="ab75a-429">앱에 대해 사용 하지 않도록 설정할 서버 수준에서 활성 IIS 동적 압축 모듈이 구성 된 경우 *web.config* 파일에 추가 하 여 모듈을 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-429">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="ab75a-430">보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-430">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ab75a-431">문제 해결</span><span class="sxs-lookup"><span data-stu-id="ab75a-431">Troubleshooting</span></span>

<span data-ttu-id="ab75a-432">`Accept-Encoding` 요청 헤더를 설정 하 고 응답 헤더, 크기 및 본문을 학습할 수 있는 [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-432">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="ab75a-433">기본적으로 응답 압축 미들웨어는 다음 조건을 충족 하는 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-433">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="ab75a-434">`Accept-Encoding` 헤더는 설정 된 사용자 지정 압축 공급자와 일치 하는 `br`, `gzip`, `*`또는 사용자 지정 인코딩의 값과 함께 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-434">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="ab75a-435">값을 `identity` 하거나 품질 값 (qvalue, `q`)을 0 (영)으로 설정 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-435">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="ab75a-436">MIME 형식 (`Content-Type`)을 설정 하 고 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>에 구성 된 MIME 형식과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-436">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="ab75a-437">요청은 `Content-Range` 헤더를 포함 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-437">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="ab75a-438">응답 압축 미들웨어 옵션에 보안 프로토콜 (https)이 구성 되지 않은 경우 요청은 안전 하지 않은 프로토콜 (http)을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-438">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="ab75a-439">*보안 콘텐츠 압축을 사용 하도록 설정할 때 [위에서 설명한](#compression-with-secure-protocol) 위험에 유의 하십시오.*</span><span class="sxs-lookup"><span data-stu-id="ab75a-439">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ab75a-440">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ab75a-440">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="ab75a-441">Mozilla 개발자 네트워크: 수락-인코딩</span><span class="sxs-lookup"><span data-stu-id="ab75a-441">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="ab75a-442">RFC 7231 섹션 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="ab75a-442">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="ab75a-443">RFC 7230 섹션 4.2.3: Gzip 코딩</span><span class="sxs-lookup"><span data-stu-id="ab75a-443">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="ab75a-444">GZIP 파일 형식 사양 버전 4.3</span><span class="sxs-lookup"><span data-stu-id="ab75a-444">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ab75a-445">네트워크 대역폭은 제한 된 리소스입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-445">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="ab75a-446">응답 크기를 줄이면 일반적으로 앱의 응답성이 크게 향상 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-446">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="ab75a-447">페이로드 크기를 줄이는 한 가지 방법은 앱의 응답을 압축 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-447">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="ab75a-448">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ab75a-448">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="ab75a-449">응답 압축 미들웨어를 사용 하는 경우</span><span class="sxs-lookup"><span data-stu-id="ab75a-449">When to use Response Compression Middleware</span></span>

<span data-ttu-id="ab75a-450">IIS, Apache 또는 Nginx에서 서버 기반 응답 압축 기술을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-450">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="ab75a-451">미들웨어의 성능은 서버 모듈의 성능과 일치 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-451">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="ab75a-452">[Http.sys 서버](xref:fundamentals/servers/httpsys) 서버와 [kestrel](xref:fundamentals/servers/kestrel) 서버는 현재 기본 제공 압축 지원을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-452">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="ab75a-453">응답 압축 미들웨어를 사용 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="ab75a-453">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="ab75a-454">다음 서버 기반 압축 기술을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-454">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="ab75a-455">IIS 동적 압축 모듈</span><span class="sxs-lookup"><span data-stu-id="ab75a-455">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="ab75a-456">Apache mod_deflate 모듈</span><span class="sxs-lookup"><span data-stu-id="ab75a-456">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="ab75a-457">Nginx 압축 및 압축 풀기</span><span class="sxs-lookup"><span data-stu-id="ab75a-457">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="ab75a-458">에서 직접 호스팅:</span><span class="sxs-lookup"><span data-stu-id="ab75a-458">Hosting directly on:</span></span>
  * <span data-ttu-id="ab75a-459">[Http.sys 서버](xref:fundamentals/servers/httpsys) (이전에는 weblistener 라고 함)</span><span class="sxs-lookup"><span data-stu-id="ab75a-459">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="ab75a-460">Kestrel 서버</span><span class="sxs-lookup"><span data-stu-id="ab75a-460">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="ab75a-461">응답 압축</span><span class="sxs-lookup"><span data-stu-id="ab75a-461">Response compression</span></span>

<span data-ttu-id="ab75a-462">일반적으로 기본적으로 압축 되지 않은 모든 응답은 응답 압축의 이점을 누릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-462">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="ab75a-463">기본적으로 압축 되지 않은 응답은 일반적으로 CSS, JavaScript, HTML, XML 및 JSON을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-463">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="ab75a-464">PNG 파일과 같이 기본적으로 압축 된 자산은 압축 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-464">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="ab75a-465">고유 하 게 압축 된 응답을 추가로 압축 하려는 경우 크기 및 전송 시간의 작은 추가 감소는 압축을 처리 하는 데 걸린 시간을 overshadowed 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-465">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="ab75a-466">파일의 내용과 압축 효율성에 따라 약 150-1000 바이트 보다 작은 파일은 압축 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-466">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="ab75a-467">작은 파일을 압축 하는 오버 헤드로 인해 압축 되지 않은 파일 보다 큰 압축 된 파일이 생성 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-467">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="ab75a-468">클라이언트가 압축 된 콘텐츠를 처리할 수 있는 경우 클라이언트는 요청과 함께 `Accept-Encoding` 헤더를 전송 하 여 해당 기능을 서버에 알려야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-468">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="ab75a-469">서버는 압축 된 콘텐츠를 보낼 때 압축 된 응답을 인코딩하는 방법에 대 한 정보를 `Content-Encoding` 헤더에 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-469">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="ab75a-470">미들웨어에서 지 원하는 콘텐츠 인코딩 명칭은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-470">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="ab75a-471">`Accept-Encoding` 헤더 값</span><span class="sxs-lookup"><span data-stu-id="ab75a-471">`Accept-Encoding` header values</span></span> | <span data-ttu-id="ab75a-472">미들웨어 지원</span><span class="sxs-lookup"><span data-stu-id="ab75a-472">Middleware Supported</span></span> | <span data-ttu-id="ab75a-473">설명</span><span class="sxs-lookup"><span data-stu-id="ab75a-473">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="ab75a-474">아니요</span><span class="sxs-lookup"><span data-stu-id="ab75a-474">No</span></span>                   | [<span data-ttu-id="ab75a-475">Brotli 압축 된 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-475">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="ab75a-476">아니요</span><span class="sxs-lookup"><span data-stu-id="ab75a-476">No</span></span>                   | [<span data-ttu-id="ab75a-477">DEFLATE 압축 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-477">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="ab75a-478">아니요</span><span class="sxs-lookup"><span data-stu-id="ab75a-478">No</span></span>                   | [<span data-ttu-id="ab75a-479">W3C 효율적인 XML 교환</span><span class="sxs-lookup"><span data-stu-id="ab75a-479">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="ab75a-480">예 (기본값)</span><span class="sxs-lookup"><span data-stu-id="ab75a-480">Yes (default)</span></span>        | [<span data-ttu-id="ab75a-481">Gzip 파일 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-481">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="ab75a-482">예</span><span class="sxs-lookup"><span data-stu-id="ab75a-482">Yes</span></span>                  | <span data-ttu-id="ab75a-483">"인코딩 안 함" 식별자: 응답은 인코딩되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-483">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="ab75a-484">아니요</span><span class="sxs-lookup"><span data-stu-id="ab75a-484">No</span></span>                   | [<span data-ttu-id="ab75a-485">Java 보관을 위한 네트워크 전송 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-485">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="ab75a-486">예</span><span class="sxs-lookup"><span data-stu-id="ab75a-486">Yes</span></span>                  | <span data-ttu-id="ab75a-487">명시적으로 요청 되지 않은 모든 사용 가능한 콘텐츠 인코딩입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-487">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="ab75a-488">자세한 내용은 [IANA 공식 콘텐츠 코딩 목록](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-488">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="ab75a-489">미들웨어를 사용 하면 사용자 지정 `Accept-Encoding` 헤더 값에 대 한 추가 압축 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-489">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="ab75a-490">자세한 내용은 아래의 [사용자 지정 공급자](#custom-providers) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-490">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="ab75a-491">미들웨어는 압축 체계의 우선 순위를 지정 하기 위해 클라이언트에서 보낼 때 품질 값 (qvalue, `q`) 가중치에 대응 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-491">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="ab75a-492">자세한 내용은 [RFC 7231: 수락-인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-492">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="ab75a-493">압축 알고리즘은 압축 속도와 압축의 효율성 사이의 균형을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-493">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="ab75a-494">이 컨텍스트의 *효과* 는 압축 후 출력의 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-494">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="ab75a-495">가장 작은 크기는 가장 *최적* 압축을 통해 달성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-495">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="ab75a-496">다음 표에서는 압축 된 콘텐츠 요청, 송신, 캐싱 및 수신에 관련 된 헤더에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-496">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="ab75a-497">헤더</span><span class="sxs-lookup"><span data-stu-id="ab75a-497">Header</span></span>             | <span data-ttu-id="ab75a-498">Role</span><span class="sxs-lookup"><span data-stu-id="ab75a-498">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="ab75a-499">클라이언트에서 서버로 전송 되어 클라이언트에 허용 되는 콘텐츠 인코딩 스키마를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-499">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="ab75a-500">페이로드에 있는 콘텐츠의 인코딩을 나타내기 위해 서버에서 클라이언트로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-500">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="ab75a-501">압축이 발생 하면 응답이 압축 될 때 본문 내용이 변경 되기 때문에 `Content-Length` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-501">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="ab75a-502">압축이 발생 하면 본문 내용이 변경 되 고 해시가 더 이상 유효 하지 않기 때문에 `Content-MD5` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-502">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="ab75a-503">콘텐츠의 MIME 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-503">Specifies the MIME type of the content.</span></span> <span data-ttu-id="ab75a-504">모든 응답은 해당 `Content-Type`을 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-504">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="ab75a-505">미들웨어는이 값을 확인 하 여 응답이 압축 되어야 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-505">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="ab75a-506">미들웨어는 인코딩할 수 있는 [기본 MIME 형식의](#mime-types) 집합을 지정 하지만 mime 형식을 바꾸거나 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-506">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="ab75a-507">클라이언트 및 프록시에 `Accept-Encoding` 값을 사용 하 여 서버에서 보낸 경우 `Vary` 헤더는 요청의 `Accept-Encoding` 헤더 값을 기준으로 응답을 캐시 (vary) 해야 함을 클라이언트 또는 프록시에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-507">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="ab75a-508">`Vary: Accept-Encoding` 헤더를 사용 하 여 콘텐츠를 반환한 결과는 압축 된 응답과 압축 되지 않은 응답이 모두 개별적으로 캐시 된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-508">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="ab75a-509">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)을 사용 하 여 응답 압축 미들웨어의 기능을 탐색 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-509">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="ab75a-510">샘플은 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-510">The sample illustrates:</span></span>

* <span data-ttu-id="ab75a-511">Gzip 및 사용자 지정 압축 공급자를 사용 하 여 앱 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-511">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="ab75a-512">Mime 형식을 압축을 위한 MIME 형식의 기본 목록에 추가 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-512">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="ab75a-513">패키지</span><span class="sxs-lookup"><span data-stu-id="ab75a-513">Package</span></span>

<span data-ttu-id="ab75a-514">프로젝트에 미들웨어를 포함 하려면 [ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 패키지를 포함 하는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에 대 한 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-514">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="ab75a-515">구성</span><span class="sxs-lookup"><span data-stu-id="ab75a-515">Configuration</span></span>

<span data-ttu-id="ab75a-516">다음 코드에서는 기본 MIME 형식 및 [Gzip 압축 공급자](#gzip-compression-provider)에 대해 응답 압축 미들웨어를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-516">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="ab75a-517">참고:</span><span class="sxs-lookup"><span data-stu-id="ab75a-517">Notes:</span></span>

* <span data-ttu-id="ab75a-518">`app.UseResponseCompression`는 응답을 압축 하는 미들웨어 보다 먼저 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-518">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="ab75a-519">자세한 내용은 <xref:fundamentals/middleware/index#middleware-order>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-519">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="ab75a-520">[Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/) 과 같은 도구를 사용 하 여 `Accept-Encoding` 요청 헤더를 설정 하 고 응답 헤더, 크기 및 본문을 연구 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-520">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="ab75a-521">`Accept-Encoding` 헤더를 제외 하 고 샘플 앱에 요청을 제출 하 고 응답이 압축 되지 않은 상태 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-521">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="ab75a-522">`Content-Encoding` 및 `Vary` 헤더가 응답에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-522">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![수락 인코딩 헤더가 없는 요청 결과를 보여 주는 Fiddler 창](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="ab75a-525">`Accept-Encoding: gzip` 헤더를 사용 하 여 샘플 앱에 요청을 제출 하 고 응답이 압축 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-525">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="ab75a-526">`Content-Encoding` 및 `Vary` 헤더가 응답에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-526">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![허용 인코딩 헤더와 값이 포함 된 요청의 결과를 보여 주는 Fiddler 창입니다.](response-compression/_static/request-compressed.png)

## <a name="providers"></a><span data-ttu-id="ab75a-530">공급자</span><span class="sxs-lookup"><span data-stu-id="ab75a-530">Providers</span></span>

### <a name="gzip-compression-provider"></a><span data-ttu-id="ab75a-531">Gzip 압축 공급자</span><span class="sxs-lookup"><span data-stu-id="ab75a-531">Gzip Compression Provider</span></span>

<span data-ttu-id="ab75a-532"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>를 사용 하 여 [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952)으로 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-532">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="ab75a-533"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>에 명시적으로 추가 된 압축 공급자가 없으면:</span><span class="sxs-lookup"><span data-stu-id="ab75a-533">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="ab75a-534">Gzip 압축 공급자는 기본적으로 압축 공급자 배열에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-534">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="ab75a-535">클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-535">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="ab75a-536">모든 압축 공급자가 명시적으로 추가 되 면 Gzip 압축 공급자를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-536">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="ab75a-537"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>압축 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-537">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="ab75a-538">Gzip 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-538">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="ab75a-539">가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-539">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="ab75a-540">압축 수준</span><span class="sxs-lookup"><span data-stu-id="ab75a-540">Compression Level</span></span> | <span data-ttu-id="ab75a-541">설명</span><span class="sxs-lookup"><span data-stu-id="ab75a-541">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="ab75a-542">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="ab75a-542">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-543">결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-543">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="ab75a-544">CompressionLevel 압축</span><span class="sxs-lookup"><span data-stu-id="ab75a-544">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-545">압축을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-545">No compression should be performed.</span></span> |
| [<span data-ttu-id="ab75a-546">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="ab75a-546">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="ab75a-547">압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-547">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="ab75a-548">사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="ab75a-548">Custom providers</span></span>

<span data-ttu-id="ab75a-549"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>를 사용 하 여 사용자 지정 압축 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-549">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="ab75a-550"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>는이 `ICompressionProvider` 생성 하는 콘텐츠 인코딩을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-550">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="ab75a-551">미들웨어는이 정보를 사용 하 여 요청의 `Accept-Encoding` 헤더에 지정 된 목록에 따라 공급자를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-551">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="ab75a-552">클라이언트는 샘플 앱을 사용 하 여 `Accept-Encoding: mycustomcompression` 헤더를 사용 하 여 요청을 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-552">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="ab75a-553">미들웨어는 사용자 지정 압축 구현을 사용 하 고 `Content-Encoding: mycustomcompression` 헤더가 포함 된 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-553">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="ab75a-554">클라이언트는 사용자 지정 압축 구현이 작동 하기 위해 사용자 지정 인코딩의 압축을 해제할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-554">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="ab75a-555">`Accept-Encoding: mycustomcompression` 헤더를 사용 하 여 샘플 앱에 요청을 제출 하 고 응답 헤더를 관찰 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-555">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="ab75a-556">`Vary` 및 `Content-Encoding` 헤더가 응답에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-556">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="ab75a-557">응답 본문 (표시 되지 않음)은 샘플에 의해 압축 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-557">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="ab75a-558">샘플의 `CustomCompressionProvider` 클래스에는 압축 구현이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-558">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="ab75a-559">그러나이 샘플에서는 이러한 압축 알고리즘을 구현 하는 위치를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-559">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 헤더를 포함 하는 요청 결과와 mycustomcompression 값을 보여 주는 창](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="ab75a-562">MIME 형식</span><span class="sxs-lookup"><span data-stu-id="ab75a-562">MIME types</span></span>

<span data-ttu-id="ab75a-563">미들웨어는 압축을 위한 기본 MIME 형식 집합을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-563">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="ab75a-564">MIME 형식을 응답 압축 미들웨어 옵션으로 바꾸거나 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-564">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="ab75a-565">`text/*`와 같은 와일드 카드 MIME 형식은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-565">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="ab75a-566">샘플 앱은 `image/svg+xml`에 대 한 MIME 형식을 추가 하 고 압축 하 여 ASP.NET Core 배너 이미지 (*배너나*)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-566">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="ab75a-567">보안 프로토콜을 사용한 압축</span><span class="sxs-lookup"><span data-stu-id="ab75a-567">Compression with secure protocol</span></span>

<span data-ttu-id="ab75a-568">보안 연결을 통한 압축 된 응답은 기본적으로 사용 하지 않도록 설정 된 `EnableForHttps` 옵션을 사용 하 여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-568">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="ab75a-569">동적으로 생성 된 페이지에서 압축을 사용 하면 [범죄](https://wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://wikipedia.org/wiki/BREACH_(security_exploit)) 공격과 같은 보안 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-569">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="ab75a-570">Vary 헤더 추가</span><span class="sxs-lookup"><span data-stu-id="ab75a-570">Adding the Vary header</span></span>

<span data-ttu-id="ab75a-571">`Accept-Encoding` 헤더를 기반으로 응답을 압축 하는 경우 여러 압축 버전의 응답 및 압축 되지 않은 버전이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-571">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="ab75a-572">여러 버전이 존재 하 고 저장 해야 함을 클라이언트 및 프록시 캐시에 지시 하기 위해 `Vary` 헤더가 `Accept-Encoding` 값으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-572">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="ab75a-573">ASP.NET Core 2.0 이상에서 미들웨어는 응답이 압축 될 때 `Vary` 헤더를 자동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-573">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="ab75a-574">Nginx 역방향 프록시 뒤에 있는 미들웨어 문제</span><span class="sxs-lookup"><span data-stu-id="ab75a-574">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="ab75a-575">Nginx에서 요청을 프록시 하는 경우에는 `Accept-Encoding` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-575">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="ab75a-576">`Accept-Encoding` 헤더를 제거 하면 미들웨어가 응답을 압축 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-576">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="ab75a-577">자세한 내용은 [NGINX: 압축 및](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)압축 해제를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ab75a-577">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="ab75a-578">이 문제는 [Nginx (aspnet/BasicMiddleware #123)에 대 한 통과 압축](https://github.com/aspnet/BasicMiddleware/issues/123)을 통해 추적 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-578">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="ab75a-579">IIS 동적 압축 사용</span><span class="sxs-lookup"><span data-stu-id="ab75a-579">Working with IIS dynamic compression</span></span>

<span data-ttu-id="ab75a-580">앱에 대해 사용 하지 않도록 설정할 서버 수준에서 활성 IIS 동적 압축 모듈이 구성 된 경우 *web.config* 파일에 추가 하 여 모듈을 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-580">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="ab75a-581">보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-581">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ab75a-582">문제 해결</span><span class="sxs-lookup"><span data-stu-id="ab75a-582">Troubleshooting</span></span>

<span data-ttu-id="ab75a-583">`Accept-Encoding` 요청 헤더를 설정 하 고 응답 헤더, 크기 및 본문을 학습할 수 있는 [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-583">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="ab75a-584">기본적으로 응답 압축 미들웨어는 다음 조건을 충족 하는 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-584">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="ab75a-585">설정 된 사용자 지정 압축 공급자와 일치 하는 `gzip`, `*`또는 사용자 지정 인코딩의 값이 `Accept-Encoding` 헤더가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-585">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="ab75a-586">값을 `identity` 하거나 품질 값 (qvalue, `q`)을 0 (영)으로 설정 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-586">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="ab75a-587">MIME 형식 (`Content-Type`)을 설정 하 고 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>에 구성 된 MIME 형식과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-587">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="ab75a-588">요청은 `Content-Range` 헤더를 포함 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-588">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="ab75a-589">응답 압축 미들웨어 옵션에 보안 프로토콜 (https)이 구성 되지 않은 경우 요청은 안전 하지 않은 프로토콜 (http)을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab75a-589">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="ab75a-590">*보안 콘텐츠 압축을 사용 하도록 설정할 때 [위에서 설명한](#compression-with-secure-protocol) 위험에 유의 하십시오.*</span><span class="sxs-lookup"><span data-stu-id="ab75a-590">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ab75a-591">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ab75a-591">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="ab75a-592">Mozilla 개발자 네트워크: 수락-인코딩</span><span class="sxs-lookup"><span data-stu-id="ab75a-592">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="ab75a-593">RFC 7231 섹션 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="ab75a-593">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="ab75a-594">RFC 7230 섹션 4.2.3: Gzip 코딩</span><span class="sxs-lookup"><span data-stu-id="ab75a-594">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="ab75a-595">GZIP 파일 형식 사양 버전 4.3</span><span class="sxs-lookup"><span data-stu-id="ab75a-595">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
