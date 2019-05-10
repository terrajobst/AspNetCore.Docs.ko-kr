---
title: HTTP 처리기 및 모듈을 ASP.NET Core 미들웨어로 마이그레이션
author: rick-anderson
description: ''
ms.author: tdykstra
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: 516230a66ee3edba986c91d79684256aa8e4c994
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087014"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>HTTP 처리기 및 모듈을 ASP.NET Core 미들웨어로 마이그레이션

[Matt Perdeck](https://www.linkedin.com/in/mattperdeck)

이 문서에서는 기존 ASP.NET 마이그레이션하는 방법을 보여 줍니다 [HTTP 모듈 및 처리기 system.webserver에서](/iis/configuration/system.webserver/) ASP.NET core [미들웨어](xref:fundamentals/middleware/index)합니다.

## <a name="modules-and-handlers-revisited"></a>모듈 및 처리기 revisited

ASP.NET Core 미들웨어를 계속 하기 전에 먼저 요약해 보면 HTTP 모듈 및 처리기의 작동 방식:

![모듈 처리기](http-modules/_static/moduleshandlers.png)

**처리기는:**

* 구현 하는 클래스 [IHttpHandler](/dotnet/api/system.web.ihttphandler)

* 지정 된 파일 이름 또는 확장명으로 요청을 처리 하는 데 *보고서*

* [구성할](/iis/configuration/system.webserver/handlers/) 에서 *Web.config*

**모듈은 됩니다.**

* 구현 하는 클래스 [IHttpModule](/dotnet/api/system.web.ihttpmodule)

* 모든 요청에 대 한 호출

* (이후 처리를 중지 요청)를 단락 (short-circuit) 할

* HTTP 응답에 추가 하거나 직접 만들 수

* [구성할](/iis/configuration/system.webserver/modules/) 에서 *Web.config*

**모듈에는 들어오는 요청을 처리 하는 순서는 의해 결정 됩니다.**

1. 합니다 [응용 프로그램 수명 주기](https://msdn.microsoft.com/library/ms227673.aspx), ASP.NET에 의해 발생 하는 시리즈 이벤트는: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. 각 모듈에는 하나 이상의 이벤트 처리기를 만들 수 있습니다.

2. 동일한 이벤트에서 구성 하는 순서에 대 한 *Web.config*합니다.

모듈 외에도 수명 주기 이벤트에 대 한 처리기를 추가할 수 있습니다 하 *Global.asax.cs* 파일입니다. 이러한 처리기는 구성 된 모듈의 처리기 후 실행합니다.

## <a name="from-handlers-and-modules-to-middleware"></a>처리기 및 모듈을 미들웨어로

**미들웨어는 HTTP 모듈 및 처리기 보다 간단 합니다.**

* 모듈, 처리기 *Global.asax.cs*를 *Web.config* (제외 IIS 구성)은 응용 프로그램 수명 주기 및

* 미들웨어를 통해 수행 된 모듈 및 처리기의 역할

* 미들웨어는 코드를 사용 하 여 구성 된 대신 *Web.config*

* [파이프라인 분기](xref:fundamentals/middleware/index#use-run-and-map) 뿐 아니라 요청 헤더, 쿼리 문자열에도 URL을 기준으로 특정 미들웨어에서 요청을 보낼 수 있습니다.

**미들웨어 모듈 매우 비슷합니다.**

* 모든 요청에 대 한 원칙에서 호출

* 요청에 의해 단락 (short-circuit) 할 [다음 미들웨어에 요청을 전달 하지 않고](#http-modules-shortcircuiting-middleware)

* HTTP 응답 자체를 만들려면

**미들웨어 및 모듈을 다른 순서로 처리 됩니다.**

* 에 삽입할 요청 파이프라인을 모듈의 순서는 주로 기반으로 하는 동안 순서를 기반으로 하는 미들웨어 순서 [응용 프로그램 수명 주기](https://msdn.microsoft.com/library/ms227673.aspx) 이벤트

* 미들웨어의 응답에 대 한 순서가 반대로 하는 요청에 대 한 요청 및 응답에 대 한 동일한 모듈의 순서는

* 참조 [IApplicationBuilder로 미들웨어 파이프라인 만들기](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

![미들웨어](http-modules/_static/middleware.png)

어떻게 요청 short-circuited 인증 미들웨어에서 위의 이미지 note 합니다.

## <a name="migrating-module-code-to-middleware"></a>모듈 코드를 미들웨어로 마이그레이션

기존 HTTP 모듈은 다음과 유사 하 게 표시 됩니다.

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

에 표시 된 대로 [미들웨어](xref:fundamentals/middleware/index) 페이지는 ASP.NET Core 미들웨어가 노출 하는 클래스는 `Invoke` 메서드 만들기는 `HttpContext` 반환 하 고는 `Task`합니다. 새 미들웨어는 다음과 같습니다.

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

이전 미들웨어 템플릿 섹션에서 수행한 [미들웨어를 작성](xref:fundamentals/middleware/write)합니다.

합니다 *MyMiddlewareExtensions* 도우미 클래스를 사용 하면에서 미들웨어를 구성 하기 더 쉬울 프로그램 `Startup` 클래스입니다. `UseMyMiddleware` 메서드 요청 파이프라인에 미들웨어 클래스를 추가 합니다. 미들웨어에서 필요한 서비스는 미들웨어의 생성자에 지정 된 가져오기.

<a name="http-modules-shortcircuiting-middleware"></a>

모듈에는 예를 들어 사용자 권한이 없는 경우 요청을 종료할 수 있습니다.

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

미들웨어를 호출 하지 않음으로써이 처리 `Invoke` 에서 파이프라인의 다음 미들웨어입니다. 이전 미들웨어의 응답은 파이프라인을 통해 다시 때 호출 될 여전히 때문에 완벽 하 게 요청을 종료 하지 않고이 염두에서에 둡니다.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

새 미들웨어를 모듈의 기능으로 마이그레이션한 경우 때문에 코드는 컴파일되지 않습니다 알 수 있습니다는 `HttpContext` 클래스 ASP.NET Core에서 크게 변경 되었습니다. [나중에](#migrating-to-the-new-httpcontext), 새 ASP.NET Core HttpContext에 마이그레이션하는 방법을 배웁니다.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>마이그레이션 모듈 삽입 요청 파이프라인

HTTP 모듈은 일반적으로 사용 하 여 요청 파이프라인에 추가 됩니다 *Web.config*:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

이 변환 [새 미들웨어를 추가](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 를 요청 파이프라인에 프로그램 `Startup` 클래스:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

새 미들웨어를 삽입 하면 파이프라인에서 정확한 지점 모듈로 처리 하는 이벤트에 따라 달라 집니다 (`BeginRequest`, `EndRequest`등) 및 모듈에는 목록에서 해당 순서 *Web.config*합니다.

앞에서 설명한 대로 ASP.NET Core에 없는 응용 프로그램 수명 주기를 없는 언급 하는 미들웨어에서 처리 되는 응답 순서 모듈에서 사용 하는 순서에서 다릅니다. 이 순서 결정 까다롭게를 만들 수 있습니다.

순서 지정 문제가 되 면 독립적으로 주문할 수 있습니다. 여러 미들웨어 구성 요소에 모듈을 분할할 수 있습니다.

## <a name="migrating-handler-code-to-middleware"></a>처리기 코드를 미들웨어로 마이그레이션

HTTP 처리기는 다음과 같습니다.

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

ASP.NET Core 프로젝트에서 다음과 유사 하 게 하는 미들웨어로이 변환는 있습니다.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

이 미들웨어는 해당 모듈을 미들웨어와 매우 비슷합니다. 유일한 차이점은에 대 한 호출이 같습니다 `_next.Invoke(context)`합니다. 합리적이 지 처리기가 요청 파이프라인의 끝에는 없는 다음 미들웨어를 호출 합니다.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>마이그레이션 처리기 삽입 요청 파이프라인

HTTP 처리기 구성 이루어집니다 *Web.config* 같습니다 및:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

새 처리기 미들웨어를 요청 파이프라인에 추가 하 여이 변환할 수 있습니다 프로그램 `Startup` 클래스, 모듈에서 변환 하는 미들웨어와 비슷합니다. 해당 방법 사용 하 여 문제는 새 처리기 미들웨어에 모든 요청을 보내기는 것입니다. 그러나 미들웨어를 연결할 지정된 된 확장을 사용 하 여 요청 하려는 있습니다. HTTP 처리기를 사용 하 여 사용 했던 동일한 기능을 제공 됩니다.

한 가지 해결책은 지정된 된 확장을 사용 하 여 요청에 대 한 파이프라인 분기를 사용 하 여는 `MapWhen` 확장 메서드. 이렇게 하면 동일한 `Configure` 메서드는 다른 미들웨어를 추가 하는 위치:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen` 이러한 매개 변수를 사용 합니다.

1. 사용 하는 람다는 `HttpContext` 반환 `true` 요청 분기 아래로 이동 해야 하는 경우. 즉, 요청 뿐 아니라 해당 확장에서 뿐만 아니라 요청 헤더, 쿼리 문자열 매개 변수를 기반으로 분기할 수 있습니다.

2. 사용 하는 람다는 `IApplicationBuilder` 분기에 대 한 모든 미들웨어를 추가 합니다. 즉, 처리기 미들웨어 앞에 분기에 추가 하는 미들웨어를 추가할 수 있습니다.

분기 모든 요청에서 호출 될 미들웨어 파이프라인에 추가 분기에 영향을 주지 않습니다.

## <a name="loading-middleware-options-using-the-options-pattern"></a>로드 옵션 패턴을 사용 하 여 미들웨어 옵션입니다.

일부 모듈 및 처리기에 저장 되는 구성 옵션을 사용할 *Web.config*합니다. 그러나 ASP.NET Core에서 새 구성 모델을는 대신 *Web.config*합니다.

새 [구성 시스템](xref:fundamentals/configuration/index) 이 문제를 해결 하려면 다음이 옵션을 제공 합니다.

* 에 표시 된 대로 옵션을 미들웨어로 직접 삽입 합니다 [다음 섹션에서는](#loading-middleware-options-through-direct-injection)합니다.

* 사용 된 [옵션 패턴](xref:fundamentals/configuration/options):

1. 예를 들어 미들웨어 옵션을 보유 하는 클래스를 만듭니다.

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. 옵션 값을 저장

   구성 시스템을 사용 하면 옵션 어디서 나 원하는 값을 저장할 수 있습니다. 그러나 사용 하 여 대부분의 사이트 *appsettings.json*이므로 그 방법을 알아보겠습니다.

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   *MyMiddlewareOptionsSection* 섹션 이름은 다음과 같습니다. 옵션 클래스의 이름으로 동일할 필요가 없습니다.

3. 옵션 값 옵션 클래스를 사용 하 여 연결

    옵션 패턴 ASP.NET Core의 종속성 주입 프레임 워크를 사용 하 여 연결 옵션의 유형을 (같은 `MyMiddlewareOptions`)와 `MyMiddlewareOptions` 실제 옵션 개체입니다.

    업데이트 프로그램 `Startup` 클래스:

   1. 사용 중인 경우 *appsettings.json*에서 구성 작성기에 추가 된 `Startup` 생성자:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. 옵션 서비스를 구성 합니다.

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. 옵션 클래스를 사용 하 여 옵션을 연결 합니다.

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. 미들웨어 생성자에 대 한 옵션을 삽입 합니다. 이 옵션을 컨트롤러에 삽입 하는 것과 비슷합니다.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   합니다 [UseMiddleware](#http-modules-usemiddleware) 미들웨어를 추가 하는 확장 메서드는 `IApplicationBuilder` 종속성 주입을 처리 합니다.

   이에 국한 되지 않습니다 `IOptions` 개체입니다. 이러한 방식으로 미들웨어가 필요한 다른 모든 개체를 삽입할 수 있습니다.

## <a name="loading-middleware-options-through-direct-injection"></a>로드 직접 주입을 통해 미들웨어 옵션입니다.

옵션 패턴 옵션 값 및 소비자 간에 느슨한 연결 생성 되는 이점이 있습니다. 실제 옵션 값을 사용 하는 옵션 클래스를 연결한 후 다른 클래스는 종속성 주입 프레임 워크를 통해 옵션에 대 한 액세스를 가져올 수 있습니다. 옵션 값을 전달할 필요가 없습니다 있습니다.

이 세분화 하지만 다양 한 옵션을 사용 하 여 동일한 미들웨어를 두 번 사용 하려는 경우. 예를 들어 인증 미들웨어를 다양 한 역할을 허용 하는 다른 분기에서 사용 합니다. 하나의 옵션 클래스를 사용 하 여 두 가지 다른 옵션 개체를 연결할 수 없습니다.

솔루션에 실제 옵션 값이 있는 옵션 개체를 가져오는 데는 프로그램 `Startup` 클래스 및 미들웨어의 각 인스턴스를 직접 전달 합니다.

1. 두 번째 키를 추가 *appsettings.json*

   옵션 중 두 번째 집합을 추가 하는 *appsettings.json* 파일을 고유 하 게 식별 하는 데 새 키를 사용 합니다.

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. 옵션 값을 검색 하 고 미들웨어에 전달 합니다. `Use...` 확장 메서드 (파이프라인에 미들웨어를 추가) 옵션 값을 전달할 수 있는 논리 위치는: 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. 미들웨어 옵션 매개 변수를 사용 하도록 설정 합니다. 오버 로드를 제공 합니다 `Use...` 확장 메서드 (옵션 매개 변수를 사용 하 고 전달 `UseMiddleware`). 때 `UseMiddleware` 라고 매개 변수를 사용 하 여 해당 매개 변수를 전달 미들웨어 생성자에 미들웨어 개체를 인스턴스화할 때.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   이 옵션 개체에 배치 되는 방법을 참고는 `OptionsWrapper` 개체입니다. 이 구현 `IOptions`처럼 미들웨어 생성자가 필요 합니다.

## <a name="migrating-to-the-new-httpcontext"></a>새 HttpContext로 마이그레이션

앞서 살펴본 하는 `Invoke` 형식의 매개 변수를 사용 하는 미들웨어에서 메서드 `HttpContext`:

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext` ASP.NET Core에서 크게 변경 되었습니다. 이 섹션에서는 자주 사용 하는 속성을 변환 하는 방법을 보여 줍니다 [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) 새 `Microsoft.AspNetCore.Http.HttpContext`합니다.

### <a name="httpcontext"></a>HttpContext

**HttpContext.Items** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**고유한 요청 ID (System.Web.HttpContext 해당)**

제공 된 고유 id 각 요청에 대 한 합니다. 로그에 포함 하도록 매우 유용 합니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext.Request

**HttpContext.Request.HttpMethod** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext.Request.QueryString** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext.Request.Url** 하 고 **HttpContext.Request.RawUrl** 로 변환 합니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext.Request.IsSecureConnection** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext.Request.UserHostAddress** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext.Request.Cookies** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext.Request.RequestContext.RouteData** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext.Request.Headers** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext.Request.UserAgent** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext.Request.UrlReferrer** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext.Request.ContentType** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext.Request.Form** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> 콘텐츠 하위 형식이 있는 경우에 양식 값을 읽을 *x-www-형식-urlencoded* 하거나 *양식 데이터*입니다.

**HttpContext.Request.InputStream** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> 이 코드는 처리기 형식 미들웨어 파이프라인의 끝 에서만 사용 합니다.
>
>요청당 한 번만 위에 표시 된 대로 원시 본문을 읽을 수 있습니다. 첫 번째 읽기 후 본문을 읽는 동안 미들웨어는 빈 본문을 읽습니다.
>
>이를 폼 앞에 표시 된 것 처럼 버퍼에서 수행 되기 때문에 적용 되지 않습니다.

### <a name="httpcontextresponse"></a>HttpContext.Response

**HttpContext.Response.Status** 하 고 **HttpContext.Response.StatusDescription** 로 변환 합니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext.Response.ContentEncoding** 하 고 **HttpContext.Response.ContentType** 로 변환 합니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpContext.Response.ContentType** 에서 자체도로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext.Response.Output** 로 변환 됩니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext.Response.TransmitFile**

파일 처리 부분은 [여기](../fundamentals/request-features.md#middleware-and-request-features)합니다.

**HttpContext.Response.Headers**

응답 헤더를 보내는 것은 복잡 팩트를 설정할 경우 응답 본문에 아무 것도 기록 된 후에 전송 되지 것입니다.

솔루션은 오른쪽 응답 시작에 쓰기 전에 호출 되는 콜백 메서드를 설정 하는 것입니다. 시작 부분에 가장 잘 이루어집니다는 `Invoke` 미들웨어에서 메서드. 에 대 한 응답 헤더를 설정 하는이 콜백 메서드는 것입니다.

다음 코드에서는 호출 하는 콜백 메서드 설정 `SetHeaders`:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetHeaders` 콜백 메서드는 다음과 같습니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext.Response.Cookies**

브라우저에서 쿠키 이동 된 *Set-cookie* 응답 헤더입니다. 결과적으로, 쿠키를 보낼 응답 헤더를 보내는 데 사용 되는 동일한 콜백이 필요 합니다.

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetCookies` 콜백 메서드는 다음과 같습니다.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>추가 자료

* [HTTP 처리기 및 HTTP 모듈 개요](/iis/configuration/system.webserver/)
* [구성](xref:fundamentals/configuration/index)
* [애플리케이션 시작](xref:fundamentals/startup)
* [미들웨어](xref:fundamentals/middleware/index)
