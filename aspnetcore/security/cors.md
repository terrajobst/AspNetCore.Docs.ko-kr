---
title: ASP.NET Core에서 원본 간 요청 (CORS)를 사용 하도록 설정
author: rick-anderson
description: 에 대해 알아봅니다 어떻게 CORS 허용 하거나 거부 하는 ASP.NET Core 앱에서 크로스-원본 요청에 대 한 표준으로 합니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: security/cors
ms.openlocfilehash: fe5b750c44e5fad9ba80efb2cc8116d0a64b1a17
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068299"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="91c5d-103">ASP.NET Core에서 원본 간 요청 (CORS)를 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="91c5d-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="91c5d-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="91c5d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="91c5d-105">이 문서에서는 ASP.NET Core 앱에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="91c5d-106">브라우저 보안 요청을 웹 페이지를 제공 하는 다른 도메인에서 웹 페이지를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="91c5d-107">이 제한은 라고 합니다 *동일 원본 정책*합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="91c5d-108">동일 원본 정책에서 다른 사이트의 중요 한 데이터를 읽을 악성 사이트를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="91c5d-109">경우에 따라 다음 다른 사이트 앱 크로스-원본 요청을 허용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="91c5d-110">자세한 내용은 참조는 [Mozilla CORS 문서](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="91c5d-111">[원본 간 리소스 공유](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="91c5d-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="91c5d-112">W3C은 동일 원본 정책을 완화 하는 서버를 허용 하는 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="91c5d-113">됩니다 **되지** CORS 보안 기능으로 보안을 완화 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="91c5d-114">API는 CORS 허용 하 여 안전 하 게 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="91c5d-115">자세한 내용은 [CORS 어떻게 작동](#how-cors)합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="91c5d-116">명시적으로 다른 사용자를 거부 하는 동안 일부 원본 간 요청을 허용 하도록 서버를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="91c5d-117">안전 하 고 이전 기술 보다 더 유연한 같은입니다 [JSONP](/dotnet/framework/wcf/samples/jsonp)합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="91c5d-118">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/2.2-stage-samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="91c5d-118">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/2.2-stage-samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="91c5d-119">동일한 원본</span><span class="sxs-lookup"><span data-stu-id="91c5d-119">Same origin</span></span>

<span data-ttu-id="91c5d-120">동일한 구성표, 호스트 및 포트에 있는 경우 두 개의 Url가 동일한 원본 ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="91c5d-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="91c5d-121">다음 두 URL은 동일한 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="91c5d-122">이러한 Url에 이전 두 Url 보다 다양 한 원본:</span><span class="sxs-lookup"><span data-stu-id="91c5d-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="91c5d-123">`https://example.net` &ndash; 다른 도메인</span><span class="sxs-lookup"><span data-stu-id="91c5d-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="91c5d-124">`https://www.example.com/foo.html` &ndash; 다른 하위 도메인</span><span class="sxs-lookup"><span data-stu-id="91c5d-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="91c5d-125">`http://example.com/foo.html` &ndash; 다른 구성표</span><span class="sxs-lookup"><span data-stu-id="91c5d-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="91c5d-126">`https://example.com:9000/foo.html` &ndash; 다른 포트</span><span class="sxs-lookup"><span data-stu-id="91c5d-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="91c5d-127">Internet Explorer는 원본을 비교할 때 포트를 고려하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="91c5d-128">명명 된 정책 및 미들웨어를 사용 하 여 CORS</span><span class="sxs-lookup"><span data-stu-id="91c5d-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="91c5d-129">CORS 미들웨어는 크로스-원본 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="91c5d-130">다음 코드는 지정 된 origin 사용 하 여 전체 앱에 대 한 CORS를 사용 하면:</span><span class="sxs-lookup"><span data-stu-id="91c5d-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="91c5d-131">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="91c5d-131">The preceding code:</span></span>

* <span data-ttu-id="91c5d-132">정책 이름 설정 "\_myAllowSpecificOrigins"입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="91c5d-133">정책 이름은 임의로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="91c5d-134">호출 된 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> CORS를 사용 하도록 설정 하는 확장 메서드.</span><span class="sxs-lookup"><span data-stu-id="91c5d-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="91c5d-135">호출 <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> 사용 하 여는 [람다 식](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="91c5d-136">람다는 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 개체를 전달받습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="91c5d-137">[구성 옵션](#cors-policy-options)와 같은 `WithOrigins`,이 문서의 뒷부분에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="91c5d-138"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> CORS 서비스 앱의 서비스 컨테이너를 추가 하는 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="91c5d-139">자세한 내용은 [CORS 정책 옵션](#cpo) 이 문서의.</span><span class="sxs-lookup"><span data-stu-id="91c5d-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="91c5d-140"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 메서드는 다음 코드와 같이 메서드를 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="91c5d-141">다음 강조 표시 된 코드에는 CORS 미들웨어를 통해 모든 앱 끝점에 CORS 정책 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-141">The following highlighted code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>

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

<span data-ttu-id="91c5d-142">참조 [Razor 페이지, 컨트롤러 및 작업 메서드에서 CORS를 사용 하도록 설정](#ecors) 페이지/컨트롤러/작업 수준에서 CORS 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-142">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="91c5d-143">참고:</span><span class="sxs-lookup"><span data-stu-id="91c5d-143">Note:</span></span>

* <span data-ttu-id="91c5d-144">`UseCors` 먼저 호출 해야 `UseMvc`합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-144">`UseCors` must be called before `UseMvc`.</span></span>
* <span data-ttu-id="91c5d-145">URL은 **되지** 후행 슬래시를 포함 (`/`).</span><span class="sxs-lookup"><span data-stu-id="91c5d-145">The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="91c5d-146">URL을 사용 하 여 종료 되 면 `/`, 비교 반환 `false` 헤더가 없으면 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-146">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="91c5d-147">참조 [테스트 CORS](#test) 앞의 코드를 테스트에 대 한 지침은 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-147">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="91c5d-148">특성을 사용 하 여 CORS를 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="91c5d-148">Enable CORS with attributes</span></span>

<span data-ttu-id="91c5d-149">합니다 [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성을 전역적으로 CORS를 적용 하는 대신 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-149">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="91c5d-150">`[EnableCors]` 특성 모든 끝점 보다는 선택한 끝점에 대 한 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-150">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="91c5d-151">사용 하 여 `[EnableCors]` 기본 정책을 지정 하 고 `[EnableCors("{Policy String}")]` 정책을 지정 하려면.</span><span class="sxs-lookup"><span data-stu-id="91c5d-151">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="91c5d-152">`[EnableCors]` 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-152">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="91c5d-153">Razor 페이지 `PageModel`</span><span class="sxs-lookup"><span data-stu-id="91c5d-153">Razor Page `PageModel`</span></span>
* <span data-ttu-id="91c5d-154">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="91c5d-154">Controller</span></span>
* <span data-ttu-id="91c5d-155">컨트롤러 동작 메서드</span><span class="sxs-lookup"><span data-stu-id="91c5d-155">Controller action method</span></span>

<span data-ttu-id="91c5d-156">컨트롤러/페이지-모델/작업과 다른 정책을 적용할 수는 `[EnableCors]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-156">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="91c5d-157">경우는 `[EnableCors]` 컨트롤러/페이지-모델/작업 메서드에 특성이 적용 되 고 CORS는 미들웨어에서 사용 하도록 설정, 두 정책이 모두 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-157">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="91c5d-158">정책 결합에 대 한 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-158">We recommend against combining policies.</span></span> <span data-ttu-id="91c5d-159">사용 된 `[EnableCors]` 특성 또는 둘 다 동일한 앱에서 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-159">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="91c5d-160">각 메서드에 다음 코드에 다른 정책을 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-160">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="91c5d-161">다음 코드에서는 CORS 기본 정책 및 정책 이라는 `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="91c5d-161">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="91c5d-162">CORS 비활성시키기</span><span class="sxs-lookup"><span data-stu-id="91c5d-162">Disable CORS</span></span>

<span data-ttu-id="91c5d-163">합니다 [ &lbrack;하도록 설정 하려면 DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성 컨트롤러/페이지-모델/작업에 대 한 CORS를 해제 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-163">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="91c5d-164">CORS 정책 옵션</span><span class="sxs-lookup"><span data-stu-id="91c5d-164">CORS policy options</span></span>

<span data-ttu-id="91c5d-165">이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-165">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="91c5d-166">허용되는 원본 설정하기</span><span class="sxs-lookup"><span data-stu-id="91c5d-166">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="91c5d-167">허용되는 HTTP 메서드 설정하기</span><span class="sxs-lookup"><span data-stu-id="91c5d-167">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="91c5d-168">허용되는 요청 헤더 설정하기</span><span class="sxs-lookup"><span data-stu-id="91c5d-168">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="91c5d-169">노출되는 응답 헤더 설정하기</span><span class="sxs-lookup"><span data-stu-id="91c5d-169">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="91c5d-170">교차 원본 요청의 자격 증명</span><span class="sxs-lookup"><span data-stu-id="91c5d-170">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="91c5d-171">예비 요청 만료 시간 설정하기</span><span class="sxs-lookup"><span data-stu-id="91c5d-171">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="91c5d-172"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 라고 `Startup.ConfigureServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-172"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="91c5d-173">일부 옵션에 대 한 읽기 하면 도움이 될 합니다 [CORS 어떻게 작동](#how-cors) 먼저 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-173">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="91c5d-174">허용되는 원본 설정하기</span><span class="sxs-lookup"><span data-stu-id="91c5d-174">Set the allowed origins</span></span>

<span data-ttu-id="91c5d-175"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; 임의의 체계를 사용 하 여 모든 원본에서 CORS 요청할 수 있습니다 (`http` 또는 `https`).</span><span class="sxs-lookup"><span data-stu-id="91c5d-175"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="91c5d-176">`AllowAnyOrigin` 안전 하지 않은 때문에 *웹 사이트* 앱에 크로스-원본 요청을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-176">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="91c5d-177">지정 `AllowAnyOrigin` 고 `AllowCredentials` 는 안전 하지 않은 구성 및 교차 사이트 요청 위조 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-177">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="91c5d-178">CORS 서비스 앱 모두 메서드를 사용 하 여 구성 된 경우 잘못 된 CORS 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-178">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="91c5d-179">지정 `AllowAnyOrigin` 고 `AllowCredentials` 는 안전 하지 않은 구성 및 교차 사이트 요청 위조 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-179">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="91c5d-180">보안 앱의 경우 클라이언트 자체 서버 리소스에 액세스 권한을 부여 해야 하는 경우는 정확한 원본 목록이 올바르면을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-180">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<!-- REVIEW required
I changed from
Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration. **This** setting affects preflight requests and the ...
to
**`AllowAnyOrigin`** affects preflight requests and the

to remove the ambiguous **This**.
-->

<span data-ttu-id="91c5d-181">`AllowAnyOrigin` 영향을 줍니다 요청 실행 전 및 `Access-Control-Allow-Origin` 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-181">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="91c5d-182">자세한 내용은 참조는 [요청을 실행 전](#preflight-requests) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-182">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="91c5d-183"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; 집합의 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 원본 허용 되는 경우 평가할 때 구성 된 와일드 카드 도메인에 맞게 원본을 허용 하는 함수를 정책의 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-183"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-104&highlight=4)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="91c5d-184">허용되는 HTTP 메서드 설정하기</span><span class="sxs-lookup"><span data-stu-id="91c5d-184">Set the allowed HTTP methods</span></span>

<span data-ttu-id="91c5d-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="91c5d-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="91c5d-186">모든 HTTP 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-186">Allows any HTTP method:</span></span>
* <span data-ttu-id="91c5d-187">영향을 줍니다 요청 실행 전 및 `Access-Control-Allow-Methods` 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-187">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="91c5d-188">자세한 내용은 참조는 [요청을 실행 전](#preflight-requests) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-188">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="91c5d-189">허용되는 요청 헤더 설정하기</span><span class="sxs-lookup"><span data-stu-id="91c5d-189">Set the allowed request headers</span></span>

<span data-ttu-id="91c5d-190">특정 헤더를 CORS 요청을 보낼 수 있도록 호출 *요청 헤더를 작성*, 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 허용 되는 헤더를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-190">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="91c5d-191">요청 헤더에 모든 author 수 있도록 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="91c5d-191">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="91c5d-192">이 설정은 실행 전 요청 및 `Access-Control-Request-Headers` 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-192">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="91c5d-193">자세한 내용은 참조는 [요청을 실행 전](#preflight-requests) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-193">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="91c5d-194">지정 된 특정 헤더에 CORS 미들웨어 정책 일치가 `WithHeaders` 은 헤더 전송 하는 경우에 가능 `Access-Control-Request-Headers` 에 명시 된 헤더와 정확히 일치 `WithHeaders`합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-194">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="91c5d-195">예를 들어 다음과 같이 구성 된 앱을 고려해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-195">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="91c5d-196">CORS 미들웨어 때문에 다음 요청 헤더를 사용 하 여 실행 전 요청을 거절 `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage))에 나열 되지 않은 `WithHeaders`:</span><span class="sxs-lookup"><span data-stu-id="91c5d-196">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="91c5d-197">앱 반환을 *200 확인* 응답 하지만 CORS 헤더를 다시 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-197">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="91c5d-198">따라서 브라우저는 크로스-원본 요청을 시도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-198">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="91c5d-199">CORS 미들웨어의 네 가지 헤더를 항상 허용 된 `Access-Control-Request-Headers` CorsPolicy.Headers에 구성 된 값에 관계 없이 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-199">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="91c5d-200">이 헤더 목록에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-200">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="91c5d-201">예를 들어 다음과 같이 구성 된 앱을 고려해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-201">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="91c5d-202">CORS 미들웨어가 성공적으로 응답 다음 요청 헤더를 사용 하 여 실행 전 요청에 있으므로 `Content-Language` 은 항상 허용 목록에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-202">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="91c5d-203">노출되는 응답 헤더 설정하기</span><span class="sxs-lookup"><span data-stu-id="91c5d-203">Set the exposed response headers</span></span>

<span data-ttu-id="91c5d-204">브라우저는 기본적으로 모든 앱에 대 한 응답 헤더를 노출 하지 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-204">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="91c5d-205">자세한 내용은 참조 하세요. [W3C 크로스-원본 자원 공유 (용어): 간단한 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-205">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="91c5d-206">기본적으로 사용 가능한 응답 헤더들은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-206">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="91c5d-207">이러한 헤더를 호출 하는 CORS 사양 *간단한 응답 헤더*합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-207">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="91c5d-208">다른 헤더를 사용할 수 있도록 앱을 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="91c5d-208">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="91c5d-209">교차 원본 요청의 자격 증명</span><span class="sxs-lookup"><span data-stu-id="91c5d-209">Credentials in cross-origin requests</span></span>

<span data-ttu-id="91c5d-210">CORS 요청에서 자격 증명(Credentials)은 특별한 처리를 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-210">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="91c5d-211">기본적으로 브라우저는 크로스-원본 요청을 사용 하 여 자격 증명을 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-211">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="91c5d-212">자격 증명 쿠키 및 HTTP 인증 체계를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-212">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="91c5d-213">크로스-원본 요청을 사용 하 여 자격 증명을 보내려면 클라이언트 설정 해야 합니다 `XMLHttpRequest.withCredentials` 에 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-213">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="91c5d-214">사용 하 여 `XMLHttpRequest` 직접.</span><span class="sxs-lookup"><span data-stu-id="91c5d-214">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="91c5d-215">JQuery를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-215">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="91c5d-216">사용 하 여 [API를 가져올](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="91c5d-216">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="91c5d-217">서버 자격 증명을 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-217">The server must allow the credentials.</span></span> <span data-ttu-id="91c5d-218">크로스-원본 자격 증명을 허용 하려면 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="91c5d-218">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="91c5d-219">HTTP 응답에 포함 됩니다는 `Access-Control-Allow-Credentials` 서버 크로스-원본 요청에 대 한 자격 증명을 허용 하는지 브라우저에 지시 하는 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-219">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="91c5d-220">경우 브라우저는 자격 증명을 보내지만 응답 유효한 없는 `Access-Control-Allow-Credentials` 헤더, 브라우저 앱에 대 한 응답을 제공 하지 않습니다 및 크로스-원본 요청에 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-220">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="91c5d-221">크로스-원본 자격 증명을 허용 하는 것은 보안상 위험 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-221">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="91c5d-222">다른 도메인에서 웹 사이트는 사용자의 지식 없이 사용자를 대신 하 여 앱에는 로그인 한 사용자의 자격 증명을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-222">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="91c5d-223">CORS 사양도 해당 설정에 따라 원본이 `"*"` (모든 원본) 올바르지 않습니다 경우는 `Access-Control-Allow-Credentials` 헤더가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-223">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="91c5d-224">실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="91c5d-224">Preflight requests</span></span>

<span data-ttu-id="91c5d-225">브라우저는 일부 CORS 요청에 대 한 실제 요청 하기 전에 추가 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-225">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="91c5d-226">이 요청에서 호출 되는 *실행 전 요청*합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-226">This request is called a *preflight request*.</span></span> <span data-ttu-id="91c5d-227">다음 조건이 true 인 경우 브라우저가 실행 전 요청을 건너뛸 수 있습니다.:</span><span class="sxs-lookup"><span data-stu-id="91c5d-227">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="91c5d-228">요청 메서드가 GET, HEAD 또는 POST 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-228">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="91c5d-229">앱 이외의 요청 헤더를 설정 하지 않는 `Accept`, `Accept-Language`를 `Content-Language`를 `Content-Type`, 또는 `Last-Event-ID`합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-229">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="91c5d-230">`Content-Type` 헤더 경우 설정에 다음 값 중 하나:</span><span class="sxs-lookup"><span data-stu-id="91c5d-230">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="91c5d-231">호출 하 여 앱을 설정 하는 헤더에 적용 되는 클라이언트 요청에 대 한 요청 헤더에는 규칙 집합 `setRequestHeader` 에 `XMLHttpRequest` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-231">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="91c5d-232">이러한 헤더를 호출 하는 CORS 사양 *요청 헤더를 작성*합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-232">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="91c5d-233">헤더와 같은 브라우저 설정 수에 규칙 적용 되지 않습니다 `User-Agent`하십시오 `Host`, 또는 `Content-Length`합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-233">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="91c5d-234">다음은 실행 전 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-234">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="91c5d-235">HTTP OPTIONS 메서드를 사용 하는 사전 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-235">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="91c5d-236">두 가지 특수 헤더를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-236">It includes two special headers:</span></span>

* <span data-ttu-id="91c5d-237">`Access-Control-Request-Method`: 실제 요청에 사용 될 HTTP 메서드.</span><span class="sxs-lookup"><span data-stu-id="91c5d-237">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="91c5d-238">`Access-Control-Request-Headers`: 실제 요청에서 앱을 설정 하는 요청 헤더의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-238">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="91c5d-239">여기와 같은 브라우저 설정 하는 헤더가 포함 되지 않습니다 앞에서 설명한 대로 `User-Agent`입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-239">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="91c5d-240">CORS 실행 전 요청에 포함 될 수 있습니다는 `Access-Control-Request-Headers` 헤더로 실제 요청과 함께 전송 되는 헤더를 서버에 알립니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-240">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="91c5d-241">특정 헤더를 허용 하려면 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="91c5d-241">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="91c5d-242">요청 헤더에 모든 author 수 있도록 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="91c5d-242">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="91c5d-243">브라우저 설정 하는 방법에 전적으로 일관 되지 `Access-Control-Request-Headers`합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-243">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="91c5d-244">이외의 헤더 값으로 설정 하면 `"*"` (사용할지 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>)를 하나 이상 포함 해야 `Accept`, `Content-Type`, 및 `Origin`, 지원 하려는 모든 사용자 지정 헤더 및.</span><span class="sxs-lookup"><span data-stu-id="91c5d-244">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="91c5d-245">다음은 실행 전 요청 (서버 요청을 허용 하는지 가정할 경우)에 대 한 예제 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-245">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="91c5d-246">응답에 포함 되어는 `Access-Control-Allow-Methods` 허용 되는 메서드를 나열 하는 헤더 및 필요에 따라는 `Access-Control-Allow-Headers` 허용 되는 헤더를 나열 하는 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-246">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="91c5d-247">실행 전 요청이 성공 하면 브라우저는 실제 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-247">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="91c5d-248">실행 전 요청이 거부 되 면 앱에서 반환 된 *200 확인* 응답 CORS 헤더를 다시 보내지 않습니다. 하지만 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-248">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="91c5d-249">따라서 브라우저는 크로스-원본 요청을 시도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-249">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="91c5d-250">예비 요청 만료 시간 설정하기</span><span class="sxs-lookup"><span data-stu-id="91c5d-250">Set the preflight expiration time</span></span>

<span data-ttu-id="91c5d-251">`Access-Control-Max-Age` 헤더 실행 전 요청에 응답을 캐시할 수 기간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-251">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="91c5d-252">이 헤더를 설정 하려면 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span><span class="sxs-lookup"><span data-stu-id="91c5d-252">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="91c5d-253">CORS의 동작 방식</span><span class="sxs-lookup"><span data-stu-id="91c5d-253">How CORS works</span></span>

<span data-ttu-id="91c5d-254">이 섹션에서는 수행 되는 작업에 대해 설명 합니다.는 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) HTTP 메시지 수준에서 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-254">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="91c5d-255">CORS는 **되지** 보안 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-255">CORS is **not** a security feature.</span></span> <span data-ttu-id="91c5d-256">CORS는 서버를 동일 원본 정책을 완화할 수 있는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-256">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="91c5d-257">예를 들어, 악의적인 행위자가 사용할 수 [되지 않도록 사이트 간 스크립팅 (XSS)](xref:security/cross-site-scripting) 사이트에 대 한 정보를 도용 하는 사용 하도록 설정 하는 CORS 사이트로 교차 사이트 요청을 실행 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-257">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="91c5d-258">API는 CORS 허용 하 여 안전 하 게 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-258">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="91c5d-259">CORS를 적용 하는 클라이언트 (브라우저)에 달려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-259">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="91c5d-260">서버 요청을 실행 하 고 응답을 반환 합니다, 그리고 오류가 및 블록 응답을 반환 하는 클라이언트입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-260">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="91c5d-261">예를 들어 서버 응답을 표시는 다음 도구 중 하나:</span><span class="sxs-lookup"><span data-stu-id="91c5d-261">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="91c5d-262">Fiddler</span><span class="sxs-lookup"><span data-stu-id="91c5d-262">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="91c5d-263">Postman</span><span class="sxs-lookup"><span data-stu-id="91c5d-263">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="91c5d-264">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="91c5d-264">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="91c5d-265">주소 표시줄에 URL을 입력 하 여 웹 브라우저.</span><span class="sxs-lookup"><span data-stu-id="91c5d-265">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="91c5d-266">크로스-원본을 실행 하는 브라우저를 허용 하도록 서버에 대 한 방법을 것 [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 또는 [인출 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 요청 금지 될 것입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-266">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="91c5d-267">브라우저 (CORS) 없이 크로스-원본 요청을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-267">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="91c5d-268">CORS를 하기 전에 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 이 제한 사항을 회피할 하는 데 사용 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-268">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="91c5d-269">JSONP XHR를 사용 하지 않는 사용 하 여는 `<script>` 태그를 응답을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-269">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="91c5d-270">스크립트는 로드 된 크로스-원본 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-270">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="91c5d-271">합니다 [CORS 사양](https://www.w3.org/TR/cors/) 크로스-원본 요청을 사용 하도록 설정 하는 몇 가지 새로운 HTTP 헤더를 도입 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-271">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="91c5d-272">브라우저에서 CORS를 지 원하는 경우 이러한 머리글 크로스-원본 요청을 자동으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-272">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="91c5d-273">CORS를 사용 하도록 사용자 지정 JavaScript 코드 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-273">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="91c5d-274">다음은 원본 간 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-274">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="91c5d-275">`Origin` 헤더는 요청 하는 사이트의 도메인을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-275">The `Origin` header provides the domain of the site that's making the request:</span></span>

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

<span data-ttu-id="91c5d-276">서버 요청을 허용 하는 경우에 설정의 `Access-Control-Allow-Origin` 헤더를 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-276">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="91c5d-277">이 헤더의 값이 일치 하거나 합니다 `Origin` 요청의 헤더 또는 와일드 카드 값 `"*"`, 모든 원본을 허용 되는 의미:</span><span class="sxs-lookup"><span data-stu-id="91c5d-277">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="91c5d-278">응답에 포함 되지 않은 경우는 `Access-Control-Allow-Origin` 헤더를 크로스-원본 요청에 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-278">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="91c5d-279">특히 브라우저 요청을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-279">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="91c5d-280">서버 성공적인 응답을 반환 하는 경우에 브라우저 되어도 응답이 클라이언트 앱에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-280">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="91c5d-281">CORS 테스트</span><span class="sxs-lookup"><span data-stu-id="91c5d-281">Test CORS</span></span>

<span data-ttu-id="91c5d-282">CORS 테스트:</span><span class="sxs-lookup"><span data-stu-id="91c5d-282">To test CORS:</span></span>

1. <span data-ttu-id="91c5d-283">[API 프로젝트를 만들고](xref:tutorials/first-web-api)합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-283">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="91c5d-284">또는 수 있습니다 [샘플을 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cors/sample/Cors)합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-284">Alternatively, you can [download the sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="91c5d-285">이 문서의 방법 중 하나를 사용 하 여 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-285">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="91c5d-286">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="91c5d-286">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="91c5d-287">`WithOrigins("https://localhost:<port>");` 유사한 샘플 앱을 테스트용만 사용 해야 합니다 [샘플 코드 다운로드](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/cors/sample/Cors)합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-287">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="91c5d-288">(Razor 페이지나 MVC) 웹 앱 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-288">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="91c5d-289">이 샘플에서는 Razor 페이지를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-289">The sample uses Razor Pages.</span></span> <span data-ttu-id="91c5d-290">API 프로젝트와 동일한 솔루션에서 웹 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-290">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="91c5d-291">다음 강조 표시 된 코드를 추가 합니다 *Index.cshtml* 파일:</span><span class="sxs-lookup"><span data-stu-id="91c5d-291">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="91c5d-292">위의 코드에서 `url: 'https://<web app>.azurewebsites.net/api/values/1',` 배포 된 앱에 대 한 URL로 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-292">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="91c5d-293">API 프로젝트를 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-293">Deploy the API project.</span></span> <span data-ttu-id="91c5d-294">예를 들어 [Azure에 배포](xref:host-and-deploy/azure-apps/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-294">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="91c5d-295">바탕 화면에서 Razor 페이지 또는 MVC 앱을 실행 하 고 클릭 합니다 **테스트** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-295">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="91c5d-296">오류 메시지를 검토 하려면 F12 도구를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-296">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="91c5d-297">localhost 원점 제거 `WithOrigins` 및 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-297">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="91c5d-298">또는 다른 포트를 사용 하 여 클라이언트 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-298">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="91c5d-299">예를 들어, Visual Studio에서 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-299">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="91c5d-300">클라이언트 앱을 사용 하 여 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-300">Test with the client app.</span></span> <span data-ttu-id="91c5d-301">CORS 오류는 오류를 반환 하지만 오류 메시지를 JavaScript에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-301">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="91c5d-302">F12 도구 콘솔 탭을 사용 하 여 오류를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="91c5d-302">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="91c5d-303">브라우저에 따라 오류가 발생 (F12 도구 콘솔)에서 다음과 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-303">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="91c5d-304">Microsoft Edge를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-304">Using Microsoft Edge:</span></span>

     <span data-ttu-id="91c5d-305">**SEC7120: [CORS] 원점 `https://localhost:44375` 찾지 `https://localhost:44375` 에서 크로스-원본 자원에 대 한 액세스 제어-허용-원본 응답 헤더 `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="91c5d-305">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="91c5d-306">Chrome을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91c5d-306">Using Chrome:</span></span>

     <span data-ttu-id="91c5d-307">**XMLHttpRequest에 대 한 액세스 `https://webapi.azurewebsites.net/api/values/1` 원본의 `https://localhost:44375` CORS 정책에 의해 차단 되었습니다. 요청된 된 리소스에을 ' 액세스 제어-허용-원본 ' 헤더가 없습니다.**</span><span class="sxs-lookup"><span data-stu-id="91c5d-307">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="91c5d-308">추가 자료</span><span class="sxs-lookup"><span data-stu-id="91c5d-308">Additional resources</span></span>

* [<span data-ttu-id="91c5d-309">크로스-원본 자원 공유 (CORS)</span><span class="sxs-lookup"><span data-stu-id="91c5d-309">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
