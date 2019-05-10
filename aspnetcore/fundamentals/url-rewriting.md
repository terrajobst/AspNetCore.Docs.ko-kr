---
title: ASP.NET Core에서 URL 재작성 미들웨어
author: guardrex
description: ASP.NET Core 애플리케이션에서 URL 재작성 미들웨어로 URL 재작성 및 리디렉션 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/url-rewriting
ms.openlocfilehash: 72d5b2e902a95442ccffb7a149b917c50373775b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64889928"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="55087-103">ASP.NET Core에서 URL 재작성 미들웨어</span><span class="sxs-lookup"><span data-stu-id="55087-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="55087-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="55087-104">By [Luke Latham](https://github.com/guardrex) and [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="55087-105">이 항목의 1.1 버전을 얻으려면 [ASP.NET Core에서 URL 재작성 미들웨어(버전 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/URL_Rewriting_1.1.pdf)를 다운로드하세요.</span><span class="sxs-lookup"><span data-stu-id="55087-105">For the 1.1 version of this topic, download [URL Rewriting Middleware in ASP.NET Core (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/URL_Rewriting_1.1.pdf).</span></span>

::: moniker-end

<span data-ttu-id="55087-106">본문에서는 ASP.NET Core 응용 프로그램에서 URL 재작성 미들웨어를 사용하는 방법에 관한 지침과 URL 재작성에 관해서 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="55087-106">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="55087-107">URL 재작성은 하나 이상의 미리 정의된 규칙을 기반으로 하는 요청 URL을 수정하는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="55087-107">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="55087-108">URL 재작성은 위치와 주소가 밀접하게 연결되지 않도록 리소스 위치와 해당 주소 간의 추상화를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="55087-108">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="55087-109">URL 재작성이 중요한 몇 가지 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-109">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="55087-110">서버 리소스를 일시적 또는 영구적으로 이동하거나 대체하고, 해당 리소스에 대한 안정적인 로케이터를 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-110">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="55087-111">여러 앱 또는 한 앱의 여러 영역 간에 요청 처리를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-111">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="55087-112">들어오는 요청의 URL 세그먼트를 제거, 추가, 또는 다시 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-112">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="55087-113">SEO(검색 엔진 최적화)에 맞게 공용 URL을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-113">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="55087-114">친숙한 공용 URL을 사용하여 방문자가 리소스를 요청함으로써 반환되는 콘텐츠를 예측할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-114">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="55087-115">안전하지 않은 요청을 보안 엔드포인트로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-115">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="55087-116">외부 사이트에서 자산을 자체의 콘텐츠에 연결하여 다른 사이트에서 호스팅된 정적 자산을 사용하는 핫 링크를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-116">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="55087-117">URL 재작성은 응용 프로그램의 성능을 저하시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-117">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="55087-118">가능한 경우 규칙의 수와 복잡성을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-118">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="55087-119">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="55087-119">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="55087-120">URL 리디렉션 및 URL 재작성</span><span class="sxs-lookup"><span data-stu-id="55087-120">URL redirect and URL rewrite</span></span>

