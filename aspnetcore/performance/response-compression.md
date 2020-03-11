---
title: ASP.NET Core 응답 압축
author: rick-anderson
description: ASP.NET Core 앱에서 응답 압축 미들웨어를 사용하는 방법 및 응답 압축에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: performance/response-compression
ms.openlocfilehash: aae0b8d74fc424cc81c046e9042279856865bf6a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654345"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="4ee35-103">ASP.NET Core 응답 압축</span><span class="sxs-lookup"><span data-stu-id="4ee35-103">Response compression in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4ee35-104">네트워크 대역폭은 제한 된 리소스입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-104">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="4ee35-105">응답 크기를 줄이면 일반적으로 앱의 응답성이 크게 향상 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-105">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="4ee35-106">페이로드 크기를 줄이는 한 가지 방법은 앱의 응답을 압축 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-106">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="4ee35-107">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4ee35-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="4ee35-108">응답 압축 미들웨어를 사용 하는 경우</span><span class="sxs-lookup"><span data-stu-id="4ee35-108">When to use Response Compression Middleware</span></span>

<span data-ttu-id="4ee35-109">IIS, Apache 또는 Nginx에서 서버 기반 응답 압축 기술을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-109">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="4ee35-110">미들웨어의 성능은 서버 모듈의 성능과 일치 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-110">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="4ee35-111">[Http.sys 서버](xref:fundamentals/servers/httpsys) 서버와 [kestrel](xref:fundamentals/servers/kestrel) 서버는 현재 기본 제공 압축 지원을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="4ee35-112">응답 압축 미들웨어를 사용 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="4ee35-112">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="4ee35-113">다음 서버 기반 압축 기술을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-113">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="4ee35-114">IIS 동적 압축 모듈</span><span class="sxs-lookup"><span data-stu-id="4ee35-114">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="4ee35-115">Apache mod_deflate 모듈</span><span class="sxs-lookup"><span data-stu-id="4ee35-115">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="4ee35-116">Nginx 압축 및 압축 풀기</span><span class="sxs-lookup"><span data-stu-id="4ee35-116">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="4ee35-117">에서 직접 호스팅:</span><span class="sxs-lookup"><span data-stu-id="4ee35-117">Hosting directly on:</span></span>
  * <span data-ttu-id="4ee35-118">[Http.sys 서버](xref:fundamentals/servers/httpsys) (이전에는 weblistener 라고 함)</span><span class="sxs-lookup"><span data-stu-id="4ee35-118">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="4ee35-119">Kestrel 서버</span><span class="sxs-lookup"><span data-stu-id="4ee35-119">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="4ee35-120">응답 압축</span><span class="sxs-lookup"><span data-stu-id="4ee35-120">Response compression</span></span>

<span data-ttu-id="4ee35-121">일반적으로 기본적으로 압축 되지 않은 모든 응답은 응답 압축의 이점을 누릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-121">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="4ee35-122">기본적으로 압축 되지 않은 응답은 일반적으로 CSS, JavaScript, HTML, XML 및 JSON을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-122">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="4ee35-123">PNG 파일과 같이 기본적으로 압축 된 자산은 압축 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-123">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="4ee35-124">고유 하 게 압축 된 응답을 추가로 압축 하려는 경우 크기 및 전송 시간의 작은 추가 감소는 압축을 처리 하는 데 걸린 시간을 overshadowed 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-124">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="4ee35-125">파일의 내용과 압축 효율성에 따라 약 150-1000 바이트 보다 작은 파일은 압축 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-125">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="4ee35-126">작은 파일을 압축 하는 오버 헤드로 인해 압축 되지 않은 파일 보다 큰 압축 된 파일이 생성 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-126">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="4ee35-127">클라이언트가 압축 된 콘텐츠를 처리할 수 있는 경우 클라이언트는 요청과 함께 `Accept-Encoding` 헤더를 전송 하 여 해당 기능을 서버에 알려야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-127">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="4ee35-128">서버는 압축 된 콘텐츠를 보낼 때 압축 된 응답을 인코딩하는 방법에 대 한 정보를 `Content-Encoding` 헤더에 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-128">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="4ee35-129">미들웨어에서 지 원하는 콘텐츠 인코딩 명칭은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-129">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="4ee35-130">`Accept-Encoding` 헤더 값</span><span class="sxs-lookup"><span data-stu-id="4ee35-130">`Accept-Encoding` header values</span></span> | <span data-ttu-id="4ee35-131">미들웨어 지원</span><span class="sxs-lookup"><span data-stu-id="4ee35-131">Middleware Supported</span></span> | <span data-ttu-id="4ee35-132">Description</span><span class="sxs-lookup"><span data-stu-id="4ee35-132">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="4ee35-133">예 (기본값)</span><span class="sxs-lookup"><span data-stu-id="4ee35-133">Yes (default)</span></span>        | [<span data-ttu-id="4ee35-134">Brotli 압축 된 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-134">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="4ee35-135">예</span><span class="sxs-lookup"><span data-stu-id="4ee35-135">No</span></span>                   | [<span data-ttu-id="4ee35-136">DEFLATE 압축 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-136">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="4ee35-137">예</span><span class="sxs-lookup"><span data-stu-id="4ee35-137">No</span></span>                   | [<span data-ttu-id="4ee35-138">W3C 효율적인 XML 교환</span><span class="sxs-lookup"><span data-stu-id="4ee35-138">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="4ee35-139">yes</span><span class="sxs-lookup"><span data-stu-id="4ee35-139">Yes</span></span>                  | [<span data-ttu-id="4ee35-140">Gzip 파일 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-140">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="4ee35-141">yes</span><span class="sxs-lookup"><span data-stu-id="4ee35-141">Yes</span></span>                  | <span data-ttu-id="4ee35-142">"인코딩 안 함" 식별자: 응답은 인코딩되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-142">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="4ee35-143">예</span><span class="sxs-lookup"><span data-stu-id="4ee35-143">No</span></span>                   | [<span data-ttu-id="4ee35-144">Java 보관을 위한 네트워크 전송 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-144">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="4ee35-145">yes</span><span class="sxs-lookup"><span data-stu-id="4ee35-145">Yes</span></span>                  | <span data-ttu-id="4ee35-146">명시적으로 요청 되지 않은 모든 사용 가능한 콘텐츠 인코딩입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-146">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="4ee35-147">자세한 내용은 [IANA 공식 콘텐츠 코딩 목록](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-147">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="4ee35-148">미들웨어를 사용 하면 사용자 지정 `Accept-Encoding` 헤더 값에 대 한 추가 압축 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-148">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="4ee35-149">자세한 내용은 아래의 [사용자 지정 공급자](#custom-providers) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-149">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="4ee35-150">미들웨어는 압축 체계의 우선 순위를 지정 하기 위해 클라이언트에서 보낼 때 품질 값 (qvalue, `q`) 가중치에 대응 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-150">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="4ee35-151">자세한 내용은 [RFC 7231: 수락-인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-151">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="4ee35-152">압축 알고리즘은 압축 속도와 압축의 효율성 사이의 균형을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-152">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="4ee35-153">이 컨텍스트의 *효과* 는 압축 후 출력의 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-153">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="4ee35-154">가장 작은 크기는 가장 *최적* 압축을 통해 달성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-154">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="4ee35-155">다음 표에서는 압축 된 콘텐츠 요청, 송신, 캐싱 및 수신에 관련 된 헤더에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-155">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="4ee35-156">헤더</span><span class="sxs-lookup"><span data-stu-id="4ee35-156">Header</span></span>             | <span data-ttu-id="4ee35-157">역할</span><span class="sxs-lookup"><span data-stu-id="4ee35-157">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="4ee35-158">클라이언트에서 서버로 전송 되어 클라이언트에 허용 되는 콘텐츠 인코딩 스키마를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-158">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="4ee35-159">페이로드에 있는 콘텐츠의 인코딩을 나타내기 위해 서버에서 클라이언트로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-159">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="4ee35-160">압축이 발생 하면 응답이 압축 될 때 본문 내용이 변경 되기 때문에 `Content-Length` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-160">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="4ee35-161">압축이 발생 하면 본문 내용이 변경 되 고 해시가 더 이상 유효 하지 않기 때문에 `Content-MD5` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-161">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="4ee35-162">콘텐츠의 MIME 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-162">Specifies the MIME type of the content.</span></span> <span data-ttu-id="4ee35-163">모든 응답은 해당 `Content-Type`을 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-163">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="4ee35-164">미들웨어는이 값을 확인 하 여 응답이 압축 되어야 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-164">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="4ee35-165">미들웨어는 인코딩할 수 있는 [기본 MIME 형식의](#mime-types) 집합을 지정 하지만 mime 형식을 바꾸거나 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-165">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="4ee35-166">클라이언트 및 프록시에 `Accept-Encoding` 값을 사용 하 여 서버에서 보낸 경우 `Vary` 헤더는 요청의 `Accept-Encoding` 헤더 값을 기준으로 응답을 캐시 (vary) 해야 함을 클라이언트 또는 프록시에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-166">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="4ee35-167">`Vary: Accept-Encoding` 헤더를 사용 하 여 콘텐츠를 반환한 결과는 압축 된 응답과 압축 되지 않은 응답이 모두 개별적으로 캐시 된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-167">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="4ee35-168">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)을 사용 하 여 응답 압축 미들웨어의 기능을 탐색 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-168">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="4ee35-169">샘플은 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-169">The sample illustrates:</span></span>

