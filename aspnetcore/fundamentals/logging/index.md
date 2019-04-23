---
title: ASP.NET Core에 로그인
author: tdykstra
description: ASP.NET Core의 로깅 프레임워크에 대해 알아봅니다. 기본 제공 로깅 공급자를 살펴보고 인기 있는 타사 공급자에 대해 알아봅니다.
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/02/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 8a2e310b47e32e9015b0c127ed79d8f6bdf2e44d
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982855"
---
# <a name="logging-in-aspnet-core"></a>ASP.NET Core에 로그인

작성자: [Steve Smith](https://ardalis.com/) 및 [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core는 다양한 기본 제공 및 타사 로깅 공급자와 함께 작동하는 로깅 API를 지원합니다. 이 문서에서는 기본 제공 공급자에서 로깅 API를 사용하는 방법을 보여줍니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>공급자 추가

로깅 공급자는 로그를 표시하거나 저장합니다. 예를 들어 콘솔 공급자는 콘솔에 로그를 표시하고 Azure Application Insights 공급자는 이를 Azure Application Insights에 저장합니다. 여러 공급자를 추가하여 로그를 여러 대상으로 보낼 수 있습니다.

::: moniker range=">= aspnetcore-2.0"

공급자를 추가하려면 *Program.cs*에서 공급자의 `Add{provider name}` 확장 메서드를 호출합니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=17-19)]

기본 프로젝트 템플릿은 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>를 호출하여 다음과 같은 로깅 공급자를 추가합니다.

* 콘솔
* 디버그
* EventSource(ASP.NET Core 2.2에서 시작)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

`CreateDefaultBuilder`를 사용하는 경우 기본 제공자를 사용자가 선택한 대로 바꿀 수 있습니다. <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>를 호출하여 원하는 공급자를 추가합니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

공급자를 사용하려면 해당 NuGet 패키지를 설치하고 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 인스턴스에서 공급자의 확장 메서드를 호출합니다.