<span data-ttu-id="55087-121">*URL 리디렉션*과 *URL 재작성* 간의 표현 차이는 미묘하지만 클라이언트에 리소스를 제공하는 데 더 중요한 의미가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-121">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="55087-122">ASP.NET Core의 URL 재작성 미들웨어는 두 가지 모두에 대한 요구를 만족합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-122">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="55087-123">*URL 리디렉션*은 클라이언트 쪽 작업과 관련되어 있습니다. 여기서 클라이언트는 원래 요청한 클라이언트와는 다른 주소로 리소스에 액세스하도록 지시받습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-123">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="55087-124">이 경우 서버를 왕복해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-124">This requires a round trip to the server.</span></span> <span data-ttu-id="55087-125">클라이언트로 반환된 리디렉션 URL은 클라이언트가 리소스에 대한 새로운 요청을 만들 때 브라우저의 주소 표시줄에 나타나게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-125">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="55087-126">`/resource`가 `/different-resource`로 *리디렉션*되는 경우 서버는 임시 또는 영구 리디렉션을 나타내는 상태 코드와 함께 클라이언트가 `/different-resource`에서 리소스를 가져와야 한다고 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-126">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 임시 변경됩니다.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="55087-132">요청을 다른 URL로 리디렉션하는 경우 응답과 함께 상태 코드를 지정하여 영구 리디렉션 또는 임시 리디렉션인지의 여부를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-132">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="55087-133">*301 - 영구적으로 이동됨* 상태 코드는 리소스에 새 영구 URL이 있고, 리소스에 대한 이후의 모든 요청에서 새 URL을 사용해야 한다고 클라이언트에 지시하려는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-133">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="55087-134">*301 상태 코드를 받으면 클라이언트에서 응답을 캐시하고 다시 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="55087-134">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="55087-135">*302 - 있음* 상태 코드는 리디렉션이 일시적이거나 일반적으로 변경될 수 있는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-135">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="55087-136">302 상태 코드는 클라이언트에서 URL을 저장하지 않고 나중에 사용하지 못하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-136">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="55087-137">상태 코드에 대한 자세한 내용은 [RFC 2616: 상태 코드 정의](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="55087-137">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="55087-138">*URL 재작성*은 요청한 클라이언트와 다른 리소스 주소에서 리소스를 제공하는 서버 쪽 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="55087-138">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="55087-139">URL을 다시 작성하는 경우 서버를 왕복할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-139">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="55087-140">다시 작성된 URL은 클라이언트에 반환되지 않고 브라우저의 주소 표시줄에도 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-140">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="55087-141">`/resource`가 `/different-resource`에 *다시 작성*되면 서버에서 *내부적으로* `/different-resource`에 있는 리소스를 가져와서 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-141">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="55087-142">클라이언트는 다시 작성된 URL에서 리소스를 검색할 수 있지만, 요청을 만들고 응답을 받을 때 리소스가 다시 작성된 URL에 있음을 클라이언트에 알리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-142">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 변경됩니다.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="55087-147">URL 재작성 예제 응용 프로그램</span><span class="sxs-lookup"><span data-stu-id="55087-147">URL rewriting sample app</span></span>

<span data-ttu-id="55087-148">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)을 사용하면 URL 재작성 미들웨어의 기능을 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-148">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="55087-149">이 앱은 리디렉션 및 재작성 규칙을 적용하고, 여러 시나리오에 대해 리디렉션되거나 다시 작성된 URL을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-149">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="55087-150">URL 재작성 미들웨어를 사용해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="55087-150">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="55087-151">다음 방법을 사용할 수 없는 경우 URL 재작성 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-151">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* <span data-ttu-id="55087-152">Windows Server의 IIS를 사용하는 [URL 재작성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)</span><span class="sxs-lookup"><span data-stu-id="55087-152">[URL Rewrite module with IIS on Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)</span></span>
* <span data-ttu-id="55087-153">Apache Server의 [Apache mod_rewrite 모듈](https://httpd.apache.org/docs/2.4/rewrite/)</span><span class="sxs-lookup"><span data-stu-id="55087-153">[Apache mod_rewrite module on Apache Server](https://httpd.apache.org/docs/2.4/rewrite/)</span></span>
* [<span data-ttu-id="55087-154">Nginx의 URL 재작성</span><span class="sxs-lookup"><span data-stu-id="55087-154">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="55087-155">또한 앱이 [HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)에서 호스팅되는 경우 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-155">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="55087-156">IIS, Apache 및 Nginx에서 서버 기반 URL 재작성 기술을 사용하는 주요 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-156">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="55087-157">미들웨어에서 이러한 모듈의 전체 기능을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-157">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="55087-158">서버 모듈의 일부 기능이 IIS 재작성 모듈의 `IsFile` 및 `IsDirectory` 제약 조건과 같은 ASP.NET Core 프로젝트에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-158">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="55087-159">바로 이런 시나리오에서 대신 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-159">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="55087-160">미들웨어의 성능이 아마도 모듈의 성능과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-160">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="55087-161">벤치마킹은 성능을 가장 많이 저하시키는 방법 또는 저하된 성능을 무시할 수 있는 경우를 확인할 수 있는 유일한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="55087-161">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="55087-162">패키지</span><span class="sxs-lookup"><span data-stu-id="55087-162">Package</span></span>

<span data-ttu-id="55087-163">프로젝트에 미들웨어를 포함시키려면 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 패키지가 포함된 프로젝트 파일의 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-163">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="55087-164">`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 `Microsoft.AspNetCore.Rewrite` 패키지에 프로젝트 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-164">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="55087-165">확장 및 옵션</span><span class="sxs-lookup"><span data-stu-id="55087-165">Extension and options</span></span>

<span data-ttu-id="55087-166">각각의 재작성 규칙에 대한 확장 메서드를 사용하여 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 클래스의 인스턴스를 만들어 URL 재작성 및 리디렉션 규칙을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-166">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="55087-167">처리하고자 하는 순서대로 여러 규칙을 연결하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-167">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="55087-168">`RewriteOptions`는 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>를 사용하여 요청 파이프라인에 추가될 때 URL 재작성 미들웨어에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-168">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="55087-169">www 이외 요청을 www로 리디렉션</span><span class="sxs-lookup"><span data-stu-id="55087-169">Redirect non-www to www</span></span>

<span data-ttu-id="55087-170">이러한 옵션은 앱이 `www` 이외 요청을 `www`로 리디렉션하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-170">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="55087-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; 요청이 `www`가 아닌 경우 `www` 하위 도메인으로 영구적으로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="55087-172">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 상태 코드를 사용하여 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-172">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="55087-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; 들어오는 요청이 `www`가 아닌 경우 요청을 `www` 하위 도메인으로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="55087-174">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 상태 코드를 사용하여 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-174">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="55087-175">오버로드를 사용하면 응답에 대한 상태 코드를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-175">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="55087-176">상태 코드 할당을 위해 <xref:Microsoft.AspNetCore.Http.StatusCodes> 클래스의 필드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-176">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="55087-177">URL 리디렉션</span><span class="sxs-lookup"><span data-stu-id="55087-177">URL redirect</span></span>

<span data-ttu-id="55087-178">요청을 리디렉션하려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-178">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="55087-179">첫 번째 매개 변수에는 들어오는 URL의 경로와 일치하는 부분을 찾기 위한 정규식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-179">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="55087-180">두 번째 매개 변수는 대체 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="55087-180">The second parameter is the replacement string.</span></span> <span data-ttu-id="55087-181">필요한 경우 세 번째 매개 변수로 상태 코드를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-181">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="55087-182">상태 코드를 지정하지 않으면 상태 코드가 기본적으로 *302 - 있음*으로 설정되며, 이는 리소스가 일시적으로 이동하거나 대체되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="55087-182">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="55087-183">개발자 도구가 활성화된 브라우저에서 예제 응용 프로그램에 `/redirect-rule/1234/5678` 경로를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-183">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="55087-184">그러면 정규식이 `redirect-rule/(.*)`의 요청 경로와 일치하므로 `/redirected/1234/5678`로 경로가 치환됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-184">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="55087-185">리디렉션 URL은 *302 - 있음* 상태 코드와 함께 클라이언트로 다시 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-185">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="55087-186">브라우저는 리디렉션 URL에 대한 새로운 요청을 만들고 이 주소는 브라우저의 주소 표시줄에 출력됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-186">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="55087-187">샘플 앱의 규칙이 리디렉션 URL에서 일치하지 않으므로 다음과 같이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-187">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="55087-188">두 번째 요청에서 앱의 *200 - 정상* 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-188">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="55087-189">응답 본문에 리디렉션 URL이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-189">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="55087-190">URL이 *리디렉션*되면 서버로의 왕복이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-190">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="55087-191">리디렉션 규칙을 설정할 때에는 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="55087-191">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="55087-192">리디렉션 규칙은 리디렉션 이후를 포함하여 앱에 대한 모든 요청에서 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-192">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="55087-193">따라서 *무한 리디렉션 루프*를 실수로 만들기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-193">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="55087-194">원본 요청: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="55087-194">Original Request: `/redirect-rule/1234/5678`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="55087-196">표현식에서 괄호로 둘러쌓인 부분을 *캡처 그룹(Capture Group)* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-196">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="55087-197">그리고 표현식에서 마침표(`.`)는 모든 문자와 일치함을 뜻합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-197">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="55087-198">마지막으로 별표(`*`)는 앞의 문자와 0번 이상 일치함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="55087-198">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="55087-199">따라서 URL의 마지막 두 세그먼트, `1234/5678`은 캡쳐 그룹 `(.*)`에 의해 캡쳐됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-199">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="55087-200">즉 요청 URL에서 `redirect-rule/` 이후에 제공하는 모든 값이 이 단일 캡처 그룹에 의해서 캡처됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-200">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="55087-201">대체 문자열에서, 캡처된 그룹은 캡처의 일련번호가 뒤에 붙는 달러 기호(`$`)를 통해서 문자열에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-201">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="55087-202">첫 번째 캡처 그룹 값은 `$1`로 얻을 수 있고, 두 번째 캡처 그룹 값은 `$2`로 얻을 수 있으며, 이는 정규식에 포함된 캡처 그룹에 대해 순차적으로 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-202">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="55087-203">예제 응용 프로그램에서 리디렉션 규칙의 정규식에 캡처된 그룹은 단 하나뿐이므로 대체 문자열에 삽입되는 그룹도 `$1` 하나뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="55087-203">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="55087-204">규칙이 적용되고 나면 URL은 `/redirected/1234/5678`로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-204">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="55087-205">보안 엔드포인트에 대한 URL 리디렉션</span><span class="sxs-lookup"><span data-stu-id="55087-205">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="55087-206"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>를 사용하여 HTTPS 프로토콜을 통해 HTTP 요청을 동일한 호스트 및 경로로 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-206">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="55087-207">상태 코드가 제공되지 않는 경우 미들웨어는 기본적으로 *302 - 있음*으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-207">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="55087-208">포트가 제공되지 않는 경우 다음과 같이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-208">If the port isn't supplied:</span></span>

* <span data-ttu-id="55087-209">미들웨어가 기본적으로 `null`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-209">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="55087-210">체계가 `https`(HTTPS 프로토콜)로 변경되고 클라이언트에서 443 포트의 리소스에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-210">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="55087-211">다음 예제에서는 상태 코드를 *301 - 영구적으로 이동됨*으로 설정하고 포트를 5001로 변경하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="55087-211">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="55087-212"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>를 사용하여 443 포트의 HTTPS 프로토콜을 통해 안전하지 않은 요청을 동일한 호스트 및 경로로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-212">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="55087-213">미들웨어에서 상태 코드를 *301 - 영구적으로 이동됨*으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-213">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="55087-214">추가 리디렉션 규칙을 요구하지 않고 보안 엔드포인트로 리디렉션하는 경우 HTTPS 리디렉션 미들웨어를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-214">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="55087-215">자세한 내용은 [HTTPS 적용](xref:security/enforcing-ssl#require-https) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="55087-215">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="55087-216">예제 응용 프로그램을 통해서 `AddRedirectToHttps` 또는 `AddRedirectToHttpsPermanent`의 사용 방법을 확인해 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-216">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="55087-217">먼저 `RewriteOptions`에 이 확장 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-217">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="55087-218">모든 URL에서 앱에 대한 안전하지 않은 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="55087-218">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="55087-219">자체 서명된 인증서를 신뢰할 수 없다는 브라우저 보안 경고는 무시하면 됩니다. 또는 인증서를 신뢰할 예외를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="55087-219">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="55087-220">`AddRedirectToHttps(301, 5001)`에 대한 원본 요청: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="55087-220">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="55087-222">`AddRedirectToHttpsPermanent`에 대한 원본 요청: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="55087-222">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="55087-224">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="55087-224">URL rewrite</span></span>

<span data-ttu-id="55087-225">URL을 재작성하는 규칙을 만들려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-225">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="55087-226">첫 번째 매개 변수에는 들어오는 URL의 경로에서 일치하는 정규식이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-226">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="55087-227">두 번째 매개 변수는 대체 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="55087-227">The second parameter is the replacement string.</span></span> <span data-ttu-id="55087-228">세 번째 매개 변수, `skipRemainingRules: {true|false}`는 현재 규칙이 적용되는 경우에 추가 재작성 규칙을 건너뛸 것인지 여부를 미들웨어에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="55087-228">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="55087-229">원래 요청: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="55087-229">Original Request: `/rewrite-rule/1234/5678`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="55087-231">식의 시작 부분에 있는 캐럿(`^`)은 URL 경로의 시작 부분에서 일치가 시작된다는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-231">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="55087-232">`redirect-rule/(.*)` 리디렉션 규칙이 있는 이전 예제에는 정규식의 시작 부분에 캐럿(`^`)이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-232">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="55087-233">따라서 일치하는 모든 문자가 경로의 `redirect-rule/` 앞에 나올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-233">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="55087-234">경로</span><span class="sxs-lookup"><span data-stu-id="55087-234">Path</span></span>                               | <span data-ttu-id="55087-235">일치</span><span class="sxs-lookup"><span data-stu-id="55087-235">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="55087-236">예</span><span class="sxs-lookup"><span data-stu-id="55087-236">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="55087-237">예</span><span class="sxs-lookup"><span data-stu-id="55087-237">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="55087-238">예</span><span class="sxs-lookup"><span data-stu-id="55087-238">Yes</span></span>   |

<span data-ttu-id="55087-239">반면 `^rewrite-rule/(\d+)/(\d+)` 재작성 규칙의 경우에는 오로지 `rewrite-rule/`로 시작하는 경로만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-239">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="55087-240">다음 표에는 일치에서의 차이가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-240">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="55087-241">경로</span><span class="sxs-lookup"><span data-stu-id="55087-241">Path</span></span>                              | <span data-ttu-id="55087-242">일치</span><span class="sxs-lookup"><span data-stu-id="55087-242">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="55087-243">예</span><span class="sxs-lookup"><span data-stu-id="55087-243">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="55087-244">아니요</span><span class="sxs-lookup"><span data-stu-id="55087-244">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="55087-245">아니요</span><span class="sxs-lookup"><span data-stu-id="55087-245">No</span></span>    |

<span data-ttu-id="55087-246">표현식의 `^rewrite-rule/` 부분 뒤에는 계속해서 두 개의 캡처 그룹, `(\d+)/(\d+)`이 위치해 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-246">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="55087-247">여기서 `\d`는 *숫자 하나와 일치*함을 뜻합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-247">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="55087-248">그리고 더하기 기호(`+`)는 *앞의 문자와 한 번 이상 일치*함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="55087-248">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="55087-249">따라서 URL은 반드시 숫자 뒤에 슬래시와 다른 숫자가 연이어 나타나는 부분을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-249">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="55087-250">이 캡쳐 그룹들은 `$1` 및 `$2`를 통해서 재작성 URL에 삽입됩니다. </span><span class="sxs-lookup"><span data-stu-id="55087-250">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="55087-251">재작성 규칙의 대체 문자열은 캡처된 그룹을 쿼리 문자열에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-251">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="55087-252">즉, 요청 경로 `/rewrite-rule/1234/5678`은 `/rewritten?var1=1234&var2=5678`에서 리소스를 가져오도록 재작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-252">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="55087-253">원본 요청에 쿼리 문자열이 있으면 URL을 다시 작성할 때 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-253">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="55087-254">리소스를 가져오기 위해 서버를 왕복하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-254">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="55087-255">리소스가 있으면 이를 가져와서 *200 - 정상* 상태 코드와 함께 클라이언트에 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-255">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="55087-256">클라이언트는 리디렉션 되지 않으므로 브라우저 주소 표시줄의 URL은 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-256">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="55087-257">클라이언트는 서버에서 URL 재작성 작업이 발생했음을 검색할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-257">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="55087-258">일치 규칙은 컴퓨팅 측면에서 비용이 많이 들고 앱의 응답 속도가 증가되므로 가능한 경우 `skipRemainingRules: true`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-258">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="55087-259">최대한 빠른 응용 프로그램 응답을 위해서는:</span><span class="sxs-lookup"><span data-stu-id="55087-259">For the fastest app response:</span></span>
>
> * <span data-ttu-id="55087-260">재작성 규칙을 가장 자주 일치하는 규칙에서 가장 드물게 일치하는 규칙으로의 순서로 정렬합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-260">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="55087-261">일치가 발생하고 추가적인 규칙 처리가 필요하지 않다면 나머지 규칙의 처리를 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-261">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-modrewrite"></a><span data-ttu-id="55087-262">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="55087-262">Apache mod_rewrite</span></span>

<span data-ttu-id="55087-263"><xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>를 사용하면 Apache mod_rewrite 규칙을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-263">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="55087-264">규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="55087-264">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="55087-265">mod_rewrite 규칙에 대한 보다 자세한 내용과 예제는 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="55087-265">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="55087-266"><xref:System.IO.StreamReader>는 *ApacheModRewrite.txt* 규칙 파일에서 규칙을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-266">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="55087-267">예제 응용 프로그램은 `/apache-mod-rules-redirect/(.\*)`에서 `/redirected?id=$1`로 요청을 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-267">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="55087-268">응답 상태 코드는 *302 - 있음*입니다.</span><span class="sxs-lookup"><span data-stu-id="55087-268">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="55087-269">원본 요청: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="55087-269">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="55087-271">미들웨어는 다음과 같은 Apache mod_rewrite 서버 변수를 지원합니다:</span><span class="sxs-lookup"><span data-stu-id="55087-271">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="55087-272">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="55087-272">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="55087-273">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="55087-273">HTTP_ACCEPT</span></span>
* <span data-ttu-id="55087-274">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="55087-274">HTTP_CONNECTION</span></span>
* <span data-ttu-id="55087-275">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="55087-275">HTTP_COOKIE</span></span>
* <span data-ttu-id="55087-276">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="55087-276">HTTP_FORWARDED</span></span>
* <span data-ttu-id="55087-277">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="55087-277">HTTP_HOST</span></span>
* <span data-ttu-id="55087-278">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="55087-278">HTTP_REFERER</span></span>
* <span data-ttu-id="55087-279">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="55087-279">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="55087-280">HTTPS</span><span class="sxs-lookup"><span data-stu-id="55087-280">HTTPS</span></span>
* <span data-ttu-id="55087-281">IPV6</span><span class="sxs-lookup"><span data-stu-id="55087-281">IPV6</span></span>
* <span data-ttu-id="55087-282">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="55087-282">QUERY_STRING</span></span>
* <span data-ttu-id="55087-283">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="55087-283">REMOTE_ADDR</span></span>
* <span data-ttu-id="55087-284">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="55087-284">REMOTE_PORT</span></span>
* <span data-ttu-id="55087-285">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="55087-285">REQUEST_FILENAME</span></span>
* <span data-ttu-id="55087-286">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="55087-286">REQUEST_METHOD</span></span>
* <span data-ttu-id="55087-287">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="55087-287">REQUEST_SCHEME</span></span>
* <span data-ttu-id="55087-288">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="55087-288">REQUEST_URI</span></span>
* <span data-ttu-id="55087-289">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="55087-289">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="55087-290">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="55087-290">SERVER_ADDR</span></span>
* <span data-ttu-id="55087-291">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="55087-291">SERVER_PORT</span></span>
* <span data-ttu-id="55087-292">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="55087-292">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="55087-293">TIME</span><span class="sxs-lookup"><span data-stu-id="55087-293">TIME</span></span>
* <span data-ttu-id="55087-294">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="55087-294">TIME_DAY</span></span>
* <span data-ttu-id="55087-295">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="55087-295">TIME_HOUR</span></span>
* <span data-ttu-id="55087-296">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="55087-296">TIME_MIN</span></span>
* <span data-ttu-id="55087-297">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="55087-297">TIME_MON</span></span>
* <span data-ttu-id="55087-298">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="55087-298">TIME_SEC</span></span>
* <span data-ttu-id="55087-299">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="55087-299">TIME_WDAY</span></span>
* <span data-ttu-id="55087-300">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="55087-300">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="55087-301">IIS URL 재작성 모듈 규칙</span><span class="sxs-lookup"><span data-stu-id="55087-301">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="55087-302">IIS URL 재작성 모듈에 적용되는 것과 동일한 규칙 세트를 사용하려면 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-302">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="55087-303">규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="55087-303">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="55087-304">Windows Server IIS에서 실행하는 경우 미들웨어에서 앱의 *web.config* 파일을 사용하도록 지시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-304">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="55087-305">IIS를 사용하는 경우 IIS 재작성 모듈과 충돌하지 않도록 이러한 규칙을 앱의 *web.config* 파일 외부에 저장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-305">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="55087-306">IIS URL 재작성 모듈 규칙에 대한 보다 자세한 내용 및 예제는 [URL 재작성 모듈 2.0 사용](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)과 [URL 재작성 모듈 구성 참조](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="55087-306">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="55087-307"><xref:System.IO.StreamReader>는 *IISUrlRewrite.xml* 규칙 파일에서 규칙을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-307">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="55087-308">예제 응용 프로그램은 `/iis-rules-rewrite/(.*)`에서 `/rewritten?id=$1`로 요청을 재작성합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-308">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="55087-309">응답이 *200 - 정상* 상태 코드와 함께 클라이언트에 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-309">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="55087-310">원본 요청: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="55087-310">Original Request: `/iis-rules-rewrite/1234`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="55087-312">원하지 않는 방식으로 응용 프로그램에 영향을 주는 서버 수준 규칙이 구성된 활성 IIS 재작성 모듈이 존재할 경우 응용 프로그램에 대한 IIS 재작성 모듈을 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-312">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="55087-313">보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="55087-313">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="55087-314">지원되지 않는 기능</span><span class="sxs-lookup"><span data-stu-id="55087-314">Unsupported features</span></span>

<span data-ttu-id="55087-315">ASP.NET Core 2.x로 출시된 미들웨어는 다음과 같은 IIS URL 재작성 모듈 기능을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-315">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="55087-316">아웃바운드 규칙</span><span class="sxs-lookup"><span data-stu-id="55087-316">Outbound Rules</span></span>
* <span data-ttu-id="55087-317">사용자 지정 서버 변수</span><span class="sxs-lookup"><span data-stu-id="55087-317">Custom Server Variables</span></span>
* <span data-ttu-id="55087-318">와일드카드</span><span class="sxs-lookup"><span data-stu-id="55087-318">Wildcards</span></span>
* <span data-ttu-id="55087-319">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="55087-319">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="55087-320">지원되는 서버 변수</span><span class="sxs-lookup"><span data-stu-id="55087-320">Supported server variables</span></span>

<span data-ttu-id="55087-321">미들웨어는 다음과 같은 IIS URL 재작성 모듈 서버 변수를 지원합니다:</span><span class="sxs-lookup"><span data-stu-id="55087-321">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="55087-322">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="55087-322">CONTENT_LENGTH</span></span>
* <span data-ttu-id="55087-323">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="55087-323">CONTENT_TYPE</span></span>
* <span data-ttu-id="55087-324">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="55087-324">HTTP_ACCEPT</span></span>
* <span data-ttu-id="55087-325">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="55087-325">HTTP_CONNECTION</span></span>
* <span data-ttu-id="55087-326">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="55087-326">HTTP_COOKIE</span></span>
* <span data-ttu-id="55087-327">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="55087-327">HTTP_HOST</span></span>
* <span data-ttu-id="55087-328">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="55087-328">HTTP_REFERER</span></span>
* <span data-ttu-id="55087-329">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="55087-329">HTTP_URL</span></span>
* <span data-ttu-id="55087-330">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="55087-330">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="55087-331">HTTPS</span><span class="sxs-lookup"><span data-stu-id="55087-331">HTTPS</span></span>
* <span data-ttu-id="55087-332">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="55087-332">LOCAL_ADDR</span></span>
* <span data-ttu-id="55087-333">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="55087-333">QUERY_STRING</span></span>
* <span data-ttu-id="55087-334">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="55087-334">REMOTE_ADDR</span></span>
* <span data-ttu-id="55087-335">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="55087-335">REMOTE_PORT</span></span>
* <span data-ttu-id="55087-336">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="55087-336">REQUEST_FILENAME</span></span>
* <span data-ttu-id="55087-337">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="55087-337">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="55087-338"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>를 이용해서 <xref:Microsoft.Extensions.FileProviders.IFileProvider>를 가져올 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-338">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="55087-339">이 방식이 재작성 규칙 파일의 위치에 대해 더 많은 유연성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-339">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="55087-340">재작성 규칙 파일이 서버의 지정한 경로에 배포되는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="55087-340">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="55087-341">메서드 기반 규칙</span><span class="sxs-lookup"><span data-stu-id="55087-341">Method-based rule</span></span>

<span data-ttu-id="55087-342">메서드를 이용해서 직접 규칙 로직을 구현하고 싶다면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-342">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="55087-343">`Add`는 메서드에서 사용할 <xref:Microsoft.AspNetCore.Http.HttpContext>를 사용할 수 있게 하는 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-343">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="55087-344">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*)는 추가 파이프라인 처리가 수행되는 방법을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-344">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="55087-345">값을 다음 표에 설명된 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 필드 중 하나로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-345">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="55087-346">작업</span><span class="sxs-lookup"><span data-stu-id="55087-346">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="55087-347">`RuleResult.ContinueRules`(기본값)</span><span class="sxs-lookup"><span data-stu-id="55087-347">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="55087-348">규칙 적용을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-348">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="55087-349">규칙 적용을 중지하고 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="55087-349">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="55087-350">규칙 적용을 중지하고 컨텍스트를 다음 미들웨어로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="55087-350">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="55087-351">예제 응용 프로그램은 *.xml*로 끝나는 경로 요청을 리디렉션하는 메서드를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="55087-351">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="55087-352">`/file.xml`에 대한 요청이 수행되면 해당 요청이 `/xmlfiles/file.xml`로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-352">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="55087-353">상태 코드는 *301 - 영구적으로 이동됨*으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-353">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="55087-354">브라우저에서 */xmlfiles/file.xml*에 대한 새 요청이 수행되면 정적 파일 미들웨어에서 *wwwroot/xmlfiles* 폴더의 파일을 클라이언트에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-354">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="55087-355">리디렉션의 경우 응답의 상태 코드를 명시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-355">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="55087-356">그렇지 않으면 *200 - 정상* 상태 코드가 반환되고 클라이언트에서 리디렉션이 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-356">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="55087-357">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="55087-357">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="55087-358">또한 이 방법은 요청을 다시 작성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-358">This approach can also rewrite requests.</span></span> <span data-ttu-id="55087-359">샘플 앱에서는 *wwwroot* 폴더의 *file.txt* 텍스트 파일을 제공할 텍스트 파일 요청의 경로를 다시 작성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="55087-359">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="55087-360">정적 파일 미들웨어는 업데이트된 요청 경로에 따라 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-360">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="55087-361">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="55087-361">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="55087-362">IRule 기반 규칙</span><span class="sxs-lookup"><span data-stu-id="55087-362">IRule-based rule</span></span>

<span data-ttu-id="55087-363"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하여 <xref:Microsoft.AspNetCore.Rewrite.IRule> 인터페이스를 구현하는 클래스에서 규칙 논리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-363">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="55087-364">`IRule`은 메서드 기반 규칙 방식을 사용하는 것보다 더 높은 유연성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-364">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="55087-365">구현 클래스에는 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 메서드에 대한 매개 변수를 전달할 수 있는 생성자가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55087-365">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="55087-366">예제 응용 프로그램은 `extension` 및 `newPath` 매개 변수 값들이 다양한 조건을 만족하는지 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-366">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="55087-367">`extension`매개 변수는 값을 포함하고 있어야 하고, 그 값은 *.png*, *.jpg*, 또는 *.gif* 중 하나이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="55087-367">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="55087-368">만약 `newPath`가 유효하지 않으면 <xref:System.ArgumentException>이 던져집니다.</span><span class="sxs-lookup"><span data-stu-id="55087-368">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="55087-369">*image.png*에 대한 요청이 수행되면 해당 요청이 `/png-images/image.png`으로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-369">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="55087-370">*image.jpg*에 대한 요청이 수행되면 해당 요청이 `/jpg-images/image.jpg`로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-370">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="55087-371">상태 코드는 *301 - 영구적으로 이동됨*으로 설정되고, `context.Result`는 규칙 처리를 중지하고 응답을 보내도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="55087-371">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="55087-372">원본 요청: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="55087-372">Original Request: `/image.png`</span></span>

![image.png에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="55087-374">원본 요청: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="55087-374">Original Request: `/image.jpg`</span></span>

![image.jpg에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="55087-376">정규식 예제</span><span class="sxs-lookup"><span data-stu-id="55087-376">Regex examples</span></span>

| <span data-ttu-id="55087-377">Goal</span><span class="sxs-lookup"><span data-stu-id="55087-377">Goal</span></span> | <span data-ttu-id="55087-378">정규식 문자열 및</span><span class="sxs-lookup"><span data-stu-id="55087-378">Regex String &</span></span><br><span data-ttu-id="55087-379">일치 예제</span><span class="sxs-lookup"><span data-stu-id="55087-379">Match Example</span></span> | <span data-ttu-id="55087-380">대체 문자열 및</span><span class="sxs-lookup"><span data-stu-id="55087-380">Replacement String &</span></span><br><span data-ttu-id="55087-381">출력 예제</span><span class="sxs-lookup"><span data-stu-id="55087-381">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="55087-382">경로를 쿼리 문자열로 재작성</span><span class="sxs-lookup"><span data-stu-id="55087-382">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="55087-383">후행 슬래시 제거</span><span class="sxs-lookup"><span data-stu-id="55087-383">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="55087-384">후행 슬래시 적용</span><span class="sxs-lookup"><span data-stu-id="55087-384">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="55087-385">특정 요청 재작성 방지</span><span class="sxs-lookup"><span data-stu-id="55087-385">Avoid rewriting specific requests</span></span> | <span data-ttu-id="55087-386">`^(.*)(?<!\.axd)$` 또는 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="55087-386">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="55087-387">예: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="55087-387">Yes: `/resource.htm`</span></span><br><span data-ttu-id="55087-388">아니요: `/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="55087-388">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="55087-389">URL 세그먼트 재정렬</span><span class="sxs-lookup"><span data-stu-id="55087-389">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="55087-390">URL 세그먼트 대체</span><span class="sxs-lookup"><span data-stu-id="55087-390">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

## <a name="additional-resources"></a><span data-ttu-id="55087-391">추가 자료</span><span class="sxs-lookup"><span data-stu-id="55087-391">Additional resources</span></span>

* [<span data-ttu-id="55087-392">애플리케이션 시작</span><span class="sxs-lookup"><span data-stu-id="55087-392">Application Startup</span></span>](startup.md)
* [<span data-ttu-id="55087-393">미들웨어</span><span class="sxs-lookup"><span data-stu-id="55087-393">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="55087-394">.NET에서의 정규식</span><span class="sxs-lookup"><span data-stu-id="55087-394">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="55087-395">정규식 언어 - 빠른 참조</span><span class="sxs-lookup"><span data-stu-id="55087-395">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="55087-396">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="55087-396">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="55087-397">Url 재작성 모듈 2.0 사용(IIS용)</span><span class="sxs-lookup"><span data-stu-id="55087-397">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="55087-398">URL 재작성 모듈 구성 참조</span><span class="sxs-lookup"><span data-stu-id="55087-398">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="55087-399">IIS URL 재작성 모듈 포럼</span><span class="sxs-lookup"><span data-stu-id="55087-399">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="55087-400">간단한 URL 구조 유지</span><span class="sxs-lookup"><span data-stu-id="55087-400">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="55087-401">10가지 URL 재작성 팁과 요령</span><span class="sxs-lookup"><span data-stu-id="55087-401">10 URL Rewriting Tips and Tricks</span></span>](http://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="55087-402">슬래시 여부</span><span class="sxs-lookup"><span data-stu-id="55087-402">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
