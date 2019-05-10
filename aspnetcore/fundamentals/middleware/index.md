---
title: ASP.NET Core 미들웨어 기본 사항
author: rick-anderson
description: ASP.NET Core 미들웨어 및 요청 파이프라인에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 02/17/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: bac121441d6856ca79affe1a3130e5cbc76debd9
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64882338"
---
# <a name="aspnet-core-middleware"></a>ASP.NET Core 미들웨어

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)

미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 어셈블리되는 소프트웨어입니다. 각 구성 요소:

* 요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.
* 파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.

요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다. 요청 대리자는 각 HTTP 요청을 처리합니다.

요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 확장 메서드를 사용하여 구성됩니다. 개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다. 이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드는 *미들웨어*이며, *미들웨어 구성 요소*라고도 합니다. 요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다. 미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어*라고 합니다.

<xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 더 많은 미들웨어 샘플을 제공합니다.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>IApplicationBuilder로 미들웨어 파이프라인 만들기

ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다. 다음 다이어그램은 개념을 보여줍니다. 실행 스레드는 검은색 화살표를 따릅니다.

![요청이 도착하고, 세 가지 미들웨어를 처리하고, 응답이 앱에서 나가는 것을 보여주는 요청 처리 패턴입니다. 각 미들웨어는 해당 논리를 실행하고 next() 문에서 다음 미들웨어에 대한 요청을 전달합니다. 세 번째 미들웨어가 요청을 처리한 후 요청은 클라이언트에 대한 응답으로 앱을 떠나기 전에 해당 다음() 문 후에 추가 처리에 대한 반대 순서로 이전의 두 개의 미들웨어를 통해 다시 전달합니다.](index/_static/request-delegate-pipeline.png)

각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다. 예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 catch할 수 있도록 파이프라인의 초기에 호출되어야 합니다.

가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다. 이 경우 실제 요청 파이프라인은 포함하지 않습니다. 대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

첫 번째 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 대리자는 파이프라인을 종료합니다.