[!code-csharp[](index/samples/1.x/TodoApiSample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

ASP.NET Core [DI(종속성 주입](xref:fundamentals/dependency-injection))는 `ILoggerFactory` 인스턴스를 제공합니다. `AddConsole` 및 `AddDebug` 확장 메서드는 [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) 및 [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) 패키지에 정의됩니다. 각 확장 메서드는 `ILoggerFactory.AddProvider` 메서드를 호출하고, 공급자 인스턴스를 전달합니다.

> [!NOTE]
> [샘플 앱](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x)은 `Startup.Configure` 메서드에서 로그 공급자를 추가합니다. 먼저 실행되는 코드의 로그 출력을 가져오려면 `Startup` 클래스 생성자에 로그 공급자를 추가합니다.

::: moniker-end

문서의 뒷부분에 나오는 [기본 제공 로깅 공급자](#built-in-logging-providers) 및 [타사 로깅 공급자](#third-party-logging-providers)에 대해 자세히 알아봅니다.

## <a name="create-logs"></a>로그 만들기

DI에서 <xref:Microsoft.Extensions.Logging.ILogger%601> 개체를 가져옵니다.

::: moniker range=">= aspnetcore-2.0"

다음 컨트롤러 예제에서는 `Information` 및 `Warning` 로그를 만듭니다. *범주*는 `TodoApiSample.Controllers.TodoController`(샘플 앱에서 `TodoController`의 정규화된 클래스 이름 샘플)입니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

다음 Razor Pages 예제에서는 `Information`을 *수준*으로, `TodoApiSample.Pages.AboutModel`을 *범주*로 사용하는 로그를 만듭니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3, 7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

앞의 예제에서는 `Information` 및 `Warning`을 *수준*으로, `TodoController` 클래스를 *범주*로 사용하는 로그를 만듭니다. 

::: moniker-end

로그 *수준*은 기록된 이벤트의 심각도를 나타냅니다. 로그 *범주*는 각 로그와 연결된 문자열입니다. `ILogger<T>` 인스턴스는 `T` 형식의 정규화된 이름을 가진 로그를 범주로 만듭니다. [수준](#log-level) 및 [범주](#log-category)는 이 문서의 뒷부분에 자세히 설명되어 있습니다. 

::: moniker range=">= aspnetcore-2.0"

### <a name="create-logs-in-startup"></a>시작 시 로그 만들기

`Startup` 클래스에 로그를 작성하려면 생성자 시그니처에 `ILogger` 매개 변수를 포함시킵니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-program"></a>프로그램에 로그 만들기

`Program` 클래스에 로그를 작성하려면 DI에서 `ILogger` 인스턴스를 가져옵니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a>비동기 로거 메서드 없음

로깅은 매우 빨라서 비동기 코드의 성능 비용을 들일 필요가 없습니다. 로깅 데이터 저장소가 느린 경우 직접 작성하지 마세요. 로그 메시지를 처음에 빠른 저장소에 작성한 다음, 나중에 느린 저장소로 이동하는 것이 좋습니다. 예를 들어 SQL Server에 로그하는 경우 `Log` 메서드는 동기식이므로 `Log` 메서드에서 직접 로그하지는 않습니다. 대신 동기적으로 로그 메시지를 메모리 내 큐에 추가하고 백그라운드 작업자가 큐에서 메시지를 풀하여 SQL Server에 대해 비동기 데이터 푸시 작업을 수행하도록 합니다.

## <a name="configuration"></a>구성

로깅 공급자 구성은 하나 이상의 구성 공급자에서 제공합니다.

* 파일 형식(INI, JSON 및 XML).
* 명령줄 인수.
* 환경 변수.
* 메모리 내 .NET 개체.
* 암호화되지 않은 [암호 관리자](xref:security/app-secrets) 스토리지.
* 암호화된 사용자 저장소(예:[Azure Key Vault](xref:security/key-vault-configuration)).
* 사용자 지정 공급자(설치 또는 생성된).

예를 들어 로깅 구성은 일반적으로 앱 설정 파일의 `Logging` 섹션에서 제공됩니다. 다음 예제에서는 일반적인 *appsettings.Development.json* 파일의 콘텐츠를 보여줍니다.

::: moniker range=">= aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

`Logging` 속성은 `LogLevel` 및 로그 공급자 속성을 포함할 수 있습니다(콘솔이 표시됨).

`Logging` 아래의 `LogLevel` 속성은 선택한 범주에 대해 로그할 최소 [수준](#log-level)을 지정합니다. 이 예제에서는 `System` 및 `Microsoft` 범주는 `Information` 수준으로 로그되고 다른 모든 범주는 `Debug` 수준으로 로그됩니다.

`Logging` 아래의 다른 속성은 로깅 공급자를 지정합니다. 이 예제는 콘솔 공급자를 위한 것입니다. 공급자가 [로그 범위](#log-scopes)를 지원하는 경우 `IncludeScopes`는 사용 가능 여부를 나타냅니다. 공급자 속성(예: 예제에서 `Console`)은 `LogLevel` 속성을 지정할 수도 있습니다. 공급자 아래의 `LogLevel`은 해당 공급자에 대한 로그 수준을 지정합니다.

수준이 `Logging.{providername}.LogLevel`에 지정된 경우 `Logging.LogLevel`에 설정된 모든 수준을 재정의합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  }
}
```

`LogLevel` 키는 로그 이름을 나타냅니다. `Default` 키는 명시적으로 나열되지 않은 로그에 적용됩니다. 값은 지정된 로그에 적용된 [로그 수준](#log-level)을 나타냅니다.

::: moniker-end

구성 공급자 구현에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.

## <a name="sample-logging-output"></a>샘플 로깅 출력

이전 섹션에 나와 있는 샘플 코드를 사용하면 명령줄에서 앱을 실행할 때 콘솔에 로그가 표시됩니다. 다음은 콘솔 출력의 예입니다.

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

이전 로그는 `http://localhost:5000/api/todo/0`의 샘플 앱에 HTTP Get 요청을 하여 생성되었습니다.

다음은 Visual Studio에서 샘플 앱을 실행할 때 디버그 창에 나타나는 것과 동일한 로그의 예입니다.

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

이전 섹션에 표시된 `ILogger` 호출을 통해 생성된 로그는 "TodoApi.Controllers.TodoController"로 시작합니다. "Microsoft" 범주로 시작하는 로그는 ASP.NET Core 프레임워크 코드에서 온 것입니다. ASP.NET Core 및 애플리케이션 코드는 동일한 로깅 API와 공급자를 사용합니다.

이 문서의 나머지 부분에서는 로깅에 대한 일부 세부 정보 및 옵션을 설명합니다.

## <a name="nuget-packages"></a>NuGet 패키지

`ILogger` 및 `ILoggerFactory` 인터페이스는 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)에 있으며, 두 인터페이스의 기본 구현은 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)에 있습니다.

## <a name="log-category"></a>로그 범주

`ILogger` 개체가 생성되면 개체에 대한 *범주*가 지정됩니다. 해당 범주는 `ILogger`의 해당 인스턴스에서 만든 각 로그 메시지에 포함됩니다. 범주는 문자열일 수 있지만 "TodoApi.Controllers.TodoController"와 같은 클래스 이름을 사용하는 것이 규칙입니다.

`ILogger<T>`를 사용하여 `T`의 정규화된 형식 이름을 범주로 사용하는 `ILogger` 인스턴스를 가져옵니다.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

범주를 명시적으로 지정하려면 `ILoggerFactory.CreateLogger`를 호출합니다.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>`는 `T`의 정규화된 형식 이름으로 `CreateLogger`를 호출하는 것과 동일합니다.

## <a name="log-level"></a>로그 수준

모든 로그는 <xref:Microsoft.Extensions.Logging.LogLevel> 값을 지정합니다. 로그 수준은 심각도 또는 중요도를 나타냅니다. 예를 들어 메서드가 정상적으로 종료되면 `Information` 로그를 작성하고 메서드가 *404 찾을 수 없음* 상태 코드를 반환할 때 `Warning` 로그를 작성할 수 있습니다.

다음 코드에서는 `Information` 및 `Warning` 코드를 만듭니다.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

이전 코드에서 첫 번째 매개 변수는 [로그 이벤트 ID](#log-event-id)입니다. 두 번째 매개 변수는 나머지 메서드 매개 변수가 제공하는 인수 값에 대한 자리 표시자가 있는 메시지 템플릿입니다. 메서드 매개 변수는 이 문서의 뒷부분에 있는 [메시지 템플릿 섹션](#log-message-template)에 설명되어 있습니다.

메서드 이름(예: `LogInformation` 및 `LogWarning`)의 수준을 포함하는 로그 메서드는 [ILogger에 대한 확장 메서드](xref:Microsoft.Extensions.Logging.LoggerExtensions)입니다. 이러한 메서드는 `LogLevel` 매개 변수를 사용하는 `Log` 메서드를 호출합니다. 이러한 확장 메서드 중 하나를 호출하는 대신 `Log` 메서드를 직접 호출할 수 있지만, 구문이 비교적 복잡합니다. 자세한 내용은 <xref:Microsoft.Extensions.Logging.ILogger> 및 [로거 확장 소스 코드](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)를 참조하세요.

ASP.NET Core는 다음 로그 수준을 정의하며, 여기에 가장 낮은 심각도에서 가장 높은 심각도 순으로 정렬됩니다.

* 추적 = 0

  일반적으로 디버깅에만 유용한 정보입니다. 이러한 메시지는 중요한 애플리케이션 데이터를 포함할 수 있으므로 프로덕션 환경에서 사용하면 안 됩니다. *기본적으로 사용하지 않도록 설정됩니다.*

* 디버그 = 1

  개발 및 디버깅에 유용할 수 있는 정보입니다. 예제: `Entering method Configure with flag set to true.` 로그 볼륨이 크기 때문에 문제 해결 시에만 `Debug` 수준 로그를 프로덕션 환경에서 사용합니다.

* 정보 = 2

  앱의 일반적인 흐름을 추적합니다. 이러한 로그는 일반적으로 장기적인 가치가 있습니다. 예: `Request received for path /api/todo`

* 경고 = 3

  앱 흐름에 비정상적이거나 예기치 않은 이벤트가 있습니다. 앱이 중지되지 않지만 조사해야 하는 오류 또는 기타 조건이 여기에 포함될 수 있습니다. 처리된 예외는 `Warning` 로그 수준을 사용하는 일반적인 장소입니다. 예: `FileNotFoundException for file quotes.txt.`

* 오류 = 4

  처리할 수 없는 오류 및 예외입니다. 이러한 메시지는 전체 앱 오류가 아닌 현재 동작 또는 작업(예: 현재 HTTP 요청)의 오류를 의미합니다. 예제 로그 메시지: `Cannot insert record due to duplicate key violation.`

* 중요 = 5

  즉각적인 대응이 필요한 오류입니다. 예: 데이터 손실 시나리오, 디스크 공간 부족.

로그 수준을 사용하여 특정 스토리지 매체 또는 디스플레이 창에 기록되는 로그 출력의 양을 제어합니다. 예:

* 프로덕션 환경에서 `Trace` ~ `Information` 수준을 볼륨 데이터 저장소로 보냅니다. 값 데이터 저장소에 `Warning` ~ `Critical`을 보냅니다.
* 개발 중에 `Warning` ~ `Critical`을 콘솔에 보내고 문제 해결 시 `Trace` ~ `Information`을 추가합니다.

이 문서의 뒷부분에 나오는 [로그 필터링](#log-filtering) 섹션에서는 공급자가 처리하는 로그 수준을 제어하는 방법을 설명합니다.

ASP.NET Core는 프레임워크 이벤트에 대한 로그를 작성합니다. 이 문서 앞부분에 나온 로그 예제는 `Information` 수준 미만 로그를 제외했기 때문에 `Debug` 또는 `Trace` 수준 로그가 생성되지 않습니다. 다음은 `Debug` 로그를 표시하도록 구성된 샘플 앱을 실행하여 생성된 콘솔 로그의 예입니다.

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>로그 이벤트 ID

각 로그는 *이벤트 ID*를 지정할 수 있습니다. 샘플 앱은 로컬로 정의된 `LoggingEvents` 클래스를 사용하여 이 작업을 수행합니다.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

이벤트 ID는 이벤트 집합을 연결합니다. 예를 들어 페이지에서 항목 목록을 표시하는 것과 관련된 모든 로그는 1001일 수 있습니다.

로깅 공급자는 이벤트 ID를 ID 필드, 로그 메시지에 저장하거나 전혀 저장할 수 없습니다. 디버그 공급자는 이벤트 ID를 표시하지 않습니다. 콘솔 공급자는 범주 뒤에 대괄호로 이벤트 ID를 표시합니다.

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>로그 메시지 템플릿

각 로그는 메시지 템플릿을 지정합니다. 메시지 템플릿에는 인수가 제공되는 자리 표시자를 포함할 수 있습니다. 숫자가 아니라 자리 표시자의 이름을 사용합니다.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

값을 제공하는 데 사용되는 매개 변수를 결정하는 것은 자리 표시자의 번호가 아닌 이름입니다. 다음 코드에서는 매개 변수 이름이 메시지 템플릿 순서를 벗어났습니다.

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

이 코드는 매개 변수 값을 순서대로 사용하여 로그 메시지를 만듭니다.

```
Parameter values: parm1, parm2
```

로깅 프레임워크는 로깅 공급자가 [구조화된 로깅이라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 구현할 수 있도록 이 방식으로 작동합니다. 인수 자체는 서식이 지정된 메시지 템플릿뿐만 아니라 로깅 시스템에 전달됩니다. 이 정보를 통해 로깅 공급자는 매개 변수 값을 필드로 저장할 수 있습니다. 예를 들어 로거 메서드 호출이 다음과 같다고 가정합니다.

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

Azure Table Storage에 로그를 보내는 경우 각 Azure Table 엔터티는 로그 데이터에 대한 쿼리를 간소화하는 `ID` 및 `RequestTime` 속성을 가질 수 있습니다. 쿼리는 문자 메시지의 시간 초과를 구문 분석하지 않고 특정 `RequestTime` 범위 내의 모든 로그를 찾을 수 있습니다.

## <a name="logging-exceptions"></a>로깅 처리

로거 메서드는 다음 예제와 같이 예외를 전달할 수 있는 오버로드를 갖고 있습니다.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

공급자들은 다양한 방식으로 예외 정보를 처리합니다. 다음은 위에서 보여드린 코드의 디버그 공급자 출력의 예제입니다.

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>로깅 필터링

::: moniker range=">= aspnetcore-2.0"

특정 공급자 및 범주 또는 모든 공급자나 모든 범주의 최소 로그 수준을 지정할 수 있습니다. 최소 수준 미만인 로그는 해당 공급자에게 전달되지 않으므로 표시되거나 저장되지 않습니다.

모든 로그를 표시하지 않으려면 `LogLevel.None`을 최소 로그 수준으로 지정합니다. `LogLevel.None`의 정수 값은 `LogLevel.Critical`(5)보다 높은 6입니다.

### <a name="create-filter-rules-in-configuration"></a>구성에서 필터 규칙 만들기

프로젝트 템플릿 코드는 `CreateDefaultBuilder`를 호출하여 콘솔 및 디버그 공급자에 대한 로깅을 설정합니다. 또한 `CreateDefaultBuilder` 메서드는 다음과 같은 코드를 사용하여 `Logging` 섹션에서 구성을 조회하는 로깅을 설정합니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=16)]

구성 데이터는 다음 예제와 같이 공급자 및 범주별로 최소 로그 수준을 지정합니다.

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

이 JSON은 6개의 필터 규칙을 만드는데, 하나는 디버그 공급자용이고, 4개는 콘솔 공급자용이고, 나머지 하나는 모든 공급자용입니다. `ILogger` 개체가 생성될 때 각 공급자에 대해 단일 규칙이 선택됩니다.

### <a name="filter-rules-in-code"></a>코드의 필터 규칙

다음 예제에서는 코드에 필터 규칙을 등록하는 방법을 보여줍니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

두 번째 `AddFilter`는 형식 이름을 사용하여 디버그 공급자를 지정합니다. 첫 번째 `AddFilter`는 공급자 형식을 지정하지 않으며, 따라서 모든 공급자에 적용됩니다.

### <a name="how-filtering-rules-are-applied"></a>필터링 규칙 적용 방식

이전 예제의 구성 데이터 및 `AddFilter` 코드는 다음 표에 표시된 규칙을 만듭니다. 처음 6개는 구성 예제에서 가져온 것이고 마지막 2개는 코드 예제에서 가져온 것입니다.

| 수 | 공급자      | 다음으로 시작하는 범주...          | 최소 로그 수준 |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | 디버그         | 모든 범주                          | 정보       |
| 2      | 콘솔       | Microsoft.AspNetCore.Mvc.Razor.Internal | 경고           |
| 3      | 콘솔       | Microsoft.AspNetCore.Mvc.Razor.Razor    | 디버그             |
| 4      | 콘솔       | Microsoft.AspNetCore.Mvc.Razor          | 오류             |
| 5      | 콘솔       | 모든 범주                          | 정보       |
| 6      | 모든 공급자 | 모든 범주                          | 디버그             |
| 7      | 모든 공급자 | 시스템                                  | 디버그             |
| 8      | 디버그         | Microsoft                               | 추적             |

`ILogger` 개체를 만들 때 `ILoggerFactory` 개체는 공급자마다 해당 로거에 적용할 단일 규칙을 선택합니다. `ILogger` 인스턴스에서 작성된 모든 메시지는 선택한 규칙에 따라 필터링됩니다. 사용 가능한 규칙 중에서 각 공급자 및 범주 쌍에 적용 가능한 가장 구체적인 규칙이 선택됩니다.

지정된 범주에 대한 `ILogger`가 생성되면 다음 알고리즘이 각 공급자에 사용됩니다.

* 공급자 또는 공급자의 별칭과 일치하는 모든 규칙을 선택합니다. 일치하는 규칙이 없는 경우 빈 공급자가 있는 모든 규칙을 선택합니다.
* 앞 단계의 결과에서 일치하는 범주 접두사가 가장 긴 규칙을 선택합니다. 일치하는 규칙이 없는 경우 범주를 지정하지 않는 규칙을 모두 선택합니다.
* 여러 규칙을 선택하는 경우 **마지막** 규칙을 사용합니다.
* 규칙을 선택하지 않는 경우 `MinimumLevel`을 사용합니다.

앞의 규칙 목록을 통해 "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" 범주에 대한 `ILogger` 개체를 만든다고 가정합니다.

* 디버그 공급자에게는 규칙 1, 6, 8이 적용됩니다. 규칙 8이 가장 구체적이므로 선택됩니다.
* 콘솔 공급자에게는 규칙 3, 4, 5, 6이 적용됩니다. 규칙 3이 가장 구체적입니다.

결과 `ILogger` 인스턴스는 `Trace` 수준 이상의 로그를 디버그 공급자에게 보냅니다. `Debug` 수준 이상의 로그가 콘솔 공급자에게 전송됩니다.

### <a name="provider-aliases"></a>공급자 별칭

각 공급자는 정규화된 형식 이름 대신 구성에서 사용할 수 있는 *별칭*을 정의합니다.  기본 공급자의 경우 다음 별칭을 사용합니다.

* 콘솔
* 디버그
* EventLog
* AzureAppServicesFile
* AzureAppServicesBlob
* TraceSource
* EventSource

### <a name="default-minimum-level"></a>기본 최소 수준

특정 공급자 및 범주에 대한 구성 또는 코드의 규칙이 적용되지 않는 경우에만 효력이 있는 최소 수준 설정이 있습니다. 다음 예제는 최소 수준을 설정하는 방법을 보여 줍니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

최소 수준을 명시적으로 설정하지 않으면 `Trace` 및 `Debug` 로그를 무시하는 `Information`이 기본값으로 설정됩니다.

### <a name="filter-functions"></a>필터 함수

필터 함수는 구성 또는 코드를 통해 규칙이 할당되지 않은 모든 공급자와 범주에 대해 호출됩니다. 함수의 코드는 공급자 형식, 범주 및 로그 수준에 액세스할 수 있습니다. 예:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

일부 로깅 공급자는 로그 수준 및 범주에 따라 로그를 스토리지 매체에 기록할 것인지 아니면 무시할 것인지를 개발자가 지정할 수 있게 해줍니다.

`AddConsole` 및 `AddDebug` 확장 메서드는 필터링 조건을 허용하는 오버로드를 제공합니다. 다음 샘플 코드는 콘솔 공급자가 `Warning` 수준 미만의 로그를 무시하게 만들고, 디버그 공급자는 프레임워크에서 만드는 로그를 무시합니다.

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

`AddEventLog` 메서드는 `EventLogSettings` 인스턴스를 사용하는 오버로드를 갖고 있으며, 이 인스턴스의 `Filter` 속성에는 필터링 함수가 포함될 수 있습니다. TraceSource 공급자는 오버로드를 제공하지 않습니다. 로깅 수준 및 기타 매개 변수가 사용하는 `SourceSwitch` 및 `TraceListener`를 기반으로 하기 때문입니다.

`ILoggerFactory` 인스턴스에 등록된 모든 공급자에 대한 필터링 규칙을 설정하려면 `WithFilter` 확장 방법을 사용합니다. 아래 예제는 앱 코드로 생성된 로그에 대한 디버그 수준에서 로깅하는 동안 프레임워크 로그(범주가 "Microsoft" 또는 "시스템"으로 시작)를 경고로 제한합니다.

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

로그가 기록되지 않도록 하려면 `LogLevel.None`을 최소 로그 수준으로 지정합니다. `LogLevel.None`의 정수 값은 `LogLevel.Critical`(5)보다 높은 6입니다.

`WithFilter` 확장 메서드는 [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet 패키지에서 제공합니다. 이 메서드는 등록된 모든 로거 공급자로 전달되는 메시지를 필터링하는 새로운 `ILoggerFactory` 인스턴스를 반환합니다. 원래 `ILoggerFactory` 인스턴스를 포함하여 어떤 `ILoggerFactory` 인스턴스에도 영향을 주지 않습니다.

::: moniker-end

## <a name="system-categories-and-levels"></a>시스템 범주 및 수준

다음은 ASP.NET Core 및 Entity Framework Core에서 사용되는 몇 가지 범주로, 예상되는 로그에 대한 참고 사항입니다.

| 범주                            | 참고 사항 |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | 일반 ASP.NET Core 진단. |
| Microsoft.AspNetCore.DataProtection | 고려되고, 발견되고, 사용된 키. |
| Microsoft.AspNetCore.HostFiltering  | 호스트가 허용됩니다. |
| Microsoft.AspNetCore.Hosting        | HTTP 요청을 완료하는 데 걸린 시간과 시작 시간. 로드된 호스팅 시작 어셈블리. |
| Microsoft.AspNetCore.Mvc            | MVC 및 Razor 진단. 모델 바인딩, 필터 실행, 뷰 컴파일 작업 선택. |
| Microsoft.AspNetCore.Routing        | 경로 일치 정보. |
| Microsoft.AspNetCore.Server         | 연결 시작, 중지 및 활성 응답 유지. HTTPS 인증서 정보. |
| Microsoft.AspNetCore.StaticFiles    | 파일이 제공되었습니다. |
| Microsoft.EntityFrameworkCore       | 일반 Entity Framework Core 진단. 데이터베이스 작업 및 구성, 변경 내용 검색, 마이그레이션. |

## <a name="log-scopes"></a>로그 점수

 *범위*는 논리적 작업 집합을 그룹화할 수 있습니다. 이 그룹화는 집합의 일부로 생성된 각 로그에 동일한 데이터를 연결하는 데 사용될 수 있습니다. 예를 들어 트랜잭션 처리의 일부로 생성되는 모든 로그에는 트랜잭션 ID가 포함될 수 있습니다.

범위는 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 메서드에서 반환하는 `IDisposable` 형식이며 삭제될 때까지 유지됩니다. `using` 블록에 로거 호출을 래핑하여 범위를 사용합니다.

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

다음은 콘솔 공급자에 대한 범위를 사용하도록 설정하는 코드입니다.

::: moniker range="> aspnetcore-2.0"

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> 범위 기반 로깅을 사용하려면 `IncludeScopes` 콘솔 로거 옵션을 구성해야 합니다.
>
> 구성에 대한 자세한 내용은 [구성](#configuration) 섹션을 참조하세요.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> 범위 기반 로깅을 사용하려면 `IncludeScopes` 콘솔 로거 옵션을 구성해야 합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

*Startup.cs*:

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

각 로그 메시지는 범위 정보를 포함하고 있습니다.

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>기본 로깅 공급자

ASP.NET Core는 다음 공급자를 제공합니다.

* [콘솔](#console-provider)
* [디버그](#debug-provider)
* [EventSource](#eventsource-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)

[Azure에서 로깅](#logging-in-azure)에 대한 옵션은 이 문서의 뒷부분에 나와 있습니다.

stdout 로깅에 대한 자세한 내용은 <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> 및 <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>을 참조하세요.

### <a name="console-provider"></a>콘솔 공급자

[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 공급자 패키지는 콘솔에 로그 출력을 보냅니다. 

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddConsole();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddConsole();
```

[AddConsole 오버로드](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions)를 사용하여 최소 로그 수준, 필터 함수 및 범위 지원 여부를 나타내는 부울을 전달할 수 있습니다. 다른 옵션으로는 `IConfiguration` 개체를 전달할 수 있으며, 이 개체는 범위 지원 및 로깅 수준을 지정할 수 있습니다.

콘솔 공급자는 성능에 상당한 영향을 미치며 일반적으로 프로덕션 환경에서 사용하기에 적합하지 않습니다.

Visual Studio에서 새 프로젝트를 만들면 `AddConsole` 메서드는 다음과 같습니다.

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

이 코드는 *appSettings.json* 파일의 `Logging` 섹션을 참조합니다.

[!code-json[](index/samples/1.x/TodoApiSample//appsettings.json)]

[로그 필터링](#log-filtering) 섹션에서 설명했듯이, 제한 프레임워크를 표시한 설정은 경고에 기록되는 한편 앱이 디버그 수준에서 기록하는 것을 허용합니다. 자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.

::: moniker-end

콘솔 로깅 출력을 보려면 프로젝트 폴더에서 명령 프롬프트를 열고 다음 명령을 실행합니다.

```console
dotnet run
```

### <a name="debug-provider"></a>디버그 공급자

[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 공급자 패키지는 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 클래스(`Debug.WriteLine` 메서드 호출)를 사용하여 로그 출력을 씁니다.

Linux에서 이 공급자는 */var/log/message*에 로그를 씁니다.

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddDebug();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddDebug();
```

[AddDebug 오버로드](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions)를 사용하여 최소 수준 로그 또는 필터 함수를 전달할 수 있습니다.

::: moniker-end

### <a name="eventsource-provider"></a>EventSource 공급자

ASP.NET Core 1.1.0 이상을 대상으로 하는 앱의 경우 [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 공급자 패키지가 이벤트 추적을 구현할 수 있습니다. Windows에서는 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)를 사용합니다. 플랫폼 간 공급자이지만 이벤트를 수집하지 않으며 Linux 또는 macOS용 도구를 표시합니다.

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventSourceLogger();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventSourceLogger();
```

::: moniker-end

로그를 수집하고 보는 좋은 방법은 [PerfView 유틸리티](https://github.com/Microsoft/perfview)를 사용하는 것입니다. ETW 로그를 보는 다른 도구가 있지만, PerfView는 ASP.NET에서 내보내는 ETW 이벤트를 처리하기에 가장 좋은 환경을 제공합니다.

이 공급자가 기록한 이벤트를 수집하도록 PerfView를 구성하려면 **추가 공급자** 목록에 `*Microsoft-Extensions-Logging` 문자열을 추가합니다. (문자열의 시작 부분에 별표를 누락하지 마세요.)

![Perfview 추가 공급자](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Windows EventLog 공급자

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 공급자 패키지는 Windows 이벤트 로그에 로그 출력을 보냅니다.

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventLog();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventLog();
```

[AddEventLog 오버로드](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)를 사용하여 `EventLogSettings` 또는 최소 로그 수준을 전달할 수 있습니다.

::: moniker-end

### <a name="tracesource-provider"></a>TraceSource 공급자

[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 공급자 패키지는 <xref:System.Diagnostics.TraceSource> 라이브러리 및 공급자를 사용합니다.

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddTraceSource(sourceSwitchName);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

::: moniker-end

[AddTraceSource 오버로드](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)를 사용하여 원본 스위치 및 추적 수신기를 전달할 수 있습니다.

이 공급자를 사용하려면 앱이 .NET Framework(.NET Core가 아닌)에서 실행되어야 합니다. 공급자는 샘플 앱에 사용된 <xref:System.Diagnostics.TextWriterTraceListener>와 같은 다양한 [수신기](/dotnet/framework/debug-trace-profile/trace-listeners)로 메시지를 라우팅할 수 있습니다.

::: moniker range="< aspnetcore-2.0"

다음은 `Warning` 이상 메시지를 콘솔 창에 기록하는 `TraceSource` 공급자를 구성하는 예제입니다.

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

::: moniker-end

## <a name="logging-in-azure"></a>Azure에서 로깅

Azure에서 로깅에 대한 자세한 내용은 다음 섹션을 참조하세요.

* [Azure App Service 공급자](#azure-app-service-provider)
* [Azure 로그 스트리밍](#azure-log-streaming)

::: moniker range=">= aspnetcore-1.1"

* [Azure Application Insights 추적 로깅](#azure-application-insights-trace-logging)

::: moniker-end

### <a name="azure-app-service-provider"></a>Azure App Service 공급자

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 공급자 패키지는 Azure App Service 앱의 파일 시스템과 Azure Storage 계정의 [Blob 스토리지](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)에 텍스트 파일을 기록합니다. 공급자 패키지는 .NET Core 1.1 이상을 대상으로 하는 앱에 사용할 수 있습니다.

::: moniker range=">= aspnetcore-2.0"

.NET Core를 대상으로 하는 경우 다음 사항에 유의합니다.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* 공급자 패키지는 ASP.NET Core [Microsoft.AspNetCore.All 메타패키지](xref:fundamentals/metapackage)에 포함되어 있습니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* 공급자 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있지 않습니다. 공급자를 사용하려면 패키지를 설치합니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

.NET Framework를 대상으로 지정하거나 `Microsoft.AspNetCore.App` 메타패키지를 참조하는 경우 패키지 공급자를 프로젝트에 추가합니다. `AddAzureWebAppDiagnostics` 호출:

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="= aspnetcore-1.1"

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> 오버로드로 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>를 전달할 수 있습니다. 설정 개체는 로깅 출력 템플릿, BLOB 이름, 파일 크기 제한과 같은 기본 설정을 재정의할 수 있습니다. (*출력 템플릿*은 `ILogger` 메서드를 호출과 함께 제공되는 것 이외에 모든 로그에 적용되는 메시지 템플릿입니다.)

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

공급자 설정을 구성하려면 다음 예제와 같이 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 및 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>를 사용합니다.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

App Service 앱에 배포할 때 애플리케이션은 Azure Portal **App Service** 페이지의 [진단 로그](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) 섹션에 있는 설정을 따릅니다. 이러한 설정을 업데이트하는 경우 앱을 다시 시작하거나 재배포하지 않아도 변경 내용은 즉시 적용됩니다.

![Azure 로깅 설정](index/_static/azure-logging-settings.png)

로그 파일의 기본 위치는 *D:\\home\\LogFiles\\Application* 폴더이며, 기본 파일 이름은 *diagnostics-yyyymmdd.txt*입니다. 기본 파일 크기 제한은 10MB이고, 보존되는 기본 최대 파일 수는 2입니다. 기본 BLOB 이름은 *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*입니다.

공급자는 프로젝트가 Azure 환경에서 실행되는 경우에만 작동합니다. 프로젝트를 로컬로 실행하는 경우에는 아무 영향도 없습니다&mdash;Blob에 대한 로컬 파일 또는 로컬 개발 스토리지에 기록하지 않습니다.

### <a name="azure-log-streaming"></a>Azure 로그 스트리밍

Azure 로그 스트리밍을 통해 로그 작업을 실시간으로 볼 수 있습니다.

* 앱 서버
* 웹 서버
* 실패한 요청 추적

Azure 로그 스트리밍을 구성하려면:

* 앱의 포털 페이지에서 **진단 로그** 페이지로 이동합니다.
* **애플리케이션 로깅(파일 시스템)** 을 **On**으로 설정합니다.

![Azure Portal 진단 로그 페이지](index/_static/azure-diagnostic-logs.png)

**로그 스트리밍** 페이지로 이동하여 앱 메시지를 봅니다. 앱이 `ILogger` 인터페이스를 통해 기록한 것입니다.

![Azure Portal 애플리케이션 로그 스트리밍](index/_static/azure-log-streaming.png)

::: moniker range=">= aspnetcore-1.1"

### <a name="azure-application-insights-trace-logging"></a>Azure Application Insights 추적 로깅

Application Insights SDK는 ASP.NET Core 로깅 인프라에 생성된 로그를 수집하고 보고할 수 있습니다. 자세한 내용은 다음 리소스를 참조하세요.

* [Application Insights 개요](/azure/application-insights/app-insights-overview)
* [ASP.NET Core용 Application Insights](/azure/application-insights/app-insights-asp-net-core)
* [Application Insights 로깅 어댑터](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md).
* [Application Insights ILogger 구현 샘플](/azure/azure-monitor/app/ilogger)

::: moniker-end

## <a name="third-party-logging-providers"></a>타사 로깅 공급자

ASP.NET Core와 호환되는 타사 로깅 프레임워크는 다음과 같습니다.

* [elmah.io](https://elmah.io/)([GitHub 리포지토리](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub 리포지토리](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](http://jsnlog.com/)([GitHub 리포지토리](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/)([GitHub 리포지토리](https://github.com/catalingavan/KissLog-net))
* [Loggr](http://loggr.net/)([GitHub 리포지토리](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](http://nlog-project.org/)([GitHub 리포지토리](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/)([GitHub 리포지토리](https://github.com/serilog/serilog-extensions-logging))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)([Github 리포지토리](https://github.com/googleapis/google-cloud-dotnet))

일부 타사 프레임워크는 [구조적 로깅이라고 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 수행할 수 있습니다.

타사 프레임워크를 사용하는 방법은 다음과 같이 기본 공급자 중 하나를 사용하는 방법과 비슷합니다.

1. 프로젝트에 NuGet 패키지를 추가합니다.
1. `ILoggerFactory`를 호출합니다.

자세한 내용은 각 공급자의 설명서를 참조하세요. 타사 로깅 공급자는 Microsoft에서 지원되지 않습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/logging/loggermessage>