* <span data-ttu-id="4ee35-170">Gzip 및 사용자 지정 압축 공급자를 사용 하 여 앱 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-170">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="4ee35-171">Mime 형식을 압축을 위한 MIME 형식의 기본 목록에 추가 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-171">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="4ee35-172">패키지</span><span class="sxs-lookup"><span data-stu-id="4ee35-172">Package</span></span>

<span data-ttu-id="4ee35-173">응답 압축 미들웨어는 ASP.NET Core 앱에 암시적으로 포함 되는 [ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 패키지에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-173">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="configuration"></a><span data-ttu-id="4ee35-174">구성</span><span class="sxs-lookup"><span data-stu-id="4ee35-174">Configuration</span></span>

<span data-ttu-id="4ee35-175">다음 코드에서는 기본 MIME 형식 및 압축 공급자 ([Brotli](#brotli-compression-provider) 및 [Gzip](#gzip-compression-provider))에 대 한 응답 압축 미들웨어를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-175">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="4ee35-176">참고:</span><span class="sxs-lookup"><span data-stu-id="4ee35-176">Notes:</span></span>

* <span data-ttu-id="4ee35-177">`app.UseResponseCompression`는 응답을 압축 하는 미들웨어 보다 먼저 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-177">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="4ee35-178">자세한 내용은 <xref:fundamentals/middleware/index#middleware-order>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-178">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="4ee35-179">[Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/) 과 같은 도구를 사용 하 여 `Accept-Encoding` 요청 헤더를 설정 하 고 응답 헤더, 크기 및 본문을 연구 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-179">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="4ee35-180">`Accept-Encoding` 헤더를 제외 하 고 샘플 앱에 요청을 제출 하 고 응답이 압축 되지 않은 상태 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-180">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="4ee35-181">`Content-Encoding` 및 `Vary` 헤더가 응답에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-181">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![수락 인코딩 헤더가 없는 요청 결과를 보여 주는 Fiddler 창](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="4ee35-184">`Accept-Encoding: br` 헤더 (Brotli 압축)를 사용 하 여 샘플 앱에 요청을 제출 하 고 응답이 압축 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-184">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="4ee35-185">`Content-Encoding` 및 `Vary` 헤더가 응답에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-185">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![허용 인코딩 헤더와 값이 br 인 요청 결과를 보여 주는 Fiddler 창입니다.](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="4ee35-189">공급자</span><span class="sxs-lookup"><span data-stu-id="4ee35-189">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="4ee35-190">Brotli 압축 공급자</span><span class="sxs-lookup"><span data-stu-id="4ee35-190">Brotli Compression Provider</span></span>

<span data-ttu-id="4ee35-191"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>를 사용 하 여 [Brotli 압축 된 데이터 형식의](https://tools.ietf.org/html/rfc7932)응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-191">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="4ee35-192"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>에 명시적으로 추가 된 압축 공급자가 없으면:</span><span class="sxs-lookup"><span data-stu-id="4ee35-192">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="4ee35-193">Brotli 압축 공급자는 기본적으로 [Gzip 압축 공급자](#gzip-compression-provider)와 함께 압축 공급자 배열에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-193">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="4ee35-194">클라이언트에서 Brotli 압축 데이터 형식이 지원 되는 경우 압축의 기본값은 Brotli 압축입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-194">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="4ee35-195">클라이언트에서 Brotli을 지원 하지 않는 경우 클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-195">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="4ee35-196">압축 공급자가 명시적으로 추가 되 면 Brotoli 압축 공급자를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-196">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="4ee35-197"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>압축 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-197">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="4ee35-198">Brotli 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며,이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-198">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="4ee35-199">가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-199">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="4ee35-200">압축 수준</span><span class="sxs-lookup"><span data-stu-id="4ee35-200">Compression Level</span></span> | <span data-ttu-id="4ee35-201">Description</span><span class="sxs-lookup"><span data-stu-id="4ee35-201">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="4ee35-202">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="4ee35-202">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-203">결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-203">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="4ee35-204">CompressionLevel 압축</span><span class="sxs-lookup"><span data-stu-id="4ee35-204">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-205">압축을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-205">No compression should be performed.</span></span> |
| [<span data-ttu-id="4ee35-206">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="4ee35-206">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-207">압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-207">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="4ee35-208">Gzip 압축 공급자</span><span class="sxs-lookup"><span data-stu-id="4ee35-208">Gzip Compression Provider</span></span>

<span data-ttu-id="4ee35-209"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>를 사용 하 여 [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952)으로 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-209">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="4ee35-210"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>에 명시적으로 추가 된 압축 공급자가 없으면:</span><span class="sxs-lookup"><span data-stu-id="4ee35-210">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="4ee35-211">Gzip 압축 공급자는 기본적으로 [Brotli 압축](#brotli-compression-provider)공급자와 함께 압축 공급자 배열에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-211">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="4ee35-212">클라이언트에서 Brotli 압축 데이터 형식이 지원 되는 경우 압축의 기본값은 Brotli 압축입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-212">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="4ee35-213">클라이언트에서 Brotli을 지원 하지 않는 경우 클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-213">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="4ee35-214">모든 압축 공급자가 명시적으로 추가 되 면 Gzip 압축 공급자를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-214">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4ee35-215"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>압축 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-215">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="4ee35-216">Gzip 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-216">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="4ee35-217">가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-217">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="4ee35-218">압축 수준</span><span class="sxs-lookup"><span data-stu-id="4ee35-218">Compression Level</span></span> | <span data-ttu-id="4ee35-219">Description</span><span class="sxs-lookup"><span data-stu-id="4ee35-219">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="4ee35-220">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="4ee35-220">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-221">결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-221">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="4ee35-222">CompressionLevel 압축</span><span class="sxs-lookup"><span data-stu-id="4ee35-222">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-223">압축을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-223">No compression should be performed.</span></span> |
| [<span data-ttu-id="4ee35-224">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="4ee35-224">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-225">압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-225">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="4ee35-226">사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="4ee35-226">Custom providers</span></span>

<span data-ttu-id="4ee35-227"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>를 사용 하 여 사용자 지정 압축 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-227">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="4ee35-228"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>는이 `ICompressionProvider` 생성 하는 콘텐츠 인코딩을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-228">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="4ee35-229">미들웨어는이 정보를 사용 하 여 요청의 `Accept-Encoding` 헤더에 지정 된 목록에 따라 공급자를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-229">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="4ee35-230">클라이언트는 샘플 앱을 사용 하 여 `Accept-Encoding: mycustomcompression` 헤더를 사용 하 여 요청을 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-230">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4ee35-231">미들웨어는 사용자 지정 압축 구현을 사용 하 고 `Content-Encoding: mycustomcompression` 헤더가 포함 된 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-231">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4ee35-232">클라이언트는 사용자 지정 압축 구현이 작동 하기 위해 사용자 지정 인코딩의 압축을 해제할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-232">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


<span data-ttu-id="4ee35-233">`Accept-Encoding: mycustomcompression` 헤더를 사용 하 여 샘플 앱에 요청을 제출 하 고 응답 헤더를 관찰 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-233">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="4ee35-234">`Vary` 및 `Content-Encoding` 헤더가 응답에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-234">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="4ee35-235">응답 본문 (표시 되지 않음)은 샘플에 의해 압축 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-235">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="4ee35-236">샘플의 `CustomCompressionProvider` 클래스에는 압축 구현이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-236">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="4ee35-237">그러나이 샘플에서는 이러한 압축 알고리즘을 구현 하는 위치를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-237">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 헤더를 포함 하는 요청 결과와 mycustomcompression 값을 보여 주는 창](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="4ee35-240">MIME 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-240">MIME types</span></span>

<span data-ttu-id="4ee35-241">미들웨어는 압축을 위한 기본 MIME 형식 집합을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-241">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="4ee35-242">MIME 형식을 응답 압축 미들웨어 옵션으로 바꾸거나 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-242">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="4ee35-243">`text/*`와 같은 와일드 카드 MIME 형식은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-243">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="4ee35-244">샘플 앱은 `image/svg+xml`에 대 한 MIME 형식을 추가 하 고 압축 하 여 ASP.NET Core 배너 이미지 (*배너나*)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-244">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="4ee35-245">보안 프로토콜을 사용한 압축</span><span class="sxs-lookup"><span data-stu-id="4ee35-245">Compression with secure protocol</span></span>

<span data-ttu-id="4ee35-246">보안 연결을 통한 압축 된 응답은 기본적으로 사용 하지 않도록 설정 된 `EnableForHttps` 옵션을 사용 하 여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-246">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="4ee35-247">동적으로 생성 된 페이지에서 압축을 사용 하면 [범죄](https://wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://wikipedia.org/wiki/BREACH_(security_exploit)) 공격과 같은 보안 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-247">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="4ee35-248">Vary 헤더 추가</span><span class="sxs-lookup"><span data-stu-id="4ee35-248">Adding the Vary header</span></span>

<span data-ttu-id="4ee35-249">`Accept-Encoding` 헤더를 기반으로 응답을 압축 하는 경우 여러 압축 버전의 응답 및 압축 되지 않은 버전이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-249">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="4ee35-250">여러 버전이 존재 하 고 저장 해야 함을 클라이언트 및 프록시 캐시에 지시 하기 위해 `Vary` 헤더가 `Accept-Encoding` 값으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-250">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="4ee35-251">ASP.NET Core 2.0 이상에서 미들웨어는 응답이 압축 될 때 `Vary` 헤더를 자동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-251">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="4ee35-252">Nginx 역방향 프록시 뒤에 있는 미들웨어 문제</span><span class="sxs-lookup"><span data-stu-id="4ee35-252">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="4ee35-253">Nginx에서 요청을 프록시 하는 경우에는 `Accept-Encoding` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-253">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="4ee35-254">`Accept-Encoding` 헤더를 제거 하면 미들웨어가 응답을 압축 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-254">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="4ee35-255">자세한 내용은 [NGINX: 압축 및](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)압축 해제를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-255">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="4ee35-256">이 문제는 [Nginx (aspnet/BasicMiddleware #123)에 대 한 통과 압축](https://github.com/aspnet/BasicMiddleware/issues/123)을 통해 추적 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-256">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="4ee35-257">IIS 동적 압축 사용</span><span class="sxs-lookup"><span data-stu-id="4ee35-257">Working with IIS dynamic compression</span></span>

<span data-ttu-id="4ee35-258">앱에 대해 사용 하지 않도록 설정할 서버 수준에서 활성 IIS 동적 압축 모듈이 구성 된 경우 *web.config* 파일에 추가 하 여 모듈을 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-258">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="4ee35-259">보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-259">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="4ee35-260">문제 해결</span><span class="sxs-lookup"><span data-stu-id="4ee35-260">Troubleshooting</span></span>

<span data-ttu-id="4ee35-261">`Accept-Encoding` 요청 헤더를 설정 하 고 응답 헤더, 크기 및 본문을 학습할 수 있는 [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-261">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="4ee35-262">기본적으로 응답 압축 미들웨어는 다음 조건을 충족 하는 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-262">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="4ee35-263">`Accept-Encoding` 헤더는 설정 된 사용자 지정 압축 공급자와 일치 하는 `br`, `gzip`, `*`또는 사용자 지정 인코딩의 값과 함께 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-263">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="4ee35-264">값을 `identity` 하거나 품질 값 (qvalue, `q`)을 0 (영)으로 설정 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-264">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="4ee35-265">MIME 형식 (`Content-Type`)을 설정 하 고 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>에 구성 된 MIME 형식과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-265">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="4ee35-266">요청은 `Content-Range` 헤더를 포함 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-266">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="4ee35-267">응답 압축 미들웨어 옵션에 보안 프로토콜 (https)이 구성 되지 않은 경우 요청은 안전 하지 않은 프로토콜 (http)을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-267">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="4ee35-268">*보안 콘텐츠 압축을 사용 하도록 설정할 때 [위에서 설명한](#compression-with-secure-protocol) 위험에 유의 하십시오.*</span><span class="sxs-lookup"><span data-stu-id="4ee35-268">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ee35-269">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="4ee35-269">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="4ee35-270">Mozilla 개발자 네트워크: 수락-인코딩</span><span class="sxs-lookup"><span data-stu-id="4ee35-270">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="4ee35-271">RFC 7231 섹션 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="4ee35-271">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="4ee35-272">RFC 7230 섹션 4.2.3: Gzip 코딩</span><span class="sxs-lookup"><span data-stu-id="4ee35-272">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="4ee35-273">GZIP 파일 형식 사양 버전 4.3</span><span class="sxs-lookup"><span data-stu-id="4ee35-273">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="4ee35-274">네트워크 대역폭은 제한 된 리소스입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-274">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="4ee35-275">응답 크기를 줄이면 일반적으로 앱의 응답성이 크게 향상 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-275">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="4ee35-276">페이로드 크기를 줄이는 한 가지 방법은 앱의 응답을 압축 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-276">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="4ee35-277">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4ee35-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="4ee35-278">응답 압축 미들웨어를 사용 하는 경우</span><span class="sxs-lookup"><span data-stu-id="4ee35-278">When to use Response Compression Middleware</span></span>

<span data-ttu-id="4ee35-279">IIS, Apache 또는 Nginx에서 서버 기반 응답 압축 기술을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-279">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="4ee35-280">미들웨어의 성능은 서버 모듈의 성능과 일치 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-280">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="4ee35-281">[Http.sys 서버](xref:fundamentals/servers/httpsys) 서버와 [kestrel](xref:fundamentals/servers/kestrel) 서버는 현재 기본 제공 압축 지원을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-281">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="4ee35-282">응답 압축 미들웨어를 사용 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="4ee35-282">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="4ee35-283">다음 서버 기반 압축 기술을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-283">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="4ee35-284">IIS 동적 압축 모듈</span><span class="sxs-lookup"><span data-stu-id="4ee35-284">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="4ee35-285">Apache mod_deflate 모듈</span><span class="sxs-lookup"><span data-stu-id="4ee35-285">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="4ee35-286">Nginx 압축 및 압축 풀기</span><span class="sxs-lookup"><span data-stu-id="4ee35-286">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="4ee35-287">에서 직접 호스팅:</span><span class="sxs-lookup"><span data-stu-id="4ee35-287">Hosting directly on:</span></span>
  * <span data-ttu-id="4ee35-288">[Http.sys 서버](xref:fundamentals/servers/httpsys) (이전에는 weblistener 라고 함)</span><span class="sxs-lookup"><span data-stu-id="4ee35-288">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="4ee35-289">Kestrel 서버</span><span class="sxs-lookup"><span data-stu-id="4ee35-289">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="4ee35-290">응답 압축</span><span class="sxs-lookup"><span data-stu-id="4ee35-290">Response compression</span></span>

<span data-ttu-id="4ee35-291">일반적으로 기본적으로 압축 되지 않은 모든 응답은 응답 압축의 이점을 누릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-291">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="4ee35-292">기본적으로 압축 되지 않은 응답은 일반적으로 CSS, JavaScript, HTML, XML 및 JSON을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-292">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="4ee35-293">PNG 파일과 같이 기본적으로 압축 된 자산은 압축 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-293">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="4ee35-294">고유 하 게 압축 된 응답을 추가로 압축 하려는 경우 크기 및 전송 시간의 작은 추가 감소는 압축을 처리 하는 데 걸린 시간을 overshadowed 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-294">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="4ee35-295">파일의 내용과 압축 효율성에 따라 약 150-1000 바이트 보다 작은 파일은 압축 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-295">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="4ee35-296">작은 파일을 압축 하는 오버 헤드로 인해 압축 되지 않은 파일 보다 큰 압축 된 파일이 생성 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-296">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="4ee35-297">클라이언트가 압축 된 콘텐츠를 처리할 수 있는 경우 클라이언트는 요청과 함께 `Accept-Encoding` 헤더를 전송 하 여 해당 기능을 서버에 알려야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-297">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="4ee35-298">서버는 압축 된 콘텐츠를 보낼 때 압축 된 응답을 인코딩하는 방법에 대 한 정보를 `Content-Encoding` 헤더에 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-298">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="4ee35-299">미들웨어에서 지 원하는 콘텐츠 인코딩 명칭은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-299">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="4ee35-300">`Accept-Encoding` 헤더 값</span><span class="sxs-lookup"><span data-stu-id="4ee35-300">`Accept-Encoding` header values</span></span> | <span data-ttu-id="4ee35-301">미들웨어 지원</span><span class="sxs-lookup"><span data-stu-id="4ee35-301">Middleware Supported</span></span> | <span data-ttu-id="4ee35-302">Description</span><span class="sxs-lookup"><span data-stu-id="4ee35-302">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="4ee35-303">예 (기본값)</span><span class="sxs-lookup"><span data-stu-id="4ee35-303">Yes (default)</span></span>        | [<span data-ttu-id="4ee35-304">Brotli 압축 된 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-304">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="4ee35-305">예</span><span class="sxs-lookup"><span data-stu-id="4ee35-305">No</span></span>                   | [<span data-ttu-id="4ee35-306">DEFLATE 압축 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-306">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="4ee35-307">예</span><span class="sxs-lookup"><span data-stu-id="4ee35-307">No</span></span>                   | [<span data-ttu-id="4ee35-308">W3C 효율적인 XML 교환</span><span class="sxs-lookup"><span data-stu-id="4ee35-308">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="4ee35-309">yes</span><span class="sxs-lookup"><span data-stu-id="4ee35-309">Yes</span></span>                  | [<span data-ttu-id="4ee35-310">Gzip 파일 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-310">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="4ee35-311">yes</span><span class="sxs-lookup"><span data-stu-id="4ee35-311">Yes</span></span>                  | <span data-ttu-id="4ee35-312">"인코딩 안 함" 식별자: 응답은 인코딩되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-312">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="4ee35-313">예</span><span class="sxs-lookup"><span data-stu-id="4ee35-313">No</span></span>                   | [<span data-ttu-id="4ee35-314">Java 보관을 위한 네트워크 전송 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-314">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="4ee35-315">yes</span><span class="sxs-lookup"><span data-stu-id="4ee35-315">Yes</span></span>                  | <span data-ttu-id="4ee35-316">명시적으로 요청 되지 않은 모든 사용 가능한 콘텐츠 인코딩입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-316">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="4ee35-317">자세한 내용은 [IANA 공식 콘텐츠 코딩 목록](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-317">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="4ee35-318">미들웨어를 사용 하면 사용자 지정 `Accept-Encoding` 헤더 값에 대 한 추가 압축 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-318">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="4ee35-319">자세한 내용은 아래의 [사용자 지정 공급자](#custom-providers) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-319">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="4ee35-320">미들웨어는 압축 체계의 우선 순위를 지정 하기 위해 클라이언트에서 보낼 때 품질 값 (qvalue, `q`) 가중치에 대응 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-320">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="4ee35-321">자세한 내용은 [RFC 7231: 수락-인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-321">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="4ee35-322">압축 알고리즘은 압축 속도와 압축의 효율성 사이의 균형을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-322">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="4ee35-323">이 컨텍스트의 *효과* 는 압축 후 출력의 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-323">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="4ee35-324">가장 작은 크기는 가장 *최적* 압축을 통해 달성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-324">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="4ee35-325">다음 표에서는 압축 된 콘텐츠 요청, 송신, 캐싱 및 수신에 관련 된 헤더에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-325">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="4ee35-326">헤더</span><span class="sxs-lookup"><span data-stu-id="4ee35-326">Header</span></span>             | <span data-ttu-id="4ee35-327">역할</span><span class="sxs-lookup"><span data-stu-id="4ee35-327">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="4ee35-328">클라이언트에서 서버로 전송 되어 클라이언트에 허용 되는 콘텐츠 인코딩 스키마를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-328">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="4ee35-329">페이로드에 있는 콘텐츠의 인코딩을 나타내기 위해 서버에서 클라이언트로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-329">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="4ee35-330">압축이 발생 하면 응답이 압축 될 때 본문 내용이 변경 되기 때문에 `Content-Length` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-330">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="4ee35-331">압축이 발생 하면 본문 내용이 변경 되 고 해시가 더 이상 유효 하지 않기 때문에 `Content-MD5` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-331">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="4ee35-332">콘텐츠의 MIME 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-332">Specifies the MIME type of the content.</span></span> <span data-ttu-id="4ee35-333">모든 응답은 해당 `Content-Type`을 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-333">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="4ee35-334">미들웨어는이 값을 확인 하 여 응답이 압축 되어야 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-334">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="4ee35-335">미들웨어는 인코딩할 수 있는 [기본 MIME 형식의](#mime-types) 집합을 지정 하지만 mime 형식을 바꾸거나 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-335">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="4ee35-336">클라이언트 및 프록시에 `Accept-Encoding` 값을 사용 하 여 서버에서 보낸 경우 `Vary` 헤더는 요청의 `Accept-Encoding` 헤더 값을 기준으로 응답을 캐시 (vary) 해야 함을 클라이언트 또는 프록시에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-336">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="4ee35-337">`Vary: Accept-Encoding` 헤더를 사용 하 여 콘텐츠를 반환한 결과는 압축 된 응답과 압축 되지 않은 응답이 모두 개별적으로 캐시 된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-337">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="4ee35-338">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)을 사용 하 여 응답 압축 미들웨어의 기능을 탐색 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-338">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="4ee35-339">샘플은 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-339">The sample illustrates:</span></span>

* <span data-ttu-id="4ee35-340">Gzip 및 사용자 지정 압축 공급자를 사용 하 여 앱 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-340">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="4ee35-341">Mime 형식을 압축을 위한 MIME 형식의 기본 목록에 추가 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-341">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="4ee35-342">패키지</span><span class="sxs-lookup"><span data-stu-id="4ee35-342">Package</span></span>

<span data-ttu-id="4ee35-343">프로젝트에 미들웨어를 포함 하려면 [ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 패키지를 포함 하는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에 대 한 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-343">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="4ee35-344">구성</span><span class="sxs-lookup"><span data-stu-id="4ee35-344">Configuration</span></span>

<span data-ttu-id="4ee35-345">다음 코드에서는 기본 MIME 형식 및 압축 공급자 ([Brotli](#brotli-compression-provider) 및 [Gzip](#gzip-compression-provider))에 대 한 응답 압축 미들웨어를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-345">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="4ee35-346">참고:</span><span class="sxs-lookup"><span data-stu-id="4ee35-346">Notes:</span></span>

* <span data-ttu-id="4ee35-347">`app.UseResponseCompression`는 응답을 압축 하는 미들웨어 보다 먼저 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-347">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="4ee35-348">자세한 내용은 <xref:fundamentals/middleware/index#middleware-order>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-348">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="4ee35-349">[Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/) 과 같은 도구를 사용 하 여 `Accept-Encoding` 요청 헤더를 설정 하 고 응답 헤더, 크기 및 본문을 연구 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-349">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="4ee35-350">`Accept-Encoding` 헤더를 제외 하 고 샘플 앱에 요청을 제출 하 고 응답이 압축 되지 않은 상태 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-350">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="4ee35-351">`Content-Encoding` 및 `Vary` 헤더가 응답에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-351">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![수락 인코딩 헤더가 없는 요청 결과를 보여 주는 Fiddler 창](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="4ee35-354">`Accept-Encoding: br` 헤더 (Brotli 압축)를 사용 하 여 샘플 앱에 요청을 제출 하 고 응답이 압축 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-354">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="4ee35-355">`Content-Encoding` 및 `Vary` 헤더가 응답에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-355">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![허용 인코딩 헤더와 값이 br 인 요청 결과를 보여 주는 Fiddler 창입니다.](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="4ee35-359">공급자</span><span class="sxs-lookup"><span data-stu-id="4ee35-359">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="4ee35-360">Brotli 압축 공급자</span><span class="sxs-lookup"><span data-stu-id="4ee35-360">Brotli Compression Provider</span></span>

<span data-ttu-id="4ee35-361"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>를 사용 하 여 [Brotli 압축 된 데이터 형식의](https://tools.ietf.org/html/rfc7932)응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-361">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="4ee35-362"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>에 명시적으로 추가 된 압축 공급자가 없으면:</span><span class="sxs-lookup"><span data-stu-id="4ee35-362">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="4ee35-363">Brotli 압축 공급자는 기본적으로 [Gzip 압축 공급자](#gzip-compression-provider)와 함께 압축 공급자 배열에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-363">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="4ee35-364">클라이언트에서 Brotli 압축 데이터 형식이 지원 되는 경우 압축의 기본값은 Brotli 압축입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-364">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="4ee35-365">클라이언트에서 Brotli을 지원 하지 않는 경우 클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-365">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="4ee35-366">압축 공급자가 명시적으로 추가 되 면 Brotoli 압축 공급자를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-366">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="4ee35-367"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>압축 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-367">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="4ee35-368">Brotli 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며,이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-368">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="4ee35-369">가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-369">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="4ee35-370">압축 수준</span><span class="sxs-lookup"><span data-stu-id="4ee35-370">Compression Level</span></span> | <span data-ttu-id="4ee35-371">Description</span><span class="sxs-lookup"><span data-stu-id="4ee35-371">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="4ee35-372">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="4ee35-372">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-373">결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-373">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="4ee35-374">CompressionLevel 압축</span><span class="sxs-lookup"><span data-stu-id="4ee35-374">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-375">압축을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-375">No compression should be performed.</span></span> |
| [<span data-ttu-id="4ee35-376">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="4ee35-376">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-377">압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-377">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="4ee35-378">Gzip 압축 공급자</span><span class="sxs-lookup"><span data-stu-id="4ee35-378">Gzip Compression Provider</span></span>

<span data-ttu-id="4ee35-379"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>를 사용 하 여 [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952)으로 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-379">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="4ee35-380"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>에 명시적으로 추가 된 압축 공급자가 없으면:</span><span class="sxs-lookup"><span data-stu-id="4ee35-380">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="4ee35-381">Gzip 압축 공급자는 기본적으로 [Brotli 압축](#brotli-compression-provider)공급자와 함께 압축 공급자 배열에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-381">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="4ee35-382">클라이언트에서 Brotli 압축 데이터 형식이 지원 되는 경우 압축의 기본값은 Brotli 압축입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-382">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="4ee35-383">클라이언트에서 Brotli을 지원 하지 않는 경우 클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-383">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="4ee35-384">모든 압축 공급자가 명시적으로 추가 되 면 Gzip 압축 공급자를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-384">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4ee35-385"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>압축 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-385">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="4ee35-386">Gzip 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-386">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="4ee35-387">가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-387">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="4ee35-388">압축 수준</span><span class="sxs-lookup"><span data-stu-id="4ee35-388">Compression Level</span></span> | <span data-ttu-id="4ee35-389">Description</span><span class="sxs-lookup"><span data-stu-id="4ee35-389">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="4ee35-390">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="4ee35-390">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-391">결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-391">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="4ee35-392">CompressionLevel 압축</span><span class="sxs-lookup"><span data-stu-id="4ee35-392">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-393">압축을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-393">No compression should be performed.</span></span> |
| [<span data-ttu-id="4ee35-394">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="4ee35-394">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-395">압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-395">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="4ee35-396">사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="4ee35-396">Custom providers</span></span>

<span data-ttu-id="4ee35-397"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>를 사용 하 여 사용자 지정 압축 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-397">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="4ee35-398"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>는이 `ICompressionProvider` 생성 하는 콘텐츠 인코딩을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-398">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="4ee35-399">미들웨어는이 정보를 사용 하 여 요청의 `Accept-Encoding` 헤더에 지정 된 목록에 따라 공급자를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-399">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="4ee35-400">클라이언트는 샘플 앱을 사용 하 여 `Accept-Encoding: mycustomcompression` 헤더를 사용 하 여 요청을 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-400">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4ee35-401">미들웨어는 사용자 지정 압축 구현을 사용 하 고 `Content-Encoding: mycustomcompression` 헤더가 포함 된 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-401">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4ee35-402">클라이언트는 사용자 지정 압축 구현이 작동 하기 위해 사용자 지정 인코딩의 압축을 해제할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-402">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="4ee35-403">`Accept-Encoding: mycustomcompression` 헤더를 사용 하 여 샘플 앱에 요청을 제출 하 고 응답 헤더를 관찰 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-403">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="4ee35-404">`Vary` 및 `Content-Encoding` 헤더가 응답에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-404">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="4ee35-405">응답 본문 (표시 되지 않음)은 샘플에 의해 압축 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-405">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="4ee35-406">샘플의 `CustomCompressionProvider` 클래스에는 압축 구현이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-406">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="4ee35-407">그러나이 샘플에서는 이러한 압축 알고리즘을 구현 하는 위치를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-407">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 헤더를 포함 하는 요청 결과와 mycustomcompression 값을 보여 주는 창](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="4ee35-410">MIME 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-410">MIME types</span></span>

<span data-ttu-id="4ee35-411">미들웨어는 압축을 위한 기본 MIME 형식 집합을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-411">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="4ee35-412">MIME 형식을 응답 압축 미들웨어 옵션으로 바꾸거나 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-412">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="4ee35-413">`text/*`와 같은 와일드 카드 MIME 형식은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-413">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="4ee35-414">샘플 앱은 `image/svg+xml`에 대 한 MIME 형식을 추가 하 고 압축 하 여 ASP.NET Core 배너 이미지 (*배너나*)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-414">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="4ee35-415">보안 프로토콜을 사용한 압축</span><span class="sxs-lookup"><span data-stu-id="4ee35-415">Compression with secure protocol</span></span>

<span data-ttu-id="4ee35-416">보안 연결을 통한 압축 된 응답은 기본적으로 사용 하지 않도록 설정 된 `EnableForHttps` 옵션을 사용 하 여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-416">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="4ee35-417">동적으로 생성 된 페이지에서 압축을 사용 하면 [범죄](https://wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://wikipedia.org/wiki/BREACH_(security_exploit)) 공격과 같은 보안 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-417">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="4ee35-418">Vary 헤더 추가</span><span class="sxs-lookup"><span data-stu-id="4ee35-418">Adding the Vary header</span></span>

<span data-ttu-id="4ee35-419">`Accept-Encoding` 헤더를 기반으로 응답을 압축 하는 경우 여러 압축 버전의 응답 및 압축 되지 않은 버전이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-419">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="4ee35-420">여러 버전이 존재 하 고 저장 해야 함을 클라이언트 및 프록시 캐시에 지시 하기 위해 `Vary` 헤더가 `Accept-Encoding` 값으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-420">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="4ee35-421">ASP.NET Core 2.0 이상에서 미들웨어는 응답이 압축 될 때 `Vary` 헤더를 자동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-421">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="4ee35-422">Nginx 역방향 프록시 뒤에 있는 미들웨어 문제</span><span class="sxs-lookup"><span data-stu-id="4ee35-422">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="4ee35-423">Nginx에서 요청을 프록시 하는 경우에는 `Accept-Encoding` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-423">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="4ee35-424">`Accept-Encoding` 헤더를 제거 하면 미들웨어가 응답을 압축 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-424">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="4ee35-425">자세한 내용은 [NGINX: 압축 및](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)압축 해제를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-425">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="4ee35-426">이 문제는 [Nginx (aspnet/BasicMiddleware #123)에 대 한 통과 압축](https://github.com/aspnet/BasicMiddleware/issues/123)을 통해 추적 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-426">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="4ee35-427">IIS 동적 압축 사용</span><span class="sxs-lookup"><span data-stu-id="4ee35-427">Working with IIS dynamic compression</span></span>

<span data-ttu-id="4ee35-428">앱에 대해 사용 하지 않도록 설정할 서버 수준에서 활성 IIS 동적 압축 모듈이 구성 된 경우 *web.config* 파일에 추가 하 여 모듈을 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-428">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="4ee35-429">보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-429">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="4ee35-430">문제 해결</span><span class="sxs-lookup"><span data-stu-id="4ee35-430">Troubleshooting</span></span>

<span data-ttu-id="4ee35-431">`Accept-Encoding` 요청 헤더를 설정 하 고 응답 헤더, 크기 및 본문을 학습할 수 있는 [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-431">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="4ee35-432">기본적으로 응답 압축 미들웨어는 다음 조건을 충족 하는 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-432">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="4ee35-433">`Accept-Encoding` 헤더는 설정 된 사용자 지정 압축 공급자와 일치 하는 `br`, `gzip`, `*`또는 사용자 지정 인코딩의 값과 함께 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-433">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="4ee35-434">값을 `identity` 하거나 품질 값 (qvalue, `q`)을 0 (영)으로 설정 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-434">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="4ee35-435">MIME 형식 (`Content-Type`)을 설정 하 고 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>에 구성 된 MIME 형식과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-435">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="4ee35-436">요청은 `Content-Range` 헤더를 포함 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-436">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="4ee35-437">응답 압축 미들웨어 옵션에 보안 프로토콜 (https)이 구성 되지 않은 경우 요청은 안전 하지 않은 프로토콜 (http)을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-437">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="4ee35-438">*보안 콘텐츠 압축을 사용 하도록 설정할 때 [위에서 설명한](#compression-with-secure-protocol) 위험에 유의 하십시오.*</span><span class="sxs-lookup"><span data-stu-id="4ee35-438">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ee35-439">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="4ee35-439">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="4ee35-440">Mozilla 개발자 네트워크: 수락-인코딩</span><span class="sxs-lookup"><span data-stu-id="4ee35-440">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="4ee35-441">RFC 7231 섹션 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="4ee35-441">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="4ee35-442">RFC 7230 섹션 4.2.3: Gzip 코딩</span><span class="sxs-lookup"><span data-stu-id="4ee35-442">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="4ee35-443">GZIP 파일 형식 사양 버전 4.3</span><span class="sxs-lookup"><span data-stu-id="4ee35-443">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4ee35-444">네트워크 대역폭은 제한 된 리소스입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-444">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="4ee35-445">응답 크기를 줄이면 일반적으로 앱의 응답성이 크게 향상 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-445">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="4ee35-446">페이로드 크기를 줄이는 한 가지 방법은 앱의 응답을 압축 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-446">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="4ee35-447">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4ee35-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="4ee35-448">응답 압축 미들웨어를 사용 하는 경우</span><span class="sxs-lookup"><span data-stu-id="4ee35-448">When to use Response Compression Middleware</span></span>

<span data-ttu-id="4ee35-449">IIS, Apache 또는 Nginx에서 서버 기반 응답 압축 기술을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-449">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="4ee35-450">미들웨어의 성능은 서버 모듈의 성능과 일치 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-450">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="4ee35-451">[Http.sys 서버](xref:fundamentals/servers/httpsys) 서버와 [kestrel](xref:fundamentals/servers/kestrel) 서버는 현재 기본 제공 압축 지원을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-451">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="4ee35-452">응답 압축 미들웨어를 사용 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="4ee35-452">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="4ee35-453">다음 서버 기반 압축 기술을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-453">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="4ee35-454">IIS 동적 압축 모듈</span><span class="sxs-lookup"><span data-stu-id="4ee35-454">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="4ee35-455">Apache mod_deflate 모듈</span><span class="sxs-lookup"><span data-stu-id="4ee35-455">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="4ee35-456">Nginx 압축 및 압축 풀기</span><span class="sxs-lookup"><span data-stu-id="4ee35-456">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="4ee35-457">에서 직접 호스팅:</span><span class="sxs-lookup"><span data-stu-id="4ee35-457">Hosting directly on:</span></span>
  * <span data-ttu-id="4ee35-458">[Http.sys 서버](xref:fundamentals/servers/httpsys) (이전에는 weblistener 라고 함)</span><span class="sxs-lookup"><span data-stu-id="4ee35-458">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="4ee35-459">Kestrel 서버</span><span class="sxs-lookup"><span data-stu-id="4ee35-459">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="4ee35-460">응답 압축</span><span class="sxs-lookup"><span data-stu-id="4ee35-460">Response compression</span></span>

<span data-ttu-id="4ee35-461">일반적으로 기본적으로 압축 되지 않은 모든 응답은 응답 압축의 이점을 누릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-461">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="4ee35-462">기본적으로 압축 되지 않은 응답은 일반적으로 CSS, JavaScript, HTML, XML 및 JSON을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-462">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="4ee35-463">PNG 파일과 같이 기본적으로 압축 된 자산은 압축 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-463">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="4ee35-464">고유 하 게 압축 된 응답을 추가로 압축 하려는 경우 크기 및 전송 시간의 작은 추가 감소는 압축을 처리 하는 데 걸린 시간을 overshadowed 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-464">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="4ee35-465">파일의 내용과 압축 효율성에 따라 약 150-1000 바이트 보다 작은 파일은 압축 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-465">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="4ee35-466">작은 파일을 압축 하는 오버 헤드로 인해 압축 되지 않은 파일 보다 큰 압축 된 파일이 생성 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-466">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="4ee35-467">클라이언트가 압축 된 콘텐츠를 처리할 수 있는 경우 클라이언트는 요청과 함께 `Accept-Encoding` 헤더를 전송 하 여 해당 기능을 서버에 알려야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-467">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="4ee35-468">서버는 압축 된 콘텐츠를 보낼 때 압축 된 응답을 인코딩하는 방법에 대 한 정보를 `Content-Encoding` 헤더에 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-468">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="4ee35-469">미들웨어에서 지 원하는 콘텐츠 인코딩 명칭은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-469">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="4ee35-470">`Accept-Encoding` 헤더 값</span><span class="sxs-lookup"><span data-stu-id="4ee35-470">`Accept-Encoding` header values</span></span> | <span data-ttu-id="4ee35-471">미들웨어 지원</span><span class="sxs-lookup"><span data-stu-id="4ee35-471">Middleware Supported</span></span> | <span data-ttu-id="4ee35-472">Description</span><span class="sxs-lookup"><span data-stu-id="4ee35-472">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="4ee35-473">예</span><span class="sxs-lookup"><span data-stu-id="4ee35-473">No</span></span>                   | [<span data-ttu-id="4ee35-474">Brotli 압축 된 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-474">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="4ee35-475">예</span><span class="sxs-lookup"><span data-stu-id="4ee35-475">No</span></span>                   | [<span data-ttu-id="4ee35-476">DEFLATE 압축 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-476">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="4ee35-477">예</span><span class="sxs-lookup"><span data-stu-id="4ee35-477">No</span></span>                   | [<span data-ttu-id="4ee35-478">W3C 효율적인 XML 교환</span><span class="sxs-lookup"><span data-stu-id="4ee35-478">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="4ee35-479">예 (기본값)</span><span class="sxs-lookup"><span data-stu-id="4ee35-479">Yes (default)</span></span>        | [<span data-ttu-id="4ee35-480">Gzip 파일 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-480">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="4ee35-481">yes</span><span class="sxs-lookup"><span data-stu-id="4ee35-481">Yes</span></span>                  | <span data-ttu-id="4ee35-482">"인코딩 안 함" 식별자: 응답은 인코딩되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-482">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="4ee35-483">예</span><span class="sxs-lookup"><span data-stu-id="4ee35-483">No</span></span>                   | [<span data-ttu-id="4ee35-484">Java 보관을 위한 네트워크 전송 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-484">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="4ee35-485">yes</span><span class="sxs-lookup"><span data-stu-id="4ee35-485">Yes</span></span>                  | <span data-ttu-id="4ee35-486">명시적으로 요청 되지 않은 모든 사용 가능한 콘텐츠 인코딩입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-486">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="4ee35-487">자세한 내용은 [IANA 공식 콘텐츠 코딩 목록](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-487">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="4ee35-488">미들웨어를 사용 하면 사용자 지정 `Accept-Encoding` 헤더 값에 대 한 추가 압축 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-488">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="4ee35-489">자세한 내용은 아래의 [사용자 지정 공급자](#custom-providers) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-489">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="4ee35-490">미들웨어는 압축 체계의 우선 순위를 지정 하기 위해 클라이언트에서 보낼 때 품질 값 (qvalue, `q`) 가중치에 대응 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-490">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="4ee35-491">자세한 내용은 [RFC 7231: 수락-인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-491">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="4ee35-492">압축 알고리즘은 압축 속도와 압축의 효율성 사이의 균형을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-492">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="4ee35-493">이 컨텍스트의 *효과* 는 압축 후 출력의 크기를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-493">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="4ee35-494">가장 작은 크기는 가장 *최적* 압축을 통해 달성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-494">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="4ee35-495">다음 표에서는 압축 된 콘텐츠 요청, 송신, 캐싱 및 수신에 관련 된 헤더에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-495">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="4ee35-496">헤더</span><span class="sxs-lookup"><span data-stu-id="4ee35-496">Header</span></span>             | <span data-ttu-id="4ee35-497">역할</span><span class="sxs-lookup"><span data-stu-id="4ee35-497">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="4ee35-498">클라이언트에서 서버로 전송 되어 클라이언트에 허용 되는 콘텐츠 인코딩 스키마를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-498">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="4ee35-499">페이로드에 있는 콘텐츠의 인코딩을 나타내기 위해 서버에서 클라이언트로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-499">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="4ee35-500">압축이 발생 하면 응답이 압축 될 때 본문 내용이 변경 되기 때문에 `Content-Length` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-500">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="4ee35-501">압축이 발생 하면 본문 내용이 변경 되 고 해시가 더 이상 유효 하지 않기 때문에 `Content-MD5` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-501">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="4ee35-502">콘텐츠의 MIME 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-502">Specifies the MIME type of the content.</span></span> <span data-ttu-id="4ee35-503">모든 응답은 해당 `Content-Type`을 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-503">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="4ee35-504">미들웨어는이 값을 확인 하 여 응답이 압축 되어야 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-504">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="4ee35-505">미들웨어는 인코딩할 수 있는 [기본 MIME 형식의](#mime-types) 집합을 지정 하지만 mime 형식을 바꾸거나 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-505">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="4ee35-506">클라이언트 및 프록시에 `Accept-Encoding` 값을 사용 하 여 서버에서 보낸 경우 `Vary` 헤더는 요청의 `Accept-Encoding` 헤더 값을 기준으로 응답을 캐시 (vary) 해야 함을 클라이언트 또는 프록시에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-506">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="4ee35-507">`Vary: Accept-Encoding` 헤더를 사용 하 여 콘텐츠를 반환한 결과는 압축 된 응답과 압축 되지 않은 응답이 모두 개별적으로 캐시 된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-507">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="4ee35-508">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)을 사용 하 여 응답 압축 미들웨어의 기능을 탐색 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-508">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="4ee35-509">샘플은 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-509">The sample illustrates:</span></span>

* <span data-ttu-id="4ee35-510">Gzip 및 사용자 지정 압축 공급자를 사용 하 여 앱 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-510">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="4ee35-511">Mime 형식을 압축을 위한 MIME 형식의 기본 목록에 추가 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-511">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="4ee35-512">패키지</span><span class="sxs-lookup"><span data-stu-id="4ee35-512">Package</span></span>

<span data-ttu-id="4ee35-513">프로젝트에 미들웨어를 포함 하려면 [ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 패키지를 포함 하는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에 대 한 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-513">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="4ee35-514">구성</span><span class="sxs-lookup"><span data-stu-id="4ee35-514">Configuration</span></span>

<span data-ttu-id="4ee35-515">다음 코드에서는 기본 MIME 형식 및 [Gzip 압축 공급자](#gzip-compression-provider)에 대해 응답 압축 미들웨어를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-515">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="4ee35-516">참고:</span><span class="sxs-lookup"><span data-stu-id="4ee35-516">Notes:</span></span>

* <span data-ttu-id="4ee35-517">`app.UseResponseCompression`는 응답을 압축 하는 미들웨어 보다 먼저 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-517">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="4ee35-518">자세한 내용은 <xref:fundamentals/middleware/index#middleware-order>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-518">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="4ee35-519">[Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/) 과 같은 도구를 사용 하 여 `Accept-Encoding` 요청 헤더를 설정 하 고 응답 헤더, 크기 및 본문을 연구 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-519">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="4ee35-520">`Accept-Encoding` 헤더를 제외 하 고 샘플 앱에 요청을 제출 하 고 응답이 압축 되지 않은 상태 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-520">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="4ee35-521">`Content-Encoding` 및 `Vary` 헤더가 응답에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-521">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![수락 인코딩 헤더가 없는 요청 결과를 보여 주는 Fiddler 창](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="4ee35-524">`Accept-Encoding: gzip` 헤더를 사용 하 여 샘플 앱에 요청을 제출 하 고 응답이 압축 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-524">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="4ee35-525">`Content-Encoding` 및 `Vary` 헤더가 응답에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-525">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![허용 인코딩 헤더와 값이 포함 된 요청의 결과를 보여 주는 Fiddler 창입니다.](response-compression/_static/request-compressed.png)

## <a name="providers"></a><span data-ttu-id="4ee35-529">공급자</span><span class="sxs-lookup"><span data-stu-id="4ee35-529">Providers</span></span>

### <a name="gzip-compression-provider"></a><span data-ttu-id="4ee35-530">Gzip 압축 공급자</span><span class="sxs-lookup"><span data-stu-id="4ee35-530">Gzip Compression Provider</span></span>

<span data-ttu-id="4ee35-531"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>를 사용 하 여 [Gzip 파일 형식](https://tools.ietf.org/html/rfc1952)으로 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-531">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="4ee35-532"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>에 명시적으로 추가 된 압축 공급자가 없으면:</span><span class="sxs-lookup"><span data-stu-id="4ee35-532">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="4ee35-533">Gzip 압축 공급자는 기본적으로 압축 공급자 배열에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-533">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="4ee35-534">클라이언트에서 Gzip 압축을 지 원하는 경우 압축의 기본값은 Gzip입니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-534">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="4ee35-535">모든 압축 공급자가 명시적으로 추가 되 면 Gzip 압축 공급자를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-535">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4ee35-536"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>압축 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-536">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="4ee35-537">Gzip 압축 공급자는 기본적으로 가장 빠른 압축 수준 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel))으로 설정 되며이는 가장 효율적인 압축을 생성 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-537">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="4ee35-538">가장 효율적인 압축이 필요한 경우 최적의 압축을 위해 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-538">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="4ee35-539">압축 수준</span><span class="sxs-lookup"><span data-stu-id="4ee35-539">Compression Level</span></span> | <span data-ttu-id="4ee35-540">Description</span><span class="sxs-lookup"><span data-stu-id="4ee35-540">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="4ee35-541">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="4ee35-541">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-542">결과 출력이 최적으로 압축 되지 않은 경우에도 압축이 최대한 빨리 완료 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-542">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="4ee35-543">CompressionLevel 압축</span><span class="sxs-lookup"><span data-stu-id="4ee35-543">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-544">압축을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-544">No compression should be performed.</span></span> |
| [<span data-ttu-id="4ee35-545">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="4ee35-545">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="4ee35-546">압축을 완료 하는 데 더 많은 시간이 소요 되는 경우에도 응답은 최적으로 압축 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-546">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="4ee35-547">사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="4ee35-547">Custom providers</span></span>

<span data-ttu-id="4ee35-548"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>를 사용 하 여 사용자 지정 압축 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-548">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="4ee35-549"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>는이 `ICompressionProvider` 생성 하는 콘텐츠 인코딩을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-549">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="4ee35-550">미들웨어는이 정보를 사용 하 여 요청의 `Accept-Encoding` 헤더에 지정 된 목록에 따라 공급자를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-550">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="4ee35-551">클라이언트는 샘플 앱을 사용 하 여 `Accept-Encoding: mycustomcompression` 헤더를 사용 하 여 요청을 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-551">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4ee35-552">미들웨어는 사용자 지정 압축 구현을 사용 하 고 `Content-Encoding: mycustomcompression` 헤더가 포함 된 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-552">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="4ee35-553">클라이언트는 사용자 지정 압축 구현이 작동 하기 위해 사용자 지정 인코딩의 압축을 해제할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-553">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="4ee35-554">`Accept-Encoding: mycustomcompression` 헤더를 사용 하 여 샘플 앱에 요청을 제출 하 고 응답 헤더를 관찰 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-554">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="4ee35-555">`Vary` 및 `Content-Encoding` 헤더가 응답에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-555">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="4ee35-556">응답 본문 (표시 되지 않음)은 샘플에 의해 압축 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-556">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="4ee35-557">샘플의 `CustomCompressionProvider` 클래스에는 압축 구현이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-557">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="4ee35-558">그러나이 샘플에서는 이러한 압축 알고리즘을 구현 하는 위치를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-558">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 헤더를 포함 하는 요청 결과와 mycustomcompression 값을 보여 주는 창](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="4ee35-561">MIME 형식</span><span class="sxs-lookup"><span data-stu-id="4ee35-561">MIME types</span></span>

<span data-ttu-id="4ee35-562">미들웨어는 압축을 위한 기본 MIME 형식 집합을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-562">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="4ee35-563">MIME 형식을 응답 압축 미들웨어 옵션으로 바꾸거나 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-563">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="4ee35-564">`text/*`와 같은 와일드 카드 MIME 형식은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-564">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="4ee35-565">샘플 앱은 `image/svg+xml`에 대 한 MIME 형식을 추가 하 고 압축 하 여 ASP.NET Core 배너 이미지 (*배너나*)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-565">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="4ee35-566">보안 프로토콜을 사용한 압축</span><span class="sxs-lookup"><span data-stu-id="4ee35-566">Compression with secure protocol</span></span>

<span data-ttu-id="4ee35-567">보안 연결을 통한 압축 된 응답은 기본적으로 사용 하지 않도록 설정 된 `EnableForHttps` 옵션을 사용 하 여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-567">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="4ee35-568">동적으로 생성 된 페이지에서 압축을 사용 하면 [범죄](https://wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://wikipedia.org/wiki/BREACH_(security_exploit)) 공격과 같은 보안 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-568">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="4ee35-569">Vary 헤더 추가</span><span class="sxs-lookup"><span data-stu-id="4ee35-569">Adding the Vary header</span></span>

<span data-ttu-id="4ee35-570">`Accept-Encoding` 헤더를 기반으로 응답을 압축 하는 경우 여러 압축 버전의 응답 및 압축 되지 않은 버전이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-570">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="4ee35-571">여러 버전이 존재 하 고 저장 해야 함을 클라이언트 및 프록시 캐시에 지시 하기 위해 `Vary` 헤더가 `Accept-Encoding` 값으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-571">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="4ee35-572">ASP.NET Core 2.0 이상에서 미들웨어는 응답이 압축 될 때 `Vary` 헤더를 자동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-572">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="4ee35-573">Nginx 역방향 프록시 뒤에 있는 미들웨어 문제</span><span class="sxs-lookup"><span data-stu-id="4ee35-573">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="4ee35-574">Nginx에서 요청을 프록시 하는 경우에는 `Accept-Encoding` 헤더가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-574">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="4ee35-575">`Accept-Encoding` 헤더를 제거 하면 미들웨어가 응답을 압축 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-575">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="4ee35-576">자세한 내용은 [NGINX: 압축 및](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)압축 해제를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4ee35-576">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="4ee35-577">이 문제는 [Nginx (aspnet/BasicMiddleware #123)에 대 한 통과 압축](https://github.com/aspnet/BasicMiddleware/issues/123)을 통해 추적 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-577">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="4ee35-578">IIS 동적 압축 사용</span><span class="sxs-lookup"><span data-stu-id="4ee35-578">Working with IIS dynamic compression</span></span>

<span data-ttu-id="4ee35-579">앱에 대해 사용 하지 않도록 설정할 서버 수준에서 활성 IIS 동적 압축 모듈이 구성 된 경우 *web.config* 파일에 추가 하 여 모듈을 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-579">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="4ee35-580">보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-580">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="4ee35-581">문제 해결</span><span class="sxs-lookup"><span data-stu-id="4ee35-581">Troubleshooting</span></span>

<span data-ttu-id="4ee35-582">`Accept-Encoding` 요청 헤더를 설정 하 고 응답 헤더, 크기 및 본문을 학습할 수 있는 [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-582">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="4ee35-583">기본적으로 응답 압축 미들웨어는 다음 조건을 충족 하는 응답을 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-583">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="4ee35-584">설정 된 사용자 지정 압축 공급자와 일치 하는 `gzip`, `*`또는 사용자 지정 인코딩의 값이 `Accept-Encoding` 헤더가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-584">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="4ee35-585">값을 `identity` 하거나 품질 값 (qvalue, `q`)을 0 (영)으로 설정 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-585">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="4ee35-586">MIME 형식 (`Content-Type`)을 설정 하 고 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>에 구성 된 MIME 형식과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-586">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="4ee35-587">요청은 `Content-Range` 헤더를 포함 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-587">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="4ee35-588">응답 압축 미들웨어 옵션에 보안 프로토콜 (https)이 구성 되지 않은 경우 요청은 안전 하지 않은 프로토콜 (http)을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ee35-588">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="4ee35-589">*보안 콘텐츠 압축을 사용 하도록 설정할 때 [위에서 설명한](#compression-with-secure-protocol) 위험에 유의 하십시오.*</span><span class="sxs-lookup"><span data-stu-id="4ee35-589">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ee35-590">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="4ee35-590">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="4ee35-591">Mozilla 개발자 네트워크: 수락-인코딩</span><span class="sxs-lookup"><span data-stu-id="4ee35-591">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="4ee35-592">RFC 7231 섹션 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="4ee35-592">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="4ee35-593">RFC 7230 섹션 4.2.3: Gzip 코딩</span><span class="sxs-lookup"><span data-stu-id="4ee35-593">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="4ee35-594">GZIP 파일 형식 사양 버전 4.3</span><span class="sxs-lookup"><span data-stu-id="4ee35-594">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
