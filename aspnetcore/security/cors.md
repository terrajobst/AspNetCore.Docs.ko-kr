---
title: ASP.NET Core에서 CORS (원본 간 요청) 사용
author: rick-anderson
description: ASP.NET Core 앱에서 원본 간 요청을 허용 하거나 거부 하기 위한 표준으로 CORS를 학습 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: 09cc296ebf605907371619124cac00883beb6abb
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511576"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="d35c9-103">ASP.NET Core에서 CORS (원본 간 요청) 사용</span><span class="sxs-lookup"><span data-stu-id="d35c9-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d35c9-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d35c9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d35c9-105">이 문서에서는 ASP.NET Core 앱에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="d35c9-106">브라우저 보안은 웹 페이지에서 웹 페이지를 제공 하는 도메인과 다른 도메인에 대 한 요청을 수행 하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="d35c9-107">이러한 제한을 *동일한 원본 정책*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="d35c9-108">동일한 원본 정책은 악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="d35c9-109">경우에 따라 다른 사이트에서 앱에 대 한 원본 간 요청을 허용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="d35c9-110">자세한 내용은 [MOZILLA CORS 문서](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="d35c9-111">CORS ( [원본 간 리소스 공유](https://www.w3.org/TR/cors/) ):</span><span class="sxs-lookup"><span data-stu-id="d35c9-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="d35c9-112">는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="d35c9-113">는 CORS 완화 security의 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="d35c9-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="d35c9-114">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="d35c9-115">자세한 내용은 [CORS 작동 방식](#how-cors)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="d35c9-116">다른 서버를 거부 하는 동안 서버에서 일부 교차 원본 요청을 명시적으로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="d35c9-117">[JSONP](/dotnet/framework/wcf/samples/jsonp)와 같은 이전 기술 보다 안전 하 고 유연 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="d35c9-118">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d35c9-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="d35c9-119">동일한 원점</span><span class="sxs-lookup"><span data-stu-id="d35c9-119">Same origin</span></span>

