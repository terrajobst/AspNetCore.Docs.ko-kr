---
title: IIS에서 ASP.NET Core 문제 해결
author: guardrex
description: ASP.NET Core 앱의 IIS(인터넷 정보 서비스) 배포에 대한 문제 진단 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 03/14/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: f89eac3ae6fc704bc8bf38a9707fc3c6c3568e91
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64884348"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a>IIS에서 ASP.NET Core 문제 해결

[Luke Latham](https://github.com/guardrex)으로

이 문서에서는 [IIS(인터넷 정보 서비스)](/iis)를 통해 호스트할 경우 ASP.NET Core 앱 시작 문제를 진단하는 방법에 대한 지침을 제공합니다. 이 문서의 정보는 Windows Server 및 Windows 데스크톱의 IIS에서 호스트하는 경우에 적용됩니다.

::: moniker range=">= aspnetcore-2.2"

Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다. 로컬에서 디버그할 때 발생하는 *502.5 - 프로세스 실패* 또는 *500.30 - 시작 실패*는 이 항목의 권장 사항을 사용하여 해결할 수 있습니다.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다. 로컬에서 디버그할 때 발생하는 *502.5 프로세스 실패*는 이 항목의 권장 사항을 사용하여 해결할 수 있습니다.

::: moniker-end

추가 문제 해결 항목:

<xref:host-and-deploy/azure-apps/troubleshoot>App Service는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 IIS를 사용하여 앱을 호스트하지만 App Service 관련 지침은 전용 항목을 참조하세요.

<xref:fundamentals/error-handling>로컬 시스템에서 개발하는 동안 ASP.NET Core 앱에서 오류를 처리하는 방법을 알아봅니다.

[Visual Studio를 사용하여 디버그하는 방법 알아보기](/visualstudio/debugger/getting-started-with-the-debugger) 이 항목에서는 Visual Studio 디버거의 기능을 소개합니다.

[Visual Studio Code를 사용한 디버깅](https://code.visualstudio.com/docs/editor/debugging) Visual Studio Code에 기본 제공되는 디버깅 지원을 알아봅니다.

## <a name="app-startup-errors"></a>앱 시작 오류

### <a name="5025-process-failure"></a>502.5 프로세스 실패

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

ASP.NET Core 모듈이 백엔드 dotnet 프로세스를 시작하려고 하지만 시작할 수 없습니다. 프로세스 시작 실패의 원인은 일반적으로 [애플리케이션 이벤트 로그](#application-event-log) 및 [ASP.NET Core 모듈 stdout 로그](#aspnet-core-module-stdout-log)의 항목에서 확인할 수 있습니다.

일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다. 대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다. 공유 프레임워크는 머신에 설치되고 메타패키지(예: `Microsoft.AspNetCore.App`)에서 참조되는 어셈블리(*.dll* 파일) 세트입니다. 메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다. 자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.

호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.

![502.5 프로세스 실패 페이지를 보여주는 브라우저 창](troubleshoot/_static/process-failure-page.png)

::: moniker range=">= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a>500.30 In-Process 시작 실패

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

ASP.NET Core 모듈이 .NET Core CLR in-process를 시작하려고 하지만 시작할 수 없습니다. 프로세스 시작 실패의 원인은 일반적으로 [애플리케이션 이벤트 로그](#application-event-log) 및 [ASP.NET Core 모듈 stdout 로그](#aspnet-core-module-stdout-log)의 항목에서 확인할 수 있습니다.

일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다. 대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.

### <a name="5000-in-process-handler-load-failure"></a>500.0 In-Process 처리기 로드 실패

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

ASP.NET Core 모듈이 .NET Core CLR을 찾지 못하고 in-process 요청 처리기(*aspnetcorev2_inprocess.dll*)를 찾지 못했습니다. 다음을 확인합니다.

* 앱은 [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet 패키지 또는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app) 중 하나를 대상으로 합니다.
* 앱이 대상으로 하는 ASP.NET Core 공유 프레임워크의 버전이 대상 머신에 설치됩니다.

### <a name="5000-out-of-process-handler-load-failure"></a>500.0 Out-Of-Process 처리기 로드 실패

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

ASP.NET Core 모듈이 out-of-process 호스팅 요청 처리기를 찾지 못했습니다. *aspnetcorev2_outofprocess.dll*이 *aspnetcorev2.dll* 옆의 하위 폴더에 있는지 확인하세요.

::: moniker-end

### <a name="500-internal-server-error"></a>500 내부 서버 오류

앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.

이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다. 응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다. 애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다. 서버의 관점에서 보면 맞습니다. 앱이 시작되었지만 유효한 응답을 생성할 수 없습니다. 서버의 [명령 프롬프트에서 앱을 실행](#run-the-app-at-a-command-prompt)하거나 [ASP.NET Core 모듈 stdout 로그를 사용](#aspnet-core-module-stdout-log)하여 문제를 해결합니다.

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

앱의 어셈블리(*.dll*)를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.

게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.

앱 풀의 32비트 설정이 올바른지 확인합니다.

1. IIS 관리자의 **애플리케이션 풀**에서 앱 풀을 선택합니다.
1. **작업** 패널의 **애플리케이션 풀 편집**에서 **고급 설정**을 선택합니다.
1. **32비트 애플리케이션 사용**을 설정합니다.
   * 32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.
   * 64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.

### <a name="connection-reset"></a>연결 다시 설정

헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류**를 전송하는 것은 너무 늦은 것입니다. 응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다. 이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다. [애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.

## <a name="default-startup-limits"></a>기본 시작 제한

ASP.NET Core 모듈은 기본 *startupTimeLimit*이 120초로 구성됩니다. 기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다. 모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.

## <a name="troubleshoot-app-startup-errors"></a>앱 시작 오류 해결

### <a name="enable-the-aspnet-core-module-debug-log"></a>ASP.NET Core 모듈 디버그 로그 사용

앱의 *web.config* 파일에 다음 처리기 설정을 추가하여 ASP.NET Core 모듈 디버그 로그를 사용합니다.

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

로그에 대해 지정된 경로가 있는지 및 앱 풀의 ID에 해당 위치에 대한 쓰기 권한이 있는지 확인합니다.

### <a name="application-event-log"></a>응용 프로그램 이벤트 로그

애플리케이션 이벤트 로그에 액세스합니다.

1. [시작] 메뉴를 열고 **이벤트 뷰어**를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.
1. **이벤트 뷰어**에서 **Windows 로그** 노드를 엽니다.
1. **애플리케이션**을 선택하여 애플리케이션 이벤트 로그를 엽니다.
1. 실패한 앱과 연결된 오류를 검색합니다. 오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.

### <a name="run-the-app-at-a-command-prompt"></a>명령 프롬프트에서 앱 실행

애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다. 호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.

#### <a name="framework-dependent-deployment"></a>프레임워크 종속 배포

앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:

1. 명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe*로 앱의 어셈블리를 실행하여 앱을 실행합니다. `dotnet .\<assembly_name>.dll` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.
1. 오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.
1. 앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다. 기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다. 앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.

#### <a name="self-contained-deployment"></a>자체 포함 배포

앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:

1. 명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다. `<assembly_name>.exe` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.
1. 오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.
1. 앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다. 기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다. 앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.

### <a name="aspnet-core-module-stdout-log"></a>ASP.NET Core 모듈 stdout 로그

stdout 로그를 사용하고 보려면:

1. 호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.
1. *logs* 폴더가 없으면 폴더를 만듭니다. MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.
1. *web.config* 파일을 편집합니다. **stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`). 경로의 `stdout`은 로그 파일 이름 접두사입니다. 로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다. `stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log*로 지정됩니다.
1. 애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.
1. 업데이트된 *web.config* 파일을 저장합니다.
1. 앱에 대한 요청을 실행합니다.
1. *logs* 폴더로 이동합니다. 가장 최근의 stdout 로그를 찾아서 엽니다.
1. 오류에 대한 로그를 검토합니다.

> [!IMPORTANT]
> 문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.

1. *web.config* 파일을 편집합니다.
1. **stdoutLogEnabled**를 `false`로 설정합니다.
1. 파일을 저장합니다.

> [!WARNING]
> stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.
>
> ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

## <a name="enable-the-developer-exception-page"></a>개발자 예외 페이지 사용

`ASPNETCORE_ENVIRONMENT` [환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)하여 개발 환경에서 앱을 실행할 수 있습니다. 앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다. 문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다. *web.config*에서 환경 변수를 설정하는 방법에 대한 자세한 내용은[aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.

## <a name="common-startup-errors"></a>일반 시작 오류

<xref:host-and-deploy/azure-iis-errors-reference>을 참조하세요. 앱 시작을 차단하는 대부분의 일반적인 문제는 참조 항목에서 다룹니다.

## <a name="obtain-data-from-an-app"></a>앱에서 데이터 얻기

앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다. 자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.

## <a name="create-a-dump"></a>덤프 만들기

*덤프*는 시스템 메모리의 스냅숏이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.

### <a name="app-crashes-or-encounters-an-exception"></a>앱 충돌 또는 예외 발생

[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.

1. `c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다. 앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.
1. [EnableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1) 실행:
   * 앱에서 [in-process 호스팅 모델](xref:fundamentals/servers/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * 앱에서 [out-of-process 호스팅 모델](xref:fundamentals/servers/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. 충돌이 발생하는 조건에서 앱을 실행합니다.
1. 충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1) 실행:
   * 앱에서 [in-process 호스팅 모델](xref:fundamentals/servers/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * 앱에서 [out-of-process 호스팅 모델](xref:fundamentals/servers/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:

     ```console
     .\DisableDumps dotnet.exe
     ```

앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다. PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.

> [!WARNING]
> 크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>앱 중단 시작 중에 실패 또는 정상적으로 실행

앱이 *중단*(응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.

### <a name="analyze-the-dump"></a>덤프 분석

덤프는 여러 방법을 사용하여 분석할 수 있습니다. 자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.

## <a name="remote-debugging"></a>원격 디버깅

Visual Studio 설명서에서 [Visual Studio 2017의 원격 IIS 컴퓨터에서 ASP.NET Core 원격 디버그](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)를 참조하세요.

## <a name="application-insights"></a>응용 프로그램 정보

[Application Insights](/azure/application-insights/)에서는 오류 로깅 및 보고 기능을 포함하여 IIS를 통해 호스트된 앱의 원격 분석을 제공합니다. Application Insights는 앱의 로깅 기능을 사용할 수 있게 될 때 애플리케이션이 시작된 후 발생하는 오류만 보고할 수 있습니다. 자세한 내용은 [ASP.NET Core용 Application Insights](/azure/application-insights/app-insights-asp-net-core)를 참조하세요.

## <a name="additional-advice"></a>추가적인 조언

개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드한 후에 즉시 작동 중인 앱에서 오류가 발생하는 경우가 있습니다. 경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다. 이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.

1. *bin* 및 *obj* 폴더를 삭제합니다.
1. *%UserProfile%\\.nuget\\packages* 및 *%LocalAppData%\\Nuget\\v3-cache*에 있는 패키지 캐시를 지웁니다.
1. 프로젝트를 복원하고 다시 빌드합니다.
1. 서버의 이전 배포가 앱을 다시 배포하기 전에 완전히 삭제되었는지 확인합니다.

> [!TIP]
> 명령 프롬프트에서 `dotnet nuget locals all --clear`를 실행하면 간편하게 패키지 캐시를 지울 수 있습니다.
>
> [nuget.exe](https://www.nuget.org/downloads) 도구를 사용하고 명령 `nuget locals all -clear`를 실행하여 패키지 캐시를 지울 수도 있습니다. *nuget.exe*는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
