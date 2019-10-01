---
title: ASP.NET Core 웹 API에서 오류 처리
author: pranavkm
description: ASP.NET Core 웹 API를 사용한 오류 처리에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/25/2019
uid: web-api/handle-errors
ms.openlocfilehash: 9c5dd2f89e7351f386d1f0633c831952dc58e568
ms.sourcegitcommit: 994da92edb0abf856b1655c18880028b15a28897
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71278727"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="7568a-103">ASP.NET Core 웹 API에서 오류 처리</span><span class="sxs-lookup"><span data-stu-id="7568a-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="7568a-104">이 문서에서는 ASP.NET Core 웹 API를 사용하여 오류를 처리하고 사용자 지정하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="7568a-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7568a-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="7568a-106">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="7568a-106">Developer Exception Page</span></span>

<span data-ttu-id="7568a-107">[개발자 예외 페이지](xref:fundamentals/error-handling)는 서버 오류에 대한 자세한 스택 추적을 가져오는 데 유용한 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span>

<span data-ttu-id="7568a-108">클라이언트에서 HTML 형식의 출력을 허용하지 않는 경우 개발자 예외 페이지에 일반 텍스트 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-108">The Developer Exception Page displays a plain-text response if the client doesn't accept HTML-formatted output.</span></span> <span data-ttu-id="7568a-109">예:</span><span class="sxs-lookup"><span data-stu-id="7568a-109">For example:</span></span>

```
> curl https://localhost:5001/weatherforecast
System.ArgumentException: count
   at errorhandling.Controllers.WeatherForecastController.Get(Int32 x) in D:\work\Samples\samples\aspnetcore\mvc\errorhandling\Controllers\WeatherForecastController.cs:line 35
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
...
```

> [!WARNING]
> <span data-ttu-id="7568a-110">**앱이 개발 환경에서 실행 중인 경우에만** 개발자 예외 페이지를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-110">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="7568a-111">프로덕션 환경에서 앱을 실행할 때 자세한 예외 정보를 공개적으로 공유하지 않을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-111">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="7568a-112">환경 구성 방법에 대한 자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7568a-112">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="7568a-113">예외 처리기</span><span class="sxs-lookup"><span data-stu-id="7568a-113">Exception handler</span></span>

<span data-ttu-id="7568a-114">비개발 환경에서는 [예외 처리 미들웨어](xref:fundamentals/error-handling)를 사용하여 오류 페이로드를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-114">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="7568a-115">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>를 호출하여 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-115">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="7568a-116">`/error` 경로에 응답하도록 컨트롤러 작업을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-116">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="7568a-117">위의 `Error` 작업은 [RFC7807](https://tools.ietf.org/html/rfc7807) 호환 페이로드를 클라이언트에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-117">The preceding `Error` action sends an [RFC7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="7568a-118">예외 처리 미들웨어는 로컬 개발 환경에서 보다 자세한 콘텐츠 협상 출력을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-118">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="7568a-119">다음 단계를 사용하여 개발 및 프로덕션 환경에서 일관된 페이로드 형식을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-119">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="7568a-120">`Startup.Configure`에서 환경별 예외 처리 미들웨어 인스턴스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-120">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="7568a-121">위의 코드에서 미들웨어는 다음에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-121">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="7568a-122">개발 환경에서 `/error-local-development`의 경로.</span><span class="sxs-lookup"><span data-stu-id="7568a-122">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="7568a-123">개발되지 않은 환경에서 `/error`의 경로.</span><span class="sxs-lookup"><span data-stu-id="7568a-123">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="7568a-124">컨트롤러 작업에 특성 라우팅 적용:</span><span class="sxs-lookup"><span data-stu-id="7568a-124">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="7568a-125">예외를 사용하여 응답 수정</span><span class="sxs-lookup"><span data-stu-id="7568a-125">Use exceptions to modify the response</span></span>

<span data-ttu-id="7568a-126">응답의 내용은 컨트롤러 외부에서 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-126">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="7568a-127">ASP.NET 4.x Web API에서 이것을 수행하는 한 가지 방법은 <xref:System.Web.Http.HttpResponseException> 형식을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-127">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="7568a-128">ASP.NET Core에는 동일한 형식이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-128">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="7568a-129">다음 단계로 `HttpResponseException`에 대한 지원을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-129">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="7568a-130">`HttpResponseException`이라는 잘 알려진 예외 형식을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-130">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="7568a-131">`HttpResponseExceptionFilter`이라는 작업 필터를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-131">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. <span data-ttu-id="7568a-132">`Startup.ConfigureServices`에서 필터 컬렉션에 작업 필터를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-132">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="7568a-133">유효성 검사 실패 오류 응답</span><span class="sxs-lookup"><span data-stu-id="7568a-133">Validation failure error response</span></span>

<span data-ttu-id="7568a-134">Web API 컨트롤러의 경우, 모델 유효성 검사에 실패하면 MVC는 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 응답 형식으로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-134">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="7568a-135">MVC는 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>의 결과를 사용하여 유효성 검사 실패에 대한 오류 응답을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-135">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="7568a-136">다음 예제에서는 팩터리를 사용하여 `Startup.ConfigureServices`에서 기본 응답 형식을 <xref:Microsoft.AspNetCore.Mvc.SerializableError>로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-136">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="7568a-137">클라이언트 오류 응답</span><span class="sxs-lookup"><span data-stu-id="7568a-137">Client error response</span></span>

<span data-ttu-id="7568a-138">*오류 결과*는 400 이상의 HTTP 상태 코드를 가진 결과로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-138">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="7568a-139">Web API 컨트롤러의 경우, MVC는 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>의 결과로 오류 결과를 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-139">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7568a-140">오류 응답은 다음 방법 중 하나로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-140">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="7568a-141">ProblemDetailsFactory 구현</span><span class="sxs-lookup"><span data-stu-id="7568a-141">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="7568a-142">ApiBehaviorOptions.ClientErrorMapping 사용</span><span class="sxs-lookup"><span data-stu-id="7568a-142">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="7568a-143">ProblemDetailsFactory 구현</span><span class="sxs-lookup"><span data-stu-id="7568a-143">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="7568a-144">MVC는 `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory`를 사용하여 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 및 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>의 모든 인스턴스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-144">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="7568a-145">여기에는 클라이언트 오류 응답, 유효성 검사 실패 오류 응답, `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` 및 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> 도우미 메서드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-145">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="7568a-146">문제 세부 정보 응답을 사용자 지정하려면 `Startup.ConfigureServices`에서 `ProblemDetailsFactory`의 사용자 지정 구현을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-146">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7568a-147">오류 응답은 [ApiBehaviorOptions.ClientErrorMapping 사용](#use-apibehavioroptionsclienterrormapping) 섹션에 설명된 대로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-147">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="7568a-148">ApiBehaviorOptions.ClientErrorMapping 사용</span><span class="sxs-lookup"><span data-stu-id="7568a-148">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="7568a-149"><xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> 속성을 사용하여 `ProblemDetails` 응답의 내용을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-149">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="7568a-150">예를 들어 `Startup.ConfigureServices` 내의 다음 코드는 404 응답에 대해 `type` 속성을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7568a-150">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
