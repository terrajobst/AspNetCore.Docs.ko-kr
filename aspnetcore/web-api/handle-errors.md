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
# <a name="handle-errors-in-aspnet-core-web-apis"></a>ASP.NET Core 웹 API에서 오류 처리

이 문서에서는 ASP.NET Core 웹 API를 사용하여 오류를 처리하고 사용자 지정하는 방법을 설명합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples)([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>개발자 예외 페이지

[개발자 예외 페이지](xref:fundamentals/error-handling)는 서버 오류에 대한 자세한 스택 추적을 가져오는 데 유용한 도구입니다.

클라이언트에서 HTML 형식의 출력을 허용하지 않는 경우 개발자 예외 페이지에 일반 텍스트 응답이 표시됩니다. 예:

```
> curl https://localhost:5001/weatherforecast
System.ArgumentException: count
   at errorhandling.Controllers.WeatherForecastController.Get(Int32 x) in D:\work\Samples\samples\aspnetcore\mvc\errorhandling\Controllers\WeatherForecastController.cs:line 35
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
...
```

> [!WARNING]
> **앱이 개발 환경에서 실행 중인 경우에만** 개발자 예외 페이지를 사용하도록 설정합니다. 프로덕션 환경에서 앱을 실행할 때 자세한 예외 정보를 공개적으로 공유하지 않을 수도 있습니다. 환경 구성 방법에 대한 자세한 내용은 <xref:fundamentals/environments>를 참조하세요.

## <a name="exception-handler"></a>예외 처리기

비개발 환경에서는 [예외 처리 미들웨어](xref:fundamentals/error-handling)를 사용하여 오류 페이로드를 생성할 수 있습니다.

1. `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>를 호출하여 미들웨어를 사용합니다.

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. `/error` 경로에 응답하도록 컨트롤러 작업을 구성합니다.

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

위의 `Error` 작업은 [RFC7807](https://tools.ietf.org/html/rfc7807) 호환 페이로드를 클라이언트에 보냅니다.

예외 처리 미들웨어는 로컬 개발 환경에서 보다 자세한 콘텐츠 협상 출력을 제공할 수도 있습니다. 다음 단계를 사용하여 개발 및 프로덕션 환경에서 일관된 페이로드 형식을 생성합니다.

1. `Startup.Configure`에서 환경별 예외 처리 미들웨어 인스턴스를 등록합니다.

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

    위의 코드에서 미들웨어는 다음에 등록됩니다.

    * 개발 환경에서 `/error-local-development`의 경로.
    * 개발되지 않은 환경에서 `/error`의 경로.
    
1. 컨트롤러 작업에 특성 라우팅 적용:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a>예외를 사용하여 응답 수정

응답의 내용은 컨트롤러 외부에서 수정할 수 있습니다. ASP.NET 4.x Web API에서 이것을 수행하는 한 가지 방법은 <xref:System.Web.Http.HttpResponseException> 형식을 사용하는 것입니다. ASP.NET Core에는 동일한 형식이 포함되지 않습니다. 다음 단계로 `HttpResponseException`에 대한 지원을 추가할 수 있습니다.

1. `HttpResponseException`이라는 잘 알려진 예외 형식을 만듭니다.

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. `HttpResponseExceptionFilter`이라는 작업 필터를 만듭니다.

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. `Startup.ConfigureServices`에서 필터 컬렉션에 작업 필터를 추가합니다.

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a>유효성 검사 실패 오류 응답

Web API 컨트롤러의 경우, 모델 유효성 검사에 실패하면 MVC는 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 응답 형식으로 응답합니다. MVC는 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>의 결과를 사용하여 유효성 검사 실패에 대한 오류 응답을 생성합니다. 다음 예제에서는 팩터리를 사용하여 `Startup.ConfigureServices`에서 기본 응답 형식을 <xref:Microsoft.AspNetCore.Mvc.SerializableError>로 변경합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a>클라이언트 오류 응답

*오류 결과*는 400 이상의 HTTP 상태 코드를 가진 결과로 정의됩니다. Web API 컨트롤러의 경우, MVC는 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>의 결과로 오류 결과를 변환합니다.

::: moniker range=">= aspnetcore-3.0"

오류 응답은 다음 방법 중 하나로 구성할 수 있습니다.

1. [ProblemDetailsFactory 구현](#implement-problemdetailsfactory)
1. [ApiBehaviorOptions.ClientErrorMapping 사용](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a>ProblemDetailsFactory 구현

MVC는 `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory`를 사용하여 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 및 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>의 모든 인스턴스를 생성합니다. 여기에는 클라이언트 오류 응답, 유효성 검사 실패 오류 응답, `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` 및 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> 도우미 메서드가 포함됩니다.

문제 세부 정보 응답을 사용자 지정하려면 `Startup.ConfigureServices`에서 `ProblemDetailsFactory`의 사용자 지정 구현을 등록합니다.

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

오류 응답은 [ApiBehaviorOptions.ClientErrorMapping 사용](#use-apibehavioroptionsclienterrormapping) 섹션에 설명된 대로 구성할 수 있습니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a>ApiBehaviorOptions.ClientErrorMapping 사용

<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> 속성을 사용하여 `ProblemDetails` 응답의 내용을 구성합니다. 예를 들어 `Startup.ConfigureServices` 내의 다음 코드는 404 응답에 대해 `type` 속성을 업데이트합니다.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