<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>를 사용하여 여러 요청 대리자를 연결합니다. `next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다. *next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다. 다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

대리자가 다음 대리자에 요청을 전달하지 않을 때 이를*요청 파이프라인을 단락(short-circuiting)* 한다고 합니다. 단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 보통 바람직합니다. 예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어*로 작동할 수 있습니다. 추가 처리를 종료하는 미들웨어 앞의 파이프라인에 추가되는 미들웨어는 `next.Invoke` 문 뒤의 코드를 계속 처리합니다. 그러나 이미 전송된 응답에 쓰려고 하는 것에 대한 다음 경고를 참조하세요.

> [!WARNING]
> 클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오. 응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 throw합니다. 예를 들어 헤더 및 상태 코드를 설정하는 변경 작업은 예외를 throw합니다. `next`를 호출한 후 응답 본문에 작성하기:
>
> * 프로토콜 위반이 발생할 수 있습니다. 예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.
> * 본문 형식을 손상시킬 수 있습니다. 예를 들어 CSS 파일에 HTML 바닥글 작성하기.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.

## <a name="order"></a>순서

미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다. 순서는 보안, 성능 및 기능에 중요합니다.

다음 `Startup.Configure` 메서드는 공통 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.

::: moniker range=">= aspnetcore-2.0"

1. 예외/오류 처리
1. HTTP 엄격한 전송 보안 프로토콜
1. HTTPS 리디렉션
1. 정적 파일 서버
1. 쿠키 정책 적용
1. 인증
1. 세션
1. MVC

```csharp
public void Configure(IApplicationBuilder app)
{
    if (env.IsDevelopment())
    {
        // When the app runs in the Development environment:
        //   Use the Developer Exception Page to report app runtime errors.
        //   Use the Database Error Page to report database runtime errors.
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        // When the app doesn't run in the Development environment:
        //   Enable the Exception Handler Middleware to catch exceptions
        //     thrown in the following middlewares.
        //   Use the HTTP Strict Transport Security Protocol (HSTS)
        //     Middleware.
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    // Use HTTPS Redirection Middleware to redirect HTTP requests to HTTPS.
    app.UseHttpsRedirection();

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Use Cookie Policy Middleware to conform to EU General Data 
    // Protection Regulation (GDPR) regulations.
    app.UseCookiePolicy();

    // Authenticate before the user accesses secure resources.
    app.UseAuthentication();

    // If the app uses session state, call Session Middleware after Cookie 
    // Policy Middleware and before MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvc();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. 예외/오류 처리
1. 정적 파일
1. 인증
1. 세션
1. MVC

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable the Exception Handler Middleware to catch exceptions
    //   thrown in the following middlewares.
    app.UseExceptionHandler("/Home/Error");

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Authenticate before you access secure resources.
    app.UseIdentity();

    // If the app uses session state, call UseSession before 
    // MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다. 따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 catch 합니다.

정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다. 정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**. *wwwroot* 아래의 항목을 비롯한 제공되는 모든 파일은 공개적으로 사용할 수 있습니다. 정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.

::: moniker range=">= aspnetcore-2.0"

요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)로 전달됩니다. 인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다. 인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 ID 미들웨어(<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>)로 전달됩니다. ID는 인증되지 않은 요청을 단락(short-circuit)하지 않습니다. ID가 요청을 인증하지만 MVC가 특정 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.

::: moniker-end

다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다. 정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다. <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*>의 MVC 응답은 압축할 수 있습니다.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

### <a name="use-run-and-map"></a>Use, Run 및 Map

`Use`, `Run` 및 `Map`을 사용하여 HTTP 파이프라인을 구성합니다. `Use` 메서드는 파이프라인을 단락(short-circuit)할 수 있습니다(즉, `next` 요청 대리자를 호출하지 않는 경우). `Run`은 규칙이며 일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 확장은 파이프라인 분기에 규칙으로 사용됩니다. `Map*`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다. 요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.

| 요청             | 응답                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello from non-Map delegate. |
| localhost:1234/map1 | Map Test 1                   |
| localhost:1234/map2 | Map Test 2                   |
| localhost:1234/map3 | Hello from non-Map delegate. |

`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.

[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions)은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다. `Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다. 다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.

| 요청                       | 응답                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Hello from non-Map delegate. |
| localhost:1234/?branch=master | Branch used = master         |

`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
   ```

`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a>기본 제공 미들웨어

ASP.NET Core는 다음과 같은 미들웨어 구성 요소가 함께 제공됩니다. *순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다. 미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 다운스트림 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어*라고 합니다. 단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.

| 미들웨어 | 설명 | 순서 |
| ---------- | ----------- | ----- |
| [인증](xref:security/authentication/identity) | 인증 지원을 제공합니다. | `HttpContext.User`가 필요하기 전에. OAuth 콜백에 대한 터미널. |
| [쿠키 정책](xref:security/gdpr) | 개인 정보 저장과 관련한 사용자의 동의를 추적하고 쿠키 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다. | 쿠키를 발행하는 미들웨어 전에. 예: 인증, 세션, MVC(TempData). |
| [CORS](xref:security/cors) | 원본 간 리소스 공유를 구성합니다. | CORS를 사용하는 구성 요소 이전. |
| [예외 처리](xref:fundamentals/error-handling) | 예외를 처리합니다. | 오류를 생성하는 구성 요소 이전. |
| [전달된 헤더](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | 프록시된 헤더를 현재 요청에 전달합니다. | 업데이트된 필드를 사용하는 구성 요소 전에. 예: 체계, 호스트, 클라이언트 IP, 메서드. |
| [상태 검사](xref:host-and-deploy/health-checks) | ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다. | 요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다. |
| [HTTP 메서드 재정의](/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | 들어오는 POST 요청이 메서드를 재정의하도록 허용합니다. | 업데이트된 메서드를 사용하는 구성 요소 앞입니다. |
| [HTTPS 리디렉션](xref:security/enforcing-ssl#require-https) | HTTPS로 모든 HTTP 요청을 리디렉션합니다(ASP.NET Core 2.1 이상). | URL을 사용하는 구성 요소 이전. |
| [HSTS(HTTP 엄격한 전송 보안)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | 특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다(ASP.NET Core 2.1 이상). | 응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에. 예: 전달된 헤더, URL 재작성. |
| [MVC](xref:mvc/overview) | MVC/Razor Pages(ASP.NET Core 2.0 이상)를 사용하여 요청을 처리합니다. | 요청이 경로와 일치하는 경우 터미널입니다. |
| [OWIN](xref:fundamentals/owin) | OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다. | OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다. |
| [응답 캐싱](xref:performance/caching/middleware) | 응답 캐시에 대한 지원을 제공합니다. | 캐싱이 필요한 구성 요소 이전. |
| [응답 압축](xref:performance/response-compression) | 응답 압축에 대한 지원을 제공합니다. | 압축이 필요한 구성 요소 이전. |
| [요청 지역화](xref:fundamentals/localization) | 지역화 지원을 제공합니다. | 지역화 구분 구성 요소 이전. |
| [라우팅](xref:fundamentals/routing) | 요청 경로를 정의하고 제한합니다. | 경로 일치에 대한 터미널. |
| [세션](xref:fundamentals/app-state) | 사용자 세션 관리에 대한 지원을 제공합니다. | 세션이 필요한 구성 요소 이전. |
| [정적 파일](xref:fundamentals/static-files) | 정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다. | 요청이 파일과 일치하는 경우 터미널입니다. |
| [URL 재작성](xref:fundamentals/url-rewriting) | URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다. | URL을 사용하는 구성 요소 이전. |
| [WebSockets](xref:fundamentals/websockets) | WebSocket 프로토콜을 활성화합니다. | WebSocket 요청을 수락하는 데 필요한 구성 요소 이전. |

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
