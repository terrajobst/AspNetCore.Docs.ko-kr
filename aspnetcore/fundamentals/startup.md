---
title: ASP.NET Core에서 앱 시작
author: tdykstra
description: ASP.NET Core의 시작 클래스에서 서비스 및 앱의 요청 파이프라인을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/17/2019
uid: fundamentals/startup
ms.openlocfilehash: 362186be6feeeefeca3c56688ee6420de5fb9659
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64884508"
---
# <a name="app-startup-in-aspnet-core"></a>ASP.NET Core에서 앱 시작

작성자: [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex) 및 [Steve Smith](https://ardalis.com)

`Startup` 클래스는 서비스와 응용 프로그램의 요청 파이프라인을 구성합니다.

## <a name="the-startup-class"></a>시작 클래스

ASP.NET Core 앱은 규칙에 따라 `Startup`으로 이름이 지정된 `Startup` 클래스를 사용합니다. `Startup` 클래스:

* 선택적으로 앱의 *서비스*를 구성하는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드를 포함합니다. 서비스는 앱 기능을 제공하는 재사용 가능한 구성 요소입니다. 서비스는 `ConfigureServices`에 구성(&mdash;*등록*된 것으로도 설명&mdash;)되고 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 앱 전체에서 사용됩니다.
* 앱의 요청 처리 파이프라인을 만드는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드가 포함되어 있습니다.

`ConfigureServices` 및 `Configure`는 앱 시작 시 런타임에 의해 호출됩니다.

[!code-csharp[](startup/sample_snapshot/Startup1.cs?highlight=4,10)]

앱의 [호스트](xref:fundamentals/index#host)가 빌드될 때 `Startup` 클래스가 앱에 지정됩니다. 앱의 호스트는 `Build`가 `Program` 클래스의 호스트 작성기에서 호출될 때 빌드됩니다. `Startup` 클래스는 일반적으로 호스트 작성기에서 [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) 메서드를 호출하여 지정됩니다.

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=10)]

호스트는 `Startup` 클래스 생성자에 사용할 수 있는 서비스를 제공합니다. 앱은 `ConfigureServices`를 통해 추가 서비스를 추가합니다. 그러면 호스트 및 앱 서비스 모두를 `Configure` 및 앱 전체에서 사용할 수 있습니다.

`Startup` 클래스에 대한 [종속성 주입](xref:fundamentals/dependency-injection)의 일반적인 용도는 다음을 삽입하는 것입니다.

* 환경별로 서비스를 구성하는 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>.
* 구성을 읽는 <xref:Microsoft.Extensions.Configuration.IConfiguration>.
* `Startup.ConfigureServices`의 로거를 만드는 <xref:Microsoft.Extensions.Logging.ILoggerFactory>.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

