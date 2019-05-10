---
title: Azure App Service에서 ASP.NET Core 문제 해결
author: guardrex
description: ASP.NET Core Azure App Service 배포에 대한 문제 진단 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: 36c2bdfa585a0fd54ca93bf4c0edb4cf6f7d934a
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886908"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a>Azure App Service에서 ASP.NET Core 문제 해결

[Luke Latham](https://github.com/guardrex)으로

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

이 문서에서는 Azure App Service의 진단 도구를 사용하여 ASP.NET Core 앱 시작 문제를 진단하는 방법에 대한 지침을 제공합니다. 추가 문제 해결 권장 사항은 Azure 설명서의 [Azure App Service 진단 개요](/azure/app-service/app-service-diagnostics) 및 [방법: Azure App Service에서 앱 모니터링](/azure/app-service/web-sites-monitor)을 참조하세요.

## <a name="app-startup-errors"></a>앱 시작 오류

**502.5 프로세스 실패** 작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다. 애플리케이션 이벤트 로그를 검토하면 이 유형의 문제를 해결하는 데 도움이 될 수 있습니다. [애플리케이션 이벤트 로그](#application-event-log) 섹션에서는 로그 액세스에 대해 설명합니다.

잘못 구성된 앱으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.

![502.5 프로세스 실패 페이지를 보여주는 브라우저 창](troubleshoot/_static/process-failure-page.png)

**500 내부 서버 오류**

앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.

이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다. 응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다. 애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다. 서버의 관점에서 보면 맞습니다. 앱이 시작되었지만 유효한 응답을 생성할 수 없습니다. [Kudu 콘솔에서 앱을 실행](#run-the-app-in-the-kudu-console)하거나 [ASP.NET Core 모듈 stdout 로그를 사용](#aspnet-core-module-stdout-log)하여 문제를 해결합니다.

**연결 다시 설정**

헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류**를 전송하는 것은 너무 늦은 것입니다. 응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다. 이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다. [애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.

## <a name="default-startup-limits"></a>기본 시작 제한

ASP.NET Core 모듈은 기본 *startupTimeLimit*이 120초로 구성됩니다. 기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다. 모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.

## <a name="troubleshoot-app-startup-errors"></a>앱 시작 오류 해결

### <a name="application-event-log"></a>응용 프로그램 이벤트 로그

애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.

1. Azure Portal에서 **Azure App Services**의 앱을 엽니다.
1. **문제 진단 및 해결**을 선택합니다.
1. **진단 도구** 제목을 선택합니다.
1. **지원 도구**에서 **애플리케이션 이벤트** 단추를 선택합니다.
1. **원본** 열에서 *IIS AspNetCoreModule* 또는 *IIS AspNetCoreModule V2* 항목이 제공하는 최신 오류를 검토합니다.

**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.

1. **개발 도구** 영역에서 **고급 도구**를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.
1. **LogFiles** 폴더를 엽니다.
1. *eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.
1. 로그를 검토합니다. 로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.

### <a name="run-the-app-in-the-kudu-console"></a>Kudu 콘솔에서 앱 실행

애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다. [Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.

1. **개발 도구** 영역에서 **고급 도구**를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.

#### <a name="test-a-32-bit-x86-app"></a>32비트(x86) 앱 테스트

##### <a name="current-release"></a>현재 릴리스

1. `cd d:\home\site\wwwroot`
1. 앱을 실행합니다.
   * 앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * 앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:

     ```console
     {ASSEMBLY NAME}.exe
     ```

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a>미리 보기 릴리스에서 실행되는 프레임워크 종속 배포

ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)
1. 앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.

#### <a name="test-a-64-bit-x64-app"></a>64비트(x64) 앱 테스트

##### <a name="current-release"></a>현재 릴리스

* 앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:
  1. `cd D:\Program Files\dotnet`
  1. 앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* 앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:
  1. `cd D:\home\site\wwwroot`
  1. 앱 실행: `{ASSEMBLY NAME}.exe`

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a>미리 보기 릴리스에서 실행되는 프레임워크 종속 배포

ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)
1. 앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.

### <a name="aspnet-core-module-stdout-log"></a>ASP.NET Core 모듈 stdout 로그

ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다. stdout 로그를 사용하고 보려면:

1. Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.
1. **문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.
1. **추천 솔루션** > **Stdout 로그 리디렉션 사용** 아래에서 **Kudu 콘솔을 열어 Web.Config 편집** 단추를 선택합니다.
1. Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 폴더를 엽니다. 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.
1. *web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.
1. **stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.
1. **저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.
1. 앱에 대한 요청을 실행합니다.
1. Azure Portal로 돌아갑니다. **개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.
1. **LogFiles** 폴더를 선택합니다.
1. **수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.
1. 로그 파일이 열리면 오류가 표시됩니다.

문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.

1. Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다. 연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.
1. **stdoutLogEnabled**를 `false`로 설정합니다.
1. **저장**을 선택하여 파일을 저장합니다.

> [!WARNING]
> stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다. 앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.
>
> 시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log"></a>ASP.NET Core 모듈 디버그 로그

ASP.NET Core 모듈 디버그 로그는 ASP.NET Core 모듈에서 추가로 심층적인 로깅을 제공합니다. stdout 로그를 사용하고 보려면:

1. 개선된 진단 로그를 사용하도록 설정하려면 다음 중 하나를 수행합니다.
   * [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)의 지침에 따라 개선된 진단 로깅을 위한 앱을 구성합니다. 앱을 다시 배포합니다.
   * Kudu 콘솔을 사용하여 [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시되는 `<handlerSettings>`를 라이브 앱의 *web.config* 파일에 추가합니다.
     1. **개발 도구** 영역에서 **고급 도구**를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
     1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.
     1. 폴더를 **site** >  **wwwroot** 경로로 엽니다. 연필 단추를 선택하여 *web.config* 파일을 편집합니다. [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시된 대로 `<handlerSettings>` 섹션을 추가합니다. **저장** 단추를 선택합니다.
1. **개발 도구** 영역에서 **고급 도구**를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.
1. 폴더를 **site** >  **wwwroot** 경로로 엽니다. *aspnetcore debug.log* 파일에 대한 경로를 제공하지 않는 경우 파일이 목록에 나타납니다. 경로를 제공한 경우 로그 파일의 위치로 이동합니다.
1. 파일 이름 옆의 연필 단추를 사용하여 로그 파일을 엽니다.

문제 해결이 완료되면 디버그 로깅을 사용하지 않도록 설정합니다.

1. 향상된 디버그 로그를 사용하지 않도록 설정하려면 다음 중 하나를 수행합니다.
   * *web.config* 파일에서 `<handlerSettings>`를 로컬에서 제거하고 앱을 다시 배포합니다.
   * Kudu 콘솔을 사용하여 *web.config* 파일을 편집하고 `<handlerSettings>` 섹션을 제거합니다. 파일을 저장합니다.

> [!WARNING]
> 디버그 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기에는 제한이 없습니다. 앱 시작 문제를 해결하려면 디버그 로깅만 사용합니다.
>
> 시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

::: moniker-end

## <a name="common-startup-errors"></a>일반 시작 오류

<xref:host-and-deploy/azure-iis-errors-reference>을 참조하세요. 앱 시작을 차단하는 대부분의 일반적인 문제는 참조 항목에서 다룹니다.

## <a name="slow-or-hanging-app"></a>앱이 느리거나 중단됨

요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.

* [Azure App Service에서 느린 웹앱 성능 문제 해결](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

## <a name="remote-debugging"></a>원격 디버깅

다음 항목을 참조하십시오.

* [Visual Studio를 사용하여 Azure App Service에서 웹앱 문제 해결의 원격 디버깅 웹앱 섹션](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)(Azure 설명서)
* [Visual Studio 2017의 Azure에서 IIS 기반 ASP.NET Core 원격 디버그](/visualstudio/debugger/remote-debugging-azure)(Visual Studio 설명서)

## <a name="application-insights"></a>응용 프로그램 정보

[Application Insights](https://azure.microsoft.com/services/application-insights/)에서는 오류 로깅 및 보고 기능을 포함하여 Azure App Service에 호스트된 앱의 원격 분석을 제공합니다. Application Insights는 앱의 로깅 기능을 사용할 수 있게 될 때 애플리케이션이 시작된 후 발생하는 오류만 보고할 수 있습니다. 자세한 내용은 [ASP.NET Core용 Application Insights](/azure/application-insights/app-insights-asp-net-core)를 참조하세요.

## <a name="monitoring-blades"></a>모니터링 블레이드

모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다. 이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.

ASP.NET Core 확장이 설치되어 있는지 확인합니다. 확장이 설치되어 있지 않으면 수동으로 설치합니다.

1. **개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.
1. **ASP.NET Core 확장**이 목록에 표시됩니다.
1. 확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.
1. 목록에서 **ASP.NET Core 확장**을 선택합니다.
1. **확인**을 선택하여 적합한 조건을 적용합니다.
1. **확장 추가** 블레이드에서 **확인**을 선택합니다.
1. 정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.

stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.

1. Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.
1. **site** > **wwwroot** 경로로 폴더를 열고 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.
1. *web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.
1. **stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.
1. **저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.

계속해서 진단 로깅을 활성화합니다.

1. Azure Portal에서 **진단 로그** 블레이드를 선택합니다.
1. **애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지**에 대해 **켜기** 스위치를 선택합니다. 블레이드 위쪽에 있는 **저장** 단추를 선택합니다.
1. FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적**에 대해 **켜기** 스위치를 선택합니다.
1. 포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.
1. 앱에 대한 요청을 실행합니다.
1. 로그 스트림 데이터 내에 오류의 원인이 표시됩니다.

문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다. [ASP.NET Core 모듈 stdout 로그](#aspnet-core-module-stdout-log) 섹션의 지침을 참조하세요.

실패한 요청 추적 로그(FREB 로그)를 보려면:

1. Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.
1. 사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그**를 선택합니다.

자세한 내용은 [Azure App Service 에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ를 참조하세요. 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.

자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.

> [!WARNING]
> stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.
>
> ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Azure App Service에서 느린 웹앱 성능 문제 해결](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure의 웹앱에 대한 애플리케이션 성능 FAQ](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))
* [Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