<span data-ttu-id="d35c9-120">동일한 스키마, 호스트 및 포트 ([RFC 6454](https://tools.ietf.org/html/rfc6454))가 있는 경우 두 url의 원본이 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="d35c9-121">다음 두 URL은 동일한 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="d35c9-122">이러한 Url의 원본은 이전 두 Url과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="d35c9-123">`https://example.net` &ndash; 다른 도메인</span><span class="sxs-lookup"><span data-stu-id="d35c9-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="d35c9-124">`https://www.example.com/foo.html` &ndash; 다른 하위 도메인</span><span class="sxs-lookup"><span data-stu-id="d35c9-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="d35c9-125">`http://example.com/foo.html` &ndash; 다른 체계</span><span class="sxs-lookup"><span data-stu-id="d35c9-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="d35c9-126">다른 포트 `https://example.com:9000/foo.html` &ndash;</span><span class="sxs-lookup"><span data-stu-id="d35c9-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="d35c9-127">Internet Explorer는 원본을 비교할 때 포트를 고려하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="d35c9-128">명명 된 정책 및 미들웨어를 사용 하는 CORS</span><span class="sxs-lookup"><span data-stu-id="d35c9-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="d35c9-129">CORS 미들웨어는 크로스-원본 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="d35c9-130">다음 코드는 지정 된 원본으로 전체 앱에 대해 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="d35c9-131">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="d35c9-131">The preceding code:</span></span>

* <span data-ttu-id="d35c9-132">정책 이름을 "\_myAllowSpecificOrigins"로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="d35c9-133">정책 이름은 임의로입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="d35c9-134">CORS를 사용 하도록 설정 하는 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 확장 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="d35c9-135">[람다 식](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)으로 <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="d35c9-136">람다는 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 개체를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="d35c9-137">`WithOrigins`등의 [구성 옵션](#cors-policy-options)에 대해서는이 문서의 뒷부분에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="d35c9-138"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> 메서드 호출은 CORS 서비스를 앱의 서비스 컨테이너에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="d35c9-139">자세한 내용은이 문서의 [CORS 정책 옵션](#cpo) (영문)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="d35c9-140"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 메서드는 다음 코드와 같이 메서드를 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="d35c9-141">참고: URL은 후행 슬래시 (`/`)를 포함 **하지** 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="d35c9-142">URL이 `/`로 종료 되는 경우 비교 시 `false` 반환 되 고 헤더가 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a><span data-ttu-id="d35c9-143">모든 끝점에 CORS 정책 적용</span><span class="sxs-lookup"><span data-stu-id="d35c9-143">Apply CORS policies to all endpoints</span></span>

<span data-ttu-id="d35c9-144">다음 코드에서는 CORS 미들웨어를 통해 모든 앱 끝점에 CORS 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-144">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code omitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code omitted.
}
```

> [!WARNING]
> <span data-ttu-id="d35c9-145">끝점 라우팅을 사용 하 여 `UseRouting`에 대 한 호출과 `UseEndpoints`사이에서 실행 되도록 CORS 미들웨어를 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-145">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="d35c9-146">잘못 된 구성으로 인해 미들웨어가 제대로 작동 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-146">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

<span data-ttu-id="d35c9-147">페이지/컨트롤러/동작 수준에서 cors 정책을 적용 하려면 [Razor Pages, 컨트롤러 및 작업 메서드에서 Cors 사용](#ecors) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-147">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="d35c9-148">위의 코드를 테스트 하는 방법에 대 한 지침은 [CORS 테스트](#test) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-148">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="d35c9-149">끝점 라우팅을 사용 하 여 Cors 사용</span><span class="sxs-lookup"><span data-stu-id="d35c9-149">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="d35c9-150">끝점 라우팅을 사용 하면 `RequireCors` 확장 메서드 집합을 사용 하 여 끝점 별로 CORS를 사용 하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-150">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="d35c9-151">마찬가지로 모든 컨트롤러에 대해 CORS를 사용 하도록 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-151">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="d35c9-152">특성과 함께 CORS 사용</span><span class="sxs-lookup"><span data-stu-id="d35c9-152">Enable CORS with attributes</span></span>

<span data-ttu-id="d35c9-153">[&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성은 CORS를 전역적으로 적용 하는 대안을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-153">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="d35c9-154">`[EnableCors]` 특성은 모든 끝점이 아니라 선택한 끝점에 대해 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-154">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="d35c9-155">`[EnableCors]`를 사용 하 여 기본 정책을 지정 하 고 정책을 지정 하 `[EnableCors("{Policy String}")]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-155">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="d35c9-156">`[EnableCors]` 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-156">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="d35c9-157">Razor 페이지 `PageModel`</span><span class="sxs-lookup"><span data-stu-id="d35c9-157">Razor Page `PageModel`</span></span>
* <span data-ttu-id="d35c9-158">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="d35c9-158">Controller</span></span>
* <span data-ttu-id="d35c9-159">컨트롤러 동작 방법</span><span class="sxs-lookup"><span data-stu-id="d35c9-159">Controller action method</span></span>

<span data-ttu-id="d35c9-160">`[EnableCors]` 특성을 사용 하 여 컨트롤러/페이지-모델/작업에 다른 정책을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-160">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="d35c9-161">`[EnableCors]` 특성이 컨트롤러/페이지 모델/동작 메서드에 적용 되 고 CORS가 미들웨어에서 사용 되는 경우 두 정책이 모두 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-161">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="d35c9-162">정책을 결합 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-162">We recommend against combining policies.</span></span> <span data-ttu-id="d35c9-163">동일한 앱에서 `[EnableCors]` 특성 또는 미들웨어를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-163">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="d35c9-164">다음 코드는 각 메서드에 다른 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-164">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="d35c9-165">다음 코드에서는 CORS 기본 정책과 `"AnotherPolicy"`이라는 정책을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-165">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="d35c9-166">CORS 비활성시키기</span><span class="sxs-lookup"><span data-stu-id="d35c9-166">Disable CORS</span></span>

<span data-ttu-id="d35c9-167">[&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성은 컨트롤러/페이지-모델/작업에 대해 CORS를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-167">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="d35c9-168">CORS 정책 옵션</span><span class="sxs-lookup"><span data-stu-id="d35c9-168">CORS policy options</span></span>

<span data-ttu-id="d35c9-169">이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-169">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="d35c9-170">허용 되는 원본 설정</span><span class="sxs-lookup"><span data-stu-id="d35c9-170">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="d35c9-171">허용 되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="d35c9-171">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="d35c9-172">허용 되는 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="d35c9-172">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="d35c9-173">노출 된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="d35c9-173">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="d35c9-174">원본 간 요청에 대 한 자격 증명</span><span class="sxs-lookup"><span data-stu-id="d35c9-174">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="d35c9-175">실행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="d35c9-175">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="d35c9-176"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>은 `Startup.ConfigureServices`에서 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-176"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d35c9-177">일부 옵션의 경우 [CORS 작동 방법](#how-cors) 섹션을 먼저 읽는 것이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-177">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="d35c9-178">허용되는 원본 설정하기</span><span class="sxs-lookup"><span data-stu-id="d35c9-178">Set the allowed origins</span></span>

<span data-ttu-id="d35c9-179"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash;는 모든 원본의 CORS 요청 (`http` 또는 `https`)을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-179"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="d35c9-180">*모든 웹 사이트* 에서 앱에 대 한 원본 간 요청을 수행할 수 있기 때문에 `AllowAnyOrigin`은 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-180">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="d35c9-181">`AllowAnyOrigin` 및 `AllowCredentials` 지정은 안전 하지 않은 구성으로, 사이트 간 요청 위조를 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-181">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="d35c9-182">앱이 두 가지 방법으로 구성 된 경우 CORS 서비스는 잘못 된 CORS 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-182">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="d35c9-183">`AllowAnyOrigin`는 실행 전 요청 및 `Access-Control-Allow-Origin` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-183">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="d35c9-184">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-184">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="d35c9-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash;는 원본이 허용 되는지 평가할 때 구성 된 와일드 카드 도메인과 일치 하는 원본을 허용 하는 함수로 정책의 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="d35c9-186">허용되는 HTTP 메서드 설정하기</span><span class="sxs-lookup"><span data-stu-id="d35c9-186">Set the allowed HTTP methods</span></span>

<span data-ttu-id="d35c9-187"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="d35c9-187"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="d35c9-188">모든 HTTP 메서드를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-188">Allows any HTTP method:</span></span>
* <span data-ttu-id="d35c9-189">실행 전 요청 및 `Access-Control-Allow-Methods` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-189">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="d35c9-190">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-190">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="d35c9-191">허용되는 요청 헤더 설정하기</span><span class="sxs-lookup"><span data-stu-id="d35c9-191">Set the allowed request headers</span></span>

<span data-ttu-id="d35c9-192">*작성자 요청 헤더*라는 CORS 요청에서 특정 헤더를 전송 하도록 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>를 호출 하 고 허용 되는 헤더를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-192">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="d35c9-193">모든 작성자 요청 헤더를 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-193">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="d35c9-194">이 설정은 실행 전 요청 및 `Access-Control-Request-Headers` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-194">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="d35c9-195">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-195">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>


<span data-ttu-id="d35c9-196">`WithHeaders`로 지정 된 특정 헤더와 일치 하는 CORS 미들웨어 정책은 `Access-Control-Request-Headers` 전송 된 헤더가 `WithHeaders`에 명시 된 헤더와 정확히 일치 하는 경우에만 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-196">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="d35c9-197">예를 들어 다음과 같이 구성 된 앱을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-197">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="d35c9-198">CORS 미들웨어는 `Content-Language` ([Headernames](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage))가 `WithHeaders`에 나열 되지 않으므로 다음 요청 헤더를 사용 하 여 실행 전 요청을 거부 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-198">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="d35c9-199">앱은 *200 OK* 응답을 반환 하지만 CORS 헤더는 다시 전송 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-199">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="d35c9-200">따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-200">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="d35c9-201">노출되는 응답 헤더 설정하기</span><span class="sxs-lookup"><span data-stu-id="d35c9-201">Set the exposed response headers</span></span>

<span data-ttu-id="d35c9-202">기본적으로 브라우저는 모든 응답 헤더를 앱에 노출 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-202">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="d35c9-203">자세한 내용은 [W3C 원본 간 리소스 공유 (용어): 단순 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-203">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="d35c9-204">기본적으로 사용 가능한 응답 헤더들은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-204">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="d35c9-205">CORS 사양은 이러한 헤더 *단순 응답 헤더*를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-205">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="d35c9-206">앱에서 다른 헤더를 사용할 수 있도록 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-206">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="d35c9-207">교차 원본 요청의 자격 증명</span><span class="sxs-lookup"><span data-stu-id="d35c9-207">Credentials in cross-origin requests</span></span>

<span data-ttu-id="d35c9-208">CORS 요청에서 자격 증명(Credentials)은 특별한 처리를 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-208">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="d35c9-209">기본적으로 브라우저는 원본 간 요청과 함께 자격 증명을 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-209">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="d35c9-210">자격 증명에는 쿠키 및 HTTP 인증 스키마가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-210">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="d35c9-211">원본 간 요청을 사용 하 여 자격 증명을 보내려면 클라이언트는 `XMLHttpRequest.withCredentials`를 `true`으로 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-211">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="d35c9-212">`XMLHttpRequest` 직접 사용:</span><span class="sxs-lookup"><span data-stu-id="d35c9-212">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="d35c9-213">JQuery 사용:</span><span class="sxs-lookup"><span data-stu-id="d35c9-213">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="d35c9-214">[FETCH API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)사용:</span><span class="sxs-lookup"><span data-stu-id="d35c9-214">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="d35c9-215">서버에서 자격 증명을 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-215">The server must allow the credentials.</span></span> <span data-ttu-id="d35c9-216">원본 간 자격 증명을 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-216">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="d35c9-217">HTTP 응답에는 서버에서 원본 간 요청에 대 한 자격 증명을 허용 하도록 브라우저에 지시 하는 `Access-Control-Allow-Credentials` 헤더가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-217">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="d35c9-218">브라우저에서 자격 증명을 전송 하지만 응답에 유효한 `Access-Control-Allow-Credentials` 헤더가 포함 되어 있지 않으면 브라우저에서 앱에 응답을 노출 하지 않으며 크로스-원본 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-218">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="d35c9-219">크로스-원본 자격 증명을 허용 하는 것은 보안상 위험 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-219">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="d35c9-220">다른 도메인의 웹 사이트는 사용자의 지식 없이 사용자를 대신 하 여 사용자의 앱에 로그인 한 사용자의 자격 증명을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-220">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="d35c9-221">CORS 사양은 `Access-Control-Allow-Credentials` 헤더가 있는 경우 원본 설정 `"*"` (모든 원본)가 잘못 되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-221">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="d35c9-222">실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="d35c9-222">Preflight requests</span></span>

<span data-ttu-id="d35c9-223">일부 CORS 요청의 경우 브라우저는 실제 요청을 만들기 전에 추가 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-223">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="d35c9-224">이 요청을 실행 *전 요청*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-224">This request is called a *preflight request*.</span></span> <span data-ttu-id="d35c9-225">다음 조건에 해당 하는 경우 브라우저에서 실행 전 요청을 건너뛸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-225">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="d35c9-226">요청 메서드는 GET, HEAD 또는 POST입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-226">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="d35c9-227">앱은 `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`또는 `Last-Event-ID`이외의 요청 헤더를 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-227">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="d35c9-228">`Content-Type` 헤더 (설정 된 경우)에는 다음 값 중 하나가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-228">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="d35c9-229">클라이언트 요청에 대해 설정 된 요청 헤더에 대 한 규칙은 앱이 `XMLHttpRequest` 개체에서 `setRequestHeader`를 호출 하 여 설정 하는 헤더에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-229">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="d35c9-230">CORS 사양은 이러한 헤더 *작성자 요청 헤더*를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-230">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="d35c9-231">이 규칙은 브라우저에서 설정할 수 있는 헤더 (예: `User-Agent`, `Host`, `Content-Length`)에는 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-231">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="d35c9-232">다음은 실행 전 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-232">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="d35c9-233">사전 비행 요청은 HTTP OPTIONS 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-233">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="d35c9-234">여기에는 두 가지 특수 헤더가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-234">It includes two special headers:</span></span>

* <span data-ttu-id="d35c9-235">`Access-Control-Request-Method`: 실제 요청에 사용 되는 HTTP 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-235">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="d35c9-236">`Access-Control-Request-Headers`: 앱이 실제 요청에 대해 설정 하는 요청 헤더 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-236">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="d35c9-237">앞에서 설명한 대로 브라우저에서 설정 하는 헤더 (예: `User-Agent`)는 여기에 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-237">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="d35c9-238">CORS 실행 전 요청에는 실제 요청과 함께 전송 되는 헤더를 서버에 나타내는 `Access-Control-Request-Headers` 헤더가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-238">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="d35c9-239">특정 헤더를 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-239">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="d35c9-240">모든 작성자 요청 헤더를 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-240">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="d35c9-241">브라우저는 `Access-Control-Request-Headers`설정 하는 방법에 완전히 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-241">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="d35c9-242">헤더를 `"*"` 이외의 값으로 설정 하거나 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>를 사용 하는 경우 `Accept`, `Content-Type`, `Origin`, 지원 하려는 모든 사용자 지정 헤더를 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-242">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="d35c9-243">다음은 실행 전 요청에 대 한 응답 예제입니다 (서버에서 요청을 허용 한다고 가정).</span><span class="sxs-lookup"><span data-stu-id="d35c9-243">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="d35c9-244">응답에는 허용 된 메서드를 나열 하는 `Access-Control-Allow-Methods` 헤더와 허용 되는 헤더를 나열 하는 `Access-Control-Allow-Headers` 헤더가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-244">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="d35c9-245">실행 전 요청이 성공 하면 브라우저에서 실제 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-245">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="d35c9-246">실행 전 요청이 거부 되 면 앱은 *200 OK* 응답을 반환 하지만 CORS 헤더는 다시 전송 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-246">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="d35c9-247">따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-247">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="d35c9-248">예비 요청 만료 시간 설정하기</span><span class="sxs-lookup"><span data-stu-id="d35c9-248">Set the preflight expiration time</span></span>

<span data-ttu-id="d35c9-249">`Access-Control-Max-Age` 헤더는 실행 전 요청에 대 한 응답을 캐시할 수 있는 기간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-249">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="d35c9-250">이 헤더를 설정 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-250">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="d35c9-251">CORS의 동작 방식</span><span class="sxs-lookup"><span data-stu-id="d35c9-251">How CORS works</span></span>

<span data-ttu-id="d35c9-252">이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 요청에 발생 하는 작업에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-252">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="d35c9-253">CORS는 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="d35c9-253">CORS is **not** a security feature.</span></span> <span data-ttu-id="d35c9-254">CORS는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-254">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="d35c9-255">예를 들어 악의적인 행위자는 사이트에 대 한 [XSS (교차 사이트 스크립팅) 방지](xref:security/cross-site-scripting) 를 사용 하 여 정보를 도용 하기 위해 CORS 지원 사이트에 대 한 교차 사이트 요청을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-255">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="d35c9-256">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-256">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="d35c9-257">CORS를 적용 하는 것은 클라이언트 (브라우저)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-257">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="d35c9-258">서버는 요청을 실행 하 고 응답을 반환 합니다. 클라이언트는 오류를 반환 하 고 응답을 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-258">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="d35c9-259">예를 들어 다음 도구 중 하나는 서버 응답을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-259">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="d35c9-260">Fiddler</span><span class="sxs-lookup"><span data-stu-id="d35c9-260">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="d35c9-261">Postman</span><span class="sxs-lookup"><span data-stu-id="d35c9-261">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="d35c9-262">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="d35c9-262">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="d35c9-263">주소 표시줄에 URL을 입력 하는 웹 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-263">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="d35c9-264">서버에서 브라우저가 원본 간 [Xhr](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 요청을 실행 하도록 허용 하는 방법입니다. 그렇지 않으면이를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-264">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="d35c9-265">브라우저 (CORS 불포함)는 크로스-원본 요청을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-265">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="d35c9-266">CORS 이전에는 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 을 사용 하 여이 제한을 회피 했습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-266">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="d35c9-267">JSONP는 XHR을 사용 하지 않으며 `<script>` 태그를 사용 하 여 응답을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-267">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="d35c9-268">스크립트를 크로스-원본으로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-268">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="d35c9-269">[CORS 사양](https://www.w3.org/TR/cors/) 에는 크로스-원본 요청을 가능 하 게 하는 몇 가지 새로운 HTTP 헤더가 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-269">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="d35c9-270">브라우저에서 CORS를 지 원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-270">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="d35c9-271">CORS를 사용 하도록 설정 하는 데 사용자 지정 JavaScript 코드가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-271">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="d35c9-272">다음은 원본 간 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-272">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="d35c9-273">`Origin` 헤더는 요청을 수행 하는 사이트의 도메인을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-273">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="d35c9-274">`Origin` 헤더는 필수 이며 호스트와 달라 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-274">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="d35c9-275">서버에서 요청을 허용 하는 경우 응답에 `Access-Control-Allow-Origin` 헤더를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-275">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="d35c9-276">이 헤더의 값은 요청의 `Origin` 헤더와 일치 하거나 `"*"`와일드 카드 값입니다. 즉, 모든 원본을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-276">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="d35c9-277">응답에 `Access-Control-Allow-Origin` 헤더가 포함 되어 있지 않으면 크로스-원본 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-277">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="d35c9-278">특히 브라우저에서 요청을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-278">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="d35c9-279">서버에서 성공적인 응답을 반환 하는 경우에도 브라우저에서 응답을 클라이언트 앱에 사용할 수 있도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-279">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="d35c9-280">CORS 테스트</span><span class="sxs-lookup"><span data-stu-id="d35c9-280">Test CORS</span></span>

<span data-ttu-id="d35c9-281">CORS를 테스트 하려면:</span><span class="sxs-lookup"><span data-stu-id="d35c9-281">To test CORS:</span></span>

1. <span data-ttu-id="d35c9-282">[API 프로젝트를 만듭니다](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="d35c9-282">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="d35c9-283">또는 [샘플을 다운로드할](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-283">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="d35c9-284">이 문서의 방법 중 하나를 사용 하 여 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-284">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="d35c9-285">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-285">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="d35c9-286">`WithOrigins("https://localhost:<port>");`은 [다운로드 샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)와 유사한 샘플 앱을 테스트 하는 데만 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-286">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="d35c9-287">웹 앱 프로젝트 (Razor Pages 또는 MVC)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-287">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="d35c9-288">이 샘플에서는 Razor Pages을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-288">The sample uses Razor Pages.</span></span> <span data-ttu-id="d35c9-289">API 프로젝트와 동일한 솔루션에서 웹 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-289">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="d35c9-290">다음 강조 표시 된 코드를 *Index. cshtml* 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-290">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="d35c9-291">위의 코드에서 `url: 'https://<web app>.azurewebsites.net/api/values/1',`을 배포 된 앱의 URL로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-291">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="d35c9-292">API 프로젝트를 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-292">Deploy the API project.</span></span> <span data-ttu-id="d35c9-293">예를 들어 [Azure에 배포할 수](xref:host-and-deploy/azure-apps/index)있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-293">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="d35c9-294">바탕 화면에서 Razor Pages 또는 MVC 앱을 실행 하 고 **테스트** 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-294">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="d35c9-295">F12 도구를 사용 하 여 오류 메시지를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-295">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="d35c9-296">`WithOrigins`에서 localhost 원본을 제거 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-296">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="d35c9-297">또는 다른 포트를 사용 하 여 클라이언트 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-297">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="d35c9-298">예를 들어 Visual Studio에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-298">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="d35c9-299">클라이언트 앱을 사용 하 여 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-299">Test with the client app.</span></span> <span data-ttu-id="d35c9-300">CORS 오류는 오류를 반환 하지만 JavaScript에는 오류 메시지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-300">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="d35c9-301">F12 도구에서 콘솔 탭을 사용 하 여 오류를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-301">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="d35c9-302">브라우저에 따라 다음과 비슷한 오류가 표시 됩니다 (F12 도구 콘솔).</span><span class="sxs-lookup"><span data-stu-id="d35c9-302">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="d35c9-303">Microsoft Edge 사용:</span><span class="sxs-lookup"><span data-stu-id="d35c9-303">Using Microsoft Edge:</span></span>

     <span data-ttu-id="d35c9-304">**SEC7120: [CORS] 원본 `https://localhost:44375`에서 원본 간 리소스에 대 한 액세스 제어-원본 응답 헤더의 `https://localhost:44375`를 찾을 수 없습니다 `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="d35c9-304">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="d35c9-305">Chrome 사용:</span><span class="sxs-lookup"><span data-stu-id="d35c9-305">Using Chrome:</span></span>

     <span data-ttu-id="d35c9-306">**원본 `https://localhost:44375`에서 `https://webapi.azurewebsites.net/api/values/1`의 XMLHttpRequest 액세스가 CORS 정책에 의해 차단 되었습니다. 요청 된 리소스에 ' 액세스 제어-원본 ' 헤더가 없습니다.**</span><span class="sxs-lookup"><span data-stu-id="d35c9-306">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="d35c9-307">CORS 사용 끝점은 [Fiddler](https://www.telerik.com/fiddler) 또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 하 여 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-307">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="d35c9-308">도구를 사용 하는 경우 `Origin` 헤더에 지정 된 요청의 원점은 요청을 받는 호스트와 달라 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-308">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="d35c9-309">요청이 `Origin` 헤더의 값을 기반으로 하는 *교차 원점이* 아닌 경우:</span><span class="sxs-lookup"><span data-stu-id="d35c9-309">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="d35c9-310">CORS 미들웨어가 요청을 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-310">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="d35c9-311">CORS 헤더는 응답에서 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-311">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="d35c9-312">IIS의 CORS</span><span class="sxs-lookup"><span data-stu-id="d35c9-312">CORS in IIS</span></span>

<span data-ttu-id="d35c9-313">IIS에 배포할 때 서버에서 익명 액세스를 허용 하도록 구성 되지 않은 경우 CORS를 Windows 인증 전에 실행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-313">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="d35c9-314">이 시나리오를 지원 하려면 앱에 대해 [IIS CORS 모듈](https://www.iis.net/downloads/microsoft/iis-cors-module) 을 설치 하 고 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-314">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d35c9-315">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="d35c9-315">Additional resources</span></span>

* [<span data-ttu-id="d35c9-316">CORS (원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="d35c9-316">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="d35c9-317">IIS CORS 모듈 시작</span><span class="sxs-lookup"><span data-stu-id="d35c9-317">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d35c9-318">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d35c9-318">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d35c9-319">이 문서에서는 ASP.NET Core 앱에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-319">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="d35c9-320">브라우저 보안은 웹 페이지에서 웹 페이지를 제공 하는 도메인과 다른 도메인에 대 한 요청을 수행 하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-320">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="d35c9-321">이러한 제한을 *동일한 원본 정책*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-321">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="d35c9-322">동일한 원본 정책은 악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-322">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="d35c9-323">경우에 따라 다른 사이트에서 앱에 대 한 원본 간 요청을 허용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-323">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="d35c9-324">자세한 내용은 [MOZILLA CORS 문서](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-324">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="d35c9-325">CORS ( [원본 간 리소스 공유](https://www.w3.org/TR/cors/) ):</span><span class="sxs-lookup"><span data-stu-id="d35c9-325">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="d35c9-326">는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-326">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="d35c9-327">는 CORS 완화 security의 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="d35c9-327">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="d35c9-328">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-328">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="d35c9-329">자세한 내용은 [CORS 작동 방식](#how-cors)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-329">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="d35c9-330">다른 서버를 거부 하는 동안 서버에서 일부 교차 원본 요청을 명시적으로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-330">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="d35c9-331">[JSONP](/dotnet/framework/wcf/samples/jsonp)와 같은 이전 기술 보다 안전 하 고 유연 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-331">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="d35c9-332">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d35c9-332">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="d35c9-333">동일한 원점</span><span class="sxs-lookup"><span data-stu-id="d35c9-333">Same origin</span></span>

<span data-ttu-id="d35c9-334">동일한 스키마, 호스트 및 포트 ([RFC 6454](https://tools.ietf.org/html/rfc6454))가 있는 경우 두 url의 원본이 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-334">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="d35c9-335">다음 두 URL은 동일한 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-335">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="d35c9-336">이러한 Url의 원본은 이전 두 Url과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-336">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="d35c9-337">`https://example.net` &ndash; 다른 도메인</span><span class="sxs-lookup"><span data-stu-id="d35c9-337">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="d35c9-338">`https://www.example.com/foo.html` &ndash; 다른 하위 도메인</span><span class="sxs-lookup"><span data-stu-id="d35c9-338">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="d35c9-339">`http://example.com/foo.html` &ndash; 다른 체계</span><span class="sxs-lookup"><span data-stu-id="d35c9-339">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="d35c9-340">다른 포트 `https://example.com:9000/foo.html` &ndash;</span><span class="sxs-lookup"><span data-stu-id="d35c9-340">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="d35c9-341">Internet Explorer는 원본을 비교할 때 포트를 고려하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-341">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="d35c9-342">명명 된 정책 및 미들웨어를 사용 하는 CORS</span><span class="sxs-lookup"><span data-stu-id="d35c9-342">CORS with named policy and middleware</span></span>

<span data-ttu-id="d35c9-343">CORS 미들웨어는 크로스-원본 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-343">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="d35c9-344">다음 코드는 지정 된 원본으로 전체 앱에 대해 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-344">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="d35c9-345">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="d35c9-345">The preceding code:</span></span>

* <span data-ttu-id="d35c9-346">정책 이름을 "\_myAllowSpecificOrigins"로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-346">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="d35c9-347">정책 이름은 임의로입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-347">The policy name is arbitrary.</span></span>
* <span data-ttu-id="d35c9-348">CORS를 사용 하도록 설정 하는 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 확장 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-348">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="d35c9-349">[람다 식](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)으로 <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-349">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="d35c9-350">람다는 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 개체를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-350">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="d35c9-351">`WithOrigins`등의 [구성 옵션](#cors-policy-options)에 대해서는이 문서의 뒷부분에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-351">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="d35c9-352"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> 메서드 호출은 CORS 서비스를 앱의 서비스 컨테이너에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-352">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="d35c9-353">자세한 내용은이 문서의 [CORS 정책 옵션](#cpo) (영문)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-353">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="d35c9-354"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 메서드는 다음 코드와 같이 메서드를 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-354">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="d35c9-355">참고: URL은 후행 슬래시 (`/`)를 포함 **하지** 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-355">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="d35c9-356">URL이 `/`로 종료 되는 경우 비교 시 `false` 반환 되 고 헤더가 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-356">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="d35c9-357">다음 코드에서는 CORS 미들웨어를 통해 모든 앱 끝점에 CORS 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-357">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="d35c9-358">참고: `UseCors` `UseMvc`전에 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-358">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="d35c9-359">페이지/컨트롤러/동작 수준에서 cors 정책을 적용 하려면 [Razor Pages, 컨트롤러 및 작업 메서드에서 Cors 사용](#ecors) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-359">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="d35c9-360">위의 코드를 테스트 하는 방법에 대 한 지침은 [CORS 테스트](#test) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-360">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="d35c9-361">특성과 함께 CORS 사용</span><span class="sxs-lookup"><span data-stu-id="d35c9-361">Enable CORS with attributes</span></span>

<span data-ttu-id="d35c9-362">[&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성은 CORS를 전역적으로 적용 하는 대안을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-362">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="d35c9-363">`[EnableCors]` 특성은 모든 끝점이 아니라 선택한 끝점에 대해 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-363">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="d35c9-364">`[EnableCors]`를 사용 하 여 기본 정책을 지정 하 고 정책을 지정 하 `[EnableCors("{Policy String}")]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-364">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="d35c9-365">`[EnableCors]` 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-365">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="d35c9-366">Razor 페이지 `PageModel`</span><span class="sxs-lookup"><span data-stu-id="d35c9-366">Razor Page `PageModel`</span></span>
* <span data-ttu-id="d35c9-367">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="d35c9-367">Controller</span></span>
* <span data-ttu-id="d35c9-368">컨트롤러 동작 방법</span><span class="sxs-lookup"><span data-stu-id="d35c9-368">Controller action method</span></span>

<span data-ttu-id="d35c9-369">`[EnableCors]` 특성을 사용 하 여 컨트롤러/페이지-모델/작업에 다른 정책을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-369">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="d35c9-370">`[EnableCors]` 특성이 컨트롤러/페이지 모델/동작 메서드에 적용 되 고 CORS가 미들웨어에서 사용 되는 경우 두 정책이 모두 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-370">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="d35c9-371">정책을 결합 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-371">We recommend against combining policies.</span></span> <span data-ttu-id="d35c9-372">동일한 앱에서 `[EnableCors]` 특성 또는 미들웨어를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-372">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="d35c9-373">다음 코드는 각 메서드에 다른 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-373">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="d35c9-374">다음 코드에서는 CORS 기본 정책과 `"AnotherPolicy"`이라는 정책을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-374">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="d35c9-375">CORS 비활성시키기</span><span class="sxs-lookup"><span data-stu-id="d35c9-375">Disable CORS</span></span>

<span data-ttu-id="d35c9-376">[&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성은 컨트롤러/페이지-모델/작업에 대해 CORS를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-376">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="d35c9-377">CORS 정책 옵션</span><span class="sxs-lookup"><span data-stu-id="d35c9-377">CORS policy options</span></span>

<span data-ttu-id="d35c9-378">이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-378">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="d35c9-379">허용 되는 원본 설정</span><span class="sxs-lookup"><span data-stu-id="d35c9-379">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="d35c9-380">허용 되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="d35c9-380">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="d35c9-381">허용 되는 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="d35c9-381">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="d35c9-382">노출 된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="d35c9-382">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="d35c9-383">원본 간 요청에 대 한 자격 증명</span><span class="sxs-lookup"><span data-stu-id="d35c9-383">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="d35c9-384">실행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="d35c9-384">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="d35c9-385"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>은 `Startup.ConfigureServices`에서 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-385"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d35c9-386">일부 옵션의 경우 [CORS 작동 방법](#how-cors) 섹션을 먼저 읽는 것이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-386">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="d35c9-387">허용되는 원본 설정하기</span><span class="sxs-lookup"><span data-stu-id="d35c9-387">Set the allowed origins</span></span>

<span data-ttu-id="d35c9-388"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash;는 모든 원본의 CORS 요청 (`http` 또는 `https`)을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-388"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="d35c9-389">*모든 웹 사이트* 에서 앱에 대 한 원본 간 요청을 수행할 수 있기 때문에 `AllowAnyOrigin`은 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-389">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="d35c9-390">`AllowAnyOrigin` 및 `AllowCredentials` 지정은 안전 하지 않은 구성으로, 사이트 간 요청 위조를 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-390">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="d35c9-391">보안 응용 프로그램의 경우 클라이언트에서 서버 리소스에 액세스할 수 있는 권한을 부여 해야 하는 경우 정확한 원본 목록을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-391">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="d35c9-392">`AllowAnyOrigin`는 실행 전 요청 및 `Access-Control-Allow-Origin` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-392">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="d35c9-393">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-393">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="d35c9-394"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash;는 원본이 허용 되는지 평가할 때 구성 된 와일드 카드 도메인과 일치 하는 원본을 허용 하는 함수로 정책의 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-394"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="d35c9-395">허용되는 HTTP 메서드 설정하기</span><span class="sxs-lookup"><span data-stu-id="d35c9-395">Set the allowed HTTP methods</span></span>

<span data-ttu-id="d35c9-396"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="d35c9-396"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="d35c9-397">모든 HTTP 메서드를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-397">Allows any HTTP method:</span></span>
* <span data-ttu-id="d35c9-398">실행 전 요청 및 `Access-Control-Allow-Methods` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-398">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="d35c9-399">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-399">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="d35c9-400">허용되는 요청 헤더 설정하기</span><span class="sxs-lookup"><span data-stu-id="d35c9-400">Set the allowed request headers</span></span>

<span data-ttu-id="d35c9-401">*작성자 요청 헤더*라는 CORS 요청에서 특정 헤더를 전송 하도록 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>를 호출 하 고 허용 되는 헤더를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-401">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="d35c9-402">모든 작성자 요청 헤더를 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-402">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="d35c9-403">이 설정은 실행 전 요청 및 `Access-Control-Request-Headers` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-403">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="d35c9-404">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-404">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="d35c9-405">CORS 미들웨어는 항상 `Access-Control-Request-Headers`에서 4 개의 헤더를 사용 하 여 CorsPolicy 헤더에 구성 된 값에 관계 없이 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-405">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="d35c9-406">이 헤더 목록에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-406">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="d35c9-407">예를 들어 다음과 같이 구성 된 앱을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-407">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="d35c9-408">`Content-Language`는 항상 허용 목록 이므로 CORS 미들웨어는 다음 요청 헤더를 사용 하 여 실행 전 요청에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-408">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="d35c9-409">노출되는 응답 헤더 설정하기</span><span class="sxs-lookup"><span data-stu-id="d35c9-409">Set the exposed response headers</span></span>

<span data-ttu-id="d35c9-410">기본적으로 브라우저는 모든 응답 헤더를 앱에 노출 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-410">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="d35c9-411">자세한 내용은 [W3C 원본 간 리소스 공유 (용어): 단순 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d35c9-411">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="d35c9-412">기본적으로 사용 가능한 응답 헤더들은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-412">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="d35c9-413">CORS 사양은 이러한 헤더 *단순 응답 헤더*를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-413">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="d35c9-414">앱에서 다른 헤더를 사용할 수 있도록 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-414">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="d35c9-415">교차 원본 요청의 자격 증명</span><span class="sxs-lookup"><span data-stu-id="d35c9-415">Credentials in cross-origin requests</span></span>

<span data-ttu-id="d35c9-416">CORS 요청에서 자격 증명(Credentials)은 특별한 처리를 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-416">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="d35c9-417">기본적으로 브라우저는 원본 간 요청과 함께 자격 증명을 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-417">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="d35c9-418">자격 증명에는 쿠키 및 HTTP 인증 스키마가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-418">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="d35c9-419">원본 간 요청을 사용 하 여 자격 증명을 보내려면 클라이언트는 `XMLHttpRequest.withCredentials`를 `true`으로 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-419">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="d35c9-420">`XMLHttpRequest` 직접 사용:</span><span class="sxs-lookup"><span data-stu-id="d35c9-420">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="d35c9-421">JQuery 사용:</span><span class="sxs-lookup"><span data-stu-id="d35c9-421">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="d35c9-422">[FETCH API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)사용:</span><span class="sxs-lookup"><span data-stu-id="d35c9-422">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="d35c9-423">서버에서 자격 증명을 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-423">The server must allow the credentials.</span></span> <span data-ttu-id="d35c9-424">원본 간 자격 증명을 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-424">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="d35c9-425">HTTP 응답에는 서버에서 원본 간 요청에 대 한 자격 증명을 허용 하도록 브라우저에 지시 하는 `Access-Control-Allow-Credentials` 헤더가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-425">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="d35c9-426">브라우저에서 자격 증명을 전송 하지만 응답에 유효한 `Access-Control-Allow-Credentials` 헤더가 포함 되어 있지 않으면 브라우저에서 앱에 응답을 노출 하지 않으며 크로스-원본 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-426">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="d35c9-427">크로스-원본 자격 증명을 허용 하는 것은 보안상 위험 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-427">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="d35c9-428">다른 도메인의 웹 사이트는 사용자의 지식 없이 사용자를 대신 하 여 사용자의 앱에 로그인 한 사용자의 자격 증명을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-428">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="d35c9-429">CORS 사양은 `Access-Control-Allow-Credentials` 헤더가 있는 경우 원본 설정 `"*"` (모든 원본)가 잘못 되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-429">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="d35c9-430">실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="d35c9-430">Preflight requests</span></span>

<span data-ttu-id="d35c9-431">일부 CORS 요청의 경우 브라우저는 실제 요청을 만들기 전에 추가 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-431">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="d35c9-432">이 요청을 실행 *전 요청*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-432">This request is called a *preflight request*.</span></span> <span data-ttu-id="d35c9-433">다음 조건에 해당 하는 경우 브라우저에서 실행 전 요청을 건너뛸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-433">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="d35c9-434">요청 메서드는 GET, HEAD 또는 POST입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-434">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="d35c9-435">앱은 `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`또는 `Last-Event-ID`이외의 요청 헤더를 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-435">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="d35c9-436">`Content-Type` 헤더 (설정 된 경우)에는 다음 값 중 하나가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-436">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="d35c9-437">클라이언트 요청에 대해 설정 된 요청 헤더에 대 한 규칙은 앱이 `XMLHttpRequest` 개체에서 `setRequestHeader`를 호출 하 여 설정 하는 헤더에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-437">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="d35c9-438">CORS 사양은 이러한 헤더 *작성자 요청 헤더*를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-438">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="d35c9-439">이 규칙은 브라우저에서 설정할 수 있는 헤더 (예: `User-Agent`, `Host`, `Content-Length`)에는 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-439">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="d35c9-440">다음은 실행 전 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-440">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="d35c9-441">사전 비행 요청은 HTTP OPTIONS 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-441">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="d35c9-442">여기에는 두 가지 특수 헤더가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-442">It includes two special headers:</span></span>

* <span data-ttu-id="d35c9-443">`Access-Control-Request-Method`: 실제 요청에 사용 되는 HTTP 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-443">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="d35c9-444">`Access-Control-Request-Headers`: 앱이 실제 요청에 대해 설정 하는 요청 헤더 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-444">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="d35c9-445">앞에서 설명한 대로 브라우저에서 설정 하는 헤더 (예: `User-Agent`)는 여기에 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-445">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="d35c9-446">CORS 실행 전 요청에는 실제 요청과 함께 전송 되는 헤더를 서버에 나타내는 `Access-Control-Request-Headers` 헤더가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-446">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="d35c9-447">특정 헤더를 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-447">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="d35c9-448">모든 작성자 요청 헤더를 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-448">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="d35c9-449">브라우저는 `Access-Control-Request-Headers`설정 하는 방법에 완전히 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-449">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="d35c9-450">헤더를 `"*"` 이외의 값으로 설정 하거나 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>를 사용 하는 경우 `Accept`, `Content-Type`, `Origin`, 지원 하려는 모든 사용자 지정 헤더를 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-450">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="d35c9-451">다음은 실행 전 요청에 대 한 응답 예제입니다 (서버에서 요청을 허용 한다고 가정).</span><span class="sxs-lookup"><span data-stu-id="d35c9-451">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="d35c9-452">응답에는 허용 된 메서드를 나열 하는 `Access-Control-Allow-Methods` 헤더와 허용 되는 헤더를 나열 하는 `Access-Control-Allow-Headers` 헤더가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-452">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="d35c9-453">실행 전 요청이 성공 하면 브라우저에서 실제 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-453">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="d35c9-454">실행 전 요청이 거부 되 면 앱은 *200 OK* 응답을 반환 하지만 CORS 헤더는 다시 전송 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-454">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="d35c9-455">따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-455">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="d35c9-456">예비 요청 만료 시간 설정하기</span><span class="sxs-lookup"><span data-stu-id="d35c9-456">Set the preflight expiration time</span></span>

<span data-ttu-id="d35c9-457">`Access-Control-Max-Age` 헤더는 실행 전 요청에 대 한 응답을 캐시할 수 있는 기간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-457">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="d35c9-458">이 헤더를 설정 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-458">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="d35c9-459">CORS의 동작 방식</span><span class="sxs-lookup"><span data-stu-id="d35c9-459">How CORS works</span></span>

<span data-ttu-id="d35c9-460">이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 요청에 발생 하는 작업에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-460">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="d35c9-461">CORS는 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="d35c9-461">CORS is **not** a security feature.</span></span> <span data-ttu-id="d35c9-462">CORS는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-462">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="d35c9-463">예를 들어 악의적인 행위자는 사이트에 대 한 [XSS (교차 사이트 스크립팅) 방지](xref:security/cross-site-scripting) 를 사용 하 여 정보를 도용 하기 위해 CORS 지원 사이트에 대 한 교차 사이트 요청을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-463">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="d35c9-464">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-464">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="d35c9-465">CORS를 적용 하는 것은 클라이언트 (브라우저)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-465">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="d35c9-466">서버는 요청을 실행 하 고 응답을 반환 합니다. 클라이언트는 오류를 반환 하 고 응답을 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-466">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="d35c9-467">예를 들어 다음 도구 중 하나는 서버 응답을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-467">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="d35c9-468">Fiddler</span><span class="sxs-lookup"><span data-stu-id="d35c9-468">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="d35c9-469">Postman</span><span class="sxs-lookup"><span data-stu-id="d35c9-469">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="d35c9-470">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="d35c9-470">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="d35c9-471">주소 표시줄에 URL을 입력 하는 웹 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-471">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="d35c9-472">서버에서 브라우저가 원본 간 [Xhr](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 요청을 실행 하도록 허용 하는 방법입니다. 그렇지 않으면이를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-472">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="d35c9-473">브라우저 (CORS 불포함)는 크로스-원본 요청을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-473">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="d35c9-474">CORS 이전에는 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 을 사용 하 여이 제한을 회피 했습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-474">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="d35c9-475">JSONP는 XHR을 사용 하지 않으며 `<script>` 태그를 사용 하 여 응답을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-475">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="d35c9-476">스크립트를 크로스-원본으로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-476">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="d35c9-477">[CORS 사양](https://www.w3.org/TR/cors/) 에는 크로스-원본 요청을 가능 하 게 하는 몇 가지 새로운 HTTP 헤더가 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-477">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="d35c9-478">브라우저에서 CORS를 지 원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-478">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="d35c9-479">CORS를 사용 하도록 설정 하는 데 사용자 지정 JavaScript 코드가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-479">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="d35c9-480">다음은 원본 간 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-480">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="d35c9-481">`Origin` 헤더는 요청을 수행 하는 사이트의 도메인을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-481">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="d35c9-482">`Origin` 헤더는 필수 이며 호스트와 달라 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-482">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="d35c9-483">서버에서 요청을 허용 하는 경우 응답에 `Access-Control-Allow-Origin` 헤더를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-483">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="d35c9-484">이 헤더의 값은 요청의 `Origin` 헤더와 일치 하거나 `"*"`와일드 카드 값입니다. 즉, 모든 원본을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-484">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="d35c9-485">응답에 `Access-Control-Allow-Origin` 헤더가 포함 되어 있지 않으면 크로스-원본 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-485">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="d35c9-486">특히 브라우저에서 요청을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-486">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="d35c9-487">서버에서 성공적인 응답을 반환 하는 경우에도 브라우저에서 응답을 클라이언트 앱에 사용할 수 있도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-487">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="d35c9-488">CORS 테스트</span><span class="sxs-lookup"><span data-stu-id="d35c9-488">Test CORS</span></span>

<span data-ttu-id="d35c9-489">CORS를 테스트 하려면:</span><span class="sxs-lookup"><span data-stu-id="d35c9-489">To test CORS:</span></span>

1. <span data-ttu-id="d35c9-490">[API 프로젝트를 만듭니다](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="d35c9-490">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="d35c9-491">또는 [샘플을 다운로드할](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-491">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="d35c9-492">이 문서의 방법 중 하나를 사용 하 여 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-492">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="d35c9-493">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-493">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="d35c9-494">`WithOrigins("https://localhost:<port>");`은 [다운로드 샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)와 유사한 샘플 앱을 테스트 하는 데만 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-494">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="d35c9-495">웹 앱 프로젝트 (Razor Pages 또는 MVC)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-495">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="d35c9-496">이 샘플에서는 Razor Pages을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-496">The sample uses Razor Pages.</span></span> <span data-ttu-id="d35c9-497">API 프로젝트와 동일한 솔루션에서 웹 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-497">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="d35c9-498">다음 강조 표시 된 코드를 *Index. cshtml* 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-498">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="d35c9-499">위의 코드에서 `url: 'https://<web app>.azurewebsites.net/api/values/1',`을 배포 된 앱의 URL로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-499">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="d35c9-500">API 프로젝트를 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-500">Deploy the API project.</span></span> <span data-ttu-id="d35c9-501">예를 들어 [Azure에 배포할 수](xref:host-and-deploy/azure-apps/index)있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-501">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="d35c9-502">바탕 화면에서 Razor Pages 또는 MVC 앱을 실행 하 고 **테스트** 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-502">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="d35c9-503">F12 도구를 사용 하 여 오류 메시지를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-503">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="d35c9-504">`WithOrigins`에서 localhost 원본을 제거 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-504">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="d35c9-505">또는 다른 포트를 사용 하 여 클라이언트 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-505">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="d35c9-506">예를 들어 Visual Studio에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-506">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="d35c9-507">클라이언트 앱을 사용 하 여 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-507">Test with the client app.</span></span> <span data-ttu-id="d35c9-508">CORS 오류는 오류를 반환 하지만 JavaScript에는 오류 메시지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-508">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="d35c9-509">F12 도구에서 콘솔 탭을 사용 하 여 오류를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-509">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="d35c9-510">브라우저에 따라 다음과 비슷한 오류가 표시 됩니다 (F12 도구 콘솔).</span><span class="sxs-lookup"><span data-stu-id="d35c9-510">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="d35c9-511">Microsoft Edge 사용:</span><span class="sxs-lookup"><span data-stu-id="d35c9-511">Using Microsoft Edge:</span></span>

     <span data-ttu-id="d35c9-512">**SEC7120: [CORS] 원본 `https://localhost:44375`에서 원본 간 리소스에 대 한 액세스 제어-원본 응답 헤더의 `https://localhost:44375`를 찾을 수 없습니다 `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="d35c9-512">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="d35c9-513">Chrome 사용:</span><span class="sxs-lookup"><span data-stu-id="d35c9-513">Using Chrome:</span></span>

     <span data-ttu-id="d35c9-514">**원본 `https://localhost:44375`에서 `https://webapi.azurewebsites.net/api/values/1`의 XMLHttpRequest 액세스가 CORS 정책에 의해 차단 되었습니다. 요청 된 리소스에 ' 액세스 제어-원본 ' 헤더가 없습니다.**</span><span class="sxs-lookup"><span data-stu-id="d35c9-514">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="d35c9-515">CORS 사용 끝점은 [Fiddler](https://www.telerik.com/fiddler) 또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 하 여 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-515">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="d35c9-516">도구를 사용 하는 경우 `Origin` 헤더에 지정 된 요청의 원점은 요청을 받는 호스트와 달라 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-516">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="d35c9-517">요청이 `Origin` 헤더의 값을 기반으로 하는 *교차 원점이* 아닌 경우:</span><span class="sxs-lookup"><span data-stu-id="d35c9-517">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="d35c9-518">CORS 미들웨어가 요청을 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-518">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="d35c9-519">CORS 헤더는 응답에서 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-519">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="d35c9-520">IIS의 CORS</span><span class="sxs-lookup"><span data-stu-id="d35c9-520">CORS in IIS</span></span>

<span data-ttu-id="d35c9-521">IIS에 배포할 때 서버에서 익명 액세스를 허용 하도록 구성 되지 않은 경우 CORS를 Windows 인증 전에 실행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-521">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="d35c9-522">이 시나리오를 지원 하려면 앱에 대해 [IIS CORS 모듈](https://www.iis.net/downloads/microsoft/iis-cors-module) 을 설치 하 고 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d35c9-522">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d35c9-523">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="d35c9-523">Additional resources</span></span>

* [<span data-ttu-id="d35c9-524">CORS (원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="d35c9-524">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="d35c9-525">IIS CORS 모듈 시작</span><span class="sxs-lookup"><span data-stu-id="d35c9-525">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end