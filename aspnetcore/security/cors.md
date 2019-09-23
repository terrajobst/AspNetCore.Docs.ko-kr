---
title: ASP.NET Core에서 CORS (원본 간 요청) 사용
author: rick-anderson
description: ASP.NET Core 앱에서 원본 간 요청을 허용 하거나 거부 하기 위한 표준으로 CORS를 학습 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: security/cors
ms.openlocfilehash: a02b3497684979c1a9e792437f9f1a4c467600f0
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187260"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="8cd18-103">ASP.NET Core에서 CORS (원본 간 요청) 사용</span><span class="sxs-lookup"><span data-stu-id="8cd18-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="8cd18-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8cd18-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8cd18-105">이 문서에서는 ASP.NET Core 앱에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="8cd18-106">브라우저 보안은 웹 페이지에서 웹 페이지를 제공 하는 도메인과 다른 도메인에 대 한 요청을 수행 하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="8cd18-107">이러한 제한을 *동일한 원본 정책*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="8cd18-108">동일한 원본 정책은 악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="8cd18-109">경우에 따라 다른 사이트에서 앱에 대 한 원본 간 요청을 허용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="8cd18-110">자세한 내용은 [MOZILLA CORS 문서](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8cd18-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="8cd18-111">[원본 간 리소스 공유](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="8cd18-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="8cd18-112">는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="8cd18-113">는 CORS 완화 security의 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="8cd18-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="8cd18-114">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="8cd18-115">자세한 내용은 [CORS 작동 방식](#how-cors)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8cd18-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="8cd18-116">다른 서버를 거부 하는 동안 서버에서 일부 교차 원본 요청을 명시적으로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="8cd18-117">[JSONP](/dotnet/framework/wcf/samples/jsonp)와 같은 이전 기술 보다 안전 하 고 유연 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="8cd18-118">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8cd18-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="8cd18-119">동일한 원점</span><span class="sxs-lookup"><span data-stu-id="8cd18-119">Same origin</span></span>

<span data-ttu-id="8cd18-120">동일한 스키마, 호스트 및 포트 ([RFC 6454](https://tools.ietf.org/html/rfc6454))가 있는 경우 두 url의 원본이 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="8cd18-121">다음 두 URL은 동일한 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="8cd18-122">이러한 Url의 원본은 이전 두 Url과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="8cd18-123">`https://example.net`&ndash; 다른 도메인</span><span class="sxs-lookup"><span data-stu-id="8cd18-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="8cd18-124">`https://www.example.com/foo.html`&ndash; 다른 하위 도메인</span><span class="sxs-lookup"><span data-stu-id="8cd18-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="8cd18-125">`http://example.com/foo.html`&ndash; 다른 체계</span><span class="sxs-lookup"><span data-stu-id="8cd18-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="8cd18-126">`https://example.com:9000/foo.html`&ndash; 다른 포트</span><span class="sxs-lookup"><span data-stu-id="8cd18-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="8cd18-127">Internet Explorer는 원본을 비교할 때 포트를 고려하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="8cd18-128">명명 된 정책 및 미들웨어를 사용 하는 CORS</span><span class="sxs-lookup"><span data-stu-id="8cd18-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="8cd18-129">CORS 미들웨어는 크로스-원본 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="8cd18-130">다음 코드는 지정 된 원본으로 전체 앱에 대해 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="8cd18-131">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="8cd18-131">The preceding code:</span></span>

* <span data-ttu-id="8cd18-132">정책 이름을 "\_myAllowSpecificOrigins"로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="8cd18-133">정책 이름은 임의로입니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="8cd18-134">CORS를 사용 하도록 설정 하는 확장메서드를호출합니다.<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*></span><span class="sxs-lookup"><span data-stu-id="8cd18-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="8cd18-135"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [람다 식을](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)사용 하 여를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="8cd18-136">람다는 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 개체를 전달받습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="8cd18-137">등의 [구성 옵션](#cors-policy-options) `WithOrigins`에 대해서는이 문서의 뒷부분에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="8cd18-138"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> 메서드 호출은 CORS 서비스를 앱의 서비스 컨테이너에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="8cd18-139">자세한 내용은이 문서의 [CORS 정책 옵션](#cpo) (영문)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8cd18-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="8cd18-140">메서드 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 는 다음 코드와 같이 메서드를 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="8cd18-141">참고: URL은 후행 슬래시 (`/`)를 포함 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="8cd18-142">URL이로 `/`종료 되 면 비교가 반환 `false` 되 고 헤더는 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8cd18-143">다음 코드에서는 CORS 미들웨어를 통해 모든 앱 끝점에 CORS 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-143">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code ommitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code ommited.
}
```

> [!WARNING]
> <span data-ttu-id="8cd18-144">끝점 라우팅을 사용 하 여 및에 대 `UseRouting` 한 `UseEndpoints`호출 사이에 CORS 미들웨어를 실행 하도록 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-144">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="8cd18-145">잘못 된 구성으로 인해 미들웨어가 제대로 작동 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-145">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
<span data-ttu-id="8cd18-146">다음 코드에서는 CORS 미들웨어를 통해 모든 앱 끝점에 CORS 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-146">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="8cd18-147">참고: `UseCors` 이전 `UseMvc`에를 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-147">Note: `UseCors` must be called before `UseMvc`.</span></span>

::: moniker-end

<span data-ttu-id="8cd18-148">페이지/컨트롤러/동작 수준에서 cors 정책을 적용 하려면 [Razor Pages, 컨트롤러 및 작업 메서드에서 Cors 사용](#ecors) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8cd18-148">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="8cd18-149">위의 코드를 테스트 하는 방법에 대 한 지침은 [CORS 테스트](#test) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8cd18-149">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="8cd18-150">끝점 라우팅을 사용 하 여 Cors 사용</span><span class="sxs-lookup"><span data-stu-id="8cd18-150">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="8cd18-151">끝점 라우팅을 사용 하면 확장 메서드 `RequireCors` 집합을 사용 하 여 끝점 별로 CORS를 사용 하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-151">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="8cd18-152">마찬가지로 모든 컨트롤러에 대해 CORS를 사용 하도록 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-152">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="8cd18-153">특성과 함께 CORS 사용</span><span class="sxs-lookup"><span data-stu-id="8cd18-153">Enable CORS with attributes</span></span>

<span data-ttu-id="8cd18-154">EnableCors 특성은 [ &lbrack;CORS를 전역적으로 적용 하는 대안을 제공 합니다.&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="8cd18-154">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="8cd18-155">특성 `[EnableCors]` 은 모든 끝점이 아니라 선택한 끝점에 대해 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-155">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="8cd18-156">기본 `[EnableCors]` 정책을 지정 하 고 `[EnableCors("{Policy String}")]` 정책을 지정 하려면를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-156">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="8cd18-157">특성 `[EnableCors]` 은 다음에 적용 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-157">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="8cd18-158">Razor 페이지`PageModel`</span><span class="sxs-lookup"><span data-stu-id="8cd18-158">Razor Page `PageModel`</span></span>
* <span data-ttu-id="8cd18-159">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="8cd18-159">Controller</span></span>
* <span data-ttu-id="8cd18-160">컨트롤러 동작 방법</span><span class="sxs-lookup"><span data-stu-id="8cd18-160">Controller action method</span></span>

<span data-ttu-id="8cd18-161">`[EnableCors]` 특성을 사용 하 여 컨트롤러/페이지-모델/작업에 다른 정책을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-161">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="8cd18-162">`[EnableCors]` 특성이 컨트롤러/페이지 모델/동작 메서드에 적용 되 고 CORS가 미들웨어에서 사용 하도록 설정 된 경우 두 정책이 모두 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-162">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="8cd18-163">정책을 결합 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-163">We recommend against combining policies.</span></span> <span data-ttu-id="8cd18-164">동일한 앱에서 특성 또는 미들웨어를 사용 합니다. `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="8cd18-164">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="8cd18-165">다음 코드는 각 메서드에 다른 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-165">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="8cd18-166">다음 코드에서는 CORS 기본 정책과 라는 `"AnotherPolicy"`정책을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-166">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="8cd18-167">CORS 비활성시키기</span><span class="sxs-lookup"><span data-stu-id="8cd18-167">Disable CORS</span></span>

<span data-ttu-id="8cd18-168">DisableCors 특성은 [ &lbrack;컨트롤러/페이지-모델/작업에 대해 CORS를 사용 하지 않도록 설정 합니다.&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="8cd18-168">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="8cd18-169">CORS 정책 옵션</span><span class="sxs-lookup"><span data-stu-id="8cd18-169">CORS policy options</span></span>

<span data-ttu-id="8cd18-170">이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-170">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="8cd18-171">허용되는 원본 설정하기</span><span class="sxs-lookup"><span data-stu-id="8cd18-171">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="8cd18-172">허용되는 HTTP 메서드 설정하기</span><span class="sxs-lookup"><span data-stu-id="8cd18-172">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="8cd18-173">허용되는 요청 헤더 설정하기</span><span class="sxs-lookup"><span data-stu-id="8cd18-173">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="8cd18-174">노출되는 응답 헤더 설정하기</span><span class="sxs-lookup"><span data-stu-id="8cd18-174">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="8cd18-175">교차 원본 요청의 자격 증명</span><span class="sxs-lookup"><span data-stu-id="8cd18-175">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="8cd18-176">예비 요청 만료 시간 설정하기</span><span class="sxs-lookup"><span data-stu-id="8cd18-176">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="8cd18-177"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>는에서 `Startup.ConfigureServices`호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-177"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8cd18-178">일부 옵션의 경우 [CORS 작동 방법](#how-cors) 섹션을 먼저 읽는 것이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-178">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="8cd18-179">허용되는 원본 설정하기</span><span class="sxs-lookup"><span data-stu-id="8cd18-179">Set the allowed origins</span></span>

<span data-ttu-id="8cd18-180"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>모든 원본에서 임의의 스키마 (`http` 또는 `https`)로 CORS 요청을 허용 합니다. &ndash;</span><span class="sxs-lookup"><span data-stu-id="8cd18-180"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="8cd18-181">`AllowAnyOrigin`*모든 웹 사이트* 에서 앱에 대 한 원본 간 요청을 수행할 수 있기 때문에은 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-181">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="8cd18-182">`AllowAnyOrigin` 및`AllowCredentials` 지정은 안전 하지 않은 구성으로, 사이트 간 요청 위조를 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-182">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="8cd18-183">앱이 두 가지 방법으로 구성 된 경우 CORS 서비스는 잘못 된 CORS 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-183">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="8cd18-184">`AllowAnyOrigin` 및`AllowCredentials` 지정은 안전 하지 않은 구성으로, 사이트 간 요청 위조를 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-184">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="8cd18-185">보안 응용 프로그램의 경우 클라이언트에서 서버 리소스에 액세스할 수 있는 권한을 부여 해야 하는 경우 정확한 원본 목록을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-185">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<span data-ttu-id="8cd18-186">`AllowAnyOrigin`실행 전 요청 및 `Access-Control-Allow-Origin` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-186">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="8cd18-187">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8cd18-187">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="8cd18-188"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>원본이 허용 되는지 평가할 때 구성 된 와일드 카드 도메인에 대해 원본이 일치 하도록 설정 하는 함수로 정책의 속성을설정합니다.<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> &ndash;</span><span class="sxs-lookup"><span data-stu-id="8cd18-188"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="8cd18-189">허용되는 HTTP 메서드 설정하기</span><span class="sxs-lookup"><span data-stu-id="8cd18-189">Set the allowed HTTP methods</span></span>

<span data-ttu-id="8cd18-190"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="8cd18-190"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="8cd18-191">모든 HTTP 메서드를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-191">Allows any HTTP method:</span></span>
* <span data-ttu-id="8cd18-192">실행 전 요청 및 `Access-Control-Allow-Methods` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-192">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="8cd18-193">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8cd18-193">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="8cd18-194">허용되는 요청 헤더 설정하기</span><span class="sxs-lookup"><span data-stu-id="8cd18-194">Set the allowed request headers</span></span>

<span data-ttu-id="8cd18-195">*작성자 요청 헤더*라는 CORS 요청에서 특정 헤더를 보낼 수 있게 하려면를 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 하 고 허용 되는 헤더를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-195">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="8cd18-196">모든 작성자 요청 헤더를 허용 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-196">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="8cd18-197">이 설정은 실행 전 요청 및 `Access-Control-Request-Headers` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-197">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="8cd18-198">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8cd18-198">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8cd18-199">로 `WithHeaders` 지정 된 특정 헤더와 일치 하는 CORS 미들웨어 정책은에 지정 된 헤더와 정확 `Access-Control-Request-Headers` 하 게 `WithHeaders`일치 하는 경우에만 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-199">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="8cd18-200">예를 들어 다음과 같이 구성 된 앱을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-200">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="8cd18-201">CORS 미들웨어는 다음 요청 헤더 `Content-Language` 를 사용 하 여 `WithHeaders`실행 전 요청을 거부 합니다 ([headernames. contentlanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)).</span><span class="sxs-lookup"><span data-stu-id="8cd18-201">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="8cd18-202">앱은 *200 OK* 응답을 반환 하지만 CORS 헤더는 다시 전송 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-202">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="8cd18-203">따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-203">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="8cd18-204">CORS 미들웨어는 `Access-Control-Request-Headers` 항상에 있는 4 개의 헤더를 corspolicy 헤더에 구성 된 값에 관계 없이 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-204">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="8cd18-205">이 헤더 목록에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-205">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="8cd18-206">예를 들어 다음과 같이 구성 된 앱을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-206">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="8cd18-207">가 항상 허용 목록 이므로 `Content-Language` CORS 미들웨어는 다음 요청 헤더를 사용 하 여 실행 전 요청에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-207">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="8cd18-208">노출되는 응답 헤더 설정하기</span><span class="sxs-lookup"><span data-stu-id="8cd18-208">Set the exposed response headers</span></span>

<span data-ttu-id="8cd18-209">기본적으로 브라우저는 모든 응답 헤더를 앱에 노출 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-209">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="8cd18-210">자세한 내용은 W3C 원본 간 [리소스 공유 (용어)를 참조 하세요. 단순 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)입니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-210">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="8cd18-211">기본적으로 사용 가능한 응답 헤더들은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-211">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="8cd18-212">CORS 사양은 이러한 헤더 *단순 응답 헤더*를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-212">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="8cd18-213">앱에서 다른 헤더를 사용할 수 있도록 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-213">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="8cd18-214">교차 원본 요청의 자격 증명</span><span class="sxs-lookup"><span data-stu-id="8cd18-214">Credentials in cross-origin requests</span></span>

<span data-ttu-id="8cd18-215">CORS 요청에서 자격 증명(Credentials)은 특별한 처리를 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-215">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="8cd18-216">기본적으로 브라우저는 원본 간 요청과 함께 자격 증명을 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-216">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="8cd18-217">자격 증명에는 쿠키 및 HTTP 인증 스키마가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-217">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="8cd18-218">원본 간 요청을 사용 하 여 자격 증명을 보내려면 클라이언트는를 `XMLHttpRequest.withCredentials` 로 `true`설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-218">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="8cd18-219">직접 `XMLHttpRequest` 사용:</span><span class="sxs-lookup"><span data-stu-id="8cd18-219">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="8cd18-220">JQuery 사용:</span><span class="sxs-lookup"><span data-stu-id="8cd18-220">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="8cd18-221">[FETCH API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)사용:</span><span class="sxs-lookup"><span data-stu-id="8cd18-221">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="8cd18-222">서버에서 자격 증명을 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-222">The server must allow the credentials.</span></span> <span data-ttu-id="8cd18-223">원본 간 자격 증명을 허용 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-223">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="8cd18-224">HTTP 응답에는 서버 `Access-Control-Allow-Credentials` 에서 원본 간 요청에 대 한 자격 증명을 허용 하도록 브라우저에 지시 하는 헤더가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-224">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="8cd18-225">브라우저에서 자격 증명을 전송 하지만 응답에 유효한 `Access-Control-Allow-Credentials` 헤더가 포함 되지 않은 경우 브라우저에서 앱에 응답을 노출 하지 않으며 크로스-원본 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-225">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="8cd18-226">크로스-원본 자격 증명을 허용 하는 것은 보안상 위험 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-226">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="8cd18-227">다른 도메인의 웹 사이트는 사용자의 지식 없이 사용자를 대신 하 여 사용자의 앱에 로그인 한 사용자의 자격 증명을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-227">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="8cd18-228">CORS 사양은 `Access-Control-Allow-Credentials` 헤더가 있는 경우 원본으로 설정 ( `"*"` 모든 원본)을 사용할 수 없다는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-228">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="8cd18-229">실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="8cd18-229">Preflight requests</span></span>

<span data-ttu-id="8cd18-230">일부 CORS 요청의 경우 브라우저는 실제 요청을 만들기 전에 추가 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-230">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="8cd18-231">이 요청을 실행 *전 요청*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-231">This request is called a *preflight request*.</span></span> <span data-ttu-id="8cd18-232">다음 조건에 해당 하는 경우 브라우저에서 실행 전 요청을 건너뛸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-232">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="8cd18-233">요청 메서드는 GET, HEAD 또는 POST입니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-233">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="8cd18-234">`Accept`앱은 ,,`Content-Type` `Accept-Language` ,또는`Last-Event-ID`이외의 요청 헤더를 설정 하지 않습니다. `Content-Language`</span><span class="sxs-lookup"><span data-stu-id="8cd18-234">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="8cd18-235">헤더 `Content-Type` (설정 된 경우)에는 다음 값 중 하나가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-235">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="8cd18-236">클라이언트 요청에 대해 설정 된 요청 헤더에 대 한 규칙은 응용 프로그램에서 `setRequestHeader` `XMLHttpRequest` 개체에 대해를 호출 하 여 설정 하는 헤더에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-236">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="8cd18-237">CORS 사양은 이러한 헤더 *작성자 요청 헤더*를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-237">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="8cd18-238">`User-Agent`이 규칙은 브라우저에서 설정할 수 있는 헤더 (예:, `Host`또는 `Content-Length`)에는 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-238">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="8cd18-239">다음은 실행 전 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-239">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="8cd18-240">사전 비행 요청은 HTTP OPTIONS 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-240">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="8cd18-241">여기에는 두 가지 특수 헤더가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-241">It includes two special headers:</span></span>

* <span data-ttu-id="8cd18-242">`Access-Control-Request-Method`: 실제 요청에 사용 되는 HTTP 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-242">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="8cd18-243">`Access-Control-Request-Headers`: 앱이 실제 요청에 대해 설정 하는 요청 헤더 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-243">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="8cd18-244">앞에서 설명한 것 처럼 브라우저에서 설정 하는 헤더 (예: `User-Agent`)는 여기에 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-244">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="8cd18-245">CORS 실행 전 요청에는 `Access-Control-Request-Headers` 헤더가 포함 될 수 있으며,이는 실제 요청과 함께 전송 되는 헤더를 서버에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-245">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="8cd18-246">특정 헤더를 허용 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-246">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="8cd18-247">모든 작성자 요청 헤더를 허용 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-247">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="8cd18-248">브라우저의 설정 `Access-Control-Request-Headers`방법은 완전히 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-248">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="8cd18-249">헤더 `"*"` 를 (또는을 사용 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>하는 경우) 이외의 값으로 설정 하는 경우, 및 `Content-Type`를 포함 `Origin`하 고 지원 하려는 사용자 지정 헤더를 포함 `Accept`해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-249">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="8cd18-250">다음은 실행 전 요청에 대 한 응답 예제입니다 (서버에서 요청을 허용 한다고 가정).</span><span class="sxs-lookup"><span data-stu-id="8cd18-250">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="8cd18-251">응답에는 허용 `Access-Control-Allow-Methods` 된 메서드를 나열 하는 헤더와 허용 `Access-Control-Allow-Headers` 되는 헤더를 나열 하는 헤더 (선택 사항)가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-251">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="8cd18-252">실행 전 요청이 성공 하면 브라우저에서 실제 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-252">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="8cd18-253">실행 전 요청이 거부 되 면 앱은 *200 OK* 응답을 반환 하지만 CORS 헤더는 다시 전송 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-253">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="8cd18-254">따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-254">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="8cd18-255">예비 요청 만료 시간 설정하기</span><span class="sxs-lookup"><span data-stu-id="8cd18-255">Set the preflight expiration time</span></span>

<span data-ttu-id="8cd18-256">헤더 `Access-Control-Max-Age` 는 실행 전 요청에 대 한 응답을 캐시할 수 있는 기간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-256">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="8cd18-257">이 헤더를 설정 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-257">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="8cd18-258">CORS의 동작 방식</span><span class="sxs-lookup"><span data-stu-id="8cd18-258">How CORS works</span></span>

<span data-ttu-id="8cd18-259">이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 요청에 발생 하는 작업에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-259">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="8cd18-260">CORS는 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="8cd18-260">CORS is **not** a security feature.</span></span> <span data-ttu-id="8cd18-261">CORS는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-261">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="8cd18-262">예를 들어 악의적인 행위자는 사이트에 대 한 [XSS (교차 사이트 스크립팅) 방지](xref:security/cross-site-scripting) 를 사용 하 여 정보를 도용 하기 위해 CORS 지원 사이트에 대 한 교차 사이트 요청을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-262">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="8cd18-263">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-263">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="8cd18-264">CORS를 적용 하는 것은 클라이언트 (브라우저)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-264">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="8cd18-265">서버는 요청을 실행 하 고 응답을 반환 합니다. 클라이언트는 오류를 반환 하 고 응답을 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-265">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="8cd18-266">예를 들어 다음 도구 중 하나는 서버 응답을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-266">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="8cd18-267">Fiddler</span><span class="sxs-lookup"><span data-stu-id="8cd18-267">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="8cd18-268">Postman</span><span class="sxs-lookup"><span data-stu-id="8cd18-268">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="8cd18-269">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="8cd18-269">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="8cd18-270">주소 표시줄에 URL을 입력 하는 웹 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-270">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="8cd18-271">서버에서 브라우저가 원본 간 [Xhr](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 요청을 실행 하도록 허용 하는 방법입니다. 그렇지 않으면이를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-271">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="8cd18-272">브라우저 (CORS 불포함)는 크로스-원본 요청을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-272">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="8cd18-273">CORS 이전에는 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 을 사용 하 여이 제한을 회피 했습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-273">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="8cd18-274">JSONP는 xhr을 사용 하지 않으며 `<script>` 태그를 사용 하 여 응답을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-274">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="8cd18-275">스크립트를 크로스-원본으로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-275">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="8cd18-276">[CORS 사양](https://www.w3.org/TR/cors/) 에는 크로스-원본 요청을 가능 하 게 하는 몇 가지 새로운 HTTP 헤더가 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-276">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="8cd18-277">브라우저에서 CORS를 지 원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-277">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="8cd18-278">CORS를 사용 하도록 설정 하는 데 사용자 지정 JavaScript 코드가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-278">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="8cd18-279">다음은 원본 간 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-279">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="8cd18-280">헤더 `Origin` 는 요청을 만드는 사이트의 도메인을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-280">The `Origin` header provides the domain of the site that's making the request:</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="8cd18-281">서버에서 요청을 허용 하는 경우 응답에 `Access-Control-Allow-Origin` 헤더를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-281">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="8cd18-282">이 헤더의 값은 요청의 `Origin` 헤더와 일치 하거나 와일드 카드 값 `"*"`입니다. 즉, 모든 원본이 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-282">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="8cd18-283">응답에 `Access-Control-Allow-Origin` 헤더가 포함 되지 않은 경우 원본 간 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-283">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="8cd18-284">특히 브라우저에서 요청을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-284">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="8cd18-285">서버에서 성공적인 응답을 반환 하는 경우에도 브라우저에서 응답을 클라이언트 앱에 사용할 수 있도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-285">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="8cd18-286">CORS 테스트</span><span class="sxs-lookup"><span data-stu-id="8cd18-286">Test CORS</span></span>

<span data-ttu-id="8cd18-287">CORS를 테스트 하려면:</span><span class="sxs-lookup"><span data-stu-id="8cd18-287">To test CORS:</span></span>

1. <span data-ttu-id="8cd18-288">[API 프로젝트를 만듭니다](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="8cd18-288">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="8cd18-289">또는 [샘플을 다운로드할](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-289">Alternatively, you can [download the sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="8cd18-290">이 문서의 방법 중 하나를 사용 하 여 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-290">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="8cd18-291">예:</span><span class="sxs-lookup"><span data-stu-id="8cd18-291">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="8cd18-292">`WithOrigins("https://localhost:<port>");`[다운로드 샘플 코드](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)와 유사한 샘플 앱을 테스트 하는 데만 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-292">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="8cd18-293">웹 앱 프로젝트 (Razor Pages 또는 MVC)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-293">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="8cd18-294">이 샘플에서는 Razor Pages을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-294">The sample uses Razor Pages.</span></span> <span data-ttu-id="8cd18-295">API 프로젝트와 동일한 솔루션에서 웹 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-295">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="8cd18-296">다음 강조 표시 된 코드를 *Index. cshtml* 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-296">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="8cd18-297">위의 코드에서를 배포 된 `url: 'https://<web app>.azurewebsites.net/api/values/1',` 앱의 URL로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-297">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="8cd18-298">API 프로젝트를 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-298">Deploy the API project.</span></span> <span data-ttu-id="8cd18-299">예를 들어 [Azure에 배포할 수](xref:host-and-deploy/azure-apps/index)있습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-299">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="8cd18-300">바탕 화면에서 Razor Pages 또는 MVC 앱을 실행 하 고 **테스트** 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-300">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="8cd18-301">F12 도구를 사용 하 여 오류 메시지를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-301">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="8cd18-302">에서 `WithOrigins` localhost 원본을 제거 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-302">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="8cd18-303">또는 다른 포트를 사용 하 여 클라이언트 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-303">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="8cd18-304">예를 들어 Visual Studio에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-304">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="8cd18-305">클라이언트 앱을 사용 하 여 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-305">Test with the client app.</span></span> <span data-ttu-id="8cd18-306">CORS 오류는 오류를 반환 하지만 JavaScript에는 오류 메시지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-306">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="8cd18-307">F12 도구에서 콘솔 탭을 사용 하 여 오류를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="8cd18-307">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="8cd18-308">브라우저에 따라 다음과 비슷한 오류가 표시 됩니다 (F12 도구 콘솔).</span><span class="sxs-lookup"><span data-stu-id="8cd18-308">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="8cd18-309">Microsoft Edge 사용:</span><span class="sxs-lookup"><span data-stu-id="8cd18-309">Using Microsoft Edge:</span></span>

     <span data-ttu-id="8cd18-310">**SEC7120: [CORS] 원본 `https://localhost:44375` 에서 원본 간 리소스에 대 한 액세스 제어-원본 응답 헤더를 찾을 `https://localhost:44375` 수 없습니다.`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="8cd18-310">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="8cd18-311">Chrome 사용:</span><span class="sxs-lookup"><span data-stu-id="8cd18-311">Using Chrome:</span></span>

     <span data-ttu-id="8cd18-312">**`https://webapi.azurewebsites.net/api/values/1` 원본`https://localhost:44375` 에서의 XMLHttpRequest에 대 한 액세스가 CORS 정책에 의해 차단 되었습니다. 요청한 리소스에 ' 액세스 제어-원본 ' 헤더가 없습니다.**</span><span class="sxs-lookup"><span data-stu-id="8cd18-312">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8cd18-313">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8cd18-313">Additional resources</span></span>

* [<span data-ttu-id="8cd18-314">CORS (원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="8cd18-314">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