`IHostingEnvironment` 삽입의 대안은 규칙 기반 접근 방식을 사용하는 것입니다. 앱에서 다양한 환경(예: `StartupDevelopment`)에 대해 별도의 `Startup` 클래스를 정의할 때 런타임에 적절한 `Startup` 클래스가 선택됩니다. 해당 이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다. 앱이 개발 환경에서 실행되고 `Startup` 클래스 및 `StartupDevelopment` 클래스 모두를 포함하는 경우 `StartupDevelopment` 클래스가 사용됩니다. 자세한 내용은 [여러 환경 사용](xref:fundamentals/environments#environment-based-startup-class-and-methods)를 참조하세요.

호스트에 대한 자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요. 시작하는 동안 오류를 처리하는 방법은 [시작 예외 처리](xref:fundamentals/error-handling#startup-exception-handling)를 참조하세요.

## <a name="the-configureservices-method"></a>ConfigureServices 메서드

<xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드는 다음과 같습니다.

* 선택 사항입니다.
* `Configure` 메서드 전에 호스트에 의해 호출되어 앱의 서비스를 구성합니다.
* 여기서 [구성 옵션](xref:fundamentals/configuration/index)은 규칙에 의해 설정됩니다.

일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 다음, 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다. 예는 [ID 서비스 구성](xref:security/authentication/identity#pw)을 참조하세요.

호스트는 `Startup` 메서드가 호출되기 전에 일부 서비스를 구성할 수 있습니다. 자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요.

대부분의 설치가 필요한 기능의 경우 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>에 `Add{Service}` 확장 메서드가 있습니다. 일반적인 ASP.NET Core 앱은 Entity Framework, ID 및 MVC에 대한 서비스를 등록합니다.

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

서비스 컨테이너에 서비스를 추가하면 앱 내 및 `Configure` 메서드에서 사용할 수 있습니다. 서비스는 [종속성 주입](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 해결됩니다.

`SetCompatibilityVersion`에 대한 자세한 내용은 [SetCompatibilityVersion](xref:mvc/compatibility-version)을 참조하세요.

## <a name="the-configure-method"></a>Configure 메서드

<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드는 앱이 HTTP 요청에 응답하는 방식을 지정하는 데 사용됩니다. 요청 파이프라인은 [미들웨어](xref:fundamentals/middleware/index) 구성 요소를 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 인스턴스에 추가하여 구성됩니다. `IApplicationBuilder`는 `Configure` 메서드에 사용할 수 있지만 서비스 컨테이너에 등록되지 않습니다. 호스팅은 `IApplicationBuilder`를 만들고 `Configure`에 직접 전달합니다.

[ASP.NET Core 템플릿](/dotnet/core/tools/dotnet-new)은 다음을 지원하는 파이프라인을 구성합니다.

* [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)
* [예외 처리기](xref:fundamentals/error-handling#exception-handler-page)
* [HSTS(HTTP 엄격한 전송 보안)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [HTTPS 리디렉션](xref:security/enforcing-ssl)
* [정적 파일](xref:fundamentals/static-files)
* [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)
* ASP.NET Core [MVC](xref:mvc/overview) 및 [Razor Pages](xref:razor-pages/index)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

각 `Use` 확장 메서드는 요청 파이프라인에 하나 이상의 미들웨어 구성 요소를 추가합니다. 예를 들어 `UseMvc` 확장 메서드는 [라우팅 미들웨어](xref:fundamentals/routing)를 요청 파이프라인에 추가하고 기본 처리기로 [MVC](xref:mvc/overview)를 구성합니다.

요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 다음 구성 요소를 호출하거나 적절한 경우 체인을 단락(short-circuiting)하는 일을 담당합니다. 미들웨어 체인을 따라 단락(short-circuiting)이 발생하지 않으면, 각 미들웨어는 클라이언트에 전송되기 전에 요청을 처리할 수 있는 두 번째 기회를 얻게 됩니다.

`IHostingEnvironment` 및 `ILoggerFactory`와 같은 추가 서비스는 `Configure` 메서드 서명에서 지정될 수도 있습니다. 지정되면 사용 가능한 경우 추가 서비스가 삽입됩니다.

`IApplicationBuilder`를 사용하는 방법 및 미들웨어 처리 순서에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.

## <a name="convenience-methods"></a>편리한 메서드

`Startup` 클래스를 사용하지 않고 서비스 및 요청 처리 파이프라인을 구성하려면 호스트 작성기에서 `ConfigureServices` 및 `Configure` 편의성 메서드를 호출합니다. `ConfigureServices`에 대한 여러 호출은 서로 추가합니다. 여러 `Configure` 메서드 호출이 있는 경우 마지막 `Configure` 호출이 사용됩니다.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>시작 필터로 시작 확장

<xref:Microsoft.AspNetCore.Hosting.IStartupFilter>를 사용하여 앱의 [구성](#the-configure-method) 미들웨어 파이프라인의 시작 또는 끝에 미들웨어를 구성합니다. `IStartupFilter`는 미들웨어가 앱의 요청 처리 파이프라인의 시작 또는 끝에 라이브러리에 의해 추가되는 미들웨어 전이나 후에 실행되도록 하는데 유용합니다.

`IStartupFilter`는 `Action<IApplicationBuilder>`를 받고 반환하는 단일 메서드, <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>를 구성합니다. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>는 앱의 요청 파이프라인을 구성하는 클래스를 정의합니다. 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)를 참조하세요.

각 `IStartupFilter`는 요청 파이프라인에서 하나 이상의 미들웨어를 구현합니다. 필터는 서비스 컨테이너에 추가된 순서 대로 호출됩니다. 필터는 다음 필터에 컨트롤을 전달하기 전이나 후에 미들웨어를 추가할 수 있으므로 앱 파이프라인의 시작 또는 끝에 추가합니다.

다음 예제에서는 `IStartupFilter`를 사용하여 미들웨어를 등록하는 방법을 보여줍니다.

`RequestSetOptionsMiddleware` 미들웨어는 쿼리 문자열 매개 변수에서 옵션 값을 설정합니다.

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

`RequestSetOptionsMiddleware`는 `RequestSetOptionsStartupFilter` 클래스에서 구성됩니다.

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter`는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>의 서비스 컨테이너에 등록되고 `Startup`은 `Startup` 클래스 외부에서 보강됩니다.

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

`option`에 대한 쿼리 문자열 매개 변수가 제공되는 경우 미들웨어는 MVC 미들웨어가 응답을 렌더링하기 전에 값 할당을 처리합니다.

![렌더링된 인덱스 페이지를 표시하는 브라우저 창 옵션의 값은 쿼리 문자열 매개 변수 및 'From Middleware'로 설정된 옵션의 값으로 페이지 요청에 따라 'From Middleware'로 렌더링됩니다.](startup/_static/index.png)

미들웨어 실행 순서는 `IStartupFilter` 등록 순서로 설정됩니다.

* 여러 `IStartupFilter` 구현은 동일한 개체와 상호 작용할 수 있습니다. 순서 지정이 중요한 경우 해당 미들웨어가 실행해야 하는 순서와 일치하도록 해당 `IStartupFilter` 서비스 등록의 순서를 지정합니다.
* 라이브러리는 `IStartupFilter`로 등록된 다른 앱 미들웨어 전이나 후에 실행하는 하나 이상의 `IStartupFilter` 구현으로 미들웨어를 추가할 수 있습니다. 라이브러리의 `IStartupFilter`에 의해 추가되는 미들웨어 전에 `IStartupFilter` 미들웨어를 호출하려면 라이브러리가 서비스 컨테이너에 추가되기 전에 서비스 등록의 위치를 지정합니다. 나중에 호출하려면 라이브러리가 추가된 후에 서비스 등록의 위치를 지정합니다.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>외부 어셈블리의 시작 시 구성 추가

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup>구현에서는 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다. 자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>을 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [호스트](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>
